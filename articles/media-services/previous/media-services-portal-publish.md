---
title: Azure portal içerik yayımlama | Microsoft Docs
description: Bu öğretici, Azure portal içeriğinizi yayımlama adımlarında size yol gösterir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c22570153200b9daeae44701c814faa1a28916c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009819"
---
# <a name="publish-content-in-the-azure-portal"></a>Azure portal içerik yayımlama

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Genel Bakış
> [!NOTE]
> Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. Ayrıntılar için bkz. [Azure Ücretsiz deneme](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Kullanıcınıza içeriğinizin akışını sağlamak veya indirmek için kullanılabilecek bir URL sağlamak üzere, önce bulucu oluşturarak varlığınızı “yayımlamanız” gerekir. Konumlandırıcı varlık dosyalarına erişim sağlar. Azure Media Services iki tür bulucuyu destekler: 

* **Akış (OnDemandOrigin) bulucuları**. Akış bulucuları, uyarlamalı akış için kullanılır. Uyarlamalı akışa örnek olarak, Apple HTTP Canlı Akışı (HLS), Microsoft Kesintisiz Akış ve HTTP üzerinden dinamik uyarlamalı akış (MPEG-DASH olarak da adlandırılır) verilebilir. Akış bulucusu oluşturmak için varlığınız bir .ism dosyası içermelidir. Örneğin, `http://amstest.streaming.mediaservices.windows.net/61b3da1d-96c7-489e-bd21-c5f8a7494b03/scott.ism/manifest`.
* **Aşamalı (paylaşılan erişim imzası) bulucuları**. Aşamalı bulucular, videoları aşamalı indirme aracılığıyla sunmak için kullanılır.

HLS akış URL 'SI oluşturmak için, URL 'ye *(format = M3U8-AAPL)* ekleyin:

`{streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)`

Kesintisiz Akış varlıklarını oynatacak akış URL’sini oluşturmak için aşağıdaki URL biçimini kullanın:

`{streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest`

MPEG DASH akış URL’si oluşturmak için, URL’ye *(format=mpd-time-csf)* ekleyin.

`{streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)`

Paylaşılan erişim imzası URL'si aşağıdaki biçime sahiptir:

`{blob container name}/{asset name}/{file name}/{shared access signature}`

Daha fazla bilgi için bkz. [içerik sunma genel bakış](media-services-deliver-content-overview.md).

> [!NOTE]
> Azure portalında Mart 2015 öncesinde oluşturulmuş olan bulucuların iki yıllık sona erme tarihi vardır.  
> 
> 

Bir bulucunun sona erme tarihini güncelleştirmek için, kullanımı bir [REST API](/rest/api/media/operations/locator#update_a_locator) veya [.NET API 'si](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator)kullanabilir. 

> [!NOTE]
> Paylaşılan erişim imzası bulucunun sona erme tarihini güncelleştirdiğinizde URL değişir.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Bir varlık yayımlamak için portal kullanmak üzere
1. [Azure portalında](https://portal.azure.com/) Azure Media Services hesabınızı seçin.
2. **Ayar**  >  **varlıkları**' nı seçin. Yayımlamak istediğiniz varlığı seçin.
3. **Yayımla** düğmesini seçin.
4. Bulucu türünü seçin.
5. **Add (Ekle)** seçeneğini belirleyin.
   
    ![Videoyu yayımlama](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL **Yayımlanan URL’ler** listesine eklenir.

## <a name="play-content-in-the-portal"></a>Portalda içerik yürütme
Videonuzu Azure portalında bir içerik oynatıcıda test edebilirsiniz.

Videoyu seçin ve ardından **Oynat** düğmesini seçin.

![Videoyu Azure portalında oynatma](./media/media-services-portal-vod-get-started/media-services-play.png)

Bazı dikkate alınması gereken noktalar vardır:

* Videonun yayımlandığından emin olun.
* Azure portal medya oynatıcı varsayılan akış uç noktasından oynatılır. Varsayılan olmayan bir akış uç noktasından oynatmak istiyorsanız, URL'yi seçip kopyalayın ve başka bir oynatıcıya yapıştırın. Örneğin [Azure Media Player](https://aka.ms/azuremediaplayer) üzerinde videonuzu test edebilirsiniz.
* Akış olduğunuz akış uç noktasının çalışıyor olması gerekir.  

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]