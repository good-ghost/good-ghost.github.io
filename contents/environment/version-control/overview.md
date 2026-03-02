---
title: "개요"
sort: 1
---

# Git에 대한 개요

## 기본적인 사용법은 아래의 링크를 방문하여 알아보면 됩니다.

- [Git 기초](https://backlog.com/git-tutorial/kr/intro/intro1_1.html)

- [[Git] git 사용법 튜토리얼1 - 초기화, 스테이징, 커밋, 되돌리기](https://ebbnflow.tistory.com/196)

- [Visual Studio Code에 Github 연동하기](https://dev-youngjun.tistory.com/7)

## 관련 동영상

- [How to connect VSCode Salesforce Project to GitHub](https://www.youtube.com/watch?v=AxBu4JARPCM)

- [A Practical Guide to git-Based Development](https://www.youtube.com/watch?v=sZoNe-cA89A)

간단하게 설명하자면, git은 수행중인 모든 변경 사항을 추적 관리하는 버전 컨트롤 시스템(VCS)입니다.

그러나 새로 만드는 모든 파일이 처음부터 자동으로 추적되지는 않습니다.

## Git 저장소의 각 파일은 다음의 상태 중 하나일 수 있습니다.

1.	**Untracked** : 사용자가 새로운 파일을 만들면 추적되지는 않거나, 이 파일이 git에 알려진 상태가 아니라고 할 수 있습니다.

2.	**Tracked** / **Index Added** : 이 파일이 새로 만들어진 파일이고, 이 파일을 추적하여야 한다고 git에 지정하거나 알리면(Staging이라고 함) 파일 상태가 Tracked로 변경되거나 특정 Index(숫자)가 부여되므로 git으로 식별할 수 있습니다.

3.	**Committed** : 필요한 변경 작업이 완료되면 해당 파일을 Commit합니다. Git은 해당 파일을 Committed로 표시하고, 내부 변경 로그에 Commit 메시지로 업데이트가 있었음을 기록을 합니다.

4.	**Untracked** / **Modified** : 이전에 Commit된 파일이 업데이트가 되면 파일의 상태가 Untracked / Modified로 변경됩니다. 이 파일이 Commit될 준비가 되었음을 Git에게 알리기 위하여 이 파일을 다시 Staging하여야 합니다.

5.	**Tracked** / **Index Modified** : 업데이트된 파일이 다시 Staging되면 상태가 다시 Tracked로 변경됩니다. 이 시점에서 파일의 Index는 최근 변경사항으로 수정되고 파일은 다시 Commit할 준비가 됩니다.

**새로운 파일에 대한 기본적인 흐름은**

Untracked > Index Added(Tracked) > Committed

**기존 파일이 업데이트된 경우의 흐름은**

Untracked > Index Modified(Tracked) > Committed

따라서 git Repository를 초기화한 상태 직후라면 기본적으로 모든 파일이 추적되지 않습니다. 다음 단계는 파일을 Staging 하는 것입니다. 

이것은 git에게 변경이 완료가 되었고 Commit할 준비가 되었음을 알려주는 것입니다.

