---
title: Azure Kubernetes hizmeti 'nde (AKS) çıkış trafiğini kısıtlama
description: Azure Kubernetes Service (AKS) ' de çıkış trafiğini denetlemek için hangi bağlantı noktalarının ve adreslerin gerekli olduğunu öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/29/2019
ms.author: mlearned
ms.openlocfilehash: 3010973c7d0af784938e9295bb80fc22b7f718f3
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018650"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki küme düğümleri için çıkış trafiğini denetleme

Varsayılan olarak, AKS kümelerinde sınırsız giden (çıkış) internet erişimi vardır. Bu ağ erişimi düzeyi, çalıştırdığınız düğüm ve hizmetlere, gerektiğinde dış kaynaklara erişmek için izin verir. Çıkış trafiğini kısıtlamak istiyorsanız, sağlıklı küme bakım görevlerini sürdürmek için sınırlı sayıda bağlantı noktasına ve adrese erişilebilir olması gerekir. Kümeniz, varsayılan olarak Microsoft Container Registry (MCR) veya Azure Container Registry (ACR) tarafından kullanılan temel sistem kapsayıcısı görüntülerini kullanacak şekilde yapılandırılır. Tercih ettiğiniz güvenlik duvarını ve güvenlik kurallarınızı, bu gerekli bağlantı noktalarına ve adreslere izin verecek şekilde yapılandırın.

Bu makalede, bir AKS kümesindeki çıkış trafiğini kısıtladığınızda ağ bağlantı noktaları ve tam etki alanı adları (FQDN 'Ler) gerekli ve isteğe bağlı olarak ayrıntılı olarak açıklanmaktadır.

> [!IMPORTANT]
> Bu belge, yalnızca AKS alt ağını bırakarak trafiğin nasıl kilitleneceği hakkında ele alınmaktadır. AKS 'ler giriş gereksinimlerine sahip değildir.  Ağ güvenlik grupları (NSG 'ler) ve güvenlik duvarları kullanılarak iç alt ağ trafiğinin engellenmesi desteklenmez. Küme içindeki trafiği denetlemek ve engellemek için [ağ ilkelerini][network-policy]kullanın.

## <a name="before-you-begin"></a>Başlamadan önce

Azure CLı sürüm 2.0.66 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][install-azure-cli].

## <a name="egress-traffic-overview"></a>Çıkış trafiğine genel bakış

Yönetim ve operasyonel amaçlar için, bir AKS kümesindeki düğümlerin belirli bağlantı noktalarına ve tam etki alanı adlarına (FQDN) erişmesi gerekir. Bu eylemler, API sunucusuyla iletişim kurmak veya temel Kubernetes küme bileşenlerini ve düğüm güvenliği güncelleştirmelerini indirmek ve yüklemek olabilir. Varsayılan olarak, çıkış (giden) internet trafiği bir AKS kümesindeki düğümler için sınırlandırılmaz. Küme, dış depolardan temel sistem kapsayıcısı görüntülerini alabilir.

AKS kümenizin güvenliğini arttırmak için çıkış trafiğini kısıtlamak isteyebilirsiniz. Küme, MCR veya ACR 'den temel sistem kapsayıcısı görüntülerini çekmek üzere yapılandırılmıştır. Çıkış trafiğini bu şekilde kilitlerseniz, AKS düğümlerinin gerekli dış hizmetlerle doğru iletişim kurmasına izin vermek için belirli bağlantı noktalarını ve FQDN 'leri tanımlayın. Bu yetkili bağlantı noktaları ve FQDN 'Ler olmadan AKS düğümleriniz API sunucusuyla iletişim kuramaz veya çekirdek bileşenleri yükleyemez.

Çıkış trafiğinizi güvenli hale getirmek ve bu gerekli bağlantı noktalarını ve adresleri tanımlamak için [Azure Güvenlik Duvarı][azure-firewall] 'nı veya üçüncü taraf güvenlik duvarı gereçini kullanabilirsiniz. AKS bu kuralları sizin için otomatik olarak oluşturmaz. Aşağıdaki bağlantı noktaları ve adresler, ağ güvenlik duvarınızdaki uygun kuralları oluştururken başvuru içindir.

> [!IMPORTANT]
> Çıkış trafiğini kısıtlamak ve tüm çıkış trafiğini zorlamak için Kullanıcı tanımlı yol (UDR) oluşturmak üzere Azure Güvenlik Duvarı 'nı kullandığınızda, giriş trafiğine doğru şekilde izin vermek için güvenlik duvarında uygun bir DNAT kuralı oluşturduğunuzdan emin olun. Azure Güvenlik Duvarı 'nı bir UDR ile kullanmak, asimetrik yönlendirme nedeniyle giriş kurulumunu keser. (AKS alt ağının, güvenlik duvarının özel IP adresine giden bir varsayılan yolu varsa, ancak türünde ortak yük dengeleyici veya Kubernetes hizmeti kullanıyorsanız bu sorun oluşur: LoadBalancer). Bu durumda, gelen yük dengeleyici trafiği genel IP adresi aracılığıyla alınır, ancak döndürülen yol güvenlik duvarının özel IP adresinden geçer. Güvenlik duvarı durum bilgisi olduğundan, güvenlik duvarı kurulu bir oturumun farkında olmadığından döndürülen paketi bırakır. Azure Güvenlik duvarını giriş veya hizmet yük dengeleyicinizle tümleştirmeyi öğrenmek için bkz. Azure [güvenlik duvarını azure standart Load Balancer tümleştirme](https://docs.microsoft.com/azure/firewall/integrate-lb).
> TCP bağlantı noktası 9000 ve TCP bağlantı noktası 22 trafiğini, çıkış çalışan düğümü IP 'leri ve API sunucusunun IP 'si arasında bir ağ kuralı kullanarak azaltabilirsiniz.

AKS 'de, iki bağlantı noktası ve adres kümesi vardır:

* [AKS kümelerine yönelik gerekli bağlantı noktaları ve adresler](#required-ports-and-addresses-for-aks-clusters) , yetkili çıkış trafiği için en düşük gereksinimleri ayrıntılarıyla ayrıntılardır.
* [AKS kümeleri için isteğe bağlı önerilen adresler ve bağlantı noktaları](#optional-recommended-addresses-and-ports-for-aks-clusters) tüm senaryolar için gerekli değildir, ancak Azure izleyici gibi diğer hizmetlerle tümleştirme doğru çalışmaz. İsteğe bağlı bağlantı noktaları ve FQDN 'Ler listesini gözden geçirin ve AKS Kümenizde kullanılan hizmet ve bileşenlerden herhangi birini yetkilendirin.

> [!NOTE]
> Çıkış trafiğini kısıtlamak yalnızca yeni AKS kümelerinde kullanılabilir. Mevcut kümeler için çıkış trafiğini sınırlandırmadan önce `az aks upgrade` komutunu kullanarak [bir küme yükseltme işlemi gerçekleştirin][aks-upgrade] .

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS kümeleri için gerekli bağlantı noktaları ve adresler

AKS kümesi için aşağıdaki giden bağlantı noktaları/ağ kuralları gereklidir:

* TCP bağlantı noktası *443*
* API sunucusuyla iletişim kurmayı gerektiren bir uygulamanız varsa, TCP [ıpaddrofyourapiserver]: 443 gereklidir.  Bu değişiklik küme oluşturulduktan sonra ayarlanabilir.
* Tünel ön pod için, API sunucusundaki tünel sonuyla iletişim kurmak üzere TCP bağlantı noktası *9000* ve TCP bağlantı noktası *22* .
    * Daha fazla bilgi almak için * *. HCP öğesine bakın.\< Location\>. azmk8s.io* ve * *. tun.\< Aşağıdaki\>tablodaki location. azmk8s.io* adresleri.
* API sunucusuna doğrudan erişiyorsanız, DNS için UDP bağlantı noktası *53* de gereklidir.

Aşağıdaki FQDN/uygulama kuralları gereklidir:

| FQDN                       | Port      | Bir yönetim grubuna bağlanmak veya bağlı bir yönetim grubunun özelliklerini düzenlemek için Yönetim çalışma alanında      |
|----------------------------|-----------|----------|
| *. HCP. \<Location\>. azmk8s.io | HTTPS: 443, TCP: 22, TCP: 9000 | Bu adres, API sunucusu uç noktasıdır. Konumu aks kümenizin dağıtıldığı bölge ile değiştirin. *\<\>* |
| *. tun. \<Location\>. azmk8s.io | HTTPS: 443, TCP: 22, TCP: 9000 | Bu adres, API sunucusu uç noktasıdır. Konumu aks kümenizin dağıtıldığı bölge ile değiştirin. *\<\>* |
| aksrepos.azurecr.io        | HTTPS: 443 | Bu adres Azure Container Registry (ACR) içindeki görüntülere erişmek için gereklidir. Bu kayıt defteri, kümenin yükseltilmesi ve ölçeklendirilmesi sırasında kümenin çalışması için gerekli olan üçüncü taraf resimleri/grafikleri (örneğin, ölçüm sunucusu, çekirdek DNS vb.) içerir|
| *.blob.core.windows.net    | HTTPS: 443 | Bu adres, ACR 'de depolanan görüntülerin arka uç deposudur. |
| mcr.microsoft.com          | HTTPS: 443 | Bu adres, Microsoft Container Registry (MCR) içindeki görüntülere erişmek için gereklidir. Bu kayıt defteri, kümenin yükseltilmesi ve ölçeklendirilmesi sırasında kümenin çalışması için gerekli olan ilk taraf görüntülerini/grafikleri (örneğin, Moby, vb.) içerir |
| *.cdn.mscr.io              | HTTPS: 443 | Bu adres, Azure Content Delivery Network (CDN) tarafından desteklenen MCR depolama alanı için gereklidir. |
| Management.Azure.com       | HTTPS: 443 | Bu adres, Kubernetes GET/PUT işlemleri için gereklidir. |
| login.microsoftonline.com  | HTTPS: 443 | Bu adres Azure Active Directory kimlik doğrulaması için gereklidir. |
| ntp.ubuntu.com             | UDP: 123   | Bu adres, Linux düğümlerinde NTP zaman eşitlemesi için gereklidir. |
| packages.microsoft.com     | HTTPS: 443 | Bu adres, önbelleğe alınmış *apt-get* işlemleri Için kullanılan Microsoft paketleri deposudur.  Örnek paketlere Moby, PowerShell ve Azure CLı dahildir. |
| acs-mirror.azureedge.net   | HTTPS: 443 | Bu adres, Kubernetes kullanan ve Azure CNı gibi gerekli ikilileri yüklemek için gereken depoya yöneliktir. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>AKS kümeleri için isteğe bağlı önerilen adresler ve bağlantı noktaları

Aşağıdaki giden bağlantı noktaları/ağ kuralları bir AKS kümesi için isteğe bağlıdır:

AKS kümelerinin doğru çalışması için aşağıdaki FQDN/uygulama kuralları önerilir:

| FQDN                                    | Port      | Bir yönetim grubuna bağlanmak veya bağlı bir yönetim grubunun özelliklerini düzenlemek için Yönetim çalışma alanında      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP: 80   | Bu adres, Linux küme düğümlerinin gerekli güvenlik düzeltme eklerini ve güncelleştirmelerini indirmesini sağlar. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>GPU etkin AKS kümeleri için gerekli adresler ve bağlantı noktaları

GPU etkin olan AKS kümeleri için aşağıdaki FQDN/uygulama kuralları gereklidir:

| FQDN                                    | Port      | Bir yönetim grubuna bağlanmak veya bağlı bir yönetim grubunun özelliklerini düzenlemek için Yönetim çalışma alanında      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS: 443 | Bu adres, GPU tabanlı düğümlerde doğru sürücü yükleme ve işlem için kullanılır. |
| us.download.nvidia.com | HTTPS: 443 | Bu adres, GPU tabanlı düğümlerde doğru sürücü yükleme ve işlem için kullanılır. |
| apt.dockerproject.org | HTTPS: 443 | Bu adres, GPU tabanlı düğümlerde doğru sürücü yükleme ve işlem için kullanılır. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Kapsayıcılar için Azure Izleyici ile gerekli adresler ve bağlantı noktaları

Aşağıdaki FQDN/uygulama kuralları, kapsayıcılar için Azure Izleyicisi etkinleştirilmiş olan AKS kümeleri için gereklidir:

| FQDN                                    | Port      | Bir yönetim grubuna bağlanmak veya bağlı bir yönetim grubunun özelliklerini düzenlemek için Yönetim çalışma alanında      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS: 443  | Bu, Azure Izleyici kullanarak doğru ölçümler ve izleme telemetrisine yöneliktir. |
| *.ods.opinsights.azure.com    | HTTPS: 443 | Bu, Azure Izleyici tarafından günlük analizi verilerini almak için kullanılır. |
| *.oms.opinsights.azure.com | HTTPS: 443 | Bu adres, Log Analytics hizmetinin kimliğini doğrulamak için kullanılan omsagent tarafından kullanılır. |
|*.microsoftonline.com | HTTPS: 443 | Bu, Azure Izleyici 'ye yönelik kimlik doğrulaması ve ölçüm göndermek için kullanılır. |
|*. monitoring.azure.com | HTTPS: 443 | Bu, ölçüm verilerini Azure Izleyici 'ye göndermek için kullanılır. |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Azure Ilkesi ile AKS kümeleri için gerekli adresler ve bağlantı noktaları (genel önizlemede) etkin

> [!CAUTION]
> Aşağıdaki özelliklerden bazıları önizleme aşamasındadır.  Bu makaledeki öneriler, özellik genel önizlemeye ve gelecek sürüm aşamasına taşınıyor şekilde değişir.

Azure Ilkesi etkinleştirilmiş AKS kümeleri için aşağıdaki FQDN/uygulama kuralları gereklidir.

| FQDN                                    | Port      | Bir yönetim grubuna bağlanmak veya bağlı bir yönetim grubunun özelliklerini düzenlemek için Yönetim çalışma alanında      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS: 443 | Bu adres, Azure Ilkesi 'nin doğru çalışması için kullanılır. (Şu anda AKS 'deki önizlemededir) |
| RAW.githubusercontent.com | HTTPS: 443 | Bu adres, Azure Ilkesinde doğru işlem yapıldığından emin olmak için yerleşik ilkeleri GitHub 'dan çekmek için kullanılır. (Şu anda AKS 'deki önizlemededir) |
| *. gk. <location>. azmk8s.io | HTTPS: 443 | Azure ilke eklentisi, Denetim sonuçlarını almak için ana sunucuda çalışan Gatekeeper denetim uç noktasına gidin. |
| dc.services.visualstudio.com | HTTPS: 443 | Azure ilke eklentisi, telemetri verilerini uygulamalar öngörüleri uç noktasına gönderir. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Windows Server tabanlı düğümler için gerekli (genel önizlemede) etkin

> [!CAUTION]
> Aşağıdaki özelliklerden bazıları önizleme aşamasındadır.  Bu makaledeki öneriler, özellik genel önizlemeye ve gelecek sürüm aşamasına taşınıyor şekilde değişir.

Windows Server tabanlı AKS kümeleri için aşağıdaki FQDN/uygulama kuralları gereklidir:

| FQDN                                    | Port      | Bir yönetim grubuna bağlanmak veya bağlı bir yönetim grubunun özelliklerini düzenlemek için Yönetim çalışma alanında      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS: 443 | Windows ile ilgili ikili dosyaları yüklemek için |
| mp.microsoft.com, www<span></span>. msftconnecttest.com, ctldl.windowsupdate.com | HTTP: 80 | Windows ile ilgili ikili dosyaları yüklemek için |
| kms.core.windows.net | TCP: 1688 | Windows ile ilgili ikili dosyaları yüklemek için |


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, kümenin çıkış trafiğini kısıtladığınızda hangi bağlantı noktalarının ve adreslerin izin verdiklerini öğrendiniz. Ayrıca, ayırımların nasıl iletişim kurabildiğini ve küme içinde sahip oldukları kısıtlamaları tanımlayabilirsiniz. Daha fazla bilgi için bkz. [aks 'deki ağ ilkelerini kullanarak Pod arasındaki trafiği güvenli hale getirme][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
