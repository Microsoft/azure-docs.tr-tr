---
title: İncelemeler, Iş akışları ve Işler kavramları-Content Moderator
titleSuffix: Azure Cognitive Services
description: Bu makalede Inceleme aracının temel kavramları hakkında bilgi edineceksiniz. İncelemeler, iş akışları ve işler.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744417"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>İçerik denetleme incelemeleri, iş akışları ve işler

Content Moderator, gerçek dünyada senaryolar için en iyi denetleme sürecini oluşturmak üzere, bir bilgisayar yardımlı denetimi döngü içi yetenekler ile birleştirir. Bunu, bulut tabanlı [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com)aracılığıyla yapar. Bu kılavuzda Inceleme aracının temel kavramları hakkında bilgi edineceksiniz: incelemeler, iş akışları ve işler.

## <a name="reviews"></a>İncelemeler

İnceleme içinde içerik gözden geçirme aracına yüklenir ve **Gözden geçirme** sekmesi altında görüntülenir. Buradan, kullanıcılar uygulanan etiketleri değiştirebilir ve kendi özel etiketlerini uygun şekilde uygulayabilir. Bir Kullanıcı bir gözden geçirme gönderdiğinde, sonuçlar belirtilen geri çağırma uç noktasına gönderilir ve içerik siteden kaldırılır.

![Inceleme sekmesinde bir tarayıcıda açık olan araç Web sitesini gözden geçirme](./Review-Tool-user-Guide/images/image-workflow-review.png)

İncelemeleri oluşturmaya başlamak için [İnceleme aracı kılavuzuna](./review-tool-user-guide/review-moderated-images.md) bakın veya bunu programlı olarak nasıl yapacağınızı öğrenmek için [REST API kılavuzuna](./try-review-api-review.md) bakın.

## <a name="workflows"></a>İş akışları

Bir iş akışı, içerik için bulut tabanlı özelleştirilmiş bir filtredir. İş akışları, farklı yollarla içerik filtrelemek için çeşitli hizmetlere bağlanabilir ve ardından uygun eylemi gerçekleştirebilir. Content Moderator Bağlayıcısı ile, bir iş akışı otomatik olarak denetleme etiketleri uygulayabilir ve gönderilen içerikle ilgili incelemeler oluşturabilir.

### <a name="view-workflows"></a>İş akışlarını görüntüle

Mevcut iş akışlarınızı görüntülemek için [İnceleme aracına](https://contentmoderator.cognitive.microsoft.com/) gidin ve **Ayarlar** > **iş akışları**' nı seçin.

![Varsayılan iş akışı](images/default-workflow-listed.PNG)

İş akışları tamamen JSON dizeleri olarak açıklanabilir, bu da programlı bir şekilde erişilebilir hale gelir. İş akışınız için **Düzenle** seçeneğini belirleyip **JSON** sekmesini seçerseniz, aşağıdaki gibi bir JSON ifadesi görürsünüz:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

İş akışlarını oluşturmaya ve kullanmaya başlamak için [Gözden geçirme aracı kılavuzuna](./review-tool-user-guide/workflows.md) bakın veya bunu programlı olarak nasıl yapacağınızı öğrenmek için [REST API kılavuzuna](./try-review-api-workflow.md) bakın.

## <a name="jobs"></a>İşler

Bir denetleme işi, içerik denetleme, iş akışları ve İncelemeler işlevleri için bir sarmalayıcı türü görevi görür. İş, Content Moderator görüntü denetleme API 'SI veya metin denetleme API 'sini kullanarak içeriğinizi tarar ve ardından belirlenen iş akışına karşı denetler. İş akışı sonuçlarına bağlı olarak, [Gözden geçirme aracında](./review-tool-user-guide/human-in-the-loop.md)içerik için bir gözden geçirme oluşturmayabilir veya olmayabilir. Her iki İnceleme ve iş akışı kendi API 'Leriyle oluşturulup yapılandırılabileceği sürece, iş API 'SI işlemin tamamına (belirtilen geri çağırma uç noktasına gönderilebilir) ilişkin ayrıntılı bir rapor elde etmenizi sağlar.

İşleri kullanmaya başlamak için [REST API kılavuzuna](./try-review-api-job.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Iş API konsolunun](try-review-api-job.md)sürücüsünü test edin ve REST API kod örneklerini kullanın. Visual Studio ve C#hakkında bilgi sahibiyseniz, [işler .net hızlı başlangıç](moderation-jobs-quickstart-dotnet.md)' i de gözden geçirin. 
* İncelemeler için, [API konsolunu gözden geçir](try-review-api-review.md)' i kullanmaya başlayın ve REST API kod örneklerini kullanın. Ardından [.net hızlı başlangıç](dotnet-sdk-quickstart.md)konusunun incelemeler bölümüne bakın.
* Video incelemeleri için, [video incelemesi hızlı](video-reviews-quickstart-dotnet.md)başlangıcı ' nı kullanın ve [video incelemesine yorum ekleme](video-transcript-reviews-quickstart-dotnet.md)hakkında bilgi edinin.
