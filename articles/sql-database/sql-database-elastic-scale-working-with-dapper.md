---
title: Kaber ile elastik veritabanı istemci kitaplığı kullanma
description: Kaber ile elastik veritabanı istemci kitaplığı kullanma.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 83d24d45d7628a2e02068c8757fa6568d6d3fc37
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823467"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Kaber ile elastik veritabanı istemci kitaplığı kullanma
Bu belge, uygulamalar oluşturmak için daber 'yi kullanan geliştiricilere yöneliktir, ancak aynı zamanda veri katmanını ölçeklendirmek için parçalara ayırma uygulayan uygulamalar oluşturmak üzere [elastik veritabanı](sql-database-elastic-scale-introduction.md) araçlarını benimseyin.  Bu belge, elastik veritabanı araçlarıyla tümleştirme için gerekli olan paber tabanlı uygulamalardaki değişiklikleri gösterir. Odaklanmamız, elastik veritabanı oluşturma yönetimini ve veri odaklı yönlendirmeyi kaber ile oluşturmaya yönelik. 

**Örnek kod**: [Azure SQL veritabanı için elastik veritabanı araçları-paber tümleştirmesi](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Azure SQL veritabanı için elastik veritabanı istemci kitaplığı ile **daber** ve **dapperextensions** 'ın tümleştirilmesi kolaydır. Uygulamalarınız, yeni [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) nesnelerinin oluşturulmasını ve açılmasını, [Istemci kitaplığından](https://msdn.microsoft.com/library/azure/dn765902.aspx) [openconnectionforkey](https://msdn.microsoft.com/library/azure/dn807226.aspx) çağrısını kullanacak şekilde değiştirerek veriye bağlı yönlendirmeyi kullanabilir. Bu, uygulamanızdaki değişiklikleri yalnızca yeni bağlantıların oluşturulduğu ve açıldığı yerde sınırlandırır. 

## <a name="dapper-overview"></a>Kaber genel bakış
**Kaber** , nesne ilişkisel bir eşleştiricisidir. Uygulamanızdaki .NET nesnelerini bir ilişkisel veritabanına (ve tam tersi) eşler. Örnek kodun ilk bölümü, esnek veritabanı istemci kitaplığını, paber tabanlı uygulamalarla nasıl tümleştirebileceğinizi gösterir. Örnek kodun ikinci bölümü, hem daber hem de DapperExtensions kullanılırken nasıl tümleştirileceğini gösterir.  

Paber 'deki Eşleyici işlevselliği, yürütme veya veritabanını sorgulama için T-SQL deyimlerinin gönderilmesini kolaylaştıran Veritabanı bağlantılarında uzantı yöntemleri sağlar. Örneğin, Korber, **yürütme** çağrıları için .net nesneleriniz ile SQL deyimlerinin parametreleri arasında eşlemeyi kolaylaştırır veya katin **sorgu** çağrılarını kullanarak SQL sorgularınızın sonuçlarını .net Objects 'e tüketebilir. 

DapperExtensions kullanırken, artık SQL deyimlerini sağlamanız gerekmez. {1 & gt; veritabanı & lt; 1} **bağlantı gibi uzantılar** Yöntemler arka planda SQL deyimlerini oluşturur.

Daber ve ayrıca Daperextensions 'ın başka bir avantajı da uygulamanın veritabanı bağlantısı oluşturulmasını denetliyorsa. Bu, veritabanı bağlantılarını, veritabanlarının veritabanlarına yönelik olarak eşleşmesini temel alan elastik veritabanı istemci kitaplığıyla etkileşime geçmesini sağlar.

Kaber derlemelerini almak için bkz. [PBER dot net](https://www.nuget.org/packages/Dapper/). Daber uzantıları için bkz. [Dapperextensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Elastik veritabanı istemci kitaplığına hızlı bakış
Elastik veritabanı istemci kitaplığı ile, uygulama verilerinizin parçalarını *parçalarım*olarak tanımlar, bunları veritabanlarına eşleyin ve bunları *parçalara*ayırarak belirleyin. İhtiyacınız olan çok sayıda veritabanınız olabilir ve bu veritabanlarına ait kıardlarınızı dağıtabilirsiniz. Parçalı anahtar değerlerini veritabanlarına eşleme, kitaplığın API 'Leri tarafından sunulan bir parça eşlemesi tarafından depolanır. Bu yetenek, parça **eşleme yönetimi**olarak adlandırılır. Parça eşleme aynı zamanda bir parçalama anahtarı taşıyan istekler için veritabanı bağlantıları Aracısı olarak da kullanılır. Bu yetenek, **verilere bağımlı yönlendirme**olarak adlandırılır.

![Parça haritaları ve verilere bağımlı yönlendirme][1]

Parça eşleme Yöneticisi, kullanıcıların eş zamanlı kıardalım yönetim işlemleri veritabanlarında meydana gelen verileri tutarsız görünümlerden, parçalı verilerle korur. Bunu yapmak için, parça haritaları, kitaplıkla oluşturulmuş bir uygulama için veritabanı bağlantılarını Broker. Parça yönetimi işlemleri, parçalamayı etkileyebilecek şekilde, parça eşleme işlevinin bir veritabanı bağlantısını otomatik olarak sonlandırmasına olanak tanır. 

Kaber için bağlantı oluşturmanın geleneksel yolunu kullanmak yerine, [Openconnectionforkey yöntemini](https://msdn.microsoft.com/library/azure/dn824099.aspx)kullanmanız gerekir. Bu, tüm doğrulamanın gerçekleşmesini ve parçaların arasında herhangi bir veri taşındığı zaman bağlantıların doğru yönetilmesini sağlar.

### <a name="requirements-for-dapper-integration"></a>Kaber tümleştirmesi için gereksinimler
Hem elastik veritabanı istemci kitaplığı hem de kaber API 'Leri ile çalışırken, aşağıdaki özellikleri sürdürmek istersiniz:

* **Ölçeği genişletme**: uygulamanın kapasite taleplerini için gereken şekilde, parçalı uygulamanın veri katmanında veritabanı eklemek veya kaldırmak istiyoruz. 
* **Tutarlılık**: uygulama parçalama kullanılarak azaltıldığından, verilere bağımlı yönlendirme yapmanız gerekir. Bunu yapmak için kitaplığın veriye bağımlı yönlendirme özelliklerini kullanmak istiyoruz. Özellikle, bozulmaları veya yanlış sorgu sonuçlarının olmaması için parça eşleme Yöneticisi aracılığıyla aracılı bağlantılar tarafından sunulan doğrulama ve tutarlılık garantilerini sürdürmek istiyorsunuz. Bu, (örneğin), parça/birleştirme API 'Leri kullanılarak parçalanan farklı bir parçaya taşınabilmesi durumunda belirli bir parçalanmaya yönelik bağlantıların reddedildiğini veya durdurulmasını sağlar.
* **Nesne eşleme**: uygulamadaki sınıflar ve temel alınan veritabanı yapıları arasında çeviri yapmak Için daber tarafından sunulan eşlemelerin rahatlığını sürdürmek istiyoruz. 

Aşağıdaki bölümde, **daber** ve **dapperextensions**tabanlı uygulamalar için bu gereksinimlere yönelik yönergeler sağlanmaktadır.

## <a name="technical-guidance"></a>Teknik kılavuz
### <a name="data-dependent-routing-with-dapper"></a>Kaber ile verilere bağımlı yönlendirme
Kaber ile uygulama genellikle temel alınan veritabanına yönelik bağlantıları oluşturup açmaktan sorumludur. Uygulama tarafından bir tür T verildiğinde, Korber sorgu sonuçlarını T türünde .NET koleksiyonları olarak döndürüyor. Davber, t-SQL sonuç satırlarından t türünde nesnelere eşleme yapar. Benzer şekilde, Davber .NET nesnelerini SQL değerlerine veya veri işleme dili (DML) ifadelerine yönelik parametrelere eşler. Kaber, ADO .NET SQL Istemci kitaplıklarından normal [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) nesnesindeki genişletme yöntemleri aracılığıyla bu işlevselliği sunar. DDR için elastik ölçek API 'Leri tarafından döndürülen SQL bağlantısı da normal [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) nesneleridir. Bu, aynı zamanda basit bir SQL Istemci bağlantısı olduğundan, istemci kitaplığının DDR API 'SI tarafından döndürülen tür üzerinde doğrudan paber uzantılarını kullanmamızı sağlar.

Bu gözlemler, paber için elastik veritabanı istemci kitaplığı tarafından aracılı bağlantıları kullanmayı kolaylaştırır.

Bu kod örneği (eşlik eden örnekten), sağ parça bağlantısını aracıya bağlamak için, uygulama tarafından, uygulamanın, parçalara ayırma anahtarının sağlandığı yaklaşımı gösterir.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1,
                     connectionString: connStrBldr.ConnectionString,
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

[Openconnectionforkey](https://msdn.microsoft.com/library/azure/dn807226.aspx) API 'sine yapılan çağrı, bir SQL istemci bağlantısının varsayılan oluşturma ve açma işlemini değiştirir. [Openconnectionforkey](https://msdn.microsoft.com/library/azure/dn807226.aspx) çağrısı, verilere bağımlı yönlendirme için gerekli olan bağımsız değişkenleri alır: 

* Veri bağımlı yönlendirme arabirimlerine erişim için parça eşlemesi
* Parçalama anahtarı, kıardlet 'yi belirlemek için
* Parçaya bağlanmak için kimlik bilgileri (Kullanıcı adı ve parola)

Parça eşleme nesnesi, verilen parçalı anahtar için parçalama ile aynı parçayı tutan bir bağlantı oluşturur. Elastik veritabanı istemci API 'Leri, tutarlılık garantilerini uygulamak için bağlantıyı etiketleyebilir. [Openconnectionforkey](https://msdn.microsoft.com/library/azure/dn807226.aspx) çağrısı normal bir SQL istemci bağlantı nesnesi döndürdüğünden, bir sonraki yöntemden **Execute** genişletme yöntemine yapılan çağrı standart kaber uygulamasını izler.

Sorgular çok çok aynı şekilde çalışır. ilk önce bağlantıyı istemci API 'sinden [Openconnectionforkey](https://msdn.microsoft.com/library/azure/dn807226.aspx) kullanarak açarsınız. Daha sonra, SQL sorgunuzun sonuçlarını .NET nesneleriyle eşlemek için normal kaber uzantı yöntemlerini kullanın:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate ))
    {
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT *
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

DDR bağlantısı olan **using** bloğunun, blok içindeki tüm veritabanı Işlemlerini, tenantId1 'in tutulduğu bir parçaya kadar bir parçadır. Sorgu yalnızca geçerli parça üzerinde depolanan blogları döndürür, ancak diğer parçalara depolanamazlar. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Daber ve DapperExtensions ile verilere bağımlı yönlendirme
Kaber, veritabanı uygulamaları geliştirirken veritabanına daha kolay ve soyutlama sağlayan ek uzantıların bir ekosistemiyle gelir. DapperExtensions bir örnektir. 

Uygulamanızda Daperextensions kullanılması, veritabanı bağlantılarının oluşturulma ve yönetilme şeklini değiştirmez. Uygulamanın bağlantıları açma sorumluluğu hala vardır ve uzantı yöntemleri tarafından düzenli SQL Istemci bağlantısı nesneleri beklenmektedir. Yukarıda özetlenen [Openconnectionforkey](https://msdn.microsoft.com/library/azure/dn807226.aspx) öğesine güvenebiliriz. Aşağıdaki kod örnekleri de gösterildiği gibi tek değişiklik, artık T-SQL deyimlerini yazmanız gerekmez:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Sorgu için kod örneği aşağıda verilmiştir: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Geçici hataları işleme
Microsoft düzenleri & Yöntemler ekibi, uygulama geliştiricilerinin bulutta çalışırken karşılaşılan yaygın geçici hata koşullarını azaltmalarına yardımcı olmak için [geçici hata Işleme uygulama bloğunu](https://msdn.microsoft.com/library/hh680934.aspx) yayımladı. Daha fazla bilgi için, bkz. [Tüm Anferance, geçici hata Işleme uygulama bloğunu kullanma](https://msdn.microsoft.com/library/dn440719.aspx).

Kod örneği, geçici hatalara karşı korumak için geçici hata kitaplığını kullanır. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

Yukarıdaki koddaki **SqlDatabaseUtils. SqlRetryPolicy** , yeniden deneme sayısı 10 olan bir **SqlDatabaseTransientErrorDetectionStrategy** olarak tanımlanır ve yeniden denemeler arasında 5 saniye bekleyin. İşlem kullanıyorsanız, yeniden deneme kapsamınızda geçici bir hata olması durumunda işlemin başlangıcına doğru gitdiğinizden emin olun.

## <a name="limitations"></a>Sınırlamalar
Bu belgede özetlenen yaklaşımların birkaç sınırlaması vardır:

* Bu belgenin örnek kodu parçalar genelinde şemanın nasıl yönetileceğini göstermez.
* İstek verildiğinde, tüm veritabanı işlemlerinin, istek tarafından sağlanan parçalı anahtar tarafından tanımlanan tek bir parça içinde yer aldığı varsayılır. Ancak, bu varsayım her zaman ayrı tutulmaz, örneğin bir parçalama anahtarı kullanılabilir hale getirmek mümkün değildir. Bunu çözmek için, elastik veritabanı istemci kitaplığı [Multishardquery sınıfını](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx)içerir. Sınıfı, çeşitli parçaları sorgulamak için bir bağlantı soyutlaması uygular. Çoklu Shardquery 'nin kaber ile birlikte kullanılması, bu belgenin kapsamının ötesinde.

## <a name="conclusion"></a>Sonuç
Daber ve DapperExtensions kullanan uygulamalar, Azure SQL veritabanı için elastik veritabanı araçlarından kolayca yararlanabilir. Bu belgede özetlenen adımlarda, bu uygulamalar, yeni [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) nesnelerinin oluşturulmasını ve açılmasını esnek bir [Openconnectionforkey](https://msdn.microsoft.com/library/azure/dn807226.aspx) çağrısını kullanacak şekilde değiştirerek, veri bağımlı yönlendirme için aracın özelliğini kullanabilir veritabanı istemci kitaplığı. Bu, yeni bağlantıların oluşturulduğu ve açıldığı yerlerde gerekli olan uygulama değişikliklerini kısıtlar. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
