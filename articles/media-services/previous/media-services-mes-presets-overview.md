---
title: Media Encoder Standard için görev önayarları (MES) | Microsoft Docs
description: Konusu, Media Encoder Standard (MES) için hizmet tarafından tanımlanan örnek Önayarların yanı sıra genel bakış sunar.
author: IngridAtMicrosoft
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 74e557aa02f5ab83828262209653bf8a78fdd2d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011655"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Media Encoder Standard için örnek ön ayarlar (MES)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

**Media Encoder Standard** , kodlama işleri oluştururken kullanabileceğiniz, önceden tanımlanmış bir sistem kodlama önayarı kümesi tanımlar. Media Services ile akış için bir video kodlamak istiyorsanız "uyarlamalı akış" ön ayarını kullanmanız önerilir. Bu önayarı belirttiğinizde Media Encoder Standard, [bit hızı bir el ile otomatik olarak oluşturur](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Örneklerden özel ön ayarlar oluşturma
Media Services, özel kodlama ihtiyaçlarınızı ve gereksinimlerinizi karşılamak için hazır ayarların tüm değerlerini özelleştirmeyi tam olarak destekler. Bir kodlama ön ayarını özelleştirmeniz gerekiyorsa, bu bölümde özel yapılandırmanızın bir şablonu olarak belirtilen sistem ön ayarlarından biriyle başlamanız gerekir. Bu önayarlardır her öğe ve her öğe için geçerli değerler hakkında açıklamalar için [Media Encoder Standard şeması](media-services-mes-schema.md) konusuna bakın.  
  
> [!NOTE]
>  4k kodlama için önceden ayarlanmış bir ayar kullanırken, `S3` ayrılmış birim türünü almalısınız. Daha fazla bilgi için bkz. [kodlamayı ölçeklendirme](./media-services-scale-media-processing-overview.md).  

#### <a name="video-rotation-default-setting-in-presets"></a>Önceden ayarlı video döndürme varsayılan ayarı:
Media Encoder Standard ile çalışırken, video döndürme varsayılan olarak etkindir. Videonuz bir mobil cihazda dikey modda kaydedilmişse, bu Önayarlar kodlama öncesinde yatay moda döndürülür.
 
## <a name="available-presets"></a>Kullanılabilir Önayarlar: 

 [H264 çoklu bit hızı 1080P ses 5,1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) , 6000 kbps ile 400 Kbps ve AAC 5,1 ses arasında değişen 8 GOP hizalanmış MP4 dosyası kümesi oluşturur.  
  
 [H264 çoklu bit hızı 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) , 6000 kbps 'den 400 Kbps 'e ve stereo AAC ses arasında değişen 8 GOP hizalanmış MP4 dosyası kümesi oluşturur.  
  
 [İOS Için H264 çoklu bit hızı 16X9](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) , 8500 kbps 'den 200 kbps 'e ve stereo AAC ses arasında değişen 8 GOP hizalanmış MP4 dosyası kümesi oluşturur.  
  
 [H264 çoklu bit hızı 16X9 SD ses 5,1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) , 1900 kbps ile 400 Kbps ve AAC 5,1 ses arasında değişen 5 GOP hizalanmış MP4 dosyası kümesi oluşturur.  
  
 [H264 çoklu bit hızı 16X9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) , 1900 kbps 'den 400 Kbps 'e ve stereo AAC ses arasında değişen bir dızı 5 GOP hizalanmış MP4 dosyası üretir.  
  
 [H264 çoklu bit hızı 4k Audio 5,1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) , 20000 kbps ile 1000 kbps ve AAC 5,1 sesten oluşan 12 GOP hizalanmış MP4 dosyası kümesi oluşturur.  
  
 [H264 çoklu bit hızı 4k](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) , 20000 kbps 'den 1000 kbps 'e ve stereo AAC ses arasında değişen 12 GOP hizalanmış MP4 dosyası kümesi oluşturur.  
  
 [İOS Için H264 çoklu bit hızı 4x3](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) , 8500 kbps 'den 200 kbps 'e ve stereo AAC sesten oluşan bir dızı 8 GOP hizalanmış MP4 dosyası üretir.  
  
 [H264 çoklu bit hızı 4x3 SD ses 5,1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) , 1600 kbps ile 400 Kbps ve AAC 5,1 sesten oluşan bir dızı 5 GOP hizalanmış MP4 dosyası üretir.  
  
 [H264 çoklu bit hızı 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) , 1600 kbps 'den 400 Kbps 'e ve stereo AAC ses arasında değişen 5 GOP hizalanmış MP4 dosyası kümesi oluşturur.  
  
 [H264 çoklu bit hızı 720p ses 5,1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) , 3400 kbps ile 400 Kbps ve AAC 5,1 ses arasında değişen 6 GOP hizalanmış MP4 dosyası kümesi oluşturur.  
  
 [H264 çoklu bit hızı 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) , 3400 kbps ile 400 Kbps arasında değişen 6 GOP hizalanmış MP4 dosyası kümesi oluşturur ve stereo AAC ses.  
  
 [H264 tekli bit hızı 1080P ses 5,1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) , bit hızı 6750 kbps ve AAC 5,1 ses içeren tek bir MP4 dosyası üretir.  
  
 [H264 tek bit hızı 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) , bit hızı 6750 kbps ve stereo AAC ses içeren tek bir MP4 dosyası üretir.  
  
 [H264 tekli bit hızı 4k Audio 5,1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) , bit hızı 18000 kbps ve AAC 5,1 ses içeren tek bir MP4 dosyası üretir.  
  
 [H264 tek bit hızı 4k](media-services-mes-preset-H264-Single-Bitrate-4K.md) , bit hızı 18000 kbps ve stereo AAC ses içeren tek bir MP4 dosyası üretir.  
  
 [H264 tekli bit hızı 4x3 SD ses 5,1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) , bit hızı 1800 kbps ve AAC 5,1 ses içeren tek bir MP4 dosyası üretir.  
  
 [H264 tekli bit hızı 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) , bit hızı 1800 kbps ve stereo AAC ses içeren tek bir MP4 dosyası üretir.  
  
 [H264 tekli bit hızı 16X9 SD ses 5,1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) , bit hızı 2200 kbps ve AAC 5,1 ses içeren tek bir MP4 dosyası üretir.  
  
 [H264 tek bit hızı 16X9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) , bit hızı 2200 kbps ve stereo AAC ses içeren tek bir MP4 dosyası üretir.  
  
 [H264 tekli bit hızı 720p ses 5,1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) , bit hızı 4500 kbps ve AAC 5,1 ses içeren tek bir MP4 dosyası üretir.  
  
 [Android Için tek bit hızı 720p H264](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) , bit hızı 2000 kbps ve stereo AAC olan tek bir MP4 dosyası üretir.  
  
 [H264 tek bit hızı 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) , bit hızı 4500 kbps ve stereo AAC ses içeren tek bir MP4 dosyası üretir.  
  
 [Android Için H264 tek bit hızı yüksek kalıtelı SD](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) , bit hızı 500 kbps ve stereo AAC ses olan tek bir MP4 dosyası üretir.  
  
 [Android Için H264 tek bit hızı düşük kalıtelı SD](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) , bit hızı 56 Kbps ve stereo AAC ses içeren tek bir MP4 dosyası üretir.  
  
 Media Services kodlayıcılarla ilgili daha fazla bilgi için bkz. [Azure Media Services Ile Isteğe bağlı kodlama](./media-services-encode-asset.md).
