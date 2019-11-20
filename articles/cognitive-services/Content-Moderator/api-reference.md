---
title: API başvurusu-Content Moderator
titleSuffix: Azure Cognitive Services
description: Çeşitli içerik denetleme ve Content Moderator için API 'Leri gözden geçirme hakkında bilgi edinin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757371"
---
# <a name="content-moderator-api-reference"></a>Content Moderator API başvurusu

Azure Content Moderator API 'Leri ile aşağıdaki yollarla çalışmaya ulaşabilirsiniz:

- Azure portal, [Content moderator API 'sine abone olun](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Bkz. [Web üzerinde Content moderator deneyin](quick-start.md) [Content moderator İnceleme aracıyla](https://contentmoderator.cognitive.microsoft.com/)kaydolun.

## <a name="moderation-apis"></a>Yönetim API’leri

Denetleme sonrası iş akışlarınızı ayarlamak için aşağıdaki Content Moderator API 'Lerini kullanabilirsiniz.

| Açıklama | Başvuru |
| -------------------- |-------------|
| **Görüntü denetleme API 'SI**<br /><br />Etiketleri, güvenirlik puanlarını ve diğer ayıklanan bilgileri kullanarak görüntüleri tarayın ve olası yetişkin ve akıllı içeriği tespit edin. <br /><br />Denetleme sonrası iş akışınızda içerik yayımlamak, reddetmek veya gözden geçirmek için bu bilgileri kullanın. <br /><br />| [Görüntü denetleme API 'SI başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Görüntü denetleme API 'SI başvurusu")   |
| **Metin denetleme API 'SI**<br /><br />Metin içeriğini tarayın. Küfür terimleri ve kişisel veriler döndürülür. <br /><br />Denetleme sonrası iş akışınızda içerik yayımlamak, reddetmek veya gözden geçirmek için bu bilgileri kullanın.<br /><br /> | [Metin denetleme API başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Metin denetleme API başvurusu")   |
| **Video denetleme API 'SI**<br /><br />Videoları tarayın ve olası yetişkin ve kcy içeriğini tespit edin. <br /><br />Denetleme sonrası iş akışınızda içerik yayımlamak, reddetmek veya gözden geçirmek için bu bilgileri kullanın.<br /><br /> | [Video denetleme API 'sine genel bakış](video-moderation-api.md "Video denetleme API 'sine genel bakış")   |
| **Yönetim API 'SI Listele**<br /><br />Özel dışlama veya görüntü ve metin ekleme listeleri oluşturun ve yönetin. Etkinleştirilirse, **görüntü eşleştirmesi** ve **metin ekranı** işlemleri, gönderilen içeriğin özel listelerinize karşı benzer şekilde eşleşmesini ister. <br /><br />Verimlilik için makine öğrenimi tabanlı denetleme adımını atlayabilirsiniz.<br /><br /> | [Yönetim API 'SI başvurusunu Listele](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Yönetim API 'SI başvurusunu Listele")   |

## <a name="review-apis"></a>API’leri inceleme

Inceleme API 'Leri aşağıdaki bileşenlere sahiptir:

| Açıklama | Başvuru |
| -------------------- |-------------|
| **İşler**<br /><br /> Tarama yapın ve hem görüntü hem de metin içeriği için denetleme iş akışlarını gözden geçirin. Bir denetleme işi, görüntü denetleme API 'sini ve metin denetleme API 'sini kullanarak içeriğinizi tarar. Denetleme işleri, gözden geçirmeler oluşturmak için tanımlı ve varsayılan iş akışlarını kullanır. <br /><br />Bir insan aracı otomatik olarak atanan etiketleri ve tahmin verilerini inceledikten ve bir içerik denetleme kararı gönderdikten sonra, gözden geçirme API 'SI tüm bilgileri API uç noktanıza gönderir.<br /><br /> | [İş başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "İş başvurusu")   |
| **İnceleyen**<br /><br />İnsan moderatör için doğrudan görüntü veya metin incelemesi oluşturmak için Inceleme aracını kullanın.<br /><br /> Bir insan aracı otomatik olarak atanan etiketleri ve tahmin verilerini inceledikten ve bir içerik denetleme kararı gönderdikten sonra, gözden geçirme API 'SI tüm bilgileri API uç noktanıza gönderir.<br /><br /> | [Başvuruyu gözden geçir](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Başvuruyu gözden geçir")   |
| **İş akışları**<br /><br />Takımınızın oluşturduğu özel iş akışlarıyla ilgili ayrıntıları oluşturun, güncelleştirin ve alın. İş akışlarını gözden geçirme aracını kullanarak tanımlarsınız. <br /> <br />İş akışları genellikle Content Moderator kullanır, ancak Inceleme aracında bağlayıcı olarak kullanılabilen diğer API 'Leri de kullanabilir.<br /><br /> | [İş akışı başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "İş akışı başvurusu")   |