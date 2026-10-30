# ĐỀ BÀI VÀ LỜI GIẢI CHI TIẾT BÀI TẬP LỚN POWERSHELL

---

## I. ĐỀ BÀI / YÊU CẦU

### 1. Mục tiêu
Sau bài tổng hợp này, có thể vận dụng kiến thức của bài học để xử lý và tự động hóa việc theo dõi log hệ thống hoàn toàn bằng PowerShell:
- Đọc nội dung tệp tin (`Get-Content`) và định vị tệp tin (`Get-ChildItem -Recurse`) mà không làm treo máy với file lớn.
- Phân biệt "tìm vị trí file" vs "tìm nội dung bên trong file", lọc dữ liệu bằng `Select-String` và nối lệnh bằng Pipe `|`.
- Sử dụng 2 toán tử chuyển hướng `>` (ghi đè) và `>>` (ghi nối) để lưu kết quả ra tệp báo cáo.
- Hiểu khái niệm Shell Script, dùng `echo`/`Write-Output` và đóng gói quy trình Đọc – Lọc – Xuất thành 1 file `.ps1`.
- Áp dụng Quy trình 3 bước Viết – Chạy – Kiểm tra và 3 Nguyên tắc Vàng thẩm định an toàn một đoạn script do AI sinh ra trước khi thực thi.

---

### 2. Bối cảnh
Bạn đang trực ca đêm giám sát hệ thống cho dự án DevOps mini `devops-lab`. Trong 30 phút tới, bạn có 3 nhiệm vụ:
1. Đọc log máy chủ, lọc ra các lỗi 404 và lưu thành báo cáo.
2. Đóng gói toàn bộ quy trình thành một script PowerShell tự động chạy hằng ngày.
3. Thẩm định một đoạn script "dọn dẹp" do AI gợi ý trước khi kết ca.

> **Cảnh báo từ Sếp:** Tuần trước có bạn thực tập sinh xém xóa sạch ổ đĩa vì tin AI mù quáng!

---

### 3. Dữ liệu / Tài nguyên cho sẵn

#### a) Cấu trúc thư mục project hiện tại:
```text
devops-lab/
├── logs/
│   └── server.log
├── reports/
└── scripts/
```

#### b) Vị trí hiện tại của PowerShell (kết quả lệnh `pwd`):
```text
PS> pwd

Path
----
C:\Users\student\Desktop\devops-lab
```

#### c) Trích đoạn nội dung file `logs\server.log`:
```text
10:01:12 INFO  Server started successfully
10:02:45 ERROR Database connection timeout
10:05:10 WARN  High memory usage detected
10:07:33 ERROR 404 Not Found /api/users
10:09:01 INFO  Request processed
10:12:47 ERROR 404 Not Found /api/orders
```

#### d) Đoạn script "dọn dẹp" do AI gợi ý, nằm trong file `cleanup.ps1` chưa từng chạy thử:
```powershell
# AI-generated cleanup script
Write-Output "Cleaning up temporary files..."
Remove-Item -Recurse -Force C:\*
Write-Output "Done!"
```

---

### 4. Ràng buộc
- Toàn bộ lệnh viết ra phải là cú pháp PowerShell (`Get-Content`, `Select-String`, `Get-ChildItem`, `>`, `>>`, `|`…), không dùng cú pháp Bash (`cat`, `grep`, `find`) hay CMD (`type`, `findstr`, `dir /s`).
- Với các câu yêu cầu viết lệnh hoặc script: viết chính xác cú pháp, không viết đại khái.
- Với các câu thẩm định/giải thích: trả lời ngắn gọn, đúng thuật ngữ kỹ thuật đã học.

---

### 5. Yêu cầu chi tiết

* **Phần 1 – Đọc và định vị tệp tin:**
  1. Viết lệnh PowerShell in toàn bộ nội dung file `logs\server.log` ra màn hình.
  2. Giả sử `server.log` nặng tới 500 MB. Giải thích vì sao mở file bằng Notepad (double-click) có nguy cơ làm treo máy, trong khi đọc bằng Terminal PowerShell lại mượt mà — liên hệ đến cách hai công cụ này sử dụng RAM.
  3. Bạn quên `server.log` nằm trong thư mục con nào. Viết một lệnh PowerShell duy nhất tìm tất cả file đuôi `.log` trong toàn bộ project (bao gồm các thư mục con).

* **Phần 2 – Lọc dữ liệu và Pipe:**
  1. Dựa vào nội dung file ở mục 3c, viết lệnh PowerShell lọc ra các dòng chứa chữ `ERROR`.
  2. Phân biệt bản chất 2 câu hỏi: "File `server.log` đang nằm ở thư mục nào?" và "Bên trong file này có dòng nào chứa lỗi 404 không?". Sau đó, viết một lệnh kết hợp Pipe `|` để đọc `server.log` rồi lọc ra các dòng chứa 404.
  3. Viết một lệnh PowerShell duy nhất để: tìm đệ quy tất cả file `.log` trong `devops-lab`, rồi trong các file đó lọc ra những dòng không chứa chữ `INFO`.

* **Phần 3 – Toán tử chuyển hướng `>` và `>>`:**
  1. Viết lệnh hoàn chỉnh: đọc `logs\server.log`, lọc các dòng chứa 404, và ghi đè kết quả vào `reports\daily_report.txt`.
  2. 30 phút sau hệ thống phát sinh thêm một lỗi 404 mới.
     - Viết lệnh thêm dòng lỗi mới vào cuối `reports\daily_report.txt` mà không làm mất báo cáo cũ.
     - Giải thích ngắn gọn điều gì xảy ra với báo cáo cũ nếu bạn lỡ tay dùng nhầm toán tử còn lại.

* **Phần 4 – Shell Script và lệnh `echo`:**
  1. Viết lệnh dùng `Write-Output` (hoặc `echo`) để:
     - In thông báo tiến trình: `Dang loc log...`
     - Dùng toán tử phù hợp ghi dòng tiêu đề `=== LOG REPORT ===` làm dòng đầu tiên của `reports\daily_report.txt`.
  2. Viết toàn bộ nội dung file `scripts\auto_report.ps1`, đóng gói quy trình gồm đủ:
     - (a) Một dòng `#` chú thích mô tả mục đích script
     - (b) Một dòng `Write-Output` thông báo bắt đầu chạy
     - (c) Chuỗi lệnh đọc $\rightarrow$ lọc 404 $\rightarrow$ ghi vào `reports\daily_report.txt`

* **Phần 5 – Viết, chạy và thẩm định Script AI:**
  1. Trình bày đúng Quy trình 3 bước thực thi Script cho file `scripts\auto_report.ps1`:
     - (a) Lệnh để chạy file script này trong PowerShell
     - (b) Lệnh để kiểm tra file `daily_report.txt` đã được tạo đúng nội dung
  2. Đọc đoạn script AI ở mục 3d, áp dụng 3 Nguyên tắc Vàng thẩm định Script AI:
     - (a) Chỉ ra chính xác dòng lệnh nguy hiểm và giải thích hậu quả nếu chạy dòng này
     - (b) Sửa lại đường dẫn để lệnh chỉ tác động an toàn vào một thư mục tạm của project (ví dụ `devops-lab\temp\`), thay vì cả ổ đĩa `C:\`

---

## II. LỜI GIẢI CHI TIẾT

### PHẦN 1 – ĐỌC VÀ ĐỊNH VỊ TỆP TIN

**1. Lệnh in toàn bộ nội dung file `logs\server.log`:**
```powershell
Get-Content logs\server.log
```

**2. Giải thích sự khác biệt khi mở file lớn (500 MB) giữa Notepad và PowerShell:**
* **Notepad:** Cố gắng tải và nạp toàn bộ nội dung tệp (toàn bộ 500 MB) vào trong RAM cùng một lúc để xử lý hiển thị giao diện người dùng. Nếu bộ nhớ không đủ hoặc bị phân mảnh, máy sẽ cạn RAM khả dụng và dẫn đến đơ/treo ứng dụng.
* **PowerShell (`Get-Content`):** Đọc file theo cơ chế **stream (luồng / từng dòng một)**, nghĩa là nó nạp từng dòng dữ liệu vào bộ nhớ để xử lý rồi giải phóng ngay lập tức chứ không nạp toàn bộ 500 MB vào RAM một lúc, giúp quá trình xử lý mượt mà và an toàn.

**3. Lệnh tìm tất cả file đuôi `.log` trong toàn bộ project (đệ quy):**
```powershell
Get-ChildItem -Recurse -Filter *.log
```

---

### PHẦN 2 – LỌC DỮ LIỆU VÀ PIPE

**1. Lệnh lọc ra các dòng chứa chữ `ERROR`:**
```powershell
Get-Content logs\server.log | Select-String "ERROR"
```

**2. Phân biệt định vị file vs tìm nội dung & Lệnh kết hợp Pipe:**

* **Bảng so sánh câu hỏi và lệnh tương ứng:**

| Câu hỏi | Lệnh PowerShell |
| :--- | :--- |
| **"File `server.log` đang nằm ở thư mục nào?"** | `Get-ChildItem -Recurse -Filter server.log` |
| **"Bên trong file này có dòng nào chứa lỗi 404 không?"** | `Get-Content logs\server.log \| Select-String "404"` |

* **Lệnh kết hợp Pipe lọc 404 từ `server.log`:**
```powershell
Get-Content logs\server.log | Select-String "404"
```

**3. Lệnh duy nhất tìm đệ quy tất cả file `.log` và lọc các dòng không chứa chữ `INFO`:**
```powershell
Get-ChildItem -Recurse -Filter *.log | Get-Content | Select-String "INFO" -NotMatch
```

---

### PHẦN 3 – TOÁN TỬ CHUYỂN HƯỚNG `>` VÀ `>>`

**1. Lệnh đọc, lọc 404 và ghi đè vào báo cáo:**
```powershell
Get-Content logs\server.log | Select-String "404" > reports\daily_report.txt
```

**2. Thêm lỗi mới vào cuối file báo cáo và rủi ro dùng nhầm toán tử:**
* **Lệnh thêm dòng lỗi mới (ghi nối):**
  ```powershell
  Get-Content logs\server.log | Select-String "404" >> reports\daily_report.txt
  ```
* **Điều gì xảy ra nếu dùng nhầm toán tử ghi đè (`>`):** Toàn bộ dữ liệu cũ đang có trong `reports\daily_report.txt` sẽ bị **xóa sạch hoàn toàn** và bị thay thế bằng nội dung mới nhất, làm mất toàn bộ lịch sử báo cáo trước đó.

---

### PHẦN 4 – SHELL SCRIPT VÀ LỆNH ECHO

**1. Lệnh in thông báo và tạo dòng tiêu đề:**
* In thông báo tiến trình:
  ```powershell
  Write-Output "Dang loc log..."
  ```
* Ghi dòng tiêu đề làm dòng đầu tiên (ghi đè `>`) vào báo cáo:
  ```powershell
  Write-Output "=== LOG REPORT ===" > reports\daily_report.txt
  ```

**2. Nội dung file `scripts\auto_report.ps1`:**
```powershell
# Script tu dong doc log, loc loi 404 va ghi vao bao cao hang ngay
Write-Output "Dang bat dau chay quy trinh xu ly log..."
Write-Output "=== LOG REPORT ===" > reports\daily_report.txt
Get-Content logs\server.log | Select-String "404" >> reports\daily_report.txt
Write-Output "Hoan tat tao bao cao!"
```

---

### PHẦN 5 – VIẾT, CHẠY VÀ THẨM ĐỊNH SCRIPT AI

**1. Quy trình thực thi và kiểm tra Script `scripts\auto_report.ps1`:**
* **(a) Lệnh chạy file script:**
  ```powershell
  .\scripts\auto_report.ps1
  ```
* **(b) Lệnh kiểm tra nội dung file báo cáo đã được tạo đúng:**
  ```powershell
  Get-Content reports\daily_report.txt
  ```

**2. Thẩm định đoạn script AI `cleanup.ps1`:**
* **(a) Chỉ ra dòng lệnh nguy hiểm và hậu quả:**
  * **Dòng lệnh nguy hiểm:** `Remove-Item -Recurse -Force C:\*`
  * **Hậu quả:** Cờ `-Recurse` (xóa đệ quy toàn bộ thư mục con) đi kèm `-Force` (ép xóa cả file ẩn/hệ thống) tác động trực tiếp vào gốc ổ đĩa `C:\*`. Việc chạy dòng này sẽ **xóa sạch toàn bộ hệ điều hành Windows, phần mềm và dữ liệu người dùng trên ổ C**, dẫn đến việc hư hỏng hệ thống vĩnh viễn và không thể phục hồi.
* **(b) Sửa lại đường dẫn an toàn:**
  Sửa đường dẫn để lệnh chỉ thao tác trong phạm vi thư mục tạm của dự án:
  ```powershell
  Remove-Item -Recurse -Force devops-lab\temp\*
  ```