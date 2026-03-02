---
title: "인증서/개인키 생성 및 설정"
sort: 1
---

# 인증서/개인키 생성 및 설정

Salesforce에서는 JKS (Java Key Store) 파일만을 등록할 수 있습니다. 아래의 순서에 따라 키스토어 파일을 만들고 Salesforce의 Certificate and Key Management에 등록을 합니다. 이 인증서 및 개인키 파일은 Salesforce에서 Connected App, Single Sign-on, Identity Provider등에서 사용할 수 있습니다.

## 준비 사항

인증서 및 개인키, 키스토어 파일을 생성하기 위해서는 아래의 도구가 설치되어 있어야 합니다.

- **keytool** (JDK에 포함되어 있습니다.), Azul의 [Zulu JDK 17](https://www.azul.com/downloads/?version=java-17-lts&os=windows&architecture=x86-64-bit&package=jdk#zulu)을 사용하시기 바랍니다. 최신 버전은 21 LTS이지만 VS Code의 Salesforce Extension에서는 버전 17까지만을 지원합니다.

- **openssl** ([Windows용 다운로드 사이트](https://slproweb.com/products/Win32OpenSSL.html)), 버전 3.0.x를 다운로드 하여 설치하시기 바랍니다.

## 인증서 및 개인키 파일 생성

1. Self-sign 인증서 및 개인키 생성
   
   10년(3650일) 유효기간의 Self-sign 인증서를 생성합니다.
   
   ```bash
   $> openssl req -x509 -sha256 -nodes -days 3650 -newkey rsa:2048 -keyout company.key -out company.crt
   ```

   *company*는 원하는 이름을 사용하시기 바랍니다.

   <pre>
   -----
   You are about to be asked to enter information that will be incorporated
   into your certificate request.
   What you are about to enter is what is called a Distinguished Name or a DN.
   There are quite a few fields but you can leave some blank
   For some fields there will be a default value,
   If you enter '.', the field will be left blank.
   -----
   Country Name (2 letter code) [AU]:<b><i>KR</i></b>
   State or Province Name (full name) [Some-State]:<b><i>Seoul</i></b>
   Locality Name (eg, city) []:<b><i>GangNam-gu</i></b>
   Organization Name (eg, company) [Internet Widgits Pty Ltd]:<b><i>Company Inc</i></b>
   Organizational Unit Name (eg, section) []:<b><i>Sales</i></b>
   Common Name (e.g. server FQDN or YOUR name) []:<b><i>http://www.comapny.co.kr</i></b>
   Email Address []:<b><i>admin@company.co.kr</i></b>
   </pre>

   명령을 실행하고 위의 형식대로 값을 입력을 하면 면 3650일 사용가능한 PKCS#8 PEM 형식의 **company.key**, **company.crt** 파일이 생성됩니다.
   - **company.key** : 개인키 파일(Private Key File)
   - **company.crt** : 인증서 파일(Public Certificate File)
  
2. PFX 키스토어 파일 만들기
   ```bash
   $> openssl pkcs12 -export -in company.crt -inkey company.key -out company.pfx
   ```

   *company*는 원하는 이름을 사용하시기 바랍니다.
   
   ```bash
   Enter Export Password:
   Verifying - Enter Export Password:
   ```

   명령을 실행하고 사용할 암호를 설정을 하면 PKCS#12 형식의 **company.pfx** 파일이 생성됩니다.

   - **company.pfx** : PKCS#12 형식의 키스토어 파일
  
3. PFX 키스토어 파일을 JKS 키스토어 파일로 변환하기
   ```bash
   $> keytool -importkeystore -srckeystore company.pfx -srcstoretype pkcs12 -srcalias 1 -destkeystore company.jks -deststoretype jks -destalias company
   ```

   *company*는 원하는 이름을 사용하시기 바랍니다.

   ```bash
   키 저장소 company.pfx을(를) company.jks(으)로 임포트하는 중...
   대상 키 저장소 비밀번호 입력:
   새 비밀번호 다시 입력:
   소스 키 저장소 비밀번호 입력:
   ```

   명령을 실행하고 사용할 암호를 설정을 하면 JKS 형식의 **company.jks** 파일이 생성됩니다.
   - **company.jks** : JKS 형식의 키스토어 파일
   
````note
 Salesforce의 **Certificate and Key Management** 메뉴에서 Export된 JKS 키스토어 파일을 PKCS#12 형식의 PFX 키스토어 파일로 변환하기
 ```bash
 keytool -importkeystore -srckeystore exported.jks -srcstoretype JKS -srcstorepass password -destkeystore exported.pfx -deststoretype PKCS12 -deststorepass password
 ```
 PKCS#12 형식의 PFX 키스토어 파일에서 PKCS#8 PEM 형식의 개인키(Private Key) 추출하기
 ```bash
 openssl pkcs12 -in exported.pfx -nocerts -nodes -out exported.key
 ```
 PKCS#12 형식의 PFX 키스토어 파일에서 PKCS#8 PEM 형식의 인증서(Certificate) 추출하기
 ```bash
 openssl pkcs12 -in exported.pfx -clcerts -nokeys -out exported.crt
 ```
 PKCS#8 PEM 형식의 개인키 파일을 RSA 암호화가 적용된 개인키 파일로 변환하기
 ```bash
 openssl rsa -des3 -in exported.key -out exported_rsa.key
 ```
 RSA 암호화 되어있는 개인키 파일을 암호 해제하여 PKCS#8 PEM 형식의 개인키 파일로 변환하기
 ```bash
 openssl rsa -in exported_rsa.key -out exported.key
 ```
 또는
 ```bash
 openssl pkcs8 -topk8 -inform PEM -outform PEM -nocrypt -in exported_rsa.key -out exported.key
 ```
````
