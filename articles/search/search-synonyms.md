---
title: Arama dizini üzerinde sorgu genişletmesi için eş anlamlılar
titleSuffix: Azure Cognitive Search
description: Bir Azure Bilişsel Arama dizininde arama sorgusunun kapsamını genişletmek için bir eş anlamlı eşleme oluşturun. Kapsam, bir listede sağladığınız eşdeğer terimleri içerecek şekilde belirlenir.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7c94ad096cf7d0d01bf2076f6748b49cf4ae1bb4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794219"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Azure Bilişsel Arama eş anlamlılar

Arama altyapılarındaki eş anlamlılar, kullanıcının terimi gerçekten sağlaması gerekmeden bir sorgunun kapsamını örtülü olarak genişletmekte olan eşdeğer terimleri ilişkilendirir. Örneğin, "köpek" teriminin ve "Canine" ve "pupkopyala" teriminin eşanlamlısı, "köpek", "Canine" veya "pupkopyala" gibi tüm belgeler, sorgunun kapsamı içinde yer alacak.

Azure Bilişsel Arama 'de, eş anlamlı genişletmesi sorgu zamanında yapılır. Mevcut işlemlere kesinti olmadan, bir hizmete eş anlamlı eşlemeler ekleyebilirsiniz. Dizini yeniden derlemek zorunda kalmadan, bir alan tanımına bir **eş anlamlı Eşeşlemeler** özelliği ekleyebilirsiniz.

## <a name="create-synonyms"></a>Eş anlamlı oluştur

Eş anlamlıları oluşturmak için portal desteği yoktur, ancak REST API veya .NET SDK 'sını kullanabilirsiniz. REST ile çalışmaya başlamak için bu API 'YI kullanarak isteklerin [Postman](search-get-started-postman.md) ve formüllerinin kullanılması önerilir: [eş anlamlı haritalar oluşturma](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map). Geliştiriciler C# için, [kullanarak C#Azure bilişsel arama 'da eşanlamlı ekleme ](search-synonyms-tutorial-sdk.md)ile çalışmaya başlayın.

İsteğe bağlı olarak, hizmet tarafı şifreleme için [müşteri tarafından yönetilen anahtarlar](search-security-manage-encryption-keys.md) kullanıyorsanız, bu korumayı eş anlamlı Haritalarınızın içeriğine uygulayabilirsiniz.

## <a name="use-synonyms"></a>Eş anlamlıları kullan

Azure Bilişsel Arama 'de, eş anlamlı destek, sizin tanımladığınız ve hizmetinize yüklediğiniz eş anlamlılar haritalarını temel alır. Bu haritalar bağımsız bir kaynak (dizinler veya veri kaynakları gibi) oluşturur ve arama hizmetinizdeki herhangi bir dizinde aranabilir bir alan tarafından kullanılabilir.

Eş anlamlı haritalar ve dizinler bağımsız olarak korunur. Bir eş anlamlı eşleme tanımladıktan ve bu dosyayı hizmetinize yükledikten sonra, alan tanımında eş anlamlılar özelliğini **, eş anlamlılar adlı yeni** bir özellik ekleyerek etkinleştirebilirsiniz. Bir eş anlamlı eşleme oluşturmak, güncelleştirmek ve silmek her zaman bir tam belge işlemidir. Bu, eş anlamlı eşleme parçalarını artımlı olarak oluşturamaz, güncelleştiremez veya silemezsiniz. Tek bir girdinin güncelleştirilmesi, yeniden yükleme gerektirir.

Eş anlamlıları arama uygulamanıza eklemek iki adımlı bir işlemdir:

1.  Aşağıdaki API 'Ler aracılığıyla arama hizmetinize bir eş anlamlı eşleme ekleyin.  

2.  Dizin tanımında eş anlamlı eşlemeyi kullanmak için aranabilir bir alan yapılandırın.

Arama uygulamanız için birden fazla eş anlamlı harita oluşturabilirsiniz (örneğin, uygulamanız çok dilli müşteri temelini destekliyorsa, dile göre). Şu anda bir alan yalnızca birini kullanabilir. Bir alanın eş eşlemler özelliğini dilediğiniz zaman güncelleştirebilirsiniz.

### <a name="synonymmaps-resource-apis"></a>Eş eşlemler kaynak API 'Leri

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>POST veya PUT kullanarak hizmetiniz altına bir eş anlamlı harita ekleyin veya güncelleştirin.

Eş anlamlı eşlemeler POST veya PUT aracılığıyla hizmete yüklenir. Her kural yeni satır karakteriyle (' \n ') sınırlandırmalıdır. Tüm diğer SKU 'Larda ücretsiz bir hizmette ve 10.000 kurallarında, eş anlamlı eşleme başına en fazla 5.000 kural tanımlayabilirsiniz. Her kural en fazla 20 genişleme sahip olabilir.

Eş anlamlı eşlemeler aşağıda açıklanan Apache Solr biçiminde olmalıdır. Farklı bir biçimde mevcut bir eş anlamlı sözlüğü varsa ve bunu doğrudan kullanmak istiyorsanız [UserVoice](https://feedback.azure.com/forums/263029-azure-search)'ta bize bildirin.

Aşağıdaki örnekte olduğu gibi, HTTP POST kullanarak yeni bir eş anlamlı eşleme oluşturabilirsiniz:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Alternatif olarak, koy ' u kullanabilir ve URI üzerinde eş anlamlı eşleme adını belirtebilirsiniz. Eş anlamlı eşleme yoksa, oluşturulur.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Apache Solr eş anlamlı biçimi

Solr biçimi eşdeğer ve açık eş anlamlı eşlemelerini destekler. Eşleme kuralları, bu belgede açıklanan Apache Solr 'nin açık kaynaklı eş anlamlı filtre belirtimine uyar: [eşanlamlı Ymfilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Eşdeğer eş anlamlılar için örnek bir kural aşağıda verilmiştir.
```
USA, United States, United States of America
```

Yukarıdaki kuralla birlikte "USA" arama sorgusu "ABD" veya "Birleşik Devletler" ya da "Birleşik Devletler" olarak genişletilir.

Açık eşleme bir ok "= >" ile gösterilir. Belirtildiğinde, "= >" öğesinin sol tarafıyla eşleşen bir arama sorgusunun terim sırası, sağ taraftaki alternatiflerle birlikte değişir. Aşağıdaki kural verildiğinde, "Washington", "yıkama" sorguları üzerinde arama yapın. ya da "WA" tümü "WA" olarak yeniden yazılır. Açık eşleme yalnızca belirtilen yönde geçerlidir ve bu durumda "WA" sorgusunu "Washington" olarak yeniden yazmaz.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Hizmetiniz altındaki eş anlamlı haritaları listeleyin.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Hizmetiniz altında bir eş anlamlı eşleme alın.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Hizmetiniz altındaki bir eş anlamlı eşlemeyi silin.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Dizin tanımında eş anlamlı eşlemeyi kullanmak için aranabilir bir alan yapılandırın.

Yeni bir alan özelliği olan eşanlamlı bir alan için kullanılacak bir eş anlamlı eşleme belirtmek için, **eş** anlamlılar özelliği kullanılabilir. Eş anlamlı eşlemeler hizmet düzeyi kaynaklardır ve hizmet altındaki bir dizinin herhangi bir alanı tarafından başvurulabilir.

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

' Edm. String ' veya ' Collection (EDM. String) ' türündeki aranabilir alanlar için **eş anlamlı** alanları belirtilebilir.

> [!NOTE]
> Alan başına yalnızca bir eş anlamlı eşlemeye sahip olabilirsiniz. Birden çok eş anlamlı eşleme kullanmak istiyorsanız lütfen [UserVoice](https://feedback.azure.com/forums/263029-azure-search)üzerinde bize bildirin.

## <a name="impact-of-synonyms-on-other-search-features"></a>Diğer arama özelliklerinde eş anlamlıların etkisi

Eş anlamlılar özelliği, veya işleciyle birlikte eşanlamlı olan özgün sorguyu yeniden yazar. Bu nedenle, isabet vurgulama ve Puanlama profillerinin özgün terim ve eş anlamlıları eşdeğer olarak kabul eder.

Eş anlamlı özelliği arama sorguları için geçerlidir ve filtreler veya modeller için geçerli değildir. Benzer şekilde, öneriler yalnızca özgün terime dayalıdır; eş anlamlı eşleşmeler yanıtta görünmez.

Joker karakter arama terimleri için eş anlamlı genişletmeleri uygulanmaz; ön ek, belirsiz ve Regex terimleri genişletilmedi.

Eş anlamlı genişletme ve joker karakter, Regex veya benzer aramalar uygulayan tek bir sorgu yapmanız gerekiyorsa, veya söz dizimini kullanarak sorguları birleştirebilirsiniz. Örneğin, basit sorgu söz dizimi için eş anlamlıları joker karakterlerle birleştirmek için, terim `<query> | <query>*` olur.

Geliştirme (üretim dışı) ortamında var olan bir dizininiz varsa, eş anlamlılar eklemenin, arama deneyimini nasıl değiştirdiğinden, Puanlama profilleri, isabet vurgulama ve öneriler de dahil olmak üzere küçük bir sözlükten deneyin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Eş anlamlı eşleme oluşturma](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)