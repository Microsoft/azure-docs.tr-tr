---
title: T-SQL görünümlerini kullanma
description: Azure SQL veri ambarı 'nda çözüm geliştirmeye yönelik T-SQL görünümlerini kullanma ipuçları.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 14ff990fa21f4af3c77b4dba10b4fea758ff32b6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685764"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda görünümler
Görünümler, çözümünüzün kalitesini artırmak için çeşitli şekillerde kullanılabilir. 

Azure SQL veri ambarı, standart ve gerçekleştirilmiş görünümleri destekler. Her ikisi de SELECT ifadelerle oluşturulmuş ve sorguları Mantıksal tablolar olarak sunulan sanal tablolardır. Görünümler ortak veri hesaplamasının karmaşıklığını yalıtır ve değişiklikleri hesaplama için bir soyutlama katmanı ekler. böylece sorguları yeniden yazmaya gerek kalmaz.

## <a name="standard-view"></a>Standart Görünüm
Standart Görünüm, görünümün her seferinde verilerini hesaplar. Diskte depolanan veri yok. İnsanlar genellikle standart görünümleri, bir veritabanındaki mantıksal nesneleri ve sorguları düzenlemeye yardımcı olan bir araç olarak kullanır. Standart bir görünüm kullanmak için bir sorgunun kendisine doğrudan başvuru yapması gerekir. Daha fazla bilgi için bkz. [create VIEW](/sql/t-sql/statements/create-view-transact-sql) documentation.

SQL veri ambarı 'ndaki görünümler yalnızca meta veri olarak depolanır. Sonuç olarak, aşağıdaki seçenekler kullanılamaz:
* Şema bağlama seçeneği yok
* Temel tablolar görünüm aracılığıyla güncelleştirilemez
* Görünümler geçici tablolar üzerinde oluşturulamaz
* GENIŞLETME/NOEXPAND ipuçları desteği yok
* SQL Data Warehouse 'da dizinli görünüm yok

Standart Görünümler, tablolar arasında performans için iyileştirilmiş birleştirmeleri zorlamak için kullanılabilir. Örneğin, bir görünüm, veri hareketini en aza indirmek için katılım ölçütlerinin bir parçası olarak yedekli bir dağıtım anahtarı içerebilir. Bir görünümün başka bir avantajı, belirli bir sorguyu veya katılma ipucunu zorlamak olabilir. Bu şekilde görünümlerin kullanılması, birleşimlerin her zaman en iyi şekilde gerçekleştirilmesini sağlar ve kullanıcıların birleştirmeler için doğru yapıyı hatırlamaları gereksinimini ortadan önler.

## <a name="materialized-view"></a>Gerçekleştirilmiş görünüm
Gerçekleştirilmiş bir görünüm, verileri Azure SQL veri ambarı 'nda tıpkı bir tablo gibi önceden hesaplar, depolar ve korur. Gerçekleştirilmiş bir görünüm kullanıldığında her seferinde yeniden hesaplama gerekmez. Veriler temel tablolara yüklendiği için, Azure SQL veri ambarı gerçekleştirilmiş görünümleri zaman uyumlu olarak yeniler.  Sorgu iyileştirici, görünümler sorguda başvurulmamış olsa bile sorgu performansını artırmak için dağıtılmış gerçekleştirilmiş görünümleri otomatik olarak kullanır.  Gerçekleştirilmiş görünümlerin en büyük yararlanırken sorguları, küçük sonuç kümesi üreten büyük tablolarda karmaşık sorgulardır (genellikle birleşimler ve toplamaları olan sorgular).  

Gerçekleştirilmiş görünüm sözdizimi ve diğer gereksinimlere ilişkin ayrıntılar için bkz. [Select olarak GERÇEKLEŞTIRILMIŞ görünüm oluşturma](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  

Sorgu ayarlama Kılavuzu için, [gerçekleştirilmiş görünümlerle performans ayarlamayı](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)kontrol edin. 

## <a name="example"></a>Örnek
Ortak bir uygulama deseninin ardından CREATE TABLE SELECT (CTAS) kullanarak tabloları yeniden oluşturmak ve ardından verileri yüklerken bir nesne yeniden adlandırma deseninin oluşturulması.  Aşağıdaki örnek bir tarih boyutuna yeni tarih kayıtları ekler. Yeni bir tablo olan DimDate_New, ilk olarak nasıl oluşturulduğunu ve tablonun orijinal sürümünün yerini alacak şekilde yeniden adlandırdığını aklınızda edin.

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
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```
Bununla birlikte, bu yaklaşım, tabloların bir kullanıcının görünümünden görünmesine ve görünmesine neden olabilir ve "tablo yok" hata iletileri de görünür. Görünümler, kullanıcılara, temel alınan nesnelerin yeniden adlandırıldığını tutarlı bir sunum katmanı sağlamak için kullanılabilir. Görünümler aracılığıyla verilere erişim sağlayarak, kullanıcılar temeldeki tablolara yönelik görünürlüğe gerek kalmaz. Bu katman, veri ambarı tasarımcılarının veri modelini gelişebilmesini sağlarken tutarlı bir kullanıcı deneyimi sağlar. Temel tabloları geliştirmekte olan tasarımcılar, tasarımcı 'nın veri yükleme işlemi sırasında performansı en üst düzeye çıkarmak için CTAS 'yi kullanabileceği anlamına gelir.   

## <a name="next-steps"></a>Sonraki adımlar
Geliştirme ile ilgili daha fazla ipucu için bkz. [SQL Data Warehouse geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).


