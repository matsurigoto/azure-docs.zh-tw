---
title: 使用入口網站向 Azure AD v2.0 端點註冊應用程式 | Microsoft Docs
description: 如何向 Microsoft 註冊 app，以使用 v2.0 端點啟用登入及存取 Microsoft 服務
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 8ab4e6b5b2813a216b6dd6f0fc108a09239ca9a6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157838"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>如何使用 v2.0 端點註冊 App
若要建置同時接受個人 Microsoft 帳戶 (MSA) 與公司或學校帳戶 (Azure AD) 登入的應用程式，您必須先向 Microsoft 註冊應用程式。 您目前無法使用任何現有的 app 搭配 Azure AD 或 MSA - 您需要建立一個全新的 app。

> [!NOTE]
> v2.0 端點並非支援每個 Azure Active Directory 案例和功能。 若要判斷是否應該使用 v2.0 端點，請閱讀 [v2.0 限制](active-directory-v2-limitations.md)。


## <a name="visit-the-microsoft-app-registration-portal"></a>造訪 Microsoft App 註冊入口網站
首先，巡覽至 Microsoft 應用程式註冊入口網站，網址為 [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)。 

使用個人或公司或學校的 Microsoft 帳戶進行登入。 如果您沒有任何帳戶，請註冊新的個人帳戶。

完成了嗎？ 您現在應該看一下您的 Microsoft 應用程式清單，該清單有可能空白。 讓我們改變這點。

按一下 [新增應用程式]，並為它命名。 入口網站將會指派全域唯一的應用程式識別碼給您的應用程式，以便稍後在您的程式碼中使用。 如果您的應用程式包含的伺服器端元件需要用來呼叫 API (例如：Office、Azure 或您自己的 Web API) 的存取權杖，您也會想在此處建立**應用程式密碼**。

接下來，新增您的應用程式將使用的**平台**。

* 針對 Web 架構的應用程式，提供可傳送登入訊息的**重新導向 URI**。
* 針對行動應用程式，複製系統自動為您建立的預設重新導向 URI。
* 針對 Web API，系統會自動為您建立用來存取 Web API 的預設範圍。 您可以選擇使用 [新增範圍] 按鈕新增其他範圍。 您也可以使用 [已預先授權應用程式] 表單新增任何已預先獲得授權可以使用您 Web API 的應用程式。 

(選擇性) 您可以在 [設定檔] 區段中自訂登入頁面的外觀及操作方式。 繼續之前，請務必按一下 [儲存]  。

> [!NOTE]
> 當您使用 [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) 建立應用程式時，系統將會在您用來登入入口網站的帳戶的家用租用戶中註冊該應用程式。 這表示您不能使用個人的 Microsoft 帳戶，在 Azure AD 租用戶中註冊應用程式。 如果您明確地想要在特定的租用戶中註冊應用程式，請使用原本在該租用戶中建立的帳戶登入。


## <a name="build-a-quickstart-app"></a>建置快速入門應用程式
您現在已有 Microsoft 應用程式，您可以完成其中一個 v2.0 快速入門教學課程。 以下是一些建議：

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

