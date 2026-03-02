---
title: "Batch 쿼리하기"
sort: 2
---

# Batch 쿼리하기

Batch 쿼리는 Salesforce에서 대량의 레코드를 쿼리할 때에 사용됩니다.

일반적인 `query`가 최대 2,000개의 레코드를 쿼리할 수 있지만 인터페이스 프로그램의 경우 더 많은 데이터를 동시에 처리해야할 수 있습니다. Batch 쿼리는 그런 경우에 사용됩니다.

Batch 쿼리는 `GET`으로 쿼리를 할 때 `batch size`를 지정하여 각각의 쿼리에서 만들어질 레코드 리스트의 크기를 지정할 수 있습니다.

Batch 쿼리는 첫번째 쿼리는 일반적인 `query`와 거의 동일합니다. Url 파라메터에 `query`가 아니라 `queryAll`을 사용하며 batch size` 헤더를 포함하는것만이 다릅니다. `batch size`는 200 ~ 2,000 사이의 숫자를 지정을하여야 합니다.

1. **Database** 모듈에 아래의 메소드를 추가합니다.

    **HttpGet** 메소드를 사용합니다.

    ``` python
    def batchQuery(connInfo, queryUrl, batchSize):
        try:
            headers = {
                'Content-Type' : 'application/x-www-form-urlencoded',
                'Authorization' : 'Bearer %s' % connInfo.get('accessToken'),
                'Sforce-Query-Options' : 'batchSize=%d' % batchSize
            }
            
            url = '%s%s' % (connInfo.get('instanceUrl'), queryUrl)
            
            req = requests.Request('GET', url, headers=headers)
            
            prepped = req.prepare()
            
            result = call(prepped)
            
            return result
        except Exception as e:
            print('Exception -> ', e)
    ```
    
    batch 쿼리에서는 지정한 SOQL문에 해당하는 모든 레코드가 쿼리될때까지 반복 호출을 하게 되는데 이때 사용되는 Url이 변경되게     됩니다. 따라서 batch 쿼리에서는 서비스 Url 전체를 파라메터로 넘겨 주어야 합니다.
    
2. **sample.py** 모듈에 아래의 메소드를 추가합니다.

    ``` python
    def batchSample(connInfo):
        try:
            serviceEndpoint = "/services/data/v58.0/queryAll?q="

            query = "SELECT Id, Name FROM Account LIMIT 10"
            queryUrl = "%s%s" % (serviceEndpoint, urllib.parse.quote_plus(query))

            batchSize = 200
            
            result = Database.batchQuery(connInfo, queryUrl, batchSize)

            print(json.dumps(result, indent=2))
        except Exception as e:
            print('Exception -> ', e)
    ```

    `serviceEndpoint`는 일반적인 쿼리에서 사용하던 `query` 파라메터가 아니라 `queryAll` 파라메터를 사용합니다. 또, SOQL문에 `LIMIT`이 지정되면 안됩니다. 또한 batchSize를 지정을 해주어야 합니다. 기본값은 500이며 200 ~ 2,000 사이의 값을 지정하면 됩니다.

3. **sample.py** 모듈의 `main` 메소드에 아래의 내용을 추가합니다.

    ``` python
        # Data function block begin
        # querySample(connInfo)
        batchSample(connInfo)
        
        # Data function block end
    ```

4. 터미널에서 `python sample.py`이라고 명령을 실행해 봅니다.

   아래와 같은 형식의 데이터가 표시되면 정상적으로 동작을 하는 것입니다.

   <pre>
   {
     "totalSize": 3222,
     <font color="red"><i>"done": false,</i></font>
     <font color="red"><i>"nextRecordsUrl": "/services/data/v58.0/query/01gRO0000016PIAYA2-200",</i></font>
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
   </pre>

   batch 쿼리에서 중요한 것은 결과값에 포함되어있는 `done`과 `nextRecordsUrl`의 값입니다.
   
   `done`의 값이 **true**이면 `nextRecordsUrl`값은 존재하지 않게 됩니다. `done`의 값이 **false**일 경우 `nextRecordsUrl`값이 존재하며, QueryLocator로서 다음 셋의 레코드를 쿼리할 수 있는 `serviceEndpoint`가 됩니다.

   이제 SOQL에 해당하는 모든 레코드를 쿼리하기 위하여 호출 메소드를 수정해봅니다.

5. **sample.py** 모듈의 `batchSample` 메소드를 아래와 같이 수정을 합니다.

    ``` python
    def batchSample(connInfo):
        try:
            serviceEndpoint = "/services/data/v58.0/queryAll?q="

            query = "SELECT Id, Name FROM Account LIMIT 10"
            queryUrl = "%s%s" % (serviceEndpoint, urllib.parse.quote_plus(query))

            batchSize = 200
            
            result = Database.batchQuery(connInfo, queryUrl, batchSize)

            totalSize = result.get("totalSize")
            done = result.get("done")
            records = result.get("records")

            print("total size : %d" % totalSize)
            print(json.dumps(records, indent=2))
            
            while not done:
                queryUrl = result.get('nextRecordsUrl')
                
                result =  Database.batchQuery(connInfo, queryUrl, batchSize)
                done = result.get("done")
                records = result.get("records")

                print(json.dumps(records, indent=2))

        except Exception as e:
            print('Exception -> ', e)
    ```

    `done`의 값이 **false**일 경우, 반복해서 `nextRecordsUrl`을 호출하도록 합니다.

6. 터미널에서 `python sample.py`이라고 명령을 실행해 봅니다.

    `done`값이 **true**가 될때까지 반복해서 호출하는것을 확인할 수 있습니다.

