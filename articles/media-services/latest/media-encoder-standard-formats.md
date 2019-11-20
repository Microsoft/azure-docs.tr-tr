---
title: Standart kodlayıcı biçimleri ve codec bileşenleri-Azure
description: Bu konu, standart kodlayıcı biçimleri ve codec bileşenleri için bir genel bakış sunar.
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
ms.date: 02/10/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 195bbb70ac5062c6bbf6034e6a6e9abac018c62f
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677967"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standart kodlayıcı biçimleri ve codec bileşenleri

Bu makale, [Standardencoderönayar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)ile kullanabileceğiniz en yaygın içe aktarma ve dışa aktarma dosyası biçimlerinin bir listesini içerir. **Standardencoderönayar**kullanarak özel ön ayarlar oluşturma hakkında bilgi için bkz. [özel önayar ile dönüşüm oluşturma](customize-encoder-presets-how-to.md).

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

### <a name="audio-formats-in-input-containers"></a>Giriş kapsayıcılarındaki ses biçimleri

Standart kodlayıcı, giriş kapsayıcılarında aşağıdaki ses biçimlerinin kullanılmasını destekler:

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
| HEVC/H. 265| Ana profil|

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

## <a name="next-steps"></a>Sonraki adımlar

[Özel önayar olan bir dönüşüm oluşturma](customize-encoder-presets-how-to.md)
