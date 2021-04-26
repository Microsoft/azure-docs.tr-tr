---
title: Azure Kullanılabilirlik Alanları ile SAP iş yükü yapılandırması | Microsoft Docs
description: Azure Kullanılabilirlik Alanları kullanarak SAP NetWeaver için yüksek kullanılabilirliğe sahip mimari ve senaryolar
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 842c56ef1fb6f68c3d8b82e2633d9a604db9fde2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671640"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Azure Kullanılabilirlik Alanlarıyla SAP iş yükü yapılandırmaları
Ayrıca, Azure kullanılabilirlik kümelerinde farklı SAP mimari katmanlarının dağıtımına ek olarak, daha önce sunulan [Azure KULLANıLABILIRLIK ALANLARı](../../../availability-zones/az-overview.md) SAP iş yükü dağıtımları için de kullanılabilir. Bir Azure kullanılabilirlik bölgesi: "bir bölge içinde benzersiz fiziksel konumlar olarak tanımlanır. Her bölge, bağımsız güç, soğutma ve ağ ile donatılmış bir veya daha fazla veri merkezinden oluşur. Azure Kullanılabilirlik Alanları tüm bölgelerde kullanılamaz. Kullanılabilirlik Alanları sağlayan Azure bölgeleri için [Azure bölge haritasını](https://azure.microsoft.com/global-infrastructure/geographies/)denetleyin. Bu harita, Kullanılabilirlik Alanları sağlamak için hangi bölgelerin sunduğunu veya duyurduğunu gösterir. 

Tipik SAP NetWeaver veya S/4HANA mimarisinden itibaren üç farklı katmanı korumanız gerekir:

- SAP uygulama katmanı, bir kaç düzine VM 'ye kadar olabilir. VM 'lerin aynı konak sunucusuna dağıtılması olasılığını en aza indirmek istiyorsunuz. Ayrıca, kabul edilebilir bir pencerede ağ gecikmesini korumak için bu VM 'Leri DBMS katmanına kabul edilebilir bir biçimde istiyorsunuz
- SAP NetWeaver ve S/4HANA mimarisinde tek bir başarısızlık noktasını temsil eden SAP yoks/SCS katmanı. Genellikle bir yük devretme çerçevesiyle birlikte kapsamak istediğiniz iki VM 'ye bakmanız gerekir. Bu nedenle, bu VM 'Ler farklı altyapı hatası ve güncelleştirme etki alanlarında ayrılmalıdır
- SAP DBMS katmanı, tek bir başarısızlık noktasını temsil eder. Her zamanki durumlarda, bir yük devretme çerçevesi kapsamındaki iki VM 'den oluşur. Bu nedenle, bu VM 'Ler farklı altyapı hatası ve güncelleştirme etki alanlarında ayrılmalıdır. Özel durumlar, ikiden fazla VM 'nin kullanılabileceği genişleme dağıtımlarıdır SAP HANA

Kritik sanal makinelerinizi kullanılabilirlik kümeleri veya Kullanılabilirlik Alanları aracılığıyla dağıtma arasındaki önemli farklılıklar şunlardır:

- Bir kullanılabilirlik kümesiyle dağıtmak, küme içindeki VM 'Leri tek bir bölgede veya veri merkezinde (belirli bölge için geçerli olan) yukarı yerleştirirler. Sonuç olarak, kullanılabilirlik kümesi üzerinden dağıtım, bölgenin bir bütün olarak veri serilerini etkileyen güç, soğutma veya ağ sorunları ile korunmaz. Artı tarafında VM 'Ler, bu bölgedeki veya veri merkezindeki güncelleştirme ve hata etki alanları ile hizalanır. Kullanılabilirlik kümesi başına iki VM 'yi koruduğumuz SAP yoks veya DBMS katmanı için, hata ve güncelleştirme etki alanlarına yönelik hizalama her iki VM 'nin de aynı konak donanımında sonlandırmasını önler 
- VM 'Leri bir Azure Kullanılabilirlik Alanları ile dağıtma ve farklı bölgeler (şimdiye kadar en fazla mümkün olan en fazla) seçme, VM 'Leri farklı fiziksel konumlara dağıtmak ve bu sayede, bölgenin veri cılarını bir bütün olarak etkileyen güç, soğutma veya ağ sorunlarından ek koruma ekler. Ancak, aynı VM ailesinden birden fazla VM 'yi aynı Kullanılabilirlik alanına dağıtırken, bu VM 'lerden aynı konakta sonlanan bir koruma yoktur. Sonuç olarak, Kullanılabilirlik Alanları dağıtım, genellikle iki VM 'ye baktığımız SAP ASCS ve DBMS katmanı için idealdir. İki VM 'den büyük ölçüde daha fazla olabilecek SAP uygulama katmanı için, farklı bir dağıtım modeline geri dönebilmeniz gerekebilir (daha sonra bkz.)

Azure Kullanılabilirlik Alanları genelindeki bir dağıtıma ilişkin işlem, tek bir kritik VM 'nin hatasını kapsayan veya kritik bir süre içinde yazılım düzeltme eki uygulama için kapalı kalma süresini azaltma yeteneğinin bir veya birden çok Azure veri merkezinin kullanılabilirliğini etkileyebilecek daha büyük altyapı sorunlarından korunmak için yapmanız gerekir. 

## <a name="considerations-for-deploying-across-availability-zones"></a>Kullanılabilirlik Alanları genelinde dağıtım konuları


Kullanılabilirlik Alanları kullandığınızda aşağıdakileri göz önünde bulundurun:

- Bir Azure bölgesi içindeki çeşitli Kullanılabilirlik Alanları arasındaki uzaklıklarla ilgili garanti yoktur.
- Kullanılabilirlik Alanları ideal bir DR çözümü değildir. Doğal felaketler, uluslararası altyapılara ağır hasar da dahil olmak üzere dünya bölgelerinde yaygın olarak hasar verebilir. Çeşitli bölgeler arasındaki uzaklıklar doğru bir DR çözümü sağlayacak kadar büyük olmayabilir.
- Kullanılabilirlik Alanları arasındaki ağ gecikmesi tüm Azure bölgelerinde aynı değildir. Bazı durumlarda, bir bölgeden etkin DBMS VM 'sine yönelik ağ gecikmesi kabul edilebilir olduğundan SAP uygulama katmanını farklı bölgelerde dağıtabilir ve çalıştırabilirsiniz. Ancak bazı Azure bölgelerinde, farklı bölgelerde dağıtıldığında etkin DBMS sanal makinesi ve SAP uygulama örneği arasındaki gecikme süresi, SAP iş işlemlerinde kabul edilebilir olmayabilir. Bu durumlarda, dağıtım mimarisinin farklı olması gerekir, uygulama için etkin/etkin mimari veya çapraz bölge ağ gecikmesi çok yüksek olduğunda bir etkin/Pasif mimari.
- Kullanılabilirlik Alanları nerede kullanacağınızı saptarken, bölgeler arasındaki ağ gecikmesi üzerinde kararınızı temel alır. Ağ gecikmesi iki alanda önemli bir rol oynar:
    - Zaman uyumlu çoğaltmaya sahip olması gereken iki DBMS örneği arasındaki gecikme süresi. Ağ gecikmesi arttıkça, iş yükünüzün ölçeklenebilirliğini de etkiler.
    - Etkin DBMS örneğiyle bir SAP iletişim kutusu örneği çalıştıran bir VM ve başka bir bölgedeki benzer bir VM arasındaki ağ gecikme süresi farkı. Bu fark arttıkça, iş süreçlerinin ve toplu işlerin çalışma süresi üzerindeki etki, DBMS ile veya farklı bir bölgede bölge içinde çalıştırıldıklarından bağımsız olarak da artar.

Azure VM 'lerini Kullanılabilirlik Alanları genelinde dağıtırken ve aynı Azure bölgesinde yük devretme çözümleri oluşturduğunuzda, bazı kısıtlamalar uygulanır:

- Azure Kullanılabilirlik Alanları ' a dağıtırken [Azure yönetilen diskleri](https://azure.microsoft.com/services/managed-disks/) kullanmanız gerekir. 
- Bölge Numaralandırmaların fiziksel bölgelere eşlenmesi, bir Azure aboneliği temelinde düzeltilmelidir. SAP sistemlerinizi dağıtmak için farklı abonelikler kullanıyorsanız, her abonelik için ideal bölgeleri tanımlamanız gerekir.
- Azure [yakınlık yerleşimi grubunu](../../co-location.md)kullanmadığınız takdirde Azure kullanılabilirlik gruplarını bir Azure kullanılabilirlik bölgesi içinde dağıtamazsınız. SAP DBMS katmanını ve merkezi hizmetleri bölgelere dağıtmak ve aynı zamanda SAP uygulama katmanını kullanılabilirlik kümeleri kullanarak dağıtma ve yine de VM 'lerin yakınlığını elde etme yöntemi, [SAP uygulamalarıyla en iyi ağ gecikme süresi Için Azure yakınlık yerleşimi grupları](sap-proximity-placement-scenarios.md)makalesinde belgelenmiştir. Azure yakınlık yerleşimi gruplarını kullanmıyorsanız, sanal makineler için bir dağıtım çerçevesi olarak bir veya diğerini seçmeniz gerekir.
- Windows Server Yük Devretme Kümelemesi veya Linux pacemaker tabanlı yük devretme kümesi çözümleri oluşturmak için [Azure temel Load Balancer](../../../load-balancer/load-balancer-overview.md) kullanamazsınız. Bunun yerine, [Azure Standart Load Balancer SKU](../../../load-balancer/load-balancer-standard-availability-zones.md)'sunu kullanmanız gerekir.



## <a name="the-ideal-availability-zones-combination"></a>İdeal Kullanılabilirlik Alanları birleşimi
Bir SAP NetWeaver veya S/4HANA sistemini bölgeler arasında dağıtmak istiyorsanız, dağıtabileceğiniz iki ilke mimarisi vardır:

- Etkin/etkin: yoks/SCS çalıştıran VM 'lerin çifti ve DBMS katmanını çalıştıran VM 'lerin çifti iki bölgeye dağıtılır. SAP uygulama katmanını çalıştıran VM 'lerin sayısı, aynı iki bölge genelinde çift sayılara dağıtılır. Bir DBMS veya yoks/SCS sanal makinesi yük devrettikten sonra bazı açık ve etkin işlemler geri alınabilir. Ancak kullanıcılar oturum açtı. Etkin DBMS sanal makinesi ve uygulama örneklerinin hangi bölgelerden çalıştırıldıklarından emin değildir. Bu mimari, bölgeler arasında dağıtım için tercih edilen mimaridir.
- Etkin/Pasif: yoks/SCS çalıştıran VM 'lerin çifti ve DBMS katmanını çalıştıran VM 'lerin çifti iki bölgeye dağıtılır. SAP uygulama katmanını çalıştıran VM 'lerin sayısı Kullanılabilirlik Alanları birine dağıtılır. Uygulama katmanını, etkin yoks/SCS ve DBMS örneğiyle aynı bölgede çalıştırırsınız. Farklı bölgelerde ağ gecikmesi, bölgeler arasında dağıtılan uygulama katmanını çalıştırmak için çok yüksekse, bu dağıtım mimarisini kullanırsınız. Bunun yerine, SAP uygulama katmanının etkin yoks/SCS ve/veya DBMS örneğiyle aynı bölgede çalışması gerekir. Bir ASCS/SCS veya DBMS sanal makinesi ikincil bölgeye yük devreder, daha yüksek ağ gecikme süresi ve aktarım hızı azalmasıyla karşılaşabilirsiniz. Daha önce yük devretme düzeylerine geri dönmek için, daha önce yük devredilen VM 'yi mümkün olduğunca geri almanız gerekir. Bir bölgesel kesintisi oluşursa, uygulama katmanının yük devretilmesi için ikincil bölgeye yük devri gerekir. Kullanıcıların tüm sistem kapatması olarak deneyilecekleri bir etkinlik. Azure bölgelerinde bu mimari, Kullanılabilirlik Alanları kullanmak istediğinizde yalnızca önemli mimaridir. İkincil bölgeye yük devredilemeyen bir ASCS/SCS veya DBMS VM 'lerinin olası etkisini kabul edemeyebilirsiniz, kullanılabilirlik kümesi dağıtımlarıyla daha iyi olabilirsiniz


Bu nedenle Kullanılabilirlik Alanları kullanmaya karar vermeden önce şunları belirlemeniz gerekir:

- Bir Azure bölgesinin üç bölgesi arasındaki ağ gecikmesi. Bir bölgenin bölgeleri arasındaki ağ gecikmesini bilmeniz, bölgeler arası ağ trafiğinden en az ağ gecikme süresine sahip bölgeleri seçmenizi sağlar.
- Bir bölgede bulunan VM-VM gecikme süresi ve seçtiğiniz iki bölge arasındaki ağ gecikmesi arasındaki fark.
- Dağıtmanız gereken sanal makine türlerinin seçtiğiniz iki bölgede kullanılabilir olup olmadığını belirleme. Bazı VM 'Lerde, özellikle de a serisi VM 'lerle, bazı SKU 'Ların üç bölgenin yalnızca iki bölgede kullanılabildiği durumlarla karşılaşabilirsiniz.

## <a name="network-latency-between-and-within-zones"></a>Bölgeler arasında ve içinde ağ gecikmesi
Farklı bölgeler arasındaki gecikmeyi öğrenmek için şunları yapmanız gerekir:

- Her üç bölgede DBMS örneğiniz için kullanmak istediğiniz VM SKU 'sunu dağıtın. Bu ölçümü gerçekleştirdiğinizde [Azure hızlandırılmış ağ](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) 'ın etkinleştirildiğinden emin olun.
- En az ağ gecikmesi olan iki bölgeyi bulduğunuzda, üç Kullanılabilirlik Alanları üzerinde uygulama katmanı VM 'si olarak kullanmak istediğiniz VM SKU 'sunun başka üç VM 'sini dağıtın. Seçtiğiniz iki DBMS bölgesindeki iki DBMS sanal makinesi için ağ gecikmesini ölçün. 
- **`niping`** Ölçüm aracı olarak kullanın. SAP 'nin bu aracı, SAP destek notları [#500235](https://launchpad.support.sap.com/#/notes/500235) ve [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)açıklanmaktadır. Gecikme süresi ölçümleri için belgelenen komutlara odaklanın. **Ping** , Azure hızlandırılmış ağ kodu yolları üzerinden çalışmadığından, bunu kullanmanızı önermiyoruz.

Bu testleri el ile gerçekleştirmeniz gerekmez. Açıklanan gecikme süresi testlerini otomatikleştiren bir PowerShell yordamı [kullanılabilirlik bölgesi gecikme testi](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) bulabilirsiniz. 

Ölçümlerinizi ve Kullanılabilirlik Alanları sanal makine SKU 'larınızın kullanılabilirliğine göre, bazı kararlar vermeniz gerekir:

- DBMS katmanının ideal bölgelerini tanımlayın.
- Etkin SAP uygulama katmanınızı bir, iki veya üç bölgenin tamamında, bölgedeki ağ gecikme süresinin bölge genelinde farklılıklar temelinde dağıtmak isteyip istemediğinizi belirleme.
- Bir uygulama görünümünden etkin/Pasif bir yapılandırma ya da etkin/etkin bir yapılandırma dağıtmak istediğinizi belirleme. (Bu konfigürasyonlar Bu makalenin ilerleyen kısımlarında açıklanmaktadır.)

Bu kararları verirken SAP Note [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)bölümünde belgelendiği gibi, hesap SAP 'nin ağ gecikmesi önerilerini de göz önünde bulabilirsiniz.

> [!IMPORTANT]
> Yaptığınız ölçümler ve kararlar, ölçümleri aldığınızda kullandığınız Azure aboneliği için geçerlidir. Başka bir Azure aboneliği kullanıyorsanız, ölçümleri tekrarlamanız gerekir. Numaralandırılmış bölgelerin eşlemesi, başka bir Azure aboneliği için farklı olabilir.


> [!IMPORTANT]
> Daha önce açıklanan ölçümlerin [kullanılabilirlik alanları](https://azure.microsoft.com/global-infrastructure/geographies/)destekleyen her Azure bölgesinde farklı sonuçlar sağlayacağı beklenmektedir. Ağ gecikmesi gereksinimleriniz aynı olsa da, bölgeler arasındaki ağ gecikmesi farklı olabileceğinden farklı Azure bölgelerinde farklı dağıtım stratejilerini benimsemeniz gerekebilir. Bazı Azure bölgelerinde, üç farklı bölge arasındaki ağ gecikmesi büyük ölçüde farklı olabilir. Diğer bölgelerde, üç farklı bölge arasındaki ağ gecikmesi daha Tekdüzen olabilir. 1 ile 2 milisaniyelik arasında her zaman bir ağ gecikmesi olduğu talep doğru değildir. Azure bölgelerindeki Kullanılabilirlik Alanları arasındaki ağ gecikmesi genelleştirilemez.

## <a name="activeactive-deployment"></a>Etkin/etkin dağıtım
Bu dağıtım mimarisi, etkin/etkin olarak adlandırılır çünkü etkin SAP uygulama sunucularınızı iki veya üç bölgede dağıtmış olursunuz. Sıraya alma çoğaltması kullanan SAP Merkezi Hizmetler örneği iki bölge arasında dağıtılır. Aynı işlem, SAP Merkezi hizmeti ile aynı bölgelerde dağıtılacak olan DBMS katmanı için de geçerlidir. Bu yapılandırmayı düşünürken, bölgenizde, iş yükünüz ve zaman uyumlu DBMS çoğaltmayla kabul edilebilir bölgeler arası ağ gecikmesi sunan iki Kullanılabilirlik Alanları bulmanız gerekir. Ayrıca, seçtiğiniz bölgelerin içindeki ağ gecikmesi ve çapraz bölge ağ gecikmesi çok büyük olmadığından emin olmak istersiniz. 

SAP mimarisinin doğası, farklı bir şekilde yapılandırmadığınız müddetçe, kullanıcılar ve toplu işlerin farklı uygulama örneklerinde yürütülebileceğini unutmayın. Bu olgunun etkin/etkin dağıtım ile yan etkisi, toplu işlerin, etkin DBMS ile aynı bölgede çalışmasına bakılmaksızın herhangi bir SAP uygulama örneği tarafından yürütülmesi olabilir. Fark bölgeleri arasındaki ağ gecikmesi arasındaki fark, bir bölgedeki ağ gecikme süresine kıyasla küçük ise, toplu işlerin çalışma sürelerinin farkı önemli olmayabilir. Ancak, bölge ağ trafiğine kıyasla bir bölgedeki ağ gecikme süresinin farkı arttıkça, iş, DBMS örneğinin etkin olmadığı bir bölgede yürütülürse, Batch işlerinin çalışma süresi daha fazla etkilenebilir. Çalışma zamanında kabul edilebilir farkların ne olduğuna karar vermek sizin için bir müşteri olarak yapılır. Ve bölgeler arası trafik için toleransyon ağ gecikmesinin ne olduğunu gösterir.

Bu tür bir etkin/etkin dağıtımın, farklı Kullanılabilirlik Alanları dağıtılan uygulama katmanında çalışma süresi ve aktarım hızı bakımından büyük farklılıklar olmadan mümkün olması gereken Azure bölgeleri, şu şekilde listeleyin:

- Batı ABD2 (üç bölge)
- Doğu ABD2 (tüm üç bölge)
- Orta ABD (üç bölge)
- Kuzey Avrupa (üç bölge)
- Batı Avrupa (üç bölgenin ikisi)
- Doğu ABD (üç bölgenin ikisi)
- Orta Güney ABD (üç bölgenin ikisi)
- UK Güney (üç bölgenin ikisi)

Bölgeler arasında bu SAP dağıtım mimarisinin kullanılması önerilmez:

- Orta Fransa 
- Güney Afrika - Kuzey
- Orta Kanada
- Doğu Japonya

Çalışma zamanı farklılıklarına ne kadar uygun olduğuna bağlı olarak, listelenmeyen diğer bölgeler de uygun olabilir.


İki bölgede etkin/etkin bir dağıtımın Basitleştirilmiş şeması şuna benzeyebilir:

![Etkin/etkin bölge dağıtımı](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Bu yapılandırma için aşağıdaki noktalar geçerlidir:

- [Azure yakınlık yerleşimi grubunu](../../co-location.md)kullanmıyorsanız, kullanılabilirlik kümeleri Azure kullanılabilirlik alanları dağıtılamadığından, tüm VM 'ler için Azure kullanılabilirlik alanları hata ve güncelleştirme etki alanlarını kabul edersiniz.
- DBMS katmanı ve merkezi hizmetler için bölgesel dağıtımlarını birleştirmek istiyorsanız, ancak uygulama katmanı için Azure kullanılabilirlik kümelerini kullanmak istiyorsanız, [SAP uygulamalarıyla en iyi ağ gecikmesi için Azure yakınlık yerleşimi grupları](sap-proximity-placement-scenarios.md)makalesinde açıklandığı gibi Azure yakınlık grupları 'nı kullanmanız gerekir.
- SAP Merkezi Hizmetleri ve DBMS katmanının yük devretme kümelerinin yük dengeleyiciler için [Standart SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)kullanmanız gerekir. Temel Load Balancer bölgeler arasında çalışmaz.
- SAP sistemini barındırmak için dağıttığınız Azure sanal ağı, alt ağları ile birlikte bölgeler arasında uzatılır. Her bölge için ayrı sanal ağlara gerek yoktur.
- Dağıttığınız tüm sanal makineler için [Azure yönetilen diskleri](https://azure.microsoft.com/services/managed-disks/)kullanmanız gerekir. Yönetilmeyen diskler, bölgesel dağıtımları için desteklenmez.
- Azure Premium Depolama, [Ultra SSD Storage](../../disks-types.md#ultra-disk)veya ANF bölgeler arasında herhangi bir tür depolama çoğaltmasını desteklemez. Uygulama (DBMS veya SAP Merkezi Hizmetler) önemli verileri çoğaltmalıdır.
- Aynı, paylaşılan bir disk (Windows), CIFS paylaşım (Windows) veya NFS paylaşım (Linux) olan paylaşılan sapmnt dizini için de geçerlidir. Bu Paylaşılan diskleri veya paylaşımları bölgeler arasında çoğaltan bir teknoloji kullanmanız gerekir. Bu teknolojiler desteklenir:
  - Windows için, [Azure 'da bir küme paylaşılan diski kullanarak bir Windows Yük devretme KÜMESINDEKI SAP ASCS/SCS örneği](./sap-high-availability-guide-wsfc-shared-disk.md)bölümünde belgelendiği gıbı, SIOS Dataman kullanan bir küme çözümüdür.
  - SUSE Linux için, [SUSE Linux Enterprise Server üzerinde Azure VM 'LERINDE NFS Için yüksek kullanılabilirlik](./high-availability-guide-suse-nfs.md)makalesinde belgelenen şekilde oluşturulmuş bir NFS paylaşımıdır.
    
    Şu anda, [bir Windows Yük devretme kümesi ve SAP ASCS/SCS örnekleri için dosya paylaşma kullanarak SAP yüksek kullanılabilirlik Için Azure altyapısını hazırlama](./sap-high-availability-infrastructure-wsfc-file-share.md)bölümünde belgelendiği gibi, Microsoft Scale-Out dosya sunucusu kullanan çözüm bölgeler arasında desteklenmez.
- Üçüncü bölge, [SUSE Linux Paceyapıcısı kümesi](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) oluşturup Azure ayırma Aracısı yerine SBD cihazlarını kullanıyorsanız SBD cihazını barındırmak için kullanılır. Ya da ek uygulama örnekleri için.
- Kritik iş süreçlerine yönelik çalışma süresi tutarlılığı elde etmek için, SAP Batch sunucu grupları, SAP oturum açma grupları veya RFC grupları ' nı kullanarak, belirli toplu işleri ve kullanıcıları, etkin DBMS örneğiyle bölge içinde olan uygulama örneklerine yönlendirmeyi deneyebilirsiniz. Ancak, bir yük devretme durumunda, bu grupları etkin DB VM 'si ile bölgedeki VM 'lerde çalışan örneklere el ile taşımanız gerekir.  
- Her bölgede etkin olmayan iletişim kutusu örnekleri dağıtmak isteyebilirsiniz. 

> [!IMPORTANT]
> Bu etkin/etkin senaryoda, bant genişliği için ek ücretler, Microsoft tarafından 04/01/2020 adresinden duyurulur. Belge [bant genişliği fiyatlandırma ayrıntılarını](https://azure.microsoft.com/pricing/details/bandwidth/)kontrol edin. SAP uygulama katmanı ve SAP DBMS katmanı arasındaki veri aktarımı oldukça yoğun. Bu nedenle, etkin/etkin senaryo maliyetlerde tam olarak bir bit olarak katkıda bulunabilir. Maliyetleri tam olarak almak için bu makaleyi denetlemeye devam edin 


## <a name="activepassive-deployment"></a>Etkin/Pasif dağıtım
Bir bölgedeki ağ gecikme süresi ve çapraz bölge ağ trafiği gecikmesi arasında kabul edilebilir bir Delta bulamıyorsanız, görünümün SAP uygulama katmanı noktasından etkin/Pasif bir karakter içeren bir mimari dağıtabilirsiniz. Tüm uygulama katmanını dağıttığınız ve hem etkin DBMS hem de SAP Merkezi Hizmetleri örneğini çalıştırmayı denebileceğiniz bölge olan *etkin* bir bölge tanımlarsınız. Böyle bir yapılandırma sayesinde, bir işin etkin DBMS örneğiyle bölge içinde çalışıp çalışmadığını, iş işlemlerinde ve Batch işlerinde değil, çok fazla çalışma zamanı varyasyonlarından emin olmanız gerekir.

Farklı bölgelerde bu tür bir dağıtım mimarisinin tercih edileceği Azure bölgeleri şunlardır:

- Güneydoğu Asya
- Doğu Avustralya
- Güney Brezilya
- Almanya Orta Batı
- Güney Afrika - Kuzey
- Orta Fransa 
- Orta Kanada
- Doğu Japonya


Mimarinin temel düzeni şöyle görünür:

![Etkin/Pasif bölge dağıtımı](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Bu yapılandırma için aşağıdaki noktalar geçerlidir:

- Kullanılabilirlik kümeleri Azure Kullanılabilirlik Alanları ' de dağıtılamaz. Bunu dengelemek için, [SAP uygulamalarıyla en iyi ağ gecikme süresi Için Azure yakınlık yerleşimi grupları](sap-proximity-placement-scenarios.md)makalesinde belgelenen Azure yakınlık yerleşimi gruplarını kullanabilirsiniz.
- Bu mimariyi kullandığınızda, durumu yakından izlemeniz ve etkin DBMS ve SAP Merkezi Hizmetleri örneklerini dağıtılan uygulama katmanınız ile aynı bölgede tutmaya çalışmalısınız. SAP Merkezi hizmetinin veya DBMS örneğinin yük devretmesi durumunda, mümkün olduğunca hızlı bir şekilde dağıtılmış SAP uygulama katmanı ile bölgeye el ile yeniden oturum açmak istiyorsanız emin olmanız gerekir.
- SAP Merkezi Hizmetleri ve DBMS katmanının yük devretme kümelerinin yük dengeleyiciler için [Standart SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)kullanmanız gerekir. Temel Load Balancer bölgeler arasında çalışmaz.
- SAP sistemini barındırmak için dağıttığınız Azure sanal ağı, alt ağları ile birlikte bölgeler arasında uzatılır. Her bölge için ayrı sanal ağlara gerek yoktur.
- Dağıttığınız tüm sanal makineler için [Azure yönetilen diskleri](https://azure.microsoft.com/services/managed-disks/)kullanmanız gerekir. Yönetilmeyen diskler, bölgesel dağıtımları için desteklenmez.
- Azure Premium Depolama, [Ultra SSD Storage](../../disks-types.md#ultra-disk)veya ANF bölgeler arasında herhangi bir tür depolama çoğaltmasını desteklemez. Uygulama (DBMS veya SAP Merkezi Hizmetler) önemli verileri çoğaltmalıdır.
- Aynı, paylaşılan bir disk (Windows), CIFS paylaşım (Windows) veya NFS paylaşım (Linux) olan paylaşılan sapmnt dizini için de geçerlidir. Bu Paylaşılan diskleri veya paylaşımları bölgeler arasında çoğaltan bir teknoloji kullanmanız gerekir. Bu teknolojiler desteklenir:
    - Windows için, [Azure 'da bir küme paylaşılan diski kullanarak bir Windows Yük devretme KÜMESINDEKI SAP ASCS/SCS örneği](./sap-high-availability-guide-wsfc-shared-disk.md)bölümünde belgelendiği gıbı, SIOS Dataman kullanan bir küme çözümüdür.
    - SUSE Linux için, [SUSE Linux Enterprise Server üzerinde Azure VM 'LERINDE NFS Için yüksek kullanılabilirlik](./high-availability-guide-suse-nfs.md)makalesinde belgelenen şekilde oluşturulmuş bir NFS paylaşımıdır.
    
  Şu anda, [bir Windows Yük devretme kümesi ve SAP ASCS/SCS örnekleri için dosya paylaşma kullanarak SAP yüksek kullanılabilirlik Için Azure altyapısını hazırlama](./sap-high-availability-infrastructure-wsfc-file-share.md)bölümünde belgelendiği gibi, Microsoft Scale-Out dosya sunucusu kullanan çözüm bölgeler arasında desteklenmez.
- Üçüncü bölge, [SUSE Linux Paceyapıcısı kümesi](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) oluşturup Azure ayırma Aracısı yerine SBD cihazlarını kullanıyorsanız SBD cihazını barındırmak için kullanılır. Ya da ek uygulama örnekleri için.
- Bir bölge hatası durumunda uygulama kaynaklarını başlatabilmeniz için, pasif bölgeye (bir DBMS bakış noktasından) etkin olmayan VM 'Ler dağıtmanız gerekir.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) Şu anda, etkin VM 'leri bölgeler arasında etkin olmayan VM 'lere çoğaltamaz. 
- Bir bölgesel kesintisi oluşursa, ikinci bölgede SAP uygulama katmanını otomatik olarak başlatabilmeniz için Otomasyon 'a yatırım yapmanız gerekir.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Birleşik yüksek kullanılabilirlik ve olağanüstü durum kurtarma yapılandırması
Microsoft, bir Azure bölgesindeki farklı Azure Kullanılabilirlik Alanları barındıran tesislerde coğrafi uzaklıklar hakkında herhangi bir bilgi paylaşmaz. Yine de bazı müşteriler, bir kurtarma noktası hedefini (RPO) içeren bir birleştirilmiş HA ve DR yapılandırması için bölgeleri kullanıyor. Sıfır RPO 'SU, olağanüstü durum kurtarma durumunda bile, tüm kaydedilmiş veritabanı işlemlerini kaybetmemeniz gerektiği anlamına gelir. 

> [!NOTE]
> Yalnızca belirli koşullarda bu şekilde bir yapılandırma kullanmanızı öneririz. Örneğin, veriler Azure bölgesinden güvenlik veya uyumluluk nedenleriyle bu şekilde çıkabileceği zaman kullanabilirsiniz. 

Bu tür bir yapılandırmanın nasıl görünebileceğini gösteren bir örnek aşağıda verilmiştir:

![Bölgelerde yüksek kullanılabilirliğe sahip DR](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Bu yapılandırma için aşağıdaki noktalar geçerlidir:

- Bir kullanılabilirlik bölgesini barındıran tesislerin arasında önemli bir mesafe olduğunu veya belirli bir Azure bölgesi içinde kalmaya zorladığınızı varsayalım. Kullanılabilirlik kümeleri Azure Kullanılabilirlik Alanları ' de dağıtılamaz. Bunu dengelemek için, [SAP uygulamalarıyla en iyi ağ gecikme süresi Için Azure yakınlık yerleşimi grupları](sap-proximity-placement-scenarios.md)makalesinde belgelenen Azure yakınlık yerleşimi gruplarını kullanabilirsiniz.
- Bu mimariyi kullandığınızda, durumu yakından izlemeniz ve etkin DBMS ve SAP Merkezi Hizmetleri örneklerini dağıtılan uygulama katmanınız ile aynı bölgede tutmaya çalışmalısınız. SAP Merkezi hizmetinin veya DBMS örneğinin yük devretmesi durumunda, mümkün olduğunca hızlı bir şekilde dağıtılmış SAP uygulama katmanı ile bölgeye el ile yeniden oturum açmak istiyorsanız emin olmanız gerekir.
- Etkin QA uygulama örneklerini çalıştıran VM 'lerde önceden yüklenmiş üretim uygulaması örneklerinin olması gerekir.
- Bir bölgesel hata durumunda, qa uygulama örneklerini kapatın ve bunun yerine üretim örneklerini başlatın. Bu işi yapmak için uygulama örnekleri için sanal adlar kullanmanız gerekir.
- SAP Merkezi Hizmetleri ve DBMS katmanının yük devretme kümelerinin yük dengeleyiciler için [Standart SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)kullanmanız gerekir. Temel Load Balancer bölgeler arasında çalışmaz.
- SAP sistemini barındırmak için dağıttığınız Azure sanal ağı, alt ağları ile birlikte bölgeler arasında uzatılır. Her bölge için ayrı sanal ağlara gerek yoktur.
- Dağıttığınız tüm sanal makineler için [Azure yönetilen diskleri](https://azure.microsoft.com/services/managed-disks/)kullanmanız gerekir. Yönetilmeyen diskler, bölgesel dağıtımları için desteklenmez.
- Azure Premium Depolama ve [Ultra SSD depolama](../../disks-types.md#ultra-disk) alanları arasında herhangi bir tür depolama çoğaltmasını desteklemez. Uygulama (DBMS veya SAP Merkezi Hizmetler) önemli verileri çoğaltmalıdır.
- Aynı, paylaşılan bir disk (Windows), CIFS paylaşım (Windows) veya NFS paylaşım (Linux) olan paylaşılan sapmnt dizini için de geçerlidir. Bu Paylaşılan diskleri veya paylaşımları bölgeler arasında çoğaltan bir teknoloji kullanmanız gerekir. Bu teknolojiler desteklenir:
    - Windows için, [Azure 'da bir küme paylaşılan diski kullanarak bir Windows Yük devretme KÜMESINDEKI SAP ASCS/SCS örneği](./sap-high-availability-guide-wsfc-shared-disk.md)bölümünde belgelendiği gıbı, SIOS Dataman kullanan bir küme çözümüdür.
    - SUSE Linux için, [SUSE Linux Enterprise Server üzerinde Azure VM 'LERINDE NFS Için yüksek kullanılabilirlik](./high-availability-guide-suse-nfs.md)makalesinde belgelenen şekilde oluşturulmuş bir NFS paylaşımıdır.

  Şu anda, [bir Windows Yük devretme kümesi ve SAP ASCS/SCS örnekleri için dosya paylaşma kullanarak SAP yüksek kullanılabilirlik Için Azure altyapısını hazırlama](./sap-high-availability-infrastructure-wsfc-file-share.md)bölümünde belgelendiği gibi, Microsoft Scale-Out dosya sunucusu kullanan çözüm bölgeler arasında desteklenmez.
- Üçüncü bölge, [SUSE Linux Paceyapıcısı kümesi](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) oluşturup Azure ayırma Aracısı yerine SBD cihazlarını kullanıyorsanız SBD cihazını barındırmak için kullanılır. 





## <a name="next-steps"></a>Sonraki adımlar
Azure Kullanılabilirlik Alanları arasında dağıtım için bazı sonraki adımlar aşağıda verilmiştir:

- [Azure 'da küme paylaşılan diski kullanarak bir Windows Yük devretme kümesinde SAP ASCS/SCS örneği oluşturma](./sap-high-availability-guide-wsfc-shared-disk.md)
- [SAP Ass/SCS örnekleri için bir Windows Yük devretme kümesi ve dosya paylaşma kullanarak SAP yüksek kullanılabilirlik için Azure altyapısını hazırlama](./sap-high-availability-infrastructure-wsfc-file-share.md)