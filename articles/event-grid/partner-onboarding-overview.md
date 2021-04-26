---
title: İş ortağı eklemeye genel bakış (Azure Event Grid)
description: Event Grid iş ortağı olarak nasıl kullanabileceğiniz hakkında genel bakış sağlar.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 40d0afe0aaeb40412948eb304a36a3627566551b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869698"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>İş ortağı eklemeye genel bakış (Azure Event Grid)

Bu makalede, Azure Event Grid iş ortağı kaynaklarını özel olarak kullanma ve genel kullanıma açık iş ortağı konu türü olma konuları açıklanır.

Event Grid iş ortağı olarak yayımlama olayları ile ilişkili Event Grid kaynak türlerini kullanmaya başlamak için özel izninizin olması gerekmez. Aslında, kendi Azure aboneliklerinize özel olarak olay yayımlamak ve bir iş ortağı haline getiriyorsanız kaynak modelini test etmek için bunları bugün kullanabilirsiniz.

> [!NOTE]
> Azure portal kullanarak Event Grid iş ortağı olarak ekleme hakkında adım adım yönergeler için, bkz. [Event Grid iş ortağı olarak ekleme (Azure Portal)](onboard-partner.md). 

## <a name="how-partner-events-work"></a>İş ortağı olayları nasıl çalışır?
Iş ortağı olayları özelliği, Azure depolama ve Azure IoT Hub gibi Azure kaynaklarından olayları yayınlamak için Event Grid zaten kullandığı mevcut mimariyi alır ve bu araçların herkesin kullanmasına açık bir şekilde kullanılabilmesini sağlar. Bu araçların kullanılması, varsayılan olarak yalnızca Azure aboneliğiniz için özeldir. Olaylarınızın genel kullanıma açık olması için formu doldurun ve [Event Grid ekibine başvurun](mailto:gridpartner@microsoft.com).

Iş ortağı olayları özelliği, çok kiracılı tüketim için Azure Event Grid olayları yayımlamanıza izin verir.

## <a name="onboarding-and-event-publishing-overview"></a>Ekleme ve olay yayımlamaya genel bakış

### <a name="partner-flow"></a>İş ortağı akışı

1. Henüz yoksa bir Azure kiracısı oluşturun.
1. Yeni bir Event Grid oluşturmak için Azure CLı 'yi kullanın `partnerRegistration` . Bu kaynak, görünen ad, açıklama, kurulum URI 'SI vb. gibi bilgileri içerir.

    ![İş ortağı oluşturma konusu](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Olayları yayımlamak istediğiniz her bölgede bir veya daha fazla iş ortağı ad alanı oluşturun. Event Grid hizmeti bir yayımlama uç noktası (örneğin, `https://contoso.westus-1.eventgrid.azure.net/api/events` ) ve erişim anahtarları sağlar.

    ![İş ortağı ad alanı oluşturma](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Müşterilerin sisteminize bir iş ortağı konusu için kayıt yaptırmaları için bir yol sağlayın.
1. İş ortağı konu türünün genel hale gelmesini istediğinizi bildirmek için Event Grid ekibine başvurun.

### <a name="customer-flow"></a>Müşteri akışı

1. Müşteriniz, içinde iş ortağı konusunun oluşturulmasını istedikleri Azure abonelik KIMLIĞI ve kaynak grubu ' nu izlemek için Azure portal ziyaret ediyor.
1. Müşteri, sisteminiz aracılığıyla bir iş ortağı konusu ister. Yanıt olarak, iş ortağı ad alanınız için bir olay tüneli oluşturursunuz.
1. Event Grid müşterinin Azure aboneliğinde ve kaynak grubunda **bekleyen** bir iş ortağı konusu oluşturur.

    ![Olay kanalı oluşturma](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Müşteri, Azure portal aracılığıyla iş ortağı konusunu etkinleştirir. Olaylar artık hizmetinize müşterinin Azure aboneliğine akabilir.

    ![İş ortağını etkinleştirme konuları](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Kaynak modeli
Aşağıdaki kaynak modeli Iş ortağı olayları içindir.

### <a name="partner-registrations"></a>İş ortağı kayıtları
* Kaynak `partnerRegistrations`
* Kullanan: Iş ortakları
* Açıklama: hizmet olarak yazılım (SaaS) iş ortağının genel meta verilerini yakalar (örneğin, ad, görünen ad, açıklama, kurulum URI 'SI).
    
    İş ortağı kaydı oluşturma veya güncelleştirme, iş ortakları için self servis bir işlemdir. Bu self servis özelliği, iş ortaklarının tam uçtan uca akışı oluşturmasını ve test etmesini sağlar.
    
    Yalnızca Microsoft tarafından onaylanan iş ortağı kayıtları müşteriler tarafından bulunabilir.
* Kapsam: iş ortağının Azure aboneliğinde oluşturuldu. Meta veriler, genel kullanıma alındıktan sonra müşteriler tarafından görülebilir.

### <a name="partner-namespaces"></a>İş ortağı ad alanları
* Kaynak `partnerNamespaces`
* Kullanan: Iş ortakları
* Açıklama: müşteri olaylarını yayımlamak için bir bölgesel kaynak sağlar. Her iş ortağı ad alanı bir yayımlama uç noktası ve kimlik doğrulama anahtarlarına sahiptir. Ad alanı Ayrıca ortağın belirli bir müşteri için bir iş ortağı konusu istemesi ve etkin müşterileri listelemesidir.
* Kapsam: iş ortağının aboneliğinde bulunur.

### <a name="event-channel"></a>Olay kanalı
* Kaynak `partnerNamespaces/eventChannels`
* Kullanan: Iş ortakları
* Açıklama: olay kanalları, müşterinin iş ortağı konusunun bir yansımasıdır. Bir olay kanalı oluşturup müşterinin Azure aboneliğini ve kaynak grubunu meta verilerde belirterek, müşteri için bir iş ortağı konusu oluşturmak üzere Event Grid ' ı işaret edersiniz. Event Grid, müşterinin aboneliğindeki ilgili iş ortağı konu başlığını oluşturmak için Azure Resource Manager bir çağrı yayınlar. İş ortağı konusu, bekleyen bir durumda oluşturulur. Her olay kanalı ve iş ortağı konusu arasında bire bir bağlantı vardır.
* Kapsam: iş ortağının aboneliğinde bulunur.

### <a name="partner-topics"></a>İş ortağı konuları
* Kaynak `partnerTopics`
* Kullanan: müşteriler
* Açıklama: Iş ortağı konuları Event Grid içindeki özel konulara ve sistem konularına benzer. Her iş ortağı konusu belirli bir kaynak (örneğin, `Contoso:myaccount` ) ve belirli bir iş ortağı konu türü (örneğin, Contoso) ile ilişkilendirilir. Müşteriler, olayları çeşitli olay işleyicilerine yönlendirmek için iş ortağı konusunda olay abonelikleri oluşturur.

    Müşteriler bu kaynağı doğrudan oluşturamaz. İş ortağı konusu oluşturmanın tek yolu, bir olay kanalı oluşturan iş ortağı işlemidir.
* Kapsam: müşterinin aboneliğinde bulunur.

### <a name="partner-topic-types"></a>İş ortağı konu türleri
* Kaynak `partnerTopicTypes`
* Kullanan: müşteriler
* Açıklama: Iş ortağı konu türleri, müşterilerin onaylanan iş ortağı konu türleri listesini bulmasını sağlayan kiracı genelinde kaynak türleridir. URL şöyle görünür https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Kapsam: genel

## <a name="publish-events-to-event-grid"></a>Olayları Event Grid yayımlayın
Bir Azure bölgesinde bir iş ortağı ad alanı oluşturduğunuzda, bölgesel bir uç nokta ve karşılık gelen kimlik doğrulama anahtarlarını alırsınız. Bu ad alanındaki tüm müşteri olay kanalları için olay toplu işlerini bu uç noktaya yayımlayın. Olaydaki kaynak alana göre her bir olayı ilgili iş ortağı konularıyla eşler Azure Event Grid.

### <a name="event-schema-cloudevents-v10"></a>Olay şeması: CloudEvents v 1.0
CloudEvents 1,0 şemasını kullanarak olayları Azure Event Grid yayımlayın. Event Grid hem yapılandırılmış modu hem de toplu modunu destekler. CloudEvents 1,0, iş ortağı ad alanları için desteklenen tek olay şemadır.

### <a name="example-flow"></a>Örnek akış

1.  Yayımlama hizmeti bir HTTP GÖNDERISINI `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .
1.  İstekte, kimlik doğrulaması için bir anahtar içeren AEG-SAS-Key adlı bir başlık değeri ekleyin. Bu anahtar, iş ortağı ad alanının oluşturulması sırasında sağlanır. Örneğin, geçerli bir üst bilgi değeri AEG-SAS-Key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg = = olur.
1.  Content-Type üstbilgisini "Application/cloudevents-Batch + JSON" olarak ayarlayın. charset = UTF-8A ".
1.  Yayımlama URL 'sine, bu bölgeye karşılık gelen olaylar toplu işi ile bir HTTP POST sorgusu çalıştırın. Örnek:

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

İş ortağı ad alanı uç noktasına gönderdikten sonra bir yanıt alırsınız. Yanıt standart bir HTTP yanıt kodudur. Bazı genel yanıtlar şunlardır:

| Sonuç                             | Yanıt              |
|------------------------------------|-----------------------|
| Başarılı                            | 200 TAMAM                |
| Olay verileri hatalı biçime sahip    | 400 Hatalı İstek       |
| Geçersiz erişim anahtarı                 | 401 Yetkisiz      |
| Geçersiz uç nokta                 | 404 Bulunamadı         |
| Dizi veya olay boyut sınırlarını aşıyor | 413 yükü çok büyük |

## <a name="references"></a>Başvurular

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM şablonu](/azure/templates/microsoft.eventgrid/allversions)
  * [ARM şablon şeması](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST API'leri](/azure/templates/microsoft.eventgrid/2020-04-01-preview/partnernamespaces)
  * [CLı uzantısı](/cli/azure/)

### <a name="sdks"></a>SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Git](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Sonraki adımlar
- [İş ortağı konularına genel bakış](partner-events-overview.md)
- [İş ortağı konuları ekleme formu](https://aka.ms/gridpartnerform)
- [Auth0 iş ortağı konusu](auth0-overview.md)
- [Auth0 iş ortağı konusunu kullanma](auth0-how-to.md)
