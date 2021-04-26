---
title: Azure isteğe bağlı medya Kodlayıcılara genel bakış | Microsoft Docs
description: Azure Media Services, buluttaki medya kodlaması için birden çok seçenek sağlar. Bu makale, Azure isteğe bağlı medya Kodlayıcılara genel bakış sunar.
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
ms.openlocfilehash: 0fcf3aaa830c092dfa225f55fef882e2263b16cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013559"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>İsteğe bağlı Azure Medya Kodlayıcılara genel bakış

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure Media Services, buluttaki medya kodlaması için birden çok seçenek sağlar.

Media Services ile Başlarken, codec bileşenleri ve dosya biçimleri arasındaki farkı anlamak önemlidir.
Codec bileşenleri, sıkıştırma/açma algoritmalarını uygulayan yazılımdır, ancak dosya biçimleri sıkıştırılmış videoyu tutan kapsayıcılardır.

Media Services, bu akış biçimlerine yeniden paketlemenize gerek kalmadan, uyarlamalı bit hızı MP4 veya Kesintisiz Akış kodlanmış içerikleri Media Services (MPEG DASH, HLS, Kesintisiz Akış) tarafından desteklenen akış biçimlerinde sunmanıza olanak tanıyan dinamik paketleme sağlar.

Media Services hesabınız oluşturulduğunda hesabınıza **Durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir. Akış uç noktaları için faturalandırma, uç nokta **çalışır** durumda olduğunda oluşur.

Media Services, aşağıdaki isteğe bağlı Kodlayıcısı destekler:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

Bu makalede, isteğe bağlı medya kodlayıcıları hakkında kısa bir genel bakış ve daha ayrıntılı bilgiler içeren makalelerin bağlantıları verilmektedir.

Varsayılan olarak her Media Services hesabının tek seferde bir etkin kodlama görevi olabilir. Aynı anda birden çok kodlama görevinin, satın aldığınız her kodlamaya ayrılan birim için bir tane çalışmasına izin veren kodlama birimlerini ayırabilirsiniz. Bilgi için bkz. [kodlama birimlerini ölçeklendirme](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Nasıl kullanılır?
[Media Encoder Standard ile kodlama](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Biçimler
[Biçimler ve codec bileşenleri](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>'Nı
Media Encoder Standard, [burada](./media-services-mes-presets-overview.md)açıklanan kodlayıcı ön ayarlarından biri kullanılarak yapılandırılır.

### <a name="input-and-output-metadata"></a>Giriş ve çıkış meta verileri
Kodlayıcılar giriş meta verileri [burada](media-services-input-metadata-schema.md)açıklanmıştır.

Kodlayıcılar çıkış meta verileri [burada](media-services-output-metadata-schema.md)açıklanmıştır.

### <a name="generate-thumbnails"></a>Küçük resim oluşturma
Bilgi için bkz. [Media Encoder Standard kullanarak küçük resimleri oluşturma](media-services-advanced-encoding-with-mes.md).

### <a name="trim-videos-clipping"></a>Videoları kırpma (kırpma)
Bilgi için bkz. [Media Encoder Standard kullanarak videoları kırpma](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Yer paylaşımları oluştur
Bilgi için bkz. [Media Encoder Standard kullanarak yer paylaşımları oluşturma](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Ayrıca bkz.
[Media Services blogu](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

### <a name="known-issues"></a>Bilinen sorunlar
Giriş videonuz kapalı açıklamalı alt yazı içermiyorsa, çıkış varlığı hala boş bir TTML dosyası içerecektir.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>İlgili makaleler:
* [Media Encoder Standard ön ayarlarını özelleştirerek gelişmiş kodlama görevleri gerçekleştirin](media-services-custom-mes-presets-with-dotnet.md)
* [Kotalar ve sınırlamalar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/