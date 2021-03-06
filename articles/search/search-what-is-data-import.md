---
title: Arama dizinlerinde içeri ve dışarı veri alımı
titleSuffix: Azure Cognitive Search
description: Dış veri kaynaklarından Azure Bilişsel Arama verileri doldurun ve dizine yükleyin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: aa44a27fa5bf6b7b4ea649e1a9b9a69ef8cd78d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049330"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Veri içeri genel bakış-Azure Bilişsel Arama

Azure Bilişsel Arama 'de sorgular, ' a yüklenmiş ve bir [arama dizinine](search-what-is-an-index.md)kaydedilen içeriklerinizin üzerinde yürütülür. Bu makalede, bir dizini doldurmak için iki temel yaklaşım incelenir: verileri programlı bir şekilde dizine *gönderin* veya desteklenen bir veri kaynağındaki [Azure bilişsel arama Dizin oluşturucuyu](search-indexer-overview.md) , verileri *çekmek* için işaretleyin.

Her iki yaklaşımla de amaç bir dış veri kaynağından Azure Bilişsel Arama dizinine veri yükleme. Azure Bilişsel Arama, boş bir dizin oluşturmanıza izin verir, ancak veri göndermeye veya çekene kadar sorgulanabilir değildir.

> [!NOTE]
> [AI zenginleştirme](cognitive-search-concept-intro.md) bir çözüm gereksinimidir, bir dizin yüklemek için çekme modelini (Dizin oluşturucular) kullanmanız gerekir. Dış işleme yalnızca bir dizin oluşturucuya iliştirilmiş becerileri aracılığıyla desteklenir.

## <a name="pushing-data-to-an-index"></a>Verileri dizine gönderme

Verilerinizi Azure Bilişsel Arama 'a programlı bir şekilde göndermek için kullanılan anında iletme modeli, en esnek yaklaşımdır. Birincisi, veri kaynağı türüne hiçbir kısıtlama getirmez. JSON belgelerinden oluşan herhangi bir veri kümesi, bir Azure Bilişsel Arama dizinine itilmiş olduğundan, veri kümesindeki her belgenin dizin şemanızda tanımlı alanlarla alanları eşleştirmiş olduğunu varsayarsak. İkincisi, yürütme frekansı üzerinde hiçbir kısıtlaması yoktur. Değişiklikleri istediğiniz sıklıkta dizine gönderebilirsiniz. Çok düşük gecikme süresi gereksinimlerine sahip uygulamalar için (örneğin, arama işlemlerinin dinamik stok veritabanlarıyla eşitlenmiş olması gerekiyorsa), tek seçeneğiniz gönderme modelidir.

Belgeleri tek tek veya toplu işlemle karşıya yükleyebileceğinizden (toplu işlem başına en fazla 1000 veya 16 MB sınırlarından hangisi önce gelirse), bu yaklaşım çekme modelinden daha esnektir. Anında iletme modeli, verilerinizi nerede olursa olsun Azure Bilişsel Arama yüklemenize de olanak tanır.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Azure Bilişsel Arama dizinine veri gönderme

Dizin bir tek veya birden çok belge yüklemek için şu API'leri kullanabilirsiniz:

+ [Belge Ekleme, Güncelleştirme veya Silme (REST API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Indexdocumentsaction sınıfı](/dotnet/api/azure.search.documents.models.indexdocumentsaction) veya [ındexdocumentsbatch sınıfı](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) 

Şu an portal aracılığıyla veri gönderme için hiçbir araç desteği yoktur.

Her metodolojiye giriş için bkz. [hızlı başlangıç: PowerShell veya C# hızlı başlangıç kullanarak azure bilişsel arama dizini oluşturma](./search-get-started-powershell.md) [: .NET SDK kullanarak Azure bilişsel arama dizini oluşturma](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Dizin oluşturma eylemleri: karşıya yükleme, birleştirme, mergeOrUpload, Delete

Belge başına temelinde dizin oluşturma eylemi türünü denetleyebilir, belgenin tam olarak karşıya yüklenip yüklenmeyeceğini, varolan belge içeriğiyle birleştirilip silinmeyeceğini belirtebilir.

REST API Azure Bilişsel Arama dizininizin Endpoint URL 'nize JSON istek gövdeleriyle HTTP POST istekleri verin. "Value" dizisindeki her JSON nesnesi belgenin anahtarını içerir ve bir dizin oluşturma eyleminin belge içeriğini ekleyip eklemediğini veya silmediğini belirtir. Kod örneği için bkz. [belgeleri yükleme](search-get-started-dotnet.md#load-documents).

.NET SDK 'sında verilerinizi bir `IndexBatch` nesneye paketleyin. `IndexBatch` `IndexAction` , Her biri bir belge ve bu belgede hangi eylemin gerçekleştirileceğini bilişsel arama Azure 'a söyleyen bir özellik içeren bir nesne koleksiyonunu kapsüller. Kod örneği için bkz. [C# hızlı](search-get-started-dotnet.md)başlangıcı.


| @search.action | Description | Her bir belge için gerekli alanlar | Notlar |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Bir `upload` eylemi, belgenin yeni olması durumunda ekleneceği ve var olması durumunda güncelleştirileceği/değiştirileceği bir "upsert" ile benzerlik gösterir. |anahtar ve tanımlamak istediğiniz diğer alanlar |Var olan bir belgeyi güncelleştirirken/değiştirirken istekte belirtilmeyen herhangi bir alan `null` olarak ayarlanır. Bu durum, alan daha önce değersiz olmayan bir değere ayarlanmış olsa dahi gerçekleşir. |
| `merge` |Var olan belgeyi belirtilen alanlarla güncelleştirir. Belge dizinde mevcut değilse birleştirme işlemi başarısız olur. |anahtar ve tanımlamak istediğiniz diğer alanlar |Birleştirmede belirttiğiniz herhangi bir alan belgede var olan alanın yerini alır. .NET SDK 'sında bu, türünde alanlar içerir `DataType.Collection(DataType.String)` . REST API, bu, türünde alanlar içerir `Collection(Edm.String)` . Örneğin, belge `["budget"]` değerine sahip bir `tags` alanını içeriyorsa ve `tags` için `["economy", "pool"]` değeriyle bir birleştirme yürütürseniz `tags` alanının son değeri `["economy", "pool"]` olur. `["budget", "economy", "pool"]` olmayacaktır. |
| `mergeOrUpload` |Belirtilen anahtara sahip bir belge dizinde zaten mevcutsa bu eylem `merge` gibi davranır. Belge mevcut değilse yeni bir belgeyle `upload` gibi davranır. |anahtar ve tanımlamak istediğiniz diğer alanlar |- |
| `delete` |Belirtilen belgeyi dizinden kaldırır. |yalnızca anahtar |Anahtar alanı dışında belirttiğiniz tüm alanlar yoksayılır. Bir belgeden tek bir alanı kaldırmak istiyorsanız bunun yerine `merge` kullanıp alanı açık bir şekilde null olarak ayarlamanız yeterlidir. |

### <a name="formulate-your-query"></a>Sorgunuzu düzenleme

[REST API kullanarak dizininizi aramanın](/rest/api/searchservice/Search-Documents) iki yolu bulunur. Bu yollardan biri, sorgu parametrelerinizin istek gövdesindeki bir JSON nesnesinde tanımlanacağı bir HTTP POST isteği göndermektir. Diğer yol ise sorgu parametrelerinizin istek URL'si içinde tanımlanacağı bir HTTP GET isteği göndermektir. POST, sorgu parametrelerinin boyutu açısından GET'ten daha [esnek sınırlara](/rest/api/searchservice/Search-Documents) sahiptir. Bu nedenle, GET'i kullanmanın daha kullanışlı olduğu özel durumlar olmadığı sürece POST kullanmanızı öneririz.

Hem GÖNDERI hem de GET için, istek URL 'sinde *hizmet adınızı*, *Dizin adınızı* ve bir *API sürümünü* sağlamanız gerekir. 

GET için sorgu parametrelerini URL'nin sonundaki *sorgu dizesine* sağlarsınız. URL biçimi için aşağıya bakın:

```http
    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06
```

POST biçimi aynı ancak `api-version` sorgu dizesi parametrelerinde ile aynıdır.

## <a name="pulling-data-into-an-index"></a>Verileri dizine çekme

Çekme modeli, desteklenen veri kaynağında gezinir ve dizininize verileri otomatik olarak yükler. Azure Bilişsel Arama, bu özellik şu anda bu platformlar için kullanılabilen *Dizin oluşturucular* aracılığıyla uygulanır:

+ [Blob depolama](search-howto-indexing-azure-blob-storage.md)
+ [Tablo depolama](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL veritabanı, SQL yönetilen örneği ve Azure VM 'lerinde SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SharePoint Online (Önizleme)](search-howto-index-sharepoint-online.md)
+ [Azure Data Lake Storage 2. (Önizleme)](search-howto-index-azure-data-lake-storage.md)

Dizin oluşturucular bir dizini bir veri kaynağına (genelde tablo, görünüm veya eşdeğer bir yapı) bağlar ve kaynak alanları dizindeki eşdeğer alanlara eşler. Yürütme sırasında satır kümesi otomatik olarak JSON'a dönüştürülür ve belirtilen dizine yüklenir. Tüm dizin oluşturucular zamanlamayı destekler ve bu sayede verilerin yenilenme sıklığını belirleyebilirsiniz. Çoğu dizin oluşturucular veri kaynağının desteklemesi durumunda değişiklik izleme özelliği sunar. Dizin oluşturucular, var olan belgelerdeki değişiklikleri ve silmeleri takip etmenin yanı sıra yeni belgeleri tanıyarak, dizininizdeki verileri aktif şekilde yönetme ihtiyacını ortadan kaldırır.

### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Azure Bilişsel Arama dizinine veri çekme

Dizin oluşturucu işlevleri [Azure portalı](search-import-data-portal.md), [REST API'sı](/rest/api/searchservice/Indexer-operations) ve [.NET SDK'sında](/dotnet/api/azure.search.documents.indexes.searchindexerclient) belirtilmiştir.

Portalı kullanmanın bir avantajı, Azure Bilişsel Arama kaynak veri kümesinin meta verilerini okuyarak genellikle sizin için varsayılan bir dizin şeması oluşturabilir. Oluşturulan dizini işlenene kadar değiştirebilirsiniz ancak işlendikten sonra yalnızca dizinin yeniden oluşturulmasını gerektirmeyen şema düzenlemelerine izin verilir. Yapmak istediğiniz değişikliklerin şemayı doğrudan etkilemesi halinde dizini yeniden oluşturmanız gerekir. 

## <a name="verify-data-import-with-search-explorer"></a>Arama Gezgini ile veri içeri aktarmayı doğrulama

Karşıya belge yükleme sırasında bir ön denetim yapmanın hızlı bir yolu, portalda **Arama Gezgini** 'ni kullanmaktır. Bu gezgin, bir dizini kod yazmadan sorgulamanızı sağlar. Arama deneyimi [basit söz dizimi](/rest/api/searchservice/simple-query-syntax-in-azure-search) ve varsayılan [searchMode sorgu parametresi](/rest/api/searchservice/search-documents)gibi varsayılan ayarları temel alır. Belgenin tamamını inceleyebilmeniz için sonuçlar JSON biçiminde döndürülür.

> [!TIP]
> Birçok [Azure bilişsel arama kod örneği](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) , kolayca kullanmaya başlamak için kolay bir yol sunan eklenmiş veya hazır veri kümelerini içerir. Portalda ayrıca örnek dizin oluşturucu ve küçük bir emlak veri kümesini ("realestate-us-sample" adlı) içeren veri kaynağı mevcuttur. Önceden yapılandırılmış dizin oluşturucuyu örnek veri kaynağında çalıştırdığınızda, bir dizin oluşturulur ve daha sonra arama Gezgini 'nde veya yazdığınız kodla sorgulanabilecek belgeler ile yüklenir.

## <a name="see-also"></a>Ayrıca bkz.

+ [Dizin Oluşturucu’ya genel bakış](search-indexer-overview.md)
+ [Portal kılavuzu: dizini oluşturma, yükleme, sorgulama](search-get-started-portal.md)