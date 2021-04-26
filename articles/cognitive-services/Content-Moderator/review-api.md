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
ms.openlocfilehash: 43c39c40af6e02861211a8666fefa57c34072f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96905205"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>İçerik denetleme incelemeleri, iş akışları ve işler

Content Moderator, gerçek dünyada senaryolar için en iyi denetleme sürecini oluşturmak üzere, bir bilgisayar yardımlı denetimi döngü içi yetenekler ile birleştirir. Bunu, bulut tabanlı [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com)aracılığıyla yapar. Bu kılavuzda Inceleme aracının temel kavramları hakkında bilgi edineceksiniz: incelemeler, iş akışları ve işler.

## <a name="reviews"></a>İncelemeler

İnceleme içinde içerik gözden geçirme aracına yüklenir. Panoda, Pano üzerinde **gözden geçir** sekmesinde bulunan içerik türü ' ne tıklayarak bu görünümü görüntüleyebilirsiniz. İnceleme ekranından, uygulanan etiketleri değiştirebilir ve kendi özel etiketlerinizi uygun şekilde uygulayabilirsiniz. Bir gözden geçirme gönderdiğinizde, sonuçlar belirtilen geri çağırma uç noktasına gönderilir ve içerik siteden kaldırılır.

> [!div class="mx-imgBorder"]
> ![Gözden geçirme açılır menüsü vurgulanır. Bu içerik türlerini gösterir: görüntü, metin ve video.](./Review-Tool-user-Guide/images/review-tab.png)

### <a name="manage-reviews"></a>İncelemeleri yönetme

Panodan yönetici   ->  ekranını görüntülemek için yönetici **gözden geçirmeleri Yönet** ' e gidin. Burada, tüm incelemelerinin bir listesini görebilirsiniz (bekliyor ve tamamlandı).

Her incelemede üç noktalı **Eylemler** düğmesi İnceleme ekranına gitmenizi veya gözden geçirin geçmişini incelemenizi sağlar.

> [!div class="mx-imgBorder"]
> ![Inceleme ekranında araç Web sitesini gözden geçirin](./Review-Tool-user-Guide/images/manage-reviews.png)

Gözden geçirme durumu, Etiketler, içerik türü, alt takımlar, atanan kullanıcılar ve oluşturulma/değiştirilme tarihi gibi çeşitli kategorilere göre İncelemeleri sıralamak için **arama** araç çubuğunu kullanın.

> [!div class="mx-imgBorder"]
> ![Arama araç çubuğu görüntülenir. Inceleme durumu ve etiketleri gibi arama ölçütlerini girmeye yönelik çeşitli Birleşik giriş kutularına sahiptir.](./Review-Tool-user-Guide/images/review-search.png)

İncelemeleri oluşturmaya başlamak için [Gözden geçirme aracı kılavuzuna](./review-tool-user-guide/review-moderated-images.md) bakın veya bunu programlı olarak nasıl yapacağınızı öğrenmek için [API konsolu kılavuzuna](./try-review-api-review.md) bakın.

## <a name="workflows"></a>İş Akışları

Bir iş akışı, içerik için bulut tabanlı özelleştirilmiş bir filtredir. İş akışları, farklı yollarla içerik filtrelemek için çeşitli hizmetlere bağlanabilir ve ardından uygun eylemi gerçekleştirebilir. Content Moderator Bağlayıcısı ile, bir iş akışı otomatik olarak denetleme etiketleri uygulayabilir ve gönderilen içerikle ilgili incelemeler oluşturabilir.

### <a name="view-workflows"></a>İş akışlarını görüntüle

Mevcut iş akışlarınızı görüntülemek için [İnceleme aracına](https://contentmoderator.cognitive.microsoft.com/) gidin ve **yönetici**  >  **iş akışları**' nı seçin.

> [!div class="mx-imgBorder"]
> ![Varsayılan iş akışı](images/default-workflow-list.png)

İş akışları JSON dizeleri olarak tanımlanır ve bu, programlı olarak erişilebilir hale gelir. İş akışınız için **Düzenle** seçeneğini belirleyip **JSON** sekmesini seçerseniz, aşağıdaki gibi bir JSON ifadesi görürsünüz:

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

İş akışlarını oluşturmaya ve kullanmaya başlamak için [Gözden geçirme aracı kılavuzuna](./review-tool-user-guide/workflows.md) bakın veya bunu programlı olarak nasıl yapacağınızı öğrenmek için [API konsol kılavuzuna](./try-review-api-workflow.md) bakın.

## <a name="jobs"></a>İşler

Bir denetleme işi, içerik denetleme, iş akışı ve gözden geçirme işlevleri için bir sarmalayıcı türü görevi görür. İş, Content Moderator görüntü denetleme API 'SI veya metin denetleme API 'sini kullanarak içeriğinizi tarar ve ardından belirlenen iş akışına karşı denetler. İş akışı sonuçlarına bağlı olarak, [Gözden geçirme aracında](./review-tool-user-guide/human-in-the-loop.md)içerik için bir gözden geçirme oluşturmayabilir veya olmayabilir. Her iki İnceleme ve iş akışı kendi API 'Leriyle oluşturulup yapılandırılabileceği sürece, iş API 'SI işlemin tamamına (belirtilen geri çağırma uç noktasına gönderilebilir) ilişkin ayrıntılı bir rapor elde etmenizi sağlar.

İşleri kullanmaya başlamak için [API konsolu kılavuzuna](./try-review-api-job.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Iş API konsolunun](try-review-api-job.md)sürücüsünü test edin ve REST API kod örneklerini kullanın. Visual Studio ve C# hakkında bilginiz varsa, [işler .net hızlı](moderation-jobs-quickstart-dotnet.md)başlangıcı ' na de göz atın. 
* İncelemeler için, [API konsolunu gözden geçir](try-review-api-review.md)' i kullanmaya başlayın ve REST API kod örneklerini kullanın. Ardından [.net hızlı başlangıç](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)konusunun incelemeler bölümüne bakın.
* Video incelemeleri için, [video incelemesi hızlı](video-reviews-quickstart-dotnet.md)başlangıcı ' nı kullanın ve [video incelemesine yorum ekleme](video-transcript-reviews-quickstart-dotnet.md)hakkında bilgi edinin.