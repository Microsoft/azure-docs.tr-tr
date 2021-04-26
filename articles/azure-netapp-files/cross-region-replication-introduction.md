---
title: Azure NetApp Files birimlerinin çapraz bölge çoğaltması | Microsoft Docs
description: Azure NetApp Files çapraz bölge çoğaltmasının ne yaptığını, desteklenen bölge çiftlerini, hizmet düzeyi hedeflerini, veri dayanıklılığı ve maliyet modelini açıklar.
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
ms.date: 03/10/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: ac0f9e6e5e1a1988386cc85c2d7576719acbd6e6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590968"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Azure NetApp Files birimlerinin çapraz bölge çoğaltması

Azure NetApp Files çoğaltma işlevselliği, çapraz bölge birimi çoğaltması aracılığıyla veri koruması sağlar. Verileri, bir bölgedeki Azure NetApp Files biriminden (kaynak) başka bir bölgedeki başka bir Azure NetApp Files birimine (hedef) zaman uyumsuz olarak çoğaltabilirsiniz.  Bu özellik, bölge genelinde kesinti veya olağanüstü durum durumunda kritik uygulamanızın yük devretmesini yapmanızı sağlar.

> [!IMPORTANT]
> Bölgeler arası çoğaltma özelliği şu anda genel önizlemededir. [Azure NetApp Files çapraz bölge çoğaltma eklenebileceğinizi gönderimi sayfası](https://aka.ms/anfcrrpreviewsignup)aracılığıyla özelliğe erişmek için bir eklenebileceğinizi isteği göndermeniz gerekir. Bölgeler arası çoğaltma özelliğini kullanmadan önce Azure NetApp Files ekibinden bir resmi onay e-postası bekleyin.

## <a name="supported-cross-region-replication-pairs"></a><a name="supported-region-pairs"></a>Desteklenen çapraz bölge çoğaltma çiftleri

Azure NetApp Files birim çoğaltma, çeşitli [Azure bölgesel çiftleri](../best-practices-availability-paired-regions.md#azure-regional-pairs) ve çiftler dışında desteklenir. Azure NetApp Files birim çoğaltması Şu anda aşağıdaki bölgeler arasında kullanılabilir:  

### <a name="azure-regional-pairs"></a>Azure bölgesel çiftleri

* Doğu ABD ve Batı ABD
* Doğu ABD 2 ve Orta ABD
* Avustralya Doğu ve Avustralya Güneydoğu
* Kanada Orta ve Kanada Doğu
* Güney Hindistan ve Orta Hindistan 
* Almanya Orta Batı ve Almanya Kuzey
* Japonya Doğu ve Japonya Batı
* Kuzey Avrupa ve Batı Avrupa
* UK Güney ve UK Batı

### <a name="azure-regional-non-pairs"></a>Azure bölgesel olmayan çiftler

*   Batı ABD 2 ve Doğu ABD
*   Orta Güney ABD ve Orta ABD
*   Orta Güney ABD ve Doğu ABD
*   Orta Güney ABD ve Doğu ABD 2
*   Doğu ABD ve Doğu ABD 2
*   Doğu ABD 2 ve Batı ABD 2
*   Avustralya Doğu ve Güneydoğu Asya 
*   Almanya Orta Batı ve UK Güney

## <a name="service-level-objectives"></a>Hizmet düzeyi hedefleri

Kurtarma noktası hedefleri (RPO) veya en fazla toleranable veri kaybı, çoğaltma zamanlamasının iki katı olarak tanımlanır.  Gerçek RPO gözlemlendi, toplam veri kümesi boyutu gibi etkenlere bağlı olarak değişiklik hızı, verilerin yüzdesi üzerine yazılır ve aktarım için kullanılabilir çoğaltma bant genişliği gibi etkenlere göre değişiklik gösterebilir.   

* 10 dakikalık çoğaltma zamanlaması için maksimum RPO 20 dakikadır.  
* Saatlik çoğaltma zamanlaması için maksimum RPO iki saattir.  
* Günlük çoğaltma zamanlaması için maksimum RPO iki gündür.  

Kurtarma süresi hedefi (RTO) veya en fazla toleranable iş uygulaması kesinti süresi, uygulamayı getirme ve ikinci sitedeki verilere erişim sağlama faktörlerine göre belirlenir. Hedef birimi etkinleştirmek ve ikinci sitede okuma ve yazma veri erişiminin sağlanması için, RTO 'nın eşleme ilişkisinin depolama bölümü bir dakika içinde tamamlanmalıdır.

## <a name="cost-model-for-cross-region-replication"></a>Bölgeler arası çoğaltma için maliyet modeli  

Azure NetApp Files çapraz bölge çoğaltmasıyla, yalnızca çoğaltılan veri miktarı için ödeme yaparsınız. Kurulum ücretleri veya minimum kullanım ücreti yoktur. Çoğaltma fiyatı, çoğaltma sıklığı ve ilk çoğaltma yapılandırması sırasında seçtiğiniz *hedef* birimin bölgesine göre belirlenir. Daha fazla bilgi için [Azure NetApp Files fiyatlandırma](https://azure.microsoft.com/pricing/details/netapp/) sayfasına bakın.  

Düzenli Azure NetApp Files depolama kapasitesi ücreti, çoğaltma hedefi birimi ( *veri koruma* birimi olarak da bilinir) için geçerlidir. 

### <a name="pricing-examples"></a>Fiyatlandırma örnekleri

Ayda faturalandırılan çapraz bölge çoğaltma miktarı, söz konusu ay içindeki çapraz bölge çoğaltma özelliği aracılığıyla çoğaltılan veri miktarına bağlıdır. Çoğaltılan veri miktarı GiB içinde ölçülür. Kaynak birimlerden hedef birimlere tüm düzenli çoğaltmalar sırasında ve hedef birimlerden kaynak birimlere yapılan tüm yeniden eşitleme çoğaltmaları sırasında iki bölgede çoğaltılan verilerin toplamını temsil eder.

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>Örnek 1: ay 1 temel çoğaltma ve artımlı çoğaltmalar

Aşağıdaki durumları varsayın:

* *Kaynak* biriminiz Azure NetApp Files *Premium* hizmet düzeyinden. Bir ayın ilk gününün başında 1000 GiB 'nin birim kotası ve en fazla 500 GiB boyutunu içerir. Birim *ABD Orta Güney* bölgesidir.
* *Hedef* biriminiz Azure NetApp Files *Standart* hizmet düzeyinden. *ABD Doğu 2* bölgesidir.
* Yukarıdaki iki birim arasında *saatlik* tabanlı bir çapraz bölge çoğaltması yapılandırdınız. Bu nedenle, çoğaltma fiyatı GiB başına $0,12 ' dir.
* Kolaylık olması için, kaynak biriminizde her saat 0,5-GiB veri değişikliği olduğunu varsayalım, ancak tüketilen toplam birim boyutu büyümez (500 GiB 'de kalır). 

İlk kurulumdan sonra, temel çoğaltma hemen gerçekleşir.  

* Temel çoğaltma sırasında çoğaltılan veri miktarı: `500 GiB`
* Temel çoğaltma ücretleri: `500 GiB * $0.12 = $60`

Temel çoğaltmadan sonra, yalnızca değiştirilen bloklar çoğaltılır. Bu nedenle, sonraki artımlı çoğaltmalarda her saat yalnızca 0,5 GiB veri çoğaltılır.

* 30 günlük bir ay için artımlı çoğaltmalar genelinde çoğaltılan veri miktarının toplamı: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* Artımlı çoğaltma ücretleri: `360 GiB * $0.12 = $43.2`

1. ayın sonuna kadar, toplam çapraz bölge çoğaltma ücreti şu şekildedir:  

*  Aydan itibaren toplam çapraz bölge çoğaltma ücreti 1: `$60 + $43.2 = $103.2`

Düzenli Azure NetApp Files depolama kapasitesi ücreti hedef birim için geçerlidir. Ancak, hedef birim, kaynak birim katmanının (ve kimlerden itibaren) farklı bir depolama katmanını kullanabilir.

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>Örnek 2: ay 2 artımlı çoğaltmalar ve yeniden eşitleme çoğaltmaları  

Örnek 1 ' de açıklandığı gibi, bir kaynak biriminiz, hedef biriminiz ve iki ayarlama arasında bir çoğaltma ilişkisi olduğunu varsayalım. İkinci ayın 29 günü (30 günlük bir ay) için saatlik çoğaltmalar beklenen şekilde oluşmuştur.

* 29 gün boyunca artımlı çoğaltmalar genelinde çoğaltılan veri miktarının toplamı: `0.5 GiB * 24 hours * 29 days = 348 GiB`

Ayın son gününde kaynak bölgede planlanmamış bir kesinti meydana geldi ve hedef birime yük devreteceğini varsayalım. 2 saat sonra, kaynak bölge kurtarıldı ve hedef birimden kaynak birime yeniden eşitleme çoğaltması gerçekleştirdiniz. 2 saat boyunca, hedef birimde 0,8 GiB veri değişikliği meydana geldi ve kaynağa yeniden eşitlenmesi gerekiyor.

* Son günde 22 saat boyunca düzenli çoğaltmalar genelinde çoğaltılan veri miktarının toplamı: `0.5 GiB * 22 hours = 11 GiB`
* Bir yeniden eşitleme çoğaltması sırasında çoğaltılan veri miktarı: `0.8 GiB`

Bu nedenle, ay 2 ' nin sonuna kadar, toplam çapraz bölge çoğaltma ücreti aşağıdaki gibidir:  

* 2. aydan toplam çapraz bölge çoğaltma ücreti: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

Aylık 2 için düzenli Azure NetApp Files depolama kapasitesi ücreti hedef birim için geçerlidir.

## <a name="next-steps"></a>Sonraki adımlar
* [Bölgeler arası çoğaltmayı kullanma gereksinimleri ve konuları](cross-region-replication-requirements-considerations.md)
* [Birim çoğaltması oluşturma](cross-region-replication-create-peering.md)
* [Çoğaltma ilişkisinin uygunluk durumunu görüntüleme](cross-region-replication-display-health-status.md)
* [Olağanüstü durum kurtarmayı yönetme](cross-region-replication-manage-disaster-recovery.md)
* [Çapraz bölge çoğaltma hedef birimini yeniden boyutlandırma](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Birim çoğaltma ölçümleri](azure-netapp-files-metrics.md#replication)
* [Birim çoğaltmalarını veya birimleri silme](cross-region-replication-delete.md)
* [Bölgeler arası çoğaltma sorunlarını giderme](troubleshoot-cross-region-replication.md)