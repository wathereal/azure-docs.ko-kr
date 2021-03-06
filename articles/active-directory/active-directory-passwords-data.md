---
title: "Azure AD SSPR 데이터 요구 사항 | Microsoft Docs"
description: "Azure AD 셀프 서비스 암호 재설정의 데이터 요구 사항 및 충족 방법"
services: active-directory
keywords: 
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
ms.openlocfilehash: 7936f47007285e3f7fa1d3220efa022a6e3881ca
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>최종 사용자를 등록할 필요 없이 암호 재설정 배포

SSPR(셀프 서비스 암호 재설정)을 배포하려면 인증 데이터가 존재해야 합니다. 일부 조직에서는 해당 사용자가 해당 인증 데이터를 입력하도록 하지만 대부분의 조직에서는 Active Directory에서 기존 데이터와 동기화하는 것을 선호합니다. 온-프레미스 디렉터리에 적절한 형식의 데이터가 있고 [빠른 설정을 사용하여 Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md)를 구성하는 경우 사용자 조작 없이 Azure AD 및 SSPR에 해당 데이터를 사용할 수 있게 됩니다.

전화 번호가 제대로 작동하려면 +국가 코드 전화 번호 형식이어야 합니다(예: +1 4255551234).

> [!NOTE]
> 암호 재설정은 전화 번호 확장을 지원하지 않습니다. +1 4255551234X12345 형식에서도 전화를 걸지 전에 확장이 제거됩니다.

## <a name="fields-populated"></a>채워진 필드

Azure AD Connect에서 기본 설정을 사용하는 경우 다음 매핑이 수행됩니다.

| 온-프레미스 AD | Azure AD | Azure AD 인증 연락처 정보 |
| --- | --- | --- |
| telephoneNumber | 사무실 전화 | 대체 전화 |
| mobile | 휴대폰 | Phone |


## <a name="security-questions-and-answers"></a>보안 질문 및 답변

보안 질문 및 답변은 Azure AD 테넌트에 안전하게 저장되며 사용자가 [SSPR 등록 포털](https://aka.ms/ssprsetup)을 통해서만 액세스할 수 있습니다. 관리자는 다른 사용자의 질문 및 답변의 내용을 보거나 수정할 수 없습니다.

### <a name="what-happens-when-a-user-registers"></a>사용자가 등록하면 어떻게 되나요?

사용자가 등록하면 등록 페이지에서 다음 필드를 설정합니다.

* 인증 전화
* 인증 전자 메일
* 보안 질문 및 답변

**휴대폰** 또는 **대체 전자 메일**에 대한 값을 제공하면, 서비스에 등록하지 않은 사용자도 해당 값을 사용하여 자신의 암호를 즉시 재설정할 수 있습니다. 또한 사용자는 처음으로 등록할 때 해당 값을 볼 수 있고, 원하는 경우에는 값을 변경할 수 있습니다. 등록을 완료한 후에는 **인증 전화** 및 **인증 전자 메일** 필드에 해당하는 값이 유지됩니다.

## <a name="set-and-read-authentication-data-using-powershell"></a>PowerShell을 사용하여 인증 데이터 설정 및 읽기

PowerShell을 사용하여 다음 필드를 설정할 수 있습니다.

* 대체 전자 메일
* 휴대폰
* 사무실 전화 - 온-프레미스 디렉터리와 동기화하지 않는 경우에만 설정할 수 있습니다.

### <a name="using-powershell-v1"></a>PowerShell V1 사용

시작하려면 [Azure AD PowerShell 모듈을 다운로드하고 설치](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)해야 합니다. 설치를 완료한 후에는 다음 단계에 따라서 각 필드를 구성합니다.

#### <a name="set-authentication-data-with-powershell-v1"></a>PowerShell V1을 사용하여 인증 데이터 설정

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-authentication-data-with-powershellpowershell-v1"></a>PowerShell V1을 사용하여 인증 데이터 읽기

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="authentication-phone-and-authentication-email-can-only-be-read-using-powershell-v1-using-the-commands-that-follow"></a>다음과 같은 명령을 사용하는 PowerShell V1을 사용해야만 인증 전화 및 인증 전자 메일을 읽을 수 있습니다.

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="using-powershell-v2"></a>PowerShell V2 사용

시작하려면 [Azure AD V2 PowerShell 모듈을 다운로드하고 설치](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md)해야 합니다. 설치를 완료한 후에는 다음 단계에 따라서 각 필드를 구성합니다.

Install-Module을 지원하는 최신 버전의 PowerShell을 신속하게 설치하려면 다음 명령을 실행합니다(첫 번째 줄은 해당 항목이 이미 설치되어 있는지 확인함).

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-authentication-data-with-powershell-v2"></a>PowerShell V2를 사용하여 인증 데이터 설정

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

### <a name="read-authentication-data-with-powershell-v2"></a>PowerShell V2를 사용하여 인증 데이터 읽기

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>다음 단계

* [성공적인 SSPR 롤아웃을 어떻게 완료합니까?](active-directory-passwords-best-practices.md)
* [암호 재설정 또는 변경](active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](active-directory-passwords-reset-register.md)
* [라이선스 관련 질문이 있습니까?](active-directory-passwords-licensing.md)
* [사용자가 사용할 수 있는 인증 방법은 무엇입니까?](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR에서 사용하는 정책 옵션은 무엇입니까?](active-directory-passwords-policy.md)
* [비밀번호 쓰기 저장은 무엇이며, 왜 관심을 가져야 합니까?](active-directory-passwords-writeback.md)
* [SSPR 작업은 어떻게 보고 합니까?](active-directory-passwords-reporting.md)
* [모든 SSPR 옵션과 그 의미는 무엇입니까?](active-directory-passwords-how-it-works.md)
* [무엇인가 손상된 문제가 있습니다. SSPR 문제는 어떻게 해결합니까?](active-directory-passwords-troubleshoot.md)
* [다른 곳에서 다루지 않았던 질문이 있습니다.](active-directory-passwords-faq.md)
