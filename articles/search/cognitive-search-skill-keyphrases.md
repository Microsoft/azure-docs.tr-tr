---
title: Bilişsel Beceri Anahtar İfade Ayıklama
titleSuffix: Azure Cognitive Search
description: Yapılandırılmamış metni değerlendirir ve her kayıt için Azure Bilişsel Arama içindeki bir AI zenginleştirme ardışık düzeninde anahtar tümceciklerin listesini döndürür.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ccdd25d82af2b4893260af18dac818816d9e4579
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791971"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Bilişsel Beceri Anahtar İfade Ayıklama

**Anahtar ifade ayıklama** beceri yapılandırılmamış metinleri değerlendirir ve her kayıt için anahtar tümceciklerin bir listesini döndürür. Bu beceri bilişsel hizmetler 'de [metin analizi](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) tarafından sunulan makine öğrenimi modellerini kullanır.

Bu özellik, kayıttaki ana konuşmayı hızlı bir şekilde belirlemeniz gerekiyorsa yararlıdır. Örneğin, "gıda merak ediyor ve harika personel vardı" adlı giriş metni verildiğinde, hizmet "yiyecek" ve "harika personel" döndürür.

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://go.microsoft.com/fwlink/?linkid=2042400)açıklanmaktadır.


## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. Text. KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Veri sınırları
Bir kaydın en büyük boyutu, [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)ölçülen 50.000 karakter olmalıdır. Anahtar ifade ayıklayıcıya göndermeden önce verilerinizi kesmeniz gerekiyorsa, [metin bölme becerinizi](cognitive-search-skill-textsplit.md)kullanmayı göz önünde bulundurun.

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır.

| Girişler                | Açıklama |
|---------------------|-------------|
| defaultLanguageCode | Seçim Açıkça dil belirtmeyen belgelere uygulanacak dil kodu.  Varsayılan dil kodu belirtilmemişse, varsayılan dil kodu olarak Ingilizce (en) kullanılır. <br/> [Desteklenen dillerin tam listesini](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)görüntüleyin. |
| maxKeyPhraseCount   | Seçim Üretilecek anahtar tümceciklerin en fazla sayısı. |

## <a name="skill-inputs"></a>Beceri girişleri

| Girişler     | Açıklama |
|--------------------|-------------|
| metin | Çözümlenecek metin.|
| languageCode  |  Kayıtların dilini gösteren bir dize. Bu parametre belirtilmemişse, kayıtları çözümlemek için varsayılan dil kodu kullanılacaktır. <br/>[Desteklenen dillerin tam listesini](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) görün|

##  <a name="sample-definition"></a>Örnek tanım

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>Örnek çıktı

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Hatalar ve uyarılar
Desteklenmeyen bir dil kodu sağlarsanız bir hata oluşturulur ve anahtar ifadeler ayıklanmaz.
Metniniz boşsa bir uyarı üretilir.
Metniniz 50.000 karakterden fazlaysa, yalnızca ilk 50.000 karakter analiz edilir ve bir uyarı verilir.

## <a name="see-also"></a>Ayrıca bkz.

+ [Yerleşik yetenekler](cognitive-search-predefined-skills.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
