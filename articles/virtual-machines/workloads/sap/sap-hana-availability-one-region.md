---
title: Tek bir Azure bölgesinde kullanılabilirliği SAP HANA | Microsoft Docs
description: Azure yerel VM 'lerde tek bir Azure bölgesinde SAP HANA işlemlerini açıklar.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c5b4904419af1fe86e43dc2f781ef43ce8dd762
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078780"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Tek bir Azure bölgesinde kullanılabilirliği SAP HANA
Bu makalede, bir Azure bölgesindeki çeşitli kullanılabilirlik senaryoları açıklanmaktadır. Azure, dünyanın tamamında yayılan birçok bölgeye sahiptir. Azure bölgelerinin listesi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/). Tek bir Azure bölgesindeki sanal makinelere SAP HANA dağıtmak için, Microsoft, bir HANA örneğiyle tek bir sanal makinenin dağıtımını sunmaktadır. Daha yüksek kullanılabilirlik için, kullanılabilirlik için HANA sistem çoğaltması kullanan bir [Azure kullanılabilirlik kümesi](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) IÇINDE iki Hana örneğiyle iki VM dağıtabilirsiniz. 

Azure Şu anda [Azure kullanılabilirlik alanları](https://docs.microsoft.com/azure/availability-zones/az-overview)sunmaktadır. Bu makale Kullanılabilirlik Alanları ayrıntılı olarak tanımlamaz. Ancak, Kullanılabilirlik Alanları ve kullanılabilirlik kümelerini kullanma hakkında genel bir tartışma içerir.

Kullanılabilirlik Alanları sunulan Azure bölgelerinin birden çok veri merkezi vardır. Veri merkezleri, güç kaynağı, soğutma ve ağ tedarikinden bağımsızdır. Tek bir Azure bölgesi içinde farklı bölgeler sunma nedeni, sunulan iki veya üç Kullanılabilirlik Alanları arasında uygulama dağıtmaktır. Bölgeler arasında dağıtım, güç ve ağ sorunları yalnızca bir Azure kullanılabilirlik alanı altyapısını etkileyerek, bir Azure bölgesindeki uygulama dağıtımınız hala çalışır durumda. Azaltılan bazı kapasite meydana gelebilir. Örneğin, bir bölgedeki VM 'Ler kaybolabilir, ancak diğer iki bölgedeki VM 'Ler çalışmaya devam edebilir. 
 
Azure kullanılabilirlik kümesi, kullanılabilirlik kümesi içinde yerleştirdiğiniz VM kaynaklarının, bir Azure veri merkezinde dağıtıldığında birbirinden hata yalıtımlı olmasını sağlamaya yardımcı olan bir mantıksal gruplandırma özelliğidir. Azure, bir Kullanılabilirlik Kümesi içine yerleştirdiğiniz sanal makinelerin birden fazla fiziksel sunucuda, bilgi işlem rafında, depolama biriminde ve ağ anahtarında çalışmasını sağlar. Bazı Azure belgelerinde, bu yapılandırma farklı [güncelleştirme ve hata etki alanlarında](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)placeas olarak adlandırılır. Bu Place, genellikle bir Azure veri merkezinde yer alan. Güç kaynağı ve ağ sorunlarının dağıttığınız veri merkezini etkileyeceğini varsayarsak, bir Azure bölgesindeki tüm kapasiteniz etkilenir.

Azure Kullanılabilirlik Alanları temsil eden veri merkezlerinin yerleşimi, farklı bölgelerde dağıtılan hizmetler arasında kabul edilebilir ağ gecikme süresi ve veri merkezleri arasında bir mesafe arasında bir uzlaşdır. Doğal catastrophes ideal olarak bu bölgedeki tüm Kullanılabilirlik Alanları için güç, ağ kaynağı ve altyapıyı etkilemez. Ancak, Monumental doğal catastrophes gösterildiği gibi, Kullanılabilirlik Alanları her zaman bir bölgede istediğiniz kullanılabilirliği sağlayamayabilir. 20 Eylül 2017 ' de Porto Riko 'nun Adası ile başlayan Acericane Maria hakkında düşünün. Acericane, temelde 90-Wide Adası üzerinde yaklaşık% 100 bir kararya neden oldu.

## <a name="single-vm-scenario"></a>Tek VM senaryosu

Tek VM senaryosunda, SAP HANA örneği için bir Azure VM oluşturursunuz. Azure Premium Storage 'ı kullanarak işletim sistemi diskini ve tüm veri disklerinizi barındırabilirsiniz. Azure çalışma süresi SLA 'Sı% 99,9 ve diğer Azure bileşenlerinin SLA 'ları, müşterileriniz için kullanılabilirlik SLA 'larını karşılamanız için yeterlidir. Bu senaryoda, DBMS katmanını çalıştıran VM 'Ler için bir Azure kullanılabilirlik kümesinden faydalanmanıza gerek yoktur. Bu senaryoda, iki farklı özelliğe güvenin:

- Azure VM otomatik yeniden başlatması (Azure hizmet düzeltme hizmeti olarak da bilinir)
- SAP HANA otomatik yeniden başlatma

Azure VM otomatik yeniden başlatması veya hizmet düzeltme hizmeti, Azure 'da iki düzeyde çalışacak bir işlevdir:

- Azure Server ana bilgisayarı, sunucu konağında barındırılan bir VM 'nin durumunu denetler.
- Azure yapı denetleyicisi, sunucu konağının sistem durumunu ve kullanılabilirliğini izler.

Bir sistem durumu denetimi işlevselliği, bir Azure Server konağında barındırılan her VM 'nin sistem durumunu izler. Bir VM sağlıklı olmayan bir durumda kalırsa, sanal makinenin yeniden başlatılması, sanal makinenin sistem durumunu denetleyen Azure konak Aracısı tarafından başlatılabilir. Yapı denetleyicisi konak donanımı ile ilgili sorunları gösterebilecek olabilecek birçok farklı parametreyi denetleyerek konağın sistem durumunu denetler. Ayrıca, ağ üzerinden ana bilgisayarın erişilebilirliğini denetler. Ana bilgisayarla ilgili sorunların göstergesi aşağıdaki olaylara yol açabilir:

- Ana bilgisayar hatalı bir sistem durumuna işaret eder, ana bilgisayarın yeniden başlatılması ve konakta çalışan VM 'lerin yeniden başlatılması tetiklenir.
- Bilgisayar başarıyla yeniden başlatıldıktan sonra sağlıklı bir durumda değilse, aslında sağlıklı bir ana bilgisayar üzerinde artık sağlıksız bir düğümde olan VM 'lerin yeniden dağıtımı başlatılır. Bu durumda, özgün ana bilgisayar sağlıklı değil olarak işaretlenir. Bu, temizlenmeden veya değiştirilene kadar daha fazla dağıtım için kullanılmaz.
- Yeniden başlatma işlemi sırasında sağlıksız konağın sorunları varsa, sağlıklı bir konaktaki VM 'lerin hemen yeniden başlatılması tetiklenir. 

Azure tarafından sunulan konak ve VM izleme ile, ana bilgisayar sorunları yaşayan Azure VM 'Leri sağlıklı bir Azure ana bilgisayarında otomatik olarak yeniden başlatılır. 

>[!IMPORTANT]
>Azure hizmeti düzeltme, Konuk işletim sisteminin bir çekirdek panik durumunda olduğu Linux VM 'lerini yeniden başlatmaz. Yaygın olarak kullanılan Linux yayınlarının varsayılan ayarları, Linux çekirdeğinin panik durumunda olduğu VM 'leri veya sunucuyu otomatik olarak yeniden başlatmamalıdır. Bunun yerine, varsayılan olarak, çözümlemek üzere bir çekirdek hata ayıklayıcısı iliştirebilmek için sistemi çekirdek panik durumunda tutmaya yönelik olarak görür. Azure, Konuk işletim sistemi olan bir VM 'yi böyle bir durumda otomatik olarak yeniden başlatmayana bu davranışı garanti ediyor. Varsayım, bu oluşumların son derece nadir bir durumdur. VM 'nin yeniden başlatılmasını sağlamak için varsayılan davranışın üzerine yazabilirsiniz. Varsayılan davranışı değiştirmek için,/etc/sysctl.exe içindeki ' Kernel. Panic ' parametresini etkinleştirin. Bu parametre için ayarladığınız zaman Saniyeler içinde. Bu parametre aracılığıyla yeniden başlatmayı tetiklemeden önce, sık önerilen değerler 20-30 saniye beklemeniz önerilir. Ayrıca <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>bkz.

Bu senaryoda kullandığınız ikinci özellik, yeniden başlatılan bir VM 'de çalışan HANA hizmetinin VM yeniden başlatıldıktan sonra otomatik olarak başlayacağını bulmasıdır. Farklı HANA hizmetlerinin izleme hizmetleri aracılığıyla [Hana hizmetini otomatik yeniden başlatmayı](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) ayarlayabilirsiniz.

Bir SAP HANA yapılandırmasına bir soğuk yük devretme düğümü ekleyerek bu tek VM senaryosunu geliştirebilirsiniz. SAP HANA belgelerinde, bu kuruluma [konak otomatik yük devretme](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html)adı verilir. Bu yapılandırma, sunucu donanımının sınırlı olduğu bir şirket içi dağıtım durumunda anlam verebilir ve bir üretim Konakları kümesi için konak otomatik yük devretme düğümü olarak tek sunuculu bir düğüm ayırabilirsiniz. Azure 'da, Azure 'un temel alınan altyapısının başarılı bir VM yeniden başlatması için sağlıklı bir hedef sunucu sağladığından, SAP HANA konak otomatik yük devretmesini dağıtmak mantıklı değildir. Azure hizmeti iyileştirme nedeniyle, HANA konak otomatik yük devretmesi için bir bekleme düğümü sunan bir başvuru mimarisi yoktur. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Azure 'da SAP HANA genişleme yapılandırmalarının özel durumu
SAP HANA genişleme yapılandırmalarına yönelik yüksek kullanılabilirlik, Azure VM 'lerinin hizmet düzeltme yapılandırmasına ve sanal makine çalışır duruma geçtiği SAP HANA örneğinin yeniden başlatılmasına bağlıdır. HANA sistem çoğaltmasını temel alan yüksek kullanılabilirliğe sahip mimariler, daha sonraki bir zamanda tanıtılmaktadır. 


## <a name="availability-scenarios-for-two-different-vms"></a>İki farklı VM için kullanılabilirlik senaryoları

Bir Azure kullanılabilirlik kümesi içinde iki Azure sanal makinesi kullanıyorsanız, tek bir Azure bölgesinde bir Azure kullanılabilirlik kümesine yerleştirilebilecek bu iki VM arasındaki çalışma süresini artırabilirsiniz. Azure 'daki temel kurulum şöyle görünür:

![Tüm katmanlarla iki VM 'nin diyagramı](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Farklı kullanılabilirlik senaryolarını göstermek için diyagramdaki katmanların bazıları atlanır. Diyagram yalnızca VM 'Leri, Konakları, kullanılabilirlik kümelerini ve Azure bölgelerini belirleyen katmanları gösterir. Azure sanal ağ örnekleri, kaynak grupları ve abonelikler bu bölümde açıklanan senaryolarda bir rol oynamaz.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Yedeklemeleri ikinci bir sanal makineye çoğaltma

En ilkel kurulumlardan biri yedeklemeleri kullanmaktır. Özellikle, bir VM 'den başka bir Azure VM 'ye gönderilen işlem günlüğü yedeğine sahip olabilirsiniz. Azure depolama türünü seçebilirsiniz. Bu kurulumda, ilk VM 'de ikinci sanal makineye yürütülen zamanlanmış yedeklemelerin kopyasının komut dosyası oluşturma sorumluluğunuz vardır. İkinci sanal makine örneklerini kullanmanız gerekiyorsa, tam, artımlı/fark ve işlem günlüğü yedeklemelerini ihtiyacınız olan noktaya geri yüklemeniz gerekir. 

Mimari şöyle görünür:

![Depolama çoğaltması olan iki VM 'nin diyagramı](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Bu kurulum, harika kurtarma noktası hedefi (RPO) ve kurtarma süresi hedefi (RTO) zamanlarını elde etmek için uygun değildir. RTO zamanları özellikle, kopyalanmış yedeklemeleri kullanarak tam veritabanını tamamen geri yükleme gereksiniminden kaynaklanıyor olabilir. Ancak, bu kurulum ana örneklerde istenmeden veri silmeyi kurtarmak için yararlıdır. Bu kurulumla, herhangi bir zamanda belirli bir noktaya geri yükleme yapabilir, verileri ayıklayabilir ve silinen verileri ana örneğinizle içeri aktarabilirsiniz. Bu nedenle, bir yedek kopya yönteminin diğer yüksek kullanılabilirlik işlevselliğiyle birlikte kullanılması mantıklı olabilir. 

Yedeklemeler kopyalanırken, SAP HANA örneğinin üzerinde çalıştığı ana VM 'den daha küçük bir VM kullanabilirsiniz. Daha küçük VM 'lere daha az sayıda VHD iliştirebileceğinizi aklınızda bulundurun. Tek tek VM türlerinin limitleri hakkında daha fazla bilgi için bkz. [Azure 'Da Linux sanal makineleri Için boyutlar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Otomatik yük devretme olmadan sistem çoğaltmasını SAP HANA

Bu bölümde açıklanan senaryolar SAP HANA sistem çoğaltmasını kullanır. SAP belgeleri için bkz. [sistem çoğaltma](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Otomatik yük devretme olmadan senaryolar, bir Azure bölgesindeki yapılandırmalarda yaygın değildir. Otomatik yük devretme olmadan bir yapılandırma olan bir pacemaker kurulumundan kaçınmanın yanı sıra el ile izleme ve yük devretme obligates. Bunun yanı sıra, çoğu müşteri bunun yerine Azure hizmet düzeltme hizmetine bağlıdır. Bu yapılandırmanın hata senaryoları açısından yardımcı olabileceği bazı uç durumlar vardır. Ya da bir müşteri, bazı durumlarda daha fazla verimlilik sağlamak isteyebilir.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Otomatik yük devretme olmadan ve veri önyükleme olmadan sistem çoğaltmasını SAP HANA

Bu senaryoda, verileri eşit bir şekilde taşımak için SAP HANA sistem çoğaltmasını kullanarak 0 RPO 'SU elde edersiniz. Öte yandan, yük devretmeye veya HANA örnek önbelleğinde önceden yüklenen verilere gerek duymayın. Bu durumda, aşağıdaki eylemleri gerçekleştirerek yapılandırmanızda daha fazla ekonomisi elde etmek mümkündür:

- İkinci VM 'de başka bir SAP HANA örneği çalıştırın. İkinci VM 'deki SAP HANA örneği, sanal makine belleğinin büyük bir kısmını alır. İkinci VM 'ye yük devretme durumunda, çoğaltılan verilerin ikinci sanal makinede hedeflenen HANA örneğinin önbelleğine yüklenebilmesi için ikinci sanal makineye tamamen yüklenmiş olan çalışan SAP HANA örneğini kapatmanız gerekir.
- İkinci VM 'de daha küçük bir VM boyutu kullanın. Yük devretme gerçekleşirse, el ile yük devretmeden önce ek bir adımınız olur. Bu adımda, VM 'yi kaynak sanal makinenin boyutuna göre yeniden boyutlandırabilirsiniz. 
 
Senaryo şu şekilde görünür:

![Depolama çoğaltması olan iki VM 'nin diyagramı](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> HANA sistem çoğaltma hedefinde veri önyükleme kullanmasanız bile, en az 64 GB bellek gerekir. Ayrıca, rowstore verilerini hedef örneğin belleğine tutmak için 64 GB 'a ek olarak yeterli belleğe ihtiyacınız vardır.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Otomatik yük devretme ve veri önyükleme olmadan sistem çoğaltmasını SAP HANA

Bu senaryoda, ikinci VM 'de HANA örneğine çoğaltılan veriler önceden yüklenir. Bu, verileri önceden yüklemenin iki avantajını ortadan kaldırır. Bu durumda, ikinci sanal makinede başka bir SAP HANA sistemi çalıştıramazsınız. Ayrıca, daha küçük bir VM boyutu kullanamazsınız. Bu nedenle, müşteriler nadiren bu senaryoyu uygular.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Otomatik yük devretme ile sistem çoğaltmasını SAP HANA

Tek bir Azure bölgesindeki standart ve en yaygın kullanılabilirlik yapılandırmasında, SLES Linux çalıştıran iki Azure VM 'nin tanımlanmış bir yük devretme kümesi vardır. SLES Linux kümesi, bir [Stonith](http://linux-ha.org/wiki/STONITH) aygıtıyla birlikte [pacemaker](http://www.linux-ha.org/wiki/Pacemaker) çerçevesini temel alır. 

SAP HANA perspektifinden, kullanılan çoğaltma modu eşitlenir ve otomatik yük devretme yapılandırılır. İkinci VM 'de SAP HANA örnek, etkin bir bekleme düğümü işlevi görür. Bekleme düğümü, birincil SAP HANA örneğinden gelen değişiklik kayıtlarının zaman uyumlu bir akışını alır. İşlemler, HANA birincil düğümündeki uygulama tarafından işlendiği için, birincil HANA düğümü, ikincil SAP HANA düğümü, işleme kaydını aldığını onaylaana kadar uygulamaya yapılan yürütmeyi onaylamasını bekler. SAP HANA iki zaman uyumlu çoğaltma modu sunar. Ayrıntılar ve bu iki zaman uyumlu çoğaltma modu arasındaki farkların açıklaması için, [SAP HANA sistem çoğaltması IÇIN SAP makalesi çoğaltma modları](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html)bölümüne bakın.

Genel yapılandırma şöyle görünür:

![Depolama çoğaltması ve yük devretme ile iki sanal makine diyagramı](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Bu çözümü seçebilirsiniz çünkü bir RPO = 0 ve düşük RTO elde etmenizi sağlar. SAP HANA istemcilerinin HANA sistem çoğaltma yapılandırmasına bağlanmak üzere sanal IP adresini kullanabilmesi için SAP HANA istemci bağlantısını yapılandırın. Bu tür bir yapılandırma, ikincil düğüme bir yük devretme gerçekleşirse uygulamayı yeniden yapılandırma gereksinimini ortadan kaldırır. Bu senaryoda, birincil ve ikincil VM 'Ler için Azure sanal makine SKU 'Larının aynı olması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Azure 'da bu yapılandırmaların ayarlanmasına yönelik adım adım yönergeler için, bkz.:

- [Azure VM 'lerinde SAP HANA sistem çoğaltması ayarlama](sap-hana-high-availability.md)
- [Sistem çoğaltması kullanılarak SAP HANA için yüksek kullanılabilirlik](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Azure bölgelerinde SAP HANA kullanılabilirliği hakkında daha fazla bilgi için bkz.:

- [Azure bölgeleri arasında kullanılabilirlik SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

