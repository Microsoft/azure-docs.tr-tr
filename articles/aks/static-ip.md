---
title: Azure Kubernetes Service (AKS) yük dengeleyicisiyle bir statik IP adresi kullanın
description: Azure Kubernetes Service (AKS) yük dengeleyicisiyle statik bir IP adresi oluşturmayı ve kullanmayı öğrenin.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 9e32715766734bcbb150d70aeed2dc5b06a4bcbb
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614463"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Azure Kubernetes Service (AKS) yük dengeleyicisiyle statik bir genel IP adresi kullanın

Varsayılan olarak, bir AKS kümesi tarafından oluşturulan bir yük dengeleyici kaynağına atanan genel IP adresi yalnızca söz konusu kaynağın kullanım ömrü için geçerlidir. Kubernetes hizmetini silerseniz ilişkili yük dengeleyici ve IP adresi de silinir. Belirli bir IP adresi atamak veya yeniden dağıtılan Kubernetes Hizmetleri için bir IP adresi korumak istiyorsanız statik bir genel IP adresi oluşturabilir ve kullanabilirsiniz.

Bu makalede, statik bir genel IP adresi oluşturma ve Kubernetes hizmetinize atama işlemlerinin nasıl yapılacağı gösterilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü `az --version` bulmak için ' i çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek][install-azure-cli].

Şu anda yalnızca *temel IP SKU 'su*destekleniyor. *Standart IP* kaynak SKU 'sunu desteklemek için iş devam ediyor. Daha fazla bilgi için bkz. [Azure 'Da IP adresi türleri ve ayırma yöntemleri][ip-sku].

## <a name="create-a-static-ip-address"></a>Statik IP adresi oluşturma

AKS ile kullanılmak üzere statik bir genel IP adresi oluşturduğunuzda, IP adresi kaynağının **düğüm** kaynak grubunda oluşturulması gerekir. Kaynakları ayırmak istiyorsanız, [düğüm kaynak grubunun dışında statik BIR IP adresi kullanmak](#use-a-static-ip-address-outside-of-the-node-resource-group)için aşağıdaki bölüme bakın.

İlk olarak, [az aks Show][az-aks-show] komutuyla düğüm kaynak grubu adını alın ve `--query nodeResourceGroup` sorgu parametresini ekleyin. Aşağıdaki örnek, *Myresourcegroup*kaynak grubu adı altında *Myakscluster* adlı aks kümesi için düğüm kaynak grubunu alır:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Şimdi [az Network public IP Create][az-network-public-ip-create] komutuyla bir STATIK genel IP adresi oluşturun. Önceki komutta elde edilen düğüm kaynak grubu adını ve sonra da *Myakspublicıp*gibi IP adresi kaynağı için bir ad belirtin:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi IP adresi görüntülenir:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

Daha sonra [az Network public-IP List][az-network-public-ip-list] komutunu kullanarak genel IP adresini alabilirsiniz. Oluşturduğunuz düğüm kaynak grubu ve genel IP adresinin adını belirtin ve aşağıdaki örnekte gösterildiği gibi *IPAddress* için sorgulama yapın:

```azurecli-interactive
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Statik IP adresini kullanarak bir hizmet oluşturma

Statik genel IP adresine sahip bir hizmet oluşturmak için, `loadBalancerIP` özelliği ve statik genel IP adresinin değerini YAML bildirimine ekleyin. Aşağıdaki YAML 'de `load-balancer-service.yaml` adlı bir dosya oluşturun ve kopyalayın. Önceki adımda oluşturulan kendi genel IP adresinizi sağlayın.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

`kubectl apply` Komutuyla hizmeti ve dağıtımı oluşturun.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Düğüm kaynak grubu dışında statik bir IP adresi kullan

Kubernetes 1,10 veya üzeri ile, düğüm kaynak grubu dışında oluşturulan statik bir IP adresi kullanabilirsiniz. AKS kümesi tarafından kullanılan hizmet sorumlusu, aşağıdaki örnekte gösterildiği gibi diğer kaynak grubuna temsilci izinleri vermelidir:

```azurecli-interactive
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Düğüm kaynak grubu dışında bir IP adresi kullanmak için, hizmet tanımına bir ek açıklama ekleyin. Aşağıdaki örnek, ek açıklamayı *Myresourcegroup*adlı kaynak grubuna ayarlar. Kendi kaynak grubu adınızı sağlayın:

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

## <a name="troubleshoot"></a>Sorun giderme

Kubernetes hizmet bildiriminin *Loadbalancerıp* özelliğinde tanımlanan statik IP adresi yoksa veya düğüm kaynak grubunda oluşturulmamışsa ve ek temsilci yapılandırılmamışsa, yük dengeleyici hizmeti oluşturma işlemi başarısız olur. Sorunu gidermek için, [kubectl açıkla][kubectl-describe] komutuyla hizmet oluşturma olaylarını gözden geçirin. Aşağıdaki örnekte gösterildiği gibi, YAML bildiriminde belirtilen hizmetin adını sağlayın:

```console
kubectl describe service azure-load-balancer
```

Kubernetes hizmet kaynağı hakkında bilgi görüntülenir. Aşağıdaki örnek çıktının sonundaki *Olaylar* , *Kullanıcı tarafından sağlanan IP adresinin bulunamadığını*gösterir. Bu senaryolarda, düğüm kaynak grubunda statik genel IP adresi oluşturdunuz ve Kubernetes hizmet bildiriminde belirtilen IP adresinin doğru olduğundan emin olun.

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

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
