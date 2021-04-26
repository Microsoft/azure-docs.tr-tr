---
title: Adanmış SQL havuzu Azure Advisor önerileri
description: SYNAPSE SQL önerileri ve bunların nasıl üretildiği hakkında bilgi edinin
services: synapse-analytics
author: julieMSFT
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: b418b46199c524ca92d60dece6031073938e159b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568429"
---
# <a name="azure-advisor-recommendations-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzu için Azure Advisor önerileri

Bu makalede, Azure Advisor 'da sunulan adanmış SQL havuzu önerileri açıklanmaktadır.  

Adanmış SQL havuzu veri ambarı iş yükünüzün performans için tutarlı bir şekilde iyileştirildiğinden emin olmak için öneriler sağlar Öneriler, doğrudan [Azure Portal](https://aka.ms/Azureadvisor)içinde en iyi uygulamaları sunmak Için [Azure Danışmanı](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ile sıkı bir şekilde tümleşiktir. Adanmış SQL havuzu, günlük temposunda etkin iş yükünüz için telemetri ve yüzey önerilerini toplar. Desteklenen öneri senaryoları, önerilen eylemlerin nasıl uygulanacağı ile birlikte aşağıda özetlenmiştir.

Önerilerinizi bugün [kontrol](https://aka.ms/Azureadvisor) edebilirsiniz! 

## <a name="data-skew"></a>Veri eğriltme

Veri eğriltme, iş yükünüzü çalıştırırken ek veri hareketine veya kaynak performans sorunlarına neden olabilir. Aşağıdaki belgelerde, veri eğriliğini belirlemek ve en iyi bir dağıtım anahtarı seçerek oluşmasını engellemek için gösteri açıklanır.

- [Eğriliği tanımla ve Kaldır](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Hiç veya güncel olmayan istatistik

En iyi performans istatistikleri, SQL sorgu iyileştiricinin alt sorgu planları oluşturmasına neden olabileceği için sorgu performansını ciddi bir şekilde etkileyebilir. Aşağıdaki belgelerde, istatistikleri oluşturma ve güncelleştirme konusunda en iyi uygulamalar açıklanmaktadır:

- [Tablo istatistikleri oluşturma ve güncelleştirme](sql-data-warehouse-tables-statistics.md)

Bu önerilerin etkilenen tablolarının listesini görmek için aşağıdaki  [T-SQL betiğini](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)çalıştırın. Danışman, bu önerileri oluşturmak için sürekli olarak aynı T-SQL betiğini çalıştırır.

## <a name="replicate-tables"></a>Tabloları Çoğalt

Çoğaltılan tablo önerileri için, Advisor aşağıdaki fiziksel özelliklere göre tablo adaylarını algılar:

- Çoğaltılan tablo boyutu
- Sütun sayısı
- Tablo dağıtım türü
- Bölüm sayısı

Danışman, tablo erişim sıklığı, ortalama olarak döndürülen satırlar ve veri ambarı boyutunun ve etkinliğinin etrafındaki eşiklerini, yüksek kaliteli önerilerin oluşturulmasını sağlamak için sürekli olarak iş yükü tabanlı buluşsal yöntemler kullanır.

Aşağıdaki bölümde, her bir çoğaltılan tablo önerisi için Azure portal bulabileceğiniz iş yükü tabanlı buluşsal yöntemler açıklanmaktadır:

- Ortalama tarama-son yedi gün içinde her tablo erişimi için tablodan döndürülen satırların ortalama yüzdesi
- Sık okunan, güncelleştirme yok-tablonun, erişim etkinliğini gösterirken son yedi gün içinde güncelleştirilmemiş olduğunu belirtir
- Okuma/güncelleştirme oranı-tablonun son yedi gün içinde güncelleştirildiği zamana göre ne sıklıkta erişildiğine ilişkin oran
- Etkinlik-kullanımı, erişim etkinliğine göre ölçer. Bu etkinlik, son yedi gün içinde veri ambarı genelinde ortalama tablo erişim etkinliğine göre tablo erişim etkinliğini karşılaştırır.

Şu anda Danışman, en yüksek etkinliğin önceliğini belirleme kümelenmiş columnstore dizinleri ile aynı anda en fazla dört çoğaltılan tablo adayını gösterir.

> [!IMPORTANT]
> Çoğaltılan tablo önerisi, tam kanıt değildir ve hesaba veri taşıma işlemlerini almaz. Bunu bir buluşsal yöntem olarak eklemekle çalışıyoruz ancak bu arada, öneriyi uyguladıktan sonra iş yükünüzü her zaman doğrulamanız gerekir. Çoğaltılan tablolar hakkında daha fazla bilgi edinmek için aşağıdaki [belgeleri](design-guidance-for-replicated-tables.md#what-is-a-replicated-table)ziyaret edin.


## <a name="adaptive-gen2-cache-utilization"></a>Uyarlamalı (Gen2) önbellek kullanımı
Büyük bir çalışma kümesine sahipseniz, düşük bir önbellek isabet yüzdesi ve yüksek önbellek kullanımı yaşayabilirsiniz. Bu senaryoda, önbellek kapasitesini artırmak ve iş yükünüzü yeniden çalıştırmak için ölçeği ölçeklendirmelisiniz. Daha fazla bilgi için aşağıdaki [belgeleri](./sql-data-warehouse-how-to-monitor-cache.md)ziyaret edin. 

## <a name="tempdb-contention"></a>Tempdb çekişmesi

Yüksek tempdb Çekişmesi olduğunda sorgu performansı düşebilir.  Tempdb çekişmesi, Kullanıcı tanımlı geçici tablolar aracılığıyla veya büyük miktarda veri hareketi olduğunda meydana gelebilir. Bu senaryoda, daha fazla tempdb ayırması için ölçeklendirebilir ve sorgularınızı daha fazla bellek sağlamak üzere [kaynak sınıflarını ve iş yükü yönetimini yapılandırabilirsiniz](./sql-data-warehouse-workload-management.md) . 

## <a name="data-loading-misconfiguration"></a>Veri yükleme yanlış yapılandırma

Gecikmeyi en aza indirmek için, her zaman ayrılmış SQL havuzunuz ile aynı bölgedeki bir depolama hesabındaki verileri yüklemeniz gerekir. [Yüksek aktarım hızı verileri alımı Için kopyalama ifadesini](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) kullanın ve üretilen dosyalarınızı depolama hesabınızda bölerek üretilen iş üretimini en üst düzeye çıkarın. COPY ifadesini kullanamıyoruz, daha iyi aktarım hızı için SqlBulkCopy API veya bcp 'yi yüksek bir toplu iş boyutuyla birlikte kullanabilirsiniz. Ek veri yükleme kılavuzu için aşağıdaki [belgeleri](./guidance-for-loading-data.md)ziyaret edin.