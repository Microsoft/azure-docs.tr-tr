---
title: Azure Işlevleri coğrafi olağanüstü durum kurtarma ve yüksek kullanılabilirlik | Microsoft Docs
description: Azure Işlevlerinde artıklık ve yük devretme için coğrafi bölgeleri kullanma.
services: functions
documentationcenter: na
author: wesmc7777
manager: jeconnoc
keywords: Azure işlevleri, desenler, en iyi uygulama, işlevler
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: jehollan
ms.openlocfilehash: 1d75d58a6df622ffb1b277f75ceedc2c2a66369d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576237"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Işlevleri coğrafi olağanüstü durum kurtarma

Tüm Azure bölgeleri veya veri merkezleri kapalı kalma süresi geldiğinde, işlem, farklı bir bölgede işlemeye devam etmek için kritik öneme sahiptir.  Bu makalede, olağanüstü durum kurtarmaya izin vermek için işlevleri dağıtmak üzere kullanabileceğiniz stratejilerin bazıları açıklanmaktadır.

## <a name="basic-concepts"></a>Temel kavramlar

Azure Işlevleri belirli bir bölgede çalışır.  Daha yüksek kullanılabilirlik sağlamak için aynı işlevleri birden çok bölgeye dağıtabilirsiniz.  Birden çok bölgede, işlevleriniz *etkin/etkin* düzende veya *etkin/Pasif* düzende çalışıyor olabilir.  

* Etkin/etkin. Her iki bölge de etkin ve olayları alıyor (yinelenen veya rotalıo). Etkin/etkin, HTTPS işlevleri için Azure ön kapısının birleşimiyle önerilir.
* Etkin/Pasif. Bir bölge etkin ve olayları alıyor, ancak ikincil boşta kalır.  Yük devretme gerekli olduğunda, ikincil bölge etkinleştirilir ve işleme alır.  Bu, Service Bus ve Event Hubs gibi HTTP olmayan işlevler için önerilir.

Çok bölgeli dağıtımlar hakkında daha fazla bilgi için [birden çok bölgede uygulama çalıştırmayı](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) okuyun.

## <a name="activeactive-for-https-functions"></a>HTTPS işlevleri için etkin/etkin

İşlevlerin etkin/etkin dağıtımına ulaşmak için, her iki bölge arasındaki olayları koordine eden bir bileşen gerektirir.  HTTPS işlevleri için bu koordinasyon, [Azure ön kapısı](../frontdoor/front-door-overview.md)kullanılarak gerçekleştirilir.  Azure ön kapısı, birden çok bölgesel işlev arasında HTTPS isteklerini yönlendirebilir ve hepsini bir kez deneme olabilir.  Ayrıca, her uç noktanın sistem durumunu düzenli olarak denetler.  Bölgesel bir işlev sistem durumu denetimlerine yanıt vermezse, Azure ön kapısının dönüşü kalmaz ve yalnızca trafiği sağlıklı işlevlere iletir.  

![Azure ön kapısının ve Işlevin mimarisi](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>HTTPS olmayan işlevler için etkin/etkin

HTTPS olmayan işlevler için hala etkin/etkin dağıtımlar elde edebilirsiniz.  Bununla birlikte, iki bölgenin birbirleriyle nasıl etkileşime gireceğini veya nasıl koordine olacağını dikkate almanız gerekir.  Aynı işlev uygulamasını, her biri aynı Service Bus kuyruğu üzerinde tetiklenirken iki bölgeye dağıttıysanız, bu kuyruğu sıraya alma sırasında rekabet eden tüketiciler olarak hareket ederler.  Bu, her iletinin yalnızca örneklerden biri tarafından işlendiği anlamına geliyor olsa da, tek hizmet veri yolu üzerinde hala tek bir hata noktası olduğu anlamına gelir.  İki hizmet veri yolu kuyruğu (bir birincil bölgede, biri ikincil bölgede olmak üzere) ve kendi bölge kuyruğuna işaret eden iki işlev uygulaması dağıtırsanız, sınama artık sıra iletilerinin iki bölge arasında nasıl dağıtıldığına gelir.  Genellikle bu, her yayımcının her *iki* bölgeye bir ileti yayımlamayı denediği ve her ileti hem etkin işlev uygulamaları tarafından işlendiği anlamına gelir.  Bu bir etkin/etkin model oluştururken, işlem yinelemesi ve verilerin ne zaman ya da nasıl birleştirilecektir konularında daha fazla zorluk oluşturur.  Bu nedenlerden dolayı, HTTPS olmayan tetikleyicilerinin etkin/Pasif model kullanması önerilir.

## <a name="activepassive-for-non-https-functions"></a>HTTPS olmayan işlevler için etkin/Pasif

Etkin/Pasif, her iletiyi yalnızca tek bir işlevin işlemesi için bir yol sağlar, ancak bir olağanüstü durum durumunda ikincil bir bölgeye yük devretmek için bir mekanizma sağlar.  Azure Işlevleri, [coğrafi kurtarma](../service-bus-messaging/service-bus-geo-dr.md) ve [Azure Event Hubs coğrafi kurtarma](../event-hubs/event-hubs-geo-dr.md)Azure Service Bus birlikte çalışır.

Azure Event Hubs tetiklerini örnek olarak kullanarak, etkin/Pasif düzende aşağıdakiler yer alabilir:

* Hem birincil hem de ikincil bölgeye dağıtılan Azure Olay Hub 'ı.
* Birincil ve ikincil Olay Hub 'ını eşleştirmek için coğrafi olağanüstü durum etkindir.  Bu Ayrıca, bağlantı bilgilerini değiştirmeden, Olay Hub 'larına bağlanmak ve birincili ikinciye geçiş yapmak için kullanabileceğiniz bir "diğer ad" oluşturur.
* Hem birincil hem de ikincil bölgeye dağıtılan işlev uygulamaları.
* İşlev uygulamaları, kendi olay hub 'ı için *doğrudan* (diğer ad olmayan) bağlantı dizesinde tetikleniyor. 
* Olay Hub 'ının yayımcıları, diğer ad bağlantı dizesine yayımlamanız gerekir. 

![Aktif-pasif örnek mimarisi](media/functions-geo-dr/active-passive.png)

Yük devretmeden önce, paylaşılan diğer ada gönderen yayımcılar birincil olay hub 'ına yol edecektir.  Birincil işlev uygulaması yalnızca birincil olay hub 'ına dinliyor.  İkincil işlev uygulaması pasif ve boşta olacaktır.  Yük devretme işlemi başlatıldıktan hemen sonra, paylaşılan diğer ada gönderilen yayımcılar artık ikincil Olay Hub 'ına yol açmaz.  İkincil işlev uygulaması artık etkin hale gelecek ve otomatik olarak tetikleniyor.  Bir ikincil bölgeye yönelik etkin yük devretme, yalnızca ilgili olay hub 'ı etkin olduğunda işlevler etkin hale gelmeyle, tamamen Olay Hub 'ından çalıştırılabilir.

[Service Bus](../service-bus-messaging/service-bus-geo-dr.md) ve [Event hub 'ları](../event-hubs/event-hubs-geo-dr.md)ile yük devretme hakkında daha fazla bilgi ve dikkat edilecek noktalar okuyun.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure ön kapısı oluşturma](../frontdoor/quickstart-create-front-door.md)
* [Event Hubs yük devretme konuları](../event-hubs/event-hubs-geo-dr.md#considerations)
