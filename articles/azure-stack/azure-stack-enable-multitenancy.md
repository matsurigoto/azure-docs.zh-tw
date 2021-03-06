---
title: 在 Azure Stack 中啟用多重租用 | Microsoft Docs
description: 了解如何在 Azure Stack 中支援多重 Azure Active Directory 目錄
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: mabrigg
ms.openlocfilehash: 59b0f8e4c7234b246d4fb54d065ff318939e2662
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301830"
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>在 Azure Stack 中啟用多重租用

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

您可以設定 Azure Stack，以支援來自多重 Azure Active Directory (Azure AD) 租用戶的使用者在 Azure Stack 中使用服務。 例如，請考慮下列情節：

 - 您是 contoso.onmicrosoft.com 的服務管理員，而 Azure Stack 也安裝於此。
 - Mary 是 fabrikam.onmicrosoft.com 的目錄系統管理員，也是來賓使用者的所在位置。 
 - Mary 的公司從貴公司接收 IaaS 和 paas 整合服務，且需要允許來自來賓目錄 (fabrikam.onmicrosoft.com) 的使用者登入，並且使用 contoso.onmicrosoft.com 中的 Azure Stack 資源。

本指南提供此情節內容中必要的逐步執行，如何在 Azure Stack 中設定多重租用。  在此情節中，您和 Mary 必須完成逐步執行好讓使用者從 Fabrikam 登入，並在 Contoso 中從 Azure Stack 部署使用服務。  

## <a name="before-you-begin"></a>開始之前
在您於 Azure Stack 中開始設定多重租用之前，有幾個必要條件：
  
 - 您和 Mary 必須協調管理橫跨 Azure Stack (Contoso) 安裝目錄，以及來賓目錄 (Fabrikam) 的逐步執行。  
 - 請確定您已經[安裝](azure-stack-powershell-install.md)和[設定](azure-stack-powershell-configure-admin.md)好適用於 Azure Stack 的 PowerShell。
 - [下載 Azure Stack 工具](azure-stack-powershell-download.md)，並匯入「連線」和「身分識別」模組：

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - Mary 將會需要能夠存取 Azure Stack 的 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。 

## <a name="configure-azure-stack-directory"></a>設定 Azure Stack 目錄
在本節中，您可以設定 Azure Stack 以允許從 Fabrikam Azure AD 目錄租用戶登入。

### <a name="onboard-guest-directory-tenant"></a>加入來賓目錄租用戶
接下來，請將「來賓目錄租用戶」 (Fabrikam) 加入至 Azure Stack。  此逐步執行將會設定 Azure Resource Manager 接受來自來賓目錄租用戶的使用者和服務主體。

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local" `
 -ResourceGroupName $ResourceGroupName
````



## <a name="configure-guest-directory"></a>設定來賓目錄
在您完成 Azure Stack 目錄中的逐步執行之後，Mary 必須同意 Azure Stack 存取來賓目錄，並向 Azure Stack 註冊來賓目錄。 

### <a name="registering-azure-stack-with-the-guest-directory"></a>向 Azure Stack 註冊來賓目錄
一旦來賓目錄系統管理員同意讓 Azure Stack 存取 Fabrikam 的目錄，Mary 必須向 Azure Stack 註冊 Fabrikam 的目錄租用戶。

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>將使用者導向登入
現在，您和 Mary 已完成將 Mary 目錄加入的逐步執行，Mary 可以將 Fabrikam 使用者導向登入。  Fabrikam 使用者 (亦即尾碼為 fabrikam.onmicrosoft.com 的使用者) 可藉由瀏覽 https://portal.local.azurestack.external 來進行登入。  

Mary 會將 Fabrikam 目錄中的任何[外部主體](../role-based-access-control/rbac-and-directory-admin-roles.md) (亦即在 Fabrikam 目錄中但尾碼不是 fabrikam.onmicrosoft.com 的使用者) 導向使用 https://portal.local.azurestack.external/fabrikam.onmicrosoft.com 來進行登入。如果他們不使用此 URL，則會傳送至其預設的目錄 (Fabrikam)，並收到說明他們管理員尚未同意的錯誤。

## <a name="next-steps"></a>後續步驟

- [管理委派提供者](azure-stack-delegated-provider.md)
- [Azure Stack 重要概念](azure-stack-key-features.md)
