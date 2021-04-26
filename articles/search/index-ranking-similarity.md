---
title: Benzerlik algoritmasını yapılandırma
titleSuffix: Azure Cognitive Search
description: Daha eski Arama hizmetlerinde BM25 etkinleştirmeyi ve BM25 parametrelerinin dizinlerinizin içeriğine daha iyi uyum sağlamak için nasıl değiştirileceğini öğrenin.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 52b3523d3c092f1b9375f53038cc3b20d0ddedcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232843"
---
# <a name="configure-the-similarity-ranking-algorithm-in-azure-cognitive-search"></a>Azure Bilişsel Arama benzerlik derecelendirme algoritmasını yapılandırma

Azure Bilişsel Arama, iki benzerlik derecelendirme algoritmasını destekler:

+ 15 Temmuz 2020 ' ye kadar tüm arama hizmetleri tarafından kullanılan *Klasik bir benzerlik* algoritması.
+ 15 Temmuz 'dan sonra oluşturulan tüm Arama hizmetlerinde kullanılan *OKAPI BM25* algoritmasının bir uygulamasıdır.

BM25 derecelendirmesi, Kullanıcı beklentileriyle daha iyi uyum sağlayan arama derecelendirmelerine neden olduğunu eğilimi sağladığından yeni varsayılandır. Belge boyutu gibi etkenlere bağlı olarak sonuçları ayarlama [parametreleriyle](#set-bm25-parameters) birlikte gelir. 

15 Temmuz 2020 ' den sonra oluşturulan yeni hizmetler için, BM25 otomatik olarak kullanılır ve tek benzerlik algoritmadır. Yeni bir hizmette benzerlik olarak benzerliği ayarlamaya çalışırsanız, bu algoritma hizmet tarafından desteklenmediğinden bir HTTP 400 hatası döndürülür.

15 Temmuz 2020 tarihinden önce oluşturulan eski hizmetler için, klasik benzerlik varsayılan algoritmaya devam eder. Daha eski hizmetler, aşağıda açıklandığı gibi, dizin başına BM25 'e yükseltebilirsiniz. Klasik 'den BM25 'e geçiş yapıyorsanız, arama sonuçlarının nasıl sıralanmasına ilişkin bazı farklılıkları görmeyi bekleyebilir.

> [!NOTE]
> Şu anda seçili bölgelerde standart hizmetler için önizleme aşamasında olan anlam derecelendirmesi, daha fazla ilgili sonuç üretmede ileri bir adımdır. Diğer algoritmalardan farklı olarak, var olan bir sonuç kümesi üzerinde yineleme yapan bir eklenti özelliğidir. Daha fazla bilgi için bkz. [anlam aramasına genel bakış](semantic-search-overview.md) ve [anlam derecelendirmesi](semantic-ranking.md).

## <a name="enable-bm25-scoring-on-older-services"></a>Eski hizmetlerde BM25 Puanlama 'nı etkinleştir

15 Temmuz 2020 ' den önce oluşturulmuş bir arama hizmeti çalıştırıyorsanız, yeni dizinlerde bir benzerlik özelliği ayarlayarak BM25 'yi etkinleştirebilirsiniz. Özelliği yalnızca yeni dizinlerde kullanıma sunulur. bu nedenle, var olan bir dizinde BM25 istiyorsanız, dizini yeni bir benzerlik özelliği "Microsoft. Azure. Search. BM25Similarity" olarak ayarlanmış şekilde bırakmalısınız ve [yeniden oluşturmanız](search-howto-reindex.md) gerekir.

Bir dizin, benzerlik özelliği ile varsa, BM25Similarity veya Classıbenzerlik arasında geçiş yapabilirsiniz. 

Aşağıdaki bağlantılar, Azure SDK 'larında benzerlik özelliğini anlatmaktadır. 

| İstemci kitaplığı | Benzerlik özelliği |
|----------------|---------------------|
| .NET  | [Searchındex. benzerlik](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [Searchındex. Setbenzerlik](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [Searchındex. benzerlik](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [Searchındex 'te benzerlik özelliği](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>REST örneği

Ayrıca, aşağıdaki örnekte gösterildiği gibi [REST API](/rest/api/searchservice/create-index)de kullanabilirsiniz:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

## <a name="set-bm25-parameters"></a>BM25 parametrelerini ayarla

BM25 benzerlik, hesaplanan uygunluk Puanını denetlemek için iki Kullanıcı özelleştirilebilir parametre ekler. Dizin oluşturma sırasında BM25 parametrelerini veya dizin oluşturma sırasında BM25 algoritması belirtilmişse Dizin güncelleştirmesini ayarlayabilirsiniz.

| Özellik | Tür | Description |
|----------|------|-------------|
| K1 | sayı | Bir belge sorgu çiftinin son uygunluk puanına, eşleşen koşulların Dönem sıklığı arasındaki ölçekleme işlevini denetler. Değerler genellikle 0,0 ile 3,0 arasında, varsayılan olarak 1,2. </br></br>0,0 değeri bir "ikili model" temsil eder; burada tek bir eşleştirme terimi, bu terimin kaç kez göründüğüne bakılmaksızın tüm eşleşen belgeler için aynı olduğunda, daha büyük bir K1 değeri, belgede aynı terimin daha fazla örneği bulunduğunda bu puanın artmaya devam etmesine izin verir. </br></br>Daha yüksek bir K1 değeri kullanmak, birden çok terimi bir arama sorgusunun parçası olarak beklediği durumlarda önemli olabilir. Bu durumlarda, yalnızca tek bir tane, birden çok kez eşleşen belgeler üzerinde aranan farklı sorgu terimlerinin birçoğu ile eşleşen belgeleri tercih etmek isteyebilirsiniz. Örneğin, "Apollo spaceflight" terimlerini içeren belgeler için Dizin sorgulanırken "Apollo" terimini ("spaceflight" koşulunu) içeren Yunanca Mythology hakkında bir makalenin Puanını, "Tollo" ve "spaceflight" gibi tek bir kez açıkça bahsetme gibi başka bir makaleye kıyasla |
| b | sayı | Bir belge uzunluğunun, ilgi Puanını nasıl etkilediğini denetler. Değerler, varsayılan olarak 0,75 ile 0 ile 1 arasındadır. </br></br>0,0 değeri, belgenin uzunluğunun puanı etkilemeyeceği anlamına gelir, ancak 1,0 değeri, ölçüdeki sıklık teriminin etkisinin belgenin uzunluğuna göre normalleştirilmesine neden olur. </br></br>Belgenin uzunluğuna göre sıklık sıklığını normalleştirmek, uzun belgeleri yarmak istiyoruz. Bazı durumlarda, çok daha kısa bir belge ile karşılaştırıldığında daha fazla ilgisiz terim, bazı durumlarda daha uzun belgelerde bulunabilir. |

### <a name="setting-k1-and-b-parameters"></a>K1 ve b parametrelerini ayarlama

B veya K1 değerlerini ayarlamak veya değiştirmek için, BM25 benzerlik nesnesine ekleyin. Var olan bir dizinde bu değerlerin ayarlanması veya değiştirilmesi, dizini en az birkaç saniyede çevrimdışı hale getirir ve etkin dizin oluşturma ve sorgu isteklerinin başarısız olmasına neden olur. Sonuç olarak, Güncelleştirme isteğinin "Allowındexkesinti = true" parametresini ayarlamanız gerekir:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>Ayrıca bkz.  

+ [REST API başvurusu](/rest/api/searchservice/)
+ [Dizinlerinize Puanlama profilleri ekleme](index-add-scoring-profiles.md)
+ [Dizin API 'SI oluştur](/rest/api/searchservice/create-index)
+ [Azure Bilişsel Arama .NET SDK](/dotnet/api/overview/azure/search)