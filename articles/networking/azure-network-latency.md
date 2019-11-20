---
title: Azure ağ gidiş dönüş gecikme süresi istatistikleri | Microsoft Docs
description: Azure bölgeleri arasında gidiş dönüş gecikme süresi istatistikleri hakkında bilgi edinin.
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587595"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure ağ gidiş dönüş gecikme süresi istatistikleri

Azure, iç izleme araçlarının yanı sıra üçüncü taraf yapay bir izleme hizmeti olan [ThousandEyes](https://thousandeyes.com)tarafından toplanan ölçümleri kullanarak ağının temel alanlarının gecikme süresini (hızını) sürekli olarak izler.

## <a name="how-are-the-measurements-collected"></a>Ölçümler nasıl toplanır?

Gecikme ölçümleri, Azure bulut bölgelerinde barındırılan ThousandEyes aracılarından toplanır ve bunlar arasında sürekli olarak 1 dakikalık aralıklarla ağ yoklamaları gönderilir. Aylık gecikme istatistikleri, bu ay için toplanan örneklerin ortalarından ortalaması alınır.

## <a name="october-2019-latency-figures"></a>2019 Ekim gecikme süresi rakamları

31 Ekim 2019 tarihinde sona eren 31 gün boyunca, toplanan bölgelerde aylık min ve maksimum gidiş dönüş gecikme süreleri şunlardır:

- **Kuzey Amerika** bölgelerin içindeki gidiş dönüşler için **5 MS** ile **72 MS** arası.
- **Avrupa** bölgeleri 'nin içindeki gidiş dönüşler için **3 MS** - **28 MS** .
- **Asya** bölgeleri içindeki gidiş dönüşler için **4 MS** 'den **134 MS** 'e.

Aşağıdaki bölgeler arası gecikme ölçümleri [ThousandEyes](https://thousandeyes.com)tarafından desteklenmektedir. Aşağıdaki tablodaki ölçü birimi milisaniyedir (MS).

![Azure bölgeler arası gecikme istatistikleri](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>Sonraki adımlar
- [Azure bölgeleri](https://azure.microsoft.com/global-infrastructure/regions/)hakkında bilgi edinin.