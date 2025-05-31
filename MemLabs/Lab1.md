# MemLabs Lab 1 - Beginner's Luck
## Mô tả thử thách
- Máy tính của chị tôi bị hỏng. Chúng tôi rất may mắn khi khôi phục được bản sao lưu bộ nhớ này. Nhiệm vụ của bạn là lấy tất cả các tệp quan trọng của chị ấy khỏi hệ thống. Theo những gì chúng tôi nhớ, chúng tôi đột nhiên thấy một cửa sổ màu đen bật lên với một số thứ đang được thực thi. Khi sự cố xảy ra, chị ấy đang cố vẽ một thứ gì đó. Đó là tất cả những gì chúng tôi nhớ từ thời điểm xảy ra sự cố.
- Note: Thử thách này bao gồm 3 flags
## Flag đầu tiên
Đầu tiên, ta phải xác định profile của bản memory dump
- Profile cho chúng ta biết hệ điều hành của máy tính từ memory dump. Sử dụng volatility 2.6 để xác định profile
- sử dụng plugin imageinfo
```bash
vol.py -f MemLabs-Lab1/MemoryDump_Lab1.raw imageinfo
```
![image](https://github.com/user-attachments/assets/8974147a-246a-4d4f-8aa6-8359a6d449a1)
- Kết quả gợi ý profile là: Win7SP1x64
Giờ ta sẽ liệt kê các tiến trình đang chạy với plugin pslist
```bash
vol.py -f MemLabs-Lab1/MemoryDump_Lab1.raw --profile=Win7SP1x64 pslist
```
![image](https://github.com/user-attachments/assets/f5ecdf65-d5f1-423a-b102-b05cb3c2822e)
Ta tìm được tiến trình cmd.exe đang hoạt động. Giờ ta sẽ thử xem liệu có lệnh nào được thực thi và nội dung nào được in ra trong terminal không, ta sử dụng plugin consoles
```bash
vol.py -f MemLabs-Lab1/MemoryDump_Lab1.raw --profile=Win7SP1x64 consoles
```
![image](https://github.com/user-attachments/assets/eb0225b9-d248-46d0-85b7-c8d6b71bbccd)
Ta tìm thấy chuỗi base 64: ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=
Dùng base64 decode để giải mã chuối, sau khi giải mã ta được flag đầu tiên
flag{th1s_1s_th3_1st_st4g3!!}
## flag thứ 2
Ta xem lại list các tiến trình đang chạy và tìm thấy 1 tiến trình lạ có tên là mspaint.exe, giờ ta sẽ dump bộ nhớ của tiến trình này bằng plugin memdump và lưu nó vào thư mục output
```bash
 vol.py -f MemLabs-Lab1/MemoryDump_Lab1.raw --profile=Win7SP1x64 memdump -p 2424 -D output/
```
![image](https://github.com/user-attachments/assets/5443817d-1ca2-4ded-b037-f3b3f036249c)
khi lưu file ảnh trên máy, file ảnh sẽ có một phần đầu gọi là "magic bytes" — đó là những dãy byte đặc biệt giúp phần mềm nhận ra đây là file ảnh
- Khi mở ảnh bằng mspaint, chương trình không giữ nguyên file ảnh gốc trong bộ nhớ. Thay vào đó, mspaint đọc file ảnh, giải mã nó thành dữ liệu thôi - tức là bảng màu và pixels thực tế trên màn hình
- Dữ liệu thô này không có magic bytes như file ảnh gốc. Nó chỉ là chuỗi mảng pixel liên tục, không có phần đầu nhận dạng file.
Vì vậy để lấy ảnh ra, ta sẽ đổi tên file .dmp thành .data để đánh dấu đây là dữ liệu ảnh thô
mở file .data này bằng GIMP, phần mềm chuyên chỉnh sửa ảnh có thể mở và hiển thị được ảnh thô
- Mở GIMP. Chọn file -> open -> chọn 2424.data trong thư mục output
- Chỉnh sửa lại độ rộng và cao và lật lại ảnh, ta sẽ được flag thứ 2:
  ![image](https://github.com/user-attachments/assets/54b5a9eb-e922-4aa8-975d-7f4ab9fa9f3f)
  flag{G00d_BoY_good_girL}
  ## flag thứ 3
Khi kiểm tra danh sách các tiến trình đang chạy, ta nhận thấy một tiến trình WinRAR đang hoạt động. Vậy có thể có file đang được mở hoặc xử lý trong quá trình này.
  ![image](https://github.com/user-attachments/assets/c600f10c-6cc7-4e56-ab74-10489fed29ce)
Để tìm hiểu kỹ hơn, ta sử dụng plugin filescan để quét toàn bộ vùng nhớ, nhằm phát hiện tất cả các file mà hệ thống đang xử lý kể cả những file:
đã bị xóa, đang được mở bởi 1 tiến trình hoặc bị ẩn khỏi hệ thống
```bash
vol.py -f MemLabs-Lab1/MemoryDump_Lab1.raw --profile=Win7SP1x64 filescan
```
![image](https://github.com/user-attachments/assets/a2c26a89-ef11-45d4-bea4-9984f1ecd60a)
có thể thấy rằng có rất nhiều file và ta không thể dò từng cái được nên ta sẽ dùng câu lệnh grep để tìm các file nén .rar trong bộ nhớ
![image](https://github.com/user-attachments/assets/fbb95d32-c4b8-48cc-9309-8259055f08c1)

```bash
vol.py -f MemLabs-Lab1/MemoryDump_Lab1.raw --profile=Win7SP1x64 filescan | grep -i '.rar'
```
![image](https://github.com/user-attachments/assets/aa93d147-8fa0-4c6d-8b09-98a88f939051)
Ta tìm thấy được 1 file có tên là Important.rar, vì vậy ta sẽ dump file này từ bộ nhớ RAM ra thành file thật trên ổ đĩa để kiểm tra sử dụng plugin dumpfiles
```bash
vol.py -f MemLabs-Lab1/MemoryDump_Lab1.raw --profile=Win7SP1x64 dumpfiles -Q 0x000000003fa3ebc0 -D output/
```
![image](https://github.com/user-attachments/assets/9aea04df-f13a-48f8-be97-3b4527901c85)
Ta có được file rar như này
![image](https://github.com/user-attachments/assets/69a5d400-f5f8-47b8-be59-41adf9bb9107)
Kiểm tra bên trong file rar ta sẽ thấy có 1 file .kmp như này
![image](https://github.com/user-attachments/assets/cc75f6de-a8ff-4056-990a-ccb273a42cdc)
Khi liệt kê các file bên trong thư mục bằng lệnh 
```bash
ls "file.None.0xfffffa8001034450"
```
![image](https://github.com/user-attachments/assets/40f12f35-1d01-4594-adc4-b9ef15a98d76)
Ta thấy rằng bên trong có file .dat, nhưng ở trong thư mục lại ghi là file .kmp, nên có thể nó có nội dung quan trọng đã được che giấu ở bên trong, vì vậy ta sẽ trích xuất file này thành chuỗi ký tự có thể đọc bằng lệnh
```bash
strings "file.None.0xfffffa8001034450/file.None.0xfffffa8001034450.dat" > output.txt
```
Mở file output.txt để đọc, ta có gợi ý về một file ảnh có tên là flag.png0, đây có thể là file chứa flag, và 1 đoạn gợi ý về mật khẩu để mở file là CMTPassword là NTLM hash (uppercase) của mật khẩu tài khoản Alissa.
![image](https://github.com/user-attachments/assets/a41043e4-9b52-489d-bb67-f6cd4a246536)
Trước tiên ta sẽ đổi tên file .kmp sang file flag.png0, ta thấy nó yêu cầu mật khẩu để có thể mở được file
![image](https://github.com/user-attachments/assets/567247d9-9130-4f79-908b-f26d506ea6bc)
Tiếp theo, ta dùng plugin hashdump trích xuất thông tin tài khoản người dùng và lấy mật khẩu
```bash
vol.py -f MemLabs-Lab1/MemoryDump_Lab1.raw --profile=Win7SP1x64 hashdump
```
![image](https://github.com/user-attachments/assets/55043c74-d71e-473f-a5ab-76ef7c6dbacf)
Ta tìm được password là F4FF64C8BAAC57D22F22EDC681055BA6. Nhập mật khẩu vào ta sẽ được flag thứ 3
![image](https://github.com/user-attachments/assets/25fd7c21-c1e2-471f-b934-8be0ff23e0a9)















  








