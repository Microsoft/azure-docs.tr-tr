---
title: Azure portal kullanarak medya işlemeyi ölçeklendirin | Microsoft Docs
description: Bu öğretici, Azure portal kullanarak medya işlemeyi ölçeklendirme adımlarında size yol gösterir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 49c3899b912a88605e9269cdb1c34c7e18ed5247
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009734"
---
# <a name="change-the-reserved-unit-type"></a>Ayrılmış birim türünü değiştirme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Genel Bakış

Media Services hesabı bir Ayrılmış Birim Türüyle ilişkilendirilir ve bu da medya işleme görevlerinizin ne hızda işleneceğini belirler. Şu ayrılmış birim türleri arasından seçim yapabilirsiniz: **S1**, **S2** veya **S3**. Örneğin, aynı kodlama işi **S2** ayrılmış birim türünü kullandığınızda **S1** türüne göre daha hızlı çalışır.

Ayrılmış birim türünü belirtmenin yanı sıra, hesabınızı **ayrılmış birimler** (ru) ile sağlamayı belirtebilirsiniz. Sağlanan RU sayısı, verili bir hesapta eşzamanlı olarak işlenebilecek medya görevlerinin sayısını belirler.

>[!NOTE]
>RU, tüm medya işlemesini paralel hale getirmek için çalışır ve Azure Media Indexer’ın kullanıldığı dizin oluşturma işleri de buna dahildir. Bununla birlikte kodlamadan farklı olarak, dizin oluşturma işleri daha hızlı ayrılmış birimlerde daha hızlı işlenmez.

> [!IMPORTANT]
> Medya işleme konusunu ölçeklendirme hakkında daha fazla bilgi edinmek için [genel bakış](media-services-scale-media-processing-overview.md) konusunu gözden geçirdiğinizden emin olun.
> 
> 

## <a name="scale-media-processing"></a>Medya işlemeyi ölçeklendirme
Ayrılmış birim türünü ve ayrılmış birim sayısını değiştirmek için aşağıdakileri yapın:

1. [Azure portalında](https://portal.azure.com/) Azure Media Services hesabınızı seçin.
2. **Ayarlar** penceresinde, **medya ayrılmış birimleri**' ni seçin.
   
    Seçilen ayrılmış birim türü için ayrılan birim sayısını değiştirmek için, ekranın üst kısmındaki **medyaya sunulan birimler** kaydırıcısını kullanın.
   
    **Ayrılmış bırım türünü** değiştirmek için **ayrılan Işleme birimleri çubuğunun hızına** tıklayın. Ardından, ihtiyacınız olan fiyatlandırma katmanını seçin: S1, S2 veya S3.
   
3. Yaptığınız değişiklikleri kaydetmek için KAYDET düğmesine basın.
   
    Kaydet 'e bastığınızda yeni ayrılmış birimler ayrılır.

## <a name="next-steps"></a>Sonraki adımlar
Media Services öğrenme yollarını gözden geçirin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
