---
title: RLS ve elastik veritabanı araçlarıyla çok kiracılı uygulamalar
description: Son derece ölçeklenebilir bir veri katmanıyla bir uygulama oluşturmak için, satır düzeyi güvenlik ile elastik veritabanı araçlarını kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: a5fe5d6d4076c5d82d33737d05bb95ede0a89c00
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822027"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Elastik veritabanı araçları ve satır düzeyi güvenliği olan çok kiracılı uygulamalar

Azure SQL veritabanı ile çok kiracılı bir uygulamanın veri katmanını ölçeklendirmeye olanak tanımak için [elastik veritabanı araçları](sql-database-elastic-scale-get-started.md) ve [satır DÜZEYI güvenlik (RLS)][rls] birlikte çalışması. Bu teknolojiler birlikte, yüksek oranda ölçeklenebilir bir veri katmanına sahip bir uygulama oluşturmanıza yardımcı olur. Veri katmanı çok kiracılı parçaları destekler ve **ADO.NET SqlClient** veya **Entity Framework**kullanır. Daha fazla bilgi için bkz. [Azure SQL veritabanı Ile çok kiracılı SaaS uygulamaları Için tasarım desenleri](saas-tenancy-app-design-patterns.md).

- **Elastik veritabanı araçları** , geliştiricilerin .net kitaplıklarını ve Azure hizmet şablonlarını kullanarak veri katmanını standart parçalama uygulamalarıyla ölçeklendirmesine olanak tanır. [Elastik veritabanı Istemci kitaplığını][s-d-elastic-database-client-library] kullanarak parçaların yönetilmesi, genellikle parçalama ile ilişkili altyapısal görevlerinin çoğunun otomatikleştirilmesine ve kolaylaştırılmasına yardımcı olur.
- **Satır düzeyi güvenlik** , geliştiricilerin aynı veritabanında birden fazla kiracıya güvenli bir şekilde veri depolamasını sağlar. RLS güvenlik ilkeleri bir sorgu yürüten kiracıya ait olmayan satırları filtreler. Veritabanı içindeki filtre mantığını merkezileştirirken bakım basitleşilir ve güvenlik hatası riski azalır. Güvenliği zorlamak için tüm istemci koduna bağlı olan alternatif risklidir.

Bir uygulama, bu özellikleri birlikte kullanarak, birden fazla kiracıya ait verileri aynı parça veritabanında depolayabilirler. Kiracılar bir veritabanını paylaşıyorsa Kiracı başına daha düşük bir maliyet maliyeti. Yine de aynı uygulama, kendi Premium kiracılarını kendi ayrılmış tek kiracılı parça için ödeme seçeneğiyle sunabileceği bir uygulamadır. Tek kiracılı yalıtımın bir avantajı, performans garantisi sağlar. Tek kiracılı bir veritabanında, kaynaklar için rekabet eden başka bir kiracı yoktur.

Amaç, belirli bir kiracıyı doğru parça veritabanına otomatik olarak bağlamak için elastik veritabanı istemci kitaplığı [verilerine bağımlı yönlendirme](sql-database-elastic-scale-data-dependent-routing.md) API 'lerini kullanmaktır. Yalnızca bir parça, belirtilen kiracı için belirli bir Tenantıd değeri içeriyor. Tenantıd, *parça anahtarıdır*. Bağlantı kurulduktan sonra, veritabanı içindeki bir RLS güvenlik ilkesi, belirtilen kiracının yalnızca kendi Tenantıd 'sini içeren veri satırlarına erişebilmesini sağlar.

> [!NOTE]
> Kiracı tanımlayıcısı birden fazla sütundan oluşabilir. Bu tartışmada kolaylık olması için tek sütunlu bir Tenantıd 'nin olduğunu varsayalım.

![Blog uygulama mimarisi][1]

## <a name="download-the-sample-project"></a>Örnek projeyi indirin

### <a name="prerequisites"></a>Ön koşullar

- Visual Studio 'Yu kullanma (2012 veya üzeri)
- Üç Azure SQL veritabanı oluşturma
- Örnek projeyi indir: [Azure SQL Için elastik veritabanı araçları-çok kiracılı](https://go.microsoft.com/?linkid=9888163) parçalar
  - Veritabanlarınıza ilişkin bilgileri **program.cs** başlangıcında girin

Bu proje, çok kiracılı parça veritabanları için destek ekleyerek [Azure SQL Entity Framework tümleştirme Için elastik veritabanı araçları](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) 'nda açıklanan birini genişletir. Proje, blog ve gönderi oluşturmak için basit bir konsol uygulaması oluşturur. Proje dört kiracı, artı iki adet çok kiracılı parça veritabanı içerir. Bu yapılandırma, önceki diyagramda gösterilmiştir.

Uygulamayı derleyin ve çalıştırın. Bu çalıştırma, elastik veritabanı araçları ' parça eşleme Yöneticisi 'ni önyükleme ve aşağıdaki testleri gerçekleştirir:

1. Entity Framework ve LINQ kullanarak, yeni bir blog oluşturun ve ardından her kiracı için tüm blogları görüntüleyin
2. ADO.NET SqlClient kullanarak bir kiracının tüm blogları görüntüleyin
3. Bir hatanın yapıldığını doğrulamak için yanlış kiracı için blog eklemeyi deneyin

RLS 'nin parça veritabanlarında henüz etkinleştirilmediği için, bu testlerin her birinde bir sorun ortaya çıkaran için bu testlerin her biri bir sorunla karşılaşırsınız: kiracılar bu dosyalara ait olmayan blogları görebilir ve uygulamanın yanlış kiracı için blog eklemesini önlenemez. Bu makalenin geri kalanında, bu sorunların nasıl çözümleneceği RLS ile kiracı yalıtımı zorlanarak açıklanmaktadır. İki adım vardır:

1. **Uygulama katmanı**: bir bağlantı açtıktan sonra, oturum\_bağlamındaki geçerli tenantıd 'yi her zaman ayarlamak için uygulama kodunu değiştirin. Örnek proje zaten Tenantıd 'yi bu şekilde ayarlıyor.
2. **Veri katmanı**: oturum\_bağlamında depolanan tenantıd 'ye göre satırları filtrelemek için her parça VERITABANıNDA bir RLS güvenlik ilkesi oluşturun. Parça veritabanlarınızın her biri için bir ilke oluşturun, aksi halde çok kiracılı parçaların satırları filtrelenmez.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. uygulama katmanı: oturum\_BAĞLAMıNDA Tenantıd ayarlayın

İlk olarak, elastik veritabanı istemci kitaplığının veri bağımlı yönlendirme API 'Lerini kullanarak bir parça veritabanına bağlanırsınız. Uygulamanın, bağlantıyı hangi Tenantıd 'nin kullandığını söylemeye devam etmelidir. Tenantıd, RLS güvenlik ilkesine, hangi satırların diğer kiracılara ait olarak filtrelenmesini gerektiğini söyler. Geçerli Tenantıd 'yi bağlantının [oturum\_bağlamına](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) depolayın.

OTURUM\_BAĞLAMı için bir alternatif, [bağlam\_bilgisini](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql)kullanmaktır. Ancak oturum\_BAĞLAMı daha iyi bir seçenektir. OTURUM\_BAĞLAMı kullanımı daha kolaydır, varsayılan olarak NULL değerini döndürür ve anahtar-değer çiftlerini destekler.

### <a name="entity-framework"></a>Entity Framework

Entity Framework kullanan uygulamalar için en kolay yaklaşım, oturum\_BAĞLAMıNı, [EF DbContext kullanarak veriye bağımlı yönlendirmede](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext)açıklanan elakıscalecontext geçersiz kılma içinde ayarlamaya yöneliktir. OTURUM\_BAĞLAMıNDAKI Tenantıd 'yi bağlantı için belirtilen shardingKey olarak ayarlayan bir SqlCommand oluşturun ve yürütün. Ardından, bağlantıyı verilere bağımlı yönlendirme aracılığıyla aracılı olarak döndürün. Bu şekilde, oturum\_BAĞLAMıNı ayarlamak için yalnızca bir kez kod yazmanız gerekir.

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}
// ...
```

Artık, her Elaun Scalecontext çağrıldığında, oturum\_BAĞLAMı belirtilen Tenantıd ile otomatik olarak ayarlanır:

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);
        foreach (var item in query)
        {
            Console.WriteLine(item.Name);
        }
    }
});
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

ADO.NET SqlClient kullanan uygulamalar için, ShardMap. OpenConnectionForKey yöntemi etrafında bir sarmalayıcı işlevi oluşturun. Sarmalayıcı bir bağlantı döndürmeden önce oturum\_BAĞLAMıNDAKI Tenantıd 'yi geçerli Tenantıd 'ye otomatik olarak ayarlamış. OTURUM\_BAĞLAMıNıN her zaman ayarlandığından emin olmak için, yalnızca bu sarmalayıcı işlevini kullanarak bağlantı açmanız gerekir.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. veri katmanı: satır düzeyi güvenlik ilkesi oluşturma

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Her kiracının erişebileceği satırları filtrelemek için bir güvenlik ilkesi oluşturun

Uygulama sorgulanmadan önce geçerli Tenantıd ile oturum\_BAĞLAMı ayarlamadığına göre, bir RLS güvenlik ilkesi sorguları filtreleyebilir ve farklı bir Tenantıd 'ye sahip satırları hariç tutabilir.

RLS, Transact-SQL ' t a uygulanır. Kullanıcı tanımlı bir işlev, erişim mantığını tanımlar ve bir güvenlik ilkesi bu işlevi herhangi bir sayıda tabloya bağlar. Bu proje için:

1. İşlevi uygulamanın veritabanına bağlı olduğunu ve oturum\_BAĞLAMı 'nda depolanan Tenantıd 'nin belirli bir satırın Tenantıd ile eşleştiğini doğrular.
    - Uygulama, başka bir SQL kullanıcısı yerine bağlı.

2. FILTRE koşulu, Tenantıd filtresini karşılayan satırların SELECT, UPDATE ve DELETE sorguları için geçişine olanak sağlar.
    - Bir blok koşulu, filtrenin eklenmesini veya güncelleştirilmesini başarısız olan satırları engeller.
    - OTURUM\_BAĞLAMı ayarlanmamışsa, işlev NULL değerini döndürür ve hiçbir satır görünür değil veya eklenebilir.

Tüm parçalarda RLS 'yi etkinleştirmek için, Visual Studio (SSDT), SSMS veya projeye dahil olan PowerShell betiğini kullanarak aşağıdaki T-SQL ' i yürütün. Ya da [elastik veritabanı işleri](elastic-jobs-overview.md)kullanıyorsanız, bu T-SQL ' i tüm parçalar üzerinde otomatik hale getirebilirsiniz.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> Karmaşık bir projede, koşulu yüzlerce tablo üzerinde eklemeniz gerekebilir ve bu da sıkıcı olabilir. Otomatik olarak bir güvenlik ilkesi üreten ve bir şemadaki tüm tablolara bir koşul ekleyen bir yardımcı saklı yordam vardır. Daha fazla bilgi için, [tüm tablolarda (blog) satır düzeyi güvenliği uygulama](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-to-all-tables-helper-script)başlıklı blog gönderisine bakın.

Şimdi örnek uygulamayı yeniden çalıştırırsanız, kiracılar yalnızca bunlara ait olan satırları görür. Ayrıca, uygulama, şu anda parça veritabanına bağlı olan kiracılara ait olan satırları ekleyemiyor. Ayrıca, uygulama, tarafından görebileceğiniz herhangi bir satırda Tenantıd 'yi güncelleştiremez. Uygulama herhangi birini yapmayı denerse, bir DbUpdateException tetiklenir.

Daha sonra yeni bir tablo eklerseniz, yeni tabloya FILTRE ve engelleme koşulları eklemek için güvenlik ilkesini DEĞIŞTIRIN.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Tenantıd ekleme için otomatik olarak doldurmak üzere varsayılan kısıtlamalar ekleyin

Tenan\_TID 'yi otomatik olarak doldurmak için her tabloya varsayılan bir kısıtlama koyabilirsiniz. Aşağıda bir örnek verilmiştir.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

Artık uygulamanın satır eklerken bir Tenantıd belirtmesi gerekmez:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Bir Entity Framework projesi için varsayılan kısıtlamalar kullanıyorsanız, EF veri modelinize Tenantıd sütununu *dahil etmeniz* önerilir. Bu öneri Entity Framework sorgularının, oturum\_BAĞLAMıNı kullanan T-SQL ' de oluşturulan varsayılan kısıtlamaları geçersiz kılan varsayılan değerleri otomatik olarak sağlamalarından kaynaklanır.
> Örnek projede varsayılan kısıtlamaları kullanmak için, örneğin Tenantıd 'yi DataClasses.cs 'den kaldırmanız gerekir (ve Paket Yöneticisi konsolunda Add-Migration ' ı çalıştırmalısınız) ve alanın veritabanı tablolarında mevcut olduğundan emin olmak için T-SQL ' i kullanın. Bu şekilde, EF veri eklenirken yanlış varsayılan değerleri otomatik olarak sağlar.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>Seçim *Süper kullanıcının* tüm satırlara erişmesini sağlama

Bazı uygulamalar, tüm satırlara erişebilen bir *Süper Kullanıcı* oluşturmak isteyebilir. Bir süper kullanıcı tüm parçalardaki tüm kiracılar üzerinde raporlamayı etkinleştirebilir. Ya da bir süper Kullanıcı, kiracı satırlarını veritabanları arasında taşımayı kapsayan parçalar üzerinde bölünmüş birleştirme işlemleri gerçekleştirebilir.

Bir süper kullanıcıyı etkinleştirmek için, her parça veritabanında yeni bir SQL kullanıcısı (Bu örnekte`superuser`) oluşturun. Ardından güvenlik ilkesini, bu kullanıcının tüm satırlara erişmesine izin veren yeni bir koşul işleviyle değiştirin. Böyle bir işlev daha sonra verilir.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Bakım

- **Yeni parçalar ekleme**: yeni parçalar üzerinde RLS 'yi etkinleştirmek için T-SQL betiğini yürütün, aksi takdirde bu parçaların sorguları filtrelenmez.
- **Yeni tablolar ekleniyor**: her yeni tablo oluşturulduğunda tüm parçalardan GÜVENLIK ilkesine FILTRE ve engelleme koşulu ekleyin. Aksi halde yeni tablodaki sorgular filtrelenmez. Bu ekleme, [Yeni oluşturulan tablolara (blog) otomatik olarak satır düzeyi güvenlik uygulama](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx)bölümünde açıklandığı gıbı bir DDL tetikleyicisi kullanılarak otomatikleştirilebilir.

## <a name="summary"></a>Özet

Esnek veritabanı araçları ve satır düzeyi güvenliği, bir uygulamanın veri katmanını hem çok kiracılı hem de tek kiracılı parçalar için destek ile ölçeklendirmek için birlikte kullanılabilir. Çok kiracılı parçalar, verileri daha verimli bir şekilde depolamak için kullanılabilir. Bu verimlilik, çok sayıda kiracının yalnızca birkaç satırlık veri olduğu durumlarda oluşur. Tek kiracılı parçalar, daha sıkı performans ve yalıtım gereksinimlerine sahip Premium kiracılar destekleyebilir. Daha fazla bilgi için bkz. [satır düzeyi güvenlik başvurusu][rls].

## <a name="additional-resources"></a>Ek kaynaklar

- [Azure elastik havuzu nedir?](sql-database-elastic-pool.md)
- [Azure SQL Veritabanı ile ölçek genişletme](sql-database-elastic-scale-introduction.md)
- [Azure SQL Veritabanı ile Çok Kiracılı SaaS Uygulamaları için Tasarım Desenleri](saas-tenancy-app-design-patterns.md)
- [Azure AD ve OpenID Connect kullanarak çok müşterili uygulamalarda kimlik doğrulama](../guidance/guidance-multitenant-identity-authenticate.md)
- [Tailspin Surveys uygulaması](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Sorular ve özellik Istekleri

Sorular için [SQL veritabanı forumundan](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)bizimle iletişim kurun. Ve [SQL veritabanı geri bildirim forumuna](https://feedback.azure.com/forums/217321-sql-database/)herhangi bir özellik isteği ekleyin.

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md
