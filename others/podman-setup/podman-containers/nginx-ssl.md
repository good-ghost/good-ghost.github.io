---
title: "NGINX 컨테이너 SSL 설정"
sort: 2
---

# NGINX SSL 설정

## 기본 환경

 - Oracle Cloud Free-tier

 - Ubuntu 22.04

 - Podman 3.4.4

   Docker 대신 Cockpit 서버매니저에서 관리도구를 제공하는 Podman을 사용합니다.

 - 웹서버 도메인

   웹서버는 [공식 NGINX 이미지](https://hub.docker.com/_/nginx)를 도커 컨테이너로 사용하여 서비스한다고 가정합니다.

   http 프로토콜을 사용하여 접속하는 웹서버의 도메인 이름. 본인이 사용하는 명칭을 적용합니다.
  
   `my-domain.co.kr`

   ``` warning
   iptime.org 또는 duckdns.org 같은 DDNS 서비스를 사용하는 경우 전체 도메인 이름을 사용하여야 합니다.

   예) `my-server.iptime.org`, `my-server.duckdns.org`
   ```

 - Web 컨텐츠 디렉토리

   `/opt/web/_site` : 웹서버의 index.html 파일이 저장되어 있는 루트 폴더

````note

Oracle Cloud에서 80 포트 열기

1. Cloud의 VM 인스턴스 VNet 설정에서 80 포트를 열어 줍니다. 이때 반드시 Source는 'All'로 두어야 합니다.

   [[여기]](/others/linux-server/oracle-cloud-port-open.html)를 참조하여 포트 설정을 진행합니다.

2. 80 포트로 웹접속이 되는지를 확인합니다. 만약 연결이 안될 경우 서버에 ssh로 접속하여 아래의 3 ~ 5 절차를 수행합니다.

3. `iptables`를 사용하여 80, 443 포트를 열어 줍니다.

   ``` bash
   $ sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
   $ sudo iptables -I INPUT 7 -m state --state NEW -p tcp --dport 443 -j ACCEPT
   ```

4. 설정값을 저장합니다.

   ``` bash
   $ sudo  netfilter-persistent save
   ```

   아래와 같은 메시지를 표시하며 저장됩니다.

   ``` bash
   run-parts: executing /usr/share/netfilter-persistent/plugins.d/15-ip4tables save
   run-parts: executing /usr/share/netfilter-persistent/plugins.d/25-ip6tables save
   ```

5. `iptables` 서비스를 재시작 합니다.

   ``` bash
   $ sudo systemctl restart iptables
   $ sudo service iptables restart
   ```
````

## Let's Encrypt 인증서 발급

정상적으로 웹서버가 80 포트에서 동작하여야 진행 할 수 있습니다.

### certbot 설치

 - Ubuntu 패키지로 설치

   ``` bash
   $ sudo apt update
   $ sudo apt install certbot
   ```

 - Snap 패키지 관리자로 설치

   Ubuntu 패키지로 설치할 경우 의존 패키지가 많이 설치되므로 그런것을 피할려면 Snap 패키지 관리자를 사용하면 됩니다.

   ``` bash
   $ sudo snap install core
   $ sudo snap refresh core
   $ sudo snap install --classic certbot
   ```

   ````note
   만약 snap이 설치되지 않은 경우 아래의 명령으로 설치하면 됩니다.

   ``` bash
   $ sudo apt update
   $ sudo apt install snapd
   ``` 

   살치된 `snapd` 서비스를 활성화 해주어야 합니다.

   ``` bash
   $ sudo systemctl enable --now snapd.socket
   ```
   ````

### certbot으로 인증서 발급받기

다음의 명령을 실행합니다.

``` bash
$ sudo certbot certonly --webroot -w /opt/web/_site
```

명령이 표시되는 부분에 발급자의 이메일 주소를 입력하고, 뉴스레터 수신여부(Y/N) 입력하면 인증서가 발급이 됩니다.

발급되는 파일은 다음과 같습니다.

 - `/etc/letsencrypt/live/good-ghost.duckdns.org/fullchain.pem`
 - `/etc/letsencrypt/live/good-ghost.duckdns.org/privkey.pem`

만약 오류가 발생하는 경우, 대부분의 경우에는 80 포트로 접속이 안되는 경우입니다.

`curl localhost:80` 명령으로 웹서버가 정상 동작하는지를 확인하고, 정상 동작하는 경우 `curl http://my-domain.co.kr:80` 명령으로 인터넷으로 웹서버가 정상동작하는지를 확인해봅니다.

### NGINX 웹서버에 SSL 적용하기

 - NGINX 도커를 기본값으로 사용중이라면 먼저 컨테이너의 config 파일을 복사를 해야합니다. config를 저장하는 폴더는 `/opt/docker/nginx`라고 가정하겠습니다.

   ``` bash
   $ sudo docker cp nginx:/etc/nginx/conf.d/default.conf /opt/docker/nginx/nginx.conf
   ```

   에디터로 `/opt/docker/nginx/nginx.conf` 파일을 열고 아래의 내용을 추가합니다. 기본적으로 이 파일에는 80 포트에 대한 설정값만을 포함하고 있습니다.

   ```
   # ... 80번 포트 설정
   server{

       listen 443 ssl http2;
       server_name my-domain.co.kr;

       # SSL Certificate
       ssl_certificate /etc/nginx/cert/fullchain.pem;
       ssl_certificate_key /etc/nginx/cert/privkey.pem;

       location / {
           try_files $uri $uri/ =404;
           proxy_set_header Host $http_host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }

   }
   ```

   ``` danger
   `443` 포트를 사용하는 `server` 설정에서 `location / {}`에 `proxy_pass localhost:80;`을 절대 사용하지 않습니다.

   대신 `try_files $uri $uri/ =404;`를 사용하도록 합니다.
   
   `80` 포트 리다이렉트를 지정하였는데, `443` 포트의 설정이 `proxy_pass localhost:80;`를 사용할 경우 리다이렉트 무한루프를 돌게 됩니다.
   ```

 - 다음의 명령으로 NGINX 도커 컨테이너를 제거합니다.

   NGINX 컨테이너 이름이 `myweb`라고 가정합니다.

   ``` bash
   $ sudo docker rm myweb
   ```

 - 아래의 명령으로 NGINX 컨테이너를 시작합니다.

   ``` bash
   $ sudo docker run -itd \
       --name myweb \
       -p 80:80 -p 443:443 \
       -v /opt/docker/nginx/nginx.conf:/etc/nginx/conf.d/default.conf:ro \
       -v /opt/web/_site:/usr/share/nginx/html:ro \
       -v /etc/letsencrypt/live/my-domain.co.kr/fullchain.pem:/etc/nginx/cert/fullchain.pem \
       -v /etc/letsencrypt/live/my-domain.co.kr/privkey.pem:/etc/nginx/cert/privkey.pem \
   nginx
   ```

 - `https://my-domain.co.kr` 주소로 접속이 정상적으로 이루어지는지 확인을 합니다.

 - 만일 80 포트로 접속하는것을 허용하지 않으려면, 80 포트로 접속하는 모든 요청을 443 보안 포트로 리다이렉트하면 됩니다.

   `/opt/docker/nginx/nginx.conf` 파일의 80 포트 설정 부분을 아래와 같이 수정을 합니다.

   ```
   server {

       listen 80;
       server_name my-domain.co.kr;
       return 301 https://my-domain.co.kr$request_uri;

   }
   ```

   NGINX 도커 컨테이너를 재시작하고 `http://my-domain.co.kr` 주소로 접속하여 https:// 주소로 리다이렉트 되는지를 확인합니다.

### 인증서 갱신하기

기본적으로 Let's Encrypt의 인증서는 90일간 유효합니다. 아래의 절차로 인증서를 갱신할 수 있습니다.

 - 인증서 확인

    ``` bash
    $ sudo certbot certificates
    ```

    ````result
    ```
    Saving debug log to /var/log/letsencrypt/letsencrypt.log
    
    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    Found the following certs:
      Certificate Name: my-domain.co.kr
        Serial Number: ********************************
        Key Type: RSA
        Domains: my-domain.co.kr
        Expiry Date: yyyy-MM-dd hh:mm:ss+00:00 (VALID: nn days)
        Certificate Path: /etc/letsencrypt/live/my-domain.co.kr/fullchain.pem
        Private Key Path: /etc/letsencrypt/live/my-domain.co.kr/privkey.pem
    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    ```
    ````

 - 인증서 갱신

   인증서를 갱신할 경우엔 80 포트를 443 포트로 리다이렉트 하고 있으면 오류가 발생을 합니다.
   nginx.conf에서 80 포트 설정을 원상 복구하고 아래 명령을 실행을 하여야 합니다.

    ``` bash
    $ sudo certbot renew --dry-run
    ```

    ````result
    ```
    Saving debug log to /var/log/letsencrypt/letsencrypt.log

    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    Processing /etc/letsencrypt/renewal/my-domain.co.kr.conf
    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    Simulating renewal of an existing certificate for my-domain.co.kr

    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    Congratulations, all simulated renewals succeeded:
      /etc/letsencrypt/live/my-domain.co.kr/fullchain.pem (success)
    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    ```
    ````

 - 인증서 삭제

    ``` bash
    $ sudo certbot delete --cert-name my-domain.co.kr
    ```

 - 인증서 자동 갱신

   Ubuntu에 설치된 인증서는 `systemd` 데몬에 의해 기본적으로 하루에 두번 갱신하도록 설정이 되어있습니다.

   아래의 명령으로 관련 파일을 찾아봅니다.

   ``` bash
   $ sudo dpkg -L certbot | grep systemd
   ```

   아래와 같이 표시됩니다.

   ````result
   ```
   /lib/systemd
   /lib/systemd/system
   /lib/systemd/system/certbot.service
   /lib/systemd/system/certbot.timer
   ```
   ````

   내용을 한번 보면...

   - certbot.service

     ```
     [Unit]
     Description=Certbot
     Documentation=file:///usr/share/doc/python-certbot-doc/html/index.html
     Documentation=https://certbot.eff.org/docs
     [Service]
     Type=oneshot
     ExecStart=/usr/bin/certbot -q renew
     PrivateTmp=true
     ```

   - certbot.timer

     ```
     [Unit]
     Description=Run certbot twice daily
     
     [Timer]
     OnCalendar=*-*-* 00,12:00:00
     RandomizedDelaySec=43200
     Persistent=true
     
     [Install]
     WantedBy=timers.target
     ```

   위의 설정대로라면 매일 자정 및 정오에 `/usr/bin/certbot -q renew`을 실행하여 인증서를 갱신하도록 하고 있습니다.

   인증서가 90일동안 유효하니, 갱신주기를 한달에 한번으로 바꾸어 보겠습니다.

   `certbot.timer` 파일의 `OnCalendar` 부분을 다음과 같이 수정하고 저장을 합니다.

   ```
   OnCalendar=*-*-01 01:00:00
   ```

   위와 같이 변경을 하면 매달 1일 새벽 1시에 갱신을 실행할 것입니다.

### 80 포트를 443 포트로 리다이렉트 하고 있는 경우 자동 갱신하기

80 포트 리다이렉트를 하더라도 웹 서비스가 정상적일 경우에만 아래 사항을 적용합니다.

80 포트 리다이렉트를 하고 있을 경우 아래와 같은 `systemd` 설정만으로는 자동 갱신이 실패하게 됩니다.

```
ExecStart=/usr/bin/certbot -q renew
```

NGINX의 80 포트가 리다이렉트하지 않도록 변경을 하고 `certbot` 갱신을 실행하도록 하여야 합니다.

먼저 `/opt/docker/nginx/nginx/conf`를 사용하여 `redirect.conf` 파일과 `normal.conf` 파일을 생성합니다.

 - `redirect.conf` - 80 포트를 443 포트로 리다이렉트하는 config 파일

 - `normal.conf` - 80 포트를 리다이렉트하지 않는 config 파일

`$ sudo nano /usr/bin/renew-certbot.sh` 명령으로 텍스트 파일을 생성을 하고 아래의 내용을 저장을 합니다.

``` bash
#! /bin/bash

docker stop nginx
sleep 1
cp /opt/docker/nginx/normal.conf /opt/docker/nginx/nginx.conf
sleep 1
docker start nginx
sleep 1
/usr/bin/certbot -q renew
sleep 1
docker stop nginx
sleep 1
cp /opt/docks/nginx/redirect.conf /opt/docks/nginx/nginx.conf
sleep 1
docker start nginx
```

각 명령어 마다 1초식 지연되도록 sleep 명령어를 추가했습니다.

파일을 저장합니다.

아래의 명령으로 쉘 스크립트를 실행가능한 형식으로 변경합니다.

``` bash
$ sudo chmod +x /usr/bin/renew-certbot.sh 
```

다음으로 `certbot.service` 파일의 `ExecStart` 부분을 아래와 같이 수정하고 저장을 합니다.

```
ExecStart=/usr/bin/renew-certbot.sh
```

```warning
**80 포트를 443 포트로 리다이렉트 하고 있는 경우 갱신하기** 부분은 아직 직접 검증하지 않았습니다.
```

