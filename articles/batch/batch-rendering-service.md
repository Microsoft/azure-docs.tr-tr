---
title: İşleme genel bakış
description: İşleme için Azure 'un kullanılmasına giriş ve Azure Batch işleme özelliklerine genel bakış
author: mscurrell
ms.author: markscu
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 1cd07f9322837c03e15aaeabec993820deb3170a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98232123"
---
# <a name="rendering-using-azure"></a>Azure ile işleme

İşleme, 3B modeller alma ve bunları 2B görüntülere dönüştürme işlemidir. 3B sahne dosyaları, Autodesk 3ds Max, Autodesk Maya ve Blender gibi uygulamalarda yazılır.  Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray ve Blender döngüleri gibi işleme uygulamaları 2B görüntüler üretir.  Sahne dosyalarından bazen tek görüntüler oluşturulur. Ancak, birden çok görüntü modellemek ve işlemek ve sonra bunları bir animasyonda birleştirmek yaygındır.

İşleme iş yükü, medyada ve eğlence sektöründe özel etkiler (VFX) için yoğun bir şekilde kullanılır. İşleme reklamcılık, perakende, petrol ve doğalgaz ve üretim gibi diğer birçok sektörde de kullanılır.

İşleme süreci hesaplama açısından yoğun bir işlemdir; üretilecek birçok çerçeve/görüntü olabilir ve her görüntünün işlenmesi birçok saat sürebilir.  Bu nedenle, çok sayıda oluşturmayı paralel olarak çalıştırmak ve GPU 'Lar dahil olmak üzere çok çeşitli donanımlar kullanmak için Azure 'u kullanan mükemmel bir toplu işlem iş yüküdür.

## <a name="why-use-azure-for-rendering"></a>Azure neden işleme için kullanılmalıdır?

Birçok nedenden dolayı işleme, Azure için kusursuz bir iş yüküdür:

* İşleme işleri, birden çok VM kullanılarak paralel şekilde çalıştırılabilen birçok parçaya ayrılabilir:
  * Animasyonlar birçok kareden oluşur ve her çerçeve paralel olarak oluşturulabilir.  Her çerçeveyi işlemek için kullanılabilir olan sanal makineler, tüm çerçeveler ve animasyon üretilebilirler.
  * Bazı işleme yazılımları, tek çerçevelerin kutucuk veya dilimler gibi birden çok parçaya bölünmelerini sağlar.  Her parça ayrı olarak oluşturulabilir ve tüm parçalar tamamlandığında nihai görüntüde birleştirilir.  Kullanılabilir VM 'Ler daha hızlı bir şekilde oluşturulabilir.
* İşleme projeleri, çok büyük ölçekli bir ölçek gerektirebilir:
  * Tek tek kareler karmaşık olabilir ve yüksek kaliteli donanımda bile işlemek için çok saat gerektirebilir; animasyonlar yüzlerce binlerce kareden oluşabilir.  Yüksek kaliteli animasyonları makul bir süre içinde işlemek için büyük miktarda işlem yapılması gerekir.  Bazı durumlarda, 100.000 çekirdeğin üzerinde çekirdekler paralel olarak binlerce çerçeveyi işlemek için kullanılmıştır.
* Projeler proje tabanlıdır ve değişen miktarlarda işlem gerektirir:
  * Gerektiğinde işlem ve depolama kapasitesi ayırır, bir proje sırasında yüküne göre ölçeği yukarı veya aşağı ölçeklendirin ve proje tamamlandığında kaldırın.
  * Tahsis edildiğinde kapasite için ödeme yapın, ancak projeler arasında bir yük olmadığında bu ücret için ödeme yapmayın.
  * Beklenmedik değişiklikler nedeniyle artışlarıyla için Cater; projede beklenmeyen değişiklikler varsa ve bu değişikliklerin sıkı bir zamanlamaya göre işlenmesi gerekiyorsa daha yüksek ölçeklendirin.
* Uygulamaya, iş yüküne ve zaman çerçevesine göre geniş bir donanım seçimi arasından seçim yapın:
  * Azure 'da kullanılabilen ve Batch ile yönetilebilecek ve yönetilebilen çok sayıda donanım vardır.
  * Projeye bağlı olarak, gereksinim en iyi fiyat/performans veya en iyi genel performans için olabilir.  Farklı sahneler ve/veya işleme uygulamaları farklı bellek gereksinimlerine sahip olur.  Bazı işleme uygulamaları, en iyi performans veya belirli özellikler için GPU 'ları kullanabilir. 
* Düşük öncelikli veya [spot VM 'ler](https://azure.microsoft.com/pricing/spot/) maliyeti azaltır:
  * Düşük öncelikli ve spot VM 'Ler, standart sanal makinelere kıyasla büyük bir indirimle sağlanır ve bazı iş türlerine uygundur.
  
## <a name="existing-on-premises-rendering-environment"></a>Mevcut şirket içi işleme ortamı

En yaygın durum,, PipelineFX Quya, Royal render, Thinkbox son tarihi veya özel bir uygulama gibi bir işleme yönetimi uygulaması tarafından yönetilmekte olan mevcut bir şirket içi oluşturma grubu olması içindir.  Gereksinim, Azure VM 'Leri kullanarak şirket içi işleme grubu kapasitesini genişletmelidir.

Azure altyapı ve Hizmetleri, Azure 'un Şirket içi kapasiteyi tamamlamak için kullanıldığı bir karma ortam oluşturmak için kullanılır. Örnek:

* Azure kaynaklarını şirket içi işleme grubuyla aynı ağa yerleştirmek için bir [sanal ağ](../virtual-network/virtual-networks-overview.md) kullanın.
* Bant genişliği kullanımını ve gecikme süresini azaltmak için Azure 'da kaynak dosyaları önbelleğe almak için Azure 'da veya Azure [HPC önbelleğinde](../hpc-cache/hpc-cache-overview.md) [avere vfxt](../avere-vfxt/avere-vfxt-overview.md) kullanın, performansı en üst düzeye çıkarın
* Mevcut lisans sunucusunun sanal ağda olduğundan emin olun ve çok fazla Azure tabanlı kapasite için gereken ek lisansları satın alın.

## <a name="no-existing-render-farm"></a>Mevcut işleme grubu yok

İstemci iş istasyonları işleme gerçekleştiriyor olabilir, ancak işleme yükü artmakta ve iş istasyonu kapasitesini yalnızca kullanması çok uzun sürüyor.

İki ana seçenek mevcuttur:

* Royal oluşturma gibi bir şirket içi işleme Yöneticisi dağıtın ve daha fazla kapasite veya performans gerektiğinde Azure 'u kullanmak için bir karma ortam yapılandırın. Bir işleme Yöneticisi, iş yüklerini işlemek için özel olarak uyarlanmış ve popüler istemci uygulamalarına yönelik Eklentiler, işleme işlerinin kolayca gönderilmesini sağlayan eklentiler dahil edilir.

* İşlem kapasitesini ayırmak ve yönetmek için Azure Batch kullanan ve işleme işlerini çalıştırmak için iş zamanlamayı sağlayan özel bir çözüm.

## <a name="next-steps"></a>Sonraki adımlar

 [Mevcut bir şirket içi işleme grubunu genişletmek Için Azure altyapı ve hizmetlerini nasıl kullanacağınızı](https://azure.microsoft.com/solutions/high-performance-computing/rendering/)öğrenin.

[Azure Batch işleme özellikleri](batch-rendering-functionality.md)hakkında daha fazla bilgi edinin.
