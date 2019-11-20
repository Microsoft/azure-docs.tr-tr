---
title: SQL veri ambarı önerileri
description: SQL veri ambarı önerileri ve bunların nasıl üretildiği hakkında bilgi edinin
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1a93339b99c6591ee9ed615c032d0eac58a372fe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827496"
---
# <a name="sql-data-warehouse-recommendations"></a>SQL veri ambarı önerileri

Bu makalede, Azure Advisor aracılığıyla SQL veri ambarı tarafından sunulan öneriler açıklanmaktadır.  

SQL veri ambarı, veri ambarınızın performans için tutarlı bir şekilde iyileştirildiğinden emin olmak için öneriler sağlar. Veri ambarı önerileri, doğrudan [Azure Portal](https://aka.ms/Azureadvisor)içinde en iyi uygulamaları sunmak Için [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) ile sıkı bir şekilde tümleşiktir. SQL veri ambarı, veri ambarınızın geçerli durumunu analiz eder, bir günlük temposunda etkin iş yükünüz için telemetri ve yüzey önerilerini toplar. Desteklenen veri ambarı öneri senaryoları, önerilen eylemlerin nasıl uygulanacağı ile birlikte aşağıda özetlenmiştir.

SQL veri ambarı Danışmanı hakkında geri bildiriminiz varsa veya herhangi bir sorunla karşılaşırsanız [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com)ulaşın.   

Önerilerinizi bugün denetlemek için [buraya](https://aka.ms/Azureadvisor) tıklayın! Şu anda bu özellik yalnızca Gen2 veri ambarlarında geçerlidir. 

## <a name="data-skew"></a>Veri eğriltme

Veri eğriltme, iş yükünüzü çalıştırırken ek veri hareketine veya kaynak performans sorunlarına neden olabilir. Aşağıdaki belgelerde, veri eğriliğini belirlemek ve en iyi bir dağıtım anahtarı seçerek oluşmasını engellemek için gösteri açıklanır.

- [Eğriliği tanımla ve Kaldır](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Hiç veya güncel olmayan Istatistik

Daha iyi performans istatistikleri, SQL veri ambarı sorgu iyileştiricinin en iyi şekilde sorgu planları oluşturmasına neden olabileceği için sorgu performansını ciddi bir şekilde etkileyebilir. Aşağıdaki belgelerde, istatistikleri oluşturma ve güncelleştirme konusunda en iyi uygulamalar açıklanmaktadır:

- [Tablo istatistikleri oluşturma ve güncelleştirme](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Bu önerilerin etkilenen tablolarının listesini görmek için aşağıdaki [T-SQL betiğini](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)çalıştırın. Danışman, bu önerileri oluşturmak için sürekli olarak aynı T-SQL betiğini çalıştırır.

## <a name="replicate-tables"></a>Tabloları Çoğalt

Çoğaltılan tablo önerileri için, Advisor aşağıdaki fiziksel özelliklere göre tablo adaylarını algılar:

- Çoğaltılan tablo boyutu
- Sütun sayısı
- Tablo dağıtım türü
- Bölüm sayısı

Danışman, tablo erişim sıklığı, ortalama olarak döndürülen satırlar ve veri ambarı boyutunun ve etkinliğinin etrafındaki eşiklerini, yüksek kaliteli önerilerin oluşturulmasını sağlamak için sürekli olarak iş yükü tabanlı buluşsal yöntemler kullanır. 

Aşağıda, her bir çoğaltılan tablo önerisi için Azure portal bulabileceğiniz iş yükü tabanlı buluşsal yöntemler açıklanmaktadır:

- Ortalama tarama-son yedi gün içinde her tablo erişimi için tablodan döndürülen satırların ortalama yüzdesi
- Sık okunan, güncelleştirme yok-tablonun, erişim etkinliğini gösterirken son yedi gün içinde güncelleştirilmemiş olduğunu belirtir
- Okuma/güncelleştirme oranı-tablonun son yedi gün içinde güncelleştirildiği zamana göre ne sıklıkta erişildiğine ilişkin oran
- Etkinlik-kullanımı, erişim etkinliğine göre ölçer. Bu, son yedi gün içinde veri ambarı genelinde ortalama tablo erişim etkinliğine göre tablo erişim etkinliğini karşılaştırır. 

Şu anda Danışman, en yüksek etkinliğin önceliğini belirleme kümelenmiş columnstore dizinleri ile aynı anda en fazla dört çoğaltılan tablo adayını gösterir.

> [!IMPORTANT]
> Çoğaltılan tablo önerisi, tam kanıt değildir ve hesaba veri taşıma işlemlerini almaz. Bunu bir buluşsal yöntem olarak eklemekle çalışıyoruz ancak bu arada, öneriyi uyguladıktan sonra iş yükünüzü her zaman doğrulamanız gerekir. İş yükünüzün çalışmasına neden olan çoğaltılmış tablo önerilerini buldıysanız lütfen sqldwadvisor@service.microsoft.com başvurun. Çoğaltılan tablolar hakkında daha fazla bilgi edinmek için aşağıdaki [belgeleri](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table)ziyaret edin.
