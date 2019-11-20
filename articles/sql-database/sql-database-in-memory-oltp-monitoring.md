---
title: XTP bellek Içi depolamayı izleme
description: XTP bellek Içi depolama kullanımı, kapasiteyi tahmin edin ve izleyin. Kapasite çözme hatası 41823
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: d67a6d151f65252faab40f8f7289fe9e884eff17
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689805"
---
# <a name="monitor-in-memory-oltp-storage"></a>Bellek Içi OLTP depolama alanını izleme

Bellek içi [OLTP](sql-database-in-memory.md)kullanılırken bellek için iyileştirilmiş tablolardaki ve tablo değişkenlerinin verileri bellek içi OLTP depolaması 'nda bulunur. Her Premium ve İş Açısından Kritik hizmet katmanının en fazla bellek Içi OLTP depolama boyutu vardır. Bkz. [DTU tabanlı kaynak limitleri-tek veritabanı](sql-database-dtu-resource-limits-single-databases.md), [DTU tabanlı kaynak limitleri-elastik havuzlar](sql-database-dtu-resource-limits-elastic-pools.md),[sanal çekirdek tabanlı kaynak limitleri-tek veritabanları](sql-database-vcore-resource-limits-single-databases.md) ve [sanal çekirdek tabanlı kaynak limitleri-elastik havuzlar](sql-database-vcore-resource-limits-elastic-pools.md).

Bu sınır aşıldığında INSERT ve Update işlemleri, tek veritabanları için 41823 hatası ve elastik havuzlar için hata 41840 ile başarısız olabilir. Bu noktada, belleği geri kazanmak için verileri silmeniz veya hizmet katmanını ya da veritabanınızın işlem boyutunu yükseltmeniz gerekir.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Verilerin bellek Içi OLTP depolama Cap içinde uygun olup olmadığını belirleme

Farklı hizmet katmanlarının depolama büyük harflerini belirleme. Bkz. [DTU tabanlı kaynak limitleri-tek veritabanı](sql-database-dtu-resource-limits-single-databases.md), [DTU tabanlı kaynak limitleri-elastik havuzlar](sql-database-dtu-resource-limits-elastic-pools.md),[sanal çekirdek tabanlı kaynak limitleri-tek veritabanları](sql-database-vcore-resource-limits-single-databases.md) ve [sanal çekirdek tabanlı kaynak limitleri-elastik havuzlar](sql-database-vcore-resource-limits-elastic-pools.md).

Bellek için iyileştirilmiş bir tablo için bellek gereksinimleri tahmin etmek, Azure SQL veritabanı 'nda yaptığı gibi SQL Server aynı şekilde çalışmaktadır. [MSDN](https://msdn.microsoft.com/library/dn282389.aspx)'deki makaleyi gözden geçirmek birkaç dakika sürer.

Tablo ve tablo değişkeni satırları, ayrıca dizinler, en fazla Kullanıcı veri boyutuna doğru sayılır. Ayrıca, ALTER TABLE 'ın tüm tablo ve dizinlerinin yeni bir sürümünü oluşturmak için yeterli yere ihtiyacı vardır.

## <a name="monitoring-and-alerting"></a>İzleme ve uyarı
Bellek Içi depolama kullanımını, [Azure Portal](https://portal.azure.com/)işlem boyutunuz için depolama ucunun yüzdesi olarak izleyebilirsiniz: 

1. Veritabanı dikey penceresinde kaynak kullanımı kutusunu bulun ve Düzenle ' ye tıklayın.
2. Ölçüm `In-Memory OLTP Storage percentage`seçin.
3. Uyarı eklemek için kaynak kullanımı kutusuna tıklayarak ölçüm dikey penceresini açın ve uyarı Ekle ' ye tıklayın.

Ya da bellek Içi depolama kullanımını göstermek için aşağıdaki sorguyu kullanın:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Bellek dışı OLTP depolama durumlarını düzeltme-hatalar 41823 ve 41840

Veritabanınızdaki bellek Içi OLTP depolama Cap 'e ulaşarak, ekleme, GÜNCELLEŞTIRME, DEĞIŞTIRME ve oluşturma işlemlerinde hata iletisi 41823 (tek veritabanları için) veya 41840 (elastik havuzlar için) hatası ile başarısız olur. Her iki hata da etkin işlemin durmasına neden olur.

41823 ve 41840 hata iletileri, veritabanı veya havuzdaki bellek için iyileştirilmiş tabloların ve tablo değişkenlerinin maksimum bellek Içi OLTP depolama boyutuna ulaştığından emin olduğunu gösterir.

Bu hatayı çözmek için şunlardan birini yapın:

* Bellek için iyileştirilmiş tablolardaki verileri silme, büyük olasılıkla verileri geleneksel, disk tabanlı tablolara boşaltma; veya
* Bellek için iyileştirilmiş tablolarda tutmanız gereken veriler için hizmet katmanını, bellek içi yeterli depolama alanı ile bir birine yükseltin.

> [!NOTE] 
> Nadir durumlarda, 41823 ve 41840 hataları geçici olabilir, yani bellek Içi OLTP depolama alanı üzerinde yeterli kullanılabilir ve işlem başarılı bir şekilde yeniden denenmelidir. Bu nedenle, her iki bellek Içi OLTP depolama alanını izlemenizi ve ilk olarak 41823 veya 41840 hatası ile karşılaşıldığında yeniden denemeyi öneririz. Yeniden deneme mantığı hakkında daha fazla bilgi için bkz. [bellek ıçı OLTP Ile çakışma algılama ve yeniden deneme mantığı](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Sonraki adımlar
İzleme Kılavuzu için bkz. [dinamik yönetim görünümlerini kullanarak Azure SQL Database 'ı izleme](sql-database-monitoring-with-dmvs.md).
