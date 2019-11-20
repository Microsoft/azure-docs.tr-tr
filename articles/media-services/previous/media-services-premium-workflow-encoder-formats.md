---
title: Media Encoder Premium Workflow biçimleri ve codec 'leri | Microsoft Docs
description: Bu konu, Media Encoder Premium Workflow biçimleri biçimleri ve codec bileşenleri için bir genel bakış sağlar
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
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 87cd7c63939331190530a46071a6b4c40480562f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792581"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Media Encoder Premium Workflow biçimleri ve codec bileşenleri

> [!NOTE]
> Bu konuda tartışılan Media Encoder Premium Workflow medya işlemcisi Çin 'de bulunmamaktadır. 

Bu belge, **Media Encoder Premium Workflow** Kodlayıcısı 'nın genel önizleme sürümü tarafından desteklenen giriş ve çıkış dosyası biçimlerinin ve codec bileşenlerinin bir listesini içerir.

[Media Encoder Premium Workflow giriş biçimleri ve codec bileşenleri](#input_formats)

Media Encoder Premium Workflow çıkış biçimleri ve codec bileşenleri

**Media Encoder Premium Workflow** , [Bu](#closed_captioning) bölümde açıklanan kapalı açıklamalı alt yazıları destekler. 

## <a id="input_formats"></a>Media Encoder Premium Workflow giriş biçimleri ve codec bileşenleri

Aşağıdaki bölümde, bu medya işlemcisinin giriş olarak desteklediği codec bileşenleri ve dosya biçimleri listelenmektedir.

### <a name="input-containerfile-formats"></a>Giriş kapsayıcısı/dosya biçimleri

* Adobe® Flash® F4V
* MXF/SMPTE 377D
* GXF
* MPEG-2 aktarım akışları
* MPEG-2 program akışları
* MPEG-4/MP4
* Windows Media/ASF
* AVI (sıkıştırılmamış 8bit/10bit)

### <a name="input-video-codecs"></a>Giriş video codec bileşenleri

* AVC 8 bit/10 bit, AVCIntra dahil olmak üzere 4:2:2 'e kadar
* AVID DNxHD (MXF içinde)
* DVCPro/DVCProHD (MXF içinde)
* HEVC/H. 265, Main ve ana 10 profili
* JPEG2000
* MPEG-2 (422 profile ve yüksek düzeyine kadar; XDCAM, XDCAM HD, XDCAM IMX, CableLabs® ve D10 gibi çeşitler dahil)
* MPEG-1
* Windows Media videosu/VC-1

### <a name="input-audio-codecs"></a>Giriş sesi codec bileşenleri

* AES (SMPTE 331M ve 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE ve AAC-HEv2; 5,1 'e kadar)
* MPEG Katman 2
* MP3 (MPEG-1 ses katmanı 3)
* Windows Media Audio
* WAV/PCM

## <a id="output_format"></a>Media Encoder Premium Workflow çıkış biçimleri ve codec bileşenleri

Aşağıdaki bölümde, bu medya işlemcisinin çıktısı olarak desteklenen codec bileşenleri ve dosya biçimleri listelenmektedir.

### <a name="output-containerfile-formats"></a>Çıkış kapsayıcısı/dosya biçimleri

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM ve AS02)
* DPP (AS11 dahil)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (sıkıştırılmamış 8bit/10bit)
* Kesintisiz Akış dosya biçimi (pff 1,3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Çıkış video codec bileşenleri

* AVC (H., 8 bit; en fazla yüksek profile, düzey 5,2; 4K Ultra HD; AVC Içi)
* AVID DNxHD (MXF içinde)
* DVCPro/DVCProHD (MXF içinde)
* MPEG-2 (422 profile ve yüksek düzeyine kadar; XDCAM, XDCAM HD, XDCAM IMX, CableLabs® ve D10 gibi çeşitler dahil)
* MPEG-1
* Windows Media videosu/VC-1
* JPEG küçük resim oluşturma
* HEVC (H. 265; 8 bit ve 10 bit, ana ve ana 10 profil)


### <a name="output-audio-codecs"></a>Çıkış ses codec bileşenleri

* AES (SMPTE 331M ve 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) 7,1 'e kadar
* AAC (AAC-LC, AAC-HE ve AAC-HEv2; 5,1 'e kadar)
* MPEG Katman 2
* MP3 (MPEG-1 ses katmanı 3)
* Windows Media Audio

>[!NOTE]
>Dolby® Digital (AC3) olarak kodlarsanız, çıkış yalnızca bir ISO MP4 dosyasına yazılabilir.

## <a id="closed_captioning"></a>Kapalı açıklamalı altyazı desteği

Alma sırasında **Media Encoder Premium Workflow** şunları destekler:

1. SCC dosyaları
2. SMPTE-TT dosyaları
3. CEA-CıL/CEA-708 – Kullanıcı verileri (SEı mesajları H. GXF öğesel akışlar, ATSC/53, SCTE20) veya MXF/dosyalarında ek veri olarak taşınan
4. STL alt başlık dosyaları

Çıkışta aşağıdaki seçenekler kullanılabilir:

1. CEA-608-CEA-708 çevirisi
2. CEA-fer/CEA-708 pass-(SEı iletilerinde H. ısemarya
3. SCC
4. SMPTE zaman aşımına uğrayan metin (bkz. kaynak CEA-608/SMPTE RP2052; DFXP dosya oluşturma dahil)
5. SRT alt başlık dosyası
6. DVB alt başlık akışları

> [!NOTE]
> Yukarıdaki çıkış biçimlerinin hepsi, Azure Media Services akış aracılığıyla teslim için desteklenmez.

## <a name="known-issues"></a>Bilinen sorunlar

Giriş videonuz kapalı açıklamalı alt yazı içermiyorsa, çıkış varlığı hala boş bir TTML dosyası içerecektir. 

## <a name="need-help"></a>Yardım mı gerekiyor?

[Yeni destek isteğine](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) giderek bir destek bileti açabilirsiniz
## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirim sağlayın

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

