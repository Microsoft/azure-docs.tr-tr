---
title: Azure IoT Central Azure Sphere bir cihaz bağlama | Microsoft Docs
description: Bir Azure Sphere (DevKit) cihazını Azure IoT Central uygulamasına bağlamayı öğrenin.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.custom: device-developer
ms.openlocfilehash: 770f6e56a669ab2d9b425a7a2879eeef5d37377b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92123432"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>Azure Sphere cihazını Azure IoT Central uygulamanıza bağlama

*Bu makale, cihaz geliştiricileri için geçerlidir.*

Bu makalede bir Azure Sphere (DevKit) cihazının bir Azure IoT Central uygulamasına nasıl bağlanacağı gösterilmektedir.

Azure Sphere güvenli, internete bağlı cihazlara yönelik yerleşik bağlantı ve güvenlik özellikler içeren üst düzey bir uygulama platformudur. Güvenli, bağlı, çapraz mikro denetleyici birimi (MCU), özel bir üst düzey Linux tabanlı işletim sistemi (OS) ve sürekli ve yenilenebilir güvenlik sağlayan bulut tabanlı bir güvenlik hizmeti içerir. Daha fazla bilgi için bkz. [Azure Sphere nedir?](/azure-sphere/product-overview/what-is-azure-sphere).

[Azure Sphere geliştirme setleri](https://azure.microsoft.com/services/azure-sphere/get-started/) , prototip oluşturma ve Azure Sphere uygulamalar geliştirme için ihtiyacınız olan her şeyi sağlar. Azure Sphere ile Azure IoT Central, IoT çözümüne yönelik uçtan uca bir yığın sağlar. Azure Sphere, cihaz desteği sağlar ve sıfır kodlu bir yönetilen IoT uygulama platformu olarak IoT Central.

Bu nasıl yapılır makalesinde şunları yapabilirsiniz:

- Kitaplıktan Azure Sphere DevKit cihaz şablonunu kullanarak IoT Central Azure Sphere bir cihaz oluşturun.
- Azure IoT için Azure Sphere DevKit cihazını hazırlayın.
- Azure Sphere DevKit 'i Azure IoT Central 'ye bağlayın.
- IoT Central cihazda Telemetriyi görüntüleyin.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlamak için aşağıdaki kaynaklar gereklidir:

- Azure IoT Central uygulaması.
- Visual Studio 2019, sürüm 16,4 veya üzeri.
- [Seeed Studios 'den bir Azure Sphere MT3620 geliştirme seti](/azure-sphere/hardware/mt3620-reference-board-design).

> [!NOTE]
> Fiziksel bir cihazınız yoksa, sanal bir cihazı denemek için ilk adım adım son bölüme atlayın.

## <a name="create-the-device-in-iot-central"></a>Cihazı IoT Central oluşturma

IoT Central Azure Sphere bir cihaz oluşturmak için:

1. Azure IoT Central uygulamanızda **cihaz şablonları** sekmesini seçin ve **+ Yeni**' yi seçin. **Öne çıkan bir cihaz şablonu kullan** bölümünde **Azure Sphere örnek cihaz**' ı seçin.

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Azure Sphere DevKit için cihaz şablonu":::

1. Cihaz şablonunda, **sıcaklık** ve **düğme basma**' ı göstermek için **genel bakış** adlı görünümü düzenleyin.

1. Okuma/yazma özelliği **durumunu** gösteren başka bir görünüm eklemek için **cihazı ve bulut verilerini** görüntüleme türünü seçin. **Durum LED** özelliğini formun sağ tarafındaki boş, noktalı dikdörtgene sürükleyin. **Kaydet**’i seçin.

## <a name="prepare-the-device"></a>Cihazı hazırlama

Azure Sphere DevKit cihazını IoT Central 'e bağlayabilmeniz için önce [Cihaz ve geliştirme ortamını](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT)oluşturmanız gerekir.

## <a name="connect-the-device"></a>Cihazı bağlama

Örneğin IoT Central bağlanmasına izin vermek için [bir Azure IoT Central uygulaması yapılandırmanız ve ardından Örneğin uygulama bildirimini değiştirmeniz](https://aka.ms/iotcentral-sphere-git-readme)gerekir.

## <a name="view-the-telemetry-from-the-device"></a>Cihazdan Telemetriyi görüntüleme

Cihaz IoT Central bağlandığında, panoda telemetri görebilirsiniz.

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Azure Sphere DevKit panosu":::

## <a name="create-a-simulated-device"></a>Sanal cihaz oluşturma

Bir fiziksel Azure Sphere DevKit cihazınız yoksa, Azure IoT Central uygulamasını denemek için sanal bir cihaz oluşturabilirsiniz.

Sanal cihaz oluşturmak için:

- **Azure IoT Sphere > cihazları** seçme
- **+ Yeni** seçeneğini belirleyin.
- Benzersiz bir **CIHAZ kimliği** ve kolay bir **Cihaz adı** girin.
- **Benzetimli** ayarı etkinleştirin.
- **Oluştur**’u seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bir cihaz geliştiricisiyseniz, önerilen bazı sonraki adımlar şunlardır:

- [Azure IoT Central cihaz bağlantısı](./concepts-get-connected.md) hakkında bilgi edinin
- [Azure CLI kullanarak cihaz bağlantısını izlemeyi](./howto-monitor-devices-azure-cli.md) öğrenin