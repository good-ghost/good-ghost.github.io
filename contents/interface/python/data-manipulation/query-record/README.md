---
title: "단일 레코드 조회"
sort: 2
---

# 단일 레코드 조회

SObject의 API Name과 레코드 Id를 사용하여 레코드의 상세 정보를 조회합니다.

1. **Database** 모듈에 아래의 메소드를 추가합니다

    **HttpGet** 메소드를 사용합니다.

    ``` python
    def querySingle(connInfo, objectName, recordId):
        result = None
        try:
            headers = {
                'Content-Type' : 'application/x-www-form-urlencoded',
                'Authorization' : 'Bearer %s' % connInfo.get('accessToken') 
            }
            
            serviceEndpoint = "/services/data/v58.0/sobjects/%s/%s" % (objectName, recordId)
            
            url = '%s%s' % (connInfo.get('instanceUrl'), serviceEndpoint)
            
            req = requests.Request('GET', url, headers=headers)
            
            prepped = req.prepare()
            
            result = call(prepped)

        except Exception as e:
            print('Exception -> ', e)

        return result
    ```

2. **sample.py** 모듈에 아래의 메소드를 추가합니다.

    ``` python
    def querySingle(connInfo):
        try:
            objectName = "Account"
            recordId = "001xxxxxxxxxxxxxxx"
            result = Database.querySingle(connInfo, objectName, recordId)

            print(json.dumps(result, indent=2))

        except Exception as e:
            print('Exception -> ', e)
    ```

    `recordId`에는 정상적인 Account의 레코드 Id를 지정하시기 바랍니다.

3. **sample.py** 모듈의 `main` 메소드에 아래의 내용을 추가합니다.

    ``` python
        # Data function block begin
        # querySample(connInfo)
        # batchSample(connInfo)
        querySingle(connInfo)
        
        # Data function block end
    ```

4. 터미널에서 `python sample.py`이라고 명령을 실행해 봅니다.

    아래와 같은 형식의 데이터가 표시되면 정상적으로 동작을 하는 것입니다.

    ``` json
    {
      "attributes": {
        "type": "Account",
        "url": "/services/data/v58.0/sobjects/Account/001xxxxxxxxxxxxxxx"
      },
      "Id": "001xxxxxxxxxxxxxxx",
      "IsDeleted": false,
      "Name": "GenePoint",
      "Type": "Customer - Channel",
      "BillingStreet": "345 Shoreline Park\nMountain View, CA 94043\nUSA",
      "BillingCity": "Mountain View",
      "BillingState": "CA",
      "BillingAddress": {
        "city": "Mountain View",
        "state": "CA",
        "street": "345 Shoreline Park\nMountain View, CA 94043\nUSA"
      },
      "ShippingStreet": "345 Shoreline Park\nMountain View, CA 94043\nUSA",
      ...
      ...
    }
    ```

