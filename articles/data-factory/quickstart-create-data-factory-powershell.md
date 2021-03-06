---
title: "PowerShell을 사용하여 Azure Data Factory 만들기 | Microsoft Docs"
description: "Azure Data Factory를 만들어서 Azure Blob Storage의 한 위치에서 동일한 Blob Storage의 다른 위치로 데이터를 복사합니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 82c6f69e886bfe86b2839e7efc3579782c8c3dce
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-data-factory-and-pipeline-using-powershell"></a>PowerShell을 사용하여 데이터 팩터리 및 파이프라인 만들기
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [버전 2 - 미리 보기](quickstart-create-data-factory-powershell.md)

Azure Data Factory는 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory를 사용하여 서로 다른 데이터 저장소에서 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 일정을 조정하며, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 계산 서비스를 사용하여 데이터를 처리/변환하고, 사용할 BI(비즈니스 인텔리전스) 응용 프로그램의 Azure SQL Data Warehouse와 같은 데이터 저장소에 출력 데이터를 게시할 수 있습니다. 

이 빠른 시작에서는 PowerShell을 사용하여 Azure Data Factory를 만드는 방법을 설명합니다. 이 데이터 팩터리의 파이프라인은 Azure Blob Storage의 한 위치에서 다른 위치로 데이터를 복사합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* **Azure Storage 계정**. Blob 저장소를 **원본** 및 **싱크** 데이터 저장소 모두로 사용합니다. Azure Storage 계정이 없는 경우 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하여 하나 만듭니다. 
* Blob Storage에 **Blob 컨테이너**를 만들고 컨테이너에 입력 **폴더**를 만들고 폴더에 일부 파일을 업로드합니다. [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)와 같은 도구를 사용하여 Azure Blob Storage에 연결, Blob 컨테이너 만들기, 입력 파일 업로드 및 출력 파일 확인을 수행할 수 있습니다.
* **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침을 따르세요.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. **PowerShell**을 시작합니다. 이 빠른 시작을 완료할 때까지 Azure PowerShell을 열어 둡니다. 닫은 후 다시 여는 경우 명령을 다시 실행해야 합니다.

    다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.
        
    ```powershell
    Login-AzureRmAccount
    ```        
    다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```powershell
    Get-AzureRmSubscription
    ```
    다음 명령을 실행하여 사용하려는 구독을 선택합니다. **SubscriptionId**를 Azure 구독의 ID로 바꿉니다.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
2. **Set-AzureRmDataFactoryV2** cmdlet을 실행하여 데이터 팩터리를 만듭니다. 명령을 실행하기 전에 자리 표시자를 사용자의 고유한 값으로 바꿉니다. **자리 표시자**를 사용자 고유한 값으로 바꿉니다. 

    나중에 PowerShell 명령에서 사용할 수 있는 리소스 그룹 이름에 대한 변수를 정의합니다. 
    ```powershell
    $resourceGroupName = "<your resource group to create the factory>";
    ```

    나중에 PowerShell 명령에서 사용할 수 있는 데이터 팩터리 이름에 대한 변수를 정의합니다. 

    ```powershell
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    ```

    다음 명령을 실행하여 데이터 팩터리를 만듭니다. 
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

    다음 사항에 유의하세요.

    * Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Data Factory 인스턴스를 만들려면 Azure 구독의 참가자 또는 관리자여야 합니다.
    * 현재 **미국 동부** 또는 **미국 동부 2** 지역에서만 Data Factory V2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

## <a name="create-a-linked-service"></a>연결된 서비스 만들기

데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소 및 계산 서비스를 데이터 팩터리에 연결합니다. 이 빠른 시작에서는 원본 및 싱크 저장소 모두로 사용되는 이 샘플의 “AzureStorageLinkedService”라는 하나의 Azure Storage 연결된 서비스를 만들기만 하면 됩니다.

1. 다음 콘텐츠가 포함된 **AzureStorageLinkedService.json**이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다. (없는 경우 ADFv2QuickStartPSH 폴더를 만듭니다.) 파일을 저장하기 전에 &lt;accountName&gt; 및 &lt;accountKey&gt;를 Azure Storage 계정의 이름 및 키로 바꿉니다.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

2. **Azure PowerShell**에서 **ADFv2QuickStartPSH** 폴더로 전환합니다.

3. **Set-AzureRmDataFactoryV2LinkedService** cmdlet을 실행하여 **AzureStorageLinkedService** 연결된 서비스를 만듭니다. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>데이터 집합 만들기

원본에서 싱크로 복사할 데이터를 나타내는 데이터 집합을 정의합니다. 이 예에서 이 Blob 데이터 집합은 이전 단계에서 만든 Azure Storage 연결된 서비스를 참조합니다. 데이터 집합은 값이 데이터 집합을 사용하는 활동에 설정되어 있는 매개 변수를 사용합니다. 매개 변수는 데이터가 상주/저장되는 위치를 가리키는 “folderPath”를 구성하는 데 사용됩니다.

1. 다음 콘텐츠가 포함된 **BlobDataset.json**이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다.

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": {
                    "value": "@{dataset().path}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. **BlobDataset** 데이터 집합을 만들려면 **Set-AzureRmDataFactoryV2Dataset** cmdlet을 실행합니다.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
  
이 예제에서 이 파이프라인은 하나의 활동을 포함하고 입력 Blob 경로 및 출력 Blob 경로의 두 매개 변수를 사용합니다. 이러한 매개 변수의 값은 파이프라인이 트리거/실행될 때 설정됩니다. 복사 활동은 입력 및 출력 시 이전 단계에서 만든 동일한 Blob 데이터 집합을 참조합니다. 데이터 집합을 입력된 데이터 집합으로 사용하는 경우 입력된 경로가 지정됩니다. 또한 데이터 집합을 출력된 데이터 집합으로 사용하는 경우 출력된 경로가 지정됩니다. 

1. 다음 콘텐츠가 포함된 **Adfv2QuickStartPipeline.json**이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다.

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. **Adfv2QuickStartPipeline** 파이프라인을 만들려면 **Set-AzureRmDataFactoryV2Pipeline** cmdlet을 실행합니다.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기

이 단계에서 **inputPath** 및 **outputPath** 파이프라인 매개 변수에 대한 값을 원본 및 싱크 Blob 경로의 실제 값으로 설정합니다. 그런 다음 이러한 인수를 사용하여 실행할 파이프라인을 만듭니다. 

1. 다음 콘텐츠가 포함된 **PipelineParameters.json**이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다.

    파일을 저장하기 전에 “inputPath” 및 “outputPath”의 값을 데이터를 복사할 원본 및 싱크 Blob 경로로 바꿉니다.

    ```json
    {
        "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/foldername>",
        "outputPath": "<the blob path to copy data to, e.g. containername/foldername>"
    }
    ```

2. **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet을 실행하여 매개 변수 값에서 실행 및 전달할 파이프라인을 만듭니다. 또한 향후 모니터링을 위해 ID를 실행하는 파이프라인을 캡처합니다.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-a-pipeline-run"></a>파이프라인 실행 모니터링

1. 다음 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```

    파이프라인 실행의 샘플 출력은 다음과 같습니다.

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

2. 복사 활동 실행 세부 정보(예: 읽고/쓴 데이터의 크기)를 검색하는 다음 스크립트를 실행합니다.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. 활동 실행 결과의 다음 샘플 출력과 유사한 출력이 표시되는지 확인합니다.

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>출력 확인
[Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)와 같은 도구를 사용하여 inputBlobPath의 Blob이 outputBlobPath로 복사되었는지 검사합니다.

## <a name="clean-up-resources"></a>리소스 정리
빠른 시작에서 만든 리소스는 두 가지 방법으로 정리할 수 있습니다. 리소스 그룹의 모든 리소스를 포함하고 있는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 삭제할 수 있습니다. 다른 리소스를 그대로 유지하려면 이 자습서에서 만든 데이터 팩터리만 삭제합니다.

다음 명령을 실행하여 전체 리소스 그룹을 삭제합니다. 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

다음 명령을 실행하여 데이터 팩터리만 삭제합니다. 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사합니다. [자습서](tutorial-copy-data-dot-net.md)를 통해 더 많은 시나리오에서의 데이터 팩터리 사용에 관해 알아보세요. 
