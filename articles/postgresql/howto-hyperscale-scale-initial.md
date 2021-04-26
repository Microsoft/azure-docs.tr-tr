---
title: İlk sunucu grubu boyutu-hiper ölçek (Citus)-PostgreSQL için Azure veritabanı
description: Kullanım durumu için doğru başlangıç boyutunu seçin
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 8b87cfc8276d13ccc7e12a4901489ea0b1e770a5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012515"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Hiperscale (Citus) sunucu grubu için başlangıç boyutunu seç

Sunucu grubunun boyutu, hem düğümlerin sayısı hem de donanım kapasitesi [kolayca değiştirilebilir](howto-hyperscale-scale-grow.md)). Ancak yine de yeni bir sunucu grubu için bir başlangıç boyutu seçmeniz gerekir. Makul bir seçenek için bazı ipuçları aşağıda verilmiştir.

## <a name="use-cases"></a>Kullanım örnekleri

Hiper ölçek (Citus), aşağıdaki yollarla sık kullanılır.

### <a name="multi-tenant-saas"></a>Çok kiracılı SaaS

Mevcut bir tek düğümlü PostgreSQL veritabanı örneğinden hiper ölçeğe geçiş yaparken, toplam çalışan sanal çekirdekleri ve RAM sayısının orijinal örneğe eşit olduğu bir küme seçin. Bu tür senaryolarda, parça kaynak kullanımını iyileştirtiğinden ve daha küçük bir dizin kullanılmasına izin verirken 2 3x performans iyileştirmeleri gördük.

Sanal çekirdek sayısı aslında tek karardır. RAM ayırma şu anda, [hiper ölçek (Citus) yapılandırma seçenekleri](concepts-hyperscale-configuration-options.md) sayfasında açıklandığı gibi, vCore sayısına göre belirlenir.
Düzenleyici düğümü, çalışan olarak çok RAM gerektirmez, ancak RAM ve sanal çekirdekleri bağımsız olarak seçme yolu yoktur.

### <a name="real-time-analytics"></a>Gerçek zamanlı analiz

Toplam Vçekirdekler: RAM 'e uygun veri miktarı kullanıldığında, hiper ölçekte (Citus) bir doğrusal performans geliştirmesini, çalışan çekirdekleri sayısıyla orantılı olarak bekleyebilir. Gereksinimlerinize göre doğru sanal çekirdek sayısını öğrenmek için, tek düğümlü veritabanınızdaki sorgular için geçerli gecikme süresini ve hiper ölçekte gereken gecikme süresini göz önünde bulundurun (Citus). Geçerli gecikme süresini istenen gecikme süresine bölün ve sonucu yuvarlayın.

Çalışan RAM: en iyi durum, çalışma kümesinin belleğe sığabileceği yeterli bellek sağlamaktır. Uygulamanızın kullandığı sorgu türleri bellek gereksinimlerini etkiler. Sorgu üzerinde analizine ilişkin AÇıKLA ' yı çalıştırarak, gereken bellek miktarını belirleyebilirsiniz. Sanal çekirdekler ve RAM 'in, [Hyperscale (Citus) yapılandırma seçenekleri](concepts-hyperscale-configuration-options.md) makalesinde açıklandığı şekilde birlikte ölçeklendirilmesini unutmayın.

## <a name="choosing-a-hyperscale-citus-tier"></a>Hyperscale (Citus) katmanı seçme

> [!IMPORTANT]
> Hiperscale (Citus) temel katmanı Şu anda önizlemededir.  Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
>
> Diğer yeni özelliklerin bir listesini, [hiper ölçek için Önizleme özellikleri (Citus)](hyperscale-preview-features.md)sayfasına bakabilirsiniz.

Yukarıdaki bölümler, her bir kullanım durumu için kaç sanal çekirdeğin ve ne kadar RAM gerektiği hakkında fikir verir. Bu talepleri iki hiper ölçek (Citus) katmanları arasında bir seçim aracılığıyla karşılayabilirsiniz: temel katman ve Standart katman.

Temel katman, işleme gerçekleştirmek için tek bir veritabanı düğümü kullanır, ancak standart katman daha fazla düğüme izin verir. Katmanlar başka bir şekilde benzerdir ve aynı özellikleri sunar. Bazı durumlarda, tek bir düğümün sanal çekirdekleri ve disk alanı yeterli bir şekilde ölçeklendirilebileceğinden, diğer durumlarda ise birden çok düğüm için birlikte işlem yapılması gerekir.

Katmanların karşılaştırması için bkz. [temel katman](concepts-hyperscale-tiers.md) kavramları sayfası.

## <a name="next-steps"></a>Sonraki adımlar

- [Sunucu grubu ölçeklendirme](howto-hyperscale-scale-grow.md)
- Sunucu grubu [performans seçenekleri](concepts-hyperscale-configuration-options.md)hakkında daha fazla bilgi edinin.
