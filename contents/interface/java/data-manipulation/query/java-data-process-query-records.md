---
title: "SOQL 쿼리하기"
sort: 1
---

# SOQL 쿼리하기

Salesforce REST API에서 SOQL 쿼리는 `GET` 방식을 사용하여 데이터를 조회할 수 있습니다.

쿼리문의 형식은 다음과 같습니다.

`<instanceUrl>/services/data/vXX.x/query?q=<URL Encoded SOQL Query String>`

`vXX.x`는 API의 버전을 의미하며, 이 설명에서는 v58.0을 적용하였습니다.

1. **Database** 클래스에 아래의 메소드를 추가합니다.

    **HttpGet** 메소드를 사용합니다.

    ``` java
    public static JsonObject query(ConnectionInfo connInfo, String query) {
        JsonObject jsonObject = null;
        try {
            String encodedQuery = URLEncoder.encode(query, StandardCharsets.UTF_8.toString());
            String url = connInfo.getInstanceUrl() + 
                "/services/data/v58.0/query?q=" + encodedQuery;

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
    public static void querySample(ConnectionInfo connectionInfo){
        try {
            String query = "SELECT Id, Name FROM Account LIMIT 10";
            JsonObject jsonObject = Database.query(connectionInfo, query);


            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            String json = gson.toJson(jsonObject);
            System.out.println(json);
        } catch (Exception e) {
            System.out.println(e);
        }
    }
    ```

3. **App** 클래스의 `main` 메소드에 아래의 내용을 추가합니다.

    ``` java
    // Data function block begin
    querySample(connectionInfo);

    // Data function block end
    ```

4. `main` 메소드 위의 `Run`을 클릭하여 프로그램을 실행해 봅니다.

   아래와 같은 형식의 데이터가 표시되면 정상적으로 동작을 하는 것입니다.

   ````result
   ``` json
   {
     "totalSize": 10,
     "done": true,
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
   ```
   ````