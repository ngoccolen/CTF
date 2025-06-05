# MemLabs Lab 3 - The Evil's Den
## Mô tả thử thách
Một tập lệnh độc hại đã mã hóa một thông tin rất bí mật mà tôi có trên hệ thống của mình. Bạn có thể khôi phục thông tin đó cho tôi được không?
Note 1: Thử thách này gồm 1 flag. Flag này bị chia làm 2 phần
Note 2: Bạn sẽ cần nửa đầu của flag để có được nửa sau của flag
Đầu tiên, ta phải xác định profile của bản memory dump
``` bash
vol.py -f MemLabs-Lab3/MemoryDump_Lab3.raw imageinfo
```
![image](https://github.com/user-attachments/assets/86d211c4-4af3-4045-b625-a4ad29870c6c)



