# MemLabs Lab 1 - Beginner's Luck
## Mô tả thử thách
- Máy tính của chị tôi bị hỏng. Chúng tôi rất may mắn khi khôi phục được bản sao lưu bộ nhớ này. Nhiệm vụ của bạn là lấy tất cả các tệp quan trọng của chị ấy khỏi hệ thống. Theo những gì chúng tôi nhớ, chúng tôi đột nhiên thấy một cửa sổ màu đen bật lên với một số thứ đang được thực thi. Khi sự cố xảy ra, chị ấy đang cố vẽ một thứ gì đó. Đó là tất cả những gì chúng tôi nhớ từ thời điểm xảy ra sự cố.
> Note: Thử thách này bao gồm 3 flags
## Flag đầu tiên
> Bước 1: Xác định profile của bản memory dump
> > Profile cho chúng ta biết hệ điều hành của máy tính từ memory dump. Sử dụng volatility 2.6 để xác định profile
> > sử dụng plugin imageinfo
> > $ vol.py -f MemLabs-Lab1/MemoryDump_Lab1.raw imageinfo
