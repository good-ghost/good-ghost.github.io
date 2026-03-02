---
title: "access_token 발급받기"
sort: 2
---


# access_token 발급받기

생성된 Token으로 Salesforce에 인증 요청을 하기 위하여 아래의 코드를 `Auth` 클래스에 추가합니다.

``` java
public static JsonObject getAuth(String clientId, String loginUrl, String loginId, PrivateKey privateKey) 
    throws KeyStoreException, NoSuchAlgorithmException, CertificateException, IOException, UnrecoverableKeyException {
    JsonObject jsonObject = null;
    try {
        //////////////////
        // Make a Token //
        //////////////////
        StringBuffer token = getToken(clientId, loginUrl, loginId, privateKey);

        //////////////////////
        // Query Token Part //
        //////////////////////
        String grant_type = "urn:ietf:params:oauth:grant-type:jwt-bearer";
              
        String url = loginUrl + "/services/oauth2/token";
        List<NameValuePair> nameValuePairs = new ArrayList<NameValuePair>();
        nameValuePairs.add(new BasicNameValuePair("grant_type", grant_type));
        nameValuePairs.add(new BasicNameValuePair("assertion", token.toString()));

        HttpClient client = HttpClientBuilder.create().build();
        HttpPost req = new HttpPost(url);
        req.setHeader("Content-type", "application/x-www-form-urlencoded");
        HttpResponse response = null;
        try {
            req.setEntity(new UrlEncodedFormEntity(nameValuePairs, "utf-8"));
            response = client.execute(req);
            System.out.println(response.getStatusLine());
        } catch(UnsupportedEncodingException ueException){
            ueException.printStackTrace();
        } catch(ClientProtocolException cpException){
            cpException.printStackTrace();
        } catch(IOException ioException){
            ioException.printStackTrace();
        }

        final int statusCode = response.getStatusLine().getStatusCode();
        if (statusCode != HttpStatus.SC_OK) {
            System.out.println("Error authenticating to Force.com: " + statusCode);
            System.out.println(response.toString());
            return null;
        }

        String getResult = null;
        try {
            getResult = EntityUtils.toString(response.getEntity());
        } catch (IOException ioException) {
            ioException.printStackTrace();
        }

        try {
            JsonElement jl = JsonParser.parseString(getResult);
            jsonObject = jl.getAsJsonObject();
        } catch (JsonParseException jsonException) {
            jsonException.printStackTrace();
        }

        req.releaseConnection();

        System.out.println("Successful login");
    } catch (Exception e) {
        System.out.println(e);
    }

    return jsonObject;
}
```

- **grant_type** : 인증으로 요청하는 형식입니다.

`App.java` 파일의 `main` 메소드에 아래와 같이 호출 코드를 추가하고 'Run'을 실행해 봅니다.

``` java
public static void main( String[] args ) throws Exception {
    try {
        PrivateKey privateKey = Auth.getPrivateKeyFromKeyFile("./company.key");

        //////////////////
        // Make a Token //
        //////////////////
        String consumerKey = "3MVG9YDQS5WtC11q60F............QYX68awYhUzKVMMB";
        String loginUrl    = "https://login.salesforce.com/";
        String loginUser   = "username@example.com";

        JsonObject jsonObject = Auth.getAuth(consumerKey, loginUrl, loginUser, privateKey);

        if(jsonObject != null) {
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            String json = gson.toJson(jsonObject);

            System.out.println(json);
        }

    } catch(Exception e) {
        System.out.println(e);
    }
}
```

아래와 같은 문자열이 표시되면 정상적으로 access_token을 발급받은 것입니다.

``` json
{
  "access_token": "00D......JurjbZDxSflyj",
  "scope": "visualforce ... id api full",
  "instance_url": "https://********.my.salesforce.com",
  "id": "https://***.salesforce.com/id/00D******/005*******",
  "token_type": "Bearer"
}
```

인증 절차가 정상적으로 동작하므로, Salesforce의 데이터 조작을 위하여 `App.java`의 메소드를 아래와 같이 정리를 합니다.

``` java
public static void main(String[] args) {
    try {

        ConnectionInfo connectionInfo = getAuth();

        System.out.println("Access Token : " + connectionInfo.getAccessToken());
        System.out.println("Instance Url : " + connectionInfo.getInstanceUrl());


        // Data function block begin


        // Data function block end

    } catch (Exception e) {
        System.out.println(e);
    }
}

public static ConnectionInfo getAuth(){
    ConnectionInfo info = new ConnectionInfo();
    try {
        String consumerKey = "3MVG9YDQS5WtC11q60F............QYX68awYhUzKVMMB";
        String loginUrl    = "https://login.salesforce.com/";
        String loginUser   = "username@example.com";

        // Converted PrivateKey(PKCS#1) to PKCS#8 by
        // openssl pkcs8 -topk8 -inform PEM -outform PEM -in rsa_private.key -out pkcs8PrivateKey.pem -nocrypt
        // String pkcs8PrivateKeyString = "" +
        // "MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDN4c80VU9yjbAu" +
        // "JWq3RYPBsLnP2wcipVdGIzPYLqyXUq2GBlhyaLjN0NI5mO+AyXE8FSJtD/Y22ILQ" +
        // "..." +
        // "..." +
        // "..." +
        // "Qmh4OV5ZHXwx5R6ffAOAwAY=";
        // PrivateKey privateKey = Auth.getPrivateKeyFromString(pkcs8PrivateKeyString);
        // PrivateKey privateKey = Auth.getPrivateKeyFromJKS("./company.jks", "company", "password");
        PrivateKey privateKey = Auth.getPrivateKeyFromKeyFile("./company.key");

        JsonObject jsonObject = Auth.getAuth(consumerKey, loginUrl, loginUser, privateKey);

        info.setAccessToken(jsonObject.get("access_token").toString().replaceAll("\"", ""));
        info.setInstanceUrl(jsonObject.get("instance_url").toString().replaceAll("\"", ""));

        // Gson gson = new GsonBuilder().setPrettyPrinting().create();
        // String json = gson.toJson(jsonObject);
        // System.out.println(json);
    } catch (Exception e) {
        System.out.println(e);
    }

    return info;
}
```

`App.java` 파일의 'Run'을 실행하여 Access Token 과 Instance Url의 값이 표시되는지 확인해 봅니다.

