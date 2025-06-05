# MemLabs Lab 2 - A New World
## Mô tả thử thách
Một trong những khách hàng của công ty chúng tôi đã mất quyền truy cập vào hệ thống của mình do một lỗi không xác định. Ông ấy được cho là một nhà hoạt động "môi trường" rất nổi tiếng. Trong quá trình điều tra, ông ấy đã nói với chúng tôi rằng các ứng dụng ông ấy sử dụng là trình duyệt, trình quản lý mật khẩu, v.v. Chúng tôi hy vọng rằng bạn có thể đào sâu vào bản sao lưu bộ nhớ này và tìm thấy những thứ quan trọng của ông ấy và trả lại cho chúng tôi.
Note: Thử thách này gồm 3 flag
# flag thứ nhất
Đầu tiên, ta phải xác định profile của bản memory dump
``` bash
vol.py -f MemLabs-Lab2/MemoryDump_Lab2.raw imageinfo
```
![image](https://github.com/user-attachments/assets/efe3a984-8559-4772-baca-f1592de462de)
Tiếp theo, kiểm tra các tiến trình đang chạy
``` bash
vol.py -f MemLabs-Lab2/MemoryDump_Lab2.raw --profile Win7SP1x64 pslist
```
![image](https://github.com/user-attachments/assets/0f0ff35d-186b-409f-920f-c97aab6a7d5e)
Ta để ý thấy trong mô tả thử thách có cụm từ "environmental". Vì vậy, ta sẽ thử trích xuất các biến môi trường từ tiến trình đang chạy sử dụng plugin envars
``` bash
vol.py -f MemLabs-Lab2/MemoryDump_Lab2.raw --profile Win7SP1x64 envars
```
![image](https://github.com/user-attachments/assets/46dbd6a8-3e12-4604-b10c-85b7dec8aa8b)
Phân tích mô tả thử thách ta thấy ứng dụng mà khách hàng dùng là trình duyệt và trình quản lý mật khẩu. Vì vậy, ta sẽ tìm các biến môi trường của tiến trình chrome.exe. Ta tìm thấy biến môi trường NEW_TMP với giá trị giống như giá trị base64
![image](https://github.com/user-attachments/assets/b7547f50-32e9-48a2-83e0-feef259537b9)
Giờ ta sẽ thử decode giá trị này và ra được flag đầu tiên
flag{w3lc0m3_T0_$T4g3_!_Of_L4B_2}
# flag thứ 2
Tiếp theo, ta sẽ phân tích tiến trình của trình quản lý mật khẩu, là tiến trình KeePass.exe
![image](https://github.com/user-attachments/assets/9f479e91-42e6-454b-974b-f1ec5428eb5c)
KeePass quản lý và lưu trữ toàn bộ mật khẩu trong file .kdbx. Vì thế ta sẽ tìm các file .kdbx có trong hệ thống
``` bash
vol.py -f MemLabs-Lab2/MemoryDump_Lab2.raw --profile Win7SP1x64 filescan | grep ".kdbx"
```
![image](https://github.com/user-attachments/assets/8575aef1-78de-4d1a-bd19-ae18f22b119e)
Giờ ta sẽ dump file này từ bộ nhớ RAM ra thành file thật trên ổ đĩa để kiểm tra
``` bash
vol.py -f MemLabs-Lab2/MemoryDump_Lab2.raw --profile Win7SP1x64 dumpfiles -Q 0x000000003fb112a0 -D output1/
```
![image](https://github.com/user-attachments/assets/1fa0d89f-2583-4049-972c-62d70e1c0fb5)
Sau khi nén và đổi tên file thành .kdbx, ta mở file bằng KeePass và nó yêu cầu phải nhập mật khẩu
![image](https://github.com/user-attachments/assets/f4ea725f-0f49-442e-a2cd-95b945273510)
Giờ ta quay lại để quét file lần nữa và tìm các file có chữ "Pass"
``` bash
vol.py -f MemLabs-Lab2/MemoryDump_Lab2.raw --profile Win7SP1x64 filescan | grep "Pass"
```
![image](https://github.com/user-attachments/assets/258664f6-88e2-417f-a1b2-cd73d36fd26d)
Ta thấy được 1 file ảnh có tên là Password.png. Giờ hãy thử dump file này để kiểm tra
``` bash
vol.py -f MemLabs-Lab2/MemoryDump_Lab2.raw --profile Win7SP1x64 dumpfiles -Q 0x000000003fce1c70 -D output1/
```
![image](https://github.com/user-attachments/assets/91d9e7b0-12d6-4ce6-9cb5-75dde4bc1e66)
Sau khi nén và đổi tên file thành Password.png, ta được bức ảnh chứa mật khẩu ở bên dưới
![image](https://github.com/user-attachments/assets/4aeb8f46-703f-455a-b27b-58f9a1eec548)
Giờ ta sẽ nhập mật khẩu đó vào file hidden.kdbx
![image](https://github.com/user-attachments/assets/0cba16c9-7ced-48cf-9cc6-ee81703f1574)
Ta mở được file và tìm thấy được flag thứ 2
![image](https://github.com/user-attachments/assets/c54f2a99-458c-4768-b4a7-a55359d2fb25)
flag{w0w_th1s_1s_Th3_SeC0nD_ST4g3_!!}
# flag thứ 3
Giờ ta sẽ đào sâu vào lịch sử của trình duyệt chrome để xem có file gì đáng chú ý không
``` bash
vol.py -f MemLabs-Lab2/MemoryDump_Lab2.raw --profile Win7SP1x64 filescan | grep "Chrome" | grep "History"
```
![image](https://github.com/user-attachments/assets/d689a1a7-d07f-488a-bf6f-df0889cd13cd)
Ta sẽ dump file History 
``` bash
vol.py -f MemLabs-Lab2/MemoryDump_Lab2.raw --profile Win7SP1x64 dumpfiles -Q 0x000000003fcfb1d0  -D output2/
```
![image](https://github.com/user-attachments/assets/3632194f-e588-4364-b902-3a411c8f15e9)
Mở file bằng DB Browser for SQLite. Check bảng url ta thấy có 1 mega link
![image](https://github.com/user-attachments/assets/ae916db4-55a6-4a7d-9b54-0a8e46cbbd35)
Nhập đường dẫn url vào ta được 1 file có tên là Important.zip
![image](https://github.com/user-attachments/assets/1319d2e5-1019-465d-ac9c-9fc23530c281)
Tải file xuống và mở file ta nhận được flag thứ 
![image](https://github.com/user-attachments/assets/e8f89ef4-c675-4b21-9002-0ab2095c5699)
flag{oK_So_Now_St4g3_3_is_DoNE!!} 






























