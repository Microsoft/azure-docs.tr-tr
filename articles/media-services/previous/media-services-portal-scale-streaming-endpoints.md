---
title: Akış uç noktalarını Azure portal ölçeklendirin | Microsoft Docs
description: Bu öğretici, Azure portal akış uç noktalarını ölçeklendirme adımlarında size yol gösterir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 4db53d64131e6fb43ce425cf579d79e62775754a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009768"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Azure portal ile akış uç noktalarını ölçeklendirme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Genel Bakış

> [!NOTE]
> Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

**Premium** akış uç noktaları, adanmış ve ölçeklenebilir bant genişliği kapasitesi sağlar; dolayısıyla gelişmiş iş yükleri için uygundur. **Premium** akış uç noktası olan müşteriler, varsayılan olarak bir akış birimi (SU) alır. Akış uç noktası, SU’lar eklenerek ölçeklendirilebilir. Her SU, uygulamaya ek bant genişliği kapasitesi sağlar. Akış uç noktası türleri ve CDN yapılandırması hakkında daha fazla bilgi için bkz. [akış uç noktası genel bakış](media-services-streaming-endpoints-overview.md) konusu.
 
Bu konuda, bir akış uç noktasının nasıl ölçeklenmesi gösterilmektedir.

Fiyatlandırma ayrıntıları hakkında daha fazla bilgi için bkz. [Media Services fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="scale-streaming-endpoints"></a>Akış uç noktalarını ölçeklendirme

Akış birimlerinin sayısını değiştirmek için aşağıdakileri yapın:

1. [Azure portalında](https://portal.azure.com/) Azure Media Services hesabınızı seçin.
2. **Ayarlar** penceresinde, **akış uç noktaları**' nı seçin.
3. Ölçeklendirmek istediğiniz akış uç noktasına tıklayın. 

    > [!NOTE] 
    > Yalnızca **Premium** akış uç noktalarını ölçekleyebilirsiniz.

4. Akış birim sayısını belirtmek için kaydırıcıyı taşıyın.

    ![Akış uç noktası](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Sonraki adımlar
Media Services öğrenme yollarını gözden geçirin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

