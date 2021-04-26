---
title: Azure IoT Hub kaynakları için cihaz güncelleştirmesini anlama | Microsoft Docs
description: Azure IoT Hub kaynakları için cihaz güncelleştirmesini anlama
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101664078"
---
# <a name="device-update-resources"></a>Cihaz güncelleştirme kaynakları

IoT Hub cihaz güncelleştirmesini kullanmak için bir cihaz güncelleştirme hesabı ve örnek kaynağı oluşturmanız gerekir. 

## <a name="device-update-account"></a>Cihaz güncelleştirme hesabı

Bir cihaz güncelleştirme hesabı, Azure aboneliğinizde oluşturulan bir kaynaktır. Cihaz güncelleştirme hesabı düzeyinde, cihaz güncelleştirme hesabınızın oluşturulacağı bölgeyi seçebilirsiniz. Ayrıca, cihaz güncelleştirmesine erişimi olacak kullanıcılara yetki verme izinleri de ayarlayabilirsiniz.


## <a name="device-update-instance"></a>Cihaz güncelleştirme örneği
Bir hesap oluşturulduktan sonra bir cihaz güncelleştirme örneği oluşturmanız gerekir. Örnek, belirli bir IoT Hub ile ilişkili güncelleştirmeleri ve dağıtımları içeren bir mantıksal kapsayıcıdır. Cihaz güncelleştirme, IoT Hub 'ı bir cihaz dizini olarak ve cihazlarıyla bir iletişim kanalı kullanır. 

Genel Önizleme sırasında, her abonelik için iki cihaz güncelleştirme hesabı oluşturulabilir. Ayrıca, hesap başına iki cihaz güncelleştirme örneği oluşturulabilir.

## <a name="configuring-device-update-linked-iot-hub"></a>Cihaz güncelleştirme bağlı IoT Hub yapılandırılıyor 

Cihaz güncelleştirmesinin IoT Hub değişiklik bildirimlerini alabilmesi için, cihaz güncelleştirme "yerleşik" Olay Hub 'ı ile tümleştirilir. Örneğinizdeki "yapılandırma IoT Hub" düğmesine tıklamak, IoT cihazlarıyla iletişim kurmak için gerekli ileti yollarını ve erişim ilkesini yapılandırır. 

Aşağıdaki Ileti yolları cihaz güncelleştirmesi için yapılandırılmıştır:

|   Yol adı    | Yönlendirme sorgusu  | Description  |
| :--------- | :---- |:---- |
|  DeviceUpdate. DigitalTwinChanges | true |Dijital Ikizi değişiklikleri olaylarını dinler  |
|  DeviceUpdate. DeviceLifeCycle | opType = ' Deletedeviceıdentity '  | Silinen cihazları dinler |
|  DeviceUpdate. TelemetryModelInformation | ıothub-interface-id = "urn: azureiot: ModelDiscovery: ModelInformation: 1 | Yeni cihaz türlerini dinler |
|  DeviceUpdate. Devicetwınevents| (opType = ' updateTwin' veya opType = ' ınclucetwin') VE IS_DEFINED ($body. Tags. ADUGroup) | Yeni cihaz güncelleştirme gruplarını dinler |

## <a name="next-steps"></a>Sonraki adımlar

[Cihaz güncelleştirme kaynakları oluşturma](./create-device-update-account.md)
