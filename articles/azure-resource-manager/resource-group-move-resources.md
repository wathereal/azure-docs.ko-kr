---
title: "새 구독 또는 리소스 그룹으로 Azure 리소스 이동 | Microsoft Docs"
description: "Azure Resource Manager를 사용하여 리소스를 새 리소스 그룹 또는 구독으로 이동합니다."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: tomfitz
ms.openlocfilehash: 5a28914d967e77d6c8881cd6e56b798269d3df3e
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2017
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>새 리소스 그룹 또는 구독으로 리소스 이동

이 문서에서는 리소스를 새 구독 또는 동일한 구독의 새 리소스 그룹으로 이동하는 방법을 보여 줍니다. 포털, PowerShell, Azure CLI 또는 REST API를 사용하여 리소스를 이동할 수 있습니다. 이 문서의 이동 작업은 Azure 지원의 도움 없이 사용할 수 있습니다.

리소스를 이동할 때 원본 그룹과 대상 그룹은 모두 작업 중에 잠겨 있습니다. 쓰기 및 삭제 작업은 이동이 완료될 때까지 리소스 그룹에서 차단됩니다. 이 잠금은 리소스 그룹에서 리소스를 추가, 업데이트, 삭제할 수 없음을 의미하지만 리소스가 고정되었음을 의미하지는 않습니다. 예를 들어, SQL Server와 해당 데이터베이스를 새 리소스 그룹으로 이동하는 경우 해당 데이터베이스를 사용하는 응용 프로그램에는 가동 중지 시간이 발생하지 않습니다. 데이터베이스에 계속해서 읽고 쓸 수 있습니다.

리소스의 위치는 변경할 수 없습니다. 리소스를 이동할 때는 새 리소스 그룹으로만 이동됩니다. 새 리소스 그룹은 다른 위치를 가질 수 있지만 리소스의 위치는 변경되지 않습니다.

> [!NOTE]
> 이 문서에서는 기존 Azure 계정 제품 내에서 리소스를 이동하는 방법을 설명합니다. 기존 리소스를 계속 사용하면서 실제로 Azure 계정 제품을 변경하려는 경우(예: 종량제 요금에서 선불로 업그레이드) [Azure 구독을 다른 제품으로 전환](../billing/billing-how-to-switch-azure-offer.md)을 참조하세요.
>
>

## <a name="checklist-before-moving-resources"></a>리소스를 이동하기 전의 검사 목록

리소스를 이동하기 전에 몇 가지 중요한 단계가 있습니다. 이러한 조건을 확인하면 오류를 방지할 수 있습니다.

1. 원본 및 대상 구독은 동일한 [Azure Active Directory 테넌트](../active-directory/active-directory-howto-tenant.md) 내에 있어야 합니다. 두 구독이 모두 동일한 테넌트 ID를 갖는지 확인하려면 Azure PowerShell 또는 Azure CLI를 사용합니다.

  Azure PowerShell의 경우 다음을 사용합니다.

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  Azure CLI의 경우 

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  원본 및 대상 구독에 대한 테넌트 ID가 동일하지 않으면 [지원](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에 문의하여 리소스를 새 테넌트로 이동해야 합니다.

2. 서비스는 리소스 이동 기능을 사용하도록 설정해야 합니다. 이 문서에서는 리소스 이동이 가능한 서비스와 그렇지 않은 서비스를 나열합니다.
3. 이동되는 리소스의 리소스 공급자가 대상 구독에 등록되어야 합니다. 그러지 않으면 **구독이 리소스 형식에 대해 등록되지 않았음**을 알리는 오류 메시지가 표시됩니다. 해당 리소스 종류와 함께 사용된 적이 없는 새 구독으로 리소스를 이동할 때 이 문제가 발생할 수 있습니다.

  PowerShell의 경우 다음 명령을 사용하여 등록 상태를 가져옵니다.

  ```powershell
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  리소스 공급자를 등록하려면 다음을 사용합니다.

  ```powershell
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  Azure CLI의 경우 다음 명령을 사용하여 등록 상태를 가져옵니다.

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  리소스 공급자를 등록하려면 다음을 사용합니다.

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

## <a name="when-to-call-support"></a>지원을 호출해야 하는 경우

대부분의 리소스는 이 문서에 나오는 셀프 서비스 작업을 통해 이동할 수 있습니다. 다음에 대해 셀프 서비스 작업을 사용합니다.

* Resource Manager 리소스를 이동합니다.
* [클래식 배포 제한 사항](#classic-deployment-limitations)에 따라 클래식 리소스를 이동합니다.

다음을 수행해야 하는 경우 [지원](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에 문의하세요.

* 리소스를 새 Azure 계정(및 Azure Active Directory 테넌트)으로 이동합니다.
* 클래식 리소스를 이동하지만 제한 사항으로 문제가 발생합니다.

## <a name="services-that-enable-move"></a>이동을 사용하는 서비스

새 리소스 그룹 및 구독으로 이동할 수 있게 하는 서비스는 다음과 같습니다.

* API 관리
* 앱 서비스 앱(웹앱) - [앱 서비스 제한](#app-service-limitations)
* Application Insights
* Automation
* Azure Cosmos DB
* 배치
* Bing 지도
* CDN
* 클라우드 서비스 - [클래식 배포 제한 사항](#classic-deployment-limitations)
* Cognitive Services
* Content Moderator
* 데이터 카탈로그
* 데이터 팩터리
* 데이터 레이크 분석
* 데이터 레이크 저장소
* DNS
* Event Hubs
* HDInsight 클러스터 - [HDInsight 제한 사항](#hdinsight-limitations) 참조
* IoT Hub
* 키 자격 증명 모음
* 부하 분산 장치
* Logic Apps
* 기계 학습
* 미디어 서비스
* 모바일 고객 관리
* 알림 허브
* Operational Insights
* 운영 관리
* Power BI
* Redis 캐시
* 스케줄러
* 검색
* 서버 관리
* 서비스 버스
* 서비스 패브릭
* 저장소
* 저장소(클래식) - [클래식 배포 제한 사항](#classic-deployment-limitations)
* Stream Analytics - 실행 중 상태일 때는 Stream Analytics 작업을 이동할 수 없습니다.
* SQL Database 서버 - 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다.
* 트래픽 관리자
* Virtual Machines - 관리 디스크가 있는 VM은 이동할 수 없습니다. [Virtual Machines 제한 사항](#virtual-machines-limitations) 참조
* 가상 컴퓨터(클래식) - [클래식 배포 제한 사항](#classic-deployment-limitations)
* Virtual Machine Scale Sets - [Virtual Machines 제한 사항](#virtual-machines-limitations) 참조
* Virtual Networks - [Virtual Networks 제한 사항](#virtual-networks-limitations) 참조
* VPN 게이트웨이

## <a name="services-that-do-not-enable-move"></a>이동을 사용하지 않는 서비스

현재 리소스 이동을 사용하지 않는 서비스는 다음과 같습니다.

* AD Domain Services
* AD 하이브리드 상태 관리 서비스
* 응용 프로그램 게이트웨이
* BizTalk 서비스
* 컨테이너 서비스
* Express 경로
* DevTest Labs - 동일한 구독에서 새 리소스 그룹으로 이동이 가능하지만, 구독 간 이동은 사용 가능하지 않습니다.
* Dynamics LCS
* Managed Applications
* Managed Disks - [Virtual Machines 제한 사항](#virtual-machines-limitations) 참조
* Recovery Services 자격 증명 모음 - Recovery Services 자격 증명 모음과 연결된 Compute, Network 및 Storage 리소스도 이동하지 않습니다. [Recovery Services 제한 사항](#recovery-services-limitations)을 참조하세요.
* 보안
* StorSimple 장치 관리자
* 가상 네트가상 네트워크(클래식) - [클래식 배포 제한 사항](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Virtual Machines 제한 사항

관리 디스크는 이동을 지원하지 않습니다. 이 제한 사항은 여러 관련 리소스도 이동할 수 없음을 의미합니다. 이동할 수 없는 디스크:

* 관리 디스크
* 관리 디스크가 있는 가상 컴퓨터
* 관리 디스크에서 만든 이미지
* 관리 디스크에서 만든 스냅숏
* 관리 디스크가 있는 가상 컴퓨터가 포함된 가용성 집합

Marketplace 리소스에서 만든 가상 컴퓨터는 구독 간에 이동할 수 없습니다. 현재 구독의 가상 컴퓨터를 프로비전 해제하고 새 구독에 다시 배포합니다.

Key Vault에 저장된 인증서가 있는 Virtual Machines는 동일한 구독에서 새 리소스 그룹으로 이동할 수 있지만 구독 간에는 이동할 수 없습니다.

## <a name="virtual-networks-limitations"></a>Virtual Networks 제한 사항

피어링된 가상 네트워크를 이동하려면 먼저 가상 네트워크 피어링을 사용하지 않도록 설정해야 합니다. 사용하지 않도록 설정되면 가상 네트워크를 이동할 수 있습니다. 이동 후에는 가상 네트워크 피어링을 사용하도록 다시 설정합니다.

리소스 탐색 링크가 있는 서브넷이 가상 네트워크에 있는 경우 가상 네트워크를 다른 구독으로 이동할 수 없습니다. 예를 들어 Redis Cache 리소스가 서브넷에 배포된 경우 해당 서브넷에는 리소스 탐색 링크가 있습니다.

## <a name="app-service-limitations"></a>앱 서비스 제한

앱 서비스 앱으로 작업할 경우에는 앱 서비스 계획만 이동할 수 없습니다. 앱 서비스 앱을 이동할 때는 다음 옵션을 사용할 수 있습니다.

* 앱 서비스 계획과 해당 리소스 그룹에 있는 모든 기타 앱 서비스 리소스를 이미 앱 서비스 리소스가 없는 새 리소스 그룹으로 이동합니다. 이 요구 사항은 App Service 계획에 연결되지 않은 App Service 리소스도 이동해야 함을 의미합니다.
* 앱을 다른 리소스 그룹으로 이동하지만 모든 앱 서비스는 원래 리소스 그룹에 유지합니다.

App Service 계획은 제대로 기능하기 위해 해당 앱에 대해 앱과 같은 리소스 그룹에 상주하지 않아도 됩니다.

예를 들어 리소스 그룹에 다음 사항이 포함된 경우:

* **plan-a**와 연결된 **web-a**
* **plan-b**와 연결된 **web-b**

옵션은 다음과 같습니다.

* **web-a**, **plan-a**, **web-b** 및 **plan-b** 이동
* **web-a** 및 **web-b** 이동
* **web-a**
* **web-b**

다른 모든 조합에는 App Service 계획 이동 시 그대로 남겨 둘 수 없는 리소스 형식(App Service 리소스 형식)을 남겨두는 것이 있습니다.

웹앱이 해당 앱 서비스 계획과는 다른 리소스 그룹에 상주하지만 이 두 가지를 새로운 리소스 그룹으로 이동하려는 경우에는 두 단계로 이동을 수행해야 합니다. 예:

* **web-a**가 **web-group**에 상주하는 경우
* **plan-a**가 **plan-group**에 상주하는 경우
* **web-a** 및 **plan-a**를 **combined-group**에 상주시키려는 경우

이 이동을 수행하려면 다음 순서로 두 개의 별도 이동 작업을 수행하세요.

1. **web-a**를 **plan-group**으로 이동
2. **web-a** 및 **plan-a**를 **combined-group**으로 이동

아무런 문제 없이 App Service Certificate를 새 리소스 그룹 또는 구독으로 이동할 수 있습니다. 하지만 외부에서 구매하여 앱에 업로드한 SSL 인증서가 웹앱에 포함된 경우 웹앱을 이동하기 전에 해당 인증서를 삭제해야 합니다. 예를 들어, 다음 단계를 수행해야 합니다.

1. 웹앱에서 업로드한 인증서 삭제
2. 웹앱 이동
3. 웹앱에 인증서 업로드

## <a name="classic-deployment-limitations"></a>클래식 배포 제한 사항

구독 내 또는 새 구독으로 리소스를 이동할지 여부에 따라 클래식 모델을 통해 배포된 리소스의 이동 옵션은 다릅니다.

### <a name="same-subscription"></a>동일한 구독

한 리소스 그룹에서 같은 구독 내 다른 리소스 그룹으로 리소스를 이동할 경우 다음 제한 사항이 적용됩니다.

* 가상 네트워크(클래식)은 이동할 수 없습니다.
* 가상 컴퓨터(클래식)는 클라우드 서비스로 이동해야 합니다.
* 클라우드 서비스는 이동에 모든 가상 컴퓨터가 포함된 경우에만 이동할 수 있습니다.
* 한 번에 하나의 클라우드 서비스만 이동할 수 있습니다.
* 한 번에 하나의 저장소 계정(클래식)만 이동할 수 있습니다.
* 저장소 계정(클래식)은 가상 컴퓨터 또는 클라우드 서비스와 같은 작업으로 이동할 수 없습니다.

클래식 리소스를 동일한 구독 내의 새 리소스 그룹으로 이동하려면, [포털](#use-portal), [Azure PowerShell](#use-powershell), [Azure CLI](#use-azure-cli) 또는 [REST API](#use-rest-api)를 통해 표준 이동 작업을 사용합니다. Resource Manager 리소스 이동을 위해 사용하는 동일한 작업을 사용합니다.

### <a name="new-subscription"></a>새 구독

새 구독으로 리소스 이동 시 다음 제한 사항이 적용됩니다.

* 구독의 모든 클래식 리소스는 동일한 작업에서 이동해야 합니다.
* 대상 구독은 다른 어떠한 클래식 리소스도 포함할 수 없습니다.
* 이동은 클래식 이동에 대한 별도의 REST API를 통해서만 요청할 수 있습니다. 클래식 리소스를 새 구독으로 이동할 경우 표준 Resource Manager 이동 명령은 작동하지 않습니다.

클래식 리소스를 새 구독으로 이동하려면 클래식 리소스와 관련된 REST 작업을 사용합니다. REST를 사용하려면 다음 단계를 수행합니다.

1. 원본 구독이 구독 간 이동에 참여할 수 있는지 확인합니다. 다음 작업을 사용합니다.

  ```HTTP
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     요청 본문에 다음을 포함합니다.

  ```json
  {
    "role": "source"
  }
  ```

     유효성 검사 작업에 대한 응답은 다음 형식입니다.

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. 대상 구독이 구독 간 이동에 참여할 수 있는지 확인합니다. 다음 작업을 사용합니다.

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     요청 본문에 다음을 포함합니다.

  ```json
  {
    "role": "target"
  }
  ```

     응답이 원본 구독 유효성 검사와 동일한 형식입니다.
3. 두 구독이 유효성 검사를 통과하면 다음 작업으로 한 구독에서 다른 구독으로 모든 클래식 리소스를 이동합니다.

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    요청 본문에 다음을 포함합니다.

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

이 작업은 몇 분 정도 실행될 수 있습니다.

## <a name="recovery-services-limitations"></a>Recovery Services 제한 사항

Azure Site Recovery로 재해 복구를 설정하는 데 사용된 Storage, Network 또는 Compute 리소스에 대해서는 이동이 사용되지 않습니다.

예를 들어, 온-프레미스 컴퓨터에서 저장소 계정(Storage1)으로 복제를 설정했고 Azure에 장애 조치(failover) 후 가상 네트워크(Network1)에 연결된 가상 컴퓨터(VM1)로 보호되는 컴퓨터를 실행하려고 한다고 가정합니다. 같은 구독 내에 있거나 여러 구독에 있는 리소스 그룹에 대해 이러한 Azure 리소스(Storage1, VM1, Network1) 작업 중 어떠한 것도 이동할 수 없습니다.

## <a name="hdinsight-limitations"></a>HDInsight 제한 사항

새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC, 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 컴퓨터에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.

HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 저장소 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

## <a name="search-limitations"></a>검색 제한 사항

Search 리소스를 이동하여 한 번에 모두 다른 지역에 배치할 수 없습니다.
이러한 경우에는 개별적으로 이동해야 합니다.

## <a name="use-portal"></a>포털 사용

리소스를 이동하려면 해당 리소스가 포함된 리소스 그룹을 선택한 후 **이동** 단추를 선택합니다.

![리소스 이동](./media/resource-group-move-resources/select-move.png)

리소스를 새 리소스 그룹으로 이동할지 또는 새 구독으로 이동할지를 선택합니다.

이동할 리소스와 대상 리소스 그룹을 선택합니다. 이러한 리소스에 대해 스크립트를 업데이트해야 함을 승인하고 **확인**을 선택합니다. 이전 단계에서 구독 편집 아이콘을 선택한 경우 대상 구독도 선택해야 합니다.

![대상 선택](./media/resource-group-move-resources/select-destination.png)

**알림**에서 이동 작업이 실행 중임을 볼 수 있습니다.

![이동 상태 표시](./media/resource-group-move-resources/show-status.png)

완료되면 결과를 알려 줍니다.

![이동 결과 표시](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>PowerShell 사용

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 [Move-AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) 명령을 사용합니다. 다음 예제에서는 여러 리소스를 새 리소스 그룹으로 이동하는 방법을 보여 줍니다.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

새 구독으로 이동하려면 `DestinationSubscriptionId` 매개 변수 값을 포함합니다.

## <a name="use-azure-cli"></a>Azure CLI 사용

기존 리소스를 다른 리소스 그룹 또는 구독으로 이동하려면 [az resource move](/cli/azure/resource?view=azure-cli-latest#az_resource_move) 명령을 사용합니다. 이동할 리소스에 대한 리소스 ID를 제공합니다. 다음 예제에서는 여러 리소스를 새 리소스 그룹으로 이동하는 방법을 보여 줍니다. `--ids` 매개 변수에서 이동할 리소스 ID를 쉼표로 구분한 목록을 제공합니다.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

새 구독으로 이동하려면 `--destination-subscription-id` 매개 변수를 제공합니다.

## <a name="use-rest-api"></a>REST API 사용

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 다음을 실행합니다.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

요청 본문에서 대상 리소스 그룹 및 이동할 리소스를 지정합니다. 이동 REST 작업에 대한 자세한 내용은 [리소스 이동](/rest/api/resources/Resources/MoveResources)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 구독을 관리하기 위한 PowerShell cmdlet에 대한 자세한 내용은 [Resource Manager에서 Azure PowerShell 사용](powershell-azure-resource-manager.md)을 참조하세요.
* 구독을 관리하기 위한 Azure CLI 명령에 대한 자세한 내용은 [Resource Manager에서 Azure CLI 사용](xplat-cli-azure-resource-manager.md)을 참조하세요.
* 구독을 관리하기 위한 포털 기능에 대한 자세한 내용은 [Azure 포털을 사용하여 리소스 관리](resource-group-portal.md)를 참조하세요.
* 리소스를 논리적으로 구성하는 방법에 대한 자세한 내용은 [태그를 사용하여 리소스 구성](resource-group-using-tags.md)을 참조하세요.
