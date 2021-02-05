# SSH arrange

![SSH_arrange](https://i.imgur.com/JIom2OI.jpg)

```
使用密碼登入
1. $ ssh a20@192.168.55.200
server 的公鑰存在 client 的家目錄的 .ssh/known_hosts

使用憑證登入
$ ssh-keygen -t rsa -P ‘’
產生公私鑰憑證
放在 client 的家目錄的 .ssh/id_rsa
/id_rsa.pub
$ ssh-copy-Id a20@192.168.55.200
將client 的id_rsa.pub 複製到server 的家目錄的.ssh/authorized_keys

server 產生憑證（server操作
$ ssh-keygen -t rsa -b 2048 -P
$ cp ~/.ssh/id_rsa.pub ~/.ssh/authorized_keys

􂜁􀅋*􏿿老師操作
$ ./busybox httpd -P 6666 ~/account/

學生 client
curl -o ~/.ssh/id_rsa —create-dies 192.168.55.200:6666/a20/id_rsa
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
```
---
[Next - Architecture](https://github.com/xuan103/SRE_PU/blob/main/Documents/Architecture.md)
[Previous - SRE Introduction](https://github.com/xuan103/SRE_PU/blob/main/Documents/SRE_Introduction.md)
[Directory](https://github.com/xuan103/SRE_PU)