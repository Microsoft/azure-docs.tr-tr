---
title: Azure IoT Hub yapılandırma dosyası için cihaz güncelleştirmesini anlama | Microsoft Docs
description: Azure IoT Hub yapılandırma dosyası için cihaz güncelleştirmesini anlayın.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101663829"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>IoT Hub yapılandırma dosyası için cihaz güncelleştirmesi

"adu-conf.txt", aşağıdaki konfigürasyonları yönetmek için oluşturulabilecek isteğe bağlı bir dosyadır.

* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["üreticisi"]
* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["model"]
* Deviceınformation. Manufacturer
* Deviceınformation. model
* Cihaz bağlantı dizesi (Cihaz Güncelleştirme Aracısı tarafından bilinmiyorsa).

## <a name="purpose"></a>Amaç
Cihaz Güncelleştirme Aracısı öncelikle, `manufacturer` `model` [arabirim katmanı](device-update-agent-overview.md#the-interface-layer)için kullanılacak cihazdan ve değerlerini almaya çalışır. Bu işlem başarısız olursa, cihaz Güncelleştirme Aracısı bir sonraki "adu-conf.txt" dosyasını arayacaktır ve buradan değerleri kullanır. Her iki girişim de başarılı olmazsa, cihaz Güncelleştirme Aracısı [varsayılan](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt) değerleri kullanır.

[ADU Core arabirimi](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) ve [cihaz bilgileri arabirimi](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)hakkında daha fazla bilgi edinin.

## <a name="file-location"></a>Dosya konumu

Linux sisteminde, bölüm veya disk içinde, `adu` aşağıdaki alanlarla "adu-conf.txt" adlı bir metin dosyası oluşturun.

## <a name="list-of-fields"></a>Alan listesi

|Ad|Açıklama|
|-----------|--------------------|
|connection_string|Cihazın IoT Hub bağlanmak için kullanabileceği, önceden sağlanmış bağlantı dizesi. Not: [Azure IoT kimlik hizmeti](https://azure.github.io/iot-identity-service/) aracılığıyla cihaz Güncelleştirme Aracısı sağlıyorsanız gerekli değildir|
|aduc_manufacturer|`AzureDeviceUpdateCore:4.ClientMetadata:4`Cihazın güncelleştirme dağıtımını hedeflemek için sınıflandırılacağı arabirim tarafından bildirilir.|
|aduc_model|`AzureDeviceUpdateCore:4.ClientMetadata:4`Cihazın güncelleştirme dağıtımını hedeflemek için sınıflandırılacağı arabirim tarafından bildirilir.|
|üretici|Arabirimin bir parçası olarak cihaz Güncelleştirme Aracısı tarafından bildirilir `DeviceInformation` .|
|model|Arabirimin bir parçası olarak cihaz Güncelleştirme Aracısı tarafından bildirilir `DeviceInformation` .|

## <a name="example-adu-conftxt-file-contents"></a>Örnek "adu-conf.txt" dosya içerikleri

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
