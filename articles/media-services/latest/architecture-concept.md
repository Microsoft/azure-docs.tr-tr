---
title: Media Services mimarileri
description: Bu makalede Media Services mimarileri açıklanmaktadır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: 4d14ab8b72e3e120e8ee8cc2be4ae29f20c32e87
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105964337"
---
# <a name="media-services-architectures"></a>Media Services mimarileri

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>Canlı akış dijital medya

Canlı akış çözümü, videoyu gerçek zamanlı olarak yakalamanızı ve müşterilere gerçek zamanlı olarak (akış görüşmeleri, konferanslar ve spor olayları çevrimiçi olarak) yayınlamanızı sağlar. Bu çözümde video, bir video kamera tarafından yakalanır ve bir kanal giriş uç noktasına gönderilir. Kanal, canlı giriş akışını alır ve bir Web tarayıcısına veya mobil uygulamaya akış uç noktası aracılığıyla akış için kullanılabilir hale getirir. Kanal Ayrıca, daha fazla işlem ve teslim yapmadan önce akışınızı önizlemek ve doğrulamak için bir önizleme izleme uç noktası sağlar. Kanal Ayrıca, içeri aktarılan içeriği daha sonra akışa almak için kaydedebilir ve saklayabilir (isteğe bağlı video).

Bu çözüm, Azure yönetilen hizmetlerinde oluşturulmuştur: Media Services ve Content Delivery Network. Bu hizmetler, düzeltme eki uygulanan ve desteklenen yüksek kullanılabilirliğe sahip bir ortamda çalışır ve üzerinde çalıştıkları ortam yerine çözümünüze odaklanmanızı sağlar.

Bkz. Azure mimari merkezindeki [canlı akış dijital medyası](/azure/architecture/solution-ideas/articles/digital-media-live-stream) .

## <a name="video-on-demand-digital-media"></a>Dijital medya ile isteğe bağlı video

Video özellikli herhangi bir uç nokta cihazına, mobil uygulamaya veya masaüstü tarayıcısına film, haber klipleri, spor segmentleri, eğitim videoları ve müşteri destek öğreticileri gibi kaydedilmiş video içeriğini akışa yönelik bir özellik sunan temel bir video isteğe bağlı bir çözümdür. Video dosyaları, Azure Blob depolama alanına yüklenir, çoklu bit hızına sahip standart biçimde kodlanır ve sonra tüm büyük Uyarlamalı bit hızı akış protokolleriyle (HLS, MPEG-DASH, kesintisiz) Azure Media Player istemcisine dağıtılır.

Bu çözüm, Azure yönetilen hizmetlerinde oluşturulmuştur: BLOB depolama, Content Delivery Network ve Azure Media Player. Bu hizmetler, düzeltme eki uygulanan ve desteklenen yüksek kullanılabilirliğe sahip bir ortamda çalışır ve üzerinde çalıştıkları ortam yerine çözümünüze odaklanmanızı sağlar.

Bkz. Azure mimari merkezi 'nde [isteğe bağlı video dijital medyası](/azure/architecture/solution-ideas/articles/digital-media-video) .

## <a name="gridwich-media-processing-system"></a>Gridwich medya işleme sistemi

Gridwich sistemi, Azure 'da medya varlıklarını işlemeye ve sunmaya yönelik en iyi uygulamaları gösterir. Gridwich sistemi medyaya özgü olsa da, ileti işleme ve olay çerçevesi herhangi bir durum bilgisiz olay işleme iş akışı için uygulanabilir.

Bkz. Azure mimari merkezindeki [Gridwich medya işleme sistemi](/azure/architecture/reference-architectures/media-services/gridwich-architecture) .

## <a name="next-steps"></a>Sonraki adımlar

> [Azure Media Services’a genel bakış](media-services-overview.md)