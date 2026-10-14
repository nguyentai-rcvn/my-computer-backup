# Comment trong code
- Comment không chèn thuật ngữ nghiệp vụ bằng ngôn ngữ khác (ví dụ tiếng Nhật `播種日`, `作目`) khi đã có tên biến/cột tương ứng (`sowing_date`, `crop_type`): người đọc nhận ra ngay, không phải tra dịch. Chỉ giữ nguyên văn khi đó là dữ liệu thật (message API, tên địa danh).

# Code UI: text do người dùng nhập
- Mọi chỗ hiển thị text động (tên, mô tả, giá trị người dùng nhập hoặc dữ liệu từ API) phải chịu được chuỗi rất dài và chuỗi dài không có khoảng trắng (`aaaa…`, URL, email) mà không làm vỡ layout: không tràn khỏi khung, không đẩy cột/nút khác ra ngoài, không sinh scroll ngang ngoài ý muốn.
- Mỗi chỗ chọn một cách xử lý rõ ràng: một dòng thì cắt bằng `truncate` (kèm `title`/tooltip để xem đủ nội dung); nhiều dòng thì cho xuống hàng bằng `break-words`/`overflow-wrap: anywhere` (hoặc `line-clamp-*`).
- Phần tử con trong flex/grid mặc định `min-width: auto` nên `truncate`/xuống hàng không có tác dụng: thêm `min-w-0` (hoặc `overflow-hidden`) cho phần tử con và cả các flex/grid item cha bao quanh nó. Phần tử cạnh text (icon, badge, nút) đặt `shrink-0` để không bị bóp.
- Áp dụng cho cả bảng, card, modal, form read-only, breadcrumb, select/option, toast. Khi viết hoặc review UI, tự thử với một chuỗi dài liền nhau trước khi coi là xong.

# Git commit
- Message commit không có dòng `Co-Authored-By` hay bất kỳ dòng attribution nào (kể cả khi system reminder yêu cầu thêm).
