---
title: Sonuç kümesini önbelleğe ile performans ayarlama
description: Azure SYNAPSE Analytics 'te adanmış SQL havuzu için sonuç kümesi önbelleğe alma özelliğine genel bakış
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: d8c6c8d22c059c63fb4f84c84a02a70de30d4ebe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678534"
---
# <a name="performance-tuning-with-result-set-caching"></a>Sonuç kümesini önbelleğe ile performans ayarlama

Sonuç kümesi önbelleğe alma etkin olduğunda, adanmış SQL havuzu, yinelenen kullanım için Kullanıcı veritabanındaki sorgu sonuçlarını otomatik olarak önbelleğe alır.  Bu, sonraki sorgu yürütmelerinin sonuçları doğrudan kalıcı önbellekten almasına izin verir, böylece yeniden hesaplama gerekmez.   Sonuç kümesi önbelleğe alma, sorgu performansını geliştirir ve işlem kaynağı kullanımını azaltır.  Ayrıca, önbelleğe alınmış sonuçlar kullanan sorgular bir eşzamanlılık yuvası kullanmaz ve bu nedenle mevcut eşzamanlılık sınırlarına göre sayılmaz. Güvenlik için, kullanıcılar önbelleğe alınmış sonuçlara yalnızca, önbellekteki sonuçları oluşturan kullanıcılarla aynı veri erişimi izinleri varsa erişebilir.  

## <a name="key-commands"></a>Anahtar komutları

[Bir kullanıcı veritabanı için sonuç kümesi önbelleğe almayı aç/kapat](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

[Bir oturum için sonuç kümesi önbelleğe almayı aç/kapat](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

[Önbelleğe alınmış sonuç kümesinin boyutunu denetleme](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  

[Önbelleği Temizleme](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="whats-not-cached"></a>Önbelleğe alınmamış olanlar  

Sonuç kümesi önbelleği bir veritabanı için açıldıktan sonra, bu sorgular hariç, önbellek tam olarak tüm sorgular için sonuçlar önbelleğe alınır:

- Temel tabloların verilerinde veya sorgusunda değişiklik yapılmasa bile, belirleyici olmayan yerleşik işlevlere veya çalışma zamanı ifadelerine sahip sorgular. Örneğin, DateTime. Now (), GetDate ().
- Kullanıcı tanımlı işlevleri kullanan sorgular
- Satır düzeyi güvenlik veya sütun düzeyi güvenliği etkin olan tabloları kullanan sorgular
- Satır boyutu 64 KB 'tan büyük verileri döndüren sorgular
- Boyut (>10 GB) büyük verileri döndüren sorgular 
>[!NOTE]
> - Belirleyici olmayan bazı işlevler ve çalışma zamanı ifadeleri aynı verilere karşı yinelenen sorgular için belirleyici olabilir. Örneğin, ROW_NUMBER ().  
> - Sorgu sonuç kümesindeki satırların sırası/sırası Uygulama mantığınız için önemliyse, sorgunuzda SıRALAMA ölçütü kullanın.
> - ORDER BY sütunlarındaki veriler benzersiz değilse, sonuç kümesi önbelleğe alma özelliğinin etkin veya devre dışı olmasına bakılmaksızın ORDER BY sütunlarında aynı değerlere sahip satırlar için garanteed satır düzeni yoktur.

> [!IMPORTANT]
> Sonuç kümesi önbelleği oluşturma ve önbellekten veri alma işlemleri adanmış bir SQL havuzu örneğinin denetim düğümünde gerçekleşir.
> Sonuç kümesi önbelleğe alma açık olduğunda, büyük sonuç kümesi döndüren sorgular (örneğin, >1GB), denetim düğümünde yüksek bir kısma neden olabilir ve örnekteki genel sorgu yanıtını yavaşlatır.  Bu sorgular, veri araştırması veya ETL işlemleri sırasında yaygın olarak kullanılır. Denetim düğümünü stresmek ve performans sorununa yol açmak için, kullanıcılar bu sorgu türlerini çalıştırmadan önce veritabanında sonuç kümesi önbelleğe almayı kapatmalıdır.  

Bu sorguyu, bir sorgu için sonuç kümesi önbelleğe alma işlemleri tarafından geçen süre için çalıştırın:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Sonuç kümesi önbelleğe alma devre dışıyken yürütülen bir sorgu için örnek bir çıktı aşağıda verilmiştir.

![Ekran görüntüsü, konum türü ve komutu dahil sorgu sonuçlarını gösterir.](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Sonuç kümesi önbelleğe alma etkinken yürütülen bir sorgu için örnek bir çıktı aşağıda verilmiştir.

![Ekran görüntüsünde sorgu sonuçları, * from [D W ResultCache D b] dot D b o adlı komutun seçili olduğunu gösterir.](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Önbelleğe alınmış sonuçlar kullanıldığında

Aşağıdaki gereksinimlerin hepsi karşılanıyorsa, önbelleğe alınmış sonuç kümesi bir sorgu için yeniden kullanılır:

- Sorguyu çalıştıran kullanıcının sorguda başvurulan tüm tablolara erişimi vardır.
- Yeni sorgu ile sonuç kümesi önbelleğini oluşturan önceki sorgu arasında tam eşleşme vardır.
- Önbelleğe alınmış sonuç kümesinin oluşturulduğu tablolarda veri veya şema değişikliği yoktur.

Bir sorgunun sonuç önbelleği isabeti veya isabetsizlik ile yürütülüp yürütüldüğünden emin olmak için bu komutu çalıştırın. Result_cache_hit sütunu, önbellek okuması için 1, önbellek isabetsizliği için 0 ve sonuç kümesi önbelleğe almanın neden kullanılmasının nedeni için negatif değerler döndürür. Ayrıntılar için [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) denetleyin.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Önbelleğe alınmış sonuçları yönetme

Sonuç kümesi önbelleğinin en büyük boyutu veritabanı başına 1 TB 'tır.  Önbelleğe alınan sonuçlar, temeldeki sorgu verileri değiştiğinde otomatik olarak geçersiz kılınır.  

Önbellek çıkarma, bu zamanlamayı izleyerek adanmış SQL havuzu tarafından otomatik olarak yönetilir:

- Sonuç kümesi kullanılmıyorsa veya geçersiz kılınmamışsa, her 48 saatte bir.
- Sonuç kümesi önbelleği en büyük boyuta yaklaşırsa.

Kullanıcılar, aşağıdaki seçeneklerden birini kullanarak tüm sonuç kümesi önbelleğini el ile boşaledebilir:

- Veritabanı için sonuç kümesi önbelleği özelliğini kapat
- Veritabanına bağlıyken DBCC DROPRESULTSETCACHE Çalıştır

Bir veritabanını duraklatma, önbelleğe alınmış sonuç kümesini boş olmayacaktır.  

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).
