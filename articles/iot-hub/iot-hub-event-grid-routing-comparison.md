---
title: IoT Hub için Event Grid karşılaştırın, yönlendirme | Microsoft Docs
description: IoT Hub kendi ileti yönlendirme hizmetini sunar, ancak olay yayımlama için Event Grid ile tümleşir. İki özelliği karşılaştırın.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 877467b65d346c871dd93f4b3f96b2c1664fa4b9
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906797"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>IoT Hub için ileti yönlendirmeyi ve Event Grid karşılaştırın

Azure IoT Hub, bağlı cihazlarınızdan verileri akışa almak ve bu verileri iş uygulamalarınızla bütünleştirmek için yetenek sağlar. IoT Hub, IoT olaylarını diğer Azure hizmetleriyle veya iş uygulamalarıyla tümleştirmek için iki yöntem sunar. Bu makalede, senaryonuza en uygun seçeneği belirleyebilmeniz için bu özelliği sağlayan iki özellik ele alınmaktadır.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT Hub ileti yönlendirme](iot-hub-devguide-messages-d2c.md)** : Bu IoT Hub özelliği, kullanıcıların cihazdan buluta Iletileri Azure depolama kapsayıcıları, Event Hubs, Service Bus kuyrukları ve Service Bus konuları gibi hizmet uç noktalarına yönlendirmesine olanak sağlar. Yönlendirme Ayrıca, verileri uç noktalara yönlendirmeden önce verilerin filtreleneceği bir sorgulama özelliği de sağlar. Cihaz telemetri verilerine ek olarak, eylemleri tetiklemek için kullanılabilecek [telemetri dışı olaylar](iot-hub-devguide-messages-d2c.md#non-telemetry-events) da gönderebilirsiniz. 

**Event Grid ile tümleştirme IoT Hub**: Azure Event Grid, yayımlama-abonelik modeli kullanan tam olarak yönetilen bir olay yönlendirme hizmetidir. IoT Hub ve Event Grid, IoT Hub olaylarını neredeyse gerçek zamanlı olarak [Azure ve Azure dışı hizmetlere bütünleştirmek](iot-hub-event-grid.md)için birlikte çalışır. IoT Hub hem [cihaz olaylarını](iot-hub-event-grid.md#event-types) hem de telemetri olaylarını yayımlar.

## <a name="differences"></a>Fark

İleti yönlendirme ve Event Grid uyarı yapılandırmasını etkinleşirken, ikisi arasında bazı önemli farklılıklar vardır. Ayrıntılar için aşağıdaki tabloya bakın:

| Özellik | IoT Hub ileti yönlendirme | Event Grid ile tümleştirme IoT Hub |
| ------- | --------------- | ---------- |
| **Cihaz iletileri ve olayları** | Evet, ileti yönlendirme telemetri verileri, rapor cihazı ikizi değişiklikleri, cihaz yaşam döngüsü olayları ve dijital ikizi değişiklik olayları ( [ıot Tak ve Kullan genel önizlemenin](../iot-pnp/overview-iot-plug-and-play.md)bir parçası) için kullanılabilir. | Evet, Event Grid telemetri verileri için kullanılabilir, ancak cihazların oluşturulma, silinme, bağlanma ve bağlantısı kesildiğinde de rapor verebilir IoT Hub |
| **Sıralama** | Evet, olayların sıralaması korunur.  | Hayır, olay sırası garanti edilmez. | 
| **Menin** | İleti uygulama özellikleri, ileti sistemi özellikleri, ileti gövdesi, cihaz ikizi etiketleri ve cihaz ikizi özellikleri üzerinde zengin filtreleme. Filtreleme, dijital ikizi değişiklik olaylarına uygulanmaz. Örnekler için bkz. [Ileti yönlendirme sorgusu sözdizimi](iot-hub-devguide-routing-query-syntax.md). | Her olaydaki olay türüne, konu türüne ve özniteliklere göre filtreleme. Örnekler için bkz. [Event Grid aboneliklerinde filtreleme olaylarını anlama](../event-grid/event-filtering.md). Telemetri olaylarına abone olurken, Event Grid yayımlamadan önce IoT Hub ileti özelliklerini, ileti gövdesini ve cihaz ikizi filtrelemek için verilere ek filtreler uygulayabilirsiniz. [Olayları filtreleme](../iot-hub/iot-hub-event-grid.md#filter-events)bölümüne bakın. |
| **Noktalarının** | <ul><li>Event Hubs</li> <li>Azure Blob Depolama</li> <li>Service Bus kuyruğu</li> <li>Hizmet Veri Yolu konuları</li></ul><br>Ücretli IoT Hub SKU 'Lar (S1, S2 ve S3), 10 özel uç nokta ile sınırlıdır. Her IoT Hub için 100 yol oluşturulabilir. | <ul><li>Azure İşlevleri</li> <li>Azure Otomasyonu</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Depolama Blobu</li> <li>Özel Konu Başlıkları</li> <li>Kuyruk Depolama</li> <li>Microsoft Flow</li> <li>Web kancaları aracılığıyla üçüncü taraf hizmetleri</li></ul><br>IoT Hub başına 500 uç noktası desteklenir. Uç noktaların en güncel listesi için bkz. [Event Grid olay işleyicileri](../event-grid/overview.md#event-handlers). |
| **Maliyet** | İleti yönlendirme için ayrı ücret alınmaz. Yalnızca IoT Hub telemetri girişi ücretlendirilir. Örneğin, üç farklı uç noktaya yönlendirilmiş bir iletiniz varsa, yalnızca bir ileti için faturalandırılırsınız. | IoT Hub ücret alınmaz. Event Grid ayda ilk 100.000 işlemi ücretsiz olarak ve daha sonra yalnızca milyon işlem başına $0,60 ' i sunmaktadır. |

## <a name="similarities"></a>Benzerlikler

IoT Hub ileti yönlendirme ve Event Grid benzerlikler vardır ve bazıları aşağıdaki tabloda ayrıntılıdır:

| Özellik | IoT Hub ileti yönlendirme | Event Grid ile tümleştirme IoT Hub |
| ------- | --------------- | ---------- |
| **En büyük ileti boyutu** | 256 KB, cihazdan buluta | 256 KB, cihazdan buluta |
| **Güvenilirlik** | Yüksek: her bir rota için her iletiyi en az bir kez uç noktaya teslim eder. Bir saat içinde teslim edilmemiş tüm iletiler zaman aşımına uğrar. | Yüksek: her bir abonelik için her iletiyi en az bir kez Web kancasına gönderir. , 24 saat içinde teslim edilmemiş tüm olayları sona ermez. | 
| **Ölçeklenebilirlik** | Yüksek: milyarlarca ileti gönderen milyonlarca eşzamanlı bağlı cihazı desteklemek için Iyileştirildi. | Yüksek: bölge başına saniyede 10.000.000 olay yönlendirme özelliği vardır. |
| **Gecikme süresi** | Düşük: neredeyse gerçek zamanlı. | Düşük: neredeyse gerçek zamanlı. |
| **Birden çok uç noktaya gönder** | Evet, birden çok uç noktaya tek bir ileti gönderin. | Evet, birden çok uç noktaya tek bir ileti gönderin.  
| **Güvenlik** | IoT Hub 'ı cihaz başına kimliği ve iptal edilebilir erişim denetimi sağlar. Daha fazla bilgi için [IoT Hub Access Control](iot-hub-devguide-security.md)bölümüne bakın. | Event Grid, üç noktada doğrulama sağlar: olay abonelikleri, olay yayımlama ve Web kancası olay teslimi. Daha fazla bilgi için bkz. [güvenlik ve kimlik doğrulaması Event Grid](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Seçim yapma

IoT Hub ileti yönlendirme ve Event Grid ile IoT Hub tümleştirme, benzer sonuçlara ulaşmak için farklı eylemler gerçekleştirir. Bunlar her ikisi de IoT Hub çözümünüzde bilgi alır ve diğer hizmetlerin yanıt verebilmesi için bunu üzerine geçitirsiniz. Kaç tane kullanacağınıza karar verirsiniz? Kararınız kılavuzluk etmenize yardımcı olması için aşağıdaki soruları göz önünde bulundurun: 

* **Uç noktalara ne tür veriler gönderiyorsunuz?**

   Diğer hizmetlere telemetri verileri göndermeniz gerektiğinde IoT Hub ileti yönlendirmeyi kullanın. İleti yönlendirme Ayrıca ileti uygulama ve sistem özelliklerinin, ileti gövdesinin, Device ikizi etiketlerinin ve Device ikizi özelliklerinin sorgulanmasına da izin vermez.

   Event Grid ile tümleştirme IoT Hub IoT Hub hizmetinde oluşan olaylarla birlikte çalışmaktadır. Bu IoT Hub olaylar telemetri verilerini, oluşturulan, silinen, bağlanılan ve bağlantısı kesilen cihazları içerir. Telemetri olaylarına abone olurken, Event Grid yayımlamadan önce IoT Hub ileti özelliklerini, ileti gövdesini ve cihaz ikizi filtrelemek için verilere ek filtreler uygulayabilirsiniz. [Olayları filtreleme](../iot-hub/iot-hub-event-grid.md#filter-events)bölümüne bakın.

* **Bu bilgileri almak için hangi uç noktalar gerekir?**

   IoT Hub ileti yönlendirme sınırlı sayıda benzersiz uç noktayı ve uç nokta türlerini destekler, ancak verileri ve olayları ek uç noktalara yönlendirmek için bağlayıcılar oluşturabilirsiniz. Desteklenen uç noktaların tüm listesi için önceki bölümdeki tabloya bakın. 

   Event Grid ile IoT Hub tümleştirme, IoT Hub başına 500 uç noktasını ve çok çeşitli uç nokta türlerini destekler. Azure Işlevleri, Logic Apps, depolama ve Service Bus kuyruklarıyla yerel olarak tümleştirilir ve ayrıca Web kancalarıyla birlikte çalışarak Azure Service ekosisteminin dışına ve üçüncü taraf iş uygulamalarına veri gönderilmesini genişletebilirsiniz.

* **Verileriniz sırayla alınırsa ne olursa olsun?**

   IoT Hub ileti yönlendirme, iletilerin gönderilme sırasını korur, böylece aynı şekilde ulaşır.

   Event Grid, uç noktaların olayları gerçekleştikleri sırada alacağını garanti etmez. İletilerin mutlak sırasının önemli olduğu ve/veya bir tüketicinin iletiler için güvenilir benzersiz bir tanımlayıcıya ihtiyacı olan bu durumlar için ileti yönlendirmeyi kullanmanızı öneririz. 

## <a name="next-steps"></a>Sonraki adımlar

* [IoT Hub Ileti yönlendirme](iot-hub-devguide-messages-d2c.md) ve [IoT Hub uç noktaları](iot-hub-devguide-endpoints.md)hakkında daha fazla bilgi edinin.
* [Azure Event Grid](../event-grid/overview.md) hakkında daha fazla bilgi edinin.
* Ileti yolları oluşturmayı öğrenmek için, rotalar öğreticisini [kullanarak cihazdan buluta iletileri IoT Hub işleme](../iot-hub/tutorial-routing.md) bakın.
* [Logic Apps kullanarak Azure IoT Hub olaylarıyla ilgili e-posta bildirimleri göndererek](../event-grid/publish-iot-hub-events-to-logic-apps.md)Event Grid tümleştirmeyi deneyin.
