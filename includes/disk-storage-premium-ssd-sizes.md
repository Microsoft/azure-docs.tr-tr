---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 52dbf03c7d4c2c2de565d6793fc867cf5b886db3
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "74003932"
---
| Premium SSD Boyutlar | P1 | P2 | P3 | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| GiB 'de disk boyutu | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2\.048 | 4\.096 | 8,192 | 16.384 | 32,767 |
| Disk başına IOPS | 120 | 120 | 120 | 120 | 240 | 500 | 1\.100 | 2\.300 | 5\.000 | 7\.500 | 7\.500 | 16,000 | 18.000 | 20,000 |
| Disk başına aktarım hızı | 25 MiB/sn | 25 MiB/sn | 25 MiB/sn | 25 MiB/sn | 50 MIB/sn | 100 MIB/sn | 125 MIB/sn | 150 MIB/sn | 200 MIB/sn | 250 MIB/sn | 250 MIB/sn| 500 MIB/sn | 750 MIB/sn | 900 MIB/sn |
| Disk başına en fazla patlama ıOPS * * | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 |
| Disk başına en fazla patlama performansı * * | 170 MIB/sn | 170 MIB/sn | 170 MIB/sn | 170 MIB/sn | 170 MIB/sn | 170 MIB/sn | 170 MIB/sn | 170 MIB/sn |
| En fazla patlama süresi * * | 30 dakika  | 30 dakika  | 30 dakika  | 30 dakika  | 30 dakika  | 30 dakika  | 30 dakika  | 30 dakika  |

\*, şu anda önizleme aşamasında olan bir disk boyutunu gösterir, bölgesel kullanılabilirlik bilgileri için bkz. [yeni disk boyutları: yönetilen ve yönetilmeyen](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).

\** Şu anda önizleme aşamasında olan bir özelliği gösterir. daha fazla bilgi için bkz. [disk](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) patlaması.
