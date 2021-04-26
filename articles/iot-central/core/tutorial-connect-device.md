---
title: Öğretici-Azure 'a genel bir istemci uygulaması bağlama IoT Central | Microsoft Docs
description: Bu öğreticide, bir cihaz geliştiricisi olarak bir C, C#, Java, JavaScript veya Python istemci uygulaması çalıştıran bir cihaza Azure IoT Central uygulamanıza nasıl bağlanacağı gösterilmektedir. Bir işlecin bağlı bir cihazla etkileşime girmesine izin veren görünümler ekleyerek otomatik olarak oluşturulan cihaz şablonunu değiştirirsiniz.
author: dominicbetts
ms.author: dobett
ms.date: 11/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: bbf94b6e000d5c082debd6a0d41a8d62b8b3f26e
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491109"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Öğretici: Azure IoT Central uygulamanıza istemci uygulaması oluşturma ve bağlama

*Bu makale, çözüm oluşturucular ve cihaz geliştiricileri için geçerlidir.*

Bu öğreticide, bir cihaz geliştiricisi olarak, bir istemci uygulamasını Azure IoT Central uygulamanıza nasıl bağlayabilmeniz gösterilmektedir. Uygulama, bir sıcaklık denetleyicisi cihazının davranışını taklit eder. Uygulama IoT Central bağlandığı zaman, ısı denetleyicisi cihaz modelinin model KIMLIĞINI gönderir. IoT Central, cihaz modelini almak ve sizin için bir cihaz şablonu oluşturmak üzere model KIMLIĞINI kullanır. Bir işlecin bir cihazla etkileşime geçmesini sağlamak için cihaz şablonuna özelleştirmeler ve görünümler eklersiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Cihaz kodunu oluşturup çalıştırın ve IoT Central uygulamanıza bağlanın.
> * Cihazdan gönderilen sanal Telemetriyi görüntüleyin.
> * Özel görünümleri bir cihaz şablonuna ekleyin.
> * Cihaz şablonunu yayımlayın.
> * Cihaz özelliklerini yönetmek için bir görünüm kullanın.
> * Cihazı denetlemek için bir komut çağırın.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>Ham verileri görüntüleme

Bir cihaz geliştiricisi olarak, cihazınızın IoT Central gönderdiği ham verileri incelemek için **ham veri** görünümünü kullanabilirsiniz:

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="Ham veri görünümü":::

Bu görünümde, görüntülenecek sütunları seçebilir ve görüntülenecek zaman aralığını ayarlayabilirsiniz. **Modellenmemiş veriler** sütunu, cihaz şablonundaki herhangi bir özellik veya telemetri tanımından eşleşmeyen cihaz verilerini gösterir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

IoT Central öğreticiler kümesine devam etmeyi tercih ediyorsanız ve bir IoT Central çözümü oluşturma hakkında daha fazla bilgi edinmek istiyorsanız, bkz.:

> [!div class="nextstepaction"]
> [Ağ geçidi cihaz şablonu oluşturma](./tutorial-define-gateway-device-type.md)

Bir cihaz geliştiricisi olarak, Java kullanarak bir cihazın nasıl oluşturulacağı hakkında temel bilgileri öğrendiğinize göre, önerilen bazı sonraki adımlar şunlardır:

* Cihaz kodunuzu uygularken cihaz şablonlarının rolü hakkında daha fazla bilgi edinmek için cihaz [şablonu nedir?](./concepts-device-templates.md) makalesini okuyun.
* Cihazların IoT Central nasıl kaydedileceği ve cihaz bağlantılarının güvenliğini IoT Central sağlama hakkında daha fazla bilgi edinmek için [Azure IoT Central 'ye](./concepts-get-connected.md) bağlanın.
* IoT Central ile cihaz alışverişi yapılan veriler hakkında daha fazla bilgi edinmek için [telemetri, özellik ve komut yüklerini](concepts-telemetry-properties-commands.md) okuyun.
* [Iot Tak ve kullan cihaz geliştirici kılavuzunu](../../iot-pnp/concepts-developer-guide-device.md)okuyun.
