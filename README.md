# MH-Dowsample

> **Trạng thái:** công cụ cá nhân đang phát triển. Repository này được tạo trước hết để phục vụ quy trình quản lý sample của Minh Hiếu; chưa được giới thiệu là sản phẩm thương mại hoặc phần mềm ổn định cho mọi người dùng.

MH-Dowsample là công cụ chạy local giúp kiểm tra, phân loại và sắp xếp thư viện sample âm thanh. Dự án xuất phát từ nhu cầu cá nhân khi làm nhạc: nhiều file nằm rải rác, tên khó đọc, thiếu metadata và dễ bị trùng.

Khi công cụ đủ ổn định và an toàn, dự án có thể được chia sẻ để cộng đồng producer tham khảo hoặc dùng thử. README này chỉ mô tả chức năng có trong source và các giới hạn quan trọng; không xem số phiên bản, badge hoặc build thành công là bằng chứng sản phẩm đã hoàn thiện.

## Chức năng hiện có

- Kiểm tra một số thuộc tính chất lượng của file âm thanh.
- Phân loại thực dụng thành loop, one-shot, FX hoặc nhóm chưa xác định.
- Ước lượng BPM, key và gợi ý thể loại khi có thể.
- Chuyển đổi file được chấp nhận sang WAV theo cấu hình.
- Dùng SHA-256 và SQLite để hỗ trợ phát hiện file trùng.
- Tổ chức output theo cấu trúc folder dễ duyệt hơn trong DAW.
- Xử lý batch với số worker có thể cấu hình.
- Có Telegram bot tùy chọn cho chủ dự án.

## Cảnh báo quan trọng

Một lần chạy thông thường có thể di chuyển file nguồn đã xử lý ra khỏi thư mục input.

Luôn thử trước bằng:

```powershell
python organize.py --input .\raw_samples --dry-run
```

Khi cần giữ nguyên file nguồn, dùng `--copy`:

```powershell
python organize.py --input .\raw_samples --output .\organized --copy
```

Không chạy công cụ trên thư viện quan trọng khi chưa có backup và chưa đọc kỹ kết quả dry-run.

## Giới hạn và nguyên tắc

- Công cụ chạy local; dữ liệu âm thanh và cơ sở dữ liệu không được đưa lên Git theo mặc định.
- Ước lượng BPM, key hoặc genre có thể sai và phải được người dùng kiểm tra lại.
- Chức năng phát hiện nội dung trên web chỉ được dùng với nội dung người dùng có quyền truy cập và tải xuống.
- Telegram bot chỉ nên cấu hình cho tài khoản quản trị riêng.
- Dự án chưa tuyên bố phù hợp cho môi trường production hoặc thư viện không có backup.
- Không có cam kết thương mại, SLA hoặc hỗ trợ doanh nghiệp ở giai đoạn hiện tại.

## Yêu cầu

- Python 3.11 trở lên.
- FFmpeg và FFprobe có trong `PATH`.
- Node.js được khuyến nghị trên Windows nếu sử dụng Playwright.

## Cài đặt

```powershell
git clone https://github.com/studiozengermany-cmd/MH---DOWSAMPLE-PRO.git
cd MH---DOWSAMPLE-PRO

python -m venv .venv
.\.venv\Scripts\Activate.ps1

python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -m playwright install chromium --only-shell
Copy-Item .env.example .env
```

Công cụ phát triển bổ sung:

```powershell
python -m pip install -r requirements-dev.txt
```

## Cấu hình chính

| Biến | Mục đích | Mặc định |
| --- | --- | --- |
| `DOWNLOAD_DIR` | Nơi giữ file tải về | `./downloads` |
| `OUTPUT_DIR` | Thư viện WAV đã tổ chức | `./organized` |
| `DB_PATH` | SQLite inventory | `./data/database.db` |
| `TARGET_SAMPLE_RATE` | Sample rate output | `44100` |
| `WORKERS` | Số worker | `4` |
| `BATCH_SIZE` | Kích thước batch | `50` |
| `TELEGRAM_TOKEN` | Token bot tùy chọn | rỗng |
| `ADMIN_USER_ID` | Telegram user ID được phép | `0` |

Không commit file `.env` thật, token, database, browser profile hoặc nội dung âm thanh của bên thứ ba.

## Một số lệnh sử dụng

```powershell
# Xem trước, không thay đổi file
python organize.py --input .\raw_samples --dry-run

# Xử lý và giữ nguyên file nguồn
python organize.py --input .\raw_samples --output .\organized --copy

# Xem thống kê
python organize.py --stats

# Xem toàn bộ tham số
python organize.py --help
```

## Kiểm tra khi phát triển

```powershell
python -m pytest tests -v --cov=. --cov-fail-under=68
python -m ruff check .
python -m mypy config.py exceptions.py quality_gate.py processor.py organizer.py organize.py crawler.py bot.py utils tools --ignore-missing-imports
python -m bandit -r . -x ./tests,./tools -ll
```

Các lệnh kiểm tra giúp xác nhận source tại thời điểm chạy; chúng không thay thế việc thử nghiệm trên bản sao dữ liệu thật.

## Cấu trúc chính

```text
MH-Dowsample/
├─ organize.py
├─ quality_gate.py
├─ processor.py
├─ organizer.py
├─ library_layout.py
├─ crawler.py
├─ bot.py
├─ utils/
├─ tools/
└─ tests/
```

## Giấy phép

Source hiện được phát hành theo [MIT License](LICENSE). Người dùng vẫn phải tự chịu trách nhiệm về quyền sử dụng đối với mọi file âm thanh, website hoặc nội dung bên thứ ba được xử lý.

## Liên hệ

- Website: https://studiominhhieu.com/
- Email: support@studiominhhieu.com
- GitHub: https://github.com/studiozengermany-cmd
