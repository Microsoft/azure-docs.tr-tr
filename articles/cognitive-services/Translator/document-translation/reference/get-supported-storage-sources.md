---
title: Desteklenen depolama kaynakları yöntemini Al
titleSuffix: Azure Cognitive Services
description: Desteklenen depolama kaynaklarını al yöntemi, desteklenen depolama kaynaklarının bir listesini döndürür.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 03203206da6ae3ea9a1174aebafda0b58e22ea41
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864873"
---
# <a name="get-supported-storage-sources"></a>Desteklenen depolama kaynaklarını al

Desteklenen depolama kaynaklarını al yöntemi, belge çevirisi hizmeti tarafından desteklenen depolama kaynaklarının/seçeneklerinin bir listesini döndürür.

## <a name="request-url"></a>İstek URL’si

Şu kişiye bir `GET` istek gönder:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/storagesources
```

[Özel etki alanı adınızı](../get-started-with-document-translation.md#find-your-custom-domain-name)bulmayı öğrenin.

> [!IMPORTANT]
>
> * **Belge çevirisi hizmetine yönelik tüm API istekleri özel bir etki alanı uç noktası gerektirir**.
> * Belge çevirisi için HTTP istekleri yapmak üzere Azure portal kaynak _anahtarlarınız ve uç_ nokta sayfanızda veya küresel çevirmen uç noktasında bulunan uç noktayı kullanamazsınız `api.cognitive.microsofttranslator.com` .

## <a name="request-headers"></a>İstek üst bilgileri

İstek üst bilgileri:

|Üst Bilgiler|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Gerekli istek üst bilgisi|

## <a name="response-status-codes"></a>Yanıt durum kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir.

|Durum Kodu|Description|
|--- |--- |
|200|Tamam ögesini seçin. Başarılı istek ve depolama kaynaklarının listesini döndürür.|
|500|İç sunucu hatası.|
|Diğer durum kodları|<ul><li>Çok fazla istek</li><li>Sunucu geçici olarak kullanılamıyor</li></ul>|

## <a name="get-supported-storage-sources-response"></a>Desteklenen depolama kaynakları yanıtını al

### <a name="successful-get-supported-storage-sources-response"></a>Başarılı bir şekilde desteklenen depolama kaynakları yanıtı al
Liste için temel tür desteklenen depolama kaynaklarını al API 'sine dönün.

|Ad|Tür|Description|
|--- |--- |--- |
|değer|String []|Nesnelerin listesi.|


### <a name="error-response"></a>Hata yanıtı

|Ad|Tür|Description|
|--- |--- |--- |
|kod|string|Üst düzey hata kodlarını içeren Numaralandırmalar. Olası değerler:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>Invalidrequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Yetkisiz</li></ul>|
|message|string|Üst düzey hata iletisini alır.|
|ınnererror|InnerErrorV2|Bilişsel hizmetler API yönergelerine uyan yeni bir Iç hata biçimi. Gerekli özellikler hata kodu, ileti ve isteğe bağlı özellikler hedefi, ayrıntılar (anahtar değeri çifti), iç hata (Bu iç içe olabilir) içeriyor.|
|ınnererror. Code|string|Kod hata dizesini alır.|
|ınnererror. Message|string|Üst düzey hata iletisini alır.|

## <a name="examples"></a>Örnekler

### <a name="example-successful-response"></a>Örnek başarılı yanıt

Başarılı bir yanıt örneği aşağıda verilmiştir.

```JSON
{
  "value": [
    "AzureBlob"
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