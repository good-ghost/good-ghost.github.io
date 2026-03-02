---
title: "초기 환경 설정"
sort: 1
---

# 초기 환경 설정

## 프로젝트 생성

Salesforce 개발을 위한 프로젝트를 생성합니다.

1. Visual Studio Code에서 **View** > **Command Palette…**를 선택하거나, `Ctrl+Shift+P` 단축키를 누릅니다.

    ![initial environment](/assets/images/salesforce-project-001.png)

2. 입력창에 `SFDX`를 입력을 하면, 아래 그림과 같이 3개의 메뉴항목이 표시되는데, **SFDX: Create Project with Manifest**를 선택합니다.

    ![initial environment](/assets/images/salesforce-project-002.png)

3. 아래처럼 표시되는 메뉴에서 **Standard**를 선택합니다.

    ![initial environment](/assets/images/salesforce-project-003.png)

4. Project의 이름(예: `Sample`)을 입력하고 엔터키를 누릅니다

    ![initial environment](/assets/images/salesforce-project-004.png)

5. 프로젝트를 저장할 폴더의 위치를 지정하는 모달이 표시됩니다. 원하는 폴더를 지정을 하면 해당 폴더에 프로젝트 이름으로 서브 폴더가 만들어지고 프로젝트 기본 파일들이 저장이 됩니다.

    ![initial environment](/assets/images/salesforce-project-005.png)

6. Visual Studio Code에 아래 그림처럼 프로젝트가 생성이 됩니다.

    ![initial environment](/assets/images/salesforce-project-006.png)

## Salesforce에 개발 환경 연결

프로젝트와 대상이 되는 Salesforce의 ORG를 연결하여야 합니다.

1. **Command Palette**에서 입력창에 `SFDX`를 입력을 하고 표시되는 목록에서 **SFDX: Authorize an Org**를 선택합니다

    ![initial environment](/assets/images/salesforce-project-007.png)

2. Developer Edition의 경우, 아래 그림에서 **Production**을 선택을 합니다. Sandbox 환경인 경우 **Sandbox**를 선택하면 됩니다.

    ![initial environment](/assets/images/salesforce-project-008.png)

3. 아래 그림의 입력란에 로그인하려는 Org의 명칭(Alias)를 입력을 하고 엔터키를 누릅니다.

    ![initial environment](/assets/images/salesforce-project-009.png)

4. Visual Studio Code의 화면 오른쪽 아래에 아래 그림과 같이 표시되면서 Salesforce에 로그인하는 화면이 웹브라우저에 표시됩니다.

    ![initial environment](/assets/images/salesforce-project-010.png)

5. 웹브라우저에서 Developer Edition 또는 Sandbox에 로그인을 합니다.

    ![initial environment](/assets/images/salesforce-project-011.png)

6. 웹브라우저에서 정상적으로 로그인이 되면 아래와 같은 화면이 표시됩니다. 브라우저 창을 닫습니다.

    ![initial environment](/assets/images/salesforce-project-012.png)

7. 로그인이 완료되면 Visual Studio Code 화면 오른쪽 아래에 아래 그림처럼 표시가 됩니다.

    ![initial environment](/assets/images/salesforce-project-013.png)

## 기본 메타데이터 다운로드

개발을 진행하려면 ORG의 기존 소스코드, 메타데이터를 다운로드하여야 합니다.

1. 다운로드할 수 있는 메타데이터는 기본 프로젝트의 설정값을 확인합니다.
    `/manifest/package.xml` 파일에 명시된 항목을 다운로드 할 수 있습니다.

    ![initial environment](/assets/images/salesforce-project-014.png)

2. `package.xml`에 포함되지 않은 메타데이터를 다운로드하려면 Visual Studio Code의 왼쪽 구름 아이콘을 클릭하여 
    **ORG BROWSER**을 열고 원하는 메타 데이터를 다운로드 할 수 있습니다.

    ![initial environment](/assets/images/salesforce-project-015.png)

3. 예를 들어 **Custom Objects** 오른쪽의 ![icon](/assets/images/salesforce-project-020.png) 아이콘을 클릭하여
    커스톰 오브젝트에 대한 메타데이터를 다운로드할 수 있습니다.

    ![initial environment](/assets/images/salesforce-project-016.png)

4. `package.xml`에 명시된 메타데이터를 일괄적으로 다운로드 하려면 `force-app\main\default` 폴더에서 
    마우스 오른쪽 클릭을 하고 표시된 메뉴에서 **SFDX: Retrieve Source from Org**를 클릭하여 다운로드 할 수 있습니다.

    ![initial environment](/assets/images/salesforce-project-017.png)

    ````warning

    4번의 방법으로 다운로드를 시도하면 프로젝트 폴더에 없는 새로운 메타데이터를 다운로드 하는것이 아니라 기존에 다운로드된
    메타데이터를 업데이트합니다. 따라서 프로젝트를 생성하고 4번의 방법을 사용하면 아래 그림과 같이 아무런 메타데이터를
    다운로드 하지 못했다는 메시지를 보게 됩니다.

    ![initial environment](/assets/images/salesforce-project-018.png)

    따라서 `package.xml`의 모든 메타데이터를 다운로드 하려면 아래 그림 처럼 터미널 창에서 명령을 입력을 하여야 합니다.

    ![initial environment](/assets/images/salesforce-project-019.png)

    ``` powershell
    sf project retrieve start -x .\manifest\package.xml
    ```

    위의 명령은 Salesforce DX Cli v2부터 사용할 수 있는 명령 옵션입니다.

    기존 v1의 명령 방식도 사용할 수 있습니다.

    ``` powershell
    sfdx force:source:retrieve -x .\manifest\package.xml
    ```
    ````

````note
`package.xml` 파일의 메타데이터 설정을 변경하려면 다음과 같은 방법을 사용할 수 있습니다.

1. **Salesforce Package.xml Generator Extension for VS Code** Extension을 설치합니다.

    ![extension](/assets/images/salesforce-project-021.png)

2. **Command Palette...**에서 **SFDX: Package.xml Generator: Choose Metadata Components**를 선택합니다.

    ![extension](/assets/images/salesforce-project-022.png)

    메타데이터 목록 데이터를 모두 다운로드할때 까지 대기합니다.

    ![extension](/assets/images/salesforce-project-023.png)

3. `package.xml`에 포함시킬 메타데이터의 항목을 체크하고 **UPDATE PACKAGE.XML** 버튼을 클릭하여 `package.xml`
    파일을 업데이트 합니다.

    ![extension](/assets/images/salesforce-project-024.png)

4. 터미널 창에서 아래의 명령을 사용하여 메타데이터를 다운로드 합니다.

    ``` powershell
    sf project retrieve start -x .\manifest\package.xml
    ```
````

일반적으로 많이 사용되는 `package.xml` 항목은 아래와 같습니다.

``` xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<Package xmlns="http://soap.sforce.com/2006/04/metadata">
    <types>
        <members>*</members>
        <name>ApexClass</name>
    </types>
    <types>
        <members>*</members>
        <name>ApexComponent</name>
    </types>
    <types>
        <members>*</members>
        <name>ApexPage</name>
    </types>
    <types>
        <members>*</members>
        <name>ApexTrigger</name>
    </types>
    <types>
        <members>*</members>
        <name>AuraDefinitionBundle</name>
    </types>
    <types>
        <members>*</members>
        <name>CustomField</name>
    </types>
    <types>
        <members>*</members>
        <name>CustomLabels</name>
    </types>
    <types>
        <members>Account</members>
        <members>Case</members>
        <members>Contact</members>
        <members>Contract</members>
        <members>Event</members>
        <members>Lead</members>
        <members>Opportunity</members>
        <members>OpportunityLineItem</members>
        <members>Task</members>
        <members>*</members>
        <name>CustomObject</name>
    </types>
    <types>
        <members>*</members>
        <name>CustomObjectTranslation</name>
    </types>
    <types>
        <members>*</members>
        <name>EmailTemplate</name>
    </types>
    <types>
        <members>*</members>
        <name>FlowDefinition</name>
    </types>
    <types>
        <members>*</members>
        <name>LightningComponentBundle</name>
    </types>
    <types>
        <members>*</members>
        <name>StaticResource</name>
    </types>
    <types>
        <members>*</members>
        <name>Translations</name>
    </types>
    <types>
        <members>*</members>
        <name>ValidationRule</name>
    </types>
    <version>58.0</version>
</Package>
```
