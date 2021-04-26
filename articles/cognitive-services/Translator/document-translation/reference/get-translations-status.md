---
title: Çeviri durumunu al
titleSuffix: Azure Cognitive Services
description: Çevirileri al durumu yöntemi, gönderilen Batch isteklerinin bir listesini ve her isteğin durumunu döndürür.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: c3301283f0a7334a7c207ff7c80b4f71a13de465
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864840"
---
# <a name="get-translations-status"></a>Çeviri durumunu al

Çevirileri al durumu yöntemi, gönderilen Batch isteklerinin bir listesini ve her isteğin durumunu döndürür. Bu liste yalnızca Kullanıcı tarafından gönderilen toplu istekleri içerir (aboneliğe göre). Her isteğin durumu kimliğe göre sıralanır.

İstek sayısı, sayfalama sınırınızı aşarsa, sunucu tarafı sayfalama kullanılır. Sayfalandırılmış yanıtlar kısmi bir sonuç gösterir ve yanıta bir devamlılık belirteci ekler. Devamlılık belirteci yokluğu, kullanılabilir ek sayfalar olmadığı anlamına gelir.

$top ve $skip sorgu parametreleri, döndürülecek sonuç sayısını ve koleksiyon için bir sapmayı belirtmek için kullanılabilir.

Sunucu, istemci tarafından belirtilen değerleri geliştirir. Ancak, istemcilerin farklı bir sayfa boyutu veya devamlılık belirteci içeren yanıtları işleyecek şekilde hazırlanmalıdır.

Hem $top hem de $skip dahil edildiğinde, sunucu önce $skip ve sonra koleksiyonda $top. 

> [!NOTE]
> Sunucu $top ve/veya $skip karşılayamaz, sunucu yalnızca sorgu seçeneklerini yoksaymak yerine istemciye bir hata döndürmelidir. Bu, istemcinin döndürülen veriler hakkında varsayımlar yapma riskini azaltır.

## <a name="request-url"></a>İstek URL’si

Şu kişiye bir `GET` istek gönder:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
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
|$skip|Yanlış|Koleksiyondaki $skip girdileri atlayın. $Top ve $skip her ikisi de sağlandığında $skip önce uygulanır.|
|$top|Yanlış|Koleksiyondaki $top girdileri alın. $Top ve $skip her ikisi de sağlandığında $skip önce uygulanır.|

## <a name="request-headers"></a>İstek üst bilgileri

İstek üst bilgileri:

|Üst Bilgiler|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Gerekli istek üst bilgisi|

## <a name="response-status-codes"></a>Yanıt durum kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir.

|Durum Kodu|Description|
|--- |--- |
|200|Tamam ögesini seçin. Başarılı istek ve tüm işlemlerin durumunu döndürür. HeadersRetry-After: ıntegeretag: String|
|400|Hatalı Istek. Geçersiz istek. Giriş parametrelerini denetleyin.|
|401|Erişilmesini. Kimlik bilgilerinizi denetleyin.|
|500|İç sunucu hatası.|
|Diğer durum kodları|<ul><li>Çok fazla istek</li><li>Sunucu geçici olarak kullanılamıyor</li></ul>|

## <a name="get-translations-status-response"></a>Çeviri durumu yanıtını al

### <a name="successful-get-translations-status-response"></a>Başarılı çevirileri al durum yanıtı

Aşağıdaki bilgiler başarılı bir yanıtta döndürülür.

|Ad|Tür|Description|
|--- |--- |--- |
|kimlik|string|İşlemin KIMLIĞI.|
|createdDateTimeUtc|string|İşlem Oluşturulma tarihi.|
|lastActionDateTimeUtc|string|İşlemin durumunun güncelleştirildiği tarih ve saat.|
|durum|Dize|İş veya belge için olası durumların listesi: <ul><li>İptal edildi</li><li>Hoparlör</li><li>Başarısız</li><li>NotStarted</li><li>Çalışma</li><li>Başarılı</li><li>ValidationFailed</li></ul>|
|Özet|StatusSummary []|Aşağıda listelenen ayrıntıları içeren Özet.|
|Özet. Toplam|tamsayı|Toplam belge sayısı.|
|Özet. başarısız|tamsayı|Belge sayısı başarısız oldu.|
|Özet. başarılı|tamsayı|Başarıyla çevrilen belge sayısı.|
|Summary. InProgress|tamsayı|Sürmekte olan belge sayısı.|
|Summary. notYetStarted|tamsayı|Henüz işleme başlamamış belge sayısı.|
|Özet. iptal edildi|tamsayı|İptal edilen belge sayısı.|
|Summary. Totalkarakterücret|tamsayı|Ücretlendirilen toplam karakter sayısı.|

### <a name="error-response"></a>Hata yanıtı

|Ad|Tür|Description|
|--- |--- |--- |
|kod|string|Üst düzey hata kodlarını içeren Numaralandırmalar. Olası değerler:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>Invalidrequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Yetkisiz</li></ul>|
|message|string|Üst düzey hata iletisini alır.|
|hedef|string|Hatanın kaynağını alır. Örneğin, geçersiz bir belge söz konusu olduğunda "belgeler" veya "belge kimliği" olacaktır.|
|ınnererror|InnerErrorV2|Bilişsel hizmetler API yönergelerine uyan yeni bir Iç hata biçimi. Gerekli özellikler hata kodu, ileti ve isteğe bağlı özellikler hedefi, ayrıntılar (anahtar değeri çifti), iç hata (Bu iç içe olabilir) içeriyor.|
|ınnererror. Code|string|Kod hata dizesini alır.|
|ınnererror. Message|string|Üst düzey hata iletisini alır.|

## <a name="examples"></a>Örnekler

### <a name="example-successful-response"></a>Örnek başarılı yanıt

Başarılı bir yanıt örneği aşağıda verilmiştir.

```JSON
{
  "value": [
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
    "target": "Operation",
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
