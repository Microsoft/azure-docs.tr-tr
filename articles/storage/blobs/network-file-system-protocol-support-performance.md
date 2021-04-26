---
title: NFS 3,0 performans konuları Azure Blob depolama (Önizleme) | Microsoft Docs
description: Bu makaledeki önerileri kullanarak ağ dosya sistemi (NFS) 3,0 depolama isteklerinizin performansını iyileştirin.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: de511fa30caa608c2dc87b6c0ba166ed56ff9499
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490191"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>Ağ dosya sistemi (NFS) 3,0 performans konuları Azure Blob depolama (Önizleme)

Blob Storage artık ağ dosya sistemi (NFS) 3,0 protokolünü desteklemektedir. Bu makale, depolama isteklerinizin performansını iyileştirmenize yardımcı olan öneriler içerir. Azure Blob depolamada NFS 3,0 desteği hakkında daha fazla bilgi edinmek için bkz. [Azure Blob Storage 'da (Önizleme) ağ dosya sistemi (NFS) 3,0 protokol desteği](network-file-system-protocol-support.md).

> [!NOTE]
> Azure Blob depolamada NFS 3,0 protokol desteği genel önizlemeye sunuldu. Şu bölgelerde Standart katman performansına sahip GPV2 Storage hesaplarını destekler: Avustralya Doğu, Kore Orta, Doğu ABD ve Orta Güney ABD. Önizleme Ayrıca tüm genel bölgelerde Premium performans katmanıyla Blok Blobu destekler.

## <a name="add-clients-to-increase-throughput"></a>Aktarım hızını artırmak için istemciler ekleyin 

Azure Blob depolama, en fazla depolama hesabı çıkış ve giriş sınırına ulaşana kadar doğrusal bir şekilde ölçeklendirilir. Bu nedenle, uygulamalarınız daha fazla istemci kullanarak daha yüksek aktarım hızı elde edebilir.  Depolama hesabı çıkış ve giriş sınırlarını görüntülemek için bkz. [Standart depolama hesapları Için ölçeklenebilirlik ve performans hedefleri](../common/scalability-targets-standard-account.md).

Aşağıdaki grafikte daha fazla istemci eklerken bant genişliğinin nasıl artdığı gösterilmektedir. Bu grafikte, istemci bir sanal makinedir (VM) ve hesap standart performans katmanını kullanır. 

> [!div class="mx-imgBorder"]
> ![Standart performans](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

Aşağıdaki grafik, Premium performans katmanını kullanan bir hesaba uygulandığında aynı etkiyi gösterir.

> [!div class="mx-imgBorder"]
> ![Premium performans](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>Küçük ölçekli uygulamalar için Premium performans katmanını kullanın

Tüm uygulamalar daha fazla istemci ekleyerek ölçeklenmez. [Azure Premium Blok Blob depolama hesabı](storage-blob-create-account-block-blob.md) , bu uygulamalar için tutarlı düşük gecikme süresi ve yüksek işlem ücretleri sunar. Premium Blok Blobu depolama hesabı, daha az iş parçacığı ve istemcisi ile en yüksek bant genişliğine ulaşabilir. Örneğin, tek bir istemci ile bir Premium Blok Blob depolama hesabı, standart performans genel amaçlı v2 depolama hesabıyla kullanılan aynı kuruluma göre **2.3 x** bant genişliğine sahip olabilir. 

Aşağıdaki grafikteki her çubuk, Premium ve standart performans depolama hesapları arasındaki elde edilen bant genişliğine göre farklılık gösterir. İstemci sayısı arttıkça, bu farklılık azalır.  

> [!div class="mx-imgBorder"]
> ![Göreli performans](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-data"></a>Verilerdeki sık kullanılan üzerine yazma yapmaktan kaçının

Yeni yazma işleminden farklı bir üzerine yazma işlemi tamamlaması daha uzun zaman alır. Bunun nedeni, özellikle kısmen yerinde bir dosya düzenleme işlemi olan bir NFS üzerine yazma işlemi, temel alınan blob işlemlerinin bir birleşimidir: okuma, değiştirme ve yazma işlemi. Bu nedenle, sık sık yerinde düzenleme gerektiren bir uygulama, NFS etkin BLOB depolama hesapları için uygun değildir. 

## <a name="other-best-practice-recommendations"></a>Diğer en iyi yöntem önerileri 

- Yeterli ağ bant genişliğine sahip VM 'Leri kullanın.

- İş yükleriniz buna izin vermek için birden çok bağlama noktası kullanın.

- Mümkün olduğunca çok iş parçacığı kullanın.

- Büyük blok boyutlarını kullanın.

- Depolama hesabı ile aynı bölgede bulunan bir istemciden depolama istekleri yapın. Bu, ağ gecikmesini iyileştirebilir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Blob depolamada NFS 3,0 desteği hakkında daha fazla bilgi edinmek için bkz. [Azure Blob Storage 'da (Önizleme) ağ dosya sistemi (NFS) 3,0 protokol desteği](network-file-system-protocol-support.md).

- Başlamak için bkz. [ağ dosya sistemi (NFS) 3,0 protokolünü (Önizleme) kullanarak blob depolamayı bağlama](network-file-system-protocol-support-how-to.md).
