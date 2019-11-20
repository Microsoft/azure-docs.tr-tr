---
title: Windows Azure Media Encoder 'dan Media Encoder Standard 'e geçiş | Microsoft Docs
description: Bu konuda, Azure Medya Kodlayıcısı 'ndan Media Encoder Standard medya işlemcisine nasıl geçiş yapılacağı anlatılmaktadır.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 1f4760713eccd612014f6b75a1623dd9ad0c8c0f
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595497"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Windows Azure Medya Kodlayıcısı 'ndan Media Encoder Standard 'e geçiş

Bu makalede, 30 Kasım 2019 ' de devre dışı bırakılmakta olan eski Windows Azure Medya Kodlayıcısı (WAME) medya işlemcisinden, Media Encoder Standard medya işlemcisine geçiş adımları ele alınmaktadır.

Dosyaları WAME ile kodlarken müşteriler genellikle `H264 Adaptive Bitrate MP4 Set 1080p` gibi adlandırılmış bir ön ayar dizesi kullanıyordu. Geçiş yapmak için, kodunuzun WAME yerine **Media Encoder Standard** medya işlemcisini kullanacak şekilde güncelleştirilmesi ve `H264 Multiple Bitrate 1080p` gibi eşdeğer [sistem önayarlarından](media-services-mes-presets-overview.md) biri olması gerekir. 

## <a name="migrating-to-media-encoder-standard"></a>Media Encoder Standard geçiriliyor

Eski bileşeni kullanan tipik C# bir kod örneği aşağıda verilmiştir. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Media Encoder Standard kullanan güncelleştirilmiş sürüm aşağıda verilmiştir.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Gelişmiş senaryolar 

Kendi şemasını kullanarak WAME için kendi kodlama ön ayarını oluşturduysanız, [Media Encoder Standard için eşdeğer bir şema](media-services-mes-schema.md)vardır.

## <a name="known-differences"></a>Bilinen farklar 

Media Encoder Standard daha sağlam, güvenilir, daha iyi performansa sahiptir ve eski WAME kodlayıcısından daha iyi bir kalite çıkışı üretir. Ayrıca: 

* Media Encoder Standard, WAME 'dan farklı bir adlandırma kuralına sahip çıktı dosyaları üretir.
* Media Encoder Standard, [giriş dosyası meta verilerini](media-services-input-metadata-schema.md) ve [Çıkış dosyası meta verilerini](media-services-output-metadata-schema.md)içeren dosyalar gibi yapıtlar üretir.
* [Fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/media-services/#encoding) belgelendiği gibi (özellikle SSS bölümünde), Media Encoder Standard kullanarak videoları kodlarken, çıktı olarak üretilen dosyaların süresine göre faturalandırılırsınız. WAME ile, giriş video dosyalarının ve çıkış video dosyalarının boyutlarına göre faturalandırılırsınız.

## <a name="need-help"></a>Yardım mı gerekiyor?

[Yeni destek isteğine](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) giderek bir destek bileti açabilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

* [Eski bileşenler](legacy-components.md)
* [Fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/media-services/#encoding)
