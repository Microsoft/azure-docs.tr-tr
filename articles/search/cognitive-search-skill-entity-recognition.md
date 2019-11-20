---
title: Varlık tanıma Bilişsel Beceri
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de bir zenginleştirme ardışık düzeninde bulunan metinden farklı varlık türlerini ayıklayın.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 559d8cb25624c1d8bebb2969fbeeb80bdcc020e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73479742"
---
#   <a name="entity-recognition-cognitive-skill"></a>Varlık tanıma Bilişsel Beceri

**Varlık tanıma** yeteneği, metinden farklı türlerdeki varlıkları ayıklar. Bu beceri bilişsel hizmetler 'de [metin analizi](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) tarafından sunulan makine öğrenimi modellerini kullanır.

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://go.microsoft.com/fwlink/?linkid=2042400)açıklanmaktadır.


## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. Text. Entityrecognitionbeceri

## <a name="data-limits"></a>Veri sınırları
Bir kaydın en büyük boyutu, [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)ölçülen 50.000 karakter olmalıdır. Anahtar ifade ayıklayıcıya göndermeden önce verilerinizi kesmeniz gerekiyorsa, [metin bölme becerinizi](cognitive-search-skill-textsplit.md)kullanmayı göz önünde bulundurun.

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır ve tümü isteğe bağlıdır.

| Parametre adı     | Açıklama |
|--------------------|-------------|
| kategoriler    | Ayıklanmak zorunda olan kategorilerin dizisi.  Olası kategori türleri: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Hiçbir kategori sağlanmazsa, tüm türler döndürülür.|
|defaultLanguageCode |  Giriş metninin dil kodu. Aşağıdaki diller desteklenir: `de, en, es, fr, it`|
|minimumPrecision | 0 ile 1 arasında bir değer. Güven puanı (`namedEntities` çıktısında) bu değerden düşükse, varlık döndürülmez. Varsayılan değer 0 ' dır. |
|ıncludetypelessentities | Geçerli kategorilere uymayan iyi bilinen varlıkları tanımak istiyorsanız `true` olarak ayarlayın. Tanınan varlıklar `entities` karmaşık çıkış alanında döndürülür. Örneğin, "Windows 10" iyi bilinen bir varlıktır (bir ürün), ancak "Ürünler" desteklenen bir kategori olmadığından, bu varlık varlık çıktısı alanına dahil edilir. Varsayılan değer `false` |


## <a name="skill-inputs"></a>Beceri girişleri

| Giriş adı      | Açıklama                   |
|---------------|-------------------------------|
| languageCode  | İsteğe bağlı. `"en"` varsayılan değerdir.  |
| metin          | Çözümlenecek metin.          |

## <a name="skill-outputs"></a>Yetenek çıkışları

> [!NOTE]
> Tüm diller için varlık kategorilerinin hepsi desteklenmez. Yalnızca _en_, _es_ `"Quantity"`, `"Datetime"`, `"URL"``"Email"` türlerini ayıklamayı destekler.

| Çıkış adı     | Açıklama                   |
|---------------|-------------------------------|
| elemanları      | Her bir dizenin bir kişinin adını temsil ettiği dizeler dizisi. |
| yerlerini  | Her bir dizenin bir konumu temsil ettiği dizeler dizisi. |
| organizations  | Her bir dizenin bir kuruluşu temsil ettiği dizeler dizisi. |
| miktarlara  | Her bir dizenin bir miktarı temsil ettiği dizeler dizisi. |
| Tarih saat  | Her bir dizenin bir tarih/saat değerini temsil ettiği dizeler dizisi (metin içinde göründüğü gibi). |
| adresleri | Her bir dizenin bir URL 'YI temsil ettiği dizelerin dizisi |
| - | Her bir dizenin bir e-postayı temsil ettiği dizelerin dizisi |
| namedEntities | Aşağıdaki alanları içeren karmaşık türlerin dizisi: <ul><li>category</li> <li>değer (gerçek varlık adı)</li><li>fark (metinde bulunduğu konum)</li><li>güvenirlik (daha yüksek değer gerçek bir varlık olması anlamına gelir)</li></ul> |
| varlıklar | Aşağıdaki alanlarla metinden ayıklanan varlıklar hakkında zengin bilgiler içeren karmaşık türlerin dizisi <ul><li> ad (gerçek varlık adı. Bu bir "normalleştirilmiş" formu temsil eder)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (varlık için Vikipedi sayfasına bağlantı)</li><li>Bingıd</li><li>tür (tanınan varlığın kategorisi)</li><li>Alt tür (yalnızca belirli kategoriler için kullanılabilir, bu varlık türünün daha ayrıntılı bir görünümünü sağlar)</li><li> Eşleşmeler (içeren karmaşık bir koleksiyon)<ul><li>metin (varlık için ham metin)</li><li>fark (konumun bulunduğu konum)</li><li>Uzunluk (ham varlık metninin uzunluğu)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Örnek tanım

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
##  <a name="sample-input"></a>Örnek giriş

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

##  <a name="sample-output"></a>Örnek çıktı

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


## <a name="error-cases"></a>Hata durumları
Belge için dil kodu desteklenmiyorsa bir hata döndürülür ve hiçbir varlık ayıklanmaz.

## <a name="see-also"></a>Ayrıca bkz.

+ [Yerleşik yetenekler](cognitive-search-predefined-skills.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
