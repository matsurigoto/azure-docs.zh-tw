---
title: 在 Azure IoT Central 中定義新的裝置類型 | Microsoft Docs
description: 本教學課程將為建置者說明如何在 Azure IoT Central 應用程式中定義新的裝置類型。 您會定義類型的遙測、狀態、屬性和設定。
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: e1488b708bbbee67362d834a9a703520d37bef37
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201667"
---
# <a name="1---define-a-new-device-type-in-your-azure-iot-central-application"></a>1 - 在 Azure IoT Central 應用程式中定義新的裝置類型

本教學課程將為建置者說明如何使用裝置範本在 Microsoft Azure IoT Central 應用程式中定義新的裝置類型。 裝置範本會定義裝置類型的遙測、狀態、屬性和設定。

為了讓您能夠先測試應用程式再連接實際裝置，Azure IoT Central 會在您建立裝置範本時，從範本產生模擬裝置。

在本教學課程中，您會建立**連線的空調**裝置範本。 連線的空調裝置：

* 傳送遙測資料，例如溫度和濕度。
* 回報狀態，例如裝置處於開啟還是關閉狀態。
* 具有屬性，例如裝置的韌體版本和序號。
* 具有設定，例如裝置的目標溫度和風扇速度。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立新的裝置範本
> * 將遙測資料新增至裝置
> * 檢視模擬的遙測資料
> * 定義事件測量
> * 檢視模擬的事件
> * 定義狀態測量
> * 檢視模擬的狀態
> * 使用裝置屬性
> * 使用裝置設定

## <a name="prerequisites"></a>先決條件

若要完成本快速入門，您需要 Azure IoT Central 應用程式。 如果您已完成[建立 Azure IoT Central 應用程式](quick-deploy-iot-central.md)快速入門，則可以重複使用您在該快速入門中建立的應用程式。 否則，請完成下列步驟以建立空的 Azure IoT Central 應用程式：

1. 瀏覽至 Azure IoT Central 的[應用程式管理員](https://aka.ms/iotcentral)頁面。

1. 輸入您用來存取 Azure 訂用帳戶的電子郵件地址和密碼：

   ![輸入您的組織帳戶](media/tutorial-define-device-type/sign-in.png)

1. 若要開始建立新的 Azure IoT Central 應用程式，請選擇 [新增應用程式]：

    ![Azure IoT Central 應用程式管理員頁面](media/tutorial-define-device-type/iotcentralhome.png)

1. 若要建立新的 Azure IoT Central 應用程式：

    1. 選擇易記的應用程式名稱，例如 **Contoso Air Conditioners**。 Azure IoT Central 會為您產生唯一的 URL 前置詞。 您可以將其變更為更好記的 URL 前置詞。
    1. 選擇要使用的 Azure Active Directory 和 Azure 訂用帳戶。 如需關於目錄和訂用帳戶的詳細資訊，請參閱[建立 Azure IoT Central 應用程式](howto-create-application.md)。
    1. 使用現有的資源群組，或以您選擇的名稱建立一個新群組。 例如 **contoso-rg**。
    1. 選擇最接近您所在位置的區域。
    1. 選擇 [自訂應用程式] 應用程式範本。
    1. 選擇 [30 天免費試用版應用程式] 付款方案。
    1. 然後選擇 [建立]。

    ![Azure IoT Central 的建立應用程式頁面](media/tutorial-define-device-type/iotcentralcreate.png)

如需詳細資訊，請參閱[如何建立 Azure IoT Central 應用程式](howto-create-application.md)。

## <a name="create-a-new-custom-device-template"></a>建立新的自訂裝置範本

身為建置者，您可以在應用程式中建立和編輯裝置範本。 當您建立裝置範本時，Azure IoT Central 會從範本產生模擬裝置。 模擬裝置會產生遙測資料，讓您可先測試應用程式的行為，再連接實體裝置。

若要將新的裝置範本新增至應用程式，您必須移至 [應用程式建置者] 頁面。 若要這麼做，請選擇左側導覽功能表上的 [應用程式建置者]：

    ![Application Builder page](media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>新增裝置並定義遙測

下列步驟說明如何為要將溫度遙測資料傳送至應用程式的裝置建立新的**連線的空調**裝置範本：

1. 在 [應用程式建置者] 頁面上，選擇 [建立裝置範本]：

    ![應用程式建置者頁面，建立裝置範本](media/tutorial-define-device-type/builderhomedevices.png)

1. 在 [裝置範本] 頁面上，選擇 [自訂]。 [自訂] 裝置範本可讓您為連線的空調您定義所有特性和行為：

    ![裝置](media/tutorial-define-device-type/builderhomedevicescustom.png)

1. 在 [新增裝置範本] 頁面上，輸入**連線的空調**作為裝置名稱，然後選擇 [建立]。 您也可以上傳操作員在裝置總管中可見的裝置影像：

    ![自訂裝置](media/tutorial-define-device-type/createcustomdevice.png)

1. 在 [連線的空調] 裝置範本中，確定您是在定義遙測資料的 [測量] 頁面上。 您所定義的每個裝置範本都有個別的頁面，以供您：

    * 指定裝置所傳送的測量，例如遙測、事件和狀態。
    * 定義用來控制裝置的設定。
    * 定義用來記錄裝置相關資訊的屬性。
    * 定義與裝置相關聯的規則。
    * 自訂操作員的裝置儀表板。

    ![空調測量](media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > 若要變更裝置或裝置範本的名稱，請按一下頁面頂端的文字。

1. 若要新增溫度遙測測量，請選擇 [新增測量]。 然後，選擇 [遙測] 作為測量類型：

    ![連線的空調測量](media/tutorial-define-device-type/airconmeasurementsnew.png)

1. 您為裝置範本定義的每種遙測類型都會包含[組態選項](howto-set-up-template.md)，例如：

    * 顯示選項。
    * 遙測的詳細資料。
    * 模擬參數。

    若要設定**溫度**遙測，請使用下表中的資訊：

    | 設定              | 值         |
    | -------------------- | -----------   |
    | 顯示名稱         | 溫度   |
    | 欄位名稱           | 溫度   |
    | Units                | F             |
    | Min                  | 60            |
    | max                  | 110           |
    | 小數位數       | 0             |

    您也可以選擇遙測顯示的色彩。 若要儲存遙測定義，請選擇 [儲存]：

    ![設定溫度模擬](media/tutorial-define-device-type/temperaturesimulation.png)

1. 不久之後，[測量] 頁面即會顯示從模擬的連線空調裝置產生的溫度遙測資料的圖表。 您可以使用控制項管理可見性和彙總，或編輯遙測定義：

    ![檢視溫度模擬](media/tutorial-define-device-type/viewsimulation.png)

1. 您也可以使用 [列]、[堆疊] 和 [編輯時間範圍] 等控制項來自訂圖表：

    ![自訂圖表](media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>定義事件測量
您可以使用事件來定義裝置所傳送的時間點資料，以指出具有重要性的事項，例如錯誤或元件失敗。 如同遙測測量，Azure IoT Central 也可模擬裝置事件，而讓您能夠先測試應用程式的行為，再連接實體裝置。 您可以在 [測量] 檢視中為您的裝置類型定義事件測量。

1. 若要新增 [風扇馬達錯誤] 事件測量，請選擇 [新增測量]。 然後，選擇 [事件] 作為測量類型：

    ![連線的空調測量](media/tutorial-define-device-type/eventnew.png)

1. 您為裝置範本定義的每種事件類型都會包含[組態選項](howto-set-up-template.md)，例如：

    * 顯示名稱。
    * 欄位名稱。
    * 嚴重性。

    若要設定 [風扇馬達錯誤] 事件，請使用下表中的資訊：

    | 設定              | 值             |
    | -------------------- | -----------       |
    | 顯示名稱         | 風扇馬達錯誤   |
    | 欄位名稱           | fanmotorerr       |
    | 嚴重性             | Error             |

    若要儲存事件定義，請選擇 [儲存]：

    ![設定事件測量](media/tutorial-define-device-type/eventconfiguration.png)

1. 不久之後，[測量] 頁面即會顯示從模擬的連線空調裝置隨機產生之事件的圖表。 您可以使用控制項管理可見性，或編輯事件定義：

    ![檢視事件模擬](media/tutorial-define-device-type/eventview.png)

1. 若要檢視關於事件的其他詳細資料，請按一下圖表上的事件：

    ![檢視事件詳細資料](media/tutorial-define-device-type/eventviewdetail.png)


## <a name="define-state-measurement"></a>定義狀態測量
您可以使用 [狀態] 來定義和視覺化裝置或其元件在一段時間內的狀態。 如同遙測測量，Azure IoT Central 也可模擬裝置狀態，而讓您能夠先測試應用程式的行為，再連接實體裝置。 您可以在 [測量] 檢視中為您的裝置類型定義狀態測量。

1. 若要新增 [風扇模式]，請選擇 [新增測量]。 然後，選擇 [狀態] 作為測量類型：

    ![連線的空調狀態測量](media/tutorial-define-device-type/statenew.png)

1. 您為裝置範本定義的每種狀態類型都會包含[組態選項](howto-set-up-template.md)，例如：

    * 顯示名稱。
    * 欄位名稱。
    * 選用顯示標籤的值。
    * 每個值的色彩

    若要設定 [風扇模式] 狀態，請使用下表中的資訊：

    | 設定              | 值             |
    | -------------------- | -----------       |
    | 顯示名稱         | 風扇模式          |
    | 欄位名稱           | fanmode           |
    | 值                | 1                 |
    | 顯示標籤        | 運作中         |
    | 值                | 0                 |
    | 顯示標籤        | 已停止           |

    若要儲存狀態測量定義，請選擇 [儲存]：

    ![設定狀態測量](media/tutorial-define-device-type/stateconfiguration.png)

1. 不久之後，[測量] 頁面即會顯示從模擬的連線空調裝置隨機產生之狀態的圖表。 您可以使用控制項管理可見性，或編輯狀態定義：

    ![檢視狀態模擬](media/tutorial-define-device-type/stateview.png)

1. 如果裝置在一小段時間內傳送太多資料點，狀態測量在顯示時就會使用不同的視覺效果，如下所示。 如果您按一下圖表，則該時段內的所有資料點將會依時間先後順序顯示。 您也可以縮小時間範圍，以查看圖表上所繪製的測量。

    ![檢視狀態詳細資料](media/tutorial-define-device-type/stateviewdetail.png)

## <a name="properties-device-properties-and-settings"></a>屬性、裝置屬性和設定

屬性、裝置屬性和設定是定義於裝置範本中，且與個別裝置相關聯的不同值：

* 您可以使用_設定_將組態資料從您的應用程式傳送至裝置。 例如，操作員可以使用設定將裝置的遙測間隔從兩秒變更為五秒。 當操作員變更設定時，在裝置確認它已處理設定變更之前，設定在 UI 中會標示為擱置中。
* 您可以使用_屬性_來記錄與您應用程式中的裝置有關的資訊。 例如，您可以使用屬性來記錄裝置的序號或裝置製造商的電話號碼。 屬性會儲存在應用程式中，且不會與裝置同步處理。 操作員可以將值指派給屬性。
* 您可以使用_裝置屬性_，讓裝置能夠將屬性值傳送至您的應用程式。 只有裝置可以變更這些屬性。 對操作員而言，裝置屬性是唯讀的。

## <a name="use-settings"></a>使用設定

您可以使用_設定_，讓操作員能夠將組態資料傳送至裝置。 在本節中，您會將設定新增**連線的空調**裝置範本，讓操作員為連線的空調設定目標溫度。

1. 瀏覽至 [連線的空調] 裝置範本的 [設定] 頁面：

    ![準備新增設定](media/tutorial-define-device-type/deviceaddsetting.png)

    您可以建立不同類型的設定，例如數值或文字。

1. 選擇 [數值]，將數值設定新增至裝置。

1. 若要設定 [設定溫度] 設定，請使用下表中的資訊：

    | 欄位                | 值           |
    | -------------------- | -----------     |
    | 顯示名稱         | 設定溫度 |
    | 欄位名稱           | setTemperature  |
    | 測量單位  | F               |
    | 小數位數       | 1               |
    | 最小值        | 20              |
    | 最大值        | 200             |
    | 初始值        | 80              |
    | 說明          | 設定空調的目標溫度 |

    然後，選擇 [儲存]：

    ![設定「設定溫度」設定](media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > 當裝置確認設定變更之後，該設定的狀態將會變更為 [已同步]。

1. 您可以藉由移動設定圖格和調整其大小，來自訂 [設定] 頁面的版面配置：

    ![自訂設定版面配置](media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>使用屬性

您可以使用_屬性_來儲存與應用程式中的裝置有關的資訊。 在本節中，您會將屬性新增至**連線的空調**裝置範本，以儲存每個裝置的裝置序號和韌體版本。

1. 瀏覽至 [連線的空調] 裝置範本的 [屬性] 頁面：

    ![準備新增屬性](media/tutorial-define-device-type/deviceaddproperty.png)

    您可以建立不同類型的屬性，例如數值或文字。 若要將序號屬性新增至裝置範本，請選擇 [文字]。

1. 若要設定序號屬性，請使用下表中的資訊：

    | 欄位                | 值                |
    | -------------------- | -------------------- |
    | 顯示名稱         | 序號        |
    | 欄位名稱           | serialNumber         |
    | 初始值        | cac00001             |
    | 說明          | 裝置序號 |

    讓其他欄位保留其預設值。

    ![設定裝置屬性](media/tutorial-define-device-type/configureproperties.png)

    然後，選擇 [儲存]。

1. 若要將韌體版本屬性新增至裝置範本，請選擇 [文字]

1. 若要設定韌體版本屬性，請使用下表中的資訊：

    | 欄位                | 值                   |
    | -------------------- | ----------------------- |
    | 顯示名稱         | 韌體版本        |
    | 欄位名稱           | firmwareVersion         |
    | 初始值        | 0.1                     |
    | 說明          | 裝置韌體版本 |

    ![設定裝置屬性](media/tutorial-define-device-type/configureproperties2.png)

    然後，選擇 [儲存]。

1. 您可以藉由移動屬性圖格和調整其大小，來自訂 [屬性] 頁面的版面配置：

    ![自訂屬性版面配置](media/tutorial-define-device-type/propertieslayout.png)

## <a name="view-your-simulated-device"></a>檢視模擬裝置

現在，您已定義**連線的空調**裝置範本，接下來可以自訂其**儀表板**，以包含您所定義的測量、設定和屬性。 然後，您可以用操作員的身分預覽儀表板：

1. 選擇 [連線的空調] 裝置範本的 [儀表板] 頁面：

    ![連線的空調儀表板](media/tutorial-define-device-type/aircondashboards.png)

1. 選擇 [折線圖]，以在 [儀表板] 上新增元件：

    ![儀表板元件](media/tutorial-define-device-type/dashboardcomponents1.png)

1. 使用下表中的資訊設定 [折線圖] 元件：

    | 設定      | 值       |
    | ------------ | ----------- |
    | 標題        | 溫度 |
    | 時間範圍   | 過去 30 分鐘 |
    | 測量 | 溫度 (選擇 [溫度] 旁的 [可見性]) |

    ![折線圖設定](media/tutorial-define-device-type/linechartsettings.png)

    然後，選擇 [儲存]。

1. 使用下表中的資訊設定 [事件圖] 元件：

    | 設定      | 值       |
    | ------------ | ----------- |
    | 標題        | 活動 |
    | 時間範圍   | 過去 30 分鐘 |
    | 測量 | 風扇馬達錯誤 (選擇 [風扇馬達錯誤] 旁的 [可見性]) |

    ![折線圖設定](media/tutorial-define-device-type/dashboardeventchartsetting.png)

    然後，選擇 [儲存]。

1. 使用下表中的資訊設定 [狀態圖] 元件：

    | 設定      | 值       |
    | ------------ | ----------- |
    | 標題        | 風扇模式 |
    | 時間範圍   | 過去 30 分鐘 |
    | 測量 | 風扇馬達 (選擇 [風扇模式] 旁的 [可見性]) |

    ![折線圖設定](media/tutorial-define-device-type/dashboardstatechartsetting.png)

    然後，選擇 [儲存]。

1. 若要將「設定溫度」設定新增至儀表板，請選擇 [設定和屬性]：

    ![儀表板元件](media/tutorial-define-device-type/dashboardcomponents4.png)

1. 使用下表中的資訊設定 [設定和屬性] 元件：

    | 設定                 | 值         |
    | ----------------------- | ------------- |
    | 標題                   | 設定目標溫度 |
    | 設定和屬性 | 設定溫度 |

    ![序號屬性設定](media/tutorial-define-device-type/propertysettings3.png)

    然後，選擇 [儲存]。

1. 若要將裝置序號新增至儀表板，請選擇 [設定和屬性]：

    ![儀表板元件](media/tutorial-define-device-type/dashboardcomponents3.png)

1. 使用下表中的資訊設定 [設定和屬性] 元件：

    | 設定                 | 值         |
    | ----------------------- | ------------- |
    | 標題                   | 序號 |
    | 設定和屬性 | 序號 |

    ![序號屬性設定](media/tutorial-define-device-type/propertysettings1.png)

    然後，選擇 [儲存]。

1. 若要將裝置韌體版本新增至儀表板，請選擇 [設定和屬性]：

    ![儀表板元件](media/tutorial-define-device-type/dashboardcomponents4.png)

1. 使用下表中的資訊設定 [設定和屬性] 元件：

    | 設定                 | 值            |
    | ----------------------- | ---------------- |
    | 標題                   | 韌體版本 |
    | 設定和屬性 | 韌體版本 |

    ![序號屬性設定](media/tutorial-define-device-type/propertysettings2.png)

    然後，選擇 [儲存]。

1. 若要以操作員的身分檢視儀表板，請將頁面右上方的 [設計模式] 切換為關閉。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * 建立新的裝置範本
> * 將遙測資料新增至裝置
> * 檢視模擬的遙測資料
> * 定義裝置事件
> * 檢視模擬的事件
> * 定義狀態
> * 檢視模擬的狀態
> * 使用裝置屬性
> * 使用裝置設定

現在，您已在 Azure IoT Central 應用程式中定義裝置範本，以下是建議的後續步驟：

* [為您的裝置設定規則和動作](tutorial-configure-rules.md)
* [自訂操作員的檢視](tutorial-customize-operator.md)
