---
title: "Azure Service Bus ileti işleme mimarisine genel bakış | Microsoft Docs"
description: "Azure Service Bus hizmetinin ileti ve geçiş işleme mimarisini açıklar."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: baf94c2d-0e58-4d5d-a588-767f996ccf7f
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 3df12cd8700a75c1288967e86cd92e6ed4886d59
ms.lasthandoff: 03/24/2017


---
# <a name="service-bus-architecture"></a>Service Bus mimarisi
Bu makale, Azure Service Bus hizmetinin ileti işleme mimarisini açıklar.

## <a name="service-bus-scale-units"></a>Service Bus ölçek birimleri
Service Bus, *ölçek birimleri* tarafından düzenlenir. Ölçek birimi, bir dağıtım birimidir ve hizmeti çalıştırmak için gerekli tüm bileşenleri içerir. Her bölge, bir veya daha fazla Service Bus ölçek birimi dağıtır.

Service Bus ad alanı, bir ölçek birimi ile eşleştirilir. Ölçek birimi, Service Bus varlıklarının her türünü işler: Geçişler ve aracılı mesajlaşma varlıkları (kuyruklar, konu başlıkları, abonelikler). Service Bus ölçek birimi, şu bileşenlerden oluşur:

* **Ağ geçidi düğümleri kümesi.** Ağ geçidi düğümleri, gelen isteklerin kimliklerini doğrular ve geçiş isteklerini işler. Her ağ geçidi düğümünün genel bir IP adresi vardır.
* **Mesajlaşma aracısı düğümleri kümesi.** Mesajlaşma aracısı düğümleri, mesajlaşma varlıkları ile ilgili istekleri işler.
* **Bir ağ geçidi deposu.** Ağ geçidi deposu, bu ölçek biriminde tanımlanan her varlık için veriler tutar. Ağ geçidi deposu, SQL Azure veritabanının üst kısmında uygulanır.
* **Birden çok mesajlaşma deposu.** Mesajlaşma depoları, bu ölçek biriminde tanımlanan tüm kuyrukların, konu başlıklarının ve aboneliklerin iletilerini içerir. Ayrıca, tüm abonelik verilerini de içerir. [Bölümlenmiş mesajlaşma varlıkları](service-bus-partitioning.md) etkinleştirilmediği sürece kuyruk veya konu başlığı, bir mesajlaşma deposuna eşlenir. Abonelikler, üst konu başlıklarıyla aynı mesajlaşma deposunda depolanır. Service Bus [Premium Mesajlaşma](service-bus-premium-messaging.md) dışındaki mesajlaşma depoları, SQL Azure veritabanının üst kısmında uygulanır.

## <a name="containers"></a>Kapsayıcılar
Her mesajlaşma varlığı, belirli bir kapsayıcıya atanır. Kapsayıcı, kendisiyle ilgili tüm verileri depolamak için tam olarak bir mesajlaşma deposu kullanan mantıksal bir yapıdır. Her kapsayıcı, bir mesajlaşma aracısı düğümüne atanır. Genellikle, mesajlaşma aracısı düğümlerinden daha fazla sayıda kapsayıcı vardır. Bu nedenle, her mesajlaşma aracısı düğümü birden çok kapsayıcı yükler. Kapsayıcıların bir mesajlaşma aracısı düğümüne dağıtımı, tüm mesajlaşma aracısı düğümlerine eşit olarak yüklenecek şekilde düzenlenmiştir. Yük düzeni değişirse (örneğin, kapsayıcılardan biri çok meşgul olursa) veya bir mesajlaşma aracısı düğümü geçici olarak kullanılamazsa kapsayıcılar mesajlaşma aracısı düğümleri arasında yeniden dağıtılır.

## <a name="processing-of-incoming-messaging-requests"></a>Gelen mesajlaşma isteklerinin işlenmesi
Bir istemci, Service Bus hizmetine istek gönderdiğinde Azure yük dengeleyici bu isteği ağ geçidi düğümlerinden herhangi birine yönlendirir. Ağ geçidi düğümü, isteği yetkilendirir. İstek, mesajlaşma varlığıyla (kuyruk, konu başlığı, abonelik) ilgiliyse ağ geçidi düğümü, ağ geçidi deposunda varlığı arar ve varlığın hangi mesajlaşma deposunda bulunduğunu belirler. Daha sonra, şu anda bu kapsayıcıya hizmet veren mesajlaşma aracısı düğümünü arar ve o mesajlaşma aracısı düğümüne istek gönderir. Mesajlaşma aracısı düğümü, isteği işler ve kapsayıcı deposundaki varlık durumunu güncelleştirir. Ardından mesajlaşma aracısı düğümü, orijinal talebi sağlayan istemciye uygun yanıtı gönderen ağ geçidi düğümüne isteği geri gönderir.

![Gelen Mesajlaşma İsteklerinin İşlenmesi](./media/service-bus-architecture/IC690644.png)

## <a name="processing-of-incoming-relay-requests"></a>Gelen geçiş isteklerinin işlenmesi
Bir istemci, Service Bus hizmetine istek gönderdiğinde Azure yük dengeleyici bu isteği ağ geçidi düğümlerinden herhangi birine yönlendirir. İstek, bir dinleme isteğiyse ağ geçidi düğümü yeni bir geçiş oluşturur. İstek, belirli bir geçiş için bağlantı isteğiyse ağ geçidi düğümü, geçişe sahip olan ağ geçidi düğümüne bağlantı isteğini iletir. Geçişe sahip ağ geçidi dinleyen istemciye bir randevu isteği göndererek dinleyiciden bağlantı isteğini alan ağ geçidi düğümüne geçici bir kanal kurmasını ister.

Geçiş bağlantısı kurulduğunda istemciler, randevu için kullanılan ağ geçidi düğümü aracılığıyla iletileri değiştirebilir.

![Gelen WCF Geçiş İsteklerinin işlenmesi](./media/service-bus-architecture/IC690645.png)

## <a name="next-steps"></a>Sonraki adımlar
Service Bus mimarisine ilişkin genel bir bakış edindiğinize göre, daha fazla bilgi için aşağıdaki bağlantıları ziyaret edebilirsiniz:

* [Service Bus mesajlaşma hizmetine genel bakış](service-bus-messaging-overview.md)
* [Azure Geçiş’e genel bakış](../service-bus-relay/relay-what-is-it.md)
* [Service Bus ile ilgili temel bilgiler](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus kuyruklarını kullanan kuyruğa alınan mesajlaşma çözümü](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)



