---
title: "단일 레코드 생성"
sort: 3
---

# 단일 레코드 생성

SObject의 API Name과 필드정보를 바탕으로 하나의 레코드를 생성합니다.

1. **Database** 클래스에 아래의 메소드를 추가합니다

    **HttpPost** 메소드를 사용합니다.

    ``` java
    public static JsonObject createRecord(ConnectionInfo connInfo, String objectName, String jsonBody) {
        JsonObject jsonObject = null;
        try {
            String serviceEndpoint = "/services/data/v58.0/sobjects/" + objectName + "/";
            String url = connInfo.getInstanceUrl() + serviceEndpoint;

            HttpPost req = new HttpPost(url);
            req.setHeader("Content-type", "application/json");
            req.setHeader("Authorization", "Bearer " + connInfo.getAccessToken());

            req.setEntity(new StringEntity(jsonBody, ContentType.APPLICATION_JSON));

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
    public static void createRecord(ConnectionInfo connectionInfo){
        try {
            String objectName = "Account";
            String recordBody = "" +
            "{" +
            "    \"Name\" : \"Dev Test Company\"," +
            "    \"NumberOfEmployees\" : 100" +            
            "}";
            
            JsonObject jsonObject = Database.createRecord(connectionInfo, objectName, recordBody);

            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            String json = gson.toJson(jsonObject);

            System.out.println(json);
        } catch (Exception e) {
            System.out.println(e);
        }
    }
    ```

    `recordBody`는 JsonObject로 만들어서 String 형태로 변환하여 사용해도 됩니다.

3. **App** 클래스의 `main` 메소드를 아래와 같이 수정을 합니다.

    ``` java
        // Data function block begin
        //querySample(connectionInfo);
        //batchSample(connectionInfo);
        //querySingle(connectionInfo);

        createRecord(connectionInfo);
        // Data function block end
    ```

4. `main` 메소드 위의 `Run`을 클릭하여 프로그램을 실행해 봅니다.

    아래와 같은 형식의 데이터가 표시되면 정상적으로 레코드가 생성이 된것입니다.

    ``` json
	{
	  "id": "001IS000002kYvQYAU",
	  "success": true,
	  "errors": []
	}
    ```

    여기서 만들어진 레코드를 사용하여 레코드 업데이트 및 삭제를 진행해 보도록 하겠습니다.
    