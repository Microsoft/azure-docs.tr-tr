---
title: Varlık tanıma Bilişsel Beceri
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de bir zenginleştirme ardışık düzeninde bulunan metinden farklı varlık türlerini ayıklayın.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 3300d68c6356cb7cdd56a9ad79eb0ac011942eb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547909"
---
#    <a name="entity-recognition-cognitive-skill"></a>Varlık tanıma Bilişsel Beceri

**Varlık tanıma** yeteneği, metinden farklı türlerdeki varlıkları ayıklar. Bu beceri bilişsel hizmetler 'de [metin analizi](../cognitive-services/text-analytics/overview.md) tarafından sunulan makine öğrenimi modellerini kullanır.

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/search/)açıklanmaktadır.


## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. Text. Entityrecognitionbeceri

## <a name="data-limits"></a>Veri sınırları
Bir kaydın en büyük boyutu, tarafından ölçülen 50.000 karakter olmalıdır [`String.Length`](/dotnet/api/system.string.length) . Anahtar ifade ayıklayıcıya göndermeden önce verilerinizi kesmeniz gerekiyorsa, [metin bölme becerinizi](cognitive-search-skill-textsplit.md)kullanmayı göz önünde bulundurun.

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır ve tümü isteğe bağlıdır.

| Parametre adı     | Açıklama |
|--------------------|-------------|
| `categories`    | Ayıklanmak zorunda olan kategorilerin dizisi.  Olası kategori türleri: `"Person"` , `"Location"` , `"Organization"` , `"Quantity"` , `"Datetime"` , `"URL"` , `"Email"` . Hiçbir kategori sağlanmazsa, tüm türler döndürülür.|
| `defaultLanguageCode` |    Giriş metninin dil kodu. Aşağıdaki diller desteklenir: `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans` . Tüm diller için tüm varlık kategorileri desteklenmez; Aşağıdaki nota bakın.|
| `minimumPrecision` | 0 ile 1 arasında bir değer. Güven puanı ( `namedEntities` çıktıda) bu değerden düşükse varlık döndürülmez. Varsayılan değer, 0'dur. |
| `includeTypelessEntities` | `true`Geçerli kategorilere uymayan iyi bilinen varlıkları tanımak istiyorsanız olarak ayarlayın. Tanınan varlıklar `entities` karmaşık çıkış alanında döndürülür. Örneğin, "Windows 10" iyi bilinen bir varlıktır (bir ürün), ancak "Ürünler" desteklenen bir kategori olmadığından, bu varlık varlık çıktısı alanına dahil edilir. Varsayılan değer `false` |


## <a name="skill-inputs"></a>Beceri girişleri

| Giriş adı      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | İsteğe bağlı. `"en"` varsayılan değerdir.  |
| `text`          | Çözümlenecek metin.          |

## <a name="skill-outputs"></a>Yetenek çıkışları

> [!NOTE]
> Tüm diller için varlık kategorilerinin hepsi desteklenmez. `"Person"`, `"Location"` , Ve `"Organization"` varlık kategorisi türleri yukarıdaki dillerin tam listesi için desteklenir. Yalnızca _de_, _en_, _es_, _fr_ ve _zh-Hans_ ,,, `"Quantity"` `"Datetime"` `"URL"` ve türlerinin ayıklanmasını destekler `"Email"` . Daha fazla bilgi için bkz. [Metin Analizi API'si Için dil ve bölge desteği](../cognitive-services/text-analytics/language-support.md).  

| Çıkış adı      | Description                   |
|---------------|-------------------------------|
| `persons`       | Her bir dizenin bir kişinin adını temsil ettiği dizeler dizisi. |
| `locations`  | Her bir dizenin bir konumu temsil ettiği dizeler dizisi. |
| `organizations`  | Her bir dizenin bir kuruluşu temsil ettiği dizeler dizisi. |
| `quantities`  | Her bir dizenin bir miktarı temsil ettiği dizeler dizisi. |
| `dateTimes`  | Her bir dizenin bir tarih/saat değerini temsil ettiği dizeler dizisi (metin içinde göründüğü gibi). |
| `urls` | Her bir dizenin bir URL 'YI temsil ettiği dizelerin dizisi |
| `emails` | Her bir dizenin bir e-postayı temsil ettiği dizelerin dizisi |
| `namedEntities` | Aşağıdaki alanları içeren karmaşık türlerin dizisi: <ul><li>category</li> <li>değer (gerçek varlık adı)</li><li>fark (metinde bulunduğu konum)</li><li>güvenirlik (daha yüksek değer gerçek bir varlık olması anlamına gelir)</li></ul> |
| `entities` | Aşağıdaki alanlarla metinden ayıklanan varlıklar hakkında zengin bilgiler içeren karmaşık türlerin dizisi <ul><li> ad (gerçek varlık adı. Bu bir "normalleştirilmiş" formu temsil eder)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (varlık için Vikipedi sayfasına bağlantı)</li><li>Bingıd</li><li>tür (tanınan varlığın kategorisi)</li><li>Alt tür (yalnızca belirli kategoriler için kullanılabilir, bu varlık türünün daha ayrıntılı bir görünümünü sağlar)</li><li> Eşleşmeler (içeren karmaşık bir koleksiyon)<ul><li>metin (varlık için ham metin)</li><li>fark (konumun bulunduğu konum)</li><li>Uzunluk (ham varlık metninin uzunluğu)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Örnek tanım

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
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
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
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
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Örnek çıktı

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```

Bu yeteneğin çıktılarındaki varlıklar için döndürülen uzaklıklardan doğrudan [Metin Analizi API'si](../cognitive-services/text-analytics/overview.md)döndürüldüğünü unutmayın. Bu, özgün dizede dizin haline getirmek için kullanıyorsanız, doğru içeriği ayıklamak üzere .net Içindeki [StringInfo](/dotnet/api/system.globalization.stringinfo) sınıfını kullanmanız gerekir.  [Daha fazla ayrıntı burada bulunabilir.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="warning-cases"></a>Uyarı durumları
Belge dil kodu desteklenmiyorsa bir uyarı döndürülür ve hiçbir varlık ayıklanmaz.

## <a name="see-also"></a>Ayrıca bkz.

+ [Yerleşik yetenekler](cognitive-search-predefined-skills.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)