---
title: 'Hızlı başlangıç: Python form tanıyıcısı kullanarak alındı verilerini ayıklama'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, satış alındıları görüntülerinden veri ayıklamak için Python ile REST API adlı form tanıyıcıyı kullanacaksınız.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: ef5c9e8d548e8acbcbdbe83f6e7c9965c798ad44
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931247"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Hızlı başlangıç: Python ile REST API form tanıyıcı kullanarak alındı verilerini ayıklama

Bu hızlı başlangıçta, satış girişlerinde ilgili bilgileri ayıklamak ve tanımlamak için Python ile Azure form tanıyıcı REST API kullanacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar
Bu hızlı başlangıcı tamamlayabilmeniz için şunları yapmanız gerekir:
- Form tanıyıcı sınırlı erişim önizlemesine erişim. Önizlemeye erişim sağlamak için [form tanıyıcı erişim isteği](https://aka.ms/FormRecognizerRequestAccess) formunu doldurun ve gönderebilirsiniz.
- [Python](https://www.python.org/downloads/) yüklendi (örneği yerel olarak çalıştırmak istiyorsanız).
- Bir makbuz görüntüsünün URL 'SI. Bu hızlı başlangıç için [örnek bir görüntü](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) kullanabilirsiniz.

## <a name="create-a-form-recognizer-resource"></a>Form tanıyıcı kaynağı oluşturma

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Okundu bilgisi Analizi

Bir alındısı analizine başlamak için aşağıdaki Python betiğini kullanarak **Çözümleme alındı** API 'sini çağırabilirsiniz. Betiği çalıştırmadan önce Şu değişiklikleri yapın:

1. `<Endpoint>`, form tanıyıcı aboneliğinizle edindiğiniz uç noktayla değiştirin.
1. `<your receipt URL>`, bir makbuz resminin URL adresiyle değiştirin.
1. `<subscription key>`, önceki adımdan kopyaladığınız abonelik anahtarıyla değiştirin.

    ```python
    import http.client, urllib.request, urllib.parse, urllib.error, base64

    source = r"<your receipt URL>"
    body = {"url":source}
    body = json.dumps(body)

    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        conn = http.client.HTTPSConnection('<Endpoint>')
        conn.request("POST", "/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze", body, headers)
        response = conn.getresponse()
        data = response.read()
        operationURL = "" + response.getheader("Operation-Location")
        print ("Operation-Location header: " + operationURL)
        conn.close()
    except Exception as e:
        print(e)
        exit()
    ```

1. Kodu. Kopyala uzantılı bir dosyaya kaydedin. Örneğin, *form-recognizer-receipts.py*.
1. Bir komut istemi penceresi açın.
1. İstemde, örneği çalıştırmak için `python` komutunu kullanın. Örneğin, `python form-recognizer-receipts.py`.

Komut dosyasının konsola yazdırılacağı bir **Işlem konumu** üst bilgisi içeren `202 (Success)` yanıtını alırsınız. Bu üst bilgi, işlemin durumunu sorgulamak ve analiz sonuçlarını almak için kullanabileceğiniz bir işlem KIMLIĞI içerir. Aşağıdaki örnek değerde, `operations/` sonraki dize işlem KIMLIĞIDIR.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Makbuz sonuçlarını alma

**Çözümleme alındı** API 'sini çağırdıktan sonra, işlemin durumunu ve ayıklanan verileri almak Için **alma sonucu** API 'sini çağırın. Aşağıdaki kodu Python betiğinizin altına ekleyin. Bu işlem KIMLIĞI değerini ayıklar ve yeni bir API çağrısına geçirir. İşlem zaman uyumsuzdur, bu nedenle, sonuçlar kullanılabilir olana kadar bu betik API 'YI düzenli aralıklarla çağırır. Bir saniye veya daha fazla Aralık öneririz.

```python
operationId = operationURL.split("operations/")[1]

conn = http.client.HTTPSConnection('<Endpoint>')
while True:
    try:
        conn.request("GET", f"/formrecognizer/v1.0-preview/prebuilt/receipt/operations/{operationId}", "", headers)
        responseString = conn.getresponse().read().decode('utf-8')
        responseDict = json.loads(responseString)
        conn.close()
        print(responseString)
        if 'status' in responseDict and responseDict['status'] not in ['NotStarted','Running']:
            break
        time.sleep(1)
    except Exception as e:
        print(e)
        exit()
```

1. Betiği kaydedin.
1. Örneği çalıştırmak için `python` komutunu yeniden kullanın. Örneğin, `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Yanıtı inceleme

Betik, Çözümle işlemi tamamlanana kadar yanıtları konsola yazdıracaktır. Daha sonra, ayıklanan metin verilerini JSON biçiminde yazdıracaktır. `"recognitionResults"` alanı, alış irsaliyesinden ayıklanan her metin satırını içerir ve `"understandingResults"` alanı, girişin en ilgili bölümleri için anahtar/değer bilgilerini içerir.

Aşağıdaki makbuz görüntüsüne ve buna karşılık gelen JSON çıktısına bakın. Çıktı okunabilirlik için kısaltıldı.

![Contoso mağazasından alındı](../media/contoso-receipt.png)

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir modeli eğitme ve örnek bir senaryoda çalıştırmak için Python ile birlikte REST API form tanıyıcıyı kullandınız. Sonra, form tanıyıcı API 'sini daha ayrıntılı incelemek için başvuru belgelerine bakın.

> [!div class="nextstepaction"]
> [REST API başvuru belgeleri](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
