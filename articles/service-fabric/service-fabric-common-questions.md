---
title: Microsoft Azure Service Fabric hakkında sık sorulan sorular | Microsoft Docs
description: Service Fabric ve yanıtları hakkında sık sorulan sorular
services: service-fabric
documentationcenter: .net
author: chackdan
manager: chackdan
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: 28a0418fd94c03f1fe308c7cd6f17b6d9a331fb0
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529361"
---
# <a name="commonly-asked-service-fabric-questions"></a>Sık sorulan Service Fabric sorular

Service Fabric ne yapabilecekleri ve nasıl kullanılması gerektiği hakkında sık sorulan birçok soru vardır. Bu belge, yaygın soruların ve bunların yanıtlarının çoğunu içerir.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Küme kurulumu ve yönetimi

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Service Fabric küme sertifikamı geri almak Nasıl yaparım??

Uygulamanıza yapılan herhangi bir yükseltmeyi geri alma işlemi, Service Fabric kümesi çekirdeğinin değişikliği yapmadan önce sistem durumu hata algılaması gerektirir; yürütülen değişiklikler yalnızca ileri alınabilir. İzlenmeyen bir sertifika değişikliği ortaya çıktıysa, müşteri destek hizmetleri aracılığıyla yükseltme mühendisi, kümenizi kurtarmak için gerekli olabilir.  [Service Fabric uygulama yükseltmesi](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) [uygulama yükseltme parametreleri](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master)uygular ve sıfır kesinti süresi yükseltme taahhüdünü sunar.  Önerilen uygulama yükseltme izlenen Modumuzdan sonra, güncelleştirme etki alanları aracılığıyla otomatik ilerleme durumu denetimleri başarılı olur ve varsayılan bir hizmetin güncelleştirilmesi başarısız olursa otomatik olarak geri döndürülüyor.
 
Kümeniz Kaynak Yöneticisi şablonunuzda klasik sertifika parmak Izi özelliğini kullanmaya devam ediyorsa, modern gizli dizi yönetim özelliklerinden yararlanmak için [kümeyi sertifika parmak iziyle ortak ad olarak değiştirmeniz](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn)önerilir.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Birden çok Azure bölgesini veya kendi veri merkezlerimi kapsayan bir küme oluşturabilir miyim?

Evet. 

Çekirdek Service Fabric kümeleme teknolojisi, dünyanın herhangi bir yerinden çalışan makineleri birleştirmek için kullanılabilir, bu sayede birbirleriyle ağ bağlantısı olduğu sürece. Ancak, böyle bir küme oluşturmak ve çalıştırmak karmaşık olabilir.

Bu senaryoyla ilgileniyorsanız, ek rehberlik elde etmek için [Service Fabric GitHub sorunları listesi](https://github.com/azure/service-fabric-issues) veya destek temsilcilerinizle iletişim kurmanız önerilir. Service Fabric takım, bu senaryoya yönelik ek netlik, kılavuz ve öneriler sağlamak için çalışmaktadır. 

Dikkate alınması gereken bazı noktalar şunlardır: 

1. Azure 'daki Service Fabric küme kaynağı, kümenin üzerinde oluşturulduğu sanal makine ölçek kümeleri olduğundan, bugün bölgesel olarak bölgedir. Bu durum, bölgesel bir hata durumunda Azure Resource Manager veya Azure portal aracılığıyla kümeyi yönetme özelliğini kaybedebileceğinizi gösterir. Bu durum, küme çalışır durumda kalmaya devam edebilir ve doğrudan etkileşime girebileceksiniz. Ayrıca, Azure bugün bölgeler arasında kullanılabilen tek bir sanal ağa sahip olmanın yanı sıra bu özelliği de sunar. Bu, Azure 'daki çok bölgeli bir kümenin, VM Ölçek kümelerinde veya [Azure VPN ağ geçitlerinde](../vpn-gateway/vpn-gateway-about-vpngateways.md) [her VM için genel IP adresleri](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) gerektirdiği anlamına gelir. Bu ağ seçimleri, maliyet, performans ve bazı derece uygulama tasarımında farklı etkileri vardır. bu nedenle, bu tür bir ortam olmadan önce dikkatli bir analiz ve planlama gereklidir.
2. Bu makinelerin bakımı, yönetimi ve izlenmesi, özellikle farklı bulut sağlayıcıları veya şirket içi kaynaklar ile Azure arasında gibi ortam _türlerine_ yayıldığınızda karmaşık hale gelebilir. Yükseltmeler, izleme, yönetim ve tanılamayı, bu tür bir ortamda üretim iş yüklerini çalıştırmadan önce hem küme hem de uygulamalar için anlaşılabilmesini sağlamak için dikkatli olunması gerekir. Bu sorunları Azure 'da veya kendi veri merkezlerinizde çözmenizde deneyimleriniz zaten varsa, Service Fabric kümenizi oluştururken veya çalıştırırken aynı çözümlerin uygulanması olasıdır. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Service Fabric düğümleri işletim sistemi güncelleştirmelerini otomatik olarak alır mi?

[Sanal makine ölçek kümesi otomatik işletim sistemi görüntüsü güncelleştirme](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) genel olarak kullanılabilir özelliğini bugün kullanabilirsiniz.

Azure 'da çalıştırılmayan kümeler için, Service Fabric düğümlerinizin altında işletim sistemlerinin yaması için [bir uygulama sunuyoruz](service-fabric-patch-orchestration-application.md) .

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>SF kümemdeki büyük sanal makine ölçek kümelerini kullanabilir miyim? 

**Kısa yanıt** -Hayır. 

**Uzun yanıt** -büyük sanal makine ölçek kümeleri, bir sanal makine ÖLÇEĞINI 1000 VM örneğine kadar ölçeklendirmenize izin verse de, bu, yerleştirme gruplarının (PG 'ler) kullanılmasını sağlar. Hata etki alanları (FDs) ve yükseltme etki alanları (UDs) yalnızca bir yerleştirme grubu hizmeti dokusunda tutarlıdır, hizmet çoğaltmalarınızın/hizmet örneklerinizin yerleştirme kararları vermek için FDs ve UDs kullanır. FDs ve UDs 'ler yalnızca bir yerleştirme grubu içinde karşılaştırılabilir olduğundan, SF bunu kullanamaz. Örneğin, PG1 'ın içindeki VM1 'in bir FD = 0 topolojisi varsa ve PG2 içindeki VM9, FD = 4 topolojisine sahipse, bu, VM1 ve VM2 iki farklı donanım rafın üzerinde olduğu anlamına gelmez, bu nedenle BT kararları almak için bu durumda SF değerlerini kullanamaz.

Şu anda büyük sanal makine ölçek kümelerinde, düzey 4 Yük Dengeleme desteğinin bulunmaması gibi diğer sorunlar vardır. [Büyük ölçekli kümeler hakkında ayrıntılı bilgi](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) için bkz.



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Service Fabric kümesinin en küçük boyutu nedir? Neden daha küçük olamaz?

Üretim iş yüklerini çalıştıran bir Service Fabric kümesi için desteklenen minimum boyut beş düğümünüz. Geliştirme senaryolarında, tek bir düğüm (Visual Studio 'da hızlı geliştirme deneyimi için iyileştirilmiş) ve beş düğüm kümesi destekliyoruz.

Aşağıdaki üç nedenden dolayı bir üretim kümesinin en az 5 düğüme sahip olması gerekir:
1. Hiçbir Kullanıcı Hizmeti çalıştırılmasa bile, Service Fabric küme, adlandırma hizmeti ve yük devretme Yöneticisi hizmeti de dahil olmak üzere durum bilgisi olan bir sistem hizmetleri kümesi çalıştırır. Bu sistem hizmetleri, kümenin çalışır durumda kalması için gereklidir.
2. Her zaman düğüm başına bir hizmetin çoğaltmasını yerleştirdik, bu nedenle küme boyutu bir hizmetin (gerçekte bir bölümün) sahip olduğu kopyaların sayısı için üst sınırdır.
3. Bir küme yükseltmesi en az bir düğüm getirecek olduğundan, en az bir düğüm için bir arabelleğe sahip olmak istiyoruz, bu nedenle bir üretim kümesinin en düşük düzeyde *ek* olarak en az iki düğüme sahip olmasını istiyoruz. En az, aşağıda açıklandığı gibi bir sistem hizmetinin çekirdek boyutudur.  

Kümenin iki düğümden oluşan eşzamanlı hata durumunda kullanılabilmesini istiyoruz. Service Fabric kümesinin kullanılabilir olması için sistem hizmetlerinin kullanılabilir olması gerekir. Adlandırma hizmeti ve yük devretme Yöneticisi hizmeti gibi durum bilgisi olan sistem hizmetleri, kümeye hangi hizmetlerin dağıtıldığını ve şu anda barındırıldığını izleyen, güçlü tutarlılığa bağlıdır. Bu güçlü tutarlılık, buna karşılık, belirtilen bir hizmet için bir çekirdeğin bir katı büyük çoğunluğunu (N/2 + 1) temsil ettiği bu hizmetlerin durumuna bir *çekirdek* alma özelliğine bağlıdır. Bu nedenle, iki düğümün eşzamanlı kaybına karşı dayanıklı olması (Bu nedenle, bir sistem hizmetinin iki çoğaltmasının eşzamanlı kaybolması) için, en düşük boyutu beş olarak zorlayan ClusterSize-QuorumSize > = 2 olması gerekir. Bunu görmek için, kümenin N düğümü olduğunu ve her düğümde bir sistem hizmetinin N çoğaltmaları olduğunu düşünün. Bir sistem hizmeti için çekirdek boyutu (N/2 + 1). Yukarıdaki eşitsizlik, N-(N/2 + 1) > = 2 gibi görünüyor. Göz önünde bulundurulması gereken iki durum vardır: N çift olduğunda ve N tekse. N çiftse, N = 2 \*m burada m > = 1 olduğunda, eşitsizlik 2 \*m (2 \*m/2 + 1) > = 2 veya m > = 3 gibi görünür. N için en az 6 ve bu, e = 3 olduğunda elde edilir. Öte yandan, N tek ise, n = 2 \*m + 1 (m > = 1) eşitsizse, eşitsizlik 2 \*m + 1-((2 \*m + 1)/2 + 1) > = 2 veya 2 \*m + 1-(m + 1) > = 2 veya m > = 2 olduğunu söylüyor. N için en düşük değer 5 ' tir ve bu, a = 2 olduğunda elde edilir. Bu nedenle, eşitsizlik ClusterSize ' > = 2 ' yi karşılayan tüm N değerleri arasında en az 5 ' tir.

Yukarıdaki bağımsız değişkende, her düğümün bir sistem hizmeti kopyası olduğunu varsaydık, bu nedenle çekirdek boyutu kümedeki düğümlerin sayısına göre hesaplanır. Ancak, *Targetreplicasetsize* öğesini değiştirerek çekirdek boyutunu (N/2 + 1), bu durum 5 düğümden daha küçük bir küme olabilecek ve yine de çekirdek boyutunun üzerinde 2 ek düğüm bulunan bir duruma sahip olduğumuz izlenimi verebilir. Örneğin, 4 düğümlü bir kümede TargetReplicaSetSize öğesini 3 olarak ayarlarsanız, TargetReplicaSetSize 'yi temel alan çekirdek boyutu (3/2 + 1) veya 2 ' dir. bu nedenle, ClusterSize-QuorumSize = 4-2 > = 2 ' dir. Bununla birlikte, aynı anda herhangi bir çift düğüm kaybederseniz sistem hizmetinin çekirdeği üzerinde veya üzerinde olacağını garanti edemeyiz, kaybedildiğimiz iki düğüm iki çoğaltma barındırıyor olabilir, bu nedenle sistem hizmeti bir çekirdek kaybına (yalnızca bir çoğaltma sola sahip) geçer ND kullanılamaz olacak.

Bu arka planda, bazı olası küme yapılandırmalarının incelim:

Tek **düğüm**: Bu seçenek, herhangi bir nedenden dolayı tek bir düğümün kaybedilmesi tüm kümenin kaybedilmesinden dolayı yüksek kullanılabilirlik sağlamaz.

**İki düğüm**: iki düğüm arasında dağıtılan bir hizmetin çekirdeği (N = 2) 2 ' dir (2/2 + 1 = 2). Tek bir çoğaltma kaybedilmişse, çekirdek oluşturmak olanaksızdır. Hizmet yükseltmesi gerçekleştirmede bir çoğaltmanın geçici olarak alınması gerektiğinden, bu kullanışlı bir yapılandırma değildir.

**Üç düğüm**: üç düğüm (N = 3) ile bir çekirdek oluşturma gereksinimi yine iki düğüm olur (3/2 + 1 = 2). Bu, tek bir düğümü kaybedebilmeniz ve yine de çekirdeği koruyabilmeniz, ancak iki düğümden oluşan eşzamanlı başarısızlığın sistem hizmetlerini çekirdek kaybına ayıracağı ve kümenin kullanılamaz hale gelmesine neden olacağı anlamına gelir.

**Dört**düğüm: dört düğüm (N = 4) ile bir çekirdek oluşturma gereksinimi üç düğümtür (4/2 + 1 = 3). Bu, tek bir düğümü kaybedebilmeniz ve yine de çekirdeği koruyabilmeniz, ancak iki düğümden oluşan eşzamanlı başarısızlığın sistem hizmetlerini çekirdek kaybına ayıracağı ve kümenin kullanılamaz hale gelmesine neden olacağı anlamına gelir.

**Beş düğüm**: beş düğüm (N = 5) ile bir çekirdek oluşturma gereksinimi hala üç düğüm olur (5/2 + 1 = 3). Bu, aynı anda iki düğümü kaybedebilmeniz ve sistem hizmetleri için çekirdeği sürdürmenize yol açabilir.

Üretim iş yükleri için, en az iki düğüm için (örneğin, diğer nedenlerden dolayı bir tane olmak üzere küme yükseltmesi nedeniyle) eş zamanlı hata arızası olması gerekir. bu nedenle beş düğüm gerekir.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Maliyetleri kaydetmek için kümi gece/hafta sonları üzerinden kapatabilir miyim?

Genel, hayır. Service Fabric durumu yerel, kısa ömürlü disklerde depolar, yani sanal makine farklı bir konağa taşınırsa, verilerin onunla birlikte hareket etmez. Normal işlemde, yeni düğüm diğer düğümlere göre güncel hale getirilmediğinden bu bir sorun değildir. Ancak, tüm düğümleri durdurup daha sonra yeniden başlatırsanız, düğümlerin çoğunun yeni konaklarda başlaması ve sistemin kurtulamadığında önemli bir olasılık vardır.

Uygulamanızı dağıtılmadan önce test etmek üzere kümeler oluşturmak isterseniz, bu kümeleri [sürekli tümleştirme/sürekli dağıtım işlem hattının](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)bir parçası olarak dinamik olarak oluşturmanızı öneririz.


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Işletim sistemmi (örneğin, Windows Server 2012 ' den Windows Server 2016 ' e) yükseltmek Nasıl yaparım??

Günümüzde, geliştirilmiş bir deneyim üzerinde çalışıyoruz, bu da yükseltmeden sorumludur. İşletim sistemi görüntüsünü aynı anda tek bir VM 'nin sanal makinelerinde yükseltmeniz gerekir. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Bir küme düğümü türündeki (sanal makine ölçek kümesi) bağlı veri disklerini şifreleyebilir miyim?
Evet.  Daha fazla bilgi için bkz. [sanal makine ölçek kümeleri için](../virtual-machine-scale-sets/disk-encryption-overview.md)bağlı veri diskleri ve Azure disk şifrelemesi [ile küme oluşturma](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) .

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Düşük öncelikli VM 'Leri bir küme düğümü türünde (sanal makine ölçek kümesi) kullanabilir miyim?
Hayır. Düşük öncelikli VM 'Ler desteklenmez. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Kümemdeki virüsten koruma programını çalıştırırken dışlandığım dizin ve süreçler nelerdir?

| **Virüsten koruma için dışlanan dizinler** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (küme yapılandırmasından) |
| FabricLogRoot (küme yapılandırmasından) |

| **Virüsten koruma hariç tutulan süreçler** |
| --- |
| Fabric. exe |
| FabricHost. exe |
| Fabricınstallerservice. exe |
| FabricSetup. exe |
| FabricDeployer. exe |
| Imagebuilder. exe |
| FabricGateway. exe |
| FabricDCA. exe |
| FabricFAS. exe |
| FabricUOS. exe |
| FabricRM. exe |
| FileStoreService. exe |
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Uygulamamın gizli dizileri almak için Keykasada kimlik doğrulaması nasıl yapılır?
Aşağıdakiler, uygulamanızın Keykasada kimlik doğrulaması için kimlik bilgilerini alması anlamına gelir:

A. Uygulamalarınızın derleme/paketleme işi sırasında, SF uygulamanızın veri paketine bir sertifika çekebilir ve bunu anahtar kasasında kimlik doğrulaması yapmak için kullanabilirsiniz.
B. Sanal makine ölçek kümesi MSI etkin konaklar için, [MSI uç noktasından bir erişim belirteci](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)almak üzere SF uygulamanız için basit bir PowerShell setupentrypoint geliştirebilir ve sonra [gizli dizileri keykasasından alabilirsiniz](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Uygulama tasarımı

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Güvenilir bir koleksiyonun bölümleri genelinde verileri sorgulamak için en iyi yol nedir?

Güvenilir koleksiyonlar genellikle daha fazla performans ve verimlilik için ölçeklendirmeyi etkinleştirmek üzere [bölümlenir](service-fabric-concepts-partitioning.md) . Bu, belirli bir hizmetin durumunun on binlerce veya yüzlerce makineye yayılabilen anlamına gelir. Bu tam veri kümesi üzerinde işlem gerçekleştirmek için birkaç seçeneğiniz vardır:

- Gerekli verileri çekmek için başka bir hizmetin tüm bölümlerini sorgulayan bir hizmet oluşturun.
- Başka bir hizmetin tüm bölümlerinden veri alabilen bir hizmet oluşturun.
- Her hizmetten gelen verileri bir dış depoya düzenli olarak gönderin. Bu yaklaşım yalnızca, gerçekleştirdiğiniz sorgular çekirdek iş mantığınızın bir parçası değilse uygundur.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Aktörlerde verileri sorgulamak için en iyi yol nedir?

Aktörler bağımsız durum ve işlem birimleri olacak şekilde tasarlanmıştır. bu nedenle, çalışma zamanında aktör durumunun geniş sorgularının yapılması önerilmez. Aktör durumunda tam olarak sorgu yapmanız gerekiyorsa, şunlardan birini göz önünde bulundurmanız gerekir:

- Aktör hizmetlerinizi, durum bilgisi olan güvenilir hizmetlerle değiştirme, bu sayede ağ sayısı her türlü verileri, hizmetinizdeki bölüm sayısına kadar tüm verileri toplamak üzere
- Aktörlerinizi, daha kolay sorgulamak için düzenli aralıklarla bir dış depoya göndermek için tasarlama. Yukarıdaki gibi, bu yaklaşım yalnızca, gerçekleştirdiğiniz sorgular çalışma zamanı davranışınız için gerekmiyorsa geçerlidir.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Güvenilir bir koleksiyonda ne kadar veri depolayabilirim?

Güvenilir hizmetler genellikle bölümlenebilir, bu nedenle depoladığınız miktar yalnızca kümedeki makine sayısıyla ve bu makinelerde kullanılabilir bellek miktarı ile sınırlıdır.

Örnek olarak, 100 bölümlü ve 3 çoğaltmada bir hizmette güvenilir bir koleksiyonunuz olduğunu ve 1 KB 'lik ortalama bir nesne boyutunu Artık makine başına 16 GB bellek içeren bir 10 makine kümeniz olduğunu varsayalım. Basitlik ve koruyucu için, işletim sistemi ve sistem hizmetleri, Service Fabric çalışma zamanı ve hizmetlerinizin 6 GB kullanmasını, makine başına 10 GB veya küme için 100 GB olduğunu varsayalım.

Her bir nesnenin üç kez depolanması gerektiğini aklınızda bulundurun (bir birincil ve iki çoğaltma), tam kapasiteyle çalışırken koleksiyonunuzda yaklaşık 35.000.000 nesne için yeterli belleğe sahip olursunuz. Bununla birlikte, bir hata etki alanının ve yükseltme etki alanının eş zamanlı kaybını dayanıklı bir şekilde kullanmanızı öneririz. Bu, 1/3 kapasitesini temsil eder ve sayıyı kabaca 23.000.000 olarak azaltır.

Bu hesaplamanın ayrıca şunları varsaydığını unutmayın:

- Verilerin bölümler arasında dağıtılması kabaca Tekdüzen olur veya yük ölçümlerini Küme Kaynak Yöneticisi olarak raporlıyoruz. Varsayılan olarak, Service Fabric çoğaltma sayısına göre dengeyi yükler. Yukarıdaki örnekte, kümedeki her düğüme 10 birincil çoğaltma ve 20 ikincil çoğaltma yerleştirdi. Bu, bölümler arasında eşit olarak dağıtılan yük için iyi bir sonuç verir. Yük bile olmasa da, Kaynak Yöneticisi daha küçük çoğaltmaları paket, daha büyük çoğaltmaların tek bir düğümde daha fazla bellek kullanmasına izin vermek için yükleme rapor etmeniz gerekir.

- Söz konusu güvenilir hizmetin kümedeki tek bir depolama durumu olduğu durumdur. Bir kümeye birden çok hizmet dağıtabileceğinizden, her birinin çalışması ve durumunu yönetmesi gereken kaynakların en az olması gerekir.

- Kümenin kendisinin büyümesi veya küçültülmediği. Daha fazla makine eklerseniz, tek bir çoğaltma makinelere yayılabileceğinden makine sayısı, hizmetinizdeki bölüm sayısını geçirene kadar çoğaltmalarınızı daha fazla kapasiteyi kullanacak şekilde yeniden dengeleyecek Service Fabric. Buna karşılık, makineleri kaldırarak kümenin boyutunu azaltmanız durumunda çoğaltmalar daha sıkı bir şekilde paketlenmiştir ve genel kapasiteye sahiptir.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Bir aktörde ne kadar veri depolayabilirim?

Güvenilir hizmetlerde olduğu gibi, bir aktör hizmetinde depolayabilmeniz gereken veri miktarı yalnızca kümenizdeki düğümlerde bulunan toplam disk alanı ve bellekle sınırlıdır. Ancak, tek tek aktörler, az miktarda durum ve ilişkili iş mantığını kapsüllemek için kullanıldıkları zaman en etkilidir. Genel bir kural olarak, tek bir aktör, kilobayt cinsinden ölçülen bir duruma sahip olmalıdır.

## <a name="other-questions"></a>Diğer sorular

### <a name="how-does-service-fabric-relate-to-containers"></a>Service Fabric kapsayıcılarla nasıl ilişkilidir?

Kapsayıcılar Hizmetleri ve bağımlılıklarını, her türlü ortamda tutarlı bir şekilde çalıştıkları ve tek bir makinede yalıtılmış bir biçimde çalışabilecek şekilde paketlemeyi sağlayan basit bir yol sunar. Service Fabric, [bir kapsayıcıda paketlenmiş hizmetler](service-fabric-containers-overview.md)de dahil olmak üzere hizmetleri dağıtmak ve yönetmek için bir yol sunar.

### <a name="are-you-planning-to-open-source-service-fabric"></a>Açık kaynak Service Fabric mı planlıyorsunuz?

GitHub ve kabul etme sırasında Service Fabric ([güvenilir hizmetler çerçevesi](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [güvenilir aktör çerçevesi](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [ASP.NET Core tümleştirme KITAPLıKLARı](https://github.com/Azure/service-fabric-aspnetcore), [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer)ve [Service Fabric CLI](https://github.com/Azure/service-fabric-cli)) açık kaynaklı parçaları sunuyoruz Bu projelere topluluk katkıları. 

[Kısa süre önce](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) Service Fabric çalışma zamanını açık kaynak olarak planladığımızda duyuruyoruz. Bu noktada, Linux derleme ve test araçlarıyla GitHub 'da [Service Fabric](https://github.com/Microsoft/service-fabric/) , depoyu kopyalayabilir, Linux için Service Fabric derleyebilir, temel testleri çalıştırabilir, sorunları açabilir ve çekme istekleri gönderebilirsiniz. Windows Build ortamını, tüm CI ortamıyla birlikte geçiş yapmak için çok çalıştık.

Duyurulduğu gibi daha fazla ayrıntı için [Service Fabric blogunu](https://blogs.msdn.microsoft.com/azureservicefabric/) izleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Temel Service Fabric kavramları](service-fabric-technical-overview.md) ve [en iyi uygulamalar](service-fabric-best-practices-overview.md) (Service-Fabric-Technical-Overview.MD) ve [en iyi yöntemler](service-fabric-best-practices-overview.md) hakkında bilgi edinin
