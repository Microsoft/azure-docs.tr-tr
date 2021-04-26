---
title: Azure Event Grid kavramlar
description: Azure Event Grid ve kavramlarını açıklar. Event Grid birçok anahtar bileşenini tanımlar.
ms.topic: conceptual
ms.date: 01/21/2021
ms.openlocfilehash: 6edc8a3980bfea15f28cfb7114bb9f8350a47a3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685712"
---
# <a name="concepts-in-azure-event-grid"></a>Azure Event Grid kavramlar

Bu makalede Azure Event Grid temel kavramları açıklanmaktadır.

## <a name="events"></a>Ekinlikler

Bir olay, sistemde gerçekleşen bir şeyi tam olarak açıklayan en düşük bilgi miktarıdır. Her olayda, olayın kaynağı, olayın gerçekleştiği süre ve benzersiz tanımlayıcı gibi yaygın bilgiler bulunur. Her olay, yalnızca belirli olay türüyle ilgili belirli bilgilere de sahiptir. Örneğin, Azure Depolama'da oluşturulan yeni dosya hakkındaki olayda dosyayla ilgili `lastTimeModified` değeri gibi ayrıntılar vardır. Öte yandan, bir Event Hubs olayında Capture dosyasının URL'si bulunur. 

Bir olay için izin verilen en büyük boyut 1 MB 'tır. 64 KB üzerindeki olaylar 64 KB 'lik artışlarla ücretlendirilir. Bir olayda gönderilen özellikler için bkz. [Azure Event Grid olay şeması](event-schema.md).

## <a name="publishers"></a>Yayımcılar

Yayımcı, Event Grid olayları gönderilmeye karar veren kullanıcı veya kuruluştur. Microsoft çeşitli Azure hizmetleri için olaylar yayımlar. Kendi uygulamanızdan olay yayımlayabilirsiniz. Hizmetleri Azure'ın dışında barındıran kuruluşlar Event Grid aracılığıyla olay yayımlayabilir.

## <a name="event-sources"></a>Olay kaynakları

Olay kaynağı, olayın gerçekleştiği yerdir. Her olay kaynağı bir veya birden çok olay türüyle ilişkilidir. Örneğin Azure Depolama, blob tarafından oluşturulan olayların olay kaynağıdır. IoT Hub, cihaz tarafından oluşturulan olayların olay kaynağıdır. Uygulamanız, sizin tanımladığınız özel olayların olay kaynağıdır. Event Grid'e olayları göndermek olay kaynaklarının sorumluluğundadır.

Desteklenen Event Grid kaynaklarından herhangi birini uygulama hakkında daha fazla bilgi için, bkz. [Azure Event Grid olay kaynakları](overview.md#event-sources).

## <a name="topics"></a>Konu başlıkları

Olay Kılavuzu konusu, kaynağın olayları gönderdiği bir uç nokta sağlar. Yayımcı, olay Kılavuzu konusunu oluşturur ve bir olay kaynağının bir konu veya birden fazla konu ihtiyacı olup olmadığına karar verir. İlgili olayların toplanması için bir konu kullanılır. Belirli olay türlerine yanıt vermek için, aboneler hangi konuların abone olacağına karar verir.

**Sistem konuları** Azure depolama, Azure Event Hubs ve Azure Service Bus gibi Azure hizmetleri tarafından sunulan yerleşik konulardır. Azure aboneliğinizde sistem konuları oluşturabilir ve bunlara abone olabilirsiniz. Daha fazla bilgi için bkz. [sistem konularına genel bakış](system-topics.md). 

**Özel konular** uygulama ve üçüncü taraf konulardır. Özel konu başlığı oluşturduğunuzda veya özel konu başlığına erişim için atandığınızda, aboneliğinizde özel konu başlığını görürsünüz. Daha fazla bilgi için bkz. [özel konular](custom-topics.md). Uygulamanızı tasarlarken, kaç tane konu oluşturacağına karar verirken esneklik elde edersiniz. Büyük çözümler için ilgili olayların her kategorisi için özel bir konu oluşturun. Örneğin, kullanıcı hesaplarını değiştirme ve siparişleri işleme ile ilgili olaylar gönderen bir uygulamayı ele alalım. Herhangi bir olay işleyicisinin her iki olay kategorisini de istemesi pek olası değildir. İki özel konu başlığı oluşturun ve olay işleyicilerinin ilgilendikleri konu başlığına abone olmalarına izin verin. Küçük çözümler için tüm olayları tek bir konuya göndermenizi tercih edebilirsiniz. Olay aboneleri istedikleri olay türlerini filtreleyebilir.

Başka türde bir konu vardır: **iş ortağı konusu**. [Iş ortağı olayları](partner-events-overview.md) özelliği, üçüncü taraf SaaS sağlayıcısı 'nın, bu olaylara abone olan tüketiciler tarafından kullanılabilmesini sağlamak için hizmetlerinden olayları yayımlamasına olanak sağlar. SaaS sağlayıcısı, abonelerin olayları tüketmek için kullandığı bir **iş ortağı konusu** olan bir konu türü sunar. Ayrıca, olay yayımcıları ve aboneler tarafından kullanılan kaynakların kaygılarını ve sahipliğini ayırarak temiz bir yayın-alt modeli sunar.

## <a name="event-subscriptions"></a>Olay abonelikleri

Abonelik, almak istediğiniz bir konu üzerinde hangi olayların Event Grid söyler. Aboneliği oluştururken, olayı işlemek için bir uç nokta sağlarsınız. Uç noktaya gönderilen olaylara filtre uygulayabilirsiniz. Olay türüne veya konu düzenine göre filtreleyebilirsiniz. Daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).

Abonelik oluşturma örnekleri için bkz.:

* [Event Grid için Azure CLI örnekleri](cli-samples.md)
* [Event Grid için Azure PowerShell örnekleri](powershell-samples.md)
* [Event Grid için Azure Resource Manager şablonları](template-samples.md)

Geçerli olay Kılavuzu aboneliklerinizi alma hakkında daha fazla bilgi için bkz. [sorgu Event Grid abonelikleri](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Olay aboneliği süre sonu
Olay aboneliğinin süresi bu tarihte otomatik olarak sona erer. Yalnızca sınırlı bir süre için gerekli olan olay abonelikleri için bir süre sonu ayarlayın ve bu abonelikleri temizleme hakkında endişelenmeniz gerekmez. Örneğin, bir senaryoyu test etmek üzere bir olay aboneliği oluştururken bir süre sonu ayarlamak isteyebilirsiniz. 

Süre sonu ayarlamanın bir örneği için bkz. [Gelişmiş filtrelerle abone olma](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Olay işleyicileri

Bir Event Grid perspektifinden olay işleyicisi, olayın gönderildiği yerdir. İşleyici, olayı işlemek için başka bir eylem gerçekleştirir. Event Grid çeşitli işleyici türlerini destekler. Desteklenen bir Azure hizmetini veya kendi web kancasını işleyici olarak kullanabilirsiniz. İşleyicinin türüne bağlı olarak, Event Grid olayın teslimini güvence altına almak için farklı mekanizmalardan oluşur. HTTP Web kancası olay işleyicileri için, işleyici bir durum kodu döndürene kadar olay yeniden denenir `200 – OK` . Azure depolama kuyruğu için, Kuyruk hizmeti ileti gönderimi sıraya başarıyla işleyerek olaylar yeniden denenir.

Desteklenen Event Grid işleyicilerinden herhangi birini uygulama hakkında daha fazla bilgi için, bkz. [Azure Event Grid olay işleyiciler](event-handlers.md).

## <a name="security"></a>Güvenlik

Event Grid konularda abone olma ve konuları yayımlama konusunda güvenlik sağlar. Abone olurken, kaynak veya olay Kılavuzu konusunda yeterli izinlere sahip olmanız gerekir. Yayımlarken, konusu için bir SAS belirtecine veya anahtar kimlik doğrulamasına sahip olmanız gerekir. Daha fazla bilgi için bkz. [güvenlik ve kimlik doğrulaması Event Grid](security-authentication.md).

## <a name="event-delivery"></a>Olay teslimi

Event Grid, abonenin uç noktası tarafından bir olayın alındığını doğrulayamıyorsa olayı yeniden dağıtır. Daha fazla bilgi için bkz. [ileti teslimi Event Grid ve yeniden deneme](delivery-and-retry.md).

## <a name="batching"></a>Toplu İşleme

Özel bir konu kullanıldığında, olaylar her zaman bir dizide yayımlanmalıdır. Bu, düşük performanslı senaryolar için bir toplu iş olabilir, ancak yüksek hacimli kullanım durumları için, daha yüksek bir verimlilik elde etmek üzere yayımlama başına birkaç olayı toplu olarak toplu yapmanız önerilir. Toplu işlemler 1 MB 'a kadar, bir etkinliğin en büyük boyutu 1 MB 'tır. 

## <a name="next-steps"></a>Sonraki adımlar

* Event Grid’e giriş için bkz. [Event Grid hakkında](overview.md).
* Event Grid kullanmaya hızlıca başlamak için bkz. [özel olayları oluşturma ve Azure Event Grid ile yönlendirme](custom-event-quickstart.md).
