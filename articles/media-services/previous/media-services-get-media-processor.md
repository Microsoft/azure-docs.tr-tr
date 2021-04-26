---
title: .NET için Azure Media Services SDK kullanarak medya işlemcisi oluşturma | Microsoft Docs
description: Azure Media Services için medya içeriğini kodlamak, dönüştürmek, şifrelemek veya şifresini çözmek için bir medya işlemcisi bileşeni oluşturmayı öğrenin. Kod örnekleri C# dilinde yazılır ve .NET için Media Services SDK kullanır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 6ce2a28ff9b34373cc716ea397927ef160bd1097
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013355"
---
# <a name="how-to-get-a-media-processor-instance"></a>Nasıl yapılır: medya Işlemcisi örneği alma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)

## <a name="overview"></a>Genel Bakış

Media Services bir medya işlemcisi, kodlama, biçim dönüştürme, şifreleme veya medya içeriğini çözme gibi belirli bir işleme görevini işleyen bir bileşendir. Medya içeriğini kodlamak, şifrelemek veya dönüştürmek için bir görev oluştururken genellikle bir medya işlemcisi oluşturursunuz.

## <a name="azure-media-processors"></a>Azure Medya işlemcileri

Aşağıdaki konuda medya işlemcilerin listesi verilmiştir:

* [Kodlama medya işleyicileri](scenarios-and-availability.md)
* [Analiz medya işlemcileri](scenarios-and-availability.md)

## <a name="get-media-processor"></a>Medya Işlemcisini al

Aşağıdaki yöntem, bir medya işlemcisi örneğinin nasıl alınacağını gösterir. Kod örneği, [nasıl yapılır: Media Services programlı olarak bağlanma](media-services-use-aad-auth-to-access-ams-api.md)bölümünde açıklandığı gibi sunucu bağlamına başvurmak için **_context** adlı modül düzeyi değişkenin kullanımını varsayar.

```csharp
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Sonraki Adımlar

Bir medya işlemcisi örneğinin nasıl alınacağını öğrenmiş olduğunuza göre, bir varlığı kodlamak için Media Encoder Standard nasıl kullanacağınızı gösteren [bir varlık kodlama](media-services-dotnet-encode-with-media-encoder-standard.md) konusuna gidin.