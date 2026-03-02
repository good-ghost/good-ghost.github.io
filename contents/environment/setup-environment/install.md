---
title: "기본 환경 구성"
sort: 1
---

# 기본 환경 구성

## 필요한 프로그램

1. OpenJDK

    OpenJDK는 Visual Studio Code의 Salesforce 문법 검사 및 Salesforce Data Loader에서 사용을 하며, Version 11 이상을 설치하여야 합니다. OpenJDK는여러가지 배포판이 있지만 여기서는 Salesforce Data Loader에서 기본적으로 설정 값을 가지고 있는 Azul의 OpenJDK 배포판을 설치하도록 합니다. 이글을 쓰는 현재 21 LTS 버전이 가장 최신 버전이지만, Salesforce Extension이 17 LTS 까지만을 지원합니다.

    - Windows - [Azul Zulu 17](https://www.azul.com/downloads/?version=java-17-lts&os=windows&architecture=x86-64-bit#zulu)

    - Linux - Azul Zulu 17 [Debian](https://www.azul.com/downloads/?version=java-17-lts&os=debian&architecture=x86-64-bit#zulu), [Ubuntu](https://www.azul.com/downloads/?version=java-17-lts&os=ubuntu&architecture=x86-64-bit#zulu), [RHEL](https://www.azul.com/downloads/?version=java-17-lts&os=rhel&architecture=x86-64-bit#zulu), [CentOS](https://www.azul.com/downloads/?version=java-17-lts&os=centos&architecture=x86-64-bit#zulu), 기타 배포판은 [이곳](https://www.azul.com/downloads/?version=java-17-lts&architecture=x86-64-bit#zulu) 참조

    - Mac OS X - [Azul Zulu 17](https://www.azul.com/downloads/?version=java-17-lts&os=macos#zulu)

2. Microsoft Visual Studio Code

    - [Download](https://code.visualstudio.com/Download)

    - Visual Studio Code Salesforce Extensions

3. Salesforce DX Cli

    - [Download](https://developer.salesforce.com/tools/salesforcecli)

4. Git

    - Windows 버전 [Download](https://gitforwindows.org/)

5. PMD 소스 코드 분석 도구

    - [Download](https://pmd.github.io/#downloads)

    - 개발 진행중인 버전이 아닌 안정화 버전을 다운로드합니다.
      2024-01-18 현재 안정화 버전은 6.55입니다.

## 프로그램 설치

###	OpenJDK 설치

1. 설치

    Windows 버전의 경우, `C:\Program Files`에 설치되지 않도록 합니다. 경로의 중간에 공백이 있으면 Visual Studio Code에서 정상 동작하지 않을 수 있습니다.

    이 문서의 설명에서는 `C:\Utils\ZuluJDK\17`에 설치하는 것으로 합니다.

### Visual Studio Code 설치

1. 설치

    편의상 `C:\Utils\Code`에 설치를 합니다.

2. Visual Studio Code Salesforce Extensions 설치

    Visual Studio Code에 Salesforce 개발을 위한 Extension을 설치를 합니다.
 
    - 검색 칸에 `Salesforce`를 입력을 하면 하단에 관련된 목록이 표시가 됩니다.

    - 표시된 목록에서 **Salesforce Extensions Pack (Expanded)**의 **Install** 버튼을 클릭하여 설치를 합니다.

      ![VS Code Extensions](/assets/images/vscode-setup-001.png)
      
      모두 14개의 관련 Extension이 설치가 되므로 약간의 시간이 소요가 됩니다.

      Visual Studio Code 사용시 **Salesforce Extensions Pack (Expanded)**을 설치하였음에도 **Salesforce Extensions Pack**을 설치하라고 하는 **Recommended** 메시지가 표시될 수 있습니다. 설치를 하지 않으면 계속 메시지가 표시되므로 설치를 하도록 합니다.
      
      ![VS Code Extensions](/assets/images/vscode-setup-002.png)

    - 추가적으로 효율적인 코드 관리 및 편의성을 위하여 아래의 Extension을 설치하도록 합니다.

      ![VS Code Extensions](/assets/images/vscode-setup-003.png)

      ![VS Code Extensions](/assets/images/vscode-setup-004.png)

3. 기본 설정 변경 및 Extension 설정은 모든 도구를 설치한 후에 진행하도록 합니다.

### Salesforce DX Cli 설치

1. 설치

    편의상 `C:\Utils\SFDX`에 설치하는 것으로 합니다.

2. 업데이트

    Salesforce DX Cli는 빈번하게 업데이트가 이루어지고 있습니다. 최소한 한달에 한번은 업데이트를 확인하시기 바랍니다.

    Visual Studio Code의 터미널 창에서 아래의 명령을 실행을 합니다.

    ```
    > sf update
    ```

### Git Windows 버전 설치

1. 설치

    편의상 `C:\Utils\Git`에 설치하는 것으로 합니다.


### PMD 소스 코드 분석 도구 설치

1. 설치

    편의상 `C:\Utils\PMD`에 설치하는 것으로 합니다. `C:\Utils` 폴더에 압축을 풀어 놓습니다.

    폴더 이름을 `PMD`로 변경하고 시스템 환경 변수 PATH에 `C:\Utils\PMD\bin`을 추가합니다.

## 환경 구성 및 설정값 변경

### 환경 변수 설정

각 OS에 맞는 방법으로 아래의 환경 변수를 설정합니다. 아래의 예는 Windows를 예로 설명을 합니다.

- JDK

    아래의 내용을 시스템 환경 변수에 추가합니다.

    ```dos
    JAVA_HOME=C:\Utils\ZuluJDK\17
    ZULU_HOME=C:\Utils\ZuluJDK\17
    ```

    `ZULU_HOME` 환경 변수는 Salesforce Data Loader의 버전에 따라 요구하는 경우가 있습니다.
    사용하는 Data Loader를 확인하여 사용하지 않을 수도 있습니다.

    `PATH` 환경 변수에 `C:\Utils\ZuluJDK\17\bin`이 추가되어있는지 확인하고 없는 경우 직접 추가합니다.

    Visual Studio Code를 재시작 한 후에 터미널에서 `pmd --version` 명령으로 정상적으로 설치되었는지 확인 합니다.

- Git

    `PATH` 환경 변수에 `C:\Utils\Git\bin`이 추가되어있는지 확인하고 없는 경우 직접 추가합니다.

    Visual Studio Code를 재시작 한 후에 터미널에서 `git --version` 명령으로 정상적으로 설치되었는지 확인 합니다.

- PMD

    `PATH` 환경 변수에 `C:\Utils\PMD\bin`이 추가되어있는지 확인하고 없는 경우 직접 추가합니다.

    Visual Studio Code를 재시작 한 후에 터미널에서 `pmd --version` 명령으로 정상적으로 설치되었는지 확인 합니다.

### 설정값 변경

설치된 도구들이 Visual Studio Code에서 정상적으로 동작할 수 있도록 설정을 변경을 합니다.

설정을 변경하기 위하여 Visual Studio Code의 **Settings** 창을 엽니다.

![Extension Config](/assets/images/vscode-setup-005.png)

1. Git 설정

    ![Extension Config](/assets/images/vscode-setup-006.png)

    **Edit in settings.json**을 클릭하여 `settings.json` 파일을 열고 아래의 내용을 입력합니다.

    ``` json
    "git.path": "C:\\WUTIL\\Git\\cmd\\git.exe",
    ```

2. Apex PMD 설정

    - 소스 분석을 위한 PMD 설치 경로를 입력합니다.

    ![Extension Config](/assets/images/vscode-setup-007.png)

    - 소스 분석을 위한 룰셋 파일을 지정을 합니다.

    ![Extension Config](/assets/images/vscode-setup-008.png)

    `Defualt-PMD-Rules.xml` 파일은 [[여기]](/assets/Defualt-PMD-Rules.xml){:target="_blank"} 링크를 `C:\Utils` 폴더에 저장을 합니다.

3. Apex 설정

    Apex 언어 지원을 위하여 JDK Home 폴더를 지정합니다.

    ![Extension Config](/assets/images/vscode-setup-009.png)

4. Core 설정

    - 공동 작업일 경우 수정된 코드가 다른 개발자에 의해 덮여 쓰여질 수 있습니다.
    Deploy 전에 Salesforce에 저장된 소스코드가 본인이 내려받은 상태가 아니라 다른 개발자에 의해 업데이트 되었을 경우 경고를 표시하도록 **Detect Conflicts At Sync**를 체크합니다.

        ![Extension Config](/assets/images/vscode-setup-010.png)

    - 테스트 코드를 실행 한 후에 그 결과에 대한 코드 커버리지를 확인할 수 있습니다.

        ![Extension Config](/assets/images/vscode-setup-011.png)

        1. 테스트 클래스 위에 표시되는 **Run All Tests**를 클릭합니다.

            ![Extension Config](/assets/images/vscode-setup-012.png)

        2. 잠시 후 테스트 코드 실행이 종료되면 테스트 대상이되는 Class 파일을 열고 Visual Studio Code 화면 하단의 `☰` 아이콘을 클릭합니다.

            ![Extension Config](/assets/images/vscode-setup-013.png)

        3. 아래 그림처럼 테스트 커버리지를 확인 할 수 있습니다. 녹색으로 표시되는 부분은 테스트 코드가 수행된 부분이고, 빨간색으로 표시되는 부분은 테스트 코드가 수행되지 못한 부분입니다.

            ![Extension Config](/assets/images/vscode-setup-014.png)

5. Documenter 설정

    Salesforce의 Apex 쿨래스, Javascript 코드에 JavaDoc 형식의 주석문을 삽입하고 싶다면 Documenter를 사용하면 됩니다.

    - 아래 그림에 보이는 모든 체크박스를 체크합니다.

    - 주석문에 사용할 날짜 형식을 입력을 합니다. `YYYY-MM-DD`

        ![Extension Config](/assets/images/vscode-setup-015.png)

    - 파일에 추가되는 주석문에 사용될 형식을 살장히기 위하여  `settiings.json` 파일을 열고 아래의 내용을 추가합니다.
  
        ![Extension Config](/assets/images/vscode-setup-016.png)

        ``` json
        "SFDoc.FileHeaderProperties": [
            {
                "name": "description"
            },
            {
                "name": "author",
                "defaultValue": "$username"
            },
            {
                "name": "group"
            },
            {
                "name": "last modified on",
                "defaultValue": "$date"
            },
            {
                "name": "last modified by",
                "defaultValue": "$username"
            }
        ],
        ```

    - `Username` 항목에 개발자의 이메일 주소를 입력을 합니다.

        ![Extension Config](/assets/images/vscode-setup-017.png)


설정이 완료된 `settings.json` 파일의 내용은 아래와 유사할 것입니다. 

``` json
{
    "apexPMD.rulesets": [
        "C:\\Utils\\Defualt-PMD-Rules.xml"
    ],
    "apexPMD.pmdBinPath": "C:\\Utils\\PMD",
    "git.path": "C:\\Utils\\Git\\cmd\\git.exe",
    "redhat.telemetry.enabled": true,
    "salesforcedx-vscode-apex.java.home": "C:\\Utils\\ZuluJDK\\17",
    "salesforcedx-vscode-core.detectConflictsAtSync": true,
    "salesforcedx-vscode-core.clearOutputTab": true,
    "salesforcedx-vscode-core.retrieve-test-code-coverage": true,
    "search.exclude": {
        "**/node_modules": true,
        "**/bower_components": true,
        "**/.sfdx": true
    },
    "SFDoc.DateFormat": "YYYY-MM-DD",
    "SFDoc.EnableForLightningJavascript": true,
    "SFDoc.FileHeaderProperties": [
        {
            "name": "description"
        },
        {
            "name": "author",
            "defaultValue": "$username"
        },
        {
            "name": "group"
        },
        {
            "name": "last modified on",
            "defaultValue": "$date"
        },
        {
            "name": "last modified by",
            "defaultValue": "$username"
        }
    ],
    "SFDoc.username": "developer@example.com",
    "workbench.colorTheme": "Default Dark Modern",
}
```

````note
추가적으로 License 문구등을 소스코드에 추가하려면 *psioniq File Header* 애드온을 사용할 수있습니다.

다음의 설정을 `settings.json` 파일에 추가합니다. `templates`의 `License Description` 부분에 삽입할 문구를 입력하여 저장하면 됩니다.

``` json
    "psi-header.config": {
        "forceToTop": true,
        "blankLinesAfter": 1,
        "authorEmail": "developer@example.com",
        "license": "Custom"
    },
    "psi-header.changes-tracking": {
        "isActive": true,
        "enforceHeader": true,
        "exclude": ["markdown", "md", "json", "jsonc", "shellscript", "xml", "css"],
        "autoHeader": "autoSave"
    },
    "psi-header.templates": [
        {
            "language": "javascript",
            "template": [
                "License Description 1",
                " ",
                "License Description 2",
            ]    
        },
        {
            "language": "apex",
            "template": [
                "License Description 1",
                " ",
                "License Description 2",
            ]    
        }
    ],
    "psi-header.lang-config": [
        {
            "language": "javascript",
            "begin": "/**",
            "prefix": " * ",
            "end": " */"
        },
        {
            "language": "apex",
            "begin": "/**",
            "prefix": " * ",
            "end": "**/"
        }
    ],
```
````

