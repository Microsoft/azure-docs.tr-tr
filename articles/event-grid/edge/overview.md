---
title: Kenarda olay odaklı mimariler — IoT Edge Azure Event Grid
description: Modüller, uç cihazlar ve bulut arasındaki iletme olayları için IoT Edge bir modül olarak Azure Event Grid kullanın.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: e03429ed3df5bd3518d5e5194bd842b9a4f290ba
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991949"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>Azure IoT Edge Azure Event Grid nedir?
IoT Edge Event Grid, tüm yayın/alt ve olay odaklı senaryolar için Azure Event Grid güç ve esneklik sunar. Aynı cihazda, diğer uç cihazlarda veya buluttaki hizmetlerde modüller olup olmadığı konusunda konular oluşturun, olayları yayımlayın ve birden çok hedefe abone olun.

Bulutta olduğu gibi IoT Edge modülündeki Event Grid, olayların bir şekilde yönlendirilmesini, filtrelenmesini ve güvenilir teslimini işler. Yalnızca ilgili olayların gelişmiş dize, sayısal ve Boole filtreleri kullanılarak farklı olay işleyicilerine gönderilmesini sağlamak için olayları filtreleyin. Yeniden deneme mantığı, etkinliğin yayımlama sırasında mevcut olmasa bile hedef modüle, uç cihaza veya bulut hizmetine ulaşmasını sağlar. IoT Edge Event Grid güçlü bir depolama ve iletme mekanizması olarak kullanmanıza olanak tanır.

IoT Edge Event Grid hem CloudEvents v 1.0 hem de özel olay şemalarını destekler. Ayrıca, Azure ve üçüncü taraflarla kolayca birlikte çalışabilirlik için bulutta Event Grid aynı yayımlama ve abone olma semantiğini de destekler.

Bu makalede IoT Edge Azure Event Grid bir genel bakış sunulmaktadır. Bu modülü Edge üzerinde kullanmaya yönelik adım adım yönergeler için bkz. [Yayımlama, olaylara yerel olarak abone olma](pub-sub-events-webhook-local.md). 

![Kaynak ve işleyicilerin IoT Edge modeline Event Grid](../media/edge-overview/functional-model.png)

Bu görüntüde IoT Edge Event Grid kullanabileceğiniz bazı yollar gösterilmektedir ve desteklenen işlevlerin kapsamlı bir listesi değildir.

## <a name="when-to-use-event-grid-on-iot-edge"></a>IoT Edge Event Grid ne zaman kullanılır?

IoT Edge Event Grid, kenar ve bulut arasında olay odaklı mimariler oluşturmak için Tekdüzen, kullanımı kolay ve güvenilir bir olay modeli sağlamak üzere oluşturulmuştur.

IoT Edge Event Grid, Azure bulut hizmetinde bir simetrik çalışma zamanı yüzeyi alanı ile oluşturulmuştur, bu sayede ihtiyacınız olan her yerde aynı olayları ve API çağrılarını kullanabilirsiniz. Bulutta yayın/alt, uçta veya iki IoT Edge Event Grid arasında, artık tek bir go çözümü olabilir.

Modüller arasında basit iş akışlarını tetiklemek için IoT Edge Event Grid kullanın. Örneğin, bir konu oluşturun ve depolama modülünüzün "Depolama Blobu oluşturuldu" olaylarını konuya yayımlayın. Artık bir veya birkaç işlevi ya da özel modülü bu konulara abone olabilirsiniz.

Kenar cihazları arasında işlevselliği genişletin. Blob modülü olaylarını yayımlıyorsanız ve birden çok Edge cihazının hesaplama gücünü kullanmak istiyorsanız, cihazlar arası abonelikler oluşturun.

Son olarak, buluta bağlanın. Blob modül olaylarınızın düzenli aralıklarla bulutla eşitlenmesi gerekiyorsa, bulutta bulunan daha büyük bir işlem kullanın veya işlenen verileri gönderin, ek bulut hizmeti abonelikleri oluşturun.

IoT Edge Event Grid, esnek ve güvenilir bir ayrılmış olay mimarisi sağlar.

## <a name="event-sources"></a>Olay kaynakları

Bulutta çok benzer şekilde, IoT Edge Event Grid, olay odaklı mimariler oluşturmak için modüller arasında doğrudan tümleştirmeye izin verir. Şu anda, olaylar şu kaynaktan IoT Edge Event Grid gönderilebilir:

* IoT Edge üzerinde Azure Blob Depolama
* CloudEvents kaynakları
* HTTP POST aracılığıyla özel modüller & kapsayıcılar

## <a name="event-handlers"></a>Olay işleyicileri

IoT Edge Event Grid, olayları istediğiniz yere göndermek için oluşturulmuştur. Şu anda aşağıdaki hedefler desteklenir:

* IoT Hub, işlevler ve özel modüller dahil diğer modüller
* Diğer Edge cihazları
* Bulutta barındırılan ve Azure Event Grid ve Azure Işlevleri dahil hizmetler
* Web kancaları

## <a name="supported-environments"></a>Desteklenen ortamlar
Şu anda, Windows 64-bit, Linux 64-bit ve ARM 32 bit ortamları desteklenir.

## <a name="concepts"></a>Kavramlar

Azure Event Grid, başlamanıza izin veren beş kavram vardır:

* **Olaylar** — ne oldu?
* Olay **kaynakları** — olayın gerçekleştiği yer.
* **Konular** — yayımcıların olay gönderdikleri uç nokta.
* **Olay abonelikleri** — olayları yönlendirmek için uç nokta veya yerleşik mekanizma, bazen birden fazla işleyiciye. Abonelikler ayrıca işleyiciler tarafından gelen olayları akıllıca filtrelemek için de kullanılır.
* **Olay işleyicileri** — olaya tepki veren uygulama veya hizmettir.

## <a name="cost"></a>Maliyet

IoT Edge Event Grid genel önizleme sırasında ücretsizdir.

## <a name="issues"></a>Sorunlar
[https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)IoT Edge üzerinde Event Grid kullanma ile ilgili tüm sorunları bildirin.

## <a name="next-steps"></a>Sonraki adımlar

* [Olaylara yerel olarak yayımlama, abone olma](pub-sub-events-webhook-local.md)
* [Bulutta etkinliklere yayımlama, abone olma](pub-sub-events-webhook-cloud.md)
* [Olayları Event Grid buluta ilet](forward-events-event-grid-cloud.md)
* [Olayları ıothub 'e ilet](forward-events-iothub.md)
* [BLOB depolama olaylarına yerel olarak tepki verme](react-blob-storage-events-locally.md)