---
title: 包含檔案
description: 包含檔案
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 13aa40331849c775898913129f8048a06a1f8456
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2018
---
您可將標籤套用至 Azure 資源，以便以邏輯方式依照類別組織這些資源。 每個標記都是由一個名稱和一個值所組成。 例如，您可以將「環境」名稱和「生產」值套用至生產環境中的所有資源。

套用標記之後，您可以擷取訂用帳戶中具有該標記名稱和值的所有資源。 標記可讓您擷取不同資源群組中的相關資源。 當您需要組織資源以進行計費或管理時，此方法很有用。

標籤具有下列限制：

* 每個資源或資源群組最多都可以有 15 個標記名稱/值組。 此限制只適用於直接套用至資源群組或資源的標記。 資源群組可以包含許多資源，其各自有 15 個標記名稱/值組。 如果您有超過 15 個要與資源產生關聯的值，請使用 JSON 字串作為標記值。 JSON 字串可以包含許多套用至單一標記名稱的值。 本文會示範將 JSON 字串指派給標記。
* 標記名稱上限為 512 個字元，且標記值上限為 256 字元。 儲存體帳戶的標記名稱上限為 128 個字元，且標記值上限為 256 個字元。
* 資源群組中的資源不會繼承套用至該資源群組的標籤。
* 您無法將標記套用到傳統資源，例如雲端服務。
* 不支援以下字元：`<`、`>`、`%`、`&`、`\\`、`?`、`/`
