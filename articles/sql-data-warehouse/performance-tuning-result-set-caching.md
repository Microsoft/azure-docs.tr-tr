---
title: Sonuç kümesi önbelleğe alma performans ayarı
description: Azure SQL veri ambarı için sonuç kümesi önbelleğe alma özelliğine genel bakış
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: seo-lt-2019
ms.openlocfilehash: 461320b9c3ed48176fb60fe695704c582edcd552
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692943"
---
# <a name="performance-tuning-with-result-set-caching"></a>Sonuç kümesi önbelleğe alma performans ayarı  
Sonuç kümesi önbelleği etkinleştirildiğinde, Azure SQL veri ambarı yinelenen kullanım için Kullanıcı veritabanındaki sorgu sonuçlarını otomatik olarak önbelleğe alır.  Bu, sonraki sorgu yürütmelerinin sonuçları doğrudan kalıcı önbellekten almasına izin verir, böylece yeniden hesaplama gerekmez.   Sonuç kümesi önbelleğe alma, sorgu performansını geliştirir ve işlem kaynağı kullanımını azaltır.  Ayrıca, önbelleğe alınmış sonuçlar kullanan sorgular bir eşzamanlılık yuvası kullanmaz ve bu nedenle mevcut eşzamanlılık sınırlarına göre sayılmaz. Güvenlik için, kullanıcılar önbelleğe alınmış sonuçlara yalnızca, önbellekteki sonuçları oluşturan kullanıcılarla aynı veri erişimi izinleri varsa erişebilir.  

## <a name="key-commands"></a>Anahtar komutları
[Bir kullanıcı veritabanı için sonuç kümesi önbelleğe almayı aç/kapat](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Bir oturum için sonuç kümesi önbelleğe almayı aç/kapat](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Önbelleğe alınmış sonuç kümesinin boyutunu denetleme](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Önbelleği Temizleme](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Önbelleğe alınmamış olanlar  

Sonuç kümesi önbelleği bir veritabanı için açıldıktan sonra, bu sorgular hariç, önbellek tam olarak tüm sorgular için sonuçlar önbelleğe alınır:
- DateTime. Now () gibi belirleyici olmayan işlevler kullanan sorgular
- Kullanıcı tanımlı işlevleri kullanan sorgular
- Satır düzeyi güvenlik veya sütun düzeyi güvenliği etkin olan tabloları kullanan sorgular
- Satır boyutu 64 KB 'tan büyük verileri döndüren sorgular

> [!IMPORTANT]
> Sonuç kümesi önbelleği oluşturma ve önbellekten veri alma işlemleri, bir veri ambarı örneğinin denetim düğümünde gerçekleşir. Sonuç kümesi önbelleğe alma açık olduğunda, büyük sonuç kümesi döndüren sorgular (örneğin, > 1 milyon satır), denetim düğümünde yüksek CPU kullanımına neden olabilir ve örnekteki genel sorgu yanıtını yavaşlatır.  Bu sorgular, veri araştırması veya ETL işlemleri sırasında yaygın olarak kullanılır. Denetim düğümünü stresmek ve performans sorununa yol açmak için, kullanıcılar bu sorgu türlerini çalıştırmadan önce veritabanında sonuç kümesi önbelleğe almayı kapatmalıdır.  

Bu sorguyu, bir sorgu için sonuç kümesi önbelleğe alma işlemleri tarafından geçen süre için çalıştırın:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

Sonuç kümesi önbelleğe alma devre dışıyken yürütülen bir sorgu için örnek bir çıktı aşağıda verilmiştir.

![Sorgu--RSC-devre dışı](media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Sonuç kümesi önbelleğe alma etkinken yürütülen bir sorgu için örnek bir çıktı aşağıda verilmiştir.

![Sorgu--RSC-etkin](media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Önbelleğe alınmış sonuçlar kullanıldığında

Aşağıdaki gereksinimlerin hepsi karşılanıyorsa, önbelleğe alınmış sonuç kümesi bir sorgu için yeniden kullanılır:
- Sorguyu çalıştıran kullanıcının sorguda başvurulan tüm tablolara erişimi vardır.
- Yeni sorgu ile sonuç kümesi önbelleğini oluşturan önceki sorgu arasında tam eşleşme vardır.
- Önbelleğe alınmış sonuç kümesinin oluşturulduğu tablolarda veri veya şema değişikliği yoktur.

Bir sorgunun sonuç önbelleği isabeti veya isabetsizlik ile yürütülüp yürütüldüğünden emin olmak için bu komutu çalıştırın. Önbellek okuması varsa, result_cache_hit 1 döndürür.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Önbelleğe alınmış sonuçları yönetme 

Sonuç kümesi önbelleğinin en büyük boyutu veritabanı başına 1 TB 'tır.  Önbelleğe alınan sonuçlar, temeldeki sorgu verileri değiştiğinde otomatik olarak geçersiz kılınır.  

Önbellek çıkarma işlemi, Azure SQL veri ambarı tarafından bu zamanlamayı izleyerek otomatik olarak yönetilir: 
- Sonuç kümesi kullanılmıyorsa veya geçersiz kılınmamışsa, her 48 saatte bir. 
- Sonuç kümesi önbelleği en büyük boyuta yaklaşırsa.

Kullanıcılar, aşağıdaki seçeneklerden birini kullanarak tüm sonuç kümesi önbelleğini el ile boşaledebilir: 
- Veritabanı için sonuç kümesi önbelleği özelliğini kapat 
- Veritabanına bağlıyken DBCC DROPRESULTSETCACHE Çalıştır

Bir veritabanını duraklatma, önbelleğe alınmış sonuç kümesini boş olmayacaktır.  

## <a name="next-steps"></a>Sonraki adımlar
Geliştirme ile ilgili daha fazla ipucu için bkz. [SQL Data Warehouse geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md). 
