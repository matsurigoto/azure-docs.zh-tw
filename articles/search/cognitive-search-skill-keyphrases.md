---
title: 關鍵片語擷取認知搜尋技能 (Azure 搜尋服務) | Microsoft Docs
description: 評估非結構化的文字，並針對每一筆記錄，透過 Azure 搜尋服務擴充管線傳回關鍵片語清單。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a12efaa020e626e4a10a0708c9b84d8fe125588c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786807"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>關鍵片語擷取認知技能

**關鍵片語擷取**技能會評估非結構化的文字，並針對每一筆記錄，傳回關鍵片語的清單。

如果您需要快速識別記錄中的主要討論要點，此功能相當有用。 例如，假設輸入文字為「The food was delicious and there were wonderful staff」，服務會傳回「food」和「wonderful staff」。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>資料限制
根據 `String.Length` 測量，記錄的大小上限應該是 50,000 個字元。 如果您需要先分割資料，然後再將該資料傳送至關鍵片語擷取器，請考慮使用 [文字分割技能][](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。
| 輸入                | 說明 |
|---------------------|-------------|
| defaultLanguageCode | (選用) 要套用至未明確指定語言之文件的語言代碼。  如果未指定預設語言代碼，則會使用英文 (en) 做為預設語言代碼。 <br/> 請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)。 |
| maxKeyPhraseCount   | (選用) 產生的關鍵片語數量上限。 |

## <a name="skill-inputs"></a>技能輸入
| 輸入     | 說明 |
|--------------------|-------------|
| text | 要分析的文字。|
| languageCode  |  此字串表示記錄的語言。 如果未指定此參數，將使用預設語言代碼來分析記錄。 <br/>請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>範例定義

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-input"></a>範例輸入

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>範例輸出

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>錯誤和警告
如果您提供不支援的語言代碼，則會產生錯誤，而且不會擷取關鍵片語。
如果您的文字是空白的，則會產生警告。
如果文字超過 50,000 個字元，則只會分析前 50,000 個字元，並且會發出警告。

## <a name="see-also"></a>另請參閱

+ [預先定義的技能](cognitive-search-predefined-skills.md) (英文)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)