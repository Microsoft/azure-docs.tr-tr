---
title: include dosyası
description: include dosyası
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: e160b21581bc7b5fa38b12309bd9deb90bfbbe51
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251262"
---
Aşağıdaki tabloda Azure Service Bus mesajlaşma 'ya özgü kota bilgileri listelenmektedir. Service Bus yönelik fiyatlandırma ve diğer kotalar hakkında daha fazla bilgi için bkz. [Service Bus fiyatlandırması](https://azure.microsoft.com/pricing/details/service-bus/).

| Kota adı | Kapsam | Notlar | Değer |
| --- | --- | --- | --- |
| Azure aboneliği başına en fazla temel veya standart ad alanı sayısı |Ad Alanı |Ek temel veya standart ad alanları için sonraki istekler Azure portal tarafından reddedilir. | Varsayılan değer 100'dür. <br/> Maksimum değer 1.000 ' dir. <br/><br/> Limiti artırmak için Azure desteği ile iletişim kurun. |
| Azure aboneliği başına en fazla Premium ad alanı sayısı |Ad Alanı |Ek Premium ad alanları için sonraki istekler Portal tarafından reddedilir. | Varsayılan değer 100'dür. <br/> Maksimum değer 1.000 ' dir. <br/><br/> Limiti artırmak için Azure desteği ile iletişim kurun. |
| Kuyruk veya konu boyutu |Varlık |Sıra veya konu oluşturulduktan sonra tanımlanır. <br/><br/> Sonraki gelen iletiler reddedilir ve çağıran kod tarafından bir özel durum alınır. |1, 2, 3, 4 GB veya 5 GB.<br /><br />Premium SKU 'sunda ve [bölümlendirme](../articles/service-bus-messaging/service-bus-partitioning.md) ETKINLEŞTIRILMIŞ standart SKU 'da en büyük sıra veya konu boyutu 80 GB 'tır. |
| Bir ad alanındaki eşzamanlı bağlantı sayısı |Ad Alanı |Daha sonraki ek bağlantı istekleri reddedilir ve çağıran kod tarafından bir özel durum alınır. REST işlemleri, eşzamanlı TCP bağlantılarına doğru sayılmaz. |NET mesajlaşma: 1.000.<br /><br />AMQP: 5.000. |
| Bir kuyruk, konu veya abonelik varlığındaki eşzamanlı alma isteği sayısı |Varlık |Sonraki alma istekleri reddedilir ve çağıran kod tarafından bir özel durum alınır. Bu kota, bir konudaki tüm abonelikler arasında eş zamanlı alma işlemlerinin Birleşik sayısı için geçerlidir. |5.000 |
| Ad alanı başına konu veya kuyruk sayısı |Ad Alanı |Ad alanındaki yeni bir konunun veya kuyruğun oluşturulması için sonraki istekler reddedilir. Sonuç olarak, [Azure Portal][Azure portal]ile yapılandırıldıysa bir hata iletisi oluşturulur. Yönetim API 'sinden çağrılırsa, çağıran kod tarafından bir özel durum alınır. |Temel veya Standart katman için 10.000. Bir ad alanındaki konuların ve kuyrukların toplam sayısı 10.000 ' e eşit veya ondan küçük olmalıdır. <br/><br/>Premium katmanı için, 1.000 ileti birimi başına (MU). |
| Ad alanı başına [bölümlenmiş konuların veya kuyrukların](../articles/service-bus-messaging/service-bus-partitioning.md) sayısı |Ad Alanı |Ad alanındaki yeni bir bölümlenmiş konunun veya kuyruğun oluşturulması için sonraki istekler reddedilir. Sonuç olarak, [Azure Portal][Azure portal]ile yapılandırıldıysa bir hata iletisi oluşturulur. Yönetim API 'sinden çağrılırsa, **QuotaExceededException** özel durumu çağıran kod tarafından alınır. |Temel ve Standart katmanlar: 100.<br/><br/>Bölümlenmiş varlıklar [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) katmanda desteklenmez.<br/><br />Her bölümlenmiş kuyruk veya konu, ad alanı başına 1.000 varlık kotasına doğru sayılır. |
| Herhangi bir mesajlaşma varlığı yolunun en büyük boyutu: kuyruk veya konu |Varlık |- |260 karakter. |
| Herhangi bir mesajlaşma varlığı adının en büyük boyutu: ad alanı, abonelik veya abonelik kuralı |Varlık |- |50 karakter. |
| İleti KIMLIĞININ en büyük boyutu | Varlık |- | 128 |
| İleti oturumu KIMLIĞININ en büyük boyutu | Varlık |- | 128 |
| Bir kuyruk, konu veya abonelik varlığı için ileti boyutu |Varlık |Bu kotaları aşan gelen iletiler reddedilir ve çağıran kod tarafından bir özel durum alınır. |En büyük ileti boyutu: [Standart katman](../articles/service-bus-messaging/service-bus-premium-messaging.md)IÇIN 256 KB, [Premium katman](../articles/service-bus-messaging/service-bus-premium-messaging.md)için 1 MB. <br /><br />Sistem yükü nedeniyle, bu sınır bu değerlerden küçüktür.<br /><br />En büyük üst bilgi boyutu: 64 KB.<br /><br />Özellik paketinde en fazla üstbilgi özelliği sayısı: **byte/int. MaxValue**.<br /><br />Özellik paketinde özelliğin en büyük boyutu: Açık sınır yok. Üst bilgi boyutuyla sınırlıdır. |
| Bir kuyruk, konu veya abonelik varlığı için ileti özelliği boyutu |Varlık | Özel durum `SerializationException` oluşturulur. |Her özellik için en büyük ileti özelliği boyutu 32.000 ' dir. Tüm özelliklerin birikimli boyutu 64.000 ' i aşamaz. Bu sınır, hem Kullanıcı özelliklerine hem de sıra numarası, etiket ve ileti KIMLIĞI gibi sistem özelliklerine sahip olan aracılı ileti üstbilgisinin tamamına uygulanır. |
| Konu başına abonelik sayısı |Varlık |Konu için ek abonelikler oluşturmaya yönelik sonraki istekler reddedilir. Sonuç olarak, Portal üzerinden yapılandırıldıysa bir hata iletisi gösterilir. Yönetim API 'sinden çağrılırsa, çağıran kod tarafından bir özel durum alınır. |Standart katman ve Premium katman için 2.000 konu. |
| Konu başına SQL filtresi sayısı |Varlık |Konu üzerinde ek filtreler oluşturmaya yönelik sonraki istekler reddedilir ve çağıran kod tarafından bir özel durum alınır. |2.000 |
| Konu başına bağıntı filtresi sayısı |Varlık |Konu üzerinde ek filtreler oluşturmaya yönelik sonraki istekler reddedilir ve çağıran kod tarafından bir özel durum alınır. |100.000 |
| SQL filtrelerinin veya eylemlerinin boyutu |Ad Alanı |Ek filtrelerin oluşturulmasına yönelik sonraki istekler reddedilir ve çağıran kod tarafından bir özel durum alınır. |Maksimum filtre koşulu dizesi uzunluğu: 1.024 (1 K).<br /><br />Kural eylemi dizesinin uzunluk üst sınırı: 1.024 (1 K).<br /><br />Kural eylemi başına en fazla deyim sayısı: 32. |
| Ad alanı, kuyruk veya konu başına paylaşılan erişim yetkilendirme kuralı sayısı |Varlık, ad alanı |Ek kuralların oluşturulmasına yönelik sonraki istekler reddedilir ve çağıran kod tarafından bir özel durum alınır. |Varlık türü başına en fazla kural sayısı: 12. <br /><br /> Bir Service Bus ad alanı üzerinde yapılandırılan kurallar tüm türler için geçerlidir: kuyruklar, konular. |
| İşlem başına ileti sayısı | İşlem | Diğer gelen iletiler reddedilir ve "tek bir işlemde 100 'den fazla ileti gönderilemiyor" belirten bir özel durum, çağıran kod tarafından alınır. | 100 <br /><br /> Hem **Send ()** hem de **sendadsync ()** işlemleri için. |
| Sanal ağ ve IP filtresi kuralları sayısı | Ad Alanı | &nbsp; | 128 |

[Azure portal]: https://portal.azure.com
