---
title: 在 Azure Active Directory 中變更企業應用程式的名稱或標誌 | Microsoft Docs
description: 如何在 Azure Active Directory 中變更自訂企業應用程式的名稱或標誌
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: a8c166e4c0abeddbdb23622e147cd7ecd5395b05
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中變更企業應用程式的名稱或標誌
在 Azure Active Directory (Azure AD) 中，您可以輕鬆變更自訂企業應用程式的名稱或標誌。 您必須具備適當的權限，才能進行這些變更，而且必須是自訂應用程式的建立者。

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>如何變更企業應用程式的名稱或標誌？
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
2. 選取 [所有服務]，在文字方塊中輸入 **Azure Active Directory**，然後選取 **Enter** 鍵。
3. 在 [Azure Active Directory - directoryname] 窗格 (也就是您所管理之目錄的 Azure AD 窗格) 上，選取 [企業應用程式]。

    ![開啟企業應用程式](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)
4. 在 [企業應用程式] 窗格上，選取 [所有應用程式]。 您會看到一份您可以管理的應用程式清單。
5. 在 [企業應用程式 - 所有應用程式]  窗格上，選取一個應用程式。
6. 在 [appname] 窗格 (亦即標題中含有所選應用程式之名稱的窗格) 上，選取 [屬性]。

    ![選取 [屬性] 命令](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)
7. 在 [***appname*** - 屬性] 窗格上，瀏覽要作為新標誌的檔案或編輯應用程式名稱，或同時執行兩者。

    ![變更應用程式標誌或名稱屬性命令](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)
8. 選取 [儲存]  命令。

## <a name="next-steps"></a>後續步驟
* [查看我的所有群組](active-directory-groups-view-azure-portal.md)
* [將使用者或群組指派給企業應用程式](active-directory-coreapps-assign-user-azure-portal.md)
* [從企業應用程式中移除使用者或群組指派](active-directory-coreapps-remove-assignment-azure-portal.md)
* [停用企業應用程式的使用者登入](active-directory-coreapps-disable-app-azure-portal.md)
