---
title: Görüntü analizi Bilişsel Beceri
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de bir AI zenginleştirme işlem hattındaki görüntü analizi bilişsel yeteneği kullanarak görüntü analizi aracılığıyla anlamsal metin ayıklayın.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4819f34e16efebcdab734270988382e086c44e36
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73479708"
---
# <a name="image-analysis-cognitive-skill"></a>Görüntü analizi Bilişsel Beceri

**Görüntü analizi** özelliği, görüntü içeriğine göre zengin bir görsel özellikler kümesini ayıklar. Örneğin, bir görüntüden açıklamalı alt yazı oluşturabilir, Etiketler oluşturabilir veya ünlüleri ve yer işaretlerini tanımlayabilir. Bu beceri bilişsel hizmetler 'de [görüntü işleme](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) tarafından sunulan makine öğrenimi modellerini kullanır. 

> [!NOTE]
> Küçük birimler (20 işlem altında) Azure Bilişsel Arama ücretsiz olarak yürütülebilir, ancak daha büyük iş yükleri faturalanabilir bilişsel [Hizmetler kaynağı iliştirmesini](cognitive-search-attach-cognitive-services.md)gerektirir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://go.microsoft.com/fwlink/?linkid=2042400)açıklanmaktadır.


## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. Vision. ımageanalysisbeceri 

## <a name="skill-parameters"></a>Yetenek parametreleri

Parametreler büyük/küçük harfe duyarlıdır.

| Parametre adı     | Açıklama |
|--------------------|-------------|
| defaultLanguageCode   |  Döndürülecek dili gösteren bir dize. Hizmet, belirtilen bir dilde tanıma sonuçları döndürür. Bu parametre belirtilmemişse, varsayılan değer "en" olur. <br/><br/>Desteklenen diller şunlardır: <br/>*en* -İngilizce (varsayılan) <br/> *zh* -Basitleştirilmiş Çince|
|visualFeatures |   Döndürülecek görsel özellik türlerini gösteren dizeler dizisi. Geçerli görsel özellik türleri şunlardır:  <ul><li> *Kategoriler* -bilişsel Hizmetler [görüntü işleme belgelerinde](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy)tanımlanan bir sınıflandırmaya göre görüntü içeriğini kategorilere ayırır. </li><li> *Etiketler* -görüntüyü, resim içeriğiyle ilgili ayrıntılı bir sözcük listesiyle Etiketler.</li><li>*Açıklama* -tam İngilizce cümle içeren görüntü içeriğini açıklar.</li><li>*yüzler* -yüzlerin mevcut olup olmadığını algılar. Varsa, koordinatlar, cinsiyet ve yaş üretir.</li><li>    *ImageType* -resmin küçük resim veya çizgi çizimi olduğunu algılar.</li><li>  *Color* -vurgu rengini, baskın rengi ve görüntünün siyah & beyaz olduğunu belirler.</li><li>*yetişkin* -görüntünün doğası halinde pornografik (çıplaklık veya bir sex Yasası gösterir) olduğunu algılar. Cinsel ve kışkırtıcı içerik de algılanır.</li></ul> Görsel özelliklerin adları büyük/küçük harfe duyarlıdır.|
| Bilgileri   | Hangi alana özgü ayrıntıların dönebileceğini gösteren dizeler dizisi. Geçerli görsel özellik türleri şunlardır: <ul><li>*ünlüler* -görüntüde algılanırsa ünlülikler tanımlar.</li><li>yer *işaretleri* -görüntüde algılanırsa yer işaretlerini tanımlar. </li></ul> |

## <a name="skill-inputs"></a>Beceri girişleri

| Giriş adı      | Açıklama                                          |
|---------------|------------------------------------------------------|
| image         | Karmaşık tür. Şu anda, ```imageAction``` ```none```dışında bir değere ayarlandığında Azure Blob Indexer tarafından oluşturulan "/Document/normalized_ımages" alanı ile birlikte çalışıyor. Daha fazla bilgi için [örneğe](#sample-output) bakın.|



##  <a name="sample-skill-definition"></a>Örnek yetenek tanımı

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "Tags",
                "Categories",
                "Description",
                "Faces"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>Örnek dizin (yalnızca Kategoriler, açıklama, yüzler ve Etiketler alanları için)
```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```
### <a name="sample-output-field-mapping-for-the-above-index"></a>Örnek çıkış alanı eşlemesi (yukarıdaki dizin için)
```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>Çıkış alanı eşlemelerinde çeşitleme (iç içe Özellikler)

Yalnızca yer işaretleri veya ünlüler gibi alt düzey özelliklerle çıkış alanı eşlemelerini tanımlayabilirsiniz. Bu durumda, Dizin şemanızın özel olarak dikkat edilecek bir alana sahip olduğundan emin olun.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```
##  <a name="sample-input"></a>Örnek giriş

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
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
                "categories": [
                    {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                        "name": "people_",
                        "score": 0.83984375,
                        "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceBoundingBox": [
                                        {
                                            "x": 273,
                                            "y": 309
                                        },
                                        {
                                            "x": 395,
                                            "y": 309
                                        },
                                        {
                                            "x": 395,
                                            "y": 431
                                        },
                                        {
                                            "x": 273,
                                            "y": 431
                                        }
                                    ],
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                        "faceBoundingBox": [
                            {
                                "x": 1601,
                                "y": 395
                            },
                            {
                                "x": 1653,
                                "y": 395
                            },
                            {
                                "x": 1653,
                                "y": 447
                            },
                            {
                                "x": 1601,
                                "y": 447
                            }
                        ]
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBwImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
            }
        }
    ]
}
```


## <a name="error-cases"></a>Hata durumları
Aşağıdaki hata durumlarında hiçbir öğe ayıklanmaz.

| Hata kodu | Açıklama |
|------------|-------------|
| NotSupportedLanguage | Belirtilen dil desteklenmiyor. |
| InvalidImageUrl | Görüntü URL 'SI hatalı biçimlendirildi veya erişilebilir durumda değil.|
| InvalidImageFormat | Giriş verileri geçerli bir görüntü değil. |
| InvalidImageSize | Giriş resmi çok büyük. |
| NotSupportedVisualFeature  | Belirtilen özellik türü geçerli değil. |
| NotSupportedImage | Desteklenmeyen görüntü, örneğin alt pornografi. |
| Invaliddetails | Etki alanına özgü desteklenmeyen model. |

`"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"`benzer bir hata alırsanız yolu denetleyin. Her iki ünlüler ve yer işaretleri `detail`altında özelliklerdir.

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>Ayrıca bkz.

+ [Yerleşik yetenekler](cognitive-search-predefined-skills.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [Dizin Oluşturucu oluştur (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
