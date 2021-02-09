# Docker Image

<!--15-Docker-Image-->

<!--![](https://lh3.googleusercontent.com/gTGVaRHK7nZXp595catxQgZEc8GHi6heUhqVWeRVWYbbXnmmINh8U5T_fwB2aJGoEFuTrMzBLGQQvDVO7wZWVAfIG1BK9yePQ7wgUdy7tU2qM8uHBX-LsXp1z6NGOdpJl8B4ZIbBhPwvL4RELS99Au5WSv3uBwiJEbxxiu5oK6iTAqv8GIDjqEM2p37a2FH20pAgTfJmsjcGxWd19DlfdG8tIbDD0lgCgmiG1EqAqpSq9yCmW0XmeKnq_ZqxKBri8TuWSMsCnNLXQIyVKAv1557kmr47OstUsVPknOuxa7lq7-8d9k-YHRmoZ98I1nA0lcg7gJLH4KKM-XcN9jw4m4B_cuETWAHhRmKUQ0VNG_6chqv79TQg49DGuZzJDUsqISk_rIU2-X4Fejc8zsbl8aRknF_b7R188_oT0KPnk3jroRZ5yYXjNGisiFD3wRUG3zoEmeNGzKl9UrRRjj-QYSCRnkFrEy6oY9hxaDSH0DILEpIZO6WUdY_tluNLy4Z3jhSzapybI-ggEd7TWW79AICSt2IAKvYal4-C808q2muvufQXNKfb0lHAHwfQdBxVWPBG8vv1twvjhYG01K-0Zr4K1AcGRexu79eM43ffHYM90GA95WvqSyKMqqWLGjQFLDc--gU0cK_ENASsoDKpRFji8v0WxfXlbxfo_UEJwvoaJQTvBBnao14zb5iT8g=w992-h744-no?authuser=2)-->

## 認識 Dockerfile

- Dockerfile 就是建置 Docker Image 的腳本

![Dockerfile](https://i.imgur.com/JypYUfh.png)

<!--1. Best practices for writing Dockerfiles
https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
2. Top 20 Docker Security Tips (一定要看)
https://towardsdatascience.com/top-20-docker-security-tips-81c41dd06f57 -->

# 自製 Alpine OpenSSH Server 的 Docker Image

## 撰寫 alpine.plus Dockerfile 

$ cd ~/wulin; mkdir plus
$ nano plus/Dockerfile 
FROM alpine.base
RUN apk update && \
    apk add --no-cache openssh-server tzdata && \
    # 設定時區
    cp /usr/share/zoneinfo/Asia/Taipei /etc/localtime && \
    ssh-keygen -t rsa -P "" -f /etc/ssh/ssh_host_rsa_key && \
    echo -e 'Welcome to Alpine 3.11.6\n' > /etc/motd && \ 
    # 建立管理者帳號 bigred   
    adduser -s /bin/bash -h /home/bigred -G wheel -D bigred && echo '%wheel ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers && \
    echo -e "bigred\nbigred\n" | passwd bigred &>/dev/null && [ "$?" == "0" ] && echo "bigred ok"
 
EXPOSE 22
 
ENTRYPOINT ["/usr/sbin/sshd"]
CMD ["-D"]

[重要] ENTRYPOINT 所指定的執行命令, 在建立 Container 時強制執行此命令並且不可被其它命令取代

<!--The CMD instruction has three forms:

1. CMD ["executable","param1","param2"] (exec form, this is the preferred form)
    這種格式直接執行 CMD 中的系統命令, 它是使用 JSON 陣列格式, 來描述系統命令

2. CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
    這種格式必需搭配 ENTRYPOINT 命令

3. CMD command param1 param2 (shell form)
    這種格式必定使用 sh -c 命令執行 RUN 後面的系統命令

There can only be one CMD instruction in a Dockerfile. If you list more than one CMD then only the last CMD will take effect.
-->

## 建立與使用  alpine.plus image 

$ docker build --no-cache  -t alpine.plus plus/

$ docker run --rm --name s1 -h s1 alpine.plus sh
Extra argument sh.

[重要] 因使用 entrypoint 宣告內定命令, 便無法自行指定執行命令

## 建立  s1 container 

$ docker run  --rm --name s1 -h s1 -d -p 22100:22 alpine.plus

登入 s1 貨櫃主機
$ ssh puxxxx@localhost -p 22100
puxxxx@140.128.xx.xx's password: xxxxx

$ ps aux

........

$ sudo kill -9 1
sudo: setrlimit(RLIMIT_CORE): Operation not permitted

$ exit

$ docker stop s1

---
$ docker ps -a

$ docker rm -f s1 

$ network rm mynet

$ network create mynetxx

$ nano *network*

(8 9 改成自己要的不能和別人重疊）

$ docker run --rm --name s2021 -net=mynetxx -d -p 22100:22 astich/alpine.plus

$ docker exec -it s2021 sh

/# ifconfig 

不能用 bigred 登入, 令建立帳號個別, 就無須 sudo 權限

$ docker exec s01 useradd -m -s /bin/bash s01

$ docker exec -it s01 sh

/# passwd s01

(不用一直做 images)

<!--1. Introducing dumb-init, an init system for Docker containers (一定要看: 詳細解釋如何執行 Container 中的第一個程式)
https://engineeringblog.yelp.com/2016/01/dumb-init-an-init-for-docker.html
2. The PID 1 problem: reaping zombies (zombies 解釋很清楚)
https://blog.phusion.nl/2015/01/20/docker-and-the-pid-1-zombie-reaping-problem/
3. Docker demons: PID-1, orphans, zombies, and signals.
https://www.fpcomplete.com/blog/2016/10/docker-demons-pid1-orphans-zombies-signals

$ nano plus/Dockerfile 
FROM alpine.base
RUN apk update && apk upgrade && apk add --no-cache openssh openrc && rc-update add sshd && \  
    # 設定 OpenSSH
    mkdir /run/openrc && touch /run/openrc/softlevel && rc-status &>/dev/null && \
    echo -e 'Welcome to Alpine 3.11.6\n' > /etc/motd && \ 
    # 建立管理者帳號 bigred   
    adduser -s /bin/bash -h /home/bigred -G wheel -D bigred && echo '%wheel ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers && \
    echo -e "bigred\nbigred\n" | passwd bigred &>/dev/null && [ "$?" == "0" ] && echo "bigred ok"

CMD ["/bin/bash", "-c","/etc/init.d/sshd start && ps aux && tail -f /dev/null"]

$ docker rmi alpine.plus; docker build --no-cache  -t alpine.plus plus/

s1 container 建立流程如下: 
/bin/bash -c 這命令因是第一個執行 ,會先佔有 PID 1, 然後執行 etc/init.d/sshd start 這命令, 如下它的 PID 是 29, 最後再執行 tail -f /dev/null, 
$ docker logs s1
..............
/lib/rc/sh/openrc-run.sh: line 100: can't create /sys/fs/cgroup/systemd/tasks: Read-only file system
ssh-keygen: generating new host keys: RSA DSA ECDSA ED25519 
 * Starting sshd ... [ ok ]
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1 17.0  0.0   2192  1656 ?        Ss   06:59   0:00 /bin/bash -c /etc/init.d/sshd start && ps aux && tail -f /dev/null
root         29  0.0  0.0   4316   536 ?        S    06:59   0:00 /usr/sbin/sshd
root         35  0.0  0.0   1616   556 ?        R    06:59   0:00 ps aux

因最後這 Conatiner 是這個  tail -f /dev/null 命令在前景執行, 這時它會取代 /bin/bash -c 命令, PID 會變 1 
$ docker exec s1 ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.0   1936   564 ?        Ss   07:06   0:00 tail -f /dev/null
root         29  0.0  0.0   4316   544 ?        S    07:06   0:00 /usr/sbin/sshd
root         37  0.0  0.0   1616   564 ?        Rs   07:25   0:00 ps aux


使用 ssh 登入 s1 Conatiner
$ docker exec s1 hostname -i
172.17.0.2

$ ssh 172.17.0.2
bigred@172.17.0.2's password: 
Welcome to Alpine 3.10.2

s1:~$ ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.0   1936   564 ?        Ss   07:06   0:00 tail -f /dev/null
root         29  0.0  0.0   4316   544 ?        S    07:06   0:00 /usr/sbin/sshd
root         48  0.0  0.0   4348  3468 ?        Ss   07:29   0:00 sshd: bigred [priv]
bigred       50  0.0  0.0   4348  2440 ?        S    07:29   0:00 sshd: bigred@pts/0
bigred       51  0.0  0.0   2400  2032 pts/0    Ss   07:29   0:00 -bash
bigred       57  0.0  0.0   1616   556 pts/0    R+   07:29   0:00 ps aux
s1:~$ pstree -p
tail(1)---sshd(29)---sshd(48)---sshd(50)---bash(51)---pstree(58)
s1:~$ exit
logout

再次檢視 Process ID
$ docker exec s1 ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.0   1936   564 ?        Ss   07:06   0:00 tail -f /dev/null
root         29  0.0  0.0   4316   544 ?        S    07:06   0:00 /usr/sbin/sshd
bigred       50  0.0  0.0      0     0 ?        Z    07:29   0:00 [sshd] <defunct>
root         59  0.0  0.0   1616   508 ?        Rs   07:34   0:00 ps aux
$ docker exec s1 pstree -p
tail(1)-+-sshd(29)
        `-sshd(50)

以上資訊顯示, PID 50 竟然變成 zombit, 這是因爲 tail -f /dev/null 不具有 reaping orphaned zombie processes 功能,
如再次使用 ssh 登入 s1 container, 這時又會多一 zombit, 如下 :

$ ssh 172.17.0.2
bigred@172.17.0.2's password: 
Welcome to Alpine 3.10.2

s1:~$ ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.0   1936   564 ?        Ss   07:06   0:00 tail -f /dev/null
root         29  0.0  0.0   4316   544 ?        S    07:06   0:00 /usr/sbin/sshd
bigred       50  0.0  0.0      0     0 ?        Z    07:29   0:00 [sshd] <defunct>
root         70  0.1  0.0   4348  3492 ?        Ss   07:44   0:00 sshd: bigred [priv]
bigred       72  0.0  0.0   4348  2344 ?        S    07:44   0:00 sshd: bigred@pts/0
bigred       73  0.0  0.0   2404  2080 pts/0    Ss   07:44   0:00 -bash
bigred       74  0.0  0.0   1616   560 pts/0    R+   07:44   0:00 ps aux
s1:~$ exit
logout

$ docker exec s1 ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.0   1936   564 ?        Ss   07:06   0:00 tail -f /dev/null
root         29  0.0  0.0   4316  1964 ?        S    07:06   0:00 /usr/sbin/sshd
bigred       50  0.0  0.0      0     0 ?        Z    07:29   0:00 [sshd] <defunct>
bigred       72  0.0  0.0      0     0 ?        Z    07:44   0:00 [sshd] <defunct>
root         80  0.0  0.0   1616   572 ?        Rs   07:45   0:00 ps aux

$ docker exec s1 pstree -p
tail(1)-+-sshd(29)
        |-sshd(50)
        `-sshd(72)


要解決以上問題, 可用 dump-init 來解決 

$ docker run --init --name s1 -h s1 -d alpine.plus
c71b81b1654597b355e763b1d9e1b12c0067d0e5a068dcb10091a84f3e3206fe

$ docker logs s1
........
/lib/rc/sh/openrc-run.sh: line 100: can't create /sys/fs/cgroup/systemd/tasks: Read-only file system
ssh-keygen: generating new host keys: RSA DSA ECDSA ED25519 
 * Starting sshd ... [ ok ]
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  8.5  0.0   1084     4 ?        Ss   15:24   0:00 /sbin/docker-init -- /bin/bash -c /etc/init.d/sshd start && ps aux && tail -f /dev/null
root          6  0.0  0.0   2192  1604 ?        S    15:24   0:00 /bin/bash -c /etc/init.d/sshd start && ps aux && tail -f /dev/null
root         28  0.0  0.0   4312   540 ?        S    15:24   0:00 /usr/sbin/sshd
root         35  0.0  0.0   1616   456 ?        R    15:24   0:00 ps aux

$ docker exec s1 ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.3  0.0   1084     4 ?        Ss   15:24   0:00 /sbin/docker-init -- /bin/bash -c /etc/init.d/sshd start && ps aux && tail -f /dev/null
root          6  0.0  0.0   1936   464 ?        S    15:24   0:00 tail -f /dev/null
root         28  0.0  0.0   4312   540 ?        S    15:24   0:00 /usr/sbin/sshd
root         36  0.0  0.0   1616   548 ?        Rs   15:24   0:00 ps aux

$ docker exec s1 pstree -p
docker-init(1)-+-sshd(28)
               `-tail(6)
再次登入 s1 Container
$ rm ~/.ssh/known_hosts 
$ ssh 172.17.0.2
bigred@172.17.0.2's password: 
Welcome to Alpine 3.10.2

s1:~$ ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.0   1084     4 ?        Ss   15:24   0:00 /sbin/docker-init -- /bin/bash -c /etc/init.d/s
root          6  0.0  0.0   1936   464 ?        S    15:24   0:00 tail -f /dev/null
root         28  0.0  0.0   4312   540 ?        S    15:24   0:00 /usr/sbin/sshd
root         48  0.1  0.0   4348  3500 ?        Ss   15:28   0:00 sshd: bigred [priv]
bigred       50  0.0  0.0   4348  2568 ?        S    15:28   0:00 sshd: bigred@pts/0
bigred       51  0.0  0.0   2400  2068 pts/0    Ss   15:28   0:00 -bash
bigred       52  0.0  0.0   1616   452 pts/0    R+   15:29   0:00 ps aux
s1:~$ pstree -p
docker-init(1)-+-sshd(28)---sshd(48)---sshd(50)---bash(51)---pstree(53)
               `-tail(6)
s1:~$ exit
logout

$ docker exec s1 pstree -p
docker-init(1)-+-sshd(28)
               `-tail(6)

以上操作不會出現 zombit process, 但這時可使用 sudo kill -9 將 tail -f /dev/null 命令關閉, 這時會造成 s1 Container 停止運作, 如下 : 

$ ssh 172.17.0.2
bigred@172.17.0.2's password: 
Welcome to Alpine 3.10.2

s1:~$ ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.0   1084     4 ?        Ss   15:24   0:00 /sbin/docker-init -- /bin/bash -c /etc/init.d/s
root          6  0.0  0.0   1936   464 ?        S    15:24   0:00 tail -f /dev/null
root         28  0.0  0.0   4312  2008 ?        S    15:24   0:00 /usr/sbin/sshd
root         66  0.0  0.0   4348  3488 ?        Ss   15:32   0:00 sshd: bigred [priv]
bigred       68  0.0  0.0   4348  2520 ?        S    15:32   0:00 sshd: bigred@pts/0
bigred       69  0.0  0.0   2404  2180 pts/0    Ss   15:32   0:00 -bash
bigred       70  0.0  0.0   1616   460 pts/0    R+   15:32   0:00 ps aux
s1:~$ sudo kill -9 6
Connection to 172.17.0.2 closed by remote host.

$ docker exec -it s1 bash
Error response from daemon: Container c71b81b1654597b355e763b1d9e1b12c0067d0e5a068dcb10091a84f3e3206fe is not running -->

# Docker Image 備份與還原

<!--1. [Import images to k3s without docker registry](https://cwienczek.com/2020/06/import-images-to-k3s-without-docker-registry/)
-->

## Docker Image 備份

$ cd ~/wk/wulin

$ docker history alpine.plus

- 備份 Image 
$ docker save alpine.plus > alpine.plus.tar

<!--下載 Docker 輔助命令
$ docker run --rm  -v /home/bigred/bin:/opt dafu/dkutil:16.04  download.sh
$ sudo chown -R bigred:bigred ~/bin/
$ docker rmi dafu/dkutil:16.04
$ sudo reboot
-->

## Docker Image 還原

- 還原 Image
$ docker rmi alpine.plus

$ docker load < alpine.plus.tar

$ docker history alpine.plus

[重點]  還原的 image 的目錄架構, 與原先 Image目錄架構一樣

## Docker Container 備份與還原

$ docker run --name s2 -h s2 -d alpine.plus
6e52bd5aee18c0aed8dbb17920037be0b9109158329b401c56b4f64417c2d784

[重要] 使用上述命令建立 s2 Container, 這個 Container 有啟動 openssh server, 所以這個 Container 有 openssh 的執行狀態資訊檔, 這時使用 docker export 命令 
匯出的 Tar 檔中就會有殘留 openssh 的執行暫存檔, 以至後續做出的 image 無法啟動 openssh server, 所以必須先關閉 s2 container, 才可備份此 container

$ docker stop s2             
$ docker export s2 > s2.tar

$ docker rm s2 && docker rmi alpine.plus
$ cat s2.tar | docker import - alpine.plus &>/dev/null

$ docker history alpine.plus

<!--$ nano  ~/bin/dkcompact 
#!/bin/bash

[ "$1" == "" ] && echo "dkcompact image tag" && exit 1

if [ "$2" != "" ]; then
   img="$1:$2"
else 
   img="$1"
fi
docker images | grep -e "^$1 *$2" &>/dev/null
if [ "$?" == "0" ]; then
   docker run --name tempcontainer -itd $img /bin/bash &>/dev/null 
   docker export tempcontainer > /tmp/tempcontainer.tar
   docker rm -f tempcontainer &>/dev/null

   docker rmi $img &>/dev/null
   cat /tmp/tempcontainer.tar | docker import - $img &>/dev/null
   [ "$?" == "0" ] && echo "$img compact success"
else
   echo "$img not exist"
fi

$ chmod +x  ~/bin/dkcompact 
-->

## 使用重製 alpine.plus image

$ docker run --name s2 -h s2 -d alpine.plus
docker: Error response from daemon: No command specified.
See 'docker run --help'.

[重要] 重製後的 alpine.plus image 必須指定 執行命令

$ docker run --name s2 -h s2 -d alpine.plus /usr/sbin/sshd -D

$ docker ps -a

$ docker rm -f s2

<!--![](https://lh3.googleusercontent.com/FuYU5EgOms1x2s3nwTtmUFp5IIDOgpDwkTx4cvTttHfeoFxQXseYKUs82cGs9iz14ZutwaPErGpkxL4TEwChmdR4VB93HV7z6kSmqmX6qtf79oSguZv_TfXceZ3qgndPqM1m5lKtOa8e4iZgPZANcvanK6tHIm1X_GHM7ZhRU0KllbpsAvVppA7CZsll4ICJ858RVs-PuBV4ToFDCqOXg9NP1mrCqPw64-CmZAOrw0mNXJYN9zmgLseVuwu7CBY-P51o9Y-tLHfdiWjiwZCS6B-Pknq0iB3-Wapkh_XUYGaoI5rKpkbQzXFRQhXT0wfEzPEbnp90TOlN1fxlYnmLwCmU8p2t1UWwTvTZJQT8eL1TRx8bFCmr3Loz8P7hSq0IyexNsf6gIyWSLZjsj_AFc9Ea2Tb7eXVr30jMdA_TADGLRblCYoEh5xVUQM2wfJUbG3rD_zYiJOO1dFZ2uK67-jjAjAQFxJMQd5xsg4bHClylo0PqsSySU4hIBOE4OLGvsfrFylZ0YasS_m6p6d-6fKL6ZdORi_4NCvS9XUEcYinWgjcD-28aJ5tPc5angUdyOZM--IO8Pp-k_S3DrFxM9r3HeLvvUqqz0Y424KXveExTdjgBQTAw0TVgVJ2sAKmjae9lEtHzb_TZvIk3cZRs4GeZCKLPnpcce6cb0fKV-FCjRYu2Iz7YEGJYM-BzGaw=w992-h744-no?authuser=2
)-->

---
$ docker run --name [m1] -e MYSQL_ROOT_PASSWORD=[kuan] --net=[mynet] -d mariadb:latest

![](https://i.imgur.com/N6xdJ9S.png)

---

[Next - Docker Container](https://github.com/xuan103/SRE_PU/blob/main/Documents/Docker_Container.md)
[Previous - complement](https://github.com/xuan103/SRE_PU/blob/main/Documents/Complement.md)
[Directory](https://github.com/xuan103/SRE_PU)