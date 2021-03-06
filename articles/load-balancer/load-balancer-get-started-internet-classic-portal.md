---
title: "인터넷 연결 부하 분산 장치 만들기 - Azure 포털 클래식 | Microsoft Docs"
description: "Azure 클래식 포털을 사용하여 클래식 배포 모델에서 인터넷 연결 부하 분산 장치를 만드는 방법에 대해 알아봅니다."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fa3e93c0-968a-472d-a17c-65665c050db2
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: e07b6808f2401ac7b2b21e5f8816bac5a15b50b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Azure 클래식 포털에서 인터넷 연결 부하 분산 장치(클래식) 만들기 시작

> [!div class="op_single_selector"]
> * [Azure 클래식 포털](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure 클라우드 서비스](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Azure 리소스로 작업하기 전에 Azure에는 현재 Azure Resource Manager와 클래식 모드의 두 가지 배포 모델이 있다는 것을 이해해야 합니다. Azure 리소스로 작업하기 전에 [배포 모델 및 도구](../azure-classic-rm.md) 를 이해해야 합니다. 이 문서의 윗부분에 있는 탭을 클릭하여 다양한 도구에 대한 설명서를 볼 수 있습니다. 이 문서에서는 클래식 배포 모델에 대해 설명합니다. 또한 [Azure 리소스 관리자를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 배울 수 있습니다](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>가상 컴퓨터에 대한 인터넷 연결 부하 분산 장치 설정

인터넷에서 전송된 네트워크 트래픽을 클라우드 서비스의 가상 컴퓨터 간에 부하 분산하려면 부하 분산 집합을 만들어야 합니다. 이 절차에서는 가상 컴퓨터를 이미 만들었으며 모두 동일한 클라우드 서비스 내에 있다고 가정합니다.

**가상 컴퓨터에 대한 부하 분산 집합을 구성하려면**

1. Azure 클래식 포털에서 **가상 컴퓨터**를 클릭한 다음 부하 분산 집합에서 가상 컴퓨터의 이름을 클릭합니다.
2. **끝점**을 클릭한 후 **추가**를 클릭합니다.
3. **가상 컴퓨터에 끝점 추가** 페이지에서 오른쪽 화살표를 클릭합니다.
4. **끝점의 세부 정보를 지정하세요.** 페이지에서 다음을 수행합니다.

   * **이름**에 끝점 이름을 입력하거나 일반 프로토콜용으로 미리 정의된 끝점 목록에서 이름을 선택합니다.
   * **Protocol**에서 끝점 유형에 따라 필요한 프로토콜을 TCP 또는 UDP 중에 선택합니다.
   * **공용 포트 및 개인 포트**에 가상 컴퓨터에서 사용할 포트 번호를 필요에 따라 입력합니다. 가상 컴퓨터에서 개인 포트와 방화벽 규칙을 사용하여 응용 프로그램에 맞추어 트래픽을 리디렉션할 수 있습니다. 개인 포트는 공용 포트와 같게 설정해도 됩니다. 예를 들어 웹(HTTP) 트래픽의 끝점의 경우 공용 포트와 개인 포트 모두에 포트 80을 할당할 수 있습니다.

5. **Create a load-balanced set**를 선택한 후 오른쪽 화살표를 클릭합니다.
6. **부하 분산 집합 구성** 페이지에서 부하 분산 집합의 이름을 입력한 다음 Azure 부하 분산 장치 프로브 동작의 값을 할당합니다. 부하 분산 장치는 프로브를 사용하여 부하 분산 집합의 가상 컴퓨터가 들어오는 트래픽을 수신할 수 있는지 여부를 결정합니다.
7. 확인 표시를 클릭하여 부하 분산 끝점을 만듭니다. 가상 컴퓨터의 **끝점** 페이지에 있는 **부하 분산된 집합 이름** 열에 **예**가 표시됩니다.
8. 포털에서 **Virtual Machines**를 클릭하고 부하 분산 집합에서 추가 가상 컴퓨터의 이름을 클릭한 다음 **끝점**, **추가**를 차례로 클릭합니다.
9. **가상 컴퓨터에 끝점 추가** 페이지에서 **기존 부하 분산 집합에 끝점 추가**를 클릭하고 부하 분산 집합 이름을 선택한 다음 오른쪽 화살표를 클릭합니다.
10. **끝점의 세부 정보를 지정하세요.** 페이지에서 끝점 이름을 입력한 다음 확인 표시를 클릭합니다.

부하 분산 집합의 추가 가상 컴퓨터에 대해 8-10단계를 반복합니다.

## <a name="next-steps"></a>다음 단계

[내부 부하 분산 장치 구성 시작](load-balancer-get-started-ilb-arm-ps.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
