---
title: Azure Tablo depolama içeriğini arama
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama Dizinleyicisi ile Azure Tablo depolamada depolanan verilerin nasıl dizinleyeceğinizi öğrenin.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8f6c0454497b1cb1d62417e566e9662469c56d0
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113007"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Azure Bilişsel Arama Azure Tablo depolamadaki tabloları dizin oluşturma

Bu makalede, Azure Tablo depolamada depolanan verileri indekslemek için Azure Bilişsel Arama nasıl kullanılacağı gösterilmektedir.

## <a name="set-up-azure-table-storage-indexing"></a>Azure Tablo depolama Dizin oluşturmayı ayarlama

Şu kaynakları kullanarak bir Azure Tablo depolama Dizin Oluşturucu oluşturabilirsiniz:

* [Azure Portal](https://ms.portal.azure.com)
* Azure Bilişsel Arama [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Bilişsel Arama [.NET SDK](https://aka.ms/search-sdk)

Burada, REST API kullanarak akışı gösteririz. 

### <a name="step-1-create-a-datasource"></a>1\. Adım: veri kaynağı oluşturma

Bir veri kaynağı, hangi verilerin dizine alınacağı, verilere erişmek için gereken kimlik bilgilerinin ve Azure Bilişsel Arama 'nin verilerdeki değişiklikleri verimli bir şekilde belirlemesine olanak tanıyan ilkelere göre belirler.

Tablo dizini oluşturma için, veri kaynağı aşağıdaki özelliklere sahip olmalıdır:

- **ad** , arama hizmetinizin içindeki veri kaynağının benzersiz adıdır.
- **tür** `azuretable`olmalıdır.
- **kimlik bilgileri** parametresi, depolama hesabı bağlantı dizesini içerir. Ayrıntılar için [kimlik bilgilerini belirtin](#Credentials) bölümüne bakın.
- **kapsayıcı** , tablo adını ve isteğe bağlı bir sorguyu ayarlar.
    - `name` parametresini kullanarak tablo adını belirtin.
    - İsteğe bağlı olarak, `query` parametresini kullanarak bir sorgu belirtin. 

> [!IMPORTANT] 
> Mümkün olduğunda, daha iyi performans için PartitionKey üzerinde bir filtre kullanın. Diğer herhangi bir sorgu tam tablo taraması yapar ve bu da büyük tablolar için düşük performansa neden olur. [Performans konuları](#Performance) bölümüne bakın.


Bir veri kaynağı oluşturmak için:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

DataSource API 'SI oluşturma hakkında daha fazla bilgi için bkz. [veri kaynağı oluşturma](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Kimlik bilgilerini belirtme yolları ####

Aşağıdaki yollarla tablo için kimlik bilgilerini sağlayabilirsiniz: 

- **Tam erişimli depolama hesabı bağlantı dizesi**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Azure Portal **depolama hesabı dikey penceresine** giderek > **Ayarlar** > **anahtarlar** (klasik depolama hesapları için) veya **Ayarlar** > **erişim anahtarları** (Azure Resource Manager depolama hesapları için) üzerinden bağlantı dizesini alabilir.
- **Depolama hesabı paylaşılan erişim imzası bağlantı dizesi**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` paylaşılan erişim imzasının, kapsayıcılar (Bu durumda tablolar) ve nesneler (tablo satırları) üzerinde liste ve okuma izinlerine sahip olması gerekir.
-  **Tablo paylaşılan erişim imzası**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` paylaşılan erişim imzasının tablo üzerinde sorgu (okuma) izinlerine sahip olması gerekir.

Depolama paylaşılan erişim imzaları hakkında daha fazla bilgi için bkz. [paylaşılan erişim Imzalarını kullanma](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Paylaşılan erişim imzası kimlik bilgilerini kullanıyorsanız, bu veri kaynağı kimlik bilgilerini yenilenen imzalara göre düzenli aralıklarla güncelleştirmeniz gerekir. Paylaşılan erişim imzası kimlik bilgilerinin kullanım süreleri dolarsa, Dizin Oluşturucu, "bağlantı dizesinde girilen kimlik bilgileri geçersiz veya süreleri sona erdiğinde" aşağıdakine benzer bir hata iletisiyle başarısız olur.  

### <a name="step-2-create-an-index"></a>2\. Adım: Dizin oluşturma
Dizin, bir belgedeki alanları, öznitelikleri ve arama deneyimini şekillendirieden diğer yapıları belirler.

Bir dizin oluşturmak için:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Dizinler oluşturma hakkında daha fazla bilgi için bkz. [Dizin oluşturma](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>3\. Adım: Dizin Oluşturucu oluşturma
Dizin Oluşturucu bir veri kaynağını hedef arama diziniyle bağlar ve veri yenilemeyi otomatikleştirmek için bir zamanlama sağlar. 

Dizin ve veri kaynağı oluşturulduktan sonra, Dizin oluşturucuyu oluşturmaya hazırsınız:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Bu Dizin Oluşturucu her iki saatte bir çalışır. (Zamanlama aralığı "PT2H" olarak ayarlanır.) Her 30 dakikada bir dizin oluşturucu çalıştırmak için, aralığı "PT30M" olarak ayarlayın. Desteklenen en kısa Aralık beş dakikadır. Zamanlama isteğe bağlıdır; Atlanırsa, bir Dizin Oluşturucu oluşturulduğunda yalnızca bir kez çalışır. Ancak, bir dizin oluşturucuyu dilediğiniz zaman isteğe bağlı olarak çalıştırabilirsiniz.   

Dizin Oluşturucu oluşturma API 'SI hakkında daha fazla bilgi için bkz. [Dizin Oluşturucu oluşturma](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Dizin Oluşturucu zamanlamalarını tanımlama hakkında daha fazla bilgi için bkz. [Azure bilişsel arama için Dizin Oluşturucu zamanlama](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Farklı alan adlarıyla dağıt
Bazen, var olan dizininizdeki alan adları, tablonuzdaki Özellik adlarından farklı olabilir. Alan eşlemelerini, tablodaki özellik adlarını, arama dizininizdeki alan adlarıyla eşlemek için kullanabilirsiniz. Alan eşlemeleri hakkında daha fazla bilgi edinmek için bkz. [Azure bilişsel arama Dizin Oluşturucu alan eşlemeleri köprü kaynakları ve arama dizinleri arasındaki farklılıklar](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Belge anahtarlarını işle
Azure Bilişsel Arama 'de belge anahtarı bir belgeyi benzersiz şekilde tanımlar. Her arama dizininin `Edm.String`türünde tam olarak bir anahtar alanı olmalıdır. Dizine eklenmekte olan her belge için anahtar alanı gereklidir. (Aslında, tek gerekli alandır.)

Tablo satırları bileşik bir anahtara sahip olduğundan Azure Bilişsel Arama, bölüm anahtarı ve satır anahtarı değerlerinin birleşimi olan `Key` adlı yapay bir alan oluşturur. Örneğin, bir satırın PartitionKey değeri `PK1` ve RowKey `RK1`, `Key` alanının değeri `PK1RK1`olur.

> [!NOTE]
> `Key` değeri, çizgiler gibi belge anahtarlarında geçersiz olan karakterler içeriyor olabilir. `base64Encode` [alan eşleme işlevini](search-indexer-field-mappings.md#base64EncodeFunction)kullanarak geçersiz karakterlerle işlem yapabilirsiniz. Bunu yaparsanız, Arama gibi API çağrılarında belge anahtarlarını geçirirken de güvenli URL Base64 kodlamasını kullanmayı unutmayın.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Artımlı dizin oluşturma ve silme algılaması
Bir zamanlamaya göre çalıştırılacak bir tablo dizin oluşturucuyu ayarlarken, bir satırın `Timestamp` değeri tarafından belirlendiği şekilde yalnızca yeni veya güncelleştirilmiş satırları yeniden dizinleyebilirsiniz. Değişiklik algılama ilkesi belirtmeniz gerekmez. Artımlı dizin oluşturma sizin için otomatik olarak etkinleştirilir.

Belirli belgelerin dizinden kaldırılması gerektiğini belirtmek için, bir geçici silme stratejisi kullanabilirsiniz. Bir satırı silmek yerine, silindiğini göstermek için bir özellik ekleyin ve DataSource üzerinde bir geçici silme algılama ilkesi ayarlayın. Örneğin, aşağıdaki ilke, satırda `"true"`değer `IsDeleted` bir özellik varsa bir satırın silindiğini kabul eder:

    PUT https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Azure Bilişsel Arama, varsayılan olarak şu sorgu filtresini kullanır: `Timestamp >= HighWaterMarkValue`. Azure tablolarının `Timestamp` alanında ikincil bir dizini olmadığından, bu tür bir sorgu tam tablo taraması gerektirir ve bu nedenle büyük tablolar için yavaş olur.


Tablo dizinleme performansını iyileştirmeye yönelik iki olası yaklaşım aşağıda verilmiştir. Bu yaklaşımların her ikisi de tablo bölümlerinin kullanılmasına bağımlıdır: 

- Verileriniz doğal olarak çeşitli bölüm aralıklarında bölümlenebilir, her bölüm aralığı için bir veri kaynağı ve karşılık gelen bir Dizin Oluşturucu oluşturun. Her bir dizin oluşturucunun şimdi yalnızca belirli bir bölüm aralığını işlemesi gerekir ve bu, daha iyi sorgu performansına neden olur. Dizine alınması gereken verilerin az sayıda sabit bölümü varsa ve daha iyi: her bir Dizin Oluşturucu yalnızca bir bölüm taraması yapar. Örneğin, `000` anahtarlarla `100`bir bölüm aralığını işlemek için bir veri kaynağı oluşturmak için, şöyle bir sorgu kullanın: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Verileriniz zamana göre bölümlense (örneğin, her gün veya hafta için yeni bir bölüm oluşturursanız) aşağıdaki yaklaşımı göz önünde bulundurun: 
    - Şu biçimde bir sorgu kullanın: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Dizin Oluşturucu [durum API 'sini](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)kullanarak dizin oluşturucunun ilerlemesini izleyin ve en son başarılı yüksek su işaretine göre sorgunun `<TimeStamp>` koşulunu düzenli olarak güncelleştirin. 
    - Bu yaklaşım sayesinde, bir yeniden dizin oluşturmayı tetiklemeniz gerekiyorsa, Dizin oluşturucuyu sıfırlamaya ek olarak DataSource sorgusunu sıfırlamanız gerekir. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Azure Bilişsel Arama daha iyi hale getirmemize yardımcı olun
Geliştirmeler için özellik istekleriniz veya fikirler varsa, bunları [UserVoice sitemiz](https://feedback.azure.com/forums/263029-azure-search/)üzerinde gönderebilirsiniz.
