---
title: 將存取原則指派給 Azure Service Fabric 服務端點 | Microsoft Docs
description: 了解如何將安全性存取原則指派給 Service Fabric 服務中的 HTTP 或 HTTPS 端點。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: f9de8d213d11a8ccb3ffff484a67560d9e2abe77
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>為 HTTP 和 HTTPS 端點指派安全性存取原則
如果您套用執行身分原則，而且服務資訊清單宣告 HTTP 端點資源，您就必須指定 **SecurityAccessPolicy**。  **SecurityAccessPolicy** 可確保配置給這些端點的連接埠受到正確限制，唯有執行服務的使用者帳戶才能使用。 否則， **http.sys** 無法存取服務，而且您從用戶端呼叫時將會失敗。 下列範例會將 Customer1 帳戶套用到名為 **EndpointName** 的端點，這會給予它完整的存取權限。

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

針對 HTTPS 端點，請同時指出要傳回給用戶端的憑證名稱。 您可以使用 **EndpointBindingPolicy** 參考憑證。  憑證的定義位於應用程式資訊清單的 [憑證] 區段。

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
若要了解後續步驟，請閱讀下列文章：
* [了解應用程式模型](service-fabric-application-model.md)
* [在服務資訊清單中指定資源](service-fabric-service-manifest-resources.md)
* [部署應用程式](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
