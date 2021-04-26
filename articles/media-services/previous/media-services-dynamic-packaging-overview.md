---
title: Azure Media Services dinamik paketlemeye genel bakış | Microsoft Docs
description: Bu makaleler Microsoft Azure Media Services dinamik paketlemeye genel bakış sunar.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0358f7a6591309ad9ffa7263cdee417647a8fa2b
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108175"
---
# <a name="dynamic-packaging"></a>Dinamik paketleme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Media Services sürümünü seçin:"]
> * [Sürüm 3](../latest/encode-dynamic-packaging-concept.md)
> * [Sürüm 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure Media Services birçok medya kaynak dosya biçimini, medya akış biçimlerini ve içerik koruma biçimlerini çeşitli istemci teknolojilerine (örneğin, iOS, XBOX, Silverlight, Windows 8) dağıtmak için kullanılabilir. Bu istemciler farklı protokolleri anlamış, örneğin iOS için bir HTTP Canlı Akışı (HLS) V4 biçimi ve Silverlight ve Xbox için Kesintisiz Akış gerekir. MPEG DASH, HLS veya Kesintisiz Akış anlayan istemcilere sağlamak istediğiniz bir uyarlamalı bit hızı (çoklu bit hızı) MP4 (ISO tabanlı medya 14496-12) dosyası veya bir dizi Uyarlamalı bit hızı Kesintisiz Akış, Media Services dinamik paketlemeden yararlanabilirsiniz.

Dinamik paketleme sayesinde, bir dizi Uyarlamalı bit hızı MP4 dosyası veya Uyarlamalı bit hızı Kesintisiz Akış dosya içeren bir varlık oluşturmanız gerekir. Daha sonra, bildirim veya parça isteğindeki belirtilen biçime bağlı olarak, Isteğe bağlı akış sunucusu akışı seçtiğiniz protokolde almanızı sağlayacaktır. Bunu sonucunda, dosyaları yalnızca tek bir depolama biçiminde depolamanız ve buna göre ödeme yapmanız gerekir. Media Services hizmeti, istemciden gelen isteklere göre uygun yanıtı derler ve sunar.

Aşağıdaki diyagramda geleneksel kodlama ve statik paketleme iş akışı gösterilmektedir.

![Statik kodlama](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Aşağıdaki diyagramda, dinamik paketleme iş akışı gösterilmektedir.

![Dinamik kodlama](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Ortak senaryo

1. Bir giriş dosyasını (Mezzanine dosyası olarak adlandırılır) karşıya yükleyin. Örneğin, H. bir, MP4 veya WMV (desteklenen biçimlerin listesi için [, Media Encoder Standard Desteklenen biçimleri](media-services-media-encoder-standard-formats.md)görebilirsiniz.
2. Mezzanine dosyanızı H., ve bu Uyarlamalı bit hızı kümelerine kodlayın.
3. Isteğe bağlı Bulucu oluşturarak Uyarlamalı bit hızı MP4 kümesini içeren varlığı yayımlayın.
4. İçeriğinizi içeriğe erişmek ve akışa almak için akış URL 'Leri oluşturun.

## <a name="preparing-assets-for-dynamic-streaming"></a>Dinamik akış için varlıkları hazırlama

Varlığınızı dinamik akış için hazırlamak üzere aşağıdaki seçenekleriniz vardır:

- [Ana dosyayı karşıya yükleyin](media-services-dotnet-upload-files.md).
- [Media Encoder Standard kodlayıcısını kullanarak H. ıCMP4 Uyarlamalı bit hızı kümeleri oluşturun](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Içeriğinizi akışa](media-services-deliver-content-overview.md)ın.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Dinamik paketleme tarafından desteklenen ses codec bileşenleri

Dinamik paketleme, [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Gelişmiş AC-3 veya E-AC3), Dolby Atmos veya [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD kayıpsız) ile kodlanmış bir ses içeren MP4 dosyalarını destekler. Dolby Atmos içeriğinin akışı, MPEG-DASH protokolü gibi ortak akış biçimi (CSF) veya ortak medya uygulama biçimi (CMAF) ile birlikte HTTP Canlı Akışı (HLS) aracılığıyla CMAF ile MPEG-DASH protokolü gibi standartlar için desteklenir.

> [!NOTE]
> Dinamik paketleme, [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) ses (eski bir codec) içeren dosyaları desteklemez.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
