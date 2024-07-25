
# Tool kiểm tra tx ví (worker)

Vì trên web `explorer` chưa hiển thị `tx` của ví nên dùng tool này kiểm tra các `tx` của ví (`worker`).

## Tool kiểm tra tx ví (worker):

http://worker-tx.nodium.xyz/

### Xem các tx của ví (worker):

Nhập địa chỉ ví vào ô `Wallet Address`

`Order by` là `Newest First` để xắp xếp tx mới nhất lên trên.

Nhấn `Search` để tìm các tx của địa chỉ đó. Kiểm tra tx ví (worker)

![image](https://github.com/user-attachments/assets/f1ea8253-4f52-462d-8bac-605237ed00f9)




Kết quả ở cột `Action` là `MsgRegisterWorker` là tx của worker đăng ký topic.

Kết quả ở cột `Action` là `MsgInsertBulkWorkerPayload` là tx của worker leader gởi giá dự đoán vào mạng Allora.

### Xem các tx của worker đã hoạt động trên mạng:

`Order by` là `Newest First` để xắp xếp tx mới nhất lên trên.

Chọn: `Action` là `MsgRegisterWorker` là tx của worker đăng ký topic.

Chọn: `Action` là `MsgInsertBulkWorkerPayload` là tx của worker leader gởi giá dự đoán vào mạng Allora.

![image](https://github.com/user-attachments/assets/5bb86e60-6d8f-4863-b496-1493be1b8b85)


_Kiểm tra tx worker_
_
Kích vào liên kết TX để xem chi tiết tx.


### Thông tin thêm:

Ví worker không có tx `MsgInsertBulkWorkerPayload` chưa chắc `worker` lỗi, vì chỉ cần ví đó có trong danh sách `Worker Data Bundles` trong tx của ví khác là chứng tỏ `worker` đó đã hoạt động. Như hình dưới: `Worker Data Bundles`

![image](https://github.com/user-attachments/assets/c86b6b22-46e0-4461-9dc7-ad5c92c6d724)


Ví có tx `MsgInsertBulkWorkerPayload` nhưng không có trong danh sách `Worker Data Bundles` của chính tx mình gởi thì cần kiểm tra lại logs có bị lỗi dự đoán hay gì không. Như hình dưới: `Worker Data Bundles`

![image](https://github.com/user-attachments/assets/8e8e3924-3fd8-4844-9422-6ca542eccc86)


