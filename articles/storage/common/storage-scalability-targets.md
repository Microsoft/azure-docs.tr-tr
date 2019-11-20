---
title: Azure depolama ölçeklenebilirlik ve performans hedefleri-depolama hesapları
description: Azure depolama hesapları için kapasite, istek hızı ve gelen ve giden bant genişliği dahil olmak üzere ölçeklenebilirlik ve performans hedefleri hakkında bilgi edinin.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 293d0de024ad815b4755d48833b7058c29ebcd5a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513277"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Depolama hesapları için Azure depolama ölçeklenebilirlik ve performans hedefleri

Bu makalede, Azure depolama hesapları için ölçeklenebilirlik ve performans hedefleri ayrıntılı olarak açıklanır. Burada listelenen ölçeklenebilirlik ve performans hedefleri, yüksek kaliteli hedeflerdir, ancak ulaşılabilir ' dir. Her durumda, depolama hesabınız tarafından elde edilen istek hızı ve bant genişliği depolanan nesnelerin boyutuna, kullanılan erişim desenlerine ve uygulamanızın gerçekleştirdiği iş yükünün türüne bağlıdır.

Performansını test ettiğinizden emin olun ve bunun performans gereksinimini karşılayıp karşılamadığını tespit edin. Mümkünse, trafik hızında ani artışlar önleyin ve trafiğin bölümler arasında iyi bir şekilde dağıtıldığından emin olun.

Uygulamanız iş yükünüz için bir bölümün işleyebileceği sınıra ulaştığında, Azure Storage 503 (sunucu meşgul) hata kodunu veya hata kodu 500 (Işlem zaman aşımı) yanıtlarını döndürmeye başlar. 503 hatası oluşuyorsa, yeniden denemeler için uygulamanızı bir üstel geri alma ilkesi kullanacak şekilde değiştirmeyi göz önünde bulundurun. Üstel geri alma, bölümdeki yükün azaltılmasını ve söz konusu bölüme giden trafik artışlarını kolaylaştırır.

## <a name="storage-account-scale-limits"></a>Depolama hesabı ölçek sınırları

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Premium performans depolama hesabı ölçek sınırları

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Depolama kaynak sağlayıcısı ölçek sınırları

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob depolama ölçek hedefleri

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure dosyaları ölçek hedefleri

Azure dosyaları ve Azure Dosya Eşitleme için ölçek ve performans hedefleri hakkında daha fazla bilgi için bkz. [Azure dosyaları ölçeklenebilirlik ve performans hedefleri](../files/storage-files-scale-targets.md).

> [!IMPORTANT]
> Depolama hesabı sınırları tüm paylaşımlar için geçerlidir. Depolama hesaplarının en büyük değerine kadar ölçeklendirilmesi yalnızca depolama hesabı başına yalnızca bir adet paylaşma varsa ulaşılabilir.
>
> 5 TiB 'den büyük standart dosya paylaşımlarında bazı sınırlamalar ve bölgesel kısıtlamalar vardır.
> Sınırlamalar, bölgesel bilgiler ve bu büyük dosya paylaşımı boyutlarını etkinleştirmeye yönelik yönergelerin listesi için, Azure dosyaları Planlama Kılavuzu ' ndaki [daha büyük dosya paylaşımlarına (Standart katman)](../files/storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) ekleme bölümüne bakın.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Premium dosyalar ölçek hedefleri

Premium dosyalar için göz önünde bulundurmanız gereken üç sınırlama kategorisi vardır: depolama hesapları, paylaşımlar ve dosyalar.

Örneğin: tek bir paylaşımın 100.000 ıOPS ile elde edilebilir ve tek bir dosya 5.000 ıOPS 'ye kadar ölçeklendirebilirler. Bu nedenle, örneğin, bir paylaşımda üç dosya varsa, bu paylaşımdan alabileceğiniz Maksimum IOPS 15.000 olur.

#### <a name="premium-file-share-limits"></a>Premium dosya paylaşma sınırları

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure Dosya Eşitleme ölçek hedefleri

Azure Dosya Eşitleme sınırsız kullanım hedefi ile tasarlanmıştır, ancak sınırsız kullanım her zaman mümkün değildir. Aşağıdaki tabloda Microsoft 'un test olan sınırları ve hangi hedeflerin sabit sınırları olduğunu gösterilmektedir:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure kuyruk depolama ölçek hedefleri

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure Tablo depolama ölçek hedefleri

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Depolama fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/storage/)
- [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../../azure-subscription-service-limits.md)
- [Azure depolama çoğaltma](../storage-redundancy.md)
- [Microsoft Azure Depolama Performansı ve Ölçeklenebilirlik Onay Listesi](../storage-performance-checklist.md)
