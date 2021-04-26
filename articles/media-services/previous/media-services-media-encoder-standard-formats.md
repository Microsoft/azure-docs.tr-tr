---
title: Media Encoder Standard biçimleri ve codec bileşenleri-Azure
description: Bu makale Media Encoder Standard biçimlerine ve codec larına genel bakış sağlar.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: 9055c4c1af14c9ecc8fbdeb621c30ac6ebe5afc4
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108814"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard Biçimleri ve Kodlayıcılar

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Media Services sürümünü seçin:"]
> * [Sürüm 2](media-services-media-encoder-standard-formats.md)
> * [Sürüm 3](../latest/encode-media-encoder-standard-formats-reference.md)

Bu belge, Media Encoder Standard ile kullanabileceğiniz en yaygın içe aktarma ve dışa aktarma dosyası biçimlerinin bir listesini içerir.

## <a name="input-containerfile-formats"></a>Giriş kapsayıcısı/dosya biçimleri
| Dosya biçimleri (dosya uzantıları) | Desteklenir |
| --- | --- |
| FLV (H.264 ve AAC codec bileşenleriyle) (.flv) |Yes |
| MXF    (.mxf) |Yes |
| GXF    (.gxf) |Yes |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Yes |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Yes |
| AVI (Sıkıştırılmamış 8 bit/10 bit) (.avi) |Yes |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Yes |
| [Microsoft Digital Video Recording(DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (.dvr-ms) |Yes |
| Matroska/WebM (.mkv) |Yes |
| WAVE/WAV (.wav) |Yes |
| QuickTime (.mov) |Yes |

> [!NOTE]
> Yukarıda, daha sık karşılaşılan dosya uzantılarının listesi verilmiştir. Media Encoder Standard diğerlerini destekler (örneğin:. m2ts,. mpeg2video,. qt). Bir dosyayı kodlamaya çalışırsanız ve desteklenmeyen biçim hakkında bir hata iletisi alırsanız, görüşlerinizi [buraya](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)girin.
> 
> 

### <a name="audio-formats-in-input-containers"></a>Giriş kapsayıcılarındaki ses biçimleri
Media Encoder Standard, giriş kapsayıcılarında aşağıdaki ses biçimlerinin kullanılmasını destekler:

* Araya eklemeli stereo veya 5,1 örnekleri ile ses parçaları bulunan MXF, GXF ve QuickTime dosyaları

veya

* Sesin ayrı PCM parçaları olarak taşındığı, ancak kanal eşlemesinin (stero’ya veya 5.1’e) dosya meta verilerinden çıkarılabildiği MXF, GXF ve QuickTime dosyaları

## <a name="input-video-codecs"></a>Giriş video codec bileşenleri
| Giriş video codec bileşenleri | Desteklenir |
| --- | --- |
| AVC 8 bit/10 bit, 4:2:2’ye kadar, AVCIntra dahil |8 bit 4:2:0 ve 4:2:2 |
| Avid DNxHD (MXF biçiminde) |Yes |
| DVCPro/DVCProHD (MXF biçiminde) |Yes |
| Digital video (DV) (AVI dosyalarında) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (422 Profili ve Yüksek Düzeye kadar; XDCAM, XDCAM HD, XDCAM IMX, CableLabs® ve D10 gibi çeşitler dahil) |422 Profiline kadar |
| MPEG-1 |Yes |
| VC-1/WMV9 |Yes |
| Canopus HQ/HQX |No |
| MPEG-4 Bölüm 2 |Yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |
| YUV420 sıkıştırılmamış veya mezzanine |Yes |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Yes |
| Apple ProRes 422 HQ |Yes |
| Apple ProRes Proxy |Yes |
| Apple ProRes 4444 |Yes |
| Apple ProRes 4444 XQ |Yes |
| HEVC/H. 265| Ana ve ana 10 (&#42;) profilleri<br/>10. ana profil desteği, 8bit 4:2:0 içeriğine yöneliktir. |

## <a name="input-audio-codecs"></a>Giriş sesi codec bileşenleri
| Giriş Ses Codec Bileşenleri | Desteklenir |
| --- | --- |
| AAC (AAC-LC, AAC-HE ve AAC-HEv2; 5.1’e kadar) |Yes |
| MPEG Katman 2 |Yes |
| MP3 (MPEG-1 Ses Katmanı 3) |Yes |
| Windows Media Ses |Yes |
| WAV/PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |
| AMR (adaptive multi-rate) |Yes |
| AES (SMPTE 331M ve 302M, AES3-2003) |No |
| Dolby® E |No |
| Dolby® Digital (AC3) |No |
| Dolby® Digital Plus (E-AC3) |No |

## <a name="output-formats-and-codecs"></a>Çıkış biçimleri ve codec bileşenleri
Aşağıdaki tabloda, dışarı aktarma için desteklenen codec bileşenleri ve dosya biçimleri listelenmektedir.

| Dosya Biçimi | Video codec bileşeni | Ses codec bileşeni |
| --- | --- | --- |
| MP4 <br/><br/>(çoklu bit hızı MP4 kapsayıcıları dahil) |H. (yüksek, ana ve temel profiller) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H. (yüksek, ana ve temel profiller) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca bkz.
[Azure Media Services ile Isteğe bağlı Içeriği kodlama](media-services-encode-asset.md)

[Media Encoder Standard ile kodlama](media-services-dotnet-encode-with-media-encoder-standard.md)
