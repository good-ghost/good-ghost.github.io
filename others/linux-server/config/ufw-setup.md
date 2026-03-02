---
title: "UFW 방화벽 설치"
sort: 1
---

# UFW 방화벽 설치

[참고](https://lindarex.github.io/ubuntu/ubuntu-ufw-setting/)

```danger
Oracle Cloud에 Ubuntu를 설치하여 사용하는 경우 기본 방화벽으로 iptables가 설치되어 있습니다.

가급적이면 이 방화벽을 다른것으로 대체하지 않는 것을 추천합니다. 최악의 경우 시스템을 리셋해야 할 수 있습니다.
```

## 설치 여부 확인

``` bash
sudo ufw status
```

````result
```
Command ufw not found
```

UFW 방화벽이 설치가 되지 않은 것입니다. 설치를 하여야 합니다.
````

````result
```
Status: inactive
```

위와 유사한 메시지가 표시되면 UFW 방화벽이 설치는 되었지만 서비스를 시작하지 않은것입니다.
````

## UFW 방화벽 설치

``` bash
sudo apt install ufw
```

만일 **ssh**를 사용하여 터미널로 접속하고 있다면 가장 먼저 ssh 접속을 UFW 방화벽에서 허용하도록 등록을 하여야합니다.

``` bash
sudo ufw allow ssh
```

## UFW 방화벽 서비스 시작

아래의 명령으로 UFW 방화벽 서비스를 시작합니다.
서비스를 시작하기전에 반드시 `ssh` 22번 포트가 허용이 되어있는지 확인하십시오. 포트가 막히면 터미널에 접속할 수 없습니다. 이 경우에는 로컬 시스템으로 접속을 하여야 합니다.

``` bash
sudo ufw enable
```

```
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
```

위와 같이 표시되는 질문에 `y`를 입력하면 아래와 같은 메시지가 표시되면 방화벽 서비스를 시작합니다.

```
Firewall is active and enabled on system startup
```

## UFW 방화벽 서비스 확인

UFW 방화벽 서비스가 정상 동작하는지 확인해 봅니다.

``` bash
sudo ufw status
```

````result
```
Status: active

To                         Action      From
--                         ------      ----
22                         LIMIT       Anywhere
22 (v6)                    LIMIT       Anywhere (v6)
```

위와 유사하게 표시되면 정상적으로 동작을 하는것입니다.
SSH 포트 22가 허용된 것을 확인할 수 있습니다.
````

사용중인 터미널을 닫지 않고, 새 터미널을 열어서 ssh로 접속되는지를 확인해봅니다.
접속이 안 되면 어딘가 설정이 잘못된 겁니다.

**Oracle Cloud** 사용시 접속이 안될 경우 

1. [Oracle Cloud의 웹 터미널로 접속하는 방법](./oracle-cloud-terminal.html)

2. [Oracle Cloud에서 UFW, firewalld 방화벽을 사용하는 방법](./oracle-cloud-firewall.md)
