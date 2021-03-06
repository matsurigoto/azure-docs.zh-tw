---
title: 了解預付型方案訂用帳戶的 Azure 保留執行個體使用量 - Azure 計費 |Microsoft Docs
description: 了解如何讀取您的使用量，以了解預付型方案訂用帳戶的 Azure 保留 VM 執行個體如何應用。
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: 7bf4aea86d4d430c15d60a8d73365705ace18b5a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301847"
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>了解預付型方案的保留執行個體使用量

從[保留頁面](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)使用 ReservationId，以及從 [Azure 帳戶入口網站](https://account.azure.com)的使用量檔案，了解 Azure 保留 VM 執行個體的使用情況。


>[!NOTE]
>本文不適用於 EA 客戶。 如果您是 EA 客戶，請參閱[了解 Enterprise 註冊之保留執行個體的使用方式](billing-understand-reserved-instance-usage-ea.md) 本文也假設保留執行個體會套用至單一訂用帳戶。 如果此保留執行個體套用至多個訂用帳戶，保留執行個體的權益可能會延伸到多個使用量 CSV 檔案。 

針對下一節，假設您在美國東部地區執行 Standard_DS1_v2 Windows 虛擬機器，且保留執行個體資訊看起來像下面的表格：

| 欄位 | 值 |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|數量 |1|
|SKU | Standard_DS1_v2|
|區域 | eastus |

## <a name="reserved-instance-application"></a>保留執行個體的應用

已涵蓋虛擬機器的硬體部分，因為部署的虛擬機器符合保留執行個體屬性。 若要查看保留執行個體未涵蓋哪些 Windows 軟體，請前往 [Azure 保留執行個體的 Windows 軟體成本](billing-reserved-instance-windows-software-costs.md)。

### <a name="statement-section-of-csv"></a>CSV 的說明區段
此 CSV 區段會顯示保留執行個體的整體使用狀況。 將包含「Reservation-」的篩選套用至 [計量子類別]，而您的資料看起來會類似下列螢幕擷取畫面：![篩選保留執行個體使用量詳細資料與費用的螢幕擷取畫面](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Reservation-Base VM 行有保留執行個體涵蓋的小時總數。 這一行為 $0.00，因為保留執行個體可以涵蓋它。 Reservation-Windows Svr (1 核心) 行涵蓋了 Windows 軟體成本。

### <a name="daily-usage-section-of-csv"></a>CSV 的每日使用量區段
篩選其他資訊並輸入您的**保留識別碼**。 以下螢幕擷取畫面顯示和此保留執行個體相關的欄位。 

![每日使用量詳細資料與費用的螢幕擷取畫面](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. [其他資訊] 欄位中的 **ReservationId** 是用來將權益套用至虛擬機器的保留執行個體。
2. ConsumptionMeter 是虛擬機器的 MeterId。
3. Reservation-Base VM 計量子類別行在說明區段的行中代表 $0 成本。 執行此虛擬機器的成本是已由保留執行個體支付。
4. 這是保留執行個體的計量識別碼。 此計量的成本為 $0。 任何符合保留執行個體資格的虛擬機器在 CSV 中都有此 MeterId，用來說明成本。 
5. Standard_DS1_v2 是一種 vCPU 虛擬機器，且是在沒有 Azure Hybrid Benefit 的情況下部署的虛擬機器。 因此，這個計量涵蓋 Windows 軟體的額外費用。 請參閱 [Azure 保留執行個體的 Windows 軟體成本](billing-reserved-instance-windows-software-costs.md)， 以尋找與 D 系列 1 核心 VM 相對應的計量。 如果使用 Azure Hybrid Benefit，就不會產生此額外費用。 

## <a name="next-steps"></a>後續步驟
若要深入了解保留執行個體，請參閱下列文章：

- [使用 Azure 保留執行個體以節省虛擬機器的成本](billing-save-compute-costs-reservations.md)
- [預付具有保留執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理保留執行個體](billing-manage-reserved-vm-instance.md)
- [了解保留執行個體折扣如何套用](billing-understand-vm-reservation-charges.md)
- [了解 Enterprise 註冊之保留執行個體的使用方式](billing-understand-reserved-instance-usage-ea.md)
- [Windows 軟體的成本不包括在保留的執行個體內](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果您仍有其他問題，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
