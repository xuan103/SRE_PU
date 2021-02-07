# Docker Overview

<!--PPT: 11-Docker-Overview-->

<!--![](https://lh3.googleusercontent.com/70WD8QgRIPwVpphgLHBCfyGIE5sR7haXK1rh0ZiV0pDwG_usVftslpyMJDtfo23PX1mZtSiseYT1fgVYExignIgL81z4s5liFLn1YfdfGBfUJT2NnQEDBhGvTtVfrKtLBPZgN15G4fTRzPP4iCUV6ZdFX6joORsFdxeAcocnY8yoJ8a2qTWRPMBxxeTPgzUUgFXx-SgFXL69veHtFtEQfNfTgJPZK24ZrUs986ZR1Jrt8yCxu_WRJBq8mY2y7bKTnpb-k6ioaiKtadLxGIRkcN_rbb1P0S9D7RQadoX_9hL8YLWoiXR9YX60rnVAlp-sEgTyUVJ-p02s2CWpB0vtZ7zUSRL3md-1Cy6pvLZuBQegUOxInJR8fXJLzylGLo5GpAsY6qz4Ruep7fK1gHNJlXI6RyKOixklhiQMZjwEWGBmJHF1oN6acrRzRydXKPyp_OEDTOeu5j1YzNkeQPU7OxaGphKqnyuX6IQdKdF-G3YnwChTClDm3EOPWmq67WApB2CDxv6VtA53sFqJ4MV-21oygzaMAfI1weleJ584WcG307Z-TerIv585t5D_zjFBq2CzhQrns3eEcm_4I_AHa_pdhQg4-EPTyMqmWMtJ1fZiUzYQOZQnE-ZzBNQsYrIMs925tFhWmcd2n_7Vg9sWAJ4OlRbKRgYnGdmYF9_H_IxuAomuCvUzUFyOLU5ZVw=w916-h687-no?authuser=2)-->

## 使用者驅動創新 (User Driven Innovation) 

- 解決問題的能力，一直是 IT 產業最重視的一環，也是大多數公司企業面臨技術問題時所仰賴的解決途徑，然而，等待，往往是依賴 IT 廠商 提出解決方案時所需付出的.

- 多數國際化的廠商，或大型雲端服務供應商（如 Google、Amazon、Facebook...等），在現實面上，不盡然有足夠的時間可以等待 IT 廠商的回應，這時，就必須自己解決遭遇的問題。而這種自行解決問題的過程，有時，就會激盪出令人意想不到的創意，這就是所謂的 "使用者驅動創新"，如同 Google 許多創新的服務，或是大家熟悉的 3M，都是在解決問題的過程中，意外找到的創意！ 而 Docker 則是〝使用者驅動創新〞的最佳實例

<!--1. 臺灣資安大會直擊】找出內部威脅要注意「燈下黑」的情況，透過零信任的概念來發現異常
https://www.ithome.com.tw/news/139633-->

## 傳統應用軟體佈署挑戰

- 應用軟體 (Application) 可定義成一個或一組可用來控制電腦操作的程式，並且包含各種設定檔 (XML,JSON) 及相依檔 (Lib)。

- 過去應用軟體佈署, 常常因人為因素, 造成佈署不成功, 甚至損壞客戶系統. 

## 登入訓練組機

```bash=
$ ssh puxxxx@140.128.xxx.xxx
password: xxxxxx
```
- 檢視 busybox 是否提供 httpd 功能
    - busybox: 模擬 linux 命令. ex: 家裡的 ip 分享器（也是 linux 系統安裝 busybox）
    - grep -o httpd: 搜尋 httpd
$ busybox | grep -o httpd

## 簡單介紹 Docker

- Docker 碼頭工人
    - application container (應用程式貨櫃)
    
## 設定 Busybox HTTP Daemon 首頁

- 製作首頁
$ mkdir -p www/cgi-bin

$ `echo '<h1>MyWeb : /x/x/x <h1>'   >  www/index.html`

- 啟動 HTTP Daemon
    - 之後，打開網頁搜尋 "140.128.xxx.xxx:8819"
$ busybox httpd -p 8819 -h ~/www

- 關閉 HTTP Daemon
    - ps aux: 查看此電腦所有執行程式
    - -9 2227: -9 表示強迫執行，2227 為 job id
$ ps aux | pgrep busybox

$ kill -9 2227

## 設計 CGI 程式

- echo 後面的回應，用來街前端遊覽器給的資料

$ nano  www/cgi-bin/kungfu
```bash=
#!/bin/sh
echo "Content-type: text/html; charset=utf-8"
echo ""
parm=$(echo $QUERY_STRING | tr '&' ' ')
echo $parm
echo ""
```

- 給予執行權限
$ chmod +x www/cgi-bin/kungfu

- 啟動 HTTP Daemon
$ busybox httpd -p 8819 -h ~/www

$ `curl 'http://localhost:8888/cgi-bin/kungfu?name=car&size=small'
name=car size=small`


- 關閉 HTTP Daemon
$ ps aux | pgrep busybox
2227

$ kill -9 2227

---
<!--![](https://lh3.googleusercontent.com/jXCkS-deNZk56Rc4D6jZ4wCLmmusobdu6my8CYHyW6WjtBQ99E5cC2UWNscaHw8euf4criznzLZR3ShHdnRkuDy1O_OTg4aIP3PartlDjyv6IbN9UFwY2lc0drNphhISiuZzG1dZmEnxs6uKA4ygHn2okdxYTuitzlCbfeuO6w_2wZOoni26LTz2j8pD1NqbMe3ryuXdXl1AVsMdMpf5DzWEKQwcbX8myGyYTXZ_4fl81rq-JesR1yjQ4FfgsHT3qNXe4V1em_OokK2G377ynX8m5qN3IcXP-IT78M1Bi-ZOr-VwAl1sA2m_0_OzdOo1X4JiKAC4rJRVelm6w6ZS5sR2Ox5thJgaLx6ENwF8KYver8vijNoW-AZU-DEY-gmztXE46kpZEFm6PYxHW8mY3-WaGrSqGCjLerpnT6hq7iQl7HcO4HHIBcc3t-fFO9YKrdo17Ukb33bVsz_2UpLF-tm22Pl7BFXpjqRgjI1V7AWDHaHxatTdnUlBuV8PTTtNfnWnABFDXHovWm-pRaPqUkN_8kg2u9YSrhdYtz6giyaSaTokZzERGj4YKZOjDkf3hOLaCGC8dm6nDnvsx7jzqdNYUAZc_Hf-RnLPBtNX1hp1VuvqxVTnJ7SjETKHRITC0-oDuXZsvfRVh6iwANf1OAWgnd1oIW82-PGu9RrECIpyM9AGxGNK_qTh0awZ0g=w992-h744-no?authuser=2)-->

## 修改 CGI 程式

- 在 parm 下一行增加
    - `</br>`: 網頁換行語法
    - `>`: 存入檔案（覆蓋）
    - `>>`: 寫入檔案（新增，不覆蓋）

$ nano  www/cgi-bin/kungfu
```bash=
fi=$(echo $QUERY_STRING | tr '&' ' ')
echo $parm
echo "</br>"
echo $fi > /tmp/puxx.txt
echo $f2 >> /tmp/puxx.txt
```

---
<!--![](https://lh3.googleusercontent.com/gTGVaRHK7nZXp595catxQgZEc8GHi6heUhqVWeRVWYbbXnmmINh8U5T_fwB2aJGoEFuTrMzBLGQQvDVO7wZWVAfIG1BK9yePQ7wgUdy7tU2qM8uHBX-LsXp1z6NGOdpJl8B4ZIbBhPwvL4RELS99Au5WSv3uBwiJEbxxiu5oK6iTAqv8GIDjqEM2p37a2FH20pAgTfJmsjcGxWd19DlfdG8tIbDD0lgCgmiG1EqAqpSq9yCmW0XmeKnq_ZqxKBri8TuWSMsCnNLXQIyVKAv1557kmr47OstUsVPknOuxa7lq7-8d9k-YHRmoZ98I1nA0lcg7gJLH4KKM-XcN9jw4m4B_cuETWAHhRmKUQ0VNG_6chqv79TQg49DGuZzJDUsqISk_rIU2-X4Fejc8zsbl8aRknF_b7R188_oT0KPnk3jroRZ5yYXjNGisiFD3wRUG3zoEmeNGzKl9UrRRjj-QYSCRnkFrEy6oY9hxaDSH0DILEpIZO6WUdY_tluNLy4Z3jhSzapybI-ggEd7TWW79AICSt2IAKvYal4-C808q2muvufQXNKfb0lHAHwfQdBxVWPBG8vv1twvjhYG01K-0Zr4K1AcGRexu79eM43ffHYM90GA95WvqSyKMqqWLGjQFLDc--gU0cK_ENASsoDKpRFji8v0WxfXlbxfo_UEJwvoaJQTvBBnao14zb5iT8g=w992-h744-no?authuser=2)-->

<!--11-Docker-Overview, p9-->

## 認識 Docker 軟體貨櫃

- 很多人都覺得 Docker 是個新技術，其實不然，Docker 除了其開發語言用 go 比較新外，其實它還真不是個新東西，也就是個新瓶裝舊酒的東西，所謂的 The New 「Old Stuff」

![Docker_container](https://i.imgur.com/c53BnXH.png)

**Docker** 使用的 Linux 核心模組功能有下列各項：
1. Namespace - 用來隔離不同 Container 的執行空間
2. Cgroup - 用來分配硬體資源
3. chroot - 針對正在運作的軟體行程和它的子行程，改變它的根目錄
4. Bridge (Network) - 建立虛擬網路, 連接不同 Container 
5. Overlay2(chroot) - 用來建立不同 Container 的檔案系統

<!--1. The Docker Ecosystem: An Introduction to Common Components (一定要看)
https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-an-introduction-to-common-components
2.Windows Container 版本相容性與多重架構容器映像介紹
https://blog.miniasp.com/post/2019/06/24/Windows-Containers-version-compatibility-and-multi-arch-images
3. Hardening Docker containers and hosts against vulnerabilities: a security toolkit (重要  : Docker 安全防護)
https://www.stackrox.com/post/2017/08/hardening-docker-containers-and-hosts-against-vulnerabilities-a-security-toolkit/
4. The Differences Between Linux and Windows Containers
https://containerjournal.com/2019/04/03/the-differences-between-linux-and-windows-containers/
5 大 Container OS 介紹
http://www.ithome.com.tw/news/95756
6. 報告：前十大熱門Docker映像檔都有至少30個以上的漏洞
https://www.ithome.com.tw/news/129018

Docker 18.06  社群版釋出，未來要放慢發布速度，延長專案維護時間
https://mail.google.com/mail/u/0/#inbox/164b2963a71a3531

Docker Key Release Milestone
2013/03 : first release (使用 Ubuntu LXC 做為執行引擎)
2014/03 : v0.9, introducing execution drivers and libcontainer (kernel > 2.6) as execution driver
2016/07 : v1.9, overlay network
2016/09 : v1.10, DNS
2016/10 : v1.11, runC
2016/11 : v1.12, swarm mode (從單機架構跨入叢集架構)
2017/03 : v17.03 Community Edition & Enterprise Edition. Starting with this release, Docker is on a monthly release cycle and uses a new YY.MM versioning scheme to reflect this. 

Docker在 1.12 版有了大進展，從單機架構跨入叢集架構, 但在 1.12 版, 需自行建置 Discovery Service (Zookeeper,Etcd,..), 
而 17.03 版則內建 Discovery Service.


Windows Container
自從 Windows 核心版本 v14393 開始，也就是 Windows Server 2016 LTSC 與 Windows 10 年度更新版，正式開始支援 Windows 容器，這意謂著企業可以開始將傳統 Windows 應用程式正式容器化，透過容器化技術隔離應用程式，最大的優勢，就是容器技術會讓應用程式不會再遇到部署環境變更導致的任何問題。不過 Windows 核心版本不斷更新，也開始出現了相容問題。

Windows Containers 支援兩種容器執行方式：
1. Windows Server Container
2. Hyper-V Container
第一種 Windows Server Container 提供一種 程序隔離 (Process isolation) 執行模式，可以讓你直接透過 Docker Engine 存取現有 Host 主機的 Kernel 資源。直接共用 Host 主機的作業系統核心，意謂著可以得到較好的容器啟動效能，啟動時間短、執行速度快，是一種相當理想的執行模式。不過這種模式有個嚴重的問題，那就是當作業系統升級後，由於核心版本也跟著升級，因此現有的容器映像(image)將無法再使用程序隔離模式執行！

第二種 Hyper-V Container 則提供另一種 Hyper-V 隔離 (Hyper-V isolation) 執行模式，顧名思義，這些容器將會執行在一種極輕量的 Hyper-V 虛擬機中，這種類型的容器不會直接存取目前 Host 主機的作業系統核心，而是在虛擬中執行另一份輕量的 Windows 核心，讓容器直接存取這份核心。如此一來將會得到較好的隔離性，一來可以提供 SaaS 環境更好的安全性，另一方面則是可以讓你執行不同版本的 Windows 作業系統核心！

Docker 安全防護
Capability - 控制 root 帳戶的執行功能
Seccomp - Linux 内核的安全計算模式（Seccomp） 功能
AppArmor - 保護 Container 的網路及執行安全
SELinux - Linux 的安全性增強模組

解釋 IOMMU 
在周邊 I/O 的虛擬化支援上，AMD-Vi (AMD) 與 VT-d (Intel, VT for Directed I/O) 提供了 I/O 記憶體管理 (IOMMU) 的方式，讓 VM 可以透過直接記憶體存取 (DMA) 與中斷重對映 (interrupt remapping) 的方式，直接取用各種 I/O 周邊，像是網路卡、顯示卡與磁碟控制卡等。這個方式又稱為 PCI passthrough。

IOMMU 的記憶體位址轉換技術，可以讓周邊設備定址到整個記憶體位址，進而減少在周邊與主記憶體之間的 buffer 記憶體空間的需求，還可以保護作業系統的記憶體，免於惡意的軟硬體的侵害。

要完全達成 IOMMU 的功能除了需要 CPU 的支援外，仍需要主機板的晶片組、BIOS 或 UEFI 韌體的支援。

DPDK 概述
在X86結構中，處理數據包的傳統方式是CPU中斷方式，即網卡驅動接收到數據包後通過中斷通知CPU處理，然後由CPU拷貝數據並交給協議棧。在數據量大時，這種方式會產生大量CPU中斷，導致CPU無法運行其他程序。
而DPDK則採用輪詢方式實現數據包處理過程：DPDK重載了網卡驅動，該驅動在收到數據包後不中斷通知CPU，而是將數據包通過零拷貝技術存入內存，這時應用層程序就可以通過DPDK提供的接口，直接從內存讀取數據包。
這種處理方式節省了CPU中斷時間、內存拷貝時間，並向應用層提供了簡單易行且高效的數據包處理方式，使得網絡應用的開發更加方便。但同時，由於需要重載網卡驅動，因此該開發包目前只能用在部分採用Intel網絡處理晶片的網卡中。

RDMA 概述 
RDMA是一種新的記憶體訪問技術，RDMA讓計算機可以直接存取其他計算機的記憶體，而不需要經過處理器耗時的處理。 RDMA技術最早出現在Infiniband網路，用於HPC高效能運算叢集的互聯。傳統的基於Socket套接字(TCP/IP協議棧)的網路通訊，需要經過作業系統軟體協議棧，資料在系統DRAM、處理器Cache和網絡卡Buffer之間來回拷貝搬移，因此佔用了大量的CPU計算資源和記憶體匯流排頻寬，也加大了網路延時。舉例來說，40Gbps的TCP/IP流能耗盡主流伺服器的所有CPU資源；RDMA則解決了傳統TCP/IP通訊的技術痛點。例如，在40Gbps場景下，CPU佔用率從100%下降到5%，網路延時從ms級降低到10us以下。

1. 關於RDMA技術原理、三種主流實現技術對比
https://codertw.com/程式語言/587408/

2. SR-IOV in Docker containers
https://medium.com/@tukai.anirban/sr-iov-in-docker-containers-f13fdb297da0

3. IOMMU 輸入輸出記憶體管理單元 (一定要看這篇文章, 才能了解 IOMMU)
https://zh.wikipedia.org/wiki/输入输出内存管理单元

4. How-to: Deploy RDMA accelerated Docker container over InfiniBand fabric.
https://docs.mellanox.com/pages/viewpage.action?pageId=15049785
-->

## Docker Key Release Milestone

2013/03 : first release (使用 Ubuntu LXC 做為執行引擎)

2014/03 : v0.9, introducing execution drivers and libcontainer (kernel > 2.6) as execution driver

2016/07 : v1.9, overlay network 
2016/09 : v1.10, DNS
2016/10 : v1.11, runC

2016/11 : v1.12, swarm mode 
2017/03 : v17.03 Community Edition & Enterprise Edition. Starting with this release, Docker is on a monthly release cycle and uses a new YY.MM versioning scheme to reflect this. 

* Community Edition : Stable 一年出二版

<!--Docker 18.06  社群版釋出，未來要放慢發布速度，延長專案維護時間
https://mail.google.com/mail/u/0/#inbox/164b2963a71a3531

Docker在 1.12 版有了大進展，從單機架構跨入叢集架構, 但在 1.12 版, 需自行建置 Discovery Service (Zookeeper,Etcd,..), 
而 17.03 版則內建 Discovery Service.

LibContainer Overview : 用來取代 第一代 Docker Engine (Ubuntu LXC)
Libcontainer is now the default docker execution environment(since version 0.9) . It is driver (named native) and a library.
In other words, it is a replacement (since version 0.9) for formerly LXC execution environment (that can be easily brought back using -e switch).
This library is developed by docker.io, written in go and C/C++, in order to support a wider range of isolation technologies. It also can be used in python through python bindings.
-->

---
# Docker 的發展

## kubernetes (CRI) 與 docker 以及 contained 的架構演進圖

![](https://i.imgur.com/GggWhPI.png)

<!--1. [Day7] Container Runtime - CRI-O (一定要看)
https://ithelp.ithome.com.tw/articles/10219102?sc=rss.iron

1. kubelet 透過 Dockershim 與 docker engine 連接，最後一路串接到 containerd 來創建 container。
2. 繞過 Docker 直接與後端的 Containerd 溝通，為了滿足這個需求也需要一個額外的應用程式 CRI-Containerd 來作為中間溝通的橋樑
3. 隨者 containerd 1.1 版本的發行， CRI-Containerd 本身的功能已經可以透過 plugin 的方式實現於 containerd 中，可以再少掉一層溝通的耗損。
4. CRI-O 一個完全針對 kubernetes 需求的解決方案，讓整體的溝通變得更快速與簡單。

認識 CRI-O
作為一個滿足 CRI 標準且能夠產生出相容於 OCI 標準 container 的解決方案，從整個設計到特色全部都是針對 kubernetes 來打造

1. 本身的軟體版本與 kubernetes 一致，同時所有的測試都是基於 kubernetes 的使用去測試，確保穩定性。
2. 目標是支援所有相容於 OCI Runtime 的解決方案，譬如 Runc, Kata Containers
3. 支援不同的 container image，譬如 docker 自己本身就有 schema 2/version 1 與 schema 2/version 2
4. 使用 Container Network Interface CNI 來管理 Container 網路
-->

## RedHat Enterprise Linux 8 (Podman, Skopeo and Buildah) 

![](https://i.imgur.com/3jOlNh9.png)

<!--1.  Podman, Buildah and Quarkus  (一定要看)
https://events19.linuxfoundation.org/wp-content/uploads/2018/07/OSS-Japan-SlidesPodman-Buildah-and-Quarkus-The-Latest-in-Linux-Containers-Technology-.pdf -->

# Docker 和微軟合作要簡化容器應用程式開發與部署

- Docker 和微軟擴展戰略合作關係，Docker Desktop、**Azure (世界電腦)** 和 Visual Studio 系列產品將會緊密整合在一起，讓開發人員可以快速啟動 Node.js、Python 以及 .NET Core/C# 等特定語言的專案，並簡化本機到雲端的容器部署。

- 而現在 Docker 和微軟聯手，要降低開發人員在雲端應用程式開發過程所會遇到的摩擦，開發人員將可以從 Docker CLI 簡單地登入 **Azure (世界電腦)**，系統將預設自動配置，讓在 ACI 雲端容器環境創建容器更加方便快速，而且可以快速地從本地端上下文，切換到雲端下文執行應用程式。Compose 規範簡化了單個容器和多容器的應用程式開發，讓開發人員可以在雲端容器服務裡，呼叫完全相容 Docker 的命令。


<!--1.  Docker 和微軟合作要簡化容器應用程式開發與部署  (一定要看)
https://www.ithome.com.tw/news/137954 -->

---
[Next - SRE Introduction](https://github.com/xuan103/SRE_PU/blob/main/Documents/SRE_Introduction.md)
[Previous - Docker Container](https://github.com/xuan103/SRE_PU/blob/main/Documents/Docker_Container.md)
[Directory](https://github.com/xuan103/SRE_PU)