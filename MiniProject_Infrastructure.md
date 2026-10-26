# BÁO CÁO MINI PROJECT: HỆ THỐNG QUẢN LÝ HẠ TẦNG GREENTECH


## PHẦN 1: PHÂN TÍCH VÀ THIẾT KẾ KIẾN TRÚC HỆ THỐNG

### 1. Mô hình IPO và tư duy hệ thống DIKW trong GreenTech

* **IPO (Input - Process - Output):**
* **Input:** Hàng triệu dòng tệp nhật ký thô (`log-he-thong.log`) từ 100 trạm cảm biến IoT ngoài trang trại và cơ sở dữ liệu phần cứng (`quan-ly-thiet-bi.xlsx`).
* **Process:** Lọc tự động các mã lỗi nghiêm trọng (`CRITICAL`, `ERROR`) bằng Shell Script/Batch, đối soát thông số linh kiện phần cứng qua hàm `VLOOKUP` kết hợp `TRIM`.
* **Output:** Báo cáo sự cố (`bao-cao-su-co.txt`), bảng đối soát tài sản sạch sẽ và tài liệu vận hành (`README.md`, `srs-he-thong.docx`).


* **DIKW (Data - Information - Knowledge - Wisdom):**
* *Data:* Các dòng log thô chứa mã ký tự và thông số kỹ thuật rời rạc.
* *Information:* Nhật ký sau khi lọc sự cố nguy hiểm và bảng đối soát linh kiện có cấu trúc.
* *Knowledge:* Xu hướng lỗi phần cứng tại các trạm cảm biến theo thời gian thực.
* *Wisdom:* Đưa ra quyết định bảo trì dự phòng, thay thế linh kiện trước khi trạm cảm biến sập nguồn.



### 2. Thiết kế Cây thư mục dự án (kebab-case chuẩn DevOps)

```text
greentech-system/
├── docs/
│   ├── README.md
│   └── srs-he-thong.docx
├── scripts/
│   └── auto_report.bat
├── data/
│   ├── log-he-thong.log
│   └── quan-ly-thiet-bi.xlsx
├── backup/
└── .env

```

---

## PHẦN 2: TRIỂN KHAI CHI TIẾT CÁC MODULE CHỨC NĂNG

### Module 1: Thiết lập "Căn cứ số" (Terminal)

Để khởi tạo toàn bộ cấu trúc cây thư mục lồng nhau và tạo tệp cấu hình ẩn `.env` bằng lệnh Relative Path trên Windows (Command Prompt / PowerShell), sử dụng câu lệnh dòng lệnh duy nhất sau:

```cmd
mkdir docs scripts data backup && echo DB_PASS=GreenTechSecure2026# > .env && attrib +h .env

```

*Giải thích:* Lệnh `mkdir` tạo đồng thời 4 thư mục chính, sau đó dùng toán tử `&&` để tạo tệp bảo mật ẩn `.env` chứa mật khẩu cấu hình hệ thống và ẩn tệp (`attrib +h`).

---

### Module 2: Robot lọc Log tự động (`auto_report.bat`)

Tệp script `scripts/auto_report.bat` được thiết kế để xử lý tệp log lớn (500MB) mà không gây tràn RAM bằng cách sử dụng Đường ống (Pipe `|`) kết hợp bộ lọc dòng hiệu năng cao:

```batch
@echo off
chcp 65001 > nul
:: --- BƯỚC 1: KIỂM TRA & AUDIT AN TOÀN ---
echo [AUDIT] Dang tien hành kiem tra he thong va thiet lap duong dan...
set "LOG_FILE=..\data\log-he-thong.log"
set "OUT_DIR=..\backup"
set "OUT_FILE=%OUT_DIR%\bao-cao-su-co.txt"

if not exist "%LOG_FILE%" (
    echo [LOI] Khong tim thay tep log nguon tai %LOG_FILE%!
    pause
    exit /b 1
)

:: --- BƯỚC 2: TẠO TIÊU ĐỀ BÁO CÁO ---
echo ======================================================== > "%OUT_FILE%"
echo       BAO CAO SU CO HE THONG GREENTECH - IOT INFRASTRUCTURE >> "%OUT_FILE%"
echo       Thoi gian tao: %DATE% %TIME% >> "%OUT_FILE%"
echo ======================================================== >> "%OUT_FILE%"
echo. >> "%OUT_FILE%"

:: --- BƯỚC 3: LỌC LOG BẰNG PIPE VÀ FINDSTR (CHỐNG TRÀN RAM) ---
echo [INFO] Dang loc cac loi CRITICAL va ERROR tu tep log...
findstr /i "CRITICAL ERROR" "%LOG_FILE%" >> "%OUT_FILE%"

echo. >> "%OUT_FILE%"
echo [INFO] Hoan tat trich xuat bao cao su co vao thu muc backup!
pause

```

* **Bước Audit (Thẩm định an toàn):** Script có cơ chế kiểm tra sự tồn tại của tệp nguồn trước khi xử lý, tuyệt đối tránh dùng các lệnh xóa mạo hiểm (`del /f /s /q`) nhằm bảo vệ dữ liệu log gốc của doanh nghiệp.

---

### Module 3: Tài liệu hướng dẫn vận hành (`README.md` & SRS Word)

#### 1. Nội dung tệp `docs/README.md`

```markdown
# Hệ thống Quản lý Hạ tầng GreenTech IoT

## 1. Giới thiệu hệ thống
Hệ thống giám sát và quản lý hạ tầng trạm cảm biến IoT phục vụ nông nghiệp thông minh, xử lý hàng triệu bản ghi nhật ký mỗi ngày và quản lý danh mục thiết bị phần cứng tập trung.

## 2. Hướng dẫn chạy Script Tự động hóa Log
Để khởi chạy robot tự động lọc lỗi hệ thống, mở Terminal tại thư mục `scripts/` và chạy lệnh:
```bash
auto_report.bat

```

## 3. Checklist kiểm tra thiết bị định kỳ (Task List)

* [ ] Kiểm tra dung lượng tệp nhật ký `log-he-thong.log` (Dưới 500MB).
* [ ] Xác thực kết quả báo cáo sự cố trong thư mục `backup/bao-cao-su-co.txt`.
* [ ] Đối soát mã linh kiện phần cứng trạm IoT qua tệp `quan-ly-thiet-bi.xlsx`.
* [ ] Kiểm tra kết nối mạng và băng thông truyền tải của 100 trạm cảm biến.

## 4. Bảng thông số phần cứng tiêu chuẩn trạm IoT

| Mã Trạm | Vi điều khiển (CPU) | Bộ nhớ (RAM) | Lưu trữ (Storage) | Tình trạng |
| --- | --- | --- | --- | --- |
| TRAM-01 | ARM Cortex-M4 | 8GB | 256GB SSD | Hoạt động |
| TRAM-02 | ESP32 Dual-Core | 4GB | 128GB SSD | Hoạt động |
| TRAM-03 | Raspberry Pi 4 | 8GB | 512GB SSD | Bảo trì |

```

#### 2. SRS Document (`docs/srs-he-thong.docx`)
* **Heading 1:** 1. Tổng quan hệ thống GreenTech
* **Heading 2:** 1.1 Mục tiêu kỹ thuật, 1.2 Phạm vi quản lý
* **Heading 3:** 1.1.1 Yêu cầu phần cứng, 1.1.2 Yêu cầu phần mềm
* **Auto TOC:** Được đặt ở trang đầu tiên của tài liệu Word sử dụng trường mục lục tự động (Table of Contents) quét toàn bộ các cấp Heading.

---

### Module 4: Đối soát tài nguyên phần cứng (Excel Asset Database)

Tệp `data/quan-ly-thiet-bi.xlsx` được thiết kế chuẩn Database Mindset (Không Merge Cell, không có hàng/cột trống ngắt quãng, dòng tổng kết đặt ngoài bảng):

#### 1. Cấu trúc Sheet `danh-muc-linh-kien`
| ma_linh_kien (Khóa chính) | ten_thiet_bi | don_gia | loai_storage |
| :--- | :--- | :--- | :--- |
| LK-SSD-256 | Ổ cứng SSD 256GB NVMe | 1200000 | SSD |
| LK-RAM-08G | Thanh RAM 8GB DDR4 | 850000 | HDD/RAM |
| LK-CPU-ARM | Vi điều khiển ARM Cortex | 2400000 | CPU |

#### 2. Cấu trúc Sheet `trang-thai-tram` (Tích hợp VLOOKUP đối soát)
| ma_tram | ma_linh_kien (Khóa ngoại) | dung_luong_ram | ngay_lap_dat | ten_thiet_bi (Cột tự động điền) |
| :--- | :--- | :--- | :--- | :--- |
| TR-01 | LK-SSD-256 | 8GB | 2026-01-15 | *[Công thức VLOOKUP]* |
| TR-02 | LK-RAM-08G | 4GB | 2026-01-20 | *[Công thức VLOOKUP]* |

* **Công thức VLOOKUP hoàn chỉnh (tại ô chứa `ten_thiet_bi` của dòng 2):**
  ```excel
  =IFERROR(VLOOKUP(TRIM(B2), 'danh-muc-linh-kien'!$A$2:$D$10, 2, FALSE), "Linh kiện không tồn tại")

```

* **Giải thích các tham số & chống lỗi:**
* `TRIM(B2)`: Làm sạch khoảng trắng thừa vô hình ở mã linh kiện trạm, chống triệt để lỗi `#N/A`.
* `'danh-muc-linh-kien'!$A$2:$D$10`: Vùng dữ liệu tham chiếu được cố định tuyệt đối bằng dấu đô la (`$`) nhằm tránh trôi vùng dữ liệu khi kéo công thức xuống hàng loạt.
* `2`: Trả về giá trị ở cột thứ 2 (`ten_thiet_bi`) trong bảng danh mục linh kiện.
* `FALSE`: Tìm kiếm chính xác tuyệt đối (Exact Match).
* `IFERROR`: Bẫy lỗi trả về chuỗi thông báo thân thiện nếu mã linh kiện không khớp trong hệ thống.



---

## PHẦN 3: CẤU TRÚC ĐÓNG GÓI GITHUB REPOSITORY

Toàn bộ mã nguồn và tệp tin của dự án được tổ chức và đẩy lên kho lưu trữ GitHub theo cấu trúc chuẩn mực sau:

```text
[TênLớp]_[MônHọc]_MiniProject_GreenTech/
├── docs/
│   ├── README.md
│   └── srs-he-thong.docx
├── scripts/
│   └── auto_report.bat
├── data/
│   ├── log-he-thong.log
│   └── quan-ly-thiet-bi.xlsx
└── .env

```