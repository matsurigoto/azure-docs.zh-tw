---
title: Azure 自動化 Windows 混合式 Runbook 背景工作
description: 本文提供有關安裝 Azure 自動化混合式 Runbook 背景工作角色的資訊，它可讓您在本機資料中心或雲端環境內以 Windows 為基礎的電腦上，執行 Runbook。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 30eda7683a1e8c27eb117b92744bf90eae3fd5d9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193827"
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>如何部署 Windows 混合式 Runbook 背景工作角色

Azure 自動化的混合式 Runbook 背景工作角色功能可讓您直接在裝載角色的電腦上，以及針對環境中的資源執行 Runbook，從而管理這些本機資源。 Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至一或多個指定的電腦。 本文會說明如何在 Windows 機器上安裝混合式 Runbook 背景工作角色。

## <a name="installing-the-windows-hybrid-runbook-worker"></a>安裝 Windows 混合式 Runbook 背景工作角色

安裝和設定 Windows 混合式 Runbook 背景工作角色有兩種可用的方法。 建議的方法是使用自動化 Runbook，將設定 Windows 電腦所需的程序完全自動化。 第二種方法採取逐步程序來手動安裝和設定角色。

> [!NOTE]
> 若要透過「預期狀態設定」(DSC) 管理支援 Hybrid Runbook Worker 之伺服器的設定，您需要將它們新增為 DSC 節點。

Windows 混合式 Runbook 背景工作角色的最低需求如下。

* Windows Server 2012 或更新版本。
* 需要 Windows PowerShell 4.0 或更新版本 ([下載 WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))。 建議使用 Windows PowerShell 5.1 ([下載 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) 以增加可靠性。
* .NET Framework 4.6.2 或更新版本。
* 至少雙核心。
* 至少 4 GB 的 RAM。
* 連接埠 443 (輸出)

若要查看混合式 Runbook 背景工作角色的其他網路需求，請參閱[設定網路](automation-hybrid-runbook-worker.md#network-planning)。

如需有關讓它們上線以透過 DSC 進行管理的詳細資訊，請參閱[讓機器上線以透過 Azure 自動化 DSC 進行管理](automation-dsc-onboarding.md)。
如果您啟用[更新管理解決方案](../operations-management-suite/oms-solution-update-management.md)，則任何連線到 Log Analytics 工作區的 Windows 電腦都會自動設定為混合式 Runbook 背景工作角色，以支援此解決方案所包含的 Runbook。 不過，它不會向您已在自動化帳戶中定義的任何 Hybrid Worker 群組註冊。 您可以將電腦新增到您「自動化」帳戶中的 Hybrid Runbook Worker 群組來支援「自動化」Runbook，只要解決方案和 Hybrid Runbook Worker 群組成員資格兩者所用的帳戶相同即可。 此功能已新增至 Hybrid Runbook Worker 7.2.12024.0 版。

在您成功部署 Runbook 背景工作角色後，請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化。

### <a name="automated-deployment"></a>自動化部署

執行下列步驟，以將 Windows 混合式背景工作角色的安裝和設定自動化。

1. 直接在執行 Hybrid Runbook Worker 角色的電腦上，從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript)下載 *New-OnPremiseHybridWorker.ps1* 指令碼，或從環境中的另一部電腦下載，再複製到背景工作角色。

   *New-OnPremiseHybridWorker.ps1* 指令碼在執行期間需要下列參數：

   * *AutomationAccountName* (必要) - 您的自動化帳戶名稱。
   * *AAResourceGroupName* (必要) - 與您的自動化帳戶相關聯的資源群組名稱
   * *OMSResourceGroupName* (選擇性) - OMS 工作區的資源群組名稱。 若未指定，將會使用 AAResourceGroupName。
   * *HybridGroupName* (必要) - 針對支援此案例的 Runbook，您指定作為目標的「混合式 Runbook 背景工作角色」群組名稱。
   * *SubscriptionID* (必要) - 您自動化帳戶所在的「Azure 訂用帳戶 ID」。
   * *WorkspaceName* (選擇性) - Log Analytics 工作區名稱。 如果您沒有 Log Analytics 工作區，此指令碼會建立並設定一個 Log Analytics 工作區。

     > [!NOTE]
     > 目前，支援與 Log Analytics 整合的自動化區域只包括 - **澳大利亞東南部**、**美國東部 2**、**東南亞**和**西歐**。 如果您的自動化帳戶不在其中一個區域，此指令碼會建立 Log Analytics 工作區，但會警告您，指出無法將它們連結在一起。

1. 在您的電腦上，從 [開始] 畫面以系統管理員模式啟動 **Windows PowerShell**。
1. 從 PowerShell 命令列殼層，瀏覽至您下載的指令碼所在的資料夾，然後執行該指令碼，並變更參數 *-AutomationAccountName*、*-AAResourceGroupName**-OMSResourceGroupName**-HybridGroupName**-SubscriptionId* 與 *-WorkspaceName* 的值。

     > [!NOTE]
     > 執行指令碼之後，您會收到向 Azure 進行驗證的提示。 您**必須**以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入。

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

1. 系統會提示您同意安裝 **NuGet**，也會提示使用您的 Azure 認證進行驗證。

1. 指令碼完成之後，[Hybrid Worker 群組] 頁面會顯示新的群組和成員數目，或如果是現有的群組，則成員數目會遞增。 您可以從 [Hybrid Worker 群組] 頁面上的清單中選取群組，然後選取 [Hybrid Worker] 圖格。 在 [Hybrid Worker] 頁面上，您會看到列出群組的每個成員。

### <a name="manual-deployment"></a>手動部署

對您的自動化環境執行一次前兩個步驟，再對每一台背景工作角色電腦重複其餘步驟。

#### <a name="1-create-log-analytics-workspace"></a>1.建立 Log Analytics 工作區

如果您還沒有 Log Analytics 工作區，則可使用[管理您的工作區](../log-analytics/log-analytics-manage-access.md)中的指示建立一個。 如果您已經有工作區，可以使用現有的工作區。

#### <a name="2-add-automation-solution-to-log-analytics-workspace"></a>2.將自動化解決方案新增至 Log Analytics 工作區

方案會將功能加入 Log Analytics。 自動化解決方案會增加 Azure 自動化的功能，包括支援 Hybrid Runbook Worker。 將解決方案新增至工作區時，它會自動將背景工作角色元件往下推送給您在下一步將安裝的代理程式電腦。

依照[使用方案庫新增解決方案](../log-analytics/log-analytics-add-solutions.md)中的指示，將**自動化**解決方案新增至 Log Analytics 工作區。

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3.安裝 Microsoft Monitoring Agent

Microsoft Monitoring Agent 可將電腦連線至 Log Analytics。 將代理程式安裝在內部部署電腦，並連接到您的工作區時，它會自動下載 Hybrid Runbook Worker 所需的元件。

請依照[將 Windows 電腦連接到 Log Analytics](../log-analytics/log-analytics-windows-agent.md) 中的指示，將代理程式安裝在內部部署電腦上。 您可以對多部電腦重複此程序，將多個背景工作角色加入至您的環境。

當代理程式成功連線到 Log Analytics 時，它會列在 Log Analytics [設定] 頁面的 [連接的來源] 索引標籤上。 當 C:\Program Files\Microsoft Monitoring Agent\Agent 中出現 **AzureAutomationFiles** 資料夾時，就可確認代理程式已正確下載自動化解決方案。 若要確認 Hybrid Runbook Worker 版本，您可以瀏覽至 C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\，並記下 \\version 子資料夾。

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4.安裝 Runbook 環境並連接到 Azure 自動化

將代理程式新增至 Log Analytics 時，自動化解決方案會往下推送包含 **Add-HybridRunbookWorker** Cmdlet 的 **HybridRegistration** PowerShell 模組。 您可以使用這個 Cmdlet 在電腦上安裝 Runbook 環境並向 Azure 自動化進行註冊。

以系統管理員模式開啟 PowerShell 工作階段，並執行下列命令來匯入模組。

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

然後使用下列語法執行 **Add-HybridRunbookWorker** Cmdlet：

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

您可以從 Azure 入口網站的 [管理金鑰] 頁面取得這個 Cmdlet 所需的資訊。 從您「自動化」帳戶的 [設定] 頁面中選取 [金鑰]，以開啟此頁面。

![混合式 Runbook 背景工作概觀](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** 是混合式 Runbook 背景工作角色群組的名稱。 如果自動化帳戶中已有這個群組，那麼會將目前的電腦加入。 如果尚不存在，則會加入。
* **EndPoint** 是 [管理金鑰] 頁面中的 [URL] 欄位。
* **Token** 是 [管理金鑰] 頁面中的 [主要存取金鑰]。

在 **Add-HybridRunbookWorker** 中新增 **-Verbose** 參數可接收安裝的詳細資訊。

#### <a name="5-install-powershell-modules"></a>5.安裝 PowerShell 模組

Runbook 可以使用 Azure 自動化環境中安裝的模組中定義的任何活動和 Cmdlet。 不過，這些模組不會自動部署至內部部署機器，所以您必須手動安裝它們。 例外狀況是預設安裝的 Azure 模組，可提供 Azure 自動化所有 Azure 服務和活動的 Cmdlet 存取權。

由於 Hybrid Runbook Worker 功能的主要目的是要管理本機資源，您很可能必須安裝支援這些資源的模組。 您可以參考 [安裝模組](http://msdn.microsoft.com/library/dd878350.aspx) 來取得安裝 Windows PowerShell 模組的詳細資訊。 安裝的模組必須位於 PSModulePath 環境變數所參考的位置，才能由 Hybrid 背景工作角色自動匯入。 如需詳細資訊，請參閱[修改 PSModulePath 安裝路徑](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx)。

## <a name="troubleshooting"></a>疑難排解

混合式 Runbook 背景工作角色取決於與自動化帳戶通訊的 Microsoft Monitoring Agent，來註冊背景工作角色、接收 Runbook 作業及報告狀態。 如果註冊背景工作角色失敗，請參考以下一些可能的錯誤原因：

### <a name="the-microsoft-monitoring-agent-is-not-running"></a>Microsoft Monitoring Agent 未執行

如果 Microsoft Monitoring Agent Windows 服務並未執行，這可避免混合式 Runbook 背景工作角色與 Azure 自動化進行通訊。 在 PowerShell 中輸入下列命令，確認代理程式正在執行：`Get-Service healthservice`。 如果服務已停止，在 PowerShell 中輸入下列命令可啟動服務：`Start-Service healthservice`。

### <a name="event-4502-in-operations-manager-log"></a>Operations Manager 記錄中的事件 4502

在 [應用程式及服務記錄檔]\[Operations Manager] 事件記錄中，您會看到事件 4502 和 EventMessage 包含 **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**，並具有以下描述：*\<wsid\>.oms.opinsights.azure.com 服務所提供的憑證並非是由 Microsoft 服務所採用之憑證授權單位所產生。請連絡網路管理員，以查看它們是否正在執行可攔截 TLS/SSL 通訊的 Proxy。文章 KB3126513 內提供針對連線能力問題的其他疑難排解資訊。*

這可能是因 Proxy 或網路防火牆封鎖 Microsoft Azure 的通訊所引起。 確認電腦可透過連接埠 443 輸出存取 *.azure-automation.net。

記錄檔儲存每一個混合式背景工作角色本機的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。 您可以檢查是否有任何警告或錯誤事件寫入 **Application and Services Logs\Microsoft-SMA\Operations** 和 **Application and Services Logs\Operations Manager** 事件記錄，可能表示有連線能力或其他會影響角色上架到 Azure 自動化的問題，或執行正常作業時的問題。

如需如何針對更新管理的問題進行疑難排解的其他步驟，請參閱[更新管理 - 疑難排解](automation-update-management.md#troubleshooting)

## <a name="next-steps"></a>後續步驟

* 請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化。
* 如需移除混合式 Runbook 背景工作角色的指示，請參閱[移除 Azure 自動化混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)