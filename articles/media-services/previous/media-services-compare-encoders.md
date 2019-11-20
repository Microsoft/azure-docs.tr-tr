---
title: Azure isteğe bağlı medya kodlayıcılarını karşılaştırma | Microsoft Docs
description: Bu konu, **Media Encoder Standard** ve **Media Encoder Premium Workflow**kodlama yeteneklerini karşılaştırır.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 4767f7bb5ba02c838c0e21721e55a6564a14acd1
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016650"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Azure isteğe bağlı medya kodlayıcılarını karşılaştırma  

Bu konu, **Media Encoder Standard** ve **Media Encoder Premium Workflow**kodlama yeteneklerini karşılaştırır.

## <a name="video-and-audio-processing-capabilities"></a>Video ve ses işleme özellikleri

Aşağıdaki tabloda Media Encoder Standard (MES) ve Media Encoder Premium Workflow (MEPW) arasındaki işlevler karşılaştırılmaktadır. 

|Özellik|Media Encoder Standard|Media Encoder Premium İş Akışı|
|---|---|---|
|Kodlama sırasında koşullu mantık Uygula<br/>(örneğin, giriş HD ise, 5,1 ses kodlayın)|Hayır|Evet|
|Kapalı açıklamalı altyazı|Hayır|[Evet](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® profesyonel ses kullanımı düzeltmesi](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> Iletişim zeka™|Hayır|Evet|
|Tarama, ters telesine|Temel|Yayın kalitesi|
|Siyah kenarlıkları Algıla ve Kaldır <br/>(pillarkutular, harf kutuları)|Hayır|Evet|
|Küçük resim oluşturma|[Evet](media-services-dotnet-generate-thumbnail-with-mes.md)|[Evet](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Videoları kırpma/kırpma ve dikiş|[Evet](media-services-advanced-encoding-with-mes.md#trim_video)|Evet|
|Ses veya videonun Yerpaylaşımları|[Evet](media-services-advanced-encoding-with-mes.md#overlay)|[Evet](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Grafiklerin Yerpaylaşımları|Görüntü kaynaklarından|Görüntü ve metin kaynaklarından|
|Birden çok ses dili parçası|Sınırlı|[Evet](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Her kodlayıcı tarafından kullanılan faturalandırma ölçümü
| Medya Işlemcisi adı | Geçerli fiyatlandırma | Notlar |
| --- | --- | --- |
| **Media Encoder Standard** |KODLAYICISI |Kodlama görevleri, [burada][1]belirtilen HıZDA, kodlayıcı sütunu altında, çıktı olarak üretilen tüm medya dosyalarının dakika cinsinden toplam süresine göre ücretlendirilecektir. |
| **Media Encoder Premium İş Akışı** |PREMIUM KODLAYICI |Kodlama görevleri, [burada][1]BELIRTILEN HıZDA PREMIUM kodlayıcı sütunu altında, çıktı olarak üretilen tüm medya dosyalarının dakika cinsinden toplam süresine göre ücretlendirilecektir. |

## <a name="input-containerfile-formats"></a>Giriş kapsayıcısı/dosya biçimleri
| Giriş kapsayıcısı/dosya biçimleri | Media Encoder Standard | Media Encoder Premium İş Akışı |
| --- | --- | --- |
| Adobe® Flash® F4V |Evet |Evet |
| MXF/SMPTE 377D |Evet |Evet |
| GXF |Evet |Evet |
| MPEG-2 aktarım akışları |Evet |Evet |
| MPEG-2 program akışları |Evet |Evet |
| MPEG-4/MP4 |Evet |Evet |
| Windows Media/ASF |Evet |Evet |
| AVI (sıkıştırılmamış 8bit/10bit) |Evet |Evet |
| 3GPP/3GPP2 |Evet |Hayır |
| Kesintisiz Akış dosya biçimi (pff 1,3) |Evet |Hayır |
| [Microsoft dijital video kaydı (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Evet |Hayır |
| Matroska/webd |Evet |Hayır |
| QuickTime (. mov) |Evet |Hayır |

## <a name="input-video-codecs"></a>Giriş video codec bileşenleri
| Giriş video codec bileşenleri | Media Encoder Standard | Media Encoder Premium İş Akışı |
| --- | --- | --- |
| AVC 8 bit/10 bit, AVCIntra dahil olmak üzere 4:2:2 'e kadar |8 bit 4:2:0 ve 4:2:2 |Evet |
| AVID DNxHD (MXF içinde) |Evet |Evet |
| DVCPro/DVCProHD (in MXF) |Evet |Evet |
| JPEG2000 |Evet |Evet |
| MPEG-2 (422 profile ve yüksek düzeyine kadar; XDCAM, XDCAM HD, XDCAM IMX, CableLabs® ve D10 gibi çeşitler dahil) |En fazla 422 profil |Evet |
| MPEG-1 |Evet |Evet |
| Windows Media videosu/VC-1 |Evet |Evet |
| Canopus HQ/HQX |Hayır |Hayır |
| MPEG-4 Bölüm 2 |Evet |Hayır |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Evet |Hayır |
| Apple ProRes 422 |Evet |Hayır |
| Apple ProRes 422 LT |Evet |Hayır |
| Apple ProRes 422 HQ |Evet |Hayır |
| Apple ProRes proxy |Evet |Hayır |
| Apple ProRes 4444 |Evet |Hayır |
| Apple ProRes 4444 XQ |Evet |Hayır |
| HEVC/H. 265|Ana profil|Ana ve ana 10 profili|

## <a name="input-audio-codecs"></a>Giriş sesi codec bileşenleri
| Giriş sesi codec bileşenleri | Media Encoder Standard | Media Encoder Premium İş Akışı |
| --- | --- | --- |
| AES (SMPTE 331M ve 302M, AES3-2003) |Hayır |Evet |
| Dolby® E |Hayır |Evet |
| Dolby® Digital (AC3) |Hayır |Evet |
| Dolby® Digital Plus (E-AC3) |Hayır |Evet |
| AAC (AAC-LC, AAC-HE ve AAC-HEv2; 5,1 'e kadar) |Evet |Evet |
| MPEG Katman 2 |Evet |Evet |
| MP3 (MPEG-1 ses katmanı 3) |Evet |Evet |
| Windows Media Audio |Evet |Evet |
| WAV/PCM |Evet |Evet |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Evet |Hayır |
| [Opus 'lar](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Evet |Hayır |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Evet |Hayır |

## <a name="output-containerfile-formats"></a>Çıkış kapsayıcısı/dosya biçimleri
| Çıkış kapsayıcısı/dosya biçimleri | Media Encoder Standard | Media Encoder Premium İş Akışı |
| --- | --- | --- |
| Adobe® Flash® F4V |Hayır |Evet |
| MXF (OP1a, XDCAM ve AS02) |Hayır |Evet |
| DPP (AS11 dahil) |Hayır |Evet |
| GXF |Hayır |Evet |
| MPEG-4/MP4 |Evet |Evet |
| MPEG-TS |Evet |Evet |
| Windows Media/ASF |Hayır |Evet |
| AVI (sıkıştırılmamış 8bit/10bit) |Hayır |Evet |
| Kesintisiz Akış dosya biçimi (pff 1,3) |Hayır |Evet |

## <a name="output-video-codecs"></a>Çıkış video codec bileşenleri
| Çıkış video codec bileşenleri | Media Encoder Standard | Media Encoder Premium İş Akışı |
| --- | --- | --- |
| AVC (H., 8 bit; en fazla yüksek profile, düzey 5,2; 4K Ultra HD; AVC Içi) |Yalnızca 8 bit 4:2:0 |Evet |
| HEVC (H. 265; 8 bit ve 10 bit;)  |Hayır |Evet |
| AVID DNxHD (MXF içinde) |Hayır |Evet |
| MPEG-2 (422 profile ve yüksek düzeyine kadar; XDCAM, XDCAM HD, XDCAM IMX, CableLabs® ve D10 gibi çeşitler dahil) |Hayır |Evet |
| MPEG-1 |Hayır |Evet |
| Windows Media videosu/VC-1 |Hayır |Evet |
| JPEG küçük resim oluşturma |Evet |Evet |
| PNG küçük resim oluşturma |Evet |Evet |
| BMP küçük resim oluşturma |Evet |Hayır |

## <a name="output-audio-codecs"></a>Çıkış ses codec bileşenleri
| Çıkış ses codec bileşenleri | Media Encoder Standard | Media Encoder Premium İş Akışı |
| --- | --- | --- |
| AES (SMPTE 331M ve 302M, AES3-2003) |Hayır |Evet |
| Dolby® Digital (AC3) |Hayır |Evet |
| Dolby® Digital Plus (E-AC3) 7,1 'e kadar |Hayır |Evet |
| AAC (AAC-LC, AAC-HE ve AAC-HEv2; 5,1 'e kadar) |Evet |Evet |
| MPEG Katman 2 |Hayır |Evet |
| MP3 (MPEG-1 ses katmanı 3) |Hayır |Evet |
| Windows Media Audio |Hayır |Evet |

>[!NOTE]
>Dolby® Digital (AC3) olarak kodlarsanız, çıkış yalnızca bir ISO MP4 dosyasına yazılabilir.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>İlgili makaleler
* [Media Encoder Standard ön ayarlarını özelleştirerek gelişmiş kodlama görevleri gerçekleştirin](media-services-custom-mes-presets-with-dotnet.md)
* [Kotalar ve sınırlamalar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
