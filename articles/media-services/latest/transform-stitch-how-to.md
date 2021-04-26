---
title: .NET ile iki veya daha fazla video dosyasını bitişme | Microsoft Docs
description: Bu makalede, iki veya daha fazla video dosyasının nasıl bitişyapılacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eacc366961d3101a7eaf8877a34ef2d462ea76b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282251"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>.NET ile iki veya daha fazla video dosyasını bitişme

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>İki veya daha fazla video dosyasını Birleştir

Aşağıdaki örnek, iki veya daha fazla video dosyasını bitişme için nasıl önayar oluşturabileceğiniz gösterilmektedir. En yaygın senaryo, ana videoya bir üst bilgi veya bir artbilgisi eklemek istemektir.

> [!NOTE]
> Birlikte düzenlenmiş video dosyaları özellikleri paylaşmalıdır (video çözünürlüğü, kare hızı, ses izleme sayısı, vb.). Farklı kare hızlarındaki videoları veya farklı sayıda ses parçasını karıştırmamak için dikkatli olmanız gerekir.

## <a name="prerequisites"></a>Önkoşullar

[Media Services .net örneklerini](https://github.com/Azure-Samples/media-services-v3-dotnet/)kopyalayın veya indirin.  Aşağıda başvurulan kod [EncodingWithMESCustomStitchTwoAssets klasöründe](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)bulunur.

### <a name="net-code"></a>.NET kodu

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)]
