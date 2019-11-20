---
title: Eski bileşenleri Azure Media Services | Microsoft Docs
description: Bu konu, eski Azure Media Services bileşenleri ele almaktadır.
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
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 592fd8901fc7f8a82a390f3c125c712ef5829a52
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083723"
---
# <a name="azure-media-services-legacy-components"></a>Eski Azure Media Services bileşenleri

Zamanla, medya hizmeti bileşenlerinde sürekli iyileştirmeler ve geliştirmeler yapılmıştır. Sonuç olarak, bazı eski bileşenler kullanımdan kaldırılmıştır. Aşağıdaki makalelerde, uygulamanızı eski bileşenden güncel bir bileşene nasıl geçirebileceğiniz hakkında yönergeler bulabilirsiniz.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Eski bileşenlerin ve geçiş kılavuzunun kullanımdan kaldırma planları

*Windows Azure Medya Kodlayıcısı* (WAME) ve *Azure Medya Kodlayıcısı* (AME) medya işlemcilerinin kullanımdan kalkması duyuruluyor. Bu işlemciler 30 Kasım 2019 tarihinde devre dışı bırakılıyor.

* [Windows Azure Medya Kodlayıcısı 'ndan Media Encoder Standard 'e geçiş](migrate-windows-azure-media-encoder.md)
* [Azure Medya Kodlayıcısı 'ndan Media Encoder Standard 'e geçiş](migrate-azure-media-encoder.md)

Ayrıca, aşağıdaki Media Analytics medya işlemcilerinin kullanımdan kaldırılması da sunuyoruz: 
 
|Medya işlemci adı|Emeklilik tarihi|Ek notlar|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1 Ocak 2020|Bu medya işlemcisi [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/)ile değiştirilmeyecektir. Daha fazla bilgi için, bkz. [Azure Media Indexer 2 ' den Azure Media Services video Indexer geçiş](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 Ekim 2020|Bu medya işlemcisi [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/)ile değiştirilmeyecektir. Daha fazla bilgi için bkz. [Azure Media Indexer 'den Azure Media Services 'ye geçirme video Indexer](migrate-indexer-v1-v2.md)

## <a name="next-steps"></a>Sonraki adımlar

[Media Services V2 'den v3 'e geçmek için geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)
