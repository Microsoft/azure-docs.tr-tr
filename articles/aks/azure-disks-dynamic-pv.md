---
title: Azure Kubernetes hizmetinde (AKS) Azure diskleriyle kalıcı bir birimi dinamik olarak oluşturma ve kullanma
description: Azure Kubernetes Service (AKS) ' de Azure diskleriyle kalıcı bir birimi dinamik olarak oluşturmayı öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 1c7a406f0b06b94aaa6d8b4af63b1416b11c7c56
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847324"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmetinde (AKS) Azure diskleriyle kalıcı bir birimi dinamik olarak oluşturma ve kullanma

Kalıcı bir birim, Kubernetes pods ile kullanılmak üzere sağlanmış bir depolama parçasını temsil eder. Kalıcı bir birim bir veya daha fazla sayıda pods tarafından kullanılabilir ve dinamik veya statik olarak sağlanabilir. Bu makalede, Azure Kubernetes Service (AKS) kümesinde tek bir pod tarafından kullanılmak üzere Azure diskleriyle kalıcı birimleri dinamik olarak oluşturma konusu gösterilmektedir.

> [!NOTE]
> Bir Azure diski yalnızca, AKS içinde yalnızca tek bir pod için kullanılabilir hale getiren bir *Readwriteonce* *erişim modu* türü ile bağlanabilir. Kalıcı bir birimi birden çok birimde paylaşmanız gerekiyorsa [Azure dosyalarını][azure-files-pvc]kullanın.

Kubernetes birimleri hakkında daha fazla bilgi için bkz. [AKS 'de uygulamalar Için depolama seçenekleri][concepts-storage].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek][install-azure-cli].

## <a name="built-in-storage-classes"></a>Yerleşik depolama sınıfları

Depolama sınıfı, bir depolama biriminin kalıcı bir birimle dinamik olarak nasıl oluşturulduğunu tanımlamak için kullanılır. Kubernetes Depolama sınıfları hakkında daha fazla bilgi için bkz. [Kubernetes Depolama sınıfları][kubernetes-storage-classes].

Her bir AKS kümesi, Azure diskleriyle çalışacak şekilde yapılandırılmış iki önceden oluşturulmuş depolama sınıfı içerir:

* *Varsayılan* depolama sınıfı standart bir Azure diski sağlar.
    * Standart depolama, HDD 'Ler tarafından desteklenir ve performansa devam ederken uygun maliyetli depolama sunar. Standart diskler, uygun maliyetli bir geliştirme ve iş yükü testi için idealdir.
* *Yönetilen-Premium* depolama sınıfı, Premium bir Azure diski sağlar.
    * Premium diskler SSD tabanlı, yüksek performanslı ve düşük gecikme süreli disk ile desteklenir. Üretim iş yükü çalıştıran VM'ler için son derece uygundur. Kümenizdeki AKS düğümleri Premium Depolama kullanıyorsa, *yönetilen-Premium* sınıfını seçin.
    
Bu varsayılan depolama sınıfları, oluşturulduktan sonra birim boyutunu güncelleştirmenize izin vermez. Bu özelliği etkinleştirmek için, *Allowvolumegenişletmesini: true* satırını varsayılan depolama sınıflarından birine ekleyin veya size ait özel depolama sınıfınızı oluşturun. Var olan bir depolama sınıfını `kubectl edit sc` komutunu kullanarak düzenleyebilirsiniz. Depolama sınıfları ve kendinizinkini oluşturma hakkında daha fazla bilgi için bkz. [AKS 'de uygulamalar Için depolama seçenekleri][storage-class-concepts].

Önceden oluşturulmuş depolama sınıflarını görmek için [kubectl Get SC][kubectl-get] komutunu kullanın. Aşağıdaki örnek, bir AKS kümesi içinde kullanılabilir olan önceden oluşturma depolama sınıflarını gösterir:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Kalıcı birim talepleri GiB 'de belirtilmiştir ancak Azure yönetilen diskler belirli bir boyut için SKU tarafından faturalandırılır. Bu SKU 'Lar, S4 veya P4 diskleri için 32GiB 'den S80 veya P80 diskleri için (önizlemede) 32 TİB 'ye kadar uzanır. Premium yönetilen bir diskin verimlilik ve ıOPS performansı, AKS kümesindeki düğümlerin hem SKU hem de örnek boyutuna bağlıdır. Daha fazla bilgi için bkz. [yönetilen disklerin fiyatlandırma ve performansı][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Kalıcı bir birim talebi oluşturun

Kalıcı bir birim talebi (PVC), depolama sınıfına göre depolamayı otomatik olarak sağlamak için kullanılır. Bu durumda, bir PVC, standart veya Premium bir Azure yönetilen diski oluşturmak için önceden oluşturulmuş depolama sınıflarından birini kullanabilir.

`azure-premium.yaml`adlı bir dosya oluşturun ve aşağıdaki bildirimde kopyalayın. Talep, *Readwriteonce* Access Ile boyutu *5 GB* olan `azure-managed-disk` adlı bir disk ister. *Yönetilen-Premium* depolama sınıfı, depolama sınıfı olarak belirtilir.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Standart depolama kullanan bir disk oluşturmak için, *yönetilen-Premium*yerine `storageClassName: default` kullanın.

[Kubectl Apply][kubectl-apply] komutuyla kalıcı birim talebi oluşturun ve *Azure-Premium. YAML* dosyanızı belirtin:

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Kalıcı birimi kullan

Kalıcı birim talebi oluşturulduktan ve disk başarıyla sağlandıktan sonra, diske erişimi olan bir pod oluşturulabilir. Aşağıdaki bildirim, `/mnt/azure`yolunda Azure diskini bağlamak için *Azure tarafından yönetilen-disk* adlı kalıcı birim talebini kullanan temel bir NGINX Pod oluşturur. Windows Server kapsayıcıları için (Şu anda AKS 'de önizlemededir), Windows yol kuralını kullanarak *":"* gibi bir *bağlamayolu* belirtin.

`azure-pvc-disk.yaml`adlı bir dosya oluşturun ve aşağıdaki bildirimde kopyalayın.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Aşağıdaki örnekte gösterildiği gibi, [kubectl Apply][kubectl-apply] komutuyla Pod öğesini oluşturun:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Azure diskinizin `/mnt/azure` dizinine bağlı olarak çalışan bir pod sahipsiniz. Aşağıdaki sıkıştırılmış örnekte gösterildiği gibi, Pod 'niz `kubectl describe pod mypod`aracılığıyla incelenirken bu yapılandırma görülebilir:

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Kalıcı bir birimi yedekleme

Kalıcı biriminizdeki verileri yedeklemek için birim için yönetilen diskin anlık görüntüsünü alın. Daha sonra bu anlık görüntüyü kullanarak geri yüklenen bir disk oluşturabilir ve verileri geri yükleme yöntemi olarak Pod 'ye iliştirebilirsiniz.

İlk olarak, *Azure-Managed-disk*adlı PVC için gibi `kubectl get pvc` komutuyla birim adını alın:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Bu birim adı, temel alınan Azure disk adını oluşturur. Disk KIMLIĞI ' ni [az disk List][az-disk-list] ile sorgulayın ve aşağıdaki örnekte gösterildiği gıbı, PVC birimi adınızı sağlayın:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

[Az Snapshot Create][az-snapshot-create]komutuyla bir anlık görüntü diski oluşturmak IÇIN disk kimliğini kullanın. Aşağıdaki örnek, AKS kümesiyle aynı kaynak grubunda *Pvcsnapshot* adlı bir anlık görüntü oluşturur (*MC_myResourceGroup_myAKSCluster_eastus*). AKS kümesinin erişimi olmayan kaynak gruplarında anlık görüntü ve geri yükleme diskleri oluşturursanız, izin sorunlarıyla karşılaşabilirsiniz.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Diskinizdeki veri miktarına bağlı olarak, anlık görüntünün oluşturulması birkaç dakika sürebilir.

## <a name="restore-and-use-a-snapshot"></a>Anlık görüntüyü geri yükleme ve kullanma

Diski geri yüklemek ve Kubernetes Pod ile kullanmak için [az disk Create][az-disk-create]komutunu kullanarak bir disk oluşturduğunuzda anlık görüntüyü kaynak olarak kullanın. Özgün veri anlık görüntüsüne erişmeniz gerekiyorsa, bu işlem özgün kaynağı korur. Aşağıdaki örnek *Pvcsnapshot*adlı anlık görüntüden *pvcgeri yüklendi* adlı bir disk oluşturur:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Geri yüklenen diski Pod ile birlikte kullanmak için, bildirimdeki diskin KIMLIĞINI belirtin. Disk KIMLIĞINI [az disk Show][az-disk-show] komutuyla alın. Aşağıdaki örnek, önceki adımda oluşturulan *Pvcgeri yüklenen* IÇIN disk kimliğini alır:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

`azure-restored.yaml` adlı bir pod bildirimi oluşturun ve önceki adımda elde edilen disk URI 'sini belirtin. Aşağıdaki örnek, */mnt/Azure*' da bir birim olarak bağlanmış geri yüklenen disk ile temel bir NGINX web sunucusu oluşturur:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Aşağıdaki örnekte gösterildiği gibi, [kubectl Apply][kubectl-apply] komutuyla Pod öğesini oluşturun:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Bölüm bilgilerini gösteren aşağıdaki sıkıştırılmış örnek gibi Pod 'un ayrıntılarını görüntülemek için `kubectl describe pod mypodrestored` kullanabilirsiniz:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Sonraki adımlar

İlişkili en iyi uygulamalar için bkz. [AKS 'de depolama ve yedeklemeler Için en iyi uygulamalar][operator-best-practices-storage].

Azure disklerini kullanarak Kubernetes kalıcı birimleri hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure diskleri için Kubernetes eklentisi][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
