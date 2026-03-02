---
title: "Oracle Cloud VM의 포트 열기"
sort: 7
---

# Oracle Cloud VM의 포트 열기

1. Oracle Cloud의 웹 콘솔에 접속합니다.

    **Dashboard**를 클릭합니다.

    ![VM Port Open](/assets/images/oracle-cloud-001.png){: width="600" }

2. **Virtual cloud networks**를 클릭합니다.

    ![VM Port Open](/assets/images/oracle-cloud-002.png){: width="600" }

3. **Virtual cloud networks**의 항목(여기서는 **vcn-20...**)을 클릭합니다.

    ![VM Port Open](/assets/images/oracle-cloud-003.png){: width="600" }

4. **Subnet** 섹션의 항목(여기서는 **공용 서브넷**)을 클릭합니다.

    ![VM Port Open](/assets/images/oracle-cloud-004.png){: width="600" }

5. **VSecurity Lists**를 항목(여기서는 **Default Security...**)을 클릭합니다.

    ![VM Port Open](/assets/images/oracle-cloud-005.png){: width="600" }

6. **Add Ingress Rules** 버튼을 클릭합니다.

    ![VM Port Open](/assets/images/oracle-cloud-006.png){: width="600" }

7. **Add Ingress Rules** 화면에서
   
    - `Source CIDR`에 `0.0.0.0/0`을 입력합니다.
    
    - `Destination Port Range`에 `80`을 입력합니다.

    - 하단의 **Add Ingress Rules** 버튼을 클릭합니다.

    ![VM Port Open](/assets/images/oracle-cloud-007.png){: width="600" }

8.  추가된 포트를 확인합니다.

    ![VM Port Open](/assets/images/oracle-cloud-008.png){: width="600" }

