---
title: IoT Tak ve Kullan mimarisi | Microsoft Docs
description: Bir çözüm Oluşturucusu olarak IoT Tak ve Kullan 'ın temel mimari öğelerini anlayın.
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 2cccb1fdfe775250f80da6cc2ecdcc4ddaa3d88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95484092"
---
# <a name="iot-plug-and-play-architecture"></a>IoT Tak Çalıştır mimarisi

IoT Tak Çalıştır çözüm oluşturucularının el ile hiçbir yapılandırma gerçekleştirmeden akıllı cihazları kendi çözümlerine tümleştirmesine olanak tanır. IoT Tak ve Kullan 'ın temel tarafında, IoT Tak ve Kullan özellikli bir uygulama için bir cihazın yeteneklerini açıklayan bir cihaz _modelidir_ . Bu model, şunları tanımlayan bir arabirimler kümesi olarak yapılandırılmıştır:

- Bir cihazın veya diğer varlıkların salt okunurdur veya yazılabilir durumunu temsil eden _özelliklerdir_ . Örneğin, bir cihaz seri numarası salt okunurdur ve bir termostat üzerinde hedef sıcaklık yazılabilir bir özellik olabilir.
- Bir cihaz _tarafından yayılan,_ verilerin düzenli algılayıcı okumalar, zaman zaman hatası veya bilgi iletisi olup olmadığı.
- Bir cihazda yapılabilen bir işlevi veya işlemi tanımlayan _Komutlar_ . Örneğin, bir komut bir ağ geçidini yeniden başlatır veya uzak bir kamerayı kullanarak bir resim alabilir.

Her model ve arabirimin benzersiz bir KIMLIĞI vardır.

Aşağıdaki diyagramda IoT Tak ve Kullan çözümünün temel öğeleri gösterilmektedir:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT Tak Çalıştır mimarisi":::

## <a name="model-repository"></a>Model deposu

[Model deposu](./concepts-model-repository.md) model ve arabirim tanımları için bir depodur. Model ve arabirimleri [dijital TWINS tanım dili (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)kullanarak tanımlarsınız.

Web Kullanıcı arabirimi modelleri ve arabirimleri yönetmenizi sağlar.

Model deposunda, arabirim tanımlarına erişimi sınırlamanıza olanak sağlayan yerleşik rol tabanlı erişim denetimleri vardır.

## <a name="devices"></a>Cihazlar

Bir cihaz Oluşturucu, [Azure IoT cihaz SDK](./libraries-sdks.md)'larından birini kullanarak IoT akıllı cihazında çalıştırılacak kodu uygular. Cihaz SDK 'Ları cihaz oluşturucunun şunları uygulamasına yardımcı olur:

- IoT Hub 'ına güvenli bir şekilde bağlanın.
- Cihazı IoT Hub 'ınıza kaydedin ve cihazın uyguladığı DTDL arabirimlerinin koleksiyonunu tanımlayan model KIMLIĞINI duyurur.
- Cihaz ile IoT Hub 'ınız arasında DTDL arabirimlerinde tanımlanan özellikleri eşitler.
- DTDL arabirimlerinde tanımlanan komutlar için komut işleyicileri ekleyin.
- IoT Hub 'ına telemetri gönderin.

## <a name="iot-edge-gateway"></a>IoT Edge Ağ Geçidi

IoT Edge ağ geçidi, IoT Hub 'ına doğrudan bağlanamaan IoT Tak ve Kullan cihazlarını bağlamak için bir aracı görevi görür. Daha fazla bilgi için bkz. [bir IoT Edge cihazının ağ geçidi olarak nasıl kullanılabileceği](../iot-edge/iot-edge-as-gateway.md).

## <a name="iot-edge-modules"></a>IoT Edge modülleri

_IoT Edge modülü_ , iş mantığını kenarda dağıtmanıza ve yönetmenize olanak sağlar. Azure IoT Edge modüller, IoT Edge tarafından yönetilen en küçük hesaplama birimidir ve Azure Hizmetleri (Azure Stream Analytics gibi) veya kendi çözüme özgü kodunuzla bulunabilir.

_IoT Edge hub_ , Azure IoT Edge çalışma zamanını oluşturan modüllerden biridir. Aynı protokol uç noktalarını IoT Hub olarak kullanıma sunarak IoT Hub için yerel bir ara sunucu işlevi görür. Bu tutarlılık, istemcilerin (cihazların veya modüllerin) IoT Edge çalışma zamanına yalnızca IoT Hub gibi bağlanıp bağlanamadıkları anlamına gelir.

Cihaz SDK 'Ları bir modül oluşturucusunun şunları yapmanıza yardımcı olur:

- IoT Hub 'ınıza güvenli bir şekilde bağlanmak için IoT Edge hub 'ını kullanın.
- Modülü IoT Hub 'ınıza kaydedin ve cihazın uyguladığı DTDL arabirimlerinin koleksiyonunu tanımlayan model KIMLIĞINI duyurur.
- Cihaz ile IoT Hub 'ınız arasında DTDL arabirimlerinde tanımlanan özellikleri eşitler.
- DTDL arabirimlerinde tanımlanan komutlar için komut işleyicileri ekleyin.
- IoT Hub 'ına telemetri gönderin.

## <a name="iot-hub"></a>IoT Hub

[IoT Hub](../iot-hub/about-iot-hub.md) , IoT çözümünüz ile yönettiği cihazlar arasında çift yönlü iletişim için merkezi bir ileti hub 'ı görevi gören bulut tarafından barındırılan bir hizmettir.

IoT Hub 'ı:

- Bir cihaz tarafından uygulanan model KIMLIĞINI bir arka uç çözümü için kullanılabilir hale getirir.
- Hub 'a bağlı her Tak ve Kullan cihazla ilişkili dijital ikizi bakımını sağlar.
- Telemetri akışlarını işleme veya depolama için diğer hizmetlere iletir.
- Cihaz izlemeyi etkinleştirmek için dijital ikizi değişiklik olaylarını diğer hizmetlere yönlendirir.

## <a name="backend-solution"></a>Arka uç çözümü

Arka uç çözümü, IoT Hub 'ında dijital TWINS ile etkileşimde bulunarak bağlı cihazları izler ve denetler. Arka uç çözümünüzü uygulamak için hizmet SDK 'Lardan birini kullanın. Bağlı bir cihazın yeteneklerini anlamak için, çözüm arka ucu:

1. Cihazın IoT Hub 'ına kayıtlı model KIMLIĞINI alır.
1. Herhangi bir model deposundan arabirim tanımlarını almak için model KIMLIĞINI kullanır.
1. Arabirim tanımlarından bilgi ayıklamak için model ayrıştırıcılarını kullanır.

Arka uç çözümü, arabirim tanımlarındaki bilgileri kullanarak şunları yapabilir:

- Cihazların bildirdiği özellik değerlerini okuyun.
- Cihazdaki yazılabilir özellikleri güncelleştirin.
- Bir cihaz tarafından uygulanan çağrı komutları.
- Bir cihaz tarafından gönderilen telemetri biçimini anlayın.

## <a name="next-steps"></a>Sonraki adımlar

IoT Tak ve Kullan çözümünün mimarisine genel bir bakış edinolduğunuza göre, sonraki adımlar hakkında daha fazla bilgi edinebilirsiniz:

- [Model deposu](./concepts-model-repository.md)
- [Digital ikizi model tümleştirmesi](./concepts-model-discovery.md)
- [IoT Tak ve Kullan için geliştirme](./concepts-developer-guide-device.md)
