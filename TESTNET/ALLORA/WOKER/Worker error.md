

# Một số lỗi Worker

## 1. Lỗi không đăng ký được topic:

Log hiển thị lỗi:


`net1-worker1-topic-3    | 2024-07-11T13:43:45Z INF Failed: register node, retrying... (Retry 3/3) error="rpc error: code = NotFound desc = rpc error: code = NotFound desc = account allo... not found: key not found"`



### Khởi động lại toàn bộ worker:

Thì restart lại toàn bộ worker bằng lệnh sau:

```
docker compose restart
```

### Hoặc chỉ khởi động lại những worker bị lỗi:

Ví dụ khởi động lại `worker-topic-1`:

```
docker restart net1-worker1-topic-1
```

Tương tự với các worker khác.
```
docker restart net1-worker1-topic-3
docker restart net1-worker1-topic-5
docker restart net1-worker2-topic-2
docker restart net1-worker2-topic-4
docker restart net1-worker2-topic-6
```

### Hoặc dùng lệnh dưới để xem các worker đang chạy và lấy tên của worker cần khởi động lại:

```
docker compose ps
```

### Hết lỗi đăng ký topic:

Đến khi có log:

`net1-worker1-topic-3 | 2024-07-11T13:51:50Z INF Success: register node Tx Hash:=...`

là đã đăng ký topic thành công `topic 3`. Các topic khác cũng tương tự.

Topic đã có tx đăng ký rồi thì các lần chạy lại sau hiển thị như sau:

`net1-worker1-topic-3 | 2024-07-11T14:00:06Z INF node already registered for topic topic=3`

## 2. Không kết nối được đến Head Node:

![image](https://github.com/user-attachments/assets/41f57842-41fb-4b14-b296-1ac4a88d3ecb)


_Error connect head node_

Hình trên là lỗi của `worker topic 2` không kết nối được đến `head node` của `Allora`, log hiển thị dòng cuối cùng là `DBG host started peer discovery component=host` và không có log hiển thị nào khác của `worker` này.

Cần khởi động lại `worker` của topic đó để kết nối lại đến `head node`, tương tự: **`Khởi động lại chỉ worker bị lỗi`**.

Có thể dùng lệnh khởi động lại `worker` này nhiều lần và tại các thời điểm khác nhau, vì có thể do `head node` của `Allora` giới hạn kết nối đồng thời.

Nếu `worker` đã chạy bình thường, nhưng vẫn xem log vì nếu có log là `Resetting up chain connection` ở dòng cuối cùng, thì cần khởi động lại `worker` này, tương tự: **`Khởi động lại chỉ worker bị lỗi`**.

## 3. Lỗi kết nối đến peer:

![image](https://github.com/user-attachments/assets/caecfcf8-76d8-4e8c-b187-75ab9315c7b3)


_Error connect peer_

Lỗi này không ảnh hưởng vì worker này đang kết nối đến các worker khác của những người khác đã đăng ký đến head node của Allora, nên không lỗi này quan trọng, bỏ qua.

Thường khi có lỗi này thì worker vẫn chạy bình thường vì đã kết nối đến head node nên mới nhận được ip và port của các worker khác.

## 4. Kiểm tra Worker hoạt động không:

### Kiểm tra worker topic 1:

```
curl --location 'https://heads.testnet-1.testnet.allora.network/api/v1/functions/execute' \
--header 'Content-Type: application/json' \
--data '{
    "function_id": "bafybeigpiwl3o73zvvl6dxdqu7zqcub5mhg65jiky2xqb4rdhfmikswzqm",
    "method": "allora-inference-function.wasm",
    "parameters": null,
    "topic": "1",
    "config": {
        "env_vars": [
            {
                "name": "BLS_REQUEST_PATH",
                "value": "/api"
            },
            {
                "name": "ALLORA_ARG_PARAMS",
                "value": "ETH"
            }
        ],
        "number_of_nodes": -1,
        "timeout": 2
    }
}'
```

Xem log hiển thị như sau là đã chạy thành công: `Test worker-topic-1`

![image](https://github.com/user-attachments/assets/2adda275-d669-41af-9a5b-47fcc6cb8aee)


Có những lần kiểm tra chỉ hiển thị `INF reporting for roll call`, nhưng không có `command executed successfully` và `Result from WASM`: thì thử lại lệnh `curl` ở trên nhiều lần để xem có kết quả như hình không.

### Tương tự kiểm tra worker của các topic khác:

`Topic 3`: đổi `"topic": "1"` thành `"topic": "3"`, `"value": "ETH"` thành `"value": "BTC"`.

`Topic 5`: đổi `"topic": "1"` thành `"topic": "5"`, `"value": "ETH"` thành `"value": "SOL"`.

`Topic 2`: đổi `"topic": "1"` thành `"topic": "2"`.

`Topic 4`: đổi `"topic": "1"` thành `"topic": "4"`, `"value": "ETH"` thành `"value": "BTC"`.

`Topic 6`: đổi `"topic": "1"` thành `"topic": "6"`, `"value": "ETH"` thành `"value": "SOL"`.

Mỗi topic chỉ cần kiểm tra chạy thành công 1 lần là đủ, nếu khởi động lại worker topic nào thì cũng nên kiểm tra worker topic đó lại.

### TIP
```
Cần thường xuyên xem log để biết có các lỗi nào không.

Và xem thêm: `Xem các tx của worker` hoặc ví để kiểm tra ví mình có `Action` là `MsgInsertBulkWorkerPayload` không, đây là `action của tx khi worker` gởi dự đoán vào mạng `Allora`.

Gợi ý cài đặt `Worker 3` cho `topic 7-8-9`, copy `Worker 1` đổi các file sau:

`docker-compose.yml`: đổi `topic id`, `token`, `port`(chạy vps khác thì không cần đổi port)

`app.py, model.py`: đổi token
```
