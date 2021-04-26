---
title: Belge durumunu al
titleSuffix: Azure Cognitive Services
description: Belge durumunu Al yöntemi, toplu belge çevirisi isteğindeki tüm belgelerin durumunu döndürür.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 8476c4891cef9d9055b16c7ac574e569ecf5b3f2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864876"
---
# <a name="get-documents-status"></a>Belge durumunu al

Belge durumunu Al yöntemi, toplu belge çevirisi isteğindeki tüm belgelerin durumunu döndürür.

Yanıtta yer alan belgeler, belge KIMLIĞINE göre azalan düzende sıralanır. Yanıttaki belge sayısı, sayfalama sınırınızı aşarsa, sunucu tarafı sayfalama kullanılır. Sayfalandırılmış yanıtlar kısmi bir sonuç gösterir ve yanıta bir devamlılık belirteci ekler. Devamlılık belirteci yokluğu, kullanılabilir ek sayfalar olmadığı anlamına gelir.

$top ve $skip sorgu parametreleri, döndürülecek sonuç sayısını ve koleksiyon için bir sapmayı belirtmek için kullanılabilir. Sunucu, istemci tarafından belirtilen değerleri geliştirir. Ancak, istemcilerin farklı bir sayfa boyutu veya devamlılık belirteci içeren yanıtları işleyecek şekilde hazırlanmalıdır.

Hem $top hem de $skip dahil edildiğinde, sunucu önce $skip ve sonra koleksiyonda $top.

> [!NOTE]
> Sunucu $top ve/veya $skip karşılayamaz, sunucu yalnızca sorgu seçeneklerini yoksaymak yerine istemciye bir hata döndürmelidir. Bu, istemcinin döndürülen veriler hakkında varsayımlar yapma riskini azaltır.

## <a name="request-url"></a>İstek URL’si

Şu kişiye bir `GET` istek gönder:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
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
|200|Tamam ögesini seçin. Başarılı istek ve belgelerin durumunu döndürür. HeadersRetry-After: ıntegeretag: String|
|400|Geçersiz istek. Giriş parametrelerini denetleyin.|
|401|Erişilmesini. Kimlik bilgilerinizi denetleyin.|
|404|Kaynak bulunamadı.|
|500|İç sunucu hatası.|
|Diğer durum kodları|<ul><li>Çok fazla istek</li><li>Sunucu geçici olarak kullanılamıyor</li></ul>|


## <a name="get-documents-status-response"></a>Belge durumu yanıtını al

### <a name="successful-get-documents-status-response"></a>Başarılı belge al durum yanıtı

Aşağıdaki bilgiler başarılı bir yanıtta döndürülür.

|Ad|Tür|Description|
|--- |--- |--- |
|@nextLink|dize|Sonraki sayfanın URL 'si. Kullanılabilir daha fazla sayfa yoksa null.|
|değer|DocumentStatusDetail []|Aşağıda listelenen tek tek belgelerin ayrıntı durumu.|
|değer. yol|string|Belge veya klasörün konumu.|
|value. createdDateTimeUtc|string|İşlem Oluşturulma tarihi.|
|değer. lastActionDateTimeUt|string|İşlemin durumunun güncelleştirildiği tarih ve saat.|
|değer. durum|durum|İş veya belge için olası durumların listesi.<ul><li>İptal edildi</li><li>Hoparlör</li><li>Başarısız</li><li>NotStarted</li><li>Çalışma</li><li>Başarılı</li><li>ValidationFailed</li></ul>|
|value.to|string|Dile.|
|değer. Progress|string|Varsa, çeviri ilerleme durumu.|
|value.id|string|Belge KIMLIĞI.|
|değer. karakter ücretlendirilir|tamsayı|API tarafından ücretlendirilen karakterler.|

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
      "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Running",
      "to": "fr",
      "progress": 0.1,
      "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
      "characterCharged": 0
    }
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
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
