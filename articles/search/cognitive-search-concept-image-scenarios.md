---
title: Görüntülerden metin Ayıkla
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama işlem hatları 'ndaki görüntülerden metin ve diğer bilgileri işleyin ve ayıklayın.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f81bcd84dfb07958f3205f779937b8beac74166f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113841"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>AI zenginleştirme senaryolarında görüntülerden bilgi işleme ve ayıklama

Azure Bilişsel Arama, görüntülerle ve görüntü dosyalarıyla çalışmaya yönelik çeşitli yeteneklere sahiptir. Belge çözme sırasında, bir Dur işareti içinde "Durdur" sözcüğü gibi fotoğraflardan veya alfasayısal metin içeren resimlerden metin ayıklamak için *ımageaction* parametresini kullanabilirsiniz. Diğer senaryolar, bir resmin metin gösterimini (örneğin, dans eden bir fotoğraf veya "sarı" rengi) oluşturmayı içerir. Görüntü hakkında, boyutu gibi meta verileri de ayıklayabilirsiniz.

Bu makale, görüntü işlemeyi daha ayrıntılı bir şekilde ele alır ve bir AI zenginleştirme ardışık düzeninde görüntülerle çalışmaya yönelik rehberlik sağlar.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Normalleştirilmiş görüntüleri al

Belge çözme kapsamında, görüntü dosyalarını veya dosyalarda gömülü görüntüleri işlemek için yeni bir Dizin Oluşturucu yapılandırma parametreleri kümesi vardır. Bu parametreler, daha fazla aşağı akış işleme için görüntüleri normalleştirmek üzere kullanılır. Görüntülerin normalleştirilmesi, daha Tekdüzen hale gelir. Büyük görüntüler, tüketilebilir hale getirmek için maksimum yüksekliğe ve genişliğe göre yeniden boyutlandırılır. Yönlendirme üzerine meta veri sağlayan görüntüler için, görüntü döndürme dikey yükleme için ayarlanır. Meta veri ayarlamaları her görüntü için oluşturulan karmaşık bir tür içinde yakalanır. 

Resim normalleştirmesini kapatamaz. Görüntüler üzerinde yineleme yapan yetenekler, normalleştirilmiş görüntüler bekler. Bir dizin oluşturucuda görüntü normalleştirmesini etkinleştirmek için, bu dizin oluşturucuya bir beceri eklenmiş olması gerekir.

| Yapılandırma parametresi | Açıklama |
|--------------------|-------------|
| ımageaction   | Katıştırılmış görüntüler veya resim dosyaları ile karşılaşıldığında hiçbir işlem yapılması bekleniyorsa "none" olarak ayarlayın. <br/>Belge çözme işleminin bir parçası olarak normalleştirilmiş görüntülerin bir dizisini oluşturmak için "Generatenormalizedileges" olarak ayarlayın.<br/>Veri kaynağınızdaki PDF 'Ler için her sayfa bir çıkış görüntüsüne işlendiğinde, bir dizi normalleştirilmiş görüntü oluşturmak için "generateNormalizedImagePerPage" olarak ayarlayın.  Bu işlevsellik, PDF olmayan dosya türleri için "Generatenormalizediges" ile aynıdır.<br/>"None" olmayan herhangi bir seçenek için, görüntüler *normalized_images* alanında görüntülenir. <br/>Varsayılan değer "none" dır. Bu yapılandırma, "dataToExtract" ayarı "contentAndMetadata" olarak ayarlandığında yalnızca blob veri kaynaklarıyla ilgili olur. <br/>Verilen bir belgeden en fazla 1000 resim ayıklanacaktır. Bir belgede 1000 ' den fazla görüntü varsa, ilk 1000 ayıklanır ve bir uyarı oluşturulur. |
|  normalizedImageMaxWidth | Oluşturulan normalleştirilmiş görüntülerin en büyük genişliği (piksel cinsinden). Varsayılan değer 2000 ' dir. İzin verilen en büyük değer 10000 ' dir. | 
|  normalizedImageMaxHeight | Oluşturulan normalleştirilmiş görüntülerin en büyük yüksekliği (piksel cinsinden). Varsayılan değer 2000 ' dir. İzin verilen en büyük değer 10000 ' dir.|

> [!NOTE]
> *Imageaction* özelliğini "none" dışında bir şeye ayarlarsanız, *parsingmode* özelliğini "varsayılan" dışında bir şeye ayarlayamazsınız.  Bu iki özelliklerden birini, Dizin Oluşturucu yapılandırmanızda varsayılan olmayan bir değere ayarlayabilirsiniz.

**Parsingmode** parametresini `json` (her Blobun tek bir belge olarak dizinini oluşturma) veya `jsonArray` (BLOBLARıNıZ JSON dizileri içeriyorsa ve bir dizinin her bir öğesi ayrı bir belge olarak değerlendirilmelidir) olarak ayarlayın.

Normalleştirilmiş görüntüler için varsayılan değer olan 2000 piksel en fazla genişlik ve yükseklik, [OCR becerisi](cognitive-search-skill-ocr.md) ve [görüntü analizi yeteneği](cognitive-search-skill-image-analysis.md)tarafından desteklenen boyut üst sınırını temel alır. [OCR becerisi](cognitive-search-skill-ocr.md) , İngilizce dışındaki diller için maksimum genişlik ve yükseklik 4200 ve ingilizce için 10000 ' i destekler.  Maksimum sınırları artırırsanız, Beceri tanımınıza ve belgelerin diline bağlı olarak daha büyük görüntülerde işleme başarısız olabilir. 

Aşağıdaki şekilde, [Dizin Oluşturucu tanımınızda](https://docs.microsoft.com/rest/api/searchservice/create-indexer) ımageaction öğesini belirtirsiniz:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

*Imageaction* , "none" dışındaki bir değere ayarlandığında, yeni *normalized_images* alanı bir görüntü dizisi içerir. Her görüntü, aşağıdaki üyelere sahip karmaşık bir türdür:

| Görüntü üyesi       | Açıklama                             |
|--------------------|-----------------------------------------|
| data               | JPEG biçimindeki normalleştirilmiş görüntünün BASE64 kodlamalı dizesi.   |
| Genişlik              | Normalleştirilmiş resmin piksel cinsinden genişliği. |
| Yükseklik             | Normalleştirilmiş resmin piksel cinsinden yüksekliği. |
| originalWidth      | Normalleştirme yapmadan önce resmin orijinal genişliği. |
| originalHeight      | Normalleştirme yapmadan önce resmin orijinal yüksekliği. |
| rotationFromOriginal |  Normalleştirilmiş görüntüyü oluşturmak için oluşan derece saat yönünde döndürme. 0 derece ve 360 derece arasında bir değer. Bu adım, kamera veya tarayıcı tarafından oluşturulan görüntüden meta verileri okur. Genellikle 90 derecenin katları. |
| Contenentoffset | Görüntünün ayıklandığı içerik alanı içindeki karakter konumu. Bu alan yalnızca katıştırılmış görüntülere sahip dosyalar için geçerlidir. |
| pageNumber | Görüntü bir PDF 'den ayıklandıysa veya işlendiğinde, bu alan, 1 ' den başlayarak ayıklanan veya işlenen PDF 'deki sayfa numarasını içerir.  Görüntü bir PDF 'den değilse, bu alan 0 olur.  |

 *Normalized_images*örnek değeri:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Görüntüyle ilgili yetenekler

Resimleri giriş olarak alan iki yerleşik Bilişsel Beceri vardır: [OCR](cognitive-search-skill-ocr.md) ve [görüntü analizi](cognitive-search-skill-image-analysis.md). 

Şu anda bu yetenekler yalnızca belge çözme adımından oluşturulan görüntülerle çalışır. Bu nedenle, desteklenen tek giriş `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Görüntü analizi yeteneği

[Görüntü analizi](cognitive-search-skill-image-analysis.md) özelliği, görüntü içeriğine göre zengin bir görsel özellikler kümesini ayıklar. Örneğin, bir görüntüden açıklamalı alt yazı oluşturabilir, Etiketler oluşturabilir veya ünlüleri ve yer işaretlerini tanımlayabilir.

### <a name="ocr-skill"></a>OCR becerisi

[OCR becerisi](cognitive-search-skill-ocr.md) , jpgs, png 'ler ve bit eşlemler gibi görüntü dosyalarından metin ayıklar. Ayrıca, metin ve düzen bilgilerini ayıklayabilir. Düzen bilgileri, tanımlanan dizelerin her biri için sınırlayıcı kutular sağlar.

## <a name="embedded-image-scenario"></a>Katıştırılmış resim senaryosu

Yaygın bir senaryo, aşağıdaki adımları gerçekleştirerek hem metin hem de görüntü kaynağı metin olan tüm dosya içeriklerini içeren tek bir dize oluşturmayı kapsar:  

1. [Normalized_images Ayıkla](#get-normalized-images)
1. `"/document/normalized_images"` giriş olarak kullanarak OCR becerisi çalıştırma
1. Bu görüntülerin metin gösterimini dosyadan ayıklanan ham metinle birleştirin. Metin [birleştirme](cognitive-search-skill-textmerger.md) beceriye her iki metin öbeklerini tek bir büyük dizedeki birleştirmek için kullanabilirsiniz.

Aşağıdaki örnek Beceri, belgenizin metinsel içeriğini içeren bir *merged_text* alanı oluşturur. Ayrıca, katıştırılmış görüntülerden her birinden OCRed metni de içerir. 

#### <a name="request-body-syntax"></a>İstek gövdesi sözdizimi
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

Artık merged_text alana sahip olduğunuza göre, Dizin Oluşturucu tanımınızda aranabilir bir alan olarak eşleyebilirsiniz. Resimlerin metni de dahil olmak üzere dosyalarınızın tüm içeriği aranabilir olacaktır.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Ayıklanan metnin sınırlayıcı kutularını görselleştirin

Diğer bir yaygın senaryo, arama sonuçları düzen bilgilerini görselleştiriliyor. Örneğin, arama sonuçlarının bir parçası olarak görüntüde bir metin parçasının nerede olduğunu vurgulamak isteyebilirsiniz.

Normalleştirilmiş görüntülerde OCR adımı gerçekleştirildiğinden, düzen koordinatları normalleştirilmiş görüntü alanında yer alır. Normalleştirilmiş görüntüyü görüntülerken, koordinatların varlığı genellikle bir sorun değildir, ancak bazı durumlarda özgün görüntüyü görüntülemek isteyebilirsiniz. Bu durumda, düzendeki her bir koordinat noktasını özgün görüntü koordinat sistemine dönüştürün. 

Yardımcı olarak, normalleştirilmiş koordinatları özgün koordinat alanına dönüştürmeniz gerekiyorsa aşağıdaki algoritmayı kullanabilirsiniz:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>Ayrıca bkz.
+ [Dizin Oluşturucu oluştur (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Görüntü becermesini çözümle](cognitive-search-skill-image-analysis.md)
+ [OCR becerisi](cognitive-search-skill-ocr.md)
+ [Metin birleştirme yeteneği](cognitive-search-skill-textmerger.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [Zenginleştirilmiş alanları eşleme](cognitive-search-output-field-mapping.md)
