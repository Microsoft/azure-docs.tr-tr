---
title: Azure Kubernetes Service (AKS) küme düğümlerine SSH
description: Sorun giderme ve bakım görevleri için Azure Kubernetes Service (AKS) küme düğümleri ile bir SSH bağlantısı oluşturmayı öğrenin.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/31/2019
ms.author: mlearned
ms.openlocfilehash: d855e7a65b7e1ad24dcfc4fe6a6d5e02f9004bb0
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74089554"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Bakım veya sorun giderme için SSH ile Azure Kubernetes Service (AKS) küme düğümlerine bağlanma

Azure Kubernetes Service (AKS) kümenizin yaşam döngüsü boyunca bir AKS düğümüne erişmeniz gerekebilir. Bu erişim, bakım, günlük toplama veya diğer sorun giderme işlemleri için olabilir. Windows Server düğümleri dahil olmak üzere SSH kullanarak AKS düğümlerine erişebilirsiniz (Şu anda AKS 'de önizlemededir). Ayrıca, [Uzak Masaüstü Protokolü (RDP) bağlantılarını kullanarak Windows Server düğümlerine da bağlanabilirsiniz][aks-windows-rdp]. Güvenlik nedeniyle AKS düğümleri Internet 'e gösterilmez. AKS düğümlerine SSH için özel IP adresini kullanırsınız.

Bu makalede, özel IP adreslerini kullanarak AKS düğümüyle bir SSH bağlantısı oluşturma gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Varsayılan olarak, SSH anahtarları alınır veya oluşturulur, ardından bir AKS kümesi oluşturduğunuzda düğümlere eklenir. Bu makalede, AKS kümenizi oluştururken kullanılan SSH anahtarlarından farklı SSH anahtarları belirtme konusu gösterilmektedir. Makalede ayrıca, düğümünüz özel IP adresini belirleme ve SSH kullanarak buna bağlanma işlemlerinin nasıl yapılacağı gösterilmektedir. Farklı bir SSH anahtarı belirtmeniz gerekmiyorsa, düğüme SSH ortak anahtarını ekleme adımını atlayabilirsiniz.

Bu makalede ayrıca bir SSH anahtarınız olduğunu varsaymaktadır. [MacOS veya Linux][ssh-nix] veya [WINDOWS][ssh-windows]kullanarak bir SSH anahtarı oluşturabilirsiniz. Anahtar çiftini oluşturmak için PuTTY gen kullanırsanız, anahtar çiftini varsayılan PuTTy özel anahtar biçimi (. PPK dosyası) yerine bir OpenSSH biçiminde kaydedin.

Ayrıca Azure CLı sürüm 2.0.64 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>SSH erişimi için sanal makine ölçek kümesi tabanlı AKS kümelerini yapılandırma

Sanal makine ölçek kümenizi SSH erişimi için yapılandırmak üzere, kümenizin sanal makine ölçek kümesinin adını bulun ve SSH ortak anahtarınızı bu ölçek kümesine ekleyin.

AKS kümenizin kaynak grubu adını almak için [az aks Show][az-aks-show] komutunu, sonra da ölçek kümenizin adını almak için [az VMSS List][az-vmss-list] komutunu kullanın.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

Yukarıdaki örnek, *Myresourcegroup* Içindeki *Myakscluster* için küme kaynak grubunun adını *CLUSTER_RESOURCE_GROUP*olarak atar. Örnek daha sonra ölçek kümesi adını listelemek ve *SCALE_SET_NAME*atamak için *CLUSTER_RESOURCE_GROUP* kullanır.  

> [!IMPORTANT]
> Şu anda, Azure CLı kullanarak sanal makine ölçek kümesi tabanlı AKS kümeleriniz için yalnızca SSH anahtarlarınızı güncelleştirmeniz gerekir.
> 
> Linux düğümlerinde, SSH anahtarları şu anda yalnızca Azure CLı kullanılarak eklenebilir. SSH kullanarak bir Windows Server düğümüne bağlanmak istiyorsanız, AKS kümesini oluştururken sağlanmış SSH anahtarlarını kullanın ve SSH ortak anahtarınızı eklemek için bir sonraki komut kümesini atlayın. Bu bölümün son komutunda gösterilen, sorun gidermek istediğiniz düğümün IP adresine de ihtiyacınız olacaktır. Alternatif olarak, SSH kullanmak yerine [Uzak Masaüstü Protokolü (RDP) bağlantılarını kullanarak Windows Server düğümlerine bağlanabilirsiniz][aks-windows-rdp] .

SSH anahtarlarınızı bir sanal makine ölçek kümesindeki düğümlere eklemek için [az VMSS Extension set][az-vmss-extension-set] ve [az VMSS Update-Instances][az-vmss-update-instances] komutlarını kullanın.

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

Yukarıdaki örnek, önceki komutlardan *CLUSTER_RESOURCE_GROUP* ve *SCALE_SET_NAME* değişkenlerini kullanır. Yukarıdaki örnek, SSH ortak anahtarınızın konumu olarak *~/. ssh/id_rsa. pub* öğesini de kullanır.

> [!NOTE]
> Varsayılan olarak, AKS düğümlerinin Kullanıcı adı *azureuser*' dir.

SSH ortak anahtarınızı ölçek kümesine ekledikten sonra bu ölçek kümesinde IP adresini kullanarak bir düğüm sanal makinesine SSH ekleyebilirsiniz. [Kubectl Get komutunu][kubectl-get]kullanarak aks küme DÜĞÜMLERININ özel IP adreslerini görüntüleyin.

```console
kubectl get nodes -o wide
```

Aşağıdaki örnek çıktı, Windows Server düğümü dahil olmak üzere kümedeki tüm düğümlerin iç IP adreslerini gösterir.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Sorun gidermek istediğiniz düğümün iç IP adresini kaydedin.

SSH kullanarak düğümünüz erişmek için [SSH bağlantısı oluşturma](#create-the-ssh-connection)bölümündeki adımları izleyin.

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>SSH erişimi için sanal makine kullanılabilirliği kümesi tabanlı AKS kümelerini yapılandırma

Sanal makine kullanılabilirlik kümesi tabanlı AKS kümenizi SSH erişimi için yapılandırmak için, kümenizin Linux düğümünün adını bulun ve SSH ortak anahtarınızı bu düğüme ekleyin.

AKS kümenizin kaynak grubu adını almak için [az aks Show][az-aks-show] komutunu, sonra da kümenizin Linux düğümünün sanal makine adını listelemek için [az VM List][az-vm-list] komutunu kullanın.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Yukarıdaki örnek, *Myresourcegroup* Içindeki *Myakscluster* için küme kaynak grubunun adını *CLUSTER_RESOURCE_GROUP*olarak atar. Örnek daha sonra sanal makine adını listelemek için *CLUSTER_RESOURCE_GROUP* kullanır. Örnek çıkış, sanal makinenin adını gösterir: 

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

SSH anahtarlarınızı düğüme eklemek için [az VM User Update][az-vm-user-update] komutunu kullanın.

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Yukarıdaki örnekte, önceki komutlardan *CLUSTER_RESOURCE_GROUP* değişkeni ve düğüm sanal makine adı kullanılmaktadır. Yukarıdaki örnek, SSH ortak anahtarınızın konumu olarak *~/. ssh/id_rsa. pub* öğesini de kullanır. Bir yol belirtmek yerine SSH ortak anahtarınızın içeriğini de kullanabilirsiniz.

> [!NOTE]
> Varsayılan olarak, AKS düğümlerinin Kullanıcı adı *azureuser*' dir.

SSH ortak anahtarınızı düğüm sanal makinesine ekledikten sonra, IP adresini kullanarak bu sanal makineye SSH ekleyebilirsiniz. [Az VM List-IP-Addresses][az-vm-list-ip-addresses] komutunu kullanarak bir aks küme DÜĞÜMÜNÜN özel IP adresini görüntüleyin.

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Yukarıdaki örnek, önceki komutlarda ayarlanan *CLUSTER_RESOURCE_GROUP* değişkenini kullanır. Aşağıdaki örnek çıktı, AKS düğümlerinin özel IP adreslerini göstermektedir:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>SSH bağlantısı oluşturma

AKS düğümüne bir SSH bağlantısı oluşturmak için AKS kümenizde bir yardımcı Pod çalıştırın. Bu yardımcı Pod, kümeye SSH erişimi ve daha sonra ek SSH düğüm erişimi sağlar. Bu yardımcı Pod öğesini oluşturmak ve kullanmak için aşağıdaki adımları izleyin:

1. `debian` kapsayıcı görüntüsü çalıştırın ve buna bir terminal oturumu ekleyin. Bu kapsayıcı, AKS kümesinde herhangi bir düğümle bir SSH oturumu oluşturmak için kullanılabilir:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Windows Server düğümlerini (Şu anda AKS ' de önizleme aşamasında) kullanıyorsanız, bir Linux düğümündeki deler kapsayıcısını zamanlamak için komuta bir düğüm seçici ekleyin:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Terminal oturumu kapsayıcıya bağlandıktan sonra `apt-get`kullanarak bir SSH istemcisi yüklersiniz:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Kapsayıcınıza bağlı olmayan yeni bir Terminal penceresi açın ve [kubectl Get Pod][kubectl-get] komutunu kullanarak aks kümenizdeki Pod 'yi listeleyin. Önceki adımda oluşturulan Pod, aşağıdaki örnekte gösterildiği gibi *aks-SSH*adıyla başlar:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. Önceki bir adımda, Genel SSH anahtarınızı sorun gidermek istediğiniz AKS düğümüne eklediniz. Şimdi, özel SSH anahtarınızı yardımcı Pod 'a kopyalayın. Bu özel anahtar, AKS düğümüne SSH oluşturmak için kullanılır.

    Önceki adımda elde edilen *aks-SSH* Pod adınızı sağlayın. Gerekirse, *~/. ssh/id_rsa* ÖĞESINI özel SSH anahtarınızın konumuyla değiştirin:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Kapsayıcıya terminal oturumuna dönün, kopyalanmış `id_rsa` özel SSH anahtarındaki izinleri Kullanıcı salt okunurdur olacak şekilde güncelleştirin:

    ```console
    chmod 0600 id_rsa
    ```

1. AKS düğümünüz için bir SSH bağlantısı oluşturun. Yine, AKS düğümlerinin Varsayılan Kullanıcı adı *azureuser*' dir. SSH anahtarının ilk güvendiği bağlantı ile devam etmek için istemi kabul edin. Daha sonra AKS düğümünüz için bash istemiyle birlikte verilmiştir:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4
    
    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.
    
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    [...]
    
    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>SSH erişimini kaldırma

İşiniz bittiğinde, SSH oturumunu `exit` ve ardından etkileşimli kapsayıcı oturumu `exit`. Bu kapsayıcı oturumu kapandığında, AKS kümesinden SSH erişimi için kullanılan Pod silinir.

## <a name="next-steps"></a>Sonraki adımlar

Ek sorun giderme verilerine ihtiyacınız varsa, [kubelet günlüklerini görüntüleyebilir][view-kubelet-logs] veya [Kubernetes ana düğüm günlüklerini görüntüleyebilirsiniz][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
