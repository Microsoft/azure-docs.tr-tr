---
title: Azure Event Grid içindeki olay etki alanları
description: Bu makalede, çeşitli iş kuruluşlarınız, müşterileriniz veya uygulamalarınız için özel olayların akışını yönetmek üzere olay etki alanlarının nasıl kullanılacağı açıklanır.
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 32c06ac55f667ec9807c7952127c2cf0f0384024
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374718"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Event Grid yönetmek için olay etki alanlarını anlama

Bu makalede, çeşitli iş kuruluşlarınız, müşterileriniz veya uygulamalarınız için özel olayların akışını yönetmek üzere olay etki alanlarının nasıl kullanılacağı açıklanır. Olay etki alanlarını kullanarak şunları yapın:

* Çok kiracılı olay mimarilerini ölçeklendirerek yönetin.
* Yetkilendirmeyi ve kimlik doğrulamayı yönetin.
* Her birini ayrı ayrı yönetmeden konuları bölümleyin.
* Konu uç noktalarınızın her birinde tek tek yayımlamayı önleyin.

## <a name="event-domain-overview"></a>Olay etki alanına genel bakış

Olay etki alanı, aynı uygulamayla ilgili çok sayıda Event Grid konu başlığı için bir yönetim aracıdır. Bunu, binlerce bireysel konuya sahip bir meta konu olarak düşünebilirsiniz.

Olay etki alanları, Azure hizmetleri tarafından, olaylarını yayımlamak için depolama ve IoT Hub gibi kullanılan mimarinin aynısını sunar. Bunlar binlerce konuya etkinlik yayımlamanıza izin verir. Etki alanları, Kiracılarınızı bölümleyerek her konu için yetkilendirme ve kimlik doğrulama denetimi de sağlar.

## <a name="example-use-case"></a>Örnek kullanım örneği
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Erişim yönetimi

Bir etki alanı ile Azure rol tabanlı erişim denetimi (Azure RBAC) aracılığıyla her konu üzerinde ince bir yetkilendirme ve kimlik doğrulama denetimi edinirsiniz. Uygulamanızdaki her bir kiracıyı yalnızca erişim vermek istediğiniz konularda kısıtlamak için bu rolleri kullanabilirsiniz.

Olay etki alanlarında Azure RBAC, Event Grid ve Azure 'un geri kalanında aynı şekilde [yönetilen erişim denetimi](security-authorization.md) ile çalışmaktadır. Azure RBAC kullanarak olay etki alanlarında özel rol tanımları oluşturun ve uygulayın.

### <a name="built-in-roles"></a>Yerleşik roller

Event Grid, Azure RBAC 'nin olay etki alanlarıyla çalışmaya daha kolay hale getirmek için iki yerleşik rol tanımına sahiptir. Bu roller **Eventgrid Olayaboneliğine katkıda bulunan (Önizleme)** ve **Eventgrid eventsubscription Reader (Önizleme)**. Bu rolleri, olay etki alanındaki konulara abone olmaları gereken kullanıcılara atarsınız. Rol atamasını yalnızca kullanıcıların abone olmaları gereken konuya göre kapsamınız.

Bu roller hakkında daha fazla bilgi için bkz. [Event Grid Için yerleşik roller](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Konulara abone olma

Bir olay etki alanı içindeki bir konudaki olaylara abone olmak, [özel bir konu üzerinde olay aboneliği oluşturma](./custom-event-quickstart.md) veya bir Azure hizmetinden bir olaya abone olma ile aynıdır.

> [!IMPORTANT]
> Etki alanı konusu Event Grid bir **Otomatik yönetilen** kaynak olarak değerlendirilir. Etki alanı konusunu oluşturmadan etki alanı konu kapsamında bir olay aboneliği oluşturabilirsiniz. Bu durumda Event Grid, etki alanı konusunu sizin adınıza otomatik olarak oluşturur. Kuşkusuz, yine de etki alanı konusunu el ile oluşturmayı tercih edebilirsiniz. Bu davranış, çok sayıda etki alanı konusu ile ilgilenirken bir veya daha az kaynak hakkında endişelenmenize olanak tanır. Bir etki alanı konusunun son aboneliği silindiğinde, etki alanı konusunun el ile oluşturulup oluşturulmayacağı veya otomatik olarak oluşturulduğu dikkate alındığı zaman etki alanı konusu de silinir. 

### <a name="domain-scope-subscriptions"></a>Etki alanı kapsamı abonelikleri

Olay etki alanları da etki alanı kapsamı aboneliklerine izin verir. Olay etki alanındaki bir olay aboneliği, olayların gönderildiği konuya bakılmaksızın etki alanına gönderilen tüm olayları alır. Etki alanı kapsamı abonelikleri, yönetim ve denetim amacıyla yararlı olabilir.

## <a name="publishing-to-an-event-domain"></a>Bir olay etki alanına yayımlama

Bir olay etki alanı oluşturduğunuzda, Event Grid ' de bir konu oluşturduysanız, şuna benzer bir yayımlama uç noktası vermiş olursunuz. 

Olayları bir olay etki alanında herhangi bir konuya yayımlamak için olayları, [özel konu için yaptığınız gibi](./post-to-custom-topic.md), etki alanının uç noktasına gönderin. Tek fark, etkinliğin teslim edilmesini istediğiniz konuyu belirtmektir.

Örneğin, aşağıdaki olay dizisinin yayımlanması, `"id": "1111"` `foo` ile olayı konuya gönderilecek şekilde olayı konusuna gönderir `"id": "2222"` `bar` :

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
- bir olay etki alanındaki Konu başına 500 olay aboneliği
- 50 etki alanı kapsamı abonelikleri 
- saniye başına 5.000 olay alma oranı (bir etki alanına)

Bu sınırlar size uygun değilse, bir destek bileti açın veya adresine e-posta gönderin [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Fiyatlandırma
Olay etki alanları, Event Grid ' deki diğer tüm özelliklerin kullandığı aynı [işlem fiyatlandırmasını](https://azure.microsoft.com/pricing/details/event-grid/) kullanır.

İşlemler, olay etki alanlarında özel konularda olduğu gibi çalışır. Bir olayın olay etki alanına her giriş işlemi bir işlemdir ve bir olaya yönelik her bir teslim girişimi bir işlemdir.



## <a name="next-steps"></a>Sonraki adımlar

* Olay etki alanlarını ayarlama, konu oluşturma, olay abonelikleri oluşturma ve olayları yayımlama hakkında bilgi edinmek için bkz. [olay etki alanlarını yönetme](./how-to-event-domains.md).
