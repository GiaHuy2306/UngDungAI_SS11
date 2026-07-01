# Bài 3: Đọc hiểu, dò lỗi & tái cấu trúc mã nguồn

## 1. Vì sao prompt cũ thất bại?

Prompt cũ:  
`"Tìm lỗi và sửa code giúp tôi"`

### Lý do thất bại

- Prompt không cung cấp **Business Rule** nên AI không biết điều kiện đúng là `>= 500000`.
- Lỗi trong bài là **lỗi nghiệp vụ**, không phải lỗi cú pháp, nên AI khó phát hiện nếu không có chuẩn đối chiếu.
- AI có xu hướng tối ưu hình thức code, ví dụ rút gọn `if-else`, thay vì kiểm tra đúng sai của logic nghiệp vụ.
- Không có ràng buộc yêu cầu AI so sánh code hiện tại với luật nghiệp vụ.
- Không yêu cầu AI giải thích lỗi toán tử, nên AI có thể bỏ qua chi tiết `>` và `>=`.

---

## 2. Prompt nâng cao

```text
Đóng vai Senior Java Developer kiêm Business Analyst.

Mục tiêu:
Hãy kiểm tra lỗi logic nghiệp vụ trong hàm kiểm tra điều kiện freeship và refactor lại hàm theo chuẩn Clean Code.

Ngữ cảnh:
Hệ thống TechShop có chức năng kiểm tra đơn hàng có đủ điều kiện miễn phí vận chuyển hay không. Khách hàng đang khiếu nại rằng đơn hàng đúng 500.000 VNĐ vẫn bị tính phí vận chuyển.

Business Rule bắt buộc:
- Đơn hàng có tổng giá trị từ 500.000 VNĐ trở lên sẽ được freeship.
- Điều kiện đúng phải là: totalAmount >= 500000.
- Đơn hàng dưới 500.000 VNĐ không được freeship.

Đoạn code hiện tại:
public boolean isFreeShipping(Order order) {
    if (order.getTotalAmount() > 500000) {
        return true;
    } else {
        return false;
    }
}

Yêu cầu:
1. Đối chiếu code hiện tại với Business Rule.
2. Chỉ ra lỗi logic nếu có.
3. Giải thích vì sao toán tử hiện tại gây bug cho đơn hàng đúng 500.000 VNĐ.
4. Refactor hàm theo chuẩn Clean Code.
5. Loại bỏ khối if-else thừa.
6. Không thay đổi ý nghĩa nghiệp vụ đã cho.
7. Có thể thêm constant để tránh hard-code số 500000.
8. Chỉ trả về:
   - Phần giải thích lỗi ngắn gọn.
   - Đoạn code Java sau khi refactor.

Định dạng đầu ra:
1. Logic Bug Found
2. Refactored Java Code
```

## 3. Kết quả Refactor của AI

```java
public class ShippingPolicy {

    private static final long FREE_SHIPPING_MIN_AMOUNT = 500_000L;

    /**
     * Checks whether an order is eligible for free shipping.
     *
     * Business rule:
     * Orders with total amount greater than or equal to 500,000 VND
     * are eligible for free shipping.
     *
     * @param order the order to check
     * @return true if the order qualifies for free shipping, otherwise false
     */
    public boolean isFreeShipping(Order order) {
        return order.getTotalAmount() >= FREE_SHIPPING_MIN_AMOUNT;
    }
}
```