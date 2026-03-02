---
title: "iptables 방화벽 설정"
sort: 3
---

# iptables 방화벽 설정

## iptables 설치

````note
Ubuntu 시스템의 경우 UFW 방화벽이 기본적으로 설치가 되어있습니다.

iptables 방화벽을 사용하려면 먼저 UFW 방화벽을 비활성화 해야합니다.

현재 UFW 방화벽의 상태를 먼저 살펴봅니다.

```
sudo systemctl status ufw
```

방화벽이 사용중이고 활성화되어 있으면 다음의 명령을 실행하여 비활성화 시키고 시스템을 재부팅합니다.

```
sudo systemctl stop ufw
sudo systemctl disable --now ufw
sudo shutdown -r now
```

UFW 방화벽의 상태를 확인합니다.

```
sudo systemctl status ufw
```

원하는 경우, 아래의 명령으로 UFW 방화벽을 시스템에서 제거할 수 있습니다.

```
sudo apt autoremove --purge ufw
```
````

아래의 명령으로 `iptables`를 설치합니다.

```
sudo apt install iptables-persistent
```

iptables 설치를 확인하고 활성화 한 후에 시스템을 재부팅합니다.

```
sudo systemctl status iptables
sudo systemctl enable iptables
sudo shutdown -r now
```

iptables의 상태를 확인합니다.

```
sudo systemctl status iptables
```

활성화가 안되어 있다면, 서비스 정의에 Alias가 설정되지 않아서 입니다.

텍스트에디터를 사용해 서비스 정의 파일을 수정합니다.

```
sudo nano /lib/systemd/system/iptables.service
```

에디터를 활용하여 가장 아래에 내용 추가합니다.

```
Alias=iptables.service
```

서비스를 비활성화 했다가 다시 활성화하고, 시스템을 재부팅을 합니다.

```
sudo systemctl disable iptables
sudo systemctl enable iptables
sudo shutdown -r now
```

iptables 서비스를 상태를 확인하고, 서비스를 재시작합니다.

```
sudo systemctl status iptables
sudo systemctl restart netfilter-persistent.service
```

현재 설정을 저장합니다.

```
sudo netfilter-persistent save
```

iptables의 현재 설정을 확인합니다.

```
sudo iptables -L
sudo iptables -S
```

iptables의 설정을 초기화합니다.

```
sudo iptables -F
sudo iptables -X
sudo iptables -L
sudo iptables -S
```

대부분의 설명에서는 콘솔에서 명령을 하나씩 입력해서 설정을 하는것이지만, SSH를 통해서 설정을 하는 경우 순서가 잘못될 경우 연결이 끊어질 수 있습니다.

텍스트에디터로 설정 파일을 직접 수정해서 적용하도록 하겠습니다.

```
sudo nano /etc/iptables/rules.v4
```

아래의 내용으로 수정하고 저장을 합니다.

```
*filter
:INPUT DROP [0:0]
:FORWARD DROP [0:0]
:OUTPUT ACCEPT [70:9547]
-A INPUT -m state --state INVALID -j DROP
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p udp -m udp --sport 123 -j ACCEPT
-A INPUT -p tcp -s 192.168.1.0/24 --dport 22 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
```

저장된 iptables의 규칙을 다시 로드하여 시스템에 적용합니다.

```
sudo netfilter-persistent reload
```

````note
podman을 사용한다면 아래와 같은 초기값을 설정하여야 합니다.

```
*filter
:INPUT DROP [0:0]
:FORWARD DROP [0:0]
:OUTPUT ACCEPT [70:9547]
:CNI-ADMIN - [0:0]
:CNI-FORWARD - [0:0]
-A INPUT -m state --state INVALID -j DROP
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p udp -m udp --sport 123 -j ACCEPT
-A INPUT -p tcp -s 192.168.1.0/24 --dport 22 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -m comment --comment "CNI firewall plugin rules" -j CNI-FORWARD
-A CNI-FORWARD -o cni-podman0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A CNI-FORWARD -o cni-podman0 -j ACCEPT
-A CNI-FORWARD -i cni-podman0 ! -o cni-podman0 -j ACCEPT
-A CNI-FORWARD -i cni-podman0 -o cni-podman0 -j ACCEPT
```

윗 설정 아랫 부분의 `cni-podman0` 설정은 Podman에서 네트워크가 추가될때마다 `cni-podman1', `cni-podman2`... 추가해주어야 합니다.
````

`-A INPUT -p tcp -s 192.168.1.0/24 --dport 22 -j ACCEPT`

SSH 포트는 네부네트워크에만 허용을 하고 있습니다. 완전히 모든 접속에 허용을 하려면, 

`-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT`

위와 같이 설정을 하면 됩니다.

특정 포트를 열려면,

```
-A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
```

위와 같이 rules.v4에 추가하고 저장한 후에...

```
sudo netfilter-persistent reload
```

콘솔에서 명령으로 사용하려면,

```
sudo iptables -I INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
```

이렇게 명령으로 설정을 하면 시스템에 저장이 안되기때문에 아래의 명령으로 재부팅 후에도 적용될 수 있도록 합니다.

```
sudo netfilter-persistent save
```
