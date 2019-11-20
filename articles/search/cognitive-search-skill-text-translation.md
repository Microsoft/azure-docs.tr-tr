---
title: Metin çevirisi Bilişsel Beceri (Önizleme)
titleSuffix: Azure Cognitive Search
description: Metni değerlendirir ve her kayıt için Azure Bilişsel Arama içindeki bir AI zenginleştirme ardışık düzeninde belirtilen hedef dile çevrilen metni döndürür. Bu yetenek Şu anda genel önizlemededir.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7c42c9033fac057c12426726a96ae6079f3080da
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715403"
---
#   <a name="text-translation-cognitive-skill"></a>Metin çevirisi Bilişsel Beceri

> [!IMPORTANT] 
> Bu yetenek Şu anda genel önizlemededir. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API sürüm 2019-05-06-önizleme](search-api-preview.md) , Önizleme özellikleri sağlar. Şu anda sınırlı sayıda portal desteği var ve .NET SDK desteği yok.

Metin **çevirisi** becerisi metni değerlendirir ve her kayıt için belirtilen hedef dile çevrilen metni döndürür. Bu beceri bilişsel hizmetler 'de bulunan [Translator metin çevirisi API'si v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) 'ı kullanır.

Bu özellik, belgelerinizin tümünün tek bir dilde olmaması beklendiğinde yararlıdır, bu durumda, arama için dizin oluşturmadan önce metni tek bir dile normalleştirilemeyebilirsiniz.  Aynı zamanda birden çok dilde aynı metnin kopyalarının olmasını isteyebileceğiniz yerelleştirme kullanım durumları için de kullanışlıdır.

[Translator metin çevirisi API'si v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) , bölgesel olmayan bir bilişsel hizmettir, yani verilerinizin Azure bilişsel arama veya bağlı bilişsel hizmetler kaynağınız ile aynı bölgede kalması garanti edilmez.

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://go.microsoft.com/fwlink/?linkid=2042400)açıklanmaktadır.

## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. Text. Translationbeceri

## <a name="data-limits"></a>Veri sınırları
Bir kaydın en büyük boyutu, [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)ölçülen 50.000 karakter olmalıdır. Verileri metin çevirisi yeteneklerine göndermeden önce bölmeniz gerekirse, [metin bölme becerisinin](cognitive-search-skill-textsplit.md)kullanımını göz önünde bulundurun.

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır.

| Girişler                | Açıklama |
|---------------------|-------------|
| defaultToLanguageCode | Istenir Kendisine açık olarak belirtmeyen belgeler için belgeleri içine çevirecek dil kodu. <br/> [Desteklenen dillerin tam listesini](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)görüntüleyin. |
| defaultFromLanguageCode | Seçim Özel olarak kimden dilini belirtmeyen belgeler için belge çeviren dil kodu.  DefaultFromLanguageCode belirtilmemişse, kimden dilini saptamak için Translator Metin Çevirisi API'si tarafından sunulan otomatik dil algılama kullanılır. <br/> [Desteklenen dillerin tam listesini](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)görüntüleyin. |
| müported | Seçim FromLanguageCode girişi ya da defaultFromLanguageCode parametresi sağlanmıyorsa ve otomatik dil algılama başarısız olduğunda belgelerin çevrilme dil kodu.  Bir dil için bir dil belirtilmemişse, Ingilizce (en), dilden Itibaren bu şekilde kullanılacaktır. <br/> [Desteklenen dillerin tam listesini](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)görüntüleyin. |

## <a name="skill-inputs"></a>Beceri girişleri

| Giriş adı     | Açıklama |
|--------------------|-------------|
| metin | Çevrilecek metin.|
| toLanguageCode    | Metnin çevrilmesi gereken dili belirten dize. Bu giriş belirtilmemişse, metni çevirmek için defaultToLanguageCode kullanılır. <br/>[Desteklenen dillerin tam listesini](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) görün|
| fromLanguageCode  | Metnin geçerli dilini gösteren bir dize. Bu parametre belirtilmemişse, metni çevirmek için defaultFromLanguageCode (veya defaultFromLanguageCode sağlanmazsa otomatik dil algılama) kullanılır. <br/>[Desteklenen dillerin tam listesini](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) görün|

## <a name="skill-outputs"></a>Yetenek çıkışları

| Çıkış adı    | Açıklama |
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

##  <a name="sample-input"></a>Örnek giriş

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
