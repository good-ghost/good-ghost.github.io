---
title: "Oracle Cloud에서 UFW, firewalld 방화벽을 사용하려면"
sort: 6
---

# Oracle Cloud에서 UFW, firewalld 방화벽을 사용하려면

https://opentutorials.org/module/6374/32398

Oracle Cloud에서 제공하는 Ubuntu 이미지(운영체제 설치파일)에는 기본적으로 `iptables`를 방화벽으로 사용합니다.

따라서 기존 방화벽을 다른것으로 대체하지 말것을 권장합니다.

iptables가 사용하기가 까다롭기 떄문에 다른 방화벽을 사용하기를 원할 수 있습니다.

이 상태에서 UFW, firewalld와 같은 다른 방화벽을 설정해도 정상적으로 동작하지 않습니다.

## iptables의 설정값 확인하기

아래의 명령을 실행해 봅니다.

```bash
$ sudo iptables -L
```

현재 설정된 iptables의 설정값이 표시가 됩니다.

````result
```
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --  anywhere             anywhere             state RELATED,ESTABLISHED
ACCEPT     icmp --  anywhere             anywhere
ACCEPT     all  --  anywhere             anywhere
ACCEPT     udp  --  anywhere             anywhere             udp spt:ntp
ACCEPT     tcp  --  anywhere             anywhere             state NEW tcp dpt:ssh
REJECT     all  --  anywhere             anywhere             reject-with icmp-host-prohibited

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination
...
...
...
Chain InstanceServices (1 references)
target     prot opt source               destination
ACCEPT     tcp  --  anywhere             169.254.0.2          owner UID match root tcp dpt:iscsi-target /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */
ACCEPT     tcp  --  anywhere             169.254.2.0/24       owner UID match root tcp dpt:iscsi-target /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */
ACCEPT     tcp  --  anywhere             169.254.0.2          tcp dpt:http /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */
ACCEPT     udp  --  anywhere             169.254.169.254      udp dpt:domain /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */
ACCEPT     tcp  --  anywhere             169.254.169.254      tcp dpt:domain /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */
ACCEPT     tcp  --  anywhere             169.254.0.3          owner UID match root tcp dpt:http /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */
ACCEPT     tcp  --  anywhere             169.254.0.4          tcp dpt:http /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */
ACCEPT     tcp  --  anywhere             169.254.169.254      tcp dpt:http /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */
ACCEPT     udp  --  anywhere             169.254.169.254      udp dpt:bootps /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */
ACCEPT     udp  --  anywhere             169.254.169.254      udp dpt:tftp /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */
ACCEPT     udp  --  anywhere             169.254.169.254      udp dpt:ntp /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */
REJECT     tcp  --  anywhere             link-local/16        tcp /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */ reject-with tcp-reset
REJECT     udp  --  anywhere             link-local/16        udp /* See the Oracle-Provided Images section in the Oracle Cloud Infrastructure documentation for security impact of modifying or removing this rule */ reject-with icmp-port-unreachable
```
아랫 부분의 `Chain InstanceServices` 섹션 부분이 Oracle Cloud에서 추가로 설정을 하고 있는 부분입니다.
````

## UFW 방화벽으로 변경하기

Oracle Cloud의 VM 사용을 하기 위해서는 iptables 방화벽을 아예 사용하지 않을 수는 없습니다.

Oracle Cloud가 설정하는 값을 지원하면서 기타 방화벽 설정을 UFW을 사용하도록 변경을 하도록 합니다.

1. iptables 방화벽의 기본 연결 정책(Policy)을 모두 허용으로 변경합니다.

    ``` bash
    $ sudo iptables -P INPUT ACCEPT
    $ sudo iptables -P OUTPUT ACCEPT
    $ sudo iptables -P FORWARD ACCEPT
    ```

2. iptables 방화벽의 모든 연결 규칙(Rule)을 제거합니다.

    ``` bash
    $ sudo iptables -F
    ```

3. iptables 방화벽의 모든 사용자 지정 체인 설정을 제거합니다.

    ``` bash
    $ sudo iptables -X
    ```

4. iptables 방화벽에 Oracle Cloud를 위한 설정값을 지정합니다.

    iptables는 명령의 순서에 민감합니다. 순서가 잘못되었을 경우 원하는 결과를 얻지 못할 가능성이 높습니다.

    ``` bash
    $ sudo iptables -N InstanceServices
    $ sudo iptables -A InstanceServices -p tcp -d 169.254.0.2 --dport iscsi-target -m owner --uid-owner root -j ACCEPT
    $ sudo iptables -A InstanceServices -p tcp -d 169.254.2.0/24 --dport iscsi-target -m owner --uid-owner root -j ACCEPT
    $ sudo iptables -A InstanceServices -p tcp -d 169.254.4.0/24 --dport iscsi-target -m owner --uid-owner root -j ACCEPT
    $ sudo iptables -A InstanceServices -p tcp -d 169.254.5.0/24 --dport iscsi-target -m owner --uid-owner root -j ACCEPT
    $ sudo iptables -A InstanceServices -p tcp -d 169.254.0.2 --dport http -j ACCEPT
    $ sudo iptables -A InstanceServices -p udp -d 169.254.169.254 --dport domain -j ACCEPT
    $ sudo iptables -A InstanceServices -p tcp -d 169.254.169.254 --dport domain -j ACCEPT
    $ sudo iptables -A InstanceServices -p tcp -d 169.254.0.3 --dport http -m owner --uid-owner root -j ACCEPT
    $ sudo iptables -A InstanceServices -p tcp -d 169.254.0.4 --dport http -j ACCEPT
    $ sudo iptables -A InstanceServices -p tcp -d 169.254.169.254 --dport http -j ACCEPT
    $ sudo iptables -A InstanceServices -p udp -d 169.254.169.254 --dport bootps -j ACCEPT
    $ sudo iptables -A InstanceServices -p udp -d 169.254.169.254 --dport tftp -j ACCEPT
    $ sudo iptables -A InstanceServices -p udp -d 169.254.169.254 --dport ntp -j ACCEPT
    $ sudo iptables -A InstanceServices -p tcp --destination link-local/16 -j REJECT --reject-with tcp-reset
    $ sudo iptables -A InstanceServices -p udp --destination link-local/16 -j REJECT --reject-with icmp-port-unreachable
    $ sudo iptables -A OUTPUT -d link-local/16 -j InstanceServices
    ```

5. 추가로 사용할 방화벽을 설정합니다.

    이글에서는 UFW 방화벽을 사용하도록 합니다.

    UFW 방화벽을 초기화 합니다.

    ``` bash
    $ sudo ufw reset
    ```

    ````result
    ```
    Resetting all rules to installed defaults. This may disrupt existing ssh
    connections. Proceed with operation (y|n)?
    ```
    `ssh` 연결이 끊어질 수 있음을 경고합니다. 계속 진행하려면 `y`를 답합니다.
    ````

6. UFW의 기본 정책을 추가합니다.

    기본적으로 모든 들어오는 연결을 차단합니다.

    ``` bash
    $ sudo ufw default deny incoming
    ```
 
    ````result
    ```
    Default incoming policy changed to 'deny'
    (be sure to update your rules accordingly)
    ```
    ````

    기본적으로 모든 나가는 연결을 허용합니다.

    ``` bash
    $ sudo ufw default allow outgoing
    ```
 
    ````result
    ```
    Default outgoing policy changed to 'allow'
    (be sure to update your rules accordingly)
    ```
    ````

 7. OCI 규칙을 추가합니다.

    타임서버가 사용하는 포트 허용

    ``` bash
    $ sudo ufw allow from any to any port ntp proto udp comment "OCI Rule"
    ```
 
    ````result
    ```
    Rules updated
    Rules updated (v6)
    ```
    ````

    SSH 원격 접속 허용

    ``` bash
    $ sudo ufw allow from any to any port ssh proto tcp comment "Allow SSH(22) port"
    ```

    ````result
    ```
    Rules updated
    Rules updated (v6)
    ```
    ````

8. UFW 방화벽 활성화

    ``` bash
    $ sudo ufw enable
    ```

    ````result
    ```
    Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
    ```
    
    계속 진행하기 위하여 `y`를 답합니다.

    ```
    Firewall is active and enabled on system startup
    Default incoming policy changed to 'deny'
    (be sure to update your rules accordingly)
    Default outgoing policy changed to 'allow'
    (be sure to update your rules accordingly)
    ```
    ````

9. iptables의 정책 변경

    UFW의 방화벽 설정이 끝났으므로 iptables 시스템 방화벽의 정책을 변경합니다.

    ``` bash
    $ sudo iptables -P INPUT DROP
    $ sudo iptables -P OUTPUT ACCEPT
    $ sudo iptables -P FORWARD DROP
    ```

10. 시스템 방화벽의 정책 및 규칙을 확인합니다.

    ```
    $ sudo iptables -L
    ```

    ````result
    ```
    Chain INPUT (policy DROP)
    target     prot opt source               destination
    ufw-before-logging-input  all  --  anywhere             anywhere
    ufw-before-input  all  --  anywhere             anywhere
    ...
    ...
    # UFW 규칙이 있어야 합니다
 
    Chain FORWARD (policy ACCEPT)
    target     prot opt source               destination
    ufw-before-logging-forward  all  --  anywhere             anywhere
    ufw-before-forward  all  --  anywhere             anywhere
    ...
    ...
    # UFW 규칙이 있어야 합니다
 
    Chain OUTPUT (policy ACCEPT)
    target     prot opt source               destination
    InstanceServices  all  --  anywhere             link-local/16
    ufw-before-logging-output  all  --  anywhere             anywhere
    ufw-before-output  all  --  anywhere             anywhere
    ...
    ...
    # InstanceServices가 먼저 나오고, 그 다음에 UFW 규칙이 있어야 합니다
 
    Chain InstanceServices (1 references)
    target     prot opt source               destination
    ACCEPT     tcp  --  anywhere             169.254.0.2          tcp dpt:iscsi-target owner UID match root
    ACCEPT     tcp  --  anywhere             169.254.2.0/24       tcp dpt:iscsi-target owner UID match root
    ...
    ...
    ```
    ````

11. 변경된 iptables 설정값 저장
    
    변경된 정책 및 규칙을 시스템을 다시 시작해도 적용되도록 저장을 합니다.

    ``` bash
    $ sudo netfilter-persistent save
    ```