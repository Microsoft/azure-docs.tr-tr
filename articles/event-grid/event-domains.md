---
title: Azure Event Grid içindeki olay etki alanları
description: Azure Event Grid 'daki konuları yönetmek için olay etki alanlarının nasıl kullanıldığını açıklar.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: ef0a9213d095d0b7ae4343e2af145236a7e005a1
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305417"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Event Grid yönetmek için olay etki alanlarını anlama

Bu makalede, çeşitli iş kuruluşlarınız, müşterileriniz veya uygulamalarınız için özel olayların akışını yönetmek üzere olay etki alanlarının nasıl kullanılacağı açıklanır. Olay etki alanlarını kullanarak şunları yapın:

* Çok kiracılı olay mimarilerini ölçeklendirerek yönetin.
* Yetkilendirmeyi ve kimlik doğrulamayı yönetin.
* Her birini ayrı ayrı yönetmeden konuları bölümleyin.
* Konu uç noktalarınızın her birinde tek tek yayımlamayı önleyin.

## <a name="event-domain-overview"></a>Olay etki alanına genel bakış

Olay etki alanı, aynı uygulamayla ilgili çok sayıda Event Grid konu başlığı için bir yönetim aracıdır. Bunu, binlerce bireysel konuya sahip bir meta konu olarak düşünebilirsiniz.

Olay etki alanları, olaylarını yayımlamak için Azure Hizmetleri (depolama ve IoT Hub gibi) tarafından kullanılan mimaride kullanılabilir hale getirir. Bunlar binlerce konuya etkinlik yayımlamanıza izin verir. Etki alanları, Kiracılarınızı bölümleyerek her konu için yetkilendirme ve kimlik doğrulama denetimi de sağlar.

### <a name="example-use-case"></a>Örnek kullanım örneği

Olay etki alanları bir örnek kullanılarak kolayca açıklanmıştır. Tractors, dijitalde ekipman ve diğer ağır makineleri ürettiğiniz contoso oluşturma makinemizin olduğunu varsayalım. İşletmeyi çalıştırmanın bir parçası olarak, donanım bakımı, sistem durumu ve sözleşme güncelleştirmeleri hakkındaki müşterilere gerçek zamanlı bilgiler gönderirsiniz. Bu bilgilerin tümü, uygulamanız, müşteri uç noktaları ve müşterilerin ayarladığı diğer altyapıda dahil olmak üzere çeşitli uç noktalara gider.

Olay etki alanları, contoso oluşturma makineleri tek bir olay varlığı olarak modelleyebilir. Müşterilerinizin her biri etki alanı içinde bir konu olarak temsil edilir. Kimlik doğrulama ve yetkilendirme Azure Active Directory kullanılarak işlenir. Müşterilerinizin her biri konuya abone olabilir ve olaylarını kendilerine teslim edebilir. Olay etki alanı üzerinden yönetilen erişim, yalnızca kendi konularına erişebilmelerini sağlar.

Ayrıca, size tüm müşteri olaylarınızı yayımlayabilmeniz için tek bir uç nokta sağlar. Event Grid her konunun yalnızca kiracının kapsamına alınmış olayların farkında olduğundan emin olunacaktır.

![Contoso yapımı örneği](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Erişim yönetimi

Bir etki alanı ile, Azure 'un rol tabanlı erişim denetimi (RBAC) aracılığıyla her konu üzerinde ince bir yetkilendirme ve kimlik doğrulama denetimi edinirsiniz. Uygulamanızdaki her bir kiracıyı yalnızca erişim vermek istediğiniz konularda kısıtlamak için bu rolleri kullanabilirsiniz.

Olay etki alanlarında RBAC, Event Grid ve Azure 'un geri kalanında aynı şekilde [yönetilen erişim denetimi](security-authentication.md#management-access-control) ile çalışmaktadır. Olay etki alanlarında özel rol tanımları oluşturmak ve zorlamak için RBAC kullanın.

### <a name="built-in-roles"></a>Yerleşik roller

Event Grid, RBAC 'nin olay etki alanlarıyla çalışmaya daha kolay hale getirmek için iki yerleşik rol tanımına sahiptir. Bu roller **Eventgrid Olayaboneliğine katkıda bulunan (Önizleme)** ve **Eventgrid eventsubscription Reader (Önizleme)** . Bu rolleri, olay etki alanındaki konulara abone olmaları gereken kullanıcılara atarsınız. Rol atamasını yalnızca kullanıcıların abone olmaları gereken konuya göre kapsamınız.

Bu roller hakkında daha fazla bilgi için bkz. [Event Grid Için yerleşik roller](security-authentication.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Konulara abone olma

Bir olay etki alanı içindeki bir konudaki olaylara abone olmak, [özel bir konu üzerinde olay aboneliği oluşturma](./custom-event-quickstart.md) veya bir Azure hizmetinden bir olaya abone olma ile aynıdır.

### <a name="domain-scope-subscriptions"></a>Etki alanı kapsamı abonelikleri

Olay etki alanları da etki alanı kapsamı aboneliklerine izin verir. Olay etki alanındaki bir olay aboneliği, olayların gönderildiği konuya bakılmaksızın etki alanına gönderilen tüm olayları alır. Etki alanı kapsamı abonelikleri, yönetim ve denetim amacıyla yararlı olabilir.

## <a name="publishing-to-an-event-domain"></a>Bir olay etki alanına yayımlama

Bir olay etki alanı oluşturduğunuzda, Event Grid ' de bir konu oluşturduysanız, şuna benzer bir yayımlama uç noktası vermiş olursunuz. 

Olayları bir olay etki alanında herhangi bir konuya yayımlamak için olayları, [özel konu için yaptığınız gibi](./post-to-custom-topic.md), etki alanının uç noktasına gönderin. Tek fark, etkinliğin teslim edilmesini istediğiniz konuyu belirtmektir.

Örneğin, aşağıdaki olay dizisinin yayımlanması `"id": "1111"` , ile `"id": "2222"` olayı konuya `bar`gönderilecek şekilde olayı konusuna `foo` gönderir:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Olay etki alanları, sizin için konularda yayımlamayı işler. Tek tek yönettiğiniz her konuya olay yayımlamak yerine, tüm olaylarınızı etki alanının uç noktasına yayımlayabilirsiniz. Event Grid her olayın doğru konuya gönderildiğinden emin olur.

## <a name="limits-and-quotas"></a>Limitler ve kotalar
Olay etki alanlarıyla ilgili sınırlamalar ve Kotalar aşağıda verilmiştir:

- olay etki alanı başına 100.000 konu 
- Azure aboneliği başına 100 olay etki alanı 
- Bir olay etki alanında konu başına 500 olay aboneliği
- 50 etki alanı kapsamı abonelikleri 
- saniye başına 5.000 olay alma oranı (bir etki alanına)

Bu limitlere uygun değilse, bir destek bileti açarak veya uygulamasına [askgrid@microsoft.com](mailto:askgrid@microsoft.com)bir e-posta göndererek ürün ekibine ulaşın. 

## <a name="pricing"></a>Fiyatlandırma
Olay etki alanları, Event Grid ' deki diğer tüm özelliklerin kullandığı aynı [işlem fiyatlandırmasını](https://azure.microsoft.com/pricing/details/event-grid/) kullanır.

İşlemler, olay etki alanlarında özel konularda olduğu gibi çalışır. Bir olayın olay etki alanına her giriş işlemi bir işlemdir ve bir olaya yönelik her bir teslim girişimi bir işlemdir.

## <a name="next-steps"></a>Sonraki adımlar

* Olay etki alanlarını ayarlama, konu oluşturma, olay abonelikleri oluşturma ve olayları yayımlama hakkında bilgi edinmek için bkz. [olay etki alanlarını yönetme](./how-to-event-domains.md).
