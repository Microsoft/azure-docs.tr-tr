---
title: Azure ağ Izleyicisi için veri yerleşimi | Microsoft Docs
description: Bu makale, Azure ağ Izleyicisi hizmeti için veri fazlalığını anlamanıza yardımcı olur.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b5aa8167031c3b871c6a6a4d84159c3c284bf241
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018437"
---
# <a name="data-residency-for-azure-network-watcher"></a>Azure ağ Izleyicisi için veri yerleşimi
Bağlantı Izleyicisi (Önizleme) hizmeti dışında, Azure ağ Izleyicisi müşteri verilerini depolamaz.


## <a name="connection-monitor-preview-data-residency"></a>Bağlantı Izleyicisi (Önizleme) veri yerleşimi
Bağlantı Izleyicisi (Önizleme) hizmeti müşteri verilerini depolar. Bu veriler ağ Izleyicisi tarafından tek bir bölgede otomatik olarak depolanır. Bu nedenle bağlantı Izleyicisi (Önizleme), [Güven Merkezi](https://azuredatacentermap.azurewebsites.net/)'nde belirtilen gereksinimler dahil olmak üzere bölge içi veri yerleşimi gereksinimlerini otomatik olarak karşılar.

## <a name="data-residency"></a>Veri yerleşimi
Azure 'da, müşteri verilerinin tek bir bölgede depolanmasını sağlayan özelliği şu anda yalnızca Brezilya coğrafi bölgenin Asya Pasifik coğrafi ve Brezilya Güney (Sao Paulo Eyaleti) bölgesinin Güneydoğu Asya bölgesinde (Singapur) kullanılabilir. Diğer tüm bölgeler için müşteri verileri coğrafi olarak depolanır. Daha fazla bilgi için bkz. [Güven Merkezi](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Sonraki adımlar

* [Ağ İzleyicisi](./network-watcher-monitoring-overview.md)'ne genel bakış konusunu okuyun.