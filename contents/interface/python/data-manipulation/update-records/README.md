---
title: "복수 레코드 업데이트"
sort: 7
---

# 복수 레코드 업데이트

Json 오브젝트 형식을 사용하여 복수의 레코드를 업데이트할 수 있습니다. 최대 **200**개까지 동시에 업데이트가 가능하며, 모든 레코드에 오류가 없을 경우에만 업데이트하도록 하거나 일부 오류가 있더라도 나머지 레코드를 업데이트할 수 있습니다.

1. **Database** 모듈에 아래의 메소드를 추가합니다

    **HttpPatch**(Upsert)를 사용합니다. 또한, 복수의 레코드 작업은 **Json** 오브젝트가 아닌 **Json** 배열 형식의 결과를 반환합니다. 레코드들을 생성할 수 있는 Json 파라메터를 사용하면 레코드들을 생성할 수도 있습니다.

    ``` python
    def updateRecords(connInfo, data):
        result = None
        try:
            headers = {
                'Content-Type' : 'application/json',
                'Authorization' : 'Bearer %s' % connInfo.get('accessToken') 
            }

            url = "%s/services/data/v58.0/composite/sobjects" % connInfo.get('instanceUrl')

            req = requests.Request('PATCH', url, data=data, headers=headers)

            prepped = req.prepare()
            
            result = call(prepped)

        except Exception as e:
            print('Exception -> ', e)

        return result
    ```

2. **sample.py** 모듈에 아래의 메소드를 추가합니다.

    ``` python
    def updateRecords(connInfo):
        try:
            collection = {
                "allOrNone" : False,
                "records" : [{
                    "attributes" : {"type" : "Account"},
                    "Id" : "001***************",
                    "Name" : "Test Company 1 Modified"
                }, {
                    "attributes" : {"type" : "Account"},
                    "Id" : "001***************",
                    "Name" : "Test Company 2 Modified"
                }]
		    }

            result = Database.updateRecords(connInfo, json.dumps(collection))

            print(json.dumps(result, indent=2))

        except Exception as e:
            print('Exception -> ', e)
    ```

    `Id`에 사용되는 레코드 Id들은 [**복수 레코드 생성**](../create-records/)에서 생성된 레코드들입니다.


    `allOrNone` 값은 `True`이면 모든 레코드 셋에 오류가 없어야만 정상적으로 레코드들이 생성됩니다. `False`이면 오류가 섞여 있어도 오류가 없는 레코드들만 생성을 합니다.

3. **sample.py** 모듈의 `main` 메소드에 아래의 내용을 추가합니다.

    ``` python
        # Data function block begin
        # querySample(connInfo)
        # batchSample(connInfo)
        # querySingle(connInfo)
        # createRecord(connInfo)
        # updateRecord(connInfo)
        # deleteRecord(connInfo)
        # createRecords(connInfo)
        updateRecords(connInfo)
        
        # Data function block end
    ```

4. 터미널에서 `python sample.py`이라고 명령을 실행해 봅니다.

    Json 배열로 결과값을 반환합니다.

    ``` json
	[
	  {
	    "id": "001***************",
	    "success": true,
	    "errors": []
	  },
	  {
	    "id": "001***************",
	    "success": true,
	    "errors": []
	  }
	]    
	```

