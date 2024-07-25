
# Cài đặt Worker 2


Hướng dẫn đã cập nhật cho mạng mới Testnet V2:

https://docs.allora.network/devs/workers

https://github.com/allora-network/allora-huggingface-walkthrough

## Cài đặt Worker chạy `topic 2, 4, 6`

### `Topic 2, 4, 6` là chạy dự đoán giá `ETH, BTC, SOL` trong `24 giờ`.

Trang Campaign: https://app.allora.network/points/campaign/run-inference-24h

Trờ về thư mục chính:
```
cd ..
```

Hiển thị thư mục hiện tại:
```
pwd
```

Kiểm tra có đúng với thư mục ở bước Hiển thị thư mục hiện tại không. Nếu sai, cần dùng lệnh cd để di chuyển đến thư mục đó. Ví dụ:

```
cd /root
```

## Clone repository:
```
git clone https://github.com/nhunamit/basic-coin-prediction-node.git
```


### Đổi tên thư mục repository vừa clone:
```
mv basic-coin-prediction-node worker-face-24h
```

### Di chuyển vào thư mục vừa đổi tên:
```
cd worker-face-24h
```


### Xem các nhánh có sẵn:
```
git branch -a
```

### Chuyển sang nhánh `worker-face-24h`:
```
git checkout worker-face-24h
```

### Kiểm tra lại nhánh đã thay đổi sang worker-face-24h:
```
git branch -a
```

Như hình dưới là đã chuyển sang nhánh `worker-face-24h`:

![image](https://github.com/user-attachments/assets/f441d87d-f666-40d0-9720-4921405920e0)




## Branch `worker-face-24h`

### Tạo thư mục data cho các worker:
```
mkdir -p worker-topic-2-data
chmod 777 worker-topic-2-data
mkdir -p worker-topic-4-data
chmod 777 worker-topic-4-data
mkdir -p worker-topic-6-data
chmod 777 worker-topic-6-data
```


### Sửa 24 từ mnemonic trong file docker-compose.yml:
```
nano docker-compose.yml
```


Tìm 3 dòng có chữ: `--allora-chain-restore-mnemonic='just clap slim ...'` và sửa `just clap slim ...` thành `24 từ mnemonic` của ví mới tạo ở bước Tạo một ví mới cho `worker node`.

Lưu và thoát bằng cách nhấn 'Ctrl+X' , Y và nhấn Enter


## Chạy Docker Compose:
```
docker compose up -d
```

### Kiểm tra log:
```
docker compose logs -f
```

Nhấn Ctrl + C để thoát khỏi log.

### Xem riêng log của từng worker:

Hiển thị danh sách các worker đang chạy:
```
docker compose ps
```

Hoặc xem toàn bộ các container đang chạy:

```
docker ps
```

Lấy tên `NAMES của worker` cần xem log từ danh sách trên.

Ví dụ xem log của `net1-worker2-topic-2`:

```
docker logs net1-worker2-topic-2 -f
```

# END

Xem thêm: `Một số lỗi Worker`
