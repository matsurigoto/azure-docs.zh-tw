---
title: Azure AD .NET 通訊協定概觀 | Microsoft Docs
description: 如何使用 HTTP 訊息來使用 Azure AD 授權存取租用戶中的 Web 應用程式和 Web API。
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2018
ms.author: priyamo
ms.openlocfilehash: 0b78ed6cdb1209d70cf0d561f74cfcddc09b2391
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
## <a name="register-your-application-with-your-ad-tenant"></a>向 AD 租用戶註冊應用程式
首先，您必須向您的 Azure Active Directory (Azure AD) 租用戶註冊應用程式。 這會讓應用程式獲得應用程式識別碼，以及讓它可以接收權杖。

* 登入 [Azure 入口網站](https://portal.azure.com)。
* 在頁面右上角按一下您的帳戶來選擇您的 Azure AD 租用戶。
* 在左側導覽窗格中，按一下 [Azure Active Directory] 。
* 按一下 [應用程式註冊]，然後按一下 [新應用程式註冊]。
* 遵照提示進行，並建立新的應用程式。 在本教學課程中，不論它是 Web 應用程式或原生應用程式都沒關係，但如果您想要 Web 應用程式或原生應用程式的特定範例，請查看我們的[快速入門](../articles/active-directory/develop/active-directory-developers-guide.md)。
  * 若為 Web 應用程式，請提供屬於應用程式基礎 URL 的**登入 URL**以供使用者登入，例如 `http://localhost:12345`。
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * 若為原生應用程式，請提供 **重新導向 URI**，以供 Azure AD 用來傳回權杖回應。 輸入應用程式特定的值，例如 `http://MyFirstAADApp`
* 完成註冊後，Azure AD 將會為您的應用程式指派一個唯一用戶端識別碼 (**應用程式識別碼**)。 您會在後續章節中用到這個值，所以請從應用程式頁面中複製此值。
* 若要在 Azure 入口網站中尋找您的應用程式，請按一下 [應用程式註冊]，然後按一下 [檢視所有應用程式]。