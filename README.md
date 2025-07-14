# Dự án BadUSB v6.1.3

## Giới thiệu
Dự án này triển khai một cuộc tấn công BadUSB sử dụng phần cứng Arduino để cung cấp và thực thi payload trên hệ thống Windows. Thiết bị giả dạng như một bàn phím USB thông thường để thực hiện các lệnh tự động, vượt qua các biện pháp bảo mật và thiết lập quyền truy cập liên tục vào hệ thống mục tiêu.

## Yêu cầu cài đặt
### Phần cứng
- Arduino hỗ trợ chế độ HID (Leonardo, Micro, Pro Micro, v.v.)
- Cáp USB để kết nối Arduino với máy tính mục tiêu

### Phần mềm
- Arduino IDE để biên dịch và nạp mã vào Arduino
- Python 3.8+ trên máy tính thực hiện biên dịch payload (main.exe)
- Tài khoản Telegram và Bot Telegram API key cho việc điều khiển từ xa

### Thư viện Python
```
mss                  # Chụp màn hình
requests             # Thực hiện HTTP requests
psutil               # Theo dõi tiến trình
netifaces            # Lấy thông tin mạng
pynput               # Keylogger
cryptography         # Giải mã mật khẩu
pywin32              # Tương tác với Windows API
opencv-python        # Chụp ảnh từ webcam
python-telegram-bot  # Giao tiếp với Telegram
websocket-client     # Thu thập cookies trình duyệt
```

## Chức năng chính
BadUSB thực hiện một chuỗi các hoạt động xâm nhập và thu thập thông tin:

### 1. Tấn công ban đầu (Arduino)
- Mở PowerShell với quyền quản trị viên (Administrator)
- Thêm ổ đĩa C vào danh sách loại trừ của Windows Security
- Tạo script kiểm tra kết nối internet và tải xuống payload
- Kiểm tra và chờ đợi kết nối internet nếu không có
- Tải xuống payload chính (main.exe) từ một kho lưu trữ trực tuyến
- Thực thi payload với quyền quản trị viên
- Tạo tác vụ định kỳ để tự động chạy lại payload khi khởi động hệ thống 
- Xóa lịch sử PowerShell để ẩn dấu vết

### 2. Thu thập thông tin (Python)
- **Thu thập dữ liệu trình duyệt** - Lấy mật khẩu đã lưu và cookies từ Chrome/Edge
- **Chụp ảnh màn hình** - Chụp và gửi ảnh màn hình hiện tại của nạn nhân
- **Chụp ảnh từ webcam** - Chụp ảnh từ webcam với các tùy chọn nâng cao và độ phân giải cao
- **Thu thập thông tin hệ thống** - OS, phần cứng, CPU, RAM, username
- **Thu thập thông tin mạng** - Địa chỉ IP và giao diện mạng

### 3. Thiết lập quyền truy cập liên tục
- **Tạo tài khoản Administrator ẩn** - Tạo tài khoản quản trị viên ẩn không hiển thị trong màn hình đăng nhập
- **Kích hoạt Remote Desktop (RDP)** - Cho phép kết nối từ xa qua RDP
- **Tạo tác vụ khởi động** - Đảm bảo payload sẽ chạy lại mỗi khi hệ thống khởi động
- **Đa dạng phương pháp persistence** - Task Scheduler + Registry để đảm bảo tính bền vững

### 4. Giám sát và kiểm soát (Telegram)
- **Reverse Shell qua Telegram** - Cho phép điều khiển từ xa thông qua bot Telegram
- **Quản lý nhiều nạn nhân** - Lưu trữ, chọn và quản lý nhiều máy tính nạn nhân
- **Keylogger** - Ghi lại tất cả các phím nhấn và gửi định kỳ qua Telegram (mỗi 60 giây)
- **Remote Command & Control** - Thực thi các lệnh từ xa trên máy nạn nhân

### 5. Tránh phát hiện
- **Vô hiệu hóa phần mềm bảo mật** - Tắt Windows Defender và các phần mềm antivirus
- **Xóa dấu vết** - Xóa nhật ký hệ thống, lịch sử PowerShell và các dấu vết kết nối
- **Ẩn tệp** - Đặt thuộc tính ẩn cho các tệp quan trọng
- **Cơ chế offline** - Duy trì hàng đợi tin nhắn/tệp khi không có kết nối Internet

## Chi tiết kỹ thuật

### Thành phần Arduino
Vi điều khiển Arduino giả lập một bàn phím USB để thực hiện một chuỗi lệnh tự động. Mã chính được lưu trong file `code_Arduino.txt` và cần được nạp vào thiết bị Arduino thông qua Arduino IDE.

```
#include <Keyboard.h>
void setup() {
  // Khởi tạo
  // Mở PowerShell với quyền admin
  // Thêm exclusions
  // Tạo script kiểm tra kết nối và tải payload
  // Thực thi script
  // Xóa lịch sử
}
```

### Cấu trúc Payload Python
Payload chính bao gồm các module Python làm việc cùng nhau:
- **main.py** - Điều phối chuỗi tấn công và xử lý việc trích xuất dữ liệu
- **telegram_shell.py** - Triển khai reverse shell qua Telegram
- **send_telegram.py** - Xử lý gửi tin nhắn và tệp qua Telegram với hỗ trợ hàng đợi offline
- **powershell_utils.py** - Cung cấp tiện ích thực thi lệnh PowerShell
- **clean_up.py** - Xóa nhật ký và dấu vết
- **browser_data.py** - Trích xuất dữ liệu trình duyệt (mật khẩu và cookies)
- **create_admin_account.py** - Tạo tài khoản quản trị viên ẩn
- **kill_security.py** - Vô hiệu hóa dịch vụ bảo mật
- **keylogger.py** - Ghi lại phím nhấn
- **screenshot.py** - Chụp màn hình
- **webcam.py** - Chụp ảnh từ webcam với nhiều tùy chọn và chất lượng cao
- **system_info.py** - Thu thập thông tin hệ thống
- **network_info.py** - Thu thập thông tin mạng
- **utils.py** - Công cụ tiện ích
- **hide_files.py** - Ẩn tệp khỏi người dùng

Payload cần được biên dịch thành file thực thi (.exe) trước khi sử dụng và triển khai.

## Tính năng nổi bật

### Reverse Shell qua Telegram
Tính năng này cho phép tin tặc kiểm soát hoàn toàn máy nạn nhân từ xa thông qua ứng dụng Telegram:

- **Quản lý nhiều nạn nhân** - Lưu trữ và quản lý thông tin từ nhiều máy tính
- **Giao diện dòng lệnh** - Thực thi lệnh từ xa tương tự cmd/PowerShell
- **Tải lên/Tải xuống tệp** - Di chuyển tệp giữa máy nạn nhân và tin tặc
- **Chụp màn hình theo yêu cầu** - Quan sát màn hình nạn nhân bất cứ lúc nào
- **Chụp ảnh webcam** - Chụp ảnh từ webcam của nạn nhân với các tùy chọn nâng cao
- **Quản lý tiến trình** - Xem và kết thúc các tiến trình đang chạy
- **Bảo vệ phiên làm việc** - Chỉ cho phép chat_id đã được ủy quyền
- **Chế độ offline** - Tự động lưu hàng đợi tin nhắn/tệp khi mất kết nối Internet

#### Lệnh quản lý nạn nhân:
- `/victims` - Liệt kê tất cả nạn nhân đã kết nối
- `/select [ID]` - Chọn một nạn nhân để điều khiển
- `/status` - Hiển thị trạng thái của nạn nhân hiện tại

#### Lệnh shell và thu thập thông tin:
- `/exec [lệnh]` - Thực thi lệnh hệ thống
- `/upload [đường_dẫn]` - Tải tệp từ máy nạn nhân lên Telegram
- `/download [URL] [đường_dẫn]` - Tải tệp từ URL vào máy nạn nhân
- `/screenshot` - Chụp và gửi ảnh màn hình
- `/webcam` - Chụp ảnh từ webcam (tùy chọn: led-off, multi)
- `/info` - Hiển thị thông tin hệ thống chi tiết
- `/processes` - Liệt kê các tiến trình đang chạy
- `/kill [tên_tiến_trình/PID]` - Kết thúc tiến trình
- `/persist` - Đảm bảo tính bền vững của backdoor
- `/help` - Hiển thị danh sách lệnh

#### Tính năng chụp ảnh Webcam nâng cao
Chức năng này cho phép chụp và gửi ảnh từ webcam của nạn nhân với các tùy chọn được thiết kế để tối ưu hóa chất lượng và tính bí mật:

- **Độ phân giải Full HD** - Chụp ảnh với độ phân giải 1920x1080 để có chi tiết rõ nét
- **Chế độ tắt đèn LED (led-off)** - Tắt đèn chỉ báo webcam để tránh bị phát hiện, nhưng có thể làm giảm chất lượng ảnh trong điều kiện ánh sáng yếu
- **Chế độ nhiều frame (multi)** - Chụp nhiều frame liên tiếp (5-10 frame) và tự động chọn ảnh rõ nét nhất, giúp tránh vấn đề hình ảnh mờ hoặc nhiễu
- **Xử lý hình ảnh nâng cao** - Tự động tăng độ nét, cân bằng màu sắc và cải thiện độ tương phản
- **Xử lý thông minh** - Tự động xóa ảnh sau khi gửi để không để lại dấu vết
- **Cảnh báo module thiếu** - Tự động thông báo nếu thiếu module OpenCV để hỗ trợ việc cài đặt thêm

Ví dụ sử dụng:
```
/webcam             # Chụp ảnh webcam bình thường chất lượng cao
/webcam led-off     # Chụp ảnh mà không bật đèn LED (ẩn)
/webcam multi       # Chụp nhiều frame để có ảnh chất lượng tốt nhất
/webcam led-off multi  # Kết hợp cả hai tùy chọn
```

#### ID Nạn nhân và Cơ chế theo dõi cố định
Tính năng này giúp xác định và quản lý các nạn nhân một cách nhất quán, ngay cả khi họ khởi động lại máy tính:

- **ID cố định dựa trên phần cứng** - Tạo ID duy nhất dựa trên thông tin phần cứng cố định (serial number mainboard, CPU, disk)
- **Theo dõi không đổi** - ID nạn nhân không thay đổi qua các lần khởi động lại
- **Thông báo kết nối không trùng lặp** - Khi nạn nhân kết nối, thông báo chỉ được gửi một lần
- **Quản lý trạng thái** - Bot lưu trữ và theo dõi trạng thái trực tuyến/ngoại tuyến của tất cả các nạn nhân

Tính năng này đặc biệt hữu ích để:
- Theo dõi lâu dài các mục tiêu cụ thể
- Tránh tin nhắn thông báo dư thừa
- Nhận biết chính xác sự trở lại của nạn nhân sau khi ngắt kết nối

#### Cơ chế xử lý khi không có internet
Hệ thống được thiết kế để hoạt động liền mạch ngay cả khi kết nối Internet bị gián đoạn:

- **Tạm dừng và chờ đợi khi tải payload** - Arduino kiểm tra kết nối và chờ internet trước khi tải payload
- **Tác vụ kiểm tra định kỳ** - Lên lịch kiểm tra và tải payload khi có internet trở lại
- **Hàng đợi thông minh** - Tự động phát hiện khi không có kết nối và lưu tin nhắn/tệp vào hàng đợi
- **Thư mục lưu trữ ẩn** - Sử dụng thư mục ẩn trong %TEMP% để lưu trữ dữ liệu trong hàng đợi
- **Tự động thử lại** - Khi kết nối được khôi phục, tự động thử gửi lại các tin nhắn và tệp theo thứ tự thời gian
- **Thread riêng biệt** - Sử dụng thread riêng để xử lý hàng đợi, không ảnh hưởng đến hoạt động chính
- **Thứ tự ưu tiên** - Tin nhắn văn bản được ưu tiên gửi trước tệp để đảm bảo thông tin quan trọng được truyền đi sớm nhất

Chức năng này vô cùng hữu ích khi:
- Nạn nhân di chuyển máy tính ra khỏi vùng phủ sóng WiFi
- Thiết bị Arduino được kết nối vào máy tính không có internet
- Kết nối Internet của nạn nhân không ổn định hoặc bị gián đoạn
- Nạn nhân tạm thời ngắt kết nối mạng (nhưng sau đó kết nối lại)

#### Tùy chọn nâng cao:
- **Webcam** - Hỗ trợ tắt đèn LED (led-off) và chụp nhiều frame để chọn ảnh rõ nét nhất (multi)
- **Kiểm tra lệnh nguy hiểm** - Tự động phát hiện và cảnh báo các lệnh có thể gây hại
- **Phát hiện tình trạng kết nối** - Tự động lưu trữ tin nhắn/tệp khi không có Internet
- **Quản lý phiên** - Ghi nhớ tất cả các nạn nhân đã kết nối, ngay cả khi Bot bị khởi động lại

### Keylogger nâng cao
- Hoạt động ẩn trong nền
- Tự động gửi dữ liệu bàn phím mỗi 60 giây 
- Ghi lại các phím đặc biệt
- Tự động xóa nhật ký phím sau khi gửi

### Khai thác trình duyệt
- **Giải mã mật khẩu đã lưu** - Giải mã AES cho mật khẩu Chrome/Edge
- **Trích xuất cookie** - Lấy cookie từ nhiều profile trình duyệt
- **Hỗ trợ nhiều profile** - Trích xuất dữ liệu từ tất cả profile người dùng

### Tạo tài khoản Administrator ẩn
- Tạo tài khoản quản trị viên bí mật "hiddenadmin" với mật khẩu "Passw0rd123!"
- Ẩn tài khoản khỏi màn hình đăng nhập Windows (qua registry)
- Tạo script truy cập nhanh vào tài khoản (không phải backdoor)
- Kích hoạt RDP (Remote Desktop) để chuẩn bị cho truy cập từ xa

#### Chi tiết và hạn chế
Tài khoản administrator ẩn chủ yếu phục vụ hai mục đích:

1. **Persistence (duy trì quyền truy cập)**: Đây là một biện pháp dự phòng. Nếu nạn nhân phát hiện và xóa payload chính (main.exe) hoặc các điểm xâm nhập ban đầu, tài khoản ẩn này vẫn tồn tại do khó phát hiện hơn. Tuy nhiên, nếu người dùng thực hiện lệnh `net user hiddenadmin /del`, tài khoản này sẽ bị xóa hoàn toàn khỏi hệ thống.

2. **Cơ chế sử dụng**:
   - **Thông qua Reverse Shell**: Tài khoản này có thể được sử dụng để thực thi lệnh qua shell Telegram
   - **Thông qua RDP**: Tài khoản có thể dùng để đăng nhập từ xa, nhưng cần lưu ý:
     - Máy nạn nhân cần được cấu hình NAT port 3389 ra internet
     - Nếu không có NAT, chỉ có thể truy cập trong mạng nội bộ
     - Cần biết IP công khai hoặc đường dẫn đến máy nạn nhân
   - **Script truy cập nhanh**: File batch `%APPDATA%\Microsoft\Windows\Backdoor.bat` được tạo ra để người có quyền truy cập vào máy nạn nhân có thể leo thang đặc quyền lên tài khoản admin (không phải backdoor thực sự)

3. **Khả năng sử dụng tài khoản admin ẩn**:
   - Cài đặt phần mềm với quyền cao
   - Thay đổi cấu hình hệ thống và chính sách bảo mật
   - Thiết lập các cơ chế backdoor bổ sung (SSH, WinRM, ...)
   - Tạo các tác vụ định kỳ chạy với quyền administrator
   - Truy cập vào dữ liệu của tất cả người dùng

#### Backdoor thực sự
Cơ chế backdoor thực sự trong dự án là **Reverse Shell qua Telegram**, cho phép điều khiển từ xa mà không phụ thuộc vào cấu hình mạng của nạn nhân hoặc NAT port.

### Cơ chế offline và xử lý kết nối đứt đoạn
BadUSB v6.1.3 bao gồm cơ chế xử lý thông minh khi gặp vấn đề về kết nối:

1. **Script Arduino thông minh** - Kiểm tra kết nối internet trước khi tải payload
2. **Tác vụ kiểm tra định kỳ** - Tạo tác vụ theo dõi kết nối và tiếp tục quá trình khi có internet
3. **Hàng đợi tin nhắn và tệp tin** - Lưu trữ tin nhắn và tệp trong hàng đợi khi không có kết nối Internet
4. **Thử lại tự động** - Tự động thử lại gửi tin nhắn và tệp khi kết nối được khôi phục
5. **Lưu trữ an toàn** - Tin nhắn và tệp được lưu trong thư mục ẩn để tránh bị phát hiện
6. **Xử lý ưu tiên** - Tin nhắn cảnh báo và thông tin quan trọng được ưu tiên gửi trước

## Khả năng xóa nhật ký
Chương trình xóa bốn loại nhật ký Windows quan trọng:

### 1. Nhật ký sự kiện Windows
**Các tệp bị ảnh hưởng:** Nhật ký `System`, `Security`, `Application` và `Windows PowerShell`
**Vị trí:** `C:\Windows\System32\winevt\Logs\`
**Thông tin lưu trữ:** Sự kiện hệ thống, kiểm tra bảo mật, lỗi ứng dụng và thực thi lệnh PowerShell
**Tác động của việc xóa:** Loại bỏ bằng chứng về kết nối thiết bị USB, leo thang đặc quyền, thực thi ứng dụng và các lệnh PowerShell được thực thi trong quá trình tấn công

### 2. Lịch sử PowerShell
**Các tệp bị ảnh hưởng:** Tệp lịch sử PSReadLine
**Vị trí:** `$env:USERPROFILE\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`
**Thông tin lưu trữ:** Các lệnh đã nhập trong phiên PowerShell
**Tác động của việc xóa:** Xóa bỏ bản ghi của tất cả các lệnh được thực thi qua PowerShell trong quá trình tấn công, bao gồm cả việc tải xuống và thực thi các payload độc hại

### 3. Lịch sử kết nối USB
**Vị trí registry:**
- `HKLM:\SYSTEM\CurrentControlSet\Enum\USBSTOR\*\*`
- `HKLM:\SYSTEM\CurrentControlSet\Control\DeviceClasses\*\*`
- `HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\MountPoints2\*`
**Thông tin lưu trữ:** Định danh thiết bị, dấu thời gian kết nối, chi tiết trình điều khiển cho thiết bị USB
**Tác động của việc xóa:** Loại bỏ bằng chứng về việc kết nối thiết bị BadUSB vào hệ thống

### 4. Tệp Prefetch
**Các tệp bị ảnh hưởng:** Tệp `.pf`
**Vị trí:** `C:\Windows\Prefetch\`
**Thông tin lưu trữ:** Thông tin về các ứng dụng đã được thực thi trên hệ thống
**Tác động của việc xóa:** Loại bỏ dấu vết của các chương trình đã thực thi, bao gồm cả payload độc hại

### 5. Dữ liệu bổ sung bị xóa
- **Danh sách tệp gần đây** - (`$env:USERPROFILE\AppData\Roaming\Microsoft\Windows\Recent\*.*`)
- **Thông tin đăng nhập đã lưu** - (`cmdkey /list`)
- **Registry UserAssist** - Chứa thông tin về ứng dụng đã chạy (`HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist\*`)
- **Registry RecentDocs** - Chứa đường dẫn tệp gần đây (`HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs\*`)

## Quy trình hoạt động
1. **Chuẩn bị** - Nạp mã vào Arduino và chuẩn bị payload
2. **Kết nối thiết bị BadUSB** - Cắm thiết bị vào máy tính mục tiêu
3. **Tự động thực thi mã Arduino** - Giả lập bàn phím để mở PowerShell với quyền admin
4. **Kiểm tra kết nối internet** - Arduino tạo script kiểm tra và chờ đợi kết nối internet
5. **Tải payload** - Khi có kết nối, script tải main.exe về máy nạn nhân
6. **Thiết lập tự khởi động** - Tạo tác vụ định kỳ để main.exe chạy khi khởi động
7. **Thực thi payload** - main.exe chạy với quyền admin và thực hiện:
   - Vô hiệu hóa phần mềm bảo mật
   - Thu thập thông tin hệ thống, mạng, trình duyệt, màn hình
   - Gửi dữ liệu thu thập về Telegram (hoặc lưu vào hàng đợi nếu không có internet)
   - Tạo tài khoản admin ẩn và kích hoạt RDP
   - Khởi động keylogger và reverse shell
   - Xóa dấu vết và log
8. **Kiểm soát từ xa** - Tin tặc có thể điều khiển máy nạn nhân qua Telegram

## Sử dụng Telegram Bot

### Thiết lập Bot
1. Liên hệ @BotFather trên Telegram để tạo bot mới
2. Lấy Bot API Token từ BotFather
3. Xác định Chat ID của bạn (có thể sử dụng @userinfobot)
4. Cấu hình token và chat ID trong mã nguồn trước khi biên dịch

### Cấu hình lệnh Bot
Để dễ dàng sử dụng, bạn có thể thiết lập menu lệnh cho bot bằng cách gửi lệnh sau đến BotFather:
```
/setcommands
```

Sau đó dán danh sách lệnh theo định dạng sau:
```
help - Hiển thị tất cả lệnh có sẵn
victims - Liệt kê tất cả nạn nhân đã kết nối
select - Chọn một nạn nhân để điều khiển (/select ID)
status - Hiển thị trạng thái của nạn nhân hiện tại
exec - Thực thi lệnh shell (/exec command)
upload - Tải lên file từ máy nạn nhân (/upload path/to/file)
download - Tải file từ internet về máy nạn nhân (/download URL path)
screenshot - Chụp màn hình và gửi về
webcam - Chụp ảnh từ webcam (tùy chọn: led-off, multi)
info - Hiển thị thông tin hệ thống của nạn nhân
processes - Liệt kê các tiến trình đang chạy
kill - Dừng một tiến trình (/kill process_name hoặc PID)
persist - Đảm bảo tính bền vững của backdoor
```

### Mẹo sử dụng Bot hiệu quả
- Sử dụng lệnh `/status` thường xuyên để kiểm tra nạn nhân có còn kết nối không
- Tệp được tải lên qua lệnh `/upload` có giới hạn kích thước 50MB (giới hạn của Telegram)
- Khi chụp webcam, sử dụng tùy chọn `multi` để có ảnh chất lượng tốt nhất
- Lệnh nguy hiểm (shutdown, format, v.v.) sẽ được cảnh báo trước khi thực thi

## Phòng chống và bảo vệ
Để bảo vệ chống lại loại tấn công này:
- **Tắt tự động chạy USB** - Vô hiệu hóa AutoRun cho tất cả thiết bị USB
- **Chỉ sử dụng USB đáng tin cậy** - Không kết nối USB không rõ nguồn gốc
- **Cập nhật Windows Defender** - Đảm bảo phần mềm bảo mật luôn được cập nhật
- **Sử dụng giải pháp ngăn chặn BadUSB** - Cài đặt phần mềm chuyên dụng chống lại tấn công BadUSB
- **Giám sát nhật ký sự kiện** - Theo dõi thường xuyên các hoạt động đáng ngờ
- **Tắt PowerShell cho người dùng thông thường** - Hạn chế quyền truy cập vào PowerShell

## Ứng dụng trong An ninh mạng
- **Kiểm tra xâm nhập** - Đánh giá khả năng phòng thủ của hệ thống
- **Mô phỏng tấn công** - Mô phỏng APT (Advanced Persistent Threat)
- **Đào tạo nhận thức bảo mật** - Giáo dục người dùng về mối đe dọa USB
- **Khắc phục sự cố** - Phân tích cách thức BadUSB hoạt động để phát triển biện pháp bảo vệ

## Cập nhật mới trong phiên bản 6.1.3
1. **Xử lý tình huống không có internet** - Arduino tạo script kiểm tra kết nối và chờ đợi internet trước khi tải payload
2. **ID nạn nhân cố định** - Tạo ID dựa trên thông tin phần cứng cố định để ID không thay đổi khi khởi động lại
3. **Lưu trữ dữ liệu offline** - Hệ thống hàng đợi tin nhắn/file khi không có internet và tự động gửi khi có kết nối
4. **Loại bỏ thông báo trùng lặp** - Cơ chế theo dõi để chỉ gửi thông báo kết nối một lần với mỗi nạn nhân
5. **Webcam chất lượng cao** - Nâng cấp module webcam với độ phân giải Full HD, xử lý hình ảnh nâng cao

## Cảnh báo
Dự án này chỉ được tạo ra với mục đích giáo dục. Sử dụng công cụ này mà không có sự cho phép rõ ràng là bất hợp pháp và phi đạo đức. Tác giả không chịu trách nhiệm đối với việc sử dụng sai mục đích phần mềm này.

## Cách gỡ bỏ phần mềm độc hại

Dự án này đi kèm với công cụ gỡ bỏ phần mềm độc hại `uninstall_badusb.bat` để xóa hoàn toàn tất cả các thành phần của BadUSB.

### Sử dụng công cụ gỡ bỏ

1. Chạy file `uninstall_badusb.bat` với quyền quản trị viên
2. Công cụ sẽ tự động:
   - Dừng tất cả các tiến trình liên quan đến malware
   - Xóa tất cả các tệp độc hại (main.exe và các tệp ẩn khác)
   - Loại bỏ các mục tự khởi động trong Task Scheduler
   - Loại bỏ các mục tự khởi động trong Registry
   - Xóa tài khoản administrator ẩn "hiddenadmin"
   - Khôi phục các cài đặt bảo mật Windows Defender
   - Xóa tất cả dữ liệu đã thu thập (keylogger, thông tin hệ thống, ảnh chụp màn hình)
3. Khởi động lại máy tính để đảm bảo tất cả các thay đổi được áp dụng

### Các thành phần được gỡ bỏ

1. Tệp thực thi chính: `C:\main.exe`
2. Tập lệnh backdoor: `%APPDATA%\Microsoft\Windows\Backdoor.bat`
3. Tập lệnh PowerShell: `%USERPROFILE%\AppData\Local\Temp\update_check.ps1`
4. Scheduled Tasks: "RunMainExe", "WindowsUpdateCheck"
5. Registry khởi động: `HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run\WindowsUpdateManager`
6. Tài khoản ẩn: "hiddenadmin"
7. Keylogger: `%TEMP%\keylog.txt`
8. ID hệ thống ẩn: `%TEMP%\.system_id`
9. Các thư mục dữ liệu: `%TEMP%\BadUSB` và các thư mục con

## Thông tin bổ sung

### Cách thức hoạt động của BadUSB

BadUSB hoạt động bằng cách giả dạng như một bàn phím USB, thực hiện các lệnh tự động:

1. **Giai đoạn 1 (Arduino)**:
   - Mở PowerShell với quyền quản trị viên
   - Thêm ổ đĩa C vào danh sách loại trừ của Windows Defender
   - Tạo script kiểm tra kết nối internet và tải payload
   - Tải xuống payload chính (main.exe) từ Dropbox
   - Tạo tác vụ định kỳ để tự động chạy lại payload

2. **Giai đoạn 2 (Python Payload)**:
   - Thu thập thông tin hệ thống và mạng
   - Trích xuất dữ liệu từ trình duyệt (mật khẩu, cookies)
   - Chụp ảnh màn hình và webcam
   - Tạo tài khoản administrator ẩn
   - Thiết lập Reverse Shell qua Telegram
   - Xóa dấu vết để tránh bị phát hiện

### Biện pháp phòng ngừa

1. **Bảo vệ chống tấn công BadUSB**:
   - Không kết nối thiết bị USB không rõ nguồn gốc
   - Sử dụng công cụ bảo vệ cổng USB (USB port protector)
   - Giữ hệ điều hành và phần mềm bảo mật luôn cập nhật
   - Sử dụng Windows Defender với tính năng Hardware Protection

2. **Phát hiện lây nhiễm**:
   - Kiểm tra các tác vụ định kỳ không xác định trong Task Scheduler
   - Tìm các tệp lạ trong thư mục `C:\` hoặc `%TEMP%`
   - Kiểm tra các tài khoản người dùng mới hoặc lạ
   - Theo dõi hoạt động mạng bất thường

## Miễn trừ trách nhiệm

Dự án này chỉ nhằm mục đích nghiên cứu và kiểm tra bảo mật. Việc sử dụng công cụ này cho bất kỳ hoạt động phi pháp hoặc không có sự đồng ý của chủ sở hữu hệ thống là trái với luật pháp. Tác giả không chịu trách nhiệm về bất kỳ hành vi sử dụng sai mục đích nào. 
