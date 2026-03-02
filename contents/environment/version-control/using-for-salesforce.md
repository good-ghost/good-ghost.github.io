---
title: "Salesforce 개발을 위하여 Github를 사용하는 방법"
sort: 3
---

# Salesforce 개발을 위하여 Github를 사용하는 방법

Admin 또는 Release Manager가 Master Repository를 구성하고 Branch Merge 또는 Pull Request에 대한 처리를 담당합니다. 팀원(개발자)는 Master Repository를 기반으로 Branch를 만들어 개발 작업을 수행 후 Branch에서 Commit을 진행하도록 합니다. Admin, Release Manager가 아닌 개발자의 경우 아래 **ORG를 백업하는 Master Repository의 구성 및 백업**을 건너 뛰고 **개발용 Master Repository를 구성하는 방법**을 보시면 됩니다. 

## ORG를 백업하는 Master Repository의 구성 및 백업

Admin 또는 Release Manager에 의해서 수행합니다. Master Repository를 구성하는 방법은 두가지가 있습니다.

- 첫번째는 Org의 Backup Master를 기반으로 Repository를 구성하는 방식이고, 

- 다른 하나는 개발 중심의 Master Repository를 구성하는 방식입니다.

Production/QA 환경은 Backup Master를 기반으로 하고, DEV 환경은 개발 중심 Master로 하는 것이 관리상 용이하다고 봅니다. 여기에서 설명하는 방법이 정답은 아닙니다. 다만 혼선을 피하고, 코드관리의 일관성을 유지하기 위하여 사용하는 방법입니다.

또, 아래의 3) 항목에서 만들어지는 package.xml 파일을 이용하여 Salesforce Migration Toolkit으로 ORG의 메타데이터를 다운로드(retrieve) 할 경우 Visual Studio Code를 사용하여 다운로드 하는 것과는 파일 구조가 서로 다른 부분이 존재합니다. 이 부분을 같게 처리하기 위하여 모든 작업을 아래에 설명하는 것처럼 진행을 합니다.

### 이미 존재하는 Master Repository로 Backup Master를 구성하고 백업하는 방법

1. Repository를 컴퓨터에 Clone을 합니다.

    여기서는 `https://github.com/myaccount/Project` 라고 가정합니다.

    `D:\Public\Backup` 폴더에 Clone을 하도록 하겠습니다.

    ```    
    D:\Public> git clone https://github.com/myaccount/Project.git .\Backup
    ```

    `D:\Public` 폴더에 Backup 폴더가 만들어지고 그 폴더에 .git 폴더와 모든 파일이 있는 것을 확인할 수 있습니다. 만약 Clone시에 아래와 같은 에러가 발생을 한다면 PROXY 예외 처리가 안된 것입니다. PROXY 예외 처리를 하고 다시 하십시오.

    ```
    fatal: repository 'https://github.com/myaccount/Project.git/' not found
    ```

2. VS Code에서 `D:\Public\Backup` 폴더를 엽니다.

3. 현재 진행하는 과정을 위한 Branch를 생성합니다.

    ````warning
    절대로 Master Branch에 직접 Push(백업)를 하면 안됩니다. 반드시 Branch를 이용하여 Push를 하고 병합 과정을 거쳐야 합니다. 
    ````

    여기서는 **Stage** Branch를 생성하는 것으로 설명을 합니다. Visual Studio Code의 터미널에서 아래의 명령을 실행을 합니다.

    ```
    D:\Public\Backup> git branch Stage
    D:\Public\Backup> git checkout Stage
    ```

    **Stage** Branch를 생성하고 생성된 Branch로 환경을 전환합니다.


4. 백업을 할 ORG에 **SFDX: Authorize an Org** 기능을 사용하여 로그인을 합니다.

5. 로그인을 한 후에 Visual Studio Code의 터미널에서 아래의 명령을 실행을 합니다.

    ``` powershell
    PS …\Backup> sf project retrieve start -x .\manifest\package.xml
    ```

    한동안 시간이 흐르면 모든 메타데이터가 다운로드가 됩니다.

6. 아래의 명령을 순서대로 실행을 합니다.

    ```
    D:\Public\Backup> git add .
    D:\Public\Backup> git commit -m “Stage Backup Metadata First Commit”
    ```

7. 명령행에서 `git remote -v` 라고 명령을 입력해 봅니다.

    origin 이라는 항목이 표시되면 8번으로 넘어가고 표시되는 항목이 없으면 아래의 명령을 실행을 합니다.

    ```
    D:\Public\Stage> git remote add origin https://username:personal_token@github.com/myaccount/Project.git
    ```

    origin은 원격 Master Repository의 Alias 명칭입니다.

8. 아래의 명령으로 백업한 메타데이터를 Stage Repository에 업로드를 합니다.

    ```
    D:\Public\Stage> git push --set-upstream origin Stage
    ```

    `--set-upstream` 옵션은 해당 Repository에 처음으로 push를 할 때에만 사용하면 됩니다.

주기적인 백업은 5, 6, 8번의 항목만을 수행하시면 됩니다.

### 새로운 Master Repository를 구성하고 ORG를 백업하는 방법

1. Visual Studio Code에서 **SFDX: Create project with Manifest** 기능을 사용하여 Salesforce 프로젝트를 생성을 합니다. 프로젝트의 이름은 **Temp**라고 지정을 합니다.

2. Master를 구성하기 위한 **package.xml** 파일을 만들어야 합니다.

    **Salesforce Package.xml Generator Extension for VS Code** Extension을 사용하여 백업할 메타데이터 항목을 package.xml 파일에 추가합니다.

3. 백업을 할 ORG에 **SFDX: Authorize an Org** 기능을 사용하여 로그인을 합니다.

4. 로그인을 한 후에 Visual Studio Code의 터미널에서 아래의 명령을 실행을 합니다.

    ``` powershell
    PS …\Backup> sf project retrieve start -x .\manifest\package.xml
    ```

    한동안 시간이 흐르면 모든 메타데이터가 다운로드가 됩니다.

5. Github.com(또는 git 서버)에 Repository를 만듭니다. 
    여기서는 Repository의 이름을 **Project**라고 가정하겠습니다. 그렇다면 Repository의 Url은 아래와 같습니다.

    `https://<domain.com>/<account | organization>/Project`

    여기서는 `https://github.com/myaccount/Project` 라고 가정합니다.

6. Repository를 컴퓨터에 Clone을 합니다.

    `D:\Public\Backup` 폴더에 Clone을 하도록 하겠습니다.

    ```
    D:\Public> git clone https://github.com/myaccount/Project.git .\Backup
    ```

    D:\Public 폴더에 Backup 폴더가 만들어지고 그 폴더에 .git 폴더와 README 파일이 있는 것을 확인할 수 있습니다. 기업망 방화벽 안에서 사용할 때 만약 Clone시에 아래와 같은 에러가 발생을 한다면 PROXY 예외 처리가 안된 것입니다. PROXY 예외 처리를 하고 다시 하십시오.

    ```
    fatal: repository 'https://github.com/myaccount/Project.git/' not found
    ```

7. Temp 프로젝트 폴더의 모든 폴더/파일들을 `D:\Public\Backup` 폴더로 복사를 합니다.

8. Visual Studio Code에서 `D:\Public\Backup` 폴더를 엽니다.

9. 현재 진행하는 과정을 위한 Branch를 생성합니다. 

    ```warning
    절대로 Master Branch에 직접 Push(백업)를 하면 안됩니다. 반드시 Branch를 이용하여 Push를 하고 병합 과정을 거쳐야 합니다.
    ```
    
    여기서는 **Stage** Branch를 생성하는 것으로 설명을 합니다. Visual Studio Code의 터미널에서 아래의 명령을 실행을 합니다.

    ```
    D:\Public\Backup> git branch Stage
    D:\Public\Backup> git checkout Stage
    ```

    **Stage** Branch를 생성하고 생성된 Branch로 환경을 전환합니다.

10. Visual Studio Code의 터미널에서 아래의 명령을 순서대로 실행을 합니다.

    ```
    D:\Public\Backup> git add .
    D:\Public\Backup> git commit -m “First Master Metadata Commit”
    ```

11. 명령행에서 `git remote -v` 라고 명령을 입력해 봅니다. origin 이라는 항목이 표시되면 12.로 넘어가고 표시되는 항목이 없으면 아래의 명령을 실행을 합니다.

    ```
    D:\Public\Stage> git remote add origin https://username:password@github.com/myaccount/Project.git
    ```

    origin은 원격 Master Repository의 Alias 명칭입니다.

12. 아래의 명령으로 백업한 메타데이터를 **Stage** Repository에 업로드를 합니다.

    ```
    D:\Public\Stage> git push --set-upstream origin Stage
    ```

    `--set-upstream` 옵션은 해당 Repository에 처음으로 push를 할 때에만 사용하면 됩니다.

    이후 주기적인 백업을 위하여 5, 11, 13 항목을 수행하면 됩니다.

## 개발용 Master Repository를 구성하는 방법

Admin 또는 Release Manager에 의해서 수행합니다. Backup용 Master Repository는 ORG의 모든 사항을 포함하고 있어서 용량이 상당합니다. 또한 주요 시스템 설정도 포함하고 있으므로 임의 수정되는 경우를 방지하기 위하여 개발용 Master Repository를 만들거나, 실제 개발에서는 몇가지 메타데이터 구조만을 사용하므로 이 구조만을 관리하는 Repository를 만들어 사용하도록 합니다.

1. Github.com(또는 git 서버)에 Repository를 만듭니다.

    여기서는 Repository의 이름을 **Develop**라고 가정하겠습니다. 그렇다면 Repository의 Url은 아래와 같습니다.

    `https://<domain.com>/<account | organization>/Develop`

    여기서는 `https://github.com/myaccount/Develop` 라고 가정합니다. 보통 만들때 README.md를 처음으로 Commit 한 상태로 만들게 됩니다.

2. Repository를 컴퓨터에 Clone을 합니다. D:\Public 폴더에 Clone을 하도록 하겠습니다.

    ```
    D:\Public> git clone https://github.com/myaccount/Develop.git
    ```

    `D:\Public` 폴더에 Develop 폴더가 만들어지고 그 폴더에 .git 폴더와 README.md 파일이 있는것을 확인할 수 있습니다.
    기업망 방화벽안에서 사용시 만약 Clone시에 아래와 같은 에러가 발생을 한다면 PROXY 예외 처리가 안된것입니다. PROXY 예외 처리를 하고 다시 하십시오.

    ```
    fatal: repository 'https://github.com/myaccount/Develop.git/' not found
    ```

3. Visual Studio Code에서 **SFDX: Create project with Manifest** 기능을 사용하여 Salesforce 프로젝트를 생성을 합니다.

    프로젝트의 이름은 **Develop**으로 합니다.

4. 필요한 경우 프로젝트의 `package.xml` 파일에 다운로드할 메타데이터의 형식을 추가합니다.

    Ex) `CustomLabel`, `Translations`등을 추가하시면 됩니다.

5. 개발을 진행할 ORG에 **SFDX: Authorize an Org** 기능을 사용하여 로그인을 합니다.

6. 로그인을 한 후에 Visual Studio Code의 터미널에서 아래의 명령을 실행을 합니다.

    ``` powershell
    PS …\Develop> sf project retrieve start -x .\manifest\package.xml
    ```

    한동안 시간이 흐르면 모든 메타데이터가 다운로드가 됩니다.

7. **Develop** 폴더의 모든 파일을 선택하여 `D:\Public\Develop` 폴더로 복사를 합니다.

8. CMD 창에서 `D:\Public\Develop` 폴더로 이동하여 아래의 명령을 순서대로 실행을 합니다.

    ```
    D:\Public\Develop> git add .
    D:\Public\Develop> git commit -m "First Master Develop Tree Commit"
	```

9. 명령행에서 `git remote -v` 라고 명령을 입력해 봅니다. origin 이라는 항목이 표시되면 10.으로 넘어가고 표시되는 항목이 없으면 아래의 명령을 실행을 합니다.

    ```
    D:\Public\Develop> git remote add origin https://username:password@github.com/myaccount/Develop.git
    ```

    origin은 원격 Master Repository의 Alias 명칭입니다. develop와 같이 다른 이름으로 지정하여 사용할 수 있습니다.

10. 아래의 명령으로 백업한 메타데이터를 Master Repository에 업로드를 합니다.

    ```
    D:\Public\Develop> git push --set-upstream origin master
    ```

    `--set-upstream` 옵션은 해당 Repository에 처음으로 push를 할 때에만 사용하면 됩니다.

### 개발자가 개발용 Master Repository와 Branch를 사용하여 개발하는 방법

개발용 Master Repository는 확인이 된 코드만 관리할 수 있도록 Admin/Release Manager만 편집을 할 수 있도록 하고, 개발자들은 각자가 Branch를 만들어서 개발을 진행하도록 합니다. 이때 각각의 개발 모듈(Sprint 항목)마다 Branch를 만들어서 개발 후 Commit을 한 후에 Release Manager가 Branch를 제거하는 방법이 있고, 또 다른 방법으로는 개발자 각자가 고유의 Branch에서 개발을 진행하는 방법이 있습니다.

이 방법을 사용할 경우 Release Manager는 Branch를 제거를 하지 않으며, 개발자는 하나의 모듈 작업이 끝나고 나면 Branch를 최신의 상태로 업데이트를 한 후에 새로운 개발작업을 이어 나아가야 합니다. 이렇게 하여야만 나중에 Commit된 개발 코드들이 다른 개발자의 코드와 충돌할 가능성이 적어집니다.

```warning
개발자는 반드시 Branch를 만든 후에 개발하도록 하여, Master Repository가 개발자에 의해 직접 수정되는 일이 없도록 유의합니다.
```

개발용 Master Repository는 항상 최신의 상태를 유지하고 있다고 가정을 합니다.

1. Visual Studio Code에서 **SFDX: Create project with Manifest** 기능을 사용하여 Salesforce 프로젝트를 생성을 합니다.
    프로젝트의 이름은 “DEV01”으로 합니다.

2. Visual Studio Code의 프로젝트에서 터미널에서 이후 작업을 이어갑니다. 아래의 명령을 실행합니다.

    ``` powershell
    PS …\DEV01> git init
    PS …\DEV01> git remote add origin https://user01:pass01@github.com/myaccount/Develop.git
    ```

3. 아래의 명령으로 origin이라는 Remote가 등록되었는지 확인합니다.

    ```
    PS …>DEV01> git remote -v
    ```

4. 아래의 명령으로 Master Repository를 다운로드 합니다.

    ```
    PS …\DEV01> git pull [master]
    ```

5. 4.번을 진행을 하면 마지막 절차에서 프로젝트의 기본으로 생성된 파일 Master Repository의 내용이 충돌이 있다는 에러가 표시되면서 중단을 합니다. 이때 아래의 명령을 실행을 합니다.

    ```
    PS …\DEV01> git checkout -f master
    ```

    이제 프로젝트 환경과 Master Repository가 동기화된 개발환경이 만들어졌습니다. 개발을 진행하기 전에 Branch를 만들도록 합니다.

6. Branch를 만들려면 아래의 명령을 실행을 합니다. 예) mydev01 Branch 만들기

    ```
    PS …\DEV01> git branch mydev01
    ```

7. Branch 환경으로 전환을 합니다.

    ```
    PS …\DEV01> git checkout mydev01
    ```

    이제 Branch 환경으로 전환이 되었습니다. 개발자는 이 Branch 환경에서 개발을 진행하면 됩니다.

8. **SFDX: Authorize an Org** 기능을 사용하여 개발 Org에 로그인합니다.

    이 과정이 진행되기 위해서는 반드시 개발 Org와 Develop Repository의 코드 상태가 최신 상태로 일치하여야 합니다. Admin/Release Manager는 항상 Develop Repository의 상태를 개발 Org의 최신 정보로 유지를 하여야 합니다.

### 개발자가 최신의 Master 정보로 Master Branch를 업데이트 하는 방법

각각의 Branch에서 개발이 완료되면 개발자는 Master Repository의 최신 변경 사항을 업데이트를 하여야 합니다.

아래의 명령을 실행을 하여 업데이트를 진행합니다. 아래의 명령은 개발자가 고유의 Branch를 유지할 경우 사용합니다.

```
PS …\DEV01> git checkout master
PS …\DEV01> git pull
PS …\DEV01> git checkout <branch>
PS …\DEV01> git merge master
```

git merge master 명령은 현재 사용중인 Branch를 Master로 업데이트 합니다.

## 개발된 코드를 Git 서버로 Commit 하는 방법

### 개발자가 Terminal 모드에서 Branch의 개발된 내용을 Commit하는 방법

터미널에서 아래의 명령을 실행을 합니다.

```
PS …\DEV01> git add -u
PS …\DEV01> git commit -m “mydev01 <sprint> commit”
PS …\DEV01> git push --up-stream origin mydev01
```

### 개발자가 Visual Studio Code에서 Branch의 개발된 내용을 Commit하는 방법

1. Visual Studio에서 좌측 사이드바의 ![using github](/assets/images/github-016.png){: height="16" } 아이콘을 클릭합니다.

    이 아이콘이 소스코드 버전 관리를 하는 메뉴 아이콘입니다. Git 환경이 설정된 경우라면, **Changes** 아래에 추가 및 수정된 파일의 목록을 표시할 것입니다.

    **U**로 표시된 항목은 새로 추가되는 파일, **M**으로 표시되는 항목은 수정된 파일을 의미합니다.

2. 아래와 같이 각각의 파일 이름 뒤의 + 아이콘을 클릭하여 각 파일을 하나씩 수동으로 Staging을 할 수 있습니다. 

    ![using github](/assets/images/github-017.png)

    단일 파일로 Staging 된것으로 표시하면 아래 그림처럼 Visual Studio Code에 Staging된 파일과 그렇지 않은 파일을 보여줍니다. Staging 된 파일은 아래의 그림처럼 **Staged Changes** 아래에 표시가 됩니다.

    ![using github](/assets/images/github-018.png)

    만약 변경돤 모든 파일을 Staging 하려면 아래 그림처럼 **Changes** 헤더의 **+** 아이콘을 클릭합니다.

    ![using github](/assets/images/github-019.png)

3. 모든 파일이 Staging 되었으면 다음 단계는 이 파일들을 Commit하는 것입니다.

    파일을 Commit하려면 Commit Message를 지정하여야 합니다. 아래 이미지에서 볼 수 있듯이 Visual Studio Code의 메시지박스에 *Commit Message*를 입력하고 **✔ Commit** 버튼을 클릭하여 Commit을 할 수 있습니다.

    ![using github](/assets/images/github-020.png)

    Git에 대한 모든 변경 사항을 처음으로 Commit 할때는 Commit Message를 **Initial Commit**으로 보통 지정을 합니다.
    
    개발자가 원하는 Commit Message를 입력할 수 있으며, Commit하는 내용이 명확이 드러나는 메시지를 입력하는것이 좋습니다. Sprint의 코드명을 포함시키는것도 한 방법입니다.
    
    모든 파일을 Commit하면 변경 사항이 없으므로 Git의 Changes 사이드바가 아래 그림처럼 비어있게 됩니다.

    ![using github](/assets/images/github-021.png)

4. Commit을 한 후에는 어떤 단순한 변경이라도 발생을 하면 Git **Changes** 사이드바에 **M**(Modified)로 표시가 되며 해당 파일을 다시 Staging하고 Commit하여야 합니다.

    ![using github](/assets/images/github-022.png)

    위의 이미지에서 볼 수 있듯이 코드 수정이 되었으며, Git의 CHANGES 사이드바에 수정된 것(M)으로 지정되었습니다.
    
    모든 수정 작업이 완료된 뒤에 앞에서 진행한 절차와 동일하게 파일을 Staging하고 Commit하여야 합니다. Git 사이드바에서 파일 이름을 클릭하여 아래와 같이 변경한 내용을 검토할 수 있습니다.
 
    ![using github](/assets/images/github-023.png)

5. 변경 내용을 취소하려면, 하나의 파일에 대해서는 파일 이름의 **+** 아이콘의 왼쪽에 있는 Undo 아이콘(**Discard Changes**)을 클릭하거나, 모든 파일에 대한 변경 사항을 취소하려면 Changes 헤더에 있는 Undo 아이콘(**Discard All Changes**)을 클릭하면 됩니다.

## Branch에 Commit한 개발 항목을 Master Repository에 적용하는 방법

아래에서 설명하는 Pull Request는 github.com의 고유 기능입니다. 
일반적인 git 서버를 사용하는 경우 해당 서버의 절차를 따라야 합니다.

개발자가 Commit한 개발 항목은 Branch에 있으며, 아직 Master Repository에 적용되지는 않았습니다. 개발자는 Admin / Release Manager에게 개발 항목을 Master Repository에 적용하여 주기를 요청을 하여야 합니다. 이 절차를 Pull Request라고 합니다. 

### 개발자가 Admin / Release Manager에게 Pull Request하는 방법

1. Github.com의 Repository에 접속을 합니다.

2. 아래의 그림과 같은 화면에서 ①의 Branch에서 Commit한 Branch명을 선택을 하면 ②와같이 Push된 항목이 있다는 메시지와 함께 ③과 같은 링크가 표시가 될 것입니다. ③의 **Compare & pull request** 버튼을 클릭합니다.

    ![using github](/assets/images/github-005.png)

3. 아래 그림과 같은 **Open a pull request** 화면이 표시되면,

    ![using github](/assets/images/github-006.png)

    ① - 통합될 **base** Branch(여기서는 Master)를 선택합니다.

    ② - 통합할 개발 Branch를 확인합니다.

    ③ - Pull request에 대한 제목을 입력합니다. 기본값은 Commit시 입력한 내용입니다.

    ④ - 요청에 대한 설명을 입력합니다. @ 태그를 사용하여, 다른 사람에게 알림을 보낼 수 있습니다.

    ⑤ - **Create pull request** 버튼을 클릭하여 Pull Request를 등록합니다.
 

4. **Create pull request**를 클릭하여 관리자에게 요청을 보냈습니다.

    아래 그림과 같은 화면으로 이동을 합니다. `Into <base>, from <branch>` 가 정확한지 확인합니다.
 
    ![using github](/assets/images/github-007.png)

### Admin / Release Manager가 요청받은 Pull Request를 Master Repository에 적용하는 방법

Admin / Release Manager는 요청된 변경 사항을 Master Repository에 통합(Merge)합니다. 만약 다수의 요청이 있는 경우, 요청된 순서대로 Merge를 진행을 합니다.

1. Github.com의 **Pull requests** 탭을 클릭하여 화면을 이동합니다.

    아래 그림과 같은 요청 항목이 표시가 됩니다. Request 항목을 클릭합니다.

    ![using github](/assets/images/github-008.png)

    **File changed** 탭을 클릭하여 변경 내용을 확인할 수 있습니다.

    ![using github](/assets/images/github-009.png)

2. 요청된 내용에 문제가 있거나, 추가적인 변경이 필요한 경우 **Review Changes** 버튼을 클릭하고 Comment를 작성하여 요청자에게 메시지를 전달 할 수 있습니다.
 
    ![using github](/assets/images/github-010.png)

    ![using github](/assets/images/github-011.png)
    
    **Submit review** 버튼을 클릭하면, 메시지를 전달을 합니다.

    또는, **Conversation**탭의 하단에서 Comment를 입력할 수 있습니다. Pull Request한 내용에 수정이 필요한 경우, 수정을 요청하고 **Close pull request** 버튼을 클릭하여 요청된 Pull Request를 종료할 수 있습니다.
 
    ![using github](/assets/images/github-012.png)

3. Pull Request 항목을 Merge 하려면, **Conversation** 탭에서 아래의 그림처럼 **This branch has no conflicts with the base branch** 메시지가 표시되었으면 기존 코드와 충돌이 없으므로 **Merge pull request** 버튼을 클릭하여 Pull Request된 내용을 Base Branch에 Merge Commit을 합니다.

    ![using github](/assets/images/github-013.png)

    Commit 메시지를 입력하고, **Confirm merge** 버튼을 클릭합니다.

    ![using github](/assets/images/github-014.png)

    Merge가 완료되었습니다. 만약 개발용으로 만든 Branch가 개발자용 Branch가 아닌 Sprint 항목에 대한 Branch인 경우, **Delete branch**를 클릭하여 Branch를 삭제할 수 있습니다.  

    ![using github](/assets/images/github-015.png)

4. Pull Request에 대한 Merge가 완료되었습니다. 더이상 남아있는 Pull Request가 없으면 Merge 작업을 종료합니다. 
