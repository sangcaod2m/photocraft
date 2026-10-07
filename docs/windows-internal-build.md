# Build Windows x64 cho nội bộ

Workflow: `.github/workflows/windows-x64-internal.yml` — **Windows x64 Internal Build**.

## Chạy trên GitHub

1. Commit và push workflow cùng tài liệu này lên repository công ty, đưa vào default branch để GitHub hiển thị nút chạy thủ công.
2. Vào **Actions → Windows x64 Internal Build → Run workflow**.
3. Chọn branch cần build và bấm **Run workflow**.
4. Khi job thành công, mở phần **Artifacts**, tải `photocraft-windows-x64-<run>-<attempt>` và giải nén.

Repository cần bật GitHub Actions, cho phép các actions dùng trong workflow và có khả năng sử dụng GitHub-hosted Windows runners. Người chạy cần quyền write. Workflow chỉ chạy thủ công, không tạo tag/release hoặc upload lên VPS; không cần cấu hình secrets hay environment `release`.

## Kết quả

- `photocraft-<version>-windows-x64.msi`: bộ cài per-machine; có thể cần quyền administrator.
- `photocraft-<version>-windows-x64-portable.zip`: giải nén vào thư mục có quyền ghi, chạy `photocraft.exe`. Thiết lập và dữ liệu phục hồi nằm trong `PhotoCraftData` cạnh executable; giữ thư mục này khi đổi bản.
- `SHA256SUMS.txt`: SHA-256 của hai gói.
- `BUILD-INFO.txt`: commit, branch/ref và đường dẫn lần build để truy vết.

Version lấy từ `[workspace.package].version` trong `Cargo.toml`. Artifact lưu 30 ngày (tùy giới hạn của tổ chức); tải về hoặc đưa lên kho nội bộ nếu cần giữ lâu hơn. Mỗi lần chạy có tên artifact riêng dù version chưa đổi.

Build dùng Rust stable, Windows 2022 x64 runner, WiX 5.0.2 và script đóng gói sẵn có. Cargo dùng `--locked`. Script kiểm tra kiến trúc PE, subsystem GUI/console, MSI shortcut icon và chạy CLI `--version`. Đây không phải kiểm thử tương tác GUI hay toàn bộ test suite.

Đây là bản **chưa ký số** dành cho thử nghiệm nội bộ. Windows hoặc chính sách IT có thể cảnh báo/chặn; phối hợp với IT hoặc bổ sung ký số trước khi phân phối rộng. Workflow không tải `craft-fonts` tùy chọn; ứng dụng dùng font đóng gói và font cài trên Windows. Máy artist không cần Rust, WiX hay .NET SDK để chạy ứng dụng.

## Kiểm tra trước khi phát rộng

Cho 2–3 artist thử bản portable trên máy Windows x64 với bản sao tài liệu thực tế: mở PSD, font tiếng Việt/font công ty, layer/mask/effects, brush/bảng vẽ, lưu `.pcraft`, xuất ảnh và mở PSD kết quả trong Photoshop. Thử cả file lớn và các GPU đang dùng trong công ty. Có thể thử `photocraft.exe --safe-gpu` khi gặp lỗi khởi tạo GPU.

Nếu job lỗi, mở bước đỏ trong Actions: lỗi policy/quota runner cần quản trị GitHub; lỗi Cargo xem thông báo compiler đầu tiên; lỗi WiX xem bước đóng gói. Việc build thành công chưa thay thế kiểm tra ứng dụng trên Windows thật.

Hướng dẫn GitHub: https://docs.github.com/en/actions/how-tos/manage-workflow-runs/manually-run-a-workflow
