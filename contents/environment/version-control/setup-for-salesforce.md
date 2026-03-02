---
title: "Salesforce 개발을 위한 환경 설정"
sort: 2
---

# Salesforce 개발을 위한 환경 설정

## 설치
Salesforce 개발 환경에서 Git을 사용하여 형상 관리를 하려면 먼저 Git을 설치를 하여야 합니다.

https://git-scm.com/downloads 에서 본인 사용하는 OS에 맞는 설치 파일을 다운로드하여 설치를 합니다.

여기서는 설치 후 git.exe가 `C:\WUTIL\Git\cmd\git.exe`에 설치되어 있다고 가정합니다.

## Visual Studio Code에 등록

Visual Studio Code에는 기본적인 GIT에 대한 기능을 취급하는 Extension을 포함하고 있습니다. Git 설치가 되었으면, Visual Studio Code의 settings.json 파일에 아래의 내용을 추가합니다.

``` json
"git.path": "C:\\WUTIL\\Git\\cmd\\git.exe",
```

## 사용자 설정

Git을 사용하는 사용자를 등록을 합니다.

``` powershell
git config --global user.name "홍길동"
git config --global user.email "gildong.hong@email.com"
```

프로젝트마다 다르게 설정이 필요한 경우, `--global`을 제외하시면 됩니다.

## 기업망을 위한 설정

기업망 방화벽 안에서는 Git을 사용할 때 PROXY 오류가 발생을 합니다. 아래와 같이 설정을 하여 Git 환경에서는 PROXY 예외 처리를 하도록 합니다.

``` powershell
git config --global http.https://<git-server-domain>.proxy ""
git config --global http.https://<git-server-domain>.sslVerify false
```

`<git-server-domain>`은 기업망에서 사용하는 Git서버의 Domain 이름입니다.

Ex) `github.com`, `git.company.com`

## 설정 확인

설정이 정상적으로 이루어진 경우 Windows OS 사용자는 `C:\Users\[user-id]\.gitconfig` 파일이 다음과 같이 구성되어야 합니다.

``` conf
# This is Git's per-user configuration file.

[user]
	name = 홍길동
	email = gildong.hong@email.com

[http "https://<git-server-domain>"]
	proxy = ""
	sslVerify = false
```

## Personal Token 발급받기

사용자 모두 git(github)의 계정이 있다고 가정을 합니다.

Github의 경우 VS Code의 Terminal에서 사용하려면 암호대신 Personal Token이 필요합니다.

아래의 절차에 따라 사용자의 Personal Token을 발급하여야 합니다.

계정 정보를 확인합니다.

- Username은 Github에 로그인할 때 사용하는 Id입니다.

- Github의 콘솔모드에서는 암호 대신 personal access token을 사용하여야 합니다. 

- 웹브라우져를 사용하여 https://<git-server-domain>/에 로그인 합니다.

### 발급 방법
 
1. **Setting** 메뉴를 클릭합니다.

    ![github](/assets/images/github-001.png)

2. **Developer settings**를 클릭합니다.

    ![github](/assets/images/github-002.png)


3. **Personal access tokens**를 클릭합니다.

    ![github](/assets/images/github-003.png)

4. **Generate new token**을 클릭하여 **personal token**을 만들고 그 값을 따로 보관합니다.

    Github의 경우 **classic token**을 발급받도록 합니다.

    ![github](/assets/images/github-004.png)

