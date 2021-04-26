---
title: Azure Kubernetes Service (aks) içinde pod için statik birim oluşturma
description: Azure Kubernetes hizmetinde (AKS) bir pod ile kullanım için Azure diskleriyle el ile birim oluşturmayı öğrenin
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 617ad75eda766963a91fe3d41b1dbfefae62b41b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776222"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service'te (AKS) Azure diskleriyle el ile birim oluşturma ve kullanma

Kapsayıcı tabanlı uygulamalar genellikle dış veri hacminde verileri erişmesi ve kalıcı hale getirmeniz gerekir. Tek bir pod 'ın depolamaya erişmesi gerekiyorsa, uygulama kullanımı için yerel bir birim sunmak üzere Azure disklerini kullanabilirsiniz. Bu makalede, bir Azure diskini el ile oluşturma ve AKS 'te Pod 'a iliştirme gösterilmektedir.

> [!NOTE]
> Bir Azure diski, aynı anda yalnızca tek bir pod 'a bağlanabilir. Kalıcı bir birimi birden çok birimde paylaşmanız gerekiyorsa [Azure dosyalarını][azure-files-volume]kullanın.

Kubernetes birimleri hakkında daha fazla bilgi için bkz. [AKS 'de uygulamalar Için depolama seçenekleri][concepts-storage].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

## <a name="create-an-azure-disk"></a>Azure diski oluşturma

AKS ile kullanmak üzere bir Azure diski oluşturduğunuzda, **düğüm** kaynak grubunda disk kaynağını oluşturabilirsiniz. Bu yaklaşım, AKS kümesinin disk kaynağına erişip yönetmesine olanak tanır. Bunun yerine, diski ayrı bir kaynak grubunda oluşturursanız, uygulamanızın kaynak grubu için Azure Kubernetes Service (AKS) tarafından yönetilen kimliğini kümenizin rolünü sağlamanız gerekir `Contributor` .

Bu makalede, düğüm kaynak grubunda diski oluşturun. İlk olarak, [az aks Show][az-aks-show] komutuyla kaynak grubu adını alın ve `--query nodeResourceGroup` sorgu parametresini ekleyin. Aşağıdaki örnek, *Myresourcegroup* kaynak grubu adı altında *Myakscluster* adlı aks kümesi için düğüm kaynak grubunu alır:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Şimdi [az disk Create][az-disk-create] komutunu kullanarak bir disk oluşturun. Önceki komutta elde edilen düğüm kaynak grubu adını ve ardından, *Myaksdisk* gibi disk kaynağı için bir ad belirtin. Aşağıdaki örnek, bir *20* gib diski oluşturur ve oluşturulduktan sonra diskin kimliğini verir. Windows Server kapsayıcıları ile kullanmak üzere bir disk oluşturmanız gerekiyorsa, `--os-type windows` diski doğru şekilde biçimlendirmek için parametresini ekleyin.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure diskleri, belirli bir boyut için SKU tarafından faturalandırılır. Bu SKU 'Lar, S4 veya P4 diskleri için 32GiB 'den S80 veya P80 diskleri için (önizlemede) 32 TİB 'ye kadar uzanır. Premium yönetilen bir diskin verimlilik ve ıOPS performansı, AKS kümesindeki düğümlerin hem SKU hem de örnek boyutuna bağlıdır. Bkz. [yönetilen disklerin fiyatlandırma ve performansı][managed-disk-pricing-performance].

Aşağıdaki örnek çıktıda gösterildiği gibi, komut başarıyla tamamlandıktan sonra disk kaynak KIMLIĞI görüntülenir. Bu disk KIMLIĞI, diski bir sonraki adımda bağlamak için kullanılır.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Diski birim olarak bağla

Azure diskini Pod uygulamanıza bağlamak için, birimi kapsayıcı belirtiminde yapılandırın. Aşağıdaki içerikle adlı yeni bir dosya oluşturun `azure-disk-pod.yaml` . `diskName`Önceki adımda oluşturulan diskin adıyla ve disk `diskURI` Oluştur komutunun çıktısında GÖSTERILEN disk kimliğiyle güncelleştirin. İsterseniz, `mountPath` Azure diskinin Pod 'a bağlı olduğu yol olan öğesini güncelleştirin. Windows Server kapsayıcıları için, Windows yol kuralını kullanarak *":"* gibi bir *bağlamayolu* belirtin.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

`kubectl`Pod 'u oluşturmak için komutunu kullanın.

```console
kubectl apply -f azure-disk-pod.yaml
```

Artık konumunda bir Azure diskinin bulunduğu çalışan bir pod sahipsiniz `/mnt/azure` . `kubectl describe pod mypod`Diskin başarıyla takıldığını doğrulamak için ' i kullanabilirsiniz. Aşağıdaki sıkıştırılmış örnek çıktı, kapsayıcıya takılan birimi gösterir:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Sonraki adımlar

İlişkili en iyi uygulamalar için bkz. [AKS 'de depolama ve yedeklemeler Için en iyi uygulamalar][operator-best-practices-storage].

AKS kümeleri hakkında daha fazla bilgi için Azure diskleri [Için Kubernetes eklentisine][kubernetes-disks]bakın.

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az_resource_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
