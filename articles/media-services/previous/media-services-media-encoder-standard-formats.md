---
title: Media Encoder Standard biçimleri ve codec bileşenleri-Azure
description: Bu konu, Media Encoder Standard biçimlerine ve codec larına genel bakış sunar.
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
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 8fd68269f73506c2d605c0cb4560e6a57e1a2128
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016534"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard Biçimleri ve Kodlayıcılar

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Media Services sürümünü seçin:"]
> * [Sürüm 2](media-services-media-encoder-standard-formats.md)
> * [Sürüm 3](../latest/media-encoder-standard-formats.md)

Bu belge, Media Encoder Standard ile kullanabileceğiniz en yaygın içe aktarma ve dışa aktarma dosyası biçimlerinin bir listesini içerir.

## <a name="input-containerfile-formats"></a>Giriş kapsayıcısı/dosya biçimleri
| Dosya biçimleri (dosya uzantıları) | Desteklenen |
| --- | --- |
| FLV (H., ve AAC codec bileşenleri ile) (. FLV) |Evet |
| MXF (. MXF) |Evet |
| GXF (. GXF) |Evet |
| MPEG2-PS, MPEG2-TS, 3GP (. TS,. PS,. 3GP,. 3GPP,. mpg) |Evet |
| Windows Media videosu (WMV)/ASF (. wmv,. asf) |Evet |
| AVI (sıkıştırılmamış 8bit/10bit) (. avi) |Evet |
| MP4 (. mp4,. M4A,. m4v)/ISMV (. ISA,. ismv) |Evet |
| [Microsoft dijital video kaydı (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (. DVR-MS) |Evet |
| Matroska/webd (. MKV) |Evet |
| DALGA/WAV (. wav) |Evet |
| QuickTime (. mov) |Evet |

> [!NOTE]
> Yukarıda daha yaygın olarak karşılaşılan dosya uzantılarının listesi bulunur. Media Encoder Standard diğerlerini destekler (örneğin:. m2ts,. mpeg2video,. qt). Bir dosyayı kodlamaya çalışırsanız ve desteklenmeyen biçim hakkında bir hata iletisi alırsanız, görüşlerinizi [buraya](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)girin.
> 
> 

### <a name="audio-formats-in-input-containers"></a>Giriş kapsayıcılarındaki ses biçimleri
Media Encoder Standard, giriş kapsayıcılarında aşağıdaki ses biçimlerinin kullanılmasını destekler:

* Araya eklemeli stereo veya 5,1 örnekleri ile ses parçaları bulunan MXF, GXF ve QuickTime dosyaları

veya

* Sesin ayrı PCM parçaları olarak gerçekleştirildiği, ancak kanal eşlemesinin (stereo veya 5,1) dosya meta verilerinden çıkarılabilen MXF, GXF ve QuickTime dosyaları

## <a name="input-video-codecs"></a>Giriş video codec bileşenleri
| Giriş video codec bileşenleri | Desteklenen |
| --- | --- |
| AVC 8 bit/10 bit, AVCIntra dahil olmak üzere 4:2:2 'e kadar |8 bit 4:2:0 ve 4:2:2 |
| AVID DNxHD (MXF içinde) |Evet |
| DVCPro/DVCProHD (in MXF) |Evet |
| Dijital video (DV) (AVI dosyaları) |Evet |
| JPEG 2000 |Evet |
| MPEG-2 (422 profile ve yüksek düzeyine kadar; XDCAM, XDCAM HD, XDCAM IMX, CableLabs® ve D10 gibi çeşitler dahil) |En fazla 422 profil |
| MPEG-1 |Evet |
| VC-1/WMV9 |Evet |
| Canopus HQ/HQX |Hayır |
| MPEG-4 Bölüm 2 |Evet |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Evet |
| YUV420 sıkıştırılmamış veya Mezzanine |Evet |
| Apple ProRes 422 |Evet |
| Apple ProRes 422 LT |Evet |
| Apple ProRes 422 HQ |Evet |
| Apple ProRes proxy |Evet |
| Apple ProRes 4444 |Evet |
| Apple ProRes 4444 XQ |Evet |
| HEVC/H. 265| Ana ve ana 10 (&#42;) profiller<br/>10. ana profil desteği, 8bit 4:2:0 içeriğine yöneliktir. |

## <a name="input-audio-codecs"></a>Giriş sesi codec bileşenleri
| Giriş sesi codec bileşenleri | Desteklenen |
| --- | --- |
| AAC (AAC-LC, AAC-HE ve AAC-HEv2; 5,1 'e kadar) |Evet |
| MPEG Katman 2 |Evet |
| MP3 (MPEG-1 ses katmanı 3) |Evet |
| Windows Media Audio |Evet |
| WAV/PCM |Evet |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Evet |
| [Opus 'lar](https://go.microsoft.com/fwlink/?LinkId=822667) |Evet |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Evet |
| AMR (Uyarlamalı çok hızlı) |Evet |
| AES (SMPTE 331M ve 302M, AES3-2003) |Hayır |
| Dolby® E |Hayır |
| Dolby® Digital (AC3) |Hayır |
| Dolby® Digital Plus (E-AC3) |Hayır |

## <a name="output-formats-and-codecs"></a>Çıkış biçimleri ve codec bileşenleri
Aşağıdaki tabloda, dışarı aktarma için desteklenen codec bileşenleri ve dosya biçimleri listelenmektedir.

| Dosya Biçimi | Video codec bileşeni | Ses codec bileşeni |
| --- | --- | --- |
| MP4 <br/><br/>(çoklu bit hızı MP4 kapsayıcıları dahil) |H. (yüksek, ana ve temel profiller) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H. (yüksek, ana ve temel profiller) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca bkz.
[Azure Media Services ile Isteğe bağlı Içeriği kodlama](media-services-encode-asset.md)

[Media Encoder Standard ile kodlama](media-services-dotnet-encode-with-media-encoder-standard.md)

