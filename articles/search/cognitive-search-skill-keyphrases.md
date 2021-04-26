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
ms.openlocfilehash: 8aafb08ff0ccc9391071f796450e69f87de279ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547841"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Bilişsel Beceri Anahtar İfade Ayıklama

**Anahtar ifade ayıklama** beceri yapılandırılmamış metinleri değerlendirir ve her kayıt için anahtar tümceciklerin bir listesini döndürür. Bu beceri bilişsel hizmetler 'de [metin analizi](../cognitive-services/text-analytics/overview.md) tarafından sunulan makine öğrenimi modellerini kullanır.

Bu özellik, kayıttaki ana konuşmayı hızlı bir şekilde belirlemeniz gerekiyorsa yararlıdır. Örneğin, "gıda merak ediyor ve harika personel vardı" adlı giriş metni verildiğinde, hizmet "yiyecek" ve "harika personel" döndürür.

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/search/)açıklanmaktadır.


## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. Text. KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Veri sınırları
Bir kaydın en büyük boyutu, tarafından ölçülen 50.000 karakter olmalıdır [`String.Length`](/dotnet/api/system.string.length) . Anahtar ifade ayıklayıcıya göndermeden önce verilerinizi kesmeniz gerekiyorsa, [metin bölme becerinizi](cognitive-search-skill-textsplit.md)kullanmayı göz önünde bulundurun.

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır.

| Girişler | Description |
|---------------------|-------------|
| `defaultLanguageCode` | Seçim Açıkça dil belirtmeyen belgelere uygulanacak dil kodu.  Varsayılan dil kodu belirtilmemişse, varsayılan dil kodu olarak Ingilizce (en) kullanılır. <br/> [Desteklenen dillerin tam listesini](../cognitive-services/text-analytics/language-support.md)görüntüleyin. |
| `maxKeyPhraseCount`   | Seçim Üretilecek anahtar tümceciklerin en fazla sayısı. |
| `modelVersion`   | Seçim Metin Analizi hizmeti çağrılırken kullanılacak modelin sürümü. Belirtilmediğinde, varsayılan olarak en son kullanılabilir. Kesinlikle gerekli olmadığı takdirde bu değeri belirtmemenizi öneririz. Daha fazla ayrıntı için [Metin Analizi API'si model sürümü oluşturma](../cognitive-services/text-analytics/concepts/model-versioning.md) bölümüne bakın. |

## <a name="skill-inputs"></a>Beceri girişleri

| Giriş  | Description |
|--------------------|-------------|
| `text` | Çözümlenecek metin.|
| `languageCode`    |  Kayıtların dilini gösteren bir dize. Bu parametre belirtilmemişse, kayıtları çözümlemek için varsayılan dil kodu kullanılacaktır. <br/>[Desteklenen dillerin tam listesini](../cognitive-services/text-analytics/language-support.md) görün|

## <a name="skill-outputs"></a>Yetenek çıkışları

| Çıktı     | Description |
|--------------------|-------------|
| `keyPhrases` | Giriş metninde ayıklanan anahtar tümceciklerin listesi. Anahtar tümceleri önem sırasına göre döndürülür. |


##  <a name="sample-definition"></a>Örnek tanım

Aşağıdaki alanlara sahip bir SQL kaydı düşünün:

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

Daha sonra beceri tanımınız şöyle görünebilir:

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
        "source": "/document/language" 
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

##  <a name="sample-output"></a>Örnek çıktı

Yukarıdaki örnekte, becerinizi gösteren çıktı, belirttiğimiz bu yana "Document/myKeyPhrases" adlı zenginleştirilmiş ağaçta yeni bir düğüme yazılır `targetName` . Bir belirtmezseniz `targetName` , "Document/keyPhrases" olacaktır.

#### <a name="documentmykeyphrases"></a>Belge/myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

"Document/myKeyPhrases" öğesini diğer becerilere giriş olarak veya bir [Çıkış alanı eşlemesinin](cognitive-search-output-field-mapping.md)kaynağı olarak kullanabilirsiniz.

## <a name="warnings"></a>Uyarılar
Desteklenmeyen bir dil kodu sağlarsanız, bir uyarı oluşturulur ve anahtar ifadeler ayıklanmaz.
Metniniz boşsa bir uyarı üretilir.
Metniniz 50.000 karakterden fazlaysa, yalnızca ilk 50.000 karakter analiz edilir ve bir uyarı verilir.

## <a name="see-also"></a>Ayrıca bkz.

+ [Yerleşik yetenekler](cognitive-search-predefined-skills.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [Çıkış alanı eşlemelerini tanımlama](cognitive-search-output-field-mapping.md)