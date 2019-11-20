---
title: DTU kaynağı tek veritabanlarını sınırlar
description: Bu sayfada, Azure SQL veritabanı 'nda tek veritabanları için bazı yaygın DTU kaynak sınırları açıklanmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: 3fb021c06369d1f37dad83b3f9b27e039ddf8b5e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811279"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>DTU satın alma modelini kullanan tek veritabanları için kaynak sınırları

Bu makalede, DTU satın alma modelini kullanarak Azure SQL veritabanı tekil veritabanları için ayrıntılı kaynak sınırları sağlanmaktadır.

Elastik havuzlar için DTU satın alma modeli kaynak sınırları için bkz. [DTU kaynak limitleri-elastik havuzlar](sql-database-dtu-resource-limits-elastic-pools.md). VCore kaynak sınırları için bkz. [Vcore kaynak limitleri-tek veritabanları](sql-database-vcore-resource-limits-single-databases.md) ve [sanal çekirdek kaynak limitleri-elastik havuzlar](sql-database-vcore-resource-limits-elastic-pools.md). Farklı satın alma modelleriyle ilgili daha fazla bilgi için bkz. [model ve hizmet katmanları satın alma](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Tek veritabanı: depolama boyutları ve işlem boyutları

Aşağıdaki tablolarda, her bir hizmet katmanında ve işlem boyutunda tek bir veritabanı için kullanılabilir kaynaklar gösterilmektedir. [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)veya [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases)kullanarak tek bir veritabanı için hizmet katmanını, işlem boyutunu ve depolama miktarını ayarlayabilirsiniz.

> [!IMPORTANT]
> Ölçeklendirme Kılavuzu ve konuları için bkz. [tek bir veritabanını ölçeklendirme](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Temel hizmet katmanı

| **İşlem boyutu** | **Temel** |
| :--- | --: |
| Maks. DTU | 5 |
| Dahil edilen depolama alanı (GB) | 2 |
| En fazla depolama seçimi (GB) | 2 |
| Maks. bellek içi OLTP depolama alanı (GB) |Yok |
| Maksimum eş zamanlı çalışan (istek) | 30 |
| Maks. eş zamanlı oturum | 300 |
|||

### <a name="standard-service-tier"></a>Standart hizmet katmanı

| **İşlem boyutu** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maks. DTU | 10 | 20 | 50 | 100 |
| Dahil edilen depolama alanı (GB) | 250 | 250 | 250 | 250 |
| En fazla depolama seçimi (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maks. bellek içi OLTP depolama alanı (GB) | Yok | Yok | Yok | Yok |
| Maksimum eş zamanlı çalışan (istek)| 60 | 90 | 120 | 200 |
| Maks. eş zamanlı oturum |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standart hizmet katmanı (devamı)

| **İşlem boyutu** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maks. DTU | 200 | 400 | 800 | 1600 | 3000 |
| Dahil edilen depolama alanı (GB) | 250 | 250 | 250 | 250 | 250 |
| En fazla depolama seçimi (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maks. bellek içi OLTP depolama alanı (GB) | Yok | Yok | Yok | Yok |Yok |
| Maksimum eş zamanlı çalışan (istek)| 400 | 800 | 1600 | 3200 |6000 |
| Maks. eş zamanlı oturum |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Premium hizmet katmanı

| **İşlem boyutu** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maks. DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Dahil edilen depolama alanı (GB) | 500 | 500 | 500 | 500 | 4096 * | 4096 * |
| En fazla depolama seçimi (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 * | 4096 * |
| Maks. bellek içi OLTP depolama alanı (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maksimum eş zamanlı çalışan (istek)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maks. eş zamanlı oturum | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

1024 GB 'Den 4096 GB 'a kadar \* GB 'lik 256 artışlarla

> [!IMPORTANT]
> Premium katmanda 1 TB 'den fazla depolama alanı şu anda tüm bölgelerde kullanılabilir: Çin Doğu, Çin Kuzey, Almanya Orta, Almanya Kuzeydoğu, Orta Batı ABD, US DoD bölgeleri ve ABD Devlet Merkezi. Bu bölgelerde Premium katmanda depolama için 1 TB üst sınırı uygulanır.  Daha fazla bilgi için bkz. [P11-P15 geçerli sınırlamalar](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> `tempdb` sınırları için bkz. [tempdb limitleri](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Sonraki adımlar

- Tek bir veritabanı için sanal çekirdek kaynak sınırları için bkz [. Vcore satın alma modelini kullanarak tek veritabanları için kaynak sınırları](sql-database-vcore-resource-limits-single-databases.md)
- Elastik havuzların sanal çekirdek kaynak sınırları için bkz [. Vcore satın alma modelini kullanarak elastik havuzlar için kaynak sınırları](sql-database-vcore-resource-limits-elastic-pools.md)
- Elastik havuzların DTU kaynak sınırları için bkz [. DTU satın alma modelini kullanarak elastik havuzlar için kaynak limitleri](sql-database-dtu-resource-limits-elastic-pools.md)
- Yönetilen örnekler için kaynak sınırları için bkz. [yönetilen örnek kaynak sınırları](sql-database-managed-instance-resource-limits.md).
- Genel Azure limitleri hakkında daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-subscription-service-limits.md).
- Bir veritabanı sunucusundaki kaynak sınırları hakkında daha fazla bilgi için bkz. sunucu ve abonelik düzeylerindeki sınırlamalar hakkında bilgi için bkz. [SQL veritabanı sunucusundaki kaynak sınırlarına genel bakış](sql-database-resource-limits-database-server.md) .
