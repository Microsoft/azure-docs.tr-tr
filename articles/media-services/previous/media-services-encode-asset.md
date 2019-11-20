---
title: Azure isteğe bağlı medya Kodlayıcılara genel bakış | Microsoft Docs
description: Bu konu, Azure isteğe bağlı medya Kodlayıcılara genel bakış sunar.
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
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: 5c55b419b88a66d2e1acf1687478ab35d9f0a059
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019043"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>İsteğe bağlı Azure Medya Kodlayıcılara genel bakış 

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>En son sürüm olan [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)’ü inceleyin. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)

Azure Media Services, buluttaki medya kodlaması için birden çok seçenek sağlar.

Media Services ile Başlarken, codec bileşenleri ve dosya biçimleri arasındaki farkı anlamak önemlidir.
Codec bileşenleri, sıkıştırma/açma algoritmalarını uygulayan yazılımdır, ancak dosya biçimleri sıkıştırılmış videoyu tutan kapsayıcılardır.

Media Services, uyarlamalı bit hızı MP4 veya Kesintisiz Akış kodlanmış içeriğinizi Media Services (MPEG DASH, HLS, Kesintisiz Akış) tarafından desteklenen akış biçimlerinde, bunlara yeniden paketlemenize gerek kalmadan sunmanıza olanak tanıyan dinamik paketleme sağlar akış biçimleri.

Media Services hesabınız oluşturulduğunda hesabınıza **Durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir. Akış uç noktaları için faturalandırma, uç nokta **çalışır** durumda olduğunda oluşur.

Media Services, aşağıdaki makalede açıklanan isteğe bağlı kodlayıcıları destekler:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium İş Akışı](media-services-encode-asset.md#media-encoder-premium-workflow)

Bu makalede, isteğe bağlı medya kodlayıcıları hakkında kısa bir genel bakış sunulmaktadır ve daha ayrıntılı bilgi veren makalelerin bağlantıları sağlanmaktadır. Bu konu, kodlayıcılarla kıyaslaması de sağlar.

Varsayılan olarak her Media Services hesabının tek seferde bir etkin kodlama görevi olabilir. Aynı anda birden çok kodlama görevinin, satın aldığınız her kodlamaya ayrılan birim için bir tane çalışmasına izin veren kodlama birimlerini ayırabilirsiniz. Bilgi için bkz. [kodlama birimlerini ölçeklendirme](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Nasıl kullanılır
[Media Encoder Standard ile kodlama](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Biçimini
[Biçimler ve codec bileşenleri](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Önayarlar
Media Encoder Standard, [burada](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)açıklanan kodlayıcı ön ayarlarından biri kullanılarak yapılandırılır.

### <a name="input-and-output-metadata"></a>Giriş ve çıkış meta verileri
Kodlayıcılar giriş meta verileri [burada](media-services-input-metadata-schema.md)açıklanmıştır.

Kodlayıcılar çıkış meta verileri [burada](media-services-output-metadata-schema.md)açıklanmıştır.

### <a name="generate-thumbnails"></a>Küçük resim oluştur
Bilgi için bkz. [Media Encoder Standard kullanarak küçük resimleri oluşturma](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Videoları kırpma (kırpma)
Bilgi için bkz. [Media Encoder Standard kullanarak videoları kırpma](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Yer paylaşımları oluştur
Bilgi için bkz. [Media Encoder Standard kullanarak yer paylaşımları oluşturma](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Ayrıca bkz.
[Media Services blogu](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Media Encoder Premium İş Akışı
### <a name="overview"></a>Genel Bakış
[Azure Media Services Premium kodlamaya giriş](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Nasıl kullanılır
Media Encoder Premium Workflow karmaşık iş akışları kullanılarak yapılandırılır. İş akışı dosyaları [iş akışı Tasarımcısı](media-services-workflow-designer.md) Aracı kullanılarak oluşturulup güncelleştirilemeyebilir.

[Azure Media Services içinde Premium kodlama kullanma](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Bilinen sorunlar
Giriş videonuz kapalı açıklamalı alt yazı içermiyorsa, çıkış varlığı hala boş bir TTML dosyası içerecektir.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>İlgili makaleler
* [Media Encoder Standard ön ayarlarını özelleştirerek gelişmiş kodlama görevleri gerçekleştirin](media-services-custom-mes-presets-with-dotnet.md)
* [Kotalar ve sınırlamalar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
