---
title: Becerileri 'de özel Web API 'SI yeteneği
titleSuffix: Azure Cognitive Search
description: Web API 'Lerine çağırarak Azure Bilişsel Arama becerileri 'in yeteneklerini genişletin. Özel kodunuzu bütünleştirmek için özel Web API 'SI yeteneklerinizi kullanın.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: cb5ee7d3549e433fb184b8c55c28b9a28ed89272
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011940"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Azure Bilişsel Arama enzenginleştirme ardışık düzeninde özel Web API 'SI yeteneği

**Özel Web API 'si** yeteneği, özel işlemler sağlayan BIR Web API uç noktasına çağrı yaparak AI zenginleştirme kapsamını genişletmenizi sağlar. Yerleşik becerilerle benzer şekilde, **özel Web API 'si** becerilerinin giriş ve çıkışları vardır. Girişlere bağlı olarak, Web API 'niz, Dizin Oluşturucu çalıştırıldığında bir JSON yükü alır ve başarılı durum kodu ile birlikte bir JSON yükünün yanıt olarak çıkışını verir. Yanıtın özel becerinize göre belirtilen çıkışların olması beklenir. Diğer herhangi bir yanıt bir hata olarak değerlendirilir ve hiçbir zenginleştirilmez.

JSON yükleri yapısı, bu belgede daha fazla açıklanacaktır.

> [!NOTE]
> Dizin Oluşturucu, Web API 'sinden döndürülen belirli standart HTTP durum kodları için iki kez yeniden dener. Bu HTTP durum kodları şunlardır: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. Custom. WebApiSkill

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır.

| Parametre adı     | Açıklama |
|--------------------|-------------|
| `uri` | _JSON_ yükünün GÖNDERILECEĞI Web API 'sinin URI 'si. Yalnızca **https** URI şemasına izin veriliyor |
| `httpMethod` | Yük gönderilirken kullanılacak yöntem. İzin verilen yöntemler `PUT` veya `POST` |
| `httpHeaders` | Anahtarların üstbilgi adlarını ve değerlerini temsil ettiği anahtar-değer çiftleri koleksiyonu, yük ile birlikte Web API 'nize gönderilecek üst bilgi değerlerini temsil eder. Şu üst bilgilerin bu koleksiyonda olması yasaktır:  `Accept` , `Accept-Charset` ,, `Accept-Encoding` `Content-Length` , `Content-Type` , `Cookie` , `Host` , `TE` , `Upgrade` , `Via` |
| `timeout` | Seçim Belirtildiğinde, API çağrısını yapan http istemcisinin zaman aşımını gösterir. XSD "dayTimeDuration" değeri ( [ıso 8601 Duration](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) değerinin kısıtlı bir alt kümesi) olarak biçimlendirilmelidir. Örneğin, `PT60S` 60 saniye için. Ayarlanmamışsa, varsayılan değer olan 30 saniye seçilir. Zaman aşımı en fazla 230 saniyeye ayarlanabilir ve en az 1 saniye olabilir. |
| `batchSize` | Seçim Her API çağrısı için, kaç "veri kaydı" (aşağıdaki _JSON_ yük yapısına bakın) gönderileceğini belirtir. Ayarlanmamışsa, varsayılan olarak 1000 seçilidir. Dizin oluşturma işleme ve API 'niz üzerinde yükleme arasında uygun bir zorunluluğunu getirir elde etmek için bu parametreyi kullanmanızı öneririz |
| `degreeOfParallelism` | Seçim Belirtildiğinde, dizin oluşturucunun verdiğiniz bitiş noktasına paralel olarak kullanacağı çağrı sayısını gösterir. Uç noktanız bir istek yükünün çok yüksek altındaysa bu değeri azaltabilir veya uç noktanız daha fazla istek kabul edebilse ve dizin oluşturucunun performansına bir artış istiyorsanız bu değeri azaltabilirsiniz.  Ayarlanmamışsa, varsayılan 5 değeri kullanılır. `degreeOfParallelism`En fazla 10 ve en az 1 olarak ayarlanabilir. |

## <a name="skill-inputs"></a>Beceri girişleri

Bu beceri için "önceden tanımlanmış" giriş yok. Bu beceri yürütme sırasında zaten kullanılabilir olacak bir veya daha fazla alan seçebilirsiniz ve Web API 'sine gönderilen _JSON_ yükünün farklı alanları olacaktır.

## <a name="skill-outputs"></a>Yetenek çıkışları

Bu beceri için "önceden tanımlanmış" çıkış yok. Web API 'nizin döndürdüğü yanıta bağlı olarak, _JSON_ yanıtından alınabilmeleri için çıktı alanları ekleyin.


## <a name="sample-definition"></a>Örnek tanım

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Örnek giriş JSON yapısı

Bu _JSON_ yapısı, Web API 'nize gönderilecek yükü temsil eder.
Her zaman şu kısıtlamalara uyar:

* En üst düzey varlık çağrılır `values` ve bir nesne dizisi olur. Bu tür nesnelerin sayısı en fazla şu kadar olacaktır: `batchSize`
* Dizideki her bir nesne `values` ,
    * `recordId`Bu kaydı tanımlamak için kullanılan **benzersiz** bir dize olan bir özellik.
    * `data` _JSON_ nesnesi olan bir özellik. Özelliğin alanları, `data` yetenek tanımının bölümünde belirtilen "adlara" karşılık gelir `inputs` . Bu alanların değeri `source` Bu alanlardan (belgedeki bir alandan ya da başka bir beceriye ait olabilir) olacaktır

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Örnek çıkış JSON yapısı

"Output", Web API 'nizden döndürülen yanıta karşılık gelir. Web API 'si yalnızca bir _JSON_ yükü döndürmelidir ( `Content-Type` Yanıt üstbilgisine bakılarak doğrulanır) ve aşağıdaki kısıtlamalara uygun olmalıdır:

* Bir nesne dizisi olması gereken adlı üst düzey bir varlık olmalıdır `values` .
* Dizideki nesne sayısı, Web API 'sine gönderilen nesne sayısıyla aynı olmalıdır.
* Her nesne şunları içermelidir:
   * Bir `recordId` Özellik
   * `data`Alanların içindeki "adlar" `output` ile eşleşen ve değeri enzenginleştirme olarak kabul edildiği bir nesne olan bir özellik.
   * `errors`Dizin Oluşturucu yürütme geçmişine eklenecek hatalarla karşılaşan bir dizi özellik. Bu özellik gereklidir, ancak bir değere sahip olabilir `null` .
   * `warnings`Dizin Oluşturucu yürütme geçmişine eklenecek herhangi bir uyarıyı listelemesi için bir özellik. Bu özellik gereklidir, ancak bir değere sahip olabilir `null` .
* Dizideki nesneler, `values` `values` Web API 'sine istek olarak gönderilen dizideki nesnelerle aynı sırada olmamalıdır. Ancak, `recordId` bağıntısı için kullanıldığında, `recordId` Web API 'sine yapılan özgün isteğin bir parçası olan yanıttaki tüm kayıtlar atılır.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Hata durumları
Web API 'nizin kullanılamaz hale veya başarılı olmayan durum kodları gönderilmesine ek olarak aşağıdakiler hatalı durumlar olarak kabul edilir:

* Web API 'SI bir başarı durum kodu döndürürse ancak yanıt bunun olmadığını gösteriyorsa, `application/json` yanıt geçersiz olarak kabul edilir ve hiçbir zenginleştirilmez.
* Yanıt dizisinde **geçersiz** ( `recordId` orijinal istekte değil veya yinelenen değerler içeren) kayıtları varsa `values` , **Bu** kayıtlar için hiçbir zenginleştirme gerçekleştirilmez.

Web API 'sinin kullanılamadığı veya bir HTTP hatası döndürdüğü durumlarda, Dizin Oluşturucu yürütme geçmişine HTTP hatası ile ilgili tüm ayrıntıları içeren bir kolay hata eklenir.

## <a name="see-also"></a>Ayrıca bkz.

+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [Bir AI zenginleştirme ardışık düzenine özel yetenek ekleme](cognitive-search-custom-skill-interface.md)
+ [Örnek: AI zenginleştirme için özel bir yetenek oluşturma](cognitive-search-create-custom-skill-example.md)
