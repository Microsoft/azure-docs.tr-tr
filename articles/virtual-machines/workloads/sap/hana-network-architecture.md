---
title: Azure 'da SAP HANA ağ mimarisi (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA dağıtımı (büyük örnekler) için ağ mimarisi.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0872d3c798bd5bd94e425869822602e8123517b4
ms.sourcegitcommit: 9858ab651a520c26f0ed18215e650efbf1fc5de9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303611"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (büyük örnekler) ağ mimarisi

Azure ağ hizmetleri 'nin mimarisi, HANA büyük örnek üzerinde SAP uygulamalarının başarıyla dağıtılması için önemli bir bileşendir. Genellikle, Azure 'da SAP HANA (büyük örnekler) dağıtımlarda, farklı türlerdeki veritabanları, CPU kaynak tüketimi ve bellek kullanımı gibi çeşitli SAP çözümlerine sahip daha büyük bir SAP yatay vardır. Bu, tüm BT sistemleri Azure 'da zaten yer alıyor olabilir. SAP manzara, genellikle NetWeaver ve S/4HANA ile SAP HANA ve diğer DBMS 'nin bir karışımını kullanarak bir DBMS noktasından ve SAP uygulama noktasından de karma olarak karma bir uygulamadır. Azure, Azure 'da farklı DBMS, NetWeaver ve S/4HANA sistemlerini çalıştırmanıza olanak tanıyan farklı hizmetler sunar. Azure, Azure 'un Şirket içi yazılım dağıtımlarınıza bir sanal veri merkezi gibi görünmesini sağlamak için de ağ teknolojisi sağlar

Tamamlanmış BT sistemleri Azure 'da barındırılmaz. Azure ağ işlevleri, Azure 'un bir sanal veri merkezi gibi görünmesini sağlamak için şirket içi dünyayı Azure varlıklarınızla bağlamak için kullanılır. Kullanılan Azure ağ işlevleri şunlardır: 

- Azure sanal ağları, şirket içi ağ varlıklarınıza bağlanan [ExpressRoute](https://azure.microsoft.com/services/expressroute/) devresine bağlanır.
- Şirket içinde Azure 'a bağlanan bir ExpressRoute devresi, en az [1 Gbps veya daha yüksek](https://azure.microsoft.com/pricing/details/expressroute/)bant genişliğine sahip olmalıdır. Bu en düşük bant genişliği, VM 'lerde çalışan şirket içi sistemler ve sistemler arasında veri aktarımı için yeterli bant genişliğine izin verir. Ayrıca, şirket içi kullanıcılardan Azure sistemlerine bağlantı için yeterli bant genişliğine de olanak tanır.
- Azure 'daki tüm SAP sistemleri, sanal ağlarda birbirleriyle iletişim kuracak şekilde ayarlanır.
- Şirket içinde barındırılan Active Directory ve DNS, şirket içi üzerinden ExpressRoute aracılığıyla Azure 'a genişletilir veya Azure 'da tamamlanmış olarak çalışır.

HANA büyük örnekleri Azure veri merkezi ağ dokusuna tümleştirmeyle ilgili belirli bir durum için Azure ExpressRoute teknolojisi de kullanılır


> [!NOTE] 
> Yalnızca bir Azure aboneliği, belirli bir Azure bölgesindeki bir HANA büyük örnek damgasında yalnızca bir kiracıya bağlanabilir. Buna karşılık, tek bir HANA büyük örnek damgası kiracısı yalnızca bir Azure aboneliğine bağlanabilir. Bu gereksinim, Azure 'daki diğer faturalandırılabilir nesnelerle tutarlıdır.

Azure 'daki SAP HANA (büyük örnekler) birden çok farklı Azure bölgesinde dağıtılırsa, HANA büyük örnek damgasında ayrı bir kiracı dağıtılır. Aynı SAP yatay 'ın bir parçası olduğu sürece, her ikisini de aynı Azure aboneliği altında çalıştırabilirsiniz. 

> [!IMPORTANT] 
> Yalnızca Azure Resource Manager dağıtım yöntemi Azure 'da SAP HANA desteklenir (büyük örnekler).

 

## <a name="additional-virtual-network-information"></a>Ek sanal ağ bilgileri

Bir sanal ağı ExpressRoute 'a bağlamak için bir Azure ExpressRoute ağ geçidi oluşturulması gerekir. Daha fazla bilgi için bkz. [ExpressRoute Için ExpressRoute ağ geçitleri hakkında](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Azure ExpressRoute ağ geçidi, Azure dışındaki bir altyapıya veya Azure büyük örnek damgasına ExpressRoute ile birlikte kullanılır. Azure ExpressRoute ağ geçidini, bu bağlantılar farklı Microsoft Kurumsal kenar yönlendiricilerinde geldiği sürece en fazla dört farklı ExpressRoute devresine bağlayabilirsiniz. Daha fazla bilgi için bkz. [Azure 'da SAP HANA (büyük örnekler) altyapısı ve bağlantısı](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Bir ExpressRoute bağlantısı kullanarak, bir ExpressRoute ağ geçidiyle elde edilebilecek en fazla aktarım hızı 10 Gbps 'dir. Bir sanal ağda ve şirket içi bir sistemde bulunan bir VM arasında dosya kopyalama (tek kopya akış olarak), farklı ağ geçidi SKU 'Larının tam verimini elde etmez. ExpressRoute ağ geçidinin tüm bant genişliğinden yararlanmak için birden çok akış kullanın. Ya da farklı dosyaları tek bir dosyanın paralel akışlarına kopyalamanız gerekir.


## <a name="networking-architecture-for-hana-large-instance"></a>HANA büyük örneği için ağ mimarisi
HANA büyük örneği için ağ mimarisi dört farklı parçaya ayrılabilir:

- Azure ile şirket içi ağ ve ExpressRoute bağlantısı. Bu bölüm, müşterinin etki alanıdır ve ExpressRoute aracılığıyla Azure 'a bağlanır. Bu ExpressRoute bağlantı hattı, sizin tarafınızdan bir müşteri olarak ücretteymiştir. Bant genişliği, şirket içi varlıklarınız ile bağlanmakta olduğunuz Azure bölgesi arasındaki ağ trafiğini işleyecek kadar büyük olmalıdır. Aşağıdaki şekilde sağ alt köşedeki bölümüne bakın.
- Azure Ağ Hizmetleri, daha önce açıklandığı gibi, sanal ağlarla birlikte, ExpressRoute ağ geçitlerinin eklenmeleri gerekir. Bu bölüm, uygulama gereksinimleriniz, güvenlik ve uyumluluk gereksinimlerinizin uygun tasarımlarını bulmanız gereken bir alandır. HANA büyük örnek 'i kullanmanıza bakılmaksızın, aralarından seçim yapabileceğiniz sanal ağların ve Azure Gateway SKU 'Larının sayısını göz önünde bulundurmanız gereken başka bir nokta vardır. Şeklin sağ üst bölümüne bakın.
- ExpressRoute teknolojisi aracılığıyla Azure 'da HANA büyük örnek bağlantısı. Bu bölüm Microsoft tarafından dağıtılır ve işlenir. Tüm yapmanız gereken, HANA büyük örnekteki varlıklarınızın dağıtımıyla sonra bazı IP adresi aralıkları sağlamak, ExpressRoute devresini sanal ağlara bağlayın. Daha fazla bilgi için bkz. [Azure 'da SAP HANA (büyük örnekler) altyapısı ve bağlantısı](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Azure veri merkezi ağ yapısı ve HANA büyük örnek birimleri arasında bağlantı için müşteri olarak size ek ücret alınmaz.
- Genellikle sizin için saydam olan HANA büyük örnek damgasında ağ iletişimi.

![Azure 'da SAP HANA bağlı sanal ağ (büyük örnekler) ve şirket içi](./media/hana-overview-architecture/image1-architecture.png)

HANA büyük örneğini kullandığınız için şirket içi varlıklarınızın ExpressRoute aracılığıyla Azure 'a bağlanması gerekir. HANA büyük örnek birimlerinde barındırılan HANA örneklerine bağlanan uygulama katmanını barındıran VM 'Leri çalıştıran bir veya birden çok sanal ağa sahip olmanız için de değişiklik yapmaz. 

Azure 'da SAP dağıtımları arasındaki farklar şunlardır:

- Müşteri kiracınızın HANA büyük örnek birimleri, sanal ağlarınıza başka bir ExpressRoute devresi üzerinden bağlanır. Yük koşullarını ayırmak için şirket içi ile Azure sanal ağ ExpressRoute devreleri ve Azure sanal ağları ile HANA büyük örnekleri arasındaki devreler aynı yönlendiricileri paylaşmaz.
- SAP uygulama katmanı ve HANA büyük örneği arasındaki iş yükü profili, birçok küçük istek ve veri aktarımları (sonuç kümeleri) gibi çok sayıda küçük istekte bulunan farklı bir doğası ve uygulama katmanına SAP HANA.
- SAP uygulama mimarisi, verilerin şirket içi ve Azure arasında değiş tokuş edildiği tipik senaryolardaki ağ gecikme süresine kıyasla daha duyarlıdır.
- Azure ExpressRoute ağ geçidinde en az iki ExpressRoute bağlantısı vardır. Şirket içi ve HANA büyük örneklerden bağlanan bir bağlantı. Bu, farklı MSEE 'lar ExpressRoute ağ geçidine bağlanmak üzere yalnızca başka bir iki ek devre için yer bırakır. Bu kısıtlama, ExpressRoute hızlı yolunun kullanımından bağımsızdır. Tüm bağlı devreler, ExpressRoute ağ geçidinin gelen verileri için maksimum bant genişliğini paylaşır.

HANA büyük örnek damgalarının 3. düzeltmesi sayesinde, VM 'Ler ve HANA büyük örnek birimleri arasında karşılaşılan ağ gecikmesi, tipik bir sanal makineden VM ağına gidiş dönüş gecikmesinden daha yüksek olabilir. Azure bölgesine bağlı olarak, ölçülen değerler, [SAP Note #1100926-SSS: ağ performansı](https://launchpad.support.sap.com/#/notes/1100926/E)için aşağıda belirtildiği gibi sınıflandırılan 0,7-MS gidiş dönüş gecikmesini aşabilir. Bir Azure VM ve HANA büyük örnek birimi arasında ağ gidiş dönüş gecikmesini ölçmek için Azure bölgesine ve aracına bağımlıdır, ölçülen gecikme süresi 2 milisaniyeye kadar olabilir. Bununla birlikte, müşteriler SAP HANA tabanlı üretim SAP uygulamalarını SAP HANA büyük örnek üzerinde başarıyla dağıtır. Azure HANA büyük örneğinde iş işlemlerinizi iyice test ettiğinizden emin olun. ExpressRoute hızlı yolu adlı yeni bir işlev, Azure 'daki yalnızca HANA büyük örnekler ve uygulama katmanı VM 'Leri arasındaki ağ gecikmesini azaltabilir (aşağıya bakın). 

HANA büyük örnek damgalarının 4. düzeltmesi sayesinde, HANA büyük örnek damgasına yakınlık halinde dağıtılan Azure VM 'Ler arasındaki ağ gecikmesi, [SAP Note #1100926-SSS ' de belgelendiği gibi ortalama veya daha iyi bir sınıflandırmayla karşılaşmıştır: ](https://launchpad.support.sap.com/#/notes/1100926/E)Azure ExpressRoute hızlı yolu yapılandırıldıysa ağ performansı (aşağıya bakın). Azure VM 'Leri, düzeltme 4 ' ün büyük örnek birimlerine yakın bir yerde dağıtmak için [Azure yakınlık yerleşimi gruplarından](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)yararlanabilirsiniz. Aynı Azure veri merkezinde barındırılan HANA büyük örnek birimlerinin SAP uygulama katmanını bulmak için yakınlık yerleşimi gruplarının nasıl kullanılabileceği, [SAP uygulamalarıyla en iyi ağ gecikmesi Için Azure yakınlık yerleştirme gruplarında açıklanacaktır ](sap-proximity-placement-scenarios.md).

VM 'Ler ve HANA büyük örnek arasında kararlı ağ gecikmesi sağlamak için, ExpressRoute ağ geçidi SKU 'SU seçeneği gereklidir. Şirket içi ve VM 'Ler arasındaki trafik desenlerinden farklı olarak, VM 'Ler ve HANA büyük örnek arasındaki trafik deseni, iletilmek üzere küçük ancak yüksek miktarda istek ve veri birimi geliştirebilir. Bu tür artışlarıyla 'yi işlemek için UltraPerformance Gateway SKU 'sunun kullanımını önemle öneririz. HANA büyük örnek SKU 'Larının tür II sınıfı için, UltraPerformance ağ geçidi SKU 'sunun ExpressRotue Gateway olarak kullanılması zorunludur.

> [!IMPORTANT] 
> SAP uygulaması ve veritabanı katmanları arasındaki genel ağ trafiği verildiğinde, Azure 'daki SAP HANA (büyük örnekler) bağlanmak için yalnızca sanal ağlara yönelik HighPerformance veya UltraPerformance Gateway SKU 'Ları desteklenir. HANA büyük örnek türü II SKU 'Ları için, ExpressRoute ağ geçidi olarak yalnızca UltraPerformance ağ geçidi SKU 'SU desteklenir. ExpressRoute hızlı yolu (aşağıya bakın) kullanılırken özel durumlar geçerlidir

### <a name="expressroute-fast-path"></a>ExpressRoute hızlı yolu
Gecikme süresini azaltmak için, ExpressRoute hızlı yolu, 2019 Mayıs 'ta SAP uygulama VM 'lerini barındıran Azure sanal ağlarına belirli bir HANA büyük örnek bağlantısı için tanıtılmıştır ve yayımlanmıştır. Şu ana kadar çözüm olan en önemli fark, sanal makineler ve HANA büyük örnekler arasındaki veri akışı artık ExpressRoute Gateway üzerinden yönlendirilmemektedir. Bunun yerine, Azure sanal ağının alt ağında atanan VM 'Ler, adanmış kurumsal sınır yönlendiricisi ile doğrudan iletişim kuruyor. 

> [!IMPORTANT] 
> ExpressRoute hızlı yolu işlevselliği, SAP uygulama VM 'lerini çalıştıran alt ağların, HANA büyük örneklerine bağlı aynı Azure sanal ağında olmasını gerektirir. Azure sanal ağlarında bulunan ve doğrudan HANA büyük örnek birimlerine bağlı Azure sanal ağı 'nda bulunan VM 'Ler, ExpressRoute hızlı yolundan yararlanırken değildir. Sonuç olarak, ExpressRoute bağlantı hattı bir hub sanal ağı ve SAP uygulama katmanını (bağlı bileşen) içeren sanal ağları, ExpressRoute hızlı en iyi duruma getirmeye yönelik olan tipik bir merkez ve bağlı olan sanal ağ tasarımları olarak Yol çalışmayacak. Ek olarak, ExpressRoute hızlı yolu, günümüzde Kullanıcı tanımlı yönlendirme kurallarını (UDR) desteklemez. Daha fazla bilgi için bkz. [ExpressRoute sanal ağ geçidi ve FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


ExpressRoute hızlı yolunu yapılandırma hakkında daha fazla bilgi için, [bir sanal ağı Hana büyük örneklerine bağlama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)belgesini okuyun.    

> [!NOTE]
> ExpressRoute hızlı yolunun çalışmasını sağlamak için bir UltraPerformance ExpressRoute ağ geçidi gerekir


## <a name="single-sap-system"></a>Tek SAP sistemi

Daha önce gösterilen şirket içi altyapı, ExpressRoute aracılığıyla Azure 'a bağlanır. ExpressRoute bağlantı hattı bir Microsoft kurumsal sınır yönlendiricisine (MSEE) bağlanır. Daha fazla bilgi için bkz. [ExpressRoute teknik genel bakış](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Yol kurulduktan sonra Azure omurgasına bağlanır.

> [!NOTE] 
> Azure 'da SAP landscapes 'yi çalıştırmak için SAP yatay 'daki Azure bölgesine en yakın kurumsal kenar yönlendiricisine bağlanın. HANA büyük örnek damgaları, Azure IaaS ve HANA büyük örnek damgalarındaki VM 'Ler arasındaki ağ gecikmesini en aza indirmek için adanmış kurumsal uç yönlendirici cihazları aracılığıyla bağlanır.

SAP uygulama örneklerinin barındırıldığı VM 'Ler için ExpressRoute ağ geçidi, şirket içine bağlanan bir ExpressRoute devresine bağlanır. Aynı sanal ağ, büyük örnek damgalarına bağlanmak için ayrılmış ayrı bir kurumsal kenar yönlendiricisine bağlanır. ExpressRoute hızlı yolunu kullanarak, HANA büyük örneklerden SAP uygulama katmanı VM 'lerine veri akışı, artık ExpressRoute Gateway üzerinden ve ile ağ gidiş dönüş gecikmesini azaltır.

Bu sistem, tek bir SAP sisteminin basit bir örneğidir. SAP uygulama katmanı Azure 'da barındırılır. SAP HANA veritabanı, Azure 'da SAP HANA üzerinde çalışır (büyük örnekler). Varsayım, ExpressRoute ağ geçidi bant genişliğinin 2 Gbps veya 10 Gbps 'lik verimlilik bir darboğazın temsil etmediği varsayılır.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Birden çok SAP sistemi veya büyük SAP sistemleri

Azure 'da (büyük örnekler) SAP HANA bağlanmak için birden fazla SAP sistemi veya büyük SAP sistemi dağıtılırsa, ExpressRoute ağ geçidinin üretilen iş hızı bir performans sorunu haline gelebilir. Ya da üretim ve üretim dışı sistemleri farklı Azure sanal ağlarında yalıtmak istiyorsanız. Böyle bir durumda, uygulama katmanlarını birden çok sanal ağa bölebilirsiniz. Ayrıca, şu gibi durumlarda HANA büyük örneğine bağlanan özel bir sanal ağ oluşturabilirsiniz:

- Yalnızca HANA büyük örneğindeki HANA örneklerinden yedeklemeler, NFS paylaşımlarını barındıran Azure sanal makinesine doğrudan gerçekleştiriliyor.
- Büyük yedeklemeleri veya diğer dosyaları HANA büyük örnek birimlerinden Azure 'da yönetilen disk alanına kopyalama.

HANA büyük örnekler ve Azure arasında verilerin toplu aktarımı için depolamayı yöneten VM 'Leri barındırmak üzere ayrı bir sanal ağ kullanın. Bu düzenleme, SAP uygulama katmanını çalıştıran VM 'Lere hizmet eden ExpressRoute ağ geçidinde büyük dosya veya veri aktarımının etkilerini önler. 

Daha ölçeklenebilir bir ağ mimarisi için:

- Tek ve daha büyük bir SAP uygulama katmanı için birden çok sanal ağdan yararlanın.
- Bu SAP sistemlerini aynı sanal ağ altındaki ayrı alt ağlarda birleştirmeye kıyasla, dağıtılan her SAP sistemi için ayrı bir sanal ağ dağıtın.

  Azure 'da SAP HANA için daha ölçeklenebilir bir ağ mimarisi (büyük örnekler):

![SAP uygulama katmanını birden çok sanal ağ üzerinden dağıtma](./media/hana-overview-architecture/image4-networking-architecture.png)

Kurallara ve kısıtlamalara bağlı olarak, farklı SAP sistemlerine ait VM 'Leri barındıran farklı sanal ağlar arasında uygulamak istiyorsanız, bu sanal ağları eşlemelidir. Sanal ağ eşlemesi hakkında daha fazla bilgi için bkz. [sanal ağ eşlemesi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Azure 'da yönlendirme

Varsayılan dağıtım, Azure 'daki SAP HANA için üç ağ yönlendirme hakkında önemli noktalar (büyük örnekler) için önemlidir:

* Azure 'daki SAP HANA (büyük örneklere) yalnızca Azure VM 'Leri ve şirket içi olarak değil, özel ExpressRoute bağlantısı aracılığıyla erişilebilir. Microsoft 'un sizin tarafınızdan sunulan Şirket içinden HANA büyük örnek birimlerine doğrudan erişim, hemen mümkün değildir. Geçişli yönlendirme kısıtlamaları, SAP HANA büyük örnek için kullanılan geçerli Azure ağ mimarisinden kaynaklanır. Şirket içinde çalışan SAP çözüm Yöneticisi gibi bazı yönetim istemcileri ve doğrudan erişim gerektiren uygulamalar SAP HANA veritabanına bağlanamaz. Özel durumlar için ' HANA büyük örneklere doğrudan yönlendirme ' bölümüne bakın.

* Olağanüstü durum kurtarma için iki farklı Azure bölgesinde dağıtılan HANA büyük örnek birimleriniz varsa, geçmişte aynı geçici yönlendirme kısıtlamaları uygulanır. Diğer bir deyişle, bir bölgedeki bir HANA büyük örnek biriminin IP adresleri (örneğin, ABD Batı), başka bir bölgede dağıtılan bir HANA büyük örnek birimine yönlendirilmedi (örneğin, ABD Doğu). Bu kısıtlama, bölgeler genelinde Azure ağ eşleme kullanımını veya HANA büyük örnek birimlerini sanal ağlara bağlayan ExpressRoute devrelerinin çapraz bağlantı sağlamasından bağımsızdır. Grafik gösterimi için, "birden çok bölgede HANA büyük örnek birimlerini kullanma" bölümündeki şekle bakın. Dağıtılan mimariyle birlikte gelen bu kısıtlama, HANA sistem çoğaltmasının olağanüstü durum kurtarma işlevselliği olarak hemen kullanılmasını yasaklamıştır. Son değişiklikler için ' birden çok bölgede HANA büyük örnek birimlerini kullanma ' bölümüne bakın. 

* Azure (büyük örnekler) birimlerindeki SAP HANA, HANA büyük örnek dağıtımı istenirken gönderdiğiniz sunucu IP havuzu adres aralığından atanmış bir IP adresine sahiptir. Daha fazla bilgi için bkz. [Azure 'da SAP HANA (büyük örnekler) altyapısı ve bağlantısı](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Bu IP adresine Azure sanal ağlarını HANA büyük örneklerine bağlayan Azure abonelikleri ve devresi aracılığıyla erişilebilir. Bu sunucu IP havuzu adres aralığından atanan IP adresi doğrudan donanım birimine atanır. Bu çözümün ilk dağıtımında olduğu gibi, artık NAT *aracılığıyla atanmaz.* 

### <a name="direct-routing-to-hana-large-instances"></a>HANA büyük örneklerine doğrudan yönlendirme

Varsayılan olarak, geçişli yönlendirme bu senaryolarda çalışmaz:

* HANA büyük örnek birimleri ve şirket içi dağıtım arasında.

* İki farklı bölgede dağıtılan HANA büyük örnek yönlendirmesi arasında.

Bu senaryolarda geçişli yönlendirmeyi etkinleştirmenin üç yolu vardır:

- Veri yönlendirmek için bir ters proxy Örneğin, F5 BIG-IP, NGıNX, HANA büyük örneklerine ve şirket içi bir sanal güvenlik duvarı/trafik yönlendirme çözümü olarak bağlanan Azure sanal ağı 'nda dağıtılan Traffic Manager.
- Şirket içi konumlar ile HANA büyük örnek birimleri arasında veya farklı bölgelerdeki HANA büyük örnek birimleri arasında yönlendirmeyi etkinleştirmek için bir Linux sanal makinesinde [Iptables kurallarını](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) kullanma. Iptables çalıştıran VM 'nin HANA büyük örneklerine ve şirket içine bağlanan Azure sanal ağı 'nda dağıtılması gerekir. VM 'nin ağ aktarım hızı beklenen ağ trafiği için yeterli olduğundan, sanal makinenin buna uygun olarak boyutlandırılması gerekir. VM ağı bant genişliği hakkında daha fazla bilgi için, [Azure 'Da Linux sanal makinelerinin makale boyutlarını](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)kontrol edin.
- [Azure Güvenlik Duvarı](https://azure.microsoft.com/services/azure-firewall/) , şirket ıçı ve Hana büyük örnek birimleri arasında doğrudan trafiği etkinleştirmek için başka bir çözümdür. 

Bu çözümlerin tüm trafiği bir Azure sanal ağı aracılığıyla yönlendirilir ve bu nedenle trafik, Azure ağ güvenlik grupları tarafından kullanılan veya Azure ağ güvenlik grupları tarafından, belirli IP adreslerinin veya IP adresi aralıklarının kullanıldığı yazılım tarafından da kısıtlanabilir. Şirket içi Engellenen veya HANA büyük örneklere erişime açıkça izin veriliyor. 

> [!NOTE]  
> Üçüncü taraf ağ gereçlerini veya Iptables 'ı içeren özel çözümlerin uygulanması ve desteğinin Microsoft tarafından sağlandığının farkında olun. Destek, kullanılan bileşenin satıcısı veya tümleştirici tarafından sağlanmalıdır. 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft, [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)adlı yeni bir işlevsellik sunmuştur. Global Reach, iki senaryoda HANA büyük örnekleri için kullanılabilir:

- Şirket içinden, farklı bölgelerde dağıtılan HANA büyük örnek birimlerinizde doğrudan erişimi etkinleştirin
- Farklı bölgelerde dağıtılan HANA büyük örnek birimleriniz arasında doğrudan iletişimi etkinleştir


##### <a name="direct-access-from-on-premise"></a>Şirket içinden doğrudan erişim
Global Reach sunulan Azure bölgelerinde, şirket içi ağınızı, HANA büyük örnek birimlerinizde bulunan Azure sanal ağına bağlayan ExpressRoute bağlantı hattı için Global Reach işlevselliğinin etkinleştirilmesini isteyebilirsiniz. ExpressRoute bağlantı hattının şirket içi tarafı için bazı maliyet etkileri vardır. Fiyatlar için [Global Reach eklentisinin](https://azure.microsoft.com/pricing/details/expressroute/)fiyatlarını kontrol edin. HANA büyük örnek birimlerini Azure 'a bağlayan devre ile ilgili ek maliyet yoktur. 

> [!IMPORTANT]  
> HANA büyük örnek birimleriniz ve şirket içi varlıklarınız arasında doğrudan erişimi etkinleştirmek için Global Reach kullanılması durumunda, ağ verileri ve denetim akışı **Azure sanal ağları aracılığıyla yönlendirilemez**, ancak doğrudan Microsoft kurumsal Exchange arasında yönlendiriciden. Sonuç olarak, herhangi bir NSG veya ASG kuralı ya da bir Azure sanal ağında dağıttığınız herhangi bir güvenlik duvarı, NVA veya proxy türü dokunulmaz. **Şirket içinden HANA büyük örnek birimleri kısıtlamalarına doğrudan erişimi etkinleştirmek için ExpressRoute Global Reach kullanırsanız ve HANA büyük örnek birimlerine erişim izinleri şirket içi tarafta güvenlik duvarları içinde tanımlanmalıdır** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Farklı Azure bölgelerinde HANA büyük örnekleri bağlama
Aynı şekilde, ExpressRoute Global Reach, şirket içinde HANA büyük örnek birimlerine bağlantı kurmak için kullanılabilir. Bu, sizin için iki farklı bölgede dağıtılan Tow HANA büyük örnek kiracıları bağlamak için kullanılabilir. Yalıtım, HANA büyük örnek Kiracılarınızın her iki bölgede de Azure 'a bağlanmak için kullandığı ExpressRoute devreleri. İki farklı bölgede dağıtılan iki HANA büyük örnek kiracıyı bağlamak için ek ücret alınmaz. 

> [!IMPORTANT]  
> Farklı HANA büyük örnek kiracılar arasındaki ağ trafiğinin veri akışı ve denetim akışı, Azure ağları üzerinden yönlendirilmeyecektir. Sonuç olarak, iki HANA büyük örnek kiracılar arasında iletişim kısıtlamalarını zorlamak için Azure işlevselliği veya NVA 'lar kullanamazsınız. 

ExpressRoute Global Reach etkinleştirme hakkında daha fazla bilgi için, [sanal ağı Hana büyük örneklerine bağlama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)belgesini okuyun.


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA büyük örneğinin Internet bağlantısı
HANA büyük örneğinin doğrudan internet *bağlantısı yok.* Örnek olarak, bu kısıtlama işletim sistemi görüntüsünü doğrudan işletim sistemi satıcısına kaydetme yeteneğinizi kısıtlayabilir. Yerel SUSE Linux Enterprise Server abonelik yönetim aracı sunucunuz veya Red Hat Enterprise Linux abonelik Yöneticisi ile çalışmanız gerekebilir.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 'Ler ve HANA büyük örnek arasında veri şifreleme
HANA büyük örneği ve VM 'Ler arasında aktarılan veriler şifrelenmez. Ancak, yalnızca HANA DBMS yan ve JDBC/ODBC tabanlı uygulamalar arasındaki değişim için trafik şifrelemeyi etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [SAP tarafından bu belgelere](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)bakın.

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Birden çok bölgede HANA büyük örnek birimlerini kullanma

Olağanüstü durum kurtarma kümesi UPS 'i gerçekleştirmek için, birden çok Azure bölgesinde KıANA büyük örnek birimlere sahip olmanız gerekir. Azure [Global VNET eşlemesi] kullanılmasına karşın, iki farklı bölgede HANA büyük örnek kiracılar arasında varsayılan olarak geçişli yönlendirme çalışmaz. Ancak, Global Reach iki farklı bölgede sağladığınız HANA büyük örnek birimleri arasındaki iletişim yolunu açar. ExpressRoute Global Reach bu kullanım senaryosu şunları sunar:

 - Ek proxy veya güvenlik duvarları olmadan HANA sistem çoğaltması
 - Sistem kopyaları veya sistem yenilemeleri gerçekleştirmek üzere iki farklı bölgedeki HANA büyük örnek birimleri arasında yedeklemeleri kopyalama


![Farklı Azure bölgelerindeki Azure büyük örnek damgalarına bağlı sanal ağ](./media/hana-overview-architecture/image8-multiple-regions.png)

Şekilde her iki bölgedeki farklı sanal ağların her iki Azure bölgesindeki (gri çizgiler) Azure 'da SAP HANA bağlanmak için kullanılan iki farklı ExpressRoute devresine nasıl bağlandığı gösterilmektedir. Bu iki çapraz bağlantının nedeni, her iki tarafta de MIMIN kesintiinden korunabilmektedir. İki Azure bölgesindeki iki sanal ağ arasındaki iletişim akışı iki farklı bölgedeki iki sanal ağın [genel eşlemesi](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) üzerinden işlenmelidir (Mavi noktalı çizgi). Kalın kırmızı çizgi, iki farklı bölgedeki Kiracılarınızın HANA büyük örnek birimlerinin birbirleriyle iletişim kurmasına izin veren ExpressRoute Global Reach bağlantısını açıklar. 

> [!IMPORTANT] 
> Birden çok ExpressRoute devresine birini kullandıysanız, trafiğin doğru şekılde yönlendirilmesini sağlamak için yol önbekleyen ve yerel tercih BGP ayarlarının kullanılması gerekir.

**Sonraki adımlar**
- [SAP HANA (büyük örnekler) depolama mimarisini](hana-storage-architecture.md) inceleyin
