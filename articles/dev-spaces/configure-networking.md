---
title: Farklı ağ topolojilerinde Azure Dev Spaces için ağ yapılandırma
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Azure Kubernetes hizmetlerinde Azure Dev Spaces çalıştırmaya yönelik ağ gereksinimlerini açıklar
keywords: Azure Dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, CNı, kubenet, SDN, ağ
ms.openlocfilehash: 09114ab13555cbf9ef42b37c86ffb76a8fe3ab3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91970347"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Farklı ağ topolojilerinde Azure Dev Spaces için ağ yapılandırma

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces, varsayılan ağ yapılandırmasıyla Azure Kubernetes Service (AKS) kümelerinde çalışır. Kümeyi bir güvenlik duvarının arkasına yerleştirme, ağ güvenlik gruplarını kullanma veya ağ ilkeleri kullanma gibi AKS kümenizin ağ yapılandırmasını değiştirmek istiyorsanız, Azure Dev Spaces çalıştırmak için ek hususlar eklemeniz gerekir.

![Sanal ağ yapılandırması](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Sanal ağ veya alt ağ yapılandırması

AKS kümeniz, AKS kümeniz için giriş veya çıkış trafiğini kısıtlamak üzere farklı bir sanal ağ veya alt ağ yapılandırmasına sahip olabilir. Örneğin, kümeniz Azure Güvenlik Duvarı gibi bir güvenlik duvarının arkasında veya ağ trafiğini kısıtlamak için ağ güvenlik grupları veya özel roller kullanıyor olabilir. [GitHub üzerinde Azure dev Spaces örnek deposunda][sample-repo]örnek bir ağ yapılandırması bulabilirsiniz.

Azure Dev Spaces, giriş *ve çıkış* ağ trafiği için belirli gereksinimlere ve *yalnızca* giriş trafiğini içerir. Aks kümeniz için trafiği kısıtlayan bir sanal ağ veya alt ağ yapılandırmasına sahip bir AKS kümesinde Azure Dev Spaces kullanıyorsanız, Azure Dev Spaces düzgün şekilde çalışması için yalnızca aşağıdaki giriş ve çıkış trafiği gereksinimlerini izlemeniz gerekir.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Giriş ve çıkış ağ trafiği gereksinimleri

Azure Dev Spaces aşağıdaki FQDN 'Ler için giriş ve çıkış trafiğine ihtiyaç duyuyor:

| FQDN                       | Bağlantı noktası       | Kullanın      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Azure Dev Spaces için Docker görüntülerini çekmek için |
| gcr.io                     | HTTPS: 443 | Azure Dev Spaces için hele görüntülerini çekmek için |
| storage.googleapis.com     | HTTPS: 443 | Azure Dev Spaces için hele görüntülerini çekmek için |

Güvenlik duvarınızı veya güvenlik yapılandırmanızı, yukarıdaki FQDN 'Ler ve [Azure dev Spaces altyapı hizmetlerinden][service-tags]gelen ve giden ağ trafiğine izin verecek şekilde güncelleştirin. Örneğin, ağınızı güvenli hale getirmek için bir güvenlik duvarı kullanıyorsanız yukarıdaki FQDN 'Ler güvenlik duvarının uygulama kuralına eklenmelidir ve Azure Dev Spaces hizmet etiketi de [güvenlik duvarına eklenmelidir][firewall-service-tags]. Bu etki alanlarına gelen ve giden trafiğe izin vermek için güvenlik duvarında bu güncelleştirmelerin her ikisi de gereklidir.

### <a name="ingress-only-network-traffic-requirements"></a>Yalnızca ağ trafiği gereksinimlerini giriş

Azure Dev Spaces, Kubernetes ad alanı düzeyinde yönlendirme sağlar ve kendi FQDN 'sini kullanarak hizmetlere genel erişim sağlar. Bu özelliklerin her ikisinin de çalışması için, güvenlik duvarınızı veya ağ yapılandırmanızı, kümenizdeki Azure Dev Spaces giriş denetleyicisinin dış IP adresine genel giriş sağlamak üzere güncelleştirin. Alternatif olarak, güvenlik duvarınızın genel IP 'sini iç yük dengeleyicinizin IP 'si ile dönüştürmek için bir [iç yük dengeleyici][aks-internal-lb] oluşturup güvenlik DUVARıNıZA bir NAT kuralı ekleyebilirsiniz. Özel bir giriş denetleyicisi oluşturmak için [traefik][traefik-ingress] veya [NGINX][nginx-ingress] de kullanabilirsiniz.

## <a name="aks-cluster-network-requirements"></a>AKS kümesi ağ gereksinimleri

AKS, bir kümede yer alan ve bir pod 'tan çıkış trafiği arasındaki giriş ve çıkış trafiğini denetlemek için [ağ ilkelerini][aks-network-policies] kullanmanıza olanak sağlar. Azure Dev Spaces, giriş *ve çıkış* ağ trafiği için belirli gereksinimlere ve *yalnızca* giriş trafiğini içerir. Aks ağ ilkeleriyle bir AKS kümesinde Azure Dev Spaces kullanıyorsanız, Azure Dev Spaces düzgün şekilde çalışması için yalnızca aşağıdaki giriş ve çıkış trafiği gereksinimlerini izlemeniz gerekir.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Giriş ve çıkış ağ trafiği gereksinimleri

Azure Dev Spaces, hata ayıklama için kümenizdeki bir geliştirme alanındaki bir pod ile doğrudan iletişim kurmanızı sağlar. Bu özelliğin çalışması için, [bölgeye göre farklılık][service-tags]gösteren Azure dev Spaces altyapısının IP adreslerine giriş ve çıkış iletişimine izin veren bir ağ ilkesi ekleyin.

### <a name="ingress-only-network-traffic-requirements"></a>Yalnızca ağ trafiği gereksinimlerini giriş

Azure Dev Spaces ad alanları arasında oluşan yönlendirme sağlar. Örneğin, Azure Dev Spaces etkin olan ad alanları üst/alt ilişkisine sahip olabilir ve bu, üst ve alt ad alanları arasında ağ trafiğinin yönlendirilmesine olanak tanır. Azure Dev Spaces Ayrıca kendi FQDN 'sini kullanarak hizmet uç noktalarını kullanıma sunar. Hizmetleri gösterme ve ad alanı düzeyinde yönlendirmeyi nasıl etkilediği farklı yollar yapılandırmak için bkz. [farklı uç nokta seçenekleri kullanma][endpoint-options].

## <a name="using-azure-cni"></a>Azure CNı kullanma

Varsayılan olarak, aks kümeleri, Azure dev Spaces ile birlikte çalışarak ağ için [Kubernetes kullanan][aks-kubenet] kullanacak şekilde yapılandırılmıştır. AKS kümenizi [Azure Container Networking Interface (CNI)][aks-cni]kullanacak şekilde de yapılandırabilirsiniz. Aks kümenizde Azure CNI ile Azure dev Spaces kullanmak için, sanal ağ ve alt ağ adres alanları, Azure dev Spaces tarafından dağıtılan Pod 'ler için 10 özel IP adresine sahip olacak şekilde izin verir. Özel IP adreslerine izin verme hakkında daha fazla ayrıntı, [aks Azure CNI belgelerinde][aks-cni-ip-planning]bulunabilir.

## <a name="using-api-server-authorized-ip-ranges"></a>API sunucusu yetkilendirilmiş IP aralıklarını kullanma

AKS kümeleri, özel sanal ağlar veya [YETKILENDIRILMIŞ IP aralıklarını kullanarak API sunucusuna erişimin güvenliğini sağlamak][aks-ip-auth-ranges]gibi kümelerinizle etkileşime girebilen ek güvenliği yapılandırmanıza olanak tanır. Kümenizi [oluştururken][aks-ip-auth-range-create] bu ek güvenliği kullanırken Azure dev Spaces kullanmak için [bölgenize göre ek aralıklara izin vermeniz][service-tags]gerekir. Ayrıca, var olan bir kümeyi bu ek aralıklara izin verecek şekilde [güncelleştirebilirsiniz][aks-ip-auth-range-update] . Ayrıca, API sunucunuza bağlanmak üzere hata ayıklama için AKS kümenize bağlanan herhangi bir geliştirme makinesi IP adresine izin vermeniz gerekir.

## <a name="using-aks-private-clusters"></a>AKS özel kümelerini kullanma

Şu anda, [aks özel kümeleriyle][aks-private-clusters]Azure dev Spaces desteklenmez.

## <a name="using-different-endpoint-options"></a>Farklı uç nokta seçeneklerini kullanma

Azure Dev Spaces, AKS üzerinde çalışan hizmetlerinize yönelik uç noktaları kullanıma sunma seçeneğine sahiptir. Kümenizde Azure Dev Spaces etkinleştirilirken, kümeniz için uç nokta türünü yapılandırmak için aşağıdaki seçeneklere sahip olursunuz:

* Varsayılan olan *genel* bir uç nokta, genel IP adresi ile bir giriş denetleyicisi dağıtır. Genel IP adresi kümenin DNS 'sine kaydedilir ve bir URL kullanarak hizmetlerinize genel erişim sağlar. Kullanarak bu URL 'YI görebilirsiniz `azds list-uris` .
* *Özel* bir uç nokta, özel bir IP adresi olan bir giriş denetleyicisi dağıtır. Özel bir IP adresi ile, kümenizin yük dengeleyiciye yalnızca kümenin sanal ağı içinden erişilebilir. Yük dengeleyicinin özel IP adresi kümenin DNS 'sine kaydedilir, böylece kümenin sanal ağı içindeki hizmetlere bir URL kullanılarak erişilebilir. Kullanarak bu URL 'YI görebilirsiniz `azds list-uris` .
* Uç nokta seçeneği için *hiçbiri* ayarı, giriş denetleyicisinin dağıtılmamasını sağlar. Giriş denetleyicisi dağıtılmadı, [Azure dev Spaces yönlendirme özellikleri][dev-spaces-routing] çalışmayacak. İsteğe bağlı olarak, [traefik][traefik-ingress] veya [NGINX][nginx-ingress]kullanarak kendi giriş denetleyicisi çözümünüzü uygulayabilir, bu da yönlendirme yeteneklerinin yeniden çalışmasına imkan sağlar.

Uç nokta seçeneğinizi yapılandırmak için, kümenizde Azure Dev Spaces etkinleştirirken *-e* veya *--uç noktasını* kullanın. Örnek:

> [!NOTE]
> Uç nokta seçeneği için Azure CLı sürüm 2.2.0 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>İstemci gereksinimleri

Azure Dev Spaces, hata ayıklama için AKS kümeniz ile iletişim kurmak için Azure Dev Spaces CLı uzantısı, Visual Studio Code uzantısı ve Visual Studio uzantısı gibi istemci tarafı araçları kullanır. Azure Dev Spaces istemci tarafı araçları 'nı kullanmak için geliştirme makinelerinden [Azure dev Spaces altyapısına][dev-spaces-allow-infrastructure]giden trafiğe izin verin. [API sunucusu YETKILENDIRILMIŞ IP aralıklarını][auth-range-section]KULLANıYORSANıZ, API sunucunuza bağlanmak için hata ayıklama için aks kümenize bağlanan herhangi bir GELIŞTIRME makinesi IP adresine de izin vermeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Dev Spaces nasıl çalıştığı hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure Dev Spaces nasıl çalışır?](how-dev-spaces-works.md)

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags