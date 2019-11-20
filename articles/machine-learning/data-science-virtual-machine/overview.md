---
title: Azure Veri Bilimi Sanal Makinesi nedir?
description: Windows ve Linux Veri Bilimi Sanal Makineleri için önemli analiz senaryoları ve bileşenler.
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: 8359a8407e93a2b31466342b82539cef04b0fe01
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802189"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Linux ve Windows için Azure Veri Bilimi Sanal Makinesi nedir?

Veri Bilimi Sanal Makinesi (DSVM), veri bilimi yapmak için özel olarak oluşturulan Azure bulut platformunda özelleştirilmiş bir VM görüntüsüdür. Önceden yüklenmiş ve gelişmiş analiz için akıllı uygulamalar oluşturmaya başlamak üzere önceden yapılandırılmış birçok popüler veri bilimi aracı vardır. 

DSVM şu şirket üzerinde kullanılabilir:
+ Windows Server 2016
+ Ubuntu 16,04 LTS ve CentOS 7,4

> [!NOTE]
> Derin öğrenme için tüm VM araçları Veri Bilimi Sanal Makinesi katlanmıştır. 


## <a name="why-choose-the-dsvm"></a>DSVM neden seçmelisiniz?
Veri Bilimi Sanal Makinesi amacı, tüm beceri seviyeleri ve sektör genelinde, önceden yapılandırılmış bir veri bilimi ortamı sayesinde veri uzmanları sağlamaktır. Kendi kendinize benzer bir çalışma alanı kullanmak yerine, bir DSVM sağlayabilirsiniz. Bu seçenek, yükleme, yapılandırma ve paket yönetim işlemlerinde günlerinizi veya hatta _haftaları_ tasarruf edebilir. DSVM ayrıldıktan sonra veri bilimi projenizin üzerinde çalışmaya hemen başlayabilirsiniz.

## <a name="sample-use-cases"></a>Örnek kullanım örnekleri

Aşağıda, DSVM müşterileri için bazı yaygın kullanım durumları gösterilmektedir.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Veri bilimi iş yüklerini buluta taşıma

DSVM, bir ekipte bulunan tüm veri bilimlerinin denemeleri doğrulamak ve işbirliğini yükseltmek için tutarlı bir kuruluma sahip olmasını sağlamak üzere yerel masaüstlerini yönetilen bir bulut Desktop ile değiştirmek isteyen veri bilimi ekipleri için bir temel yapılandırma sağlar. Ayrıca, sysadmin yükünü azaltarak maliyetleri düşürür. Bu yük azaltma, gelişmiş analizler için yazılım paketlerini değerlendirmek, yüklemek ve sürdürmek için gereken zamana kaydedilir.

### <a name="data-science-training-and-education"></a>Veri bilimi eğitimi
Veri bilimi sınıflarını öğreten kurumsal traers ve eğitimciler, genellikle bir sanal makine görüntüsü sağlar. Görüntü, öğrencilerin tutarlı bir kuruluma sahip olmasını ve örneklerin öngörülebilir bir şekilde çalışmasını sağlar. 

DSVM, destek ve uyumsuzluk sorunlarını ele alan tutarlı bir kurulum ile isteğe bağlı bir ortam oluşturur. Başta daha kısa eğitim sınıfları olmak üzere bu ortamların sıklıkla oluşturulması gereken durumlar önemli ölçüde avantajlıdır.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Büyük ölçekli projeler için isteğe bağlı esnek kapasite
Veri bilimi hack maratonları/yarışmaları veya büyük ölçekli veri modelleme ve araştırma, genellikle kısa süre için ölçekli donanım kapasitesi gerektirir. DSVM, yüksek performanslı bilgi işlem kaynaklarının çalışmasına izin veren ölçeği genişletilmiş sunucularda veri bilimi ortamını hızla çoğaltmaya yardımcı olabilir.

### <a name="custom-compute-power-for-azure-notebooks"></a>Azure Notebooks için özel işlem gücü
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) , bulutta JUPITER not defterlerini geliştirmek, çalıştırmak ve paylaşmak için ücretsiz bir barındırılan hizmettir. Ücretsiz hizmet katmanı, 4 GB bellek ve 1 GB veri ile sınırlıdır. 

Tüm limitleri serbest bırakmak için bir DSVM 'ye veya jupi sunucusu üzerinde çalışan başka bir VM 'ye bir not defteri projesi ekleyebilirsiniz. Azure Active Directory (örneğin, bir kurumsal hesap) kullanarak bir hesapla Azure Notebooks oturum açarsanız, Not defterleri bu hesapla ilişkili tüm aboneliklerde otomatik olarak DSVMs 'yi gösterir. Kullanılabilir işlem gücünü genişletmek için [Azure Notebooks BIR DSVM](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) ekleyebilirsiniz.

### <a name="short-term-experimentation-and-evaluation"></a>Kısa süreli deneme ve değerlendirme
Yeni veri bilimi [araçlarını](./tools-included.md), özellikle de yayınlanmış [örneklerimizden ve yönergelerden](./dsvm-samples-and-walkthroughs.md)bazılarını izleyerek değerlendirmek ya da öğrenmek için dsvm 'yi kullanabilirsiniz.


### <a name="deep-learning-with-gpus"></a>GPU 'Lar ile derin öğrenme
DSVM 'de, eğitim modelleriniz grafik işleme birimlerine (GPU 'Lar) dayalı donanımlar üzerinde derin öğrenme algoritmaları kullanabilir. DSVM, Azure platformunun VM ölçeklendirme özelliğinden yararlanarak, gereksinimlerinize göre bulutta GPU tabanlı donanımlar kullanmanıza yardımcı olur. Büyük modellere eğitim yaparken veya aynı işletim sistemi diskini korurken yüksek hızda hesaplamalar yapmanız gerektiğinde GPU tabanlı bir VM 'ye geçiş yapabilirsiniz. DSVM ile N serisi GPU etkin sanal makine SKU 'Larını seçebilirsiniz. Lütfen Azure Ücretsiz hesapları GPU etkin sanal makine SKU 'Larını desteklemez.

DSVM 'nin Windows Server 2016 sürümü, derin öğrenme çerçevelerinin GPU sürücüleri, çerçeveleri ve GPU sürümleriyle önceden yüklenmiş olarak gelir. Linux sürümünde, GPU 'Larda derin öğrenme hem CentOS hem de Ubuntu DSVMs üzerinde etkinleştirilmiştir. 

Ayrıca, DSVM 'nin Ubuntu, CentOS veya Windows 2016 sürümünü GPU 'lara dayalı olmayan bir Azure sanal makinesine dağıtabilirsiniz. Bu durumda, tüm derin öğrenme çerçeveleri CPU moduna geri dönecektir.
 
[Kullanılabilir derin öğrenme ve AI çerçeveleri hakkında daha fazla bilgi edinin](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>DSVM 'ye neler dahildir?

[Burada](tools-included.md)hem Windows hem de LINUX dsvm 'deki araçların tam listesini görebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalelerle daha fazla bilgi edinin:

+ Windows:
  + [Windows DSVM’si ayarlama](provision-vm.md)
  + [Windows DSVM üzerinde yapabileceğiniz on şey](vm-do-ten-things.md)

+ Linux:
  + [Linux DSVM 'YI ayarlama (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Linux DSVM ayarlama (CentOS)](linux-dsvm-intro.md)
  + [Linux DSVM üzerinde veri bilimi](linux-dsvm-walkthrough.md)
