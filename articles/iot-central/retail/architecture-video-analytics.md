---
title: Azure IoT Central video analizi nesnesi ve hareket algılama | Microsoft Docs
description: IoT Central içindeki video analizi-nesne ve hareket algılama uygulama şablonunu kullanarak bir IoT Central uygulaması derlemeyi öğrenin. Bu şablon, canlı video analizlerini ve bağlı kameraları kullanır.
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 48808f762536390287bae40e8af3849da20b81c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91874297"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>Video analizi-nesne ve hareket algılama uygulama mimarisi

**Video analizi-nesne ve hareket algılama** uygulama şablonu, IoT çözümleri oluşturmanızı sağlar ve canlı video analizi özelliklerini içerir.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="Video analizi nesne ve hareket algılama bileşenlerine genel bakış Diyagramı.":::

Video analizi çözümünün temel bileşenleri şunlardır:

## <a name="live-video-analytics-lva"></a>Canlı video analizi (LVA)

LVA, kenarı ve bulutu kapsayan akıllı video uygulamaları oluşturmanız için bir platform sağlar. Platform, kenarı ve bulutu kapsayan akıllı video uygulamaları oluşturmanızı sağlar. Platform, canlı videoyu yakalama, kaydetme, çözümleme ve sonuçları video veya video analizi olabilecek sonuçları Azure hizmetlerine yayımlama özelliği sunar. Azure Hizmetleri, bulutta veya kenarda çalışıyor olabilir. Platform, video analiziyle IoT çözümlerini geliştirmek için kullanılabilir.

Daha fazla bilgi için bkz. GitHub 'da [canlı video analizi](https://github.com/Azure/live-video-analytics) .

## <a name="iot-edge-lva-gateway-module"></a>IoT Edge LVA Gateway modülü

IoT Edge LVA Gateway modülü, kameraları yeni cihaz olarak başlatır ve IoT cihazı istemci SDK 'sını kullanarak doğrudan IoT Central bağlar.

Bu başvuru uygulamasında cihazlar, uçtan simetrik anahtarlar kullanarak çözüme bağlanır. Cihaz bağlantısı hakkında daha fazla bilgi edinmek için bkz. [Azure 'a bağlanma IoT Central](../core/concepts-get-connected.md)

## <a name="media-graph"></a>Medya grafiği

Medya grafiği, medyanın nerede yakalanacağını, nasıl işlem yapılacağını ve sonuçların nereye teslim edileceği hakkında bir tanımlama sağlar. Bileşenleri veya düğümleri istenen şekilde bağlayarak medya grafiğini yapılandırırsınız. Daha fazla bilgi için bkz. GitHub 'da [medya grafiği](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) .

## <a name="next-steps"></a>Sonraki adımlar

Önerilen sonraki adım, [video analizi-nesne ve hareket algılama uygulama şablonunu kullanarak bir IoT Central uygulamasının nasıl dağıtılacağını](tutorial-video-analytics-deploy.md)öğrenmektir.
