## Thao tác trên server

1. Login vào server
2. Nếu service đang chạy gõ `tmux ls` xem các session đang chạy.
Phải đủ 2 session là runserver, worker thì mới chạy đúng.
3. Nếu muốn update code hoặc debug thì attach vào session runserver như sau:

```bash
# command trên macos
tmux a
^ b s (control + b sau đó s)
# thì sẽ ra màn hình screen, dùng arrow up / down để chọn screen runserver
```

![](https://i.imgur.com/m7MtB5Y.png)

4. Để thoát khỏi screen dùng

```bash
^ b d
```

## Add deployment key vào project để pull code

1. kiểm tra xem trên server đã có file ~/.ssh/id_rsa & ~/.ssh/id_rsa.pub chưa,
nếu chưa tạo mới bằng

```bash
ssh key-gen
```

2. Copy nội dung file ~/.ssh/id_rsa.pub paste vào như hình bên dưới.

![](https://i.imgur.com/ztpAcM8.png)

3. Kiểm tra remote dưới server đã đúng định dạng `git@gitlab.com:` chưa, nếu chưa đổi lại cho đúng.
Sau đó pull code mới về.

![](https://i.imgur.com/hKeIb47.png)
