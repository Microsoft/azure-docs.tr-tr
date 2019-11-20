---
title: StorSimple 10 GbE arabirimleri için donanım | Microsoft Docs
description: StorSimple cihazınızda 10 GbE ağ arabirimleri için desteklenen küçük form faktörlü takılabilir (SFP) alıcı vericiler, kablolar ve anahtarlar açıklanmaktadır.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 7fafe177ea0c6c618dc4ab0727ba14c83cbb0102
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965008"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>StorSimple cihazınızda 10 GbE ağ arabirimleri için desteklenen donanım

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Genel Bakış
Bu makale, Microsoft Azure StorSimple cihazlarınızla birlikte çalışarak tamamlayıcı donanımlar hakkında bilgi sağlar.

## <a name="list-of-devices-tested-by-microsoft"></a>Microsoft tarafından test edilen cihazların listesi
Microsoft, cihazlarla en iyi şekilde çalışmasını sağlamak için aşağıdaki küçük form faktörlü takılabilir (SFP) alıcı vericiler, kabloları ve anahtarları test etti. (Yeni donanım test edildiğinde aşağıdaki tablolar güncelleştirilecektir.)

### <a name="sfp-transceivers"></a>SFP + alıcı vericiler
| Yapın | Model |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kablolar
| S. Hayır. | Yapın | Model |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05D (OM3) |

### <a name="switches"></a>Anahtarlar
| S. Hayır. | Yapın | Model |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Alanda sınanan cihazların listesi
Bu bölüm, StorSimple müşterileri tarafından alana başarıyla dağıtılan cihazların listesini içerir. Bunlar Microsoft tarafından sınanmamıştır, ancak büyük olasılıkla StorSimple cihazınıza göre çalışmalardır.

| Parametre | Value |
| --- | --- |
| Yap |Juniper |
| Modeli değiştir |ex4550-32F |
| İşletim sistemi sürümünü değiştir |JunOS 12.3 R 9.4 |
| Dikey pencere modeli |Yerleşik bağlantı noktaları (PIC 0) |
| Alıcı verici oluştur |Juniper |
| Alıcı verici modeli |Bölüm numarası 740-021308 <br></br> Bölüm numarası 740-030658 |
| Alıcı yazılımı bellenim sürümü |Rev 01 sürüm 0,0 (bildirilen) |
| Kablo modeli |Çift yönlü atlatıcı LC/LC 50/125μ, OM3, LSZH |
| StorSimple modeli |8600 |
| StorSimple yazılım sürümü |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>OEM sağlayıcısı (Mellanox) tarafından test edilen cihazların listesi
Mellanox, StorSimple cihazınızda 10 GbE ağ arabirimleri gibi Mellanox ağı arabirimleriyle en iyi şekilde çalışmasını sağlamak için aşağıdaki küçük form faktörlü takılabilir (SFP) alıcı vericiler, kabloları ve anahtarları test etti.

### <a name="cables-and-modules-supported-by-mellanox"></a>Mellanox tarafından desteklenen kablolar ve modüller
Aşağıdaki tabloda, Mellanox tarafından desteklenen kablolar ve modüller listelenmiştir. Bunlar Microsoft tarafından sınanmamıştır, ancak büyük olasılıkla StorSimple cihazınıza göre çalışmalardır.

| S. Hayır. | Hız | Model | Açıklama | Yapın |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |Pasif bakır kablo SFP + 10 GB/s 1m |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |Pasif bakır kablo SFP + 10 GB/s 2m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3DK |Pasif bakır kablo SFP + 10 GB/sn 3DK |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5DK |Pasif bakır kablo SFP + 10 GB/sn 5 dk |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cisco SFP + kablosu |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cisco SFP + kablosu |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cisco SFP + kablosu |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP +-SFP + 1m doğrudan ek bakır kablo |HP |
| 9. |10 GbE |455883-B21 HP BLc |10Gb SR SFP + opt |HP |
| 10. |10 GbE |455886-B21 HP BLc |10Gb LR SFP + opt |HP |
| 11. |10 GbE |487649-B21 HP BLc |SFP + 0,5 milyon 10GbE bakır kablo |HP |
| 12. |10 GbE |487652-B21 HP BLc |SFP + 1m 10GbE bakır kablo |HP |
| 13. |10 GbE |487655-B21 HP BLc |SFP + 3DK 10GbE bakır kablo |HP |
| 14. |10 GbE |487658-B21 HP BLc |SFP + 7DK 10GbE bakır kablo |HP |
| 15. |10 GbE |537963-B21 HP BLc |SFP + 5dk 10GbE bakır kablo |HP |
| 16. |10 GbE |AP784A HP |3M C Serisi pasif bakır SFP + kablo |HP |
| 17. |10 GbE |AP785A HP |5 dk C Serisi pasif bakır SFP + kablo |HP |
| 18. |10 GbE |AP818A HP |1m B serisi etkin bakır SFP + kablo |HP |
| 19. |10 GbE |AP819A HP |3M B-serisi etkin bakır SFP + kablosu |HP |
| 20. |10 GbE |J9150A HP |X132 10G SFP + LC SR alıcısı |HP |
| 21. |10 GbE |J9151A HP |X132 10G SFP + LC LR alıcısı |HP |
| 22. |10 GbE |J9283B HP |X242 10G SFP + SFP + 3D DAC kablosu |HP |
| 23. |10 GbE |J9285B HP |X242 10G SFP + SFP + 7d DAC kablosu |HP |
| 24. |10 GbE |JD095B HP |X240 10G SFP + SFP + 0.65 d DAC kablosu |HP |
| 25. |10 GbE |JD096B HP |X240 10G SFP + SFP + 1.2 d DAC kablosu |HP |
| 26. |10 GbE |JD097B HP |X240 10G SFP + SFP + 3M DAD kablosu |HP |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |QSFP ve SFP + bağdaştırıcı |Mellanox teknolojileri |
| 28. |10 GbE |MC2309124-006 MT |Pasif bakır kablo 1x SFP +-QSFP 10GB/s 24AWG 7 dk |Mellanox teknolojileri |
| 29. |10 GbE |MC2309124-007 MT |Pasif bakır kablo 1x SFP +-QSFP 10GB/s 24AWG 7 dk |Mellanox teknolojileri |
| 30. |10 GbE |MC2309130-003 MT |Pasif bakır kablo 1x SFP +-QSFP 10Gb/s 30awg 3M |Mellanox teknolojileri |
| 31. |10 GbE |MC2309130-00A MT |Pasif bakır kablo 1x SFP +-QSFP 10Gb/s 30awg 0,5 m |Mellanox teknolojileri |
| 32. |10 GbE |MC3309124-005 MT |Pasif bakır kablo 1x SFP + 10GB/s 24AWG 5 dk |Mellanox teknolojileri |
| 33. |10 GbE |MC3309124-007 MT |Pasif bakır kablo 1x SFP + 10GB/s 24AWG 7 dk |Mellanox teknolojileri |
| 34. |10 GbE |MC3309130-003 MT |Pasif bakır kablo 1x SFP + 10Gb/s 30awg 3M |Mellanox teknolojileri |
| 35. |10 GbE |MC3309130-00A MT |Pasif bakır kablo 1x SFP + 10Gb/s 30awg 0,5 m |Mellanox teknolojileri |

### <a name="switches-supported-by-mellanox"></a>Mellanox tarafından desteklenen anahtarlar
Aşağıdaki tabloda, Mellanox tarafından desteklenen anahtarlar listelenmektedir. Bunlar Microsoft tarafından sınanmamıştır, ancak büyük olasılıkla StorSimple cihazınıza göre çalışmalardır.

| S. Hayır. | Hız | Model | Açıklama | Yapın |
| --- | --- | --- | --- | --- |
| 1. |Noktalı |516733-B21 |HP ProCurve 6120XG 10GbE Ethernet dikey pencere anahtarı |HP |
| 2. |Noktalı |538113-B21 |HP 10GbE geçişli geçişli modül (PTM) |HP |
| 3. |Noktalı |EN4093 |IBM Purefleks System Fabric EN4093 10 Gigabit ölçeklenebilir anahtar modülü |IBM |
| 4. |1GbE |3020 |Cisco Catalyst 3020 1GbE anahtar dikey penceresi |Cisco |
| 5. |1GbE |3020X |Cisco Catalyst 3020X 1GbE anahtar dikey penceresi |Cisco |
| 6. |1GbE |438030-B21 |HP 1GbE Switch Modülü-GbE2c Layer 2/3 Ethernet dikey penceresi |HP |
| 7. |1GbE |6120G |HP ProCurve 6120G/XG 1GbE anahtar dikey penceresi |HP |

## <a name="next-steps"></a>Sonraki adımlar
[StorSimple donanım bileşenleri ve durumu hakkında daha fazla bilgi edinin](storsimple-monitor-hardware-status.md).

