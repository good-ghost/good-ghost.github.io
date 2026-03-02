---
title: "단일 레코드 업데이트"
sort: 4
---

# 단일 레코드 업데이트

SObject의 API Name과 레코드 Id를 사용하여 레코드의 상세 정보를 업데이트합니다.

1. **Database** 모듈에 아래의 메소드를 추가합니다

    **HttpPatch** 메소드를 사용합니다.

    ``` python
    def updateRecord(connInfo, objectName, recordId, data):
        result = None
        try:
            headers = {
                'Content-Type' : 'application/json',
                'Authorization' : 'Bearer %s' % connInfo.get('accessToken') 
            }

            serviceEndpoint = "/services/data/v58.0/sobjects/%s/%s" % (objectName, recordId)
            url = "%s%s" % (connInfo.get('instanceUrl'), serviceEndpoint)

            req = requests.Request('PATCH', url, data=data, headers=headers)

            prepped = req.prepare()
            
            result = call(prepped)

        except Exception as e:
            print('Exception -> ', e)

        return result
    ```

2. **sample.py** 모듈에 아래의 메소드를 추가합니다.

    ``` python
    def updateRecord(connInfo):
        try:
            objectName = "Account"
            recordId = "001*****************"
            recordBody = {
                "Name" : "Dev Test Company Modified",
                "NumberOfEmployees" : 200         
            }
            
            result = Database.updateRecord(connInfo, objectName, recordId, json.dumps(recordBody))

            print(json.dumps(result, indent=2))

        except Exception as e:
            print('Exception -> ', e)
    ```

    `recordId`는 앞글 [**단일 레코드 생성**](../create-record/)에서 생성된 레코드의 Id입니다.

3. **sample.py** 모듈의 `main` 메소드에 아래의 내용을 추가합니다.

    ``` python
        # Data function block begin
        # querySample(connInfo)
        # batchSample(connInfo)
        # querySingle(connInfo)
        # createRecord(connInfo)
        updateRecord(connInfo)
        
        # Data function block end
    ```

4. 터미널에서 `python sample.py`이라고 명령을 실행해 봅니다.

    `update`는 반환하는 메시지가 없습니다. 정상적으로 동작했다는 것을 표시하기 위하여 아래 내용을 출력하도록 `call` 메소드에 처리 기능을 넣습니다.

    ``` json
	{
	  "result": "success"
	}
    ```

