---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67188360"
---
**Premium yönetilmeyen sanal makine diskleri: Hesap başına sınırlar**

| Resource | Varsayılan limit |
| --- | --- |
| Hesap başına toplam disk kapasitesi |35 TB |
| Hesap başına toplam anlık görüntü kapasitesi |10 TB |
| Hesap başına maksimum bant genişliği (giriş + çıkış)<sup>1</sup> |En fazla 50 Gbps |

<sup>1</sup> Giriş, bir depolama hesabına gönderilen isteklerin tüm *verilerine başvurur.* *Çıkış* , bir depolama hesabından alınan yanıtların tüm verilerine başvurur.

**Premium yönetilmeyen sanal makine diskleri: Disk başına sınırlar**

| Premium depolama diski türü | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Disk boyutu |128 GiB |512 GiB |1\.024 GiB (1 TB) |2\.048 GiB (2 TB)|4\.095 GiB (4 TB)|
| Disk başına maksimum ıOPS |500 |2,300 |5\.000 |7\.500 |7,500 |
| Disk başına en fazla aktarım hızı |100 MB/sn | 150 MB/sn |200 MB/sn |250 MB/sn |250 MB/sn |
| Depolama hesabı başına en fazla disk sayısı |280 |70 |35 | 17 | 8 |

**Premium yönetilmeyen sanal makine diskleri: VM başına sınırlar**

| Resource | Varsayılan limit |
| --- | --- |
| VM başına maksimum ıOPS |GS5 VM ile 80.000 ıOPS |
| VM başına en fazla aktarım hızı |GS5 VM ile 2.000 MB/sn |

