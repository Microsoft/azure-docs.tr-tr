---
title: Adlandırılmış varlık tanıma bilişsel yeteneği
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama içindeki bir AI zenginleştirme ardışık düzeninde bulunan metin, konum ve kuruluş için adlandırılmış varlıkları ayıklayın.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 127155e492b556ce1ce02b67cf0b0846b99ebcd4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791945"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Adlandırılmış varlık tanıma bilişsel yeteneği

**Adlandırılmış varlık tanıma** yeteneği, adlandırılmış varlıkları metinden ayıklar. Kullanılabilir varlıklar `person`, `location` ve `organization`türlerini içerir.

> [!IMPORTANT]
> Adlandırılmış varlık tanıma yeteneği artık [Microsoft. yetenekler. Text. Entityrecognitionbeceri](cognitive-search-skill-entity-recognition.md)tarafından değiştirilmiştir. Destek 15 Şubat 2019 tarihinde durdurulur ve API, 2 Mayıs 2019 tarihinde üründen kaldırılmıştır. Desteklenen bir yeteneğe geçiş yapmak için kullanım dışı bilişsel [arama becerilerinin](cognitive-search-skill-deprecated.md) önerilerini izleyin.

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://go.microsoft.com/fwlink/?linkid=2042400)açıklanmaktadır.


## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. Text. Namedentityrecognitionbeceri

## <a name="data-limits"></a>Veri sınırları
Bir kaydın en büyük boyutu, [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)ölçülen 50.000 karakter olmalıdır. Anahtar ifade ayıklayıcıya göndermeden önce verilerinizi kesmeniz gerekiyorsa, [metin bölme becerinizi](cognitive-search-skill-textsplit.md)kullanmayı göz önünde bulundurun.

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır.

| Parametre adı     | Açıklama |
|--------------------|-------------|
| kategoriler    | Ayıklanmak zorunda olan kategorilerin dizisi.  Olası kategori türleri: `"Person"`, `"Location"`, `"Organization"`. Hiçbir kategori sağlanmazsa, tüm türler döndürülür.|
|defaultLanguageCode |  Giriş metninin dil kodu. Aşağıdaki diller desteklenir: `de, en, es, fr, it`|
| minimumPrecision  | 0 ile 1 arasında bir sayı. Duyarlık bu değerden düşükse varlık döndürülmez. Varsayılan değer 0 ' dır.|

## <a name="skill-inputs"></a>Beceri girişleri

| Giriş adı      | Açıklama                   |
|---------------|-------------------------------|
| languageCode  | İsteğe bağlı. `"en"` varsayılan değerdir.  |
| metin          | Çözümlenecek metin.          |

## <a name="skill-outputs"></a>Yetenek çıkışları

| Çıkış adı     | Açıklama                   |
|---------------|-------------------------------|
| elemanları      | Her bir dizenin bir kişinin adını temsil ettiği dizeler dizisi. |
| yerlerini  | Her bir dizenin bir konumu temsil ettiği dizeler dizisi. |
| organizations  | Her bir dizenin bir kuruluşu temsil ettiği dizeler dizisi. |
| varlıklar | Karmaşık türlerden oluşan dizi. Her karmaşık tür aşağıdaki alanları içerir: <ul><li>kategori (`"person"`, `"organization"`veya `"location"`)</li> <li>değer (gerçek varlık adı)</li><li>fark (metinde bulunduğu konum)</li><li>güvenirlik (değerin gerçek bir varlık olduğunu belirten, bu güveni temsil eden 0 ile 1 arasında bir değer)</li></ul> |

##  <a name="sample-definition"></a>Örnek tanım

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
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
##  <a name="sample-input"></a>Örnek giriş

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
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
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
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
+ [Varlık tanıma yeteneği](cognitive-search-skill-entity-recognition.md)
