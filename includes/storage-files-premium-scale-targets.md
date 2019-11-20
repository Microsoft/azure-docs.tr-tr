---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180079"
---
#### <a name="additional-premium-file-share-level-limits"></a>Ek Premium dosya paylaşma düzeyi limitleri

|Alan  |Hedef  |
|---------|---------|
|En küçük boyut artış/azaltma    |1 GiB      |
|Temel IOPS    |GiB başına 1 ıOPS, 100.000 'e kadar|
|IOPS patlama    |GiB başına 3x ıOPS, 100.000 'e kadar|
|Çıkış oranı         |60 MIB/s + 0,06 * sağlanan GiB        |
|Giriş oranı| 40 MIB/s + 0,04 * sağlanan GiB |

#### <a name="file-level-limits"></a>Dosya düzeyi sınırları

|Alan  |Premium dosya  |Standart dosya |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|Dosya başına en fazla ıOPS     |5,000         |1000|
|Eşzamanlı tutamaçlar    |2,000         |2,000|
|Çıkış  |300 MIB/sn|      Bkz. standart dosya işleme değerleri|
|Giriş  |200 MIB/sn| Bkz. standart dosya işleme değerleri|
|Aktarım hızı| Premium dosya giriş/çıkış değerlerini gör| En fazla 60 MIB/sn|
