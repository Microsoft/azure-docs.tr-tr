---
title: Çıkış trafiği için statik IP kullan
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) kümesinde çıkış trafiği için statik bir genel IP adresi oluşturma ve kullanma hakkında bilgi edinin
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 7cff3f5d66bb9872a0c949c6237150f8b69c9fa7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773018"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-with-a-basic-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki *temel* SKU yük dengeleyiciye çıkış trafiği için statik BIR genel IP adresi kullanın

Varsayılan olarak, bir Azure Kubernetes Service (AKS) kümesinden çıkış IP adresi rastgele atanır. Örneğin, dış hizmetlere erişim için bir IP adresi belirlemeniz gerektiğinde bu yapılandırma ideal değildir. Bunun yerine, hizmet erişimi için bir izin öğesine eklenmek üzere statik bir IP adresi atamanız gerekebilir.

Bu makalede, bir AKS kümesinde çıkış trafiği ile kullanmak üzere statik bir genel IP adresi oluşturma ve kullanma işlemlerinin nasıl yapılacağı gösterilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, Azure temel Load Balancer kullandığınızı varsaymaktadır.  [Azure Standart Load Balancer](../load-balancer/load-balancer-overview.md)kullanmanızı öneririz. [aks çıkış trafiğini denetlemek](./limit-egress-traffic.md)için daha gelişmiş özellikler de kullanabilirsiniz.

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

> [!IMPORTANT]
> Bu makalede, tek düğümlü havuz ile *temel* SKU yük dengeleyici kullanılmaktadır. *Temel* SKU yük dengeleyici birden çok düğüm havuzlarıyla desteklenmediğinden, bu yapılandırma birden çok düğüm havuzu için kullanılamaz. *Standart* SKU yük dengeleyiciyi kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes SERVICE (aks) Içinde genel standart Load Balancer kullanma][slb] .

## <a name="egress-traffic-overview"></a>Çıkış trafiğine genel bakış

Bir AKS kümesinden giden trafik [Azure Load Balancer kuralları][outbound-connections]izler. Türündeki ilk Kubernetes hizmeti oluşturulmadan önce `LoadBalancer` , BIR AKS kümesindeki aracı düğümleri hiçbir Azure Load Balancer havuzunun parçası değildir. Bu yapılandırmada, düğümlerin örnek düzeyi genel IP adresi yok. Azure, giden akışı, yapılandırılamaz veya belirleyici olmayan bir ortak kaynak IP adresine çevirir.

Türü bir Kubernetes hizmeti oluşturulduktan sonra `LoadBalancer` , aracı düğümleri bir Azure Load Balancer havuzuna eklenir. Load Balancer temel, dış akışlar için birden çok (genel) IP ön uçları aday olduğunda giden akışlar için kullanılacak tek bir ön uç seçer. Bu seçim yapılandırılamaz ve seçim algoritmasını rastgele olacak şekilde göz önünde bulundurmanız gerekir. Bu genel IP adresi yalnızca söz konusu kaynağın kullanım ömrü için geçerlidir. Kubernetes LoadBalancer hizmetini silerseniz ilişkili yük dengeleyici ve IP adresi de silinir. Belirli bir IP adresi atamak veya yeniden dağıtılan Kubernetes Hizmetleri için bir IP adresi korumak istiyorsanız statik bir genel IP adresi oluşturabilir ve kullanabilirsiniz.

## <a name="create-a-static-public-ip"></a>Statik genel IP oluşturma

[Az aks Show][az-aks-show] komutuyla kaynak grubu adını alın ve `--query nodeResourceGroup` sorgu parametresini ekleyin. Aşağıdaki örnek, *Myresourcegroup* kaynak grubu adı altında *Myakscluster* adlı aks kümesi için düğüm kaynak grubunu alır:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Şimdi [az Network public IP Create][az-network-public-ip-create] komutuyla bir STATIK genel IP adresi oluşturun. Önceki komutta elde edilen düğüm kaynak grubu adını ve sonra da *Myakspublicıp* gibi IP adresi kaynağı için bir ad belirtin:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi IP adresi gösterilir:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

Daha sonra [az Network public-IP List][az-network-public-ip-list] komutunu kullanarak genel IP adresini alabilirsiniz. Düğüm kaynak grubunun adını belirtin ve sonra aşağıdaki örnekte gösterildiği gibi *IPAddress* için sorgulama yapın:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Statik IP ile bir hizmet oluşturma

Statik genel IP adresine sahip bir hizmet oluşturmak için, `loadBalancerIP` özelliği ve statik genel IP adresinin değerini YAML bildirimine ekleyin. `egress-service.yaml`Aşağıdaki YAML 'de adlı bir dosya oluşturun ve kopyalayın. Önceki adımda oluşturulan kendi genel IP adresinizi sağlayın.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Komutuyla hizmeti ve dağıtımı oluşturun `kubectl apply` .

```console
kubectl apply -f egress-service.yaml
```

Bu hizmet Azure Load Balancer yeni bir ön uç IP 'yi yapılandırır. Yapılandırılmış başka bir IP yoksa, **Tüm** çıkış trafiği artık bu adresi kullanmalıdır. Azure Load Balancer birden çok adres yapılandırıldığında, bu genel IP adreslerinden herhangi biri giden akışlar için aday olur ve rastgele bir seçilir.

## <a name="verify-egress-address"></a>Çıkış adresini doğrula

Statik ortak IP adresinin kullanıldığını doğrulamak için, gibi DNS arama hizmeti kullanabilirsiniz `checkip.dyndns.org` .

Başlangıç ve temel bir *deni* Pod 'a iliştirme:

```console
kubectl run -it --rm aks-ip --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Kapsayıcının içinden bir Web sitesine erişmek için, `apt-get` kapsayıcıya yüklemek için kullanın `curl` .

```console
apt-get update && apt-get install curl -y
```

Artık *checkip.dyndns.org* sitesine erişmek için kıvrımlı kullanın. Çıkış IP adresi, aşağıdaki örnek çıktıda gösterildiği gibi görüntülenir. Bu IP adresi, loadBalancer hizmeti için oluşturulan ve tanımlanan statik genel IP adresiyle eşleşir:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Load Balancer birden çok genel IP adresinin korunmasından kaçınmak için, bunun yerine bir giriş denetleyicisi kullanabilirsiniz. Giriş denetleyicileri SSL/TLS sonlandırma, URI yeniden için destek ve yukarı akış SSL/TLS şifrelemesi gibi ek avantajlar sağlar. Daha fazla bilgi için bkz. [AKS 'de temel giriş denetleyicisi oluşturma][ingress-aks-cluster].

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[az-aks-show]: /cli/azure/aks#az_aks_show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[slb]: load-balancer-standard.md
