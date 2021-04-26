---
title: Azure IoT Central operatör Kılavuzu
description: Azure IoT Central, IoT çözümlerinin oluşturulmasını kolaylaştıran bir IoT uygulama platformudur. Bu makale, IoT Central operatör rolüne genel bir bakış sağlar.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b8692973f187743e282de6f8e54a55363cc3105b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669952"
---
# <a name="iot-central-operator-guide"></a>IoT Central operatör Kılavuzu

*Bu makale işleçler için geçerlidir.*

IoT Central bir uygulama, yaşam döngüsü boyunca milyonlarca cihazı izlemenize ve yönetmenize olanak sağlar. Bu kılavuz, IoT cihazlarını yönetmek için bir IoT Central uygulaması kullanan işleçlere yöneliktir.

Bir işleç:

- Uygulamaya bağlı cihazları izler ve yönetir.
- Cihazların sorunlarını giderin ve sorunları giderin.
- Yeni cihazları sağlar.

## <a name="monitor-and-manage-devices"></a>Cihazları izleme ve yönetme

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="Bir cihaz görünümünü gösteren ekran görüntüsü":::

Bir operatör, cihazları izlemek için, çözüm Oluşturucu tarafından tanımlanan cihaz görünümlerini cihaz şablonunun bir parçası olarak kullanabilir. Bu görünümler cihaz telemetrisini ve özellik değerlerini gösterebilir. Bir örnek, önceki ekran görüntüsünde gösterilen **genel bakış** görünümüdür.

Daha ayrıntılı bilgi için bir operatör cihaz gruplarını ve yerleşik analiz özelliklerini kullanabilir. Daha fazla bilgi edinmek için bkz. analiz [kullanarak cihaz verilerini çözümleme](howto-create-analytics.md).

Tek tek cihazları yönetmek için bir operatör cihaz görünümlerini kullanarak cihaz ve bulut özelliklerini ayarlayabilir ve cihaz komutlarını çağırabilir. Örnek olarak, önceki ekran görüntüsünde cihaz ve **komut** görünümlerini **Yönet** ' i içerir.

Cihazları toplu olarak yönetmek için bir operatör işleri oluşturabilir ve zamanlayabilir. İşler, özellikleri güncelleştirebilir ve komutları birden çok cihazda çalıştırabilir. Daha fazla bilgi edinmek için bkz. [Azure IoT Central uygulamanızda Iş oluşturma ve çalıştırma](howto-run-a-job.md).

## <a name="troubleshoot-and-remediate-issues"></a>Sorunları giderin ve düzeltin

Operatör, uygulamanın ve cihazların sistem durumunun sorumluluğundadır. [Sorun giderme kılavuzu](troubleshoot-connection.md) , operatörlerin yaygın sorunları tanılamasına ve düzeltmesine yardımcı olur. Bir operatör, sorun çözülene kadar arızalı olarak görünen cihazları engellemek için **cihazlar** sayfasını kullanabilir.

## <a name="add-and-remove-devices"></a>Cihaz ekleme ve kaldırma

İşleci, IoT Central uygulamanıza tek tek veya toplu olarak cihaz ekleyebilir ve kaldırabilir. Daha fazla bilgi edinmek için bkz. [Azure IoT Central uygulamanızda cihazları yönetme](howto-manage-devices.md).

## <a name="personalize"></a>Kişiselleştirme

İşleçler, en sık kullandıkları kaynakların bağlantılarını içeren IoT Central bir uygulamada kişiselleştirilmiş panolar oluşturabilir. Daha fazla bilgi için bkz. [panoları yönetme](howto-create-personal-dashboards.md#manage-dashboards).

## <a name="next-steps"></a>Sonraki adımlar

IoT Central kullanma hakkında daha fazla bilgi edinmek istiyorsanız, önerilen sonraki adımlar, [Azure IoT Central uygulaması oluşturma](./quick-deploy-iot-central.md)ile başlayarak hızlı başlangıç adımlarını denemenize olanak sağlar.
