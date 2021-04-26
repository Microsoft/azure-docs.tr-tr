---
title: Windows Server düğümlerine yönelik RDP
titleSuffix: Azure Kubernetes Service
description: Sorun giderme ve bakım görevleri için Azure Kubernetes Service (AKS) kümesi Windows Server düğümleri ile bir RDP bağlantısı oluşturmayı öğrenin.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 62f29c0550b858e34d888da61f1bd7fbd358f82d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782936"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Azure Kubernetes Service (AKS) kümesi Windows Server düğümlerine bakım veya sorun giderme için RDP ile bağlanma

Azure Kubernetes Service (AKS) kümenizin yaşam döngüsü boyunca bir AKS Windows Server düğümüne erişmeniz gerekebilir. Bu erişim, bakım, günlük toplama veya diğer sorun giderme işlemleri için olabilir. RDP kullanarak AKS Windows Server düğümlerine erişebilirsiniz. Alternatif olarak, AKS Windows Server düğümlerine erişmek için SSH kullanmak istiyorsanız ve küme oluşturma sırasında kullanılan aynı KeyPair 'e erişiminiz varsa, SSH 'deki adımları [Azure Kubernetes Service (AKS) küme düğümlerine][ssh-steps]uygulayabilirsiniz. Güvenlik nedeniyle AKS düğümleri Internet 'e gösterilmez.

Bu makalede, özel IP adreslerini kullanarak AKS düğümüyle bir RDP bağlantısı oluşturma işlemlerinin nasıl yapılacağı gösterilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, bir Windows Server düğümü olan bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa, [Azure CLI kullanarak bir Windows kapsayıcısı Ile AKS kümesi oluşturma][aks-windows-cli]hakkındaki makaleye bakın. Sorun gidermek istediğiniz Windows Server düğümü için Windows Yöneticisi Kullanıcı adı ve parolası gerekir. Bunları bilmiyorsanız, [bir WINDOWS sanal makinesinde Uzak Masaüstü Hizmetleri sıfırlamayı veya yönetici parolasını ](/troubleshoot/azure/virtual-machines/reset-rdp)izleyerek bunları sıfırlayabilirsiniz. Ayrıca, [Microsoft Uzak Masaüstü][rdp-mac]gıbı bir RDP istemcisine da ihtiyacınız vardır.

Ayrıca Azure CLı sürüm 2.0.61 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Bir sanal makineyi kümeniz ile aynı alt ağa dağıtma

AKS kümenizin Windows Server düğümlerinde dışarıdan erişilebilen IP adresleri yok. RDP bağlantısı kurmak için, genel olarak erişilebilen bir IP adresine sahip bir sanal makineyi Windows Server düğümleriniz ile aynı alt ağa dağıtabilirsiniz.

Aşağıdaki örnek, *Myresourcegroup* kaynak grubunda *myvm* adlı bir sanal makine oluşturur.

İlk olarak, Windows Server düğüm havuzunuz tarafından kullanılan alt ağı alın. Alt ağ kimliğini almak için alt ağın adının olması gerekir. Alt ağın adını almak için VNET 'in adına ihtiyacınız vardır. Kümenizi ağ listesi için sorgulayarak VNET adını alın. Kümeyi sorgulamak için adına ihtiyacınız vardır. Azure Cloud Shell aşağıdakileri çalıştırarak bunların hepsini edinebilirsiniz:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Artık SUBNET_ID sahip olduğunuza göre, VM 'yi oluşturmak için aynı Azure Cloud Shell penceresinde aşağıdaki komutu çalıştırın:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

Aşağıdaki örnek çıktı, VM 'nin başarıyla oluşturulduğunu gösterir ve sanal makinenin genel IP adresini görüntüler.

```console
13.62.204.18
```

Sanal makinenin genel IP adresini kaydedin. Bu adresi sonraki bir adımda kullanacaksınız.

## <a name="allow-access-to-the-virtual-machine"></a>Sanal makineye erişime izin ver

AKS düğüm havuzu alt ağları varsayılan olarak NSG 'ler (ağ güvenlik grupları) ile korunur. Sanal makineye erişim sağlamak için NSG 'de erişimi etkinleştirmeniz gerekir.

> [!NOTE]
> NSG 'ler AKS hizmeti tarafından denetlenir. NSG 'de yaptığınız herhangi bir değişikliğin, denetim düzlemi tarafından herhangi bir zamanda üzerine yazılır.
>

İlk olarak, kuralı eklemek için NSG 'nin kaynak grubunu ve NSG adını alın:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Ardından, NSG kuralını oluşturun:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Düğüm adresini al

Kubernetes kümesini yönetmek için Kubernetes komut satırı istemcisi olan [kubectl][kubectl]'yi kullanırsınız. Azure Cloud Shell kullanıyorsanız, `kubectl` zaten yüklüdür. `kubectl`Yerel olarak yüklemek için [az aks install-cli][az-aks-install-cli] komutunu kullanın:
    
```azurecli-interactive
az aks install-cli
```

`kubectl` istemcisini Kubernetes kümenize bağlanacak şekilde yapılandırmak için [az aks get-credentials][az-aks-get-credentials] komutunu kullanın. Bu komut, kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

[Kubectl Get][kubectl-get] komutunu kullanarak Windows Server DÜĞÜMLERININ iç IP adresini listeleyin:

```console
kubectl get nodes -o wide
```

Aşağıdaki örnek çıktı, Windows Server düğümleri dahil olmak üzere kümedeki tüm düğümlerin iç IP adreslerini gösterir.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Sorun gidermek istediğiniz Windows Server düğümünün iç IP adresini kaydedin. Bu adresi sonraki bir adımda kullanacaksınız.

## <a name="connect-to-the-virtual-machine-and-node"></a>Sanal makineye ve düğüme bağlanma

Daha önce [Microsoft Uzak Masaüstü][rdp-mac]gıbı bir RDP istemcisi kullanarak oluşturduğunuz sanal makınenın genel IP adresine bağlanın.

![RDP istemcisi kullanılarak sanal makineye bağlanma görüntüsü](media/rdp/vm-rdp.png)

Sanal makinenize bağlandıktan sonra, sanal makinenizin içinden bir RDP istemcisi kullanarak sorunlarını gidermek istediğiniz Windows Server düğümünün *Iç IP adresine* bağlanın.

![RDP istemcisi kullanarak Windows Server düğümüne bağlanma görüntüsü](media/rdp/node-rdp.png)

Artık Windows Server düğümıza bağlısınız.

![Windows Server düğümündeki CMD penceresinin görüntüsü](media/rdp/node-session.png)

Artık *cmd* penceresinde herhangi bir sorun giderme komutunu çalıştırabilirsiniz. Windows Server düğümleri Windows Server Core kullandığından, RDP üzerinden bir Windows Server düğümüne bağlandığınızda tam GUI veya başka bir GUI aracı yoktur.

## <a name="remove-rdp-access"></a>RDP erişimini kaldırma

İşiniz bittiğinde, RDP bağlantısından Windows Server düğümüne çıkın ve ardından RDP oturumundan sanal makineye çıkın. Her iki RDP oturumunda de çıktıktan sonra, [az VM Delete][az-vm-delete] komutuyla sanal makineyi silin:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

NSG kuralı:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Sonraki adımlar

Ek sorun giderme verilerine ihtiyacınız varsa [Kubernetes ana düğüm günlüklerini][view-master-logs] veya [Azure izleyicisini][azure-monitor-containers]görüntüleyebilirsiniz.

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-vm-delete]: /cli/azure/vm#az_vm_delete
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md