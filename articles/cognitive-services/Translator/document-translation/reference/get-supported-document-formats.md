---
title: Desteklenen belge biçimlerini al yöntemi
titleSuffix: Azure Cognitive Services
description: Desteklenen belge biçimlerini al yöntemi, desteklenen belge biçimlerinin bir listesini döndürür.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: e47f3363a9e09a3e371c751e0bdd1143cfc72314
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864910"
---
# <a name="get-supported-document-formats"></a>Desteklenen belge biçimlerini al

Desteklenen belge biçimlerini al yöntemi, belge çevirisi hizmeti tarafından desteklenen belge biçimlerinin bir listesini döndürür. Liste, ortak dosya uzantısını ve karşıya yükleme API 'SI kullanılıyorsa içerik türünü içerir.

## <a name="request-url"></a>İstek URL’si

Şu kişiye bir `GET` istek gönder:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/documents/formats
```

[Özel etki alanı adınızı](../get-started-with-document-translation.md#find-your-custom-domain-name)bulmayı öğrenin.

> [!IMPORTANT]
>
> * **Belge çevirisi hizmetine yönelik tüm API istekleri özel bir etki alanı uç noktası gerektirir**.
> * Belge çevirisi için HTTP istekleri yapmak üzere Azure portal kaynak _anahtarlarınız ve uç_ nokta sayfanızda veya küresel çevirmen uç noktasında bulunan uç noktayı kullanamazsınız `api.cognitive.microsofttranslator.com` .

## <a name="request-headers"></a>İstek üst bilgileri

İstek üst bilgileri:

|Üst Bilgiler|Description|
|-----|-----|
|Ocp-Apim-Subscription-Key|Gerekli istek üst bilgisi|

## <a name="response-status-codes"></a>Yanıt durum kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir.

|Durum Kodu|Description|
|-----|-----|
|200|Tamam ögesini seçin. Desteklenen belge dosyası biçimlerinin listesini döndürür.|
|500|İç sunucu hatası.|
|Diğer durum kodları|<ul><li>Çok fazla istek</li><li>Sunucu geçici olarak kullanılamıyor</li></ul>|

## <a name="file-format-response"></a>Dosya biçimi yanıtı

### <a name="successful-fileformatlistresult-response"></a>Başarılı fileFormatListResult yanıtı

Aşağıdaki bilgiler başarılı bir yanıtta döndürülür.

|Ad|Tür|Description|
|--- |--- |--- |
|değer|FileFormat []|FileFormat [] aşağıda listelenen ayrıntıları içerir.|
|değer. Format|String []|Bu biçim için desteklenen Içerik türleri.|
|value. fileExtensions|String []|Bu biçim için desteklenen dosya uzantısı.|
|value. contentTypes|String []|Biçimin adı.|
|değer. sürümler|String []|Desteklenen sürüm.|

### <a name="error-response"></a>Hata yanıtı

|Ad|Tür|Description|
|--- |--- |--- |
 |kod|string|Üst düzey hata kodlarını içeren Numaralandırmalar. Olası değerler:<ul><li>InternalServerError</li><li>InvalidArgument</li><li>Invalidrequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Yetkisiz</li></ul>|
|message|string|Üst düzey hata iletisini alır.|
|ınnererror|InnerErrorV2|Bilişsel hizmetler API yönergelerine uyan yeni bir Iç hata biçimi. Gerekli özellikler hata kodu, ileti ve isteğe bağlı özellikler hedefi, ayrıntılar (anahtar değeri çifti), iç hata (Bu iç içe olabilir) içeriyor.|
|ınnererror. Code|string|Kod hata dizesini alır.|
|ınnererror. Message|string|Üst düzey hata iletisini alır.|

## <a name="examples"></a>Örnekler

### <a name="example-successful-response"></a>Örnek başarılı yanıt
Başarılı bir yanıt örneği aşağıda verilmiştir.

Durum kodu: 200

```JSON
{
  "value": [
    {
      "format": "PlainText",
      "fileExtensions": [
        ".txt"
      ],
      "contentTypes": [
        "text/plain"
      ],
      "versions": []
    },
    {
      "format": "PortableDocumentFormat",
      "fileExtensions": [
        ".pdf"
      ],
      "contentTypes": [
        "application/pdf"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlPresentation",
      "fileExtensions": [
        ".pptx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.presentationml.presentation"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlSpreadsheet",
      "fileExtensions": [
        ".xlsx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
      ],
      "versions": []
    },
    {
      "format": "OutlookMailMessage",
      "fileExtensions": [
        ".msg"
      ],
      "contentTypes": [
        "application/vnd.ms-outlook"
      ],
      "versions": []
    },
    {
      "format": "HtmlFile",
      "fileExtensions": [
        ".html"
      ],
      "contentTypes": [
        "text/html"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlWord",
      "fileExtensions": [
        ".docx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
      ],
      "versions": []
    }
  ]
}
```

### <a name="example-error-response"></a>Örnek hata yanıtı

Aşağıda bir hata yanıtı örneği verilmiştir. Diğer hata kodlarının şeması aynı.

Durum kodu: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Belge çevirisi ve istemci kitaplığı kullanma hakkında daha fazla bilgi edinmek için hızlı başlangıçlarımızı izleyin.

> [!div class="nextstepaction"]
> [Belge çevirisi ile çalışmaya başlama](../get-started-with-document-translation.md)