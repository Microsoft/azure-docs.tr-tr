---
title: Windows Server ve Linux 'ta küme oluşturma
description: Service Fabric kümeler Windows Server ve Linux üzerinde çalışır. Windows Server veya Linux 'un çalıştırılacağı her yerde Service Fabric uygulamaları dağıtabilir ve barındırabilirsiniz.
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 4aed4ab38db9f8d8b95647b6662245c93778afed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520165"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Azure 'da Service Fabric kümelerine genel bakış
Service Fabric küme, mikro hizmetlerinizin dağıtıldığı ve yönetildiği, ağa bağlı bir sanal veya fiziksel makine kümesidir. Bir kümenin parçası olan makineye veya VM 'ye küme düğümü denir. Kümeler, binlerce düğüme ölçeklendirebilir. Kümeye yeni düğümler eklerseniz, hizmet bölümü çoğaltmaları ve örneklerinin artan düğüm sayısı genelinde yeniden dengelenmesi Service Fabric. Genel uygulama performansı, bellek düşüşlerine erişim için gelişir ve çekişmeyi geliştirir. Kümedeki düğümler verimli bir şekilde kullanılmıyorsa, kümedeki düğümlerin sayısını azaltabilirsiniz. Service Fabric, her düğümdeki donanımın daha iyi kullanılmasını sağlamak için bölüm çoğaltmalarını ve örnekleri, azaltılmış düğüm sayısı genelinde yeniden dengeler.

Düğüm türü, kümedeki bir dizi düğüm (sanal makine) için boyut, sayı ve özellikleri tanımlar. Daha sonra, her düğüm türünün ölçeği birbirinden bağımsız olarak artırılabilir veya azaltılabilir, her düğüm türünde farklı bağlantı noktası kümeleri açık olabilir ve farklı kapasite ölçümleri yapılabilir. Düğüm türleri, bir düğüm kümesinin "ön uç" veya "arka uç" şeklindeki rolünün tanımlanması için kullanılır. Kümenizde birden çok düğüm türü olabilir, ancak üretim kümeleri için birincil düğüm türünde en az beş VM (veya test kümeleri için en az üç VM) olmalıdır. [Service Fabric sistem hizmetleri](service-fabric-technical-overview.md#system-services), birincil düğüm türündeki düğümlere yerleştirilir. 

## <a name="cluster-components-and-resources"></a>Küme bileşenleri ve kaynakları
Azure 'daki bir Service Fabric kümesi, diğer Azure kaynaklarıyla birlikte kullanılan ve etkileşimde bulunan bir Azure kaynağıdır:
* VM 'Ler ve sanal ağ kartları
* sanal anal makine ölçek kümeleri
* sanal ağlar
* yük dengeleyiciler
* depolama hesapları
* Genel IP adresleri

![Service Fabric kümesi][Image]

### <a name="virtual-machine"></a>Sanal makine
Bir kümenin parçası olan bir [sanal makine](../virtual-machines/index.yml) , teknik olarak bir küme düğümü olan bir Service Fabric çalışma zamanı işlemidir. Her düğüme bir düğüm adı (bir dize) atanır. Düğümlerin [yerleştirme özellikleri](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)gibi özellikleri vardır. Her makine veya VM, önyükleme zamanında çalışmaya başlayan *FabricHost.exe* bir otomatik başlatma hizmetine sahiptir ve sonra düğümü oluşturan *Fabric.exe* ve *FabricGateway.exe* iki yürütülebilir dosya başlatır. Bir üretim dağıtımı, fiziksel veya sanal makine başına bir düğümdür. Test senaryoları için birden fazla *Fabric.exe* ve *FabricGateway.exe* birden çok örneğini çalıştırarak tek bir makinede veya VM 'de birden çok düğümü barındırabilirsiniz.

Her VM bir sanal ağ arabirim kartı (NIC) ile ilişkilendirilir ve her NIC 'e özel bir IP adresi atanır.  Bir VM, NIC üzerinden bir sanal ağa ve yerel dengeleyiciye atanır.

Bir kümedeki tüm VM 'Ler bir sanal ağa yerleştirilir.  Aynı düğüm türü/ölçek kümesindeki tüm düğümler sanal ağ üzerinde aynı alt ağa yerleştirilir.  Bu düğümler yalnızca özel IP adreslerine sahiptir ve sanal ağ dışında doğrudan adreslenebilir değildir.  İstemciler, Azure yük dengeleyici üzerinden düğümlerdeki hizmetlere erişebilir.

### <a name="scale-setnode-type"></a>Ölçek kümesi/düğüm türü
Bir küme oluşturduğunuzda bir veya daha fazla düğüm türü tanımlarsınız.  Düğüm türündeki düğümler veya VM 'Ler, CPU sayısı, bellek, disk sayısı ve disk g/ç gibi aynı boyut ve özelliklere sahiptir.  Örneğin, bir düğüm türü, internet 'e açık bağlantı noktaları olan küçük, ön uç VM 'Ler için, başka bir düğüm türü ise verileri işleyen büyük, arka uç VM 'Ler için olabilir. Azure kümelerinde, her düğüm türü bir [sanal makine ölçek kümesi](../virtual-machine-scale-sets/index.yml)ile eşlenir.

Ölçek kümelerini, bir küme olarak bir sanal makine koleksiyonunu dağıtmak ve yönetmek için kullanabilirsiniz. Azure Service Fabric kümesinde tanımladığınız her düğüm türü ayrı bir ölçek kümesi ayarlar. Service Fabric Runtime, Azure VM uzantıları kullanılarak ölçek kümesindeki her bir sanal makineye önyüklendi. Her bir düğüm türünü bağımsız olarak yukarı veya aşağı ölçeklendirebilirsiniz, her küme düğümünde çalışan işletim sistemi SKU 'sunu değiştirebilir, farklı bağlantı noktası kümelerine açık olabilir ve farklı kapasite ölçümleri kullanabilirsiniz. Ölçek kümesinde beş [yükseltme etki alanı](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) ve beş [hata etki alanı](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) bulunur ve en fazla 100 VM olabilir.  Birden fazla ölçek kümesi/düğüm türü oluşturarak 100 ' den fazla düğüm kümesi oluşturabilirsiniz.

> [!IMPORTANT]
> Kümeniz için düğüm türlerinin sayısını ve her düğüm türünün özelliklerini (boyut, birincil, internet 'e bağlı, VM sayısı vb.) seçmek önemli bir görevdir.  Daha fazla bilgi için [küme kapasitesi planlama konularını](service-fabric-cluster-capacity.md)okuyun.

Daha fazla bilgi için [Service Fabric düğüm türlerini ve sanal makine ölçek kümelerini](service-fabric-cluster-nodetypes.md)okuyun.

### <a name="azure-load-balancer"></a>Azure Load Balancer
VM örnekleri, bir [genel IP adresi](../virtual-network/public-ip-addresses.md) ve DNS etiketiyle Ilişkilendirilen bir [Azure Yük dengeleyicinin](../load-balancer/load-balancer-overview.md)arkasına birleştirilir.  *&lt; Clustername &gt;*, DNS adı, clustername olan bir küme sağladığınızda *&lt; &gt; . &lt; Location &gt; . cloudapp.Azure.com* , ölçek kümesinin önünde yük dengeleyici Ile ilişkili DNS etiketidir.

Bir kümedeki sanal makinelerin yalnızca [özel IP adresleri](../virtual-network/private-ip-addresses.md)vardır.  Yönetim trafiği ve hizmet trafiği, herkese açık yük dengeleyici aracılığıyla yönlendirilir.  Ağ trafiği bu makinelere NAT kuralları aracılığıyla yönlendirilir (istemciler belirli düğümlere/örneklere bağlanır) veya yük dengeleme kurallarına (trafik, VM 'Lerin hepsini bir kez deneme) gider.  Yük dengeleyici, DNS adı şu biçimde olan ilişkili bir genel IP 'ye sahiptir: *&lt; clustername &gt; . &lt; Location &gt; . cloudapp.Azure.com*.  Genel IP, kaynak grubundaki başka bir Azure kaynağıdır.  Bir kümede birden çok düğüm türü tanımlarsanız, her düğüm türü/ölçek kümesi için bir yük dengeleyici oluşturulur. Veya, birden çok düğüm türü için tek bir yük dengeleyici ayarlayabilirsiniz.  Birincil düğüm türü, clustername DNS etiketine sahiptir *&lt; &gt; . &lt; Location &gt; . cloudapp.Azure.com*, diğer düğüm türlerinde ise DNS etiketi *&lt; clustername &gt; - &lt; NodeType vardır &gt; . &lt; Location &gt; . cloudapp.Azure.com*.

### <a name="storage-accounts"></a>Depolama hesapları
Her küme düğümü türü, bir [Azure depolama hesabı](../storage/common/storage-introduction.md) ve yönetilen diskler tarafından desteklenir.

## <a name="cluster-security"></a>Küme güvenliği
Service Fabric kümesi sahip olduğunuz bir kaynaktır.  Yetkisiz kullanıcıların bunlara bağlanmasını önlemeye yardımcı olmak için kümelerinizin güvenliğini sağlamak sizin sorumluluğunuzdadır. Küme üzerinde üretim iş yüklerini çalıştırırken güvenli bir küme özellikle önemlidir. 

### <a name="node-to-node-security"></a>Düğümden düğüme güvenlik
Düğümden düğüme güvenlik, bir kümedeki VM 'Ler veya bilgisayarlar arasındaki iletişimin güvenliğini sağlar. Bu güvenlik senaryosu, yalnızca kümeye katılma yetkisi olan bilgisayarların kümedeki uygulamaları ve Hizmetleri barındırmak için katılmasına olanak sağlar. Service Fabric, bir kümeyi güvenli hale getirmek ve uygulama güvenliği özellikleri sağlamak için X. 509.440 sertifikaları kullanır.  Küme trafiğinin güvenliğini sağlamak ve küme ve sunucu kimlik doğrulaması sağlamak için bir küme sertifikası gerekir.  Otomatik olarak imzalanan sertifikalar, test kümeleri için kullanılabilir, ancak üretim kümelerini güvenli hale getirmek için güvenilir bir sertifika yetkilisinden bir sertifika kullanılmalıdır.

Daha fazla bilgi için [düğümden düğüme güvenliği](service-fabric-cluster-security.md#node-to-node-security) okuyun

### <a name="client-to-node-security"></a>İstemciden düğüme güvenlik
İstemciden düğüme güvenlik, istemcilerin kimliğini doğrular ve kümedeki istemci ve tek düğümler arasındaki iletişimin güvenliğini sağlamaya yardımcı olur. Bu tür bir güvenlik, kümeye ve kümeye dağıtılan uygulamalara yalnızca yetkili kullanıcıların erişebildiğinden emin olmanıza yardımcı olur. İstemciler, X. 509.440 sertifika güvenlik kimlik bilgileri aracılığıyla benzersiz şekilde tanımlanır. Küme ile yönetici veya Kullanıcı istemcilerinin kimliğini doğrulamak için herhangi bir sayıda isteğe bağlı istemci sertifikası kullanılabilir.

İstemci sertifikalarına ek olarak, Azure Active Directory, küme ile istemcilerin kimliğini doğrulamak için de yapılandırılabilir.

Daha fazla bilgi için [istemciden düğüme güvenliği](service-fabric-cluster-security.md#client-to-node-security) okuyun

### <a name="role-based-access-control"></a>Rol tabanlı erişim denetimi
Azure rol tabanlı erişim denetimi (Azure RBAC), Azure kaynaklarına ayrıntılı erişim denetimleri atamanıza olanak tanır.  Abonelikler, kaynak grupları ve kaynaklara farklı erişim kuralları atayabilirsiniz.  Azure RBAC kuralları, daha düşük bir düzeyde geçersiz kılınmadıkça kaynak hiyerarşisi üzerinde devralınır.  Azure RBAC kuralları ile AAD 'nize herhangi bir kullanıcı veya Kullanıcı grubu atayabilirsiniz, böylece belirlenen kullanıcılar ve gruplar kümenizi değiştirebilir.  Daha fazla bilgi için [Azure RBAC genel bakış](../role-based-access-control/overview.md)makalesini okuyun.

Service Fabric Ayrıca, farklı Kullanıcı grupları için belirli küme işlemlerine erişimi sınırlamak üzere erişim denetimini destekler. Bu, kümenin daha güvenli olmasına yardımcı olur. Bir kümeye bağlanan istemciler için iki erişim denetimi türü desteklenir: yönetici rolü ve Kullanıcı rolü.  

Daha fazla bilgi için [Service Fabric rol tabanlı erişim denetimi](service-fabric-cluster-security.md#service-fabric-role-based-access-control)makalesini okuyun.

### <a name="network-security-groups"></a>Ağ güvenlik grupları 
Ağ güvenlik grupları (NSG 'ler) bir alt ağın, VM 'nin veya belirli bir NIC 'nin gelen ve giden trafiği denetler.  Varsayılan olarak, aynı sanal ağa birden fazla VM yerleştiriyorsa, herhangi bir bağlantı noktası üzerinden birbirleriyle iletişim kurabilir.  Makineler arasındaki iletişimleri kısıtlamak istiyorsanız, ağ kesimine veya VM 'Leri birbirinden ayırmak için NSG 'Ler tanımlayabilirsiniz.  Bir kümede birden çok düğüm türüne sahipseniz, farklı düğüm türlerine ait makinelerin birbirleriyle iletişim kurmasını engellemek için alt ağlara NSG 'ler uygulayabilirsiniz.  

Daha fazla bilgi için [güvenlik grupları](../virtual-network/network-security-groups-overview.md) hakkında bilgi edinin

## <a name="scaling"></a>Ölçeklendirme

Uygulama taleplerine zaman içinde değişiklik yapılır. Daha fazla uygulama iş yükünü veya ağ trafiğini karşılamak için küme kaynaklarını artırmanız veya talep düştüğünde küme kaynaklarını azaltmanız gerekebilir. Service Fabric kümesi oluşturduktan sonra, kümeyi yatay olarak ölçeklendirebilirsiniz (düğüm sayısını değiştirebilir) veya dikey (düğümlerin kaynaklarını değiştirebilirsiniz). Küme üzerinde iş yükleri çalışırken bile kümeyi istediğiniz zaman ölçeklendirebilirsiniz. Küme ölçeklenirken uygulamalarınız da otomatik olarak ölçeklendirilir.

Daha fazla bilgi için bkz. [Azure kümelerini ölçeklendirme](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Yükseltme
Azure Service Fabric kümesi, sahip olduğunuz ancak kısmen Microsoft tarafından yönetilen bir kaynaktır. Microsoft, temel alınan işletim sistemine düzeltme eki uygulama ve kümenizde Service Fabric çalışma zamanı yükseltmeleri gerçekleştirmekten sorumludur. Kümenizi otomatik çalışma zamanı yükseltmeleri alacak şekilde ayarlayabilir, Microsoft yeni bir sürüm yayınlar veya istediğiniz desteklenen bir çalışma zamanı sürümünü seçmenizi sağlar. Çalışma zamanı yükseltmelerine ek olarak, sertifikalar veya uygulama bağlantı noktaları gibi küme yapılandırmasını da güncelleştirebilirsiniz.

Daha fazla bilgi için, [kümeleri yükseltme](service-fabric-cluster-upgrade.md)makalesini okuyun.

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri
Lütfen daha fazla bilgi için bkz. [Azure 'Da desteklenen sürümler](./service-fabric-versions.md)


## <a name="next-steps"></a>Sonraki adımlar
Azure kümelerinin [güvenliğini sağlama](service-fabric-cluster-security.md), [ölçeklendirme](service-fabric-cluster-scaling.md)ve [yükseltme](service-fabric-cluster-upgrade.md) hakkında daha fazla bilgi edinin.

[Service Fabric destek seçenekleri](service-fabric-support.md)hakkında bilgi edinin.

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG