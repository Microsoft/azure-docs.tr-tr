---
title: Azure Service Bus Premium ve Standart Mesajlaşma hizmeti fiyatlandırma katmanlarına genel bakış | Microsoft Docs
description: Service Bus Premium ve Standart Mesajlaşma katmanları
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: 7565ce24199dd8f86f756f01f66aa79e764a1a12
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72752199"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus Premium ve Standart Mesajlaşma katmanları

Kuyruklar ve konu başlıkları gibi varlıkları içeren Service Bus Mesajlaşma, kuruluşun mesajlaşma işlevlerini bulut ölçeğinde zengin yayımla-abone ol semantiği ile birleştirir. Service Bus Mesajlaşması birçok gelişmiş bulut çözümü için iletişimin temel öğesi olarak kullanılır.

Service Bus Mesajlaşma hizmetinin *Premium* katmanı, görev açısından kritik uygulamalar için ölçek, performans ve kullanılabilirlik bağlamında yaygın müşteri isteklerini karşılar. Üretim senaryoları için Premium katman önerilir. Özellikler kümeleri neredeyse aynı olsa da, Service Bus Mesajlaşma hizmetinin bu iki katmanı farklı kullanım durumlarına göre tasarlanmıştır.

Aşağıdaki tabloda bazı üst düzey farklılıklar vurgulanmıştır.

| Premium | Standart |
| --- | --- |
| Yüksek işleme düzeyi |Değişken işleme |
| Öngörülebilir performans |Değişken gecikme süresi |
| Sabit fiyatlandırma |Kullandıkça Öde değişken fiyatlandırması |
| İş yükünün ölçeğini artırma veya azaltma |Yok |
| İleti boyutu 1 MB’a kadar |İleti boyutu 256 KB’a kadar |

**Service Bus Premium Mesajlaşma Hizmeti**, CPU'da ve bellek düzeyinde kaynak yalıtımına olanak sağladığından her müşterinin iş yükü yalıtımlı şekilde çalışır. Bu kaynak kapsayıcısı *mesajlaşma birimi* olarak adlandırılır. Her premium ad alanı, en az bir mesajlaşma birimi için ayrılmıştır. Her Service Bus Premium ad alanı için 1, 2, 4 veya 8 mesajlaşma birimi satın alabilirsiniz. Tek bir iş yükü veya varlık birden çok mesajlaşma birimini kapsayabilir ve mesaj birimlerinin sayısı her zaman değiştirilebilir. Sonuç olarak, Service Bus tabanlı çözümünüz için tahmin edilebilir ve tekrarlanabilir bir performans elde edersiniz.

Daha tahmin edilebilir ve kullanılabilir olmasının yanı sıra bu performans, daha hızlıdır. Service Bus Premium Mesajlaşma hizmeti, [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) kısmında tanıtılan depolama motorunda derlenir. Premium Mesajlaşma sayesinde, en yüksek performans Standart katmanda olduğundan daha hızlıdır.

## <a name="premium-messaging-technical-differences"></a>Premium Mesajlaşmanın teknik farklılıkları

Aşağıdaki bölümlerde Premium ve Standart mesajlaşma katmanları arasındaki bazı farklar ele alınmaktadır.

### <a name="partitioned-queues-and-topics"></a>Bölümlenmiş kuyruklar ve konular

Bölümlenmiş kuyruk ve konular Premium Mesajlaşma’da desteklenmez. Bölümleme hakkında daha fazla bilgi için bkz. [Bölümlenmiş kuyruklar ve konular](service-bus-partitioning.md).

### <a name="express-entities"></a>İfade varlıkları

Premium mesajlaşma tamamen yalıtılmış bir çalışma zamanı ortamında çalıştığından Premium ad alanlarında ifade varlıkları desteklenmez. İfade özellikleri hakkında daha fazla bilgi için [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) özelliğine bakın.

Standart mesajlaşma altında çalışan bir kodunuz varsa ve Premium katmanı ile bağlantı noktası oluşturmak istiyorsanız, [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) özelliğinin **false** (varsayılan değer) olarak ayarlandığından emin olun.

## <a name="premium-messaging-resource-usage"></a>Premium mesajlaşma kaynak kullanımı
Genel olarak, bir varlıktaki herhangi bir işlem CPU ve bellek kullanımına neden olabilir. Bu işlemlerden bazıları şunlardır: 

- Kuyruklar, konular ve aboneliklerde CRUD (oluşturma, alma, güncelleştirme ve silme) işlemleri gibi yönetim işlemleri.
- Çalışma zamanı işlemleri (ileti gönderme ve alma)
- İşlemleri ve Uyarıları izleme

Ek CPU ve bellek kullanımı da buna ek olarak fiyatlandırılmamalıdır. Premium mesajlaşma katmanı için, ileti birimi için tek bir fiyat vardır.

CPU ve bellek kullanımı aşağıdaki nedenlerden dolayı izlenir ve size görüntülenir: 

- Sistem iç işlevleri için saydamlık sağlama
- Satın alınan kaynakların kapasitesini anlayın.
- Ölçeği artırma/azaltma kararı vermenize yardımcı olan kapasite planlaması.

## <a name="messaging-unit---how-many-are-needed"></a>Mesajlaşma Birimi-kaç tane gerekir?

Azure Service Bus Premium ad alanı sağlanırken, ayrılan mesajlaşma birimlerinin sayısı belirtilmelidir. Bu mesajlaşma birimleri, ad alanına ayrılan ayrılmış kaynaklardır.

Service Bus Premium ad alanına ayrılan mesajlaşma birimi sayısı, iş yüklerindeki değişikliğin (artış veya azaldıkça) **dinamik olarak** çarpılabilecek şekilde ayarlanabilir.

Mimariniz için Mesajlaşma Birimi sayısına karar verirken dikkate alınması gereken birkaç etken vardır:

- Ad alanınız için ayrılmış ***1 veya 2 mesajlaşma birimi*** ile başlayın.
- Ad alanınız için [kaynak kullanım ölçümleri](service-bus-metrics-azure-monitor.md#resource-usage-metrics) içindeki CPU kullanım ölçümlerini inceleyin.
    - CPU kullanımı ***%20***' nin altındaysa, ad alanınız için ayrılan mesajlaşma birimi sayısını ***ölçeklendirebilirsiniz*** .
    - CPU kullanımı ***%70 üzerinde***ise, uygulamanız ad alanınız için ayrılan mesajlaşma birimlerinin sayısını ***ölçeklendirmeden*** yarar sağlar.

Bir Service Bus ad alanına ayrılan kaynakları ölçeklendirme işlemi, [Azure Otomasyonu runbook 'ları](../automation/automation-quickstart-create-runbook.md)kullanılarak otomatikleştirilebilir.

> [!NOTE]
> Ad alanına ayrılan kaynakların **ölçeklendirilmesi** preemptive veya reaktif olabilir.
>
>  * **Preemptive**: ek iş yükü bekleniyorsa (mevsimsellik veya eğilimleri nedeniyle), iş yüklerinden önce ad alanına daha fazla mesajlaşma birimi ayırmaya devam edebilirsiniz.
>
>  * **Reaktif**: kaynak kullanım ölçümlerini sağlayarak ek iş yükleri tanımlanmışsa, artan talebi birleştirmek için ad alanına ek kaynaklar ayrılabilir.
>
> Service Bus için faturalandırma ölçüleri her saat için faturalandırılır. Ölçeği artırma durumunda, yalnızca bunların kullanıldığı saatlere ait Ek kaynaklar için ödeme yaparsınız.
>

## <a name="get-started-with-premium-messaging"></a>Premium Mesajlaşmayı kullanmaya başlama

Premium Mesajlaşma ile çalışmaya başlamak kolaydır ve süreç Standart Mesajlaşma ile benzerlik gösterir. [Azure Portal](https://portal.azure.com)'da [ad alanı oluşturarak](service-bus-create-namespace-portal.md) başlayın. **Fiyatlandırma katmanı** için **Premium**'u seçtiğinizden emin olun. Her bir katman hakkında daha fazla bilgi almak için **Fiyatlandırma ayrıntılarının tamamını görüntüle**'ye tıklayın.

![create-premium-namespace][create-premium-namespace]

Ayrıca [Azure Resource Manager şablonlarını kullanarak premium ad alanları](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/) oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Service Bus Mesajlaşma hizmeti hakkında daha fazla bilgi edinmek için aşağıdaki bağlantılara bakın:

* [Azure Service Bus Mesajlaşma hizmetine giriş (blog gönderisi)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure Service Bus Premium Mesajlaşma hizmetine giriş (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Service Bus Mesajlaşma hizmetine genel bakış](service-bus-messaging-overview.md)
* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
