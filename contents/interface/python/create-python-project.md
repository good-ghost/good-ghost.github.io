---
title: "Python 프로젝트 생성"
sort: 2
---

# Python 프로젝트 생성

## 가상 환경

파이썬에서는 프로젝트 별로 독립된 개발 환경을 구성하기 위해서 가상 환경(virtual environment)을 사용할 수 있습니다.

가상 환경을 사용하지 않을 경우 모든 프로젝트가 동일한 Python 실행 환경을 사용하게 되고, 같은 패키지 관리를 적용받게 됩니다. 프로젝트마다 서로 같은 패키지라도 다른 버전의 패키지를 사용해야 할 수 있으므로 Python 프로젝트 별로 독립된 가상 환경을 구성하는 것이 좋습니다.

가상 환경을 구성하는것은 다음과 같이 두가지 방법이 있습니다.

- virtualenv : Python에서 패키지로 제공되는 가상 환경 관리 도구입니다.

- venv : Python 3.3 이상부터 제공되는 내장된 가상 환경 관리 도구입니다.

### virtualenv와 venv의 차이점

venv 모듈은 virtualenv의 경량화된 모듈입니다. 따라서 virtualenv의 모든 기능을 제공하지 않습니다.

- venv는 virtualenv보다 속도가 느립니다.

- virtualenv가 더 확장성이 좋습니다. virtualenv는 Python 버전이 다른 가상 환경을 구성할 수 있습니다.

- virtualenv는 외부 패키지이므로 pip를 통하여 업데이트가 가능합니다.

## 프로젝트 생성

1. 프로젝트를 저장할 폴더를 만들고, 그 폴더로 이동을 합니다.

    ```
    md sample-python
    cd sample-python
    ```

2. 가상 환경 구성

    - **virtualenv** 환경을 사용

        ````note
        **virtualenv**를 사용하려면 먼저 패키지를 설치를 하여야 합니다.

        ```
        pip install virtualenv
        ```
        ````

        - Visual Studio Code에서 미리 만든 프로젝트 폴더를 열고 터미널창에서

            ```
            virtualenv .venv
            ```

    - **venv** 환경을 사용

        - Visual Studio Code에서 미리 만든 프로젝트 폴더를 열고 터미널창에서

            ```
            python -m venv .venv
            ```

        - Visual Studio Code의 Python Extension을 사용

            1. Visual Studio Code의 **Command Palettes...**에 python을 입력을 하고 표시되는 목록에서 **Pyhton**: Create Environment...를 선택합니다.

                ![python-venv](/assets/images/python-venv-001.png)

            2. **Select an envirnment type...**에서 **venv**를 선택을 합니다.

                ![python-venv](/assets/images/python-venv-002.png)

            3. **Select a python installation...**에서 설치된 Python을 선택을 합니다.

                ![python-venv](/assets/images/python-venv-003.png)

                아래ㅘ 같은 메시지가 표시되며 가상 환경을 구성합니다.

                ![python-venv](/assets/images/python-venv-004.png)

                다음의 메시지가 표시되면 가상 환경 구성이 완료된것입니다.

                ![python-venv](/assets/images/python-venv-005.png)

            4. Visual Studio Code에서 Extension을 사용하여 `venv` 가상 환경을 구성하면 **활성화**까지 자동 진행을 합니다.

                **Windows** 환경에서 터미널 창으로 **Power Shell**을 사용하고 있다면 다음과 같은 오류 메시지가 표시되었을 수 있습니다.

                ![python-venv](/assets/images/python-venv-006.png)

                Power Shell의 보안 오류이므로 아래의 명령을 사용하여 현재 사용자에 대한 제한을 해제합니다.

                ``` powershell
                Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
                ```

                또는

                ``` powershell
                Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
                ```

                ![python-venv](/assets/images/python-venv-007.png)


                활성화가 정상적으로 이루어지면 프롬프트가 **(.venv)** 로 변경됩니다.

3. 가상 환경 활성화

    Windows의 Visual Studio Code 사용환경에서 Power Shell을 사용하고 있다면, 터미널 창에서

    ``` powershell
    & ./.venv/Scripts/activate.ps1
    ```

    만약 아래 그림과 같이 Security 오류가 발생을 하면

    ![python-venv](/assets/images/python-venv-006.png)

    Power Shell의 보안 오류이므로 아래의 명령을 사용하여 현재 사용자에 대한 제한을 해제합니다.

    ``` powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

    또는

    ``` powershell
    Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
    ```

    ![python-venv](/assets/images/python-venv-007.png)


    활성화가 정상적으로 이루어지면 프롬프트가 **(.venv)** 로 변경됩니다.


4. 비활성화를 하려면 아래의 명령을 실행하면 됩니다.

    ```powershell
    & ./.venv/Scripts/deactivate.bat
    ```

가상 환경이 활성화 된 상태에서는 Python 패키지를 설치를 하면 해당 가상 환경에만 적용이 됩니다.

이렇게 독립된 가상 환경에 패키지를 설치하게 되면 다른 프로젝트에서 사용하고 있는 requests 패키지를 덮어쓸 일이 없습니다.

