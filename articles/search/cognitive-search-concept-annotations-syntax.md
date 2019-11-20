---
title: Becerileri içinde başvuru girişleri ve çıktılar
titleSuffix: Azure Cognitive Search
description: Ek açıklama sözdizimini ve Azure Bilişsel Arama içindeki bir AI zenginleştirme ardışık düzeninde bulunan bir beceri giriş ve çıktılarında ek açıklamaya nasıl başvurululacağını açıklar.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e27f61239c0631fb248217777a311b13ee48a3f9
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113860"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Azure Bilişsel Arama Beceri ek açıklamalarına başvurma

Bu makalede, çeşitli senaryoları göstermek için örnekler kullanarak yetenek tanımlarındaki ek açıklamaların nasıl başvurululacağını öğrenirsiniz. Bir belge içeriği bir yetenek kümesiyle akar, bu, ek açıklamalarla zenginleştirir. Ek açıklamalar, daha fazla aşağı akış zenginleştirme girişi olarak veya bir dizindeki bir çıkış alanına eşlenmiş olarak kullanılabilir. 
 
Bu makaledeki örneklerde, belge çözme aşamasının bir parçası olarak [Azure Blob Dizin oluşturucular](search-howto-indexing-azure-blob-storage.md) tarafından otomatik olarak oluşturulan *içerik* alanı temel alınır. Blob kapsayıcısından belgelere başvuru yaparken, *içerik* alanının *belgenin*bir parçası olduğu `"/document/content"`gibi bir biçim kullanın. 

## <a name="background-concepts"></a>Arka plan kavramları

Söz dizimini gözden geçirmeden önce, bu makalenin ilerleyen kısımlarında belirtilen örnekleri daha iyi anlamak için birkaç önemli kavramı yeniden ziyaret edelim.

| Sözleşme Dönemi | Açıklama |
|------|-------------|
| Zenginleştirilmiş belge | Zenginleştirilmiş bir belge, işlem hattı tarafından oluşturulan ve bir belge ile ilgili tüm ek açıklamaları tutmak için kullanılan dahili bir yapıdır. Zenginleştirilmiş bir belgeyi, ek açıklama ağacı olarak düşünün. Genellikle, önceki bir ek açıklamanın oluşturduğu bir ek açıklama alt öğesi olur.<p/>Zenginleştirilmiş belgeler yalnızca beceri yürütmesinin süresi boyunca mevcuttur. İçerik arama dizinine eşlendikten sonra, zenginleştirilmiş belge artık gerekli değildir. Zenginleştirilmiş belgelerle doğrudan etkileşim kurmasanız da, Beceri oluştururken belge için bir model olması yararlı olacaktır. |
| Zenginleştirme bağlamı | Hangi öğenin zenginleştirmesi halinde, zenginleştirme 'nin gerçekleştiği bağlam. Varsayılan olarak, enzenginleştirme bağlamı tek tek belgeler kapsamındaki `"/document"` düzeyindedir. Bir yetenek çalıştırıldığında, bu beceriye ait çıktılar [tanımlanan bağlamın özellikleri](#example-2)haline gelir.|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Örnek 1: basit ek açıklama başvurusu

Azure Blob depolama alanında, varlık tanıma kullanarak çıkarmak istediğiniz kişilerin adlarına başvurular içeren çeşitli dosyalarınız olduğunu varsayalım. Aşağıdaki yetenek tanımında, `"/document/content"` tüm belgenin metinsel gösterimidir ve "kişiler" ise kişi olarak tanımlanan varlıkların tam adları ayıklanacaktır.

Varsayılan bağlam `"/document"`olduğundan, kişiler listesine artık `"/document/people"`olarak başvurulabilir. Bu özel durumda `"/document/people"`, artık dizindeki bir alanla eşlenemeyen veya aynı beceri başka bir becerde kullanılan bir ek açıklama.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>Örnek 2: belge içindeki bir diziye başvuru

Bu örnekte, bir zenginleştirme adımını aynı belgede birden çok kez nasıl çağırabileceğiniz gösterilmektedir. Önceki örneğin tek bir belgeden 10 kişi adıyla bir dize dizisi oluşturulduğunu varsayın. Makul bir sonraki adım, tam bir adın soyadını çıkaran ikinci bir zenginleştirme olabilir. 10 ad olduğundan, bu adımda her kişi için bir kez olmak üzere bu belgede 10 kez çağrılabilir. 

Doğru sayıda yinelemeyi çağırmak için, bağlamı, yıldız işareti (`"*"`), zenginleştirilmiş belgedeki tüm düğümleri `"/document/people"`alt öğeleri olarak temsil eden `"/document/people/*"`olarak ayarlayın. Bu yetenek yetenekler dizisinde yalnızca bir kez tanımlanmış olsa da, tüm Üyeler işlenene kadar belgedeki her üye için çağrılır.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

Ek açıklamalar diziler veya dize koleksiyonları olduğunda, bir bütün olarak dizi yerine belirli üyeleri hedeflemek isteyebilirsiniz. Yukarıdaki örnek, bağlamı temsil eden her düğüm altında `"last"` adlı bir ek açıklama üretir. Bu ek açıklama ailesine başvurmak istiyorsanız `"/document/people/*/last"`sözdizimini kullanabilirsiniz. Belirli bir ek açıklamaya başvurmak isterseniz, bir açık dizin kullanabilirsiniz: `"/document/people/1/last`"belgesinde tanımlanan ilk kişinin soyadını başvuru. Bu söz dizimi diziminde "0 dizinli" olduğuna dikkat edin.

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Örnek 3: bir dizi içindeki başvuru üyeleri

Bazen belirli bir yeteneğe iletmek için belirli bir türün tüm ek açıklamalarını gruplamak gerekir. Örnek 2 ' de ayıklanan tüm son adlardan en yaygın adı tanımlayan bir kuramsal özel yetenek düşünün. Özel beceriye yalnızca soyadlarını sağlamak için, bağlamı `"/document"` ve giriş `"/document/people/*/lastname"`olarak belirtin.

`"/document/people/*/lastname"` kardinalitesi belgeden daha büyük olduğuna dikkat edin. Bu belge için yalnızca bir belge düğümü varken 10 LastName düğümü olabilir. Bu durumda, sistem otomatik olarak belgedeki tüm öğeleri içeren `"/document/people/*/lastname"` bir dizi oluşturur.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Ayrıca bkz.
+ [Özel bir yeteneği bir zenginleştirme işlem hattı ile tümleştirme](cognitive-search-custom-skill-interface.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [Beceri oluşturma (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Zenginleştirilmiş alanları bir dizinle eşleme](cognitive-search-output-field-mapping.md)
