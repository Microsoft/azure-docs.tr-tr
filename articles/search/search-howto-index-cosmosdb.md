---
title: Azure Cosmos DB verileri arama
titleSuffix: Azure Cognitive Search
description: Azure Cosmos DB veri kaynağını gezin ve Azure Bilişsel Arama tam metin ile aranabilir bir dizinde veri alma. Dizin oluşturucular Azure Cosmos DB gibi seçili veri kaynakları için veri alma işlemini otomatik hale getirir.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7e4d51701fd8614831585aac03f2c8a909b2b847
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112746"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Azure 'da Dizin Oluşturucu kullanarak Cosmos DB verilerini dizin oluşturma Bilişsel Arama 

> [!IMPORTANT] 
> SQL API 'SI genel kullanıma sunulmuştur.
> MongoDB API 'SI, Gremlin API ve Cassandra API desteği şu anda genel önizlemededir. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Bu formu](https://aka.ms/azure-cognitive-search/indexer-preview)doldurarak önizlemelere erişim isteğinde bulabilirsiniz. [REST API sürüm 2019-05-06-önizleme](search-api-preview.md) , Önizleme özellikleri sağlar. Şu anda sınırlı sayıda portal desteği var ve .NET SDK desteği yok.

Bu makalede, içerik ayıklamak ve Azure Bilişsel Arama 'de aranabilir hale getirmek için bir Azure Cosmos DB [dizin oluşturucunun](search-indexer-overview.md) nasıl yapılandırılacağı gösterilmektedir. Bu iş akışı bir Azure Bilişsel Arama dizini oluşturur ve Azure Cosmos DB ayıklanan varolan metinle yükler. 

Terminoloji kafa karıştırıcı olabileceğinden, [Azure Cosmos DB Dizin oluşturma](https://docs.microsoft.com/azure/cosmos-db/index-overview) ve [Azure bilişsel arama dizin oluşturma](search-what-is-an-index.md) 'nın her bir hizmete özgü ayrı işlemler olduğunu belirten bir değer vardır. Azure Bilişsel Arama Dizin oluşturmaya başlamadan önce Azure Cosmos DB veritabanınız zaten var olmalı ve veri içermeli.

Azure Bilişsel Arama 'deki Cosmos DB Dizin Oluşturucu, farklı protokollerle erişilen [Azure Cosmos DB öğelerine](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) gezinebiliyor.

+ Genel kullanıma sunulan [SQL API 'si](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference)için [Portal](#cosmos-indexer-portal), [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)veya [.NET SDK 'sını](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)kullanabilirsiniz.

+ [MongoDB API (Önizleme)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) ve [Gremlin API (Önizleme)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)için, Dizin oluşturucuyu oluşturmak üzere [Portal](#cosmos-indexer-portal) 'ı veya [create Indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) çağrısını [REST API sürüm 2019-05-06-Preview](search-api-preview.md) kullanabilirsiniz.

+ [Cassandra API (Önizleme)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction)Için, [Create ıNDEXER (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) çağrısında yalnızca [REST API 2019-05-06-Preview sürümünü](search-api-preview.md) kullanabilirsiniz.


> [!Note]
> Azure Bilişsel Arama 'de desteklendiğini görmek isterseniz, [tablo API'si](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) Için Kullanıcı sesine oy verebilirsiniz.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Portalı kullanma

> [!Note]
> Portal Şu anda SQL API ve MongoDB API 'sini (Önizleme) desteklemektedir.

Azure Cosmos DB öğelerinin dizinlenmesini sağlamak için en kolay yöntem, [Azure Portal](https://portal.azure.com/)bir sihirbaz kullanmaktır. Azure [**bilişsel arama verileri**](search-import-data-portal.md) örnekleyerek ve meta verileri okuyarak, varsayılan bir dizin oluşturabilir, kaynak alanları hedef dizin alanlarıyla eşleyebilir ve dizini tek bir işlemde yükleyebilir. Kaynak verilerin boyutuna ve karmaşıklığına bağlı olarak, dakikalar içinde işlemsel bir tam metin arama dizinine sahip olabilirsiniz.

Aynı bölgede, tercihen Azure Bilişsel Arama ve Azure Cosmos DB için aynı Azure aboneliğini kullanmanızı öneririz.

### <a name="1---prepare-source-data"></a>1-kaynak verileri hazırlama

SQL API, MongoDB API 'SI (Önizleme) veya Gremlin API (Önizleme) ile eşlenmiş bir Cosmos DB hesabınız Azure Cosmos DB, veritabanı ve veritabanındaki içerikler olmalıdır.

Cosmos DB veritabanınızın veri içerdiğinden emin olun. [Veri alma Sihirbazı](search-import-data-portal.md) meta verileri okur ve bir dizin şemasını çıkarması için veri örnekleme gerçekleştirir, ancak Ayrıca Cosmos DB verileri yükler. Veriler eksikse, sihirbaz bu hatayla biter "veri kaynağından Dizin şeması tespit etme hatası: DataSource ' emptycollection ' hiçbir veri döndürdüğünden bir prototip dizini derlenemedi.

### <a name="2---start-import-data-wizard"></a>2-veri alma Sihirbazı 'nı başlatma

Sihirbazı Azure Bilişsel Arama hizmeti sayfasında komut çubuğundan [başlatabilir](search-import-data-portal.md) veya Cosmos db SQL API 'sine bağlanıyorsanız, Cosmos DB hesabınızın solundaki **ayarlar** bölümünde **Azure bilişsel arama Ekle** ' ye tıklayabilirsiniz Gezinti bölmesi.

   ![Portalda verileri içeri aktar komutu](./media/search-import-data-portal/import-data-cmd2.png "Veri alma Sihirbazı 'nı başlatma")

### <a name="3---set-the-data-source"></a>3-veri kaynağını ayarlama

**Veri kaynağı** sayfasında, kaynak **Cosmos DB**, aşağıdaki belirtimlerle birlikte olmalıdır:

+ **Ad** , veri kaynağı nesnesinin adıdır. Oluşturulduktan sonra diğer iş yükleri için bunu seçebilirsiniz.

+ **Cosmos DB hesabın** , bir `AccountEndpoint` ve bir `AccountKey`ile Cosmos DB birincil veya ikincil bağlantı dizesi olması gerekir. MongoDB koleksiyonları için, bağlantı dizesinin sonuna **Apikind = MongoDb** ekleyin ve bağlantı dizesinden noktalı virgülle ayırın. Gremlin API ve Cassandra API için [REST API](#cosmosdb-indexer-rest)yönergelerini kullanın.

+ **Veritabanı** , hesaptaki mevcut bir veritabanıdır. 

+ **Koleksiyon** bir belge kapsayıcısıdır. Almanın başarılı olması için belgeler mevcut olmalıdır. 

+ Tüm belgeleri istiyorsanız **sorgu** boş olabilir, aksi takdirde bir belge alt kümesi seçen bir sorgu girebilirsiniz. **Sorgu** yalnızca SQL API 'si için kullanılabilir.

   ![Cosmos DB veri kaynağı tanımı](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB veri kaynağı tanımı")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4-sihirbazdaki "zenginleştirme içerik" sayfasını atlayın

Bilişsel yetenekler (veya zenginleştirme) eklemek bir içeri aktarma gereksinimi değildir. Dizin oluşturma işlem hattınızda [AI zenginleştirme eklemek](cognitive-search-concept-intro.md) için özel bir ihtiyacınız yoksa bu adımı atlamanız gerekir.

Adımı atlamak için, "Ileri" ve "atla" için sayfanın altındaki mavi düğmelere tıklayın.

### <a name="5---set-index-attributes"></a>5-dizin özniteliklerini ayarla

**Dizin** sayfasında, bir veri türü ve Dizin özniteliklerini ayarlamaya yönelik bir dizi onay kutusu içeren alanların listesini görmeniz gerekir. Sihirbaz meta verileri temel alan ve kaynak verileri örnekleyerek bir alanlar listesi oluşturabilir. 

Öznitelik sütununun en üstündeki onay kutusuna tıklayarak öznitelikleri toplu olarak seçebilirsiniz. Bir istemci uygulamasına döndürülmesi gereken her alan için **alınabilir** ve **aranabilir** ' i seçin ve tam metin arama işlemesine tabidir. Tamsayıların tam metin veya benzer aranabilir olduğunu fark edeceksiniz (sayılar tam olarak değerlendirilir ve genellikle filtrelerdeki yararlı olur).

Daha fazla bilgi için [dizin özniteliklerinin](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) ve [dil çözümleyicilerinin](https://docs.microsoft.com/rest/api/searchservice/language-support) açıklamasını gözden geçirin. 

Seçimlerinizi gözden geçirmek için bir dakikanızı ayırın. Sihirbazı çalıştırdığınızda fiziksel veri yapıları oluşturulur ve tüm nesneleri bırakıp yeniden oluşturmadan bu alanları düzenleyemezsiniz.

   ![Cosmos DB Dizin tanımı](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB Dizin tanımı")

### <a name="6---create-indexer"></a>6-Dizin Oluşturucu oluştur

Tam olarak belirtilen sihirbaz, arama hizmetinizde üç ayrı nesne oluşturur. Veri kaynağı nesnesi ve dizin nesnesi, Azure Bilişsel Arama hizmetinize adlandırılmış kaynaklar olarak kaydedilir. Son adım bir Dizin Oluşturucu nesnesi oluşturur. Dizin oluşturucunun adlandırılması, aynı sihirbaz dizisinde oluşturulan dizin ve veri kaynağı nesnesinden bağımsız olarak zamanlayabilir ve yönetebileceğiniz tek başına bir kaynak olarak var olmasına izin verir.

Dizin oluşturucular hakkında bilginiz yoksa, *Dizin Oluşturucu* , aranabilir içerik için bir dış veri kaynağında gezinir ve Azure bilişsel arama bir kaynaktır. **Veri alma** Sihirbazı 'nın çıktısı, Cosmos DB veri kaynağınıza gezinir, aranabilir içeriği ayıklar ve Azure bilişsel arama bir dizine içeri aktarır.

Aşağıdaki ekran görüntüsünde varsayılan dizin oluşturucu yapılandırması gösterilmektedir. Dizin Oluşturucuyu bir kez çalıştırmak istiyorsanız bir **kez** geçiş yapabilirsiniz. Sihirbazı çalıştırmak ve tüm nesneleri oluşturmak için **Gönder** ' e tıklayın. Dizin oluşturma anında yapılır.

   ![Cosmos DB Dizin Oluşturucu tanımı](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB Dizin Oluşturucu tanımı")

Veri içeri aktarmayı Portal sayfalarında izleyebilirsiniz. İlerleme durumu bildirimleri dizin oluşturma durumunu ve kaç belge yüklendiğini gösterir. 

Dizin oluşturma işlemi tamamlandığında, dizini sorgulamak için [Arama Gezgini](search-explorer.md) ' ni kullanabilirsiniz.

> [!NOTE]
> Bekleyen verileri görmüyorsanız, daha fazla alan için daha fazla öznitelik ayarlamanız gerekebilir. Yeni oluşturduğunuz dizin ve Dizin oluşturucuyu silin ve sihirbazda adım adım 5 ' teki dizin öznitelikleri için seçimlerinizi değiştirerek yeniden ilerleyin. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API'lerini kullanma

Azure Bilişsel Arama 'deki tüm dizin oluşturucular için ortak olan üç bölümlü bir iş akışından sonra Azure Cosmos DB verileri indekslemek için REST API kullanabilirsiniz: veri kaynağı oluşturma, dizin oluşturma, Dizin Oluşturucu oluşturma. Cosmos DB veri ayıklama, Create Indexer isteği gönderdiğinizde oluşur. Bu istek bittikten sonra, sorgulanabilir bir dizininiz olur. 

> [!NOTE]
> Cosmos DB Gremlin API veya Cosmos DB veri dizini oluşturmak için Cassandra API önce [Bu formu](https://aka.ms/azure-cognitive-search/indexer-preview)doldurarak geçitli önizlemelere erişim istemeniz gerekir. İsteğiniz işlendikten sonra, veri kaynağını oluşturmak için [REST API 2019-05-06-Preview sürümünü](search-api-preview.md) kullanma yönergelerini alacaksınız.

Bu makalenin önceki kısımlarında [Azure Cosmos DB Dizin oluşturma](https://docs.microsoft.com/azure/cosmos-db/index-overview) ve [Azure bilişsel arama Dizin](search-what-is-an-index.md) oluşturma Dizin oluşturma işleminin farklı işlemleri olduğu belirtiliyor. Cosmos DB Dizin oluşturma için, varsayılan olarak tüm belgeler Cassandra API hariç otomatik olarak dizinlenir. Otomatik Dizin oluşturmayı kapatırsanız, belgelere yalnızca kendi bağlantıları aracılığıyla veya belge KIMLIĞI kullanılarak sorgulara erişilebilir. Azure Bilişsel Arama Dizin oluşturma, Azure Bilişsel Arama tarafından Dizin oluşturulacak koleksiyonda otomatik dizin oluşturma işleminin açık Cosmos DB olmasını gerektirir. Cosmos DB Cassandra API Dizin Oluşturucu önizlemesine kaydolurken, Cosmos DB Dizin oluşturma hakkında yönergeler sağlanacaktır.

> [!WARNING]
> Azure Cosmos DB, DocumentDB 'nin yeni nesli. Daha önce API sürüm **2017-11-11** ile `documentdb` sözdizimini kullanabilirsiniz. Bu, veri kaynağı türünü `cosmosdb` veya `documentdb`olarak belirtebileceğiniz anlamına gelir. API sürüm **2019-05-06** ' den Itibaren hem Azure bilişsel arama API 'leri hem de Portal yalnızca bu makalede belirtildiği gibi `cosmosdb` söz dizimini destekler. Bu, bir Cosmos DB uç noktasına bağlanmak istiyorsanız veri kaynağı türünün `cosmosdb` olması gerektiği anlamına gelir.

### <a name="1---assemble-inputs-for-the-request"></a>1-istek için girişleri birleştirin

Her istek için, Azure Bilişsel Arama için hizmet adı ve yönetici anahtarı (posta üst bilgisinde) ve BLOB depolama için depolama hesabı adı ve anahtarı sağlamalısınız. Azure Bilişsel Arama HTTP istekleri göndermek için [Postman](search-get-started-postman.md) kullanabilirsiniz.

Aşağıdaki dört değeri not defteri 'ne kopyalayın, böylece bunları bir isteğe yapıştırabilirsiniz:

+ Azure Bilişsel Arama hizmet adı
+ Azure Bilişsel Arama yönetici anahtarı
+ Cosmos DB bağlantı dizesi

Bu değerleri portalda bulabilirsiniz:

1. Azure Bilişsel Arama Portal sayfalarında, genel bakış sayfasından arama hizmeti URL 'sini kopyalayın.

2. Sol gezinti bölmesinde **anahtarlar** ' a tıklayın ve ardından birincil ya da ikincil anahtarı kopyalayın (eşdeğerdir).

3. Cosmos depolama hesabınız için Portal sayfalarına geçiş yapın. Sol gezinti bölmesindeki **Ayarlar**altında **anahtarlar**' a tıklayın. Bu sayfa bir URI, iki bağlantı dizesi kümesi ve iki anahtar kümesi sağlar. Bağlantı dizelerinden birini Not defteri 'ne kopyalayın.

### <a name="2---create-a-data-source"></a>2-veri kaynağı oluşturma

Veri **kaynağı** , verilerdeki değişiklikleri tanımlamaya yönelik verileri, kimlik bilgilerini ve ilkeleri belirtir (koleksiyonunuzdaki değiştirilen veya silinen belgeler gibi). Veri kaynağı, birden çok Dizin Oluşturucu tarafından kullanılabilmesi için bağımsız bir kaynak olarak tanımlanır.

Bir veri kaynağı oluşturmak için bir POST isteğini formüle koyun:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

İsteğin gövdesi, aşağıdaki alanları içermesi gereken veri kaynağı tanımını içerir:

| Alan   | Açıklama |
|---------|-------------|
| **name** | Gereklidir. Veri kaynağı nesnenizin temsil edilebilmesi için herhangi bir ad seçin. |
|**type**| Gereklidir. `cosmosdb`olmalıdır. |
|**Credentials** | Gereklidir. Cosmos DB bir bağlantı dizesi olmalıdır.<br/>SQL koleksiyonları için, bağlantı dizeleri şu biçimdedir: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>MongoDB koleksiyonları için, bağlantı dizesine **Apikind = MongoDb** ekleyin:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Gremlin grafikleri ve Cassandra tablolarında, önizlemeye erişim sağlamak için [geçitli Dizin Oluşturucu önizlemesine](https://aka.ms/azure-cognitive-search/indexer-preview) kaydolun ve kimlik bilgilerini biçimlendirme hakkında bilgi alın.<br/><br/>Uç nokta URL 'sindeki bağlantı noktası numaralarını önleyin. Bağlantı noktası numarasını eklerseniz, Azure Bilişsel Arama Azure Cosmos DB veritabanınızın dizinini oluşturamıyor.|
| **kapsayıcı** | Aşağıdaki öğeleri içerir: <br/>**ad**: gerekli. Endekslenecek veritabanı koleksiyonunun KIMLIĞINI belirtin.<br/>**sorgu**: isteğe bağlı. Rastgele bir JSON belgesini, Azure Bilişsel Arama 'in dizinetarafından kullanılabilecek düz bir şemaya düzleştirmek için bir sorgu belirtebilirsiniz.<br/>MongoDB API 'SI, Gremlin API ve Cassandra API için sorgular desteklenmez. |
| **dataChangeDetectionPolicy** | Önerilen. Bkz. [Dizin oluşturma değiştirilen belgeler](#DataChangeDetectionPolicy) bölümü.|
|**dataDeletionDetectionPolicy** | İsteğe bağlı. Bkz. [Dizin oluşturma silinen belgeler](#DataDeletionDetectionPolicy) bölümü.|

### <a name="using-queries-to-shape-indexed-data"></a>Dizinli verileri şekillendirmek için sorguları kullanma
İç içe özellikleri veya dizileri, proje JSON özelliklerini düzleştirmek için bir SQL sorgusu belirtebilir ve dizine eklenecek verileri filtreleyebilirsiniz. 

> [!WARNING]
> **MongoDB API 'si**, **gremlin API**ve **Cassandra API**için özel sorgular desteklenmez: `container.query` parametresinin null veya atlanmış olarak ayarlanması gerekir. Özel bir sorgu kullanmanız gerekiyorsa lütfen [Kullanıcı sesimizi](https://feedback.azure.com/forums/263029-azure-search)bize bildirin.

Örnek belge:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Filtre sorgusu:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Sorgu düzleştirme:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Projeksiyon sorgusu:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Dizi düzleştirme sorgusu:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3-hedef arama dizini oluşturma 

Henüz yoksa bir [hedef Azure bilişsel arama dizini oluşturun](/rest/api/searchservice/create-index) . Aşağıdaki örnek, bir KIMLIK ve açıklama alanı olan bir dizin oluşturur:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Hedef dizininizin şemasının, kaynak JSON belgelerinin şemasıyla veya özel sorgu projeksiyonunun çıkışıyla uyumlu olduğundan emin olun.

> [!NOTE]
> Bölümlenmiş koleksiyonlar için, varsayılan belge anahtarı Azure Cosmos DB `_rid` özelliktir. alan adları bir alt çizgi karakteriyle başlamadığı için Azure Bilişsel Arama otomatik olarak `rid` olarak yeniden adlandırılır. Ayrıca, Azure Cosmos DB `_rid` değerler Azure Bilişsel Arama anahtarlarında geçersiz karakterler içeriyor. Bu nedenle `_rid` değerleri Base64 kodlandı.
> 
> MongoDB koleksiyonları için Azure Bilişsel Arama `_id` özelliğini otomatik olarak `doc_id`olarak yeniden adlandırır.  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>JSON veri türleri ve Azure Bilişsel Arama veri türleri arasında eşleme
| JSON veri türü | Uyumlu hedef dizin alanı türleri |
| --- | --- |
| Bool |EDM. Boolean, Edm. String |
| Tamsayılar gibi görünen sayılar |EDM. Int32, Edm. Int64, Edm. String |
| Kayan nokta gibi görünen sayılar |EDM. Double, Edm. dize |
| Dize |Edm.String |
| Temel türlerin dizileri, örneğin ["a", "b", "c"] |Collection(Edm.String) |
| Tarihler gibi görünen dizeler |EDM. DateTimeOffset, Edm. String |
| GeoJSON nesneleri, örneğin {"Type": "Point", "koordinatlar": [Long, Lat]} |Edm.GeographyPoint |
| Diğer JSON nesneleri |Yok |

### <a name="4---configure-and-run-the-indexer"></a>4-dizin oluşturucuyu yapılandırma ve çalıştırma

Dizin ve veri kaynağı oluşturulduktan sonra, Dizin oluşturucuyu oluşturmaya hazırsınız:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Bu Dizin Oluşturucu iki saatte bir çalışır (zamanlama aralığı "PT2H" olarak ayarlanır). Her 30 dakikada bir dizin oluşturucu çalıştırmak için, aralığı "PT30M" olarak ayarlayın. Desteklenen en kısa Aralık 5 dakikadır. Zamanlama isteğe bağlıdır-atlanırsa, Dizin Oluşturucu yalnızca bir kez oluşturulduğunda çalışır. Ancak, bir dizin oluşturucuyu dilediğiniz zaman isteğe bağlı olarak çalıştırabilirsiniz.   

Dizin Oluşturucu oluşturma API 'SI hakkında daha fazla bilgi için bkz. [Dizin Oluşturucu oluştur](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Dizin Oluşturucu zamanlamalarını tanımlama hakkında daha fazla bilgi için bkz. [Azure bilişsel arama için Dizin Oluşturucu zamanlama](search-howto-schedule-indexers.md).

## <a name="use-net"></a>.NET’i kullanma

Genel olarak kullanılabilen .NET SDK, genel olarak kullanılabilir REST API tam eşliği vardır. Kavramları, iş akışını ve gereksinimleri öğrenmek için önceki REST API bölümünü incelemenizi öneririz. Daha sonra, yönetilen kodda bir JSON Dizin Oluşturucu uygulamak için aşağıdaki .NET API başvuru belgelerine başvurabilirsiniz.

+ [Microsoft. Azure. Search. modeller. DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [Microsoft. Azure. Search. modeller. DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [Microsoft. Azure. Search. modeller. index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [Microsoft. Azure. Search. modeller. Indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Değiştirilen belgelerin dizinini oluşturma

Veri değişikliği algılama ilkesinin amacı, değiştirilen veri öğelerini etkili bir şekilde belirlemektir. Şu anda desteklenen tek ilke, aşağıdaki gibi belirtilen Azure Cosmos DB tarafından sunulan `_ts` (timestamp) özelliğini kullanan `High Water Mark` ilkesidir:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Bu ilkenin kullanılması, iyi bir Dizin Oluşturucu performansını güvence altına almak için önemle önerilir. 

Özel bir sorgu kullanıyorsanız, `_ts` özelliğinin sorgu tarafından yansıtıldığınızdan emin olun.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Artımlı ilerleme ve özel sorgular

Dizin oluşturma sırasında artımlı ilerleme durumu, Dizin Oluşturucu yürütmesi geçici hatalara veya yürütme süresi sınırına göre kesintiye uğrarsa, dizin oluşturucunun, tüm koleksiyonu sıfırdan yeniden eklemek yerine, her çalıştırıldığında kaldığınız yeri açabilmesini sağlar. Büyük koleksiyonlar dizinlenirken bu özellikle önemlidir. 

Özel bir sorgu kullanırken artımlı ilerlemeyi etkinleştirmek için sorgunuzun sonuçları `_ts` sütununa göre sipariş ettiğinden emin olun. Bu, Azure Bilişsel Arama 'in hatalara karşı artımlı ilerleme durumunu sağlamak için kullandığı düzenli denetim noktası sağlar.   

Bazı durumlarda, sorgunuz bir `ORDER BY [collection alias]._ts` yan tümcesi içerse bile, Azure Bilişsel Arama sorgunun `_ts`göre sıralı olduğunu çıkarmayabilir. Azure Bilişsel Arama sonuçların `assumeOrderByHighWaterMarkColumn` yapılandırma özelliği kullanılarak sıralanmasına söylemiş olabilirsiniz. Bu ipucunu belirtmek için, Dizin oluşturucuyu aşağıdaki şekilde oluşturun veya güncelleştirin: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Silinen belgelerin dizinini oluşturma

Koleksiyondan satırlar silindiğinde, normalde bu satırları arama dizininden da silmek istersiniz. Veri silme algılaması ilkesinin amacı, silinen veri öğelerini etkin bir şekilde tanımlamaktır. Şu anda desteklenen tek ilke `Soft Delete` ilkesidir (silme işlemi bir sıralama bayrağıyla işaretlenir) ve aşağıdaki şekilde belirtilir:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Özel bir sorgu kullanıyorsanız, `softDeleteColumnName` tarafından başvurulan özelliğin sorgu tarafından yansıtıldığınızdan emin olun.

Aşağıdaki örnek, geçici silme ilkesiyle bir veri kaynağı oluşturur:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Sonraki adımlar

Tebrikler! Azure Cosmos DB bir Dizin Oluşturucu kullanarak Azure Bilişsel Arama ile nasıl tümleştirileceğini öğrendiniz.

* Azure Cosmos DB hakkında daha fazla bilgi edinmek için [Azure Cosmos DB hizmeti sayfasına](https://azure.microsoft.com/services/cosmos-db/)bakın.
* Azure Bilişsel Arama hakkında daha fazla bilgi için [Arama hizmeti sayfasına](https://azure.microsoft.com/services/search/)bakın.
