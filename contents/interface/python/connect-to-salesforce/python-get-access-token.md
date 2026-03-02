---
title: "access_token 발급받기"
sort: 2
---


# access_token 발급받기

생성된 Token으로 Salesforce에 인증 요청을 하기 위하여 아래의 코드를 `Auth` 모듈에 추가합니다.

``` python
def getAuth(consumerKey, loginUrl, loginUser, privateKey):
    try:
        token = getToken(consumerKey, loginUrl, loginUser, privateKey)
        url = loginUrl + "/services/oauth2/token"
        kwarg = {
            'data' : {
                'grant_type': 'urn:ietf:params:oauth:grant-type:jwt-bearer',
                'assertion': token,
            }
        }
        
        print('Making OAuth request...')
        # result = requests.post(url, data)
        result = requests.request('POST', url, **kwarg)
        
        if(result.status_code == 200):
            obj = result.json()
            connInfo = {
                'instanceUrl' : obj['instance_url'],
                'accessToken' : obj['access_token']
            }
            return connInfo
        return None
    except Exception as e:
        print('Exception -> ', e)
```

- **grant_type** : 인증으로 요청하는 형식입니다.

`sample.py` 파일의 `main` 메소드를 아래와 같이 수정하고 터미널에서 `python sample.py`를 실행해 봅니다.

``` python
def main():
    
    consumerKey = "3MVG9YDQS5WtC11q60F............QYX68awYhUzKVMMB"
    loginUrl    = "https://login.salesforce.com/"
    loginUser   = "username@example.com"

    privateKey = Auth.getPrivateKeyFromFile("company.key")

    connInfo = Auth.getAuth(consumerKey, loginUrl, loginUser, privateKey);
    print(json.dumps(connInfo, indent=2))
```

아래와 같은 문자열이 표시되면 정상적으로 access_token을 발급받은 것입니다.

``` json
{
  "instanceUrl": "https://****.my.salesforce.com",
  "accessToken": "00D***********!********************************"
}
```

인증 절차가 정상적으로 동작하므로, Salesforce의 데이터 조작을 위하여 `sample.py`의 내용을 아래와 같이 정리를 합니다.

``` python
import Auth
import Database
import json
import textwrap
import urllib.parse

def main():
    try:
        connInfo = getAuth()
        print(json.dumps(connInfo, indent=2))

        # Data function block begin
        
        # Data function block end
        
    except Exception as e:
        print('Exception -> ', e)

def getAuth():
    
    consumerKey = "3MVG9YDQS5WtC11q60F............QYX68awYhUzKVMMB"
    loginUrl    = "https://login.salesforce.com/"
    loginUser   = "username@example.com"

    privateKey = Auth.getPrivateKeyFromFile("company.key")
    # privateKey = Auth.getPrivateKeyFromJKS("company.jks", "company", "password")
    # privateKey = textwrap.dedent("""
    # -----BEGIN PRIVATE KEY-----
    # MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDN4c80VU9yjbAu
    # JWq3RYPBsLnP2wcipVdGIzPYLqyXUq2GBlhyaLjN0NI5mO+AyXE8FSJtD/Y22ILQ
    # ...
    # ...
    # h3opCCYABa+qE5dHiCDJEKuZfpDNUnIeKGUlUeOvE05JLs2nBXS+w6s3ACpxeoNE
    # Qmh4OV5ZHXwx5R6ffAOAwAY=
    # -----END PRIVATE KEY-----
    # """).encode()

    connInfo = Auth.getAuth(consumerKey, loginUrl, loginUser, privateKey);
    
    return connInfo



main()

```

터미널에서 `python sample.py`를 실행하여 Access Token 과 Instance Url의 값이 표시되는지 확인해 봅니다.
