---
title: Kavramlar-Azure Kubernetes hizmetlerinde güvenlik (AKS)
description: Ana ve düğüm iletişimi, ağ ilkeleri ve Kubernetes gizli dizileri dahil olmak üzere Azure Kubernetes hizmeti 'nde (AKS) güvenlik hakkında bilgi edinin.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: mlearned
ms.openlocfilehash: 3fafbe3f4b1c53f929682f4ca160fb19a5e91918
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105315"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service’teki (AKS) uygulamalar ve kümeler için güvenlik kavramları

Küme güvenliği, Azure Kubernetes Service (AKS) içinde uygulama iş yüklerini çalıştırırken müşteri verilerinizi korur. 

Kubernetes, *ağ ilkeleri* ve *gizli* dizileri gibi güvenlik bileşenleri içerir. Bu arada, Azure ağ güvenlik grupları gibi bileşenler içerir ve küme yükseltmelerini organize eder. AKS bu güvenlik bileşenlerini şu şekilde birleştirir:
* AKS kümenizi en son işletim sistemi güvenlik güncelleştirmeleri ve Kubernetes yayınlarını çalıştırmaya devam edin.
* Güvenli Pod trafiği sağlayın ve hassas kimlik bilgilerine erişin.

Bu makalede, AKS 'deki uygulamalarınızın güvenliğini sağlayan temel kavramlar tanıtılmaktadır:

- [Azure Kubernetes Service’teki (AKS) uygulamalar ve kümeler için güvenlik kavramları](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Ana güvenlik](#master-security)
  - [Düğüm güvenliği](#node-security)
    - [İşlem yalıtımı](#compute-isolation)
  - [Küme yükseltmeleri](#cluster-upgrades)
    - [Cordon ve boşalt](#cordon-and-drain)
  - [Ağ güvenliği](#network-security)
    - [Azure ağ güvenlik grupları](#azure-network-security-groups)
  - [Kubernetes gizli dizileri](#kubernetes-secrets)
  - [Sonraki adımlar](#next-steps)

## <a name="master-security"></a>Ana güvenlik

AKS 'de Kubernetes ana bileşenleri, Microsoft tarafından sağlanan, yönetilen ve korunan yönetilen hizmetin bir parçasıdır. Her bir AKS kümesi, API sunucusu, Zamanlayıcı vb. sağlamak için kendi tek kiracılı, adanmış Kubernetes ana öğesine sahiptir.

Varsayılan olarak, Kubernetes API sunucusu genel bir IP adresi ve tam etki alanı adı (FQDN) kullanır. [Yetkılı IP aralıklarını][authorized-ip-ranges]kullanarak API sunucusu uç noktasına erişimi sınırlayabilirsiniz. Ayrıca, API sunucusu erişimini sanal ağınıza sınırlamak için tam bir [özel küme][private-clusters] da oluşturabilirsiniz.

Kubernetes rol tabanlı erişim denetimi (Kubernetes RBAC) ve Azure RBAC kullanarak API sunucusuna erişimi denetleyebilirsiniz. Daha fazla bilgi için bkz. [AKS Ile Azure AD tümleştirmesi][aks-aad].

## <a name="node-security"></a>Düğüm güvenliği

AKS düğümleri, yönettiğiniz ve tuttuğunuz Azure sanal makinelerdir (VM). 
* Linux düğümleri `containerd` veya Moby kapsayıcı çalışma zamanını kullanarak iyileştirilmiş bir Ubuntu dağıtımı çalıştırır. 
* Windows Server düğümleri `containerd` veya Moby kapsayıcı çalışma zamanını kullanarak iyileştirilmiş bir Windows Server 2019 sürümü çalıştırır. 

Bir AKS kümesi oluşturulduğunda veya ölçeklendirildiğinde, düğümler en son işletim sistemi güvenlik güncelleştirmeleri ve yapılandırmalarına göre otomatik olarak dağıtılır.

> [!NOTE]
> Kullanarak AKS kümeleri:
> * Kubernetes sürüm 1,19 düğüm havuzları ve `containerd` kap çalışma zamanı olarak daha büyük kullanım. 
> * V 1.19 node havuzlarından önceki Kubernetes, kapsayıcı çalışma zamanı olarak [Moby](https://mobyproject.org/) (yukarı akış Docker) kullanır.

### <a name="node-security-patches"></a>Düğüm güvenliği düzeltme ekleri

#### <a name="linux-nodes"></a>Linux düğümleri
Azure platformu, gece temelinde Linux düğümlerine işletim sistemi güvenlik düzeltme eklerini otomatik olarak uygular. Bir Linux işletim sistemi güvenlik güncelleştirmesi konağın yeniden başlatılmasını gerektiriyorsa, otomatik olarak yeniden başlatılır. Şunlardan birini yapabilirsiniz:
* Linux düğümlerini el ile yeniden başlatın.
* Kubernetes için açık kaynaklı bir yeniden başlatma cini olan [Kured][kured]'yi kullanın. Kured, bir [DaemonSet][aks-daemonsets] olarak çalışır ve her düğümü, bir yeniden başlatmanın gerekli olduğunu belirten bir dosya için izler. 

Yeniden başlatmalar, küme yükseltmesi olarak aynı [Cordon ve boşalt işlemini](#cordon-and-drain) kullanarak küme genelinde yönetilir.

#### <a name="windows-server-nodes"></a>Windows Server düğümleri

Windows Server düğümleri için Windows Update otomatik olarak çalıştırılmaz ve en son güncelleştirmeleri uygulamaz. AKS kümenizde, düzenli Windows Update yayın döngüsünün ve kendi doğrulama sürecinizin etrafındaki Windows Server düğüm havuzu yükseltmelerini zamanlayın. Bu yükseltme işlemi, en son Windows Server görüntüsünü ve düzeltme eklerini çalıştıran düğümleri oluşturur, daha sonra eski düğümleri kaldırır. Bu işlemle ilgili daha fazla bilgi için bkz. [AKS 'de düğüm havuzunu yükseltme][nodepool-upgrade].

### <a name="node-deployment"></a>Düğüm dağıtımı
Düğümler, genel IP adresleri atanmadan özel bir sanal ağ alt ağına dağıtılır. Sorun giderme ve yönetim amaçlarıyla, SSH varsayılan olarak etkindir ve yalnızca iç IP adresi kullanılarak erişilebilir.

### <a name="node-storage"></a>Düğüm depolaması
Depolama sağlamak için düğümler Azure yönetilen diskleri kullanır. Azure yönetilen diskler çoğu VM düğüm boyutu için yüksek performanslı SSD 'Ler tarafından desteklenen Premium disklerdir. Yönetilen disklerde depolanan veriler, Azure platformunda Rest 'de otomatik olarak şifrelenir. Artıklığı artırmak için Azure yönetilen diskler Azure veri merkezinde güvenli bir şekilde çoğaltılır.

### <a name="hostile-multi-tenant-workloads"></a>Kötü amaçlı çok kiracılı iş yükleri

Şu anda, Kubernetes ortamları, çok kiracılı Kullanıcı kullanımı için güvenli değildir. Düğümler için *Pod güvenlik ilkeleri* veya Kubernetes RBAC gibi ek güvenlik özellikleri, etkin bir şekilde blok yararlanma. Saldırgan çok kiracılı iş yüklerini çalıştırırken doğru güvenlik için yalnızca bir hiper yöneticiye güvenin. Kubernetes güvenlik etki alanı, tek bir düğüm değil, tüm küme haline gelir. 

Bu tür çok kiracılı iş yükleri için, fiziksel olarak yalıtılmış kümeler kullanmanız gerekir. İş yüklerini yalıtma yolları hakkında daha fazla bilgi için bkz. [AKS 'de küme yalıtımı Için en iyi uygulamalar][cluster-isolation].

### <a name="compute-isolation"></a>İşlem yalıtımı

Uyumluluk veya yasal gereksinimler nedeniyle, bazı iş yükleri diğer müşteri iş yüklerinden yüksek ölçüde yalıtım gerektirebilir. Azure, bu iş yükleri için bir AKS kümesinde aracı düğümleri olarak kullanılacak [yalıtılmış VM 'ler](../virtual-machines/isolation.md) sağlar. Bu VM 'Ler belirli bir donanım türüne ayrılmıştır ve tek bir müşteriye atanır. 

Bir AKS kümesi oluştururken veya bir düğüm havuzu eklerken, **düğüm boyutu** olarak [yalıtılmış VM boyutlarından birini](../virtual-machines/isolation.md) seçin.

## <a name="cluster-upgrades"></a>Küme yükseltmeleri

Azure, bir AKS kümesini ve bileşenlerini yükseltmek, güvenlik ve uyumluluğu korumak ve en son özelliklere erişmek için yükseltme düzenleme araçları sağlar. Bu yükseltme düzenlemesi hem Kubernetes Master hem de Agent bileşenlerini içerir. 

Yükseltme işlemini başlatmak için, [listelenen mevcut Kubernetes sürümlerinden](supported-kubernetes-versions.md)birini belirtin. Azure daha sonra her bir AKS düğümünü ve yükseltmelerini güvenli bir şekilde korleştirir ve boşaltır.

### <a name="cordon-and-drain"></a>Cordon ve boşalt

Yükseltme işlemi sırasında, yeni yığınların üzerinde zamanlanmasını engellemek için AKS düğümleri kümeden tek tek bir şekilde ele alınır. Düğümler daha sonra aşağıdaki şekilde dağıtılır ve yükseltilir:

1.  Düğüm havuzuna yeni bir düğüm dağıtılır. 
    * Bu düğüm, en son işletim sistemi görüntüsünü ve düzeltme eklerini çalıştırır.
1. Mevcut düğümlerden biri yükseltme için tanımlandı. 
1. Tanımlanan düğümdeki düğüm, normal şekilde sonlandırılır ve düğüm havuzundaki diğer düğümlerde zamanlanır.
1. Boşaltılan düğüm AKS kümesinden silinir.
1. Adımlar 1-4, tüm düğümler yükseltme işleminin bir parçası olarak başarıyla değiştirilene kadar yinelenir.

Daha fazla bilgi için bkz. [AKS kümesini yükseltme][aks-upgrade-cluster].

## <a name="network-security"></a>Ağ güvenliği

Şirket içi ağlarla bağlantı ve güvenlik için, AKS kümenizi mevcut Azure sanal ağ alt ağlarına dağıtabilirsiniz. Bu sanal ağlar, Azure siteden siteye VPN veya Express Route kullanarak şirket içi ağınıza geri bağlanır. Hizmetlerin iç ağ bağlantısına erişimini sınırlandırmak için özel, iç IP adresleriyle Kubernetes giriş denetleyicilerini tanımlayın.

### <a name="azure-network-security-groups"></a>Azure ağ güvenlik grupları

Azure, sanal ağ trafiği akışını filtrelemek için ağ güvenlik grubu kurallarını kullanır. Bu kurallar, kaynaklara erişim izni verilen veya reddedilen kaynak ve hedef IP aralıklarını, bağlantı noktalarını ve protokolleri tanımlar. Kubernetes API sunucusuna TLS trafiğine izin vermek için varsayılan kurallar oluşturulur. Yük dengeleyiciler, bağlantı noktası eşleştirmeleri veya giriş rotaları ile hizmetler oluşturursunuz. AKS, trafik akışı için ağ güvenlik grubunu otomatik olarak değiştirir.

AKS kümeniz için kendi alt ağınızı sağlarsanız, AKS tarafından yönetilen alt ağ düzeyinde ağ güvenlik **grubunu değiştirmeyin.** Bunun yerine, trafik akışını değiştirmek için daha fazla alt ağ düzeyi ağ güvenlik grubu oluşturun. Yük dengeleyici erişimi, denetim düzleğiyle iletişim ve [Çıkış][aks-limit-egress-traffic]gibi, kümeyi yöneten gerekli trafikle müdahale olmadığından emin olun.

### <a name="kubernetes-network-policy"></a>Kubernetes ağ ilkesi

Kümenizdeki düğüm arasındaki ağ trafiğini sınırlandırmak için AKS 'ler, [Kubernetes ağ ilkeleri][network-policy]için destek sunar. Ağ ilkeleriyle, ad alanları ve etiket seçicileri temelinde küme içindeki belirli ağ yollarına izin verebilir veya bu yollara izin verebilirsiniz.

## <a name="kubernetes-secrets"></a>Kubernetes Gizli Dizileri

Kubernetes *gizli* dizisi ile hassas verileri, erişim kimlik bilgileri veya anahtarlar gibi bölümlere ayırır. 
1. Kubernetes API 'sini kullanarak bir gizli dizi oluşturun. 
1. Pod veya dağıtımınızı tanımlayın ve belirli bir gizli anahtar isteyin. 
    * Gizli diziler yalnızca, zamanlanmış Pod olan düğümlere sağlanır.
    * Gizli anahtar, diske yazılmadı ve *tmpfs*'de depolanır. 
1. Gizli dizi gerektiren bir düğümdeki son Pod 'yi sildiğinizde, gizli dizi, düğümün tmpfs 'den silinir. 
   * Gizli diziler belirli bir ad alanı içinde depolanır ve yalnızca aynı ad alanında yer alan Pod tarafından erişilebilir.

Gizli dizi kullanımı, Pod veya hizmet YAML bildiriminde tanımlanan hassas bilgileri azaltır. Bunun yerine, Kubernetes API sunucusunda depolanan gizli anahtarı YAML bildiriminizde bir parçası olarak istemeniz gerekir. Bu yaklaşım yalnızca gizli dizi için özel Pod erişimi sağlar. 

> [!NOTE]
> Ham gizli dizi bildirim dosyaları, Base64 biçimindeki gizli verileri içerir (daha fazla ayrıntı için [resmi belgelere][secret-risks] bakın). Bu dosyaları hassas bilgiler olarak değerlendirin ve bunları hiçbir şekilde kaynak denetimine kaydedin.

Kubernetes gizli dizileri, dağıtılmış anahtar-değer deposu olarak etcd 'de depolanır. Etcd deposu AKS tarafından tam olarak yönetilir ve [veriler Azure platformunda geri kalanında şifrelenir][encryption-atrest]. 

## <a name="next-steps"></a>Sonraki adımlar

AKS kümelerinizi korumaya başlamak için bkz. [aks kümesini yükseltme][aks-upgrade-cluster].

İlişkili en iyi uygulamalar için bkz. [AKS 'de küme güvenliği ve yükseltmeleri Için en iyi uygulamalar][operator-best-practices-cluster-security] ve [aks 'de Pod güvenliği için en iyi uygulamalar][developer-best-practices-pod-security].

Temel Kubernetes ve AKS kavramları hakkında daha fazla bilgi için bkz.:

- [Kubernetes/AKS kümeleri ve iş yükleri][aks-concepts-clusters-workloads]
- [Kubernetes/AKS kimliği][aks-concepts-identity]
- [Kubernetes/AKS sanal ağları][aks-concepts-network]
- [Kubernetes/AKS depolaması][aks-concepts-storage]
- [Kubernetes/AKS ölçeği][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
