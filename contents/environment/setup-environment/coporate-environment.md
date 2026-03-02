---
title: "기업망 방화벽 내부에서 필요한 추가 설정"
sort: 2
---

# 기업망 방화벽 내부에서 필요한 추가 설정

기업망 방화벽 내부에서 개발 환경을 사용하기 위해서는 해당 기업의 인터넷 접속을 허용하는 인증서가 있어야 합니다.

*company.cer*, *company.crt* 또는 *companypem* 과 같은 인증서가 있는 경우 3번으로 갑니다.

인증서가 없는 경우 아래의 방법으로 인증서를 얻습니다.

## 기업망에서 사용하는 인증서 다운로드

1. 크롬 브라우져를 실행을 하고, 설정 화면을 표시합니다.

2. **Privacy and security** 메뉴를 클릭하고, **Security** 항목을 클릭합니다.

    ![Cert1](/assets/images/env-cert-001.png)

3. **Manage ceritificates** 항목을 클릭하여, 인증서 관리 화면을 표시합니다.

    ![Corp ENV](/assets/images/env-cert-002.png)

4. **Trusted Root Certification Authorities** 탭에서 해당 기업의 인증서를 선택하고 **Export**를 클릭합니다. 인증서가 여러 개 등록되어 있는 경우 첫번째부터 선택을 해봅니다.

    ![Corp ENV](/assets/images/env-cert-003.png)

5. **Next**를 클릭합니다.

    ![Corp ENV](/assets/images/env-cert-004.png)

6. **Base-64 encoded x.509 (.CER)**를 선택하고, **Next**를 클릭합니다. 

    ![Corp ENV](/assets/images/env-cert-006.png)

7. 파일 이름을 지정하고, **Next**버튼을 클릭하여 저장합니다. 여기서는 `C:\Utils\company.cer` 파일로 저장을 합니다.

    ![Corp ENV](/assets/images/env-cert-005.png)

## 다운로드한 인증서를 변환하기

X.509 CER 파일을 PEM 파일로 변환합니다. OpenSSL을 사용합니다(Windows용은 [[여기]](https://slproweb.com/products/Win32OpenSSL.html)에서 다운로드하여 `C:\Utils`에 설치합니다.)

CMD 창을 열고 아래와 같이 실행하여 인증서를 변환합니다.

```
openssl x509 -in C:\Utils\company.cer -outform PEM -out C:\Utils\company.pem
```

## Windows 환경변수 설정하기

아래와 같이 Windows 의 환경 변수를 설정을 합니다.

```
HTTP_PROXY = http://xxx.xxx.xxx.xxx:yyyy (예: http://192.168.10.1:8080)
HTTPP_PROXY = http://xxx.xxx.xxx.xxx:yyyy (예: http://192.168.10.1:8080)
NODE_EXTRA_CA_CERTS = C:\Utils\company.pem
```

## JDK에 인증서 적용하기

CMD 창을 열고 알래의 명령을 차례로 실행합니다.

```
C:
CD %JAVA_HOME%\lib\security
keytool -import -trustcacerts -storepass changeit -alias COMPANY -file company.cer -keystore cacerts
```

COMPANY에 해당 회사 약자를 영문으로 공백 없이 대체해줍니다.

## Visual Studio Code에 Proxy, 인증서 적용하기

1. 터미널을 열고 아래의 명령을 실행을 합니다.

    ```
    npm config --location=global set https-proxy http://xxx.xxx.xxx.xxx:yyyy
    ```

    xxx.xxx.xxx.xxx 와 yyyy는 기업망의 프록시 서버 IP 주소와 포트입니다.

2. Visual Studio Code의 **Settings** 화면에서 **Application** > **Proxy**를 클릭합니다.

    ![Corp ENV](/assets/images/env-cert-007.png)

    **Proxy** 설정란에 기업망의 프록시 서버 설정값(Ex: http://192.168.10.1:8080)을 입력합니다.

    **Proxy Strict SSL**의 체크박스를 해제합니다.

    ![Corp ENV](/assets/images/env-cert-008.png)

    **NODE_EXTRA_CA_CERTS**에 `C:\Utils\company.pem`을 입력을 합니다.


