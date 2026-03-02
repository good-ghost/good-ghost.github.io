---
title: "편집이 필요한 문서"
sort: 1
---

# 무작위 복사 붙여넣기하는 문서의 소스......

## Java 개발 관련

[Adding Plugins to a Java Application](https://www.javaranch.com/journal/200607/Plugins.html)

[Running Arbitrary Java Code On The Fly](https://medium.com/@Mark.io/running-arbitrary-java-code-on-the-fly-d5003ebd46a8)

[Example: Loading a Java Class at Runtime](https://stackabuse.com/example-loading-a-java-class-at-runtime/)


## Typescript

[Typescript Tsc 컴파일러 옵션](https://infos.tistory.com/4879)

[웹팩을 사용하여 자산을 묶고 Typescript 정의를 생성하는 표준 방법](https://blog.naver.com/lkandif/222723254155)

## Webtop

[다국어 댓글](https://svrforum.com/svr/349515)

## 세일즈포스 관련

[VF loading dynamically](https://salesforce.stackexchange.com/questions/51262/include-page-dynamically)

[How to use jsPDF html() method in LWC](https://stackoverflow.com/questions/75902253/how-to-use-jspdf-html-method-in-lwc)

[pdf-force](https://github.com/jsmithdev/pdf-force)

[Microservice in Python using FastAPI](https://dev.to/paurakhsharma/microservice-in-python-using-fastapi-24cc)

[35 CSS Sidebar Menu Examples](https://devdevout.com/css/css-sidebar-menus)


## nextcloud 설치, memories 설치

1. network create
1-1 SNI-FORWARDING rules into iptables for each network.
2. pod create with network, without port definition
3. run container with pod, without port
4. proxy call container from outside of pod using podname, and container port

5. mariadb, adminer, nextcloud run in the same pod. adminer connect to mariadb using pod name, nextcloud user name @ mariadb ip address.

-------------
nextcloud memories가 활성화가 안될때...

I went back to CLI and ran following commands:

$ docker exec -it -u 1000 nextcloud php /config/www/nextcloud/occ app:install memories

output: memories already installed

and then:

$ docker exec -it -u 1000 nextcloud php /config/www/nextcloud/occ app:disable memories

$ docker exec -it -u 1000 nextcloud php /config/www/nextcloud/occ app:remove memories

$ docker exec -it -u 1000 nextcloud php /config/www/nextcloud/occ app:install memories

afterwards it showed in the taskbar and is working again!!





nextcloud memories
==================
**> podman exec -it -u 1000 nextcloud-server php /app/www/public/occ app:install memories

기존 파일 추가....
**> podman exec -it -u 1000 nextcloud-server php /app/www/public/occ files:scan

**> podman exec -it -u 1000 nextcloud-server php /app/www/public/occ memories:places-setup
**> podman exec -it -u 1000 nextcloud-server php /app/www/public/occ memories:index


## 도커를 systemd 서비스로 시작... 컨테이너간에 순서 정할때...

https://pyrasis.com/book/DockerForTheReallyImpatient/Chapter15/01/01

https://jugmac00.github.io/blog/how-to-run-a-dockerized-service-via-systemd/

self container update 가능...

https://stackoverflow.com/questions/54767751/running-a-docker-container-with-systemd

systemd start문에 docker run 할때는 -d -it를 사용하면 안됨....

## rclone을 systemd로 마운트

https://forum.rclone.org/t/mount-rclone-paths-before-docker-gets-started/42185

샘플

```
[Unit]
Description=RClone Service
Wants=network-online.target
After=network-online.target

[Service]
ExecStart=/usr/bin/rclone mount \
  --config=/opt/rclone/rclone.conf \
  --log-level=INFO \
  --bwlimit 25M:off \
  --log-file=/opt/rclone/logs/rclone-mount.log \
  --umask=002 \
  --gid=1000 \
  --uid=1000 \
  --allow-other \
  --timeout=1h \
  --poll-interval=15s \
  --dir-cache-time=1000h \
  --cache-dir=/media/ssd/rclone_cache \
  --vfs-cache-mode=full \
  --vfs-cache-max-size=1500G \
  --vfs-cache-max-age=12h \
  --vfs-read-ahead=2G \
  gcrypt_direct: /data1/Allt
ExecStop=/bin/fusermount -uz /data1/Allt
Restart=on-abort
RestartSec=5
StartLimitInterval=60s
StartLimitBurst=3

[Install]
WantedBy=multi-user.target
```

```
[Unit]
Description=RClone Service Nextcloud
Wants=network-online.target
After=network-online.target

[Service]
#Type=notify
#Environment=RCLONE_CONFIG=/opt/rclone/rclone.conf
#KillMode=none
#RestartSec=5
#ExecStart=/usr/bin/rclone mount gcrypt_direct: /data1/Allt --log-file /opt/rclone/logs/gcrypt_direct.log --allow-other
#ExecStop=/bin/fusermount -uz /data1/Allt
#Restart=on-failure
#User=olympen
#Group=olympen
ExecStart=/usr/bin/rclone mount \
  --config=/opt/rclone/rclone.conf \
  --log-level=INFO \
  --log-file=/opt/rclone/logs/rclone-mount-seafile.log \
  --umask=002 \
  --gid=8000 \
  --uid=8000 \
  --allow-other \
  --timeout=1h \
  --poll-interval=15s \
  --dir-cache-time=1000h \
  --cache-dir=/media/ssd/rclone_cache_seafile \
  --vfs-cache-mode=full \
  --vfs-cache-max-size=100G \
  --vfs-cache-max-age=12h \
  gcrypt_nextcloud: /data2/seafile_data
ExecStop=/bin/fusermount -uz /data2/seafile_data
Restart=on-abort
RestartSec=5
StartLimitInterval=60s
StartLimitBurst=3

[Install]
WantedBy=multi-user.target
```

ㅋㅋ잠자 옵션
```
nohup /usr/bin/rclone mount gdrive-eddylab: /media/temp/gdrive_ro \
--read-only \
--umask 000 \
--allow-other \
--allow-non-empty \
--cache-dir=/media/temp/rclone_cache/gdrive \
--log-level INFO \
--syslog \
--vfs-read-chunk-size=32M \
--vfs-cache-mode full \
--vfs-cache-max-size 100G \
--vfs-cache-max-age 86400h \
--buffer-size 32M \
--poll-interval 60s \
--vfs-read-ahead 32M \
--rc \
--rc-no-auth \
--rc-addr 127.0.0.1:55001 < /dev/null > /dev/null 2>&1 &
```

jerry80 옵션
```
RCLONE_MOUNT_USER_OPTS="
--allow-other 
--allow-non-empty 
--drive-skip-gdocs 
--poll-interval=15s 
--vfs-cache-mode full 
--vfs-write-back 5s 
--bwlimit 0 
--bwlimit-file 64M 
--buffer-size=16M 
--vfs-read-chunk-size=32M 
--vfs-cache-max-size 100G 
--vfs-cache-max-age 336h 
--vfs-read-ahead 32M 
--dir-cache-time=1000h 
--timeout 1h 
--transfers=4"
```

## Video Upscaling

source : https://macilatthefront.blogspot.com/2021/01/deinterlacing-with-avisynth-and-qtgmc.html



## File Upload to NextCloud

https://medium.com/@denis.verkhovsky/nextcloud-api-how-to-upload-the-files-9caccc4a4a6c

https://docs.nextcloud.com/server/latest/developer_manual/client_apis/LoginFlow/index.html?fbclid=IwAR0baOVOTHifkHXi0QXeAwk3qAMC3oH939dZH1kgZqsiLfBbT6L_QadcEGU


## AI

### PC 개발환경

[딥러닝 개발환경](https://theorydb.github.io/dev/2020/02/14/dev-dl-setting-local-python/)


[머신러닝 교과서](https://theorydb.github.io/review/2024/02/21/review-book-ml-with-pytorch/)


### 구글 Colab

[개발 환경 2019](https://theorydb.github.io/dev/2019/08/23/dev-ml-colab/)

[다른 설명](https://yeko90.tistory.com/entry/how-use-google-colab)

[또다른 설명](https://hongong.hanbit.co.kr/%EA%B5%AC%EA%B8%80-%EC%BD%94%EB%9E%A9-google-colab%EC%9C%BC%EB%A1%9C-%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%EB%8F%8C%EB%A6%AC%EA%B8%B0/)

[코랩, 주피터](https://modulabs.co.kr/blog/google-colab-kaggle/)


[랭체인](https://wikidocs.net/231153)


[Llama MT](https://kaitchup.substack.com/p/llama-2-mt-turn-llama-2-into-a-translation)

[ALMA](https://arxiv.org/abs/2309.11674)

[ALMA GITHUB](https://github.com/fe1ixxu/ALMA)


