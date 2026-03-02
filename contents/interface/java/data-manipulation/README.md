---
title: "Salesforce에서 데이터를 쿼리, 생성, 업데이트, 삭제하기"
sort: 4
---

# Salesforce에서 데이터를 쿼리, 생성, 업데이트, 삭제하기

## Salesforce 호출용 공통 메소드 작성하기

데이터를 처리하는 코드를 작성하기 위하여, **JAVA PROJECT**에 `Database`라는 클래스를 추가합니다.

먼저 아래에 설명하는 데이터 조작에서 공통적으로 사용하게 될 **HTTP Request** 메소드를 만듭니다.

`Database` 클래스에 아래의 코드를 추가합니다.

``` java
    public static String call(HttpRequestBase req) {
        String result = null;
        try {
            HttpClient client = HttpClientBuilder.create().build();
            HttpResponse response = null;
            try {
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
            try {
                if (statusCode == HttpStatus.SC_OK || statusCode == HttpStatus.SC_CREATED) {
                    result = EntityUtils.toString(response.getEntity());
                } else if(statusCode == HttpStatus.SC_NO_CONTENT) {
                    result = "{\"result\":\"success\"}";
                } else {
                    System.out.println("Error authenticating to Force.com: "+statusCode);
                    System.out.println(response.toString());
                    return null;    
                }
    
            } catch (IOException ioException) {
                ioException.printStackTrace();
            }

        } catch (Exception e) {
            System.out.println(e);
        }

        return result;
    }
```

이 글에서는 Http 관련 메소드를 `org.apache.httpcomponents` 라이브러리를 사용하고 있지만 사용자가 편한 라이브러리를 사용하면 됩니다.

`call()` 메소드는 데이터 생성, 조회, 업데이트, 삭제시에 사용하는 공통 메소드입니다. 각각 Http 호출기능의 `POST`, `GET`, `PATCH`, `DELETE`를 사용하므로 `call()` 메소드는 *Request*를 매개변수로 사용을 하고 있습니다.


## 데이터 처리하기

- 쿼리하기

    - [SOQL 쿼리하기](./query/java-data-process-query-records.html)

    - [Batch 쿼리하기](./query/java-data-process-batch-query.html)

- [단일 레코드 조회](./query-record/)

- [단일 레코드 생성](./create-record/)

- [단일 레코드 업데이트](./update-record/)

- [단일 레코드 삭제](./delete-record/)

- [복수 레코드 생성](./create-records/)

- [복수 레코드 업데이트](./update-records/)

- [복수 레코드 삭제](./delete-records/)
