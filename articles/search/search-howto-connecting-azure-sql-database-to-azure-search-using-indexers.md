---
title: Azure SQL verileri üzerinde arama
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de tam metin araması için Dizin oluşturucuları kullanarak Azure SQL veritabanı 'ndan veri aktarın. Bu makalede bağlantılar, Dizin Oluşturucu yapılandırması ve veri alımı ele alınmaktadır.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c09727e8d92a449b41124eae6ad8381d66cb2619
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113313"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-an-azure-cognitive-search-indexer"></a>Azure Bilişsel Arama Dizin oluşturucuyu kullanarak Azure SQL veritabanı içeriğine bağlanma ve dizin oluşturma

[Azure bilişsel arama dizinini](search-what-is-an-index.md)sorgulayabilmeniz için önce bu verileri verilerinize göre doldurmanız gerekir. Veriler bir Azure SQL veritabanında yer alıyorsa, Azure **SQL veritabanı için bir azure bilişsel arama Dizin Oluşturucu** (veya kısa IÇIN **Azure SQL Indexer** ), dizin oluşturma işlemini otomatikleştirebilir. Bu, daha az kod yazmak için daha az kod ve ilgilendiğiniz altyapıyı daha az olabilir.

Bu makalede, [Dizin oluşturucular](search-indexer-overview.md)kullanmanın mekanizması ele alınmaktadır, ancak Azure SQL veritabanlarında (örneğin, tümleşik değişiklik izleme) kullanılabilen özellikler de açıklanmaktadır. 

Azure Bilişsel Arama, Azure SQL veritabanlarına ek olarak, [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Blob depolama](search-howto-indexing-azure-blob-storage.md)ve [Azure Tablo depolama](search-howto-indexing-azure-tables.md)için Dizin oluşturucular sağlar. Diğer veri kaynaklarına yönelik destek istemek için [Azure bilişsel arama geri bildirim forumuna](https://feedback.azure.com/forums/263029-azure-search/)geri bildirimde bulunun.

## <a name="indexers-and-data-sources"></a>Dizin oluşturucular ve veri kaynakları

Veri **kaynağı** , hangi verilerin dizine alınacağı, veri erişiminin kimlik bilgilerinin ve verilerdeki değişiklikleri etkili bir şekilde tanımlayan ilkelere (yeni, değiştirilen veya silinen satırlarda) göre belirler. Bu, birden çok Dizin Oluşturucu tarafından kullanılabilmesi için bağımsız bir kaynak olarak tanımlanır.

**Dizin Oluşturucu** , hedeflenen bir arama diziniyle tek bir veri kaynağını bağlayan bir kaynaktır. Bir Dizin Oluşturucu aşağıdaki yollarla kullanılır:

* Bir dizini doldurmak için verilerin tek seferlik bir kopyasını gerçekleştirin.
* Bir dizini bir zamanlamaya göre veri kaynağındaki değişikliklerle güncelleştirin.
* Gerektiğinde bir dizini güncelleştirmek için isteğe bağlı olarak çalıştırın.

Tek bir Dizin Oluşturucu yalnızca bir tablo veya görünüm kullanabilir, ancak birden çok arama dizinini doldurmak istiyorsanız birden çok Dizin Oluşturucu oluşturabilirsiniz. Kavramlar hakkında daha fazla bilgi için bkz. [Dizin Oluşturucu işlemleri: tipik iş akışı](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Aşağıdakileri kullanarak bir Azure SQL Indexer ayarlayabilir ve yapılandırabilirsiniz:

* [Azure Portal](https://portal.azure.com) veri alma Sihirbazı
* Azure Bilişsel Arama [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Azure Bilişsel Arama [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

Bu makalede, **Dizin oluşturucular** ve **veri kaynakları**oluşturmak için REST API kullanacağız.

## <a name="when-to-use-azure-sql-indexer"></a>Azure SQL Indexer ne zaman kullanılır?
Verilerinize ilişkin çeşitli faktörlere bağlı olarak, Azure SQL Indexer kullanımı uygun olabilir veya olmayabilir. Verileriniz aşağıdaki gereksinimlere sığıyorsa Azure SQL Indexer 'ı kullanabilirsiniz.

| Ölçütler | Ayrıntılar |
|----------|---------|
| Veriler tek bir tablo veya görünümden gelmektedir | Veriler birden çok tabloya dağılmış ise, verilerin tek bir görünümünü oluşturabilirsiniz. Ancak, bir görünüm kullanıyorsanız, artımlı değişiklikler içeren bir dizini yenilemek için SQL Server tümleşik değişiklik algılama kullanamazsınız. Daha fazla bilgi için bkz. [değiştirilen ve silinen satırları yakalama](#CaptureChangedRows) . |
| Veri türleri uyumlu | Azure Bilişsel Arama dizininde en fazla SQL türü desteklenmez. Bir liste için bkz. [eşleme veri türleri](#TypeMapping). |
| Gerçek zamanlı veri eşitleme gerekli değildir | Bir Dizin Oluşturucu, tablonuzu en fazla beş dakikada bir yeniden dizin oluşturabilir. Verileriniz sıklıkla değişirse ve değişikliklerin dizinde saniye veya tek dakika içinde yansıtılması gerekiyorsa, güncelleştirilmiş satırları doğrudan göndermek için [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) veya [.NET SDK 'sını](search-import-data-dotnet.md) kullanmanızı öneririz. |
| Artımlı dizin oluşturma mümkündür | Büyük bir veri ayarladıysanız ve Dizin Oluşturucuyu bir zamanlamaya göre çalıştırmayı planlıyorsanız, Azure Bilişsel Arama yeni, değiştirilen veya silinen satırları verimli bir şekilde tanımlayabilmelidir. Artımlı olmayan dizin oluşturma için yalnızca isteğe bağlı (zamanlamada değil) veya 100.000 satırdan daha az satır dizin oluşturma yapıyorsanız izin verilir. Daha fazla bilgi için bkz. [değiştirilen ve silinen satırları yakalama](#CaptureChangedRows) . |

> [!NOTE] 
> Azure Bilişsel Arama yalnızca SQL Server kimlik doğrulamasını destekler. Azure Active Directory parola kimlik doğrulaması için desteğe ihtiyacınız varsa lütfen bu [UserVoice önerisi](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica)için oy verin.

## <a name="create-an-azure-sql-indexer"></a>Azure SQL Indexer oluşturma

1. Veri kaynağını oluşturun:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Bağlantı dizesini [Azure Portal](https://portal.azure.com)alabilir; `ADO.NET connection string` seçeneğini kullanın.

2. Henüz bir tane yoksa hedef Azure Bilişsel Arama dizinini oluşturun. [Portalı](https://portal.azure.com) veya [create INDEX API 'yi](https://docs.microsoft.com/rest/api/searchservice/Create-Index)kullanarak bir dizin oluşturabilirsiniz. Hedef dizininizin şemasının kaynak tablonun şemasıyla uyumlu olduğundan emin olun- [SQL ve Azure bilişsel arama veri türleri arasındaki eşlemeyi](#TypeMapping)inceleyin.

3. Dizin Oluşturucuyu bir ad vererek ve veri kaynağına ve hedef dizine başvurarak oluşturun:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Bu şekilde oluşturulan bir dizin oluşturucunun zamanlaması yoktur. Oluşturulduğunda otomatik olarak çalışır. Bir **çalıştırma Dizin Oluşturucu** isteği kullanarak istediğiniz zaman yeniden çalıştırabilirsiniz:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2019-05-06
    api-key: admin-key

Dizin Oluşturucu davranışının, toplu iş boyutu ve dizin oluşturucunun yürütülmesi başarısız olmadan önce kaç tane belge atlanacak gibi çeşitli yönlerini özelleştirebilirsiniz. Daha fazla bilgi için bkz. [Dizin Oluşturucu API 'Si oluşturma](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Azure hizmetlerinin veritabanınıza bağlanmasına izin vermeniz gerekebilir. Bunu nasıl yapacağınız hakkında yönergeler için bkz. [Azure 'Dan bağlanma](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) .

Dizin Oluşturucu durumunu ve yürütme geçmişini (dizin oluşturulan öğe sayısı, başarısızlık vb.) izlemek için bir **Dizin Oluşturucu durum** isteği kullanın:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2019-05-06
    api-key: admin-key

Yanıt aşağıdakine benzer görünmelidir:

    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

Yürütme geçmişi, geriye doğru kronolojik düzende sıralanan en son tamamlanan yürütmelerin 50 ' i içerir (yani en son yürütmenin yanıtta ilk olarak olması gerekir).
Yanıtla ilgili ek bilgiler, [Dizin Oluşturucu durumunu Al](https://go.microsoft.com/fwlink/p/?LinkId=528198) bölümünde bulunabilir

## <a name="run-indexers-on-a-schedule"></a>Dizin Oluşturucuyu bir zamanlamaya göre Çalıştır
Dizin Oluşturucuyu bir zamanlamaya göre düzenli aralıklarla çalışacak şekilde de düzenleyebilirsiniz. Bunu yapmak için, Dizin oluşturucuyu oluştururken veya güncelleştirirken **Schedule** özelliğini ekleyin. Aşağıdaki örnekte, Dizin oluşturucuyu güncellemek için bir PUT isteği gösterilmektedir:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**Interval** parametresi gereklidir. Aralık, arka arkaya iki Dizin Oluşturucu yürütmelerinin başlangıcı arasındaki süreyi ifade eder. İzin verilen en küçük Aralık 5 dakikadır; en uzun değer bir gündür. XSD "dayTimeDuration" değeri ( [ıso 8601 Duration](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) değerinin kısıtlı bir alt kümesi) olarak biçimlendirilmelidir. Bunun için olan model: `P(nD)(T(nH)(nM))`. Örnekler: her 2 saat için `PT2H` 15 dakikada bir `PT15M`.

Dizin Oluşturucu zamanlamalarını tanımlama hakkında daha fazla bilgi için bkz. [Azure bilişsel arama için Dizin Oluşturucu zamanlama](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Yeni, değiştirilmiş ve silinen satırları yakala

Azure Bilişsel Arama, bir dizin oluşturucunun her çalıştırılışında tüm tabloyu veya görünümü yeniden eklemek zorunda kalmamak için **artımlı dizin oluşturma** kullanır. Azure Bilişsel Arama, artımlı Dizin oluşturmayı desteklemek için iki değişiklik algılama ilkesi sağlar. 

### <a name="sql-integrated-change-tracking-policy"></a>SQL tümleşik Değişiklik İzleme Ilkesi
SQL veritabanınız [değişiklik izlemeyi](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)destekliyorsa, **SQL tümleşik değişiklik izleme ilkesi**kullanmanızı öneririz. Bu en etkili ilkedir. Ayrıca, tablonuza açık bir "geçici silme" sütunu eklemenize gerek kalmadan, Azure Bilişsel Arama silinen satırları tanımlamasına olanak tanır.

#### <a name="requirements"></a>Gereksinimler 

+ Veritabanı sürümü gereksinimleri:
  * Azure VM 'lerinde SQL Server kullanıyorsanız SQL Server 2012 SP3 ve üzeri.
  * Azure SQL veritabanı kullanıyorsanız, Azure SQL Veritabanı V12.
+ Yalnızca tablolar (görünüm yok). 
+ Veritabanında, tablo için [değişiklik izlemeyi etkinleştirin](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) . 
+ Tabloda bileşik birincil anahtar (birden fazla sütun içeren birincil anahtar) yok.  

#### <a name="usage"></a>Kullanım

Bu ilkeyi kullanmak için veri kaynağınızı şu şekilde oluşturun veya güncelleştirin:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

SQL tümleşik değişiklik izleme ilkesini kullanırken, ayrı bir veri silme algılama ilkesi belirtmeyin-Bu ilke, silinen satırları tanımlamaya yönelik yerleşik desteğe sahiptir. Ancak, silmeler "otomatikgara" olarak algılanabilmesi için, arama dizininizdeki belge anahtarı SQL tablosundaki birincil anahtarla aynı olmalıdır. 

> [!NOTE]  
> Bir SQL tablosundan çok sayıda satırı kaldırmak için [truncate table](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) kullanırken, değişiklik izleme durumunu satır silme işlemlerini çekmek üzere sıfırlamak için dizin oluşturucunun [sıfırlanması](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) gerekir.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Yüksek su Işareti değişiklik algılama ilkesi

Bu değişiklik algılama ilkesi, bir satırın en son güncelleştirildiği sürümü veya zamanı yakalayan "yüksek su işareti" sütununu kullanır. Bir görünüm kullanıyorsanız, yüksek bir su işareti ilkesi kullanmanız gerekir. Yüksek su işareti sütununun aşağıdaki gereksinimleri karşılaması gerekir.

#### <a name="requirements"></a>Gereksinimler 

* Tüm ekler sütun için bir değer belirtir.
* Bir öğeye yapılan tüm güncelleştirmeler sütunun değerini de değiştirir.
* Bu sütunun değeri, her ekleme veya güncelleştirmeyle birlikte artar.
* Aşağıdaki WHERE ve ORDER BY yan tümceleri olan sorgular verimli bir şekilde yürütülebilir: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Yüksek su işareti sütunu için [ROWVERSION](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) veri türünün kullanılması önemle önerilir. Başka herhangi bir veri türü kullanılırsa, bir Dizin Oluşturucu sorgusuyla eşzamanlı olarak çalıştırılan işlemlerin bulunduğu tüm değişiklikleri yakalamak için değişiklik izleme garantisi garanti edilmez. Salt okuma çoğaltmalarıyla bir yapılandırmada **rowversion** kullanılırken, Dizin oluşturucuyu birincil çoğaltmaya işaret etmeniz gerekir. Yalnızca birincil çoğaltma, veri eşitleme senaryoları için kullanılabilir.

#### <a name="usage"></a>Kullanım

Yüksek bir su işareti İlkesi kullanmak için veri kaynağınızı şu şekilde oluşturun veya güncelleştirin:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Kaynak tabloda yüksek su işareti sütununda bir dizin yoksa, SQL Indexer tarafından kullanılan sorgular zaman aşımına uğrar. Özellikle `ORDER BY [High Water Mark Column]` yan tümcesi, tablo çok sayıda satır içerdiğinde bir dizinin verimli bir şekilde çalışmasını gerektirir.
>
>

Zaman aşımı hatalarıyla karşılaşırsanız, sorgu zaman aşımını varsayılan 5 dakikalık zaman aşımından daha yüksek bir değere ayarlamak için `queryTimeout` Indexer yapılandırma ayarını kullanabilirsiniz. Örneğin, zaman aşımını 10 dakika olarak ayarlamak için aşağıdaki yapılandırmayla Dizin oluşturucuyu oluşturun veya güncelleştirin:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

`ORDER BY [High Water Mark Column]` yan tümcesini de devre dışı bırakabilirsiniz. Ancak, Dizin Oluşturucu yürütmesi bir hata tarafından kesintiye uğrarsa, dizin oluşturucunun daha sonra çalıştırıldığında tüm satırları yeniden işlemesi gerekir, çünkü Dizin Oluşturucu, neredeyse tüm satırları yarıda kesilen zamana göre zaten işledi. `ORDER BY` yan tümcesini devre dışı bırakmak için Dizin Oluşturucu tanımındaki `disableOrderByHighWaterMarkColumn` ayarını kullanın:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Geçici silme sütunu silme algılama ilkesi
Satırlar kaynak tablodan silindiğinde, muhtemelen bu satırları arama dizininden da silmek isteyebilirsiniz. SQL tümleşik değişiklik izleme ilkesini kullanıyorsanız, bu sizin için bu şekilde yapılır. Ancak, yüksek su işareti değişiklik izleme ilkesi, silinen satırlarda size yardımcı olmaz. Ne yapmanız gerekir?

Satırlar tablodan fiziksel olarak kaldırılırsa Azure Bilişsel Arama, artık mevcut olmayan kayıtların varlığını çıkarsanın bir yolu yoktur.  Bununla birlikte, satırları tablodan kaldırmadan mantıksal olarak silmek için "geçici silme" tekniğini kullanabilirsiniz. Tablonuza bir sütun ekleyin veya satırları bu sütun kullanılarak silinmiş olarak görüntüleyin ve işaretleyin.

Geçici silme tekniği kullanılırken, veri kaynağını oluştururken veya güncelleştirirken geçici silme ilkesini aşağıdaki gibi belirtebilirsiniz:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**SoftDeleteMarkerValue** bir dize olmalıdır: gerçek değer ' in dize gösterimini kullanın. Örneğin, silinen satırları 1 değeri ile işaretlenmiş bir tamsayı sütununuz varsa `"1"`kullanın. Silinen satırların Boolean true değeri ile işaretlendiği bir BIT sütununuz varsa, sabit değer `True` veya `true`kullanın, büyük/küçük harf bir önemi yoktur.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>SQL ve Azure Bilişsel Arama veri türleri arasında eşleme
| SQL veri türü | İzin verilen hedef dizin alanı türleri | Notlar |
| --- | --- | --- |
| bit |EDM. Boolean, Edm. String | |
| int, smallint, tinyint |EDM. Int32, Edm. Int64, Edm. String | |
| bigint |EDM. Int64, Edm. String | |
| gerçek, float |EDM. Double, Edm. dize | |
| küçük para, para ondalık sayısal |Edm.String |Azure Bilişsel Arama, kesinliği kaybedebileceğinden, Decimal türlerinin Edm. Double 'a dönüştürülmesini desteklemez |
| Char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Dize bir JSON dizisini temsil ediyorsa bir koleksiyon (EDM. String) alanını doldurmak için bir SQL dizesi kullanılabilir: `["red", "white", "blue"]` |
| smalldatetime, DateTime, datetime2, Date, DateTimeOffset |EDM. DateTimeOffset, Edm. String | |
| uniqueıdentıer |Edm.String | |
| Coğrafya |Edm.GeographyPoint |Yalnızca SRID 4326 (varsayılan) ile tür noktası olan Coğrafya örnekleri desteklenir |
| rowversion |Yok |Satır sürümü sütunları arama dizininde depolanamaz, ancak değişiklik izleme için kullanılabilirler |
| Time, TimeSpan, BINARY, varbinary, Image, XML, geometry, CLR türleri |Yok |Desteklenmiyor |

## <a name="configuration-settings"></a>Yapılandırma ayarları
SQL Indexer çeşitli yapılandırma ayarları sunar:

| Ayar | Veri türü | Amaç | Varsayılan değer |
| --- | --- | --- | --- |
| queryTimeout |string |SQL sorgu yürütmesi için zaman aşımını ayarlar |5 dakika ("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |Yüksek su işareti ilkesi tarafından kullanılan SQL sorgusunun ORDER BY yan tümcesini yok saymasına neden olur. Bkz. [yüksek su işareti ilkesi](#HighWaterMarkPolicy) |yanlış |

Bu ayarlar, Dizin Oluşturucu tanımındaki `parameters.configuration` nesnesinde kullanılır. Örneğin, sorgu zaman aşımını 10 dakika olarak ayarlamak için, aşağıdaki yapılandırmayla Dizin oluşturucuyu oluşturun veya güncelleştirin:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>SSS

**S: Azure 'da IaaS VM 'lerinde çalışan SQL veritabanları ile Azure SQL dizin oluşturucuyu kullanabilir miyim?**

Evet. Ancak, arama hizmetinizin veritabanınıza bağlanmasına izin vermeniz gerekir. Daha fazla bilgi için bkz. Azure [bilişsel arama Dizin oluşturucudan Azure sanal makinesinde SQL Server bağlantı yapılandırma](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**S: şirket içinde çalışan SQL veritabanlarıyla Azure SQL dizin oluşturucuyu kullanabilir miyim?**

Doğrudan yönetilemez. Doğrudan bir bağlantı önermiyor veya desteklemiyoruz, bu sayede veritabanlarınızı Internet trafiğine açmanızı gerektirir. Müşteriler, Azure Data Factory gibi köprü teknolojilerini kullanarak bu senaryoya başarılı olmuştur. Daha fazla bilgi için bkz. [Azure Data Factory kullanarak Azure bilişsel arama dizinine veri gönderme](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**S: Azure SQL dizin oluşturucuyu, Azure 'da IaaS 'de çalışan SQL Server dışındaki veritabanlarıyla kullanabilir miyim?**

Hayır. Dizin oluşturucuyu SQL Server dışındaki veritabanlarıyla test etmemiş olduğundan, bu senaryoyu desteklemiyoruz.  

**S: bir zamanlamaya göre çalışan birden çok dizin oluşturma yapabilir miyim?**

Evet. Ancak, tek seferde yalnızca bir Dizin Oluşturucu çalıştırılabilir. Aynı anda birden çok Dizin Oluşturucu gerekiyorsa, arama hizmetinizi birden fazla arama birimine ölçeklendirin.

**S: bir dizin oluşturucuyu çalıştırmak sorgu iş yükmi etkiler mi?**

Evet. Dizin Oluşturucu, arama hizmetinizdeki düğümlerden birinde çalışır ve düğüm kaynakları dizin oluşturma ile sorgu trafiği ve diğer API istekleri aracılığıyla paylaşılır. Yoğun dizin oluşturma ve sorgu iş yüklerini çalıştırır ve yüksek oranda 503 hatayla karşılaşır veya yanıt sürelerini artırdıysanız, [arama hizmetinizi ölçeklendirin](search-capacity-planning.md).

**S: bir [Yük devretme kümesinde](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) bir ikincil çoğaltmayı veri kaynağı olarak kullanabilir miyim?**

Duruma göre değişir. Bir tablonun veya görünümün tam dizini oluşturmak için, ikincil bir çoğaltma kullanabilirsiniz. 

Artımlı dizin oluşturma için Azure Bilişsel Arama iki değişiklik algılama ilkesini destekler: SQL tümleşik değişiklik izleme ve yüksek su Işareti.

Salt okuma çoğaltmalarda SQL veritabanı tümleşik değişiklik izlemeyi desteklemez. Bu nedenle, yüksek su Işareti ilkesi kullanmanız gerekir. 

Standart önerimiz, yüksek su işareti sütunu için ROWVERSION veri türünü kullanmaktır. Ancak, rowversion kullanmak SQL veritabanının `MIN_ACTIVE_ROWVERSION` işlevini kullanır ve bu, salt okunurdur çoğaltmalar üzerinde desteklenmez. Bu nedenle, rowversion kullanıyorsanız Dizin oluşturucuyu birincil çoğaltmaya işaret etmeniz gerekir.

Bir salt okuma çoğaltmasında ROWVERSION kullanmaya çalışırsanız aşağıdaki hatayı görürsünüz: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**S: yüksek su işareti değişiklik izleme için alternatif, rowversion olmayan bir sütun kullanabilir miyim?**

Önerilmez. Yalnızca **rowversion** , güvenilir veri eşitlemeye izin verir. Ancak, uygulama mantığınıza bağlı olarak, şu durumlarda güvenli olabilir:

+ Dizin Oluşturucu çalıştırıldığında, tablo üzerinde hiçbir bekleyen işlem olmadığından emin olabilirsiniz (örneğin, tüm tablo güncelleştirmeleri bir zamanlamaya göre bir toplu iş olarak gerçekleşir ve Azure Bilişsel Arama Indexer zamanlaması tabloyla örtüşmeden kaçınmak üzere ayarlanır) Zamanlamayı Güncelleştir).  

+ Tüm eksik satırları seçmek için düzenli aralıklarla tam yeniden dizin kullanırsınız. 