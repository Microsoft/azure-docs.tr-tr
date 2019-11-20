---
title: Microsoft Azure FXT Edge Filer genel bakış
description: Yüksek performanslı bilgi işlem için etkin bir arşiv ve dosya erişimi Hızlandırıcısı çözümü olan Azure FXT Edge Filer karma depolama önbelleğini açıklar
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: acf35015ff7851f7ea2a2527852be2573512e35d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254848"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Azure FXT Edge Filer karma depolama önbelleği nedir?

Azure FXT Edge filigran, hızlı dosya erişimi ve yüksek performanslı bilgi işlem (HPC) görevleri için etkin arşiv sağlayan bir karma depolama önbelleği gereci.

Yerel bir veri merkezinde, uzaktan veya bulutta depolanıp, birden çok veri kaynağı ile birlikte kullanılır. Azure FXT Edge Filer, farklı depolama sistemlerindeki veriler için Birleşik bir ad alanı sağlayabilir.

Üç veya daha fazla FXT Edge filigran donanım cihazı, önbelleği sağlamak için bir kümelenmiş dosya sistemi olarak birlikte çalışır. Gerekli donanımı satın alma hakkında daha fazla bilgi için Microsoft temsilcinize başvurun. 

Daha fazla bilgi edinmek için [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/)'daki ürün bilgilerini ve veri sayfasını okuyun.

## <a name="use-cases"></a>Kullanım örnekleri

Azure FXT Edge Filer, aşağıdaki gibi iş akışları için en iyi verimliliği geliştirir:

* Okuma ağır dosya erişimi iş akışı 
* NFSv3 veya SMB2 protokolleri
* 1000 ile 100.000 arasındaki CPU çekirdekleri için işlem grupları

### <a name="nas-optimization-and-scaling"></a>NAS iyileştirme ve ölçeklendirme

Mevcut NetApp 'e ve Dell EMC Ise 'ye erişimi sorunsuz şekilde kullanabilmek için Azure FXT Edge Filer önbelleğini kullanabilirsiniz. Ayrıca, istemci tarafında veri erişimi işlemlerine yeniden çalışma gerektirmeden ölçeklenebilirlik sağlamak için Azure Blob veya diğer bulut depolama alanı da ekleyebilirsiniz. 

### <a name="wan-caching"></a>WAN önbelleği

Azure FXT Edge filigran, ihtiyaç duydukları veriler başka bir yerde depolandığında Power Users 'tan hızlı dosya erişimini desteklemek için kullanılabilir. Yedekleme ve diğer veri yönetimi sistemlerini merkezi bir veri merkezinde tutarken erişim sağlayın. 

### <a name="active-archive-in-azure-blob"></a>Azure Blob 'da etkin Arşiv

Veri merkezinizi, Azure FXT Edge fili ile erişim noktası olarak bulut depolama alanına genişletin. 

## <a name="features"></a>Özellikler 

İki donanım modeli mevcuttur. 

| Model | He | NVMe SSD | Ağ bağlantı noktaları | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 GB/10Gb + 2 x 1Gb |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 GB/10Gb + 2 x 1Gb |


## <a name="next-steps"></a>Sonraki adımlar

* [Belirtimleri](fxt-specs.md) veya [yükleme öğreticisini](fxt-install.md)okuyarak Azure FXT Edge filsi hakkında öğrenmeye devam edin.
* Azure FXT Edge Filer 'ın [Azure FXT Edge filigran ürün sayfasında](https://azure.microsoft.com/services/fxt-edge-filer/)nasıl satın alınacağını öğrenin.