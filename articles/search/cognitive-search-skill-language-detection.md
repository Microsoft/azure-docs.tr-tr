---
title: Dil algılama Bilişsel Beceri
titleSuffix: Azure Cognitive Search
description: Yapılandırılmamış metni değerlendirir ve her kayıt için Azure Bilişsel Arama 'teki bir AI zenginleştirme ardışık düzeninde analizin şiddetini belirten puanla bir dil tanımlayıcısı döndürür.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 078a9312a7ee1b3b0eafd000928ed74348a540c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548062"
---
#   <a name="language-detection-cognitive-skill"></a>Dil algılama Bilişsel Beceri

**Dil algılama** Beceri, giriş metninin dilini algılar ve istekte gönderilen her belge için tek bir dil kodu bildirir. Dil kodu, çözümlemenin gücünü gösteren bir puanla eşleştirilir. Bu beceri bilişsel hizmetler 'de [metin analizi](../cognitive-services/text-analytics/overview.md) tarafından sunulan makine öğrenimi modellerini kullanır.

Bu özellik özellikle, örneğin [yaklaşım Analizi beceri](cognitive-search-skill-sentiment.md) veya [metin bölünmüş beceri](cognitive-search-skill-textsplit.md)gibi, metnin dilini girmeniz gerektiğinde faydalıdır.

Dil algılama, Bing 'in doğal dil işleme kitaplıklarını kullanır ve bu, Metin Analizi için listelenen [desteklenen dil ve bölge](../cognitive-services/text-analytics/language-support.md) sayısını aşıyor. Dillerin tam listesi yayımlanmaz, ancak tüm yaygın olarak konuşulan dillerin yanı sıra çeşitleri, diapacts ve bazı bölgesel ve kültürel dillerini içerir. Daha az sıklıkta kullanılan bir dilde ifade ettiğiniz bir içeriğiniz varsa, bir kodu döndürüp döndürdüğünü görmek için [DIL ALGıLAMA API 'sini deneyebilirsiniz](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) . Tespit edilemez dillerin yanıtı `(Unknown)` .

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/search/)açıklanmaktadır.


## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. Text. LanguageDetectionSkill

## <a name="data-limits"></a>Veri sınırları
Bir kaydın en büyük boyutu, tarafından ölçülen 50.000 karakter olmalıdır [`String.Length`](/dotnet/api/system.string.length) . Verileri dil algılama beceriye göndermeden önce bölmeniz gerekirse, [metin bölme becerinizi](cognitive-search-skill-textsplit.md)kullanabilirsiniz.

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır.

| Girişler | Description |
|---------------------|-------------|
| `defaultCountryHint` | Seçim Bir ISO 3166-1 Alpha-2 2 Letter ülke kodu, dilin ayırt edilemez olması durumunda dil algılama modelinin ipucu olarak kullanılması için kullanılabilir. Daha fazla bilgi için bu konudaki [metin analizi belgelerine](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) bakın. Özellikle, `defaultCountryHint` parametresi girişi açıkça belirtmeyen belgelerle kullanılır `countryHint` .  |
| `modelVersion`   | Seçim Metin Analizi hizmeti çağrılırken kullanılacak modelin sürümü. Belirtilmediğinde, varsayılan olarak en son kullanılabilir. Kesinlikle gerekli olmadığı takdirde bu değeri belirtmemenizi öneririz. Daha fazla ayrıntı için [Metin Analizi API'si model sürümü oluşturma](../cognitive-services/text-analytics/concepts/model-versioning.md) bölümüne bakın. |

## <a name="skill-inputs"></a>Beceri girişleri

Parametreler büyük/küçük harfe duyarlıdır.

| Girişler     | Description |
|--------------------|-------------|
| `text` | Çözümlenecek metin.|
| `countryHint` | Dili ayırt edemez bir ISO 3166-1 Alpha-2 2 mektup ülke kodu, dil algılama modeli için ipucu olarak kullanılacak. Daha fazla bilgi için bu konudaki [metin analizi belgelerine](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) bakın. |

## <a name="skill-outputs"></a>Yetenek çıkışları

| Çıkış adı    | Description |
|--------------------|-------------|
| `languageCode` | Tanımlanan dilin ISO 6391 dil kodu. Örneğin, "en". |
| `languageName` | Dilin adı. Örneğin, "Ingilizce". |
| `score` | 0 ile 1 arasında bir değer. Dilin doğru şekilde tanımlanması olasılığı. Tümcede karışık diller varsa puan 1 ' den düşük olabilir.  |

##  <a name="sample-definition"></a>Örnek tanım

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Örnek girdi

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
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
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>Ayrıca bkz.

+ [Yerleşik yetenekler](cognitive-search-predefined-skills.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)