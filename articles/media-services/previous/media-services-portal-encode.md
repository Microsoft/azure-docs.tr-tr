---
title: Azure portal Media Encoder Standard kullanarak bir varlığı kodlayın | Microsoft Docs
description: Bu öğretici, Azure portal Media Encoder Standard kullanarak bir varlığı kodlama adımlarında size yol gösterir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5f4bb3c9b23ffd68939f1088b1252c6e31c1dad7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010516"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Azure portalında Media Encoder Standard kullanarak varlık kodlama

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. Ayrıntılar için bkz. [Azure Ücretsiz deneme](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Azure Media Services ile çalışırken en sık karşılaşılan senaryolardan biri, istemcilerinize bit hızı uyarlamalı akış dağıtmakta. Media Services, aşağıdaki Uyarlamalı bit hızı akış teknolojilerini destekler: Apple HTTP Canlı Akışı (HLS), Microsoft Kesintisiz Akış ve HTTP üzerinden dinamik uyarlamalı akış (kısa çizgi, MPEG-DASH olarak da adlandırılır). Videolarınızı Uyarlamalı bit hızı akışı için hazırlamak üzere öncelikle kaynak videonuzu çoklu bit hızına sahip dosyalar olarak kodlayın. Videolarınızı kodlamak için Media Encoder Standard kullanabilirsiniz.  

Media Services dinamik paketleme olanağı sağlar. Dinamik paketleme ile, bu akış biçimlerinde yeniden paketlemeden HLS, Kesintisiz Akış ve MPEG-DASH içinde çoklu bit hızına sahip MP4 'leri sunabilirsiniz. Dinamik paketleme kullandığınızda dosyalar için tek depolama biçiminde depolama ve ödeme yapabilirsiniz. Media Services bir istemcinin isteğine göre uygun yanıtı oluşturur ve sunar.

Dinamik paketlemeden yararlanmak için kaynak dosyanızı çoklu bit hızına sahip bir dizi MP4 dosyası olarak kodlamanız gerekir. Bu makalenin ilerleyen bölümlerinde kodlama adımları gösterilmektedir.

Medya işlemeyi ölçeklendirme hakkında bilgi edinmek için bkz. [Azure Portal kullanarak medya Işlemeyi ölçeklendirme](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Azure portal kodlayın

Media Encoder Standard kullanarak içeriğinizi kodlamak için:

1. [Azure portalında](https://portal.azure.com/) Azure Media Services hesabınızı seçin.
2. **Ayar**  >  **varlıkları**' nı seçin. Kodlamak istediğiniz varlığı seçin.
3. **Kodla** düğmesini seçin.
4. **Bir varlık kodla** bölmesinde, **Media Encoder Standard** işlemcisini ve bir ön ayarı seçin. Hazır ayarlar hakkında daha fazla bilgi için bkz. [Bit hızı merdivenini otomatik oluşturma](media-services-autogen-bitrate-ladder-with-mes.md) ve [Media Encoder Standard için görev ön ayarları](media-services-mes-presets-overview.md). Girdi videonuz için en iyi sonucu verecek hazır ayarı seçmeniz önemlidir. Örneğin, girdi videonuzun 1920 &#215; 1080 piksel çözünürlüğü olduğunu biliyorsanız, **H264 Çoklu Bit hızı 1080p** ön ayarını kullanabilirsiniz. Düşük çözünürlüklü bir videonuz varsa (640 & #215; 360), **H264 Çoklu Bit Hızı 1080p** ön ayarını kullanmamalısınız.
   
   Kaynaklarınızın yönetmenize yardımcı olmak için çıktı varlığının ve işin adını düzenleyebilirsiniz.
   
   ![Varlıkları kodlama](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. **Oluştur**’u seçin.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
* Azure portal [kodlama işinizin Ilerlemesini izleyin](media-services-portal-check-job-progress.md) .  

