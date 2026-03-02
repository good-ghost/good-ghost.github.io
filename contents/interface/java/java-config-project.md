---
title: "Java 프로젝트 설정"
sort: 2
---

# Salesforce REST API를 사용하기 위한 Java 프로젝트 설정

아래에 설명하는 내용은 전문적인 Java 개발자를 위한 내용이 아닙니다.
Salesforce 개발자가 인터페이스 프로그램을 이해하기 위한 기본적인 내용을 설명하고 있습니다.

## Maven을 사용할 때의 구성

빌드 도구로 Maven을 사용하는 Java 프로젝트를 생성을 합니다. 여기서는 Salesforce의 REST API 사용을 확인하는 예제로 간단하게 구성합니다.

1. **Visual Studio Code**를 실행하고 키보드에서 `Ctrl+Shift+P`를 누릅니다.

2. **Command Palette**에서 "**Java**: Create Java Project..."를 선택합니다.

3. **Maven**을 선택합니다.
   
    ![Config-001](/assets/images/configjava-001.png)

4. **Create Maven Project**에서 **maven-archetype-quickstart**를 선택합니다.
   
    ![Config-002](/assets/images/configjava-002.png)

5. **1.4**를 선택을 합니다.
   
    ![Config-003](/assets/images/configjava-003.png)

6. **Input group Id of your project**에서 **com.example**을 확인하고 엔터키를 누릅니다.
   
    ![Config-004](/assets/images/configjava-004.png)
   
    *example* 대신 회사이름 같은 명칭을 사용할 수 있습니다.

7. **Input artifact Id**에서 **sample1**를 확인하고 엔터키를 누릅니다.
   
    ![Config-005](/assets/images/configjava-005.png)

8. 터미널에 프로젝트 생성과정이 표시됩니다. 버전 `1.0-SNAPSHOT`을 확인하고 엔터키를 누릅니다.
   
    ![Config-006](/assets/images/configjava-006.png)

9. `y`를 입력하고 엔터키를 누릅니다.
   
    ![Config-007](/assets/images/configjava-007.png)

10. 프로젝트 생성을 완료하기 위하여 엔터키를 누릅니다.
   
    ![Config-008](/assets/images/configjava-008.png)

11. 프로젝트를 저장할 폴더를 선택합니다. 위의 설정대로 따라할 경우, 선택된 폴더 아래에 **sample1** 프로젝트를 생성합니다.

12. 앞에서 생성한 **sample1** 프로젝트 폴더를 엽니다.
   
    ![Config-009](/assets/images/configjava-009.png)

프로젝트 특성에 따르는 설정을 제외하고 Salesforce의 REST API를 사용하기 위해서 몇가지 패키지를 사용하기 위하여 등록을 하여야 합니다.

1. **Command Palette**에서 "**Maven**: Add Dependency"를 선택합니다.
   
    ![Config-010](/assets/images/configjava-010.png)

2. 사용하려는 패키지 이름을 입력합니다. 여기서는 **httpcore**를 입력하고 엔터키를 누릅니다.
   
    ![Config-011](/assets/images/configjava-011.png)

3. **httpcore**를 포함하는 여러 패키지가 리스트됩니다. **org.apache.httpcomponents** 패키지를 선택합니다.
   
    ![Config-012](/assets/images/configjava-012.png)

4. 1 ~ 3의 순서를 반복하여 **httpclient**(org.apache.httpcomonents), **gson**(com.google.code.gson) 패키지를 추가합니다.

5. **pom.xml** 파일을 열고 아래 그림과 같이 **dependencies**에 정상적으로 추가되었는지를 확인합니다.
   
    ![Config-013](/assets/images/configjava-013.png)

6. **pom.xml** 파일을 저장을 하면 아래의 알림창이 표시되는데 *yes*를 클릭하여 필요한 라이브러리 설치를 진행을 합니다.
   
    ![Config-014](/assets/images/configjava-014.png)
   
   새로운 패키지를 추가할 떄마다 **pox.xml** 파일에 목록이 추가되고 저장을하면 라이브러리를 물리적으로 추가를 합니다.

Salesforce의 REST API를 사용하기 위한 기본적인 Maven 프로젝트 설정은 끝났습니다. 이제 코드 개발을 진행하면 됩니다.

## Gradle을 사용할 때의 구성

빌드 도구로 Gradle을 사용하는 Java 프로젝트를 생성을 합니다. 여기서는 Salesforce의 REST API 사용을 확인하는 예제로 간단하게 구성합니다.

1. **Visual Studio Code**를 실행하고 키보드에서 `Ctrl+Shift+P`를 누릅니다.

2. **Command Palette**에서 "**Java**: Create Java Project..."를 선택합니다.

3. **Gradle**을 선택합니다.
   
    ![Select Gradle](/assets/images/configjava-001.png)

4. 프로젝트를 저장할 폴더를 지정합니다.

5. **Create Gradle project: Select script DSL (1/2)**에서 **Groovy**를 선택을 합니다.

    ![Select build script DSL](/assets/images/configjava-016.png)

6. 프로젝트 이름을 입력하고 엔터키를 누릅니다.

    ![Select build script DSL](/assets/images/configjava-017.png)

    프로젝트를 생성합니다.

    ![Select build script DSL](/assets/images/configjava-018.png)

7. 프로젝트 생성이 완료되면 **Open**을 클릭하여 프로젝트 폴더를 엽니다.

    ![Select build script DSL](/assets/images/configjava-019.png)

    프로젝트 폴더를 열면 Gradle이 프로젝트 구성을 시작합니다. 완료될 때까지 기다립니다.

프로젝트 특성에 따르는 설정을 제외하고 Salesforce의 REST API를 사용하기 위해서 몇가지 패키지를 사용하기 위하여 등록을 하여야 합니다.

1. 프로젝트 화면의 왼쪽 **JAVA PROJECTS** 탭을 열고, 하단의 **build.gradle** 파일을 엽니다.

    ![Open build.gradle](/assets/images/configjava-020.png)

2. **<dependencies>** 섹션의 아랫 부분에 **implementation ''** 입력하고 따옴표 안에 `org.apache.httpcomponents:`을 입력합니다.

3. 표시되는 목록에서 `httpcore`를 선택합니다.

    ![Add Dependency](/assets/images/configjava-021.png)

4. 표시되는 버전 목록에서 `4.4.16`을 선택합니다.

    ![Add Dependency](/assets/images/configjava-022.png)

5. 2 ~ 4 절차를 반복하여 `org.apache.httpcomponents:httpclient:4.5.14`, `com.google.code.gson:gson:2.10.1`을 추가합니다.

6. 추가된 dependencies는 다음과 같습니다.

    ![build.gradle](/assets/images/configjava-023.png)

7. 수정된 **build.gradle** 파일을 저장합니다. **Yes** 클릭하여 추가 작업을 시작합니다.

    ![build.gradle](/assets/images/configjava-024.png)

8. **Referenced Libraries**에 라이브러리가 추가된 것을 확인합니다.

    ![Referenced Libraries](/assets/images/configjava-025.png)

Salesforce의 REST API를 사용하기 위한 기본적인 Gradle 프로젝트 설정은 끝났습니다. 이제 코드 개발을 진행하면 됩니다.
