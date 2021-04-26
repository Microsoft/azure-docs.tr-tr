---
title: Video Indexer ve Azure Media Services v3 önayarlarının karşılaştırması
description: Bu makalede Video Indexer özellikleri ve Azure Media Services v3 önayarları karşılaştırılır.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: ceb105409ff218cd633eb36d793e8fc16c7d135c
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961549"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Azure Media Services v3 önayarlarını karşılaştırın ve Video Indexer 

Bu makalede **video Indexer API** 'lerin ve **Media Services v3 API**'lerinin özellikleri karşılaştırılır. 

Şu anda, [video Indexer API](https://api-portal.videoindexer.ai/) 'leri ve [Media Services v3 API 'leri](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)tarafından sunulan özellikler arasında bir çakışma var. Aşağıdaki tabloda, farklar ve benzerlikler anlamak için geçerli kılavuz sunulmaktadır. 

## <a name="compare"></a>Karşılaştır

|Özellik|Video Indexer API 'Leri |Video Çözümleyicisi ve ses Çözümleyicisi ön ayarları<br/>Media Services v3 API 'Lerinde|
|---|---|---|
|Media Insights|[Gelişmiş](video-indexer-output-json-v2.md) |[Temel Bilgiler](../latest/analyze-video-audio-files-concept.md)|
|Lerinde|Desteklenen özelliklerin tam listesini görüntüleyin: <br/> [Genel Bakış](video-indexer-overview.md)|Yalnızca video öngörülerini döndürür|
|Faturalandırma|[Media Services fiyatlandırması](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Media Services fiyatlandırması](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Uyumluluk|En güncel Uyumluluk güncelleştirmeleri için, [Azure uyumluluk Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) ziyaret edin ve ilgili sertifikayla uyumlu olup olmadığını görmek için "video Indexer" araması yapın.|En güncel Uyumluluk güncelleştirmeleri için, [Azure uyumluluk Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) ziyaret edin ve ilgili sertifikayla uyumlu olup olmadığını görmek için "Media Services" araması yapın.|
|Ücretsiz Deneme|Doğu ABD|Kullanılamaz|
|Bölge kullanılabilirliği|Bölgeye göre bilişsel [Hizmetler kullanılabilirliğine](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) bakın|[Bölgeye göre Media Services kullanılabilirlik](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)bölümüne bakın.|

## <a name="next-steps"></a>Sonraki adımlar

[Video Indexer’a genel bakış](video-indexer-overview.md)

[Media Services v3 genel bakış](../latest/media-services-overview.md)
