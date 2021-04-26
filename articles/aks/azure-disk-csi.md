---
title: Azure Kubernetes Service (AKS) üzerinde Azure diskleri için kapsayıcı depolama arabirimi (CSı) sürücülerini kullanma
description: Azure Kubernetes Service (AKS) kümesinde Azure diskleri için kapsayıcı depolama arabirimi (CSı) sürücülerini nasıl kullanacağınızı öğrenin.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: c3421b767f465a4a705bdeb4882fd261c5cf914f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776240"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service (AKS) içindeki Azure disk kapsayıcısı depolama arabirimi (CSı) sürücülerini kullanma (Önizleme)
Azure disk kapsayıcısı depolama arabirimi (CSı) sürücüsü, Azure disk yaşam döngüsünü yönetmek için Azure Kubernetes hizmeti (AKS) tarafından kullanılan bir [CSI belirtimiyle](https://github.com/container-storage-interface/spec/blob/master/spec.md)uyumlu bir sürücü.

CSı, Kubernetes üzerindeki Kapsayıcılı iş yüklerine rastgele blok ve dosya depolama sistemleri sunmak için bir standarttır. CSı 'yi benimseerek ve kullanarak, SKS, temel Kubernetes koduna dokunarak ve yayın döngüsünü beklemek zorunda kalmadan Kubernetes 'te var olan depolama sistemlerini ortaya çıkarmak veya bunları geliştirmek için eklentileri yazabilir, dağıtabilir ve yineleyebilir.

CSı sürücü desteğiyle bir AKS kümesi oluşturmak için bkz. [Azure diskleri ve aks 'de Azure dosyaları IÇIN CSI sürücülerini etkinleştirme](csi-storage-drivers.md).

>[!NOTE]
> *Ağaç içi sürücüler* , temel Kubernetes kodunun parçası olan geçerli depolama sürücülerini, eklentiler olan yeni CSI sürücülerine karşılık gelir.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Azure diskleriyle CSı kalıcı birimler kullanma

[Kalıcı birim](concepts-storage.md#persistent-volumes) (BD), Kubernetes pods ile kullanılmak üzere sağlanan bir depolama parçasını temsil eder. Bir BD, bir veya daha fazla Pod tarafından kullanılabilir ve dinamik veya statik olarak sağlanabilir. Bu makalede, bir AKS kümesinde tek bir pod tarafından kullanılmak üzere Azure diskleriyle nasıl dinamik olarak PVs oluşturulacağı gösterilmektedir. Statik sağlama için bkz. [Azure diskleriyle bir birimi el ile oluşturma ve kullanma](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Kubernetes birimleri hakkında daha fazla bilgi için bkz. [AKS 'de uygulamalar Için depolama seçenekleri][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Yerleşik depolama sınıflarını kullanarak dinamik olarak Azure disk PVs oluşturma

Depolama sınıfı, bir depolama biriminin kalıcı bir birimle dinamik olarak nasıl oluşturulduğunu tanımlamak için kullanılır. Kubernetes Depolama sınıfları hakkında daha fazla bilgi için bkz. [Kubernetes Depolama sınıfları][kubernetes-storage-classes]. AKS üzerinde Storage CSı sürücülerini kullandığınızda, `StorageClasses` Azure DISK CSI depolama sürücülerini kullanan iki ek yerleşik vardır. Ek CSı Depolama sınıfları, ağaç içi varsayılan depolama sınıflarının yanı sıra kümeyle oluşturulur.

- `managed-csi`: Yönetilen bir disk oluşturmak için Azure Standart SSD yerel olarak yedekli depolama (LRS) kullanır.
- `managed-csi-premium`: Yönetilen bir disk oluşturmak için Azure Premium LRS kullanır.

Her iki depolama sınıflarında geri kazanma ilkesi, ilgili BD silindiğinde temeldeki Azure diskinin silinmesini sağlar. Depolama sınıfları Ayrıca PVs 'yi genişletilebilir olacak şekilde yapılandırır. Yalnızca yeni boyutla kalıcı birim talebi (PVC) düzenlemeniz gerekir.

Bu depolama sınıflarından yararlanmak için, bir [PVC](concepts-storage.md#persistent-volume-claims) ve bunlara başvuran ve bunları kullanan bir pod oluşturun. Bir PVC, depolama sınıfına göre otomatik olarak depolama sağlamak için kullanılır. Bir PVC, istenen SKU ve boyut için Azure tarafından yönetilen bir disk oluşturmak üzere önceden oluşturulmuş depolama sınıflarından veya Kullanıcı tanımlı bir depolama sınıfından birini kullanabilir. Pod tanımı oluşturduğunuzda, PVC istenen depolamayı istemek için belirtilir.

[Kubectl Apply][kubectl-apply] komutuyla örnek bir pod ve ilgili PVC oluşturun:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Pod çalışır durumda olduktan sonra adlı yeni bir dosya oluşturun `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Artık aşağıdaki komutu çalıştırarak diskin doğru şekilde takıldığını doğrulayabilir ve dosyayı çıktıda gördiğinizi doğrularsınız `test.txt` :

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Özel depolama sınıfı oluşturma

Varsayılan depolama sınıfları en yaygın senaryolara sahiptir, ancak hepsini değildir. Bazı durumlarda kendi parametrelerinizi kullanarak kendi depolama sınıfınızın özelleştirilmiş olmasını isteyebilirsiniz. Örneğin, sınıfı değiştirmek isteyebileceğiniz bir senaryonuz vardır `volumeBindingMode` .

`volumeBindingMode: Immediate`PVC oluşturulduktan hemen sonra ortaya çıkan bir sınıfı kullanabilirsiniz. Düğüm havuzlarınızın topoloji sınırlı olduğu durumlarda (örneğin, kullanılabilirlik bölgelerini kullanarak), Pod 'un zamanlama gereksinimleri (Bu durumda belirli bir bölgede olması) hakkında bilgi sahibi olmadan,, PVs 'nin bağlanması veya sağlanması gerekir.

Bu senaryoya yönelik olarak, `volumeBindingMode: WaitForFirstConsumer` PVC 'yi kullanan bir pod oluşturuluncaya kadar BIR BD 'in bağlama ve sağlama işlemlerini gecikmesini sağlayabilirsiniz. Bu şekilde, BD, Pod 'un zamanlama kısıtlamaları tarafından belirtilen kullanılabilirlik bölgesinde (veya başka bir topolojide) uyumlu olacak ve sağlanacak. Varsayılan depolama sınıfları sınıfını kullanır `volumeBindingMode: WaitForFirstConsumer` .

Adlı bir dosya oluşturun `sc-azuredisk-csi-waitforfirstconsumer.yaml` ve aşağıdaki bildirimi yapıştırın.
Depolama sınıfı, `managed-csi` depolama sınıfımızla aynı ancak farklı bir `volumeBindingMode` sınıfla aynıdır.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

[Kubectl Apply][kubectl-apply] komutuyla depolama sınıfını oluşturun ve `sc-azuredisk-csi-waitforfirstconsumer.yaml` dosyanızı belirtin:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Birim anlık görüntüleri

Azure disk CSı sürücüsü [kalıcı birimlerin anlık görüntülerini](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html)oluşturmayı destekler. Bu özelliğin bir parçası olarak, sürücü, parametrede ayarlanan değere (varsayılan olarak, true) bağlı olarak *tam* veya [ *artımlı* anlık görüntüler](../virtual-machines/disks-incremental-snapshots.md) gerçekleştirebilir `incremental` .

Tüm parametrelerle ilgili ayrıntılar için bkz. [Volume Snapshot Class Parameters](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Birim anlık görüntüsü oluşturma

Bu özelliğin bir örneği için, [kubectl Apply][kubectl-apply] komutuyla bir [birim anlık görüntü sınıfı](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) oluşturun:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Şimdi, [Bu öğreticinin başlangıcında dinamik olarak oluşturduğumuz](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes)PVC 'den bir [birim anlık görüntüsü](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) oluşturalım `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Anlık görüntünün doğru şekilde oluşturulduğundan emin olun:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Birim anlık görüntüsüne göre yeni bir PVC oluşturma

Birim anlık görüntüsüne göre yeni bir PVC oluşturabilirsiniz. Önceki adımda oluşturulan anlık görüntüyü kullanın ve [Yeni BIR PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) ve [Yeni bir pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) oluşturup bunu tükettin.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Son olarak, içerik denetlenmeden önce aynı PVC 'nin oluşturulduğundan emin olalım.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Beklendiği gibi, daha önce oluşturmuş olduğunuz dosyayı hala görebiliriz `test.txt` .

## <a name="clone-volumes"></a>Birimleri Kopyala

Kopyalanmış bir birim, var olan bir Kubernetes biriminin yinelemesi olarak tanımlanır. Kubernetes 'te birimleri kopyalama hakkında daha fazla bilgi için, [birim kopyalamaya](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)yönelik kavramsal belgelere bakın.

Azure diskleri için CSı sürücüsü birim kopyalamayı destekler. Göstermek için, [daha önce oluşturulan](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) ve yeni bir pod 'ın [klonlanan bir birimini](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) `azuredisk-pvc` [kullanmak için](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)oluşturun.


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Şimdi aşağıdaki komutu çalıştırarak kopyalanmış birimin içeriğini denetleyebilir ve oluşturulan dosya görtiğimiz dosyayı görmeye devam edebilirsiniz `test.txt` .

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Kalıcı bir birimi yeniden boyutlandırma

Bunun yerine, PVC için daha büyük bir birim isteyebilirsiniz. PVC nesnesini düzenleyin ve daha büyük bir boyut belirtin. Bu değişiklik, BD 'i yedekleyen temeldeki birimin genişletmesinin tetiklemesini tetikler.

> [!NOTE]
> Talebi karşılamak için hiçbir şekilde yeni bir BD oluşturulmaz. Bunun yerine, var olan bir birim yeniden boyutlandırılır.

AKS 'de, yerleşik `managed-csi` depolama sınıfı zaten genişlemeye izin verdiğinden, [daha önce bu depolama sınıfıyla oluşturulan PVC](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes)'yi kullanın. PVC 10-gi kalıcı birimi istedi. Şunu çalıştırarak şunları doğrulayabiliriz:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> Şu anda, Azure disk CSı sürücüsü yalnızca, hiçbir düğüm ilişkili olmayan PVC 'leri yeniden boyutlandırmayı destekler (ve birim belirli bir düğüme bağlı değildir).

Bu nedenle, daha önce oluşturduğumuz Pod 'u silelim:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Alanı artırarak PVC 'yi genişletelim `spec.resources.requests.storage` :

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Şimdi birimin daha büyük olduğunu doğrulayalim:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> PVC, onunla ilişkili bir pod olana kadar yeni boyutu yansıtmaz.

Yeni bir pod oluşturalım:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

Son olarak, PVC 'nin boyutunu ve pod 'un içini onaylayın:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Paylaşılan disk

Azure [paylaşılan diskler](../virtual-machines/disks-shared.md) , Azure diskini aracı düğümlerine aynı anda eklemeye olanak sağlayan bir Azure yönetilen diskler özelliğidir. Yönetilen bir diski birden çok aracı düğümüne eklemek, örneğin yeni bir dağıtım veya mevcut kümelenmiş uygulamaları Azure 'a geçirmek için kullanabileceğiniz bir disk sağlar.

> [!IMPORTANT] 
> Şu anda, `volumeMode: Block` Azure DISK CSI sürücüsü tarafından yalnızca ham blok cihaz () desteklenir. Uygulamalar, ham blok cihaz olarak sunulan paylaşılan disk üzerinde yazma, okuma, kilitleme, önbellek, bağlama ve kilitleme işlemlerini yönetmelidir.

`shared-disk.yaml`Paylaşılan disk depolama sınıfını ve PVC 'yi içeren aşağıdaki komutu kopyalayarak adlı bir dosya oluşturalım:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

[Kubectl Apply][kubectl-apply] komutuyla depolama sınıfını oluşturun ve `shared-disk.yaml` dosyanızı belirtin:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Şimdi aşağıdaki komutu kopyalayarak adlı bir dosya oluşturalım `deployment-shared.yml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

[Kubectl Apply][kubectl-apply] komutuyla dağıtımı oluşturun ve `deployment-shared.yml` dosyanızı belirtin:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Son olarak, blok cihazını Pod içinde denetlim:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Windows kapsayıcıları

Azure disk CSı sürücüsü ayrıca Windows düğümlerini ve kapsayıcıları destekler. Windows kapsayıcıları kullanmak istiyorsanız, Windows düğüm havuzu eklemek için [Windows kapsayıcıları öğreticisini](windows-container-cli.md) izleyin.

Bir Windows düğüm havuzunuz olduktan sonra, gibi yerleşik depolama sınıflarını kullanabilirsiniz `managed-csi` . [](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) `data.txt` Aşağıdaki komutu [kubectl Apply][kubectl-apply] komutuyla dağıtarak, zaman damgalarını dosyaya kaydeden, örnek bir Windows tabanlı durum bilgisi kümesi dağıtabilirsiniz:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Artık şu çalıştırarak birimin içeriğini doğrulayabilirsiniz:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Sonraki adımlar

- Azure dosyaları için CSı sürücülerini nasıl kullanacağınızı öğrenmek için bkz. [Azure dosyalarını CSI sürücüleriyle kullanma](azure-files-csi.md).
- Depolama en iyi uygulamaları hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmetindeki depolama ve yedeklemeler Için en iyi uygulamalar][operator-best-practices-storage].


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
