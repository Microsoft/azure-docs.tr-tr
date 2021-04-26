---
title: İş kartları-form tanıyıcı
titleSuffix: Azure Cognitive Services
description: Tanıyıcı API kullanımı ve limitleriyle iş kartı analizine ilişkin kavramları öğrenin.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5211c1263af599eb5fd09ad276545c725ce5c867
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467004"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>Form tanıyıcı önceden oluşturulmuş iş kartları modeli 

Azure form tanıyıcı, önceden oluşturulmuş iş kartları modelini kullanarak iş kartlarından iletişim bilgilerini çözümleyebilir ve ayıklayabilir. Güçlü optik karakter tanıma (OCR) yeteneklerini iş kartlarımızla, Ingilizce 'deki iş kartlarında önemli bilgileri ayıklamak için modelleme modeliyle birleştirir. Kişisel iletişim bilgilerini, şirket adını, iş başlığını ve daha fazlasını ayıklar. Önceden oluşturulmuş Iş kartı API 'SI, tanıyıcı v 2.1 Preview biçiminde herkese açık bir şekilde sunulmaktadır. 

## <a name="what-does-the-business-card-service-do"></a>Iş kartı hizmeti ne yapar?

Önceden oluşturulmuş Iş kartı API 'SI, iş kartlarından anahtar alanları ayıklar ve bunları düzenlenmiş bir JSON yanıtında döndürür.

![Contoso FOTT + JSON çıktısından resim dökümü](./media/business-card-example.jpg)



### <a name="fields-extracted"></a>Ayıklanan alanlar:

|Ad| Tür | Description | Metin | 
|:-----|:----|:----|:----|
| ContactNames | nesne dizisi | İş kartından ayıklanan kişi adı | [{"FirstName": "John", "LastName": "tikan"}] |
| FirstName | string | İlk (verilen) kişi adı | \ | 
| LastName | string | Kişinin son (aile) adı |     In | 
| CompanyNames | dize dizisi | Şirket adı, iş kartından ayıklandı | ["Contoso"] | 
| Departmanlar | dize dizisi | Kişinin Departmanı veya kuruluşu | ["R&D"] | 
| İş başlıkları | dize dizisi | İlgili kişinin Iş unvanı | ["Yazılım mühendisi"] | 
| E-postalar | dize dizisi | İş kartından ayıklanan iletişim e-postası | ["johndoe@contoso.com"] | 
| Web Siteleri | dize dizisi | Web sitesi, iş kartından ayıklandı | ["https://www.contoso.com"] | 
| Adresler | dize dizisi | İş kartından ayıklanan adres | ["123 Main Street, Redmond, WA 98052"] | 
| Mobiletelefonlar | telefon numaraları dizisi | İş kartından ayıklanan cep telefonu numarası | ["+ 19876543210"] |
| Yazdırılacağı | telefon numaraları dizisi | İş kartından ayıklanan Faks telefon numarası | ["+ 19876543211"] |
| Iş telefonları | telefon numaraları dizisi | İş kartından ayıklanan iş telefonu numarası | ["+ 19876543231"] |
| Diğer telefonlar     | telefon numaraları dizisi | İş kartından ayıklanan diğer telefon numarası | ["+ 19876543233"] |


Iş kartı API 'SI Ayrıca, Iş kartından tanınan tüm metinleri de döndürebilir. Bu OCR çıkışı JSON yanıtına dahildir.  

### <a name="input-requirements"></a>Giriş gereksinimleri 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>Iş kartını çözümle işlemi

[Çözümle Iş kartı](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync) , giriş olarak bir iş kartının GÖRÜNTÜSÜNÜ veya PDF 'sini alır ve ilgilendiğiniz değerleri ayıklar. Çağrı, adlı bir yanıt üst bilgisi alanı döndürüyor `Operation-Location` . `Operation-Location`Değer, bir sonraki adımda kullanılacak sonuç kimliğini içeren BIR URL 'dir.

|Yanıt üst bilgisi| Sonuç URL 'SI |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>Iş kartını çözümle sonucu al işlemi

İkinci adım, [Çözümle Iş kartı sonucunu al](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeBusinessCardResult) işlemini çağırmalıdır. Bu işlem, Iş kartını çözümle işlemi tarafından oluşturulan sonuç KIMLIĞINI giriş olarak alır. Aşağıdaki olası değerlere sahip bir **durum** alanı IÇEREN bir JSON yanıtı döndürür. **Başarılı** değerle döndürülünceye kadar bu işlemi tekrarlayarak çağırın. Saniye başına istek (RPS) oranını aşmamak için 3 ile 5 saniye arasında bir Aralık kullanın.

|Alan| Tür | Olası değerler |
|:-----|:----:|:----|
|durum | string | notStarted: analiz işlemi başlatılmamış.<br /><br />çalışıyor: analiz işlemi devam ediyor.<br /><br />başarısız: çözümleme işlemi başarısız oldu.<br /><br />başarılı: çözümleme işlemi başarılı oldu.|

**Durum** alanı **başarılı** DEĞERINE sahip olduğunda, JSON yanıtı, istenirse, iş kartı anlama ve isteğe bağlı metin tanıma sonuçlarını içerir. İş kartı anlama sonucu, her değerin ayıklanan metin, normalleştirilmiş değer, sınırlama kutusu, güvenirlik ve karşılık gelen Word öğelerini içerdiği adlandırılmış alan değerlerinin bir sözlüğü olarak düzenlenmiştir. Metin tanıma sonucu, metin, sınırlayıcı kutusu ve güvenle bilgi içeren bir satır ve sözcük hiyerarşisi olarak düzenlenir.

![örnek iş kartı çıkışı](./media/business-card-results.png)

### <a name="sample-json-output"></a>Örnek JSON çıkışı

Iş kartını çözümle sonucunu al işlemi, ayıklanan tüm bilgileri içeren iş kartının yapısal temsili olacaktır.  [Örnek bir iş kartı dosyası](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg) ve yapılandırılmış çıkış [örneği iş kartı çıkışı](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)için buraya bakın.

Başarılı bir JSON yanıtı örneğine bakın:
* `"readResults"`Düğüm, tüm tanınan metni içerir. Metin sayfaya, sonra satıra, sonra da tek sözcüklere göre düzenlenir. 
* `"documentResults"`Düğüm, modelin bulduğu iş kartına özgü değerleri içerir. Burada ad, soyadı, şirket adı ve daha fazlası gibi yararlı iletişim bilgileri bulabilirsiniz.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Python ve REST API kullanarak iş kartı verilerinin ayıklanmasını uygulamak için [hızlı](./QuickStarts/client-library.md) başlangıç hızlı başlangıcı ' nı izleyin.

## <a name="customer-scenarios"></a>Müşteri senaryoları  

Iş kartı API 'siyle ayıklanan veriler çeşitli görevleri gerçekleştirmek için kullanılabilir. Bu kişi bilgilerinin ayıklanması, istemci temelli rollerdeki kullanıcılar için zamanı otomatik olarak kaydeder. Müşterilerimizin Iş kartı API 'SI ile yaptığımız örneklere birkaç örnek aşağıda verilmiştir:

* Iş kartlarından kişi bilgilerini ayıklayın ve hızla telefon kişileri oluşturun. 
* İş kartı görüntülerini kullanarak otomatik olarak iletişim oluşturmak için CRM ile tümleştirin. 
* Satış fırsatlarını takip edin.  
* Mevcut iş kartı görüntülerinden toplu olarak kişi bilgilerini ayıklayın. 

Iş kartı API 'SI aynı zamanda [AI Oluşturucu Iş kartı işleme özelliğini](/ai-builder/prebuilt-business-card)de güçlendirir.

## <a name="next-steps"></a>Sonraki adımlar

- İş kartlarını tanımayı kullanmaya başlamak için [hızlı](./quickstarts/client-library.md) başlangıcı izleyin.

## <a name="see-also"></a>Ayrıca bkz.

* [Form Tanıma nedir?](./overview.md)
* [REST API başvuru belgeleri](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)
