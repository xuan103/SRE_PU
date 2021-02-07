# Docker Container

<!--13-Docker-Container-->

## 搜尋與下載 Docker Image

- `pull`: docker.io 原廠 /library/busybox
    
    $ docker search busybox

    $ docker pull busybox

- 顯示 images
$ docker images 

## 建立與執行軟體貨櫃 (Container) 主機

- 建立軟體貨櫃 (再次確認 Linux Namespace 啟動)
    - `--name`: container 名稱
    - `-it`: 虛擬終端機
    - `-d`: 在背景執行
    - `busybox`: 使用 busybox 光碟片

    $ docker run --name b1 -it -d busybox /bin/sh

- 刪除 container
$ docker rm b1

- 顯示所有 container, `-a `: all
$ docker ps -a

- 再次建立 container
$ docker run --name b1 -it -d busybox /bin/sh

- 利用虛擬終端機進入 b1 container, 使用 sh 程式執行
$ docker exec -it b1 sh

- 查看 ip
/# ifconfig

- 檢測是否連到外網
/# `ping www.hinet.net`

- 查看有多少成是在執行
    - ps: process

    /# ps aux

    /# exit

- 從外面將 container 關機
$ docker stop b1

- 強制刪除 container 
    - `-f`: 強制（不論是否在執行）
    $ docker rm -f b1 xxxx

- 建立 container
    - `--name`: container 名稱
    - `-d`: 在背景執行
    - `-p`: port
    - `nginx`: 使用 nginx 光碟片
$ docker run --name n1 -d -p 8819:80 nginx

---
<!--![](https://lh3.googleusercontent.com/4GfIAqqSky_0cOlc9qSUOf17suuhM0XTZ6gXgNfuVxSRnmC7dGMTRcAMIvcpLAdeg9wBuxhf44afuXPWcJEoMIg4IcabjiKtOVisR09nNW1YOeAjJ6u14KrKrVvk-37KE9QM8L0r0mh9b5WZZkvfh-164QrjS0l7ugT5LM3X8JTNof2sXm5OtQncX-CYF82kZ0_BXXXaHAfT3SzXlaPW-aYbfANoHQ-R48Ecuet_eVNuR8qCznh25D39wcUB8CQHFgOdFZLUfYbX2jq05WnFpfBtKXrIIh7-slKvY6H19t5pifQWwN-1TcMsaVzcl_m3mGZ_IyqZa9u4e0ENNLTOAWNOAkyU2D7XQV_uJc0kyXylKuhG73Kpmpj1xpeZ30lcL36HggLeKWbrXKnUikcVw9g83yth2x04vIBS4wRYgOIf5uIs94LVdOhfHf7eJA68JhZMWa-qMcNYy21iCtpwDMKVbs-uFPTXMecageabN-O5K8pm2UlExiN5HeVAukUAtG_Dr4b4hK3WhO3DPnPdp9p42oVohlwN3nFmbJNsqusUdPaH4kVrYfOPk8L3eqnGQ5WAuGva-LdKiR0scQ_Cj3MxM1sxS0oHUwm72MgxxjWoUPA4cfB6DKVghhSxZlAY2LN05pVDADDB-8tJHWcY7FJGsNLyBgZ8H9na1iDFkIcu6q7lk_zQGnmNE0t4Qw=w992-h744-no?authuser=2)-->

- 管理軟體貨櫃 (Container) 主機

- 建立 container
    - `--name`: container 名稱
    - `-it`: 虛擬終端機
    - `nginx`: 使用 alpine 光碟片
$ docker run --name a1 -it alpine sh

- 查看 ip
/# ifconfig

- 檢測是否連到外網
/# `ping www.hinet.net`

- 查看有多少成是在執行
    - ps: process

    /# ps aux

    /# exit

    $ docker ps -a

    $ docker start a1

    $ docker ps -a

    $ docker rm -f a1

    $ docker run --name a1 -it ubuntu sh

    / # hostname -i

    / # apt update

    / # apt install ssh

    / # exit

    $ docker export a1 > a1.tar

    $ docker rm -f a1

## 認識 Alpine Linux

- Alpine Linux 是一套由社群開發，以安全為導向的 Linux 作業系統，目前最新的版本 v3.6.2 在 2017-06-17 發布。
- 跟一般的 Linux 發行版不一樣的地方是 Alpine 是使用 musl libc 和 busybox 來減少系統需求容量(5 MB )和執行消耗資源。
- 比起其他輕量化 Image (如：Busybox，Image 大小為1.113MB)， Alpine 的優勢在於多了像是 apt 之於 Ubuntu 這樣的套件管理工具 apk ，官方提供了大量的套件供開發者使用，可以透過網站或者直接下 apk 指令進行查詢或者安裝。

* Alpine 的 busybox 是 HardLink, 而不是 Softlink. Docker busybox image 沒有提供 套件安裝平台


- 建立 Alpine 軟體貨櫃主機

    $ docker run --name a1 -it alpine sh

    /# busybox | grep http

[重要] Docker Alpine Image 內建的 busybox 並沒提供 httpd 功能

- 下載 Busybox 執行檔
/ #  wget https://busybox.net/downloads/binaries/1.28.1-defconfig-multiarch/busybox-x86_64 

- 安裝 Busybox 執行檔，並給予執行權限
/ #  chmod +x busybox-x86_64

- 移動 busybox-x86_64 到 bin 並取名為 busybox
/ #  mv busybox-x86_64 bin/busybox

- 執行 busybox 命令
/ # busybox | grep httpd

## 建立 Busybox Httpd 網站伺服器

- 製作網站目錄及首頁
/ # mkdir www
/ # `echo '<h1>Busybox HTTPd</h1>' > www/index.html`

- 啟動 Busybox Httpd 網站伺服器
/ # busybox httpd -p 8888 -h www

- 安裝網頁工具
/ # apk update
/ # apk add elinks curl

- 取得網頁
/ # `curl http://localhost:8819`

- 檢視網頁
/ # `elinks -dump 1 http://localhost:8819`

/ # exit


## 製作 a1 image 並上載至 Docker HUB

$ docker ps -a

- 燒成光碟
$ `docker commit a1 <Login Name>/a1`

- 登入
$ docker login 

- 上傳
$ `docker push <Login Name>/a1`

- 下載別人的光碟小鋪的光碟
$ docker pull xxx/xx

- 登出
$ docker logout

[重要] 關閉 Docker Host 之前, 記得執行 docker logout, 然後刪除 /home/bigred/.docker 目錄

$ docker rm a1 

- 使用自製 a1 image 

    $ `docker run --name testa1 -d <login name>/a1 httpd -p 8819 -h www`

    $ docker ps -a

- 因 httpd 沒在前景啟動, 以致 testa1 貨櫃主機沒啟動
$ docker rm testa1

貨櫃電腦 Application container 必須要用光碟並在前景執行

- 重新建立 testweb 貨櫃主機
     - `-f`: 保持在前端一直執行
    
    $ `docker run --name testa1 -d <login name>/a1 httpd -f -p 8819 -h www`

- 查看 ip 

    $ docker exec testa1 hostname -i


    $ `curl http://172.17.0.2:8819`

## 建立 Container 外部連線

- 創網路，名叫 mynet

    $ docker network create mynet

- cat *創網路的檔案*

- 重新建立 testa1 貨櫃主機, 並指定外部連接 Port

    $ `docker run --name testa1 -d --net=mynet -p 8080:8819 kuan0915/a1 httpd -f -p 8888 -h www`

    $ `curl http://localhost:80`


- 以強制方式移除 testa1

    $ docker rm -f testa1 

---
[Next - Docker Overview](https://github.com/xuan103/SRE_PU/blob/main/Documents/Docker_Overview.md)
[Previous - Docker Image]()
[Directory](https://github.com/xuan103/SRE_PU)