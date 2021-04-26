---
title: Akış uç noktalarını Azure portal yönetme | Microsoft Docs
description: Bu makalede, Azure portal ile akış uç noktalarının nasıl yönetileceği gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: bda244267e81d959abbc4ae0a8532ddce68d5726
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017401"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Azure portalı ile akış uç noktalarını yönetme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Bu makalede, akış uç noktalarını yönetmek için Azure portal nasıl kullanılacağı gösterilmektedir. 

>[!NOTE]
>[Genel bakış](media-services-streaming-endpoints-overview.md) makalesini gözden geçirdiğinizden emin olun. 

Akış uç noktasını ölçeklendirme hakkında daha fazla bilgi için [Bu](media-services-portal-scale-streaming-endpoints.md) makaleye bakın.

## <a name="start-managing-streaming-endpoints"></a>Akış uç noktalarını yönetmeye başla 

Hesabınız için akış uç noktalarını yönetmeye başlamak üzere aşağıdakileri yapın.

1. [Azure portalında](https://portal.azure.com/) Azure Media Services hesabınızı seçin.
2. **Ayarlar** dikey penceresinde, **akış uç noktaları**' nı seçin.
   
    !["Ayarlar" dikey penceresinden "akış uç noktaları" seçiliyken "Medya Hizmetleri" sayfasını gösteren ekran görüntüsü.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Yalnızca akış uç noktanız çalışır durumdaysa faturalandırılırsınız.

## <a name="adddelete-a-streaming-endpoint"></a>Akış uç noktası ekleme/silme

>[!NOTE]
>Varsayılan akış uç noktası silinemiyor.

Azure portal kullanarak akış uç noktası eklemek/silmek için aşağıdakileri yapın:

1. Bir akış uç noktası eklemek için sayfanın üst kısmındaki **+ uç noktasına** tıklayın. 

    Farklı CDNs veya CDN ve doğrudan erişim planlıyorsanız, birden çok akış uç noktası isteyebilirsiniz.

2. Akış uç noktasını silmek için **Sil** düğmesine basın.      
3. Akış uç noktasını başlatmak için **Başlat** düğmesine tıklayın.
   
    ![Seçili "Endpoint" eylemini ve "akış uç noktası ayrıntıları" bölmesinin görüntülendiğini gösteren ekran görüntüsü.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Akış uç noktasını yapılandırma
Akış uç noktası aşağıdaki özellikleri yapılandırmanıza olanak sağlar:

* Erişim denetimi
* Önbellek denetimi
* Çapraz site erişim ilkeleri

Bu özellikler hakkında ayrıntılı bilgi için bkz. [Streamingendpoint](/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>CDN etkinleştirildiğinde, IP erişimine erişemezsiniz. IP erişimi yalnızca CDN olmadığında geçerlidir.

Akış uç noktasını aşağıdakileri yaparak yapılandırabilirsiniz:

1. Yapılandırmak istediğiniz akış uç noktasını seçin.
2. **Ayarlar**'a tıklayın.

Alanların kısa bir açıklaması aşağıda verilmiştir.

![Akış uç noktası için seçilen "Ayarlar" eylemini gösteren ekran görüntüsü.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maksimum önbellek ilkesi: Bu akış uç noktası aracılığıyla sunulan varlıklar için önbellek ömrünü yapılandırmak için kullanılır. Değer ayarlanmamışsa, varsayılan kullanılır. Varsayılan değerler, doğrudan Azure Storage 'da da tanımlanabilir. Akış uç noktası için Azure CDN etkinleştirilirse, önbellek ilkesi değerini 600 saniyeden az bir değere ayarlayamamalıdır.  
2. İzin verilen IP adresleri: Yayınlanan akış uç noktasına bağlanmasına izin verilecek IP adreslerini belirtmek için kullanılır. IP adresi belirtilmemişse, herhangi bir IP adresi bağlanabilir. IP adresleri tek bir IP adresi (örneğin, ' 10.0.0.1 '), bir IP adresi ve CıDR alt ağ maskesi kullanarak bir IP aralığı (örneğin, ' 10.0.0.1/22 ') veya IP adresi ve noktalı ondalık alt ağ maskesi kullanarak bir IP aralığı (örneğin, ' 10.0.0.1 (255.255.255.0) ') olarak belirtilebilir.
3. Akamai imza üst bilgisi kimlik doğrulaması için yapılandırma: Akamai sunucularından imza üst bilgi kimlik doğrulama isteğinin nasıl yapılandırılacağını belirtmek için kullanılır. Süre sonu UTC 'dir.

## <a name="scale-your-premium-streaming-endpoint"></a>Premium akış uç noktanızı ölçeklendirin

Daha fazla bilgi için [Bu](media-services-portal-scale-streaming-endpoints.md) makaleye bakın.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Azure CDN tümleştirmeyi etkinleştir

Yeni bir hesap oluşturduğunuzda varsayılan akış uç noktası Azure CDN tümleştirme varsayılan olarak etkindir.

Daha sonra CDN 'yi devre dışı bırakmak/etkinleştirmek istiyorsanız, akış uç noktanızın **durdurulmuş** durumda olması gerekir. Azure CDN tümleştirmenin etkinleştirilmesi ve değişikliklerin tüm CDN pop 'larda etkin olması iki saate kadar sürebilir. Ancak, akış uç noktasından kesintiler olmadan akış uç noktanızı ve akışınızı başlatabilir ve tümleştirme tamamlandıktan sonra akış CDN 'den dağıtılır. Sağlama süresi boyunca, akış uç noktanız **Başlangıç** durumunda olur ve performans düşüklüğü gözlemleyebilirsiniz.

CDN tümleştirmesi, Çin ve Federal Kamu bölgeleri dışındaki tüm Azure veri merkezlerinde etkin olur.

Etkinleştirildikten sonra, **Access Control**, * * özel ana bilgisayar adı ve **Akamai imza kimlik doğrulama** yapılandırması devre dışı bırakılır.
 
> [!IMPORTANT]
> Azure CDN ile tümleştirme Azure Media Services standart akış uç noktaları için **Verizon 'tan Azure CDN** uygulanır. Premium akış uç noktaları, tüm **Azure CDN fiyatlandırma katmanları ve sağlayıcıları** kullanılarak yapılandırılabilir. Azure CDN özellikler hakkında daha fazla bilgi için bkz. [CDN 'ye genel bakış](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Diğer konular

* Bir akış uç noktası için CDN etkinleştirildiğinde istemciler doğrudan kaynaktan içerik isteyemezsiniz. İçeriğinizi CDN ile veya CDN olmadan test etmek istiyorsanız, CDN etkin olmayan başka bir akış uç noktası oluşturabilirsiniz.
* Akış uç noktası ana bilgisayar adı CDN etkinleştirildikten sonra aynı kalır. CDN etkinleştirildikten sonra medya hizmetleri iş akışınızda herhangi bir değişiklik yapmanız gerekmez. Örneğin, akış uç noktası ana bilgisayar adı strasbourg.streaming.mediaservices.windows.net ise, CDN etkinleştirildikten sonra aynı ana bilgisayar adı kullanılır.
* Yeni akış uç noktaları için, yalnızca yeni bir uç nokta oluşturarak CDN 'yi etkinleştirebilirsiniz; Mevcut akış uç noktaları için öncelikle uç noktayı durdurup CDN 'yi etkinleştirmeniz/devre dışı bırakmanız gerekir.
* Standart akış uç noktası yalnızca klasik Azure portalı kullanılarak **Verizon standart CDN sağlayıcısı** kullanılarak yapılandırılabilir. Ancak REST API 'Leri kullanarak diğer Azure CDN sağlayıcıları etkinleştirebilirsiniz.

## <a name="configure-cdn-profile"></a>CDN profilini yapılandırma

Üst kısımdaki **CDN 'Yi Yönet** DÜĞMESINI seçerek CDN profilini yapılandırabilirsiniz.

![Akış uç noktası](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Sonraki adımlar
Media Services öğrenme yollarını gözden geçirin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
