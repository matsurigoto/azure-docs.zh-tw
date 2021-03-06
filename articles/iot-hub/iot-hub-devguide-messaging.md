---
title: 了解 Azure IoT 中樞傳訊 | Microsoft Docs
description: 開發人員指南 - IoT 中樞的裝置到雲端及雲端到裝置傳訊。 其中包括訊息格式和支援之通訊協定的相關資訊。
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 50f95dc1af334468db25bce68f2ca00e0965a28b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>IoT 中樞的裝置到雲端及雲端到裝置傳訊

使用 IoT 中樞傳訊以下列方式與您的裝置通訊：

* 從您的裝置傳送[裝置對雲端][lnk-d2c]訊息到您的解決方案後端。
* 從解決方案後端傳送[雲端對裝置][lnk-c2d]訊息到您的裝置。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT 中樞傳訊功能的核心屬性是訊息的可靠性和持久性。 這些屬性可在裝置端上恢復間歇性連線，以及在雲端恢復事件處理的負載尖峰。 IoT 中樞會針對裝置到雲端和雲端到裝置訊息，實作「至少一次」  傳遞保證。

如需 IoT 中樞功能的簡介，請參閱 [Azure IoT 中樞服務的概觀][lnk-iot-hub-overview]。

## <a name="when-to-use-iot-hub-messaging"></a>使用 IoT 中樞傳訊時

使用裝置到雲端訊息可傳送裝置應用程式所傳來的時間序列遙測和警示，使用雲端到裝置訊息則可用來傳送單向通知給您的裝置應用程式。

* 如果不確定要使用裝置對雲端訊息、回報屬性或檔案上傳，請參閱[裝置到雲端通訊指引][lnk-d2c-guidance]。
* 如果不確定要使用雲端對裝置訊息、預期屬性或直接方法，請參閱[雲端到裝置通訊指引][lnk-c2d-guidance]。

## <a name="next-steps"></a>後續步驟

* 了解 IoT 中樞[裝置對雲端傳訊][lnk-d2c]。
* 了解 IoT 中樞[雲端對裝置傳訊][lnk-c2d]。

[lnk-azure-iot]: ../iot-fundamentals/index.yml
[lnk-iot-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md