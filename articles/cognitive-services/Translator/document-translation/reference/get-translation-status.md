---
title: Çeviri durumunu al
titleSuffix: Azure Cognitive Services
description: Çeviri durumunu Al yöntemi bir belge çevirisi isteğinin durumunu döndürür.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: c17cd6cb97aaac3cd08342270e4eafa07eb5fd7d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864892"
---
# <a name="get-translation-status"></a>Çeviri durumunu al

Çeviri durumunu Al yöntemi bir belge çevirisi isteğinin durumunu döndürür. Durum, isteğin bir parçası olarak çevrilmekte olan belgelerin genel istek durumunu ve durumunu içerir.

## <a name="request-url"></a>İstek URL’si

Şu kişiye bir `GET` istek gönder:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
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
|kimlik|Doğru|İşlem KIMLIĞI.|

## <a name="request-headers"></a>İstek üst bilgileri

İstek üst bilgileri:

|Üst Bilgiler|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Gerekli istek üst bilgisi|

## <a name="response-status-codes"></a>Yanıt durum kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir.

|Durum Kodu|Description|
|--- |--- |
|200|Tamam ögesini seçin. Başarılı istek ve toplu iş çevirisi işleminin durumunu döndürür. HeadersRetry-After: ıntegeretag: String|
|401|Erişilmesini. Kimlik bilgilerinizi denetleyin.|
|404|Kaynak bulunamadı.|
|500|İç sunucu hatası.|
|Diğer durum kodları|<ul><li>Çok fazla istek</li><li>Sunucu geçici olarak kullanılamıyor</li></ul>|

## <a name="get-translation-status-response"></a>Çeviri durumu yanıtını al

### <a name="successful-get-translation-status-response"></a>Başarılı çeviri durumu yanıtı al

Aşağıdaki bilgiler başarılı bir yanıtta döndürülür.

|Ad|Tür|Description|
|--- |--- |--- |
|kimlik|string|İşlemin KIMLIĞI.|
|createdDateTimeUtc|string|İşlem Oluşturulma tarihi.|
|lastActionDateTimeUtc|string|İşlemin durumunun güncelleştirildiği tarih ve saat.|
|durum|Dize|İş veya belge için olası durumların listesi: <ul><li>İptal edildi</li><li>Hoparlör</li><li>Başarısız</li><li>NotStarted</li><li>Çalışma</li><li>Başarılı</li><li>ValidationFailed</li></ul>|
|Özet|StatusSummary|Aşağıda listelenen ayrıntıları içeren Özet.|
|Özet. Toplam|tamsayı|Toplam miktar.|
|Özet. başarısız|tamsayı|Başarısız sayısı.|
|Özet. başarılı|tamsayı|Başarılı sayısı.|
|Summary. InProgress|tamsayı|Devam eden sayı.|
|Summary. notYetStarted|tamsayı|Henüz başlatılmamış olan sayı.|
|Özet. iptal edildi|tamsayı|İptal edilen sayısı.|
|Summary. Totalkarakterücret|tamsayı|API tarafından ücretlendirilen toplam karakter sayısı.|

###<a name="error-response"></a>Hata yanıtı

|Ad|Tür|Description|
|--- |--- |--- |
|kod|string|Üst düzey hata kodlarını içeren Numaralandırmalar. Olası değerler:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>Invalidrequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Yetkisiz</li></ul>|
|message|string|Üst düzey hata iletisini alır.|
|hedef|string|Hatanın kaynağını alır. Örneğin, geçersiz bir belge için "belgeler" veya "belge kimliği" olacaktır.|
|ınnererror|InnerErrorV2|Bilişsel hizmetler API yönergelerine uyan yeni bir Iç hata biçimi. Gerekli özellikleri hata kodu, ileti ve isteğe bağlı özellikler hedefi, ayrıntılar (anahtar değeri çifti), iç hata (iç içe olabilir) içerir.|
|ınnererror. Code|string|Kod hata dizesini alır.|
|ınnererror. Message|string|Üst düzey hata iletisini alır.|

## <a name="examples"></a>Örnekler

### <a name="example-successful-response"></a>Örnek başarılı yanıt

Aşağıdaki JSON nesnesi, başarılı bir yanıt örneğidir.

```JSON
{
  "id": "727bf148-f327-47a0-9481-abae6362f11e",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Succeeded",
  "summary": {
    "total": 10,
    "failed": 1,
    "success": 9,
    "inProgress": 0,
    "notYetStarted": 0,
    "cancelled": 0,
    "totalCharacterCharged": 0
  }
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
