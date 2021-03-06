---
title: 在 Azure 自動化混合式 Runbook 背景工作角色上執行 Runbook
description: 本文章提供在您的本機資料中心的電腦上執行 Runbook，或使用混合式 Runbook 背景工作角色之雲端提供者的相關資訊。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a4cf32ea7b77db3fc78a404063b8a4d69ecebf58
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195704"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>在混合式 Runbook 背景工作角色上執行 Runbook

在 Azure 自動化和混合式 Runbook 背景工作上執行的 Runbook 結構中沒有任何差異。 您對兩者使用的 Runbook 可能會有顯著差異，因為目標為混合式 Runbook 背景工作角色的 Runbook 通常會自行管理本機電腦上的資源，或針對其所部署之本機環境中的資源，而 Azure 自動化中的 Runbook 通常是管理 Azure 雲端中的資源。

當您撰寫要在混合式 Runbook 背景工作角色上執行的 Runbook 時，應該在裝載混合式背景工作角色的機器內編輯並測試 Runbook。 主機電腦具有您管理及存取本機資源所需要的所有 PowerShell 模組和網路存取。 一旦您在混合式背景工作角色機器上編輯並測試 Runbook 後，接著可以將其上傳至 Azure 自動化環境，其可供您在混合式背景工作中執行。 請務必知道，作業是在 Windows 的本機系統帳戶下或是 Linux 上的特殊使用者帳戶 **nxautomation** 下執行，所以可能會導致些微的差異，因此在撰寫混合式 Runbook 背景工作角色的 Runbook 時，必須將此列入考量。

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>在混合式 Runbook 背景工作角色上啟動 Runbook

[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) 描述啟動 Runbook 的不同方法。 混合式 Runbook 背景工作加入了 **RunOn** 選項，您可以在其中指定混合式 Runbook 背景工作群組的名稱。 如果未指定群組，則會擷取 Runbook，且由該群組中的其中一個背景工作角色執行。 如果未指定此選項，則會正常在 Azure 自動化中執行。

在 Azure 入口網站中啟動 Runbook 時，您會看到 [執行於] 選項，您可以在此選取 [Azure] 或 [Hybrid Worker]。 如果選取 [Hybrid 背景工作角色]，則您可以從下拉式清單中選取群組。

使用 **RunOn** 參數。 您可以使用 Windows PowerShell 以下列命令在 Hybrid Runbook Worker 群組上啟動名為 Test-Runbook 的 Runbook。

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> 0.9.1 版 Microsoft Azure PowerShell 的 **Start-AzureAutomationRunbook** Cmdlet 已新增 **RunOn** 參數。 如果您安裝較早的版本，您應該 [下載最新版本](https://azure.microsoft.com/downloads/) 。 您只需要在從 PowerShell 啟動 Runbook 的工作站上安裝此版本。 您不需要將它安裝在背景工作電腦上，除非您想要從該電腦啟動 Runbook。

## <a name="runbook-permissions"></a>Runbook 權限

在 Hybrid Runbook Worker 上執行的 Runbook 不能使用通常用於 Runbook 對 Azure 資源進行驗證的相同方法，因為它們會存取 Azure 外部的資源。 Runbook 可以提供自己的驗證給本機資源，或者您可以指定 RunAs 帳戶以對所有 Runbook 提供使用者內容。

### <a name="runbook-authentication"></a>Runbook 驗證

根據預設，內部部署電腦上的 Runbook 是在 Windows 的本機系統帳戶內容中或是 Linux 的特殊使用者帳戶 **nxautomation** 內容中執行，因此，它們必須對要存取的資源提供自己的驗證。

您可以在您的 Runbook 中使用[認證](automation-credentials.md)和[憑證](automation-certificates.md)資產，搭配可讓您指定認證的 Cmdlet，您就能夠向不同的資源驗證。 下列範例顯示會重新啟動電腦的 Runbook 的一部分。 它會從認證資產擷取認證和從變數資產擷取電腦的名稱，然後使用這些值搭配 Restart-Computer Cmdlet。

```azurepowershell-interactive
$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

您也可以運用 [InlineScript](automation-powershell-workflow.md#inlinescript)，它可讓您利用 [PSCredential 一般參數](/powershell/module/psworkflow/about/about_workflowcommonparameters)指定的認證在另一部電腦上執行程式碼區塊。

### <a name="runas-account"></a>RunAs 帳戶

根據預設，混合式 Runbook 背景工作角色會使用 Windows 的本機系統和 Linux 的特殊使用者帳戶 **nxautomation** 來執行 Runbook。 您不需要讓 Runbook 提供自己的驗證給本機資源，您可以針對混合式背景工作角色群組指定 **RunAs** 帳戶。 您指定具有本機資源存取權的 [認證資產](automation-credentials.md)，在群組中的 Hybrid Runbook Worker 執行時，所有 Runbook 會在這些認證下執行。

認證的使用者名稱必須是下列格式之一：

* 網域\使用者名稱
* username@domain
* 使用者名稱 (適用於內部部署機器的本機帳戶)

使用下列程序以針對混合式背景工作角色群組指定 RunAs 帳戶：

1. 建立具有本機資源存取權的 [認證資產](automation-credentials.md) 。
2. 在 Azure 入口網站中，開啟自動化帳戶。
3. 選取 [Hybrid Worker 群組]  圖格，然後選取群組。
4. 選取 [所有設定]，然後選取 [Hybrid 背景工作角色群組設定]。
5. 將 [執行身分] 從 [預設] 變更為 [自訂]。
6. 選取認證，然後按一下 [儲存] 。

### <a name="automation-run-as-account"></a>自動化執行身分帳戶

在 Azure 中部署資源的自動化建置流程中，您可能需要存取內部部署系統來支援部署序列中的工作或一組步驟。 若要支援使用「執行身分」帳戶向 Azure 驗證，您需要安裝「執行身分」帳戶憑證。

下列 PowerShell Runbook *Export-RunAsCertificateToHybridWorker* 會從 Azure 自動化帳戶匯出執行身分憑證，並將它下載和匯入 Hybird 背景工作角色 (連線至相同帳戶) 的本機憑證存放區。 完成該步驟後，它會確認背景工作角色可以使用執行身分帳戶成功向 Azure 驗證。

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

將 *Export-RunAsCertificateToHybridWorker* Runbook 以 `.ps1` 副檔名儲存至您的電腦。 將它匯入您的自動化帳戶，並編輯 Runbook，將變數 `$Password` 的值變更為您自己的密碼。 發佈 Runbook，然後以使用「執行身分」帳戶執行和驗證 Runbook 的 Hybrid Worker 群組為對象，執行 Runbook。 作業資料流會報告將憑證匯入本機電腦存放區的嘗試，後面還會出現幾行，根據訂用帳戶中定義多少自動化帳戶和驗證是否成功而定。

## <a name="job-behavior"></a>作業行為

作業在混合式 Runbook 背景工作角色上與在 Azure 沙箱上執行時的處理方式會有些許不同。 其中一個主要差異，在於混合式 Runbook 背景工作角色上並沒有限制作業持續時間。 如果您是使用長時間執行 Runbook，則需要確認它能彈性進行可能的重新開機，例如在裝載混合式背景工作角色的機器重新開機時。 如果混合式背景工作角色主機電腦重新開機，則任何執行中的 Runbook 作業都會從頭重新啟動，或是從 PowerShell 工作流程 Runbook 的最後一個檢查點重新啟動。 如果 Runbook 作業重新啟動超過 3 次，它就會暫止。

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>在 Hybrid Runbook Worker 上進行 Runbook 疑難排解

記錄檔儲存每一個混合式背景工作角色本機的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。 混合式背景工作角色也會將錯誤和事件記錄在 Windows 事件記錄的 **Application and Services Logs\Microsoft-SMA\Operational** 底下。 背景工作上執行的 Runbook 相關事件會寫入 **Application and Services Logs\Microsoft-Automation\Operational** 底下。 **Microsoft SMA** 記錄中包含的許多事件，是與推送至背景工作和處理 Runbook 的 Runbook 作業相關。 雖然 **Microsoft 自動化**事件記錄並沒有許多事件具有可協助針對 Runbook 執行進行疑難排解的詳細資料，它還是會包含 Runbook 作業的結果。

[Runbook 輸出和訊息](automation-runbook-output-and-messages.md) 會從混合式背景工作角色傳送到 Azure 自動化，就像雲端中執行的 Runbook 工作一樣。 您也可以啟用詳細資訊和進度資料流，就像您在其他 Runbook 中的作法一樣。

如果您的 Runbook 未成功完成，但作業摘要顯示 [暫止] 狀態，請檢閱疑難排解文章[混合式 Runbook 背景工作角色：Runbook 作業在暫止狀態下終止](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended)。

## <a name="next-steps"></a>後續步驟

* 若要深入了解可用來啟動 Runbook 的不同方法，請參閱[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)。
* 若要了解使用文字式編輯器在 Azure 自動化中使用 PowerShell 和 PowerShell 工作流程 Runbook 的不同程序，請參閱 [在 Azure 自動化中編輯 Runbook](automation-edit-textual-runbook.md)
