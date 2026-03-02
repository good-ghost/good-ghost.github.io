---
title: "SOQL 쿼리하기"
sort: 1
---

# SOQL 쿼리하기

Salesforce REST API에서 SOQL 쿼리는 `GET` 방식을 사용하여 데이터를 조회할 수 있습니다.

쿼리문의 형식은 다음과 같습니다.

`<instanceUrl>/services/data/vXX.x/query?q=<URL Encoded SOQL Query String>`

`vXX.x`는 API의 버전을 의미하며, 이 설명에서는 v58.0을 적용하였습니다.

1. **Database** 모듈의 data method 영역에 메소드를 추가합니다.

    **HttpGet** 메소드를 사용합니다.

    ``` python
    def query(connInfo, soql):
        try:
            headers = {
                'Content-Type' : 'application/x-www-form-urlencoded',
                'Authorization' : 'Bearer %s' % connInfo.get('accessToken') 
            }
            
            encodedSoql = urllib.parse.quote_plus(soql)
            
            url = '%s/services/data/v58.0/query?q=%s' % (connInfo.get('instanceUrl'), encodedSoql)
            
            req = requests.Request('GET', url, headers=headers)
            
            prepped = req.prepare()
            
            result = call(prepped)
            
            return result
        except Exception as e:
            print('Exception -> ', e)
    ```

2. **sample.py** 모듈에 아래의 메소드를 추가합니다.

    ``` python
    def querySample(connInfo):
        try:
            query = "SELECT Id, Name FROM Account LIMIT 10"
            result = Database.query(connInfo, query)

            print(json.dumps(result, indent=2))
        except Exception as e:
            print('Exception -> ', e)
    ```

3. **sample.py** 모듈의 `main` 메소드에 아래의 내용을 추가합니다.

    ``` python
        # Data function block begin
        querySample(connInfo)
        
        # Data function block end
    ```

4. 터미널에서 `python sample.py`이라고 명령을 실행해 봅니다.

   아래와 같은 형식의 데이터가 표시되면 정상적으로 동작을 하는 것입니다.

   ````result
   ``` json
   {
     "totalSize": 10,
     "done": true,
     "records": [
       {
         "attributes": {
           "type": "Account",
           "url": "/services/data/v58.0/sobjects/Account/001***************"
         },
         "Id": "001***************",
         "Name": "*****"
       },
       ...
       {
         "attributes": {
           "type": "Account",
           "url": "/services/data/v58.0/sobjects/Account/001***************"
         },
         "Id": "001***************",
         "Name": "*****"
       }
     ]
   }
   ```
   ````