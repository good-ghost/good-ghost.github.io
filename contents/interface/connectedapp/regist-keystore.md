---
title: "JKS 키스토어 등록"
sort: 2
---

# JKS 키스토어 등록

1. 세일즈포스에 로그인 한 후, **Setup** > **Security** > **Certificate and Key Management**로 이동합니다.

2. **Import from Keystore**를 클릭합니다.
   
    ![Certificate and Key Management](/assets/images/connectedapp-001.png)
 
3. 임포트할 JKS 파일을 선택하고 키스토어 암호를 입력한 후 **Save** 버튼을 클릭합니다.
   
    ![Import KeyStore](/assets/images/connectedapp-002.png)
 
    -----
    > ** 참고사항**
    > 
    > 만약 임포트가 성공하지 못하고 아래와 같은 오류가 발생을 하면 다음의 절차를 따릅니다.
    > ```
    > Data Not Available
    > The data you were trying to access could not be found. It may be due to another user 
    > deleting the data or a system error. If you know the data is not deleted but cannot 
    > access it, please look at our support page.
    > ```
    > 1. **Setup** > **Identity Provider** 메뉴로 이동합니다.
    > 2. **Enable Identity Provider** 버튼을 클릭합니다.
    > 
    >    ![Identity Provider](/assets/images/connectedapp-003.png)
    > 3. 1년 유효기간의 Self-sign 키를 만들며 Identity Provider가 활성화 됩니다.
    >   
    >    ![Identity Provider](/assets/images/connectedapp-004.png)
    > 4. **Disable** 버튼을 클릭하여 비활성화를 하여도 됩니다.
    > 5. **Certificate and Key Management** 메뉴로 이동한 후 **Import from Keystore** 버튼을 클릭하여 키스토어 임포트를 다시 진행합니다.
    -----

4. 등록된 키스토어를 확인합니다. **company**라는 이름의 JKS 키스토어가 등록된 것을 확인할 수 있습니다.
    ![Cerificate and Key Management](/assets/images/connectedapp-005.png)
 
