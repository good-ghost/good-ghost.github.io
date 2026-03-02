---
title: "Fail2ban 침입방지 도구 설치 및 설정"
sort: 4
---

# Fail2ban 침입방지 도구 설치 및 설정

## 전제 조건

- Ubuntu 서버 22.04

- $ sudo 권한이 있는 루트가 아닌 사용자.

- 방화벽 설정 (iptables / ufw / firewalld)

Fail2ban은 외부의 무차별 대입 공격으로부터 Linux 서버를 보호하기 위하여 사용하는 IPS(침입 방지 소프트웨어)입니다.

Fail2ban은 서비스의 로그 파일(e.f `/var/log/auth.log`)을 스캔하고 반복적으로 시도되는 잘못된 암호, 취약점 찾기 등과 같은 악의적인 로그인을 시도하는 IP 주소를 차단합니다. Fail2ban은 또한 iptables와 같은 여러 방화벽 백엔드를 지원합니다. ufw 및 방화벽. 또한 모든 차단된 로그인 시도에 대해 이메일 알림을 설정할 수 있습니다.

## Fail2ban 설치

방화벽을 설치 및 구성한 후 이제 서버에 Fail2ban 패키지를 설치합니다.

먼저 Ubuntu 리포지토리를 업데이트합니다.

``` bash
$ sudo apt update
```

아래 명령을 사용하여 Fail2ban 패키지를 설치합니다.

``` bash
$ sudo apt install fail2ban
```

Fail2ban 설치가 완료되면 Fail2ban 서비스를 활성화하고, 서비스를 시작합니다.

``` bash
$ sudo systemctl enable fail2ban
$ sudo systemctl start fail2ban
```

Fail2ban 서비스가 정상적으로 동작하는지 상태를 확인합니다.

``` bash
$ sudo systemctl status fail2ban
```

````result
```
● fail2ban.service - Fail2Ban Service
     Loaded: loaded (/lib/systemd/system/fail2ban.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2024-01-18 13:50:18 KST; 3 days ago
       Docs: man:fail2ban(1)
   Main PID: 3954 (fail2ban-server)
      Tasks: 5 (limit: 1076)
     Memory: 21.7M
        CPU: 3min 40.884s
     CGroup: /system.slice/fail2ban.service
             └─3954 /usr/bin/python3 /usr/bin/fail2ban-server -xf start

Jan 18 13:50:18 server-name systemd[1]: Started Fail2Ban Service.
Jan 18 13:50:18 server-name fail2ban-server[xxxx]: Server ready
```
````

## Fail2ban 설정

Fail2ban 설정 파일은 `/etc/fail2ban` 폴더에 저장됩니다.

`fail2ban.conf` 파일은 Fail2ban의 기본 설정 파일입니다.

`jail.conf` 파일은 Fail2ban 차단 설정의 예입니다.

`action.d` 폴더에는 메일 설정 및 방화벽 설정과 같은 fail2ban 작업 설정이 포함되어 있습니다.

`jail.d` 폴더에는 fail2ban 차단에 대한 추가 설정이 포함되어 있습니다.

Fail2ban 설정을 시작하려면 아래 명령으로 기본 차단 설정파일 `jail.conf`을 `jail.local`로 복사해야 합니다.

``` bash
$ sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

이제 nano 편집기를 사용하여 `jail.local` 파일을 엽니다.

``` bash
$ sudo nano /etc/fail2ban/jail.local
```

먼저 `ignoreip` 옵션의 주석을 해제하고 IP 주소를 추가합니다. `ignoreip` 옵션 내의 모든 IP 주소는 Fail2ban에 의해 차단되지 않습니다.

```
ignoreip = 127.0.0.1/8 ::1 # 기타 내부망을 사용하는 경우 192.168.1.0/24 192.168.10.20 같이 추가
```

차단 설정의 경우 필요에 따라 변경할 수 있습니다. 이 예에서 전역 설정값 `bantime`은 1day, `findtime`은 10분, `maxretry`는 최대 5회입니다.

`bantime` 옵션은 차단된 IP 주소가 서버에 액세스하는 것을 금지하는 시간입니다. `findtime` 옵션은 차단 조치 전 실패 횟수 사이의 기간입니다. 그리고 `maxretry` 옵션은 IP 주소가 차단되는 데까지 실패하는 횟수입니다.

```
bantime  = 1d
findtime  = 10m
maxretry = 5
```

[sshd] 섹션에 아래와 같이 추가합니다.

```
enabled  = true
bantime  = 1w
maxretry = 3
findtime = 1d
port     = ssh
logpath  = %(sshd_log)s
backend  = systemd
```

이 예에서는 SSH 서비스용 감옥을 활성화하지만 sshd 감옥의 전역 기본 구성도 재정의합니다. `bantime`은 1주일이며 최대 `maxretry` 횟수는 3회, `findtime`은 10분입니다.

Fail2ban의 버그가 있으므로 `backend`는 반드시 `systemd` 로 설정하십시오.

Fail2ban의 기본 동작은 IP 주소만 차단하는 것입니다. 그러나 IP 주소가 금지될 때마다 메일 알림을 설정할 수도 있습니다.

액션 옵션을 아래와 같이 변경하고 기본 발신인과 목적지 메일 주소를 변경합니다.

```
action = %(action_mw)s
destemail = 
```

다음으로 UFW 방화벽 을 사용하는 경우 통합을 위해 아래와 같이 `banaction` 옵션을 `ufw`로 변경해야 합니다.

```
banaction = ufw
```

파일을 저장하고 닫습니다.

이제 다음 명령을 실행하여 Fail2ban 서비스를 다시 시작하여 `jail.local`의 새로운 변경 사항을 적용하십시오.

``` bash
$ sudo systemctl restart fail2ban
```

## fail2ban-client를 사용하여 Fail2ban 상태 확인

Fail2ban 서비스와 상호 작용하기 위한 명령행 도구 fail2ban-client를 사용합니다.

fail2ban 설치 및 설정을 확인하려면 아래의 fail2ban-client 명령을 실행합니다.

``` bash
$ sudo fail2ban-client ping
```

`Server replied: pong`과 같은 메시지가 표시되면 Fail2ban이 오류 없이 실행되고 있음을 의미합니다.

다음으로 아래의 fail2ban-client 명령을 실행하여 sshd jail 상태를 확인합니다.

``` bash
$ sudo fail2ban-client status sshd
```

아래에서 sshd 감옥의 자세한 상태를 볼 수 있습니다. 여기에는 SSH 서비스에 대한 로그 파일과 sshd 감옥에서 금지된 IP 주소 목록이 포함됩니다.

````result
```
Status for the jail: sshd
|- Filter
|  |- Currently failed: 200
|  |- Total failed:     5476
|  `- Journal matches:  _SYSTEMD_UNIT=sshd.service + _COMM=sshd
`- Actions
   |- Currently banned: 581
   |- Total banned:     581
   `- Banned IP list: xxx.xxx.xxx.xxx .........(차단된 IP목록)
```
````

서버에 터미널 접속이 인터넷에 공개되어 있다면 `Banned IP list`에 순식간에 정말 어마어마하게 목록이 쌓입니다. 

이제 sshd 차단 설정의 자세한 살장값을 확인하려면 fail2ban-client 명령을 사용할 수 있습니다.

sshd jail에 대한 `bantime`을 확인합니다.

``` bash
$ sudo fail2ban-client get sshd bantime
```

sshd jail에 대한 `maxrtey`을 확인합니다.

``` bash
$ sudo fail2ban-client get sshd maxretry
```

sshd jail에 대한 `findtime`을 확인합니다.

``` bash
$ sudo fail2ban-client get sshd findtime
```

sshd 감옥에서 금지하려면 다음 명령을 사용할 수 있습니다. 그리고 sshd jail에 대한 기본 banaction으로 ufw의 출력을 얻어야 합니다.
sshd jail에 대한 `actions`을 확인합니다. 통합된 방화벽의 출력을 보여줍니다. 

``` bash
$ sudo fail2ban-client get sshd actions
```

아래 명령을 사용하여 sshd jail에 대한 기본 `ignoreip`를 확인할 수도 있습니다. sshd를 위하여 별도 설정을 하지 읺았으므로 전역 설정값을 표시합니다.

``` bash
$ sudo fail2ban-client get sshd ignoreip
```

## fail2ban-client를 사용하여 IP 차단 및 차단 해제

fail2ban-client 명령을 사용하여 IP 주소를 차단 및 차단 해제하는 방법입니다.

sshd 서비스에 대하여 IP 주소를 수동으로 차단하려면 아래의 fail2ban-client 명령을 사용할 수 있습니다. 차단하려는 IP 주소로 IP-ADDRESS를 변경하십시오.

``` bash
$ sudo fail2ban-client set sshd banip IP-ADDRESS
```

sshd 서비스에 대하여 차단된 IP 주소를 해제하려면 아래의 fail2ban-client 명령을 사용할 수 있습니다. 차단을 해제하려는 IP 주소로 IP-ADDRESS를 변경해야 합니다.

``` bash
$ sudo fail2ban-client set sshd unbanip IP-ADDRESS
```

이제 차단 또는 차단 해제된 IP 주소를 아래의 fail2ban-client 명령을 사용하여 확인할 수 있습니다.

``` bash
$ sudo fail2ban-client status sshd
```

IP 주소를 수동으로 차단하는 경우 차단된 IP 주소 목록에서 해당 IP 주소가 있는지 확인하십시오. 또, IP 주소를 차단 해제하면 차단된 IP 주소 목록에서 해당 IP 주소가 사라지는지 확인하십시오.
