---
title: Azure IoT Central mimari kavramları | Microsoft Docs
description: Bu makalede, Azure IoT Central mimarisiyle ilgili temel kavramlar tanıtılmaktadır
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 25b0ec1b86a59b944cdb895bd536da32a1f8595b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884501"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central mimarisi

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu makalede Microsoft Azure IoT Central mimarisine genel bakış sunulmaktadır.

![Üst düzey mimari](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Cihazlar

Cihazlar, Azure IoT Central uygulamanızla verileri değiş tokuş. Bir cihaz şunları yapabilir:

- Telemetri gibi ölçümleri gönderin.
- Ayarları uygulamanızla eşitler.

Azure IoT Central, bir cihazın uygulamanızla değiş tokuş edebilir bir cihaz şablonunda belirtilir. Cihaz şablonları hakkında daha fazla bilgi için bkz. [metadata Management](#metadata-management).

Cihazların Azure IoT Central uygulamanıza nasıl bağlanabileceği hakkında daha fazla bilgi edinmek için bkz. [cihaz bağlantısı](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Bulut ağ geçidi

Azure IoT Central, Azure IoT Hub cihaz bağlantısı sağlayan bir bulut ağ geçidi olarak kullanır. IoT Hub şunları sunar:

- Bulutta ölçekte veri alımı.
- Cihaz yönetimi.
- Güvenli cihaz bağlantısı.

IoT Hub hakkında daha fazla bilgi edinmek için bkz. [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Azure IoT Central cihaz bağlantısı hakkında daha fazla bilgi için bkz. [cihaz bağlantısı](concepts-connectivity.md).

## <a name="data-stores"></a>Veri depoları

Azure IoT Central, uygulama verilerini bulutta depolar. Depolanan uygulama verileri şunları içerir:

- Cihaz şablonları.
- Cihaz kimlikleri.
- Cihaz meta verileri.
- Kullanıcı ve rol verileri.

Azure IoT Central, cihazlarınızdan gönderilen ölçüm verileri için bir zaman serisi deposu kullanır. Analiz hizmeti tarafından kullanılan cihazlardan gelen zaman serisi verileri.

## <a name="analytics"></a>Analiz

Analiz hizmeti, uygulamanın görüntülediği özel raporlama verilerini oluşturmaktan sorumludur. Bir işleç, uygulamada görünen [Analizi özelleştirebilir](howto-create-analytics.md) . Analytics hizmeti [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) üzerine kurulmuştur ve cihazlarınızdan gönderilen ölçüm verilerini işler.

## <a name="rules-and-actions"></a>Kurallar ve eylemler

[Kurallar ve eylemler](howto-create-telemetry-rules.md) , uygulama içindeki görevleri otomatikleştirmek için birlikte çalışır. Bir Oluşturucu, tanımlı bir eşiği aşan sıcaklık gibi cihaz telemetrisine dayalı kurallar tanımlayabilir. Azure IoT Central, kural koşullarının ne zaman karşılandığını anlamak için bir akış işlemcisi kullanır. Bir kural koşulu karşılandığında, Oluşturucu tarafından tanımlanan bir eylemi tetikler. Örneğin, bir eylem bir mühendisin bir cihazdaki sıcaklığın çok yüksek olduğunu bildiren bir e-posta gönderebilir.

## <a name="metadata-management"></a>Meta veri yönetimi

Azure IoT Central uygulamasında cihaz şablonları cihaz türlerinin davranışını ve yeteneklerini tanımlar. Örneğin, bir soğutma cihaz şablonu, bir soğutma 'nin uygulamanıza gönderdiği Telemetriyi belirtir.

![Şablon mimarisi](media/concepts-architecture/template_architecture.png)

Bir cihaz şablonunda:

- **Ölçümler** , cihazın uygulamaya gönderdiği Telemetriyi belirler.
- **Ayarlar** bir işlecin ayarlayamalarındaki konfigürasyonları belirler.
- **Özellikler** , bir işlecin ayarlayabildirdiği meta verileri belirtir.
- **Kurallar** , bir cihazdan gönderilen verilere göre uygulamadaki davranışı otomatik hale getirir.
- **Panolar** , uygulamadaki bir cihazın özelleştirilebilir görünümleridir.

Bir uygulama, her bir cihaz şablonuna dayalı bir veya daha fazla sanal ve gerçek cihaza sahip olabilir.

## <a name="data-export"></a>Veri dışarı aktarma

Azure IoT Central uygulamasında verilerinizi kendi Azure Event Hubs, Azure Service Bus ve Azure Blob depolama örneklerine [sürekli olarak dışarı aktarabilirsiniz](howto-export-data-event-hubs-service-bus.md) . IoT Central ölçümleri, cihazları ve cihaz şablonlarını dışarı aktarabilir.

## <a name="batch-device-updates"></a>Batch cihaz güncelleştirmeleri

Azure IoT Central uygulamasında, bağlı cihazları yönetmek için [işler oluşturabilir ve çalıştırabilirsiniz](howto-run-a-job.md) . Bu işler, cihaz özellikleri veya ayarları için toplu güncelleştirmeler yapmanızı veya komutları çalıştırmanızı sağlar. Örneğin, birden çok soğutma Başlatan makinenin fan hızını artırmak için bir iş oluşturabilirsiniz.

## <a name="role-based-access-control-rbac"></a>Rol tabanlı erişim denetimi (RBAC)

Yönetici, önceden tanımlanmış rolleri kullanarak bir Azure IoT Central uygulaması için [erişim kuralları tanımlayabilir](howto-administer.md) . Bir yönetici, kullanıcının uygulamaya erişimi olan hangi bölgelere sahip olduğunu belirlemek için rollere Kullanıcı atayabilir.

## <a name="security"></a>Güvenlik

Azure IoT Central içindeki güvenlik özellikleri şunlardır:

- Veriler aktarım sırasında ve bekleyen sırada şifrelenir.
- Kimlik doğrulaması Azure Active Directory veya Microsoft hesabı tarafından sağlanır. İki öğeli kimlik doğrulama desteklenir.
- Tam kiracı yalıtımı.
- Cihaz düzeyi güvenliği.

## <a name="ui-shell"></a>UI kabuğu

UI kabuğu modern, yanıt veren ve HTML5 tarayıcı tabanlı bir uygulamadır.
Yönetici, özel temalar uygulayarak ve kendi özel yardım kaynaklarınızı işaret etmek üzere yardım bağlantılarını değiştirerek uygulamanın kullanıcı arabirimini özelleştirebilir. UI özelleştirmesi hakkında daha fazla bilgi için bkz. [Azure IoT Central kullanıcı arabirimini özelleştirme](howto-customize-ui.md) makalesi.

Bir işleç, kişiselleştirilmiş uygulama panoları oluşturabilir. Farklı verileri görüntüleyen ve aralarında geçiş yapan çeşitli panolarınız olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central mimarisini öğrendiğinize göre, önerilen sonraki adım Azure IoT Central [cihaz bağlantısı](concepts-connectivity.md) hakkında bilgi edineceksiniz.