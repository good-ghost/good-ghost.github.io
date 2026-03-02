---
title: "Salesforce에서 데이터를 쿼리, 생성, 업데이트, 삭제하기"
sort: 4
---

# Salesforce에서 데이터를 쿼리, 생성, 업데이트, 삭제하기

## Salesforce 호출용 공통 메소드 작성하기

데이터를 처리하는 코드를 작성하기 위하여, **Python 프로젝트**에 `Database.py`라는 파일을 추가합니다.

먼저 아래에 설명하는 데이터 조작에서 공통적으로 사용하게 될 **HTTP Request** 메소드를 만듭니다.

`Database` 모듈에 아래의 코드를 추가합니다.

``` python
import requests
import urllib.parse
import json

proxies = None
# proxies = {
#     'http': 'http://10.10.1.10:3128',
#     'https': 'http://10.10.1.10:1080',
# }

verify = None
# verify = False
# verify = 'company.pem' # path of cert file (not self signed, cert file that use company proxy)

timeout = 120 # number of seconds

################################
# data method begin
################################



################################
# data method end
################################

def call(prepped):
    session = requests.Session()
        
    if proxies is not None:
        session.proxies.update(proxies)

    if verify is not None:
        session.verify = verify
    
    res = session.send(prepped, timeout=timeout)

    session.close()
     
    if res.status_code == 200 or res.status_code == 201:
        return res.json()
    elif res.status_code == 204:
        return { 'success' : True }
    else:
        print(res.status_code)
        return None
```

`call()` 메소드는 데이터 생성, 조회, 업데이트, 삭제시에 사용하는 공통 메소드입니다. 각각 Http 호출기능의 `POST`, `GET`, `PATCH`, `DELETE`를 사용하므로 `call()` 메소드는 *Request* 오브젝트를 매개변수로 사용을 하고 있습니다.

데이터를 처리하는 모든 메소드는 `Database.py`의 아래 블록 사이에 입력하도록 합니다. 

``` python
################################
# data method begin/end
################################
```

## 데이터 처리하기

- 쿼리하기

    - [SOQL 쿼리하기](./query/python-data-process-query-records.html)

    - [Batch 쿼리하기](./query/python-data-process-batch-query.html)

- [단일 레코드 조회](./query-record/)

- [단일 레코드 생성](./create-record/)

- [단일 레코드 업데이트](./update-record/)

- [단일 레코드 삭제](./delete-record/)

- [복수 레코드 생성](./create-records/)

- [복수 레코드 업데이트](./update-records/)

- [복수 레코드 삭제](./delete-records/)
