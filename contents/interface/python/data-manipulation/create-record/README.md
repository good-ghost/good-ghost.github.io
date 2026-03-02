---
title: "단일 레코드 생성"
sort: 3
---

# 단일 레코드 생성

SObject의 API Name과 필드정보를 바탕으로 하나의 레코드를 생성합니다.

1. **Database** 모듈에 아래의 메소드를 추가합니다

    **HttpPost** 메소드를 사용합니다.

    ``` python
    def createRecord(connInfo, objectName, data):
        result = None
        try:
            headers = {
                'Content-Type' : 'application/json',
                'Authorization' : 'Bearer %s' % connInfo.get('accessToken') 
            }

            serviceEndpoint = "/services/data/v58.0/sobjects/%s/" % objectName
            url = "%s%s" % (connInfo.get('instanceUrl'), serviceEndpoint)

            req = requests.Request('POST', url, data=data, headers=headers)

            prepped = req.prepare()
            
            result = call(prepped)

        except Exception as e:
            print('Exception -> ', e)

        return result
    ```

2. **sample.py** 모듈에 아래의 메소드를 추가합니다.

    ``` python
    def createRecord(ConnectionInfo connectionInfo):
        try:
            objectName = "Account"
            recordBody = {
                "Name" : "Dev Test Company",
                "NumberOfEmployees" : 100            
            }
            
            result = Database.createRecord(connInfo, objectName, json.dumps(recordBody))

            print(json.dumps(result, indent=2))

        except Exception as e:
            print('Exception -> ', e)
    ```

    `recordBody`는 JsonObject로 만들어서 String 형태로 변환하여 사용해도 됩니다.

3. **sample.py** 모듈의 `main` 메소드에 아래의 내용을 추가합니다.

    ``` python
        # Data function block begin
        # querySample(connInfo)
        # batchSample(connInfo)
        # querySingle(connInfo)
        createRecord(connInfo)
        
        # Data function block end
    ```

4. 터미널에서 `python sample.py`이라고 명령을 실행해 봅니다.

    아래와 같은 형식의 데이터가 표시되면 정상적으로 레코드가 생성이 된것입니다.

    ``` json
	{
	  "id": "001*****************",
	  "success": true,
	  "errors": []
	}
    ```

    여기서 만들어진 레코드를 사용하여 레코드 업데이트 및 삭제를 진행해 보도록 하겠습니다.
    