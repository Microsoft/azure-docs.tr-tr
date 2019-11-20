---
title: REST API konsolu ile denetleme işlerini kullanın Content Moderator
titleSuffix: Azure Cognitive Services
description: Azure Content Moderator 'de görüntü veya metin içeriğine yönelik uçtan uca içerik denetleme işleri başlatmak için API 'nin iş işlemlerini gözden geçir ' i kullanın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935963"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Denetleme işlerini tanımlama ve kullanma (REST)

Bir denetleme işi, içerik denetleme, iş akışları ve İncelemeler işlevleri için bir sarmalayıcı türü görevi görür. Bu kılavuzda, içerik denetleme işlerini başlatmak ve denetlemek için iş REST API 'Lerinin nasıl kullanılacağı gösterilmektedir. API 'lerin yapısını anladıktan sonra, bu çağrıları REST ile uyumlu herhangi bir platforma kolayca bağlayabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Content Moderator [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com/) sitesinde oturum açın veya hesap oluşturun.
- Seçim İşle birlikte kullanılacak [özel bir iş akışı tanımlayın](./Review-Tool-User-Guide/Workflows.md) ; Varsayılan iş akışını da kullanabilirsiniz.

## <a name="create-a-job"></a>Bir iş oluşturma

Bir denetleme işi oluşturmak için, [iş-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API başvurusu Oluştur sayfasına gidin ve abonelik bölgeniz için düğmeyi seçin ( [Gözden geçirme aracının](https://contentmoderator.cognitive.microsoft.com/) **kimlik BILGILERI** sayfasında bunu uç nokta URL 'sinde bulabilirsiniz). Bu, REST API çağrılarını kolayca oluşturabileceğiniz ve çalıştırabileceğiniz API konsolunu başlatır.

![İş-sayfa bölgesi seçimi oluştur](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>REST çağrı parametrelerini girin

REST çağrısını oluşturmak için aşağıdaki değerleri girin:

- **TeamName**: [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com/) hesabınızı ayarlarken oluşturduğunuz takım kimliği (gözden geçirme aracınızın **kimlik bilgileri ekranındaki kimlik alanında bulunur** ).
- **ContentType**: Bu "görüntü", "metin" veya "video" olabilir.
- **ContentID**: özel bir tanımlayıcı dizesi. Bu dize, API 'ye geçirilir ve geri çağırma yoluyla döndürülür. Bir denetleme işinin sonuçlarıyla iç tanımlayıcıları veya meta verileri ilişkilendirmek için faydalıdır.
- **Workflowname**: daha önce oluşturduğunuz iş akışının adı (veya varsayılan iş akışı için "varsayılan").
- **Callbackendpoint**: (isteğe bağlı) Gözden geçirme tamamlandığında geri çağırma BILGILERINI alacak URL.
- **OCP-apim-Subscription-Key**: Content moderator anahtarınız. Bunu [İnceleme aracının](https://contentmoderator.cognitive.microsoft.com) **Ayarlar** sekmesinde bulabilirsiniz.

### <a name="fill-in-the-request-body"></a>İstek gövdesini doldur

REST çağrınızın gövdesi bir alan, **Contentvalue**içeriyor. Metni moderorsanız ham metin içeriğine yapıştırın veya görüntü/video oluşturuyorsanız bir görüntü veya video URL 'SI girin. Aşağıdaki örnek görüntü URL 'sini kullanabilirsiniz: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![İş-konsol sorgu parametrelerini, üstbilgilerini ve Istek gövdesi kutusunu oluşturma](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>İsteğinizi gönderme

**Gönder**’i seçin. İşlem başarılı olursa, **yanıt durumu** `200 OK`olur ve **yanıt içeriği** kutusunda iş için bir kimlik görüntülenir. Aşağıdaki adımlarda kullanmak için bu KIMLIĞI kopyalayın.

![İnceleme-konsol yanıt içeriği oluştur kutusu İnceleme KIMLIĞINI görüntüler](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>İş durumunu al

Çalışan veya tamamlanmış bir işin durumunu ve ayrıntılarını almak için, [iş-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API başvurusunu Al sayfasına gidin ve bölgenizin (anahtarınızın yönettiği bölge) düğmesini seçin.

![İş-bölge seçimini al](images/test-drive-region.png)

REST çağrı parametrelerini yukarıdaki bölümde olduğu gibi girin. Bu adım için iş **kimliği, işi** oluştururken aldığınız benzersiz kimlik dizesidir. **Gönder**’i seçin. İşlem başarılı olursa, **yanıt durumu** `200 OK`olur ve **yanıt içeriği** kutusu, işi aşağıdaki gibi JSON biçiminde görüntüler:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![İş-REST çağrısı yanıtını al](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Yeni Gözden geçirme (ler) i inceleyin

İçerik işiniz bir gözden geçirme oluşturma işlemi ile sonuçlanmasıyla, [Gözden geçirme aracında](https://contentmoderator.cognitive.microsoft.com)görüntüleyebilirsiniz.  > **görüntüsünü** **gözden geçir**/**metin**/**video** (kullandığınız içeriğe göre) seçeneğini belirleyin. İçerik, insan gözden geçirmesi için hazırlanmalıdır. İnsan aracı otomatik olarak atanan etiketleri ve tahmin verilerini gözden geçirdikten ve son bir denetleme kararı gönderdikten sonra, işler API 'SI tüm bu bilgileri belirtilen geri çağırma uç noktası uç noktasına gönderir.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, REST API kullanarak içerik denetleme işleri oluşturmayı ve sorgulamayı öğrendiniz. Ardından, işleri [E-ticaret denetleme](./ecommerce-retail-catalog-moderation.md) öğreticisi gibi uçtan uca bir denetleme senaryosuna tümleştirin.