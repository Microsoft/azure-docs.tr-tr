---
title: REST API konsolu ile denetleme iş akışlarını tanımlama Content Moderator
titleSuffix: Azure Cognitive Services
description: İçerik ilkelerinize göre özel iş akışlarını ve eşikleri tanımlamak için Azure Content Moderator Review API 'Lerini kullanabilirsiniz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754176"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Denetleme iş akışlarını tanımlama ve kullanma (REST)

İş akışları, içeriği daha verimli bir şekilde işlemek için kullanabileceğiniz bulut tabanlı özelleştirilmiş filtrelerdir. İş akışları, farklı yollarla içerik filtrelemek için çeşitli hizmetlere bağlanabilir ve ardından uygun eylemi gerçekleştirebilir. Bu kılavuzda, iş akışları oluşturmak ve kullanmak için, API konsolu aracılığıyla iş akışı REST API 'Lerinin nasıl kullanılacağı gösterilir. API 'lerin yapısını anladıktan sonra, bu çağrıları REST ile uyumlu herhangi bir platforma kolayca bağlayabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Content Moderator [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com/) sitesinde oturum açın veya hesap oluşturun.

## <a name="create-a-workflow"></a>İş akışı oluşturma

Bir iş akışı oluşturmak veya güncelleştirmek için, **[Iş akışı-API başvurusu oluştur veya Güncelleştir](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** sayfasına gidin ve anahtar bölgeniz için düğmeyi seçin (bunu [Inceleme aracının](https://contentmoderator.cognitive.microsoft.com/) **KIMLIK bilgileri** sayfasında uç nokta URL 'sinde bulabilirsiniz). Bu, REST API çağrılarını kolayca oluşturabileceğiniz ve çalıştırabileceğiniz API konsolunu başlatır.

![İş akışı-sayfa bölgesi seçimini oluştur veya güncelleştir](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST çağrı parametrelerini girin

**Ekip**, **workflowname**ve **OCP-apim-Subscription-Key**değerlerini girin:

- **ekip**: [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com/) hesabınızı ayarlarken oluşturduğunuz takım kimliği (gözden geçirme aracınızın kimlik bilgileri ekranındaki **kimlik** alanında bulunur).
- **workflowname**: mevcut bir iş akışını güncelleştirmek istiyorsanız eklenecek yeni bir iş akışının adı (veya var olan bir ad).
- **OCP-apim-Subscription-Key**: Content moderator anahtarınız. Bunu [İnceleme aracının](https://contentmoderator.cognitive.microsoft.com) **Ayarlar** sekmesinde bulabilirsiniz.

![İş akışı-konsol sorgu parametrelerini ve üstbilgilerini oluşturun veya güncelleştirin](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>İş akışı tanımı girin

1. **Açıklama** ve **türe** ilişkin ayrıntıları içeren JSON isteğini girmek için **istek gövdesi** kutusunu düzenleyin (`Image` ya da `Text`).
2. **İfade**için, varsayılan Iş akışı JSON ifadesini kopyalayın. Son JSON dizeniz şöyle görünmelidir:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Bu API 'YI kullanarak iş akışlarınız için basit, karmaşık ve hatta iç içe geçmiş ifadeler tanımlayabilirsiniz. [Iş akışı-oluşturma veya güncelleştirme](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) belgelerinin daha karmaşık bir mantığa örnekleri vardır.

### <a name="submit-your-request"></a>İsteğinizi gönderme
  
**Gönder**’i seçin. İşlem başarılı olursa, **yanıt durumu** `200 OK` olur ve **yanıt içeriği** kutusu `true` görüntülenir.

### <a name="examine-the-new-workflow"></a>Yeni iş akışını inceleyin

[İnceleme aracında](https://contentmoderator.cognitive.microsoft.com/), **Ayarlar**  > **iş akışları**' nı seçin. Yeni iş akışınız listede görünmelidir.

![İş akışlarının araç listesini gözden geçirin](images/workflow-console-new-workflow.PNG)

İş akışınız için **Düzenle** seçeneğini belirleyin ve **Tasarımcı** sekmesine gidin. Burada, JSON mantığının sezgisel bir gösterimini görebilirsiniz.

![Seçili iş akışı için Tasarımcı sekmesi](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>İş akışı ayrıntılarını al

Mevcut bir iş akışı hakkındaki ayrıntıları almak için, **[Iş akışı-](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API başvurusunu Al sayfasına gidin ve bölgeniz (anahtarınızın yönettiği bölge) için düğmeyi seçin.

![İş akışı-bölge seçimini al](images/test-drive-region.png)

REST çağrı parametrelerini yukarıdaki bölümde olduğu gibi girin. Bu zamanın, **workflowname** öğesinin mevcut bir iş akışının adı olduğundan emin olun.

![Sorgu parametrelerini ve üstbilgilerini al](images/workflow-get-default.PNG)

**Gönder**’i seçin. İşlem başarılı olursa, **yanıt durumu** `200 OK` olur ve **yanıt içeriği** kutusu, iş AKıŞıNı aşağıdakine benzer şekilde JSON biçiminde görüntüler:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [İçerik denetleme işleriyle](try-review-api-job.md)iş akışlarını nasıl kullanacağınızı öğrenin.