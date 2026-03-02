---
title: "단일 레코드 조회"
sort: 2
---

# 단일 레코드 조회

SObject의 API Name과 레코드 Id를 사용하여 레코드의 상세 정보를 조회합니다.

1. **Database** 클래스에 아래의 메소드를 추가합니다

    **HttpGet** 메소드를 사용합니다.

    ``` java
    public static JsonObject querySingle(ConnectionInfo connInfo, String objectName, String recordId) {
        JsonObject jsonObject = null;
        try {
            String serviceEndpoint = "/services/data/v58.0/sobjects/" + objectName + "/" + recordId;
            String url = connInfo.getInstanceUrl() + serviceEndpoint;

            HttpGet req = new HttpGet(url);
            req.setHeader("Content-type", "application/x-www-form-urlencoded");
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
    public static void querySingle(ConnectionInfo connectionInfo){
        try {
            String objectName = "Account";
            String recordId = "001xxxxxxxxxxxxxxx";
            JsonObject jsonObject = Database.querySingle(connectionInfo, objectName, recordId);


            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            String json = gson.toJson(jsonObject);

            System.out.println(json);
        } catch (Exception e) {
            System.out.println(e);
        }
    }
    ```

    `recordId`에는 정상적인 Account의 레코드 Id를 지정하시기 바랍니다.

3. **App** 클래스의 `main` 메소드를 아래와 같이 수정을 합니다.

    ``` java
        // Data function block begin
        //querySample(connectionInfo);
        //batchSample(connectionInfo);

        querySingle(connectionInfo);
        // Data function block end
    ```

4. `main` 메소드 위의 `Run`을 클릭하여 프로그램을 실행해 봅니다.

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

