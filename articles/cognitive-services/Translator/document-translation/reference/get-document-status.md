---
title: Belge durumunu Al yöntemi
titleSuffix: Azure Cognitive Services
description: Belge durumunu Al yöntemi, belirli bir belgenin durumunu döndürür.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 4c6e82af46a012ad53dfa1cc1db1252ef2c0443e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864946"
---
# <a name="get-document-status"></a>Belge durumunu al

Belge durumunu Al yöntemi, belirli bir belgenin durumunu döndürür. Yöntemi, istek KIMLIĞI ve belge KIMLIĞI temelinde belirli bir belge için çeviri durumunu döndürür.

## <a name="request-url"></a>İstek URL’si

Şu kişiye bir `GET` istek gönder:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents/{documentId}
```

[Özel etki alanı adınızı](../get-started-with-document-translation.md#find-your-custom-domain-name)bulmayı öğrenin.

> [!IMPORTANT]
>
> * **Belge çevirisi hizmetine yönelik tüm API istekleri özel bir etki alanı uç noktası gerektirir**.
> * Belge çevirisi için HTTP istekleri yapmak üzere Azure portal kaynak _anahtarlarınız ve uç_ nokta sayfanızda veya küresel çevirmen uç noktasında bulunan uç noktayı kullanamazsınız `api.cognitive.microsofttranslator.com` .

## <a name="request-parameters"></a>İstek parametreleri

Sorgu dizesine geçirilen istek parametreleri şunlardır:

|Sorgu parametresi|Gerekli|Açıklama|
|--- |--- |--- |
|DocumentID değeri bulunmuyor|Doğru|Belge KIMLIĞI.|
|kimlik|Doğru|Toplu iş KIMLIĞI.|
## <a name="request-headers"></a>İstek üst bilgileri

İstek üst bilgileri:

|Üst Bilgiler|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Gerekli istek üst bilgisi|

## <a name="response-status-codes"></a>Yanıt durum kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir.

|Durum Kodu|Description|
|--- |--- |
|200|Tamam ögesini seçin. Başarılı istek ve hizmet tarafından kabul edildi. İşlem ayrıntıları döndürülür. HeadersRetry-After: ıntegeretag: String|
|401|Erişilmesini. Kimlik bilgilerinizi denetleyin.|
|404|Bulunamadı. Kaynak bulunamadı.|
|500|İç sunucu hatası.|
|Diğer durum kodları|<ul><li>Çok fazla istek</li><li>Sunucu geçici olarak kullanılamıyor</li></ul>|

## <a name="get-document-status-response"></a>Belge durumu yanıtını al

### <a name="successful-get-document-status-response"></a>Başarılı belge durumu yanıtı al

|Ad|Tür|Description|
|--- |--- |--- |
|path|string|Belge veya klasörün konumu.|
|createdDateTimeUtc|string|İşlem Oluşturulma tarihi.|
|lastActionDateTimeUtc|string|İşlemin durumunun güncelleştirildiği tarih ve saat.|
|durum|Dize|İş veya belge için olası durumların listesi: <ul><li>İptal edildi</li><li>Hoparlör</li><li>Başarısız</li><li>NotStarted</li><li>Çalışma</li><li>Başarılı</li><li>ValidationFailed</li></ul>|
|kullanıcısı|string|Dil Için iki harfli dil kodu. Dillerin listesine bakın.|
|progress|sayı|Varsa çevirinin ilerlemesi|
|kimlik|string|Belge KIMLIĞI.|
|uygulanan karakter|tamsayı|API tarafından ücretlendirilen karakterler.|

### <a name="error-response"></a>Hata yanıtı

|Ad|Tür|Description|
|--- |--- |--- |
|kod|string|Üst düzey hata kodlarını içeren Numaralandırmalar. Olası değerler:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>Invalidrequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Yetkisiz</li></ul>|
|message|string|Üst düzey hata iletisini alır.|
|ınnererror|InnerErrorV2|Bilişsel hizmetler API yönergelerine uyan yeni bir Iç hata biçimi. Gerekli özellikleri hata kodu, ileti ve isteğe bağlı özellikler hedefi, ayrıntılar (anahtar değeri çifti), iç hata (iç içe olabilir) içerir.|
|ınnererror. Code|string|Kod hata dizesini alır.|
|ınnererror. Message|string|Üst düzey hata iletisini alır.|

## <a name="examples"></a>Örnekler

### <a name="example-successful-response"></a>Örnek başarılı yanıt
Aşağıdaki JSON nesnesi, başarılı bir yanıt örneğidir.

```JSON
{
  "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Running",
  "to": "fr",
  "progress": 0.1,
  "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
  "characterCharged": 0
}
```

### <a name="example-error-response"></a>Örnek hata yanıtı

Aşağıdaki JSON nesnesi bir hata yanıtı örneğidir. Diğer hata kodlarının şeması aynı.

Durum kodu: 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Belge çevirisi ve istemci kitaplığı kullanma hakkında daha fazla bilgi edinmek için hızlı başlangıçlarımızı izleyin.

> [!div class="nextstepaction"]
> [Belge çevirisi ile çalışmaya başlama](../get-started-with-document-translation.md)
