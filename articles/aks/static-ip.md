---
title: Yük Dengeleyici ile statik IP kullanma
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) yük dengeleyicisiyle statik bir IP adresi oluşturmayı ve kullanmayı öğrenin.
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.openlocfilehash: bb1e5691027a4bd86b57390e12259ac165ca9ed8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769526"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Azure Kubernetes Service (AKS) yük dengeleyicisiyle statik bir genel IP adresi ve DNS etiketi kullanın

Varsayılan olarak, bir AKS kümesi tarafından oluşturulan bir yük dengeleyici kaynağına atanan genel IP adresi yalnızca söz konusu kaynağın kullanım ömrü için geçerlidir. Kubernetes hizmetini silerseniz ilişkili yük dengeleyici ve IP adresi de silinir. Belirli bir IP adresi atamak veya yeniden dağıtılan Kubernetes Hizmetleri için bir IP adresi korumak istiyorsanız statik bir genel IP adresi oluşturabilir ve kullanabilirsiniz.

Bu makalede, statik bir genel IP adresi oluşturma ve Kubernetes hizmetinize atama işlemlerinin nasıl yapılacağı gösterilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

Bu makalede *Standart* SKU 'su yük dengeleyiciye sahip *Standart* SKU IP 'si kullanımı ele alınmaktadır. Daha fazla bilgi için bkz. [Azure 'Da IP adresi türleri ve ayırma yöntemleri][ip-sku].

## <a name="create-a-static-ip-address"></a>Statik IP adresi oluşturma

[Az Network public IP Create][az-network-public-ip-create] komutuyla bir STATIK genel IP adresi oluşturun. Aşağıdaki, *Myresourcegroup* kaynak grubunda *Myakspublicıp* adlı bir statik IP kaynağı oluşturur:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> AKS kümenizde *temel* bir SKU yük dengeleyicisi kullanıyorsanız, ortak IP tanımlarken *SKU* parametresi için *temel* ' yı kullanın. Yalnızca *temel* SKU 'ları *temel* SKU yük dengeleyicisiyle çalışır ve yalnızca *Standart* SKU 'ları *Standart* SKU yük dengeleyicileri ile çalışır. 

Aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi IP adresi görüntülenir:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Daha sonra [az Network public-IP List][az-network-public-ip-list] komutunu kullanarak genel IP adresini alabilirsiniz. Oluşturduğunuz düğüm kaynak grubu ve genel IP adresinin adını belirtin ve aşağıdaki örnekte gösterildiği gibi *IPAddress* için sorgulama yapın:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Statik IP adresini kullanarak bir hizmet oluşturma

Bir hizmet oluşturmadan önce, AKS kümesi tarafından kullanılan küme kimliğinin diğer kaynak grubu için izinleri temsilci olarak aldığından emin olun. Örnek:

```azurecli-interactive
az role assignment create \
    --assignee <Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

> [!IMPORTANT]
> Giden IP 'nizi özelleştirdiyseniz, küme kimliğinizin hem giden genel IP 'nin hem de bu gelen genel IP 'si için izinlere sahip olduğundan emin olun.

Statik genel IP adresi ile bir *LoadBalancer* hizmeti oluşturmak için, `loadBalancerIP` özelliği ve statik genel IP adresinin değerini YAML bildirimine ekleyin. `load-balancer-service.yaml`Aşağıdaki YAML 'de adlı bir dosya oluşturun ve kopyalayın. Önceki adımda oluşturulan kendi genel IP adresinizi sağlayın. Aşağıdaki örnek, ek açıklamayı *Myresourcegroup* adlı kaynak grubuna da ayarlar. Kendi kaynak grubunuzun adını sağlayın.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Komutuyla hizmeti ve dağıtımı oluşturun `kubectl apply` .

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Hizmete bir DNS etiketi uygulama

Hizmetiniz dinamik veya statik bir genel IP adresi kullanıyorsa, `service.beta.kubernetes.io/azure-dns-label-name` genel kullanıma yönelik BIR DNS etiketi ayarlamak için hizmet ek açıklamasını kullanabilirsiniz. Bu, Azure 'un ortak DNS sunucularını ve en üst düzey etki alanını kullanarak hizmetiniz için tam etki alanı adı yayınlar. Ek açıklama değeri Azure konumu içinde benzersiz olmalıdır, bu nedenle yeterince nitelikli bir etiket kullanılması önerilir.   

Daha sonra Azure, `<location>.cloudapp.azure.com` tam DNS adını oluşturmak için (seçtiğiniz bölgenin bulunduğu konum), sağladığınız ada göre otomatik olarak varsayılan bir alt ağ ekler. Örnek:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Hizmeti kendi etki alanında yayımlamak için, bkz. [Azure DNS][azure-dns-zone] ve [dış-DNS][external-dns] projesi.

## <a name="troubleshoot"></a>Sorun giderme

Kubernetes hizmet bildiriminin *Loadbalancerıp* özelliğinde tanımlanan statik IP adresi yoksa veya düğüm kaynak grubunda oluşturulmamışsa ve ek temsilci yapılandırılmamışsa, yük dengeleyici hizmeti oluşturma işlemi başarısız olur. Sorunu gidermek için, [kubectl açıkla][kubectl-describe] komutuyla hizmet oluşturma olaylarını gözden geçirin. Aşağıdaki örnekte gösterildiği gibi, YAML bildiriminde belirtilen hizmetin adını sağlayın:

```console
kubectl describe service azure-load-balancer
```

Kubernetes hizmet kaynağı hakkında bilgi görüntülenir. Aşağıdaki örnek çıktının sonundaki *Olaylar* , *Kullanıcı tarafından sağlanan IP adresinin bulunamadığını* gösterir. Bu senaryolarda, düğüm kaynak grubunda statik genel IP adresi oluşturdunuz ve Kubernetes hizmet bildiriminde belirtilen IP adresinin doğru olduğundan emin olun.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Sonraki adımlar

Uygulamalarınıza yönelik ağ trafiği üzerinde ek denetim için, bunun yerine [bir giriş denetleyicisi oluşturmak][aks-ingress-basic]isteyebilirsiniz. [Statik bir genel IP adresi olan][aks-static-ingress]bir giriş denetleyicisi de oluşturabilirsiniz.

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[az-aks-show]: /cli/azure/aks#az_aks_show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
