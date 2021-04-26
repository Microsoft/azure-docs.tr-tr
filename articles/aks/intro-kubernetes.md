---
title: Azure Kubernetes Service'e Giriş
description: Azure’da kapsayıcı tabanlı uygulamaları dağıtmak ve yönetmek için Azure Kubernetes Hizmetinin özelliklerini ve avantajlarını öğrenin.
services: container-service
ms.topic: overview
ms.date: 02/24/2021
ms.custom: mvc
ms.openlocfilehash: 1cddd39d0b95e021478235fcdafbacd40eb4097c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105264"
---
# <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes hizmeti (AKS), Azure 'da işlem yükünü boşaltarak Azure 'da yönetilen bir Kubernetes kümesinin dağıtılmasını basitleştirir. Barındırılan bir Kubernetes hizmeti olarak Azure, sistem durumu izleme ve bakım gibi kritik görevleri işler. Kubernetes yöneticileri Azure tarafından yönetildiğinden yalnızca aracı düğümlerini yönetebilir ve bakımını yapabilirsiniz. Bu nedenle AKS ücretsizdir; ana bilgisayarlar için değil, yalnızca kümelerinizdeki aracı düğümleri için ödeme yaparsınız.  

Şunu kullanarak bir AKS kümesi oluşturabilirsiniz:
* [Azure CLI](kubernetes-walkthrough.md)
* [Azure portal](kubernetes-walkthrough-portal.md)
* [Azure PowerShell](kubernetes-walkthrough-powershell.md)
* [Azure Resource Manager şablonları](kubernetes-walkthrough-rm-template.md) ve Teraform gibi şablon odaklı dağıtım seçeneklerini kullanma 

Bir AKS kümesini dağıttığınızda ana düğümler ve diğer tüm Kubernetes düğümleri sizin yerinize dağıtılır ve yapılandırılır. Gelişmiş ağ, Azure Active Directory (Azure AD) tümleştirmesi, izleme ve diğer özellikler dağıtım işlemi sırasında yapılandırılabilir. 

Kubernetes temelleri hakkında daha fazla bilgi için bkz. [AKS Için Kubernetes temel kavramları][concepts-clusters-workloads].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]
> AKS ayrıca Windows Server kapsayıcıları destekler.

## <a name="access-security-and-monitoring"></a>Erişim, güvenlik ve izleme

Gelişmiş güvenlik ve yönetim için, AKS 'ler Azure AD ile tümleştirmenize olanak tanır:
* Kubernetes rol tabanlı erişim denetimini (Kubernetes RBAC) kullanın. 
* Kümenizin ve kaynaklarınızın sistem durumunu izleyin.

### <a name="identity-and-security-management"></a>Kimlik ve güvenlik yönetimi

#### <a name="kubernetes-rbac"></a>Kubernetes RBAC

Küme kaynaklarına erişimi sınırlandırmak için AKS, [Kubernetes RBAC][kubernetes-rbac]'yi destekler. Kubernetes RBAC, Kubernetes kaynakları ve ad alanları için erişimi ve izinleri denetler.  

#### <a name="azure-ad"></a>Azure AD

Bir AKS kümesini Azure AD ile tümleştirilecek şekilde yapılandırabilirsiniz. Azure AD tümleştirmesi sayesinde, mevcut kimliğe ve grup üyeliğine göre Kubernetes erişimi ayarlayabilirsiniz. Mevcut Azure AD kullanıcılarınız ve gruplarınız, tümleşik bir oturum açma deneyimi ve AKS kaynaklarına erişim ile birlikte sağlanmış olabilir.  

Kimlik hakkında daha fazla bilgi için bkz. [AKS Için erişim ve kimlik seçenekleri][concepts-identity].

AKS kümelerinizin güvenliğini sağlamak için, bkz. [Azure Active Directory’yi AKS ile Tümleştirme][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Tümleşik günlüğe kaydetme ve izleme

Kapsayıcı sistem durumu için Azure Izleyici, AKS kümenizde ve dağıtılan uygulamalarda bulunan kapsayıcılardan, düğümlerden ve denetleyicilerden bellek ve işlemci performans ölçümlerini toplar. Hem kapsayıcı günlüklerini hem de [Kubernetes ana günlüklerini][aks-master-logs]gözden geçirebilirsiniz:
* Azure Log Analytics çalışma alanında depolanır.
* Azure portal, Azure CLı veya REST uç noktası aracılığıyla kullanılabilir.

Daha fazla bilgi için bkz. [Azure Kubernetes Hizmeti kapsayıcısı sistem durumunu izleme][container-health].

## <a name="clusters-and-nodes"></a>Kümeler ve düğümler

AKS düğümleri Azure sanal makinelerinde (VM 'Ler) çalışır. AKS düğümleri sayesinde, depolama alanı düğümlerine ve yığınlara bağlanabilir, küme bileşenlerini yükseltebilir ve GPU 'Lar kullanabilirsiniz. AKS, karma işletim sistemlerini ve Windows Server kapsayıcılarını desteklemek için birden çok düğüm havuzu çalıştıran Kubernetes kümelerini destekler.  

Kubernetes kümesi, düğümü ve düğüm havuzu özellikleri hakkında daha fazla bilgi için bkz. [AKS Için Kubernetes temel kavramları][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Küme düğümü ve pod ölçeklendirme

Kaynak talepleri değiştikçe, hizmetlerinizi çalıştıran küme düğümleri veya düğüm sayısı otomatik olarak ölçeği artırır veya küçültün. Taleplerine göre ayarlamak ve yalnızca gerekli kaynakları çalıştırmak için yatay Pod otomatik gizleme veya küme otomatik Scaler ' nı ayarlayabilirsiniz.

Daha fazla bilgi için bkz.[Azure Kubernetes Service (AKS) kümesini ölçeklendirme][aks-scale].

### <a name="cluster-node-upgrades"></a>Küme düğümü yükseltmeleri

AKS birden fazla Kubernetes sürümü sunmaktadır. AKS 'de yeni sürümler kullanılabilir hale geldiğinde, Azure portal veya Azure CLı kullanarak kümenizi yükseltebilirsiniz. Yükseltme işlemi sırasında, çalışan uygulamaların kesintiye uğramasını azaltmak için düğümler dikkatli bir şekilde kordonlanır ve boşaltılır.  

Yaşam döngüsü sürümleri hakkında daha fazla bilgi edinmek için, bkz. [AKS’de desteklenen Kubernetes sürümleri][aks-supported versions]. Yükseltmeyle ilgili adımlar için bkz. [Azure Kubernetes Service (AKS) kümesini yükseltme][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>GPU etkin düğümler

AKS, GPU etkin düğüm havuzlarının oluşturulmasını destekler. Azure Şu anda tek veya birden çok GPU özellikli VM sağlıyor. GPU özellikli VM 'Ler, işlem yoğunluğu, grafik yoğun ve görselleştirme iş yükleri için tasarlanmıştır.

Daha fazla bilgi için bkz. [AKS üzerinde GPU kullanma][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Gizli bilgi işlem düğümleri (Genel Önizleme)

AKS, Intel SGX tabanlı, gizli bilgi işlem düğüm havuzlarının (DCSv2 VM 'Ler) oluşturulmasını destekler. Gizli bilgi işlem düğümleri, kapsayıcıların donanım tabanlı, güvenilir bir yürütme ortamında (şifreleme) çalışmasına izin verir. Kanıtlama ile kod bütünlüğü ile birleştirilmiş kapsayıcılar arasındaki yalıtım, derinlemesine savunma kapsayıcısı güvenlik stratejinize yardımcı olabilir. Gizli bilgi işlem düğümleri hem gizli kapsayıcıları (mevcut Docker uygulamaları) hem de şifreleme kullanan kapsayıcıları destekler.

Daha fazla bilgi için bkz. [AKS üzerinde gizli bilgi işlem düğümleri][conf-com-node].

### <a name="storage-volume-support"></a>Depolama birimi desteği

Uygulama iş yüklerini desteklemek için, kalıcı veriler için statik veya dinamik depolama birimleri bağlayabilirsiniz. Depolama birimlerinin paylaşılması beklenen bağlı FID sayısına bağlı olarak, şu şekilde desteklenen depolama alanını kullanabilirsiniz:
* Tek Pod erişimi için Azure diskleri veya 
* Birden çok, eşzamanlı Pod erişimi için Azure dosyaları.

Daha fazla bilgi için bkz. [AKS 'teki uygulamalar Için depolama seçenekleri][concepts-storage].

[Azure diskleri][azure-disk] veya [Azure dosyalarını][azure-files]kullanarak dinamik kalıcı birimler ile çalışmaya başlayın.

## <a name="virtual-networks-and-ingress"></a>Sanal ağlar ve giriş

AKS kümesi var olan bir sanal ağa dağıtılabilir. Bu yapılandırmada, kümedeki her Pod sanal ağda bir IP adresi atanır ve doğrudan ile iletişim kurabilir:
* Kümedeki diğer kaynaklar 
* Sanal ağdaki diğer düğümler. 

Pods Ayrıca, eşlenmiş bir sanal ağdaki diğer hizmetlere ve ExpressRoute veya siteden siteye (S2S) VPN bağlantıları üzerinden şirket içi ağlara bağlanabilir.  

Daha fazla bilgi için bkz. [AKS 'teki uygulamalar Için ağ kavramları][aks-networking].

### <a name="ingress-with-http-application-routing"></a>HTTP uygulama yönlendirmesiyle giriş

HTTP uygulama yönlendirme eklentisi, AKS kümenize dağıtılan uygulamalara kolayca erişmenize yardımcı olur. HTTP uygulama yönlendirmesi çözümü etkinleştirildiğinde AKS kümenizde bir giriş denetleyicisi yapılandırır.  

Uygulamalar dağıtıldığında, genel olarak erişilebilen DNS adları otomatik olarak yapılandırılır. HTTP uygulama yönlendirmesi bir DNS bölgesi ayarlar ve bunu AKS kümesiyle tümleştirir. Ardından, Kubernetes giriş kaynaklarını normal olarak dağıtabilirsiniz.  

Giriş trafiği ile başlamak için, bkz. [HTTP uygulama yönlendirmesi][aks-http-routing].

## <a name="development-tooling-integration"></a>Geliştirme araçlarıyla tümleştirme

Kubernetes, AKS ile sorunsuz şekilde çalışan bir geliştirme ve yönetim araçları zengin ekosistemine sahiptir. Bu araçlar, Visual Studio Code için helk ve Kubernetes uzantısını içerir.   

Azure, Azure Dev Spaces ve DevOps Starter gibi Kubernetes 'i kolaylaştırmaya yardımcı olan çeşitli araçlar sağlar.  

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Azure Dev Spaces, ekiplere yönelik hızlı ve yinelemeli bir Kubernetes geliştirme deneyimi sunar. Minimum yapılandırma ile AKS içinde kapsayıcıları çalıştırabilir ve kapsayıcıların hatasını ayıklayabilirsiniz. Başlamak için, bkz. [Azure Dev Spaces][azure-dev-spaces].

### <a name="devops-starter"></a>DevOps başlangıç

DevOps Starter, mevcut kod ve Git depolarını Azure 'a getirmek için basit bir çözüm sunar. DevOps başlangıç otomatik:
* Azure kaynakları (örneğin, AKS) oluşturur; 
* Azure DevOps Services, CI için derleme işlem hattı içeren bir yayın işlem hattını yapılandırır. 
* CD için bir yayın işlem hattı ayarlar; ' 
* İzleme için bir Azure Application Insights kaynağı oluşturur. 

Daha fazla bilgi için bkz. [DevOps Starter][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Docker görüntü desteği ve özel kapsayıcı kayıt defteri

AKS, Docker görüntü biçimini destekler. Docker görüntülerinizin özel olarak depolanması için, AKS’yi Azure Container Registry (ACR) ile tümleştirebilirsiniz.

Özel bir görüntü deposu oluşturmak için, bkz. [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Kubernetes sertifikası

AKS 'ler, Kubernetes uyumlu olarak onaylandı.

## <a name="regulatory-compliance"></a>Mevzuata uyumluluk

AKS, SOC, ISO, PCI DSS ve HIPAA ile uyumludur. Daha fazla bilgi için bkz. [Microsoft Azure uyumluluğuna genel bakış][compliance-doc].

## <a name="next-steps"></a>Sonraki adımlar

Azure CLı hızlı başlangıç ile AKS dağıtma ve yönetme hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure CLı kullanarak AKS kümesi dağıtma][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://azure.microsoft.com/en-us/overview/trusted-cloud/compliance/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: ./view-control-plane-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
