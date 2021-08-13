1. Login vào server
2. Nếu service đang chạy gõ `tmux ls` xem các session đang chạy.
Phải đủ 2 session là runserver, worker thì mới chạy đúng.
3. Nếu muốn update code hoặc debug thì attach vào session runserver như sau:

```bash
# command trên macos
tmux a
^ b s (command + b sau đó s)
# thì sẽ ra màn hình screen, dùng arrow up / down để chọn screen runserver
```

![](https://i.imgur.com/m7MtB5Y.png)

4. Để thoát khỏi screen dùng

```bash
^ b d
```
