---
title: Azure Kubernetes Service (AKS) üzerinde Ultra disk desteğini etkinleştir
description: Azure Kubernetes Service (AKS) kümesinde Ultra disklerin nasıl etkinleştirileceğini ve yapılandırılacağını öğrenin
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 7dbe0a75ce2079bdec752f7fee0c3e97e3ae2ffa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767358"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Azure Kubernetes hizmetinde Azure Ultra diskleri kullanma (Önizleme)

[Azure Ultra diskler](../virtual-machines/disks-enable-ultra-ssd.md) , durum bilgisi olan uygulamalarınız için yüksek aktarım hızı, yüksek IOPS ve tutarlı düşük gecikmeli disk depolama alanı sunar. Ultra disklerin büyük bir avantajı, aracı düğümlerinizi yeniden başlatmanıza gerek kalmadan SSD 'nin performansını ve iş yüklerinizi dinamik olarak değiştirme yeteneğidir. Ultra diskler, veri kullanımı yoğun iş yükleri için uygundur.

## <a name="before-you-begin"></a>Başlamadan önce

Bu özellik yalnızca küme oluşturma veya düğüm havuzu oluşturma zamanında ayarlanabilir.

> [!IMPORTANT]
> Azure Ultra diskler, bu diskleri ve yalnızca belirli VM serisini destekleyen kullanılabilirlik bölgelerinde ve bölgelerde dağıtılan nodepools gerektirir. Bkz. [**Ultra DISKLER GA kapsamı ve sınırlamaları**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations).

### <a name="register-the-enableultrassd-preview-feature"></a>`EnableUltraSSD`Önizleme özelliğini kaydetme

Ultra disklerden yararlanan bir AKS kümesi veya düğüm havuzu oluşturmak için `EnableUltraSSD` aboneliğinizde Özellik bayrağını etkinleştirmeniz gerekir.

`EnableUltraSSD`Aşağıdaki örnekte gösterildiği gibi [az Feature Register][az-feature-register] komutunu kullanarak özellik bayrağını kaydedin:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Durumun *kayıtlı* gösterilmesi birkaç dakika sürer. [Az Feature List][az-feature-list] komutunu kullanarak kayıt durumunu denetleyebilirsiniz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

Hazırlandığınızda, [az Provider Register][az-provider-register] komutunu kullanarak *Microsoft. Containerservice* kaynak sağlayıcısı kaydını yenileyin:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI uzantısını yükleme

Ultra diskler kullanan bir AKS kümesi veya düğüm havuzu oluşturmak için en son *aks-Preview* CLI uzantısına ihtiyacınız vardır. [Az Extension Add][az-extension-add] komutunu kullanarak *aks-Preview* Azure CLI uzantısını veya [az Extension Update][az-extension-update] komutunu kullanarak tüm kullanılabilir güncelleştirmeleri yüklemeyi kullanın:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Sınırlamalar
- Bkz. [ **Ultra diskler GA kapsamı ve sınırlamaları**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- Bir ultra diskler için desteklenen boyut aralığı 100 ile 1500 arasındadır

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Ultra diskler kullanan yeni bir küme oluşturun

Aşağıdaki CLı komutlarını kullanarak Ultra disklerden faydalanabilir bir AKS kümesi oluşturun. `--aks-custom-headers`Özelliği ayarlamak için bayrağını kullanın `EnableUltraSSD` .

Azure Kaynak grubu oluşturun:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Ultra diskler desteğiyle AKS kümesini oluşturun.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Ultra disk desteği olmayan kümeler oluşturmak istiyorsanız, özel parametreyi atlayarak bunu yapabilirsiniz `--aks-custom-headers` .

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Mevcut kümede Ultra diskleri etkinleştir

Kümenize Ultra diskleri destekleyen yeni bir düğüm havuzu ekleyerek, mevcut kümelerde Ultra diskleri etkinleştirebilirsiniz. Bayrağını kullanarak Ultra diskler kullanmak için yeni bir düğüm havuzu yapılandırın `--aks-custom-headers` .

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Ultra diskler için destek olmadan yeni düğüm havuzları oluşturmak istiyorsanız, özel parametreyi atlayarak bunu yapabilirsiniz `--aks-custom-headers` .

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Bir depolama sınıfıyla dinamik olarak Ultra diskler kullanma

Dağıtımlarımızda veya durum bilgisi olan kümelerimizde Ultra diskler kullanmak için [dinamik sağlama için bir depolama sınıfı](azure-disks-dynamic-pv.md)kullanabilirsiniz.

### <a name="create-the-storage-class"></a>Depolama sınıfını oluşturma

Depolama sınıfı, bir depolama biriminin kalıcı bir birimle dinamik olarak nasıl oluşturulduğunu tanımlamak için kullanılır. Kubernetes Depolama sınıfları hakkında daha fazla bilgi için bkz. [Kubernetes Depolama sınıfları][kubernetes-storage-classes].

Bu durumda, Ultra disklere başvuran bir depolama sınıfı oluşturacağız. Adlı bir dosya oluşturun `azure-ultra-disk-sc.yaml` ve aşağıdaki bildirime kopyalayın.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

[Kubectl Apply][kubectl-apply] komutuyla depolama sınıfını oluşturun ve *Azure-Ultra-disk-SC. YAML* dosyanızı belirtin:

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Kalıcı bir birim talebi oluşturun

Kalıcı bir birim talebi (PVC), depolama sınıfına göre depolamayı otomatik olarak sağlamak için kullanılır. Bu durumda, bir PVC, bir ultra disk oluşturmak için önceden oluşturulmuş depolama sınıfını kullanabilir.

Adlı bir dosya oluşturun `azure-ultra-disk-pvc.yaml` ve aşağıdaki bildirime kopyalayın. Talep, `ultra-disk` *Readwriteonce* Access ile, boyutu *1000 GB* olan adlı bir disk ister. *Ultra disk-SC* depolama sınıfı, depolama sınıfı olarak belirtilir.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

[Kubectl Apply][kubectl-apply] komutuyla kalıcı birim talebi oluşturun ve *Azure-Ultra-disk-PVC. YAML* dosyanızı belirtin:

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Kalıcı birimi kullan

Kalıcı birim talebi oluşturulduktan ve disk başarıyla sağlandıktan sonra, diske erişimi olan bir pod oluşturulabilir. Aşağıdaki bildirim, Azure diskini yola bağlamak için *Ultra disk* adlı kalıcı birim talebini kullanan temel bir NGINX Pod oluşturur `/mnt/azure` .

Adlı bir dosya oluşturun `nginx-ultra.yaml` ve aşağıdaki bildirime kopyalayın.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
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
        claimName: ultra-disk
```

Aşağıdaki örnekte gösterildiği gibi, [kubectl Apply][kubectl-apply] komutuyla Pod öğesini oluşturun:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

Artık, Azure diskinizin dizine bağlanmış bir pod çalışır durumda `/mnt/azure` . Bu yapılandırma `kubectl describe pod nginx-ultra` , aşağıdaki sıkıştırılmış örnekte gösterildiği gibi Pod 'niz aracılığıyla incelenirken görülebilir:

```console
$ kubectl describe pod nginx-ultra

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


## <a name="next-steps"></a>Sonraki adımlar

- Ultra diskler hakkında daha fazla bilgi için bkz. [Azure Ultra diskler kullanma](../virtual-machines/disks-enable-ultra-ssd.md).
- Depolama en iyi uygulamaları hakkında daha fazla bilgi için bkz. [Azure Kubernetes Service (AKS) içinde depolama ve yedeklemeler Için en iyi uygulamalar][operator-best-practices-storage]

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
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
