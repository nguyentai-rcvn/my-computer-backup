---
name: code-business-flow-analysis
description: Đọc và phân tích chi tiết source code thật để suy ra luồng nghiệp vụ (business flow / business logic) của hệ thống, dựa hoàn toàn trên code mà không suy đoán. Sử dụng skill này bất cứ khi nào người dùng đưa vào codebase, file mã nguồn, hoặc repo và muốn hiểu code làm gì, luồng xử lý ra sao, nghiệp vụ là gì — kể cả khi họ chỉ nói "đọc hộ code này", "code này chạy thế nào", "giải thích luồng", "reverse engineer", "trace luồng xử lý", hay "từ code suy ra nghiệp vụ". Luôn ưu tiên dùng skill này cho mọi yêu cầu phân tích/giải thích code ở mức luồng nghiệp vụ thay vì trả lời chung chung.
---

# Phân tích Code → Luồng nghiệp vụ

Skill này hướng dẫn cách đọc source code thật, theo vết (trace) luồng thực thi, và suy ra luồng nghiệp vụ — với một quy tắc tối thượng: **mọi kết luận phải có bằng chứng từ code, tuyệt đối không suy đoán.**

## Nguyên tắc cốt lõi (KHÔNG được vi phạm)

1. **Chỉ dựa trên code thật.** Mọi nhận định phải truy được về một đoạn code cụ thể: tên file, tên hàm/class, và số dòng nếu có. Không có bằng chứng → không kết luận.
2. **Không suy đoán, không bịa.** Nếu code không nói rõ điều gì, ghi thẳng: *"Không xác định được từ code"*. Không tự điền giả định từ kinh nghiệm hay tên hàm "nghe có vẻ".
3. **Phân biệt rạch ròi hai loại phát biểu:**
   - **[CODE]** — code thực sự thể hiện điều này (kèm trích dẫn).
   - **[SUY LUẬN]** — Claude suy ra thêm dựa trên code, nhưng không phải code nói trực tiếp. Phải gắn nhãn rõ và nêu căn cứ.
4. **Đọc hết trước khi kết luận.** Đọc từng file, từng hàm, từng nhánh `if/else`, vòng lặp, try/catch, early return. Không kết luận từ tên file hay comment — comment có thể sai lệch so với code.
5. **Theo thứ tự thực thi, không theo thứ tự file.** Luồng nghiệp vụ là thứ tự code *chạy*, không phải thứ tự code *được viết*.

## Quy trình thực hiện

### Bước 1 — Khảo sát tổng thể
- Liệt kê cấu trúc thư mục, các file/module chính (`view` thư mục, đọc file cấu hình, manifest, `package.json`/`pom.xml`/`requirements.txt`...).
- Xác định loại dự án, ngôn ngữ, framework — **chỉ dựa trên file thật** (import, dependency khai báo trong code).
- Nếu code chưa nằm trong context và chỉ có đường dẫn, đọc file thật bằng công cụ trước khi phân tích.

### Bước 2 — Xác định Entry Point
Tìm điểm bắt đầu thực thi: `main()`, route handler, controller, API endpoint, event listener, message consumer, cron job, lambda handler... Trích dẫn chính xác đoạn khai báo entry point.

### Bước 3 — Trace luồng thực thi
Bắt đầu từ entry point, đi theo từng lời gọi hàm:
- Ghi lại thứ tự các bước **theo đúng dòng chảy thực thi**.
- Với mỗi nhánh điều kiện: nêu điều kiện rẽ nhánh và hệ quả của từng nhánh.
- Với vòng lặp: nêu cái gì được lặp và điều kiện dừng.
- Với xử lý lỗi: nêu exception nào bị bắt và xử lý ra sao.
- Với side effect: ghi nhận tương tác DB (query/insert/update/delete), gọi API ngoài, ghi file, gửi message queue — **chỉ khi code thực sự gọi**.
- Theo vết dữ liệu: đầu vào → các bước biến đổi → đầu ra.

### Bước 4 — Tổng hợp luồng nghiệp vụ
Từ các bước kỹ thuật đã trace, diễn đạt lại thành ngôn ngữ nghiệp vụ (cái gì được xử lý, cho ai, kết quả gì). Mỗi phát biểu nghiệp vụ phải neo về code đã trích dẫn ở Bước 3.

### Bước 5 — Đánh dấu khoảng trống
Liệt kê mọi điểm không xác định được từ code: cấu hình ẩn, biến môi trường không rõ giá trị, hàm gọi tới thư viện/dịch vụ ngoài không có source, logic phụ thuộc dữ liệu runtime.

## Định dạng đầu ra

Trình bày kết quả theo cấu trúc sau (tiếng Việt, trừ khi người dùng yêu cầu khác):

### 1. Tóm tắt nghiệp vụ
Một đoạn ngắn: hệ thống/đoạn code này làm gì về mặt nghiệp vụ.

### 2. Tổng quan kỹ thuật
Ngôn ngữ, framework, các thành phần chính — kèm bằng chứng (file dependency, import).

### 3. Entry point
Điểm bắt đầu xử lý, kèm trích dẫn `file:hàm[:dòng]`.

### 4. Luồng xử lý chi tiết
Đánh số từng bước theo thứ tự thực thi. Mỗi bước gồm:
- **Mô tả:** chuyện gì xảy ra.
- **Bằng chứng:** trích đoạn code + vị trí (`file.py:42-58`, hàm `xu_ly_don_hang`).
- Nhãn **[CODE]** hoặc **[SUY LUẬN]** khi cần.

### 5. Sơ đồ luồng
Dạng Mermaid (`flowchart` hoặc `sequenceDiagram`) khi có ích cho việc hình dung. Bỏ qua nếu luồng quá đơn giản.

### 6. Tương tác bên ngoài
Bảng hoặc danh sách: DB / API / queue / file system — mỗi mục kèm trích dẫn nơi code gọi.

### 7. Điểm không xác định được từ code
Liệt kê thẳng thắn các khoảng trống. Không lấp bằng phỏng đoán.

## Ví dụ phong cách trình bày một bước

> **Bước 3 — Kiểm tra tồn kho**
> Trước khi tạo đơn, hệ thống kiểm tra số lượng tồn. Nếu không đủ, ném `OutOfStockException` và dừng.
> **Bằng chứng** — `OrderService.java:88-95`:
> ```java
> if (product.getStock() < request.getQuantity()) {
>     throw new OutOfStockException(productId);
> }
> ```
> *[CODE] Code chặn đơn khi tồn kho thiếu. [SUY LUẬN] Quy tắc nghiệp vụ: không cho đặt vượt tồn — căn cứ là điều kiện so sánh trực tiếp ở trên, không có nhánh dự phòng nào khác trong hàm.*

## Lưu ý quan trọng

- **Comment ≠ sự thật.** Nếu comment mâu thuẫn với code, tin code và nêu rõ mâu thuẫn.
- **Tên hàm ≠ hành vi.** `validateUser()` có thể không validate gì cả — đọc thân hàm.
- **Đừng tóm tắt vội.** Nếu một hàm gọi 5 hàm con, trace cả 5 trước khi kết luận hàm cha làm gì.
- **Nếu codebase lớn,** hỏi người dùng phạm vi cần phân tích (một luồng/feature cụ thể) thay vì cố trace toàn bộ và đoán phần còn lại.
- Khi không chắc một kết luận có thực sự nằm trong code hay không, mặc định gắn nhãn **[SUY LUẬN]** và nêu căn cứ.
