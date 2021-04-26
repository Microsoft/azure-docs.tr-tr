---
title: Hızlı başlangıç-Azure IoT Central cihazlarınızı Izleyin
description: Hızlı başlangıç-bir operatör olarak, cihazlarınızı izlemek için Azure IoT Central uygulamanızı nasıl kullanacağınızı öğrenin.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4c63a9833e6b9a9b243d289d79428ddef1468253
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833890"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Hızlı başlangıç: cihazlarınızı izlemek için Azure IoT Central kullanma

*Bu makale, işleçler, oluşturucular ve yöneticiler için geçerlidir.*

Bu hızlı başlangıçta, cihazlarınızı izlemek ve ayarları değiştirmek için Microsoft Azure IoT Central uygulamanızı nasıl kullanacağınızı bir operatör olarak gösterilir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, önceki üç hızlı başlangıç [Oluştur Azure IoT Central uygulaması oluşturma](./quick-deploy-iot-central.md), [IoT Central uygulamanıza sanal bir cihaz ekleme](./quick-create-simulated-device.md) ve [cihazınız için kuralları ve eylemleri yapılandırma işlemleri](quick-configure-rules.md)gerçekleştirmeniz gerekir.

## <a name="receive-a-notification"></a>Bildirim alma

Azure IoT Central, e-posta iletileri halinde cihazlara ilişkin bildirimler gönderir. Bir Oluşturucu olarak, bağlı bir cihaz sensördeki nem bir eşiği aştığında bir işlece bildirim göndermek için bir kural eklediniz. Bir operatör olarak, e-postalarınızı bildirimler için kontrol edersiniz.

Cihaz hızlı başlangıç [için kuralları yapılandırma ve eylemlerin](quick-configure-rules.md) sonunda aldığınız e-posta iletisini açın. E-postada, cihazın bağlantısını seçin:

:::image type="content" source="media/quick-monitor-devices/email.png" alt-text="Bildirim e-postasını gösteren ekran görüntüsü":::

Önceki hızlı başlangıçlarda oluşturduğunuz sanal cihaz için **genel bakış** görünümü tarayıcınızda açılır:

:::image type="content" source="media/quick-monitor-devices/dashboard.png" alt-text="Bildirimi tetikleyen cihaza genel bakış gösteren ekran görüntüsü":::

## <a name="investigate-an-issue"></a>Sorun araştırma

Bir operatör olarak, **genel bakış**, **hakkında** ve **Komutlar** görünümlerinde cihaz hakkındaki bilgileri görüntüleyebilirsiniz. Oluşturucu cihaz bilgilerini düzenlemeniz ve cihaz özelliklerini ayarlamanız için bir **cihaz yönetme** görünümü oluşturdu.

Panodaki grafik, cihaz nem boyutunu gösterir. Cihaz nem oranı çok yüksek olduğuna karar verirsiniz.

## <a name="remediate-an-issue"></a>Sorunu düzeltme

Cihazda bir değişiklik yapmak için **Cihazı yönet** sayfasını kullanın.

Cihazın sıcak olması için **hedef sıcaklığını** 80 olarak değiştirin ve nem sayısını azaltın. Cihazı güncelleştirmek için **Kaydet** ' i seçin. Cihaz, ayarlar değişikliğini onayladığında, özelliğin durumu **eşitlenmiş** olarak değişir:

:::image type="content" source="media/quick-monitor-devices/change-settings.png" alt-text="Cihaz için güncelleştirilmiş hedef sıcaklık ayarını gösteren ekran görüntüsü":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrendiniz:

* Bildirim alma
* Sorun araştırma
* Sorunu düzeltme

Artık cihazınızı izlemeyi bildiğinize göre, önerilen sonraki adım şunları yapmanız gerekir:

> [!div class="nextstepaction"]
> [Bir cihaz şablonu oluşturun ve yönetin](howto-set-up-template.md).
