---
title: Entity Framework ile elastik veritabanı istemci kitaplığı kullanma
description: Kodlama veritabanları için elastik veritabanı istemci kitaplığı ve Entity Framework kullanma
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 4198b3a9213ed535c6649c50a20f2ff957d60c94
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823497"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Entity Framework ile elastik veritabanı istemci kitaplığı

Bu belgede, [elastik veritabanı araçlarıyla](sql-database-elastic-scale-introduction.md)tümleştirme için gereken bir Entity Framework uygulamasındaki değişiklikler gösterilmektedir. Odak, Entity Framework **Code First** yaklaşımıyla parça [eşleme yönetimi](sql-database-elastic-scale-shard-map-management.md) ve [verilere bağımlı yönlendirme](sql-database-elastic-scale-data-dependent-routing.md) oluşturmaktır. EF için [Code First-yeni veritabanı](https://msdn.microsoft.com/data/jj193542.aspx) öğreticisi, bu belgenin tamamında çalışan örnek işlevi görür. Bu belgeye eşlik eden örnek kod, Visual Studio Code örneklerindeki örnek kümesinin elastik veritabanı araçları ' nın bir parçasıdır.

## <a name="downloading-and-running-the-sample-code"></a>Örnek kodu indirme ve çalıştırma

Bu makaleye ait kodu indirmek için:

* Visual Studio 2012 veya üzeri gereklidir. 
* MSDN 'den [Azure SQL Entity Framework tümleştirme örneği Için elastik veritabanı araçları](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) 'nı indirin. Örneği, seçtiğiniz bir konuma ayıklayın.
* Visual Studio’yu çalıştırın. 
* Visual Studio 'da Dosya-> Aç proje/çözüm ' ı seçin. 
* **Projeyi aç** iletişim kutusunda, indirdiğiniz örneğe gidin ve örneği açmak Için **Entityframeworkcodefırst. sln** ' yi seçin. 

Örneği çalıştırmak için, Azure SQL veritabanı 'nda üç boş veritabanı oluşturmanız gerekir:

* Parça eşleme Yöneticisi veritabanı
* Parça 1 veritabanı
* Parça 2 veritabanı

Bu veritabanlarını oluşturduktan sonra, veritabanlarına bağlanmak için Azure SQL DB sunucunuzun adı, veritabanı adları ve kimlik bilgilerinizle **program.cs** içinde yer tutucuları girin. Visual Studio 'da çözümü oluşturun. Visual Studio, yapı sürecinin bir parçası olarak elastik veritabanı istemci kitaplığı, Entity Framework ve geçici hata işleme için gerekli NuGet paketlerini indirir. Çözümünüz için NuGet paketlerinin geri yüklenmesi etkinleştirildiğinden emin olun. Visual Studio Çözüm Gezgini çözüm dosyasına sağ tıklayarak bu ayarı etkinleştirebilirsiniz. 

## <a name="entity-framework-workflows"></a>Entity Framework iş akışları

Entity Framework geliştiriciler, uygulamalar oluşturmak ve uygulama nesneleri için kalıcılık sağlamak üzere aşağıdaki dört iş akışının birini kullanır:

* **Code First (yeni veritabanı)** : EF geliştiricisi, modeli uygulama kodunda oluşturur ve sonra veritabanını bundan oluşturur. 
* **Code First (mevcut veritabanı)** : geliştirici, var olan bir veritabanından model için uygulama kodu oluşturmasına izin verir.
* **Model First**: geliştirici, modeli EF tasarımcısında oluşturur ve EF veritabanını modelden oluşturur.
* **Database First**: geliştirici, modeli mevcut bir veritabanından ÇıKARMASı için EF araçları 'nı kullanır. 

Bu yaklaşımların hepsi, veritabanı bağlantılarını ve bir uygulamanın veritabanı şemasını saydam bir şekilde yönetmek için DbContext sınıfına bağımlıdır. DbContext temel sınıfındaki farklı oluşturucular bağlantı oluşturma, veritabanı önyükleme ve şema oluşturma üzerinde farklı denetim düzeylerine izin verir. Sorunlar öncelikle, EF tarafından belirtilen veritabanı bağlantı yönetiminin, elastik veritabanı istemci kitaplığı tarafından sunulan veri bağımlı yönlendirme arabirimlerinin bağlantı yönetimi özellikleri ile kesişmesinden kaynaklanır. 

## <a name="elastic-database-tools-assumptions"></a>Elastik veritabanı araçları varsayımları

Terim tanımları için bkz. [elastik veritabanı araçları sözlüğü](sql-database-elastic-scale-glossary.md).

Elastik veritabanı istemci kitaplığı ile, uygulama verilerinizin parçalarını parçalara izin verir olarak tanımlarsınız. Parçalara ayırma, parçalama anahtarı tarafından tanımlanır ve belirli veritabanlarına eşlenir. Uygulama, gereken sayıda veritabanına sahip olabilir ve mevcut iş gereksinimlerine göre yeterli kapasite veya performans sağlamak için kıslarını dağıtırsınız. Parçalı anahtar değerlerini veritabanlarına eşleme, elastik veritabanı istemci API 'Leri tarafından sunulan bir parça eşlemesi tarafından depolanır. Bu özellik, parça **eşleme yönetimi**veya Short için SMM olarak adlandırılır. Parça eşleme aynı zamanda bir parçalama anahtarı taşıyan istekler için veritabanı bağlantıları Aracısı olarak da kullanılır. Bu yetenek, **verilere bağımlı yönlendirme**olarak bilinir. 

Parça eşleme Yöneticisi, eş zamanlı kıardın yönetim işlemleri (örneğin, verileri bir parçadan diğerine yerleştirme gibi) meydana gelmekizin olduğunda oluşabilen verileri parçalara ayırır. Bunu yapmak için, istemci Kitaplığı Aracısı tarafından yönetilen parça haritaları bir uygulama için veritabanı bağlantılarını sağlar. Bu, parça yönetim işlemleri bağlantının oluşturulmasına izin ver ' i etkileyebilecek olan parça eşleme işlevinin bir veritabanı bağlantısını otomatik olarak sonlandırmasına olanak tanır. Bu yaklaşımın, veritabanı varlığını denetlemek için mevcut bir kaynaktan yeni bağlantılar oluşturma gibi bazı EF işlevleriyle tümleştirilmesi gerekir. Genel olarak, gözlemmize standart DbContext oluşturucuların yalnızca EF işi için güvenli bir şekilde kopyalanabilen kapalı veritabanı bağlantıları için güvenilir bir şekilde çalıştığı belirlenmiştir. Bunun yerine elastik veritabanının tasarım ilkesi yalnızca aracı tarafından açılan bağlantılara sahiptir. Biri, istemci kitaplığı tarafından bir bağlantının aracılı DbContext 'e teslim etmeden önce bu sorunu çözebileceğini düşünebilir. Bununla birlikte, bağlantıyı kapatarak ve yeniden açmak için EF 'e bağlı olarak, bir tane, kitaplık tarafından gerçekleştirilen doğrulama ve tutarlılık denetimlerini alır. Bununla birlikte, EF içindeki geçiş işlevleri, temel alınan veritabanı şemasını uygulamaya şeffaf bir şekilde yönetmek için bu bağlantıları kullanır. İdeal olarak, bu özellikleri hem elastik veritabanı istemci kitaplığından hem de aynı uygulamadaki EF ' den korur ve birleştirebilirsiniz. Aşağıdaki bölümde bu özellikler ve gereksinimler daha ayrıntılı olarak açıklanmaktadır. 

## <a name="requirements"></a>Gereksinimler

Hem elastik veritabanı istemci kitaplığıyla hem de Entity Framework API 'Leriyle çalışırken, aşağıdaki özellikleri sürdürmek istersiniz: 

* **Genişleme**: uygulamanın kapasite taleplerini için gereken şekilde, parçalı uygulamanın veri katmanından veritabanı eklemek veya kaldırmak için. Bu, veritabanlarının oluşturulması ve silinmesinin yanı sıra veritabanlarını yönetmek için elastik veritabanı parça eşleme Yöneticisi API 'Leri ve parçaların eşlemelerinde denetim sağlar. 
* **Tutarlılık**: uygulama parçalama kullanır ve istemci kitaplığının veri bağımlı yönlendirme özelliklerini kullanır. Bozulmaları veya yanlış sorgu sonuçlarının olmaması için bağlantılar parça eşleme Yöneticisi aracılığıyla aracılı olur. Bu, doğrulamayı ve tutarlılığı da korur.
* **Code First**: EF 'in kod ilk paradigmasını sağlamak için. Code First, uygulamadaki sınıflar, temel alınan veritabanı yapılarına saydam olarak eşlenir. Uygulama kodu, temel alınan veritabanı işleminde yer alan birçok yönü maskelemekte olan DbSets ile etkileşime girer.
* **Şema**: Entity Framework, ilk veritabanı şeması oluşturmayı ve sonraki şema gelişiminde geçişleri işler. Bu özellikleri koruyarak, veriler geliştikçe uygulamanızı uyarlanabilir kadar kolay. 

Aşağıdaki kılavuz, elastik veritabanı araçlarını kullanarak Code First uygulamalar için bu gereksinimlerin nasıl karşılamasını söyler. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>EF DbContext kullanarak verilere bağımlı yönlendirme

Entity Framework ile veritabanı bağlantıları genellikle **DbContext**'in alt sınıfları aracılığıyla yönetilir. **DbContext**'ten türeterek bu alt sınıfları oluşturun. Bu, uygulamanız için veritabanı tarafından desteklenen CLR nesnelerinin koleksiyonlarını uygulayan **Dbsets** 'leri tanımlayacaksınız. Verilere bağımlı yönlendirme bağlamında, diğer EF Code ilk uygulama senaryolarında tutmayan birçok yararlı özelliği tanımlayabilirsiniz: 

* Veritabanı zaten var ve elastik veritabanı parça eşlemesinde kayıtlı. 
* Uygulamanın şeması zaten veritabanına dağıtıldı (aşağıda açıklanmıştır). 
* Veritabanına yönelik verilere bağımlı yönlendirme bağlantıları, parça Haritası tarafından aracılı olarak sağlanır. 

**Dbbağlamlarını** genişleme için veriye bağımlı yönlendirmeyle tümleştirme:

1. Parça eşleme Yöneticisi 'nin elastik veritabanı istemci arabirimleri aracılığıyla fiziksel veritabanı bağlantıları oluşturun, 
2. Bağlantıyı **DbContext** alt sınıfı ile sarın
3. EF tarafında tüm işlemlerin da yapıldığından emin olmak için bağlantıyı **DbContext** temel sınıflarına geçirin. 

Aşağıdaki kod örneğinde bu yaklaşım gösterilmektedir. (Bu kod ayrıca, eşlik eden Visual Studio projesinde de bulunur)

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

    // C'tor for data-dependent routing. This call opens a validated connection 
    // routed to the proper shard by the shard map manager. 
    // Note that the base class c'tor call fails for an open connection
    // if migrations need to be done and SQL credentials are used. This is the reason for the 
    // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
    public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
        : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
        true /* contextOwnsConnection */)
    {
    }

    // Only static methods are allowed in calls into base class c'tors.
    private static DbConnection CreateDDRConnection(
    ShardMap shardMap, 
    T shardingKey, 
    string connectionStr)
    {
        // No initialization
        Database.SetInitializer<ElasticScaleContext<T>>(null);

        // Ask shard map to broker a validated connection for the given key
        SqlConnection conn = shardMap.OpenConnectionForKey<T>
                            (shardingKey, connectionStr, ConnectionOptions.Validate);
        return conn;
    }
```

## <a name="main-points"></a>Ana noktaları

* Yeni bir Oluşturucu DbContext alt sınıfının varsayılan oluşturucusunun yerini alır 
* Yeni Oluşturucu, elastik veritabanı istemci kitaplığı aracılığıyla verilere bağımlı yönlendirme için gereken bağımsız değişkenleri alır:
  
  * veri bağımlı yönlendirme arabirimlerine erişim için parça eşlemesi,
  * parçalamayı belirlemek için parçalı anahtar,
  * parça ile verilere bağımlı yönlendirme bağlantısı için kimlik bilgilerine sahip bir bağlantı dizesi. 
* Temel sınıf oluşturucusuna yapılan çağrı, veri bağımlı yönlendirme için gereken tüm adımları gerçekleştiren statik bir yönteme bir deturu alır. 
  
  * Bir açık bağlantı kurmak için parça eşlemesindeki elastik veritabanı istemci arabirimlerinin OpenConnectionForKey çağrısını kullanır.
  * Parça eşleme, belirtilen parçalı anahtar için parçalama tutan bir parça ile açık bağlantıyı oluşturur.
  * Bu açık bağlantı, EF 'in otomatik olarak yeni bir bağlantı oluşturmasına izin vermek yerine, bu bağlantının EF tarafından kullanılacağını belirtmek için DbContext 'in temel sınıf oluşturucusuna geri iletilir. Bu şekilde, bağlantı, parçalı harita yönetim işlemleri altında tutarlılığı güvence altına almak için elastik veritabanı istemci API 'SI tarafından etiketlendi.

Kodunuzda varsayılan Oluşturucu yerine DbContext alt sınıfınızın yeni oluşturucusunu kullanın. Örnek aşağıda verilmiştir: 

```csharp
// Create and save a new blog.

Console.Write("Enter a name for a new blog: "); 
var name = Console.ReadLine(); 

using (var db = new ElasticScaleContext<int>( 
                        sharding.ShardMap,  
                        tenantId1,  
                        connStrBldr.ConnectionString)) 
{ 
    var blog = new Blog { Name = name }; 
    db.Blogs.Add(blog); 
    db.SaveChanges(); 

    // Display all Blogs for tenant 1 
    var query = from b in db.Blogs 
                orderby b.Name 
                select b; 
    … 
}
```

Yeni Oluşturucu, **tenantid1**değeri tarafından tanımlanan parçalara yönelik verileri tutan parça bağlantısını açar. **Using** bloğundaki kod, **tenantid1**for the The kıard üzerinde EF kullanan bloglara yönelik **dbset** 'e erişmek için değiştirilmeden kalır. Bu, tüm veritabanı işlemlerinin artık **tenantid1** tutulduğu bir parça kapsamına gireceği şekilde, using bloğundaki kodun semantiğini değiştirir. Örneğin, blogların **Dbset** ÜZERINDEKI bir LINQ sorgusu yalnızca geçerli parça üzerinde depolanan blogları döndürür, ancak diğer parçalara depolanamazlar.  

#### <a name="transient-faults-handling"></a>Geçici hata işleme

Microsoft düzenleri & Yöntemler ekibi, [geçici hata Işleme uygulama bloğunu](https://msdn.microsoft.com/library/dn440719.aspx)yayımladı. Kitaplık, EF ile birlikte esnek ölçekli istemci kitaplığı ile birlikte kullanılır. Ancak, herhangi bir geçici özel durumun, yeni oluşturucunun geçici bir hatadan sonra kullanıldığından emin olmak için, tweaked kullandığınız oluşturucular kullanılarak herhangi bir yeni bağlantı denemesi yapılmaması için bir yere döndürüldüğünden emin olun. Aksi takdirde, doğru parça bağlantısı garanti edilmez ve parça eşlemesinde değişiklikler gerçekleşmediğinden bağlantının korunacağı hiçbir tür yoktur. 

Aşağıdaki kod örneği, bir SQL yeniden deneme ilkesinin yeni **DbContext** alt sınıf oluşturucuları etrafında nasıl kullanılabileceğini göstermektedir: 

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{ 
    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
        { 
                var blog = new Blog { Name = name }; 
                db.Blogs.Add(blog); 
                db.SaveChanges(); 
        … 
        } 
    }); 
```

Yukarıdaki koddaki **SqlDatabaseUtils. SqlRetryPolicy** , yeniden deneme sayısı 10 olan bir **SqlDatabaseTransientErrorDetectionStrategy** olarak tanımlanır ve yeniden denemeler arasında 5 saniye bekleyin. Bu yaklaşım, EF ve Kullanıcı tarafından başlatılan işlemlere yönelik kılavuza benzer (bkz. [yürütme stratejilerini yeniden deneme (EF6](https://msdn.microsoft.com/data/dn307226)ve sonraki sürümler). Her iki durumda da uygulama programının, geçici özel durumun döndürdüğü kapsamı denetlemeleri gerekir: işlemi yeniden açmak için veya (gösterildiği gibi) elastik veritabanı istemci kitaplığını kullanan uygun oluşturucudan bağlamı yeniden oluşturun.

Geçici özel durumların kapsamda nerede geri geldiğini denetleme gereksinimi, EF ile birlikte gelen yerleşik **SqlAzureExecutionStrategy** kullanımını da daha fazla kullanır. **SqlAzureExecutionStrategy** bir bağlantıyı yeniden açacağından, **openconnectionforkey** kullanmaz ve bu nedenle **openconnectionforkey** çağrısının bir parçası olarak gerçekleştirilen tüm doğrulamayı atlar. Bunun yerine, kod örneği aynı zamanda EF ile birlikte gelen yerleşik **Defaultexecutionstratejisini** kullanır. **SqlAzureExecutionStrategy**aksine, geçici hata işlemenin yeniden deneme ilkesiyle birlikte doğru bir şekilde çalışmaz. Yürütme ilkesi, **Elaun Scaledbconfiguration** sınıfında ayarlanır. Geçici özel durumlar oluşursa **SqlAzureExecutionStrategy** kullanmayı önerdiği Için **Defaultsqlexecutionstratejisini** kullanmamaya karar verdik. Bu, anlatıldığı şekilde yanlış davranışa neden olabilir. Farklı yeniden deneme ilkeleri ve EF hakkında daha fazla bilgi için bkz. [EF 'Te bağlantı dayanıklılığı](https://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Oluşturucuyu yeniden yazar

Yukarıdaki kod örnekleri, Entity Framework ile verilere bağımlı yönlendirmeyi kullanmak için uygulamanız için gereken varsayılan oluşturucuyu yeniden yazmayı gösterir. Aşağıdaki tablo diğer oluşturuculara bu yaklaşımı genelleştirir. 

| Geçerli Oluşturucu | Veriler için yeniden yazan Oluşturucu | Taban Oluşturucu | Notlar |
| --- | --- | --- | --- |
| MyContext () |Elayapışscalecontext (ShardMap, TKey) |DbContext (DbConnection, bool) |Bağlantı, parça haritasında bir işlev ve verilere bağımlı yönlendirme anahtarı olmalıdır. Otomatik bağlantı oluşturma ile EF ile geçiş yapmanız gerekir, bunun yerine bağlantıyı aracısına eklemek için parça haritasını kullanın. |
| MyContext (dize) |Elayapışscalecontext (ShardMap, TKey) |DbContext (DbConnection, bool) |Bağlantı, parça haritasının ve verilere bağımlı yönlendirme anahtarının bir işlevidir. Sabit bir veritabanı adı veya bağlantı dizesi, parça eşlemesi tarafından doğrulama tarafından doğrudan doğrulanarak çalışmaz. |
| MyContext (DbCompiledModel) |Elayapışscalecontext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |Sağlanan model ile verilen parça eşlemesi ve parçalara ayırma anahtarı için bağlantı oluşturulur. Derlenen model, temel citor 'a geçirilir. |
| MyContext (DbConnection, bool) |Elayapışscalecontext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |Bağlantı, parça haritalarından ve anahtardan çıkarsanmalıdır. Giriş olarak sağlanamaz (giriş, zaten parça haritasını ve anahtarı kullanmadıkça). Boole değeri geçirilir. |
| MyContext (dize, DbCompiledModel) |Elayapışscalecontext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |Bağlantı, parça haritalarından ve anahtardan çıkarsanmalıdır. Giriş olarak sağlanamaz (giriş, parça haritasını ve anahtarını kullanmadıkça). Derlenen model geçirilir. |
| MyContext (ObjectContext, bool) |Elayapışscalecontext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |Yeni oluşturucunun, bir giriş olarak geçirilen ObjectContext 'teki herhangi bir bağlantının elastik ölçeğe göre yönetilen bir bağlantıya yeniden yönlendirildiğinden emin olması gerekir. Objectbağlamların ayrıntılı bir tartışması, bu belgenin kapsamı dışındadır. |
| MyContext (DbConnection, DbCompiledModel, bool) |Elayapışscalecontext (ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection, DbCompiledModel, bool); |Bağlantı, parça haritalarından ve anahtardan çıkarsanmalıdır. Bağlantı, giriş olarak sağlanamaz (giriş, zaten parça haritasını ve anahtarı kullanmadıkça). Model ve Boole, temel sınıf oluşturucusuna geçirilir. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>EF geçişleri aracılığıyla parçalı şema dağıtımı

Otomatik şema yönetimi, Entity Framework tarafından sağlanmış bir kolaydır. Elastik veritabanı araçlarını kullanan uygulamalar bağlamında, veritabanları parçalı uygulamaya eklendiğinde şemayı otomatik olarak yeni oluşturulan parçalara sağlamak için bu özelliği koruyabilirsiniz. Birincil kullanım durumu, EF kullanarak parçalı uygulamalar için veri katmanındaki kapasiteyi artırmaya yöneliktir. EF 'in şema yönetimi özelliklerine bağlı olarak, EF üzerinde oluşturulmuş bir parçalı uygulamayla veritabanı yönetim çabaları azalır. 

EF geçişleri aracılığıyla şema dağıtımı, **açılmamış bağlantılarda**en iyi şekilde çalışmaktadır. Bu, elastik veritabanı istemci API 'SI tarafından belirtilen açık bağlantıyı kullanan veriye bağımlı yönlendirmeye yönelik senaryoya bağlıdır. Farklı bir farklılık, tutarlılık gereksinimidir: eşzamanlı parça eşleme düzenlemesi için tüm verilere bağımlı yönlendirme bağlantıları tutarlılığını sağlamak istediğinizde, yeni bir veritabanına yönelik ilk şema dağıtımıyla ilgili bir sorun değildir. daha önce parça eşlemesinde kayıtlı değildir, ancak henüz parçalara ayrılmalarına ayrılmamıştır. Bu nedenle, verilere bağımlı yönlendirmeye karşılık olarak bu senaryoya yönelik normal veritabanı bağlantılarına güvenebilirsiniz.  

Bu, EF geçişleri aracılığıyla şema dağıtımının, yeni veritabanının uygulamanın parça eşlemesinde bir parça olarak kaydettirilme ile sıkı bir şekilde bağlanmış olmasına yol açar. Bu, aşağıdaki önkoşulları kullanır: 

* Veritabanı zaten oluşturulmuş. 
* Veritabanı boş; Kullanıcı şeması yok ve Kullanıcı verisi yok.
* Veritabanına, verilere bağımlı yönlendirmeye yönelik elastik veritabanı istemci API 'Leri üzerinden henüz erişilemez. 

Bu önkoşulları yerine, şema dağıtımı için EF geçişlerini devre dışı bırakmak için düzenli olarak açılmamış bir **SqlConnection** oluşturabilirsiniz. Aşağıdaki kod örneğinde bu yaklaşım gösterilmektedir. 

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema 

public void RegisterNewShard(string server, string database, string connStr, int key) 
{ 

    Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

    SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
    connStrBldr.DataSource = server; 
    connStrBldr.InitialCatalog = database; 

    // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
    // This requires an un-opened connection. 
    using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
    { 
        // Run a query to engage EF migrations 
        (from b in db.Blogs 
            select b).Count(); 
    } 

    // Register the mapping of the tenant to the shard in the shard map. 
    // After this step, data-dependent routing on the shard map can be used 

    this.ShardMap.CreatePointMapping(key, shard); 
} 
```

Bu örnek, parça eşlemesinde parça kaydeden, şemayı EF geçişleri aracılığıyla dağıtan ve parça anahtarının bir eşlemesini parçaya depolayan **Registernewkıard** yöntemini gösterir. Giriş olarak bir SQL bağlantı dizesi alan **DbContext** alt sınıfının (örnekteki**Elakıscalecontext** ) bir yapıcısını kullanır. Aşağıdaki örnekte gösterildiği gibi, bu oluşturucunun kodu düz bir işlemdir: 

```csharp
// C'tor to deploy schema and migrations to a new shard 
protected internal ElasticScaleContext(string connectionString) 
    : base(SetInitializerForConnection(connectionString)) 
{ 
} 

// Only static methods are allowed in calls into base class c'tors 
private static string SetInitializerForConnection(string connectionString) 
{ 
    // You want existence checks so that the schema can get deployed 
    Database.SetInitializer<ElasticScaleContext<T>>( 
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

    return connectionString; 
} 
```

Bunlardan biri, temel sınıftan devralınan oluşturucunun sürümünü kullanmış olabilir. Ancak, bu kodun bağlama sırasında EF için varsayılan başlatıcısının kullanıldığından emin olması gerekir. Bu nedenle, bağlantı dizesiyle temel sınıf oluşturucusuna çağrılmadan önce statik metoda kısa bir tur. İşle ilgili Başlatıcı ayarlarının çakışmadığından emin olmak için parçaların kaydının farklı bir uygulama etki alanında veya işlemde çalışacağını unutmayın. 

## <a name="limitations"></a>Sınırlamalar

Bu belgede özetlenen yaklaşımların birkaç sınırlaması vardır: 

* **LocalDB** kullanan EF uygulamalarının, elastik veritabanı istemci kitaplığını kullanmadan önce düzenli bir SQL Server veritabanına geçirilmesi gerekir. **LocalDB**ile bir uygulamanın esnek ölçeğe sahip bir şekilde ölçeklendirilmesi mümkün değildir. Geliştirme, **LocalDB**kullanmaya devam edebilir. 
* Uygulamadaki tüm değişikliklerin, veritabanı şeması değişikliklerinin tüm parçalar üzerinde EF geçişlerinde olması gerekir. Bu belgenin örnek kodu bunun nasıl yapılacağını göstermez. Tüm parçaları yinelemek için Update-Database ConnectionString parametresiyle birlikte kullanmayı düşünün; veya-script seçeneğiyle Update-Database ' i kullanarak bekleyen geçiş için T-SQL betiğini ayıklayın ve T-SQL betiğini parçalara uygulayın.  
* İstek verildiğinde, tüm veritabanı işlemlerinin, istek tarafından sağlanan parçalı anahtar tarafından tanımlanan tek bir parça içinde bulunduğu varsayılır. Ancak, bu varsayım her zaman true olarak bulunmaz. Örneğin, bir parça anahtarı kullanılabilir hale getirmek mümkün olmadığında. Bu, istemci kitaplığı, çeşitli parçaları sorgulamak için bağlantı soyutlaması uygulayan **Multishardquery** sınıfını sağlar. EF ile birlikte **Multishardquery** kullanmayı öğrenmek bu belgenin kapsamının ötesinde

## <a name="conclusion"></a>Sonuç

Bu belgede özetlenen adımlarda, EF uygulamaları, EF uygulamasında kullanılan **DbContext** alt sınıflarının yeniden düzenleme oluşturucularından veriye bağımlı yönlendirme için esnek veritabanı istemci kitaplığının yeteneklerini kullanabilir. Bu, **DbContext** sınıflarının zaten mevcut olduğu yerlerde gerekli olan değişiklikleri sınırlandırır. Ayrıca, EF uygulamaları, parça haritadaki yeni parçalar ve eşlemeler kaydıyla gerekli EF geçişlerini çağıran adımları birleştirerek otomatik şema dağıtımından faydalanabilir. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
