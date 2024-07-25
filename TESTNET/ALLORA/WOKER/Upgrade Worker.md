# Cập nhật Worker

Hướng dẫn cập nhật worker 1 và worker 2 khi đã cài phiên bản mạng cũ là Edgenet.

## 1. Hiển thị thư mục hiện tại:
```
pwd
```

Kiểm tra có đúng với thư mục ở bước Hiển thị thư mục hiện tại không. Nếu sai, cần dùng lệnh cd để di chuyển đến thư mục đó. Ví dụ:
```
cd /root
```

Gõ lệnh `ls` để xem các thư mục hiện tại. Nếu có thư mục `worker-face-24h` và `worker-face-10m` thì đúng.

## 2. Dừng worker:

Gõ lệnh `docker ps` để xem các `worker` đang chạy. Như hình dưới là đang chạy: 
```
Docker ps
```

![image](https://github.com/user-attachments/assets/a61e9bdc-07fb-49a8-9814-8ea5a32628e9)



### Dừng worker `worker-face-10m` và `worker-face-24h`:

Di chuyển đến thư mục `worker-face-10m`:

```
cd worker-face-10m
```

Dừng worker `worker-face-10m`:

```
docker compose down
```

Di chuyển đến thư mục `worker-face-24h` ở thư mục trước:

```
cd ../worker-face-24h
```

Dừng worker `worker-face-24h`:

```
docker compose down
```

### Kiểm tra tất cả các worker đã dừng chưa:

Kiểm tra tất cả các worker đã dừng chưa:

```
docker ps
```

Nếu không có worker nào đang chạy thì tiếp tục.

Nếu có cần kiểm tra và thực hiện lại các lệnh trên.


## 3. Trở về thư mục chính và sao lưu dữ liệu cũ:

### a. Trở về thư mục chính:

```
cd ../
```b

Tương tự như trên cần gõ lệnh ls để xem các thư mục hiện tại. Nếu có thư mục worker-face-24h và worker-face-10m thì đúng.

### 2. Sao lưu dữ liệu cũ:

Đổi tên thư mục worker cũ:
```
mv worker-face-24h worker-face-24h-old
mv worker-face-10m worker-face-10m-old
```

Kiểm tra tên mới:

```
ls
```

Nếu có tên mới là worker-face-24h-old và worker-face-10m-old thì đã sao lưu thành công.

```
INFO

Đến bước này worker cũ đã tắt và được sao lưu lại.

Tiếp tục cài đặt worker update mới theo hướng dẫn ở bước Cài đặt Worker 1 và Cài đặt Worker 2

Nhớ Faucet để nhận uallo trong mạng mới.
```
