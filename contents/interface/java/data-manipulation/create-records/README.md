---
title: "복수 레코드 생성"
sort: 6
---

# 복수 레코드 생성

Json 오브젝트 형식을 사용하여 복수의 레코드를 생성할 수 있습니다. 최대 **200**개까지 동시에 생성이 가능하며, 모든 레코드에 오류가 없을 경우에만 생성하도록 하거나 일부 오류가 있더라도 나머지 레코드를 생성할 수 있습니다.

1. **Database** 클래스에 아래의 메소드를 추가합니다

    **HttpPost**(Insert) 메소드 또는 **HttpPatch**(Upsert)를 사용합니다. 또한, 복수의 레코드 작업은 **JsonObject**가 아닌 **JsonArray** 형식의 결과를 반환합니다.

    ``` java
    public static JsonArray createRecords(ConnectionInfo connInfo, String jsonString) {
        JsonArray jsonArray = null;
        try {
            String url = connInfo.getInstanceUrl() + 
                "/services/data/v58.0/composite/sobjects";

            HttpPost req = new HttpPost(url);
            req.setHeader("Content-type", "application/json");
            req.setHeader("Authorization", "Bearer " + connInfo.getAccessToken());

            req.setEntity(new StringEntity(jsonString, ContentType.APPLICATION_JSON));

            String result = call(req);

            try {
                JsonElement jl = JsonParser.parseString(result);
                jsonArray = jl.getAsJsonArray();
            } catch (JsonParseException jsonException) {
                jsonException.printStackTrace();
            }

            req.releaseConnection();
        } catch (Exception e) {
            System.out.println(e);
        }


        return jsonArray;
    }
    ```

2. **App** 클래스에 아래의 메소드를 추가합니다.

    ``` java
    public static void createRecords(ConnectionInfo connectionInfo){
        try {
            JsonObject objectType = new JsonObject();
            objectType.addProperty("type", "Account");

            JsonArray records = new JsonArray();

            JsonObject rec1 = new JsonObject();
            rec1.add("attributes", objectType);
            rec1.addProperty("Name", "Test Company 1");                
            records.add(rec1);

            JsonObject rec2 = new JsonObject();
            rec2.add("attributes", objectType);
            rec2.addProperty("Name", "Test Company 2");                
            records.add(rec2);

            JsonObject collection = new JsonObject();
            collection.addProperty("allOrNone", false);
            collection.add("records", records);

            JsonArray result = Database.createRecords(connectionInfo, collection.toString());

            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            String json = gson.toJson(result);

            System.out.println(json);
        } catch (Exception e) {
            System.out.println(e);
        }
    }
    ```

    `allOrNone` 값은 `true`이면 모든 레코드 셋에 오류가 없어야만 정상적으로 레코드들이 생성됩니다. `false`이면 오류가 섞여 있어도 오류가 없는 레코드들만 생성을 합니다.

    ````note
    JsonObject로 레코드셋을 만들지 않고 다음과 같이 String 으로 만들어서 `collection.toString()` 대신 파라메터로 사용할 수 있습니다.

    ``` java
    String jsonString = "{" +
    "    \"allOrNone\" : false," +
    "    \"records\" : [{" +
    "        \"attributes\" : {\"type\" : \"Account\"}," +
    "        \"Name\" : \"example.com\"," +
    "        \"BillingCity\" : \"San Francisco\"" +
    "     }, {" +
    "        \"attributes\" : {\"type\" : \"Contact\"}," +
    "        \"LastName\" : \"Johnson\"," +
    "        \"FirstName\" : \"Erica\"" +
    "     }]" +
    "}";
    ```
    위의 예처럼 서로 다른 SObject의 레코드를 혼합하여 생성할 수도 있습니다.
    ````

3. **App** 클래스의 `main` 메소드를 아래와 같이 수정을 합니다.

    ``` java
        // Data function block begin
        //querySample(connectionInfo);
        //batchSample(connectionInfo);
        //querySingle(connectionInfo);
        //createRecord(connectionInfo);
        //updateRecord(connectionInfo);
        //deleteRecord(connectionInfo);

        createRecords(connectionInfo);
        // Data function block end
    ```

4. `main` 메소드 위의 `Run`을 클릭하여 프로그램을 실행해 봅니다.

    Json 배열로 결과값을 반환합니다. 이렇게 생성된 레코드들로 `update`, `delete`를 진행하도록 합니다.

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

