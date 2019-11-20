---
title: Verilere bağımlı yönlendirme
description: Azure SQL veritabanı 'nda parçalı veritabanlarının bir özelliği olan verilere bağımlı yönlendirme için .NET uygulamalarında ShardMapManager sınıfını kullanma
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
ms.openlocfilehash: e5212ba7ed349f3596047fc0c027829b8667ddc5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823675"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Bir sorguyu uygun veritabanına yönlendirmek için veriye bağımlı yönlendirmeyi kullanma

**Veriye bağımlı yönlendirme** , isteği uygun bir veritabanına yönlendirmek için bir sorgudaki verileri kullanma olanağıdır. Verilere bağımlı yönlendirme, parçalı veritabanlarıyla çalışırken temel bir modeldir. İstek bağlamı, özellikle de parçalama anahtarı sorgunun bir parçası değilse isteği yönlendirmek için de kullanılabilir. Veriye bağlı yönlendirmeyi kullanan bir uygulamadaki her bir özel sorgu veya işleme, istek başına tek bir veritabanına erişmek için kısıtlanır. Azure SQL veritabanı elastik araçları için bu yönlendirme, **Shardmapmanager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) sınıfıyla gerçekleştirilir.

Uygulamanın, parçalı ortamdaki farklı veri dilimleriyle ilişkili çeşitli bağlantı dizelerini veya DB konumlarını izlemesi gerekmez. Bunun yerine, parça [eşleme Yöneticisi](sql-database-elastic-scale-shard-map-management.md) , verileri, parça haritadaki verilere ve uygulama isteğinin hedefi olan parçalı anahtar değerine göre gerektiğinde doğru veritabanlarına açar. Anahtar genellikle *customer_id*, *tenant_id*, *date_key*veya veritabanı isteğinin temel bir parametresi olan başka bir özel tanımlayıcı olur.

Daha fazla bilgi için bkz. [veri bağımlı yönlendirme ile SQL Server genişletme](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>İstemci kitaplığını indirin

İndirmek için:

* Kitaplığın Java sürümü, bkz. [Maven merkezi deposu](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Kitaplığın .NET sürümü, bkz. [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Veri bağımlı yönlendirme uygulamasında bir ShardMapManager kullanma

Uygulamalar, başlatma sırasında **Getsqlshardmapmanager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) fabrika çağrısını kullanarak **shardmapmanager 'ın** örneğini oluşturmalıdır. Bu örnekte, hem **Shardmapmanager** hem de içerdiği belirli bir **shardmap** başlatılır. Bu örnek, GetSqlShardMapManager ve GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.net](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))) yöntemlerini gösterir.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Parça haritasını almak için mümkün olan en düşük ayrıcalık kimlik bilgilerini kullanın

Bir uygulama parça haritasını işlemediği takdirde, Factory yönteminde kullanılan kimlik bilgileri, **genel parça eşleme** veritabanında salt okuma izinlerine sahip olmalıdır. Bu kimlik bilgileri genellikle parça eşleme yöneticisiyle bağlantıları açmak için kullanılan kimlik bilgilerinden farklıdır. Ayrıca bkz. [elastik veritabanı istemci kitaplığına erişmek için kullanılan kimlik bilgileri](sql-database-elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>OpenConnectionForKey metodunu çağırın

**Shardmap. OpenConnectionForKey yöntemi** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)), **anahtar** parametresinin değerine göre uygun veritabanına komut verme için hazır bir bağlantı döndürür. Parça bilgileri, uygulama tarafından **Shardmapmanager**tarafından önbelleğe alınır, bu nedenle bu Istekler genellikle **küresel parça eşleme** veritabanına karşı bir veritabanı aramasını içermez.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* **Anahtar** parametresi, istek için uygun veritabanını tespit etmek üzere parça eşlemesinde bir arama anahtarı olarak kullanılır.
* **ConnectionString** , istenen bağlantının yalnızca Kullanıcı kimlik bilgilerini geçirmek için kullanılır. Bu *ConnectionString* 'e veritabanı adı veya sunucu adı dahil değildir çünkü yöntem, **shardmap**'i kullanarak veritabanını ve sunucuyu belirler.
* **ConnectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)), **ConnectionOptions** olarak ayarlanmalıdır. parça haritalarının değiştirebildiği bir ortam ve satırların bölünmüş veya birleştirme işlemlerinin sonucu olarak diğer veritabanlarına taşınabileceğini doğrulayın. Bu doğrulama, bağlantı uygulamaya teslim edilmeden önce hedef veritabanındaki (Global parça eşlemesinde değil) yerel parça eşlemesine kısa bir sorgu içerir.

Yerel parça eşlemesine yönelik doğrulama başarısız olursa (önbelleğin yanlış olduğunu gösteren), parça eşleme Yöneticisi genel parça haritasını sorgular, arama için yeni doğru değeri elde etmek, önbelleği güncelleştirmek ve uygun veritabanı bağlantısını almak ve döndürmek için sorgular .

**ConnectionOptions. None** yalnızca bir uygulama çevrimiçi olduğunda parça eşleme değişiklikleri beklenmiyorsa desteklenmez. Bu durumda, önbelleğe alınan değerlerin her zaman doğru olduğu kabul edilebilir ve hedef veritabanına yönelik ek gidiş dönüş doğrulama çağrısı güvenle atlanabilir. Bu, veritabanı trafiğini azaltır. **ConnectionOptions** Ayrıca, bir süre boyunca parça değişikliklerinin beklenip beklenmediğini göstermek için bir yapılandırma dosyasındaki bir değer aracılığıyla ayarlanabilir.  

Bu örnek, **customershardmap**adlı bir **shardmap** nesnesi kullanılarak **MüşteriNo**tamsayı anahtarının değerini kullanır.  

```Java
int customerId = 12345;
int productId = 4321;
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

// Connect to the shard for that customer ID. No need to call a SqlConnection
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
{
    // Execute a simple command.
    SqlCommand cmd = conn.CreateCommand();
    cmd.CommandText = @"UPDATE Sales.Customer
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID";

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
    cmd.ExecuteNonQuery();
}  
```

**Openconnectionforkey** yöntemi, doğru veritabanına önceden açık olan yeni bir bağlantı döndürür. Bu şekilde kullanılan bağlantılar, bağlantı havuzlarından tamamen faydalanır.

**Openconnectionforkeyasync yöntemi** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)), uygulamanız zaman uyumsuz programlama kullanıyorsa de kullanılabilir.

## <a name="integrating-with-transient-fault-handling"></a>Geçici hata işlemeyle tümleştirme

Bulutta veri erişimi uygulamaları geliştirmedeki en iyi yöntem, geçici hataların uygulama tarafından yakalanıp bir hata oluşturmadan önce işlemlerin birkaç kez yeniden denenmemesini sağlamaktır. Bulut uygulamaları için geçici hata işleme, geçici hata Işlemede ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.net](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))) ele alınmıştır.

Geçici hata işleme, verilere bağımlı yönlendirme düzeniyle doğal olarak bir arada bulunabilir. Temel gereksinim, verilere bağımlı yönlendirme bağlantısını elde eden **using** bloğu dahil tüm veri erişim isteğini yeniden denemelidir. Yukarıdaki örnek aşağıdaki şekilde yeniden yazılabilir.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Örnek-geçici hata işleme ile verilere bağımlı yönlendirme

```Java
int customerId = 12345;
int productId = 4321;
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

Configuration.SqlRetryPolicy.ExecuteAction(() =&gt;
{
    // Connect to the shard for a customer ID.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
    {
        // Execute a simple command
        SqlCommand cmd = conn.CreateCommand();

        cmd.CommandText = @"UPDATE Sales.Customer
                            SET PersonID = @newPersonID
                            WHERE CustomerID = @customerID";

        cmd.Parameters.AddWithValue("@customerID", customerId);
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
        cmd.ExecuteNonQuery();

        Console.WriteLine("Update completed");
    }
});
```

Geçici hata işleme uygulamak için gereken paketler, elastik veritabanı örnek uygulaması oluşturduğunuzda otomatik olarak indirilir.

## <a name="transactional-consistency"></a>İşlem tutarlılığı

İşlemsel özellikler, bir parça için yerel olan tüm işlemler için garanti edilir. Örneğin, veriye bağımlı yönlendirme aracılığıyla gönderilen işlemler, bağlantı için hedef parçanın kapsamı içinde yürütülür. Şu anda, birden çok bağlantıyı bir işlem içine listelemek için hiçbir özellik sağlanmamıştır ve bu nedenle parçalar arasında gerçekleştirilen işlemler için işlem garantisi yoktur.

## <a name="next-steps"></a>Sonraki adımlar

Parçayı ayırmak veya parçayı yeniden eklemek için bkz [. parça eşleme sorunlarını onarmak Için RecoveryManager sınıfını kullanma](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]