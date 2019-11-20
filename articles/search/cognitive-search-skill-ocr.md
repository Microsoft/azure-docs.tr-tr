---
title: OCR Bilişsel Beceri
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de bir zenginleştirme ardışık düzeninde optik karakter tanıma (OCR) kullanarak görüntü dosyalarından metin ayıklayın.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bdb510113a8d65ac04b54e77158f46d03cccd9de
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791917"
---
# <a name="ocr-cognitive-skill"></a>OCR Bilişsel Beceri

**Optik karakter tanıma (OCR)** yeteneği, görüntü dosyalarında yazdırılmış ve el yazısı metinleri tanır. Bu beceri bilişsel hizmetler 'de [görüntü işleme](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) tarafından sunulan makine öğrenimi modellerini kullanır. **OCR** becerisi aşağıdaki işlevlere eşlenir:

+ Ingilizce dışındaki diller için ["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-optical-character-recognition-api) API 'si kullanılır. 
+ Ingilizce için yeni ["okuma"](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) API 'si kullanılır.

**OCR** becerisi, resim dosyalarından metin ayıklar. Desteklenen dosya biçimleri şunlardır:

+ . JPEG
+ . JPG
+ . KITAPLıĞıNı
+ . BMP
+ . GIF
+ . DOSYALARıNDA

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://go.microsoft.com/fwlink/?linkid=2042400)açıklanmaktadır.


## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır.

| Parametre adı     | Açıklama |
|--------------------|-------------|
| detectOrientation | Görüntü yönünün yeniden algılanmasına izin vermez. <br/> Geçerli değerler: true/false.|
|defaultLanguageCode | <p>  Giriş metninin dil kodu. Desteklenen diller: <br/> zh-Hans (Çinesebasitleştirilmiş) <br/> zh-Hant (çon Setradi) <br/>CS (Çek) <br/>da (Danimarka) <br/>nl (Felemenkçe) <br/>En (Ingilizce) <br/>Fi (Fince)  <br/>fr (Fransızca) <br/>  de (Almanca) <br/>el (Yunanca) <br/> Hu (Macarca) <br/> BT (Italyanca) <br/>  Ja (Japonca) <br/> Ko (Korece) <br/> NB (Norveç) <br/>   pl (Lehçe) <br/> PT (Portekizce) <br/>  ru (Rusça) <br/>  es (Ispanyolca) <br/>  ZF (Isveççe) <br/>  tr (Türkçe) <br/> AR (Arapça) <br/> ro (Rumence) <br/> SR-Cyrl (SerbianCyrillic) <br/> sr-Latn (SerbianLatin) <br/>  SK (Slovakça). <br/>  UNK (bilinmiyor) <br/><br/> Dil kodu belirtilmemişse veya null ise, dil Ingilizce olarak ayarlanır. Dil açıkça "UNK" olarak ayarlandıysa dil otomatik olarak algılanır. </p> |
|Satır sonu | Algılanan her satır arasında kullanılacak değer. Olası değerler: ' Space ', ' CarriageReturn ', ' LineFeed '.  Varsayılan değer ' Space ' |

Daha önce, yeteneğin "yazdırılmış" veya "el ile" metin ayıklanıp ayıklanmayacağını belirtmek için "textExtractionAlgorithm" adlı bir parametre vardı.  Bu parametre kullanım dışıdır ve en son okuma API 'SI algoritması aynı anda her iki türden metni ayıklamada artık gerekli değildir.  Yetenek tanımınızda zaten bu parametre varsa, onu kaldırmanız gerekmez, ancak artık kullanılmayacak ve her iki tür metin de ne şekilde ayarlandığına bakılmaksızın ileri doğru şekilde ayıklanacaktır.

## <a name="skill-inputs"></a>Beceri girişleri

| Giriş adı      | Açıklama                                          |
|---------------|------------------------------------------------------|
| image         | Karmaşık tür. Şu anda, ```imageAction``` ```none```dışında bir değere ayarlandığında Azure Blob Indexer tarafından oluşturulan "/Document/normalized_ımages" alanı ile birlikte çalışıyor. Daha fazla bilgi için [örneğe](#sample-output) bakın.|


## <a name="skill-outputs"></a>Yetenek çıkışları
| Çıkış adı     | Açıklama                   |
|---------------|-------------------------------|
| metin          | Görüntüden ayıklanan düz metin.   |
| layoutText    | Ayıklanan metni ve metnin bulunduğu konumu açıklayan karmaşık tür.|


## <a name="sample-definition"></a>Örnek tanım

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Örnek metin ve layoutText çıktısı

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Örnek: katıştırılmış görüntülerden ayıklanan metni belge içeriğiyle birleştirme.

Metin Merbilirliği için ortak kullanım örneği, görüntülerin metinsel gösterimini (bir OCR özelliğinden metin veya bir görüntünün başlığını) belgenin içerik alanına birleştirebilme yeteneğidir.

Aşağıdaki örnek beceri bir *merged_text* alanı oluşturur. Bu alan, belgenizin metinsel içeriğini ve bu belgede gömülü resimlerin her birinden OCRed metni içerir.

#### <a name="request-body-syntax"></a>İstek Gövdesi Sözdizimi
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
Yukarıdaki beceri örnek, normalleştirilmiş görüntüler alanının bulunduğunu varsayar. Bu alanı oluşturmak için, Dizin Oluşturucu tanımınızda *ımageaction* yapılandırmasını aşağıda gösterildiği gibi *Generatenormalizmages* olarak ayarlayın:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Ayrıca bkz.
+ [Yerleşik yetenekler](cognitive-search-predefined-skills.md)
+ [TextMerger yeteneği](cognitive-search-skill-textmerger.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [Dizin Oluşturucu oluştur (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
