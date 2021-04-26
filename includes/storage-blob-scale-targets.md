---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/01/2021
ms.author: tamram
ms.openlocfilehash: 16da73fe453760e2dc84e7d683c3a16c12b8a06f
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218625"
---
| Kaynak | Hedef |
|-|-|
| Tek blob kapsayıcısının en büyük boyutu | Maksimum depolama hesabı kapasitesinden aynı |
| Blok Blobu veya ekleme blobu içindeki maksimum blok sayısı | 50.000 bloklar |
| Blok Blobu içindeki bir bloğun en büyük boyutu | 4000 MIB |
| Blok Blobun en büyük boyutu | 50.000 X 4000 MIB (yaklaşık 190,7 TiB) |
| Bir ekleme blobundan maksimum blok boyutu | 4 MIB |
| Bir ekleme blobunun maksimum boyutu | 50.000 x 4 MiB (yaklaşık 195 GiB) |
| Sayfa Blobun en büyük boyutu | 8 TiB<sup>2</sup> |
| Blob kapsayıcısı başına en fazla depolanan erişim ilkesi sayısı | 5 |
| Tek bir blob için hedef istek oranı | Saniyede en fazla 500 istek |
| Tek sayfalı blob için hedef işleme | Saniyede 60 MIB 'ye kadar<sup>2</sup> |
| Tek Blok Blobu için hedef aktarım hızı | Depolama hesabı giriş/çıkış sınırlarını artırma<sup>1</sup> |

tek bir blob için <sup>1</sup> aktarım hızı, aşağıdakiler dahil olmak üzere çeşitli faktörlere bağlıdır, ancak bunlarla sınırlı değildir: eşzamanlılık, istek boyutu, performans katmanı, karşıya yükleme için kaynak hızı ve indirmeleri için hedef. [Yüksek verimlilik blok bloblarının](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)performans geliştirmelerinden faydalanmak için, daha büyük blob 'ları veya blokları karşıya yükleyin. Özellikle, standart depolama hesapları için 4 MiB 'den büyük bir blob veya blok boyutu ile [PUT blobu](/rest/api/storageservices/put-blob) veya [PUT bloğu](/rest/api/storageservices/put-block) işlemini çağırın. Premium Blok Blobu veya Data Lake Storage 2. depolama hesapları için 256 KiB 'den büyük bir blok veya blob boyutu kullanın.

<sup>2</sup> sayfa Blobları henüz **hiyerarşik ad alanı** ayarı olan hesaplarda desteklenmez.

Aşağıdaki tabloda hizmet sürümü tarafından izin verilen en yüksek blok ve BLOB boyutları açıklanmaktadır.

| Hizmet sürümü | Maksimum blok boyutu (put bloğu aracılığıyla) | En büyük BLOB boyutu (put blok listesi aracılığıyla) | Tek yazma işlemi aracılığıyla en büyük BLOB boyutu (put blobu aracılığıyla) |
|-|-|-|-|
| Sürüm 2019-12-12 ve üzeri | 4000 MIB | Yaklaşık 190,7 TiB (4000 MiB X 50.000 blok) | 5000 MIB (Önizleme) |
| Sürüm 2016-05-31 ile sürüm 2019-07-07 | 100 MIB | Yaklaşık 4,75 TiB (100 MiB X 50.000 blok) | 256 MIB |
| 2016-05-31 ' den önceki sürümler | 4 MIB | Yaklaşık 195 GiB (4 MIB X 50.000 blok) | 64 MIB |
