---
title: Bilişsel Beceri başına mil
titleSuffix: Azure Cognitive Search
description: Yapılandırılmamış verilerden meta verileri ve yapılandırılmış bilgileri ayıklayın ve Azure Bilişsel Arama bir AI zenginleştirme ardışık düzeninde bir karmaşık tür olarak şekillendirin.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e9ba540ee0eda2be50c88a89a139032d8d99752d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791869"
---
# <a name="shaper-cognitive-skill"></a>Bilişsel Beceri başına mil

**Mil başına** , birkaç girişi, daha sonra zenginleştirme ardışık düzeninde başvurulabilen [karmaşık bir türde](search-howto-complex-data-types.md) birleştirir. Her **Beceri,** temel olarak bir yapı oluşturmanızı, bu yapının üyelerinin adını tanımlamanızı ve her üyeye değer atamanızı sağlar. Birleştirme senaryolarında yararlı olan birleştirilmiş alan örnekleri, tek bir yapıya bir ad ve soyadı, tek bir yapıya şehir ve eyalet, benzersiz kimlik oluşturmak için ise tek bir yapıya bir ad ve Doğum tarihi birleştirmek içerir.

Ayrıca, [Senaryo 3](#nested-complex-types) ' te gösterilen yetenek **başına mil** , girişe Isteğe bağlı bir *SourceContext* özelliği ekler. *Kaynak* ve *SourceContext* özellikleri birbirini dışlıyor. Giriş beceri bağlamındaki ise, *kaynağı*kullanmanız yeterlidir. Giriş, yetenek bağlamından *farklı* bir bağlamda bulunuyorsa *SourceContext*kullanın. *SourceContext* , kaynak olarak değinmekte olan belirli bir öğe ile iç içe bir giriş tanımlamanızı gerektirir. 

Çıkış adı her zaman "çıktı" olur. Dahili olarak, işlem hattı aşağıdaki örneklerde gösterildiği gibi "çözümleyiciler Edtext" gibi farklı bir adı eşleyebilir, **ancak niteliğin kendisi** yanıtta "çıktı" döndürür. Bu, zenginleştirilmiş belgelerde hata ayıklaması yapıyorsanız ve adlandırma tutarsızlığı fark ediyorsanız veya özel bir yetenek oluşturuyorsanız ve yanıtı kendi kendinize yapılandırırken önemli olabilir.

> [!NOTE]
> Beceri **başına mil** , bilişsel HIZMETLER API 'siyle bağlantılı değildir ve bunu kullanmak için ücretlendirilirsiniz. Yine de bir bilişsel [Hizmetler kaynağı iliştirmelisiniz](cognitive-search-attach-cognitive-services.md), ancak her gün çok az sayıda günlük zenginleştirme için sizi sınırlayan **ücretsiz** Kaynak seçeneğini geçersiz kılabilirsiniz.

## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. util. Shaperbeceri

## <a name="scenario-1-complex-types"></a>Senaryo 1: karmaşık türler

İki üyeye sahip olan *çözümleyiciler* adlı bir yapı oluşturmak istediğiniz bir senaryo düşünün: *metin* *ve yaklaşım*sırasıyla. Bir dizinde, çok parçalı aranabilir bir alana *karmaşık tür* denir ve genellikle kaynak verilere eşlenen karşılık gelen bir karmaşık yapıya sahip olduğunda oluşturulur.

Ancak karmaşık türler oluşturmaya yönelik başka bir yaklaşım, Beceri **başına biçimlendiricilerledir** . Bu yeteneği bir beceri dahil ederek, Beceri işleme sırasında bellek içi işlemler veri şekillerini, daha sonra dizininizdeki karmaşık bir türle eşleştirilebilen iç içe yapılar ile çıktısını alabilir. 

Aşağıdaki örnek beceri tanımı, üye adlarını girdi olarak sağlar. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Örnek Dizin

Bir Dizin Oluşturucu tarafından bir beceri çağrılır ve bir Dizin Oluşturucu bir dizin gerektirir. Dizininizdeki bir karmaşık alan temsili aşağıdaki örneğe benzer şekilde görünebilir. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Beceri girişi

Yetenek **başına bu mil** için kullanılabilir giriş sağlayan gelen JSON belgesi şu şekilde olabilir:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Yetenek çıkışı

**Biçimlendiriciye** , *metin* ve yaklaşım birleştirilmiş öğeleri Ile *çözümleyiciler, Çözümleyicileri edtext* adlı yeni bir öğe *oluşturur.* Bu çıktı dizin şemasına uyar. Azure Bilişsel Arama dizininde içeri ve dizinli olur.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Senaryo 2: giriş birleştirme

Başka bir örnekte, ardışık düzen işlemenin farklı aşamalarında, kitabın başlığını ayıkladığınızı ve kitabın farklı sayfalarındaki bölüm başlıklarını çıkardığınızı düşünelim. Artık bu çeşitli girdilerden oluşan tek bir yapı oluşturabilirsiniz.

Bu senaryo için **Shaper** beceri tanımı aşağıdaki örneğe benzeyebilir:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Yetenek çıkışı
Bu durumda, **mil** tek bir dizi oluşturmak için tüm bölüm başlıklarını düzleştirir. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Senaryo 3: iç içe bağlamlardan giriş birleştirme

Bir kitabın unvanına, bölümlerine ve içeriğine sahip olduğunu ve içerik üzerinde varlık tanıma ve anahtar tümceciklerini çalıştırmanın yanı sıra, artık farklı becerilerden sonuçları bölüm adı, varlıklar ve anahtar tümcecikleriyle tek bir şekle toplamanız gerektiğini düşünün.

Bu senaryo için **Shaper** beceri tanımı aşağıdaki örneğe benzeyebilir:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Yetenek çıkışı
Bu durumda, **Shaper** karmaşık bir tür oluşturur. Bu yapı bellek içinde mevcuttur. Bir [bilgi deposuna](knowledge-store-concept-intro.md)kaydetmek istiyorsanız, Beceri uygulamanızda depolama özelliklerini tanımlayan bir projeksiyon oluşturmanız gerekir.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Ayrıca bkz.

+ [Yerleşik yetenekler](cognitive-search-predefined-skills.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [Karmaşık türleri kullanma](search-howto-complex-data-types.md)
+ [Bilgi deposu (Önizleme)](knowledge-store-concept-intro.md)
+ [Bilgi deposu ile çalışmaya başlama](knowledge-store-howto.md)