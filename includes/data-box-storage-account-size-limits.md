---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98225196"
---
Bir depolama hesabına kopyalanmış verilerin boyutuna ilişkin sınırlar aşağıda verilmiştir. Karşıya yüklediğiniz verilerin bu sınırlara uyduğundan emin olun. Bu limitlerle ilgili en güncel bilgiler için bkz. blob depolama ve [Azure dosyaları ölçeklenebilirlik ve performans hedefleri](../articles/storage/files/storage-files-scale-targets.md) [için ölçeklenebilirlik ve performans hedefleri](../articles/storage/blobs/scalability-targets.md) .

| Azure depolama hesabına kopyalanmış verilerin boyutu                      | Varsayılan limit          |
|---------------------------------------------------------------------|------------------------|
| Blok Blobu ve Sayfa Blobu                                            | Maksimum sınır, [Azure aboneliği için tanımlanan depolama sınırı](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) ile aynıdır ve Data Box dahil olmak üzere tüm kaynaklardaki verileri içerir.   |
| Azure Dosyaları                                                          | Data Box, Data Box sırasını oluşturmadan önce etkinleştirilmişse büyük dosya paylaşımlarını destekler (100 TiB). <br> Sipariş oluşturma işleminden önce etkinleştirilmemişse, desteklenen en büyük dosya paylaşma boyutu 5 TiB olur. <br> Data Box, depolama hesabındaki tüm paylaşımlar için toplam 100 TiB 'ye izin veren Azure Premium dosya paylaşımlarını destekler. <br> Günlüklerin ve denetim günlüklerinin kullandığı alan nedeniyle kullanılabilir en yüksek kapasite biraz daha küçüktür. En az 100 GiB her biri kopyalama günlüğü ve denetim günlüğü için ayrılmıştır. Daha fazla bilgi için bkz. [Azure Data Box Için denetim günlükleri Azure Data Box Heavy](../articles/databox/data-box-audit-logs.md). <br> *StorageAccount_AzureFiles* altındaki tüm klasörler bu sınırı izlemelidir. <br> Daha fazla bilgi için bkz. [büyük dosya paylaşımlarını etkinleştirme ve oluşturma](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
