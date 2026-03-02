---
title: "인증 토큰 만들기"
sort: 1
---

# 인증 토큰 만들기

인증 절차에 사용할 인증서 및 개인키는 [Salesforce Connected App 설정](../../connectedapp/setup-connected-app.html) 설명에서 사용된 인증서(company.crt)에 대응하는 만든 키스토어 또는 개인키를 사용합니다. **company.jks**, **company.key** 파일을 프로젝트의 루트 폴더에 복사를 해둡니다. 실제로는 jks, key 둘 중에 한가지만 필요합니다. 뒤에서 설명하는 인증서/개인키 사용 방법에 따라 선택을 하시면 됩니다. **Client Key**와 Connected App에 접속할 수 있는 **사용자 Id**도 준비를 해둡니다.

## Class 만들기

인증 받기 코드에 사용할 Java Class를 생성합니다.

이 설명에서는 Maven 프로젝트를 기준으로 설명을 합니다.

1. **JAVA PROJECTS**의 `App` 오른쪽의 '+' 기호를 클릭합니다.

   ![Create Class 1](/assets/images/javacode-001.png)

2. **Select resource type to create.** 목록에서 **Class**를 선택합니다.

   ![Create Class 2](/assets/images/javacode-002.png)

3. 생성하려는 클래스 이름을 입력하고 엔터키를 누릅니다. 여기서는 *Auth* 클래스를 생성합니다.

   ![Create Class 3](/assets/images/javacode-003.png)

4. **Auth.java** Java 클래스 파일이 생성됩니다. 같은 방법으로 *ConnectionInfo* 클래스를 생성합니다.

   ![Create Class 4](/assets/images/javacode-004.png)

   **ConnectionInfo.java** 파일을 열고 아래와 같이 내용을 추가한 후에 저장을 합니다. 데이터 조작을 위한 API를 사용할때 필요한 *instanceUrl*과 *accessToken*을 처리하기 위한 클래스입니다.

    ``` java
    public class ConnectionInfo {

        private String instanceUrl;
        private String accessToken;

        public void setInstanceUrl(String param){
            this.instanceUrl = param;
        }

        public String getInstanceUrl(){
            return this.instanceUrl;
        }

        public void setAccessToken(String param){
            this.accessToken = param;
        }

        public String getAccessToken(){
            return this.accessToken;
        }

    }
    ```

## 코드 구성

인증을 받기 위하여 Salesforce Connnected App에 전송하는 토큰은 다음과 같이 `.`으로 연결된 3부분으로 이루어져 있습니다.

   [*JWT 헤더*].[*인증 요청정보*].[*개인키로 암호화된 헤더+요청정보*]  (`JWT Header.Claim Payload.Header + Signed Payload`)

순서대로 만들어 보도록 하겠습니다.

1. `Auth.java` 클래스 윗 부분에 아래의 내용을 추가를 합니다.

    ``` java
    package com.example;

    import java.io.File;
    import java.io.FileInputStream;
    import java.io.IOException;
    import java.io.UnsupportedEncodingException;
    import java.nio.charset.Charset;
    import java.nio.file.Files;
    import java.security.KeyFactory;
    import java.security.KeyStore;
    import java.security.KeyStoreException;
    import java.security.NoSuchAlgorithmException;
    import java.security.PrivateKey;
    import java.security.Signature;
    import java.security.UnrecoverableKeyException;
    import java.security.cert.CertificateException;
    import java.security.spec.PKCS8EncodedKeySpec;
    import java.text.MessageFormat;
    import java.util.ArrayList;
    import java.util.List;

    import org.apache.commons.codec.binary.Base64;
    import org.apache.http.HttpResponse;
    import org.apache.http.HttpStatus;
    import org.apache.http.NameValuePair;
    import org.apache.http.client.ClientProtocolException;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.entity.UrlEncodedFormEntity;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.impl.client.HttpClientBuilder;
    import org.apache.http.message.BasicNameValuePair;
    import org.apache.http.util.EntityUtils;

    import com.google.gson.JsonElement;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParseException;
    import com.google.gson.JsonParser;

    public class Auth {

    }
    ```

    사용하지 않는 패키지는 나중에 제거할 수 있습니다.

2. 가장 먼저 개인키(PrivateKey)를 프로그램에 적재하여야 합니다. 개인키를 적재하는 방법은 아래의 세가지 방법이 있으니 상황에 맞는 방법을 사용하시면 됩니다.

    - JKS 키스토어의 개인키를 적재하는 방법

        아래의 메소드 코드를 `Auth` 클래스에 추가합니다.

        ``` java
        public static PrivateKey getPrivateKeyFromJKS(String filePath, String alias, String password) throws Exception {
            PrivateKey privateKey = null;
            try {
                //Load the private key from a jks keystore
                KeyStore keystore = KeyStore.getInstance("JKS");
                keystore.load(new FileInputStream(filePath), password.toCharArray());
     
                privateKey = (PrivateKey) keystore.getKey(alias, password.toCharArray());
            } catch (Exception e) {
                System.out.println(e);
            }

            return privateKey;
        }     
        ```

        `App.java` 파일의 `main` 메소드에 아래와 같이 호출 코드를 추가하고 'Run'을 실행해 봅니다.

        ``` java
        public static void main( String[] args ) throws Exception {
            try {
                PrivateKey privateKey = Auth.getPrivateKeyFromJKS("./company.jks", "company", "password");

                if(privateKey != null) {
                    System.out.println("Private Key loaded...");
                }

            } catch(Exception e) {
                System.out.println(e);
            }
        }
        ```

        - **company.jks** : jks 키스토어 파일입니다.
        - **company** : jks 키스토어를 생성할 때 `-destalias`로 지정한 alias입니다. 
        - **password** : jks 키스토어를 생성할 떄 지정한 암호입니다.


    - 개인키 파일의 개인키를 스트링으로 적재하는 방법

        아래의 메소드 코드를 `Auth` 클래스에 추가합니다.

        ``` java
        public static PrivateKey getPrivateKeyFromString(String pkcs8PrivateKeyString) throws Exception {
            PrivateKey privateKey = null;
            try {            
                KeyFactory factory;
                factory = KeyFactory.getInstance("RSA");
                byte[] encodedPK = Base64.decodeBase64(pkcs8PrivateKeyString);
                PKCS8EncodedKeySpec keySpecPv = new PKCS8EncodedKeySpec(encodedPK);
                privateKey = factory.generatePrivate(keySpecPv);
            } catch (Exception e) {
                System.out.println(e);
            }

            return privateKey;
        }
        ```

        `App.java` 파일의 `main` 메소드에 아래와 같이 호출 코드를 추가하고 'Run'을 실행해 봅니다.

        company.key 파일을 노트패드에서 열고, "-----BEGIN PRIVATE KEY-----" 행과 "-----END PRIVATE KEY-----" 행 사이의 문자열을 개행 문자를 제외하고 `pkcs8PrivateKeyString` 변수에 지정을 합니다.

        ``` java
        public static void main( String[] args ) throws Exception {
            try {
                String pkcs8PrivateKeyString = "" +
                "MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDN4c80VU9yjbAu" +
                "JWq3RYPBsLnP2wcipVdGIzPYLqyXUq2GBlhyaLjN0NI5mO+AyXE8FSJtD/Y22ILQ" +
                "..." +
                "..." +
                "..." +
                "Qmh4OV5ZHXwx5R6ffAOAwAY=";
                PrivateKey privateKey = Auth.getPrivateKeyFromString(pkcs8PrivateKeyString);

                if(privateKey != null) {
                    System.out.println("Private Key loaded...");
                }

            } catch(Exception e) {
                System.out.println(e);
            }
        }
        ```

    - 개인키 파일의 개인키를 적재하는 방법

        이 메소드를 사용하려면 아래의 라이브러리를 추가로 지정하여야 합니다.

        - Maven dependency에 추가 (pom.xml)
            
            `bcpkix-jdk18on`(org.bouncycastle)

        - Gradle dependency에 추가 (build.gradle)

            `org.bouncycastle:bcpkix-jdk18on:1.77`

        `Auth.java` 파일에 import할 패키지를 추가합니다.

        ``` java
        import java.io.FileReader;
        import org.bouncycastle.util.io.pem.PemObject;
        import org.bouncycastle.util.io.pem.PemReader;
        ```

        `Auth.java` 파일에 아래의 메소드를 추가합니다.

        ``` java
        public static PrivateKey getPrivateKeyFromKeyFile(String filePath) throws Exception {
            PrivateKey privateKey = null;
            File file = new File(filePath);
            
            try (FileReader keyReader = new FileReader(file);
                PemReader pemReader = new PemReader(keyReader)){
                    PemObject pemObject = pemReader.readPemObject();
                    byte[] content = pemObject.getContent();
                    PKCS8EncodedKeySpec privKeySpec = new PKCS8EncodedKeySpec(content);
                    KeyFactory factory = KeyFactory.getInstance("RSA");

                    privateKey = (PrivateKey) factory.generatePrivate(privKeySpec);

            } catch (Exception e) {
                System.out.println(e);
            }

            return privateKey;
        }
        ```

        `App.java` 파일의 `main` 메소드에 아래와 같이 호출 코드를 추가하고 'Run'을 실행해 봅니다.

        ``` java
        public static void main( String[] args ) throws Exception {
            try {
                PrivateKey privateKey = Auth.getPrivateKeyFromKeyFile("./company.key");

                if(privateKey != null) {
                    System.out.println("Private Key loaded...");
                }

            } catch(Exception e) {
                System.out.println(e);
            }
        }
        ```

        **company.key** : 개인키 파일입니다.

    위의 세가지 방법중의 하나를 사용하도록 합니다.

3. 인증 요청 Token을 만들기 위하여 `Auth` 클래스에 아래의 메소드를 추가합니다.

    ``` java
    public static StringBuffer getToken(String clientId, String loginUrl, String loginId, PrivateKey privateKey) {
        StringBuffer token = new StringBuffer();
        try {
            //////////////////////
            // JWT Token Header //
            //////////////////////
            String header = "{\"alg\":\"RS256\"}";
 
            //Encode the JWT Header and add it to our string to sign
            token.append(Base64.encodeBase64URLSafeString(header.getBytes("UTF-8")));

            //////////////////////
            // Claim Payload    //
            //////////////////////

            //Separate with a period
            token.append(".");
 
            //Create the JWT Claims Object
            String claimTemplate = "'{'\"iss\": \"{0}\", \"sub\": \"{1}\", \"aud\": \"{2}\", \"exp\": \"{3}\"'}'";
            String[] claimArray = new String[4];
            claimArray[0] = clientId;
            claimArray[1] = loginId;
            claimArray[2] = loginUrl;
            claimArray[3] = Long.toString((System.currentTimeMillis()/1000) + 300);
            MessageFormat claims;
            claims = new MessageFormat(claimTemplate);
            String payload = claims.format(claimArray);
 
            //Add the encoded claims object
            token.append(Base64.encodeBase64URLSafeString(payload.getBytes("UTF-8")));
            
            //////////////////////
            // Private Key      //
            //////////////////////

            //Sign the JWT Header + "." + JWT Claims Object
            Signature signature = Signature.getInstance("SHA256withRSA");
            signature.initSign(privateKey);
            signature.update(token.toString().getBytes("UTF-8"));
            String signedPayload = Base64.encodeBase64URLSafeString(signature.sign());
 
            //Separate with a period
            token.append(".");

            //Add the encoded signature
            token.append(signedPayload);
 
            System.out.println(token.toString());

        } catch(Exception e){
            System.out.println(e);
        }
        return token;
    }
    ```

    - **clientId** : ConnectedApp에서 제공하는 **Consumer Key** 입니다.

    - **loginUrl** : *login.salesforce.com* 또는 *test.salesforce.com*을 사용합니다.

    - **loginId** : ConnectedApp에서 **Profile** 또는 **Permission Set**으로 잡속이 허용된 이메일 형식의 사용자 계정입니다.

    이 코드로 생성되는 Token 값은 Salesforce의 JWT Bearer 플로우를 위한 값입니다. 다른 시스템들은 Heade와 Claim이 다른 형식을 사용할 수 있습니다.

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
            StringBuffer token = Auth.getToken(consumerKey, loginUrl, loginUser, privateKey);

            if(token != null) {
                System.out.println(token);
            }

        } catch(Exception e) {
            System.out.println(e);
        }
    }
    ```

    `eyJhbGciOiJSUzI1NiJ9.ey....` 와 같은 인코딩된 문자열이 출력되면 정상입니다.

