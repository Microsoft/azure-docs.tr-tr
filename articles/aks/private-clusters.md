---
title: Özel bir Azure Kubernetes hizmet kümesi oluşturma
description: Özel bir Azure Kubernetes hizmeti (AKS) kümesi oluşturmayı öğrenin
services: container-service
ms.topic: article
ms.date: 3/31/2021
ms.openlocfilehash: 76785caedb9ca97d947e83f5aa8ff5b32d827914
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772910"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Özel bir Azure Kubernetes hizmet kümesi oluşturma

Özel kümede, denetim düzlemi veya API sunucusu, [özel Internet belgesi Için RFC1918-Address ayırması](https://tools.ietf.org/html/rfc1918) içinde tanımlanan iç IP adreslerine sahiptir. Özel bir küme kullanarak, API sunucunuz ve düğüm havuzlarınız arasındaki ağ trafiğinin yalnızca özel ağ üzerinde kalmasını sağlayabilirsiniz.

Denetim düzlemi veya API sunucusu, Azure Kubernetes hizmeti (AKS) tarafından yönetilen bir Azure aboneliğinde bulunur. Müşterinin kümesi veya düğüm havuzu müşterinin aboneliğine ait. Sunucu ve küme veya düğüm havuzu, API sunucusu sanal ağındaki [Azure özel bağlantı hizmeti][private-link-service] ve müşterinin aks kümesinin alt ağında kullanıma sunulan özel bir uç nokta aracılığıyla birbirleriyle iletişim kurabilir.

## <a name="region-availability"></a>Bölge kullanılabilirliği

Özel küme, [aks 'in desteklendiği](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)Genel bölgelerde, Azure Kamu 'Da ve Azure Çin 21Vianet bölgelerinde kullanılabilir.

> [!NOTE]
> Azure Kamu siteleri desteklenir, ancak eksik özel bağlantı desteği nedeniyle US Gov Teksas Şu anda desteklenmiyor.

## <a name="prerequisites"></a>Önkoşullar

* Azure CLı sürüm 2.2.0 veya üzeri
* Özel bağlantı hizmeti yalnızca standart Azure Load Balancer desteklenir. Temel Azure Load Balancer desteklenmez.  
* Özel bir DNS sunucusu kullanmak için IP 168.63.129.16 Azure DNS özel DNS sunucusuna yukarı akış DNS sunucusu olarak ekleyin.

## <a name="create-a-private-aks-cluster"></a>Özel AKS kümesi oluşturma

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

AKS kümeniz için bir kaynak grubu oluşturun veya var olan bir kaynak grubunu kullanın.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Varsayılan temel ağ 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
`--enable-private-cluster`, Özel bir küme için zorunlu bir bayrak olur. 

### <a name="advanced-networking"></a>Gelişmiş ağ  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
`--enable-private-cluster`, Özel bir küme için zorunlu bir bayrak olur. 

> [!NOTE]
> Docker Köprüsü CıDR (172.17.0.1/16) alt ağ CıDR ile çakışıyor, Docker köprü adresini uygun şekilde değiştirin.

## <a name="configure-private-dns-zone"></a>Özel DNS bölgeyi yapılandırma 

Özel DNS bölgeyi yapılandırmak için aşağıdaki parametreler yararlanılabilir olabilir.

- "Sistem" varsayılan değerdir. --Private-DNS-Zone bağımsız değişkeni atlanırsa, AKS, düğüm kaynak grubunda bir Özel DNS bölgesi oluşturur.
- "None", AKS 'in bir Özel DNS bölgesi oluşturmayacağı anlamına gelir.  Bu, kendi DNS sunucunuzu yapmanızı ve özel FQDN için DNS çözümlemesini yapılandırmanızı gerektirir.  DNS çözümlemesini yapılandırmazsanız DNS yalnızca aracı düğümleri içinde çözülebilir ve dağıtımdan sonra küme sorunlarına neden olur. 
- "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID", Azure genel bulutu için bu biçimde bir Özel DNS bölgesi oluşturmanızı gerektirir: `privatelink.<region>.azmk8s.io` . Özel DNS bölgenin devam ettiğinin kaynak kimliği gerekir.  Ayrıca, en azından ve rollerinin atandığı bir kullanıcı kimliği veya hizmet sorumlusu gerekir `private dns zone contributor` `vnet contributor` .
- "FQDN-alt etki alanı" yalnızca için alt etki alanı yetenekleri sağlamak üzere "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" ile kullanılabilir `privatelink.<region>.azmk8s.io`

### <a name="prerequisites"></a>Önkoşullar

* AKS önizleme sürümü 0.5.7 veya üzeri
* API sürüm 2020-11-01 veya üzeri

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>Özel DNS bölgesi ile özel bir AKS kümesi oluşturma

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```

### <a name="create-a-private-aks-cluster-with-a-custom-private-dns-zone"></a>Özel bir Özel DNS bölgesi ile özel bir AKS kümesi oluşturma

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```

## <a name="options-for-connecting-to-the-private-cluster"></a>Özel kümeye bağlanma seçenekleri

API sunucusu uç noktasının genel IP adresi yok. API sunucusunu yönetmek için, AKS kümesinin Azure sanal ağına (VNet) erişimi olan bir VM kullanmanız gerekir. Özel kümeye Ağ bağlantısı kurmak için çeşitli seçenekler vardır.

* AKS kümesiyle aynı Azure sanal ağında (VNet) bir VM oluşturun.
* Ayrı bir ağda bir VM kullanın ve [sanal ağ eşlemesi][virtual-network-peering]ayarlayın.  Bu seçenek hakkında daha fazla bilgi için aşağıdaki bölüme bakın.
* Bir [Express Route veya VPN][express-route-or-VPN] bağlantısı kullanın.
* [Aks Run komut özelliğini](#aks-run-command-preview)kullanın.

AKS kümesiyle aynı VNET 'te VM oluşturma en kolay seçenektir.  Express Route ve VPN 'Ler maliyet ekler ve ek ağ karmaşıklığı gerektirir.  Sanal ağ eşlemesi, çakışan aralıklar bulunmadığından emin olmak için ağ CıDR aralıklarını planlamanız gerekir.

### <a name="aks-run-command-preview"></a>AKS Run komutu (Önizleme)

Bugün özel bir kümeye erişmeniz gerektiğinde, bunu küme sanal ağı veya eşlenmiş ağ ya da istemci makine içinde yapmanız gerekir. Bu genellikle makinenizin VPN veya Express Route ile küme sanal ağına veya küme sanal ağında oluşturulacak bir atlama kutusuna bağlanmasını gerektirir. AKS Run komutu, aks API 'SI aracılığıyla bir AKS kümesindeki komutları uzaktan çağırmayı sağlar. Bu özellik, örneğin özel bir küme için uzak bir dizüstü bilgisayardan tam zamanında komutlar yürütmelerine olanak tanıyan bir API sağlar. Bu, aynı RBAC denetimlerini ve özel API sunucusunu korurken ve zorlarken, istemci makinesi küme özel ağında olmadığında, özel bir kümeye hızlı bir şekilde tam zamanında erişim sağlayabilir.

### <a name="register-the-runcommandpreview-preview-feature"></a>`RunCommandPreview`Önizleme özelliğini kaydetme

Yeni komut çalıştırma API 'sini kullanmak için `RunCommandPreview` aboneliğinizdeki Özellik bayrağını etkinleştirmeniz gerekir.

`RunCommandPreview`Özellik bayrağını, aşağıdaki örnekte gösterildiği gibi [az Feature Register] [az-Feature-Register] komutunu kullanarak kaydedin:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "RunCommandPreview"
```

Durumun *kayıtlı* gösterilmesi birkaç dakika sürer. [Az Feature List][az-feature-list] komutunu kullanarak kayıt durumunu doğrulayın:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/RunCommandPreview')].{Name:name,State:properties.state}"
```

Hazırlandığınızda, [az Provider Register][az-provider-register] komutunu kullanarak *Microsoft. Containerservice* kaynak sağlayıcısı kaydını yenileyin:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-run-command"></a>AKS Run komutunu kullanma

Basit komut

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl get pods -n kube-system"
```

Belirli dosyayı ekleyerek bir bildirim dağıtın

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f deployment.yaml
```

Bir tam klasör ekleyerek bir bildirim dağıtın

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f .
```

Bir Helu yüklemesi gerçekleştirin ve belirli değerler bildirimini geçirin

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release -f values.yaml bitnami/nginx" -f values.yaml
```

## <a name="virtual-network-peering"></a>Sanal ağ eşleme

Belirtildiği gibi, sanal ağ eşlemesi özel kümenize erişmenin bir yoludur. Sanal Ağ eşlemesini kullanmak için, sanal ağ ile özel DNS bölgesi arasında bir bağlantı ayarlamanız gerekir.
    
1. Azure portal düğüm kaynak grubuna gidin.  
2. Özel DNS bölgesini seçin.   
3. Sol bölmede **sanal ağ** bağlantısını seçin.  
4. VM 'nin sanal ağını özel DNS bölgesine eklemek için yeni bir bağlantı oluşturun. DNS bölgesi bağlantısının kullanılabilir olması birkaç dakika sürer.  
5. Azure portal, kümenizin sanal ağını içeren kaynak grubuna gidin.  
6. Sağ bölmede sanal ağı seçin. Sanal ağ adı *aks-VNET- \** biçimindedir.  
7. Sol bölmede, eşlemeler ' i **seçin.**  
8. **Ekle**' yi SEÇIN, VM 'nin sanal ağını ekleyin ve ardından eşlemeyi oluşturun.  
9. VM 'nin bulunduğu sanal ağa gidin, **eşlemeler ' i seçin,** aks sanal ağını seçin ve ardından eşlemeyi oluşturun. AKS sanal ağındaki adres aralıkları ve VM 'nin sanal ağ çakışması, eşleme başarısız olur. Daha fazla bilgi için bkz.  [sanal ağ eşlemesi][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Özel DNS ile hub ve bağlı bileşen

[Hub ve bağlı bileşen mimarileri](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) genellikle Azure 'da ağları dağıtmak için kullanılır. Bu dağıtımların çoğunda, bağlı olan sanal ağlarda DNS ayarları, şirket içi ve Azure tabanlı DNS çözümüne izin vermek üzere merkezi bir DNS ileticisine başvuracak şekilde yapılandırılmıştır. Bir AKS kümesini böyle bir ağ ortamına dağıttığınızda, hesaba alınması gereken bazı özel noktalar vardır.

![Özel küme merkezi ve bağlı bileşen](media/private-clusters/aks-private-hub-spoke.png)

1. Varsayılan olarak, özel bir küme sağlandığında, küme tarafından yönetilen kaynak grubunda özel bir uç nokta (1) ve özel bir DNS bölgesi (2) oluşturulur. Küme, API sunucusuyla iletişim kurmak üzere özel uç noktasının IP 'sini çözümlemek için özel bölgedeki bir kayıt kullanır.

2. Özel DNS bölgesi yalnızca küme düğümlerinin eklendiği sanal ağa bağlanır (3). Bu, Özel uç noktanın yalnızca bağlı VNet 'teki konaklarla çözümlenebileceği anlamına gelir. VNet üzerinde özel DNS yapılandırılmadığından (varsayılan) senaryolarda, bu, bağlantı nedeniyle özel DNS bölgesindeki kayıtları çözümleyebilecek DNS için 168.63.129.16 adresinde ana bilgisayar olarak sorun olmadan çalışmaktadır.

3. Kümenizi içeren VNet 'in özel DNS ayarları (4) olduğu senaryolarda, özel DNS bölgesi özel DNS çözümleyicilerine (5) sahip olan VNet 'e bağlanmadığı takdirde küme dağıtımı başarısız olur. Bu bağlantı, Küme sağlama sırasında veya olay tabanlı dağıtım mekanizmaları (örneğin, Azure Event Grid ve Azure Işlevleri) kullanılarak bölge oluşturma algılandıktan sonra Otomasyon yoluyla, özel bölge oluşturulduktan sonra el ile oluşturulabilir.

> [!NOTE]
> [Kubernetes kullanan ile kendi yol tablonuzu getir](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) ve kendi DNS 'Nizi özel kümeyle getir ' i kullanıyorsanız, küme oluşturma işlemi başarısız olur. Oluşturma işleminin başarılı olması için, küme oluşturma işlemi başarısız olduktan sonra düğüm kaynak grubundaki [RouteTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) 'ı alt ağ ile ilişkilendirmeniz gerekir.

## <a name="limitations"></a>Sınırlamalar 
* IP yetkili aralıkları özel API sunucusu uç noktasına uygulanamıyor, yalnızca ortak API sunucusu için geçerlidir
* [Azure özel bağlantı hizmeti sınırlamaları][private-link-service] özel kümeler için geçerlidir.
* Özel kümelerle Azure DevOps Microsoft tarafından barındırılan aracılar için destek yoktur. [Şirket içinde barındırılan aracıları](/azure/devops/pipelines/agents/agents?tabs=browser)kullanmayı göz önünde bulundurun. 
* Azure Container Registry özel AKS ile çalışmak üzere etkinleştirmesi gereken müşteriler için, Container Registry sanal ağı aracı kümesi sanal ağıyla eşlenmelidir.
* Mevcut AKS kümelerini özel kümelere dönüştürme desteği yok
* Müşteri alt ağındaki özel uç noktasını silmek veya değiştirmek kümenin çalışmayı durdurmasına neden olur. 
* Müşteriler, kendi DNS sunucularındaki bir kaydı güncelleştirdikten sonra, bu FID 'ler, yeniden başlatılana kadar geçişten sonra apıver FQDN 'sini daha eski IP 'ye çözeceklerdir. Müşterilerin, denetim düzlemi geçişinden sonra hostNetwork pods ve default-DNSPolicy pods ' i yeniden başlatması gerekir.
* Denetim düzlemine yönelik bakım durumunda [aks IP](./limit-egress-traffic.md) 'niz değişebilir. Bu durumda, özel DNS sunucunuzdaki API sunucusu özel IP 'sine işaret eden bir kaydı güncelleştirmeniz ve hostnetwork kullanarak tüm özel POD veya dağıtımları yeniden başlatmanız gerekir.

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
