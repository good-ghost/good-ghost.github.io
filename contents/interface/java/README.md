---
title: "Java 프로그램 개발"
sort: 2
---

# Java 프로그램 개발

OAuth2와 JWT 인증 방식을 사용하는 Java 프로그램의 개발 방법입니다.

이 설명에서 사용하는 샘플 코드는 [[여기]](/assets/codes/java_sample.zip)에서 다운로드 할 수 있습니다.

아래에 설명하는 내용은 전문적인 Java 개발자를 위한 내용이 아닙니다.
Salesforce 개발자가 인터페이스 프로그램을 이해하기 위한 기본적인 내용만을 설명하고 있습니다.

- [VS Code를 위한 Java 개발환경 설정](./java-environment-for-vscode.html)
- [Java 프로젝트 구성](./java-config-project.html)

- Salesforce에 연결하기

    - [인증 토큰 만들기](./connected-app/java-create-claim-token.html)

    - [access_token 발급받기](./connected-app/java-get-access-token.html)

- Salesforce에서 데이터를 쿼리, 생성, 업데이트, 삭제하기

    데이터를 조작하는 코드를 작성하기 위하여, **JAVA PROJECT**에 `Database`라는 클래스를 추가합니다.

    - [데이터 처리를 위한 공통 메소드를 작성합니다](./data-manipulation/)

    - [쿼리하기](./data-manipulation/query/)

        - [SOQL 쿼리하기](./data-manipulation/query/java-data-process-query-records.html)

        - [Batch 쿼리하기](./data-manipulation/query/java-data-process-batch-query.html)

    - [단일 레코드 조회](./data-manipulation/query-record/)

    - [단일 레코드 생성](./data-manipulation/create-record/)

    - [단일 레코드 업데이트](./data-manipulation/update-record/)

    - [단일 레코드 삭제](./data-manipulation/delete-record/)

    - [복수 레코드 생성](./data-manipulation/create-records/)

    - [복수 레코드 업데이트](./data-manipulation/update-records/)

    - [복수 레코드 삭제](./data-manipulation/delete-records/)
