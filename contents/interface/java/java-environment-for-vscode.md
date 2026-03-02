---
title: "VS Code를 위한 Java 개발환경 설정"
sort: 1
---

# Java 개발을 위한 Visual Studio Code 설정하기

Java 개발을 처음으로 입문하는 사용자를 위하여 설정 방법을 설명하도록 합니다. 이 문서에서는 Windows 버전을 기준으로 설명을 합니다.

## 준비 사항

### 필수 도구

 - [Visual Studio Code](https://code.visualstudio.com/download)
  
   사용 환경에 맞는 Visual Studio Code를 다운로드하여 설치합니다.

   다음의 Extension을 설치합니다.

    - **Extension Pack for Java**

    - **Gradle Extension Pack** (빌드 도구로 Gradle을 사용하는 경우)

 - JDK

   Java 17에 해당하는 JDK를 다운로드하여 설치합니다. 이 설명에서는 Azul의 [**Julu JDK**](https://www.azul.com/downloads/?version=java-17-lts&architecture=x86-64-bit&package=jdk#zulu)를 사용하도록 합니다. 최신 버전은 **21 LTS** 이지만 아직 지원하지 않는 경우(2024/01/09 현재)가 있어서 **17 LTS** 를 사용하도록 합니다.

   이 문서에서는 JDK가 `C:\Utils\ZuluJDK\17`에 설치가 되어있다고 가정합니다.

   환경 변수에 **`JAVA_HOME`** 및 **`ZULU_HOME`**을  `C:\Utils\ZuluJDK\17`로 설정하고 **PATH** 환경 변수에  `C:\Utils\ZuluJDK\17\bin`을 추가합니다.

   설치 후, 터미널 창에서 `java -version`를 실행하여 확인을 합니다.

   ``` bash
   openjdk version "17.0.9" 2023-10-17 LTS
   OpenJDK Runtime Environment Zulu17.46+19-CA (build 17.0.9+8-LTS)
   OpenJDK 64-Bit Server VM Zulu17.46+19-CA (build 17.0.9+8-LTS, mixed mode, sharing)
   ```

### Maven 사용을 위한 도구
Java 빌드 도구로 Maven을 사용하기를 원한다면 Maven 도구를 설치하여야 합니다.

 - [Apache Maven](https://maven.apache.org/download.cgi)
  
   이 문서에서는 3.9.6 버전을 다운로드하여 사용합니다. 따라서 Maven이 `C:\Utils\Apache\apache-maven-3.9.6`에 설치가 되어있다고 가정합니다. 최근의 버전들은 **`M2_HOME`** 환경 변수를 사용하지 않습니다.

   **PATH** 환경 변수에 `C:\Utils\Apache\apache-maven-3.9.6\bin`을 추가합니다.

   설치 후, 터미널에서 `mvn -version`을 실행하여 확인합니다.

   ``` bash
   Apache Maven 3.9.6 (bc0240f3c744dd6b6ec2920b3cd08dcc295161ae)
   Maven home: C:\WUTIL\Apache\apache-maven-3.9.6
   Java version: 17.0.9, vendor: Azul Systems, Inc., runtime: C:\WUTIL\ZuluJDK\17
   Default locale: ko_KR, platform encoding: MS949
   OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
   ```

### Gradle 사용을 위한 도구

Java 빌드 도구로 Gradle을 사용하기를 원한다면 Apache Ant와 Gradle 도구를 설치하여야 합니다. Aapche Ant는 반드시 설치할 필요는 없습니다.

 - [Apache Ant](https://ant.apache.org/bindownload.cgi)
  
   이 문서에서는 1.10.14 버전을 다운로드하여 사용합니다. 따라서 Ant가 `C:\Utils\Apache\apache-ant-1.10.14`에 설치가 되어있다고 가정합니다. **`ANT_HOME`** 환경 변수에 `C:\Utils\Apache\apache-ant-1.10.14`를 설정합니다.

   **PATH** 환경 변수에 `C:\Utils\Apache\apache-ant-1.10.14\bin`을 추가합니다.

   설치 후, 터미널에서 `ant -version`을 실행하여 확인합니다.

   ``` bash
   Apache Ant(TM) version 1.10.14 compiled on August 16 2023
   ```

 - [Gradle](https://gradle.org/releases/)

   이 문서에서는 버전 [8.5 binary-only](https://gradle.org/next-steps/?version=8.3&format=bin)를 `C:\Utils\Gradle`에 설치를 하였다고 가정합니다.

   **PATH** 환경 변수에 `C:\Utils\Gradle\bin`을 추가합니다.

   설치 후, `gradle -version`을 실행하여 확인합니다.

   ``` bash
   ------------------------------------------------------------
   Gradle 8.5

   Build time:   2023-11-29 14:08:57 UTC
   Revision:     28aca86a7180baa17117e0e5ba01d8ea9feca598

   Kotlin:       1.9.20
   Groovy:       3.0.17
   Ant:          Apache Ant(TM) version 1.10.13 compiled on January 4 2023
   JVM:          17.0.9 (Azul Systems, Inc. 17.0.9+8-LTS)
   OS:           Windows 10 10.0 amd64
   ```
  
## Visual Studio Code 추가 설정

**settings.json** 파일에 아래의 내용을 추가합니다.

``` bash
    "java.jdt.ls.java.home": "C:\\Utils\\ZuluJDK\\17",
```

만약 필요에 따라서 여러개의 JDK 버전 설치가 필요하다면 아래의 내용을 참조하여 **settings.json** 파일에 버전 정보를 추가하도록 합니다.

``` json
    "java.configuration.runtimes": [
        {
            "name": "JavaSE-17",
            "path": "C:\\Utils\\ZuluJDK\\17",
            "default": true // No build tools projects에서 사용

        },
        {
            "name": "JavaSE-21",
            "path": "C:\\Utils\\ZuluJDK\\21"
        }
    ],
```

### Maven 사용을 위한 설정 추가

GUI 환경에서 컨텍스트 메뉴를 통해 Maven을 빌드 도구로 사용하려면 아래의 내용을 **settings.json** 파일에 추가합니다.

``` json
    "maven.terminal.customEnv": [
        {
            "environmentVariable": "JAVA_HOME",
            "value": "C:\\Utils\\ZuluJDK\\17"
        }
    ],
```

### Gradle 사용을 위한 설정 추가

Gradle 빌드 도구를 사용하기 위하여 아래의 내용을 **settings.json** 파일에 추가합니다.

``` json
    "java.import.gradle.java.home": "C:\\Utils\\ZuluJDK\\17",
```

### 프로젝트만을 위한 Java 환경 설정

위에서 설명하는 모든 설정은 모든 경우에 적용되는 시스템 기본 설정값입니다. 만약 특정 프로젝트에서 다른 JDK를 사용하여야 한다면 하나의 프로젝트만을 위한 설정을 하여야 합니다.

1. 프로젝트 폴더에 **`.vscode`** 폴더를 생성합니다.

2. **`.vscode`** 폴더에 **settings.json** 파일을 새로 만듭니다.

3. 만들어진 **settings.json** 파일에 아래의 내용을 입력하고 저장을 합니다.

   ``` json
   "java.configuration.updateBuildConfiguration": "interactive",
   "java.jdt.ls.java.home": "C:\\Utils\\ZuluJDK\\17",
   "java.import.gradle.java.home": "C:\\Utils\\ZuluJDK\\17",
    "maven.terminal.customEnv": [
        {
            "environmentVariable": "JAVA_HOME",
            "value": "C:\\Utils\\ZuluJDK\\17"
        }
    ],
   ```

만약 Java 빌드시 Runtime이 맞지 않아 다음의 그림과 같은 경고가 발생을 한다면 아래의 절차를 따라 대처할 수 있습니다.

![BuildPath](/assets/images/javasetup-002.png)

1. 프로젝트 폴더에 **`.settings`** 폴더를 생성합니다.

2. **`.settings`** 폴더에 `org.eclipse.jdt.core.prefs`라는 이름의 텍스트 파일을 만듭니다.

3. `org.eclipse.core.resources.prefs` 파일에 아래이 내용을 입력하고 저장을 합니다.

   ```
   eclipse.preferences.version=1
   org.eclipse.jdt.core.compiler.codegen.inlineJsrBytecode=enabled
   org.eclipse.jdt.core.compiler.codegen.targetPlatform=17
   org.eclipse.jdt.core.compiler.compliance=17
   org.eclipse.jdt.core.compiler.problem.assertIdentifier=error
   org.eclipse.jdt.core.compiler.problem.enablePreviewFeatures=disabled
   org.eclipse.jdt.core.compiler.problem.enumIdentifier=error
   org.eclipse.jdt.core.compiler.source=17
   ```

   **17**은 사용하려는 JDK의 버전을 입력해주면 됩니다.

4. **.vscode** 폴더의 **settings.json** 파일에 아래의 내용을 추가합니다.

   ``` json
   "java.settings.url": ".settings/org.eclipse.jdt.core.prefs",
   ```

아래의 설정은 옵션입니다. 

5. **`.settings`** 폴더에 `org.eclipse.core.resources.prefs`라는 이름의 텍스트 파일을 만듭니다.

6. `org.eclipse.core.resources.prefs` 파일에 아래의 내용을 입력하고 저장을 합니다.

   ```
   eclipse.preferences.version=1
   encoding//src/main/resources=UTF-8
   encoding//src/test/java=UTF-8
   encoding/<project>=UTF-8
   encoding/src=UTF-8
   ```

## Java 실행 환경 확인하기

Java 개발 환경이 정상적으로 설정되었는지를 확인하기 위하여 간단한 Maven 프로젝트를 생성하고 빌드를 진행해봅니다.

1. **Visual Studio Code**를 실행하고 키보드에서 `Ctrl+Shift+P`를 누릅니다.

2. **Command Palette**에서 "**Java**: Create Java Project..."를 선택합니다.

3. **Maven**을 선택합니다.

4. **Create Maven Project**에서 **maven-archetype-quickstart**를 선택합니다.

5. **1.4**를 선택을 합니다.

6. **Input group Id of your project**에서 **com.example**을 확인하고 엔터키를 누릅니다.

7. **Input artifact Id**에서 **demo**를 확인하고 엔터키를 누릅니다.

8. 터미널에 프로젝트 생성과정이 표시됩니다. 버전 `1.0-SNAPSHOT`을 확인하고 `y`를 입력하고 엔터키를 누릅니다.

9. 프로젝트를 저장할 폴더를 선택합니다. 위의 설정대로 따라할 경우, 선택된 폴더 아래에 **demo** 프로젝트를 생성합니다.

10. 앞에서 생성한 **demo** 프로젝트 폴더를 엽니다.

11. App.java 파일을 열고 **main** 메소드 위에 표시된 **Run**을 클릭하여 터미널에 "Hello World"가 표시되는것을 확인합니다.

    ![Java Run](/assets/images/javasetup-001.png)