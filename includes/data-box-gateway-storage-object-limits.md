---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 20d30935fe03bc002ab63f2f8ca1ce890ef9e3b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582638"
---
Yazıılabilecek Azure nesnelerinin boyutları aşağıda verilmiştir. Karşıya yüklenen tüm dosyaların bu sınırlara uygun olduğundan emin olun.

| Azure nesne türü | Karşıya yükleme sınırı                                             |
|-------------------|-----------------------------------------------------------|
| Blok Blobu        | ~ 4,75 TB                                                 |
| Sayfa Blobu         | 1 TB <br> Sayfa Blobu biçiminde karşıya yüklenen her dosya 512 bayt hizalı (tamsayı birden çok) olmalıdır, aksi takdirde karşıya yükleme başarısız olur. <br> VHD ve VHDX 512 bayt hizalı. |
| Azure Dosyaları         | 1 TB <br> Sayfa Blobu biçiminde karşıya yüklenen her dosya 512 bayt hizalı (tamsayı birden çok) olmalıdır, aksi takdirde karşıya yükleme başarısız olur. <br> VHD ve VHDX 512 bayt hizalı. |

> [!IMPORTANT]
> Dosyaların oluşturulmasına (depolama türünden bağımsız olarak) 5 TB 'a kadar izin verilir. Ancak, boyutu önceki tabloda tanımlanan karşıya yükleme sınırından daha büyük olan bir dosya oluşturursanız dosya karşıya yüklenemez. Alanı geri kazanmak için dosyayı el ile silmeniz gerekir.