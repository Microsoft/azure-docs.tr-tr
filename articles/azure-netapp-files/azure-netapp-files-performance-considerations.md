---
title: Azure NetApp Files için performans konuları | Microsoft Docs
description: Kota ve aktarım hızı sınırı ve birim kotasını dinamik olarak artırma/azaltma dahil olmak üzere Azure NetApp Files için performans hakkında bilgi edinin.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: b-juche
ms.openlocfilehash: f963c87148c08a4855befc5afb79d9c5ea0f4481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713397"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Azure NetApp Files için performansla ilgili önemli noktalar

Otomatik QoS içeren bir birim için [Aktarım hızı sınırı](azure-netapp-files-service-levels.md) , birime atanan kotanın ve seçilen hizmet düzeyinin bir birleşimiyle belirlenir. El ile QoS olan birimlerde, üretilen iş limiti tek tek tanımlanabilir. Azure NetApp Files hakkında performans planları yaparken, bazı noktaları anlamanız gerekir. 

## <a name="quota-and-throughput"></a>Kota ve verimlilik  

Aktarım hızı sınırı, gerçekleşen gerçek performansın yalnızca bir determinantı.  

Okuma ve yazma karışımı, aktarım boyutu, rastgele veya sıralı desenler ve diğer birçok etken dahil olmak üzere tipik depolama performansı konuları, teslim edilen toplam performansa katkıda bulunur.  

Sınamada gözlemlendiği maksimum empırik üretilen iş hacmi 4.500 MiB/sn 'dir.  Premium depolama katmanında 70,31 TiB 'nin otomatik QoS birimi kotası, bu performans düzeyine ulaşmak için yeterince yüksek bir aktarım hızı sınırı sağlar.  

Otomatik QoS birimleri söz konusu olduğunda, birim kotası miktarlarını 70,31 TiB 'den fazla yapmayı düşünüyorsanız ek verileri depolamak için bir birime ek kota atanabilir. Ancak, eklenen kota, gerçek aktarım hızına göre daha fazla artış elde etmez.  

Aynı empırik üretilen iş trafiği, el ile QoS olan birimlere uygulanır. En fazla üretilen iş hacmi 4.500 MIB/s olabilir.

## <a name="automatic-qos-volume-quota-and-throughput"></a>Otomatik QoS birimi kotası ve verimlilik

Bu bölümde, otomatik QoS türüne sahip birimler için kota yönetimi ve aktarım hızı açıklanmaktadır.

### <a name="overprovisioning-the-volume-quota"></a>Birim kotasının aşırı sağlanması

İş yükünün performansı verimlilik sınırına bağlıysa, daha yüksek bir verimlilik düzeyi ayarlamak ve daha yüksek performans elde etmek için otomatik QoS birimi kotasının sağlanması mümkündür.  

Örneğin, Premium Depolama katmanındaki bir otomatik QoS birimi yalnızca 500 GiB veri içeriyorsa ancak 128 MIB/s işleme gerektiriyorsa, kotayı 2 TiB olarak ayarlayabilirsiniz. bu sayede aktarım hızı düzeyi uygun şekilde ayarlanır 64 (her TB * 2 TiB = 128 MIB/sn).  

Daha yüksek bir verimlilik elde etmek için bir birimi sürekli olarak fazla temin ediyorsanız, el ile QoS birimlerini kullanmayı veya bunun yerine daha yüksek bir hizmet düzeyi kullanmayı düşünün.  Yukarıdaki örnekte, bunun yerine Ultra Storage katmanını kullanarak otomatik QoS birimi kotasının yarısı ile aynı aktarım hızı sınırını elde edebilirsiniz 128 (örneğin, TiB * 1 TiB = 128 MIB/s).

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Birim kotasını dinamik olarak artırma veya azaltma

Performans gereksinimleriniz geçici olarak geçicidir veya sabit bir süre için performans gereksinimlerini artırdıysanız, anında için birim kotasını dinamik olarak artırabilir veya azaltabilirsiniz.  Aşağıdaki noktalara dikkat edin: 

* Birim kotası, GÇ 'yi duraklatmaya gerek kalmadan artırılabilir veya azaltılabilir, ancak birime erişim kesintiye uğramaz veya etkilenmez.  

    Bir birime yönelik etkin bir g/ç işlemi sırasında kotayı ayarlayabilirsiniz.  Birim kotasının birimde depolanan mantıksal veri miktarının hiçbir şekilde azalmadığını unutmayın.

* Birim kotası değiştirildiğinde, karşılık gelen Aktarım hızı sınırı neredeyse anında gerçekleşir. 

    Değişiklik, birim erişimi veya g/ç 'yi kesintiye uğratmaz veya etkilemez.  

* Birim kotasının ayarlanması, kapasite havuzu boyutunda bir değişiklik yapılmasını gerektirebilir.  

    Kapasite havuzu boyutu dinamik olarak ve birim kullanılabilirliği veya g/ç 'yi etkilemeden ayarlanabilir.

## <a name="manual-qos-volume-quota-and-throughput"></a>El ile QoS birimi kotası ve verimlilik 

El ile QoS birimleri kullanırsanız, üretilen iş hacmi her birime bağımsız olarak atanabildiğinden daha yüksek bir verimlilik elde etmek için birim kotasını fazla temin etmeniz gerekmez. Ancak, hala kapasite havuzunun performans gereksinimleriniz için yeterli aktarım hızına sahip olduğundan emin olmanız gerekir. Kapasite havuzunun üretilen işi boyut ve hizmet düzeyine göre sağlanır. Daha fazla bilgi için bkz. [Azure NetApp Files Için hizmet düzeyleri](azure-netapp-files-service-levels.md) .


## <a name="next-steps"></a>Sonraki adımlar

- [Azure NetApp Files Performans Hesaplayıcı](https://cloud.netapp.com/azure-netapp-files/tco?hs_preview=tIKQbfoF-41214739590)
- [Azure NetApp Files için hizmet düzeyleri](azure-netapp-files-service-levels.md)
- [Linux için performans karşılaştırmaları](performance-benchmarks-linux.md)
