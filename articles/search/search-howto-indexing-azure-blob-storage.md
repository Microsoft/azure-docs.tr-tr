---
title: Blob Dizin Oluşturucu yapılandırma
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de tam metin arama işlemleri için blob içeriğini dizinlemeyi otomatik hale getirmek üzere bir Azure Blob Dizin Oluşturucu ayarlayın.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 6f70ae726cf41395e46760dc5cf7da5b4d61478a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802905"
---
# <a name="how-to-configure-blob-indexing-in-cognitive-search"></a>Bilişsel Arama blob dizinlemeyi yapılandırma

Blob Dizin Oluşturucu, Azure Blob depolamadan bir Bilişsel Arama dizinine içerik almak için kullanılır. Blob Dizinleyicileri genellikle [AI zenginleştirme](cognitive-search-concept-intro.md)'de kullanılır. burada, ekli bir [beceri](cognitive-search-working-with-skillsets.md) , aranabilir içerik oluşturmak için görüntü ve doğal dil işleme ekler. Ancak PDF 'Ler, Microsoft Office belgeler ve dosya biçimleri gibi metin tabanlı belgelerden içerik almak için AI zenginleştirme olmadan blob dizinleyicileri de kullanabilirsiniz.

Bu makalede, her iki senaryo için bir blob dizin oluşturucunun nasıl yapılandırılacağı gösterilir. Dizin Oluşturucu kavramları hakkında bilginiz yoksa, [Azure bilişsel arama Dizin oluşturucularıyla](search-indexer-overview.md) başlayın ve BLOB dizinlemeye girmeden önce [bir arama Dizin Oluşturucu oluşturun](search-howto-create-indexers.md) .

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Desteklenen belge biçimleri

Azure Bilişsel Arama blob Indexer, aşağıdaki belge biçimlerinden metin ayıklayabilir:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>Veri kaynağı tanımları

Blob Indexer ve diğer herhangi bir Dizin Oluşturucu arasındaki birincil fark, dizin oluşturucuya atanan veri kaynağı tanımıdır. Veri kaynağı tanımı, veri kaynağı türünü ("tür": "azureblob"), kimlik doğrulama ve dizine eklenecek içeriğe bağlantı için diğer özellikleri belirtir.

Blob veri kaynağı tanımı, aşağıdaki örneğe benzer şekilde görünür:

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

`"credentials"`Özelliği, yukarıdaki örnekte gösterildiği gibi bir bağlantı dizesi veya bir sonraki bölümde açıklanan alternatif yaklaşımlardan biri olabilir. `"container"`Özelliği, Azure Storage içindeki içeriğin konumunu sağlar ve `"query"` kapsayıcıda bir alt klasör belirtmek için kullanılır. Veri kaynağı tanımları hakkında daha fazla bilgi için bkz. [veri kaynağı oluşturma (REST)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

## <a name="credentials"></a>Kimlik bilgileri

Blob kapsayıcısının kimlik bilgilerini şu yollarla sağlayabilirsiniz:

**Yönetilen kimlik bağlantı dizesi**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Bu bağlantı dizesinde hesap anahtarı gerekmez, ancak [yönetilen bir kimlik kullanarak bir Azure depolama hesabına bağlantı ayarlama](search-howto-managed-identities-storage.md)yönergelerini izlemeniz gerekir.

**Tam erişimli depolama hesabı bağlantı dizesi**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

Azure portal > anahtarlar (klasik depolama hesapları için) veya ayarlar > erişim anahtarları (Azure Resource Manager depolama hesapları için) > depolama hesabı dikey penceresine giderek bağlantı dizesini alabilirsiniz.

**Depolama hesabı paylaşılan erişim imzası** (SAS) bağlantı dizesi: `{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

SAS, kapsayıcılar ve nesneler (Bu durumda Bloblar) üzerinde liste ve okuma izinlerine sahip olmalıdır.

**Kapsayıcı paylaşılan erişim imzası**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

SAS, kapsayıcıda liste ve okuma izinlerine sahip olmalıdır. Depolama paylaşılan erişim imzaları hakkında daha fazla bilgi için bkz. [paylaşılan erişim Imzalarını kullanma](../storage/common/storage-sas-overview.md).

> [!NOTE]
> SAS kimlik bilgilerini kullanıyorsanız, zaman aşımı süresini engellemek için veri kaynağı kimlik bilgilerini yenilenen imzalara göre düzenli aralıklarla güncelleştirmeniz gerekecektir. SAS kimlik bilgilerinin kullanım süreleri dolarsa, Dizin Oluşturucu, "bağlantı dizesinde girilen kimlik bilgileri geçersiz veya süreleri sona erdiğinde" aşağıdakine benzer bir hata iletisiyle başarısız olur.  

## <a name="index-definitions"></a>Dizin tanımları

Dizin, bir belge, öznitelik ve arama deneyimini şekillendirip diğer yapıların alanlarını belirtir. Tüm Dizin oluşturucular, hedef olarak bir arama Dizin tanımı belirtmenizi gerektirir. Aşağıdaki örnek, [create INDEX (REST API)](/rest/api/searchservice/create-index)kullanarak basit bir dizin oluşturur. 

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
      "name" : "my-target-index",
      "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
      ]
}
```

Dizin tanımları, koleksiyonda bir alanın `"fields"` belge anahtarı olarak davranmasını gerektirir. Dizin tanımları ayrıca içerik ve meta veriler için alanları içermelidir.

**`content`** Alan, blob içeriği için ortaktır. Bloblardan ayıklanan metni içerir. Bu alanın tanımınız yukarıdaki şuna benzer bir şekilde görünebilir. Bu adı kullanmanız gerekmez, ancak bunu yapmak örtük alan eşleştirmelerinden yararlanmanızı sağlar. Blob Indexer, bir alan eşlemesi gerekmeden, dizin içindeki bir içerik Edm. String alanına blob içeriği gönderebilir.

Dizinde istediğiniz blob meta verileri için de alanlar ekleyebilirsiniz. Dizin Oluşturucu özel meta veri özelliklerini, [Standart meta veri](#indexing-blob-metadata) özelliklerini ve [içeriğe özel meta veri](search-blob-metadata-properties.md) özelliklerini okuyabilir. Dizinler hakkında daha fazla bilgi için bkz. [Dizin oluşturma](search-what-is-an-index.md).

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Belge anahtarlarını ve alan eşlemelerini tanımlama

Bir arama dizininde, belge anahtarı her belgeyi benzersiz şekilde tanımlar. Seçtiğiniz alanın türünde olması gerekir `Edm.String` . Blob içeriği için bir belge anahtarı için en iyi aday, Blobun meta veri özellikleridir.

+ **`metadata_storage_name`** -Bu özellik, ancak dizin oluşturma işlemi yaptığınız tüm kapsayıcılar ve klasörler arasında benzersiz ise, bu özellik bir adaydır. BLOB konumundan bağımsız olarak, tüm içerik dizinlendikten sonra, bitiş sonucu belge anahtarının (ad) arama dizininde benzersiz olması gerekir. 

  Depolama adıyla ilgili bir diğer olası sorun, çizgiler gibi belge anahtarları için geçersiz karakterler içeriyor olabilir. `base64Encode` [Alan eşleme işlevini](search-indexer-field-mappings.md#base64EncodeFunction)kullanarak geçersiz karakterleri işleyebilirsiniz. Bunu yaparsanız, [Arama belgesi (REST)](/rest/api/searchservice/lookup-document)gibi API çağrılarında bu dosyaları geçirirken belge anahtarlarını da kodlamayı unutmayın. .NET ' te, URL kodlaması için [UrlTokenEncode yöntemini](/dotnet/api/system.web.httpserverutility.urltokenencode) kullanabilirsiniz.

+ **`metadata_storage_path`** -Tam yolun kullanılması benzersizlik sağlar, ancak yol kesinlikle `/` [belge anahtarında geçersiz](/rest/api/searchservice/naming-rules)olan karakterler içeriyor. Yukarıdaki gibi, `base64Encode` [işlevini](search-indexer-field-mappings.md#base64EncodeFunction) kullanarak karakterleri kodlayabilirsiniz.

+ Üçüncü seçenek, bloblara özel meta veri özelliği eklemektir. Bu seçenek, blob yükleme işleminizin bu meta veri özelliğini tüm bloblara eklemesine gerek duyar. Anahtar gerekli bir özellik olduğundan, bir değeri eksik olan Blobların dizini oluşturulamaz.

> [!IMPORTANT]
> Dizinde anahtar alanı için açık eşleme yoksa, Azure Bilişsel Arama otomatik olarak `metadata_storage_path` anahtar olarak kullanılır ve temel 64 anahtar değerlerini kodluyor (yukarıdaki ikinci seçenek).
>

#### <a name="example"></a>Örnek

Aşağıdaki örnekte `metadata_storage_name` belge anahtarı gösterilmektedir. Dizinin adlı bir anahtar alanı olduğunu `key` ve `fileSize` belge boyutunu depolamak için adlı başka bir alanı olduğunu varsayalım. Dizin Oluşturucu tanımındaki [alan eşlemeleri](search-indexer-field-mappings.md) alan ilişkilendirmelerini kurar ve `metadata_storage_name` Desteklenmeyen karakterleri işlemek için [ `base64Encode` alan eşleme işlevine](search-indexer-field-mappings.md#base64EncodeFunction) sahiptir.

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>Kodlanmış bir alanı "aranabilir" yapma

Anahtar gibi bir alanın kodlanmış bir sürümünü kullanmanız gerektiğinde `metadata_storage_path` , ancak aynı zamanda bu alanın arama dizininde aranabilir (kodlama olmadan) olması gerekir. Her iki kullanım durumunu da desteklemek için, `metadata_storage_path` biri anahtar (kodlanmış) için bir tane olmak üzere iki alanla eşleyebilirsiniz, kabul ettiğimiz bir yol alanı için ikincisi dizin şemasında "aranabilir" olarak ilişkilendirilebiliyoruz. Aşağıdaki örnekte için iki alan eşlemesi gösterilmektedir `metadata_storage_path` .

```http
PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : " blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
  ]
}
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>İçerik ve meta verileri dizine oluştur

Blob 'lar içerik ve meta veriler içerir. Yapılandırma parametresi kullanılarak Blobların hangi bölümlerinin dizine alınacağını kontrol edebilirsiniz `dataToExtract` . Bu, aşağıdaki değerleri alabilir:

+ `contentAndMetadata` -bloba ayıklanan tüm meta veri ve metin içeriğinin dizine alınacağını belirtir. Varsayılan değer budur.

+ `storageMetadata` -yalnızca [Standart blob özelliklerinin ve Kullanıcı tarafından belirtilen meta verilerin](../storage/blobs/storage-blob-container-properties-metadata.md) dizine alınacağını belirtir.

+ `allMetadata` -Standart blob özelliklerinin ve [bulunan içerik türleri için tüm meta verilerin](search-blob-metadata-properties.md) blob içeriğinden ayıklandığını ve dizine alınacağını belirtir.

Örneğin, yalnızca depolama meta verilerini indekslemek için şunu kullanın:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>Blob içeriğini dizine alma

Varsayılan olarak, JSON veya CSV gibi yapılandırılmış içeriğe sahip Bloblar, tek bir metin öbeği olarak dizinlenir. Ancak, JSON veya CSV belgelerinin bir iç yapısı (sınırlayıcılar) varsa, her satır veya öğe için ayrı arama belgeleri oluşturmak üzere ayrıştırma modları atayabilirsiniz. Daha fazla bilgi için bkz. [JSON Bloblarını dizinleme](search-howto-index-json-blobs.md) ve [CSV bloblarını dizin oluşturma](search-howto-index-csv-blobs.md).

Birleşik veya katıştırılmış bir belge (örneğin, posta arşivi, ekleri içeren katıştırılmış Outlook e-postasına sahip bir Word belgesi) veya. Eki olan MSG dosyası) aynı zamanda tek bir belge olarak dizinlenir. Örneğin, bir. eknden ayıklanan tüm görüntüler. MSG dosyası normalized_images alanına döndürülür.

Belgenin metinsel içeriği adlı bir dize alanında ayıklanır `content` .

  > [!NOTE]
  > Azure Bilişsel Arama, fiyatlandırma katmanına bağlı olarak ne kadar metin çýkarmasını kısıtlar. Geçerli [hizmet limitleri](search-limits-quotas-capacity.md#indexer-limits) ücretsiz katman için 32.000 karakter, temel için 64.000, Standart S2 için 8.000.000 4.000.000 ve Standart S3 için 16.000.000. Kesilen belgeler için Dizin Oluşturucu durum yanıtında bir uyarı bulunur.  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>Blob meta verileri dizinleniyor

Dizin oluşturucular blob meta verilerini de dizine alabilir. İlk olarak, Kullanıcı tarafından belirtilen tüm meta veri özellikleri tam olarak ayıklanabilir. Değerleri almak için, türünün arama dizininde alanı `Edm.String` , Blobun meta veri anahtarıyla aynı ada sahip tanımlamanız gerekir. Örneğin, bir Blobun değeri ile bir meta veri anahtarı varsa `Sensitivity` `High` , arama dizininizdeki adlı bir alan tanımlamalısınız `Sensitivity` ve değer ile doldurulur `High` .

İkinci olarak, standart blob meta veri özellikleri aşağıda listelenen alanlarla ayıklanabilir. Blob Indexer bu blob meta veri özellikleri için otomatik olarak iç alan eşlemeleri oluşturur. Yine de dizin tanımını kullanmak istediğiniz alanları eklemeniz gerekir, ancak dizin oluşturucuda alan eşlemelerini oluşturmayı atlayabilirsiniz.

  + **metadata_storage_name** ( `Edm.String` )-Blobun dosya adı. Örneğin, bir blob/My-Container/My-Folder/subfolder/resume.pdf varsa, bu alanın değeri `resume.pdf` .

  + **metadata_storage_path** ( `Edm.String` )-depolama hesabı da dahil olmak üzere BLOBUN tam URI 'si. Örneğin, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type** ( `Edm.String` )-blobu karşıya yüklemek için kullandığınız kodla belirtilen içerik türü. Örneğin, `application/octet-stream`.

  + **metadata_storage_last_modified** ( `Edm.DateTimeOffset` )-blob için son değiştirme zaman damgası. Azure Bilişsel Arama, ilk dizinledikten sonra her şeyin yeniden dizinlenmesini önlemek için değişen Blobları tanımlamak üzere bu zaman damgasını kullanır.

  + **metadata_storage_size** ( `Edm.Int64` )-BLOB boyutu bayt cinsinden.

  + **metadata_storage_content_md5** ( `Edm.String` )-varsa, Blob içeriğinin MD5 karması.

  + **metadata_storage_sas_token** ( `Edm.String` )-bloba erişim sağlamak için [özel yetenekler](cognitive-search-custom-skill-interface.md) tarafından kullanılabilen geçici bir SAS belirteci. Bu belirtecin kullanım süreleri dolduğunda daha sonra kullanılmak üzere depolanmamalıdır.

Son olarak, dizinleyen Blobların belge biçimine özgü tüm meta veri özellikleri dizin şemasında da temsil edilebilir. İçeriğe özgü meta veriler hakkında daha fazla bilgi için bkz. [İçerik meta verileri özellikleri](search-blob-metadata-properties.md).

Arama dizininizdeki yukarıdaki özelliklerin tümü için alan tanımlamanız gerekmez, uygulamanız için gereken özellikleri yakalayın. bu işlemin üzerine gelin.

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>Hangi Blobların dizine alınacağını denetleme

Hangi Blobların dizine alınacağını ve hangilerinin, blob 'un dosya türüne göre veya blob 'un kendileri üzerinde Özellikler ayarlayarak, dizin oluşturucunun bunların üzerinde atlamasına neden olduğunu kontrol edebilirsiniz.

### <a name="include-specific-file-extensions"></a>Belirli dosya uzantılarını dahil et

`indexedFileNameExtensions`Dizine yönelik dosya uzantılarının virgülle ayrılmış bir listesini sağlamak için kullanın (baştaki noktayla birlikte). Örneğin, yalnızca ' ı dizine eklemek için. PDF ve. DOCX blob 'ları, bunu yapın:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>Belirli dosya uzantılarını Dışla

`excludedFileNameExtensions`Atlanacak dosya uzantılarının virgülle ayrılmış bir listesini sağlamak için kullanın (sonra, önde gelen bir noktayla). Örneğin, tüm Blobları hariç tüm Blobları dizine eklemek için. PNG ve. JPEG uzantıları, bunu yapın:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Hem hem `indexedFileNameExtensions` de `excludedFileNameExtensions` parametreleri varsa, Dizin Oluşturucu önce `indexedFileNameExtensions` ' a, sonra da ' a bakar `excludedFileNameExtensions` . Her iki listede de aynı dosya uzantısı varsa, dizin oluşturma işleminden çıkarılır.

### <a name="add-skip-metadata-the-blob"></a>Blobu "Skip" meta verilerini ekleyin

Dizin Oluşturucu yapılandırma parametreleri kapsayıcı veya klasördeki tüm Bloblar için geçerlidir. Bazen, *ayrı Blobların* nasıl dizine alınacağını denetlemek isteyebilirsiniz. Bunu, blob depolamada bloblara aşağıdaki meta veri özelliklerini ve değerlerini ekleyerek yapabilirsiniz. Dizin Oluşturucu bu özelliklerle karşılaştığında, dizin oluşturma çalıştırmasında Blobu veya içeriğini atlar.

| Özellik adı | Özellik değeri | Açıklama |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |Blob Dizin oluşturucuyu blobu tamamen atlayacak şekilde yönlendirir. Meta veri veya içerik ayıklama denenmez. Bu, belirli bir blob sürekli olarak başarısız olduğunda ve dizin oluşturma işlemini kesintiye uğradığında yararlı olur. |
| `AzureSearch_SkipContent` |`"true"` |Bu, `"dataToExtract" : "allMetadata"` [yukarıda](#PartsOfBlobToIndex) belirtilen bir Blobun kapsamına alınan ayarların eşdeğeridir. |

## <a name="index-large-datasets"></a>Büyük veri kümelerini diz

Dizin oluşturma Blobları zaman alan bir işlem olabilir. Dizin oluşturmak için milyonlarca blob 'un bulunduğu durumlarda, verileri bölümleyerek ve [paralel olarak verileri işlemek](search-howto-large-index.md#parallel-indexing)için birden çok Dizin Oluşturucu kullanarak dizin oluşturmayı hızlandırabilirsiniz. Bunu nasıl ayarlayabilmeniz gerekir:

+ Verilerinizi birden çok blob kapsayıcılarına veya sanal klasöre bölme

+ Her kapsayıcı veya klasör için bir tane olmak üzere çeşitli veri kaynakları ayarlayın. Blob klasörünü işaret etmek için şu `query` parametreyi kullanın:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ Her veri kaynağı için karşılık gelen bir Dizin Oluşturucu oluşturun. Tüm Dizin oluşturucular aynı hedef arama dizinine işaret etmelidir.  

+ Hizmetinizdeki bir arama birimi, belirli bir zamanda bir Dizin Oluşturucu çalıştırabilir. Yukarıda açıklandığı gibi birden çok Dizin Oluşturucu oluşturmak yalnızca aslında paralel olarak çalıştırıldıklarında yararlıdır.

  Birden çok dizin oluşturucuyu paralel olarak çalıştırmak için, uygun sayıda bölüm ve çoğaltma oluşturarak arama hizmetinizi ölçeklendirin. Örneğin, arama hizmetinizin 6 arama birimi varsa (örneğin, 2 bölüm x 3 çoğaltmalar), 6 Dizin oluşturucular eşzamanlı olarak çalışabilir ve dizin oluşturma sırasında altı kat artışına neden olur. Ölçeklendirme ve kapasite planlaması hakkında daha fazla bilgi edinmek için bkz. [Azure bilişsel arama hizmetinin kapasitesini ayarlama](search-capacity-planning.md).

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>Hataları işleme

Dizin oluşturma sırasında yaygın olarak oluşan hatalar, desteklenmeyen içerik türleri, eksik içerik veya büyük Bloblar içerir.

Varsayılan olarak, blob Indexer, desteklenmeyen içerik türü (örneğin, bir görüntü) ile bir blob ile karşılaştığında yanıt vermez. `excludedFileNameExtensions`Belirli içerik türlerini atlamak için parametresini kullanabilirsiniz. Ancak, hata oluşması durumunda bile devam etmek için dizin oluşturmak isteyebilirsiniz ve sonra tek tek belgelerde hata ayıklaması yapabilirsiniz. Dizin Oluşturucu hataları hakkında daha fazla bilgi için bkz. [ortak Dizin Oluşturucu sorunlarını giderme](search-indexer-troubleshooting.md) ve [Dizin Oluşturucu hataları ve uyarıları](cognitive-search-common-errors-warnings.md).

### <a name="respond-to-errors"></a>Hatalara yanıt verme

Hata oluştuğunda dizin oluşturucunun yanıtını denetleyen dört Dizin Oluşturucu özelliği vardır. Aşağıdaki örneklerde, Dizin Oluşturucu tanımında bu özelliklerin nasıl ayarlanacağı gösterilmektedir. Bir Dizin Oluşturucu zaten mevcutsa, portalda tanımı düzenleyerek bu özellikleri ekleyebilirsiniz.

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` ve `"maxFailedItemsPerBatch"`

Blob 'ları ayrıştırırken veya bir dizine belge eklerken herhangi bir işlem noktasında hatalar meydana geliyorsa dizin oluşturmaya devam edin. Bu özellikleri kabul edilebilir hataların sayısına ayarlayın. Bir değeri, `-1` kaç hatanın meydana oluşduğuna bakılmaksızın işleme izin verir. Aksi takdirde, değer pozitif bir tamsayıdır.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` ve `"failOnUnprocessableDocument"` 

Bazı Bloblar için Azure Bilişsel Arama, içerik türünü belirleyemez veya başka türlü desteklenen bir içerik türünün bir belgesini işleyemez. Bu hata koşullarını yoksaymak için yapılandırma parametrelerini şu şekilde ayarlayın `false` :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>Rahat Dizin Oluşturucu kısıtlamaları

Ayrıca, bir hata koşulunun mevcut olup olmadığını etkin bir şekilde belirten [BLOB yapılandırma özelliklerini](/rest/api/searchservice/create-indexer#blob-configuration-parameters) ayarlayabilirsiniz. Aşağıdaki özellik, aksi takdirde meydana gelen hataları ortadan gizlemeyi, daha rahat kısıtlamalar verebilir.

+ `"indexStorageMetadataOnlyForOversizedDocuments"` işlemek için çok büyük olan blob içeriği için depolama meta verilerini dizine eklemek için. Büyük bir blob, varsayılan olarak hata olarak değerlendirilir. BLOB boyutu sınırları için bkz. [hizmet limitleri](search-limits-quotas-capacity.md).

## <a name="see-also"></a>Ayrıca bkz.

+ [Azure Bilişsel Arama'daki Dizin Oluşturucular](search-indexer-overview.md)
+ [Dizin oluşturucu oluşturma](search-howto-create-indexers.md)
+ [Bloblara göre AI zenginleştirme genel bakış](search-blob-ai-integration.md)
+ [Bloblarda arama genel bakış](search-blob-storage-integration.md)