---
title: Özel varlık arama bilişsel arama yeteneği
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama bilişsel arama ardışık düzeninde bulunan metinden farklı özel varlıkları ayıklayın. Bu yetenek Şu anda genel önizlemededir.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 0f233bdff54822037d15fde9ac62e34193794ad3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110317"
---
#     <a name="custom-entity-lookup-cognitive-skill"></a>Özel varlık arama Bilişsel Beceri

**Özel varlık arama** becerisi, özel, Kullanıcı tanımlı bir sözcük ve tümcecik listesinden metin arar. Bu listeyi kullanarak tüm belgeleri eşleşen varlıklarla Etiketler. Bu beceri, benzer ancak tam olmayan eşleşmeleri bulmak için uygulanabilecek belirsiz eşleştirmeyi de destekler.  

Bu yetenek bilişsel hizmetler API 'siyle bağlantılı değildir ve önizleme dönemi boyunca ücretsiz olarak kullanılabilir. Ancak günlük zenginleştirme limitini geçersiz kılmak için yine de bilişsel [Hizmetler kaynağı iliştirmelisiniz](./cognitive-search-attach-cognitive-services.md). Günlük sınır, Azure Bilişsel Arama üzerinden erişildiğinde bilişsel hizmetlere ücretsiz erişim için geçerlidir.

## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. Text. CustomEntityLookupSkill 

## <a name="data-limits"></a>Veri sınırları
+ Desteklenen en büyük giriş kaydı boyutu 256 MB 'tır. Verileri özel varlık arama beceriye göndermeden önce bölmeniz gerekirse, [metin bölme becerinizi](cognitive-search-skill-textsplit.md)kullanmayı göz önünde bulundurun.
+ *Entitiesdefinitionuri* parametresi kullanılarak sağlandıysa, desteklenen en yüksek varlık tanımı tablosu 10 MB 'tır. 
+ Varlıklar satır içi tanımlanmışsa *ınlineentitiesdefinition* parametresi kullanılarak, desteklenen en büyük boyut 10 KB 'tır.

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır.

| Parametre adı     | Açıklama |
|--------------------|-------------|
| `entitiesDefinitionUri`    | Eşleştirilecek tüm hedef metinleri içeren bir JSON veya CSV dosyasının yolu. Bu varlık tanımı, Dizin Oluşturucu çalıştırmasının başlangıcında okundu. Bu dosya için her türlü güncelleştirme, sonraki Çalışmayana kadar gerçekleştirilmeyecek. Bu yapılandırmaya HTTPS üzerinden erişilebilir olması gerekir. Beklenen CSV veya JSON şeması için aşağıda " [özel varlık tanımı](#custom-entity-definition-format) biçimi" başlığına bakın.|
|`inlineEntitiesDefinition` | Satır içi JSON varlık tanımları. Bu parametre, varsa entitiesDefinitionUri parametresinin yerini alır. En fazla 10 KB 'lik yapılandırma satır içi olarak sağlanmayabilir. Beklenen JSON şeması için aşağıdaki [özel varlık tanımına](#custom-entity-definition-format) bakın. |
|`defaultLanguageCode` |    Seçim Giriş metnini simgeleştirme ve ayırma için kullanılan giriş metninin dil kodu. Aşağıdaki diller desteklenir: `da, de, en, es, fi, fr, it, ko, pt` . Varsayılan değer English () ' dir `en` . LanguageCode-CountryCode biçimi geçirirseniz, yalnızca biçimin languageCode kısmı kullanılır.  |
|`globalDefaultCaseSensitive` | Seçim Beceri için varsayılan büyük/küçük harfe duyarlı değer. `defaultCaseSensitive`Bir varlığın değeri belirtilmemişse, bu değer bu `defaultCaseSensitive` varlık için değer olacaktır. |
|`globalDefaultAccentSensitive` | Seçim Beceri için varsayılan aksan duyarlı değer. `defaultAccentSensitive`Bir varlığın değeri belirtilmemişse, bu değer bu `defaultAccentSensitive` varlık için değer olacaktır. |
|`globalDefaultFuzzyEditDistance` | Seçim Beceri için varsayılan, benzer bir değer düzenleme uzaklığı değeri. `defaultFuzzyEditDistance`Bir varlığın değeri belirtilmemişse, bu değer bu `defaultFuzzyEditDistance` varlık için değer olacaktır. |

## <a name="skill-inputs"></a>Beceri girişleri

| Giriş adı      | Description                   |
|---------------|-------------------------------|
| `text`          | Çözümlenecek metin.          |
| `languageCode`    | İsteğe bağlı. `"en"` varsayılan değerdir.  |


## <a name="skill-outputs"></a>Yetenek çıkışları


| Çıkış adı      | Description                   |
|---------------|-------------------------------|
| `entities` | Bulunan eşleşmeler ve ilgili meta veriler hakkında bilgi içeren bir nesne dizisi. Tanımlanan varlıkların her biri aşağıdaki alanları içerebilir:  <ul> <li> *ad*: tanımlanan en üst düzey varlık. Varlık "normalleştirilmiş" formunu temsil eder. </li> <li> *ID*: "özel varlık tanımı biçiminde" Kullanıcı tarafından tanımlanan varlık için benzersiz bir tanımlayıcı.</li> <li> *Açıklama*: "özel varlık tanımı biçiminde" Kullanıcı tarafından tanımlanan varlık açıklaması. </li> <li> *şunu yazın:* "Özel varlık tanımı biçiminde" Kullanıcı tarafından tanımlanan varlık türü.</li> <li> *alt tür:* "Özel varlık tanımı biçiminde" Kullanıcı tarafından tanımlanan varlık alt türü.</li>  <li> *eşleşmeler*: kaynak metindeki bu varlık için eşleşmelerin her birini tanımlayan koleksiyon. Her eşleşme aşağıdaki üyelere sahip olacaktır: </li> <ul> <li> *metin*: ham metin, kaynak belgeden eşleşir. </li> <li> *konum*: eşleşmenin metinde bulunduğu konum. </li> <li> *uzunluk*: eşleşen metnin uzunluğu. </li> <li> *eşleşme uzaklığı*: Bu eşleşmenin farklı karakter sayısı, özgün varlık adından veya diğer addan farklıdır.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Özel varlık tanımı biçimi

Özel varlık arama beceriye özel varlıkların listesini sağlamanın 3 farklı yolu vardır. Listeyi ' de sağlayabilirsiniz. CSV dosyası, a. Yetenek tanımının bir parçası olarak JSON dosyası veya satır içi tanım.  

Tanım dosyası bir. CSV veya. JSON dosyası, dosyanın yolunun *Entitıesdefinitionuri* parametresinin bir parçası olarak sağlanması gerekir. Bu durumda, dosya her bir dizin oluşturucunun başlangıcında bir kez indirilir. Dizin oluşturucunun çalıştırılması amaçlanan sürece dosyanın erişilebilir olması gerekir. Ayrıca, dosyanın UTF-8 ile kodlanmış olması gerekir.

Tanım satır içi olarak sağlanmışsa, *ınlineentitiesdefinition* yetenek parametresinin içeriği olarak satır içi olarak sağlanmalıdır. 

### <a name="csv-format"></a>CSV biçimi

Dosya yolunu sağlayarak ve *Entitiesdefinitionuri*  yetenek parametresinde ayarlayarak bir Comma-Separated value (CSV) dosyasında aranacak özel varlıkların tanımını sağlayabilirsiniz. Yol, https konumunda olmalıdır. Tanım dosyası boyutu en fazla 10 MB olabilir.

CSV biçimi basittir. Her satır, aşağıda gösterildiği gibi benzersiz bir varlığı temsil eder:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

Bu durumda, varlık (Bill Gates, Satya Nadella, Microsoft) olarak döndürülebilecek üç varlık bulunur, ancak satırdaki koşullardan herhangi biri (diğer ad) metinde eşleşirse tanımlanırlar. Örneğin, "William H. Gates" dizesi bir belgede bulunursa, "Bill Gates" varlığı için bir eşleşme döndürülür.

### <a name="json-format"></a>JSON biçimi

Bir JSON dosyasında aranacak özel varlıkların tanımını da sağlayabilirsiniz. JSON biçimi, her terim için eşleşen kuralları tanımlamanızı sağlayan bir bit daha esneklik sağlar. Örneğin, her dönem için belirsiz eşleşen mesafeyi (Davmerau-Pavenshtein uzaklığı) veya eşleştirmesinin büyük/küçük harfe duyarlı olup olmayacağını belirtebilirsiniz. 

 CSV dosyalarında olduğu gibi, JSON dosyasının yolunu sağlamanız ve *Entitiesdefinitionuri* yetenek parametresinde ayarlamanız gerekir. Yol, https konumunda olmalıdır. Tanım dosyası boyutu en fazla 10 MB olabilir.

En temel JSON özel varlık listesi tanımı, eşleştirilecek varlıkların bir listesi olabilir:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

JSON tanımının daha karmaşık bir örneği, isteğe bağlı olarak her bir varlığın kimliğini, açıklamasını, türünü ve alt türünü ve diğer *diğer adları* sağlayabilir. Bir diğer ad terimi eşleşirse, varlık de döndürülür:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

Aşağıdaki tablolarda, eşleştirilecek varlıkları tanımlarken ayarlayabileceğiniz farklı yapılandırma parametreleri daha ayrıntılı olarak açıklanır:

|  Alan adı  |        Description  |
|--------------|----------------------|
| `name` | En üst düzey varlık tanımlayıcısı. Yetenek çıkışındaki eşleşmeler bu ada göre gruplandırılır ve bulunan metnin "normalleştirilmiş" biçimini temsil etmelidir.  |
| `description`  | Seçim Bu alan, eşleşen metinler hakkında özel meta veriler için bir PASSTHROUGH olarak kullanılabilir. Bu alanın değeri, yetenek çıkışında varlığıyla her eşleşimiyle birlikte görüntülenir. |
| `type` | Seçim Bu alan, eşleşen metinler hakkında özel meta veriler için bir PASSTHROUGH olarak kullanılabilir. Bu alanın değeri, yetenek çıkışında varlığıyla her eşleşimiyle birlikte görüntülenir. |
| `subtype` | Seçim Bu alan, eşleşen metinler hakkında özel meta veriler için bir PASSTHROUGH olarak kullanılabilir. Bu alanın değeri, yetenek çıkışında varlığıyla her eşleşimiyle birlikte görüntülenir. |
| `id` | Seçim Bu alan, eşleşen metinler hakkında özel meta veriler için bir PASSTHROUGH olarak kullanılabilir. Bu alanın değeri, yetenek çıkışında varlığıyla her eşleşimiyle birlikte görüntülenir. |
| `caseSensitive` | Seçim Varsayılan değer false şeklindedir. Varlık adıyla karşılaştırmaların karakter büyük küçük harfe duyarlı olup olmadığını belirten Boole değeri. "Microsoft" örnek büyük/küçük harf duyarsız eşleşmeleri: Microsoft, microSoft, MICROSOFT |
| `accentSensitive` | Seçim Varsayılan değer false şeklindedir. ' É ' ve ' e ' gibi aksanlı ve vurgusuz harflerin aynı olup olmadığını belirten Boole değeri. |
| `fuzzyEditDistance` | Seçim Varsayılan değer 0 ' dır. En büyük 5 değeri. Varlık adıyla bir eşleşme oluşturulmasına neden olacak kabul edilebilir sayıda sabit karakter belirtir. Verilen herhangi bir eşleşme için mümkün olan en küçük belirsizlik döndürülür.  Örneğin, düzenleme uzaklığı 3 olarak ayarlandıysa, "Windows 10" yine "Windows", "Windows10" ve "Windows 7" ile eşleşir. <br/> Büyük/küçük harf duyarlılığı yanlış olarak ayarlandığında, büyük/küçük harf farkları belirsizlik toleransına doğru sayılmaz, aksi takdirde bunu yapın. |
| `defaultCaseSensitive` | Seçim Bu varlık için varsayılan büyük/küçük harf duyarlılığı değerini değiştirir. Tüm diğer ad caseSensitive değerlerinin varsayılan değerini değiştirmek için kullanılabilir. |
| `defaultAccentSensitive` | Seçim Bu varlık için varsayılan vurgu duyarlılığı değerini değiştirir. Tüm diğer ad, Accenthassas değerlerinin varsayılan değerini değiştirmek için kullanılabilir.|
| `defaultFuzzyEditDistance` | Seçim Bu varlık için varsayılan benzer düzenleme uzaklığı değerini değiştirir. Tüm diğer adların belirsizlik değerlerini varsayılan değerini değiştirmek için kullanılabilir. |
| `aliases` | Seçim Kök varlık adının alternatif yazılarını veya eşanlamlı türlerini belirtmek için kullanılabilecek karmaşık nesneler dizisi. |

| Diğer ad özellikleri | Description |
|------------------|-------------|
| `text`  | Bir hedef varlık adının alternatif yazımı veya temsili.  |
| `caseSensitive` | Seçim Yukarıdaki kök varlık "caseSensitive" parametresiyle aynı şekilde davranır, ancak yalnızca bu diğer ad için geçerlidir. |
| `accentSensitive` | Seçim , Yukarıdaki kök varlık "Accentduyarlıdır" parametresiyle aynı şekilde davranır, ancak yalnızca bu diğer ad için geçerlidir. |
| `fuzzyEditDistance` | Seçim Yukarıdaki "belirsizlik Zyeditdistance" parametresi ile aynı şekilde davranır, ancak yalnızca bu bir diğer ad için geçerlidir. |


### <a name="inline-format"></a>Satır içi biçim

Bazı durumlarda, özel varlıkların listesini doğrudan yetenek tanımına uyacak şekilde sağlamak daha uygun olabilir. Bu durumda, yukarıda açıklanan şekilde benzer bir JSON biçimi kullanabilirsiniz, ancak bu, yetenek tanımında satır içine alınabilir.
Yalnızca boyutu 10 KB 'tan (serileştirilmiş boyut) daha az olan konfigürasyonlar satır içi olarak tanımlanabilir. 

##    <a name="sample-definition"></a>Örnek tanım

Satır içi biçim kullanan örnek bir yetenek tanımı aşağıda gösterilmiştir:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Hardware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
Alternatif olarak, varlıklar tanım dosyasına bir işaretçi sağlamaya karar verirseniz, biçimi kullanan örnek bir yetenek tanımı `entitiesDefinitionUri` aşağıda gösterilmiştir:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>Örnek girdi

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Örnek çıktı

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Hatalar ve uyarılar

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Uyarı: eşleşmeler için maksimum kapasiteye ulaşıldı, daha fazla yinelenen eşleşme atlanıyor.

Bu uyarı, algılanan eşleşme sayısı izin verilen en fazla değerden fazlaysa, bu uyarı yayınlanacaktır. Bu durumda, yinelenen eşleşmeler dahil olmak üzere duracağız. Bu sizin için kabul edilemez, lütfen tek bir kullanım örneği için size yardımcı olabilmemiz için bir [destek bileti](https://ms.portal.azure.com/#create/Microsoft.Support) yapın.

## <a name="see-also"></a>Ayrıca bkz.

+ [Yerleşik yetenekler](cognitive-search-predefined-skills.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [Varlık tanıma becerisi (tanınmış varlıkları aramak için)](cognitive-search-skill-entity-recognition.md)
