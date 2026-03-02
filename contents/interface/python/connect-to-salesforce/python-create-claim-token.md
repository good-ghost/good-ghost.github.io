---
title: "인증 토큰 만들기"
sort: 1
---

# 인증 토큰 만들기

인증 절차에 사용할 인증서 및 개인키는 [Salesforce Connected App 설정](../../connectedapp/setup-connected-app.html) 설명에서 사용된 인증서(company.crt)에 대응하는 만든 키스토어 또는 개인키를 사용합니다. **company.jks**, **company.key** 파일을 프로젝트의 루트 폴더에 복사를 해둡니다. 실제로는 jks, key 둘 중에 한가지만 필요합니다. 뒤에서 설명하는 인증서/개인키 사용 방법에 따라 선택을 하시면 됩니다. **Client Key**와 Connected App에 접속할 수 있는 **사용자 Id**도 준비를 해둡니다.

## 필요한 패키지 설치

Python에서 JWT 인증 토큰을 발급받으려면 몇가지 패키지를 설치하여야 합니다.

Visual Studio Code의 터미널 창에서 아래의 명령을 실행합니다. 이때 Python 프로젝트는 virtualenv 가상 환경이 활성화 되어있어야 합니다.

``` python
pip install requests cryptography pyjwt pyjks
```

```note
Windows 환경에서 `pyjwt` 패키지를 설치할 때 [**Microsoft C++ Build Tools**](https://visualstudio.microsoft.com/visual-cpp-build-tools/) 개발 환경을 필요로 합니다. 다운로드한 설치 파일을 실행하고 기본 설치 옵션을 모두 설치합니다.

![ms build tool](/assets/images/ms-build-tool.png)
```

설치되는 패키지는 다음과 같습니다.

- **requests** : Web 서비스 호출을 위한 패키지

- **pyjwt** : JWT 인코딩/디코딩을 처리해주는 패키지

- **pyjks** : JKS 키스토어 파일을 조작할 수 있는 패키지

- **cryptography** : **pyjwt** 패키지가 의존하는 패키지


## 모듈 만들기

인증 받기 코드에 사용할 Python 모듈을 생성합니다.

이 설명에서는 virtualenv 가상 환경 프로젝트를 기준으로 설명을 합니다.

1. Python 프로젝트 명의 오른쪽 *New File* 아이콘을 클릭합니다.

   ![Create Class 1](/assets/images/python-code-001.png)

2. 만들어지는 모듈에 대한 파일 이름으로 **Auth.py**를 입력합니다.

   ![Create Class 2](/assets/images/javacode-002.png)

3. 같은 방법으로 **sample.py** 파일을 생성합니다.

## 코드 구성

인증을 받기 위하여 Salesforce Connnected App에 전송하는 토큰은 다음과 같이 `.`으로 연결된 3부분으로 이루어져 있습니다.

   [*JWT 헤더*].[*인증 요청정보*].[*개인키로 암호화된 헤더+요청정보*]  (`JWT Header.Claim Payload.Header + Signed Payload`)

순서대로 만들어 보도록 하겠습니다.

1. `Auth.py` 모듈 윗 부분에 아래의 내용을 추가를 합니다.

    ``` python
    import requests
    import jwt
    import time
    import textwrap
    import base64

    ```

2. 가장 먼저 개인키(PrivateKey)를 프로그램에 적재하여야 합니다. 개인키를 적재하는 방법은 아래의 세가지 방법이 있으니 상황에 맞는 방법을 사용하시면 됩니다.

    - JKS 키스토어의 개인키를 적재하는 방법

        ``` python
        def getPrivateKeyFromJKS(filePath, alias, password):
            import jks
            
            keyStore = jks.KeyStore.load(filePath, password)
            pkEntry = keyStore.private_keys[alias]
            decodedKey = base64.b64encode(pkEntry.pkey_pkcs8).decode('ascii')
            pkcs8PEM = "\r\n".join([
                "-----BEGIN PRIVATE KEY-----",
                "\r\n".join(textwrap.wrap(decodedKey, 64)),
                "-----END PRIVATE KEY-----"
            ])

            return pkcs8PEM
        ```

        `sample.py` 파일에 아래의 내용을 추가합니다.


        ``` python
        import Auth
        import json
        import textwrap

        def main():
            privateKey = Auth.getPrivateKeyFromJKS("company.jks", "company", "password")

            print(privateKey)

        main()
        ```

        터미널에서 아래의 명령을 실행해봅니다.

        ``` powershell
        python sample.py
        ```

        `company.key` 파일의 내용과 같은 내용이 표시가 되면 정상 동작하는것입니다.

    - 개인키 파일의 개인키를 적재하는 방법

        `Auth.py` 파일에 아래의 메소드를 추가합니다.

        ``` python
        def getPrivateKeyFromFile(filePath):
            fd = open(filePath, "r")
            privateKey = fd.read()
            
            return privateKey
        ```

        `sample.py` 파일에 아래의 내용을 추가합니다.

        ``` python
        import Auth
        import json
        import textwrap

        def main():
            privateKey = Auth.getPrivateKeyFromFile("company.key")

            print(privateKey)

        main()
        ```

        터미널에서 아래의 명령을 실행해봅니다.

        ``` powershell
        python sample.py
        ```

        **company.key** : 개인키 파일입니다.

    - 개인키 파일의 개인키를 스트링으로 적재하는 방법

        `sample.py` 파일의 `main` 메소드를 다음과 같이 `company.key`의 내용을 직접 변수에 지정하여 사용할 수도 있습니다.

        ``` python
        import Auth
        import json
        import textwrap

        def main():
            privateKey = textwrap.dedent("""
            -----BEGIN PRIVATE KEY-----
            MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDN4c80VU9yjbAu
            JWq3RYPBsLnP2wcipVdGIzPYLqyXUq2GBlhyaLjN0NI5mO+AyXE8FSJtD/Y22ILQ
            ...
            ...
            ...
            Qmh4OV5ZHXwx5R6ffAOAwAY=
            -----END PRIVATE KEY-----
            """).encode()

        main()
        ```

    위의 세가지 방법중의 하나를 사용하도록 합니다.

3. 인증 요청 Token을 만들기 위하여 `Auth` 모듈에 아래의 메소드를 추가합니다.

    ``` python
    def getToken(consumerKey, loginUrl, loginUser, privateKey):
        print('Generating signed JWT assertion...')
        claim = {
            'iss': consumerKey,
            'sub': loginUser,
            'aud': loginUrl,
            'exp': int(time.time()) + 300,
        }

        encoded = jwt.encode(claim, privateKey, algorithm='RS256', headers={'alg':'RS256'})

        return encoded
    ```

    - **clientId** : ConnectedApp에서 제공하는 **Consumer Key** 입니다.

    - **loginUrl** : *login.salesforce.com* 또는 *test.salesforce.com*을 사용합니다.

    - **loginId** : ConnectedApp에서 **Profile** 또는 **Permission Set**으로 잡속이 허용된 이메일 형식의 사용자 계정입니다.

    이 코드로 생성되는 Token 값은 Salesforce의 JWT Bearer 플로우를 위한 값입니다. 다른 시스템들은 Heade와 Claim이 다른 형식을 사용할 수 있습니다.

    `sample.py` 파일의 `main` 메소드를 아래와 같이 수정하고 터미널에서 `python sample.py`를 실행해 봅니다.

    ``` java
    def main():
        privateKey = Auth.getPrivateKeyFromFile("company.key")

        //////////////////
        // Make a Token //
        //////////////////
        String consumerKey = "3MVG9YDQS5WtC11q60F............QYX68awYhUzKVMMB";
        String loginUrl    = "https://login.salesforce.com/";
        String loginUser   = "username@example.com";
        token = Auth.getToken(consumerKey, loginUrl, loginUser, privateKey);

        print(token)
    ```

    `eyJhbGciOiJSUzI1NiJ9.ey....` 와 같은 인코딩된 문자열이 출력되면 정상입니다.

