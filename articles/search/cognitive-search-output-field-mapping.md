---
title: Girişi çıkış alanlarına eşleyin
titleSuffix: Azure Cognitive Search
description: Kaynak veri alanlarını ayıklar ve zenginleştirin ve Azure Bilişsel Arama dizinindeki çıkış alanlarıyla eşleyin.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 58bb87d5af785d3cffd96f3bd02477f97ed967a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001312"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>AI zenginleştirilmiş alanları aranabilir bir dizinle eşleme

![Dizin Oluşturucu aşamaları](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "Dizin Oluşturucu aşamaları")

Bu makalede, aranabilir bir dizindeki çıkış alanlarına zenginleştirilmiş giriş alanlarını eşlemeyi öğreneceksiniz. [Bir beceri tanımladıktan](cognitive-search-defining-skillset.md)sonra, arama dizininizdeki belirli bir alana doğrudan değer katkıda bulunan herhangi bir yeteneğin çıkış alanlarını eşlemeniz gerekir.

İçeriği zenginleştirilmiş belgelerden dizine taşımak için çıkış alanı eşlemeleri gereklidir.  Zenginleştirilmiş belge gerçekten bir bilgi ağacıdır ve dizindeki karmaşık türler için destek olsa bile, bazen zenginleştirilmiş ağaç içindeki bilgileri daha basit bir türe (örneğin, dizeler dizisi) dönüştürmek isteyebilirsiniz. Çıkış alanı eşlemeleri, bilgileri düzleştirerek veri şekli dönüştürmeleri gerçekleştirmenize olanak tanır. Çıkış alanı eşlemeleri her zaman beceri yürütmeden sonra gerçekleşir, ancak bu aşamanın bir beceri tanımlı olmasa bile çalışması mümkündür.

Çıkış alanı eşlemelerinin örnekleri:

* Beceri bir parçası olarak, belgenizin her sayfasında bahsedilen kuruluşların adlarını ayıkladıysanız. Artık bu kuruluş adlarından her birini Edm. Collection türünde (EDM. String) dizininizdeki bir alana eşlemek istiyorsunuz.

* Beceri bir parçası olarak, "belge/translated_text" adlı yeni bir düğüm oluşturmuş olursunuz. Bu düğümdeki bilgileri dizininizdeki belirli bir alanla eşlemek istiyorsunuz.

* Bir beceri yok ancak Cosmos DB veritabanından karmaşık bir tür dizinleniyor. Bu karmaşık türdeki bir düğüme ulaşmak ve dizininizdeki bir alanla eşlemek istersiniz.

> [!NOTE]
> Son zamanlarda çıkış alanı eşlemelerinde işlevleri eşleme işlevlerini etkinleştirdik. Eşleme işlevleri hakkında daha fazla bilgi için bkz. [alan eşleme işlevleri](./search-indexer-field-mappings.md#field-mapping-functions)

## <a name="use-outputfieldmappings"></a>OutputFieldMappings kullanma

Alanları eşlemek için, `outputFieldMappings` Dizin Oluşturucu tanımınıza aşağıda gösterildiği gibi ekleyin:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

İsteğin gövdesi aşağıdaki şekilde yapılandırılır:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

Her bir çıkış alanı eşlemesi için, zenginleştirilmiş belge ağacındaki (sourceFieldName) verilerin konumunu ve dizinde başvurulan alanın adını (targetFieldName) ayarlayın.

## <a name="flattening-information-from-complex-types"></a>Karmaşık türlerden bilgileri düzleştirme 

Bir sourceFieldName içindeki yol bir öğeyi veya birden çok öğeyi temsil edebilir. Yukarıdaki örnekte, ```/document/content/sentiment``` tek bir sayısal değeri temsil ederken, ```/document/content/organizations/*/description``` çeşitli kuruluş açıklamalarını temsil eder. 

Çeşitli öğelerin olduğu durumlarda, öğelerin her birini içeren bir dizi içinde "düzleştirilmez". 

Daha fazla ```/document/content/organizations/*/description``` iyal, örneğin, *açıklamalar* alanındaki veriler, Dizin oluşturulmadan önce düz bir açıklama dizisi gibi görünür:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Bu önemli bir ilkedir, bu nedenle başka bir örnek sağlıyoruz. Zenginleştirme ağacının bir parçası olarak bir dizi karmaşık türde olduğunu düşünün. Aşağıda açıklandığı gibi karmaşık türlerde bir dizi olan customEntities adlı bir üye olduğunu varsayalım.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Burada, dizinlerinizin, varlıkların adlarından her birini depolamak istediğiniz türdeki koleksiyon (EDM. String) ' didenler ' adlı bir alana sahip olduğunu varsayalım. 

Bu, "" simgesi kullanılarak kolayca yapılabilir \* :

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Bu işlem, customEntities öğelerinin adlarından her birini şöyle tek bir dize dizisine "düzleştirebilir".

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Sonraki adımlar
Zenginleştirilmiş alanları aranabilir alanlara eşleştirdikten sonra, aranabilir alanların her biri için alan özniteliklerini [Dizin tanımının bir parçası olarak](search-what-is-an-index.md)ayarlayabilirsiniz.

Alan eşleme hakkında daha fazla bilgi için bkz. [Azure bilişsel arama Dizin oluşturucularda alan eşlemeleri](search-indexer-field-mappings.md).