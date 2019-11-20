---
title: Sanal çekirdek kaynak limitleri-tek veritabanı
description: Bu sayfa, Azure SQL veritabanı 'nda tek bir veritabanı için bazı yaygın sanal çekirdek kaynak sınırlarını açıklamaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/04/2019
ms.openlocfilehash: 11d9115893f8d0632110d86a671a6590ca48e220
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822342"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Sanal çekirdek satın alma modelini kullanan tek veritabanlarına yönelik kaynak sınırları

Bu makale, sanal çekirdek satın alma modelini kullanarak Azure SQL veritabanı tekil veritabanları için ayrıntılı kaynak sınırları sağlar.

SQL veritabanı sunucusundaki tek veritabanları için DTU satın alma model sınırları için bkz. [SQL veritabanı sunucusundaki kaynak sınırlarına genel bakış](sql-database-resource-limits-database-server.md).


[Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)veya [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases)kullanarak tek bir veritabanı için hizmet katmanını, işlem boyutunu ve depolama miktarını ayarlayabilirsiniz.

> [!IMPORTANT]
> Ölçeklendirme Kılavuzu ve konuları için bkz. [tek bir veritabanını ölçeklendirme](sql-database-single-database-scale.md).


## <a name="general-purpose---serverless-compute---gen5"></a>Genel amaçlı-sunucusuz işlem-5. nesil

[Sunucusuz bilgi işlem katmanı](sql-database-serverless.md) Şu anda yalnızca 5. nesil donanımında kullanılabilir.

### <a name="gen5-compute-generation-part-1"></a>5\. nesil işlem oluşturma (Bölüm 1)

|İşlem boyutu|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|İşlem oluşturma|Gen5|Gen5|Gen5|Gen5|Gen5|
|En fazla sanal çekirdek|0,5-1|0,5-2|0,5-4|0.75-6|1.0-8|
|En fazla bellek (GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|En az otomatik duraklatma gecikmesi (dakika)|60|60|60|60|60|
|Columnstore desteği|Evet|Evet|Evet|Evet|Evet|
|Bellek içi OLTP depolaması (GB)|Yok|Yok|Yok|Yok|Yok|
|En fazla veri boyutu (GB)|512|1024|1024|1024|1536|
|En fazla günlük boyutu (GB)|154|307|307|307|461|
|TempDB en fazla veri boyutu (GB)|32|64|128|192|256|
|Depolama türü|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|
|GÇ gecikme süresi (yaklaşık)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|
|Maksimum veri ıOPS (64 KB)|500|1000|2000|3000|4000|
|En fazla günlük hızı (MBps)|2,5|5,6|10|15|20|
|Maksimum eş zamanlı çalışan (istek)|75|150|300|450|600|
|Maks. eş zamanlı oturum|30000|30000|30000|30000|30000|
|Çoğaltma sayısı|1|1|1|1|1|
|Çoklu-AZ|Yok|Yok|Yok|Yok|Yok|
|Ölçeği okuyun|Yok|Yok|Yok|Yok|Yok|
|Dahil edilen yedekleme depolaması|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|

### <a name="gen5-compute-generation-part-2"></a>5\. nesil işlem oluşturma (2. bölüm)

|İşlem boyutu|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|İşlem oluşturma|Gen5|Gen5|Gen5|Gen5|
|En fazla sanal çekirdek|1,25-10|1.50-12|1,75-14|2,00-16|
|En fazla bellek (GB)|3,75-30|4.50-36|5.25-42|6.00-48|
|En az otomatik duraklatma gecikmesi (dakika)|60|60|60|60|
|Columnstore desteği|Evet|Evet|Evet|Evet|
|Bellek içi OLTP depolaması (GB)|Yok|Yok|Yok|Yok|
|En fazla veri boyutu (GB)|1536|1536|1536|3072|
|En fazla günlük boyutu (GB)|461|461|461|922|
|TempDB en fazla veri boyutu (GB)|320|384|448|512|
|Depolama türü|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|
|GÇ gecikme süresi (yaklaşık)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|
|Maksimum veri ıOPS (64 KB)|5000|6000|7000|8000|
|En fazla günlük hızı (MBps)|20|20|20|20|
|Maksimum eş zamanlı çalışan (istek)|750|900|1050|1200|
|Maks. eş zamanlı oturum|30000|30000|30000|30000|
|Çoğaltma sayısı|1|1|1|1|
|Çoklu-AZ|Yok|Yok|Yok|Yok|
|Ölçeği okuyun|Yok|Yok|Yok|Yok|
|Dahil edilen yedekleme depolaması|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|


## <a name="hyperscale---provisioned-compute---gen5"></a>Hiper ölçek-sağlanan işlem-5. nesil

### <a name="gen5-compute-generation"></a>5\. nesil işlem oluşturma

|Performans düzeyi|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|İşlem oluşturma|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Sanal çekirdek|2|4|8|16|24|32|40|80|
|Bellek (GB)|10.2|20,4|40,8|81,6|122,4|163,2|204|408|
|[Rbpex](sql-database-service-tier-hyperscale.md#compute) Boyutla|3X belleği|3X belleği|3X belleği|3X belleği|3X belleği|3X belleği|3X belleği|3X belleği|
|Columnstore desteği|Evet|Evet|Evet|Evet|Evet|Evet|Evet|Evet|
|Bellek içi OLTP depolaması (GB)|Yok|Yok|Yok|Yok|Yok|Yok|Yok|Yok|
|En fazla veri boyutu (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|En fazla günlük boyutu (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|TempDB en fazla veri boyutu (GB)|64|128|256|384|384|384|384|384|
|Depolama türü| [1. nota](#notes) |[1. nota](#notes)|[1. nota](#notes) |[1. nota](#notes) |[1. nota](#notes) |[1. nota](#notes) |[1. nota](#notes) | [1. nota](#notes) |
|Maksimum veri ıOPS (64 KB)|[2. nota](#notes)|[2. nota](#notes)|[2. nota](#notes)|[2. nota](#notes)|[2. nota](#notes)|[2. nota](#notes)|[2. nota](#notes)|[2. nota](#notes)|
|GÇ gecikme süresi (yaklaşık)|[3. nota](#notes)|[3. nota](#notes)|[3. nota](#notes)|[3. nota](#notes)|[3. nota](#notes)|[3. nota](#notes)|[3. nota](#notes)|[3. nota](#notes)|
|Maksimum eş zamanlı çalışan (istek)|200|400|800|1600|2400|3200|4000|8000|
|Maks. eş zamanlı oturum|30000|30000|30000|30000|30000|30000|30000|30000|
|İkincil çoğaltmalar|0-4|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Çoklu-AZ|Yok|Yok|Yok|Yok|Yok|Yok|Yok|Yok|
|Ölçeği okuyun|Evet|Evet|Evet|Evet|Evet|Evet|Evet|Evet|
|Yedekleme depolama saklama|7 gün|7 gün|7 gün|7 gün|7 gün|7 gün|7 gün|7 gün|
|||

#### <a name="notes"></a>Notlar

**Note 1**: hiper ölçek, ayrı işlem ve depolama bileşenlerine sahip çok katmanlı bir mimaridir: [hiper ölçek hizmeti katmanı mimarisi](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**2**. Adım: hiper ölçekte çok katmanlı mimaride birden çok düzeyde önbelleğe alma işlemi vardır. Etkin ıOPS iş yüküne bağlı olacaktır.

**3**. Adım: en çok kullanılan veri sayfalarını önbelleğe alan işlem Çoğaltmalarından dolayı RBPEX SSD tabanlı önbellekteki veriler için gecikme 1-2 ms 'dir. Sayfa sunucularından alınan veriler için daha yüksek gecikme.

## <a name="general-purpose---provisioned-compute---gen4"></a>Genel amaçlı-sağlanan işlem-4. nesil

> [!IMPORTANT]
> Yeni 4. nesil veritabanları artık Avustralya Doğu veya Brezilya Güney bölgelerinde desteklenmez.

### <a name="gen4-compute-generation-part-1"></a>4\. nesil işlem oluşturma (Bölüm 1)

|İşlem boyutu|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|İşlem oluşturma|4\. nesil|4\. nesil|4\. nesil|4\. nesil|4\. nesil|4\. nesil|
|Sanal çekirdek|1|2|3|4|5|6|
|Bellek (GB)|7|14|21|28|35|42|
|Columnstore desteği|Evet|Evet|Evet|Evet|Evet|Evet|
|Bellek içi OLTP depolaması (GB)|Yok|Yok|Yok|Yok|Yok|Yok|
|En fazla veri boyutu (GB)|1024|1024|1536|1536|1536|3072|
|En fazla günlük boyutu (GB)|307|307|461|461|461|922|
|TempDB en fazla veri boyutu (GB)|32|64|6400/96|128|160|192|
|Depolama türü|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|
|GÇ gecikme süresi (yaklaşık)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|
|Maksimum veri ıOPS (64 KB)|500|1000|1500|2000|2500|3000|
|En fazla günlük hızı (MBps)|3,75|7,5|11,25|15|18,75|22,5|
|Maksimum eş zamanlı çalışan (istek)|200|400|600|800|1000|1200|
|Maks. eş zamanlı oturum|30000|30000|30000|30000|30000|30000|
|Çoğaltma sayısı|1|1|1|1|1|1|
|Çoklu-AZ|Yok|Yok|Yok|Yok|Yok|Yok|
|Ölçeği okuyun|Yok|Yok|Yok|Yok|Yok|Yok|
|Dahil edilen yedekleme depolaması|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|

### <a name="gen4-compute-generation-part-2"></a>4\. nesil işlem oluşturma (2. bölüm)

|İşlem boyutu|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|İşlem oluşturma|4\. nesil|4\. nesil|4\. nesil|4\. nesil|4\. nesil|4\. nesil|
|Sanal çekirdek|7|8|9|10|16|24|
|Bellek (GB)|49|56|63|70|112|168|
|Columnstore desteği|Evet|Evet|Evet|Evet|Evet|Evet|
|Bellek içi OLTP depolaması (GB)|Yok|Yok|Yok|Yok|Yok|Yok|
|En fazla veri boyutu (GB)|3072|3072|3072|3072|4096|4096|
|En fazla günlük boyutu (GB)|922|922|922|922|1229|1229|
|TempDB en fazla veri boyutu (GB)|224|256|288|320|384|384|
|Depolama türü|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|
|GÇ gecikme süresi (yaklaşık)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)
|Maksimum veri ıOPS (64 KB)|3500|4000|4500|5000|8000|12000|
|En fazla günlük hızı (MBps)|26,25|30|30|30|30|30|
|Maksimum eş zamanlı çalışan (istek)|1400|1600|1800|2000|3200|4800|
|Maks. eş zamanlı oturum|30000|30000|30000|30000|30000|30000|
|Çoğaltma sayısı|1|1|1|1|1|1|
|Çoklu-AZ|Yok|Yok|Yok|Yok|Yok|Yok|
|Ölçeği okuyun|Yok|Yok|Yok|Yok|Yok|Yok|
|Dahil edilen yedekleme depolaması|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|

## <a name="general-purpose---provisioned-compute---gen5"></a>Genel amaçlı-sağlanan işlem-5. nesil

### <a name="gen5-compute-generation-part-1"></a>5\. nesil işlem oluşturma (Bölüm 1)

|İşlem boyutu|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|İşlem oluşturma|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Sanal çekirdek|2|4|6|8|10|12|14|
|Bellek (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Columnstore desteği|Evet|Evet|Evet|Evet|Evet|Evet|Evet|
|Bellek içi OLTP depolaması (GB)|Yok|Yok|Yok|Yok|Yok|Yok|Yok|
|En fazla veri boyutu (GB)|1024|1024|1536|1536|1536|3072|3072|
|En fazla günlük boyutu (GB)|307|307|461|461|461|922|922|
|TempDB en fazla veri boyutu (GB)|64|128|192|256|320|384|384|
|Depolama türü|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|
|GÇ gecikme süresi (yaklaşık)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|
|Maksimum veri ıOPS (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|En fazla günlük hızı (MBps)|3,75|7,5|11,25|15|18,75|22,5|26,25|
|Maksimum eş zamanlı çalışan (istek)|200|400|600|800|1000|1200|1400|
|Maks. eş zamanlı oturum|30000|30000|30000|30000|30000|30000|30000|
|Çoğaltma sayısı|1|1|1|1|1|1|1|
|Çoklu-AZ|Yok|Yok|Yok|Yok|Yok|Yok|Yok|
|Ölçeği okuyun|Yok|Yok|Yok|Yok|Yok|Yok|Yok|
|Dahil edilen yedekleme depolaması|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|

### <a name="gen5-compute-generation-part-2"></a>5\. nesil işlem oluşturma (2. bölüm)

|İşlem boyutu|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|İşlem oluşturma|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Sanal çekirdek|16|18|20|24|32|40|80|
|Bellek (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Columnstore desteği|Evet|Evet|Evet|Evet|Evet|Evet|Evet|
|Bellek içi OLTP depolaması (GB)|Yok|Yok|Yok|Yok|Yok|Yok|Yok|
|En fazla veri boyutu (GB)|3072|3072|3072|4096|4096|4096|4096|
|En fazla günlük boyutu (GB)|922|922|922|1229|1229|1229|1229|
|TempDB en fazla veri boyutu (GB)|384|384|384|384|384|384|384|
|Depolama türü|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|Uzak SSD|
|GÇ gecikme süresi (yaklaşık)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|5-7 MS (yazma)<br>5-10 MS (okuma)|
|Maksimum veri ıOPS (64 KB)|8000|9000|10000|12000|16000|20000|40000|
|En fazla günlük hızı (MBps)|30|30|30|30|30|30|30|
|Maksimum eş zamanlı çalışan (istek)|1600|1800|2000|2400|3200|4000|8000|
|Maks. eş zamanlı oturum|30000|30000|30000|30000|30000|30000|30000|
|Çoğaltma sayısı|1|1|1|1|1|1|1|
|Çoklu-AZ|Yok|Yok|Yok|Yok|Yok|Yok|Yok|
|Ölçeği okuyun|Yok|Yok|Yok|Yok|Yok|Yok|Yok|
|Dahil edilen yedekleme depolaması|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Genel amaçlı-sağlanan işlem-Fsv2-Series

### <a name="fsv2-series-compute-generation-preview"></a>Fsv2 serisi işlem oluşturma (Önizleme)

|İşlem boyutu|GP_Fsv2_72|
|:--- | --: |
|İşlem oluşturma|Fsv2-serisi|
|Sanal çekirdek|72|
|Bellek (GB)|136|
|Columnstore desteği|Evet|
|Bellek içi OLTP depolaması (GB)|Yok|
|En fazla veri boyutu (GB)|4096|
|En fazla günlük boyutu (GB)|1024|
|TempDB en fazla veri boyutu (GB)|333|
|Depolama türü|Uzak SSD|
|GÇ gecikme süresi (yaklaşık)|5-7 MS (yazma)<br>5-10 MS (okuma)|
|Maksimum veri ıOPS (64 KB)|36000|
|En fazla günlük hızı (MBps)|30|
|Maksimum eş zamanlı çalışan (Quests)|3600|
|Maks. eş zamanlı oturum|30000|
|Çoğaltma sayısı|1|
|Çoklu-AZ|Yok|
|Ölçeği okuyun|Yok|
|Dahil edilen yedekleme depolaması|1X DB boyutu|



## <a name="business-critical---provisioned-compute---gen4"></a>İş açısından kritik-sağlanan işlem-4. nesil

> [!IMPORTANT]
> Yeni 4. nesil veritabanları artık Avustralya Doğu veya Brezilya Güney bölgelerinde desteklenmez.

### <a name="gen4-compute-generation-part-1"></a>4\. nesil işlem oluşturma (Bölüm 1)

|İşlem boyutu|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|İşlem oluşturma|4\. nesil|4\. nesil|4\. nesil|4\. nesil|4\. nesil|4\. nesil|
|Sanal çekirdek|1|2|3|4|5|6|
|Bellek (GB)|7|14|21|28|35|42|
|Columnstore desteği|Evet|Evet|Evet|Evet|Evet|Evet|
|Bellek içi OLTP depolaması (GB)|1|2|3|4|5|6|
|Depolama türü|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|
|En fazla veri boyutu (GB)|1024|1024|1024|1024|1024|1024|
|En fazla günlük boyutu (GB)|307|307|307|307|307|307|
|TempDB en fazla veri boyutu (GB)|32|64|6400/96|128|160|192|
|GÇ gecikme süresi (yaklaşık)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|
|Maksimum veri ıOPS (64 KB)|5000|10000|15000|20000|25000|30000|
|En fazla günlük hızı (MBps)|8|16|24|32|40|48|
|Maksimum eş zamanlı çalışan (istek)|200|400|600|800|1000|1200|
|Maks. eş zamanlı oturum|200|400|600|800|1000|1200|
|Maks. eş zamanlı oturum|30000|30000|30000|30000|30000|30000|
|Çoğaltma sayısı|4|4|4|4|4|4|
|Çoklu-AZ|Evet|Evet|Evet|Evet|Evet|Evet|
|Ölçeği okuyun|Evet|Evet|Evet|Evet|Evet|Evet|
|Dahil edilen yedekleme depolaması|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|

### <a name="gen4-compute-generation-part-2"></a>4\. nesil işlem oluşturma (2. bölüm)

|İşlem boyutu|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|İşlem oluşturma|4\. nesil|4\. nesil|4\. nesil|4\. nesil|4\. nesil|4\. nesil|
|Sanal çekirdek|7|8|9|10|16|24|
|Bellek (GB)|49|56|63|70|112|168|
|Columnstore desteği|Evet|Evet|Evet|Evet|Evet|Evet|
|Bellek içi OLTP depolaması (GB)|7|8|9.5|11|20|36|
|Depolama türü|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|
|En fazla veri boyutu (GB)|1024|1024|1024|1024|1024|1024|
|En fazla günlük boyutu (GB)|307|307|307|307|307|307|
|TempDB en fazla veri boyutu (GB)|224|256|288|320|384|384|
|GÇ gecikme süresi (yaklaşık)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|
|Maksimum veri ıOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|En fazla günlük hızı (MBps)|56|64|64|64|64|64|
|Maksimum eş zamanlı çalışan (istek)|1400|1600|1800|2000|3200|4800|
|Maksimum eşzamanlı oturum açma sayısı (istek)|1400|1600|1800|2000|3200|4800|
|Maks. eş zamanlı oturum|30000|30000|30000|30000|30000|30000|
|Çoğaltma sayısı|4|4|4|4|4|4|
|Çoklu-AZ|Evet|Evet|Evet|Evet|Evet|Evet|
|Ölçeği okuyun|Evet|Evet|Evet|Evet|Evet|Evet|
|Dahil edilen yedekleme depolaması|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|

## <a name="business-critical---provisioned-compute---gen5"></a>İş açısından kritik-sağlanan işlem-5. nesil

### <a name="gen5-compute-generation-part-1"></a>5\. nesil işlem oluşturma (Bölüm 1)

|İşlem boyutu|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|İşlem oluşturma|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Sanal çekirdek|2|4|6|8|10|12|14|
|Bellek (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Columnstore desteği|Evet|Evet|Evet|Evet|Evet|Evet|Evet|
|Bellek içi OLTP depolaması (GB)|1,571|3,142|4,713|6,284|8,655|11,026|13,397|
|En fazla veri boyutu (GB)|1024|1024|1536|1536|1536|3072|3072|
|En fazla günlük boyutu (GB)|307|307|461|461|461|922|922|
|TempDB en fazla veri boyutu (GB)|64|128|192|256|320|384|384|
|Depolama türü|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|
|GÇ gecikme süresi (yaklaşık)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|
|Maksimum veri ıOPS (64 KB)|8000|16000|24000|32000|40000|48000|56000|
|En fazla günlük hızı (MBps)|12|24|36|48|60|72|84|
|Maksimum eş zamanlı çalışan (istek)|200|400|600|800|1000|1200|1400|
|Maks. eş zamanlı oturum|200|400|600|800|1000|1200|1400|
|Maks. eş zamanlı oturum|30000|30000|30000|30000|30000|30000|30000|
|Çoğaltma sayısı|4|4|4|4|4|4|4|
|Çoklu-AZ|Evet|Evet|Evet|Evet|Evet|Evet|Evet|
|Ölçeği okuyun|Evet|Evet|Evet|Evet|Evet|Evet|Evet|
|Dahil edilen yedekleme depolaması|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|

### <a name="gen5-compute-generation-part-2"></a>5\. nesil işlem oluşturma (2. bölüm)

|İşlem boyutu|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|İşlem oluşturma|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Sanal çekirdek|16|18|20|24|32|40|80|
|Bellek (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Columnstore desteği|Evet|Evet|Evet|Evet|Evet|Evet|Evet|
|Bellek içi OLTP depolaması (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|En fazla veri boyutu (GB)|3072|3072|3072|4096|4096|4096|4096|
|En fazla günlük boyutu (GB)|922|922|922|1229|1229|1229|1229|
|TempDB en fazla veri boyutu (GB)|384|384|384|384|384|384|384|
|Depolama türü|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|Yerel SSD|
|GÇ gecikme süresi (yaklaşık)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|1-2 ms (yazma)<br>1-2 ms (okuma)|
|Maksimum veri ıOPS (64 KB)|64000|72000|80000|96000|128000|160000|320000|
|En fazla günlük hızı (MBps)|6400/96|6400/96|6400/96|6400/96|6400/96|6400/96|6400/96|
|Maksimum eş zamanlı çalışan (istek)|1600|1800|2000|2400|3200|4000|8000|
|Maks. eş zamanlı oturum|1600|1800|2000|2400|3200|4000|8000|
|Maks. eş zamanlı oturum|30000|30000|30000|30000|30000|30000|30000|
|Çoğaltma sayısı|4|4|4|4|4|4|4|
|Çoklu-AZ|Evet|Evet|Evet|Evet|Evet|Evet|Evet|
|Ölçeği okuyun|Evet|Evet|Evet|Evet|Evet|Evet|Evet|
|Dahil edilen yedekleme depolaması|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|1X DB boyutu|


## <a name="business-critical---provisioned-compute---m-series"></a>İş açısından kritik-sağlanan işlem-a serisi

### <a name="m-series-compute-generation-preview"></a>A serisi işlem oluşturma (Önizleme)

|İşlem boyutu|GP_M_128|
|:--- | --: |
|İşlem oluşturma|M serisi|
|Sanal çekirdek|128|
|Bellek (GB)|3767|
|Columnstore desteği|Evet|
|Bellek içi OLTP depolaması (GB)|481|
|En fazla veri boyutu (GB)|4096|
|En fazla günlük boyutu (GB)|2048|
|TempDB en fazla veri boyutu (GB)|4096|
|Depolama türü|Yerel SSD|
|GÇ gecikme süresi (yaklaşık)|1-2 ms (yazma)<br>1-2 ms (okuma)|
|Maksimum veri ıOPS (64 KB)|204800|
|En fazla günlük hızı (MBps)|192|
|Maksimum eş zamanlı çalışan (istek)|12800|
|Maks. eş zamanlı oturum|30000|
|Çoğaltma sayısı|4|
|Çoklu-AZ|Evet|
|Ölçeği okuyun|Evet|
|Dahil edilen yedekleme depolaması|1X DB boyutu|




> [!IMPORTANT]
> Bazı durumlarda, kullanılmayan alanı geri kazanmak için bir veritabanını daraltmanız gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda dosya alanını yönetme](sql-database-file-space-management.md).

## <a name="next-steps"></a>Sonraki adımlar

- Tek bir veritabanı için DTU kaynak sınırları için bkz [. DTU satın alma modelini kullanarak tek veritabanları için kaynak limitleri](sql-database-dtu-resource-limits-single-databases.md)
- Elastik havuzların sanal çekirdek kaynak sınırları için bkz [. Vcore satın alma modelini kullanarak elastik havuzlar için kaynak sınırları](sql-database-vcore-resource-limits-elastic-pools.md)
- Elastik havuzların DTU kaynak sınırları için bkz [. DTU satın alma modelini kullanarak elastik havuzlar için kaynak limitleri](sql-database-dtu-resource-limits-elastic-pools.md)
- Yönetilen örnekler için kaynak sınırları için bkz. [yönetilen örnek kaynak sınırları](sql-database-managed-instance-resource-limits.md).
- Genel Azure limitleri hakkında daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-subscription-service-limits.md).
- Bir veritabanı sunucusundaki kaynak sınırları hakkında daha fazla bilgi için bkz. sunucu ve abonelik düzeylerindeki sınırlamalar hakkında bilgi için bkz. [SQL veritabanı sunucusundaki kaynak sınırlarına genel bakış](sql-database-resource-limits-database-server.md) .
