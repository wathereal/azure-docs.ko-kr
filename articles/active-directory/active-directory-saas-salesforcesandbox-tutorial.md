---
title: "자습서: Salesforce Sandbox와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 Salesforce Sandbox 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 90e08b9cf2feb93de4877bec9734352949896dca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>자습서: Salesforce Sandbox와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Salesforce Sandbox를 통합하는 방법에 대해 알아봅니다.

Salesforce Sandbox를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

- Salesforce Sandbox에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Salesforce Sandbox에 자동으로 로그인(Single Sign-On)하도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](active-directory-appssoaccess-whatis.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Salesforce Sandbox와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Salesforce Sandbox Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Salesforce Sandbox를 추가합니다.
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>갤러리에서 Salesforce Sandbox를 추가합니다.
Salesforce Sandbox의 Azure AD 통합을 구성하려면 갤러리의 Salesforce Sandbox를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Salesforce Sandbox를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Salesforce Sandbox**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_search.png)

5. 결과 창에서 **Salesforce Sandbox**를 선택하고 **추가**를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Salesforce Sandbox에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Salesforce Sandbox 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Salesforce Sandbox의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Salesforce Sandbox의 **Username** 값으로 할당하여 설정합니다.

Salesforce Sandbox에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Salesforce Sandbox 테스트 사용자 만들기](#creating-a-salesforce-sandbox-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Salesforce Sandbox에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Salesforce Sandbox 응용 프로그램에서 Single Sign-On을 구성합니다.

**Salesforce Sandbox에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Salesforce Sandbox** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-on 구성][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-on 구성](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. **Salesforce Sandbox 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_url.png)

     **로그온 URL** 텍스트 상자에 다음 패턴으로 값을 입력합니다. `https://<subdomain>.my.salesforce.com` 

    > [!NOTE] 
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트 합니다. 이 값을 얻으려면 [Salesforce Sandbox 클라이언트 지원 팀](https://help.salesforce.com/support)에 문의하세요.


4. 디렉터리의 다른 Salesforce Sandbox 인스턴스에 대한 Single Sign-On을 이미 구성한 경우 **로그온 URL**과 같은 값으로 **식별자**도 구성해야 합니다. 
    
    >[!Note]
    >대화 상자의 **앱 URL 구성** 페이지에서 **고급 설정 표시** 확인란을 선택하여 **식별자** 필드를 찾을 수 있습니다. 


5. **SAML 서명 인증서** 섹션에서 **인증서**를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

6. **저장** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_400.png)

7. **Salesforce Sandbox 구성** 섹션에서 **Salesforce Sandbox 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_configure.png) 
<CS>
8. 브라우저에서 새 탭을 열고 Salesforce Sandbox Administrator 계정으로 로그인합니다.

9. 위쪽의 메뉴에서 **설정**을 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-salesforcesandbox-tutorial/IC781024.png)
10. 왼쪽의 탐색 창에서 **보안 제어**를 클릭한 다음 **Single Sign-On 설정**을 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-salesforcesandbox-tutorial/IC781025.png)
11. Single Sign-On 설정 섹션에서 ![Single Sign-On 구성](./media/active-directory-saas-salesforcesandbox-tutorial/IC781026.png) 단계를 수행합니다.
     
     a.  **SAML 사용**을 선택합니다. 

     b.  **새로 만들기**를 클릭합니다.

12. SAML Single Sign-On 설정 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-salesforcesandbox-tutorial/IC781027.png)

    a. 이름 텍스트 상자에 구성의 이름을 입력합니다(예: *SPSSOWAAD\_Test*). 

    b. **SMAL 엔터티 ID** 값을 **발급자** 텍스트 상자에 붙여넣습니다.

    c. 디렉터리에 처음으로 추가하는 Salesforce Sandbox 인스턴스인 경우 **엔터티 Id** 텍스트 상자에 **https://test.salesforce.com**을 입력합니다. Salesforce Sandbox의 인스턴스를 이미 추가한 경우에는 **엔터티 ID**에 **로그온 URL**을 입력합니다. 형식은 다음과 같아야 합니다. `http://company.my.salesforce.com`  
 
    d. 다운로드한 인증서를 업로드하려면 **찾아보기** 를 클릭합니다.  

    e. **SAML ID 유형**으로 **사용자 개체에서 페더레이션 ID를 포함하는 어설션**을 선택합니다.
 
    f. **SAML ID 위치**로 **Subject 문의 NameIdentifier 요소에 ID 포함**을 선택합니다.

    g. **Single Sign-On 서비스 URL**을 **ID 공급자 로그인 URL** 텍스트 상자에 붙여넣습니다. 

    h. SFDC는 SAML 로그아웃을 지원하지 않습니다.  해결 방법으로 **ID 공급자 로그아웃 URL** 텍스트 상자에 'https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0'을 붙여 넣습니다.

    i. **서비스 공급자가 시작한 요청 바인딩**에서 **HTTP Post**를 선택합니다. 

    j. **Save**를 클릭합니다.

### <a name="enable-your-domain"></a>도메인 활성화
이 섹션에서는 이미 도메인을 만들었다고 가정합니다.  자세한 내용은 [도메인 이름 정의](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)를 참조하세요.

**도메인을 사용하려면 다음 단계를 수행합니다.**

1. 왼쪽 탐색 창에서 **도메인 관리**를 클릭한 다음 **내 도메인**을 클릭합니다.
   
     ![Single Sign-on 구성](./media/active-directory-saas-salesforcesandbox-tutorial/IC781029.png)
   
   >[!NOTE]
   >도메인이 올바르게 구성되었는지 확인합니다. 

2. **로그인 페이지 설정** 섹션에서 **편집**을 클릭한 다음 **인증 서비스**로 이전 섹션에서 SAML Single Sign-On 설정의 이름을 선택하고 마지막으로 **저장**을 클릭합니다.
   
   ![Single Sign-on 구성](./media/active-directory-saas-salesforcesandbox-tutorial/IC781030.png)

도메인이 구성되면 바로 사용자가 Salesforce 샌드박스에 로그인하는 도메인 URL을 사용해야 합니다.  

URL의 값을 가져오려면 이전 섹션에서 만든 SSO 프로필을 클릭합니다.    

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.


### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    c. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-salesforce-sandbox-test-user"></a>Salesforce Sandbox 테스트 사용자 만들기

이 섹션에서는 Salesforce Sandbox에서 Britta Simon이라는 사용자를 만듭니다. Salesforce Sandbox는 기본적으로 설정되는 Just-In-Time 프로비전을 지원합니다.
이 섹션에 작업 항목이 없습니다. Salesforce Sandbox에 사용자가 없는 경우 Salesforce Sandbox에 액세스하려고 시도하면 새 사용자가 만들어집니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Salesforce Sandbox에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Salesforce Sandbox에 사용자를 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Salesforce Sandbox**를 선택합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

SSO 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)
* [사용자 프로비저닝 구성](active-directory-saas-salesforce-sandbox-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_203.png

