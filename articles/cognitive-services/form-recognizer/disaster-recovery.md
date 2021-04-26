---
title: Azure form tanıyıcı için olağanüstü durum kurtarma Kılavuzu
titleSuffix: Azure Cognitive Services
description: Form tanıyıcı kaynaklarınızı yedeklemek için model kopyalama API 'sini nasıl kullanacağınızı öğrenin.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: b5eb776a7807f48ae6c1a0e3c5879da1f6823830
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466924"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>Form tanıyıcı modellerinizi yedekleme ve kurtarma

Azure portal bir form tanıyıcı kaynağı oluşturduğunuzda bir bölge belirlersiniz. Bundan sonra, kaynağınız ve tüm işlemleri söz konusu Azure Server bölgesiyle ilişkili kalır. Tüm bölgeyi ziyaret eden bir ağ sorunuyla karşılaşmanız mümkün değildir ancak imkansız değildir. Çözümünüzün her zaman kullanılabilir olması gerekiyorsa, bunu başka bir bölgeye devretmek veya iki ya da daha fazla bölge arasında iş yükünü bölmek için tasarlamanız gerekir. Her iki yaklaşım da farklı bölgelerde en az iki form tanıyıcı kaynağı ve bölgeler arasında özel modelleri eşitleme imkanını gerektirir.

Copy API 'SI, bir form tanıyıcı hesabından veya diğer kullanıcılara, desteklenen herhangi bir coğrafi bölgede bulunan özel modeller kopyalamanızı sağlayarak bu senaryoyu sağlar. Bu kılavuzda, kopyalama REST API kıvrımlı ile nasıl kullanılacağı gösterilmektedir. İstekleri vermek için Postman gibi bir HTTP isteği hizmeti de kullanabilirsiniz.

## <a name="business-scenarios"></a>İş senaryoları

Uygulamanız veya işiniz, form tanıyıcı özel modelinin kullanımına bağımlıysa, modelinizi başka bir bölgedeki başka bir form tanıyıcı hesabına kopyalamanızı öneririz. Bölgesel bir kesinti oluşursa, modelinize kopyalandığı bölgeye erişebilirsiniz.

##  <a name="prerequisites"></a>Önkoşullar

1. Farklı Azure bölgelerindeki Azure kaynaklarını iki form tanıyıcı. Bunlar yoksa, Azure portal gidin ve yeni bir form tanıyıcı kaynağı oluşturun <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" " target="_blank"> Yeni bir form tanıyıcı kaynağı oluşturun </a> .
1. Form tanıyıcı kaynağınızın abonelik anahtarı, uç nokta URL 'SI ve abonelik KIMLIĞI. Bu değerleri, Azure portal kaynağın **genel bakış** sekmesinde bulabilirsiniz.


## <a name="copy-api-overview"></a>API 'ye Genel Bakış

Özel bir modeli kopyalama işlemi aşağıdaki adımlardan oluşur:

1. İlk &mdash; olarak, kopyalanmış modeli alacak kaynak olan hedef kaynağa bir kopya yetkilendirme isteği verirsiniz. Yeni oluşturulan hedef modelin URL 'sini geri alırsınız ve bu, kopyalanmış verileri alır.
1. Sonra kopyalama isteğini, kopyalanacak modeli içeren kaynağı kaynak kaynağına gönderirsiniz &mdash; . İşlemin ilerlemesini izlemek için Sorgulayabileceğiniz bir URL 'YI geri alacaksınız.
1. İşlem başarılı olana kadar ilerleme URL 'sini sorgulamak için kaynak kaynak kimlik bilgilerinizi kullanacaksınız. Yeni modelin durumunu almak için hedef kaynaktaki yeni model KIMLIĞINI de sorgulayabilirsiniz.

## <a name="generate-copy-authorization-request"></a>Kopya yetkilendirme isteği oluştur

Aşağıdaki HTTP isteği hedef kaynaktan kopya yetkilendirmesi alır. Hedef kaynağınızın uç noktasını ve anahtarını üstbilgiler olarak girmeniz gerekir.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

`201\Created`Gövdedeki bir değere sahip bir yanıt alırsınız `modelId` . Bu dize, yeni oluşturulan (boş) modelin KIMLIĞIDIR. `accessToken`API 'nin bu kaynağa veri kopyalaması için gereklidir ve `expirationDateTimeTicks` değer belirtecin sona ercidir. Bu değerlerin üçünü de güvenli bir konuma kaydedin.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>Kopyalama işlemini Başlat

Aşağıdaki HTTP isteği, kaynak kaynakta kopyalama işlemini başlatır. Kaynak kaynağınızın uç noktasını ve anahtarını üstbilgiler olarak girmeniz gerekir. İstek URL 'SI, kopyalamak istediğiniz kaynak modelin model KIMLIĞINI içerir.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/<your model ID>/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

İsteğiniz gövdesinin aşağıdaki biçimde olması gerekir. Hedef kaynağınızın kaynak KIMLIĞI ve bölge adını girmeniz gerekir. Kaynak KIMLIĞINIZI Azure portal kaynağınızın **Özellikler** sekmesinde bulabilir ve bölge adını **anahtarlar ve uç nokta** sekmesinde bulabilirsiniz. Ayrıca, önceki adımdan kopyaladığınız model KIMLIĞI, erişim belirteci ve sona erme değeri de gereklidir.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
}
```

> [!NOTE]
> Kopya API 'SI, [AEK/CMK](https://msazure.visualstudio.com/Cognitive%20Services/_wiki/wikis/Cognitive%20Services.wiki/52146/Customer-Managed-Keys) özelliğini saydam olarak destekler. Bu, özel bir işleme gerektirmez, ancak şifrelenmemiş bir kaynak arasında şifrelenmiş bir kaynağa kopyalama yapıyorsanız, istek üst bilgisini eklemeniz gerektiğini unutmayın `x-ms-forms-copy-degrade: true` . Bu üst bilgi dahil edilmediğinden kopyalama işlemi başarısız olur ve döndürür `DataProtectionTransformServiceError` .

`202\Accepted`Operation-Location üstbilgisiyle bir yanıt alırsınız. Bu değer, işlemin ilerlemesini izlemek için kullanacağınız URL 'dir. Sonraki adım için geçici bir konuma kopyalayın.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

### <a name="common-errors"></a>Sık karşılaşılan hatalar

|Hata|Çözüm|
|:--|:--|
| 400/ile hatalı Istek `"code:" "1002"` | Doğrulama hatasını veya hatalı oluşturulmuş kopyalama isteğini gösterir. Genel sorunlar şunlardır: a) geçersiz veya değiştirilmiş `copyAuthorization` Yük. b) belirtecin süresi dolma değeri `expirationDateTimeTicks` ( `copyAuhtorization` Yük 24 saat için geçerlidir). c) geçersiz veya desteklenmiyor `targetResourceRegion` . d) geçersiz veya hatalı biçimlendirilmiş `targetResourceId` dize.
|

## <a name="track-copy-progress"></a>Kopyalama ilerlemesini izle

Kaynak kaynak uç noktasına karşı **kopya modeli sonucunu al** API 'sini sorgulayarak ilerlemenizi izleyin.

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Yanıtınız, işlemin durumuna göre değişir. `"status"`JSON gövdesindeki alanı bulun. Bu API çağrısını bir betikte otomatikleştiriyorsanız, her saniye bir işlemi sorgulıyoruz.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="common-errors"></a>Sık karşılaşılan hatalar

|Hata|Çözüm|
|:--|:--|
|"hatalar": [{"Code": "AuthorizationError",<br>"ileti": "hata nedeniyle yetkilendirme hatası <br>yetkilendirme talepleri eksik veya geçersiz. "}]   | `copyAuthorization`Yük veya içerik API tarafından döndürülmeden değiştirildiğinde gerçekleşir `copyAuthorization` . Yükün önceki çağrıdan döndürülen içeriğin aynısını içerdiğinden emin olun `copyAuthorization` .|
|"hatalar": [{"Code": "AuthorizationError",<br>"ileti": "yetkilendirme alınamadı <br>veriyi. Bu sorun devam ederse, farklı bir <br>Kopyalanacak hedef model. "}] | `copyAuthorization`Yükün bir kopyalama isteğiyle yeniden kullanıldığını belirtir. Başarılı olan bir kopya isteği, aynı yükü kullanan başka isteklere izin vermez `copyAuthorization` . Ayrı bir hata (aşağıda belirtilen olanlar gibi) ve daha sonra aynı yetkilendirme yüküyle kopyayı yeniden denemeye çalışırsanız, bu hata oluşur. Çözüm, yeni bir `copyAuthorization` Yük oluşturmak ve sonra kopyalama isteğini yeniden yayımladı.|
|"hatalar": [{"Code": "DataProtectionTransformServiceError",<br>"ileti": "veri aktarımı isteğine izin verilmiyor <br>daha az güvenli bir veri koruma şemasına indirgendiğinden. Belgelere bakın veya hizmet yöneticinize başvurun <br>Ayrıntılar için. "}]    | `AEK`Etkin bir kaynak arasında etkin olmayan bir kaynağa kopyalanırken oluşur `AEK` . Şifrelenmiş modelin hedefe şifrelenmemiş olarak kopyalanmasını sağlamak için `x-ms-forms-copy-degrade: true` kopyalama isteğiyle üst bilgi belirtin.|
|"hatalar": [{"Code": "ResourceResolverError",<br>"ileti": "kimliği '... ' olan bilişsel kaynak için bilgi getirilemedi. Kaynağın geçerli olduğundan ve belirtilen ' westus2 ' bölgesinde mevcut olduğundan emin olun.. "}] | Tarafından belirtilen Azure kaynağının `targetResourceId` geçerli bir bilişsel kaynak olmadığını veya mevcut olmadığını gösterir. Bu sorunu çözmek için kopyalama isteğini doğrulayın ve yeniden yayımlayın.|


### <a name="optional-track-the-target-model-id"></a>Seçim Hedef model KIMLIĞINI izleme 

Hedef modeli sorgulayarak işlemin durumunu izlemek için **özel model al** API 'sini de kullanabilirsiniz. İlk adımda kopyaladığınız hedef model KIMLIĞINI kullanarak bu API 'YI çağırın.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Yanıt gövdesinde, modeliyle ilgili bilgileri görürsünüz. `"status"`Modelin durumu için alanı denetleyin.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>örnek kodu kıvır

Aşağıdaki kod parçacıkları, yukarıdaki adımlarda açıklanan API çağrılarını yapmak için kıvrımlı kullanır. Yine de kendi kaynaklarınıza özel model kimliklerini ve abonelik bilgilerini doldurmanız gerekir.

### <a name="generate-copy-authorization-request"></a>Kopya yetkilendirme isteği oluştur

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>Kopyalama işlemini Başlat

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>Kopyalama ilerlemesini izle

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, özel modellerinizi ikincil form tanıyıcı kaynağına yedeklemek için kopyalama API 'sini nasıl kullanacağınızı öğrendiniz. Daha sonra, form tanıyıcı ile neler yapabileceğinizi görmek için API başvuru belgelerini inceleyin.
* [REST API başvuru belgeleri](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
