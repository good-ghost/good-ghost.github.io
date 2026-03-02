---
title: "firewalld 방화벽 설치"
sort: 2
---

# firewalld 방화벽 설치

[참고](https://lindarex.github.io/ubuntu/ubuntu-firewalld-installation/)

Firealld는 IPv4, IPv6, 이더넷 브리지 및 IPSet의 방화벽 설정을 지원하는 Linux 방화벽 관리 도구입니다.

Ubuntu의 기본 방화벽 시스템은 UFW이지만, firewalld를 설치하여 사용할 수 있습니다.

이 글에서는 ubuntu 환경에서 패키지로 firewalld를 설치하고 설정하는 방법을 소개합니다.

## 테스트 환경

- Oracle Cloud VM

- Ubuntu 22.04 LTS

## 설치 및 설정

1. apt 명령어로 firewalld 설치

    ``` bash
    $ sudo apt update
    $ sudo apt install firewalld
    ```

2. firewalld 설치 확인

    ``` bash
    $ sudo firewall-cmd --version
    ```

3. firewalld 설정

    1. firewalld에 rule 추가

        아래 예제는 영구적으로 public zone에 TCP 8080 포트를 추가하는 명령어입니다.

        ``` bash
        $ sudo firewall-cmd --permanent --zone=public --add-port=8080/tcp
        ```

    2. firewalld에 rule 적용

        아래 명령어를 실행하기 전에는 추가한 rule이 적용되지 않습니다.

        ``` bash
        $ sudo firewall-cmd --reload
        ```

    3. firewalld에 설정된 모든 값 조회

        ``` bash
        $ sudo firewall-cmd --list-all
        ```

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



