---
title: 無須撰寫程式碼即可執行 Azure Batch 作業端對端 (預覽) | Microsoft Docs
description: 建立 Azure CLI 的範本檔案來建立 Batch 集區、作業和工作。
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/18/2017
ms.author: markscu
ms.openlocfilehash: 0a6e355d8f16fed9022cc2cf55dc09781364f0b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>使用 Azure Batch CLI 範本和檔案傳輸 (預覽)

使用 Azure CLI 就可直接執行 Batch 作業而不需要撰寫程式碼。

建立及使用 Azure CLI 的範本檔案來建立 Batch 集區、作業和工作。 可以輕鬆地將作業輸入檔案上傳至與 Batch 帳戶相關聯的儲存體帳戶，以及下載作業輸出檔案。

## <a name="overview"></a>概觀

Azure CLI 的擴充功能可讓非開發人員的使用者端對端使用 Batch。 您只需要使用 CLI 命令，就可以建立集區、上傳輸入資料、建立作業和相關聯工作，以及下載結果輸出資料。 不需要額外的程式碼。 直接執行 CLI 命令，或將它們整合到指令碼。

Batch 範本會建置在 [Azure CLI 中的現有 Batch 支援](batch-cli-get-started.md#json-files-for-resource-creation)上，可讓 JSON 檔案指定建立集區、作業、工作及其他項目的屬性值。 透過 Batch 範本，可對 JSON 檔案的可行項目新增下列功能：

-   可以定義參數。 使用範本時，只會指定要建立項目的參數值，其他項目的屬性值則是在範本主體中指定。 了解 Batch 及 Batch 所要執行之應用程式的使用者可以建立範本，並指定集區、作業和工作屬性值。 較不熟悉 Batch 和/或應用程式的使用者，只需要指定已定義之參數的值。

-   作業工作 Factory 會建立一或多個與作業相關聯的工作，讓您既不必建立許多工作定義，又可大幅簡化作業的提交程序。


作業通常會使用輸入資料檔案，並且產生輸出資料檔案。 根據預設，儲存體帳戶與每個 Batch 帳戶相關聯。 使用 CLI 從這個儲存體帳戶來回傳輸檔案，不需要編碼以及儲存體認證。

例如，[ffmpeg](http://ffmpeg.org/) 是常用的應用程式，可處理音訊和視訊檔案。 以下是使用 Azure Batch CLI 來叫用 ffmpeg，將來源視訊檔案轉碼為不同解析度的步驟。

-   建立集區範本。 建立範本的使用者知道如何呼叫 ffmpeg 應用程式及其需求；他們會指定適當的 OS、VM 大小、ffmpeg 的安裝方式 (例如，從應用程式套件或使用套件管理員)，和其他集區屬性值。 會建立參數，因此當使用範本時，只需要指定集區識別碼和 VM 數目。

-   建立作業範本。 建立範本的使用者知道要將來源視訊轉碼為不同解析度所需要叫用 ffmpeg 的方式，且會指定工作命令列；他們也知道會有包含來源視訊檔案的資料夾，以及每個輸入檔案所需的工作。

-   需要將一組視訊檔案進行轉碼的使用者會先使用集區範本來建立集區，從而僅指定集區識別碼和所需的 VM 數目。 接著，他們可以上傳要轉換的來源檔案。 然後可以使用作業範本來提交作業，從而僅指定集區識別碼和已上傳來源檔案的位置。 將會建立 Batch 作業，每個所產生的輸入檔案會有一項工作。 最後，可以下載轉碼的輸出檔案。

## <a name="installation"></a>安裝

安裝 Azure Batch CLI 擴充功能以使用範本和檔案傳輸功能。

如需有關如何安裝 Azure CLI 的指示，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。

一旦安裝 Azure CLI，使用下列 CLI 命令來安裝最新版本的 Batch 擴充功能：

```azurecli
az extension add --name azure-batch-cli-extensions
```

如需 Batch 擴充功能的詳細資訊，請參閱[適用於 Windows、Mac 和 Linux 的 Microsoft Azure Batch CLI 擴充功能](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux)。

## <a name="templates"></a>範本

Azure Batch CLI 可允許建立諸如集區、作業和工作等項目，方法是指定包含屬性名稱和值的 JSON 檔案。 例如︰

```azurecli
az batch pool create –-json-file AppPool.json
```

Azure Batch 範本在功能和語法方面類似於 Azure Resource Manager 範本。 這些是包含項目屬性名稱和值的 JSON 檔案，但新增下列主要概念：

-   **參數**

    -   允許在主體區段中指定屬性值，包含僅於使用此範本時需要提供的參數值。 例如，集區的完整定義可放在主體中，且僅針對集區識別碼定義一個參數；因此，只需要提供一個集區識別碼字串即可建立集區。
        
    -   可由了解 Batch 和由 Batch 執行之應用程式的人員撰寫範本主體；僅在使用此範本時，才必須提供作者定義參數的值。 因此，未深入了解 Batch 和/或應用程式知識的使用者可以使用此範本。

-   **變數**

    -   允許在單一位置中指定簡單或複雜的參數值，並在範本主體的一個或多個位置加以使用。 變數可以簡化和降低範本的大小，並讓它更易於維護，方法是準備一個位置來變更屬性。

-   **較高層級的建構**

    -   部分在範本中可使用的較高層級建構尚無法在 Batch API 中使用。 例如，可在作業範本中定義的工作 Factory，會使用通用的工作定義來建立作業的多個工作。 這些建構不需要編碼即可動態建立多個 JSON 檔案，例如每個工作一個檔案，以及建立指令碼檔，透過套件管理員來安裝應用程式。

    -   在某些情況下，可能會將這些建構新增至 Batch 服務，且適用於 Batch API、UI 等等。

### <a name="pool-templates"></a>集區範本

集區範本支援參數和變數的標準範本功能。 它們也支援下列更高階的建構：

-   **套件參考**

    -   使用套件管理員選擇性地讓軟體複製到集區節點。 會指定套件管理員和套件識別碼。 藉由宣告一個或多個套件，您就可以避免建立會取得必要套件的指令碼、安裝指令碼，並在每個集區節點上執行指令碼。

下列範本範例可建立已安裝 ffmpeg 的 Linux 虛擬機器。 若要使用它，只要提供集區 ID 字串以及集區中的虛擬機器數目：

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04.0-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

如果範本檔名為 _pool-ffmpeg.json_，則會叫用範本，如下所示：

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>作業範本

作業範本支援參數和變數的標準範本功能。 它們也支援下列更高階的建構：

-   **工作 Factory**

    -   會從一個工作定義中建立作業的多個工作。 支援三種工作 Factory – 參數整理、每個檔案的工作和工作集合。

下列範本範例可建立一項作業，該作業會使用 ffmpeg 將 MP4 視訊檔案轉碼為兩個較低的解析度之一。 它會為每個來源視訊檔案都建立一個工作：

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

如果範本檔名為 _job-ffmpeg.json_，則會叫用範本，如下所示：

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>檔案群組和檔案傳輸

大部分的作業與工作都需要輸入檔，並且會產生輸出檔。 輸入檔案和輸出檔案通常需要進行傳輸，不論是從用戶端傳輸至節點，還是從節點傳輸至用戶端。 Azure Batch CLI 擴充功能可將檔案傳輸抽象化抽離，並且使用依預設針對每個 Batch 帳戶所建立的儲存體帳戶。

檔案群組等同於在 Azure 儲存體帳戶中建立的容器。 檔案群組可具有子資料夾。

Batch CLI 擴充功能會提供命令，以供您從用戶端將檔案上傳到指定的檔案群組，以及從指定的檔案群組將檔案下載到用戶端。

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

集區和作業範本允許指定儲存在檔案群組中的檔案，以複製到集區節點上，或從集區節點複製回到檔案群組。 例如，在先前指定的作業範本中，會指定工作處理站的 “ffmpeg-input” 檔案群組，作為向下複製到節點進行轉碼之來源視訊檔案的位置；“ffmpeg-output” 檔案群組則是從執行每項工作的節點複製轉碼輸出檔案的位置。

## <a name="summary"></a>總結

目前僅已將範本和檔案傳輸支援新增至 Azure CLI。 目標旨在將可使用 Batch 的對象拓展到不需要使用 Batch API 來開發程式碼的使用者，例如研究人員、IT 使用者等等。 無需程式碼撰寫，了解 Azure、Batch 和 Batch 所要執行之應用程式的使用者可以建立集區和作業建立的範本。 利用範本參數，未深入了解 Batch 和應用程式的使用者就可以使用範本。

請試用 Azure CLI 的 Batch 擴充功能，並利用本文的評論或透過 [Azure Batch 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)將您的意見或建議告訴我們。

## <a name="next-steps"></a>後續步驟

- 請參閱 Batch 範本部落格文章：[使用 Azure CLI 執行 Azure Batch 作業 – 不需要程式碼](https://azure.microsoft.com/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/)。
- 您可在 [Azure GitHub 存放庫](https://github.com/Azure/azure-batch-cli-extensions)中取得詳細的安裝與使用文件、範例和原始程式碼。
