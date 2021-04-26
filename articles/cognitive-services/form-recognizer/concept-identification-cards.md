---
title: Kimlikler-form tanıyıcı
titleSuffix: Azure Cognitive Services
description: Form tanıyıcı önceden oluşturulmuş kimlikler API 'SI ile kimlik belgelerinden veri ayıklama ile ilgili kavramları öğrenin.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: 00e51d2c9515191b6d127355f49eeed3000a46ed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514722"
---
# <a name="form-recognizer-prebuilt-identification-id-document-model"></a>Form tanıyıcı önceden oluşturulan kimlik (ID) belge modeli

Azure form tanıyıcı, önceden oluşturulmuş kimlikler modelini kullanarak devlet tarafından verilen tanımlama belgelerinden (kimlikler) bilgileri çözümleyebilir ve ayıklayabilir. Dünya çapındaki Passport ve ABD sürücüsünün lisanslarından önemli bilgileri ayıklamak için KIMLIK tanıma özelliklerine sahip güçlü [optik karakter tanıma (OCR)](../computer-vision/overview-ocr.md) olanaklarımızı birleştirir (tüm 50 durumlar ve D.C.). Kimlikler API 'SI bu kimlik belgelerinden, ad, soyadı, Doğum tarihi, belge numarası ve daha fazlası gibi önemli bilgileri ayıklar. Bu API, tanıyıcı v 2.1 önizlemesinde bulut hizmeti olarak ve şirket içi kapsayıcı olarak kullanılabilir.

## <a name="what-does-the-id-service-do"></a>KIMLIK hizmeti ne yapar?

Önceden oluşturulmuş kimlikler hizmeti, dünya çapındaki Passport ve ABD sürücüsünün lisanslarından gelen anahtar değerlerini ayıklar ve bunları düzenlenmiş yapılandırılmış bir JSON yanıtında döndürür.

### <a name="drivers-license-example"></a>**Sürücünün lisans örneği**

![Örnek sürücü lisansı](./media/id-example-drivers-license.JPG)

### <a name="passport-example"></a>**Passport örneği**

![Örnek Passport](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Ayıklanan alanlar

|Ad| Tür | Açıklama | Değer |
|:-----|:----|:----|:----|
|  Ülke | ülke | ISO 3166 standardı ile ülke kodu uyumlu | ABD |
|  Tarih Ofdoğum | date | DOB, YYYY-AA-GG biçiminde | "1980-01-01" |
|  DateOfExpiration | date | YYYY-AA-GG biçiminde sona erme tarihi | "2019-05-05" |
|  DocumentNumber | string | İlgili Passport numarası, sürücünün lisans numarası, vb. | "340020013" |
|  FirstName | string | Varsa verilen ad ve orta ilk başlangıç | JENNIFER |
|  LastName | string | Ayıklanan soyadı | "BROOKS" |
|  Uylık | ülke | ISO 3166 standardı ile ülke kodu uyumlu | ABD |
|  Komutu | cinsiyet | Olası ayıklanan değerler şunlardır "d", "F" ve "X" | "F" |
|  Machinereadadblezone | object | Ayıklanan Passport MRZ, her iki satır 44 karakter dahil | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentType | string | Belge türü, örneğin, Passport, sürücü lisansı | 'unuzun |
|  Adres | string | Ayıklanan adres (yalnızca sürücü lisansı) | "123 SOKAK ADRESINI ŞEHIR WA 99999-1234"|
|  Region | string | Ayıklanan bölge, eyalet, eyalet, vb. (yalnızca sürücü lisansı) | Eyaleti |

### <a name="additional-features"></a>Ek özellikler

Kimlikler API 'SI de aşağıdaki bilgileri döndürür:

* Alan güven düzeyi (her alan ilişkili bir güven değeri döndürür)
* OCR ham metni (tüm alındı için OCR ayıklanan metin çıktısı)
* ABD sürücüsünün lisanslarında ayıklanan her alanın sınırlayıcı kutusu
* Passport 'lar üzerinde makine tarafından okunabilen bölge (MRZ) için sınırlayıcı kutu

  > [!NOTE]
  > Önceden oluşturulmuş kimlikler KIMLIK özgünlük 'i algılamıyor
  >
  > Form tanıyıcı önceden oluşturulmuş kimlikler, KIMLIK verilerinden önemli verileri ayıklar. Ancak, özgün kimlik belgesinin geçerliliğini veya gerçekliğini algılamaz.

## <a name="try-it-out"></a>Deneyin

Form tanıyıcı kimlikleri hizmetini denemek için çevrimiçi örnek UI aracına gidin:

> [!div class="nextstepaction"]
> [Önceden oluşturulmuş modelleri deneyin](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Giriş gereksinimleri

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Desteklenen KIMLIK türleri

* **Önceden oluşturulmuş kimlikler v 2.1-Önizleme. 3** Dünya çapındaki Passport ve ABD sürücüsünün lisanslarından önemli değerleri ayıklar.

  > [!NOTE]
  > KIMLIK türü desteği
  >
  > Şu anda desteklenen KIMLIK türleri, dünya çapındaki Passport ve ABD sürücüsünün lisanslarını içerir. KIMLIK desteğimizi dünyanın dört bir yanındaki diğer kimlik belgeleriyle genişletmeyi etkin bir şekilde aradık.

## <a name="post-analyze-id-document"></a>Analiz No belgelerini gönder

[Kimliği çözümle](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) işlemi, bir kimliğin GÖRÜNTÜSÜNÜ veya PDF 'sini girdi olarak alır ve ilgilendiğiniz değerleri ayıklar. Çağrı, adlı bir yanıt üst bilgisi alanı döndürüyor `Operation-Location` . `Operation-Location`Değer, bir sonraki adımda kullanılacak sonuç kimliğini içeren BIR URL 'dir.

|Yanıt üst bilgisi| Sonuç URL 'SI |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>Kimlik çözümleme belge sonucunu al

<!---
Need to update this with updated APIM links when available
-->

İkinci adım [**Get, ıddocument sonuçlarını al**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) işlemini çağırmalıdır. Bu işlem, KIMLIĞI çözümle işlemi tarafından oluşturulan sonuç KIMLIĞINI giriş olarak alır. Aşağıdaki olası değerlere sahip bir **durum** alanı IÇEREN bir JSON yanıtı döndürür. **Başarılı** değerle döndürülünceye kadar bu işlemi tekrarlayarak çağırın. Saniye başına istek (RPS) oranını aşmamak için 3 ile 5 saniye arasında bir Aralık kullanın.

|Alan| Tür | Olası değerler |
|:-----|:----:|:----|
|durum | string | notStarted: analiz işlemi başlatılmamış. |
| |  | çalışıyor: analiz işlemi devam ediyor. |
| |  | başarısız: çözümleme işlemi başarısız oldu. |
| |  | başarılı: çözümleme işlemi başarılı oldu. |

**Durum** alanı **başarılı** DEĞERINE sahip olduğunda, JSON yanıtı, makbuz anlama ve metin tanıma sonuçlarını içerir. Kimlikler sonucu, her bir değerin ayıklanan metin, normalleştirilmiş değer, sınırlama kutusu, güvenirlik ve karşılık gelen Word öğelerini içerdiği adlandırılmış alan değerlerinin bir sözlüğü olarak düzenlenir. Metin tanıma sonucu, metin, sınırlayıcı kutusu ve güvenle bilgi içeren bir satır ve sözcük hiyerarşisi olarak düzenlenir.

![örnek alma sonuçları](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Örnek JSON çıkışı

Başarılı bir JSON yanıtı örneğine bakın: `readResults` düğüm, tanınan tüm metni içerir. Metin sayfaya, sonra satıra, sonra da tek sözcüklere göre düzenlenir. `documentResults`Düğüm, modelin bulduğu kimlik değerlerini içerir. Bu düğüm, ad, soyadı, belge numarası ve daha fazlası gibi faydalı anahtar/değer çiftlerini de bulacağınız yerdir.

```json
{
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],

     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* Kendi kimlik ve örneklerinizi [form tanıyıcı örnek Kullanıcı arabiriminde](https://fott-preview.azurewebsites.net/)deneyin.
* Form tanıyıcı ile seçtiğiniz geliştirme dilinde bir KIMLIK işleme uygulaması yazmaya başlamak için [form tanıyıcı hızlı](quickstarts/client-library.md) başlangıcını doldurun.

## <a name="see-also"></a>Ayrıca bkz.

* [**Form Tanıma nedir?**](./overview.md)
* [**REST API başvuru belgeleri**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
