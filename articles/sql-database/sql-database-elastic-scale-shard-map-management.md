---
title: Bir veritabanının ölçeğini genişletme
description: ShardMapManager 'ı kullanma, elastik veritabanı istemci kitaplığı
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 8175563d8c1c2ec59b4195b2ede06f6e1dbf8556
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823553"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Parça eşleme Yöneticisi ile veritabanlarını genişletme

SQL Azure veritabanlarını kolayca ölçeklendirmek için bir parça eşleme Yöneticisi kullanın. Parça eşleme Yöneticisi, bir parça kümesindeki tüm parçalar (veritabanları) hakkındaki genel eşleme bilgilerini tutan özel bir veritabanıdır. Meta veriler, bir uygulamanın, parçalı **anahtar**değerine göre doğru veritabanına bağlanmasına izin verir. Ayrıca, küme içindeki her parça yerel parça verilerini ( **kıardı**olarak bilinir) izleyen haritalar içerir.

![Parça eşleme yönetimi](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Bu eşlemelerin nasıl oluşturulduğunu anlamak, parça eşleme yönetimi için gereklidir. Bu işlem, parça haritalarını yönetmek için [elastik veritabanı istemci kitaplığı](sql-database-elastic-database-client-library.md) 'Nda bulunan ShardMapManager sınıfı ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) kullanılarak yapılır.  

## <a name="shard-maps-and-shard-mappings"></a>Parça haritaları ve parça eşlemeleri

Her parça için, oluşturulacak parça Haritası türünü seçmeniz gerekir. Seçim veritabanı mimarisine bağlıdır:

1. Veritabanı başına tek kiracı  
2. Veritabanı başına birden çok kiracı (iki tür):
   1. Liste eşleme
   2. Aralık eşleme

Tek kiracılı bir model için bir **liste eşleme** parça haritası oluşturun. Tek kiracılı model, kiracı başına bir veritabanı atar. Bu, yönetim basitleştirdiği için SaaS geliştiricileri için etkili bir modeldir.

![Liste eşleme][1]

Çok kiracılı model tek bir veritabanına birkaç kiracı atar (ve kiracılar gruplarını birden çok veritabanı arasında dağıtabilirsiniz). Her kiracının küçük veri ihtiyaçlarına sahip olmasını beklediğinde bu modeli kullanın. Bu modelde, **Aralık eşleme**kullanarak bir veritabanına kiracı aralığı atayın.

![Aralık eşleme][2]

Ya da birden çok kiracıyı tek bir veritabanına atamak için bir *liste eşlemesi* kullanarak çok kiracılı bir veritabanı modeli uygulayabilirsiniz. Örneğin, DB1 kiracı KIMLIĞI 1 ve 5 ile ilgili bilgileri depolamak için kullanılır ve DB2, kiracı 7 ve kiracı 10 için veri depolar.

![Tek VERITABANıNDA birden çok kiracı][3]

### <a name="supported-types-for-sharding-keys"></a>Parça anahtarları için desteklenen türler

Elastik ölçek, aşağıdaki türleri parçalama anahtarları olarak destekler:

| .NET | Java |
| --- | --- |
| integer |integer |
| Kalacağını |Kalacağını |
| 'ini |uuid |
| Byte []  |Byte [] |
| datetime | timestamp |
| TimeSpan | sürenin|
| türünde |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Liste ve Aralık parça haritaları

Parça haritaları, **tek parçalı anahtar değerlerinin listeleri**kullanılarak oluşturulabilir veya parçalama **anahtar değerlerinin aralıkları**kullanılarak oluşturulabilir.

### <a name="list-shard-maps"></a>Parça haritalarını Listele

Parçalar parçalara **sahiptir ve parçaları parçalara ayırma** , parçaları **bir parça Haritası** tarafından korunur. **Liste parça eşlemesi** , parçaları tanımlayan bağımsız anahtar değerleri ve parçalar olarak işlev sunan veritabanları arasındaki ilişkidir.  **Liste eşlemeleri** açıktır ve farklı anahtar değerleri aynı veritabanına eşlenebilir. Örneğin, anahtar değeri 1 A veritabanına eşlenir ve 3 ve 6 anahtar değerleri, B veritabanıyla eşlenir.

| Anahtar | Parça konumu |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Aralık parça haritaları

Bir **Aralık parça eşlemesinde**, anahtar aralığı, *düşük değerin* aralıktaki minimum anahtar olduğu ve *yüksek değer* aralıktan daha yüksek olan Ilk değer olan bir çift **[düşük değer, yüksek değer)** ile açıklanmıştır.

Örneğin, **[0, 100)** 0 ' dan büyük veya buna eşit ve 100 ' den küçük tüm tamsayılar içerir. Birden çok Aralık aynı veritabanına işaret edebilir ve ayrık aralıklar desteklenir (örneğin, [100.200) ve [400.600) her ikisi de aşağıdaki örnekte veritabanı C 'yi işaret ediyor.)

| Anahtar | Parça konumu |
| --- | --- |
| [1, 50) |Database_A |
| [50.100) |Database_B |
| [100.200) |Database_C |
| [400.600) |Database_C |
| ... |... |

Yukarıda gösterilen tabloların her biri, bir **Shardmap** nesnesine ilişkin kavramsal bir örnektir. Her satır, tek bir **Pointmapping** (liste parça eşlemesi için) veya **rangemapping** (Aralık parça haritası için) nesnesi için basitleştirilmiş bir örnektir.

## <a name="shard-map-manager"></a>Parça eşleme Yöneticisi

İstemci kitaplığında, parça eşleme Yöneticisi bir parça haritaları koleksiyonudur. Bir **Shardmapmanager** örneği tarafından yönetilen veriler üç yerde tutulur:

1. **Küresel parça Haritası (GSM)** : tüm parça haritaları ve eşlemelerinin tümü için depo olarak kullanılacak bir veritabanı belirtirsiniz. Özel tablolar ve saklı yordamlar bilgileri yönetmek için otomatik olarak oluşturulur. Bu genellikle küçük bir veritabanıdır ve güvenle erişilir ve uygulamanın diğer ihtiyaçları için kullanılmamalıdır. Tablolar **__ShardManagement**adlı özel bir şemadır.
2. **Yerel parça Haritası (LSM)** : parça olarak belirttiğiniz her veritabanı, bu parçaya özgü parça eşleme bilgilerini içeren ve yöneten birkaç küçük tablo ve özel saklı yordam içerecek şekilde değiştirilir. Bu bilgiler, GSM 'deki bilgilerle gereksizdir ve uygulamanın, GSM üzerinde herhangi bir yük yerleştirmeksizin önbelleğe alınmış parça eşleme bilgilerini doğrulamasını sağlar; uygulama, önbelleğe alınmış bir eşlemenin hala geçerli olup olmadığını anlamak için LSM 'yi kullanır. Her parçadaki LSM öğesine karşılık gelen tablolar da şema **__ShardManagement**.
3. **Uygulama önbelleği**: bir **Shardmapmanager** nesnesine erişen her uygulama örneği, eşlemelerinizin yerel bir bellek içi önbelleğini korur. Yakın zamanda alınmış yönlendirme bilgilerini depolar.

## <a name="constructing-a-shardmapmanager"></a>ShardMapManager oluşturma

Bir **Shardmapmanager** nesnesi, Factory ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) kalıbı kullanılarak oluşturulur. **Shardmapmanagerfactory. GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) yöntemi, kimlik bilgilerini (GSM 'nin bulunduğu sunucu adı ve veritabanı adı dahil olmak üzere) bir **ConnectionString** biçiminde alır ve bir **öğesinin örneğini döndürür. ShardMapManager**.  

**Lütfen unutmayın:** **Shardmapmanager** , uygulama etki alanı başına yalnızca bir kez, bir uygulamanın başlatma kodu içinde oluşturulmalıdır. Aynı uygulama etki alanında ek bir ShardMapManager örnekleri oluşturulması, uygulamanın bellek ve CPU kullanımının artmasıyla sonuçlanır. Bir **Shardmapmanager** , herhangi bir sayıda parça eşlemesi içerebilir. Tek bir parça eşlemesi birçok uygulama için yeterli olabilir, ancak farklı bir şema veya benzersiz amaçlar için farklı veritabanı kümelerinin kullanıldığı zamanlar olur; Bu durumlarda, birden çok parça haritaları tercih edilebilir.

Bu kodda, bir uygulama var olan bir **Shardmapmanager** 'ı TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) yöntemiyle) açmaya çalışır. Genel bir **shardmapmanager** 'ı (GSM) temsil eden nesneler veritabanı içinde henüz yoksa, istemci kitaplığı bunları CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) yöntemini kullanarak oluşturur.

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager;
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString,
                                        ShardMapManagerLoadPolicy.Lazy,
                                        out shardMapManager);

if (shardMapManagerExists)
{
    Console.WriteLine("Shard Map Manager already exists");
}
else
{
    // Create the Shard Map Manager.
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager");

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString,
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
}
```

.NET sürümü için PowerShell 'i kullanarak yeni bir parça eşleme Yöneticisi oluşturabilirsiniz. [Burada](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)bir örnek kullanılabilir.

## <a name="get-a-rangeshardmap-or-listshardmap"></a>RangeShardMap veya ListShardMap al

Parça eşleme Yöneticisi oluşturduktan sonra, trykrangeshardmap[(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), .net), TryGetListShardMap[(Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)) kullanarak rangeshardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap) [, .net) veya](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)listshardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) kullanabilirsiniz [. NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) veya GetShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) yöntemi.

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
}
```

### <a name="shard-map-administration-credentials"></a>Parça Haritası yönetim kimlik bilgileri

Parça haritalarını yöneten ve işleyen uygulamalar, bağlantıları yönlendirmek için parça haritaları kullananlardan farklıdır.

Parça haritalarını yönetmek için (parçalar, parça haritaları, parçalı eşlemeler, vb. ekleme veya değiştirme), **hem GSM veritabanında hem de bir parça olarak hizmet veren her veritabanında okuma/yazma ayrıcalıklarına sahip kimlik bilgilerini**kullanarak **Shardmapmanager** örneğini oluşturmanız gerekir. Kimlik bilgilerinin hem GSM hem de LSM içindeki tablolara yönelik yazma işlemlerinde izin verilmelidir ve Yeni parçalarda LSM tabloları oluşturmak için.  

[Elastik veritabanı istemci kitaplığına erişmek için kullanılan kimlik bilgilerine](sql-database-elastic-scale-manage-credentials.md)bakın.

### <a name="only-metadata-affected"></a>Yalnızca meta veriler etkilendi

**Shardmapmanager** verilerini doldurmak veya değiştirmek için kullanılan yöntemler, parçalarda depolanan kullanıcı verilerini değiştirmez. Örneğin, **Createshard**, **deleteshard**, **updatemapping**vb. gibi yöntemler yalnızca parça eşleme meta verilerini etkiler. Bunlar, parçaların içerdiği Kullanıcı verilerini kaldırmaz, eklemez veya değiştirmez. Bunun yerine, bu yöntemler gerçek veritabanlarını oluşturmak veya kaldırmak için gerçekleştirdiğiniz ayrı işlemlerle birlikte kullanılmak üzere tasarlanmıştır ya da parçalı bir ortamın yeniden dengelenmesi için satırları bir parçadan diğerine taşır.  (Elastik veritabanı araçları ile birlikte bulunan **bölünmüş birleştirme** Aracı, parçalar arasındaki gerçek veri hareketini organize etmek Için bu API 'lerin kullanımını sağlar.) Bkz. [elastik veritabanı bölünmüş birleştirme aracını kullanarak ölçeklendirme](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Verilere bağımlı yönlendirme

Parça eşleme Yöneticisi, uygulamaya özgü veri işlemlerini gerçekleştirmek üzere veritabanı bağlantıları gerektiren uygulamalarda kullanılır. Bu bağlantıların doğru veritabanıyla ilişkilendirilmesi gerekir. Bu, **verilere bağımlı yönlendirme**olarak bilinir. Bu uygulamalar için, GSM veritabanında salt okuma erişimi olan kimlik bilgilerini kullanarak bir parça eşleme Yöneticisi nesnesi oluşturun. Daha sonraki bağlantılara yönelik bireysel istekler, uygun parça veritabanına bağlanmak için gereken kimlik bilgilerini sağlar.

Bu uygulamaların ( **Shardmapmanager** kullanarak salt okuma kimlik bilgileriyle açıldığını) haritalar veya eşlemelerde değişiklik yapamadığını unutmayın. Bu gereksinimler için, daha önce anlatıldığı gibi daha yüksek ayrıcalıklı kimlik bilgilerini sağlayan, yönetim özel uygulamaları veya PowerShell betikleri oluşturun. [Elastik veritabanı istemci kitaplığına erişmek için kullanılan kimlik bilgilerine](sql-database-elastic-scale-manage-credentials.md)bakın.

Daha fazla bilgi için bkz. [verilere bağımlı yönlendirme](sql-database-elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Parça haritasını değiştirme

Parça Haritası farklı şekillerde değiştirilebilir. Aşağıdaki yöntemlerin hepsi, parçaları ve bunların eşlemelerini tanımlayan meta verileri değiştirir ancak parçalar içindeki verileri fiziksel olarak değiştirmezler veya gerçek veritabanlarını oluşturmaz veya silmez.  Parça haritasının aşağıda açıklanan işlemlerinden bazılarının, verileri fiziksel olarak taşıyan veya parçalara ayırma olarak hizmet veren ve çıkardığı yönetim eylemleri ile eşgüdümlü olması gerekebilir.

Bu yöntemler, parçalı veritabanı ortamınızda verilerin genel dağıtımını değiştirmek için kullanılabilen yapı taşları olarak birlikte çalışır.  

* Parçalama eklemek veya kaldırmak için: shardmap[(Java, .net](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap) [) sınıfının](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap) **createshard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard) [, .net) ve](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard) **deleteshard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) kullanın.
  
    Bu işlemlerin yürütülmesi için hedef parçayı temsil eden sunucu ve veritabanı zaten mevcut olmalıdır. Bu yöntemlerin, yalnızca parça eşlemesindeki meta verilerde veritabanlarının kendilerine etkisi yoktur.
* Parçalara eşlenmiş noktaları veya aralıkları oluşturmak veya kaldırmak için: RangeShardMapping[(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.net) sınıfının](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)bir **createrangemapping** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping) [, .net](https://docs.microsoft.com/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) kullanın, ve ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.net) sınıfının](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1) **createpointmapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)).
  
    Birçok farklı Punto veya Aralık aynı parça ile eşleştirilebilir. Bu yöntemler yalnızca meta verileri etkiler-parçalar zaten parçalar halinde mevcut olabilecek verileri etkilemez. Verilerin **DeleteMapping** işlemleriyle tutarlı olması için veritabanından kaldırılması gerekiyorsa, bu işlemleri ayrı olarak, ancak bu yöntemleri kullanmayla birlikte gerçekleştirirsiniz.  
* Varolan aralıkları ikiye bölmek veya bitişik aralıkları tek tek birleştirmek için: **Splitmapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.net](https://msdn.microsoft.com/library/azure/dn824205.aspx)) ve **mergemappings** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.net](https://msdn.microsoft.com/library/azure/dn824201.aspx)) kullanın.  
  
    Bölünmüş ve birleştirme işlemlerinin **, ana değerlerin eşlendiği parçayı değiştirmediğini**unutmayın. Bir bölme, mevcut bir aralığı iki parçaya ayırır, ancak her ikisini de aynı parçaya eşlenmiş olarak bırakır. Birleştirme, aynı parça ile eşlenmiş iki bitişik Aralık üzerinde çalışır ve bunları tek bir aralığa birleştirir.  Parçaların arasındaki noktaların veya aralıkların, gerçek veri hareketiyle birlikte **Updatemapping** kullanılarak koordine olması gerekir.  Hareket gerektiğinde veri hareketiyle parça eşleme değişikliklerini koordine etmek için elastik veritabanı araçlarının bir parçası olan **bölünmüş/birleştirme** hizmetini kullanabilirsiniz.
* Ayrı noktaları veya aralıkları farklı parçalara yeniden eşlemek (veya taşımak) için: **Updatemapping** kullanın ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Verilerin **Updatemapping** işlemleriyle tutarlı olması için bir parçadan diğerine taşınması gerekebilmesi için, bu hareketi ayrı ayrı gerçekleştirmeniz gerekir, ancak bu yöntemleri kullanma ile birlikte.

* Eşlemeleri çevrimiçi ve çevrimdışı olarak almak için: bir eşlemenin çevrimiçi durumunu denetlemek üzere **Markmappingoffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) ve **markmappingonline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) kullanın.
  
    Parça eşlemelerinde belirli işlemlere yalnızca bir eşleme, **Updatemapping** ve **DeleteMapping**de dahil olmak üzere "çevrimdışı" durumda olduğunda izin verilir. Bir eşleme çevrimdışıyken, bu eşlemede yer alan bir anahtara dayanan veriye bağlı bir istek bir hata döndürür. Buna ek olarak, bir Aralık ilk kez çevrimdışı yapıldığında, değişenlerin değiştiği sorgulara yönelik tutarsız veya tamamlanmamış sonuçların engellenmesi için etkilenen parça bağlantıları otomatik olarak sonlandırıldı.

Eşlemeler .net 'teki sabit nesnelerdir.  Değişiklik eşlemelerinin üzerindeki tüm yöntemler, kodunuzda bunlara yönelik başvuruları da geçersiz kılar. Bir eşlemenin durumunu değiştiren işlem dizilerini daha kolay hale getirmek için, bir eşlemeyi değiştiren tüm yöntemler yeni bir eşleme başvurusu döndürür, bu nedenle işlemler zincirlenebilir. Örneğin, 25 anahtarını içeren shardmap SM 'de var olan bir eşlemeyi silmek için aşağıdakileri yürütebilirsiniz:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Parça ekleme

Uygulamalar, zaten var olan bir parça eşlemesi için yeni anahtarlar veya anahtar aralıklarından beklenen verileri işlemek üzere genellikle yeni parçalar eklemesi gerekir. Örneğin, kiracı KIMLIĞI tarafından oluşturulmuş bir uygulamanın yeni bir kiracı için yeni bir parça sağlaması veya aylık veri parçaları, her yeni ay başlamadan önce sağlanan yeni bir parça gerektirebilir.

Yeni anahtar değerleri aralığı zaten varolan bir eşlemenin parçası değilse ve veri taşıma gerekli değilse, yeni parçayı eklemek ve yeni anahtarı veya aralığı bu parça ile ilişkilendirmek kolaydır. Yeni parçalar ekleme hakkında daha fazla bilgi için bkz. [yeni parça ekleme](sql-database-elastic-scale-add-a-shard.md).

Ancak, veri taşıma gerektiren senaryolar için, bölünmüş birleştirme aracı gerekli parça Haritası güncelleştirmeleriyle birlikte parçalar arasındaki veri hareketini yönetmek için gereklidir. Bölünmüş birleştirme aracını kullanma hakkında ayrıntılı bilgi için bkz. [split-Merge 'A genel bakış](sql-database-elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
