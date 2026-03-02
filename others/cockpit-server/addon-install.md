---
title: "Cockpit Addon 설치"
sort: 2
---

# Cockpit Addon 설치

몇가지 Cockpit 애드온 서비스를 설치를 합니다.

## Podman 관리자 설치

Cockpit에서는 Docker 대신 Podman을 지원합니다.

아래의 명령으로 Podman 관리자를 설치를 합니다.

``` bash
sudo apt-get install -t ${VERSION_CODENAME}-backports podman cockpit-podman -y
```

Podman 서비스를 시작하고, 시스템 서비스로 등록을 합니다.

``` bash
sudo systemctl start podman
sudo systemctl enable podman
```

iptables 서비스를 사용하는 경우, FORWARD 체인 정책은 일반적으로 DROP으로 설정이 되어있습니다.

Docker의 경우 이부분을 자동으로 처리해주지만 Podman은 그렇지 않습니다. 따라서 수동으로 규칙을 추가해주어야 합니다.

아래의 명령을 실행합니다.

``` bash
sudo iptables -I CNI-FORWARD 1 -o cni-podman0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
sudo iptables -I CNI-FORWARD 2 -o cni-podman0 -j ACCEPT
sudo iptables -I CNI-FORWARD 3 -i cni-podman0 ! -o cni-podman0 -j ACCEPT
sudo iptables -I CNI-FORWARD 4 -i cni-podman0 -o cni-podman0 -j ACCEPT
```

아래의 명령을 실행하여 FORWARD 규칙이 정상적으로 등록되었는지 확인합니다.

``` bash
sudo iptables -L
```

변경된 설정을 저장을 합니다.

``` bash
sudo netfilter-persistent save
```

## 써드 파티 애드온 설치

1. 써드파티 저장소를 등록

    ``` bash
    sudo curl -sSL https://repo.45drives.com/setup | sudo bash
    sudo apt update
    ```

2. 사용자 관리용 Identities 애드온 설치

    ``` bash
    sudo apt install cockpit-identities
    ```

3. Samba / NFS 파일 공유관리 애드온 설치

    ``` bash
    sudo apt install cockpit-file-sharing
    ```

4. 시스템 파일 관리용 애드온 설치

    ``` bash
    sudo apt install cockpit-navigator
    ```

5. ZFS 파일 시스템 애드온 설치

    ZFS 관리용 애드온은 설치관리자를 아직 지원하지 않습니다.
    
    Git에서 클론하여 설치를 합니다.
    
    ``` bash
    git clone https://github.com/45drives/cockpit-zfs-manager.git
    sudo cp -r cockpit-zfs-manager/zfs /usr/share/cockpit
    ```

