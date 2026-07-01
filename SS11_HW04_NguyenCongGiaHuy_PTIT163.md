# Bài 4: Chiến lược sinh dữ liệu giả lập & Test Case

## 1. Mega-Prompt duy nhất đã sử dụng

```text
Đóng vai Senior QA Automation Engineer kiêm Java Developer.

Mục tiêu:
Hãy sinh đồng thời bộ dữ liệu giả lập Mock Data dạng JSON và mã nguồn Unit Test JUnit 5 bằng Java để kiểm thử phương thức `login(String username, String password)` của class `UserAuthentication`.

Ngữ cảnh:
Hệ thống chưa có database test, vì vậy cần tạo Mock Data trực tiếp trong file test. Phương thức `login(String username, String password)` dùng để xác thực người dùng. Tôi không cung cấp source code của hàm login, vì vậy hãy tự giả định logic hợp lý dựa trên tên hàm và các kịch bản kiểm thử.

Giả định logic login:
- Nếu username và password đúng với user active thì login thành công.
- Nếu password sai thì login thất bại.
- Nếu tài khoản bị khóa thì login thất bại.
- Nếu username hoặc password chứa pattern SQL Injection thì login thất bại.
- Nếu password rỗng thì login thất bại.
- Phương thức `login()` trả về boolean:
  - `true`: đăng nhập thành công.
  - `false`: đăng nhập thất bại.

Yêu cầu bắt buộc:
1. Sinh Mock Data JSON gồm đúng 5 user, đại diện cho 5 kịch bản:
   - Thông tin đúng.
   - Sai mật khẩu.
   - Tài khoản bị khóa.
   - Chứa mã độc SQL Injection.
   - Mật khẩu rỗng.
2. Mỗi user trong JSON phải có các trường:
   - `caseId`
   - `description`
   - `username`
   - `password`
   - `accountStatus`
   - `expectedResult`
3. Sinh mã nguồn JUnit 5 Test Case bằng Java.
4. Test case phải đọc hoặc parse bộ JSON mock data.
5. Dùng parameterized test để chạy qua toàn bộ 5 kịch bản.
6. Không kết nối database thật.
7. Có thể tạo fake/mock implementation của `UserAuthentication` ngay trong test để minh họa.
8. Code phải dễ đọc, có tên test rõ ràng.
9. Chỉ trả về 2 phần:
   - Mock Data JSON
   - JUnit 5 Test Code
```

## 2. Mock Data JSON do AI sinh ra

```json
[
  {
    "caseId": "TC-AUTH-001",
    "description": "Thông tin đăng nhập đúng với tài khoản đang hoạt động",
    "username": "valid_user",
    "password": "Correct@123",
    "accountStatus": "ACTIVE",
    "expectedResult": true
  },
  {
    "caseId": "TC-AUTH-002",
    "description": "Người dùng nhập sai mật khẩu",
    "username": "valid_user",
    "password": "Wrong@123",
    "accountStatus": "ACTIVE",
    "expectedResult": false
  },
  {
    "caseId": "TC-AUTH-003",
    "description": "Tài khoản người dùng bị khóa",
    "username": "locked_user",
    "password": "Locked@123",
    "accountStatus": "LOCKED",
    "expectedResult": false
  },
  {
    "caseId": "TC-AUTH-004",
    "description": "Username chứa chuỗi SQL Injection",
    "username": "admin' OR '1'='1",
    "password": "AnyPassword@123",
    "accountStatus": "ACTIVE",
    "expectedResult": false
  },
  {
    "caseId": "TC-AUTH-005",
    "description": "Mật khẩu rỗng",
    "username": "valid_user",
    "password": "",
    "accountStatus": "ACTIVE",
    "expectedResult": false
  }
]
```

## 3. Mã nguồn JUnit 5 do AI sinh ra

```java
import com.fasterxml.jackson.core.type.TypeReference;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.MethodSource;

import java.util.List;
import java.util.Map;
import java.util.stream.Stream;

import static org.junit.jupiter.api.Assertions.assertEquals;

class UserAuthenticationTest {

    private static final String MOCK_USERS_JSON = """
        [
          {
            "caseId": "TC-AUTH-001",
            "description": "Thông tin đăng nhập đúng với tài khoản đang hoạt động",
            "username": "valid_user",
            "password": "Correct@123",
            "accountStatus": "ACTIVE",
            "expectedResult": true
          },
          {
            "caseId": "TC-AUTH-002",
            "description": "Người dùng nhập sai mật khẩu",
            "username": "valid_user",
            "password": "Wrong@123",
            "accountStatus": "ACTIVE",
            "expectedResult": false
          },
          {
            "caseId": "TC-AUTH-003",
            "description": "Tài khoản người dùng bị khóa",
            "username": "locked_user",
            "password": "Locked@123",
            "accountStatus": "LOCKED",
            "expectedResult": false
          },
          {
            "caseId": "TC-AUTH-004",
            "description": "Username chứa chuỗi SQL Injection",
            "username": "admin' OR '1'='1",
            "password": "AnyPassword@123",
            "accountStatus": "ACTIVE",
            "expectedResult": false
          },
          {
            "caseId": "TC-AUTH-005",
            "description": "Mật khẩu rỗng",
            "username": "valid_user",
            "password": "",
            "accountStatus": "ACTIVE",
            "expectedResult": false
          }
        ]
        """;

    private final UserAuthentication userAuthentication = new UserAuthentication();

    @ParameterizedTest(name = "{0} - {1}")
    @MethodSource("provideLoginTestCases")
    void shouldValidateLoginBasedOnMockData(
            String caseId,
            String description,
            String username,
            String password,
            String accountStatus,
            boolean expectedResult
    ) {
        boolean actualResult = userAuthentication.login(username, password, accountStatus);

        assertEquals(
                expectedResult,
                actualResult,
                "Failed test case: " + caseId + " - " + description
        );
    }

    static Stream<Object[]> provideLoginTestCases() throws Exception {
        ObjectMapper objectMapper = new ObjectMapper();

        List<Map<String, Object>> testCases = objectMapper.readValue(
                MOCK_USERS_JSON,
                new TypeReference<List<Map<String, Object>>>() {}
        );

        return testCases.stream()
                .map(testCase -> new Object[] {
                        testCase.get("caseId"),
                        testCase.get("description"),
                        testCase.get("username"),
                        testCase.get("password"),
                        testCase.get("accountStatus"),
                        testCase.get("expectedResult")
                });
    }

    static class UserAuthentication {

        private static final String VALID_USERNAME = "valid_user";
        private static final String VALID_PASSWORD = "Correct@123";

        boolean login(String username, String password, String accountStatus) {
            if (username == null || password == null) {
                return false;
            }

            if (password.isBlank()) {
                return false;
            }

            if ("LOCKED".equalsIgnoreCase(accountStatus)) {
                return false;
            }

            if (containsSqlInjectionPattern(username) || containsSqlInjectionPattern(password)) {
                return false;
            }

            return VALID_USERNAME.equals(username) && VALID_PASSWORD.equals(password);
        }

        private boolean containsSqlInjectionPattern(String value) {
            String lowerValue = value.toLowerCase();

            return lowerValue.contains("' or '1'='1")
                    || lowerValue.contains(" or 1=1")
                    || lowerValue.contains("--")
                    || lowerValue.contains(";")
                    || lowerValue.contains("drop table");
        }
    }
}
```