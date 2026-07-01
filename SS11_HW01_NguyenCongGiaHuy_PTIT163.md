# Bài 1: Phân tích & lựa chọn phương án tạo mã nguồn

## Đáp án chọn: Phương án B

## Lý do chọn phương án B

- Phương án B là tối ưu nhất vì prompt có cấu trúc rõ ràng theo 5 thành phần:
  - **Vai trò:** “Đóng vai Senior Java Developer”.
  - **Nhiệm vụ:** Viết class `PaymentValidator` kiểm tra tính hợp lệ của thẻ tín dụng.
  - **Ngữ cảnh:** Hệ thống E-commerce, cần validate trước khi gửi API thanh toán.
  - **Ràng buộc:** Dùng Java 17, triển khai thuật toán Luhn, ném `IllegalArgumentException` nếu thẻ rỗng hoặc chứa chữ cái.
  - **Định dạng:** Chỉ trả về mã nguồn Java trong một code block, không giải thích.

- Prompt này giúp AI hiểu đúng phạm vi cần làm:
  - Chỉ sinh class Java.
  - Không sinh thêm tài liệu, database hoặc hướng dẫn ngoài yêu cầu.
  - Tập trung vào logic kiểm tra thẻ bằng thuật toán Luhn.

- Các ràng buộc kỹ thuật được nêu rõ nên giảm rủi ro sinh code sai:
  - Đúng phiên bản Java 17.
  - Đúng thuật toán Luhn.
  - Có xử lý input rỗng.
  - Có xử lý input chứa chữ cái.
  - Có yêu cầu định dạng đầu ra cụ thể.

- Phương án B phù hợp với môi trường dự án vì tạo ra kết quả dễ tích hợp vào source code hiện có, ít cần chỉnh sửa thủ công.

## Lý do loại trừ phương án A

- Prompt quá ngắn và thiếu cấu trúc.
- Không nêu rõ vai trò của AI nên chất lượng code có thể không ổn định.
- Không có ngữ cảnh hệ thống E-commerce nên AI có thể viết code chung chung.
- Không nêu rõ phiên bản Java cần dùng.
- Không quy định cách xử lý lỗi khi số thẻ rỗng, null hoặc chứa ký tự không hợp lệ.
- Cụm “code ngắn gọn thôi” có thể làm AI bỏ qua kiểm tra quan trọng để rút ngắn code.
- Rủi ro hallucination thấp hơn phương án C nhưng rủi ro thiếu logic và thiếu chuẩn dự án khá cao.

## Lý do loại trừ phương án C

- Phương án C bị sai lệch phạm vi nghiêm trọng.
- Yêu cầu ban đầu chỉ cần class Java để kiểm tra số thẻ tín dụng, nhưng prompt lại yêu cầu thêm:
  - Database SQL.
  - Lưu thông tin thẻ.
  - Hướng dẫn cài đặt database.
  - Kết nối JDBC.

- Việc yêu cầu lưu thông tin thẻ có rủi ro bảo mật cao vì dữ liệu thẻ tín dụng là dữ liệu nhạy cảm.
- AI có thể hallucinate ra thiết kế database, bảng dữ liệu, cấu hình JDBC hoặc quy trình lưu thẻ không phù hợp chuẩn bảo mật.
- Prompt làm lệch khỏi mục tiêu chính là validate bằng thuật toán Luhn.
- Kết quả có thể dài, phức tạp và khó tích hợp vào dự án.
- Phương án này làm tăng rủi ro sinh mã nguồn không cần thiết, sai ngữ cảnh và vi phạm nguyên tắc tối thiểu hóa dữ liệu nhạy cảm.

## Kết luận

- Phương án B là lựa chọn tốt nhất vì có prompt rõ ràng, đầy đủ vai trò, nhiệm vụ, ngữ cảnh, ràng buộc và định dạng đầu ra.
- Phương án A thiếu chi tiết nên dễ sinh code chưa đủ chuẩn.
- Phương án C mở rộng sai phạm vi, tăng rủi ro hallucination và phát sinh vấn đề bảo mật.