# Cài đặt Worker 1

INFO

Hướng dẫn đã cập nhật cho mạng mới Testnet V2:

https://docs.allora.network/devs/workers

https://github.com/allora-network/allora-huggingface-walkthrough

# Cài đặt Worker chạy topic 1, 3, 5

### `Topic 1, 3, 5` là chạy dự đoán giá `ETH, BTC, SOL` trong `10 phút`.

Trang Campaign: https://app.allora.network/points/campaign/run-inference-10m

### Hiển thị thư mục hiện tại:

```
pwd
```

Lưu lại thư mục này kiểm tra sau này.

## Clone repository:
```
git clone https://github.com/nhunamit/basic-coin-prediction-node.git
```

### Đổi tên thư mục repository vừa clone:
```
mv basic-coin-prediction-node worker-face-10m
```

### Di chuyển vào thư mục vừa đổi tên:
```
cd worker-face-10m
```


### Xem các nhánh có sẵn:
```
git branch -a
```

### Chuyển sang nhánh `worker-face-10m`:

```
git checkout worker-face-10m
```

### Kiểm tra lại nhánh đã thay đổi sang `worker-face-10m`:
```
git branch -a
```

Như hình dưới là đã chuyển sang nhánh `worker-face-10m`:

![image](https://github.com/user-attachments/assets/886c5028-911e-4aa7-a7ce-dffb7b809ccf)


## Branch `worker-face-10m`

### Tạo thư mục data cho các worker:
```
mkdir -p worker-topic-1-data
chmod 777 worker-topic-1-data
mkdir -p worker-topic-3-data
chmod 777 worker-topic-3-data
mkdir -p worker-topic-5-data
chmod 777 worker-topic-5-data
```

### Sửa `24 từ mnemonic` trong file `docker-compose.yml`:
```
nano docker-compose.yml
```


Tìm 3 dòng có chữ: `--allora-chain-restore-mnemonic='just clap slim ...'` và sửa `just clap slim ...` thành `24 từ mnemonic` của ví mới tạo ở bước Tạo một ví mới cho `worker node`.

Lưu và thoát bằng cách nhấn Ctrl+X >> Y  và nhấn Enter)


## Chạy Docker Compose:
```
docker compose up -d
```

### Kiểm tra log:
```
docker compose logs -f
```

Nhấn `Ctrl + C` để thoát khỏi log.

## Xem riêng log của từng worker:


### Hiển thị danh sách các worker đang chạy:
```
docker compose ps
```

### Hoặc xem toàn bộ các container đang chạy:

```
docker ps
```

Lấy tên `NAMES` của `worker` cần xem log từ danh sách trên.

Ví dụ xem log của `net1-worker1-topic-1`:
```
docker logs net1-worker1-topic-1 -f
```

Xem thêm: `Một số lỗi Worker`

https://nodium.xyz/docs/allora/install-worker/install-worker-1-3-5

# END
