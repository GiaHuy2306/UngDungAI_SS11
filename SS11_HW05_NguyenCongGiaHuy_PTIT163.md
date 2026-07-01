# Bài 5: Bài tập sáng tạo - Xây dựng Workflow tích hợp  
## Tính năng: Loyalty Points - Tính điểm thưởng

## 1. Thiết kế luồng xử lý

### 1.1. Input

Hệ thống cần các dữ liệu đầu vào sau:

- `orderAmount`: Tổng giá trị đơn hàng, đơn vị VNĐ.
- `orderDate`: Ngày mua hàng.
- `customerType`: Loại khách hàng, gồm:
  - `NORMAL`
  - `VIP`

### 1.2. Output

Hệ thống trả về:

- `loyaltyPoints`: Số điểm thưởng cuối cùng của khách hàng.

### 1.3. Quy tắc nghiệp vụ

- Cứ tiêu `100.000 VNĐ` thì được `1 điểm`.
- Nếu mua hàng vào cuối tuần, tức Thứ 7 hoặc Chủ Nhật, tổng điểm cơ bản được nhân đôi.
- Nếu khách hàng là `VIP`, cộng thêm `20%` vào số điểm cuối cùng sau khi đã áp dụng ưu đãi cuối tuần.
- Nếu `orderAmount < 0`, hệ thống phải báo lỗi.
- Nếu `orderAmount < 100.000`, khách hàng được `0 điểm`.
- Điểm thưởng là số nguyên, phần thập phân được làm tròn xuống.

### 1.4. Sơ đồ ASCII

```text
Start
  |
  v
Nhận orderAmount, orderDate, customerType
  |
  v
Kiểm tra orderAmount < 0?
  |
  +-- Có --> Throw IllegalArgumentException
  |
  +-- Không
        |
        v
Tính điểm cơ bản:
basePoints = orderAmount / 100000
        |
        v
Kiểm tra orderDate là Thứ 7 hoặc Chủ Nhật?
        |
        +-- Có --> basePoints = basePoints * 2
        |
        +-- Không --> Giữ nguyên basePoints
        |
        v
Kiểm tra customerType là VIP?
        |
        +-- Có --> finalPoints = basePoints * 1.2
        |
        +-- Không --> finalPoints = basePoints
        |
        v
Làm tròn xuống finalPoints
        |
        v
Trả về loyaltyPoints
```

## 2. Workflow / Mega-Prompt đã sử dụng

```text
Đóng vai AI Solution Architect kiêm Senior Java Developer và QA Automation Engineer.

Mục tiêu:
Hãy chuyển hóa yêu cầu kinh doanh thô thành mã nguồn Java production-ready cho tính năng tính điểm thưởng Loyalty Points của hệ thống TechShop.

Ngữ cảnh nghiệp vụ:
Ban giám đốc TechShop muốn tung ra tính năng Loyalty Points trong đợt Sale cuối tuần. Business Analyst cung cấp 3 luật nghiệp vụ:
1. Cứ tiêu 100.000 VNĐ thì được 1 điểm thưởng.
2. Nếu mua hàng vào cuối tuần, tức Thứ 7 hoặc Chủ Nhật, tổng số điểm được nhân đôi.
3. Khách hàng thuộc hạng VIP luôn được cộng thêm 20% vào số điểm cuối cùng, sau khi đã tính các ưu đãi khác.

Yêu cầu đầu vào:
- orderAmount: tổng giá trị đơn hàng, đơn vị VNĐ.
- orderDate: ngày mua hàng.
- customerType: loại khách hàng, gồm NORMAL hoặc VIP.

Yêu cầu đầu ra:
- loyaltyPoints: số điểm thưởng cuối cùng.

Ràng buộc kỹ thuật:
- Dùng Java 17.
- Viết class `LoyaltyPointsCalculator`.
- Viết enum `CustomerType` gồm NORMAL và VIP.
- Method chính tên là `calculatePoints(long orderAmount, LocalDate orderDate, CustomerType customerType)`.
- Nếu `orderAmount < 0`, ném `IllegalArgumentException`.
- Nếu `orderDate` hoặc `customerType` null, ném `IllegalArgumentException`.
- Điểm thưởng phải là số nguyên.
- Nếu phát sinh số thập phân sau khi cộng thêm 20% cho VIP, làm tròn xuống.
- Không dùng database.
- Không gọi API bên ngoài.
- Code phải dễ đọc, tách constant rõ ràng, tránh magic number.
- Thêm Javadoc đầy đủ cho class và method.
- Thêm inline comment cho các dòng tính toán quan trọng.

Yêu cầu Unit Test:
- Viết JUnit 5 test cho class trên.
- Bao phủ ít nhất các case:
  1. Đơn hàng thường ngày, khách NORMAL.
  2. Đơn hàng cuối tuần, khách NORMAL.
  3. Đơn hàng thường ngày, khách VIP.
  4. Đơn hàng cuối tuần, khách VIP.
  5. Đơn hàng dưới 100.000 VNĐ.
  6. Đơn hàng đúng 100.000 VNĐ.
  7. Đơn hàng âm.
  8. orderDate null.
  9. customerType null.
  10. Case VIP bị lẻ điểm sau khi cộng 20%, cần làm tròn xuống.

Định dạng đầu ra:
1. Brief Logic Explanation
2. Java Production Code
3. JUnit 5 Test Code
4. Notes about Edge Cases
```

## 3. Kiểm chứng thực tế - Text Log cuộc hội thoại

```text
User:
Đóng vai AI Solution Architect kiêm Senior Java Developer và QA Automation Engineer.

Mục tiêu:
Hãy chuyển hóa yêu cầu kinh doanh thô thành mã nguồn Java production-ready cho tính năng tính điểm thưởng Loyalty Points của hệ thống TechShop.

Ngữ cảnh nghiệp vụ:
Ban giám đốc TechShop muốn tung ra tính năng Loyalty Points trong đợt Sale cuối tuần. Business Analyst cung cấp 3 luật nghiệp vụ:
1. Cứ tiêu 100.000 VNĐ thì được 1 điểm thưởng.
2. Nếu mua hàng vào cuối tuần, tức Thứ 7 hoặc Chủ Nhật, tổng số điểm được nhân đôi.
3. Khách hàng thuộc hạng VIP luôn được cộng thêm 20% vào số điểm cuối cùng, sau khi đã tính các ưu đãi khác.

Yêu cầu đầu vào:
- orderAmount: tổng giá trị đơn hàng, đơn vị VNĐ.
- orderDate: ngày mua hàng.
- customerType: loại khách hàng, gồm NORMAL hoặc VIP.

Yêu cầu đầu ra:
- loyaltyPoints: số điểm thưởng cuối cùng.

Ràng buộc kỹ thuật:
- Dùng Java 17.
- Viết class `LoyaltyPointsCalculator`.
- Viết enum `CustomerType` gồm NORMAL và VIP.
- Method chính tên là `calculatePoints(long orderAmount, LocalDate orderDate, CustomerType customerType)`.
- Nếu `orderAmount < 0`, ném `IllegalArgumentException`.
- Nếu `orderDate` hoặc `customerType` null, ném `IllegalArgumentException`.
- Điểm thưởng phải là số nguyên.
- Nếu phát sinh số thập phân sau khi cộng thêm 20% cho VIP, làm tròn xuống.
- Không dùng database.
- Không gọi API bên ngoài.
- Code phải dễ đọc, tách constant rõ ràng, tránh magic number.
- Thêm Javadoc đầy đủ cho class và method.
- Thêm inline comment cho các dòng tính toán quan trọng.

Yêu cầu Unit Test:
- Viết JUnit 5 test cho class trên.
- Bao phủ ít nhất các case:
  1. Đơn hàng thường ngày, khách NORMAL.
  2. Đơn hàng cuối tuần, khách NORMAL.
  3. Đơn hàng thường ngày, khách VIP.
  4. Đơn hàng cuối tuần, khách VIP.
  5. Đơn hàng dưới 100.000 VNĐ.
  6. Đơn hàng đúng 100.000 VNĐ.
  7. Đơn hàng âm.
  8. orderDate null.
  9. customerType null.
  10. Case VIP bị lẻ điểm sau khi cộng 20%, cần làm tròn xuống.

Định dạng đầu ra:
1. Brief Logic Explanation
2. Java Production Code
3. JUnit 5 Test Code
4. Notes about Edge Cases

Assistant:
## 1. Brief Logic Explanation

Tính năng Loyalty Points tính điểm thưởng dựa trên tổng giá trị đơn hàng. Hệ thống lấy phần nguyên của phép chia `orderAmount / 100000` để ra điểm cơ bản. Nếu ngày mua hàng là Thứ 7 hoặc Chủ Nhật, điểm cơ bản được nhân đôi. Sau đó, nếu khách hàng là VIP, hệ thống cộng thêm 20% vào điểm sau ưu đãi cuối tuần. Kết quả cuối cùng được làm tròn xuống vì điểm thưởng là số nguyên.

## 2. Java Production Code

```java
import java.time.DayOfWeek;
import java.time.LocalDate;
import java.util.Objects;

/**
 * Calculates loyalty points for TechShop orders based on order amount,
 * purchase date, and customer type.
 *
 * <p>Business rules:</p>
 * <ul>
 *     <li>Every 100,000 VND spent earns 1 loyalty point.</li>
 *     <li>Orders placed on Saturday or Sunday receive double points.</li>
 *     <li>VIP customers receive an additional 20% points after all other promotions.</li>
 *     <li>Final points are returned as an integer and decimal values are rounded down.</li>
 * </ul>
 */
public class LoyaltyPointsCalculator {

    private static final long VND_PER_POINT = 100_000L;
    private static final int WEEKEND_MULTIPLIER = 2;
    private static final double VIP_BONUS_MULTIPLIER = 1.2;

    /**
     * Calculates final loyalty points for an order.
     *
     * @param orderAmount the total order amount in VND
     * @param orderDate the date when the order was placed
     * @param customerType the customer type, either NORMAL or VIP
     * @return final loyalty points after applying weekend and VIP bonuses
     * @throws IllegalArgumentException if orderAmount is negative, orderDate is null,
     *                                  or customerType is null
     */
    public int calculatePoints(long orderAmount, LocalDate orderDate, CustomerType customerType) {
        validateInput(orderAmount, orderDate, customerType);

        // Calculate base points using integer division, so amounts below 100,000 VND get 0 points.
        int points = (int) (orderAmount / VND_PER_POINT);

        // Apply weekend multiplier before VIP bonus according to business priority.
        if (isWeekend(orderDate)) {
            points *= WEEKEND_MULTIPLIER;
        }

        // Apply VIP bonus after all previous promotions and round down to integer.
        if (customerType == CustomerType.VIP) {
            points = (int) Math.floor(points * VIP_BONUS_MULTIPLIER);
        }

        return points;
    }

    private void validateInput(long orderAmount, LocalDate orderDate, CustomerType customerType) {
        if (orderAmount < 0) {
            throw new IllegalArgumentException("Order amount must not be negative.");
        }

        Objects.requireNonNull(orderDate, "Order date must not be null.");
        Objects.requireNonNull(customerType, "Customer type must not be null.");
    }

    private boolean isWeekend(LocalDate orderDate) {
        DayOfWeek dayOfWeek = orderDate.getDayOfWeek();
        return dayOfWeek == DayOfWeek.SATURDAY || dayOfWeek == DayOfWeek.SUNDAY;
    }
}

enum CustomerType {
    NORMAL,
    VIP
}
```


---

## 4. Code Java hoàn chỉnh sau khi kiểm chứng

```java
import java.time.DayOfWeek;
import java.time.LocalDate;
import java.util.Objects;

/**
 * Calculates loyalty points for TechShop orders based on order amount,
 * purchase date, and customer type.
 *
 * <p>Business rules:</p>
 * <ul>
 *     <li>Every 100,000 VND spent earns 1 loyalty point.</li>
 *     <li>Orders placed on Saturday or Sunday receive double points.</li>
 *     <li>VIP customers receive an additional 20% points after all other promotions.</li>
 *     <li>Final points are returned as an integer and decimal values are rounded down.</li>
 * </ul>
 */
public class LoyaltyPointsCalculator {

    private static final long VND_PER_POINT = 100_000L;
    private static final int WEEKEND_MULTIPLIER = 2;
    private static final double VIP_BONUS_MULTIPLIER = 1.2;

    /**
     * Calculates final loyalty points for an order.
     *
     * @param orderAmount the total order amount in VND
     * @param orderDate the date when the order was placed
     * @param customerType the customer type, either NORMAL or VIP
     * @return final loyalty points after applying weekend and VIP bonuses
     * @throws IllegalArgumentException if orderAmount is negative, orderDate is null,
     *                                  or customerType is null
     */
    public int calculatePoints(long orderAmount, LocalDate orderDate, CustomerType customerType) {
        validateInput(orderAmount, orderDate, customerType);

        // Calculate base points using integer division, so amounts below 100,000 VND get 0 points.
        int points = (int) (orderAmount / VND_PER_POINT);

        // Apply weekend multiplier before VIP bonus according to business priority.
        if (isWeekend(orderDate)) {
            points *= WEEKEND_MULTIPLIER;
        }

        // Apply VIP bonus after all previous promotions and round down to integer.
        if (customerType == CustomerType.VIP) {
            points = (int) Math.floor(points * VIP_BONUS_MULTIPLIER);
        }

        return points;
    }

    private void validateInput(long orderAmount, LocalDate orderDate, CustomerType customerType) {
        if (orderAmount < 0) {
            throw new IllegalArgumentException("Order amount must not be negative.");
        }

        Objects.requireNonNull(orderDate, "Order date must not be null.");
        Objects.requireNonNull(customerType, "Customer type must not be null.");
    }

    private boolean isWeekend(LocalDate orderDate) {
        DayOfWeek dayOfWeek = orderDate.getDayOfWeek();
        return dayOfWeek == DayOfWeek.SATURDAY || dayOfWeek == DayOfWeek.SUNDAY;
    }
}

enum CustomerType {
    NORMAL,
    VIP
}
```

## 5. Unit Test JUnit 5

```java
import org.junit.jupiter.api.Test;

import java.time.LocalDate;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertThrows;

class LoyaltyPointsCalculatorTest {

    private final LoyaltyPointsCalculator calculator = new LoyaltyPointsCalculator();

    @Test
    void shouldCalculatePointsForNormalCustomerOnWeekday() {
        int points = calculator.calculatePoints(
                500_000L,
                LocalDate.of(2026, 7, 1),
                CustomerType.NORMAL
        );

        assertEquals(5, points);
    }

    @Test
    void shouldDoublePointsForNormalCustomerOnWeekend() {
        int points = calculator.calculatePoints(
                500_000L,
                LocalDate.of(2026, 7, 4),
                CustomerType.NORMAL
        );

        assertEquals(10, points);
    }

    @Test
    void shouldAddVipBonusForVipCustomerOnWeekday() {
        int points = calculator.calculatePoints(
                500_000L,
                LocalDate.of(2026, 7, 1),
                CustomerType.VIP
        );

        assertEquals(6, points);
    }

    @Test
    void shouldApplyWeekendMultiplierBeforeVipBonus() {
        int points = calculator.calculatePoints(
                500_000L,
                LocalDate.of(2026, 7, 5),
                CustomerType.VIP
        );

        assertEquals(12, points);
    }

    @Test
    void shouldReturnZeroWhenOrderAmountIsBelowMinimumPointThreshold() {
        int points = calculator.calculatePoints(
                99_999L,
                LocalDate.of(2026, 7, 1),
                CustomerType.NORMAL
        );

        assertEquals(0, points);
    }

    @Test
    void shouldReturnOnePointWhenOrderAmountEqualsMinimumThreshold() {
        int points = calculator.calculatePoints(
                100_000L,
                LocalDate.of(2026, 7, 1),
                CustomerType.NORMAL
        );

        assertEquals(1, points);
    }

    @Test
    void shouldThrowExceptionWhenOrderAmountIsNegative() {
        assertThrows(
                IllegalArgumentException.class,
                () -> calculator.calculatePoints(
                        -100_000L,
                        LocalDate.of(2026, 7, 1),
                        CustomerType.NORMAL
                )
        );
    }

    @Test
    void shouldThrowExceptionWhenOrderDateIsNull() {
        assertThrows(
                NullPointerException.class,
                () -> calculator.calculatePoints(
                        100_000L,
                        null,
                        CustomerType.NORMAL
                )
        );
    }

    @Test
    void shouldThrowExceptionWhenCustomerTypeIsNull() {
        assertThrows(
                NullPointerException.class,
                () -> calculator.calculatePoints(
                        100_000L,
                        LocalDate.of(2026, 7, 1),
                        null
                )
        );
    }

    @Test
    void shouldRoundDownVipBonusWhenResultHasDecimalPart() {
        int points = calculator.calculatePoints(
                300_000L,
                LocalDate.of(2026, 7, 1),
                CustomerType.VIP
        );

        assertEquals(3, points);
    }
}
```