---
title: Azure HPC önbelleğine genel bakış
description: Yüksek performanslı bilgi işlem için bir dosya erişim Hızlandırıcısı çözümü olan Azure HPC Cache 'i açıklar
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 2085efc5f38b2252e40f4aeb1ebfa16bf38147b4
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256149"
---
# <a name="what-is-azure-hpc-cache"></a>Azure HPC Önbelleği nedir?

Azure HPC Cache, yüksek performanslı bilgi işlem (HPC) görevleri için verilerinize erişimi hızlandırır. Azure HPC Cache, Azure 'daki dosyaları önbelleğe alarak, bulut bilgi işlemin ölçeklenebilirliğini mevcut iş akışınıza getirir. Bu hizmet, verilerinizin WAN bağlantıları arasında depolandığı iş akışları (örneğin, yerel veri merkezi ağ bağlantılı depolama (NAS) ortamınızda) için de kullanılabilir.

Azure HPC önbelleğinin Azure portal kolayca başlatılması ve izlenmesi kolay bir işlemdir. Mevcut NFS depolaması veya yeni blob kapsayıcıları, arka uç depolama hedefini değiştirseniz bile, istemci erişiminin basit olmasını sağlayan, toplanmış ad alanının bir parçası haline gelebilir.

## <a name="overview-video"></a>Genel bakış videosu

[![video küçük resmi: Azure HPC önbelleğine genel bakış-video sayfasını ziyaret etmek için tıklayın](media/video-1-overview.png)](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)

[Azure HPC Cache 'e kısa bir genel bakış](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)izlemek için yukarıdaki resme tıklayın.

## <a name="use-cases"></a>Uygulama alanları

Azure HPC Cache, aşağıdaki gibi iş akışları için en iyi verimliliği geliştirir:

* Okuma ağır dosya erişimi iş akışı
* NFS erişimli depolama, Azure Blob veya her ikisine de depolanan veriler
* 75.000 adede kadar CPU çekirdeğini hesaplama grupları

Azure HPC önbelleği birçok sektör genelinde çok çeşitli iş akışlarına eklenebilir. Çok sayıda makinenin bir dosya kümesine ölçekli ve düşük gecikme süresiyle bu hizmetten faydalanır olması gereken tüm sistem. Aşağıdaki bölümler belirli örneklere sahiptir.

### <a name="visual-effects-vfx-rendering"></a>Görsel etkiler (VFX) işleme

Medya ve eğlence sürümünde Azure HPC önbelleği, zaman açısından kritik işleme projeleri için veri erişimini hızlandırabilir. VFX işleme iş akışları çoğunlukla çok sayıda işlem düğümü tarafından son dakikalık işleme gerektirir. Bu iş akışlarının verileri genellikle şirket içi bir NAS ortamında bulunur. Azure HPC önbelleği, gecikme süresini azaltmak ve isteğe bağlı işleme esnekliği geliştirmek için bulutta bu dosya verilerini önbelleğe alabilir.

### <a name="life-sciences"></a>Yaşam bilimleri

Birçok yaşam bilimleri iş akışı, genişleme dosya önbelleklemesi avantajlarından yararlanabilir.

Genomik analiz iş akışlarının Azure 'a bağlantı noktası almak isteyen bir Araştırma Enstitüsü, Azure HPC cache kullanarak bunları kolayca kaydırabilirler. Önbellek, POSIX dosya erişimi sağladığından, var olan istemci iş akışını bulutta çalıştırmak için hiçbir istemci tarafı değişikliği gerekli değildir.

Azure HPC Cache ayrıca ikincil analiz, ilaç macological simülasyonu veya AI odaklı görüntü analizi gibi görevlerde verimliliği artırmak için de yararlanılabilir olabilir.

### <a name="financial-services-analytics"></a>Finansal hizmetler analizi

Azure HPC önbellek dağıtımı, finansal hizmetler şirketlerinin stratejik kararlar vermesini daha iyi kavramak için nicel Analysis hesaplamaları, risk analizi iş yükleri ve Monte Carlo benzetimlerinin hızlandırılmasına yardımcı olabilir.

## <a name="region-availability"></a>Bölge kullanılabilirliği

Azure HPC önbelleğinin kullanılabilir olduğunu öğrenmek için [bölgeye göre Azure küresel altyapı ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache) sayfasını ziyaret edin.

Azure HPC önbelleği tek bir bölgede bulunur. Burada yer alan blob kapsayıcılarına bağlanırsanız diğer bölgelerde depolanan verilere erişebilir. Önbellek müşteri verilerini kalıcı olarak depolamaz.

## <a name="next-steps"></a>Sonraki adımlar

* Özellikleri hakkında daha fazla bilgi edinmek için [Azure HPC önbellek ürün sayfasını](https://azure.microsoft.com/services/hpc-cache) okuyun
* Ürün [önkoşulları](hpc-cache-prerequisites.md) hakkında bilgi edinin
* Azure portal [bir Azure HPC önbelleği oluşturun](hpc-cache-create.md)
