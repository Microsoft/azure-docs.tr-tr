---
title: Azure IoT Central mimari kavramları | Microsoft Docs
description: Bu makalede, Azure IoT Central mimarisiyle ilgili temel kavramlar tanıtılmaktadır
author: dominicbetts
ms.author: dobett
ms.date: 11/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 66792d9d0a8b1cd72ef8f22481016a35f37a1597
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013898"
---
# <a name="azure-iot-central-architecture-preview-features"></a>Azure IoT Central mimarisi (Önizleme özellikleri)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu makalede Microsoft Azure IoT Central mimarisine genel bakış sunulmaktadır.

![Üst düzey mimari](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Cihazlar

Cihazlar, Azure IoT Central uygulamanızla verileri değiş tokuş. Bir cihaz şunları yapabilir:

- Telemetri gibi ölçümleri gönderin.
- Ayarları uygulamanızla eşitler.

Azure IoT Central, bir cihazın uygulamanızla değiş tokuş edebilir bir cihaz şablonunda belirtilir. Cihaz şablonları hakkında daha fazla bilgi için bkz. [metadata Management](#metadata-management).

Cihazların Azure IoT Central uygulamanıza nasıl bağlanabileceği hakkında daha fazla bilgi edinmek için bkz. [cihaz bağlantısı](overview-iot-central-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Azure IOT Edge cihazları

Ayrıca, [Azure IoT SDK 'ları](https://github.com/Azure/azure-iot-sdks)kullanılarak oluşturulan cihazların yanı sıra, [Azure IoT Edge cihazlarını](../../iot-edge/about-iot-edge.md) bir IoT Central uygulamasına da bağlayabilirsiniz. IoT Edge, IoT Central tarafından yönetilen IoT cihazlarında doğrudan bulut zekasını ve özel mantık çalıştırmanızı sağlar. IoT Edge çalışma zamanı şunları yapmanızı sağlar:

- Cihaza iş yüklerini yükleyip güncelleştirin.
- Cihazda IoT Edge güvenlik standartlarının bakımını yapın.
- IoT Edge modüllerinin her zaman çalıştığından emin olun.
- Uzaktan izleme için modül durumunu buluta bildirin.
- Bir IoT Edge cihazdaki modüller arasında ve bir IoT Edge cihaz ile bulut arasında aşağı akış yaprak cihazları ve bir IoT Edge cihazı arasındaki iletişimi yönetin.

![Azure IoT Edge ile Azure IoT Central](./media/concepts-architecture/iotedge.png)

IoT Central, IoT Edge cihazlar için aşağıdaki özellikleri sağlar:

- Bir IoT Edge cihazının özelliklerini betimleyen cihaz şablonları:
  - Dağıtım bildirimi karşıya yükleme yeteneği, bu, bir veya bir cihaz için bir bildirimi yönetmenize yardımcı olur.
  - IoT Edge cihazında çalışan modüller.
  - Her modülün gönderdiği telemetri.
  - Her modülün Özellikler rapor.
  - Her modülün yanıt verdiği komutlar.
  - IoT Edge ağ geçidi cihazı yetenek modeli ve aşağı akış cihazı yetenek modeli arasındaki ilişkiler.
  - IoT Edge cihazında depolanmayan bulut özellikleri.
  - IoT Central uygulamanızın parçası olan özelleştirmeler, panolar ve formlar.

  Daha fazla bilgi için [IoT Edge cihaz şablonu oluşturma](./tutorial-define-edge-device-type.md) öğreticisine bakın.

- Azure IoT cihaz sağlama hizmeti 'ni kullanarak ölçekli IoT Edge cihazları sağlama özelliği
- Kurallar ve eylemler.
- Özel panolar ve çözümlemeler.
- IoT Edge cihazlarından Telemetriyi sürekli dışa aktarma.

### <a name="iot-edge-device-types"></a>IoT Edge cihaz türleri

IoT Central IoT Edge cihaz türlerini aşağıdaki şekilde sınıflandırır:

- Yaprak cihazlar. Bir IoT Edge cihazın aşağı akış yaprak cihazları olabilir, ancak bu cihazlar IoT Central sağlanmadı.
- Aşağı akış cihazları olan ağ geçidi cihazları. Ağ geçidi cihazı ve aşağı akış Cihazları IoT Central

![IoT Edge genel bakış ile IoT Central](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge desenleri

IoT Central aşağıdaki IoT Edge cihaz düzenlerini destekler:

#### <a name="iot-edge-as-leaf-device"></a>Yaprak cihaz olarak IoT Edge

![Yaprak cihaz olarak IoT Edge](./media/concepts-architecture/edgeasleafdevice.png)

IoT Edge cihaz IoT Central sağlanır ve tüm aşağı akış cihazları ve telemetri IoT Edge cihazdan geldiği şekilde gösterilir. IoT Edge cihazına bağlı aşağı akış Cihazları IoT Central sağlanmadı.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>Kimliği olan aşağı akış cihazlarına bağlı IoT Edge ağ geçidi cihazı

![Aşağı akış cihaz kimliği ile IoT Edge](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

IoT Edge cihaz, IoT Edge cihaza bağlı olan aşağı akış cihazları ile birlikte IoT Central sağlanır. Ağ Geçidi üzerinden aşağı akış cihazları sağlamaya yönelik çalışma zamanı desteği şu anda desteklenmiyor.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge Ağ Geçidi tarafından belirtilen kimliğe sahip aşağı akış cihazlarına bağlı IoT Edge ağ geçidi cihazı

![Kimliği olmayan aşağı akış cihazından IoT Edge](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

IoT Edge cihaz, IoT Edge cihaza bağlı olan aşağı akış cihazları ile birlikte IoT Central sağlanır. Aşağı akış cihazlarına kimlik sağlayan ve aşağı akış cihazlarının sağlanması için ağ geçidinin çalışma zamanı desteği şu anda desteklenmemektedir. Kendi kimlik çeviri modülünüzü getiriniz IoT Central, bu düzene destek verebilir.

## <a name="cloud-gateway"></a>Bulut ağ geçidi

Azure IoT Central, Azure IoT Hub cihaz bağlantısı sağlayan bir bulut ağ geçidi olarak kullanır. IoT Hub şunları sunar:

- Bulutta ölçekte veri alımı.
- Cihaz yönetimi.
- Güvenli cihaz bağlantısı.

IoT Hub hakkında daha fazla bilgi edinmek için bkz. [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Azure IoT Central cihaz bağlantısı hakkında daha fazla bilgi için bkz. [cihaz bağlantısı](overview-iot-central-get-connected.md).

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

[Kurallar ve eylemler](tutorial-create-telemetry-rules.md) , uygulama içindeki görevleri otomatikleştirmek için birlikte çalışır. Bir Oluşturucu, tanımlı bir eşiği aşan sıcaklık gibi cihaz telemetrisine dayalı kurallar tanımlayabilir. Azure IoT Central, kural koşullarının ne zaman karşılandığını anlamak için bir akış işlemcisi kullanır. Bir kural koşulu karşılandığında, Oluşturucu tarafından tanımlanan bir eylemi tetikler. Örneğin, bir eylem bir mühendisin bir cihazdaki sıcaklığın çok yüksek olduğunu bildiren bir e-posta gönderebilir.

## <a name="metadata-management"></a>Meta veri yönetimi

Azure IoT Central uygulamasında cihaz şablonları cihaz türlerinin davranışını ve yeteneklerini tanımlar. Örneğin, bir soğutma cihaz şablonu, bir soğutma 'nin uygulamanıza gönderdiği Telemetriyi belirtir.

![Şablon mimarisi](media/concepts-architecture/template-architecture.png)

IoT Central önizleme uygulaması cihaz şablonunda:

- **Cihaz yetenek modelleri** , gönderdiği telemetri, cihaz durumunu tanımlayan Özellikler ve cihazın yanıt verdiği komutları gibi bir cihazın yeteneklerini belirtir. Cihaz özellikleri bir veya daha fazla arabirimde düzenlenir. Cihaz yetenek modelleri hakkında daha fazla bilgi için bkz. [ıot Tak ve kullan](../../iot-pnp/overview-iot-plug-and-play.md) belgeleri.
- **Bulut özellikleri** bir cihaz için IoT Central depolar özelliklerini belirtir. Bu özellikler yalnızca IoT Central depolanır ve hiçbir şekilde cihaza gönderilmez.
- **Görünümler** , bir işlecin cihazları izlemesine ve yönetmesine izin vermek için oluşturucunun oluşturduğu panoları ve formları belirler.
- **Özelleştirmeler** , oluşturucunun IoT Central uygulamayla daha uygun olması için cihaz yetenek modelindeki bazı tanımları geçersiz kılmasını sağlar.

Bir uygulama, her bir cihaz şablonuna dayalı bir veya daha fazla sanal ve gerçek cihaza sahip olabilir.

## <a name="data-export"></a>Veri dışarı aktarma

Azure IoT Central uygulamasında verilerinizi kendi Azure Event Hubs ve Azure Service Bus örneklerine [sürekli olarak dışarı aktarabilirsiniz](howto-export-data.md) . Ayrıca verilerinizi Azure Blob depolama hesabınıza düzenli olarak dışarı aktarabilirsiniz. IoT Central ölçümleri, cihazları ve cihaz şablonlarını dışarı aktarabilir.

## <a name="batch-device-updates"></a>Batch cihaz güncelleştirmeleri

Azure IoT Central uygulamasında, bağlı cihazları yönetmek için [işler oluşturabilir ve çalıştırabilirsiniz](../core/howto-run-a-job.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) . Bu işler, cihaz özellikleri veya ayarları için toplu güncelleştirmeler yapmanızı veya komutları çalıştırmanızı sağlar. Örneğin, birden çok soğutma Başlatan makinenin fan hızını artırmak için bir iş oluşturabilirsiniz.

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

Azure IoT Central mimarisini öğrendiğinize göre, önerilen sonraki adım Azure IoT Central [cihaz bağlantısı](overview-iot-central-get-connected.md) hakkında bilgi edineceksiniz.