---
title: Metin çevirisi Bilişsel Beceri
titleSuffix: Azure Cognitive Search
description: Metni değerlendirir ve her kayıt için Azure Bilişsel Arama içindeki bir AI zenginleştirme ardışık düzeninde belirtilen hedef dile çevrilen metni döndürür.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0953d750ee8b59e9889512bb64cfd276a0bbeb53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654875"
---
#   <a name="text-translation-cognitive-skill"></a>Metin çevirisi Bilişsel Beceri

Metin **çevirisi** becerisi metni değerlendirir ve her kayıt için belirtilen hedef dile çevrilen metni döndürür. Bu beceri bilişsel hizmetler 'de bulunan [Translator metin çevirisi API'si v 3.0](../cognitive-services/translator/reference/v3-0-translate.md) 'ı kullanır.

Bu özellik, belgelerinizin tümünün tek bir dilde olmaması beklendiğinde yararlıdır, bu durumda, arama için dizin oluşturmadan önce metni tek bir dile normalleştirilemeyebilirsiniz.  Aynı zamanda birden çok dilde aynı metnin kopyalarının olmasını isteyebileceğiniz yerelleştirme kullanım durumları için de kullanışlıdır.

[Translator metin çevirisi API'si v 3.0](../cognitive-services/translator/reference/v3-0-reference.md) , bölgesel olmayan bir bilişsel hizmettir, yani verilerinizin Azure bilişsel arama veya bağlı bilişsel hizmetler kaynağınız ile aynı bölgede kalması garanti edilmez.

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/search/)açıklanmaktadır.

## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. Text. Translationbeceri

## <a name="data-limits"></a>Veri sınırları
Bir kaydın en büyük boyutu, tarafından ölçülen 50.000 karakter olmalıdır [`String.Length`](/dotnet/api/system.string.length) . Verileri metin çevirisi yeteneklerine göndermeden önce bölmeniz gerekirse, [metin bölme becerisinin](cognitive-search-skill-textsplit.md)kullanımını göz önünde bulundurun.

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır.

| Girişler | Description |
|---------------------|-------------|
| defaultToLanguageCode | Istenir Kendisine açık olarak belirtmeyen belgeler için belgeleri içine çevirecek dil kodu. <br/> [Desteklenen dillerin tam listesini](../cognitive-services/translator/language-support.md)görüntüleyin. |
| defaultFromLanguageCode | Seçim Özel olarak kimden dilini belirtmeyen belgeler için belge çeviren dil kodu.  DefaultFromLanguageCode belirtilmemişse, kimden dilini saptamak için Translator Metin Çevirisi API'si tarafından sunulan otomatik dil algılama kullanılır. <br/> [Desteklenen dillerin tam listesini](../cognitive-services/translator/language-support.md)görüntüleyin. |
| müported | Seçim FromLanguageCode girişi ya da defaultFromLanguageCode parametresi sağlanmıyorsa ve otomatik dil algılama başarısız olduğunda belgelerin çevrilme dil kodu.  Bir dil için bir dil belirtilmemişse, Ingilizce (en), dilden Itibaren bu şekilde kullanılacaktır. <br/> [Desteklenen dillerin tam listesini](../cognitive-services/translator/language-support.md)görüntüleyin. |

## <a name="skill-inputs"></a>Beceri girişleri

| Giriş adı     | Description |
|--------------------|-------------|
| metin | Çevrilecek metin.|
| toLanguageCode    | Metnin çevrilmesi gereken dili belirten dize. Bu giriş belirtilmemişse, metni çevirmek için defaultToLanguageCode kullanılır. <br/>[Desteklenen dillerin tam listesini](../cognitive-services/translator/language-support.md) görün|
| fromLanguageCode  | Metnin geçerli dilini gösteren bir dize. Bu parametre belirtilmemişse, metni çevirmek için defaultFromLanguageCode (veya defaultFromLanguageCode sağlanmazsa otomatik dil algılama) kullanılır. <br/>[Desteklenen dillerin tam listesini](../cognitive-services/translator/language-support.md) görün|

## <a name="skill-outputs"></a>Yetenek çıkışları

| Çıkış adı    | Description |
|--------------------|-------------|
| translatedText | TranslatedFromLanguageCode öğesinden translatedToLanguageCode öğesine metin çevirisinin dize sonucu.|
| translatedToLanguageCode  | Metnin çevrildiği dil kodunu belirten dize. Birden çok dile çeviriyor ve hangi metnin hangi dilde olduğunu takip etmek istiyorsanız yararlı olur.|
| translatedFromLanguageCode    | Metnin çevrildiği dil kodunu belirten dize. Bu çıktı size bu algılamanın sonucunu verceğinden otomatik dil algılama seçeneğini kabul ettiyseniz yararlı olur.|

##  <a name="sample-definition"></a>Örnek tanım

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
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
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Hatalar ve uyarılar
Ya da ' den-' dili için desteklenmeyen bir dil kodu sağlarsanız bir hata oluşturulur ve metin çevrilmez.
Metniniz boşsa bir uyarı üretilir.
Metniniz 50.000 karakterden fazlaysa, yalnızca ilk 50.000 karakter çevrilir ve bir uyarı verilir.

## <a name="see-also"></a>Ayrıca bkz.

+ [Yerleşik yetenekler](cognitive-search-predefined-skills.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)