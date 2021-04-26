---
title: Azure Işlem birimine genel bakış
description: Azure işlem birimleri kavramına genel bakış. ACU, Azure SKU 'Larında CPU performansını karşılaştırmak için bir yol sağlar.
author: mimckitt
ms.service: virtual-machines
ms.subservice: azure-compute-unit
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.reviewer: davberg
ms.openlocfilehash: 3233695f8a8cc14d70f0c482458e77094dde7622
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969079"
---
# <a name="azure-compute-unit-acu"></a>Azure işlem birimi (ACU)

Azure Işlem birimi (ACU) kavramı, Azure SKU 'Ları genelinde işlem (CPU) performansını karşılaştırmak için bir yol sağlar. Bu birim, performans ihtiyaçlarınızı karşılayabilecek SKU'yu kolayca belirlemenize yardımcı olacak. ACU Şu anda küçük (Standard_A1) bir VM 'de 100 olan ve diğer tüm SKU 'Ların standart bir kıyaslama işlemini kaç ne kadar hızlı çalıştırabileceğini temsil etmektedir.

* ACUs CPU sıklığını artırmak ve bir performans artışı sağlamak için Intel® Turbo teknolojisini kullanın.  Performans artışının miktarı, VM boyutu, iş yükü ve aynı konakta çalışan diğer iş yüklerine göre farklılık gösterebilir.

* * ACUs CPU sıklığını artırmak ve performans artışı sağlamak için AMD® arttırma teknolojisini kullanın.  Performans artışının miktarı, VM boyutu, iş yükü ve aynı konakta çalışan diğer iş yüklerine göre farklılık gösterebilir.

Hiper iş parçacıklı ve iç içe sanallaştırma çalıştırabilme

AMD eşzamanlı çoklu iş parçacığı teknolojisi

> [!IMPORTANT]
> ACU yalnızca rehberlik etme amacı taşımaktadır. İş yükünüzle aldığınız sonuçlar farklılık gösterebilir.
<br>

| SKU Ailesi | ACU \ vCPU | vCPU: çekirdek |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1-A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5-A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8-A11](sizes-previous-gen.md) |225* | 1:1 |
| [Kenarı](sizes-b-series-burstable.md) |Değişir | 1:1 |
| [D1-D14](sizes-previous-gen.md) |160-250 | 1:1 |
| [D1_v2 D15_v2](dv2-dsv2-series.md) |210-250* | 1:1 |
| [DS1-DS14](sizes-previous-gen.md) |160-250 | 1:1 |
| [DS1_v2 DS15_v2](dv2-dsv2-series.md) |210-250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160-190 * | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160-190 * | 2:1\*\*\* |
| [Dav4](dav4-dasv4-series.md) |230-260 * * | 2:1\*\*\*\* |
| [Dasv4](dav4-dasv4-series.md) |230-260 * * | 2:1\*\*\*\* |
| [Dv4](dv4-dsv4-series.md) | 195-210 | 2:1\*\*\* |
| [Dsv4](dv4-dsv4-series.md) | 195-210 | 2:1\*\*\* |
| [Ddv4](ddv4-ddsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [Ddsv4](ddv4-ddsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160-190 * | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160-190 * | 2:1\*\*\* |
| [Eav4](eav4-easv4-series.md) |230-260 * * | 2:1\*\*\*\* |
| [Easv4](eav4-easv4-series.md) | 230-260 * * | 2:1\*\*\*\* |
| [Ev4](ev4-esv4-series.md) | 195-210 | 2:1\*\*\* |
| [Esv4](ev4-esv4-series.md) | 195-210 | 2:1\*\*\* |
| [Edv4](edv4-edsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [Edsv4](edv4-edsv4-series.md) | 195-210 * | 2:1\*\*\* |
| [F2s_v2 F72s_v2](fsv2-series.md) |195-210 * | 2:1\*\*\* |
| [F1-F16](sizes-previous-gen.md) |210-250* | 1:1 |
| [F1s-F16s](sizes-previous-gen.md) |210-250* | 1:1 |
| [G1-G5](sizes-previous-gen.md) |180-240* | 1:1 |
| [GS1-GS5](sizes-previous-gen.md) |180-240* | 1:1 |
| [Olsun](h-series.md) |290-300* | 1:1 |
| [HB](hb-series.md) |199-216 * * | 1:1 |
| [HC](hc-series.md) |297-315 * | 1:1 |
| [L4s-L32s](sizes-previous-gen.md) |180-240* | 1:1 |
| [L8s_v2 L80s_v2](lsv2-series.md) |150-175 * * | 2:1\*\*\*\* |
| [M](m-series.md) | 160-180 | 2:1\*\*\* |
| [NVv4](nvv4-series.md) |230-260 * * | 2:1\*\*\*\* |

Farklı boyutlar hakkında daha fazla bilgi için bağlantılar aşağıda verilmiştir:

- [Genel amaçlı](sizes-general.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [İşlem için iyileştirilmiş](sizes-compute.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
