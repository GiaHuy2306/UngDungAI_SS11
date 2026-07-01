# Bài 2: Tối ưu Prompt sinh Docstring & Giải thích Logic

## 1. Phân tích lỗ hổng của prompt cũ

Prompt cũ:  
`"Giải thích hàm này làm gì và thêm comment vào code giúp tôi: [Đoạn code]"`

### Lỗ hổng

- Prompt quá chung chung, không nêu rõ vai trò của AI.
- Không cung cấp ngữ cảnh nghiệp vụ nên AI dễ giải thích theo hướng kỹ thuật thuần túy.
- Không yêu cầu AI map biến vô nghĩa sang ý nghĩa nghiệp vụ.
- Không chỉ rõ chuẩn comment cần sinh, ví dụ Javadoc cho Java.
- Không yêu cầu comment inline cho các dòng logic phức tạp.
- Không có ràng buộc về cách trình bày đầu ra.
- Dễ dẫn đến kết quả sai lệch nghiệp vụ hoặc giải thích không đủ chi tiết để bảo trì code.

---

## 2. Prompt tối ưu

```text
Đóng vai Senior Java Developer kiêm Business Analyst.

Mục tiêu:
Hãy phân tích hàm Java cũ có tên `cD`, dịch ngược logic nghiệp vụ của hàm này, sau đó viết lại phần Javadoc và comment inline để đội bảo trì có thể hiểu và chỉnh sửa an toàn.

Ngữ cảnh:
Đây là hàm tính chiết khấu trong hệ thống bán hàng. Developer cũ đã nghỉ việc, code không có comment và tên biến không rõ nghĩa. Đội hiện tại cần hiểu chính xác logic để tránh regression bug khi chỉnh sửa.

Quy ước nghiệp vụ bắt buộc:
- Biến `a` là tổng giá trị đơn hàng.
- Biến `b` là hạng thành viên:
  - `b = 1` tương ứng Silver.
  - `b = 2` tương ứng Gold.
  - Giá trị khác tương ứng khách thường.
- Biến `c` cho biết người dùng có voucher miễn phí vận chuyển hay không:
  - `c = true`: có voucher freeship.
  - `c = false`: không có voucher freeship.

Ràng buộc:
- Không được thay đổi logic xử lý gốc.
- Không được tự ý đổi hành vi của hàm.
- Có thể đề xuất tên biến dễ hiểu hơn, nhưng phần code đầu ra vẫn phải giữ logic tương đương.
- Phải sinh Javadoc chuẩn Java cho hàm.
- Phải thêm comment inline cho các dòng có logic điều kiện hoặc công thức tính toán.
- Phải giải thích ngắn gọn ý nghĩa nghiệp vụ của hàm trước phần code.
- Nếu phát hiện logic khó hiểu hoặc có rủi ro bug, hãy ghi chú rõ trong phần “Risk Notes”.

Định dạng đầu ra:
1. Business Logic Summary
2. Variable Mapping
3. Risk Notes
4. Java Code with Javadoc and Inline Comments

Đoạn code cần phân tích:
[PASTE_CODE_HERE]
```

## 3. Kết quả đầu ra do AI sinh ra
```java
/**
 * Calculates the final payable amount after applying member discount
 * and optional free shipping voucher.
 *
 * <p>Business rules:</p>
 * <ul>
 *   <li>Silver member (b = 1) receives a 5% discount.</li>
 *   <li>Gold member (b = 2) receives a 10% discount.</li>
 *   <li>Normal customer receives no member discount.</li>
 *   <li>If the customer has a freeship voucher (c = true), shipping fee is waived.</li>
 *   <li>If the customer does not have a freeship voucher, a fixed shipping fee is added.</li>
 * </ul>
 *
 * @param a the original order amount before discount and shipping fee
 * @param b the membership level:
 *          1 = Silver, 2 = Gold, other values = normal customer
 * @param c whether the customer has a freeship voucher
 * @return the final payable amount after discount and shipping fee calculation
 */
public double cD(double a, int b, boolean c) {
    double discount = 0;

    // Apply 5% discount for Silver members.
    if (b == 1) {
        discount = a * 0.05;
    }
    // Apply 10% discount for Gold members.
    else if (b == 2) {
        discount = a * 0.10;
    }

    // Calculate order amount after applying membership discount.
    double finalAmount = a - discount;

    // If the customer does not have a freeship voucher, add fixed shipping fee.
    if (!c) {
        finalAmount = finalAmount + 30000;
    }

    // Return the final payable amount.
    return finalAmount;
}
```