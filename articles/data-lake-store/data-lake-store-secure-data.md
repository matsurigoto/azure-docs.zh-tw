---
title: 保護儲存在 Azure Data Lake Store 中的資料 | Microsoft Docs
description: 了解如何使用群組和存取控制清單來保護 Azure 資料湖儲存區中的資料
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 5b83f02c55d0aa7b2e122d7fc8c9ef5734cdd924
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>保護儲存在 Azure 資料湖儲存區中的資料
保護儲存在 Azure 資料湖儲存區中的資料，其方法有三步驟。  必須設定角色型存取控制 (RBAC) 和存取控制清單 (ACL) 都，才能完整啟用使用者和安全性群組之資料的存取權。

1. 首先，在 Azure Active Directory (AAD) 中建立安全性群組。 這些安全性群組是用在 Azure 入口網站中實作角色型存取控制 (RBAC)。 如需詳細資訊，請參閱 [Microsoft Azure 中的角色型存取控制](../role-based-access-control/role-assignments-portal.md)。
2. 指派 AAD 安全性群組給 Azure 資料湖儲存區帳戶。 這會控制從入口網站至資料湖儲存區帳戶，以及從入口網站或 API 至管理作業的存取。
3. 指派 AAD 安全性群組做為資料湖儲存區檔案系統上的存取控制清單 (ACL)。
4. 此外，您也可以針對可以存取 Data Lake Store 中資料的用戶端設定 IP 位址範圍。

本文提供有關如何使用 Azure 入口網站執行上述工作的詳細指示。 如需 Data Lake Store 如何在帳戶和資料層級實作安全性的深入資訊，請參閱 [Azure Data Lake Store 安全性](data-lake-store-security-overview.md)。 如需 ACL 如何在 Azure Data Lake Store 中實作的深入資訊，請參閱 [Data Lake Store 中的存取控制的概觀](data-lake-store-access-control.md)。

## <a name="prerequisites"></a>先決條件
開始進行本教學課程之前，您必須具備下列條件：

* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure 資料湖儲存區帳戶**。 如需有關如何建立帳戶的詳細指示，請參閱 [開始使用 Azure 資料湖儲存區](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>在 Azure Active Directory 中建立安全性群組
如需有關如何建立 AAD 安全性群組及如何新增使用者至群組的詳細指示，請參閱 [管理 Azure Active Directory 中的安全性群組](../active-directory/active-directory-groups-create-azure-portal.md)。

> [!NOTE] 
> 您可以將使用者及其他群組新增至使用 Azure 入口網站的 Azure AD 的群組中。 不過，若要將服務主體新增至群組，請使用 [Azure AD 的 PowerShell 模組](../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md)。
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>指派使用者或安全性群組給 Azure 資料湖儲存區帳戶
當您指派使用者或安全性群組給 Azure 資料湖儲存區帳戶時，您可使用 Azure 入口網站和 Azure 資源管理員 API 來控制該帳戶的管理作業存取。 

1. 開啟 Azure 資料湖儲存區帳戶。 從左窗格按一下 [所有資源]，然後從 [所有資源] 刀鋒視窗中，按一下您要將使用者或安全性群組指派給它的帳戶名稱。

2. 在 Data Lake Store 帳戶刀鋒視窗中，按一下 [存取控制 (IAM)]。 根據預設，此刀鋒視窗會將訂用帳戶擁有者列為擁有者。
   
    ![指派安全性群組給 Azure Data Lake Store 帳戶](./media/data-lake-store-secure-data/adl.select.user.icon.png "指派安全性群組給 Azure Data Lake Store 帳戶")

3. 在 [存取控制 (IAM)] 刀鋒視窗中，按一下 [新增] 以開啟 [新增權限] 刀鋒視窗。 在 [新增權限] 刀鋒視窗中，針對使用者/群組選取 [角色]。 搜尋您稍早在 Azure Active Directory 中建立的安全性群組，並加以選取。 如果您需要搜尋大量的使用者和群組，請使用 [選取] 文字方塊來篩選群組名稱。 
   
    ![新增使用者的角色](./media/data-lake-store-secure-data/adl.add.user.1.png "新增使用者的角色")
   
    [擁有者] 和 [參與者] 角色會提供 Data Lake 帳戶上各種不同管理功能的存取。 對於將與 Data Lake 中的資料互動，但仍需要檢視帳戶管理資訊的使用者，您可以將他們新增至 [讀者] 角色。 這些角色的範圍僅限於與 Azure 資料湖儲存區帳戶相關的管理作業。
   
    針對資料作業，個別的檔案系統權限會定義使用者的使用範圍。 因此，擁有 [讀取器] 角色的使用者僅可檢視與帳戶相關的管理設定，但是可依指派給該使用者的檔案系統權限來讀取和寫入資料。 有關資料湖儲存區檔案系統權限的相關資訊都在 [將安全性群組以 ACL 型式指派給 Azure 資料湖儲存區檔案系統](#filepermissions)。

    > [!IMPORTANT]
    > 只有 [擁有者] 角色會自動啟用檔案系統存取權。 [參與者]、[讀者] 和其他所有角色都需要 ACL，才能啟用資料夾和檔案的任何存取層級。  [擁有者] 角色提供進階使用者檔案和資料夾權限，這些權限無法透過 ACL 覆寫。 如需有關 RBAC 原則如何對應到資料存取的詳細資訊，請參閱[用於帳戶管理的 RBAC](data-lake-store-security-overview.md#rbac-for-account-management)。

4. 如果您想要新增未列於 [新增權限] 刀鋒視窗中的群組/使用者，您可以藉由在 [選取] 文字方塊中輸入他們的電子郵件地址，然後從清單中選取來邀請他們。
   
    ![新增安全性群組](./media/data-lake-store-secure-data/adl.add.user.2.png "新增安全性群組")
   
5. 按一下 [檔案] 。 您會看見新增的安全性群組，如下所示。
   
    ![已新增的安全性群組](./media/data-lake-store-secure-data/adl.add.user.3.png "已新增的安全性群組")

6. 您的使用者/安全性群組現在可以存取 Azure 資料湖儲存區帳戶。 若要將存取權授與特定使用者，您可以將他們新增至安全性群組。 同樣地，若要撤銷使用者的存取權，您可以將他們從安全性群組中移除。 您也可以將多個安全性群組指派給一個帳戶。 

## <a name="filepermissions"></a>將使用者或安全性群組以 ACL 型式指派給 Azure Data Lake Store 檔案系統
藉由指派使用者/安全性群組給 Azure 資料湖檔案系統，您可以針對儲存在 Azure 資料湖儲存區中的資料設定存取控制。

1. 在您的 [Data Lake Store 帳戶] 刀鋒視窗中，按一下 [資料總管] 。
   
    ![透過資料總管檢視資料](./media/data-lake-store-secure-data/adl.start.data.explorer.png "透過資料總管檢視資料")
2. 在 [資料總管] 刀鋒視窗中，按一下您想要設定 ACL 的資料夾，然後按一下 [存取]。 若要將 ACL 指派至檔案，您必須先按一下檔案進行預覽，然後從 [檔案預覽] 刀鋒視窗按一下 [存取]。
   
    ![設定 Data Lake 檔案系統上的 ACL](./media/data-lake-store-secure-data/adl.acl.1.png "設定 Data Lake 檔案系統上的 ACL")
3. [存取] 刀鋒視窗會列出擁有者，且指派的權限已經指派給根。 按一下 [新增] 圖示以新增其他存取 ACL。
    > [!IMPORTANT]
    > 設定單一檔案的存取權限不一定會將使用者/群組存取權授與該檔案。 檔案的路徑必須是已指派的使用者/群組可以存取的。 如需詳細資訊和相關範例，請參閱[權限相關的常見案例](data-lake-store-access-control.md#common-scenarios-related-to-permissions)。
   
    ![列出標準和自訂存取](./media/data-lake-store-secure-data/adl.acl.2.png "列出標準和自訂的存取")
   
   * **擁有者**和**其他所有人**提供 UNIX 樣式存取，可讓您將讀取、寫入、執行 (rwx) 指定給三個不同的使用者類別：擁有者、群組和其他。
   * **已指派權限**會對應至 POSIX ACL，讓您除了設定檔案擁有者或群組的權限外，還可以設定特定具名使用者或群組的權限。 
     
     如需詳細資訊，請參閱 [HDFS ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)。 如需 ACL 如何在 Data Lake Store 中實作的詳細資訊，請參閱 [Data Lake Store 中的存取控制](data-lake-store-access-control.md)。
4. 按一下 [新增] 圖示，以開啟 [指派權限] 刀鋒視窗。 在此刀鋒視窗中，按一下 [選取使用者或群組]，然後在 [選取使用者或群組] 刀鋒視窗中，搜尋您稍早在 Azure Active Directory 中建立的安全性群組。 若您需要搜尋大量的群組，請使用頂端的文字方塊來篩選群組名稱。 按一下您要新增的群組，然後按一下 [選取] 。
   
    ![加入群組](./media/data-lake-store-secure-data/adl.acl.3.png "加入群組")
5. 按一下 [選取權限]，選取權限、權限是否應該以遞迴方式套用，以及您想要將權限指派為存取 ACL、預設 ACL 或兩者並用。 按一下 [確定]。
   
    ![將權限指派至群組](./media/data-lake-store-secure-data/adl.acl.4.png "將權限指派至群組")
   
    如需有關 Data Lake Store 中的權限及預設/存取 ACL 的詳細資訊，請參閱 [Data Lake Store 中的存取控制](data-lake-store-access-control.md)。
6. 按一下 [選取權限] 刀鋒視窗中的 [確定] 之後，新增的群組和相關聯權限現在會列在 [存取] 刀鋒視窗中。
   
    ![將權限指派至群組](./media/data-lake-store-secure-data/adl.acl.5.png "將權限指派至群組")
   
   > [!IMPORTANT]
   > 在目前版本中，您在 [已指派權限] 底下最多可有 28 個項目。 如要新增超過 28 位使用者，您必須建立安全性群組、新增使用者至安全性群組，並新增存取權限給該 Data Lake Store 帳戶的安全性群組。
   > 
   > 
7. 如有需要，您也可以在新增群組之後修改存取權限。 根據您是否要移除或指派該權限給安全性群組，選擇清除或選取每個權限類型的核取方塊 (讀取、寫入、執行)。 按一下 [儲存] 儲存變更，或按一下 [捨棄] 復原變更。

## <a name="set-ip-address-range-for-data-access"></a>設定資料存取的 IP 位址範圍
Azure Data Lake Store 可讓您進一步在網路層級鎖定資料存放區的存取。 您可以啟用防火牆、指定 IP 位址或為受信任的用戶端定義 IP 位址範圍。 一旦啟用，只有具有定義範圍內 IP 位址的用戶端可以連線到存放區。

![防火牆設定和 IP 存取](./media/data-lake-store-secure-data/firewall-ip-access.png "防火牆設定和 IP 位址")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>移除 Azure 資料湖儲存區帳戶的安全性群組
當您從 Azure 資料湖儲存區帳戶移除安全性群組時，僅會變更使用 Azure 入口網站和 Azure 資源管理員 API 的帳戶上，其管理作業的存取。  

資料的存取權不變，仍然由存取 ACL 管理。  例外狀況是「擁有者」角色中的使用者/群組。  從「擁有者」角色中移除的使用者/群組不再是進階使用者，且其存取權會回復為存取 ACL 設定。 

1. 在 Data Lake Store 帳戶刀鋒視窗中，按一下 [存取控制 (IAM)]。 
   
    ![指派安全性群組給 Azure Data Lake 帳戶](./media/data-lake-store-secure-data/adl.select.user.icon.png "指派安全性群組給 Azure Data Lake 帳戶")
2. 在 [存取控制 (IAM)]  刀鋒視窗中，按一下您想要移除的安全性群組。 按一下 [移除] 。
   
    ![已移除的安全性群組](./media/data-lake-store-secure-data/adl.remove.group.png "已移除的安全性群組")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>從 Azure 資料湖儲存區檔案系統移除安全性群組 ACL
當您從 Azure Data Lake Store 檔案系統中移除安全性群組 ACL 時，會變更 Data Lake Store 資料的存取。

1. 在您的 [Data Lake Store 帳戶] 刀鋒視窗中，按一下 [資料總管] 。
   
    ![在 Data Lake 帳戶中建立目錄](./media/data-lake-store-secure-data/adl.start.data.explorer.png "在 Data Lake 帳戶中建立目錄")
2. 在 [資料總管] 刀鋒視窗中，按一下您想要移除 ACL 的資料夾，然後按一下 [存取]。 若要移除檔案的 ACL，您必須先按一下檔案進行預覽，然後從 [檔案預覽] 刀鋒視窗按一下 [存取]。 
   
    ![設定 Data Lake 檔案系統上的 ACL](./media/data-lake-store-secure-data/adl.acl.1.png "設定 Data Lake 檔案系統上的 ACL")
3. 在 [存取] 刀鋒視窗中，按一下您要移除的安全性群組。 在 [存取詳細資料] 刀鋒視窗中，按一下 [移除]。
   
    ![將權限指派至群組](./media/data-lake-store-secure-data/adl.remove.acl.png "將權限指派至群組")

## <a name="see-also"></a>另請參閱
* [Azure Data Lake Store 概觀](data-lake-store-overview.md)
* [將資料從 Azure 儲存體 Blob 複製到資料湖存放區](data-lake-store-copy-data-azure-storage-blob.md)
* [搭配資料湖存放區使用 Azure 資料湖分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [搭配 Data Lake Store 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
* [使用 PowerShell 開始使用資料湖存放區](data-lake-store-get-started-powershell.md)
* [使用 .NET SDK 開始使用資料湖存放區](data-lake-store-get-started-net-sdk.md)
* [存取 Data Lake Store 的診斷記錄](data-lake-store-diagnostic-logs.md)

