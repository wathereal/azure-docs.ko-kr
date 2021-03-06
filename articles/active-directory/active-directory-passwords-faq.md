---
title: 'FAQ: Azure AD SSPR | Microsoft Docs'
description: "Azure AD 셀프 서비스 암호 재설정에 대해 자주 묻는 질문과 대답"
services: active-directory
keywords: "Active Directory 암호 관리, 암호 관리, Azure AD 셀프 서비스 암호 재설정"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 53075d20aff073ff46dcd6dccaefea5fc8ec3483
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="password-management-frequently-asked-questions"></a>암호 관리 질문과 대답

다음은 암호 재설정과 관련된 모든 항목에 대한 몇 가지 질문과 대답입니다.

여기에서 답변되지 않은 Azure AD 및 셀프 서비스 암호 재설정에 대한 일반적인 질문이 있는 경우 커뮤니티 지원에 [Azure AD 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD)을 지원하도록 요청할 수 있습니다. 커뮤니티 구성원에는 엔지니어, 제품 관리자, MVP 및 동료 IT 전문가가 포함됩니다.

이 FAQ는 다음 섹션으로 구분하여 설명합니다.

* [**암호 재설정 등록에 대한 질문**](#password-reset-registration)
* [**암호 재설정에 대한 질문**](#password-reset)
* [**암호 변경에 대한 질문**](#password-change)
* [**암호 관리 보고서에 대한 질문**](#password-management-reports)
* [**비밀번호 쓰기 저장에 대한 질문**](#password-writeback)

## <a name="password-reset-registration"></a>암호 재설정 등록

* **Q: 내 사용자가 자신의 암호 재설정 데이터를 등록할 수 있습니까?**

  > **A:** 예, 암호 재설정이 사용되고 라이선스가 부여된 경우 http://aka.ms/ssprsetup의 암호 재설정 등록 포털로 이동하여 인증 정보를 등록할 수 있습니다. 사용자는 http://myapps.microsoft.com의 액세스 패널로 이동하고, 프로필 탭을 클릭하고 암호 재설정 등록 옵션을 클릭하여 등록할 수도 있습니다.
  >
  >
* **Q: 내 사용자 대신 암호 재설정 데이터를 정의할 수 있습니까?**

  > **A:** 예, Azure AD Connect, PowerShell, [Azure Portal](https://portal.azure.com) 또는 Office 관리자 포털을 사용하여 수행할 수 있습니다. 자세한 내용은 [Azure AD 셀프 서비스 암호 재설정에서 사용하는 데이터](active-directory-passwords-data.md) 문서를 참조하세요.
  >
  >
* **Q: 온-프레미스에서 보안 질문에 대한 데이터를 동기화 할 수 있습니까?**

  > **A:** 현재 불가능합니다.
  >
  >
* **Q: 내 사용자가 다른 사용자가 이 데이터를 볼 수 없는 방식으로 데이터를 등록할 수 있습니까?**

  > **A:** 예, 사용자가 암호 재설정 등록 포털을 사용하여 데이터를 등록한 경우 전역 관리자 사용자에게만 표시되는 개인 인증 필드로 저장됩니다.
  >
  >
* **Q: 내 사용자를 등록해야 암호 재설정을 사용할 수 있습니까?**

  > **A:** 아니요, 충분한 인증 정보를 정의한 경우 사용자를 등록하지 않아도 됩니다. 디렉터리의 적절한 필드에 데이터의 형식이 저장되어 있다면 암호 재설정이 작동합니다.
  >
  >
* **Q: 내 사용자 대신 인증 전화, 인증 전자 메일 인증 또는 대체 인증 전화 필드를 동기화하거나 설정할 수 있습니까?**

  > **A:** 현재 불가능합니다.
  >
  >
* **Q: 등록 포털이 사용자에게 표시하는 옵션을 어떻게 확인합니까?**

  > **A:** 암호 재설정 등록 포털은 사용자에게 사용하도록 설정된 옵션만을 표시합니다. 사용자 디렉터리에 있는 [구성] 탭의 [사용자 암호 재설정 정책] 섹션에서 이러한 옵션을 찾을 수 있습니다. 예를 들어, 보안 질문을 할 수 없는 경우, 사용자는 해당 옵션에 등록할 수 없습니다.
  >
  >
* **Q: 사용자가 등록된 것으로 간주되는 경우는 언제입니까?**

  > **A:** 사용자가 적어도 [Azure Portal](https://portal.azure.com)에서 설정한 **재설정에 필요한 메서드의 수**를 등록한 경우 SSPR에 등록된 것으로 간주됩니다.
  >
  >

## <a name="password-reset"></a>암호 재설정

* **Q: 암호 재설정에서 전자 메일, SMS 또는 전화 통화를 받으려면 얼마나 오래 대기해야 합니까?**

  > **A:** 전자 메일, SMS 메시지 및 전화 통화는 1초 미만이며, 일반적인 경우 5-20초 대기해야 합니다.
    >이 시간 내에 알림을 수신하지 않은 경우 다음을 수행합니다.
        > * 정크 메일 폴더를 확인합니다.
        > * 연락 중인 전자 메일의 수가 예상대로인지를 확인합니다.
        > * 디렉터리의 인증 데이터 형식이 정확한지를 확인합니다.
                >     * 예: "+1 4255551234" 또는 "user@contoso.com"
  >
  >
* **Q: 암호 재설정에서 지원되는 언어는 무엇입니까?**

  > **A:** 암호 재설정 UI, SMS 메시지 및 음성 통화는 Office 365에서 지원되는 동일한 언어로 지역화됩니다.
  >
  >
* **Q: 내 디렉터리의 구성 탭에서 조직 브랜드를 설정하는 경우 암호 재설정의 어느 부분에서 브랜드를 설정합니까?**

  > **A:** 암호 재설정 포털은 조직 로고를 표시하며 사용자 지정 전자 메일 또는 URL을 가리키는 관리자에게 문의 링크를 구성할 수도 있습니다. 암호 재설정에서 보낸 전자 메일에는 조직의 로고, 색, 전자 메일 본문의 이름 및 이름으로 사용자 지정이 포함됩니다.
  >
  >
* **Q: 암호 재설정으로 이동할 수 있는 위치에 대해 사용자에게 어떻게 교육할 수 있습니까?**

  > **A:** 의 제안 사항을 중 일부를 시도 우리의 [SSPR 배포 문서](active-directory-passwords-best-practices.md#email-based-rollout)
  >
  >
* **Q: 모바일 장치에서 이 페이지를 사용할 수 있습니까?**

  > **A:** 예, 이 페이지는 모바일 장치에서 작동합니다.
  >
  >
* **Q: 사용자가 암호를 재설정할 때 로컬 활성 디렉터리 계정 잠금해제를 지원합니까?**

  > **A:** 예, 사용자가 자신의 암호를 재설정하고 Azure AD Connect를 사용하여 비밀번호 쓰기 저장을 배포한 경우 암호를 재설정할 때 해당 사용자의 계정은 자동으로 잠금이 해제됩니다.
  >
  >
* **Q: 암호 재설정을 내 사용자의 데스크톱 로그인 환경으로 직접 통합하려면 어떻게 해야 합니까?**

  > **A:** 사용자가 Azure AD Premium 고객인 경우, 추가 비용 없이 Microsoft Identity Manager를 설치하고 온-프레미스 암호 재설정 솔루션을 배포하여 이 요구 사항을 충족할 수 있습니다.
  >
  >
* **Q: 서로 다른 로캘로 다른 보안 질문을 설정할 수 있습니까?**

  > **A:** 현재 불가능합니다.
  >
  >
* **Q: 보안 질문 인증 옵션으로 얼마나 많은 질문을 구성할 수 있습니까?**

  > **A:**[Azure Portal](https://portal.azure.com)에서 최대 20개의 사용자 지정 보안 질문을 구성할 수 있습니다.
  >
  >
* **Q: 질문의 길이는 어떻게 설정할 수 있습니까?**

  > **A:** 보안 질문은 3자에서 200자 사이일 수 있습니다.
  >
  >
* **Q: 보안 질문에 대한 답변의 길이는 어떻게 설정할 수 있습니까?**

  > **A:** 답변은 3자에서 40자 사이일 수 있습니다.
  >
  >
* **Q: 보안 질문에 대해 중복 답변은 거부됩니까?**

  > **A:** 예, 보안 질문에 대한 중복 답변은 거부됩니다.
  >
  >
* **Q: 사용자는 동일한 보안 질문을 두 번 이상 등록할 수 있나요?**

  > **A:** 아니요, 사용자가 특정 질문을 등록하면 해당 질문을 두 번 등록할 수 없습니다.
  >
  >
* **Q:는 등록을 위한 보안 질문의 최소 제한을 설정할 수 있습니까?**

  > **A:** 예, 등록에 대해 하나의 제한, 재설정에 대해 또 하나의 제한을 설정할 수 있습니다. 3-5개의 보안 질문을 등록해야 하며 3-5개 질문은 재설정을 위해 필요할 수 있습니다.
  >
  >
* **Q: 사용자가 재설정에 대한 보안 질문을 사용해야 하도록 내 정책을 구성했지만 Azure 관리자는 다르게 구성할 것 같습니다.**

  > **A:** 이는 정상적인 동작입니다. Microsoft에서는 Azure 관리자 역할에 강력한 두 개의 기본 게이트 암호 재설정 정책을 적용합니다. 그러면 관리자가 보안 질문을 사용하지 않도록 설정합니다. 이 정책에 대한 자세한 내용은 [Azure Active Directory의 암호 정책 및 제한 사항](active-directory-passwords-policy.md#administrator-password-policy-differences) 문서에 있습니다.
  >
  >
* **Q: 사용자가 재설정에 필요한 질문의 최대 개수 보다 많은 질문을 등록한 경우, 재설정 중 보안 질문은 어떻게 선택됩니까?**

  > **A:** N개의 보안 질문이 사용자가 등록한 전체 질문 개수에서 임의로 선택되며, 여기서 N은 **재설정에 필요한 질문 개수**입니다. 예를 들어, 사용자가 5개의 보안 질문을 등록했지만 3개만 재설정에 필요한 경우, 5개 중 3개가 임의로 선택되며 재설정 시 표시됩니다. 사용자가 질문에 대답을 잘못하면, 선택 프로세스가 다시 시작되어 계속되는 질문을 방지합니다.
  >
  >
* **Q: 사용자가 짧은 기간 내에 여러 번 암호 재설정을 시도하지 못합니까?**

  > **A:** 예, 암호 재설정을 잘못 사용하지 않도록 기본 제공되는 몇 가지 보안 기능이 있습니다. 사용자가 24시간 동안 잠그기 전에 한 시간 내에 5번만 암호 재설정을 시도할 수 있습니다. 사용자가 24시간 동안 잠그기 전에 한 시간 내에 5번만 전화 번호의 유효성 검사를 시도할 수 있습니다. 사용자가 24시간 동안 잠그기 전에 한 시간 내에 5번만 단일 인증 방법을 시도할 수 있습니다.
  >
  >
* **Q: 얼마 동안 전자 메일 및 SMS 일회용 암호가 유효합니까?**

  > **A:** 암호 재설정을 위한 세션 수명은 15분입니다. 사용자에게는 암호 재설정 작업 시작부터 해당 암호를 재설정하는 데 15분이 주어집니다. 이 기간이 만료된 후 전자 메일 및 SMS 일회용 암호는 유효하지 않습니다.
  >
  >

## <a name="password-change"></a>암호 변경

* **Q: 내 사용자는 어디서 자신의 암호를 변경해야 합니까?**

  > **A:** 사용자는 자신의 [Office 365](https://portal.office.com) 또는 [액세스 패널](https://myapps.microsoft.com) 환경의 오른쪽 위 모서리와 같이 프로필 사진이나 아이콘이 표시된 곳이면 어디서나 자신의 암호를 변경할 수 있습니다. 사용자는 [액세스 패널 프로필 페이지](https://account.activedirectory.windowsazure.com/r#/profile)에서 자신의 암호를 변경할 수 있습니다. 또한 암호가 만료된 경우 사용자는 Azure AD 로그인 화면에서 자신의 암호를 변경하도록 자동으로 요청받을 수 있습니다. 마지막으로 자신의 암호를 변경하려는 경우 사용자는 [Azure AD 암호 변경 포털](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)로 직접 이동할 수 있습니다.
  >
  >
* **Q: 온-프레미스 암호가 만료되는 경우 Office 포털에서 내 사용자에게 알릴 수 있습니까?**

  > **A:** [ADFS로 암호 정책 클레임 보내기](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396)에 나와 있는 지침에 따라 ADFS를 사용하는 경우 가능합니다. 암호 해시 동기화를 사용하는 경우에는 불가능합니다. 이는 온-프레미스의 암호 정책을 동기화하지 않기 때문이며, 이에 따라 만료 알림을 클라우드 환경에 게시할 수 없습니다. 두 경우 모두 [PowerShell을 사용하여 암호가 만료될 사용자에게 알림](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx)(영문)을 보낼 수도 있습니다.
  >
  >

## <a name="password-management-reports"></a>암호 관리 보고서

* **Q: 데이터가 암호 관리 보고서를 표시하는 데 시간이 얼마나 소요됩니까?**

  > **A:** 데이터는 5~10분 내에 암호 관리 보고서를 표시됩니다. 최대 한 시간이 소요되는 경우도 있습니다.
  >
  >
* **Q: 어떻게 암호 관리 보고서를 필터링할 수 있습니까?**

  > **A:** 보고서 위쪽의 열 레이블 맨 오른쪽에 있는 작은 돋보기를 클릭하여 암호 관리 보고서를 필터링할 수 있습니다. 다양한 필터링을 원하는 경우, 보고서를 excel로 다운로드하고 피벗 테이블을 만들 수 있습니다.
  >
  >
* **Q: 암호 관리 보고서에 저장되는 최대 이벤트 수는 무엇입니까?**

  > **A:** 최대 75,000개의 암호 다시 설정 또는 암호 다시 설정 등록 이벤트가 암호 관리 보고서에 저장되며, 최대 30 일 동안 백업됩니다.  이 번호를 확장하여 더 많은 이벤트를 포함하도록 노력하고 있습니다.
  >
  >
* **Q: 암호 관리 보고서는 어디까지 표시할 수 있습니까?**

  > **A:** 암호 관리 보고서는 지난 30일 내에 발생한 작업을 표시합니다. 지금까지는, 이 데이터를 보관해야 하는 경우 보고서를 주기적으로 다운로드하여 별도 위치에 저장할 수 있습니다.
  >
  >
* **Q: 암호 관리 보고서에 최대 몇 행을 표시할 수 있습니까?**

  > **A:** 예, UI에 표시되거나 다운로드되는지 여부에 관계 없이 최대 75,000개 행이 암호 관리 보고서에 표시될 수 있습니다.
  >
  >
* **Q: 암호 재설정 또는 등록 보고 데이터에 액세스하는 API가 있습니까?**

  > **A:** 예, 데이터 스트림을 보고하는 암호 재설정에 액세스하는 방법에 대해 알아보려면 다음 설명서를 참조하세요.  [이벤트를 프로그래밍 방식으로 보고하는 암호 재설정에 액세스하는 방법을 알아봅니다](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>비밀번호 쓰기 저장

* **Q: 비밀번호 쓰기 저장은 배후에서 어떻게 작동합니까?**

  > **A:** 비밀번호 쓰기 저장을 사용하는 경우 및 시스템을 통해 온-프레미스 환경으로 다시 데이터가 흐르는 경우에 발생하는 일에 대한 설명은 [비밀번호 쓰기 저장 작동 원리](active-directory-passwords-writeback.md)를 참조하세요.
  >
  >
* **Q: 얼마 동안 비밀번호 쓰기 저장이 작동합니까?  암호 해시 동기화와 같이 동기화 지연이 있습니까?**

  > **A:** 비밀번호 쓰기 저장은 인스턴트입니다. 암호 해시 동기화와는 근본적으로 다르게 작동하는 동기 파이프라인입니다. 비밀번호 쓰기 저장을 사용하면 변경 작업 또는 해당 암호 재설정의 성공 여부에 대한 실시간 피드백을 받을 수 있습니다. 성공적인 쓰기 저장에 대한 평균 시간은 500밀리초 미만입니다.
  >
  >
* **Q: 온-프레미스 계정을 사용하지 않으면 클라우드 계정/액세스에 어떤 영향을 주나요?**

  > **A:** 온-프레미스 ID를 사용하지 않는 경우 AAD Connect를 통해 다음 동기화 간격(기본적으로 30분)에 클라우드 ID/액세스도 비활성화됩니다.
  >
  >
* **Q: 온-프레미스 계정이 온-프레미스 Active Directory 암호 정책에 의해 제한되는 경우 암호를 변경할 때 SSPR은 이 정책을 준수하나요?**

  > **A:** 예, SSPR은 일반적인 AD 도메인 암호 정책을 비롯한 온-프레미스 AD 암호 정책뿐만 아니라 지정된 사용자를 대상으로 서분화되어 있는 정의된 암호 정책에 따라 달라집니다.
  >
  >
* **Q: 비밀번호 쓰기 저장에 대해 어떤 유형의 계정이 작동합니까?**

  > **A:** 페더레이션 및 암호 해시 동기화된 사용자에 대한 비밀번호 쓰기 저장이 작동합니다.
  >
  >
* **Q: 비밀번호 쓰기 저장을 내 도메인 암호 정책에 적용합니까?**

  > **A:** 예, 비밀번호 쓰기 저장을 암호 사용 기간, 기록, 복잡성, 필터 및 로컬 도메인의 암호에 대해 시행할 다른 제한 사항에 적용합니다.
  >
  >
* **Q: 비밀번호 쓰기 저장은 안전합니까?  해킹을 당하지 않는다고 어떻게 확신할 수 있습니까?**

  > **A:** 예, 비밀번호 쓰기 저장은 안전합니다. 비밀번호 쓰기 저장 서비스에서 구현된 4개의 보안 계층 구현에 대한 자세한 내용은 비밀번호 쓰기 저장 작동 원리에서 [비밀번호 쓰기 저장 보안 모델](active-directory-passwords-writeback.md#password-writeback-security-model) 섹션을 확인합니다.
  >
  >

## <a name="next-steps"></a>다음 단계

* [성공적인 SSPR 롤아웃을 어떻게 완료합니까?](active-directory-passwords-best-practices.md)
* [암호 재설정 또는 변경](active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](active-directory-passwords-reset-register.md)
* [라이선스 관련 질문이 있습니까?](active-directory-passwords-licensing.md)
* [SSPR에서 사용하는 데이터는 무엇이며, 사용자에 대해 어떤 데이터를 채워야 합니까?](active-directory-passwords-data.md)
* [사용자가 사용할 수 있는 인증 방법은 무엇입니까?](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR에서 사용하는 정책 옵션은 무엇입니까?](active-directory-passwords-policy.md)
* [비밀번호 쓰기 저장은 무엇이며, 왜 관심을 가져야 합니까?](active-directory-passwords-writeback.md)
* [SSPR 작업은 어떻게 보고 합니까?](active-directory-passwords-reporting.md)
* [모든 SSPR 옵션과 그 의미는 무엇입니까?](active-directory-passwords-how-it-works.md)
* [무엇인가 손상된 문제가 있습니다. SSPR 문제는 어떻게 해결합니까?](active-directory-passwords-troubleshoot.md)
