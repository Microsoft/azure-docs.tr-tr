---
title: Çeviriyi Başlat
titleSuffix: Azure Cognitive Services
description: Belge çevirisi hizmeti ile bir belge çevirisi isteği başlatın.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 820b5f39192fffa0ec54b44c6016965599d85a8c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863704"
---
# <a name="start-translation"></a>Çeviriyi Başlat

Belge çevirisi hizmetiyle toplu (Batch) çeviri isteği başlatmak için bu API 'yi kullanın. Her istek birden çok belge içerebilir ve her belge için bir kaynak ve hedef kapsayıcı içermelidir.

Klasörleri filtrelemek için önek ve sonek filtresi (sağlanırsa) kullanılır. Önek, kapsayıcı adından sonra alt yol için uygulanır.

Işları/çeviri belleği, isteğe dahil edilebilir ve belge çevrildiğinde hizmet tarafından uygulanır.

Sözlük, çeviri sırasında geçersiz veya ulaşılamaz durumdaysa, belge durumunda bir hata gösterilir. Hedefte aynı ada sahip bir dosya zaten varsa üzerine yazılır. Her hedef dilin targetUrl 'Si benzersiz olmalıdır.

## <a name="request-url"></a>İstek URL’si

Şu kişiye bir `POST` istek gönder:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
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

## <a name="request-body-batch-submission-request"></a>İstek gövdesi: Batch gönderim Isteği

|Ad|Tür|Description|
|--- |--- |--- |
|girişi|BatchRequest []|Aşağıda listelenen BatchRequest. Belge içeren belge veya klasörlerin giriş listesi. Medya türleri: "Application/JSON", "Text/JSON", "Application/* + JSON".|

### <a name="inputs"></a>Girişler

Giriş toplu işlem çevirisi isteği için tanım.

|Ad|Tür|Gerekli|Açıklama|
|--- |--- |--- |--- |
|kaynak|Sourceınput []|Doğru|Aşağıda listelenen girişler. kaynak. Giriş belgelerinin kaynağı.|
|storageType|Storageınputtype []|Doğru|Aşağıda listelenen girişler. storageType. Giriş belgelerinin kaynak dizesinin depolama türü.|
|lerden|Targetınput []|Doğru|girdi. Target aşağıda listelenmiştir. Çıktının hedefinin konumu.|

**girişler. kaynak**

Giriş belgelerinin kaynağı.

|Ad|Tür|Gerekli|Açıklama|
|--- |--- |--- |--- |
|filtre|DocumentFilter []|Yanlış|DocumentFilter [] aşağıda listelenmiştir.|
|Filter. Prefix|string|Yanlış|Çeviri için kaynak yolundaki belgeleri filtrelemek için büyük/küçük harfe duyarlı bir ön ek dizesi. Örneğin, bir Azure Storage blob URI kullanırken, alt klasörleri çeviri için kısıtlamak üzere önekini kullanın.|
|Filter. suffix|string|Yanlış|Çeviri için kaynak yolundaki belgeleri filtrelemek için büyük/küçük harfe duyarlı bir sonek dizesi. Bu, çoğu zaman dosya uzantıları için kullanılır.|
|language|string|Yanlış|Dil kodu belirtilmemişse, belgede otomatik algılama işlemi gerçekleştirilecek.|
|sourceUrl|string|True|Klasör/kapsayıcının veya tek dosyanın belgelerinize sahip konumu.|
|storageSource|StorageSource|Yanlış|Aşağıda listelenen StorageSource.|
|storageSource. AzureBlob|string|Yanlış||

**girişler. storageType**

Giriş belgelerinin kaynak dizesinin depolama türü.

|Ad|Tür|
|--- |--- |
| dosyası|string|
|Klasör|string|

**girişler. Target**

Tamamlanmış çevrilmiş belgelerin hedefi.

|Ad|Tür|Gerekli|Açıklama|
|--- |--- |--- |--- |
|category|string|Yanlış|Çeviri isteği için kategori/özel sistem.|
|ışları|Sözlük []|Yanlış|Aşağıda listelenen sözlük. Sözlük listesi.|
|ışları. Format|string|Yanlış|Formatını.|
|ışları. ışaryurl 'Si|string|True (ışsam kullanılıyorsa)|Sözlük konumu. Biçim parametresi sağlanmazsa biçimlendirmeyi ayıklamak için dosya uzantısını kullanacağız. Bir çeviri dili çifti Sözlükte yoksa, uygulanmaz.|
|ışları. storageSource|StorageSource|Yanlış|Yukarıda listelenen StorageSource.|
|targetUrl|string|True|Belgelerinizdeki klasör/kapsayıcının konumu.|
|language|string|True|İki harfli hedef dil kodu. Bkz. [dil kodlarının listesi](../../language-support.md).|
|storageSource|StorageSource []|Yanlış|StorageSource [] yukarıda listelenmiştir.|
|sürüm|string|Yanlış|Sürüm.|

## <a name="example-request"></a>Örnek istek

Aşağıda toplu istek örnekleri verilmiştir.

**Bir kapsayıcıdaki tüm belgeleri çevirme**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Bir kapsayıcıdaki tüm belgeleri çevirme ışları uygulama**

Belirli bir blob/belge (kapsayıcı için değil) için SAS belirteci & için sözlük URL 'SI oluşturduğunuzdan emin olun

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
     "glossaries": [
                        {
                            "glossaryUrl": https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D,
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**Kapsayıcıda belirli bir klasör çevriliyor**

Filtre içinde önek olarak klasör adını (büyük/küçük harfe duyarlı) belirttiğinizden emin olun; ancak, SAS belirteci hala kapsayıcı için olur.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D,
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Kapsayıcıda belirli bir belgeyi çevirme**

* "StorageType" belirttiğinizden emin olun: "dosya"
* Belirli bir blob/belge (kapsayıcı için değil) için SAS belirteci & kaynak URL 'SI oluşturduğunuzdan emin olun
* Hedef dosya adını hedef URL 'nin bir parçası olarak belirttiğinizden emin olun; ancak SAS belirteci hala kapsayıcı için olur.
* Aşağıdaki örnek istek, iki hedef dile çevrilen tek bir belgeyi göstermektedir

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>Yanıt durum kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir.

|Durum Kodu|Description|
|--- |--- |
|202|Eden. Başarılı istek ve toplu istek hizmet tarafından oluşturulur. Üst bilgi Operation-Location, işlem KIMLIĞI. HeadersOperation-location: String olan bir durum URL 'si gösterir|
|400|Hatalı Istek. Geçersiz istek. Giriş parametrelerini denetleyin.|
|401|Erişilmesini. Lütfen kimlik bilgilerinizi kontrol edin.|
|429|İstek oranı çok yüksek.|
|500|İç sunucu hatası.|
|503|Hizmet şu anda kullanılamıyor.  Lütfen daha sonra yeniden deneyin.|
|Diğer durum kodları|<ul><li>Çok fazla istek</li><li>Sunucu geçici olarak kullanılamıyor</li></ul>|

## <a name="error-response"></a>Hata yanıtı

|Ad|Tür|Description|
|--- |--- |--- |
|kod|string|Üst düzey hata kodlarını içeren Numaralandırmalar. Olası değerler:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>Invalidrequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Yetkisiz</li></ul>|
|message|string|Üst düzey hata iletisini alır.|
|ınnererror|InnerErrorV2|Bilişsel hizmetler API yönergelerine uyan yeni bir Iç hata biçimi. Gerekli özellikler hata kodu, ileti ve isteğe bağlı özellikler hedefi, ayrıntılar (anahtar değeri çifti), iç hata (Bu iç içe olabilir) içeriyor.|
|Dahili. Raporladı|string|Kod hata dizesini alır.|
|ınnererror. Message|string|Üst düzey hata iletisini alır.|

## <a name="examples"></a>Örnekler

### <a name="example-successful-response"></a>Örnek başarılı yanıt

Aşağıdaki bilgiler başarılı bir yanıtta döndürülür.

İş KIMLIĞINI POST yönteminin yanıt üst bilgisinde Operation-Location URL değerinde bulabilirsiniz. URL 'nin son parametresi işlemin iş KIMLIĞIDIR ("/Operation/" ' dan sonraki dize).

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Örnek hata yanıtı

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Belge çevirisi ve istemci kitaplığı kullanma hakkında daha fazla bilgi edinmek için hızlı başlangıçlarımızı izleyin.

> [!div class="nextstepaction"]
> [Belge çevirisi ile çalışmaya başlama](../get-started-with-document-translation.md)
