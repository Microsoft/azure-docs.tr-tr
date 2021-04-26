---
title: include dosyası
description: include dosyası
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 77fdaf297fff0e145b1dd53908887bc14f9d3f14
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491131"
---
<!-- All needs updating -->
Azure IoT Central uygulamanızda bir operatör olarak şunları yapabilirsiniz:

* **Genel bakış** sayfasında iki termostat bileşeni tarafından gönderilen Telemetriyi görüntüleyin:

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Cihaz telemetrisini görüntüleme":::

* **Hakkında** sayfasında cihaz özelliklerini görüntüleyin. Bu sayfada cihaz bilgileri bileşeni ve iki termostat bileşeni özellikleri gösterilir:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Cihaz özelliklerini görüntüle":::

## <a name="customize-the-device-template"></a>Cihaz şablonunu özelleştirme

Bir çözüm geliştiricisi olarak, sıcaklık denetleyicisi cihazı bağlandığında IoT Central otomatik olarak oluşturulan cihaz şablonunu özelleştirebilirsiniz.

Cihazla ilişkili müşteri adını depolamak üzere bir bulut özelliği eklemek için:

1. IoT Central uygulamanızda, **cihaz şablonları** sayfasında **sıcaklık denetleyicisi** cihaz şablonuna gidin.

1. **Isı denetleyicisi** cihaz şablonunda, **bulut özellikleri**' ni seçin.

1. **Bulut özelliği Ekle**' yi seçin. **Görünen ad** olarak *müşteri adı* ' nı girin ve **şema** olarak **dize** ' yi seçin. Sonra **Kaydet**'i seçin.

**Get Max-Min rapor** komutlarının IoT Central uygulamanızda nasıl görüntüleneceğini özelleştirmek için:

1. Cihaz şablonunda **Özelleştir** ' i seçin.

1. **Getmaxminreport (thermostat1)** için *Get Max-Min raporunu değiştirin.* *Get thermostat1 durum raporuyla*.

1. **Getmaxminreport (thermostat2)** için *Get Max-Min raporunu değiştirin.* *Get thermostat2 durum raporuyla*.

1. **Kaydet**’i seçin.

**Hedef sıcaklık** yazılabilir özelliklerinin IoT Central uygulamanızda nasıl görüntüleneceğini özelleştirmek için:

1. Cihaz şablonunda **Özelleştir** ' i seçin.

1. **Targetsıcaklık (thermostat1)** için hedef sıcaklığını *hedef sıcaklık (1)* *ile değiştirin* .

1. **Targetsıcaklık (thermostat2)** için *hedef sıcaklığını* *hedef sıcaklık (2)* ile değiştirin.

1. **Kaydet**’i seçin.

**Sıcaklık denetleyicisi** modelindeki termostat bileşenleri, **hedef sıcaklık** yazılabilir özelliğini içerir, cihaz şablonu, **müşteri adı** bulutu özelliğini içerir. Bir işlecin bu özellikleri düzenlemek için kullanabileceği bir görünüm oluşturun:

1. **Görünümler** ' i seçin ve ardından **cihazı ve bulut verilerini Düzenle** kutucuğunu seçin.

1. _Özellikleri_ form adı olarak girin.

1. **Hedef sıcaklığın (1)**, **hedef sıcaklığın (2)** ve **müşteri adı** özelliklerinin seçin. Ardından **Bölüm Ekle**' yi seçin.

1. Yaptığınız değişiklikleri kaydedin.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Özellik değerlerini güncelleştirmek için görüntüleme":::

## <a name="publish-the-device-template"></a>Cihaz şablonunu yayımlama

Bir işlecin yaptığınız özelleştirmeleri görüp kullanabilmesi için önce cihaz şablonunu yayımlamanız gerekir.

**Termostat** cihaz şablonundan **Yayımla**' yı seçin. **Bu cihaz şablonunu uygulama panelinde Yayımla** ' da, **Yayımla**' yı seçin.

Bir operatör artık özellik değerlerini güncelleştirmek için **Özellikler** görünümünü kullanabilir ve cihaz komutları sayfasında **Get thermostat1 durum Report** ve **Get thermostat2 Status Report** adlı komutları çağırabilir:

* **Özellikler** sayfasında yazılabilir özellik değerlerini güncelleştir:

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Cihaz özelliklerini güncelleştirme":::

* **Komutlar sayfasından komutları** çağırın:

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Komutu çağırın":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Komut yanıtını görüntüleme":::
