---
title: Premium Sayfa Blobu depolama hesapları için ölçeklenebilirlik hedefleri
titleSuffix: Azure Storage
description: Premium performans sayfası BLOB depolama hesabı, okuma/yazma işlemleri için iyileştirilmiştir. Bu depolama hesabı türü, bir Azure sanal makinesi için yönetilmeyen bir diski yedekler.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 23a176bfa5e3861dbc4ad5c03ea54fc847d3f56b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96922540"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Premium sayfa BLOB depolama hesapları için ölçeklenebilirlik ve performans hedefleri

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Premium Sayfa Blobu hesapları için ölçek hedefleri

Premium performans sayfası BLOB depolama hesabı, okuma/yazma işlemleri için iyileştirilmiştir. Bu depolama hesabı türü, bir Azure sanal makinesi için yönetilmeyen bir diski yedekler.

> [!NOTE]
> Microsoft, mümkünse Azure sanal makineleri (VM 'Ler) ile yönetilen disklerin kullanılmasını önerir. Yönetilen diskler hakkında daha fazla bilgi için bkz. [VM 'ler için Azure disk depolama genel bakış](../../virtual-machines/managed-disks-overview.md).

Premium Sayfa Blobu depolama hesaplarının aşağıdaki ölçeklenebilirlik hedefleri vardır:

| Toplam hesap kapasitesi                            | Yerel olarak yedekli depolama hesabı için toplam bant genişliği                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Disk kapasitesi: 4 TB (tek disk)/35 TB (tüm disklerin birikmeli toplamı) <br>Anlık görüntü kapasitesi: 10 TB | Gelen<sup>1</sup> + giden<sup>2</sup> için saniyede 50 Gigabit |

<sup>1</sup> bir depolama hesabına gönderilen tüm veriler (istekler)

<sup>2</sup> depolama hesabından alınan tüm veriler (yanıtlar)

Premium Sayfa Blobu hesabı, Premium performans için yapılandırılmış genel amaçlı bir hesaptır. Genel amaçlı v2 depolama hesapları önerilir.

Yönetilmeyen diskler için Premium sayfa BLOB depolama hesapları kullanıyorsanız ve uygulamanız tek bir depolama hesabının ölçeklenebilirlik hedeflerini aşarsa, Microsoft yönetilen disklere geçiş yapmanızı önerir. Yönetilen diskler hakkında daha fazla bilgi için bkz. [VM 'ler için Azure disk depolama genel bakış](../../virtual-machines/managed-disks-overview.md).

Yönetilen disklere geçiş yapıamıyorsanız, uygulamanızı birden çok depolama hesabı kullanmak ve bu depolama hesaplarında bölümlemek için derleyin. Örneğin, birden çok VM arasında 51 TB diskler eklemek istiyorsanız, bunları iki depolama hesabına yayın. 35 TB, tek bir Premium depolama hesabının sınırlıdır. Tek bir Premium performans depolama hesabının 35 TB 'den fazla sağlanan disk üzerinde hiçbir şekilde bulunmadığından emin olun.

## <a name="see-also"></a>Ayrıca bkz.

- [Standart depolama hesapları için ölçeklenebilirlik ve performans hedefleri](../common/scalability-targets-standard-account.md)
- [Premium Blok Blobu depolama hesapları için ölçeklenebilirlik hedefleri](../blobs/scalability-targets-premium-block-blobs.md)
- [Azure abonelik limitleri ve kotaları](../../azure-resource-manager/management/azure-subscription-service-limits.md)
