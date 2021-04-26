---
title: DTU kaynağı Esnek havuzları sınırlar
description: Bu sayfada Azure SQL veritabanı 'nda esnek havuzlar için bazı yaygın DTU kaynak sınırları açıklanmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019 sqldbrb=1 references_regions
ms.devlang: ''
ms.topic: reference
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: 906d34a91736d28f0da4a5ba34b7114fbe410adb
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306645"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>DTU satın alma modelini kullanarak elastik havuzlar için kaynak limitleri
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu makalede, DTU satın alma modelini kullanan bir elastik havuzda Azure SQL veritabanı 'nda bulunan veritabanları için ayrıntılı kaynak sınırları sağlanmaktadır.

* Bir sunucudaki tek veritabanları için DTU satın alma model sınırları için bkz. [bir sunucudaki kaynak sınırlarına genel bakış](resource-limits-logical-server.md).
* Azure SQL veritabanı için DTU satın alma modeli kaynak sınırları için bkz. [DTU kaynak limitleri tek veritabanı](resource-limits-dtu-single-databases.md) ve [DTU kaynak sınırları elastik havuzlar](resource-limits-dtu-elastic-pools.md).
* VCore kaynak sınırları için bkz. [Vcore kaynak limitleri-Azure SQL veritabanı](resource-limits-vcore-single-databases.md) ve [sanal çekirdek kaynak limitleri-elastik havuzlar](resource-limits-vcore-elastic-pools.md).
* Farklı satın alma modelleriyle ilgili daha fazla bilgi için bkz. [model ve hizmet katmanları satın alma](purchasing-models.md).

Her salt okunurdur çoğaltma, DTU 'Lar, çalışanlar ve oturumlar gibi kendi kaynaklarına sahiptir. Her salt okuma çoğaltması, bu makalede daha sonra ayrıntılı kaynak sınırlarına tabidir.

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Elastik havuz: depolama boyutları ve işlem boyutları

Azure SQL veritabanı elastik havuzlar için aşağıdaki tablolarda, her bir hizmet katmanında ve işlem boyutunda kullanılabilen kaynaklar gösterilmektedir. Kullanarak hizmet katmanını, işlem boyutunu ve depolama miktarını ayarlayabilirsiniz:

* [Alter database](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database) aracılığıyla [Transact-SQL](elastic-pool-scale.md)
* [Azure portalı](elastic-pool-manage.md#azure-portal)
* [PowerShell](elastic-pool-manage.md#powershell)
* [Azure CLI](elastic-pool-manage.md#azure-cli)
* [REST API](elastic-pool-manage.md#rest-api)


> [!IMPORTANT]
> Ölçeklendirme Kılavuzu ve değerlendirmeleri için bkz. [elastik havuz ölçekleme](elastic-pool-scale.md)

Elastik havuzlardaki ayrı veritabanlarının kaynak sınırları, DTU 'lar ve hizmet katmanını temel alan havuzlar dışındaki tek veritabanları için genellikle aynıdır. Örneğin, bir S2 veritabanının en fazla eşzamanlı çalışanları 120 çalışandır. Bu nedenle, standart havuzdaki bir veritabanı için en fazla eş zamanlı çalışan, havuzdaki veritabanı başına maksimum DTU değeri 50 DTU ise (S2 ile eşdeğerdir) 120 çalışanlardır.
 
Aynı sayıda DTU için, elastik bir havuza sunulan kaynaklar, elastik havuzun dışındaki tek bir veritabanına sunulan kaynakları aşabilir. Bu, bir elastik havuzun eDTU kullanımının, iş yükü desenlerine bağlı olarak havuz içindeki veritabanları genelinde DTU kullanımının özetinden daha az olması mümkün olduğu anlamına gelir. Örneğin, veritabanı DTU kullanımının %100 olduğu elastik bir havuzda yalnızca bir veritabanı olan büyük bir durumda, belirli iş yükü desenleri için havuz eDTU kullanımının %50 olması mümkündür. Veritabanı başına en fazla DTU, belirtilen havuz boyutu için desteklenen en yüksek değerde kalırsa bile bu durum oluşabilir.

> [!NOTE]
> Aşağıdaki tabloların her birinde havuz başına depolama kaynağı sınırı tempdb ve günlük depolama alanı içermez.

### <a name="basic-elastic-pool-limits"></a>Temel esnek havuz sınırları

| Havuz başına eDTU | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Havuz başına dahil edilen depolama alanı (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Havuz başına maks. depolama alanı (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Havuz başına en fazla In-Memory OLTP depolaması (GB) | Yok | Yok | Yok | Yok | Yok | Yok | Yok | Yok |
| Havuz başına en fazla veritabanı sayısı <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Her havuz <sup>2</sup> için maksimum eş zamanlı çalışan (istek) | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Her havuz için en fazla eşzamanlı oturum <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Veritabanı seçenekleri başına en az DTU | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Veritabanı seçenekleri başına en fazla DTU | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Veritabanı başına maks. depolama alanı (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> daha fazla dikkat edilmesi için [yoğun esnek havuzlarda kaynak yönetimine](elastic-pool-resource-management.md) bakın.

<sup>2</sup> tek bir veritabanı için maksimum eşzamanlı çalışan (istek) için bkz. [tek veritabanı kaynak limitleri](resource-limits-vcore-single-databases.md). Örneğin, elastik havuz 5. nesil kullanıyorsa ve veritabanı başına en fazla sanal çekirdek 2 ' ye ayarlanırsa, en fazla eşzamanlı çalışan değeri 200 olur.  Veritabanı başına en fazla sanal çekirdek 0,5 olarak ayarlanırsa, 5. nesil ' den itibaren maksimum eşzamanlı çalışan 50 değeri, sanal çekirdek başına en 100 fazla eşzamanlı çalışan olur. Daha az 1 sanal çekirdek veya daha az 1 olan veritabanı başına diğer en fazla sanal çekirdek ayarları için, en fazla eş zamanlı çalışan sayısı benzer şekilde ölçeklendirildi.

### <a name="standard-elastic-pool-limits"></a>Standart esnek havuz sınırları

| Havuz başına eDTU | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Havuz başına dahil edilen depolama alanı (GB) <sup>1</sup> | 50 | 100 | 200 | 300 | 400 | 800 |
| Havuz başına maks. depolama alanı (GB) | 500 | 750 | 1024 | 1280 | 1536 | 2048 |
| Havuz başına en fazla In-Memory OLTP depolaması (GB) | Yok | Yok | Yok | Yok | Yok | Yok |
| Havuz başına en fazla veritabanı sayısı <sup>2</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Havuz başına maksimum eş zamanlı çalışan (istek) <sup>3</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Havuz başına en fazla eşzamanlı oturum <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Veritabanı seçenekleri başına en az DTU | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Veritabanı seçenekleri başına en fazla DTU | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Veritabanı başına maks. depolama alanı (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> sağlanan ek depolama alanı nedeniyle daha fazla ücret ödediğinden ilgili ayrıntılı bilgi için bkz. [SQL Veritabanı fiyatlandırma seçenekleri](https://azure.microsoft.com/pricing/details/sql-database/elastic/) .

<sup>2</sup> daha fazla dikkat edilmesi için [yoğun elastik havuzlardaki kaynak yönetimine](elastic-pool-resource-management.md) bakın.

<sup>3</sup> tek bir veritabanı için maksimum eşzamanlı çalışan (istek) için bkz. [tek veritabanı kaynak limitleri](resource-limits-vcore-single-databases.md). Örneğin, elastik havuz 5. nesil kullanıyorsa ve veritabanı başına en fazla sanal çekirdek 2 ' ye ayarlanırsa, en fazla eşzamanlı çalışan değeri 200 olur.  Veritabanı başına en fazla sanal çekirdek 0,5 olarak ayarlanırsa, 5. nesil ' den itibaren maksimum eşzamanlı çalışan 50 değeri, sanal çekirdek başına en 100 fazla eşzamanlı çalışan olur. Daha az 1 sanal çekirdek veya daha az 1 olan veritabanı başına diğer en fazla sanal çekirdek ayarları için, en fazla eş zamanlı çalışan sayısı benzer şekilde ölçeklendirildi.

### <a name="standard-elastic-pool-limits-continued"></a>Standart esnek havuz limitleri (devam)

| Havuz başına eDTU | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Havuz başına dahil edilen depolama alanı (GB) <sup>1</sup> | 1200 | 1600 | 2000 | 2500 | 3000 |
| Havuz başına maks. depolama alanı (GB) | 2560 | 3072 | 3584 | 4096 | 4096 |
| Havuz başına en fazla In-Memory OLTP depolaması (GB) | Yok | Yok | Yok | Yok | Yok |
| Havuz başına en fazla veritabanı sayısı <sup>2</sup> | 500 | 500 | 500 | 500 | 500 |
| Havuz başına maksimum eş zamanlı çalışan (istek) <sup>3</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Havuz başına en fazla eşzamanlı oturum <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Veritabanı seçenekleri başına en az DTU | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Veritabanı seçenekleri başına en fazla DTU | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Veritabanı başına maks. depolama alanı (GB) | 1024 | 1536 | 1792 | 2304 | 2816 |
|||||||

<sup>1</sup> sağlanan ek depolama alanı nedeniyle daha fazla ücret ödediğinden ilgili ayrıntılı bilgi için bkz. [SQL Veritabanı fiyatlandırma seçenekleri](https://azure.microsoft.com/pricing/details/sql-database/elastic/) .

<sup>2</sup> daha fazla dikkat edilmesi için [yoğun elastik havuzlardaki kaynak yönetimine](elastic-pool-resource-management.md) bakın.

<sup>3</sup> tek bir veritabanı için maksimum eşzamanlı çalışan (istek) için bkz. [tek veritabanı kaynak limitleri](resource-limits-vcore-single-databases.md). Örneğin, elastik havuz 5. nesil kullanıyorsa ve veritabanı başına en fazla sanal çekirdek 2 ' ye ayarlanırsa, en fazla eşzamanlı çalışan değeri 200 olur.  Veritabanı başına en fazla sanal çekirdek 0,5 olarak ayarlanırsa, 5. nesil ' den itibaren maksimum eşzamanlı çalışan 50 değeri, sanal çekirdek başına en 100 fazla eşzamanlı çalışan olur. Daha az 1 sanal çekirdek veya daha az 1 olan veritabanı başına diğer en fazla sanal çekirdek ayarları için, en fazla eş zamanlı çalışan sayısı benzer şekilde ölçeklendirildi.

### <a name="premium-elastic-pool-limits"></a>Premium esnek havuz sınırları

| Havuz başına eDTU | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Havuz başına dahil edilen depolama alanı (GB) <sup>1</sup> | 250 | 500 | 750 | 1024 | 1536 |
| Havuz başına maks. depolama alanı (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
| Havuz başına en fazla In-Memory OLTP depolaması (GB) | 1 | 2 | 4 | 10 | 12 |
| Havuz başına en fazla veritabanı sayısı <sup>2</sup> | 50 | 100 | 100 | 100 | 100 |
| Havuz başına en fazla eş zamanlı çalışan (istek) <sup>3</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Havuz başına en fazla eşzamanlı oturum <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Veritabanı başına Min. eDTU | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Veritabanı başına Maks. eDTU | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Veritabanı başına maks. depolama alanı (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
|||||||

<sup>1</sup> sağlanan ek depolama alanı nedeniyle daha fazla ücret ödediğinden ilgili ayrıntılı bilgi için bkz. [SQL Veritabanı fiyatlandırma seçenekleri](https://azure.microsoft.com/pricing/details/sql-database/elastic/) .

<sup>2</sup> daha fazla dikkat edilmesi için [yoğun elastik havuzlardaki kaynak yönetimine](elastic-pool-resource-management.md) bakın.

<sup>3</sup> tek bir veritabanı için maksimum eşzamanlı çalışan (istek) için bkz. [tek veritabanı kaynak limitleri](resource-limits-vcore-single-databases.md). Örneğin, elastik havuz 5. nesil kullanıyorsa ve veritabanı başına en fazla sanal çekirdek 2 ' ye ayarlanırsa, en fazla eşzamanlı çalışan değeri 200 olur.  Veritabanı başına en fazla sanal çekirdek 0,5 olarak ayarlanırsa, 5. nesil ' den itibaren maksimum eşzamanlı çalışan 50 değeri, sanal çekirdek başına en 100 fazla eşzamanlı çalışan olur. Daha az 1 sanal çekirdek veya daha az 1 olan veritabanı başına diğer en fazla sanal çekirdek ayarları için, en fazla eş zamanlı çalışan sayısı benzer şekilde ölçeklendirildi.

### <a name="premium-elastic-pool-limits-continued"></a>Premium esnek havuz limitleri (devam)

| Havuz başına eDTU | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Havuz başına dahil edilen depolama alanı (GB) <sup>1</sup> | 2048 | 2560 | 3072 | 3548 | 4096 |
| Havuz başına maks. depolama alanı (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Havuz başına en fazla In-Memory OLTP depolaması (GB) | 16 | 20 | 24 | 28 | 32 |
| Havuz başına en fazla veritabanı sayısı <sup>2</sup> | 100 | 100 | 100 | 100 | 100 |
| Havuz başına maksimum eş zamanlı çalışan (istek) <sup>3</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Havuz başına en fazla eşzamanlı oturum <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Veritabanı seçenekleri başına en az DTU | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Veritabanı seçenekleri başına en fazla DTU | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Veritabanı başına maks. depolama alanı (GB) | 2048 | 2560 | 3072 | 3584 | 4096 |
|||||||

<sup>1</sup> sağlanan ek depolama alanı nedeniyle daha fazla ücret ödediğinden ilgili ayrıntılı bilgi için bkz. [SQL Veritabanı fiyatlandırma seçenekleri](https://azure.microsoft.com/pricing/details/sql-database/elastic/) .

<sup>2</sup> daha fazla dikkat edilmesi için [yoğun elastik havuzlardaki kaynak yönetimine](elastic-pool-resource-management.md) bakın.

<sup>3</sup> tek bir veritabanı için maksimum eşzamanlı çalışan (istek) için bkz. [tek veritabanı kaynak limitleri](resource-limits-vcore-single-databases.md). Örneğin, elastik havuz 5. nesil kullanıyorsa ve veritabanı başına en fazla sanal çekirdek 2 ' ye ayarlanırsa, en fazla eşzamanlı çalışan değeri 200 olur.  Veritabanı başına en fazla sanal çekirdek 0,5 olarak ayarlanırsa, 5. nesil ' den itibaren maksimum eşzamanlı çalışan 50 değeri, sanal çekirdek başına en 100 fazla eşzamanlı çalışan olur. Daha az 1 sanal çekirdek veya daha az 1 olan veritabanı başına diğer en fazla sanal çekirdek ayarları için, en fazla eş zamanlı çalışan sayısı benzer şekilde ölçeklendirildi.

> [!IMPORTANT]
> Premium katmanda 1 TB 'den fazla depolama alanı şu anda tüm bölgelerde kullanılabilir: Çin Doğu, Çin Kuzey, Almanya Orta ve Almanya Kuzeydoğu. Bu bölgelerde, Premium katmanda en fazla depolama alanı 1 TB ile sınırlıdır.  Daha fazla bilgi için bkz. [P11-P15 geçerli sınırlamalar](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Bir elastik havuzun tüm DTU’ları kullanılırsa, sorguları işlemek üzere havuzdaki her bir veritabanı eşit miktarda kaynak alır. SQL Veritabanı hizmeti, eşit dilimlerde işlem süresi sunarak veritabanları arasında kaynak paylaşım eşitliğini sağlar. Elastik havuz kaynak paylaşımı eşitliği, veritabanı başına DTU dakikası sıfır olmayan bir değere ayarlandığında her bir veritabanı için garanti edilen herhangi bir kaynak miktarına ek niteliktedir.

> [!NOTE]
> `tempdb`Sınırlar için bkz. [tempdb sınırları](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Havuza alınmış veritabanları için veritabanı özellikleri

Aşağıdaki tabloda, havuza alınmış veritabanlarının özellikleri açıklanmaktadır.

| Özellik | Açıklama |
|:--- |:--- |
| Veritabanı başına Maks. eDTU |Havuzdaki diğer veritabanlarının kullanımına göre mevcutsa, havuzdaki herhangi bir veritabanının kullanabileceği en fazla eDTU sayısı. Veritabanı başına en fazla eDTU, veritabanı için kaynak garantisi anlamına gelmez. Bu ayar havuzdaki tüm veritabanları için geçerli olan genel bir ayardır. Veritabanı kullanımının en üst seviyeye çıktığı durumlarla baş edebilmek için veritabanı başına en fazla eDTU sayısını yeterince yüksek bir değere ayarlayın. Havuz genellikle tüm veritabanlarının eşzamanlı olarak en üst kullanım seviyesine çıkmadığı sıcak ve soğuk kullanım modellerini varsaydığından, bir miktar aşırı ayırma beklenir. Örneğin, veritabanı başına en yüksek kullanımın 20 eDTU olduğunu ve havuzdaki 100 veritabanının yalnızca %20’sinin aynı anda en yüksek seviyeye çıktığını varsayalım. Veritabanı başına en fazla eDTU değeri 20 eDTU’ya ayarlanırsa, havuzun 5 kat fazla kullanılması ve havuz başına eDTU sayısının 400’e ayarlanması makuldür. |
| Veritabanı başına Min. eDTU |Havuzdaki herhangi bir veritabanının kullanabileceği en az eDTU sayısı garanti edilir. Bu ayar havuzdaki tüm veritabanları için geçerli olan genel bir ayardır. Veritabanı başına en az eDTU sayısı 0’a ayarlanabilir; bu değer aynı zamanda varsayılan değerdir. Bu özellik, 0 ile veritabanı başına ortalama eDTU kullanımı arasında bir değere ayarlanır. Havuzdaki veritabanı sayısının veritabanı başına en az eDTU sayısıyla çarpımı, havuz başına eDTU sayısını aşamaz. Örneğin, bir havuzda 20 veritabanı varsa ve veritabanı başına en az eDTU sayısı 10 eDTU’ya ayarlanırsa, havuzdaki eDTU sayısı en az 200 eDTU olmalıdır. |
| Veritabanı başına en fazla depolama |Bir havuzdaki bir veritabanı için Kullanıcı tarafından ayarlanan en büyük veritabanı boyutu. Ancak, havuza alınmış veritabanları ayrılmış havuz depolama alanını paylaşır. *Veritabanı başına* toplam en fazla depolama alanı, havuzun toplam kullanılabilir depolama *alanından* daha büyük olarak ayarlansa bile, tüm veritabanları tarafından kullanılan Toplam alan, kullanılabilir havuz sınırını aşamayacak. En fazla veritabanı boyutu, veri dosyalarının boyut üst sınırını ifade eder ve günlük dosyaları tarafından kullanılan alanı içermez. |
|||

## <a name="next-steps"></a>Sonraki adımlar

* Tek bir veritabanı için sanal çekirdek kaynak sınırları için bkz [. Vcore satın alma modelini kullanarak tek veritabanları için kaynak sınırları](resource-limits-vcore-single-databases.md)
* Tek bir veritabanı için DTU kaynak sınırları için bkz [. DTU satın alma modelini kullanarak tek veritabanları için kaynak limitleri](resource-limits-dtu-single-databases.md)
* Elastik havuzların sanal çekirdek kaynak sınırları için bkz [. Vcore satın alma modelini kullanarak elastik havuzlar için kaynak sınırları](resource-limits-vcore-elastic-pools.md)
* Azure SQL yönetilen örneği 'nde yönetilen örnekler için kaynak sınırları için bkz. [SQL yönetilen örnek kaynak sınırları](../managed-instance/resource-limits.md).
* Genel Azure limitleri hakkında daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Mantıksal bir SQL Server üzerindeki kaynak limitleri hakkında bilgi için bkz. sunucu ve abonelik düzeylerindeki sınırlamalar hakkında bilgi için bkz. [MANTıKSAL SQL Server 'da kaynak sınırlarına genel bakış](resource-limits-logical-server.md) .