---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 0fda881b805eb3a967cf3b05f6c6df8c65d20730
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905802"
---
| Kaynak | Hedef        |
|----------|---------------|
| Tek blob kapsayıcısının en büyük boyutu | Maksimum depolama hesabı kapasitesinden aynı |
| Blok Blobu veya ekleme blobu içindeki maksimum blok sayısı | 50.000 bloklar |
| Blok Blobu içindeki bir bloğun en büyük boyutu | 100 MIB |
| Blok Blobun en büyük boyutu | 50.000 X 100 MIB (yaklaşık 4,75 TiB) |
| Bir ekleme blobundan maksimum blok boyutu | 4 MIB |
| Bir ekleme blobunun maksimum boyutu | 50.000 x 4 MiB (yaklaşık 195 GiB) |
| Sayfa Blobun en büyük boyutu | 8 TiB |
| Blob kapsayıcısı başına en fazla depolanan erişim ilkesi sayısı | 5 |
|Tek bir blob için hedef istek oranı | Saniyede en fazla 500 istek |
|Tek sayfalı blob için hedef işleme | Saniyede 60 MIB 'ye kadar |
|Tek Blok Blobu için hedef aktarım hızı |Depolama hesabı giriş/çıkış sınırlarını artırma<sup>1</sup> |

<sup>1</sup> tek nesne aktarım hızı, aşağıdakiler dahil olmak üzere çeşitli faktörlere bağlıdır, ancak bunlarla sınırlı değildir: eşzamanlılık, istek boyutu, performans katmanı, karşıya yükleme için kaynak hızı ve indirmeleri için hedef. [Yüksek performanslı Blok Blobu](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) performans geliştirmelerinden faydalanmak için, > 4 MIB 'Nin bir put blob 'Unu veya put blok isteği boyutunu (Premium performans bloğu BLOB depolama alanı veya Data Lake Storage 2. için > 256 KiB) kullanın.
