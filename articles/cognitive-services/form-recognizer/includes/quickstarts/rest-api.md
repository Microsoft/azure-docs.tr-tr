---
title: 'Hızlı başlangıç: form tanıyıcı REST API'
description: Özel belgelerinizden anahtar/değer çiftlerini ve tablo verilerini çıkaran bir form işleme uygulaması oluşturmak için REST API formu tanıyıcı ' i kullanın.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: cd9cf0e4a2abec1ad29d5b34f3d6f473ca5bf71d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564569"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> Bu kılavuz REST API çağrılarını yürütmek için kıvrımlı kullanır.

|[Form tanıyıcı REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) | [Azure REST API başvurusu](/rest/api/azure/)|

## <a name="prerequisites"></a>Önkoşullar

* [kıvrımlı](https://curl.haxx.se/windows/) yüklendi.
* [PowerShell sürüm 6.0 +](/powershell/scripting/install/installing-powershell-core-on-windows)veya benzer bir komut satırı uygulaması.
* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* Eğitim verileri kümesi içeren bir Azure Depolama Blobu. Eğitim veri kümesini birlikte yerleştirmeye yönelik ipuçları ve seçenekler için bkz. [özel bir model için eğitim verileri kümesi oluşturma](../../build-training-data-set.md) . Bu hızlı başlangıçta, [örnek veri kümesinin](https://go.microsoft.com/fwlink/?linkid=2090451) **eğitme** klasörü altındaki dosyaları ( *sample_data.zip* indir ve Ayıkla) kullanabilirsiniz.
* Azure aboneliğiniz olduktan sonra <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" bir form tanıyıcı kaynağı oluşturun "  target="_blank"> </a> Azure Portal anahtarınızı ve uç noktanızı almak Için bir form tanıyıcı kaynağı oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
  * Uygulamanızı form tanıyıcı API 'sine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
  * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.
* Bir makbuz görüntüsünün URL 'SI. Bu hızlı başlangıç için [örnek bir görüntü](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) kullanabilirsiniz.
* Bir iş kartının görüntüsünün URL 'SI. Bu hızlı başlangıç için [örnek bir görüntü](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) kullanabilirsiniz.
* Bir faturanın görüntüsünün URL 'SI. Bu hızlı başlangıç için bir [örnek belge](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) kullanabilirsiniz.

## <a name="analyze-layout"></a>Düzeni çözümle

Bir modeli eğmenize gerek kalmadan, belgelerde tablo, seçim işareti, metin ve yapıyı çözümlemek ve ayıklamak için form tanıyıcı kullanabilirsiniz. Düzen ayıklama hakkında daha fazla bilgi için bkz. [Düzen kavramsal Kılavuzu](../../concept-layout.md). Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{Endpoint}`Form tanıyıcı aboneliğiniz ile edindiğiniz uç noktayla değiştirin.
1. `{subscription key}`Önceki adımdan kopyaladığınız abonelik anahtarıyla değiştirin.
1. `\"{your-document-url}`Örnek URL 'lerden biriyle değiştirin.

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

---

`202 (Success)`Har **Işlem-konum** üst bilgisi içeren bir yanıt alacaksınız. Bu üstbilginin değeri, zaman uyumsuz işlemin durumunu sorgulamak ve sonuçları almak için kullanabileceğiniz bir işlem KIMLIĞI içerir. Aşağıdaki örnekte, sonraki dize `analyzeResults/` Işlem kimliğidir.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Düzen sonuçlarını al

**[Düzen çözümleme](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)** API 'sini çağırdıktan sonra, işlemin durumunu ve ayıklanan verileri almak Için **[düzenleme düzeni sonucunu al](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult)** API 'sini çağırın. Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{Endpoint}`Form tanıyıcı aboneliğiniz ile edindiğiniz uç noktayla değiştirin.
1. `{subscription key}`Önceki adımdan kopyaladığınız abonelik anahtarıyla değiştirin.
1. `{resultId}`Önceki adımdaki Işlem kimliğiyle değiştirin.
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-results"></a>Sonuçları inceleme

`200 (success)`JSON içeriğiyle bir yanıt alacaksınız.

Aşağıdaki fatura görüntüsüne ve buna karşılık gelen JSON çıktısına bakın.
* `"readResults"`Düğüm, sayfada ilgili sınırlayıcı kutusu yerleşimine sahip her metin satırını içerir.
* `"selectionMarks"`Düğüm (v 2.1 önizlemesi) her seçim işaretini (onay kutusu, radyo işareti) ve durumunun "seçili" veya "seçilmemiş" olup olmadığını gösterir.
* `"pageResults"`Bölümü ayıklanan tabloları içerir. Her tablo için metin, satır ve sütun dizini, satır ve sütun kapsayıcı, sınırlama kutusu ve daha fazlası ayıklanır.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Bir tablo içeren contoso proje beyanı belgesi.":::

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

Bu çıktı kolaylık sağlaması için kısaltıldı. [GitHub 'da tam örnek çıkışa](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)bakın.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

Bu çıktı kolaylık sağlaması için kısaltıldı. [GitHub 'da tam örnek çıkışa](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)bakın.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

---

## <a name="analyze-receipts"></a>Alındıları analiz etme

Bu bölümde, önceden eğitilen bir makbuz modeli kullanılarak ABD makbuzlarından ortak alanların nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. Makbuz analizi hakkında daha fazla bilgi için bkz. [alındılar kavramsal Kılavuzu](../../concept-receipts.md). Bir alındısı analizine başlamak için aşağıdaki kıvrımlı komutunu kullanarak, **[analiz alma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)** API 'sini çağırın. Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{Endpoint}`Form tanıyıcı aboneliğiniz ile edindiğiniz uç noktayla değiştirin.
1. `{your receipt URL}`Bir makbuz RESMININ URL adresiyle değiştirin.
1. `{subscription key>`Önceki adımdan kopyaladığınız abonelik anahtarıyla değiştirin.

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

---

`202 (Success)`Har **Işlem-konum** üst bilgisi içeren bir yanıt alacaksınız. Bu üstbilginin değeri, zaman uyumsuz işlemin durumunu sorgulamak ve sonuçları almak için kullanabileceğiniz bir işlem KIMLIĞI içerir. Aşağıdaki örnekte, sonraki dize `operations/` Işlem kimliğidir.

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>Makbuz sonuçlarını alma

**Çözümleme alındı** API 'sini çağırdıktan sonra, işlemin durumunu ve ayıklanan verileri almak Için **[çözümleme sonucu alma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult)** API 'sini çağırın. Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{Endpoint}`Form tanıyıcı abonelik anahtarınızla edindiğiniz uç noktayla değiştirin. Bunu, form tanıyıcı kaynağına **genel bakış** sekmesinde bulabilirsiniz.
1. `{operationId}`Önceki adımdaki Işlem kimliğiyle değiştirin.
1. `{subscription key}` değerini abonelik anahtarınızla değiştirin.

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-response"></a>Yanıtı inceleme

`200 (Success)`JSON çıkışıyla bir yanıt alacaksınız. İlk alan, `"status"` , işlemin durumunu gösterir. İşlem tamamlanmadıysa, değeri `"status"` `"running"` veya olur `"notStarted"` ve API 'yi el ile ya da bir komut dosyası aracılığıyla tekrar çağırmanız gerekir. Çağrılar arasında bir saniye veya daha fazla Aralık önerilir.

`"readResults"`Düğüm, tanınan metnin tamamını içerir (isteğe bağlı *ıncludetekxtdetails* parametresini olarak ayarlarsanız `true` ). Metin sayfaya, sonra satıra, sonra da tek sözcüklere göre düzenlenir. `"documentResults"`Düğüm, modelin bulduğu girişe özgü değerleri içerir. Burada, vergi, toplam, ticari adres vb. gibi faydalı anahtar/değer çiftleri bulacaksınız.

Aşağıdaki makbuz görüntüsüne ve buna karşılık gelen JSON çıktısına bakın.

![Contoso mağazasından alındı](../../media/contoso-allinone.jpg)

Bu çıktı okunabilirlik için kısaltıldı. [GitHub 'da tam örnek çıkışa](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json)bakın.

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="analyze-business-cards"></a>İş kartlarını çözümle

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

Bu bölümde, önceden eğitilen bir model kullanarak Ingilizce iş kartlarından ortak alanların nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. İş kartı analizi hakkında daha fazla bilgi için bkz. [iş kartları kavramsal Kılavuzu](../../concept-business-cards.md). Bir iş kartını çözümlemeye başlamak için aşağıdaki kıvrımlı komutunu kullanarak **[Iş kartını çözümle](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)** API 'sini çağırabilirsiniz. Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{Endpoint}`Form tanıyıcı aboneliğiniz ile edindiğiniz uç noktayla değiştirin.
1. `{your business card URL}`Bir makbuz RESMININ URL adresiyle değiştirin.
1. `{subscription key}`Önceki adımdan kopyaladığınız abonelik anahtarıyla değiştirin.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

`202 (Success)`Har **Işlem-konum** üst bilgisi içeren bir yanıt alacaksınız. Bu üstbilginin değeri, zaman uyumsuz işlemin durumunu sorgulamak ve sonuçları almak için kullanabileceğiniz bir işlem KIMLIĞI içerir.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>İş kartı sonuçlarını al

**Çözümle Iş kartı** API 'sini çağırdıktan sonra, işlemin durumunu ve ayıklanan verileri almak Için **[analiz Iş kartı sonucunu al](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeBusinessCardResult)** API 'sini çağırabilirsiniz. Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{Endpoint}`Form tanıyıcı abonelik anahtarınızla edindiğiniz uç noktayla değiştirin. Bunu, form tanıyıcı kaynağına **genel bakış** sekmesinde bulabilirsiniz.
1. `{resultId}`Önceki adımdaki Işlem kimliğiyle değiştirin.
1. `{subscription key}` değerini abonelik anahtarınızla değiştirin.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Yanıtı inceleme

`200 (Success)`JSON çıkışıyla bir yanıt alacaksınız.

`"readResults"`Düğüm, tüm tanınan metni içerir. Metin sayfaya, sonra satıra, sonra da tek sözcüklere göre düzenlenir. `"documentResults"`Düğüm, modelin bulduğu iş kartına özgü değerleri içerir. Burada şirket adı, ilk adı, soyadı, telefon numarası vb. gibi yararlı iletişim bilgileri bulabilirsiniz.

![Contoso şirketinden bir iş kartı](../../media/business-card-english.jpg)

Bu örnek JSON çıktısı okunabilirlik için kısaltıldı. [GitHub 'da tam örnek çıkışa](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)bakın.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
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
                                "confidence": 0.993
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
                    }
                }
            }
        ]
    }
}
```

Betiği, **Iş kartını çözümle** işlemi tamamlanana kadar konsola gönderilecek yanıtları yazdırır.

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Bu özellik seçili API sürümünde kullanılamaz.

---

## <a name="analyze-invoices"></a>Faturaları analiz etme

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

Bir faturayı çözümlemeye başlamak için aşağıdaki kıvrımlı komutunu kullanın. Fatura analizi hakkında daha fazla bilgi için bkz. [Fatura kavramsal Kılavuzu](../../concept-invoices.md). Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{Endpoint}`Form tanıyıcı aboneliğiniz ile edindiğiniz uç noktayla değiştirin.
1. `{your invoice URL}`Fatura BELGESININ URL adresiyle değiştirin.
1. `{subscription key}` değerini abonelik anahtarınızla değiştirin.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

`202 (Success)`Har **Işlem-konum** üst bilgisi içeren bir yanıt alacaksınız. Bu üstbilginin değeri, zaman uyumsuz işlemin durumunu sorgulamak ve sonuçları almak için kullanabileceğiniz bir işlem KIMLIĞI içerir.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Fatura sonuçlarını al

**[Faturayı çözümle](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)** API 'sini çağırdıktan sonra, işlemin durumunu ve ayıklanan verileri almak Için **[Fatura sonucunu al](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83)** API 'sini çağırın. Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{Endpoint}`Form tanıyıcı abonelik anahtarınızla edindiğiniz uç noktayla değiştirin. Bunu, form tanıyıcı kaynağına **genel bakış** sekmesinde bulabilirsiniz.
1. `{resultId}`Önceki adımdaki Işlem kimliğiyle değiştirin.
1. `{subscription key}` değerini abonelik anahtarınızla değiştirin.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Yanıtı inceleme

`200 (Success)`JSON çıkışıyla bir yanıt alacaksınız.

* `"readResults"`Alan, faturadan çıkarılan her metin satırını içerir.
* , `"pageResults"` Faturadan çıkarılan tabloları ve seçimler işaretlerini içerir.
* `"documentResults"`Alan, faturanın en ilgili bölümlerinin anahtar/değer bilgilerini içerir.

Aşağıdaki fatura belgesine ve buna karşılık gelen JSON çıktısına bakın.

* [Örnek fatura](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

Bu JSON içeriği okunabilirlik için kısaltıldı. [GitHub 'da tam örnek çıkışa](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json)bakın.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...
            }
        }]
    }
}
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Bu özellik seçili API sürümünde kullanılamaz.

---

## <a name="analyze-id-document"></a>KIMLIK belgesini çözümle

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

Bir tanımlama belgesini çözümlemeye başlamak için aşağıdaki kıvrımlı komutunu kullanın. Kimlik belgesi analizi hakkında daha fazla bilgi için bkz. [kimlik belgeleri kavramsal Kılavuzu](../../concept-identification-cards.md). Bir kimlik belgesini çözümlemeye başlamak için aşağıdaki kıvrımlı komutunu kullanarak **[kimlik belgesini çözümle] https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7738978e467c5fb8707)** API 'sini çağırın. Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{endpoint}`Form tanıyıcı aboneliğiniz ile edindiğiniz uç noktayla değiştirin.
1. `{your ID document URL}`Bir makbuz RESMININ URL adresiyle değiştirin.
1. `{subscription key}`Önceki adımdan kopyaladığınız abonelik anahtarıyla değiştirin.

### <a name="v21-previewtabv2-1"></a>[v 2.1 Önizleme] (#tab/v2-1

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your  ID document URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Bu özellik seçili API sürümünde kullanılamaz.

---

`202 (Success)` **İşlem konumu** üst bilgisi içeren bir yanıt alacaksınız. Bu üstbilginin değeri, zaman uyumsuz işlemin durumunu sorgulamak ve sonuçları almak için kullanabileceğiniz bir sonuç KIMLIĞI içerir. Aşağıdaki örnekte, sonraki dize `analyzeResults/` sonuç kimliğidir.

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/0c6cb19e-538f-4b8d-98b7-e105c9995ba6
```

### <a name="get-the-analyze-id-document-result"></a>KIMLIĞI çözümle belge sonucunu al

**Kimlik belgesini çözümle** API 'sini çağırdıktan sonra, işlemin durumunu ve ayıklanan verileri almak Için **[kodu çözümle kimlik belgesi sonucu](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822)** API 'sini çağırın.  Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{endpoint}`Form tanıyıcı abonelik anahtarınızla edindiğiniz uç noktayla değiştirin. Bunu, form tanıyıcı kaynağına **genel bakış** sekmesinde bulabilirsiniz.
1. `{resultId}`Önceki adımdaki Işlem kimliğiyle değiştirin.
1. `{subscription key}` değerini abonelik anahtarınızla değiştirin.

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Yanıtı inceleme

`200 (Success)`JSON çıkışıyla bir yanıt alacaksınız. İlk alan, `"status"` , işlemin durumunu gösterir. İşlem tamamlanmadıysa, değeri ya da olur `"status"` `"running"` ve siz `"notStarted"` değeri alıncaya kadar el ile veya bir komut dosyası aracılığıyla API 'yi yeniden çağırmanız gerekir `succeeded` . Çağrılar arasında bir saniye veya daha fazla Aralık önerilir.

* `"readResults"`Alan, kimlik belgesinden ayıklanan her metin satırını içerir.
* `"documentResults"`Alan, her biri giriş belgesinde algılanan BIR kimlik belgesini temsil eden bir nesne dizisi içerir.

Aşağıda örnek bir kimlik belgesi ve buna karşılık gelen JSON çıktısı verilmiştir

* :::image type="content" source="https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/id-license.jpg" alt-text="örnek sürücü lisansı":::

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-04-13T17:24:52Z",
    "lastUpdatedDateTime": "2021-04-13T17:24:55Z",
    "analyzeResult": {
      "version": "2.1.0",
      "readResults": [
        {
          "page": 1,
          "angle": -0.2823,
          "width": 450,
          "height": 294,
          "unit": "pixel"
        }
      ],
      "documentResults": [
        {
          "docType": "prebuilt:idDocument:driverLicense",
          "docTypeConfidence": 0.995,
          "pageRange": [
            1,
            1
          ],
          "fields": {
            "Address": {
              "type": "string",
              "valueString": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "text": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "boundingBox": [
                158,
                151,
                326,
                151,
                326,
                177,
                158,
                177
              ],
              "page": 1,
              "confidence": 0.965
            },
            "Country": {
              "type": "country",
              "valueCountry": "USA",
              "confidence": 0.99
            },
            "DateOfBirth": {
              "type": "date",
              "valueDate": "1958-01-06",
              "text": "01/06/1958",
              "boundingBox": [
                187,
                133,
                272,
                132,
                272,
                148,
                187,
                149
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DateOfExpiration": {
              "type": "date",
              "valueDate": "2020-08-12",
              "text": "08/12/2020",
              "boundingBox": [
                332,
                230,
                414,
                228,
                414,
                244,
                332,
                245
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DocumentNumber": {
              "type": "string",
              "valueString": "LICWDLACD5DG",
              "text": "LIC#WDLABCD456DG",
              "boundingBox": [
                162,
                70,
                307,
                68,
                307,
                84,
                163,
                85
              ],
              "page": 1,
              "confidence": 0.99
            },
            "FirstName": {
              "type": "string",
              "valueString": "LIAM R.",
              "text": "LIAM R.",
              "boundingBox": [
                158,
                102,
                216,
                102,
                216,
                116,
                158,
                116
              ],
              "page": 1,
              "confidence": 0.985
            },
            "LastName": {
              "type": "string",
              "valueString": "TALBOT",
              "text": "TALBOT",
              "boundingBox": [
                160,
                86,
                213,
                85,
                213,
                99,
                160,
                100
              ],
              "page": 1,
              "confidence": 0.987
            },
            "Region": {
              "type": "string",
              "valueString": "Washington",
              "confidence": 0.99
            },
            "Sex": {
              "type": "gender",
              "valueGender": "M",
              "text": "M",
              "boundingBox": [
                226,
                190,
                232,
                190,
                233,
                201,
                226,
                201
              ],
              "page": 1,
              "confidence": 0.99
            }
          }
        }
      ]
    }
  }
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Bu özellik seçili API sürümünde kullanılamaz.

---

## <a name="train-a-custom-model"></a>Özel bir modeli eğitme

Özel bir modeli eğitebilmeniz için bir Azure depolama blobunda eğitim verileri kümesine ihtiyacınız vardır. Aynı türde/yapıda en az beş adet doldurulmuş form (PDF belgesi ve/veya resim) gereklidir. Eğitim verilerinizi birlikte yerleştirmeye yönelik ipuçları ve seçenekler için bkz. [özel bir model için eğitim verileri kümesi oluşturma](../../build-training-data-set.md) .

Etiketli veriler olmadan eğitim varsayılan işlemdir ve daha basittir. Alternatif olarak, daha önce eğitim verilerinizin bazılarını veya tümünü el ile etiketleyebilirsiniz. Bu daha karmaşık bir işlemdir, ancak daha iyi eğitilen bir model ile sonuçlanır.

> [!NOTE]
> Modelleri, [form tanıyıcı örnek etiketleme aracı](../../quickstarts/label-tool.md)gibi bir grafik kullanıcı arabirimi ile de eğitebilirsiniz.


### <a name="train-a-model-without-labels"></a>Etiketler olmadan bir modeli eğitme

Azure Blob kabınızda bulunan belgelerle bir form tanıyıcı modeli eğitmek için aşağıdaki kıvrımlı komutunu çalıştırarak **[özel model eğitimi](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** API 'sini çağırın. Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{Endpoint}`Form tanıyıcı aboneliğiniz ile edindiğiniz uç noktayla değiştirin.
1. `{subscription key}`Önceki adımdan kopyaladığınız abonelik anahtarıyla değiştirin.
1. `{SAS URL}`Azure Blob depolama kapsayıcısının paylaşılan erişim imzası (SAS) URL 'si ile değiştirin. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 'SI alımı":::

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

---

`201 (Success)`Bir **konum** üst bilgisine sahip bir yanıt alacaksınız. Bu üstbilginin değeri, eğitilen yeni modelin KIMLIĞIDIR.

### <a name="train-a-model-with-labels"></a>Etiketler içeren bir modeli eğitme

Etiketlerle eğitebilmeniz için, `\<filename\>.pdf.labels.json` eğitim belgelerinin yanı sıra BLOB depolama kapsayıcıda özel etiket bilgi dosyalarına () sahip olmanız gerekir. [Form tanıyıcı örnek etiketleme aracı](../../quickstarts/label-tool.md) , bu etiket dosyalarını oluşturmanıza yardımcı olmak için bir kullanıcı arabirimi sağlar. Bunu yaptıktan sonra, JSON gövdesinde olarak ayarlanmış parametresi ile **[özel model API 'Sini eğitebilirsiniz](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** `"useLabelFile"` `true` .

Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{Endpoint}`Form tanıyıcı aboneliğiniz ile edindiğiniz uç noktayla değiştirin.
1. `{subscription key}`Önceki adımdan kopyaladığınız abonelik anahtarıyla değiştirin.
1. `{SAS URL}`Azure Blob depolama kapsayıcısının paylaşılan erişim imzası (SAS) URL 'si ile değiştirin. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 'SI alımı":::

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

---

`201 (Success)`Bir **konum** üst bilgisine sahip bir yanıt alacaksınız. Bu üstbilginin değeri, eğitilen yeni modelin KIMLIĞIDIR.

### <a name="get-training-results"></a>Eğitim sonuçlarını al

Eğitme işlemini başlattıktan sonra, eğitim durumunu denetlemek için yeni bir işlem kullanın, **[özel model alın](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** . Eğitim durumunu denetlemek için model KIMLIĞINI bu API çağrısına geçirin:

1. `{Endpoint}`Form tanıyıcı abonelik anahtarınızla edindiğiniz uç noktayla değiştirin.
1. `{subscription key}`Abonelik anahtarınızla değiştirin
1. `{model ID}`Önceki adımda aldığınız model kimliğiyle değiştirin

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

`200 (Success)`Aşağıdaki biçimde BIR JSON gövdesi ile yanıt alacaksınız. Alanına dikkat edin `"status"` . `"ready"`Eğitim tamamlandıktan sonra bu değer olacaktır. Modelde eğitim bitmiyor ise, komutu yeniden çalıştırarak hizmeti tekrar sorgulamanızı gerekir. Çağrılar arasında bir saniye veya daha fazla Aralık önerilir.

`"modelId"`Alan, eğitiminde olduğunuz MODELIN kimliğini içerir. Bu, bir sonraki adımda gerekli olacaktır.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Formları özel bir model ile analiz etme

Daha sonra, yeni eğitilen modelinizi kullanarak bir belgeyi analiz edebilir, anahtar-değer çiftlerini ve tabloları kümeden ayıklayın. Aşağıdaki kıvrımlı komutunu çalıştırarak **[formu çözümle](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)** API 'sini çağırın. Komutu çalıştırmadan önce Şu değişiklikleri yapın:

1. `{Endpoint}`Form tanıyıcı abonelik anahtarınızdan edindiğiniz uç noktayla değiştirin. Bunu, form tanıyıcı kaynağına **genel bakış** sekmesinde bulabilirsiniz.
1. `{model ID}`Önceki bölümde aldığınız model kimliğiyle değiştirin.
1. `{SAS URL}`Azure Storage 'daki dosyanız için BIR SAS URL 'si ile değiştirin. Eğitim bölümündeki adımları izleyin, ancak tüm blob kapsayıcısı için bir SAS URL 'SI almak yerine, çözümlemek istediğiniz belirli dosya için bir tane alın.
1. `{subscription key}` değerini abonelik anahtarınızla değiştirin.

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

---

`202 (Success)` **İşlem konumu** üst bilgisi olan bir yanıt alacaksınız. Bu üstbilginin değeri, Çözümle işleminin sonuçlarını izlemek için kullandığınız bir sonuç KIMLIĞI içerir. Sonraki adım için bu sonuç KIMLIĞINI kaydedin.

### <a name="get-the-analyze-results"></a>Analiz sonuçlarını al

Çözümle işleminin sonuçlarını sorgulamak için **[analiz formu sonucunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult)** al API 'sini çağırın.

1. `{Endpoint}`Form tanıyıcı abonelik anahtarınızdan edindiğiniz uç noktayla değiştirin. Bunu, form tanıyıcı kaynağına **genel bakış** sekmesinde bulabilirsiniz.
1. `{result ID}`Önceki bölümde ALDıĞıNıZ kimlik ile değiştirin.
1. `{subscription key}` değerini abonelik anahtarınızla değiştirin.

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

`200 (Success)`Aşağıdaki biçimde BIR JSON gövdesi ile yanıt alacaksınız. Çıktı basitlik için kısaltıldı. `"status"`En alttaki alana dikkat edin. `"succeeded"`Analiz işlemi tamamlandığında bu değer olacaktır. Çözümle işlemi tamamlanmadıysa, komutu yeniden çalıştırarak hizmeti tekrar sorgulamanızı gerekir. Çağrılar arasında bir saniye veya daha fazla Aralık önerilir.

Etiketler olmadan eğitilen özel modellerde, anahtar/değer çifti ilişkilendirmeleri ve tabloları `"pageResults"` JSON çıktısının düğümüdür. Etiketlerle eğitilen özel modellerde, anahtar/değer çifti ilişkilendirmeleri `"documentResults"` düğümüdür. Ayrıca, *ıncludetekxtdetails* URL parametresi aracılığıyla düz metin ayıklama belirttiyseniz, `"readResults"` düğüm belgedeki tüm metinlerin içeriğini ve konumlarını gösterir.

Bu örnek JSON çıktısı kolaylık sağlaması için kısaltıldı. [GitHub 'da tam örnek çıkışa](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json)bakın.

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          },
          ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ],
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ],
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          },
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ],
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```

---

### <a name="improve-results"></a>Sonuçları geliştirme

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>Özel modelleri yönetme

### <a name="get-a-list-of-custom-models"></a>Özel modellerin bir listesini alın

Aboneliğinize ait olan tüm özel modellerin bir listesini döndürmek için aşağıdaki komutta **[özel modeller](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModels)** API 'sini Listele ' i kullanın.

1. `{Endpoint}`Form tanıyıcı aboneliğiniz ile edindiğiniz uç noktayla değiştirin.
1. `{subscription key}`Önceki adımdan kopyaladığınız abonelik anahtarıyla değiştirin.

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

`200`Aşağıdaki gıbı JSON verileri ile başarılı bir yanıt alırsınız. `"modelList"`Öğesi, oluşturulan tüm modellerinizi ve bunların bilgilerini içerir.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>Belirli bir modeli al

Belirli bir özel model hakkında ayrıntılı bilgi almak için aşağıdaki komutta **[özel model al](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** API 'sini kullanın.

1. `{Endpoint}`Form tanıyıcı aboneliğiniz ile edindiğiniz uç noktayla değiştirin.
1. `{subscription key}`Önceki adımdan kopyaladığınız abonelik anahtarıyla değiştirin.
1. `{modelId}`Aramak istediğiniz özel MODELIN kimliğiyle değiştirin.

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

`200`Aşağıdaki gıbı JSON verileri ile başarılı bir yanıt alırsınız.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>Kaynak hesabındaki bir modeli silme

Ayrıca, KIMLIĞINE başvurarak hesabınızdan bir modeli silebilirsiniz. Bu komut, önceki bölümde kullanılan modeli silmek için **[özel model silme](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/DeleteCustomModel)** API 'sini çağırır.
kod
1. `{Endpoint}`Form tanıyıcı aboneliğiniz ile edindiğiniz uç noktayla değiştirin.
1. `{subscription key}`Önceki adımdan kopyaladığınız abonelik anahtarıyla değiştirin.
1. `{modelId}`Aramak istediğiniz özel MODELIN kimliğiyle değiştirin.

### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/v2-1)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

`204`Modelinizin silinmek üzere işaretlenip işaretlenmediğini belirten bir başarı yanıtı alacaksınız. Model yapıtları 48 saat içinde kaldırılacak.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, modelleri eğitmek ve formları farklı yollarla analiz etmek için REST API formunu kullandınız. Sonra, form tanıyıcı API 'sini daha ayrıntılı incelemek için başvuru belgelerine bakın.

> [!div class="nextstepaction"]
> [REST API başvuru belgeleri](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)

* [Form Tanıma nedir?](../../overview.md)
