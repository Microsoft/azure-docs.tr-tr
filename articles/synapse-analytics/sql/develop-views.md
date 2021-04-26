---
title: SQL havuzlarını kullanan T-SQL görünümleri
description: T-SQL görünümlerini kullanma ve Azure SYNAPSE Analytics 'te özel SQL havuzu ve sunucusuz SQL havuzu ile çözümler geliştirme ipuçları.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 9f52c3fd1284ce7e55680d051c5292361067fad9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673998"
---
# <a name="t-sql-views-with-dedicated-sql-pool-and-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzu ve sunucusuz SQL havuzu içeren T-SQL görünümleri

Bu makalede, T-SQL görünümlerini kullanma ve Azure SYNAPSE Analytics 'te adanmış SQL havuzu ve sunucusuz SQL havuzu ile çözümler geliştirme hakkında ipuçları bulacaksınız.

## <a name="why-use-views"></a>Görünümleri neden kullanılmalıdır?

Görünümler, çözümünüzün kalitesini artırmak için çeşitli şekillerde kullanılabilir.  Bu makalede, çözümünüzü görünümlerle nasıl zenginleştirmenin birkaç örneği vurgulanmıştır ve göz önünde bulundurulması gereken sınırlamalar yer almaktadır.

### <a name="sql-pool---create-view"></a>SQL havuzu-görünüm oluştur

> [!NOTE]
> OLUŞTURMA görünümü sözdizimi Bu makalede ele alınmıyor. Daha fazla bilgi için bkz. [create VIEW](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true) documentation.

## <a name="architectural-abstraction"></a>Mimari soyutlama

Ortak bir uygulama, [Create Table Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) (CTAS) kullanarak tabloları yeniden oluşturmak, bu, verileri yüklerken bir nesne yeniden adlandırma deseninin ardından.

Aşağıdaki örnek bir tarih boyutuna yeni tarih kayıtları ekler. Yeni bir tablo olan DimDate_New önce nasıl oluşturulduğuna ve sonra tablonun orijinal sürümünün yerini alacak şekilde yeniden adlandırdığına göz önünde edin.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;
```

Bu yaklaşımda, tabloların bir kullanıcının görünümünden görüntülenen ve görünmeyen tabloların oluşmasına neden olabileceğini ve "tablo yok" hata iletileri istemlerini aklınızda bulundurun. Görünümler, temel alınan nesneler yeniden adlandırıldığında kullanıcılara tutarlı bir sunum katmanı sağlamak için kullanılabilir.

Görünümler aracılığıyla verilere erişim sağlayarak, kullanıcılar temel alınan tablolara yönelik görünürlüğe gerek kalmaz. Tutarlı bir kullanıcı deneyiminin yanı sıra, bu katman analiz tasarımcılarının veri modelini geliştirebilir olmasını sağlar. Temel tabloları gelişme özelliği, tasarımcılarının veri yükleme işlemi sırasında performansı en üst düzeye çıkarmak için CTAS 'yi kullanabileceği anlamına gelir.

## <a name="performance-optimization"></a>Performansı en iyi duruma getirme

Görünümler, tablolar arasında performans için iyileştirilmiş birleştirmeleri zorlamak için de kullanılabilir. Örneğin, bir görünüm, veri hareketini en aza indirmek için katılım ölçütlerinin bir parçası olarak yedekli bir dağıtım anahtarı içerebilir.

Belirli bir sorguyu veya katılma ipucunu zorlamak, T-SQL görünümlerini kullanmanın başka bir avantajıdır. Bu nedenle, görünümler özelliği birleştirmeleri her zaman en iyi şekilde gerçekleştirilmesini sağlar. Kullanıcıların birleştirmeler için doğru yapıyı hatırlamaları gereksinimini ortadan yacaksınız.

## <a name="limitations"></a>Sınırlamalar

SYNAPSE SQL 'deki görünümler yalnızca meta veri olarak depolanır. Sonuç olarak, aşağıdaki seçenekler kullanılabilir değildir:

* Şema bağlama seçeneği yok
* Temel tablolar görünüm aracılığıyla güncelleştirilemiyor
* Görünümler geçici tablolar üzerinde oluşturulamaz
* GENIŞLETME/NOEXPAND ipuçları için destek yoktur
* SYNAPSE SQL 'de dizinli görünüm yok

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [SYNAPSE SQL geliştirmesine genel bakış](develop-overview.md).



