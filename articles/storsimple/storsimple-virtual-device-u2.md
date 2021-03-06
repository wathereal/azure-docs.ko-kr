---
title: "StorSimple 가상 장치 업데이트 2 | Microsoft Docs"
description: "Microsoft Azure 가상 네트워크에서 StorSimple 가상 장치를 만들고 배포 및 관리하는 방법을 알아봅니다. StorSimple 업데이트 2에 적용됩니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: f37752a5-cd0c-479b-bef2-ac2c724bcc37
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/07/2017
ms.author: alkohli
ms.openlocfilehash: 29f33d01cc6b640a566dc371f4b9c704978da091
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2017
---
# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Azure에서 StorSimple 가상 장치 배포 및 관리
## <a name="overview"></a>개요
StorSimple 8000 시리즈 가상 장치는 Microsoft Azure StorSimple 솔루션과 함께 제공되는 추가 기능입니다. StorSimple 가상 장치는 Microsoft Azure 가상 네트워크의 가상 컴퓨터에서 실행되며, 이 장치를 사용하여 호스트에서 데이터를 백업하고 복제할 수 있습니다. 이 자습서는 Azure에서 가상 장치를 배포하고 관리하는 방법을 설명하고 소프트웨어 버전 업데이트 2 이하를 실행하는 모든 가상 장치에 적용할 수 있습니다.

#### <a name="virtual-device-model-comparison"></a>가상 장치 모델 비교
StorSimple 가상 장치 모델은 두 가지 모델 즉, 표준 8010(이전의 1100) 및 프리미엄 8020(업데이트 2에 도입된)으로 사용할 수 있습니다. 두 가지 모델에 대한 비교가 아래 표로 정리되어 있습니다.

| 장치 모델 | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **최대 용량** |30TB |64TB |
| **Azure VM** |Standard_A3(4 코어, 7GB 메모리) |Standard_DS3 (4 코어, 14GB 메모리) |
| **버전 호환성** |사전 업데이트 2 이상을 실행하는 버전 |업데이트 2 이상을 실행하는 버전 |
| **지역 가용성** |모든 Azure 지역 |Premium Storage 및 DS3 Azure VM을 지원하는 모든 Azure 지역<br></br> [이 목록](https://azure.microsoft.com/en-us/regions/services)을 사용하여 *Virtual Machines > DS 시리즈* 및 *저장소 > 디스크 저장소*를 자신의 지역에서 사용할 수 있습니다. |
| **저장소 유형** |로컬 디스크에 Azure Standard Storage 사용<br></br> [Standard Storage 계정을 만드는](../storage/common/storage-create-storage-account.md) |로컬 디스크용 Azure Premium Storage 사용<sup>2</sup> <br></br>[Premium Storage 계정을 만드는](../virtual-machines/windows/premium-storage.md) |
| **워크로드 지침** |백업으로부터 항목 수준 파일 읽어오기 |클라우드 개발 및 테스트 시나리오, 짧은 대기 시간, 높은 성능 워크로드  <br></br>재해 복구용 보조 장치 |

<sup>1</sup> *이전에 1100로 알려짐*.

<sup>2</sup>*8010와 8020은 모두 클라우드 계층에 Azure Standard Storage를 사용합니다. 차이점은 장치* 내의 로컬 계층에만 존재한다는 것입니다.

이 문서는 Azure에서 StorSimple 가상 장치를 배포하는 단계별 프로세스를 설명합니다. 이 문서를 읽고 나면:

* 가상 장치와 물리적 장치의 차이를 이해하게 됩니다.
* 가상 장치를 만들고 구성할 수 있게 됩니다.
* 가상 장치에 연결합니다.
* 가상 장치로 작업하는 방법을 알아봅니다.

이 자습서는 업데이트 2 이상을 실행하는 모든 StorSimple 가상 장치에 적용됩니다.

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>가상 장치와 물리적 장치의 차이
StorSimple 가상 장치는 Microsoft Azure 가상 컴퓨터의 단일 노드에서 실행되는 StorSimple의 소프트웨어 전용 버전입니다. 가상 장치는 물리적 장치를 사용할 수 없는 재해 복구 시나리오를 지원하며, 백업으로부터 항목 수준 읽어오기, 온-프레미스 재해 복구, 클라우드 개발 및 테스트 시나리오에 사용하기에 적합합니다.

#### <a name="differences-from-the-physical-device"></a>물리적 장치와의 차이점
다음 테이블은 StorSimple 가상 장치와 StorSimple 물리적 장치 간의 몇 가지 주요 차이점을 보여줍니다

|  | 물리적 장치 | 가상 장치 |
| --- | --- | --- |
| **위치** |데이터 센터에 상주합니다. |Azure에서 실행됩니다. |
| **네트워크 인터페이스** |네트워크 인터페이스가 여섯 개(DATA 0에서 DATA 5까지 ) 있습니다. |네트워크 인터페이스가 하나만(DATA 0) 있습니다. |
| **등록** |구성 단계 중에 등록됩니다. |등록은 별도의 작업입니다. |
| **서비스 데이터 암호화 키** |물리적 장치에서 다시 생성된 후 새 키로 가상 장치를 업데이트합니다. |가상 장치에서 다시 생성할 수 없습니다. |

## <a name="prerequisites-for-the-virtual-device"></a>가상 장치에 대한 필수 구성 요소
다음 섹션에서는 StorSimple 가상 장치에 대한 구성 필수 구성 요소를 설명합니다. 가상 장치를 배포하기 전에 [가상 장치를 사용하기 위한 보안 고려 사항](storsimple-security.md)을 참조하세요.

#### <a name="azure-requirements"></a>Azure 요구 사항
가상 장치를 프로비전하기 전에 Azure 환경에서 다음 준비를 확인해야 합니다.

* 가상 장치의 경우, [Azure에서 가상 네트워크를 구성합니다](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Premium Storage를 사용하는 경우 Premium Storage를 지원하는 Azure 지역에 가상 네트워크를 만들어야 합니다. Premium Storage 지역은 [지역별 Azure 서비스](https://azure.microsoft.com/en-us/regions/services) 목록에서 *디스크 저장소*의 행에 해당하는 지역입니다.
* 사용자 고유의 DNS 서버 이름을 지정하는 대신 Azure에서 제공하는 기본 DNS 서버를 사용하는 것이 좋습니다. DNS 서버 이름이 유효하지 않거나 DNS 서버가 IP 주소를 제대로 확인할 수 없으면 가상 장치 만들기에 실패합니다.
* 지점 대 사이트간 및 사이트 대 사이트는 선택적이지만 필수는 아닙니다. 원하는 경우, 고급 시나리오에 대해 이 옵션을 구성할 수 있습니다.
* 가상 장치에 표시된 볼륨을 사용할 수 있는 [Azure Virtual Machines](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (호스트 서버)를 가상 네트워크에 만들 수 있습니다. 이 서버는 다음 요구 사항을 충족해야 합니다.                             

  * iSCSI 초기자 소프트웨어가 설치된 Windows 또는 Linux VM입니다.
  * 가상 장치와 동일한 가상 네트워크에서 실행 중입니다.
  * 가상 장치의 내부 IP 주소를 통해 가상 장치의 iSCSI 대상에 연결할 수 없습니다.
* 동일한 가상 네트워크에서 iSCSI 및 클라우드 트래픽에 대한 지원을 구성했는지 확인합니다.

#### <a name="storsimple-requirements"></a>StorSimple 요구 사항
가상 장치를 만들기 전에 Azure StorSimple 서비스에 대한 다음 업데이트를 확인합니다.

* 가상 장치에 대해 호스트 서버가 될 VM에 대해 [액세스 제어 레코드](storsimple-manage-acrs.md) 를 추가합니다.
* 가상 장치와 동일한 지역에 [저장소 계정](storsimple-manage-storage-accounts.md#add-a-storage-account) 을 사용합니다. 다른 영역의 Storage 계정으로 성능이 저하될 수 있습니다. 가상 장치에 표준 또는 Premium Storage 계정을 사용할 수 있습니다. 자세한 내용은 [Standard Storage 계정](../storage/common/storage-create-storage-account.md) 또는 [Premium Storage 계정](../virtual-machines/windows/premium-storage.md)을 만드는 방법을 참조하세요.
* 데이터에 사용된 계정이 아닌 가상 장치 생성을 위해 다른 저장소 계정을 사용합니다. 동일한 저장소 계정을 사용하면 성능이 저하될 수 있습니다.

시작하기 전에 다음 정보가 있는지 확인합니다.

* 액세스 자격 증명이 있는 Azure 클래식 포털 계정
* 물리적 장치의 서비스 데이터 암호화 키의 복사본

## <a name="create-and-configure-the-virtual-device"></a>가상 장치 만들기 및 구성
이러한 절차를 수행하기 전에 [가상 장치에 대한 필수 구성 요소](#prerequisites-for-the-virtual-device)를 충족하는지 확인합니다.

가상 네트워크를 만들고, StorSimple 관리자 서비스를 구성하고 해당 서비스로 물리적 StorSimple 장치를 등록한 후, 다음 단계에 따라 StorSimple 가상 장치를 만들고 구성할 수 있습니다.

### <a name="step-1-create-a-virtual-device"></a>1단계: 가상 장치 만들기
다음 단계에 따라 StorSimple 가상 장치를 만듭니다.

[!INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

이 단계에서 가상 장치 만들기에 실패하는 경우 인터넷에 연결되지 않을 수 있습니다. 자세한 내용은 가상 장치를 만들 때 [인터넷 연결 오류 문제 해결](#troubleshoot-internet-connectivity-errors)로 이동하세요.

### <a name="step-2-configure-and-register-the-virtual-device"></a>2단계: 가상 장치 구성 및 등록
이 절차를 시작하기 전에 서비스 데이터 암호화 키의 복사본을 가지고 있는지 확인합니다. 첫번째 StorSimple 장치를 구성하고 안전한 위치에 저장하도록 명령한 경우, 서비스 데이터 암호화 키가 만들어집니다. 서비스 데이터 암호화 키의 복사본이 없는 경우 Microsoft 지원에 문의해야 합니다.

다음 단계에 따라 StorSimple 가상 장치를 구성하고 등록합니다.

[!INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>3단계: (선택 사항) 장치 구성 설정 수정
다음 섹션에서는 CHAP 또는 StorSimple 스냅숏 관리자를 사용하거나 장치 관리자 암호를 변경하려는 경우 StorSimple 가상 장치에 필요한 장치 구성 설정을 설명합니다.

#### <a name="configure-the-chap-initiator"></a>CHAP 시작자 구성
이 매개 변수는 볼륨 액세스를 시도 중인 초기자(서버)에서 가상 장치(대상)가 예상하는 자격 증명을 포함합니다. 초기자는 CHAP 사용자 이름 및 CHAP 암호를 제공하여 인증 중 장치를 자체적으로 식별합니다. 자세한 단계를 보려면 [장치에 CHAP 구성](storsimple-configure-chap.md#unidirectional-or-one-way-authentication)으로 이동합니다.

#### <a name="configure-the-chap-target"></a>CHAP 대상 구성
이 매개 변수는 CHAP 지원 초기자가 상호 또는 양방향 인증을 요청하면 가상 장치를 사용하는 자격 증명을 포함합니다. 가상 장치는 이 인증 프로세스 중 시작자에게 자체 식별을 위해 역방향 CHAP 사용자 이름 및 역방향 CHAP 암호를 사용합니다. CHAP 대상 설정은 전역 설정입니다. 이 설정을 적용하면 저장소 가상 장치에 연결된 모든 볼륨이 CHAP 인증을 사용합니다. 자세한 단계를 보려면 [장치에 CHAP 구성](storsimple-configure-chap.md#bidirectional-or-mutual-authentication)으로 이동합니다.

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>StorSimple 스냅숏 관리자 암호 구성
StorSimple 스냅숏 관리자 소프트웨어는 Windows 호스트에 상주하며 관리자가 로컬 및 클라우드 스냅숏의 형태로 StorSimple 장치의 백업을 관리할 수 있습니다.

> [!NOTE]
> 가상 장치의 경우, Windows 호스트는 Azure 가상 컴퓨터입니다.
>
>

StorSimple 스냅숏 관리자에서 장치를 구성하면, StorSimple 장치 IP 주소 및 암호를 입력하여 저장소 장치를 인증하라는 메시지가 표시됩니다. 자세한 단계를 보려면 [StorSimple 스냅숏 관리자 암호 구성](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)으로 이동합니다.

#### <a name="change-the-device-administrator-password"></a>장치 관리자 암호 구성
Windows PowerShell 인터페이스를 사용하여 가상 장치에 액세스할 때 장치 관리자 암호를 입력해야 합니다. 데이터의 보안을 위해, 가상 장치를 사용하려면 먼저 이 암호를 변경해야 합니다. 자세한 단계를 보려면 [장치 관리자 암호 구성](storsimple-change-passwords.md#change-the-device-administrator-password)으로 이동합니다.

## <a name="connect-remotely-to-the-virtual-device"></a>가상 장치에 원격으로 연결
Windows PowerShell 인터페이스를 통해 가상 장치에 대한 원격 액세스를 기본적으로 사용할 수 없습니다. 먼저, 가상 장치에서 원격 관리를 사용한 다음 가상 장치에 액세스하는데 사용할 클라이언트에서 사용하도록 설정해야 합니다.

원격으로 연결하는 두 단계 프로세스가 아래 설명되어 있습니다.

### <a name="step-1-configure-remote-management"></a>1단계: 원격 관리 구성
다음 단계에 따라 StorSimple 가상 장치에 대한 원격 관리를 구성합니다.

[!INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>2단계: 가상 장치에 원격으로 액세스
StorSimple 장치 구성 페이지에서 원격 관리를 활성화한 후, Windows PowerShell 원격을 사용하여 동일한 가상 네트워크 내 다른 가상 컴퓨터에서 가상 장치에 연결할 수 있습니다. 예를 들어, iSCSI에 연결하도록 구성되고 사용된 호스트 VM에서 연결할 수 있습니다. 대부분의 배포에서는 가상 장치에 액세스하기 위해 사용할 수 있는 호스트 VM에 액세스하기 위해 이미 공용 끝점이 열려 있습니다.

> [!WARNING]
> **보안 강화를 위해 끝점에 연결할 때 HTTPS를 사용하고 PowerShell 원격 세션을 완료한 후 끝점을 삭제하는 것이 좋습니다.**
>
>

[StorSimple 장치에 원격으로 연결](storsimple-remote-connect.md) 의 절차에 따라 가상 장치에 대한 원격 서비스를 설정해야 합니다.

## <a name="connect-directly-to-the-virtual-device"></a>가상 장치에 직접 연결
가상 장치에 직접 연결할 수도 있습니다. Microsoft Azure 환경 외부 또는 가상 네트워크 외부의 다른 컴퓨터에서 가상 장치에 직접 연결하려는 경우 다음 절차에서 설명한 대로 추가 끝점을 만들어야 합니다.

가상 장치에 공용 끝점을 만들려면 다음 단계를 수행합니다.

[!INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

가상 네트워크의 공용 끝점의 수를 최소화하기 때문에 동일한 가상 네트워크 내 다른 가상 컴퓨터에서 연결하는 것이 좋습니다. 이 메서드를 사용하면, 원격 데스크톱 세션을 통해 연결한 다음 로컬 네트워크의 다른 Windows 클라이언트와 같이 가상 컴퓨터를 사용하도록 구성하면 됩니다. 포트를 이미 알 수 있으므로 공용 포트 번호를 추가할 필요가 없습니다.

## <a name="work-with-the-storsimple-virtual-device"></a>StorSimple 가상 장치 작업
StorSimple 가상 장치를 만들고 구성했으므로 작업을 시작할 준비가 되었습니다. 물리적 StorSimple 장치에서와 같이 가상 장치에서 볼륨 컨테이너, 볼륨 및 백업 정책을 작업할 수 있습니다. 유일한 차이점은 장치 목록에서 가상 장치를 선택했는지 확인해야 한다는 것입니다. 가상 장치에 대한 다양한 관리 작업의 단계별 절차는 [StorSimple 관리자 서비스를 사용하여 가상 장치 관리](storsimple-manager-service-administration.md)를 참조하세요.

다음 섹션에서는 가상 장치를 작업할 때 발생하는 차이점 중 일부에 대해 설명합니다.

### <a name="maintain-a-storsimple-virtual-device"></a>StorSimple 가상 장치 유지 관리
소프트웨어 전용 장치이기 때문에 물리적 장치에 대한 유지 관리와 비교할 때 가상 장치의 유지 관리는 최소입니다. 다음 옵션이 있습니다.

* **소프트웨어 업데이트** – 업데이트 상태 메시지와 함께 소프트웨어가 마지막으로 업데이트된 날짜를 볼 수 있습니다. 새 업데이트를 확인하려는 경우 수동 검사를 수행하려면 페이지 맨 아래에 있는 **업데이트 검색** 단추를 사용할 수 있습니다. 업데이트를 사용할 수 있는 경우 **업데이트 설치** 를 클릭하여 설치합니다. 가상 장치에는 단일 인터페이스만 있기 때문에 업데이트 적용 시 약간의 서비스가 중단됨을 의미합니다. 가상 장치가 자동으로 종료되고 다시 시작되어(필요한 경우) 출시된 업데이트를 적용합니다. 단계별 절차를 보려면 [장치 업데이트](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal)로 이동하세요.
* **지원 패키지** – 지원 패키지를 만들고 업데이트하여 Microsoft 지원이 가상 장치의 문제를 해결할 수 있도록 합니다. 단계별 절차를 보려면 [지원 패키지 만들기 및 관리](storsimple-create-manage-support-package.md)로 이동하세요.

### <a name="storage-accounts-for-a-virtual-device"></a>가상 장치에 대한 Storage 계정
Storage 계정은 StorSimple Manager 서비스, 가상 장치 및 물리적 장치에서 사용하기 위해 만듭니다. 저장소 계정을 만들면 영역은 모든 시스템 구성 요소 전체에 걸쳐 일관성을 보장 하도록 친숙한 이름의 지역 식별자를 사용하는 것이 좋습니다. 가상 장치의 경우, 성능 문제를 방지하도록 모든 구성 요소가 동일한 영역에 있는 것이 중요합니다.

단계별 절차를 보려면 [저장소 계정 추가](storsimple-manage-storage-accounts.md#add-a-storage-account)로 이동하세요.

### <a name="deactivate-a-storsimple-virtual-device"></a>StorSimple 가상 장치 비활성화
가상 장치를 비활성화하면 프로비전 시 만들어진 VM 및 리소스를 삭제합니다. 가상 장치가 비활성화된 후 이전 상태로 복원할 수 없습니다. 가상 장치를 비활성화하기 전에 가상 장치에 의존하는 클라이언트와 호스트를 중지하거나 삭제했는지 확인합니다.

가상 장치를 비활성화하면 다음 작업이 수행됩니다.

* 가상 장치가 제거됩니다.
* OS 디스크 및 가상 장치에 대해 만든 데이터 디스크가 제거됩니다.
* 프로비전 중 호스티드 서비스 및 가상 네트워크가 유지됩니다. 사용하지 않는 경우 수동으로 삭제해야 합니다.
* 가상 장치에 대해 생성된 클라우드 스냅숏이 보존됩니다.

단계별 절차는 [StorSimple 장치 비활성화 및 삭제](storsimple-deactivate-and-delete-device.md)를 참조하세요.

장치가 StorSimple 관리자 서비스 페이지에서 비활성화로 표시되는 즉시, **장치** 페이지의 장치 목록에서 가상 장치를 삭제할 수 있습니다.

### <a name="start-stop-and-restart-a-virtual-device"></a>가상 장치 시작, 중지 및 다시 시작
StorSimple 물리적 장치와 달리, StorSimple 가상 장치에서 누를 전원 켜짐 또는 꺼짐 단추가 없습니다. 그러나 가상 장치를 중지하고 다시 시작해야 하는 경우가 있을 수 있습니다. 예를 들어, 일부 업데이트에서는 VM을 다시 시작해야 업데이트 프로세스를 완료할 수 있습니다. 가상 장치를 가장 쉽게 시작, 중지 및 다시 시작할 수 있는 방법은 Virtual Machines 관리 콘솔을 사용하는 것입니다.

관리 콘솔을 보면 가상 장치가 생성된 후 시작되기 때문에 상태는 **실행 중** 입니다. 언제든지 가상 컴퓨터를 시작하고 중지하고 다시 시작할 수 있습니다.

[!INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>공장 기본값으로 재설정
가상 장치로 다시 시작하려는 지를 결정한 경우 비활성화하고 삭제한 다음 새 가상 장치를 만들면 됩니다. 물리적 장치를 재설정할 때와 마찬가지로, 새 가상 장치에는 설치된 업데이트가 없으므로 사용하기 전에 업데이트를 확인해야 합니다.

## <a name="fail-over-to-the-virtual-device"></a>가상 장치에 대한 장애 조치
DR(재해 복구)는 StorSimple 가상 장치가 설계된 주요 시나리오 중 하나입니다. 이 시나리오에서는 물리적 StorSimple 장치 또는 전체 데이터 센터를 사용하지 못할 수 있습니다. 다행스럽게도 가상 장치를 사용하여 다른 위치에서 작업을 복원할 수 있습니다. DR 중, 원본 장치의 볼륨 컨테이너는 소유권을 변경하고 가상 장치로 전송됩니다. DR에 대한 필수 구성 요소는 가상 장치가 작성되고 구성되었는지, 볼륨 컨테이너 내의 모든 볼륨을 오프라인으로 전환했는지 및 볼륨 컨테이너에 연결된 클라우드 스냅숏이 있는지 입니다.

> [!NOTE]
> * DR에 대한 보조 장치로 가상 장치를 사용하는 경우에는 8010에 30TB의 Standard Storage가 있고 8020에 64TB의 Premium Storage가 있다는 것을 기억합니다. 높은 용량의 8020 가상 장치가 DR 시나리오에 보다 적합할 수 있습니다.
> * 업데이트 2를 실행하는 장치에서 사전 업데이트 1 소프트웨어를 실행하는 장치로 장애 조치 또는 복제할 수 없습니다. 하지만 업데이트 2를 실행하는 장치에서 업데이트 1(1.1 또는 1.2)을 실행하는 장치로 장애 조치는 가능합니다.
>
>

단계별 절차를 보려면 [가상 장치로 장애 조치](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device)로 이동합니다.

## <a name="shut-down-or-delete-the-virtual-device"></a>가상 장치를 종료하거나 삭제합니다.
StorSimple 가상 장치를 이전에 구성하고 사용했지만 이제 용도에 맞게 계산 비용 발생을 중지하려는 경우, 가상 장치를 종료할 수 있습니다. 가상 장치를 종료해도 저장소에서 해당 운영 체제 또는 데이터 디스크를 삭제하지 않습니다. 구독으로 발생하는 요금을 중지하지만 OS 및 데이터 디스크에 대한 저장소 비용은 계속 청구됩니다.

가상 장치를 삭제하거나 종료하는 경우 StorSimple Manager 서비스의 장치 페이지에 **오프라인** 으로 표시됩니다. 가상 장치에서 만든 백업도 삭제하려는 경우, 장치를 삭제하거나 비활성화하도록 선택할 수 있습니다. 자세한 내용은 [StorSimple 장치 비활성화 및 삭제](storsimple-deactivate-and-delete-device.md)를 참조하세요.

[!INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[!INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>인터넷 연결 오류 문제 해결
가상 장치를 만드는 동안 인터넷에 연결되지 않은 경우 생성 단계가 실패합니다. 이 오류가 인터넷 연결 때문에 발생하는 경우 문제를 해결하려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

1. Azure에서 Windows Server 2012 가상 컴퓨터를 만듭니다. 이 가상 컴퓨터는 가상 장치에서 사용한 동일한 저장소 계정, VNet 및 서브넷을 사용해야 합니다. Azure에서 동일한 저장소 계정, VNet 및 서브넷을 사용하는 기존 Windows Server 호스트가 이미 있는 경우 인터넷 연결 문제를 해결하는 데 사용할 수 있습니다.
2. 이전 단계에서 만든 가상 컴퓨터에 원격 로그인합니다.
3. 가상 컴퓨터 내의 명령 창을 엽니다(Win + R 및 `cmd`을 입력).
4. 프롬프트에서 다음 cmd를 실행합니다.

    `nslookup windows.net`
5. `nslookup` 이 실패하는 경우 인터넷 연결 실패로 인해 가상 장치를 StorSimple Manager 서비스에 등록하지 못하게 됩니다.
6. 가상 네트워크에 필요한 변경 사항을 수행하여 가상 장치가 "windows.net"과 같은 Azure 사이트에 액세스할 수 있도록 합니다.

## <a name="next-steps"></a>다음 단계
* [StorSimple Manager 서비스를 사용하여 가상 장치를 관리](storsimple-manager-service-administration.md)하는 방법을 알아봅니다.
* [백업 세트에서 StorSimple 볼륨을 복원](storsimple-restore-from-backup-set.md)하는 방법을 알아봅니다.
