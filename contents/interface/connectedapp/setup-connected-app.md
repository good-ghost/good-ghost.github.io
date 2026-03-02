---
title: "Connected App 등록"
sort: 3
---

# Connected App 등록

다음의 [[링크](https://help.salesforce.com/s/articleView?id=sf.connected_app_create_api_integration.htm&type=5)]를 참조하여 Connected App을 생성합니다.

1. **Setup** > **Apps** > **App Manager** 메뉴로 이동한 후 **New Connected App** 버튼을 클릭합니다.
   
    ![Create ConnectedApp](/assets/images/connectedapp-006.png)
 
    a. **Basic Information**의 필수 값 **Connected App Name**, **API Name**, **Contact Email**의 값을 입력합니다.
    
    b. **API (Enable OAuth Settings)**의 **Enable OAuth Settings** 체크박스를 체크합니다.

    c. **Callback URL**에 **https://<*****login|test*****>.salesforce.com/services/oauth2/success** 값을 입력을 합니다. JWT 프로세스에서는 사용하지 않는 값으로 더미 호스트 값을 입력하여도 됩니다.

    d. **Use digital signature** 체크박스를 체크하고 미리 만들어 둔 **company.crt** 파일을 지정합니다.

    e. **Selected OAuth Scopes**에서 필요한 권한을 선택합니다. 데이터 통합용 연결을 위해서는 **Manage user data via APIs (api)**와 **Perform requests at any time (refresh_token, offline_access)** 만을 선택해도 되며, 이 문서에서는 테스트를 위하여 **Full Access (full)**을 선택합니다.

2. **Save** 버튼을 클릭합니다.

3. **Manage** 버튼을 클릭합니다.
   
    ![Manage ConnectedApp](/assets/images/connectedapp-007.png)
 
    a. **Edit Policies** 버튼을 클릭합니다.
    
    ![Manage ConnectedApp](/assets/images/connectedapp-008.png)
 
    b. **OAuth Policies** 섹션의 **Permitted Users**의 값을 **Admin approved users are pre-authorized**로 변경합니다.

    ![ConnectedApp Pilicies](/assets/images/connectedapp-009.png)
 
    c. **Save** 버튼을 클릭합니다.

    d. 하단의 **Profile** 섹션에 이 Connected App을 사용할 **Profile** 또는 **Permission Set**을 지정을 합니다.

    ![ConnectedApp Profile](/assets/images/connectedapp-010.png)
 
4. 외부 통합 프로그램에서 지금 설정한 Connected App을 사용하려면 JWT를 사용하는 인터페이스 프로그램의 경우 **Consumer Key**가 필요합니다. 아래의 Connected App 화면에서 **Manage Consumer Details** 버튼을 클릭합니다.
   
    ![Manage ConnectedApp](/assets/images/connectedapp-007.png)
 
5. **Consumer Key** 값을 복사하여 사용할 수 있습니다.
   
    ![Consumer Key](/assets/images/connectedapp-012.png)

지금 설정한 Connected App을 Java 또는 Python 인터페이스 프로그램에서 사용하려면 이 Connected App의 Consumer Key 값과 설정된 Profile/Permission Set에 지정된 사용자 로그인 아이디, 그리고 설정 과정에서 사용된 **company.crt**와 한 쌍이 되는 **company.key** 파일 또는 **company.jks** 키스토어 파일이 필요합니다.
