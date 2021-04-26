---
title: Azure Kubernetes hizmetinde (AKS) API sunucusu yetkilendirilmiş IP aralıkları
description: Azure Kubernetes Service (AKS) ' de API sunucusuna erişim için bir IP adresi aralığı kullanarak kümenizin güvenliğini nasıl sağlayacağınızı öğrenin
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 8fca3fe61e26a031e6ea09692c9ba0781bfca21f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769652"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki yetkili IP adresi aralıklarını kullanarak API sunucusuna güvenli erişim

Kubernetes 'de, API sunucusu, küme içinde kaynak oluşturmak veya düğüm sayısını ölçeklendirmek gibi eylemleri gerçekleştirmek için istekleri alır. API sunucusu, bir küme ile etkileşimde bulunmak ve yönetmek için merkezi bir yoldur. Küme güvenliğini artırmak ve saldırıları en aza indirmek için, API sunucusuna yalnızca sınırlı bir IP adresi aralığı kümesinden erişilebilir olması gerekir.

Bu makalede, API sunucusu yetkilendirilmiş IP adresi aralıklarının hangi IP adreslerinin ve Cıdrs 'nin denetim düzlemine erişebileceğini sınırlamak için nasıl kullanılacağı gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, Azure CLı kullanarak bir AKS kümesinin nasıl oluşturulacağı gösterilmektedir.

Azure CLı sürüm 2.0.76 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

### <a name="limitations"></a>Sınırlamalar

API sunucusu yetkilendirilmiş IP aralıkları özelliği aşağıdaki sınırlamalara sahiptir:
- API sunucusu yetkilendirilmiş IP adresi aralıklarının ön izleme 2019 ' de önizleme dışına taşındıktan sonra oluşturulan kümelerde, API sunucusu yetkilendirilmiş IP adresi aralıkları yalnızca *Standart* SKU yük dengeleyicide desteklenir. Yapılandırılmış *temel* SKU yük dengeleyiciye ve API sunucusu yetkilendirilmiş IP adresi aralıklarına sahip mevcut kümeler, olduğu gibi çalışmaya devam eder, ancak *Standart* bir SKU yük dengeleyiciye geçirilemez. Bu mevcut kümeler, Kubernetes sürümü veya denetim düzlemi yükseltildiyse de çalışmaya devam edecektir. API sunucusu yetkilendirilmiş IP adresi aralıkları özel kümeler için desteklenmez.
- Bu özellik, [düğüm başına genel IP](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools)kullanan kümelerle uyumlu değildir.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API sunucusu yetkilendirilmiş IP aralıklarına genel bakış

Kubernetes API sunucusu, temeldeki Kubernetes API 'Lerinin nasıl açığa çıkmasıdır. Bu bileşen, `kubectl` veya Kubernetes panosu gibi yönetim araçları için etkileşim sağlar. AKS, adanmış bir API sunucusuyla tek kiracılı bir küme denetim düzlemi sağlar. Varsayılan olarak, API sunucusuna genel bir IP adresi atanır ve Kubernetes rol tabanlı erişim denetimi (Kubernetes RBAC) veya Azure RBAC kullanarak erişimi kontrol etmelisiniz.

Genel olarak erişilebilen AKS denetim düzlemi/API sunucusuna erişimi güvenli hale getirmek için, yetkilendirilmiş IP aralıklarını etkinleştirebilir ve kullanabilirsiniz. Bu yetkili IP aralıkları yalnızca tanımlı IP adresi aralıklarının API sunucusuyla iletişim kurmasına izin verir. Bu yetkili IP aralıklarının parçası olmayan bir IP adresinden API sunucusuna yapılan istek engellenir. Kullanıcıları ve istediği eylemleri yetkilendirmek için Kubernetes RBAC veya Azure RBAC kullanmaya devam edin.

API sunucusu ve diğer küme bileşenleri hakkında daha fazla bilgi için bkz. [AKS Için Kubernetes temel kavramları][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>API sunucusu yetkilendirilmiş IP aralıkları etkin olan bir AKS kümesi oluşturma

[Az aks Create][az-aks-create] ' i kullanarak bir küme oluşturun ve *`--api-server-authorized-ip-ranges`* yetkili IP adresi aralıklarının listesini sağlamak için parametresini belirtin. Bu IP adresi aralıkları genellikle şirket içi ağlarınızda veya genel IP 'Lerde kullanılan adres aralıklarından oluşur. Bir CıDR aralığı belirttiğinizde, aralıktaki ilk IP adresi ile başlayın. Örneğin, *137.117.106.90/29* geçerli bir aralıktır, ancak ARALıKTAKI ilk IP adresini *137.117.106.88/29* gibi belirttiğinizden emin olun.

> [!IMPORTANT]
> Varsayılan olarak, kümeniz, giden ağ geçidini yapılandırmak için kullanabileceğiniz [Standart SKU yük dengeleyiciyi][standard-sku-lb] kullanır. Küme oluşturma sırasında API sunucusu yetkilendirilmiş IP aralıklarını etkinleştirdiğinizde, belirttiğiniz aralıklara ek olarak kümenizin genel IP 'sine de varsayılan olarak izin verilir. İçin *""* veya hiçbir değer belirtmezseniz *`--api-server-authorized-ip-ranges`* , API sunucusu yetkilendirilmiş IP aralıkları devre dışı bırakılır. PowerShell kullanıyorsanız, *`--api-server-authorized-ip-ranges=""`* ayrıştırma sorunlarından kaçınmak için (eşittir işareti ile) öğesini kullanın.

Aşağıdaki örnek, API sunucusu yetkilendirilmiş IP aralıkları etkin olan *Myresourcegroup* adlı kaynak grubunda *Myakscluster* adlı tek düğümlü bir küme oluşturur. İzin verilen IP adresi aralıkları *73.140.245.0/24*' dir:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Bu aralıkları bir izin verilenler listesine eklemeniz gerekir:
> - Güvenlik Duvarı genel IP adresi
> - Kümeyi yönettiğiniz ağları temsil eden herhangi bir Aralık
> - AKS kümenizde Azure Dev Spaces kullanıyorsanız, [bölgeniz temelinde ek aralıklara][dev-spaces-ranges]izin vermeniz gerekir.
>
> Belirtebileceğiniz IP aralıklarının sayısı için üst sınır 200 ' dir.
>
> Kuralların yayılması 2 dakika kadar sürebilir. Lütfen bağlantıyı sınarken bu saate kadar izin verin.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Standart SKU yük dengeleyici için giden IP 'Leri belirtin

Bir AKS kümesi oluştururken, küme için giden IP adreslerini veya öneklerini belirtirseniz, bu adreslere veya öneklere de izin verilir. Örnek:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

Yukarıdaki örnekte, parametresinde belirtilen tüm IP 'lerde, *`--load-balancer-outbound-ip-prefixes`* parametresindeki IP 'ler ile birlikte izin verilir *`--api-server-authorized-ip-ranges`* .

Bunun yerine, *`--load-balancer-outbound-ip-prefixes`* giden yük DENGELEYICI IP öneklerine izin vermek için parametresini belirtebilirsiniz.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Standart SKU yük dengeleyicisinin yalnızca giden genel IP 'si için izin ver

Küme oluşturma sırasında API sunucusu yetkilendirilmiş IP aralıklarını etkinleştirdiğinizde, kümenizin standart SKU 'SU yük dengeleyiciye giden genel IP 'ye Ayrıca belirttiğiniz aralıklara ek olarak varsayılan olarak izin verilir. Standart SKU yük dengeleyicinin yalnızca giden genel IP 'ye izin vermek için, parametreyi belirtirken *0.0.0.0/32* kullanın *`--api-server-authorized-ip-ranges`* .

Aşağıdaki örnekte, yalnızca standart SKU yük dengeleyicisine giden genel IP 'ye izin verilir ve API sunucusuna yalnızca küme içindeki düğümlerden erişebilirsiniz.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Kümenin API sunucusu yetkilendirilmiş IP aralıklarını güncelleştirme

Mevcut bir kümede API sunucusu yetkilendirilmiş IP aralıklarını güncelleştirmek için [az aks Update][az-aks-update] komutunu kullanın ve *`--api-server-authorized-ip-ranges`* ,--yük dengeleyici-giden-IP-öneklerini *, *`--load-balancer-outbound-ips`* , veya--Load-dengeleyici-giden-IP-önekleri* parametrelerini kullanın.

Aşağıdaki örnek, *Myresourcegroup* adlı kaynak grubunda *Myakscluster* adlı kümede bulunan API sunucusu yetkili IP aralıklarını güncelleştirir. Yetkilendirmek için IP adresi aralığı *73.140.245.0/24*' dir:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Yalnızca standart SKU yük dengeleyicisinin genel IP 'sini belirtmek için parametresini belirtirken *0.0.0.0/32* ' i de kullanabilirsiniz *`--api-server-authorized-ip-ranges`* .

## <a name="disable-authorized-ip-ranges"></a>Yetkili IP aralıklarını devre dışı bırak

Yetkili IP aralıklarını devre dışı bırakmak için [az aks Update][az-aks-update] kullanın ve API sunucusu yetkilendirilmiş IP aralıklarını devre dışı bırakmak için boş bir Aralık belirtin. Örnek:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>Var olan yetkili IP aralıklarını bul

Yetkilendirilmiş IP aralıklarını bulmak için [az aks Show][az-aks-show] kullanın ve kümenin adını ve kaynak grubunu belirtin. Örnek:

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Azure portal kullanarak yetkilendirilmiş IP aralıklarını güncelleştirme, devre dışı bırakma ve bulma

Yetkili IP aralıklarını eklemek, güncelleştirmek, bulmak ve devre dışı bırakmak için yukarıdaki işlemler Azure portal de gerçekleştirilebilir. Erişmek için, küme kaynağınızın menü dikey penceresinde **Ayarlar** ' ın altında bulunan **ağ** ' a gidin.

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="Bir tarayıcıda, küme kaynağının ağ ayarları Azure portal sayfasını gösterir. ' Belirtilen IP aralığını ayarla ' ve ' belirtilen IP aralıkları ' seçenekleri vurgulanır.":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>IP 'umu dahil etmek için nasıl bulunur `--api-server-authorized-ip-ranges` ?

API sunucusuna erişmek için, dağıtım makinelerinizi, araçları veya Otomasyon IP adreslerini onaylanan IP aralıklarının AKS kümesi listesine eklemeniz gerekir. 

Diğer bir seçenek de, güvenlik duvarının sanal ağındaki ayrı bir alt ağ içinde gerekli araçları içeren bir sıçrama kutusu yapılandırmaktır. Bu, ortamınızın ilgili ağla bir güvenlik duvarı olduğunu varsayar ve güvenlik duvarını yetkilendirme aralıklarına eklemiş olursunuz. Benzer şekilde, AKS alt ağından bir güvenlik duvarı alt ağına Zorlamalı tünel varsa, küme alt ağındaki sıçrama kutusunun çok iyi olmasını sağlayabilirsiniz.

Aşağıdaki komutla onaylanan aralıklara başka bir IP adresi ekleyin.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> Yukarıdaki örnek, API sunucusu yetkilendirilmiş IP aralıklarını kümeye ekler. Yetkili IP aralıklarını devre dışı bırakmak için az aks Update kullanın ve boş bir "" aralığı belirtin. 

Diğer bir seçenek de, genel IPv4 adresini almak için Windows sistemlerinde aşağıdaki komutun kullanılması veya [IP adresinizi bulma](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address)bölümündeki adımları kullanmanız olabilir.

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

Internet tarayıcısında "IP adresim nedir?" öğesini arayarak bu adresi de bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, API sunucusu yetkilendirilmiş IP aralıklarını etkinleştirdiniz. Bu yaklaşım, güvenli bir AKS kümesini nasıl çalıştıracağınızı gösteren bir parçasıdır.

Daha fazla bilgi için bkz. [aks 'teki uygulamalar ve kümeler Için güvenlik kavramları][concepts-security] ve [aks 'de küme güvenliği ve yükseltmeleri için en iyi yöntemler][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
