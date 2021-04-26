---
title: Azure 'da SAP HANA mimarisi (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA dağıtma mimarisi (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f45b84360c941e879461500a033ada3642fd5cbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671611"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Azure 'da SAP HANA (büyük örnekler) mimarisi

Yüksek düzeyde, Azure 'daki SAP HANA (büyük örnekler) çözümdeki VM 'lerde bulunan SAP uygulama katmanı vardır. Veritabanı katmanı, Azure IaaS 'ye bağlı aynı Azure bölgesindeki büyük bir örnek damgasında bulunan SAP TDı tarafından yapılandırılmış donanımlar üzerinde bulunur.

> [!NOTE]
> SAP uygulama katmanını SAP DBMS katmanıyla aynı Azure bölgesine dağıtın. Bu kural, Azure 'daki SAP iş yükleri hakkında yayımlanan bilgiler bölümünde da belgelenmiştir. 

Azure 'daki SAP HANA genel mimarisi (büyük örnekler), SAP HANA veritabanı için sanallaştırılmamış, çıplak ve yüksek performanslı bir sunucu olan SAP TDı sertifikalı bir donanım yapılandırması sağlar. Ayrıca, Azure 'un gereksinimlerinizi karşılamak üzere SAP uygulama katmanının kaynaklarını ölçeklendirmesine yönelik özellik ve esneklik sağlar.

![Azure 'da SAP HANA mimari genel bakış (büyük örnekler)](./media/hana-overview-architecture/image1-architecture.png)

Gösterilen mimari üç bölüme ayrılmıştır:

- **Sağ**: son kullanıcıların sap gibi LOB uygulamalarına erişebilmeleri için veri merkezlerinde farklı uygulamalar çalıştıran bir şirket içi altyapıyı gösterir. İdeal olarak, bu şirket içi altyapı [ExpressRoute](https://azure.microsoft.com/services/expressroute/)ile Azure 'a bağlanır.

- **Center**: Azure IaaS 'yi gösterir ve bu durumda, bir DBMS sistemi olarak SAP HANA kullanan SAP veya diğer uygulamaları barındırmak Için VM 'leri kullanın. VM 'Lerin sağladığı bellekle işlev gösteren daha küçük HANA örnekleri, uygulama katmanıyla birlikte VM 'lerde dağıtılır. Sanal makineler hakkında daha fazla bilgi için bkz. [sanal makineler](https://azure.microsoft.com/services/virtual-machines/).

   Azure Ağ Hizmetleri, SAP sistemlerini diğer uygulamalarla birlikte sanal ağlara gruplandırmak için kullanılır. Bu sanal ağlar, şirket içi sistemlere ve Azure 'daki SAP HANA (büyük örnekler) bağlanır.

   SAP NetWeaver uygulamaları ve Azure 'da çalışması desteklenen veritabanları için bkz. [sap destek notunun #1928533 – Azure 'DA SAP uygulamaları: Desteklenen Ürünler ve Azure VM türleri](https://launchpad.support.sap.com/#/notes/1928533). SAP çözümlerini Azure 'da dağıtmaya ilişkin belgeler için, bkz.:

  -  [Windows sanal makinelerinde SAP kullanma](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [Azure sanal makineler 'de SAP çözümlerini kullanma](get-started.md)

- **Sol**: Azure büyük örnek damgasında SAP HANA TDI sertifikalı donanımı gösterir. HANA büyük örnek birimleri, Şirket içinden Azure 'a bağlantı ile aynı teknolojiyi kullanarak Azure aboneliğinizin sanal ağlarına bağlanır. 2019 Mayıs itibariyle, ExpressRoute ağ geçidi ile Azure VM 'Leri arasında iletişim kurmaya izin veren bir iyileştirme tanıtılmıştır. ExpressRoute hızlı yolu olarak adlandırılan bu iyileştirme, Bu mimaride görüntülenir (kırmızı çizgiler). 

Azure büyük örnek damgası, aşağıdaki bileşenleri birleştirir:

- **Bilgi işlem**: gerekli bilgi işlem yeteneğini sağlayan, farklı nesil Intel Xeon işlemcileri temel alan sunucular ve SAP HANA sertifikalıdır.
- **Ağ**: bilgi işlem, depolama ve LAN bileşenlerini birbirine bağlayan Birleşik yüksek hızlı bir ağ yapısı.
- **Depolama**: Birleşik bir ağ dokusunda erişilen bir depolama altyapısı. Belirtilen belirli depolama kapasitesi, dağıtılan Azure (büyük örnekler) yapılandırmasındaki belirli SAP HANA bağlıdır. Ek bir aylık maliyette daha fazla depolama kapasitesi mevcuttur.

Büyük örnek damgasının çok kiracılı altyapısı içinde, müşteriler yalıtılmış kiracılar olarak dağıtılır. Kiracının dağıtımında, Azure kaydınız içinde bir Azure aboneliği adı alırsınız. Bu Azure aboneliği, HANA büyük örneğinin faturalandırılacağımız biridir. Bu kiracıların Azure aboneliğiyle 1:1 ilişkisi vardır. Bir ağ için, farklı Azure aboneliklerine ait farklı sanal ağlardan tek bir Azure bölgesinde tek bir kiracıya dağıtılan bir HANA büyük örnek birimine erişmek mümkündür. Bu Azure abonelikleri aynı Azure kaydına ait olmalıdır. 

VM 'Lerde olduğu gibi Azure 'daki SAP HANA (büyük örnekler) birden çok Azure bölgesinde sunulur. Olağanüstü durum kurtarma özellikleri sunmak için, kabul etme seçeneğini belirleyebilirsiniz. Tek bir coğrafi siyasi bölge içindeki farklı büyük örnek damgaları birbirlerine bağlanır. Örneğin, ABD Batı ve ABD Doğu içindeki HANA büyük örnek damgaları, olağanüstü durum kurtarma çoğaltması için özel bir ağ bağlantısı üzerinden bağlanır. 

Azure sanal makineler ile farklı VM türleri arasında seçim yapabileceğiniz gibi, farklı iş yükü türleri SAP HANA için özel farklı HANA büyük örnek SKU 'larından seçim yapabilirsiniz. SAP, Intel işlemci nesilleri temelinde değişen iş yükleri için bellek-işlemci yuvası oranlarını uygular. Aşağıdaki tabloda sunulan SKU türleri gösterilmektedir.

[HLI için kullanılabilir SKU 'lar için kullanılabilir SKU 'ları](hana-available-skus.md)bulabilirsiniz.

**Sonraki adımlar**
- [SAP HANA (büyük örnekler) ağ mimarisine](hana-network-architecture.md) başvurun
