---
title: "단일 레코드 삭제"
sort: 5
---

# 단일 레코드 삭제

SObject의 API Name과 레코드 Id를 사용하여 레코드 삭제합니다.

1. **Database** 클래스에 아래의 메소드를 추가합니다

    **HttpDelete** 메소드를 사용합니다.

    ``` java
    public static JsonObject deleteRecord(ConnectionInfo connInfo, String objectName, String recordId) {
        JsonObject jsonObject = null;
        try {
            String serviceEndpoint = "/services/data/v58.0/sobjects/" + objectName + "/" + recordId;
            String url = connInfo.getInstanceUrl() + serviceEndpoint;

            HttpDelete req = new HttpDelete(url);
            req.setHeader("Content-type", "application/json");
            req.setHeader("Authorization", "Bearer " + connInfo.getAccessToken());

            String result = call(req);

            try {
                JsonElement jl = JsonParser.parseString(result);
                jsonObject = jl.getAsJsonObject();
            } catch (JsonParseException jsonException) {
                jsonException.printStackTrace();
            }

            req.releaseConnection();
        } catch (Exception e) {
            System.out.println(e);
        }

        return jsonObject;
    }
    ```

2. **App** 클래스에 아래의 메소드를 추가합니다.

    ``` java
    public static void deleteRecord(ConnectionInfo connectionInfo){
        try {
            String objectName = "Account";
            String recordId = "001***************";

            JsonObject jsonObject = Database.updateRecord(connectionInfo, objectName, recordId);

            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            String json = gson.toJson(jsonObject);

            System.out.println(json);
        } catch (Exception e) {
            System.out.println(e);
        }
    }
    ```

    `recordId`는 앞글 [**단일 레코드 생성**](../create-record/)에서 생성된 레코드의 Id입니다.

3. **App** 클래스의 `main` 메소드를 아래와 같이 수정을 합니다.

    ``` java
        // Data function block begin
        //querySample(connectionInfo);
        //batchSample(connectionInfo);
        //querySingle(connectionInfo);
        //createRecord(connectionInfo);
        //updateRecord(connectionInfo);

        deleteRecord(connectionInfo);
        // Data function block end
    ```

4. `main` 메소드 위의 `Run`을 클릭하여 프로그램을 실행해 봅니다.

    `delete`는 반환하는 메시지가 없습니다. 정상적으로 동작했다는 것을 표시하기 위하여 아래 내용을 출력하도록 `call` 메소드에 처리 기능을 넣습니다.

    ``` json
	{
	  "result": "success"
	}
    ```

