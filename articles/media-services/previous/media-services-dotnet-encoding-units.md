---
title: Kodlama birimleri ekleyerek Medya işlemeyi ölçeklendirme-Azure |  Microsoft Docs
description: Bu makalede, .NET Azure Media Services kodlama birimlerinin nasıl ekleneceği gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: milangada
ms.custom: devx-track-csharp
ms.openlocfilehash: 6feb28858271f11bef0933a352f86d7545ee5bd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014086"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>.NET SDK ile kodlama ölçeklendirme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Genel Bakış
> [!IMPORTANT]
> Medya işlemeyi ölçeklendirme hakkında daha fazla bilgi edinmek için [genel bakışı](media-services-scale-media-processing-overview.md) gözden geçirdiğinizden emin olun.
> 
> 

Ayrılmış birim türünü ve kodlama ayrılmış birim sayısını .NET SDK kullanarak değiştirmek için aşağıdakileri yapın:

```csharp
IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
encodingS1ReservedUnit.Update();
Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

encodingS1ReservedUnit.CurrentReservedUnits = 2;
encodingS1ReservedUnit.Update();

Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);
```

## <a name="opening-a-support-ticket"></a>Destek bileti açma

Varsayılan olarak, her Media Services hesabı en fazla 10 S2 veya S3 medya ayrılmış birimi (MRU) veya 25 S1 MRU ve 5 Isteğe bağlı akışa ayrılan birimler kadar ölçeklendirebilir. Bir destek bileti açarak daha yüksek bir sınır talep edebilirsiniz.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
