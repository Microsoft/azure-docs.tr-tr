---
title: Belge ayıklama bilişsel yeteneği
titleSuffix: Azure Cognitive Search
description: Zenginleştirme işlem hattının içindeki bir dosyadan içerik ayıklar. Bu yetenek Şu anda genel önizlemededir.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: chalton
ms.openlocfilehash: 144e8058e640f98dc6b0ef60534405525532b00e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547875"
---
# <a name="document-extraction-cognitive-skill"></a>Belge ayıklama bilişsel yeteneği

**Belge ayıklama** becerisi, zenginleştirme işlem hattının içindeki bir dosyadan içerik ayıklar. Bu, normalde, diğer yetenekler tarafından oluşturulabilecek dosyalarla beceri yürütmeden önce gerçekleşen belge ayıklama adımının avantajlarından yararlanmanızı sağlar.

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve dizin oluşturma aşamasında belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücret tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/search/)açıklanmaktadır.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocUmentextractionbeceri

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır.

| Girişler | İzin Verilen Değerler | Description |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | `default`Saf metin veya JSON olmayan dosyalardan belge ayıklama için olarak ayarlayın. `text`Düz metin dosyalarındaki performansı artırmak için olarak ayarlayın. `json`JSON dosyalarından yapılandırılmış içeriği ayıklamak için olarak ayarlayın. `parsingMode`Açıkça tanımlanmamışsa, olarak ayarlanır `default` . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Her bir `contentAndMetadata` dosyanın tüm meta verilerini ve metin içeriğini ayıklamak için olarak ayarlayın. `allMetadata` [İçerik türü için yalnızca meta veri özelliklerini](search-blob-metadata-properties.md) ayıklamak üzere olarak ayarlanır (örneğin, yalnızca. png dosyalarına özgü meta veriler). `dataToExtract`Açıkça tanımlanmamışsa, olarak ayarlanır `contentAndMetadata` . |
| `configuration` | Aşağıya bakın. | Belge ayıklamanın nasıl gerçekleştirileceğini belirten isteğe bağlı parametrelerin bir sözlüğü. Desteklenen yapılandırma özelliklerinin açıklamaları için aşağıdaki tabloya bakın. |

| Yapılandırma parametresi   | İzin Verilen Değerler | Description |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | `none`Veri kümesindeki katıştırılmış görüntüleri veya resim dosyalarını yoksaymak için olarak ayarlayın. Bu varsayılan seçenektir. <br/>Bilişsel [becerileri kullanan görüntü analizi](cognitive-search-concept-image-scenarios.md)için, `generateNormalizedImages` niteliğin belge çözme kapsamında normalleştirilmiş görüntülerin bir dizisini oluşturmasını sağlamak için olarak ayarlayın. Bu eylem, `parsingMode` olarak ayarlanmış `default` ve `dataToExtract` olarak ayarlanmış olmasını gerektirir `contentAndMetadata` . Normalleştirilmiş bir görüntü, görsel arama sonuçlarına görüntü eklediğinizde (örneğin, [JFK demo](https://github.com/Microsoft/AzureSearch_JFK_Files)bölümünde görüldüğü gibi bir grafik denetimindeki aynı boyutlu fotoğraflar gibi) tutarlı işleme sağlamak için boyutlandırılmış ve döndürülen Tekdüzen görüntü çıkışına neden olan diğer işleme başvurur. Bu bilgi, bu seçeneği kullandığınızda her bir görüntü için oluşturulur.  <br/>' A ayarlarsanız `generateNormalizedImagePerPage` , PDF dosyaları gömülü görüntüleri ayıklamak yerine farklı şekilde değerlendirilir, her sayfa bir görüntü olarak işlenir ve buna göre normalleştirilirler.  PDF olmayan dosya türleri, ayarlandığı gibi kabul edilir `generateNormalizedImages` .
| `normalizedImageMaxWidth` | 50-10000 arasında herhangi bir tamsayı | Oluşturulan normalleştirilmiş görüntülerin en büyük genişliği (piksel cinsinden). Varsayılan değer 2000’dir. | 
| `normalizedImageMaxHeight` | 50-10000 arasında herhangi bir tamsayı | Oluşturulan normalleştirilmiş görüntülerin en büyük yüksekliği (piksel cinsinden). Varsayılan değer 2000’dir. |

> [!NOTE]
> Normalleştirilmiş görüntüler için varsayılan değer olan 2000 piksel en fazla genişlik ve yükseklik, [OCR becerisi](cognitive-search-skill-ocr.md) ve [görüntü analizi yeteneği](cognitive-search-skill-image-analysis.md)tarafından desteklenen boyut üst sınırını temel alır. [OCR becerisi](cognitive-search-skill-ocr.md) , İngilizce dışındaki diller için maksimum genişlik ve yükseklik 4200 ve ingilizce için 10000 ' i destekler.  Maksimum sınırları artırırsanız, Beceri tanımınıza ve belgelerin diline bağlı olarak daha büyük görüntülerde işleme başarısız olabilir. 
## <a name="skill-inputs"></a>Beceri girişleri

| Giriş adı     | Description |
|--------------------|-------------|
| `file_data` | İçeriğin ayıklanabilmesi gereken dosya. |

"File_data" girişi şöyle tanımlanmış bir nesne olmalıdır:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Bu dosya başvuru nesnesi 3 farklı bir şekilde oluşturulabilir:

 - `allowSkillsetToReadFileData`Dizin Oluşturucu tanımınızda parametre "true" olarak ayarlanıyor.  Bu işlem, `/document/file_data` BLOB veri kaynağınızdan indirilen özgün dosya verilerini temsil eden bir nesne olan bir yol oluşturur. Bu parametre yalnızca blob depolamada bulunan veriler için geçerlidir.

 - `imageAction`Dizin Oluşturucu tanımınızda parametresi dışında bir değere ayarlanıyor `none` .  Bu, tek tek geçirilirse (yani), bu beceriye giriş için gerekli kuralı izleyen bir görüntü dizisi oluşturur `/document/normalized_images/*` .

 - Özel bir beceriye sahip olmak, yukarıdaki gibi tam olarak tanımlanmış bir JSON nesnesi döndürür.  `$type`Parametresi tam olarak ayarlanmalıdır `file` ve `data` parametresi dosya içeriğinin temel 64 kodlu bayt dizisi verileri olmalıdır.

## <a name="skill-outputs"></a>Yetenek çıkışları

| Çıkış adı    | Description |
|--------------|-------------|
| `content` | Belgenin metinsel içeriği. |
| `normalized_images`   | `imageAction`Daha sonra diğer bir değere ayarlandığında `none` , yeni *normalized_images* alanı bir görüntü dizisi içerir. Her bir görüntünün çıkış biçimi hakkında daha fazla bilgi için bkz. [görüntü ayıklama belgeleri](cognitive-search-concept-image-scenarios.md) . |

##  <a name="sample-definition"></a>Örnek tanım

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "extracted_content"
      },
      {
        "name": "normalized_images",
        "targetName": "extracted_normalized_images"
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
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
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
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Ayrıca bkz.

+ [Yerleşik yetenekler](cognitive-search-predefined-skills.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [Bilişsel arama senaryolarında görüntülerden bilgi işleme ve çıkarma](cognitive-search-concept-image-scenarios.md)