---
title: Azure Kubernetes hizmeti 'nde (AKS) çıkış trafiğini kısıtlama
description: Azure Kubernetes Service (AKS) ' de çıkış trafiğini denetlemek için hangi bağlantı noktalarının ve adreslerin gerekli olduğunu öğrenin
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 39c0877b96a3e8c6c716c1ab9ae7ba11575990a0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765602"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki küme düğümleri için çıkış trafiğini denetleme

Bu makalede, Azure Kubernetes hizmetinden (AKS) giden trafiği güvenli hale getirmeye olanak tanıyan gerekli ayrıntılar sağlanmaktadır. Temel AKS dağıtımı için küme gereksinimlerini ve isteğe bağlı eklentiler ve özellikler için ek gereksinimleri içerir. [Azure Güvenlik Duvarı ile bu gereksinimlerin nasıl yapılandırılacağı hakkında daha fazla bir örnek](#restrict-egress-traffic-using-azure-firewall)sunulacaktır. Bununla birlikte, bu bilgileri herhangi bir giden kısıtlama yöntemine veya gerecine uygulayabilirsiniz.

## <a name="background"></a>Arka Plan

AKS kümeleri bir sanal ağa dağıtılır. Bu ağ yönetilebilecek (AKS tarafından oluşturulan) veya özel (önceden Kullanıcı tarafından önceden yapılandırılmış). Her iki durumda da, kümenin söz konusu sanal ağın dışındaki hizmetlerde **giden** bağımlılıkları vardır (hizmetin gelen bağımlılığı yoktur).

Yönetim ve operasyonel amaçlar için, bir AKS kümesindeki düğümlerin belirli bağlantı noktalarına ve tam etki alanı adlarına (FQDN) erişmesi gerekir. Bu uç noktalar, düğümlerin API sunucusuyla iletişim kurması veya temel Kubernetes küme bileşenlerini ve düğüm güvenliği güncelleştirmelerini indirip yüklemesi için gereklidir. Örneğin, kümenin Microsoft Container Registry (MCR) ' den temel sistem kapsayıcısı görüntülerini çekmesi gerekir.

AKS giden bağımlılıkları, bunların arkasında statik adresler bulunmayan FQDN 'Ler ile neredeyse tamamen tanımlıdır. Statik adreslerin olmaması, ağ güvenlik gruplarının bir AKS kümesinden giden trafiği kilitlemek için kullanılamayacağı anlamına gelir. 

Varsayılan olarak, AKS kümelerinde sınırsız giden (çıkış) internet erişimi vardır. Bu ağ erişimi düzeyi, çalıştırdığınız düğüm ve hizmetlere, gerektiğinde dış kaynaklara erişmek için izin verir. Çıkış trafiğini kısıtlamak istiyorsanız, sağlıklı küme bakım görevlerini sürdürmek için sınırlı sayıda bağlantı noktasına ve adrese erişilebilir olması gerekir. Giden adreslerin güvenliğini sağlamaya yönelik en basit çözüm, etki alanı adlarına göre giden trafiği denetleyesağlayan bir güvenlik duvarı cihazının kullanılmasına yol açabilir. Örneğin, Azure Güvenlik Duvarı, giden HTTP ve HTTPS trafiğini hedefin FQDN 'sine göre kısıtlayabilir. Ayrıca tercih ettiğiniz güvenlik duvarı ve güvenlik kurallarınızı, bu gerekli bağlantı noktalarına ve adreslere izin verecek şekilde yapılandırabilirsiniz.

> [!IMPORTANT]
> Bu belge, yalnızca AKS alt ağını bırakarak trafiğin nasıl kilitleneceği hakkında ele alınmaktadır. AKS 'ler varsayılan olarak giriş gereksinimlerine sahip değildir.  Ağ güvenlik grupları (NSG 'ler) ve güvenlik duvarları kullanılarak **iç alt ağ trafiğinin** engellenmesi desteklenmez. Küme içindeki trafiği denetlemek ve engellemek için [**_ağ ilkelerini_**][network-policy]kullanın.

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>AKS kümeleri için gerekli giden ağ kuralları ve FQDN 'Ler

Aşağıdaki ağ ve FQDN/uygulama kuralları bir AKS kümesi için gereklidir, Azure Güvenlik Duvarı dışında bir çözüm yapılandırmak istiyorsanız bunları kullanabilirsiniz.

* IP adresi bağımlılıkları HTTP/sn olmayan trafiğe yöneliktir (TCP ve UDP trafiği)
* FQDN HTTP/HTTPS uç noktaları, güvenlik duvarı cihazınıza yerleştirilebilir.
* Joker karakter HTTP/HTTPS uç noktaları, AKS kümenizde farklı bir dizi niteleyicileri temel alarak değişebilen bağımlılıklardır.
* AKS, bu FQDN 'yi kuas sistemi ve Gatekeeper-System altındaki tüm dağıtımlara bir ortam değişkeni olarak eklemek için bir giriş denetleyicisi kullanır. Bu, düğümler ve API sunucusu arasındaki tüm sistem iletişiminin API sunucusu IP 'sini kullanmamasını sağlar. 
* API sunucusuyla iletişim kurmak için gereken bir uygulama veya çözümünüz varsa, *API SUNUCUNUZUN IP 'sinin 443 numaralı bağlantı noktasına TCP iletişimine* izin vermek için **ek** bir ağ kuralı eklemeniz gerekir.
* Nadir durumlarda, API sunucunuzun IP 'si değişebilir bir bakım işlemi varsa. API sunucusu IP 'sini değiştirebildiğinden planlı bakım işlemlerine her zaman önceden bir iletilir.


### <a name="azure-global-required-network-rules"></a>Azure genel gerekli ağ kuralları

Gerekli ağ kuralları ve IP adresi bağımlılıkları şunlardır:

| Hedef uç nokta                                                             | Protokol | Bağlantı noktası    | Kullanın  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Veya* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Veya* <br/> [Bölgesel Cıdrs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Veya* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Düğümler ve denetim düzlemi arasında Tünellenen güvenli iletişim için. [Özel kümeler](private-clusters.md) için bu gerekli değildir|
| **`*:9000`** <br/> *Veya* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Veya* <br/> [Bölgesel Cıdrs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Veya* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Düğümler ve denetim düzlemi arasında Tünellenen güvenli iletişim için. [Özel kümeler](private-clusters.md) için bu gerekli değildir |
| **`*:123`** veya **`ntp.ubuntu.com:123`** (Azure Güvenlik Duvarı ağ kuralları kullanılıyorsa)  | UDP      | 123     | Linux düğümlerinde ağ zaman Protokolü (NTP) zaman eşitlemesi için gereklidir.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Özel DNS sunucuları kullanıyorsanız, bunların küme düğümleri tarafından erişilebilir olduklarından emin olmanız gerekir. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | API sunucusuna erişen pods/dağıtımlar çalıştırıyorsanız, bu pods/dağıtımlar API IP 'sini kullanır. [Özel kümeler](private-clusters.md) için bu gerekli değildir  |

### <a name="azure-global-required-fqdn--application-rules"></a>Azure genel gerekli FQDN/uygulama kuralları 

Aşağıdaki FQDN/uygulama kuralları gereklidir:

| Hedef FQDN                 | Bağlantı noktası            | Kullanın      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Node <-> API Server iletişimi için gereklidir. *\<location\>* AKS kümenizin dağıtıldığı bölge ile değiştirin. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Microsoft Container Registry (MCR) içindeki görüntülere erişmek için gereklidir. Bu kayıt defteri ilk taraf görüntülerini/grafiklerini içerir (örneğin, coreDNS, vb.). Bu görüntüler, ölçek ve yükseltme işlemleri de dahil olmak üzere kümenin doğru oluşturulması ve çalışması için gereklidir.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Azure Content Delivery Network (CDN) tarafından desteklenen MCR depolama alanı için gereklidir. |
| **`management.azure.com`**       | **`HTTPS:443`** | Azure API 'sine karşı Kubernetes işlemleri için gereklidir. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Azure Active Directory kimlik doğrulaması için gereklidir. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Bu adres, önbelleğe alınmış *apt-get* işlemleri Için kullanılan Microsoft paketleri deposudur.  Örnek paketlere Moby, PowerShell ve Azure CLı dahildir. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Bu adres, Kubernetes kullanan ve Azure CNı gibi gerekli ikilileri indirmek ve yüklemek için gereken depoya yöneliktir. |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure Çin 21Vianet gereken ağ kuralları

Gerekli ağ kuralları ve IP adresi bağımlılıkları şunlardır:

| Hedef uç nokta                                                             | Protokol | Bağlantı noktası    | Kullanın  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Veya* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Veya* <br/> [Bölgesel Cıdrs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Veya* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Düğümler ve denetim düzlemi arasında Tünellenen güvenli iletişim için. |
| **`*:9000`** <br/> *Veya* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Veya* <br/> [Bölgesel Cıdrs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Veya* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Düğümler ve denetim düzlemi arasında Tünellenen güvenli iletişim için. |
| **`*:22`** <br/> *Veya* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Veya* <br/> [Bölgesel Cıdrs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Veya* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | Düğümler ve denetim düzlemi arasında Tünellenen güvenli iletişim için. |
| **`*:123`** veya **`ntp.ubuntu.com:123`** (Azure Güvenlik Duvarı ağ kuralları kullanılıyorsa)  | UDP      | 123     | Linux düğümlerinde ağ zaman Protokolü (NTP) zaman eşitlemesi için gereklidir.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Özel DNS sunucuları kullanıyorsanız, bunların küme düğümleri tarafından erişilebilir olduklarından emin olmanız gerekir. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | API sunucusuna erişen pods/dağıtımlar çalıştırıyorsanız, bu Pod/dağıtımlar API IP 'sini kullanır.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure Çin 21Vianet gerekli FQDN/uygulama kuralları

Aşağıdaki FQDN/uygulama kuralları gereklidir:

| Hedef FQDN                               | Bağlantı noktası            | Kullanın      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Node <-> API Server iletişimi için gereklidir. *\<location\>* AKS kümenizin dağıtıldığı bölge ile değiştirin. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Node <-> API Server iletişimi için gereklidir. *\<location\>* AKS kümenizin dağıtıldığı bölge ile değiştirin. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Microsoft Container Registry (MCR) içindeki görüntülere erişmek için gereklidir. Bu kayıt defteri ilk taraf görüntülerini/grafiklerini içerir (örneğin, coreDNS, vb.). Bu görüntüler, ölçek ve yükseltme işlemleri de dahil olmak üzere kümenin doğru oluşturulması ve çalışması için gereklidir. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Azure Content Delivery Network (CDN) tarafından desteklenen MCR depolama alanı için gereklidir. |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Azure API 'sine karşı Kubernetes işlemleri için gereklidir. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Azure Active Directory kimlik doğrulaması için gereklidir. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Bu adres, önbelleğe alınmış *apt-get* işlemleri Için kullanılan Microsoft paketleri deposudur.  Örnek paketlere Moby, PowerShell ve Azure CLı dahildir. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Bu adres, Kubernetes kullanan ve Azure CNı gibi gerekli ikilileri indirmek ve yüklemek için gereken depoya yöneliktir. |

### <a name="azure-us-government-required-network-rules"></a>Azure ABD kamu gerekli ağ kuralları

Gerekli ağ kuralları ve IP adresi bağımlılıkları şunlardır:

| Hedef uç nokta                                                             | Protokol | Bağlantı noktası    | Kullanın  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Veya* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Veya* <br/> [Bölgesel Cıdrs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Veya* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Düğümler ve denetim düzlemi arasında Tünellenen güvenli iletişim için. |
| **`*:9000`** <br/> *Veya* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Veya* <br/> [Bölgesel Cıdrs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Veya* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Düğümler ve denetim düzlemi arasında Tünellenen güvenli iletişim için. |
| **`*:123`** veya **`ntp.ubuntu.com:123`** (Azure Güvenlik Duvarı ağ kuralları kullanılıyorsa)  | UDP      | 123     | Linux düğümlerinde ağ zaman Protokolü (NTP) zaman eşitlemesi için gereklidir.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Özel DNS sunucuları kullanıyorsanız, bunların küme düğümleri tarafından erişilebilir olduklarından emin olmanız gerekir. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | API sunucusuna erişen pods/dağıtımlar çalıştırıyorsanız, bu pods/dağıtımlar API IP 'sini kullanır.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure ABD kamu gerekli FQDN/uygulama kuralları 

Aşağıdaki FQDN/uygulama kuralları gereklidir:

| Hedef FQDN                                        | Bağlantı noktası            | Kullanın      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Node <-> API Server iletişimi için gereklidir. *\<location\>* AKS kümenizin dağıtıldığı bölge ile değiştirin.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Microsoft Container Registry (MCR) içindeki görüntülere erişmek için gereklidir. Bu kayıt defteri ilk taraf görüntülerini/grafiklerini içerir (örneğin, coreDNS, vb.). Bu görüntüler, ölçek ve yükseltme işlemleri de dahil olmak üzere kümenin doğru oluşturulması ve çalışması için gereklidir. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Azure Content Delivery Network (CDN) tarafından desteklenen MCR depolama alanı için gereklidir. |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Azure API 'sine karşı Kubernetes işlemleri için gereklidir. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Azure Active Directory kimlik doğrulaması için gereklidir. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Bu adres, önbelleğe alınmış *apt-get* işlemleri Için kullanılan Microsoft paketleri deposudur.  Örnek paketlere Moby, PowerShell ve Azure CLı dahildir. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Bu adres, Kubernetes kullanan ve Azure CNı gibi gerekli ikilileri yüklemek için gereken depoya yöneliktir. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>AKS kümeleri için isteğe bağlı önerilen FQDN/uygulama kuralları

Aşağıdaki FQDN/uygulama kuralları isteğe bağlıdır, ancak AKS kümeleri için önerilir:

| Hedef FQDN                                                               | Bağlantı noktası          | Kullanın      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Bu adres, Linux küme düğümlerinin gerekli güvenlik düzeltme eklerini ve güncelleştirmelerini indirmesini sağlar. |

Bu FQDN 'lerin engellenmesini/izin vermeyi seçerseniz düğümler yalnızca bir [düğüm görüntüsü yükseltmesi](node-image-upgrade.md) veya [küme yükseltmesi](upgrade-cluster.md)yaptığınızda işletim sistemi güncelleştirmelerini alır.

## <a name="gpu-enabled-aks-clusters"></a>GPU etkin AKS kümeleri

### <a name="required-fqdn--application-rules"></a>Gerekli FQDN/uygulama kuralları

GPU etkin olan AKS kümeleri için aşağıdaki FQDN/uygulama kuralları gereklidir:

| Hedef FQDN                        | Bağlantı noktası      | Kullanın      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Bu adres, GPU tabanlı düğümlerde doğru sürücü yükleme ve işlem için kullanılır. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Bu adres, GPU tabanlı düğümlerde doğru sürücü yükleme ve işlem için kullanılır. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Bu adres, GPU tabanlı düğümlerde doğru sürücü yükleme ve işlem için kullanılır. |

## <a name="windows-server-based-node-pools"></a>Windows Server tabanlı düğüm havuzları 

### <a name="required-fqdn--application-rules"></a>Gerekli FQDN/uygulama kuralları

Windows Server tabanlı düğüm havuzlarını kullanmak için aşağıdaki FQDN/uygulama kuralları gereklidir:

| Hedef FQDN                                                           | Bağlantı noktası      | Kullanın      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Windows ile ilgili ikili dosyaları yüklemek için |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Windows ile ilgili ikili dosyaları yüklemek için |

## <a name="aks-addons-and-integrations"></a>AKS eklentileri ve tümleştirmeler

### <a name="azure-monitor-for-containers"></a>Kapsayıcılar için Azure İzleyici

Kapsayıcılar için Azure Izleyici 'ye erişim sağlamanın iki seçeneği vardır. Azure Izleyici [Servicetag](../virtual-network/service-tags-overview.md#available-service-tags) 'e izin verebilir **veya** gerekli FQDN/uygulama kurallarına erişim sağlayabilirsiniz.

#### <a name="required-network-rules"></a>Gerekli ağ kuralları

Aşağıdaki FQDN/uygulama kuralları gereklidir:

| Hedef uç nokta                                                             | Protokol | Bağlantı noktası    | Kullanın  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Bu uç nokta, ölçüm verilerini ve günlükleri Azure Izleyici 'ye ve Log Analytics göndermek için kullanılır. |

#### <a name="required-fqdn--application-rules"></a>Gerekli FQDN/uygulama kuralları

Aşağıdaki FQDN/uygulama kuralları, kapsayıcılar için Azure Izleyicisi etkinleştirilmiş olan AKS kümeleri için gereklidir:

| FQDN                                    | Bağlantı noktası      | Kullanın      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Bu uç nokta, Azure Izleyici kullanarak ölçümler ve izleme telemetri için kullanılır. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Bu uç nokta, Azure Izleyici tarafından günlük analizi verilerini almak için kullanılır. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Bu uç nokta, Log Analytics hizmetinin kimliğini doğrulamak için kullanılan omsagent tarafından kullanılır. |
| *. monitoring.azure.com | **`HTTPS:443`** | Bu uç nokta, ölçüm verilerini Azure Izleyici 'ye göndermek için kullanılır. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Güvenlik duvarınızı veya güvenlik yapılandırmanızı, aşağıdaki FQDN 'lerin ve [Azure dev Spaces altyapı hizmetlerinin][dev-spaces-service-tags]tümüne ve bunlara giden ağ trafiğine izin verecek şekilde güncelleştirin.

#### <a name="required-network-rules"></a>Gerekli ağ kuralları

| Hedef uç nokta                                                             | Protokol | Bağlantı noktası    | Kullanın  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Bu uç nokta, ölçüm verilerini ve günlükleri Azure Izleyici 'ye ve Log Analytics göndermek için kullanılır. |

#### <a name="required-fqdn--application-rules"></a>Gerekli FQDN/uygulama kuralları 

Azure Dev Spaces etkin olan AKS kümeleri için aşağıdaki FQDN/uygulama kuralları gereklidir:

| FQDN                                    | Bağlantı noktası      | Kullanın      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Bu adres, Linux alp ve diğer Azure Dev Spaces görüntülerini çekmek için kullanılır |
| `gcr.io` | **`HTTPS:443`** | Bu adres, Held/Tiller görüntülerini çekmek için kullanılır |
| `storage.googleapis.com` | **`HTTPS:443`** | Bu adres, Held/Tiller görüntülerini çekmek için kullanılır |


### <a name="azure-policy"></a>Azure İlkesi

#### <a name="required-fqdn--application-rules"></a>Gerekli FQDN/uygulama kuralları 

Azure Ilkesi etkinleştirilmiş AKS kümeleri için aşağıdaki FQDN/uygulama kuralları gereklidir.

| FQDN                                          | Bağlantı noktası      | Kullanın      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | Bu adres, Kubernetes ilkelerini çekmek ve küme uyumluluk durumunu ilke hizmetine bildirmek için kullanılır. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | Bu adres, yerleşik ilkelerin geçit Denetçi yapılarını çekmek için kullanılır. |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Bu adres, Azure Ilkesi 'nin doğru çalışması için kullanılır.  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Bu adres, Azure Ilkesinde doğru işlem yapıldığından emin olmak için yerleşik ilkeleri GitHub 'dan çekmek için kullanılır. |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Uygulama öngörüleri uç noktasına telemetri verileri gönderen Azure Ilke eklentisi. |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure Çin 21Vianet gerekli FQDN/uygulama kuralları 

Azure Ilkesi etkinleştirilmiş AKS kümeleri için aşağıdaki FQDN/uygulama kuralları gereklidir.

| FQDN                                          | Bağlantı noktası      | Kullanın      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | Bu adres, Kubernetes ilkelerini çekmek ve küme uyumluluk durumunu ilke hizmetine bildirmek için kullanılır. |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | Bu adres, yerleşik ilkelerin geçit Denetçi yapılarını çekmek için kullanılır. |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure ABD kamu gerekli FQDN/uygulama kuralları

Azure Ilkesi etkinleştirilmiş AKS kümeleri için aşağıdaki FQDN/uygulama kuralları gereklidir.

| FQDN                                          | Bağlantı noktası      | Kullanın      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | Bu adres, Kubernetes ilkelerini çekmek ve küme uyumluluk durumunu ilke hizmetine bildirmek için kullanılır. |
| **`store.policy.azure.us`** | **`HTTPS:443`** | Bu adres, yerleşik ilkelerin geçit Denetçi yapılarını çekmek için kullanılır. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Azure Güvenlik Duvarı 'nı kullanarak çıkış trafiğini kısıtlama

Azure Güvenlik Duvarı, bu yapılandırmayı basitleştirmek için bir Azure Kubernetes Service ( `AzureKubernetesService` ) FQDN etiketi sağlar. 

> [!NOTE]
> FQDN etiketi, yukarıda listelenen tüm FQDN 'leri içerir ve otomatik olarak güncel tutulur.
>
> SNAT bağlantı noktası tükenmesi sorunlarını önlemek için Azure Güvenlik duvarında üretim senaryolarında en az 20 ön uç IP 'si kullanmanızı öneririz.

Dağıtımın örnek mimarisi aşağıda verilmiştir:

![Kilitlenmiş topoloji](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* Ortak giriş, güvenlik duvarı filtreleri aracılığıyla akışa zorlandı
  * AKS aracı düğümleri ayrılmış bir alt ağda yalıtılmıştır.
  * [Azure Güvenlik Duvarı](../firewall/overview.md) kendi alt ağına dağıtılır.
  * Bir DNAT kuralı, FW genel IP 'sini LB ön uç IP 'ye çevirir.
* Giden istekler, [Kullanıcı tanımlı bir yol](egress-outboundtype.md) kullanarak aracı düğümlerinden Azure Güvenlik DUVARı iç IP 'ye başlar
  * AKS aracı düğümlerinden gelen istekler, AKS kümesinin dağıtıldığı alt ağa yerleştirilmiş bir UDR 'yi izler.
  * Azure Güvenlik Duvarı, sanal ağdan genel bir IP ön ucunda yer kalmadı
  * Genel internet veya diğer Azure hizmetlerine erişim, güvenlik duvarı ön uç IP adresinden ve bu adresten akışa akar
  * İsteğe bağlı olarak, AKS denetim düzlemi erişimi, güvenlik duvarı genel ön uç IP adresini içeren [API sunucusu tarafından YETKILENDIRILMIŞ IP aralıkları](./api-server-authorized-ip-ranges.md)tarafından korunur.
* İç trafik
  * İsteğe bağlı olarak, [genel bir Load Balancer](load-balancer-standard.md) buna ek olarak, iç trafik Için bir [iç Load Balancer](internal-lb.md) kullanabilirsiniz. Bu, kendi alt ağında da ayırabilirsiniz.


Aşağıdaki adımlar, `AzureKubernetesService` AKS kümesinden giden trafiği kısıtlamak Için Azure Güvenlik duvarının FQDN etiketini kullanır ve genel gelen trafiğin güvenlik duvarı aracılığıyla nasıl yapılandırılacağını gösteren bir örnek sağlar.


### <a name="set-configuration-via-environment-variables"></a>Ortam değişkenleri aracılığıyla yapılandırmayı ayarla

Kaynak oluşturmaları için kullanılacak ortam değişkenleri kümesini tanımlayın.

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>Birden fazla alt ağa sahip bir sanal ağ oluşturma

Biri, biri güvenlik duvarı için olmak üzere iki ayrı alt ağa sahip bir sanal ağ sağlayın. İsteğe bağlı olarak, iç hizmet girişi için de bir tane oluşturabilirsiniz.

![Boş ağ topolojisi](media/limit-egress-traffic/empty-network.png)

Tüm kaynakları barındıracak bir kaynak grubu oluşturun.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

AKS kümesini ve Azure Güvenlik duvarını barındırmak için iki alt ağa sahip bir sanal ağ oluşturun. Her birinin kendi alt ağı olur. AKS ağıyla başlayalım.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>UDR ile Azure Güvenlik Duvarı oluşturma ve ayarlama

Azure Güvenlik Duvarı gelen ve giden kuralları yapılandırılmalıdır. Güvenlik duvarının ana amacı, kuruluşların parçalı giriş ve çıkış trafiği kurallarını AKS kümesi içine ve dışına yapılandırmasına olanak sağlamaktır.

![Güvenlik Duvarı ve UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Kümeniz veya uygulamanız, hedeflerin aynı veya küçük bir alt kümesine yönlendirilmiş çok sayıda giden bağlantı oluşturursa, ön uç IP 'si başına bağlantı noktalarını kullanmaktan kaçınmak için daha fazla güvenlik duvarı ön uç IP 'si gerekebilir.
> Birden çok IP ile Azure Güvenlik Duvarı oluşturma hakkında daha fazla bilgi için [ **buraya** bakın](../firewall/quick-create-multiple-ip-template.md)

Azure Güvenlik Duvarı ön uç adresi olarak kullanılacak standart SKU genel IP kaynağı oluşturun.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Azure Güvenlik duvarı oluşturmak için, CLI-uzantısını kaydedin.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
Daha önce oluşturulan IP adresi artık güvenlik duvarı ön ucunda atanabilir.

> [!NOTE]
> Azure Güvenlik Duvarı 'na genel IP adresinin ayarlanması birkaç dakika sürebilir.
> Ağ kurallarında FQDN 'yi devre dışı bırakmak için DNS proxy 'nin etkin olması gerekir. Güvenlik Duvarı etkinleştirildiğinde, bağlantı noktası 53 üzerinde dinleme yapılır ve DNS isteklerini yukarıda belirtilen DNS sunucusuna iletir. Bu, güvenlik duvarının bu FQDN 'yi otomatik olarak çevirisi için izin verir.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Önceki komut başarılı olduğunda, yapılandırma için güvenlik duvarı ön uç IP adresini daha sonra kaydedin.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> AKS API sunucusuna, [YETKILENDIRILMIŞ IP adresi aralıklarıyla](./api-server-authorized-ip-ranges.md)güvenli erişim kullanırsanız, güvenlik DUVARı genel IP 'SINI yetkili IP aralığına eklemeniz gerekir.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Azure Güvenlik Duvarı için bir atlama ile UDR oluşturma

Azure, Azure alt ağları, sanal ağlar ve şirket içi ağlar arasındaki trafiği otomatik olarak yönlendirir. Azure 'un varsayılan yönlendirmesinde herhangi birini değiştirmek istiyorsanız, bir yol tablosu oluşturarak bunu yapabilirsiniz.

Belirli bir alt ağla ilişkilendirilecek boş bir yol tablosu oluşturun. Yol tablosu, yukarıda oluşturulan Azure Güvenlik duvarı olarak bir sonraki atlamayı tanımlayacaktır. Her alt ağ ile ilişkili sıfır veya bir yol tablosu olabilir.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Azure 'un varsayılan sistem yollarını nasıl geçersiz kılabileceğiniz veya bir alt ağın yol tablosuna nasıl ek yollar ekleyebileceğiniz hakkında [sanal ağ yol tablosu belgelerine](../virtual-network/virtual-networks-udr-overview.md#user-defined) bakın.

### <a name="adding-firewall-rules"></a>Güvenlik duvarı kuralları ekleme

Aşağıda, güvenlik duvarınız üzerinde yapılandırmak için kullanabileceğiniz üç ağ kuralı verilmiştir; Bu kuralları dağıtımınıza göre uyarlamanız gerekebilir. İlk kural TCP üzerinden 9000 numaralı bağlantı noktasına erişime izin verir. İkinci kural UDP üzerinden 1194 ve 123 numaralı bağlantı noktasına erişime izin verir (Azure Çin 21Vianet 'e dağıtıyorsanız, [daha fazlasını](#azure-china-21vianet-required-network-rules)gerektirebilir). Bu kuralların her ikisi de yalnızca kullandığımız Azure bölgesi CıDR 'e giden trafiğe izin verir, bu durumda Doğu ABD. Son olarak, UDP üzerinden 123 numaralı bağlantı noktasını açmak üzere bir üçüncü ağ kuralı ekleyeceğiz `ntp.ubuntu.com` (bir ağ kuralı olarak FQDN eklemek, Azure Güvenlik duvarının belirli özelliklerinden biridir) ve kendi seçeneklerinizi kullanırken uyarlamanız gerekir.

Ağ kurallarını ayarladıktan sonra, `AzureKubernetesService` TCP bağlantı noktası 443 ve bağlantı noktası 80 aracılığıyla erişilebilen tüm gerekli FQDN 'leri içeren bir uygulama kuralı da ekleyeceğiz.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Azure Güvenlik Duvarı hizmeti hakkında daha fazla bilgi edinmek için bkz. [Azure Güvenlik Duvarı belgeleri](../firewall/overview.md) .

### <a name="associate-the-route-table-to-aks"></a>Yol tablosunu AKS ile ilişkilendir

Kümeyi güvenlik duvarıyla ilişkilendirmek için, kümenin alt ağı için ayrılmış alt ağın yukarıda oluşturulan yol tablosuna başvurması gerekir. İlişki, kümenin alt ağının yol tablosunu güncelleştirmek için hem kümeyi hem de güvenlik duvarını tutan sanal ağa bir komut vererek yapılabilir.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Mevcut ağa giden UDR türü ile AKS dağıtma

Artık bir AKS kümesi var olan sanal ağa dağıtılabilir. [Giden türü `userDefinedRouting` ](egress-outboundtype.md)de kullanacağız, bu özellik tüm giden trafiğin güvenlik duvarından zorlanmasını sağlar ve diğer çıkış yolları mevcut olmayacaktır (varsayılan olarak Load Balancer giden tür kullanılabilir).

![aks-Deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Mevcut sanal ağın içinde sağlamaya yönelik erişime sahip bir hizmet sorumlusu oluşturma

Küme kimliği (yönetilen kimlik veya hizmet sorumlusu), AKS tarafından küme kaynakları oluşturmak için kullanılır. Oluşturma zamanında geçirilen bir hizmet sorumlusu, AKS tarafından kullanılan depolama kaynakları, IP 'Ler ve yük dengeleyiciler gibi temel AKS kaynaklarını oluşturmak için kullanılır (Bunun yerine bir [yönetilen kimlik](use-managed-identity.md) de kullanabilirsiniz). Aşağıdaki uygun izinler verilmemişse, AKS kümesini sağlayamayacaksınız.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Şimdi, `APPID` `PASSWORD` önceki komut çıktısı tarafından otomatik olarak oluşturulan hizmet sorumlusu uygulama kimliği ve hizmet sorumlusu parolasıyla aşağıdaki ve altındaki öğesini değiştirin. AKS 'in bu kaynaklara kaynak dağıtabilmesi için, hizmet sorumlusu için izin vermek üzere VNET kaynak KIMLIĞINE başvuracağız.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

[Burada](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)gereken ayrıntılı izinleri kontrol edebilirsiniz.

> [!NOTE]
> Kubernetes kullanan Network eklentisini kullanıyorsanız, Kubernetes kullanan, referencesteps yönlendirme kuralları eklemek için bir yol tablosu gerektirdiğinden, önceden oluşturulmuş yol tablosuna aks hizmet sorumlusu veya yönetilen kimlik izinlerini vermeniz gerekir. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>AKS 'leri dağıtma

Son olarak, AKS kümesi, küme için ayırdığımız mevcut alt ağa dağıtılabilir. İçine dağıtılacak hedef alt ağ, ortam değişkeni ile tanımlanmıştır `$SUBNETID` . `$SUBNETID`Önceki adımlarda değişkeni tanımlamadık. Alt ağ KIMLIĞI için değeri ayarlamak için aşağıdaki komutu kullanabilirsiniz:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

Alt ağda zaten bulunan UDR 'yi kullanmak için giden türünü tanımlayacaksınız. Bu yapılandırma, AKS 'in, yük dengeleyici için kurulum ve IP sağlamasını atlayacak şekilde etkinleştirecektir.

> [!IMPORTANT]
> Sınırlamalar dahil olmak üzere giden tür UDR hakkında daha fazla bilgi için bkz. [**Çıkış giden türü UDR**](egress-outboundtype.md#limitations).


> [!TIP]
> [**Özel küme**](private-clusters.md)gibi küme dağıtımına ek özellikler eklenebilir. 
>
> API sunucusu için [**YETKILENDIRILMIŞ IP aralıklarının**](api-server-authorized-ip-ranges.md) aks ÖZELLIĞI, API sunucusu erişimini yalnızca güvenlik duvarının genel uç noktasına sınırlamak için eklenebilir. Yetkili IP aralıkları özelliği diyagramda isteğe bağlı olarak gösterilir. API sunucusu erişimini sınırlamak için yetkilendirilmiş IP aralığı özelliğini etkinleştirirken, geliştirici araçlarınızın güvenlik duvarının sanal ağından bir sıçrama kutusu kullanması veya tüm geliştirici uç noktalarını yetkilendirilmiş IP aralığına eklemeniz gerekir.

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>API sunucusuna geliştirici erişimini etkinleştirme

Önceki adımda küme için yetkilendirilmiş IP aralıklarını kullandıysanız, API sunucusuna erişmek için Geliştirici araç IP adreslerinizi onaylanan IP aralıklarının AKS kümesi listesine eklemeniz gerekir. Diğer bir seçenek de, güvenlik duvarının sanal ağındaki ayrı bir alt ağ içinde gerekli araçları içeren bir sıçrama kutusu yapılandırmaktır.

Aşağıdaki komutla onaylanan aralığa başka bir IP adresi ekleyin

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 `kubectl`Yeni oluşturduğunuz Kubernetes kümenize bağlanmak üzere yapılandırmak için [az aks Get-Credentials] [az-aks-Get-Credentials] komutunu kullanın. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Ortak hizmeti dağıtma
Artık Hizmetleri açığa çıkarmaya başlayabilir ve uygulamaları bu kümeye dağıtabilirsiniz. Bu örnekte, bir ortak hizmeti kullanıma sunacağız, ancak iç [yük dengeleyici](internal-lb.md)aracılığıyla bir iç hizmeti kullanıma sunmayı da tercih edebilirsiniz.

![Genel hizmet DNAT](media/limit-egress-traffic/aks-create-svc.png)

Aşağıdaki YAML 'yi adlı bir dosyaya kopyalayarak Azure oylama uygulama uygulamasını dağıtın `example.yaml` .

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

Hizmetini çalıştırarak hizmeti dağıtın:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Azure Güvenlik Duvarı 'na bir DNAT kuralı ekleme

> [!IMPORTANT]
> Çıkış trafiğini kısıtlamak ve tüm çıkış trafiğini zorlamak için Kullanıcı tanımlı yol (UDR) oluşturmak üzere Azure Güvenlik Duvarı 'nı kullandığınızda, giriş trafiğine doğru şekilde izin vermek için güvenlik duvarında uygun bir DNAT kuralı oluşturduğunuzdan emin olun. Azure Güvenlik Duvarı 'nı bir UDR ile kullanmak, asimetrik yönlendirme nedeniyle giriş kurulumunu keser. (AKS alt ağının, güvenlik duvarının özel IP adresine giden bir varsayılan yolu varsa, ancak türü: LoadBalancer) ortak yük dengeleyici veya Kubernetes hizmeti kullanıyorsanız bu sorun oluşur. Bu durumda, gelen yük dengeleyici trafiği genel IP adresi aracılığıyla alınır, ancak döndürülen yol güvenlik duvarının özel IP adresinden geçer. Güvenlik duvarı durum bilgisi olduğundan, güvenlik duvarı kurulu bir oturumun farkında olmadığından döndürülen paketi bırakır. Azure Güvenlik duvarını giriş veya hizmet yük dengeleyicinizle tümleştirmeyi öğrenmek için bkz. Azure [güvenlik duvarını azure standart Load Balancer tümleştirme](../firewall/integrate-lb.md).


Gelen bağlantıyı yapılandırmak için, Azure Güvenlik Duvarı 'na bir DNAT kuralı yazılması gerekir. Kümenizin bağlantısını test etmek için, güvenlik duvarı ön uç genel IP adresi için iç hizmet tarafından sunulan iç IP 'ye yönlendirmek üzere bir kural tanımlanır.

Hedef adres, erişilecek güvenlik duvarında bağlantı noktası olarak özelleştirilebilir. Çevrilen adres, iç yük dengeleyicinin IP adresi olmalıdır. Çevrilen bağlantı noktası, Kubernetes hizmetiniz için açığa çıkarılan bağlantı noktası olmalıdır.

Kubernetes hizmeti tarafından oluşturulan yük dengeleyiciye atanan iç IP adresini belirtmeniz gerekir. Şunu çalıştırarak adresi alın:

```bash
kubectl get services
```

Gereken IP adresi, aşağıdakine benzer şekilde dış IP sütununda listelenecektir.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

Hizmetini çalıştırarak hizmet IP 'sini alın:
```bash
SERVICE_IP=$(kubectl get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Şunu çalıştırarak NAT kuralını ekleyin:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Bağlantıyı doğrula

Bağlantıyı doğrulamak için bir tarayıcıda Azure Güvenlik Duvarı ön uç IP adresine gidin.

AKS oylama uygulamasını görmeniz gerekir. Bu örnekte, güvenlik duvarı genel IP 'si idi `52.253.228.132` .


![Ekran görüntüsü, kediler, köpekler ve sıfırlama için düğmeler içeren bir K. oylama uygulamasını gösterir.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Kaynakları temizleme

Azure kaynaklarını temizlemek için AKS kaynak grubunu silin.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, kümenin çıkış trafiğini kısıtlamak istiyorsanız hangi bağlantı noktalarının ve adreslerin izin verdiklerini öğrendiniz. Ayrıca, Azure Güvenlik Duvarı 'nı kullanarak giden trafiğinizi nasıl güvenli hale getirmek istediğinizi gördünüz. 

Gerekirse, [giden tür `userDefinedRoute` belgelerinin](egress-outboundtype.md)ardından trafiği tercih ettiğiniz çıkış çözümünüze iletmek için yukarıdaki adımları genelleştirebilirsiniz.

Küme içindeki ve East-West trafik kısıtlamaları arasında Pod 'nin nasıl iletişim kuracağını kısıtlamak istiyorsanız, [aks 'deki ağ ilkelerini kullanarak Pod arasındaki güvenli trafiği][network-policy]inceleyin.

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
