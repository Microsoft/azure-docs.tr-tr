---
title: Inceleme aracı ile video denetleme-Content Moderator
titleSuffix: Azure Cognitive Services
description: Uygun olmayan içeriğe sahip olan makine yardımlı video denetleme ve gözden geçirme aracını kullanın
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 05ece4d8ea24fb9da106ec3daa2cc1a63706dab9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96853507"
---
# <a name="video-moderation-with-the-review-tool"></a>Inceleme aracı ile video denetleme

İşletmeniz için en iyi sonuçları elde etmek için Content Moderator makine yardımlı [video denetleme](video-moderation-api.md) ve [izleme aracını](Review-Tool-User-Guide/human-in-the-loop.md) , Yetişkin (açık) ve kcy (müstesel) içeriğe yönelik orta ve geri betikler için kullanın.

## <a name="view-videos-under-review"></a>İnceleme altında videoları görüntüleme

Panoda, video içerik türü içinde gözden geçirme kuyrukları ' nı seçin. Bu, bir gözden geçirme başlatır ve video içerik denetleme sayfasını açar.

> [!div class="mx-imgBorder"]
> ![Content Moderator (Önizleme) üzerinde kaydırıcı vurgulanır ve 4 İnceleme olarak ayarlanır. Tümünü ve siyah beyaz anahtarları bulanıklaştırma vurgulanır ve her ikisi de ayarlanır.](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>Gözden geçirme sayısı

Sayfada görüntülenmesini istediğiniz gözden geçirmeler sayısını ayarlamak için sağ üstteki kaydırıcıyı kullanın.

### <a name="view-type"></a>Görünüm türü

Farklı içerik girişlerini kutucuk olarak veya ayrıntılı bir görünümde görüntüleyebilirsiniz. **Ayrıntı** görünümü, seçili video hakkında önemli çerçeveleri ve diğer bilgileri görmenizi sağlayacak. 

> [!NOTE]
> Video denetleme hizmeti, düzenli aralıklarla çerçeve almak yerine yalnızca tamamlanmış olabilecek (iyi) çerçeveleri tanımlar ve verir. Bu özellik, çerçeve düzeyinde yetişkinlere yönelik ve hızlı analizler için verimli çerçeve oluşturulmasına olanak sağlar.

**Döşenmiş** görünüm her videoyu tek bir kutucuk olarak gösterir. Videoyu büyütmek ve diğerlerini gizlemek için bir video çerçevesinin üzerindeki Genişlet düğmesini seçin.

### <a name="content-obscuring-effects"></a>Content-obscuring etkileri

Bu Content-obscuring efektlerini ayarlamak için **Tümünü Bulanıklaştır** ve **siyah beyaz** geçiş tuşlarını kullanın. Varsayılan olarak açıktır. **Döşenmiş** görünümde, her bir video için etkileri tek tek değiştirebilirsiniz.

## <a name="check-video-details"></a>Video ayrıntılarını denetle

**Ayrıntı** görünümünde sağ bölmede, size video hakkında ayrıntılar veren çeşitli sekmeler gösterilir.

* Videolarınıza özel notlar eklemek için **Notlar** sekmesini seçin.
*  &mdash; Hizmetin videodaki herhangi bir konuşmayı otomatik olarak çıkaran video dökümünü görmek için dökümü sekmesini seçin. Metnin bir bölümünü seçtiğinizde video oynatıcı videonun bu bölümüne atlayacaktır.
* Video dosyası meta verilerini görüntülemek için **meta veri** sekmesini seçin.
* İnceleme geçmişini (oluşturulduğu tarih ve nasıl değiştirildiği gibi) görmek için **Geçmiş** sekmesini seçin.

> [!div class="mx-imgBorder"]
> ![Sağ bölme vurgulanır ve notlar sekmesi seçilidir. Bir test alanı etiketi, bir Note ekleyin.](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>Denetleme etiketlerini Uygula

Video incelemesinin ana görevi videolarda veya videoların parçaları üzerinde denetleme etiketleri uygulamak veya kaldırmak.

### <a name="bulk-tagging"></a>Toplu etiketleme

**Toplu Etiketler** araç çubuğu, birden fazla seçili videoya tek seferde Etiketler eklemenize olanak tanır. Bir veya daha fazla video seçip uygulamak istediğiniz etiketleri seçin ve **Gönder**' e tıklayın. 

> [!div class="mx-imgBorder"]
> ![+ Düğmesi toplu Etiketler bölmesinde vurgulanır.](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>Anahtar çerçeve etiketleme

Ayrıca, belirli anahtar çerçevelerine denetleme etiketleri de ekleyebilirsiniz. Anahtar çerçevesi kutucuk bölmesinden çerçeveler ' i seçin ve ardından istediğiniz etiketleri uygulamak için **ana kare etiketleri +** ' ni seçin.

> [!NOTE]
> Hizmet anahtar çerçevelerini ayıklayamadığınızda, anahtar çerçevesi kutucuk bölmesi **kullanılabilir bir çerçeve** göstermez ve anahtar çerçeveler seçme seçeneği gri renkte görünür. Bu durumda, videoya yalnızca bir bütün olarak etiketleri uygulayabilirsiniz ( **video etiketleri +** düğmesini kullanarak).

> [!div class="mx-imgBorder"]
> ![Kutucuk bölmesi, video oynatıcı, ana kare etiketleri bölmesi ve video etiketleri bölmelerinin hepsi gösteriliyor. Ana kare etiketleri + ve video etiketleri + düğmeleri vurgulanır.](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>Beklemeye bir gözden geçirme koy

Video bölmesinin alt kısmındaki **tut** düğmesi, geri alma ve daha sonra tamamlayabilmeniz için bir gözden geçirme koymanızı sağlar. Bunu, şu anda kullanılamayan başka bir takım üyesi veya yöneticisinden bir danışmayı gerektiren bir gözden geçirme için yapabilirsiniz. 

Ekranın üst kısmındaki **tut** düğmesine tıklayarak videoları beklemeye alabilirsiniz. Tut bölmesi sağ tarafta görünür. Buradan, bekletme sırasında birden fazla inceleme seçebilir ve bunları kuyruğa geri bırakabilir ya da sona erme süresini ayarlayabilirsiniz. Önceden yapılandırılmış sürenin ardından, bekletme gözden geçirmeleri sıraya geri yayımlanır. Şu anda seçili olan süre sonu zamanından önce saymak için **Kaydet** ' i seçin.

> [!div class="mx-imgBorder"]
> ![Video bölmesinde, tut düğmesi vurgulanır. Bölmenin en altında, bir saklama zamanı Birleşik giriş kutusu, serbest bırakma ve kaydetme düğmeleriyle birlikte vurgulanır.](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>İnceleme gönder

Etiketlerinizi uyguladıktan sonra video bölmesinin alt kısmındaki **Gönder** düğmesini seçin. Birden çok video etiketlediyseniz, bunları tek bir gözden geçirme altında veya ayrı incelemeler halinde gönderebilirsiniz.

## <a name="limbo-state"></a>Limbo durumu

Bir gözden geçirmeyi gönderdikten sonra, video, ekranın üst kısmındaki **limbo** düğmesini seçerek görüntüleyebileceğiniz **limbo** durumuna taşınır. Videolar önceden yapılandırılmış bir süre için limbo durumunda kalır (alt kısımdaki menüde değişebilir) veya yeniden gözden geçirilip veya el ile gönderilene kadar olur.

Videoların limbo 'dan sona erdiğinde, İncelemeleri Tamam olarak işaretlenir.

## <a name="next-steps"></a>Sonraki adımlar

- [Video denetleme hızlı](video-moderation-api.md)başlangıcı ile çalışmaya başlayın.
- Aracılı çıkışınızdan insan gözden geçirenler için [video İncelemeleri](video-reviews-quickstart-dotnet.md) oluşturmayı öğrenin.
- Video incelemelerinizi [video dökümü](video-transcript-reviews-quickstart-dotnet.md) ekleme hakkında inceleme.
