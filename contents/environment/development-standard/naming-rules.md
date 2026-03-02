---
title: "명명 규칙"
sort: 2
---

# 명명 규칙

기본적인 명명 규칙은 일반적으로 *헝가리안 표기법*이 가장 널리 사용되는 프로그래밍 코드 패턴이지만, 복잡하기도 하고 최근의 언어들은 형규칙이 엄격하기도 하여 사용성이 많이 떨어집니다.

또, Salesforce에서는 사용되지 않는 형식이 많기 때문에 일반적인 패턴 정도만 참고하여 사용하도록 합니다.

단, 함수명, 변수명에는 **under_scores**, **CamelCase**(PascalCase) 명명 규칙을 적용하며, 구글의 Java(APEX용), JavaScript, HTML/CSS 스타일 가이드를 차용합니다.

**기본적인 규칙을 설명하자면**,

일반적으로 모든 명칭은 모두 소문자로 이루어지며 각각의 단어는 `_`로 연결합니다.

이와 같은 표기법을 **under_scores** 표기법이라고 하며, `this_is_method_name`과 같은 명칭이 됩니다.

이것을 **CamelCase** 표현법을 사용하면 `thisIsMethodName`이 됩니다. 이 표기법은 Lighting Component를 개발할 때도 적용이 됩니다.

**<font color="red">특히 LWC 개발시엔 반드시 지켜야하는 규칙이기도 합니다.</font>**

또 이렇게 명칭의 첫글자가 소문자로 시작하는 경우를 **lowerCamelCase**라고 지칭하며, `ThisIsMethodName`과 같이 첫글자가 대문자인 경우에는 **UpperCamelCase**라고 지칭을 합니다.

보통 **CamelCase**라고 한다면 **lowerCamelCase**를 의미합니다.

## SObject 및 Fields

개체와 필드의 Label은 원하는대로 명칭을 부여합니다. Developer Name(API Name)은 다음과 같은 기본 명명 규칙에 의해 부여하도록 합니다.

**SObject Developer Name**

1. SObject의 Developer Name은 Salesforce 시스템의 기본적인 명명 규칙인 **UpperCamelCase**를 사용하여 명칭을 부여합니다. 즉, **Opportunity Line Item**의 경우 `OpportunityLineItem`의 형식이 됩니다.

2. SObject가 외부의 데이터베이스(Oracle 등..)와 인터페이스용으로 사용되는 경우 동일한 명명 규칙을 사용할 수 있습니다. 예를 들어 외부 데이터베이스의 테이블 명칭이 `EXT_ACC_CODE`와 같을 때, SObject의 Developer Name을 동일하게 `EXT_ACC_CODE`라고 부여하면 됩니다.

**SObejct Field의 Developer Name**

1.	Salesforce 시스템의 기본적인 명명 규칙인 **UpperCamelCase**를 사용하여 명칭을 부여합니다.

    **Project Start Date**의 경우 `ProjectStartDate`라고 명칭을 부여합니다.

2.	SObject가 외부의 데이터베이스(Oracle 등..)와 인터페이스용으로 사용되는 경우 동일한 명명 규칙을 사용할 수 있습니다.

    외부 데이터베이스 테이블의 필드 명칭이 `PRJ_START_DATE`인 경우, 그대로 `PRJ_START_DATE`라고 명칭을 부여합니다.

기본적인 명칭은 **UpperCamelCase**를 사용하지만 외부 데이터베이스와 인터페이스하는 SObject인 경우에는 동일한 명칭을 부여하도록 합니다. 이를 가급적 지켜주어야 나중에 명칭만 보고서도 용도를 쉽게 이해할 수 있습니다.

또, 필드 명칭의 경우 서로 다른 개체에서 같은 필드 명칭을 사용하는 경우가 많은데, 프로그램 코드를 작성하다보면 같은 필드 명칭으로 인하여 혼선이 생길 수 있습니다. 이를 피하기 위하여 필드 명칭의 **prefix**로 개체를 지칭하는 이름을 붙여주는것이 좋습니다. 위의 예를 들면, 필드 Label은 **Project Start Date**이고 개체명이 **Service**라고 할 경우 필드 명칭은 `ServiceProjectStartDate`라고 해주는것이 좋습니다.

**Child Relationship Name**

필드의 형식이 Master/Detail 또는 Lookup 일 경우, **Child Relationship Name**(Developer Name)과 **Related List Label**(Label)을 설정을 하여야 합니다.

**Label**은 레코드 화면에서 Related List의 명칭으로 사용되며, **Developer Name**은 프로그램 작성시에 연관 객체 필드를 명시할 때 사용됩니다. 

이 명칭은 UpperCamelCase 표기법을 사용하여 설정을 하며, 기본적으로 복수형으로 설정을 하면 됩니다.

```note
예를 들어, 상위 개체의 Label이 **Project**, Developer Name이 `Project`이고, 하위 개체의 Label이 **Sub Task**, Developer Name이 `SubTask`인 경우, **Sub Task** 개체에는 상위 개체인 **Project**에 연결하는 Master/Detail 또는 Lookup 형식의 필드를 만들게 됩니다.

이때 일반적으로 하위 개체인 **Sub Task**에 생성되는 상위 개체와 연결하는 필드의 Label은 **Project** 또는 **Parent**, Developer Name은 `ProjectId` 또는 `ParentId`라는 이름을 사용하게 되는데 이때 Child Relationship Name은 Salesforce 시스템에서 자동으로 부여해주는 기본값으로 **Sub Task** 개체의 Label을 지정하여(한 단어 이상일 경우 `_`로 연결하여 표시됨) `Sub_Task`가 되며, Related List Label은 하위 개체의 Label과 필드의 Label을 결합하여 **Sub Task (Project)**라고 기본 설정이 됩니다.

이 경우 정확한 명칭은 Label과 Developer Name은 각각 **Sub Tasks**, `SubTasks`라고 변경해 주는것이 시인성이 좋습니다.
```

## APEX Class, Trigger

기본적으로 클래스와 트리거의 이름은 **UpperCamelCase**를 기준으로 작성을 합니다.

다른 세부 사항은 다음과 같습니다.

**클래스(Class), 트리거(Trigger) 상세 이름 규칙**

UpperCamelCase를 기준으로 만들어진 이름에 아래의 추가적인 규칙을 적용합니다.

1.	인터페이스 클래스 : **IF_** 라는 Prefix를 클래스의 이름 앞에 붙여줍니다.

    예) `IF_AccountSync`

2.	배치 클래스 :**Batch_** 라는 Prefix를 클래스의 이름 앞에 붙여줍니다.

    예) `Batch_OpportunityUpdate`

3.	스케쥴 클래스 : **Schedule_** 이라는 Prefix를 클래스의 이름 앞에 붙여줍니다.

    예) `Schedule_OpportunityUpdate`

4.	플로우 액션 클래스 : **Flow_** 라는 Prefix를 클래스의 이름 앞에 붙여줍니다.

    예) `Flow_CreateNewContacts`

5.	컨트롤러 클래스 : Visualforce, Lightning Component의 컨트롤러 클래스 이름은 **Controller**라는 Suffix를 붙여줍니다.

    예) `RoadAddressController`

6.	익스텐션 클래스 : Visualforce의 Extension 클래스의 이름은 **Extension**이라는 Suffix를 붙여줍니다.

    예) `AccountMultiEditExtension`

7.	테스트 클래스 : 위의 이름 규칙이 적용된 최종 클래스의 이름 뒤에 **_Test**라는 Suffix를 붙여줍니다.

    예) `CustomController_Test`

**함수(Methods) 및 변수(Variables)**

1.	함수 및 변수 이름은 **lowerCamelCase**를 기준으로 작성을 합니다.

2.	추가적으로 변수의 경우, 변수 타입을 직관적으로 알 수 있도록 Prefix를 붙여줄 수도 있습니다.

    - List 타입	=> `lstAccount`, `lOppty`

    - Map 타입	=> `mapAccount`, `mOppty`

    - String 타입	=> `strAddress`, `sName`

    - Integer 타입	=> `intQuantity` 또는 `nQuantity`

    - Decimal 타입	=> `fAmount`, `fWeight`

    - Boolean 타입	=> `bSwitch`, `bCondition`

    - Date 타입	=> `dToday`

    - DateTime 타입	=> `dtStartDateTime`

**상수(Constant)**

상수의 경우에는 전체를 대문자로 이름을 지정을 하며, 두개 이상의 단어는 `_`으로 연결하여 표현합니다. 

예: `PI`, `PROJECT_SERVER_URL`

## Lightning Aura Component

Aura 컴포넌트의 이름은 아래 두가지로 만들 수 있습니다.

1.	버튼, 레이아웃에서 직접 사용되는 주 컴포넌트

    버튼, 페이지 레이아웃, 앱 빌더등에서 직접 지정할 수 있는 컴포넌트의 이름은 **UpperCamelCase**를 사용하여 지정하도록 합니다.
    
    이와 같은 컴포넌트들은 반드시 `<component />`에 `implements` 속성을 지정을 하여야만 합니다.
    
    반대로 이하기하면 `implements` 속성이 반드시 필요한 컴포넌트들은 **UpperCamelCase**로 이름이 지정이 되어야만 합니다.

2.	다른 컴포넌트에서 호출되어 사용되는 서브 컴포넌트

    서브 컴포넌트는 **LWC** 컴포넌트로 작성을 하도록 합니다. 불가피하게 Aura 컴포넌트로 개발을 하게되는 경우 **lowerCamelCase**를 사용하여 이름을 지정을 합니다.
    
    이와 같은 컴포넌트들은 다른 컴포넌트의 프로그램 코드에서 명시적으로 호출을 하게되므로 `<component />`에 `implements` 속성을 지정할 필요가 없습니다.

    컴포넌트의 `–Controller.js`, `--Helper.js`에서는 클래스 정의 경우 **UpperCamelCase**를, 메소드 및 변수에서는 **lowerCamelCase**를 사용하여 이름을 지정하며 상수로 사용되는 경우 전체 대문자를 사용하여 지정을 합니다.

## Lightning Web Component

Lightning Web Component는 반드시 **lowerCamelCase** 명명 규칙을 사용하여야만 합니다.

커스톰 태그의 경우 정의는 코드개발시에는 **lowerCamelCase**로 작성을 하지만, 상위 Lightning Web Component에서 가져다 사용할 때에는 **Hyphen을 사용하는 under_score** 규칙을 사용합니다.

예를 들어, `thisIsChildComp` 라는 컴포넌트를 만들고 상위 컴포넌트에서 사용을 할 경우 `<c-this-is-child-comp ></c-this-is-child-comp>` 로 지정을 하여 사용을 하게 됩니다.

이와 같이 `<template />`에서는 **under_score(hyphen)** 규칙이 사용됩니다. 또한 커스톰 태그의 이름이 `thisIsChildComp`이라 할지라도 `thisIsChildComp.js` 파일에서 정의하는 태그를 정의하는 클래스의 이름은 `ThisIsChildComp`로 **UpperCamelCase**로 자동 지정이 됩니다.

이외의 일반적인 명명규칙은 Lightning Aura Component의 규칙을 사용하면 됩니다.

## Flow

Flow의 Developer Name(API Name)은 **UpperCamelCase**로 지정을 하며, 내부에서 사용하는 각종 변수 및 내부 기준의 이름은 **lowerCamelCase**의 규칙을 사용하여 지정을 합니다.

또한, Flow가 **Screen Flow**인 경우, 화면에 표시되는 레이블도 사용하게 되므로 다국어 적용을 위한 번역시 작업을 용이하게 하기 위하여 레이블의 이름에는 `screen`이라는 Prefix를 적용하는 것이 좋습니다.

## Custom Label

Custom Label은 다국어 적용를 위하여 사용됩니다. 새로 만드는 경우 반드시 영어 환경에서 만들어서 기준 언어가 영어가 되도록 하여야 나중에 혼란이 없습니다. 전체 대문자로 이름을 정하도록 하며, 아래의 규칙을 따르도록 합니다.

예를 들어, **Opportunity Clone**이라는 모듈에 대한 Custom Label을 작성합니다.

가장 먼저 작업 및 분류를 용이하게 하기 위하여 필수값은 아니지만 Categories의 값을 지정을 합니다. 모듈의 명칭이 Opportunity Clone이므로 Categories의 값은 `OPPORTUNITY_CLONE`으로 지정을 합니다.

**Short Description**과 **Name**은 최대 **80**자까지 사용할 수 있습니다. 편의를 위하여 Short Description과 Name은 동일하게 지정을 하겠습니다.

**Categories**의 명칭에 다음의 식별자를 추가합니다. 버튼의 Label인 경우 `_BTN`을, Picklist/Combobox의 목록 값인 경우 `_PICKVAL`을, 일반 필드 및 모달의 Label인 경우 `_LBL`(_LABEL)을, 메시지의 경우 `_MSG`를 추가합니다.

따라서, `OPPORTUNITY_CLONE_BTN`, `OPPORTUNITY_CLONE_PICKVAL`, `OPPORTUNITY_CLONE_LABEL`, `OPPORTUNITY_CLONE_MSG`와 같이 구성이 됩니다.

가장 뒤에 실제 명칭을 붙여줍니다. `Add` 버튼인 경우, `OPPORTUNITY_CLONE_BTN_ADD`가 되도록 합니다.

예를 들어, `OPPORTUNITY_CLONE_BTN_CLOSE`, `OPPORTUNITY_CLONE_PICKVAL_VALUE1`, `OPPORTUNITY_CLONE_LABEL_TITLE`, `OPPORTUNITY_CLONE_MSG_QUERY_EXCEPTION`과 같이 이름을 주면 됩니다.

## Static Resource

Static Resource의 이름은 프로그램 코드내에서 상수인 것처럼 사용이 됩니다. 따라서 프로그램의 상수명을 지정하는 것처럼 지정을 합니다.

전체 대문자로 구성하며 단어와 단어는 `_`로 연결을 하도록 합니다.

예를 들어 **jQuery UI 1.18**에 대한 경우 `JQUERY_UI_1_18`로 지정을 하는 게 좋습니다.
