---
title: "Azure CLI를 사용하여 Resource Manager 템플릿 내보내기 | Microsoft Docs"
description: "Azure Resource Manager 및 Azure CLI를 사용하여 리소스 그룹에서 템플릿을 내보냅니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2017
ms.author: tomfitz
ms.openlocfilehash: 617664129a5353e25da1e90c742c4b009db172ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="export-azure-resource-manager-templates-with-azure-cli"></a>Azure CLI를 사용하여 Azure Resource Manager 템플릿 내보내기

Resource Manager를 사용하면 구독의 기존 리소스에서 Resource Manager 템플릿을 내보낼 수 있습니다. 생성된 템플릿을 사용하여 템플릿 구문에 대해 알아보거나 필요에 따라 솔루션 재배포를 자동화할 수 있습니다.

템플릿을 내보내려면 다음과 같은 두 가지 방법이 있습니다.

* 배포에 사용된 실제 템플릿을 내보낼 수 있습니다. 내보낸 템플릿은 원본 템플릿에 나타난 대로 모든 매개 변수 및 변수를 포함합니다. 이 방법은 템플릿을 검색해야 할 때 유용합니다.
* 리소스 그룹의 현재 상태를 나타내는 템플릿을 내보낼 수 있습니다. 내보낸 템플릿은 배포에 사용된 템플릿에 기초하지 않습니다. 대신 리소스 그룹의 스냅숏인 템플릿을 만듭니다. 내보낸 템플릿에는 하드 코드된 값이 많으며 일반적으로 정의된 경우와 같이 매개 변수가 많이 포함되지 않습니다. 이 방법은 리소스 그룹을 수정한 경우에 유용합니다. 이제 리소스 그룹을 템플릿으로 캡처해야 합니다.

이 항목에서는 두 가지 방법을 모두 보여 줍니다.

## <a name="deploy-a-solution"></a>솔루션 배포

템플릿을 내보내기 위한 두 가지 방법을 확인하려면 먼저 구독에 솔루션을 배포합니다. 내보내려는 구독에 리소스 그룹이 이미 있는 경우 이 솔루션을 배포할 필요가 없습니다. 그러나 이 문서의 나머지 부분에서 이 솔루션에 대한 템플릿이 언급됩니다. 예제 스크립트는 저장소 계정을 배포합니다.

```azurecli
az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name NewStorage \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
```  

## <a name="save-template-from-deployment-history"></a>배포 기록에서 템플릿 저장

[az group deployment export](/cli/azure/group/deployment#export) 명령을 사용하여 배포 기록에서 템플릿을 검색할 수 있습니다. 다음 예제에서는 이전에 배포하는 템플릿을 저장합니다.

```azurecli
az group deployment export --name NewStorage --resource-group ExampleGroup
```

템플릿을 반환합니다. JSON을 복사하고 파일로 저장합니다. 배포에 사용한 정확한 템플릿인지 확인합니다. 매개 변수 및 변수는 GitHub의 템플릿과 일치합니다. 이 템플릿을 다시 배포할 수 있습니다.


## <a name="export-resource-group-as-template"></a>리소스 그룹을 템플릿으로 내보내기

배포 기록에서 템플릿을 검색하지 않고 [az group export](/cli/azure/group#export) 명령을 사용하여 리소스 그룹의 현재 상태를 나타내는 템플릿을 검색할 수 있습니다. 이 명령은 리소스 그룹을 많이 변경했으며 모든 변경 내용을 나타내는 기존 템플릿이 없는 경우에 사용합니다.

```azurecli
az group export --name ExampleGroup
```

템플릿을 반환합니다. JSON을 복사하고 파일로 저장합니다. GitHub의 템플릿과는 다른지 확인합니다. 매개 변수는 다르고 변수는 없습니다. 저장소 SKU 및 위치는 값으로 하드 코드됩니다. 다음 예제에서는 내보낸 템플릿을 보여 주지만 템플릿은 약간 다른 매개 변수 이름을 갖습니다.

```json
{
  "parameters": {
    "storageAccounts_mcyzaljiv7qncstandardsa_name": {
      "type": "String",
      "defaultValue": null
    }
  },
  "variables": {},
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "resources": [
    {
      "tags": {},
      "dependsOn": [],
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "type": "Microsoft.Storage/storageAccounts",
      "location": "centralus",
      "name": "[parameters('storageAccounts_mcyzaljiv7qncstandardsa_name')]",
      "properties": {}
    }
  ],
  "contentVersion": "1.0.0.0"
}
```

이 템플릿을 다시 배포할 수 있지만 저장소 계정에 대한 고유한 이름을 추측해야 합니다. 매개 변수의 이름은 약간 다릅니다.

```azurecli
az group deployment create --name NewStorage --resource-group ExampleGroup \
  --template-file examplegroup.json \
  --parameters "{\"storageAccounts_mcyzaljiv7qncstandardsa_name\":{\"value\":\"tfstore0501\"}}"
```

## <a name="customize-exported-template"></a>내보낸 템플릿 사용자 지정

이 템플릿을 수정하여 좀 더 사용하기 쉽고 유연하게 만들 수 있습니다. 더 많은 위치를 허용하려면 리소스 그룹으로 동일한 위치를 사용하도록 위치 속성을 변경합니다.

```json
"location": "[resourceGroup().location]",
```

저장소 계정에 대한 고유한 이름을 추측하지 않으려면 저장소 계정 이름에 대한 매개 변수를 제거합니다. 저장소 이름 접미사에 대한 매개 변수와 저장소 SKU를 추가합니다.

```json
"parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
},
```

uniqueString 함수를 사용하여 저장소 계정 이름을 생성하는 변수를 추가합니다.

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

저장소 계정의 이름을 변수로 설정합니다.

```json
"name": "[variables('storageAccountName')]",
```

SKU를 매개 변수로 설정합니다.

```json
"sku": {
    "name": "[parameters('storageAccountType')]",
    "tier": "Standard"
},
```

템플릿은 이제 다음과 같이 표시됩니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), parameters('storageSuffix'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "[parameters('storageAccountType')]",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

수정된 템플릿을 다시 배포합니다.

## <a name="next-steps"></a>다음 단계
* 포털을 사용하여 템플릿을 내보내는 방법에 대한 자세한 내용은 [기존 리소스에서 Azure Resource Manager 템플릿 내보내기](resource-manager-export-template.md)를 참조하세요.
* 템플릿에서 매개 변수를 정의하려면 [템플릿 작성](resource-group-authoring-templates.md#parameters)을 참조하세요.
* 일반적인 배포 오류를 해결하는 방법은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.