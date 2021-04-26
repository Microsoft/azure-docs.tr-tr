---
title: Microsoft Azure Media Services yaygın senaryolar | Microsoft Docs
description: Bu makale Microsoft Azure Media Services senaryolarına genel bakış sunar.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: add2a4aba1f7664b919e88992b2a1d71e8291b53
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226778"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Microsoft Azure Media Services yaygın senaryolar

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. [V3 Media Services](../latest/media-services-overview.md)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure Media Services (AMS), çeşitli istemcilere (TV, PC ve mobil cihazlar gibi) isteğe bağlı olarak veya canlı akış halinde teslim amacıyla video ve ses içeriklerini güvenli bir şekilde karşıya yüklemenizi, depolamanızı, kodlamanızı ve paketlemenizi sağlar.

Bu makalede, içeriğinizi canlı veya isteğe bağlı olarak sunmaya yönelik yaygın senaryolar gösterilmektedir.

## <a name="overview"></a>Genel Bakış

### <a name="prerequisites"></a>Ön koşullar

* Azure hesabı. Bir hesabınız yoksa, yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com).
* Bir Azure Media Services hesabı. Daha fazla bilgi için bkz. [Hesap Oluşturma](media-services-portal-create-account.md).
* İçerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumunda olması gerekir.

    AMS hesabınız oluşturulduğunda, hesabınıza **Durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için akış uç noktasının **Çalışıyor** durumda olması gerekir.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>AMS OData modeline göre geliştirirken sık kullanılan nesneler

Aşağıdaki resimde Media Services OData modeliyle geliştirirken en sık kullanılan nesnelerin bazıları gösterilmektedir.

Resmi tam boyutlu görüntülemek için tıklayın.  

[![Azure Media Services nesne veri modeline göre geliştirilirken en yaygın kullanılan nesnelerden bazılarını gösteren diyagram.](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

Modelin tamamını [buradan](https://m.eet.com/media/1170326/ms-part1.pdf) görüntüleyebilirsiniz.  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Depolama alanında içeriği koruma ve akan medyayı temiz olarak (şifrelenmemiş) teslim etme

![VoD iş akışı](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Yüksek kaliteli bir medya dosyasını bir varlığa yükleyin.

    Karşıya yükleme sırasında içeriğinizi korumak ve depolama alanına geri dönmek için depolama şifreleme seçeneğini varlığınıza uygulama

1. Uyarlamalı bit hızlı bir MP4 dosyaları grubuna kodlayın.

    Rest 'de içeriğinizi korumak için depolama şifreleme seçeneğini çıkış varlığına uygulamak önerilir.

1. Varlık teslim ilkesini (dinamik paketleme tarafından kullanılır) yapılandırın.

    Varlığınıza depolama şifrelemesi uygulanmışsa varlık teslim ilkesini yapılandırmanız **gerekir**.
1. Bir OnDemand bulucu oluşturarak varlığı yayımlayın.
1. Yayımlanan içeriği akışla aktarın.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Depolama alanında içeriği koruma, dinamik olarak şifrelenmiş akan medya teslim etme

![PlayReady ile koruma](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Yüksek kaliteli bir medya dosyasını bir varlığa yükleyin. Varlığa depolama şifrelemesi seçeneğini uygulayın.
1. Uyarlamalı bit hızlı bir MP4 dosyaları grubuna kodlayın. Çıktı varlığına depolama şifrelemesi seçeneğini uygulayın.
1. Kayıttan yürütme sırasında dinamik olarak şifrelenmesini istediğiniz varlık için şifreleme içerik anahtarı oluşturun.
1. İçerik anahtarı yetkilendirme ilkesini yapılandırın.
1. Varlık teslim ilkesini (dinamik paketleme ve dinamik şifreleme tarafından kullanılır) yapılandırın.
1. Bir OnDemand bulucu oluşturarak varlığı yayımlayın.
1. Yayımlanan içeriği akışla aktarın.

## <a name="deliver-progressive-download"></a>Aşamalı indirme teslimi

1. Yüksek kaliteli bir medya dosyasını bir varlığa yükleyin.
1. Tek bir MP4 dosyasına kodlayın.
1. Bir OnDemand veya SAS bulucu oluşturarak varlığı yayımlayın. SAS Bulucu kullanıyorsanız içerik, Azure blob depolama alanından indirilir. Başlangıç durumunda akış uç noktaları olması gerekmez.
1. Aşamalı olarak içerik indirin.

## <a name="delivering-live-streaming-events"></a>Canlı akış olayları teslimi

1. Çeşitli canlı akış protokollerini (RTMP veya Kesintisiz Akış gibi) kullanarak canlı içerik alın.
1. (isteğe bağlı) Akışınızı, bit hızı uyarlamalı akışa kodlayın.
1. Canlı akışınızın önizlemesini görüntüleyin.
1. İçeriği şu şekilde teslim edin:
    1. Yaygın akış protokolleri (örneğin, MPEG DASH, kesintisiz, HLS) doğrudan müşterileriniz için
    1. Daha fazla dağıtım için bir Content Delivery Network (CDN) veya
    1. Alınan içeriği daha sonra akışa almak için kaydedin ve depolayın (Isteğe bağlı video).

Canlı akış yaparken, aşağıdaki yollardan birini seçebilirsiniz:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Şirket içi kodlayıcılardan çoklu bit hızlı canlı akış alan kanallar ile çalışma (doğrudan geçiş)

Aşağıdaki diyagramda, AMS platformunun **doğrudan geçiş** iş akışında rol oynayan başlıca parçaları gösterilmektedir.

!["Doğrudan geçiş" iş akışında bir d S platformunun ana parçalarını gösteren diyagram.](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Daha fazla bilgi için bkz. [Şirket İçi Kodlayıcılardan Çoklu Bit Hızlı Canlı Akış Alan Kanallar ile Çalışma](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Azure Media Services ile gerçek zamanlı kodlama gerçekleştirmek için etkinleştirilmiş kanallar ile çalışma

Aşağıdaki diyagramda, bir kanalın Media Services ile canlı kodlama yapmak üzere etkinleştirildiği canlı akış iş akışında yer alan AMS platformunun önemli kısımları gösterilmektedir.

![Canlı iş akışı](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Daha fazla bilgi için bkz. [Azure Media Services ile Gerçek Zamanlı Kodlama Gerçekleştirmek İçin Etkinleştirilmiş Kanallar ile Çalışma](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>İçerik kullanma

Azure Media Services, şunlar dahil olmak üzere çoğu platform için zengin ve dinamik istemci oynatıcı uygulamaları oluştururken ihtiyacınız olan araçları sağlar: iOS Cihazları, Android Cihazları, Windows, Windows Phone, Xbox ve Alıcı kutuları.

## <a name="enabling-azure-cdn"></a>Azure CDN'yi etkinleştirme

Media Services, Azure CDN ile tümleştirmeyi destekler. Azure CDN'yi etkinleştirme hakkında daha fazla bilgi için bkz. [Media Services Hesabında Akış Uç Noktalarını Yönetme](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Media Services hesabını ölçeklendirme

AMS müşterileri akış uç noktalarını, medya işleme ve depolamayı kendi AMS hesaplarında ölçeklendirebilir.

* Media Services müşterileri **Standart** akış uç noktası veya **Premium** akış uç noktası seçebilir. **Standart** akış uç noktası çoğu akış iş yükü için uygundur. **Premium** akış uç noktaları ile aynı özellikleri taşır ve giden bant genişliğini otomatik olarak ölçeklendirir.

    **Premium** akış uç noktaları, adanmış ve ölçeklenebilir bant genişliği kapasitesi sağlar; dolayısıyla gelişmiş iş yükleri için uygundur. **Premium** akış uç noktası olan müşteriler, varsayılan olarak bir akış birimi (SU) alır. Akış uç noktası, SU’lar eklenerek ölçeklendirilebilir. Her SU, uygulamaya ek bant genişliği kapasitesi sağlar. **Premium** akış uç noktalarını ölçeklendirme hakkında daha fazla bilgi için, [Akış uç noktalarını ölçeklendirme](media-services-portal-scale-streaming-endpoints.md) konusuna bakın.

* Media Services hesabı bir Ayrılmış Birim Türüyle ilişkilendirilir ve bu da medya işleme görevlerinizin ne hızda işleneceğini belirler. Şu ayrılmış birim türleri arasından seçim yapabilirsiniz: **S1**, **S2** veya **S3**. Örneğin, aynı kodlama işi **S2** ayrılmış birim türünü kullandığınızda **S1** türüne göre daha hızlı çalışır.

    Ayrılmış birim türünü belirtmenin yanı sıra, hesabınızı **ayrılmış birimler** (ru) ile sağlamayı belirtebilirsiniz. Sağlanan RU sayısı, verili bir hesapta eşzamanlı olarak işlenebilecek medya görevlerinin sayısını belirler.

    > [!NOTE]
    > RU, tüm medya işlemesini paralel hale getirmek için çalışır ve Azure Media Indexer’ın kullanıldığı dizin oluşturma işleri de buna dahildir. Bununla birlikte kodlamadan farklı olarak, dizin oluşturma işleri daha hızlı ayrılmış birimlerde daha hızlı işlenmez.

    Daha fazla bilgi için bkz. [medya Işlemeyi ölçeklendirme](media-services-portal-scale-media-processing.md).

* Media Services hesabınızı, depolama hesapları ekleyerek de ölçeklendirebilirsiniz. Her depolama hesabı 500 TB ile sınırlıdır. Daha fazla bilgi için bkz. [Depolama hesaplarını yönetme](./media-services-managing-multiple-storage-accounts.md).

## <a name="next-steps"></a>Sonraki adımlar

[Media Services v3’e geçirme](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
