---
title: "Cockpit 설치"
sort: 1
---

# Cockpit 서버관리자 설치 및 설정

Ubuntu 또는 Debian 서버에 Cockpit을 설치합니다.

Ubuntu 22.04 LTS 이상, Debian 11.0(Bullseye) 이상의 버전에 설치하는 것을 기준으로 하지만,
가급적 Ubuntu에 설치를 합니다. OS의 수명주기가 Ubuntu가 훨씬 길어서 관리하기가 용이합니다.

- Ubuntu EOL : 10년

- Debian EOL : 3년

수명주기가 종료되면 보안 패치등이 이루어지지 않습니다.

## Cockpit 서버관리자 설치

1. 시스템 업데이트

    Cockpit을 설치하기 전에 Ubuntu / Debian 시스템을 최신 상태로 업데이트를 합니다.

    ``` bash
    sudo apt update && sudo apt upgrade
    ```

2. Cockpit 설치

    가급적 Cockpit의 최신 버전을 적용하기 위하여 Backports를 사용하여 설치합니다.

    ``` bash
    echo ${VERSION_CODENAME}
    sudo apt install -t ${VERSION_CODENAME}-backports cockpit
    ```

3. Cockpit 서비스를 시작합니다.

    ``` bash
    sudo systemctl start cockpit.socket
    ```

4. Cockpit 서비스를 시스템 시작 서비스로 등록합니다.

    ``` bash
    sudo systemctl enable cockpit.socket
    ```

5. Cockpit 서비스가 정상적으로 동작하는지 확인해봅니다.

    ``` bash
    systemctl status cockpit.socket
    ```

    아래와 같은 메시지가 표시됩니다. `Active`의 값이 `active`인것을 확인합니다.

    ```
    ● cockpit.socket - Cockpit Web Service Socket
         Loaded: loaded (/lib/systemd/system/cockpit.socket; enabled; vendor preset: enabled)
         Active: active (listening) since Thu 2024-01-18 12:28:43 KST; 1 day 3h ago
       Triggers: ● cockpit.service
           Docs: man:cockpit-ws(8)
         Listen: [::]:9090 (Stream)
          Tasks: 0 (limit: 1076)
         Memory: 340.0K
            CPU: 10ms
         CGroup: /system.slice/cockpit.socket

    Jan 18 12:28:43 mgwoo01 systemd[1]: Starting Cockpit Web Service Socket...
    Jan 18 12:28:43 mgwoo01 systemd[1]: Listening on Cockpit Web Service Socket.
    ```

## Cockpit 서버관리자 설정

### Cockpit의 서비스 포트 변경

필요한 경우 Cockpit의 서비스 포트를 기본값인 `9090`에서 다른 포트로 변경합니다.

``` bash
sudo nano /etc/systemd/system/sockets.target.wants/cockpit.socket
```

파일 cockpit.socket의 위치는 Ubuntu 기준입니다.

아래의 내용에서 `9090` 포트를 변경을 원하는 포트로 변경을 합니다. 여기서는 `29090`으로 가정합니다.

```
[Socket]
ListenStream=
ListenStream=9090
```

Cockpit 서비스를 다시 시작합니다.

```
sudo systemctl daemon-reload
sudo systemctl restart cockpit.socket
```

### 서버관리자 권한 부여

Cockpit에 관리자로 로그인하는 Linux 사용자는 `sudo` 권한을 가지고 있어야 합니다.

아래의 명령으로 권한을 부여합니다. '<>' 기호없이 Linux 사용자 Id를 입력합니다.

``` bash
sudo usermod -aG sudo <your-user-name>
```

### 방화벽 서비스에 Cockpit 서비스를 등록

Linux 서버에서 방화벽 서비스를 사용하고 있는 경우, Cockpit 서비스에서 사용하는 포트 `9090`(또는 변경했을 경우 변경된 포트, 예 `29090`)을 방화벽에 등록을 하여야 합니다.

1. iptables 서비스를 사용하는 경우

    iptables 서비스 상태를 확인합니다.

    ``` bash
    sudo iptables -L
    ```

    `9090` 포트를 등록합니다.
    `INPUT n`은 위에서 상태를 확인하고 `INPUT` 테이블의 내용을 보고 적당한 순서에 맞는 `n`을 사용합니다.

    ``` bash
    sudo iptables -I INPUT 1 -m state --state NEW -p tcp --dport 9090 -j ACCEPT
    ```

    설정값을 저장합니다.

    ``` bash
    sudo netfilter-persistent save
    ```

2. UFW 서비스를 사용하는 경우

    1. UFW 상태 확인

        아래의 명령으로 방화벽 서비스의 상태를 확인합니다.
    
        ``` bash
        sudo ufw status
        ```

        서비스가 비활성화 되어있다면 아래의 명령으로 활성화 합니다.
    
        ``` bash
        sudo ufw enable
        ```

    2. Cockpit 서비스 등록

        아래의 명령을 사용하여 Cockpit 서비스를 UFW 방화벽에 등록을 합니다.

        ``` bash
        sudo ufw allow 9090
        ```

        명령이 정상적으로 실행이 되면 다음과 같은 메시지가 표시가 됩니다.

        ````result
        ```
        Rules updated
        Rules updated (v6)
        ```
        ````

        TIPv4 및 IPv6 룰셋을 업데이트 하였습니다. 이제 `9090` 포트를 사용하여 Cockpit 서비스에 접속할 수 있습니다.
    
    3. UFW 방화벽에서 Cockpit 서비스를 확인

        아래의 명령을 사용하여 UFW 방화벽에 등록된 서비스를 확인 할 수 있습니다.
    
        ```
        sudo ufw status
        ```

        아래의 메시지 처럼 `9090` 포트가 Incoming 연결을 위해 열려 있는것을 확인할 수 있습니다.
    
        ````result
        ```
        Status: active
        
        To                         Action      From
        --                         ------      ----
        9090                       ALLOW       Anywhere
        9090 (v6)                  ALLOW       Anywhere (v6)
        ```
        ````

3. firewalld 서비스를 확인 하는 경우

    1. firewalld 상태 확인

        아래의 명령으로 방화벽 서비스의 상태를 확인합니다.

        ``` bash
        sudo firewall-cmd --list-all
        ```

    2. Cockpit 서비스 등록

        아래의 명령을 사용하여 Cockpit 서비스를 FIREWALLD 방화벽에 등록을 합니다.

        ``` bash
        sudo firewall-cmd –permanent –zone=public –add-service=cockpit
        sudo firewall-cmd –permanent –zone=public –add-port=9090/tcp
        ```

        방화벽 서비스를 다시 시작합니다.

        ``` bash
        firewall-cmd –reload
        ```

    3. firewalld 방화벽에서 Cockpit 서비스를 확인

        아래의 명령을 사용하여 UFW 방화벽에 등록된 서비스를 확인 할 수 있습니다.

        ```
        sudo firewall-cmd --list-all
        ```

        아래의 메시지 처럼 `9090` 포트가 Incoming 연결을 위해 열려 있는것을 확인할 수 있습니다.

        ````result
        ```
        public
            target: default
            icmp-block-inversion: no
            interfaces:
            sources:
            services: ssh dhcpv6-client
            ports: 8080/tcp
            protocols:
            masquerade: no
            forward-ports:
            source-ports:
            icmp-blocks:
            rich rules:
        ```
        ````

    4. 서비스 제거

        ```
        sudo firewall-cmd --permanent --service cockpit --remove-port=9090/tcp
        ```

방화벽이 정상적으로 설정이 되어있다면 웹브라우져를 사용하여 `9090` 포트를 통해 Cockpit 서비스에 접속할 수 있습니다.

````note
Cockpit 서비스에 SSL을 적용하려면 Cockpit의 인증서 폴더에 인증서를 복사하여야 합니다.

Let's Encrypt의 인증서를 사용하여 인증서를 등록합니다.
Let's Encrypt 인증서는 `80` 포트를 사용하는 웹서비스가 동작을 하고 있어야 합니다.
웹서버를 설치한 후에 설정을 하도록 합니다.

인증서를 발급 받는 방법은 [[여기]](/others/podman-setup/nginx-ssl.html)를 참조하시기 바랍니다.

**Cockpit 서비스에 인증서 적용**

``` bash
sudo bash -c "cat /etc/letsencrypt/live/registered-domain-name/fullchain.pem \
/etc/letsencrypt/live/registered-domain-name/privkey.pem \
> /etc/cockpit/ws-certs.d/registered-domain-name.cert"
```

Cockpit 서비스를 다시 시작합니다.

``` bash
sudo systemctl daemon-reload
sudo systemctl restart cockpit.socket
```
````

