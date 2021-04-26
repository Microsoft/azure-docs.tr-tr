---
title: Çeviri yöntemini iptal et
titleSuffix: Azure Cognitive Services
description: Çeviri iptali yöntemi şu anda bir işleme veya sıraya alınmış işlemi iptal eder.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: e3b7da30f54b9d9468b46a2cd0972a3397e5cdce
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865116"
---
# <a name="cancel-translation"></a>Çeviriyi iptal et

Şu anda işleme veya sıraya alınmış işlemi iptal edin. Bir işlem zaten tamamlanmışsa veya başarısız olduysa veya iptal edildiğinde iptal edilemez. Hatalı bir istek döndürülecek. Çeviri tamamlanmış tüm belgeler iptal edilemez ve ücretlendirilecektir. Tüm bekleyen belgeler mümkünse iptal edilecek.

## <a name="request-url"></a>İstek URL’si

Şu kişiye bir `DELETE` istek gönder:

```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

[Özel etki alanı adınızı](../get-started-with-document-translation.md#find-your-custom-domain-name)bulmayı öğrenin.

> [!IMPORTANT]
>
> * **Belge çevirisi hizmetine yönelik tüm API istekleri özel bir etki alanı uç noktası gerektirir**.
> * Belge çevirisi için HTTP istekleri yapmak üzere Azure portal kaynak _anahtarlarınız ve uç_ nokta sayfanızda veya küresel çevirmen uç noktasında bulunan uç noktayı kullanamazsınız `api.cognitive.microsofttranslator.com` .

## <a name="request-parameters"></a>İstek parametreleri

Sorgu dizesine geçirilen istek parametreleri şunlardır:

|Sorgu parametresi|Gerekli|Açıklama|
|-----|-----|-----|
|kimlik|Doğru|İşlem kimliği.|

## <a name="request-headers"></a>İstek üst bilgileri

İstek üst bilgileri:

|Üst Bilgiler|Description|
|-----|-----|
|Ocp-Apim-Subscription-Key|Gerekli istek üst bilgisi|

## <a name="response-status-codes"></a>Yanıt durum kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir.

| Durum Kodu| Description|
|-----|-----|
|200|Tamam ögesini seçin. İptal isteği gönderildi|
|401|Erişilmesini. Kimlik bilgilerinizi denetleyin.|
|404|Bulunamadı. Kaynak bulunamadı. 
|500|İç sunucu hatası.
|Diğer durum kodları|<ul><li>Çok fazla istek</li><li>Sunucu geçici olarak kullanılamıyor</li></ul>|

## <a name="cancel-translation-response"></a>Çeviri yanıtını iptal et

### <a name="successful-response"></a>Başarılı yanıt

Aşağıdaki bilgiler başarılı bir yanıtta döndürülür.

|Ad|Tür|Description|
|--- |--- |--- |
|kimlik|string|İşlemin KIMLIĞI.|
|createdDateTimeUtc|string|İşlem Oluşturulma tarihi.|
|lastActionDateTimeUtc|string|İşlemin durumunun güncelleştirildiği tarih ve saat.|
|durum|Dize|İş veya belge için olası durumların listesi: <ul><li>İptal edildi</li><li>Hoparlör</li><li>Başarısız</li><li>NotStarted</li><li>Çalışma</li><li>Başarılı</li><li>ValidationFailed</li></ul>|
|Özet|StatusSummary|Aşağıda listelenen ayrıntıları içeren Özet.|
|Özet. Toplam|tamsayı|Toplam belge sayısı.|
|Özet. başarısız|tamsayı|Belge sayısı başarısız oldu.|
|Özet. başarılı|tamsayı|Başarıyla çevrilen belge sayısı.|
|Summary. InProgress|tamsayı|Sürmekte olan belge sayısı.|
|Summary. notYetStarted|tamsayı|Henüz işleme başlamamış belge sayısı.|
|Özet. iptal edildi|tamsayı|İptal edilen sayısı.|
|Summary. Totalkarakterücret|tamsayı|API tarafından ücretlendirilen toplam karakter sayısı.|

### <a name="error-response"></a>Hata yanıtı

|Ad|Tür|Description|
|--- |--- |--- |
|kod|string|Üst düzey hata kodlarını içeren Numaralandırmalar. Olası değerler:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>Invalidrequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Yetkisiz</li></ul>|
|message|string|Üst düzey hata iletisini alır.|
|hedef|string|Hatanın kaynağını alır. Örneğin, geçersiz bir belge için "belgeler" veya "belge kimliği" olacaktır.|
|ınnererror|InnerErrorV2|Bilişsel hizmetler API yönergelerine uyan yeni bir Iç hata biçimi. Gerekli özellikleri hata kodu, ileti ve isteğe bağlı özellikler hedefi, ayrıntılar (anahtar değeri çifti), iç hata (iç içe olabilir) içerir.|
|ınnererror. Code|string|Kod hata dizesini alır.|
|Dahili. EROOR. iletisi|string|Üst düzey hata iletisini alır.|

## <a name="examples"></a>Örnekler

### <a name="example-successful-response"></a>Örnek başarılı yanıt

Aşağıdaki JSON nesnesi, başarılı bir yanıt örneğidir.

Durum kodu: 200

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
