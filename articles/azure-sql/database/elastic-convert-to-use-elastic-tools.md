---
title: Ölçeği genişletmek için mevcut veritabanlarını geçirme
description: Parçalı veritabanlarını bir parça eşleme Yöneticisi oluşturarak elastik veritabanı araçlarını kullanacak şekilde dönüştürme
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: c6ad8b4c80f4b9c2fdb3c1a14209dcf0febc89e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92787151"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Ölçeği genişletmek için mevcut veritabanlarını geçirme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Varolan ölçeği oluşturulmuş veritabanlarını ( [elastik veritabanı istemci kitaplığı](elastic-database-client-library.md)gibi) kullanarak kolayca yönetin. Önce var olan bir veritabanı kümesini parça [eşleme yöneticisini](elastic-scale-shard-map-management.md)kullanacak şekilde dönüştürün.

## <a name="overview"></a>Genel Bakış

Var olan bir veritabanının geçişini yapmak için:

1. Parça [eşleme Yöneticisi veritabanını](elastic-scale-shard-map-management.md)hazırlayın.
2. Parça haritasını oluşturun.
3. Bireysel parçaları hazırlayın.  
4. Parça eşlemesine eşlemeler ekleyin.

Bu teknikler [.NET Framework istemci kitaplığı](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)veya [Azure SQL veritabanı-elastik veritabanı araçları betikleriyle](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)bulunan PowerShell betikleri kullanılarak uygulanabilir. Buradaki örnekler PowerShell betiklerini kullanır.

ShardMapManager hakkında daha fazla bilgi için bkz. parça [eşleme yönetimi](elastic-scale-shard-map-management.md). Elastik veritabanı araçlarına genel bakış için bkz. [elastik veritabanı özelliklerine genel bakış](elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Parça eşleme Yöneticisi veritabanını hazırlama

Parça eşleme Yöneticisi, ölçeği genişletilmiş veritabanlarını yönetmek için verileri içeren özel bir veritabanıdır. Var olan bir veritabanını kullanabilir veya yeni bir veritabanı oluşturabilirsiniz. Parça eşleme Yöneticisi olarak davranan bir veritabanı, parça ile aynı veritabanı olmamalıdır. PowerShell betiği sizin için veritabanını oluşturmaz.

## <a name="step-1-create-a-shard-map-manager"></a>1. Adım: parça eşleme Yöneticisi oluşturma

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>Parça eşleme yöneticisini almak için

Oluşturulduktan sonra, bu cmdlet ile parça eşleme yöneticisini alabilirsiniz. ShardMapManager nesnesini her kullanmanız gerektiğinde bu adım gereklidir.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>2. Adım: parça haritasını oluşturma

Oluşturulacak parça haritasının türünü seçin. Seçim veritabanı mimarisine bağlıdır:

1. Veritabanı başına tek kiracı (koşullar Için bkz. [Sözlük](elastic-scale-glossary.md).)
2. Veritabanı başına birden çok kiracı (iki tür):
   1. Liste eşleme
   2. Aralık eşleme

Tek kiracılı bir model için bir **liste eşleme** parça haritası oluşturun. Tek kiracılı model, kiracı başına bir veritabanı atar. Bu, yönetim basitleştirdiği için SaaS geliştiricileri için etkili bir modeldir.

![Liste eşleme][1]

Çok kiracılı model tek bir veritabanına birkaç kiracı atar (ve kiracılar gruplarını birden çok veritabanı arasında dağıtabilirsiniz). Her kiracının küçük veri ihtiyaçlarına sahip olmasını beklediğinde bu modeli kullanın. Bu modelde, **Aralık eşleme** kullanarak bir veritabanına kiracı aralığı atayın.

![Aralık eşleme][2]

Ya da birden çok kiracıyı tek bir veritabanına atamak için bir *liste eşlemesi* kullanarak çok kiracılı bir veritabanı modeli uygulayabilirsiniz. Örneğin, DB1 kiracı KIMLIĞI 1 ve 5 ile ilgili bilgileri depolamak için kullanılır ve DB2, kiracı 7 ve kiracı 10 için veri depolar.

![Tek VERITABANıNDA birden çok kiracı][3]

**Seçtiğiniz seçeneğe göre şu seçeneklerden birini seçin:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Seçenek 1: bir liste eşlemesi için parça haritası oluşturma

ShardMapManager nesnesini kullanarak bir parça haritası oluşturun.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>2. seçenek: bir Aralık eşlemesi için parça haritası oluşturma

Bu eşleme modelini kullanmak için kiracı KIMLIĞI değerlerinin sürekli aralıklar olması gerekir ve veritabanlarını oluştururken aralığı atlayarak aralıklarda boşluk olması kabul edilebilir.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Seçenek 3: ayrı bir veritabanında eşleştirmeleri listeleme

Bu düzenin ayarlanması ayrıca adım 2, seçenek 1 ' de gösterildiği gibi bir liste eşlemesinin oluşturulmasını gerektirir.

## <a name="step-3-prepare-individual-shards"></a>3. Adım: tek tek parçaları hazırlama

Parça eşleme Yöneticisi 'ne her parça (veritabanı) ekleyin. Bu, eşleme bilgilerini depolamak için ayrı veritabanlarını hazırlar. Her parça üzerinde bu yöntemi yürütün.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>4. Adım: eşlemeler ekleme

Eşlemelerin eklenmesi, oluşturduğunuz parça haritasının türüne bağlıdır. Bir liste eşlemesi oluşturduysanız, liste eşlemeleri eklersiniz. Bir Aralık eşlemesi oluşturduysanız, Aralık eşlemeleri eklersiniz.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Seçenek 1: bir liste eşlemesi için verileri eşleme

Her kiracı için bir liste eşlemesi ekleyerek verileri eşleyin.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Seçenek 2: bir Aralık eşleme için verileri eşleme

Tüm kiracı KIMLIĞI aralığı-veritabanı ilişkilendirmeleri için Aralık eşlemelerini ekleyin:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>4. Adım 3: tek bir veritabanındaki birden çok kiracının verilerini eşleme

Her kiracı için Add-ListMapping çalıştırın (1. seçenek).

## <a name="checking-the-mappings"></a>Eşlemeler denetleniyor

Mevcut parçalar ve bunlarla ilişkili eşlemeler hakkında bilgiler aşağıdaki komutlar kullanılarak sorgulanabilecek:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Özet

Kurulumu tamamladıktan sonra elastik veritabanı istemci kitaplığını kullanmaya başlayabilirsiniz. Ayrıca [veriye bağımlı yönlendirme](elastic-scale-data-dependent-routing.md) ve [çok parçalı sorgu](elastic-scale-multishard-querying.md)kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure SQL Database-Elastic veritabanı araçları betiklerinden](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)PowerShell betikleri alın.

Araçlar ayrıca GitHub üzerinde de bulunur: [Azure/elastik-DB-Tools](https://github.com/Azure/elastic-db-tools).

Verileri çok kiracılı bir modelden tek bir kiracı modeline taşımak için bölünmüş birleştirme aracını kullanın. Bkz. [bölünmüş birleştirme aracı](elastic-scale-get-started.md).

## <a name="additional-resources"></a>Ek kaynaklar

Çok kiracılı hizmet olarak yazılım (SaaS) veritabanı uygulamalarının ortak veri mimarisi düzenlerine ilişkin bilgi için bkz. [Azure SQL Veritabanı ile Çok Kiracılı SaaS Uygulamaları için Tasarım Düzenleri](saas-tenancy-app-design-patterns.md).

## <a name="questions-and-feature-requests"></a>Sorular ve özellik istekleri

Sorular için, [SQL veritabanı Için Microsoft Q&soru sayfasını](/answers/topics/azure-sql-database.html) kullanın ve özellik istekleri Için bunları [SQL veritabanı geri bildirim forumuna](https://feedback.azure.com/forums/217321-sql-database/)ekleyin.

<!--Image references-->
[1]: ./media/elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/elastic-convert-to-use-elastic-tools/multipleonsingledb.png