# Hệ thống Thu thập và Xử lý Dữ liệu Lịch Âm

Dự án này là một pipeline tự động hóa quy trình thu thập dữ liệu lịch âm từ website, làm sạch và cấu trúc hóa dữ liệu bằng Google Gemini AI, sau đó kiểm tra chất lượng đầu ra (Quality Assurance) và gộp file báo cáo.

## Mục Lục
- [Giới Thiệu](#giới-thiệu)
- [Cấu Trúc Dự Án](#cấu-trúc-dự-án)
- [Cài Đặt](#cài-đặt)
- [Hướng Dẫn Sử Dụng](#hướng-dẫn-sử-dụng)
- [Tùy Biến Nâng Cao](#tùy-biến-nâng-cao)

## Giới Thiệu

Dự án này tự động hóa quy trình thu thập dữ liệu lịch âm từ website, làm sạch và cấu trúc hóa dữ liệu bằng Google Gemini AI, sau đó kiểm tra chất lượng đầu ra (Quality Assurance) và gộp file báo cáo.

## Cấu Trúc Dự Án

*   `Crawl.py`: Script chính thực hiện crawl dữ liệu thô và gọi AI để trích xuất thông tin.
*   `evaluate.py`: Script đánh giá chất lượng file đầu ra dựa trên bộ tiêu chí Regex.
*   `merge_txt_files.py`: Script tiện ích để gộp các file báo cáo riêng lẻ thành file tổng hợp.
*   `.env`: File cấu hình chứa các khóa bảo mật (API Keys).
*   `output/`: Thư mục chứa các file kết quả sau khi xử lý.

## Cài Đặt

### Yêu Cầu
*   Python 3.8 trở lên.

### Cài Đặt Thư Viện
Sử dụng pip để cài đặt các thư viện cần thiết:
```bash
pip install python-dotenv firecrawl-py google-genai
```

### Cấu Hình API Keys
Tạo file `.env` tại thư mục gốc của dự án và điền các API Key tương ứng:
```dotenv
FIRECRAWL_API_KEY="your_firecrawl_key"
GEMINI_API_KEY="your_google_gemini_key"
```

## Hướng Dẫn Sử Dụng

### Bước 1: Thu Thập và Xử Lý Dữ Liệu (`Crawl.py`)
Chạy script này để bắt đầu quy trình thu thập và xử lý dữ liệu. Script sẽ đọc danh sách URL, lấy nội dung Markdown thô và chuyển đổi thành văn bản có cấu trúc.
```bash
python Crawl.py
```
Kết quả sẽ được lưu trong thư mục `output/` với tên file có tiền tố `kq_`.

### Bước 2: Đánh Giá Chất Lượng Đầu Ra (`evaluate.py`)
Sau khi có dữ liệu trong thư mục `output/`, chạy script này để kiểm tra xem các file văn bản có đáp ứng đủ các trường thông tin yêu cầu hay không.
```bash
python evaluate.py
```
Hệ thống sẽ báo cáo:
*   **score/total**: Số lượng tiêu chí đạt được.
*   **missing**: Các mục thông tin bị thiếu (nếu có).

### Bước 3: Gộp File Báo Cáo (`merge_txt_files.py`)
Sử dụng script này nếu muốn gộp nhiều file kết quả vào một file duy nhất để lưu trữ hoặc in ấn.
```bash
python merge_txt_files.py
```

## Tùy Biến Nâng Cao

### Tùy Biến Nguồn Dữ Liệu (URL)
Mở file `Crawl.py`, tìm đến biến `my_urls` ở cuối file (trong khối `if __name__ == "__main__":`).
Thêm hoặc xóa các đường dẫn cần xử lý trong danh sách này.
```python
my_urls = [
    "https://example.com/link-moi-1",
    "https://example.com/link-moi-2",
]
```

### Tùy Biến Cấu Trúc Dữ Liệu Đầu Ra (Prompt Engineering)
Để thay đổi định dạng hoặc nội dung thông tin muốn trích xuất, cần sửa biến `final_prompt` trong hàm `process_list_urls` tại `Crawl.py`.

**Lưu ý**: Nếu thay đổi cấu trúc trong Prompt (ví dụ: đổi tên mục "I. THÔNG TIN ĐỊNH DANH" thành "A. THÔNG TIN CƠ BẢN"), bạn bắt buộc phải cập nhật lại bộ tiêu chí đánh giá trong `evaluate.py`.

### Tùy Biến Tiêu Chí Đánh Giá (Evaluation Criteria)
Mở file `evaluate.py`, chỉnh sửa danh sách `CRITERIA_LIST`. Đây là danh sách các Tuple chứa `(Regex Pattern, Tên hiển thị)`.
Ví dụ: Nếu muốn thêm tiêu chí kiểm tra xem có mục "Lời khuyên" hay không:
```python
CRITERIA_LIST = [
    # ... các tiêu chí cũ
    (r"Lời khuyên:", "Mục Lời khuyên"), # Thêm dòng này
]
```

### Tùy Biến Logic Retry và Rate Limit
Trong `Crawl.py`, các tham số sau có thể được điều chỉnh:
*   `max_retries = 3`: Số lần thử lại tối đa nếu gặp lỗi mạng.
*   `retry_delay = 5`: Thời gian chờ (giây) trước khi thử lại.
*   `time.sleep(5)` (cuối vòng lặp): Thời gian nghỉ giữa các lần xử lý URL khác nhau để tránh bị chặn IP hoặc quá tải API.