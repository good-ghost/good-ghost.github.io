---
title: "복수 레코드 업데이트"
sort: 7
---

# 복수 레코드 업데이트

Json 오브젝트 형식을 사용하여 복수의 레코드를 업데이트할 수 있습니다. 최대 **200**개까지 동시에 업데이트가 가능하며, 모든 레코드에 오류가 없을 경우에만 업데이트하도록 하거나 일부 오류가 있더라도 나머지 레코드를 업데이트할 수 있습니다.

1. **Database** 클래스에 아래의 메소드를 추가합니다

    **HttpPatch**(Upsert)를 사용합니다. 또한, 복수의 레코드 작업은 **JsonObject**가 아닌 **JsonArray** 형식의 결과를 반환합니다. 레코드들을 생성할 수 있는 Json 파라메터를 사용하면 레코드들을 생성할 수도 있습니다.

    ``` java
    public static JsonArray updateRecords(ConnectionInfo connInfo, String jsonString) {
        JsonArray jsonArray = null;
        try {
            String url = connInfo.getInstanceUrl() + 
                "/services/data/v58.0/composite/sobjects";

            HttpPatch req = new HttpPatch(url);
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
    public static void updateRecords(ConnectionInfo connectionInfo){
        try {
		    String jsonString = "{" +
		    "    \"allOrNone\" : false," +
		    "    \"records\" : [{" +
		    "        \"attributes\" : {\"type\" : \"Account\"}," +
		    "        \"Id\" : \"001***************\"," +
		    "        \"Name\" : \"Test Company 1 Modified\"" +
		    "     }, {" +
		    "        \"attributes\" : {\"type\" : \"Account\"}," +
		    "        \"Id\" : \"001***************\"," +
		    "        \"Name\" : \"Test Company 2 Modified\"" +
		    "     }]" +
		    "}"

            JsonArray result = Database.updateRecords(connectionInfo, jsonString);

            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            String json = gson.toJson(result);

            System.out.println(json);
        } catch (Exception e) {
            System.out.println(e);
        }
    }
    ```

    `Id`에 사용되는 레코드 Id들은 [**복수 레코드 생성**](../create-records/)에서 생성된 레코드들입니다.


    `allOrNone` 값은 `true`이면 모든 레코드 셋에 오류가 없어야만 정상적으로 레코드들이 생성됩니다. `false`이면 오류가 섞여 있어도 오류가 없는 레코드들만 생성을 합니다.

3. **App** 클래스의 `main` 메소드를 아래와 같이 수정을 합니다.

    ``` java
        // Data function block begin
        //querySample(connectionInfo);
        //batchSample(connectionInfo);
        //querySingle(connectionInfo);
        //createRecord(connectionInfo);
        //updateRecord(connectionInfo);
        //deleteRecord(connectionInfo);
        //createRecords(connectionInfo);
        //updateRecords(connectionInfo);

        deleteRecords(connectionInfo);
        // Data function block end
    ```

4. `main` 메소드 위의 `Run`을 클릭하여 프로그램을 실행해 봅니다.

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

