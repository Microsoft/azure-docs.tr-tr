---
title: Bölgesel kullanılabilirlik Azure Media Services | Microsoft Docs
description: Bu makale, Microsoft Azure Media Services özelliklerine ve hizmet bölgesel kullanılabilirliğine genel bir bakış sunar.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 125f2be205760618ab6ffa3175f719f5e91e8c05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012267"
---
# <a name="media-services-regional-availability"></a>Bölgesel kullanılabilirlik Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. [V3 Media Services](../latest/media-services-overview.md)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure Media Services (AMS), çeşitli istemcilere (TV, PC ve mobil cihazlar gibi) isteğe bağlı olarak veya canlı akış halinde teslim amacıyla video ve ses içeriklerini güvenli bir şekilde karşıya yüklemenizi, depolamanızı, kodlamanızı ve paketlemenizi sağlar.

AMS dünyanın dört bir yanındaki birden çok bölgede çalışır ve uygulamalarınızın nerede oluşturulacağı konusunda esneklik sağlar. Bu makale, Microsoft Azure Media Services özelliklerine ve hizmet bölgesel kullanılabilirliğine genel bir bakış sunar.

Tüm Azure genel altyapısı hakkında daha fazla bilgi için bkz. [Azure geographiler](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="ams-accounts"></a>AMS hesapları

Media Services belirli bir bölgede kullanılabilir olup olmadığını öğrenmek için [bölgeye göre Azure ürünlerini](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) kullanın.

## <a name="streaming-endpoints"></a>Akış uç noktaları

Media Services müşterileri **Standart** akış uç noktası veya **Premium** akış uç noktası seçebilir.

|Name|Durum|Region
|---|---|---|
|Standart|GA|Tümü|
|Premium|GA|Tümü|

## <a name="live-encoding"></a>Live encoding

Şunlar dışında tüm bölgelerde kullanılabilir: Almanya, Brezilya Güney, Hindistan Batı, Hindistan Güney ve Hindistan Orta.

## <a name="encoding-media-processors"></a>Kodlama medya işleyicileri

AMS, isteğe bağlı iki kodlayıcı sunar: **Media Encoder Standard** ve **Media Encoder Premium İş Akışı**. Daha fazla bilgi için bkz. [Azure isteğe bağlı medya kodlayıcılarına genel bakış ve karşılaştırma](media-services-encode-asset.md).

|Medya işlemci adı|Durum|Bölgeler
|---|---|---|
|Media Encoder Standard|GA|Tümü|
|Media Encoder Premium İş Akışı|GA|Çin dışında tümü|

## <a name="analytics-media-processors"></a>Analiz medya işlemcileri

Medya Analizi, kuruluş ve işletmelerin video dosyalarından eyleme dönüştürülebilir öngörüler türetmesini kolaylaştıran bir grup konuşma ve görme bileşenidir. Daha fazla bilgi için bkz. [Azure Media Services Analizi’ne Genel Bakış](./legacy-components.md).

> [!NOTE]
> Bazı Analytics medya işlemcileri kullanımdan kaldırılacak. Kullanımdan kaldırma tarihleri için, [eski bileşenler](legacy-components.md) konusuna bakın.

|Medya işlemci adı|Durum|Region
|---|---|---|
|Azure Media Face Detector|Önizleme|Tümü|
|Azure Media Indexer|GA|Tümü|
|Azure Media Motion Detector|Önizleme|Tümü|
|Azure Media OCR|Önizleme|Tümü|
|Azure Media Redactor|GA|Tümü|
|Azure Media Video Thumbnails|Önizleme|Tümü|

## <a name="protection"></a>Koruma

Microsoft Azure Media Services, medyanızı bilgisayarınızdan ayrıldığı andan başlayıp depolama, işleme ve teslim aşamaları boyunca güvenlik altına almanızı sağlar. Daha fazla bilgi için bkz. [AMS içeriğini koruma](media-services-content-protection-overview.md).

|Şifreleme|Durum|Bölgeler|
|---|---|---| 
|Depolama|GA|Tümü|
|AES-128 anahtarları|GA|Tümü|
|Fairplay|GA|Tümü|
|PlayReady|GA|Tümü|
|Widevine|GA|Almanya, Federal kamu ve Çin dışında tümü.

> [!NOTE]
> Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="reserved-units-rus"></a>Ayrılmış birimler (RU)

Sağlanan ayrılmış birim sayısı, verili bir hesapta eşzamanlı olarak işlenebilecek medya görevlerinin sayısını belirler.

Tüm bölgelerde kullanılabilir.

## <a name="reserved-unit-ru-type"></a>Ayrılmış birim (RU) türü

Media Services hesap, medya işleme görevlerinizin tamamlanma hızını belirleyen bir ayrılmış birim türüyle ilişkilendirilir. Şu ayrılmış birim türlerinden birini seçebilirsiniz: S1, S2 veya S3.

|RU türü adı|Durum|Bölgeler
|---|---|---|
|S1|GA|Tümü|
|S2|GA|Güney Brezilya ve Hindistan Batı dışında tümü|
|S3|GA|Hindistan Batı dışında tümü|

## <a name="next-steps"></a>Sonraki adımlar

[Media Services v3’e geçirme](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Geribildirim gönderme

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]