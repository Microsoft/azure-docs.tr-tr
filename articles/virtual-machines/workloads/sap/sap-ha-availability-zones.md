---
title: Azure Kullanılabilirlik Alanları ile SAP iş yükü yapılandırması | Microsoft Docs
description: Azure Kullanılabilirlik Alanları kullanarak SAP NetWeaver için yüksek kullanılabilirliğe sahip mimari ve senaryolar
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f5186f456003c341af41fc6067f3b5c08acb2b4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078879"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Azure Kullanılabilirlik Alanlarıyla SAP iş yükü yapılandırmaları
[Azure kullanılabilirlik alanları](https://docs.microsoft.com/azure/availability-zones/az-overview) , Azure 'un sağladığı yüksek kullanılabilirliğe sahip özelliklerden biridir. Kullanılabilirlik Alanları kullanmak, Azure üzerinde SAP iş yüklerinin genel kullanılabilirliğini geliştirir. Bu özellik bazı [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/regions/)zaten kullanılabilir. Gelecekte, daha fazla bölgede kullanıma sunulacaktır.

Bu grafik, temel SAP yüksek kullanılabilirliği mimarisini göstermektedir:

![Standart yüksek kullanılabilirlik yapılandırması](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP uygulama katmanı, tek bir Azure [kullanılabilirlik kümesi](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)üzerinden dağıtılır. SAP merkezi hizmetlerinin yüksek kullanılabilirliğine sahip olmak için, ayrı bir kullanılabilirlik kümesinde iki VM dağıtımı yapabilirsiniz. Altyapı veya yazılım sorunu durumunda otomatik yük devretme ile yüksek kullanılabilirliğe sahip bir çerçeve olarak Windows Server Yük Devretme Kümelemesi veya Paceyapıcısı (Linux) kullanın. Bu dağıtımlar hakkında daha fazla bilgi edinmek için bkz.:

- [Küme Paylaşılan diski kullanarak bir Windows Yük devretme kümesinde SAP ASCS/SCS örneği oluşturma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Dosya paylaşma kullanarak bir Windows Yük devretme kümesinde SAP ASCS/SCS örneği oluşturma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [SAP uygulamaları için SUSE Linux Enterprise Server Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Red Hat Enterprise Linux SAP NetWeaver için Azure sanal makineleri yüksek kullanılabilirliği](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Benzer bir mimari, SAP NetWeaver, S/4HANA veya Hybris sistemlerinin DBMS katmanı için geçerlidir. Altyapı veya yazılım arızasına karşı koruma sağlamak için bir yük devretme kümesi çözümü ile, DBMS katmanını etkin/pasif modda dağıtırsınız. Yük devretme kümesi çözümü, DBMS 'ye özgü yük devretme çerçevesi, Windows Server Yük Devretme Kümelemesi veya Paceyapıcısı olabilir.

Azure Kullanılabilirlik Alanları kullanarak aynı mimariyi dağıtmak için, daha önce özetlenen mimaride bazı değişiklikler yapmanız gerekir. Bu makalede bu değişiklikler açıklanmaktadır.

## <a name="considerations-for-deploying-across-availability-zones"></a>Kullanılabilirlik Alanları genelinde dağıtım konuları


Kullanılabilirlik Alanları kullandığınızda aşağıdakileri göz önünde bulundurun:

- Bir Azure bölgesi içindeki çeşitli Kullanılabilirlik Alanları arasındaki uzaklıklarla ilgili garanti yoktur.
- Kullanılabilirlik Alanları ideal bir DR çözümü değildir. Doğal felaketler, uluslararası altyapılara ağır hasar da dahil olmak üzere dünya bölgelerinde yaygın olarak hasar verebilir. Çeşitli bölgeler arasındaki uzaklıklar doğru bir DR çözümü sağlayacak kadar büyük olmayabilir.
- Kullanılabilirlik Alanları arasındaki ağ gecikmesi tüm Azure bölgelerinde aynı değildir. Bazı durumlarda, bir bölgeden etkin DBMS VM 'sine yönelik ağ gecikmesi kabul edilebilir olduğundan SAP uygulama katmanını farklı bölgelerde dağıtabilir ve çalıştırabilirsiniz. Ancak bazı Azure bölgelerinde, farklı bölgelerde dağıtıldığında etkin DBMS sanal makinesi ve SAP uygulama örneği arasındaki gecikme süresi, SAP iş işlemlerinde kabul edilebilir olmayabilir. Bu durumlarda, dağıtım mimarisinin, uygulama için etkin/etkin mimari veya çapraz bölge ağ gecikmesi çok yüksek olduğu bir etkin/Pasif mimari ile farklı olması gerekir.
- Kullanılabilirlik Alanları nerede kullanacağınızı saptarken, bölgeler arasındaki ağ gecikmesi üzerinde kararınızı temel alır. Ağ gecikmesi iki alanda önemli bir rol oynar:
    - Zaman uyumlu çoğaltmaya sahip olması gereken iki DBMS örneği arasındaki gecikme süresi. Ağ gecikmesi arttıkça, iş yükünüzün ölçeklenebilirliğini de etkiler.
    - Etkin DBMS örneğiyle bir SAP iletişim kutusu örneği çalıştıran bir VM ve başka bir bölgedeki benzer bir VM arasındaki ağ gecikme süresi farkı. Bu fark arttıkça, iş süreçlerinin ve toplu işlerin çalışma süresi üzerindeki etki, DBMS ile veya farklı bir bölgede bölge içinde çalıştırıldıklarından bağımsız olarak da artar.

Azure VM 'lerini Kullanılabilirlik Alanları genelinde dağıtırken ve aynı Azure bölgesinde yük devretme çözümleri oluşturduğunuzda, bazı kısıtlamalar uygulanır:

- Azure Kullanılabilirlik Alanları ' a dağıtırken [Azure yönetilen diskleri](https://azure.microsoft.com/services/managed-disks/) kullanmanız gerekir. 
- Bölge Numaralandırmaların fiziksel bölgelere eşlenmesi, bir Azure aboneliği temelinde düzeltilmelidir. SAP sistemlerinizi dağıtmak için farklı abonelikler kullanıyorsanız, her abonelik için ideal bölgeleri tanımlamanız gerekir.
- Azure [yakınlık yerleşimi grubunu](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)kullanmadığınız takdirde Azure kullanılabilirlik gruplarını bir Azure kullanılabilirlik bölgesi içinde dağıtamazsınız. SAP DBMS katmanını ve merkezi hizmetleri bölgelere dağıtmak ve aynı zamanda SAP uygulama katmanını kullanılabilirlik kümeleri kullanarak dağıtma ve yine de VM 'lerin yakınlığını elde etme yönteminiz [Azure yakınlık yerleşimi makalesinde belgelenmiştir SAP uygulamalarıyla en iyi ağ gecikmesi için gruplar](sap-proximity-placement-scenarios.md). Azure yakınlığı yerleştirme gruplarıyla yararlandıysanız, sanal makineler için bir dağıtım çerçevesi olarak bir veya diğerini seçmeniz gerekir.
- Windows Server Yük Devretme Kümelemesi veya Linux pacemaker tabanlı yük devretme kümesi çözümleri oluşturmak için [Azure temel Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) kullanamazsınız. Bunun yerine, [Azure Standart Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)'sunu kullanmanız gerekir.



## <a name="the-ideal-availability-zones-combination"></a>İdeal Kullanılabilirlik Alanları birleşimi
Kullanılabilirlik Alanları kullanmaya karar vermeden önce şunları belirlemeniz gerekir:

- Bir Azure bölgesinin üç bölgesi arasındaki ağ gecikmesi. Bu, bölgeler arası ağ trafiğinden en az ağ gecikme süresine sahip bölgeleri seçmenize olanak sağlar.
- Bir bölgede bulunan VM-VM gecikme süresi ve seçtiğiniz iki bölge arasındaki ağ gecikmesi arasındaki fark.
- Dağıtmanız gereken sanal makine türlerinin seçtiğiniz iki bölgede kullanılabilir olup olmadığını belirleme. Bazı VM 'Lerde, özellikle de a serisi VM 'lerle, bazı SKU 'Ların üç bölgenin yalnızca iki bölgede kullanılabildiği durumlarla karşılaşabilirsiniz.

## <a name="network-latency-between-and-within-zones"></a>Bölgeler arasında ve içinde ağ gecikmesi
Farklı bölgeler arasındaki gecikmeyi öğrenmek için şunları yapmanız gerekir:

- Her üç bölgede DBMS örneğiniz için kullanmak istediğiniz VM SKU 'sunu dağıtın. Bu ölçümü gerçekleştirdiğinizde [Azure hızlandırılmış ağ](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) 'ın etkinleştirildiğinden emin olun.
- En az ağ gecikmesi olan iki bölgeyi bulduğunuzda, üç Kullanılabilirlik Alanları üzerinde uygulama katmanı VM 'si olarak kullanmak istediğiniz VM SKU 'sunun başka üç VM 'sini dağıtın. Seçtiğiniz iki DBMS bölgesindeki iki DBMS sanal makinesi için ağ gecikmesini ölçün. 
- Ölçüm aracı olarak **niping** kullanın. SAP 'nin bu aracı, SAP destek notları [#500235](https://launchpad.support.sap.com/#/notes/500235) ve [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)açıklanmaktadır. Gecikme süresi ölçümleri için belgelenen komutlara odaklanın. **Ping** , Azure hızlandırılmış ağ kodu yolları üzerinden çalışmadığından, bunu kullanmanızı önermiyoruz.

Ölçümlerinizi ve Kullanılabilirlik Alanları sanal makine SKU 'larınızın kullanılabilirliğine göre, bazı kararlar vermeniz gerekir:

- DBMS katmanının ideal bölgelerini tanımlayın.
- Etkin SAP uygulama katmanınızı bir, iki veya üç bölgenin tamamında, bölgedeki ağ gecikme süresinin bölge genelinde farklılıklar temelinde dağıtmak isteyip istemediğinizi belirleme.
- Bir uygulama görünümünden etkin/Pasif bir yapılandırma ya da etkin/etkin bir yapılandırma dağıtmak istediğinizi belirleme. (Bu konfigürasyonlar Bu makalenin ilerleyen kısımlarında açıklanmaktadır.)

Bu kararları verirken SAP Note [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)bölümünde belgelendiği gibi, hesap SAP 'nin ağ gecikmesi önerilerini de göz önünde bulabilirsiniz.

> [!IMPORTANT]
> Yaptığınız ölçümler ve kararlar, ölçümleri aldığınızda kullandığınız Azure aboneliği için geçerlidir. Başka bir Azure aboneliği kullanıyorsanız, ölçümleri tekrarlamanız gerekir. Numaralandırılmış bölgelerin eşlemesi, başka bir Azure aboneliği için farklı olabilir.


> [!IMPORTANT]
> Daha önce açıklanan ölçümlerin [kullanılabilirlik alanları](https://docs.microsoft.com/azure/availability-zones/az-overview)destekleyen her Azure bölgesinde farklı sonuçlar sağlayacağı beklenmektedir. Ağ gecikmesi gereksinimleriniz aynı olsa da, bölgeler arasındaki ağ gecikmesi farklı olabileceğinden farklı Azure bölgelerinde farklı dağıtım stratejilerini benimsemeniz gerekebilir. Bazı Azure bölgelerinde, üç farklı bölge arasındaki ağ gecikmesi büyük ölçüde farklı olabilir. Diğer bölgelerde, üç farklı bölge arasındaki ağ gecikmesi daha Tekdüzen olabilir. 1 ile 2 milisaniyelik arasında her zaman bir ağ gecikmesi olduğu talep doğru değildir. Azure bölgelerindeki Kullanılabilirlik Alanları arasındaki ağ gecikmesi genelleştirilemez.

## <a name="activeactive-deployment"></a>Etkin/etkin dağıtım
Bu dağıtım mimarisi, etkin/etkin olarak adlandırılır çünkü etkin SAP uygulama sunucularınızı iki veya üç bölgede dağıtmış olursunuz. Sıraya alma çoğaltması kullanan SAP Merkezi Hizmetler örneği iki bölge arasında dağıtılır. Aynı işlem, SAP Merkezi hizmeti ile aynı bölgelerde dağıtılacak olan DBMS katmanı için de geçerlidir.

Bu yapılandırmayı düşünürken, bölgenizde, iş yükünüz ve zaman uyumlu DBMS çoğaltmayla kabul edilebilir bölgeler arası ağ gecikmesi sunan iki Kullanılabilirlik Alanları bulmanız gerekir. Ayrıca, seçtiğiniz bölgelerin içindeki ağ gecikmesi ve çapraz bölge ağ gecikmesi çok büyük olmadığından emin olmak istersiniz. Bunun nedeni, bir işin DBMS sunucusu ya da bölgeler arasında, iş süreçlerinizin veya toplu işlerin çalışma saatlerinde şirket içinde çalışıp çalışmadığına bağlı olarak büyük Çeşitlemeler istemezsiniz. Bazı Çeşitlemeler kabul edilebilir, ancak farklılık faktörleri değildir.

İki bölgede etkin/etkin bir dağıtımın Basitleştirilmiş şeması şuna benzeyebilir:

![Etkin/etkin bölge dağıtımı](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Bu yapılandırma için aşağıdaki noktalar geçerlidir:

- [Azure yakınlık yerleşimi grubunu](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)kullanmıyorsanız, kullanılabilirlik kümeleri Azure kullanılabilirlik alanları dağıtılamadığından, tüm VM 'ler için Azure kullanılabilirlik alanları hata ve güncelleştirme etki alanlarını kabul edersiniz.
- DBMS katmanı ve merkezi hizmetler için bölgesel dağıtımlarını birleştirmek istiyorsanız, ancak uygulama katmanı için Azure kullanılabilirlik kümelerini kullanmak istiyorsanız, Azure yakınlık [yerleşimi grupları hakkında en iyi durumda açıklandığı şekilde Azure yakınlık grupları 'nı kullanmanız gerekir SAP uygulamalarıyla ağ gecikmesi](sap-proximity-placement-scenarios.md).
- SAP Merkezi Hizmetleri ve DBMS katmanının yük devretme kümelerinin yük dengeleyiciler için [Standart SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)kullanmanız gerekir. Temel Load Balancer bölgeler arasında çalışmaz.
- SAP sistemini barındırmak için dağıttığınız Azure sanal ağı, alt ağları ile birlikte bölgeler arasında uzatılır. Her bölge için ayrı sanal ağlara gerek yoktur.
- Dağıttığınız tüm sanal makineler için [Azure yönetilen diskleri](https://azure.microsoft.com/services/managed-disks/)kullanmanız gerekir. Yönetilmeyen diskler, bölgesel dağıtımları için desteklenmez.
- Azure Premium Depolama ve [Ultra SSD depolama](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) alanları arasında herhangi bir tür depolama çoğaltmasını desteklemez. Uygulama (DBMS veya SAP Merkezi Hizmetler) önemli verileri çoğaltmalıdır.
- Aynı, paylaşılan bir disk (Windows), CIFS paylaşım (Windows) veya NFS paylaşım (Linux) olan paylaşılan sapmnt dizini için de geçerlidir. Bu Paylaşılan diskleri veya paylaşımları bölgeler arasında çoğaltan bir teknoloji kullanmanız gerekir. Bu teknolojiler desteklenir:
  - Windows için, [Azure 'da bir küme paylaşılan diski kullanarak bir Windows Yük devretme KÜMESINDEKI SAP ASCS/SCS örneği](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)bölümünde belgelendiği gıbı, SIOS Dataman kullanan bir küme çözümüdür.
  - SUSE Linux için, [SUSE Linux Enterprise Server üzerinde Azure VM 'LERINDE NFS Için yüksek kullanılabilirlik](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)makalesinde belgelenen şekilde oluşturulmuş bir NFS paylaşımıdır.
    
    Şu anda Microsoft Genişleme Dosya Sunucusu kullanan çözüm, SAP [Ass/SCS örnekleri için bir Windows Yük devretme kümesi ve dosya paylaşımının KULLANıLDıĞı SAP yüksek kullanılabilirlik Için Azure altyapısını hazırlama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)bölümünde belgelendiği gibi, bölgeler arasında desteklenmez.
- Üçüncü bölge, [SUSE Linux Paceoluşturucu kümesi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) veya ek uygulama örnekleri oluşturmak için SBD cihazını barındırmak üzere kullanılır.
- Kritik iş süreçlerine yönelik çalışma süresi tutarlılığı elde etmek için, SAP Batch sunucu grupları, SAP oturum açma grupları veya RFC grupları ' nı kullanarak, belirli toplu işleri ve kullanıcıları, etkin DBMS örneğiyle bölge içinde olan uygulama örneklerine yönlendirmeyi deneyebilirsiniz. Ancak, bir yük devretme durumunda, bu grupları etkin DB VM 'si ile bölgedeki VM 'lerde çalışan örneklere el ile taşımanız gerekir.  
- Her bölgede etkin olmayan iletişim kutusu örnekleri dağıtmak isteyebilirsiniz. Bu, uygulama örneklerinizin parçası tarafından kullanılan bir bölgenin hizmet dışı olması durumunda eski kaynak kapasitesine anında geri dönüş sağlamak için kullanılır.


## <a name="activepassive-deployment"></a>Etkin/Pasif dağıtım
Bir bölgedeki ağ gecikme süresi ve çapraz bölge ağ trafiği gecikmesi arasında kabul edilebilir bir Delta bulamıyorsanız, görünümün SAP uygulama katmanı noktasından etkin/Pasif bir karakter içeren bir mimari dağıtabilirsiniz. Tüm uygulama katmanını dağıttığınız ve hem etkin DBMS hem de SAP Merkezi Hizmetleri örneğini çalıştırmayı denebileceğiniz bölge olan *etkin* bir bölge tanımlarsınız. Böyle bir yapılandırma sayesinde, bir işin etkin DBMS örneğiyle bölge içinde çalışıp çalışmadığını, iş işlemlerinde ve Batch işlerinde değil, çok fazla çalışma zamanı varyasyonlarından emin olmanız gerekir.

Mimarinin temel düzeni şöyle görünür:

![Etkin/Pasif bölge dağıtımı](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Bu yapılandırma için aşağıdaki noktalar geçerlidir:

- Kullanılabilirlik kümeleri Azure Kullanılabilirlik Alanları ' de dağıtılamaz. Bunu dengelemek için, [SAP uygulamalarıyla en iyi ağ gecikme süresi Için Azure yakınlık yerleşimi grupları](sap-proximity-placement-scenarios.md)makalesinde belgelenen Azure yakınlık yerleşimi gruplarını kullanabilirsiniz.
- Bu mimariyi kullandığınızda, durumu yakından izlemeniz ve etkin DBMS ve SAP Merkezi Hizmetleri örneklerini dağıtılan uygulama katmanınız ile aynı bölgede tutmaya çalışmalısınız. SAP Merkezi hizmetinin veya DBMS örneğinin yük devretmesi durumunda, mümkün olduğunca hızlı bir şekilde dağıtılmış SAP uygulama katmanı ile bölgeye el ile yeniden oturum açmak istiyorsanız emin olmanız gerekir.
- SAP Merkezi Hizmetleri ve DBMS katmanının yük devretme kümelerinin yük dengeleyiciler için [Standart SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)kullanmanız gerekir. Temel Load Balancer bölgeler arasında çalışmaz.
- SAP sistemini barındırmak için dağıttığınız Azure sanal ağı, alt ağları ile birlikte bölgeler arasında uzatılır. Her bölge için ayrı sanal ağlara gerek yoktur.
- Dağıttığınız tüm sanal makineler için [Azure yönetilen diskleri](https://azure.microsoft.com/services/managed-disks/)kullanmanız gerekir. Yönetilmeyen diskler, bölgesel dağıtımları için desteklenmez.
- Azure Premium Depolama ve [Ultra SSD depolama](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) alanları arasında herhangi bir tür depolama çoğaltmasını desteklemez. Uygulama (DBMS veya SAP Merkezi Hizmetler) önemli verileri çoğaltmalıdır.
- Aynı, paylaşılan bir disk (Windows), CIFS paylaşım (Windows) veya NFS paylaşım (Linux) olan paylaşılan sapmnt dizini için de geçerlidir. Bu Paylaşılan diskleri veya paylaşımları bölgeler arasında çoğaltan bir teknoloji kullanmanız gerekir. Bu teknolojiler desteklenir:
    - Windows için, [Azure 'da bir küme paylaşılan diski kullanarak bir Windows Yük devretme KÜMESINDEKI SAP ASCS/SCS örneği](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)bölümünde belgelendiği gıbı, SIOS Dataman kullanan bir küme çözümüdür.
    - SUSE Linux için, [SUSE Linux Enterprise Server üzerinde Azure VM 'LERINDE NFS Için yüksek kullanılabilirlik](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)makalesinde belgelenen şekilde oluşturulmuş bir NFS paylaşımıdır.
    
  Şu anda Microsoft Genişleme Dosya Sunucusu kullanan çözüm, SAP [Ass/SCS örnekleri için bir Windows Yük devretme kümesi ve dosya paylaşımının KULLANıLDıĞı SAP yüksek kullanılabilirlik Için Azure altyapısını hazırlama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)bölümünde belgelendiği gibi, bölgeler arasında desteklenmez.
- Üçüncü bölge, [SUSE Linux Paceoluşturucu kümesi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) veya ek uygulama örnekleri oluşturmak için SBD cihazını barındırmak üzere kullanılır.
- Bir bölge hatası durumunda uygulama kaynaklarını başlatabilmeniz için pasif bölgeye (bir DBMS bakış noktasından) etkin olmayan VM 'Ler dağıtmanız gerekir.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) Şu anda, etkin VM 'leri bölgeler arasında etkin olmayan VM 'lere çoğaltamaz. 
- Bir bölge hatası durumunda, SAP uygulama katmanını ikinci bölgede otomatik olarak başlatmak için bir Otomasyon ile yatırım yapmanız gerekir.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Birleşik yüksek kullanılabilirlik ve olağanüstü durum kurtarma yapılandırması
Microsoft, bir Azure bölgesindeki farklı Azure Kullanılabilirlik Alanları barındıran tesislerde coğrafi uzaklıklar hakkında herhangi bir bilgi paylaşmaz. Yine de bazı müşteriler, bir kurtarma noktası hedefini (RPO) içeren bir birleştirilmiş HA ve DR yapılandırması için bölgeleri kullanıyor. Yani, olağanüstü durum kurtarma durumunda bile, tüm kaydedilmiş veritabanı işlemlerini kaybetmemeniz gerekir. 

> [!NOTE]
> Yalnızca belirli koşullarda bu şekilde bir yapılandırma kullanmanızı öneririz. Örneğin, veriler Azure bölgesinden güvenlik veya uyumluluk nedenleriyle bu şekilde çıkabileceği zaman kullanabilirsiniz. 

Bu tür bir yapılandırmanın nasıl görünebileceğini gösteren bir örnek aşağıda verilmiştir:

![Bölgelerde yüksek kullanılabilirliğe sahip DR](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Bu yapılandırma için aşağıdaki noktalar geçerlidir:

- Bir kullanılabilirlik bölgesini barındıran tesislerin arasında önemli bir mesafe olduğunu veya belirli bir Azure bölgesi içinde kalmaya zorladığınızı varsayalım. Kullanılabilirlik kümeleri Azure Kullanılabilirlik Alanları ' de dağıtılamaz. Bunu dengelemek için, [SAP uygulamalarıyla en iyi ağ gecikme süresi Için Azure yakınlık yerleşimi grupları](sap-proximity-placement-scenarios.md)makalesinde belgelenen Azure yakınlık yerleşimi gruplarını kullanabilirsiniz.
- Bu mimariyi kullandığınızda, durumu yakından izlemeniz ve etkin DBMS ve SAP Merkezi Hizmetleri örneklerini dağıtılan uygulama katmanınız ile aynı bölgede tutmaya çalışmalısınız. SAP Merkezi hizmetinin veya DBMS örneğinin yük devretmesi durumunda, mümkün olduğunca hızlı bir şekilde dağıtılmış SAP uygulama katmanı ile bölgeye el ile yeniden oturum açmak istiyorsanız emin olmanız gerekir.
- Etkin QA uygulama örneklerini çalıştıran VM 'lerde önceden yüklenmiş üretim uygulaması örneklerinin olması gerekir.
- Bir bölge hatası durumunda, QA uygulama örneklerini kapatın ve bunun yerine üretim örneklerini başlatın. Bu işi yapmak için uygulama örnekleri için sanal adlar kullanmanız gerektiğini unutmayın.
- SAP Merkezi Hizmetleri ve DBMS katmanının yük devretme kümelerinin yük dengeleyiciler için [Standart SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)kullanmanız gerekir. Temel Load Balancer bölgeler arasında çalışmaz.
- SAP sistemini barındırmak için dağıttığınız Azure sanal ağı, alt ağları ile birlikte bölgeler arasında uzatılır. Her bölge için ayrı sanal ağlara gerek yoktur.
- Dağıttığınız tüm sanal makineler için [Azure yönetilen diskleri](https://azure.microsoft.com/services/managed-disks/)kullanmanız gerekir. Yönetilmeyen diskler, bölgesel dağıtımları için desteklenmez.
- Azure Premium Depolama ve [Ultra SSD depolama](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) alanları arasında herhangi bir tür depolama çoğaltmasını desteklemez. Uygulama (DBMS veya SAP Merkezi Hizmetler) önemli verileri çoğaltmalıdır.
- Aynı, paylaşılan bir disk (Windows), CIFS paylaşım (Windows) veya NFS paylaşım (Linux) olan paylaşılan sapmnt dizini için de geçerlidir. Bu Paylaşılan diskleri veya paylaşımları bölgeler arasında çoğaltan bir teknoloji kullanmanız gerekir. Bu teknolojiler desteklenir:
    - Windows için, [Azure 'da bir küme paylaşılan diski kullanarak bir Windows Yük devretme KÜMESINDEKI SAP ASCS/SCS örneği](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)bölümünde belgelendiği gıbı, SIOS Dataman kullanan bir küme çözümüdür.
    - SUSE Linux için, [SUSE Linux Enterprise Server üzerinde Azure VM 'LERINDE NFS Için yüksek kullanılabilirlik](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)makalesinde belgelenen şekilde oluşturulmuş bir NFS paylaşımıdır.

  Şu anda Microsoft Genişleme Dosya Sunucusu kullanan çözüm, SAP [Ass/SCS örnekleri için bir Windows Yük devretme kümesi ve dosya paylaşımının KULLANıLDıĞı SAP yüksek kullanılabilirlik Için Azure altyapısını hazırlama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)bölümünde belgelendiği gibi, bölgeler arasında desteklenmez.
- Üçüncü bölge, [SUSE Linux Paceoluşturucu kümesi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) veya ek uygulama örnekleri oluşturmak için SBD cihazını barındırmak üzere kullanılır.





## <a name="next-steps"></a>Sonraki adımlar
Azure Kullanılabilirlik Alanları arasında dağıtım için bazı sonraki adımlar aşağıda verilmiştir:

- [Azure 'da küme paylaşılan diski kullanarak bir Windows Yük devretme kümesinde SAP ASCS/SCS örneği oluşturma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [SAP Ass/SCS örnekleri için bir Windows Yük devretme kümesi ve dosya paylaşma kullanarak SAP yüksek kullanılabilirlik için Azure altyapısını hazırlama](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






