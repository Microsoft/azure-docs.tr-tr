---
title: Dinamik olarak Azure diskler birimi oluşturma
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) ' de Azure diskleriyle kalıcı bir birimi dinamik olarak oluşturmayı öğrenin
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 066a52024e91610882889bb7fbe6b20efa262b71
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776150"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmetinde (AKS) Azure diskleriyle kalıcı bir birimi dinamik olarak oluşturma ve kullanma

Kalıcı bir birim, Kubernetes pods ile kullanılmak üzere sağlanmış bir depolama parçasını temsil eder. Kalıcı bir birim bir veya daha fazla sayıda pods tarafından kullanılabilir ve dinamik veya statik olarak sağlanabilir. Bu makalede, Azure Kubernetes Service (AKS) kümesinde tek bir pod tarafından kullanılmak üzere Azure diskleriyle kalıcı birimleri dinamik olarak oluşturma konusu gösterilmektedir.

> [!NOTE]
> Azure diski yalnızca *erişim modu* türü *readwriteonce* ile bağlanabilir ve bu, aks 'deki bir düğüm için kullanılabilir hale gelir. Kalıcı bir birimi birden çok düğüm arasında paylaşmanız gerekiyorsa [Azure dosyalarını][azure-files-pvc]kullanın.

Kubernetes birimleri hakkında daha fazla bilgi için bkz. [AKS 'de uygulamalar Için depolama seçenekleri][concepts-storage].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

## <a name="built-in-storage-classes"></a>Yerleşik depolama sınıfları

Depolama sınıfı, bir depolama biriminin kalıcı bir birimle dinamik olarak nasıl oluşturulduğunu tanımlamak için kullanılır. Kubernetes Depolama sınıfları hakkında daha fazla bilgi için bkz. [Kubernetes Depolama sınıfları][kubernetes-storage-classes].

Her bir AKS kümesi, Azure diskleriyle çalışmak üzere yapılandırılmış dört adet önceden oluşturulmuş depolama sınıfı içerir:

* *Varsayılan* depolama sınıfı standart bir SSD Azure diski sağlar.
    * Standart depolama, standart SSD 'Ler tarafından desteklenir ve hala güvenilir performans sağlarken uygun maliyetli depolama sunar. 
* *Yönetilen-Premium* depolama sınıfı, Premium bir Azure diski sağlar.
    * Premium diskler SSD tabanlı, yüksek performanslı ve düşük gecikme süreli disk ile desteklenir. Üretim iş yükü çalıştıran VM'ler için son derece uygundur. Kümenizdeki AKS düğümleri Premium Depolama kullanıyorsa, *yönetilen-Premium* sınıfını seçin.
    
Varsayılan depolama sınıflarından birini kullanırsanız, depolama sınıfı oluşturulduktan sonra birim boyutunu güncelleştiremezsiniz. Bir depolama sınıfı oluşturulduktan sonra birim boyutunu güncelleştirebilmek için, satırı `allowVolumeExpansion: true` varsayılan depolama sınıflarından birine ekleyin veya size ait özel depolama sınıfınızı oluşturabilirsiniz. Bir PVC 'nin boyutunu azaltmak için (veri kaybını engellemek için) desteklenmediğini unutmayın. Var olan bir depolama sınıfını komutunu kullanarak düzenleyebilirsiniz `kubectl edit sc` . 

Örneğin, 4 TiB boyutundaki bir disk kullanmak istiyorsanız, `cachingmode: None` [disk önbelleği 4 TİB ve daha büyük diskler için desteklenmediğinden](../virtual-machines/premium-storage-performance.md#disk-caching)tanımlayan bir depolama sınıfı oluşturmanız gerekir.

Depolama sınıfları ve kendi depolama sınıfınızı oluşturma hakkında daha fazla bilgi için bkz. [AKS 'de uygulamalar Için depolama seçenekleri][storage-class-concepts].

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

Adlı bir dosya oluşturun `azure-premium.yaml` ve aşağıdaki bildirime kopyalayın. Talep, `azure-managed-disk` boyutu *5 GB* olan ve *readwriteonce* erişimi olan adlı bir disk ister. *Yönetilen-Premium* depolama sınıfı, depolama sınıfı olarak belirtilir.

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
> Standart depolama kullanan bir disk oluşturmak için, `storageClassName: default` *yönetilen-Premium* yerine kullanın.

[Kubectl Apply][kubectl-apply] komutuyla kalıcı birim talebi oluşturun ve *Azure-Premium. YAML* dosyanızı belirtin:

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Kalıcı birimi kullan

Kalıcı birim talebi oluşturulduktan ve disk başarıyla sağlandıktan sonra, diske erişimi olan bir pod oluşturulabilir. Aşağıdaki bildirim, Azure diskini yolda bağlamak için *Azure-Managed-disk* adlı kalıcı birim talebini kullanan temel bir NGINX Pod oluşturur `/mnt/azure` . Windows Server kapsayıcıları için, Windows yol kuralını kullanarak *":"* gibi bir *bağlamayolu* belirtin.

Adlı bir dosya oluşturun `azure-pvc-disk.yaml` ve aşağıdaki bildirime kopyalayın.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

Artık, Azure diskinizin dizine bağlanmış bir pod çalışır durumda `/mnt/azure` . Bu yapılandırma `kubectl describe pod mypod` , aşağıdaki sıkıştırılmış örnekte gösterildiği gibi Pod 'niz aracılığıyla incelenirken görülebilir:

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

## <a name="use-ultra-disks"></a>Ultra diskler kullanma
Ultra disk 'ten yararlanmak için bkz. [Azure Kubernetes Service (AKS) üzerinde Ultra diskler kullanma](use-ultra-disks.md).

## <a name="back-up-a-persistent-volume"></a>Kalıcı bir birimi yedekleme

Kalıcı biriminizdeki verileri yedeklemek için birim için yönetilen diskin anlık görüntüsünü alın. Daha sonra bu anlık görüntüyü kullanarak geri yüklenen bir disk oluşturabilir ve verileri geri yükleme yöntemi olarak Pod 'ye iliştirebilirsiniz.

İlk `kubectl get pvc` olarak, *Azure-Managed-DISK* adlı PVC için gibi, komutuyla birim adını alın:

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

Diski geri yüklemek ve Kubernetes Pod ile kullanmak için [az disk Create][az-disk-create]komutunu kullanarak bir disk oluşturduğunuzda anlık görüntüyü kaynak olarak kullanın. Özgün veri anlık görüntüsüne erişmeniz gerekiyorsa, bu işlem özgün kaynağı korur. Aşağıdaki örnek *Pvcsnapshot* adlı anlık görüntüden *pvcgeri yüklendi* adlı bir disk oluşturur:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Geri yüklenen diski Pod ile birlikte kullanmak için, bildirimdeki diskin KIMLIĞINI belirtin. Disk KIMLIĞINI [az disk Show][az-disk-show] komutuyla alın. Aşağıdaki örnek, önceki adımda oluşturulan *Pvcgeri yüklenen* IÇIN disk kimliğini alır:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Adlı bir pod bildirimi oluşturun `azure-restored.yaml` ve önceki adımda elde edilen DISK URI 'sini belirtin. Aşağıdaki örnek, */mnt/Azure*' da bir birim olarak bağlanmış geri yüklenen disk ile temel bir NGINX web sunucusu oluşturur:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

`kubectl describe pod mypodrestored`Bölüm bilgilerini gösteren aşağıdaki sıkıştırılmış örnek gibi Pod 'un ayrıntılarını görüntülemek için kullanabilirsiniz:

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
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
