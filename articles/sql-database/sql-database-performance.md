---
title: Performansı izleme ve iyileştirme
description: Azure SQL veritabanı, geçerli sorgu performansını iyileştirebilecek olan bölgeleri belirlemenize yardımcı olacak performans araçları sağlar.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 11/14/2019
ms.openlocfilehash: 474c2f4f00374ce785b81fe048e11cb353b3078a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151243"
---
# <a name="monitor-and-improve-performance"></a>Performansı izleme ve iyileştirme

Azure SQL veritabanı, veritabanınızdaki olası sorunları tanımlar ve akıllı ayarlama eylemleri ve önerileri sağlayarak iş yükünüzün performansını iyileştirebilecek eylemler önerir.

## <a name="performance-tuning-options"></a>Performans ayarlama seçenekleri

Performans ayarlama seçenekleri kullanılabilir Azure SQL veritabanı, "akıllı performans" altındaki veritabanı gezinti menüsünde bulunabilir:

| Performans ayarlama seçeneği | Tek veritabanı ve havuza alınmış veritabanı desteği | Örnek veritabanı desteği |
| :----------------------------- | ----- | ----- |
| **Performansa genel bakış** -veritabanınız için tüm performans etkinliklerini izleyin. | Yes | Hayır | 
| **Performans önerileri** -iş yükünüzün performansını iyileştirebilecek performans önerilerini gösterir. | Yes | Hayır | 
| **Sorgu performansı içgörüleri** -veritabanında en çok kullanılan sorguların performansını gösterir. | Yes | Hayır | 
| **Otomatik ayarlama** -veritabanı performansınızı otomatik olarak Iyileştirmek IÇIN Azure SQL veritabanı 'nı kullanın. | Yes | Hayır | 

## <a name="performance-overview"></a>Performansa genel bakış

Bu görünüm, veritabanı Performanslarınızın bir özetini sağlar ve performans ayarlama ve sorun giderme konusunda size yardımcı olur. 

![Azure SQL veritabanı için performansa genel bakış](./media/sql-database-performance/performance-overview-annotated.png)

* **Öneriler** kutucuğu, veritabanınız için ayarlama önerilerinin dökümünü sağlar (daha fazla varsa ilk üç öneri gösterilir). Bu Kutucuğa tıkladığınızda **[performans önerilerine](#performance-recommendations)** gidersiniz. 
* **Ayarlama etkinliği** kutucuğu, veritabanınız için devam eden ve tamamlanmış ayarlama eylemlerinin bir özetini sağlar ve bu sayede ayarlama etkinliğinin geçmişine hızlı bir görünüm sağlar. Bu Kutucuğa tıkladığınızda veritabanınız için tam ayarlama geçmişi görünümüne gidersiniz.
* **Otomatik ayarlama** kutucuğu veritabanınız için [otomatik ayarlama yapılandırmasını](sql-database-automatic-tuning-enable.md) gösterir (veritabanınıza otomatik olarak uygulanan ayarlama seçenekleri). Bu Kutucuğa tıkladığınızda Otomasyon yapılandırma iletişim kutusu açılır.
* **Veritabanı sorguları** kutucuğu, veritabanınızın sorgu performansının özetini gösterir (genel DTU kullanımı ve en üstteki kaynak kullanan sorgular). Bu Kutucuğa tıkladığınızda **[sorgu performansı içgörüleri](#query-performance-insight)** için gidersiniz.

## <a name="performance-recommendations"></a>Performans önerileri

Bu sayfa, veritabanınızın performansını iyileştirebilecek akıllı [ayarlama önerileri](sql-database-advisor.md) sağlar. Bu sayfada aşağıdaki tür öneriler gösterilmektedir:

* Oluşturulacak veya bırakılacak dizinlere ilişkin öneriler.
* Veritabanında şema sorunlarının belirlenme önerileri.
* Sorguların parametreli sorgulardan faydalanabileceği öneriler.

![Azure SQL veritabanı için performans önerileri](./media/sql-database-performance/performance-recommendations-annotated.png)

Geçmişte uygulanan ayarlama eylemlerinin tamamlanma geçmişini de bulabilirsiniz.

[Performans önerilerini bul ve Uygula](sql-database-advisor-portal.md) makalesindeki performans önerilerini nasıl bulacağınızı öğrenin.

## <a name="query-performance-insight"></a>Sorgu Performansı İçgörüleri

[Sorgu performansı içgörüleri](sql-database-query-performance.md) , aşağıdakileri sağlayarak veritabanı performansını sorun gidermeye daha az zaman harcamanızı sağlar:

* Veritabanları kaynağı (DTU) tüketiminiz hakkında daha derin Öngörüler. 
* Daha iyi performans için ayarlanabilir olabilecek, en üstteki CPU kullanan sorgular. 
* Bir sorgunun ayrıntılarına gitme olanağı. 

  ![Azure SQL veritabanı için sorgu Performans öngörüleri](./media/sql-database-performance/query-performance-insights-annotated.png)

Bu sayfa hakkında daha fazla bilgi edinmek **[için sorgu performansı içgörüleri kullanma makalesine gidin](sql-database-query-performance.md)** .

## <a name="automatic-tuning"></a>Otomatik ayarlama

Azure SQL veritabanları, [performans önerileri](sql-database-advisor.md)uygulayarak veritabanı performansını otomatik olarak ayarlayabilir. Etkinleştirmek için [otomatik ayarlamayı etkinleştirme](sql-database-automatic-tuning-enable.md)konusunu okuyun.

  ![Azure SQL veritabanı için otomatik ayarlama](./media/sql-database-performance/automatic-tuning-annotated.png)

Daha fazla bilgi edinmek için [otomatik ayarlama makalesini](sql-database-automatic-tuning.md)okuyun.

## <a name="additional-resources"></a>Ek kaynaklar

* [Tek veritabanları için Azure SQL veritabanı performans Kılavuzu](sql-database-performance-guidance.md)
* [Elastik havuz ne zaman kullanılmalıdır?](sql-database-elastic-pool-guidance.md)
