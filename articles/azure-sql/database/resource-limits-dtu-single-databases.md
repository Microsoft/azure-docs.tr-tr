---
title: DTU kaynağı tek veritabanlarını sınırlar
description: Bu sayfada, Azure SQL veritabanı 'nda tek veritabanları için bazı yaygın DTU kaynak sınırları açıklanmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: references_regions, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/09/2021
ms.openlocfilehash: 3afc2a1e3b279e7c4fd350e96787454e01e01f79
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303228"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>DTU satın alma modelini kullanan tek veritabanlarına yönelik kaynak sınırları-Azure SQL veritabanı
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu makalede, DTU satın alma modelini kullanarak Azure SQL veritabanı tekil veritabanları için ayrıntılı kaynak sınırları sağlanmaktadır.

* Bir sunucudaki tek veritabanları için DTU satın alma model sınırları için bkz. [bir sunucudaki kaynak sınırlarına genel bakış](resource-limits-logical-server.md).
* Azure SQL veritabanı için DTU satın alma modeli kaynak sınırları için bkz. [DTU kaynak limitleri tek veritabanı](resource-limits-dtu-single-databases.md) ve [DTU kaynak sınırları elastik havuzlar](resource-limits-dtu-elastic-pools.md).
* VCore kaynak sınırları için bkz. [Vcore kaynak limitleri-Azure SQL veritabanı](resource-limits-vcore-single-databases.md) ve [sanal çekirdek kaynak limitleri-elastik havuzlar](resource-limits-vcore-elastic-pools.md).
* Farklı satın alma modelleriyle ilgili daha fazla bilgi için bkz. [model ve hizmet katmanları satın alma](purchasing-models.md).

Her salt okunurdur çoğaltma, DTU 'Lar, çalışanlar ve oturumlar gibi kendi kaynaklarına sahiptir. Her salt okuma çoğaltması, bu makalede daha sonra ayrıntılı kaynak sınırlarına tabidir. 


## <a name="single-database-storage-sizes-and-compute-sizes"></a>Tek veritabanı: depolama boyutları ve işlem boyutları

Aşağıdaki tablolarda, her bir hizmet katmanında ve işlem boyutunda tek bir veritabanı için kullanılabilir kaynaklar gösterilmektedir. Kullanarak tek bir veritabanı için hizmet katmanını, işlem boyutunu ve depolama miktarını ayarlayabilirsiniz:

* [Alter database](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database) aracılığıyla [Transact-SQL](single-database-manage.md#transact-sql-t-sql)
* [Azure portalı](single-database-manage.md#the-azure-portal)
* [PowerShell](single-database-manage.md#powershell)
* [Azure CLI](single-database-manage.md#the-azure-cli)
* [REST API](single-database-manage.md#rest-api)

> [!IMPORTANT]
> Ölçeklendirme Kılavuzu ve konuları için bkz. [tek bir veritabanını ölçeklendirme](single-database-scale.md)

### <a name="basic-service-tier"></a>Temel hizmet katmanı

| **İşlem boyutu** | **Temel** |
| :--- | --: |
| Maks. DTU | 5 |
| Dahil edilen depolama alanı (GB) | 2 |
| Maksimum depolama alanı (GB) | 2 |
| Maks. bellek içi OLTP depolama alanı (GB) |Yok |
| Maksimum eş zamanlı çalışan (istek) | 30 |
| Maks. eş zamanlı oturum | 300 |
|||

> [!IMPORTANT]
> Temel hizmet katmanı, birden az sanal çekirdek (CPU) sağlar.  CPU yoğunluklu iş yükleri için S3 veya daha büyük bir hizmet katmanı önerilir.
>
>Veri depolama hakkında temel hizmet katmanı standart sayfa Bloblarına yerleştirilir. Standart sayfa Blobları, sabit disk sürücüsü (HDD) tabanlı depolama medyası kullanır ve performans çeşitliliğine daha az duyarlı olan geliştirme, test ve diğer Seyrek erişilen iş yükleri için idealdir.
>

### <a name="standard-service-tier"></a>Standart hizmet katmanı

| **İşlem boyutu** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maks. DTU | 10 | 20 | 50 | 100 |
| Dahil edilen depolama alanı (GB) <sup>1</sup> | 250 | 250 | 250 | 250 |
| Maksimum depolama alanı (GB) | 250 | 250 | 250 | 1024 |
| Maks. bellek içi OLTP depolama alanı (GB) | Yok | Yok | Yok | Yok |
| Maksimum eş zamanlı çalışan (istek)| 60 | 90 | 120 | 200 |
| Maks. eş zamanlı oturum |600 | 900 | 1200 | 2400 |
||||||

<sup>1</sup> sağlanan ek depolama alanı nedeniyle daha fazla ücret ödediğinden ilgili ayrıntılı bilgi için bkz. [SQL Veritabanı fiyatlandırma seçenekleri](https://azure.microsoft.com/pricing/details/sql-database/single/) .

> [!IMPORTANT]
> Standart S0, S1 ve S2 katmanları, birden az sanal çekirdek (CPU) sağlar.  CPU yoğunluklu iş yükleri için S3 veya daha büyük bir hizmet katmanı önerilir.
>
>Veri depolama ile ilgili Standart S0 ve S1 hizmet katmanları standart sayfa Bloblarına yerleştirilir. Standart sayfa Blobları, sabit disk sürücüsü (HDD) tabanlı depolama medyası kullanır ve performans çeşitliliğine daha az duyarlı olan geliştirme, test ve diğer Seyrek erişilen iş yükleri için idealdir.
>

### <a name="standard-service-tier-continued"></a>Standart hizmet katmanı (devamı)

| **İşlem boyutu** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maks. DTU | 200 | 400 | 800 | 1600 | 3000 |
| Dahil edilen depolama alanı (GB) <sup>1</sup> | 250 | 250 | 250 | 250 | 250 |
| Maksimum depolama alanı (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
| Maks. bellek içi OLTP depolama alanı (GB) | Yok | Yok | Yok | Yok |Yok |
| Maksimum eş zamanlı çalışan (istek)| 400 | 800 | 1600 | 3200 |6000 |
| Maks. eş zamanlı oturum |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

<sup>1</sup> sağlanan ek depolama alanı nedeniyle daha fazla ücret ödediğinden ilgili ayrıntılı bilgi için bkz. [SQL Veritabanı fiyatlandırma seçenekleri](https://azure.microsoft.com/pricing/details/sql-database/single/) .

### <a name="premium-service-tier"></a>Premium hizmet katmanı

| **İşlem boyutu** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maks. DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Dahil edilen depolama alanı (GB) <sup>1</sup> | 500 | 500 | 500 | 500 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| Maksimum depolama alanı (GB) | 1024 | 1024 | 1024 | 1024 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| Maks. bellek içi OLTP depolama alanı (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maksimum eş zamanlı çalışan (istek)| 200 | 400 | 800 | 1600 | 2800 | 6400 |
| Maks. eş zamanlı oturum | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

<sup>1</sup> sağlanan ek depolama alanı nedeniyle daha fazla ücret ödediğinden ilgili ayrıntılı bilgi için bkz. [SQL Veritabanı fiyatlandırma seçenekleri](https://azure.microsoft.com/pricing/details/sql-database/single/) .

<sup>2</sup> 1024 gb 'den 4096 GB 'a kadar, 256 GB 'lik artışlarla 2.

> [!IMPORTANT]
> Premium katmanda 1 TB 'den fazla depolama alanı şu anda tüm bölgelerde kullanılabilir: Çin Doğu, Çin Kuzey, Almanya Orta ve Almanya Kuzeydoğu. Bu bölgelerde, Premium katmanda en fazla depolama alanı 1 TB ile sınırlıdır.  Daha fazla bilgi için bkz. [P11-P15 geçerli sınırlamalar](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).
> [!NOTE]
> `tempdb`Sınırlar için bkz. [tempdb sınırları](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Sonraki adımlar

- Tek bir veritabanı için sanal çekirdek kaynak sınırları için bkz [. Vcore satın alma modelini kullanarak tek veritabanları için kaynak sınırları](resource-limits-vcore-single-databases.md)
- Elastik havuzların sanal çekirdek kaynak sınırları için bkz [. Vcore satın alma modelini kullanarak elastik havuzlar için kaynak sınırları](resource-limits-vcore-elastic-pools.md)
- Elastik havuzların DTU kaynak sınırları için bkz [. DTU satın alma modelini kullanarak elastik havuzlar için kaynak limitleri](resource-limits-dtu-elastic-pools.md)
- Azure SQL yönetilen örneği 'nde yönetilen örnekler için kaynak sınırları için bkz. [SQL yönetilen örnek kaynak sınırları](../managed-instance/resource-limits.md).
- Genel Azure limitleri hakkında daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Mantıksal bir SQL Server üzerindeki kaynak limitleri hakkında bilgi için bkz. sunucu ve abonelik düzeylerindeki sınırlamalar hakkında bilgi için bkz. [MANTıKSAL SQL Server 'da kaynak sınırlarına genel bakış](resource-limits-logical-server.md) .