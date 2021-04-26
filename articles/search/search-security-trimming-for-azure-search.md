---
title: Sonuçları kırpma için güvenlik filtreleri
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama arama sonuçları, güvenlik filtrelerini ve Kullanıcı kimliklerini kullanarak belge düzeyinde güvenlik ayrıcalıkları uygulamayı öğrenin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 8bd162fcf2011d2ccce716564763e7f54f19ff69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97631812"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Azure Bilişsel Arama sonuçları kırpma için güvenlik filtreleri

Arama sonuçlarını kullanıcı kimliğine göre Azure Bilişsel Arama kırpmak için güvenlik filtreleri uygulayabilirsiniz. Bu arama deneyimi genellikle, arama isteğini belge üzerinde izinlere sahip olan ilkeleri içeren bir alana karşı istek yapanın kimliğini karşılaştırmaya gerek duyar. Bir eşleşme bulunduğunda, kullanıcının veya sorumlunun (bir grup veya rol gibi) bu belgeye erişimi vardır.

Güvenlik filtrelemesi elde etmenin bir yolu, eşitlik ifadelerinin karmaşık bir ilişkisini kullanmaktır: Örneğin,, vb `Id eq 'id1' or Id eq 'id2'` . Bu yaklaşım hataya açıktır, bakım zordur ve listenin yüzlerce veya binlerce değer içermesi durumunda sorgu yanıt süresini pek çok saniyeye yavaşlatır. 

Daha basit ve daha hızlı bir yaklaşım `search.in` işlevi kullanmaktır. `search.in(Id, 'id1, id2, ...')`Bir eşitlik ifadesi yerine kullanıyorsanız, alt saniyelik yanıt sürelerini bekleyebilir.

Bu makalede, aşağıdaki adımları kullanarak güvenlik filtrelemeyi nasıl gerçekleştirebileceğiniz gösterilmektedir:
> [!div class="checklist"]
> * Asıl tanımlayıcıları içeren bir alan oluşturun 
> * Mevcut belgeleri ilgili asıl tanımlayıcılarla gönderin veya güncelleştirin
> * İle `search.in` bir arama isteği verme `filter`

>[!NOTE]
> Asıl tanımlayıcıları alma işlemi bu belgede kapsanmıyor. Bunu kimlik hizmeti sağlayıcınızdan almalısınız.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede bir [Azure aboneliğiniz](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), bir[Azure bilişsel arama hizmeti](search-create-service-portal.md)ve bir [Dizin](search-what-is-an-index.md)olduğunu varsaymaktadır.  

## <a name="create-security-field"></a>Güvenlik alanı oluştur

Belgeleriniz hangi grupların erişimi olduğunu belirten bir alan içermelidir. Bu bilgiler, veren olarak döndürülen sonuç kümesinden belgelerin seçildiği veya reddedildiği bir filtre ölçütü olur.
Güvenli dosyalar dizini olduğunu ve her dosyaya farklı bir kullanıcı kümesi tarafından erişilebilmesini varsayalım.

1. Alan ekleyin `group_ids` (burada herhangi bir ad seçebilirsiniz) `Collection(Edm.String)` . `filterable` `true` Arama sonuçlarının, kullanıcının sahip olduğu erişime göre filtrelenmesini sağlamak için alanın olarak ayarlanmış bir özniteliği olduğundan emin olun. Örneğin, `group_ids` alanı `["group_id1, group_id2"]` "secured_file_b" olan belge için olarak ayarlarsanız `file_name` , yalnızca "group_id1" veya "group_id2" grup kimliklerine ait olan kullanıcılar dosyaya okuma erişimine sahiptir.
   
   Alan `retrievable` özniteliğinin, `false` arama isteğinin bir parçası olarak döndürülmemesi için olarak ayarlandığından emin olun.

2. Ayrıca `file_id` `file_name` , bu örneğin sake için ve alanlarını ekleyin.  

    ```JSON
    {
        "name": "securedfiles",  
        "fields": [
            {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
            {"name": "file_name", "type": "Edm.String"},
            {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
        ]
    }
    ```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>REST API kullanarak dizininizdeki verileri iletme
  
Dizininizin URL uç noktasına bir HTTP POST isteği gönderin. HTTP isteğinin gövdesi, eklenecek belgeleri içeren bir JSON nesnesidir:

```http
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2020-06-30  
Content-Type: application/json
api-key: [admin key]
```

İstek gövdesinde, belgelerinizin içeriğini belirtin:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Mevcut bir belgeyi Grup listesi ile güncelleştirmeniz gerekiyorsa, `merge` ya da `mergeOrUpload` eylemini kullanabilirsiniz:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Belge ekleme veya güncelleştirme hakkında ayrıntılı bilgi için [belgeleri düzenleme belgesini](/rest/api/searchservice/addupdate-or-delete-documents)okuyabilirsiniz.

## <a name="apply-the-security-filter"></a>Güvenlik filtresini uygulama

Belgelere erişimi temel alarak kırpmak için bir `group_ids` filtre içeren bir arama sorgusu oluşturmalısınız `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` , burada ' group_id1, group_id2,... ' , arama isteği verenin ait olduğu gruplarıdır.

Bu filtre, `group_ids` alanın verilen tanımlayıcılardan birini içerdiği tüm belgelerle eşleşir.
Azure Bilişsel Arama kullanarak belge arama hakkında ayrıntılı bilgi için, [arama belgelerini](/rest/api/searchservice/search-documents)okuyabilirsiniz.
Bu örnekte, bir POST isteği kullanılarak belgelerin nasıl aranacağı gösterilmektedir.

HTTP POST isteğini verme:

```http
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2020-06-30
Content-Type: application/json  
api-key: [admin or query key]
```

İstek gövdesinde filtreyi belirtin:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Belgeleri `group_ids` "group_id1" veya "group_id2" içerdiğinde geri almalısınız. Diğer bir deyişle, istek verenin okuma erişimine sahip olduğu belgeleri alırsınız.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, sonuçları kullanıcı kimliğine ve işleve göre filtrelemeye yönelik bir model açıklanmaktadır `search.in()` . Bu işlevi, istenen kullanıcının her bir hedef belge ile ilişkili asıl tanımlayıcılarla eşleşmesi için ilke tanımlayıcılarını geçirmek için kullanabilirsiniz. Bir arama isteği işlendiği zaman, işlevi, `search.in` Kullanıcı sorumlularının hiçbirinin okuma erişimine sahip olmadığı arama sonuçlarını filtreler. Asıl tanımlayıcılar güvenlik grupları, roller ve hatta kullanıcının kendi kimliği gibi şeyleri temsil edebilir.

Active Directory temel alan alternatif bir düzende veya diğer güvenlik özelliklerini tekrar ziyaret etmek için aşağıdaki bağlantılara bakın.

* [Active Directory kimliklerini kullanarak sonuçları kırpma için güvenlik filtreleri](search-security-trimming-for-azure-search-with-aad.md)
* [Azure Bilişsel Arama güvenliği](search-security-overview.md)