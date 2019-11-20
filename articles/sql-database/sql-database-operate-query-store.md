---
title: Sorgu deposunu çalıştırma
description: Azure SQL veritabanı 'nda sorgu deposunu nasıl çalıştıracağınızı öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: fa60992c85e69143bfd65cc1a1f420ed85c8fd93
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802764"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Azure SQL veritabanı 'nda sorgu deposunu çalıştırma

Azure 'daki sorgu deposu, her zaman sürekli olarak toplanan ve tüm sorgular hakkında ayrıntılı geçmiş bilgileri sunan tam olarak yönetilen bir veritabanı özelliğidir. Sorgu deposunu, hem bulut hem de şirket içi müşterilere yönelik sorgu performansı sorunlarını önemli ölçüde basitleştiren bir uçak veri kaydedicisine benzer şekilde düşünebilirsiniz. Bu makalede, Azure 'da çalışan sorgu deposunun belirli yönleri açıklanmaktadır. Önceden toplanmış bu sorgu verilerini kullanarak, performans sorunlarını hızla tanılayabilir ve çözümleyebilir ve bu sayede işletmelerinde daha fazla zaman ayırabilirsiniz. 

Sorgu deposu, Kasım 2015 ' den beri Azure SQL veritabanı 'nda [genel olarak kullanılabilir](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) . Sorgu deposu, performans analizi ve [SQL veritabanı danışmanı ve performans panosu](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)gibi ayarlama özelliklerinin temelidir. Bu makalenin yayımlanması sırasında, Query Store, Azure 'da 200.000 ' den fazla kullanıcı veritabanında çalışır ve kesintiye uğramadan birkaç aya ilişkin sorguyla ilgili bilgiler toplar.

> [!IMPORTANT]
> Microsoft, tüm Azure SQL veritabanları (mevcut ve yeni) için Query Store 'u etkinleştirme sürecinizdeki bir işlemdir. 

## <a name="optimal-query-store-configuration"></a>En iyi sorgu deposu yapılandırması

Bu bölümde, sorgu deposunun ve [SQL veritabanı danışmanı ve performans panosu](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)gibi bağımlı özelliklerin güvenilir bir şekilde çalışmasını sağlamak için tasarlanan en iyi yapılandırma Varsayılanları açıklanmaktadır. Varsayılan yapılandırma, sürekli veri toplama için en iyi duruma getirilmiştir. Bu, kapalı/READ_ONLY durumlarında harcanan en az zamandır.

| Yapılandırma | Açıklama | Varsayılan | Açıklama |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Sorgu deposunun müşteri veritabanının içinde götürebileceği veri alanı sınırını belirtir |100 |Yeni veritabanları için zorlandı |
| INTERVAL_LENGTH_MINUTES |Sorgu planları için toplanan çalışma zamanı istatistiklerinin toplandığı ve kalıcı olduğu zaman penceresinin boyutunu tanımlar. Her etkin sorgu planının, bu yapılandırmayla tanımlanan bir süre için en fazla bir satırı vardır |60 |Yeni veritabanları için zorlandı |
| STALE_QUERY_THRESHOLD_DAYS |Kalıcı çalışma zamanı istatistikleri ve etkin olmayan sorguların bekletme süresini denetleyen zamana dayalı temizleme İlkesi |30 |Önceki varsayılan ile yeni veritabanları ve veritabanları için zorlandı (367) |
| SIZE_BASED_CLEANUP_MODE |Sorgu deposu veri boyutu sınıra yaklaşırsa otomatik veri temizleme işleminin yapılıp yapılmayacağını belirtir |Otomatik |Tüm veritabanları için zorlandı |
| QUERY_CAPTURE_MODE |Tüm sorguların veya yalnızca bir sorgu alt kümesinin izlenip izlenmediğini belirtir |Otomatik |Tüm veritabanları için zorlandı |
| FLUSH_INTERVAL_SECONDS |Diske reçeteye göre, yakalanan çalışma zamanı istatistiklerinin bellekte tutulduğu maksimum süreyi belirtir |900 |Yeni veritabanları için zorlandı |
|  | | | |

> [!IMPORTANT]
> Bu varsayılanlar, tüm Azure SQL veritabanlarında sorgu deposu etkinleştirme 'nin son aşamasında otomatik olarak uygulanır (önceki önemli nota bakın). Bu ışık bittikten sonra, Azure SQL veritabanı, birincil iş yükünü veya sorgu deposunun güvenilir işlemlerini olumsuz yönde etkilemedikleri müddetçe, müşteriler tarafından ayarlanan yapılandırma değerlerini değiştirmezler.

Özel ayarlarınız ile kalmak istiyorsanız, yapılandırmayı önceki duruma geri almak için [sorgu deposu seçenekleriyle alter database](https://msdn.microsoft.com/library/bb522682.aspx) kullanın. En iyi yapılandırma parametrelerini nasıl seçeceğinizi öğrenmek için [Query Store Ile En Iyi uygulamaları](https://msdn.microsoft.com/library/mt604821.aspx) inceleyin.

## <a name="next-steps"></a>Sonraki adımlar

[SQL veritabanı performans öngörüleri](sql-database-performance.md)

## <a name="additional-resources"></a>Ek kaynaklar

Daha fazla bilgi için aşağıdaki makalelere göz atın:

- [Veritabanınız için bir uçuş veri kaydedicisi](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [Sorgu deposunu kullanarak performansı izleme](https://msdn.microsoft.com/library/dn817826.aspx)
- [Query Store Kullanım Senaryoları](https://msdn.microsoft.com/library/mt614796.aspx)
