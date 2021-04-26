---
title: Azure Kubernetes Service (AKS) üzerinde Azure dosyaları için kapsayıcı depolama arabirimi (CSı) sürücülerini kullanma
description: Azure Kubernetes Service (AKS) kümesinde Azure dosyaları için kapsayıcı depolama arabirimi (CSı) sürücülerini nasıl kullanacağınızı öğrenin.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: a83d2222862db6bc3e3ff86ba4074114c1a872e5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776168"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service (AKS) içindeki Azure dosya kapsayıcısı depolama arabirimi (CSı) sürücülerini kullanma (Önizleme)

Azure dosya kapsayıcısı depolama arabirimi (CSı) sürücüsü, Azure dosya paylaşımlarının yaşam döngüsünü yönetmek için Azure Kubernetes hizmeti (AKS) tarafından kullanılan bir [CSI belirtimiyle](https://github.com/container-storage-interface/spec/blob/master/spec.md)uyumlu bir sürücü.

CSı, Kubernetes üzerindeki Kapsayıcılı iş yüklerine rastgele blok ve dosya depolama sistemleri sunmak için bir standarttır. PKS, CSı 'yi benimseerek ve kullanarak, temel Kubernetes koduna dokunarak ve kendi yayın döngüsünü beklemek zorunda kalmadan Kubernetes 'te yeni veya daha fazla depolama sistemi geliştirmek için eklentileri yazabilir, dağıtabilir ve yineleyebilir.

CSı sürücü desteğiyle bir AKS kümesi oluşturmak için bkz. [Azure diskleri ve aks 'de Azure dosyaları IÇIN CSI sürücülerini etkinleştirme](csi-storage-drivers.md).

>[!NOTE]
> *Ağaç içi sürücüler* , temel Kubernetes kodunun parçası olan geçerli depolama sürücülerini, eklentiler olan yeni CSI sürücülerine karşılık gelir.

## <a name="use-a-persistent-volume-with-azure-files"></a>Azure dosyaları ile kalıcı bir birim kullanma

[Kalıcı birim (BD)](concepts-storage.md#persistent-volumes) , Kubernetes pods ile kullanılmak üzere sağlanan bir depolama parçasını temsil eder. Bir BD, bir veya daha fazla Pod tarafından kullanılabilir ve dinamik veya statik olarak sağlanabilir. Aynı depolama birimine eşzamanlı olarak birden çok dizin gerekiyorsa, [sunucu Ileti bloğu (SMB) protokolünü][smb-overview]kullanarak bağlanmak Için Azure dosyalarını kullanabilirsiniz. Bu makalede, bir aks kümesinde birden çok Pod tarafından kullanılmak üzere bir Azure dosyaları paylaşımının dinamik olarak nasıl oluşturulacağı gösterilir. Statik sağlama için bkz. [Azure dosya paylaşımıyla bir birimi el ile oluşturma ve kullanma](azure-files-volume.md).

Kubernetes birimleri hakkında daha fazla bilgi için bkz. [AKS 'de uygulamalar Için depolama seçenekleri][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Yerleşik depolama sınıflarını kullanarak Azure dosyaları PVs 'yi dinamik olarak oluşturma

Bir Azure dosya paylaşımının nasıl oluşturulduğunu tanımlamak için bir depolama sınıfı kullanılır. Depolama hesabı, Azure dosya paylaşımlarını tutmak üzere depolama sınıfıyla kullanılmak üzere [düğüm kaynak grubunda][node-resource-group] otomatik olarak oluşturulur. *Skuname* Için aşağıdaki [Azure Storage artıklık SKU][storage-skus] 'larından birini seçin:

* **Standard_LRS**: standart yerel olarak yedekli depolama
* **Standard_GRS**: Standart coğrafi olarak yedekli depolama
* **Standard_ZRS**: standart bölge-yedekli depolama
* **Standard_RAGRS**: Standart Okuma Erişimli Coğrafi olarak yedekli depolama
* **Premium_LRS**: Premium yerel olarak yedekli depolama

> [!NOTE]
> Azure dosyaları, Azure Premium depolamayı destekler. En düşük Premium dosya paylaşma 100 GB 'dir.

AKS üzerinde Storage CSı sürücülerini kullandığınızda, `StorageClasses` Azure dosyaları CSI depolama sürücülerini kullanan iki ek yerleşik vardır. Ek CSı Depolama sınıfları, ağaç içi varsayılan depolama sınıflarının yanı sıra kümeyle oluşturulur.

- `azurefile-csi`: Azure Standart depolama kullanarak bir Azure dosya paylaşımının oluşturulmasını sağlar.
- `azurefile-csi-premium`: Azure dosya paylaşma oluşturmak için Azure Premium depolama kullanır.

Her iki depolama sınıfında geri kazanma ilkesi, ilgili BD silindiğinde temeldeki Azure dosya paylaşımının silinmesini sağlar. Depolama sınıfları Ayrıca, dosya paylaşımlarını genişletilebilir olacak şekilde yapılandırır, yalnızca yeni boyutla kalıcı birim talebi (PVC) düzenlemeniz gerekir.

Bu depolama sınıflarını kullanmak için, bir [PVC](concepts-storage.md#persistent-volume-claims) ve bunlara başvuran ve bunları kullanan bir pod oluşturun. Bir PVC, depolama sınıfına göre otomatik olarak depolama sağlamak için kullanılır. Bir PVC, istenen SKU ve boyut için bir Azure dosya paylaşımının oluşturulması için önceden oluşturulmuş depolama sınıflarından birini veya Kullanıcı tanımlı bir depolama sınıfını kullanabilir. Pod tanımı oluşturduğunuzda, PVC istenen depolamayı istemek için belirtilir.

Şu anki tarihi, [kubectl Apply][kubectl-apply] komutuyla [bir `outfile` IÇINE yazdıran örnek bir PVC ve pod](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) oluşturun:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Pod çalışır durumda olduktan sonra, aşağıdaki komutu çalıştırarak ve çıktının şunu içerdiğini doğrulayarak dosya paylaşımının doğru şekilde takıldığını doğrulayabilirsiniz `outfile` :

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Özel depolama sınıfı oluşturma

Varsayılan depolama sınıfları en yaygın senaryolara sahiptir, ancak hepsini değildir. Bazı durumlarda kendi parametrelerinizi kullanarak kendi depolama sınıfınızın özelleştirilmiş olmasını isteyebilirsiniz. Örneğin, dosya paylaşımının yapılandırmasını yapılandırmak için aşağıdaki bildirimi kullanın `mountOptions` .

*FileMode* ve *dirmode* Için varsayılan değer, Kubernetes bağlı dosya paylaşımları için *0777* ' dir. Depolama sınıfı nesnesinde farklı bağlama seçeneklerini belirtebilirsiniz.

Adlı bir dosya oluşturun `azure-file-sc.yaml` ve aşağıdaki örnek bildirimi yapıştırın:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

[Kubectl Apply][kubectl-apply] komutuyla depolama sınıfını oluşturun:

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Azure Files CSı sürücüsü, kalıcı birimlerin ve temel dosya paylaşımlarının [anlık görüntülerini](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) oluşturmayı destekler.

[Kubectl Apply][kubectl-apply] komutuyla bir [birim anlık görüntü sınıfı](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) oluşturun:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

[Bu öğreticinin başlangıcında dinamik olarak oluşturulan](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes)PVC 'den bir [birim anlık görüntüsü](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) oluşturun `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Anlık görüntünün doğru şekilde oluşturulduğunu doğrulayın:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>Kalıcı bir birimi yeniden boyutlandırma

Bir PVC için daha büyük bir birim isteyebilirsiniz. PVC nesnesini düzenleyin ve daha büyük bir boyut belirtin. Bu değişiklik, BD 'i yedekleyen temeldeki birimin genişletmesinin tetiklemesini tetikler.

> [!NOTE]
> Talebi karşılamak için hiçbir şekilde yeni bir BD oluşturulmaz. Bunun yerine, var olan bir birim yeniden boyutlandırılır.

AKS 'de, yerleşik `azurefile-csi` depolama sınıfı genişletmeyi zaten desteklediğinden, [Bu depolama sınıfıyla daha önce oluşturulan PVC](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes)'yi kullanın. PVC bir 100Gi dosya paylaşımının istedi. Şunu çalıştırarak şunları doğrulayabiliriz:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Alanı artırarak PVC 'yi genişletin `spec.resources.requests.storage` :

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Pod 'ın içindeki hem PVC 'nin hem de dosya sisteminin yeni boyutu göstermesini doğrulayın:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>NFS dosya paylaşımları
[Azure dosyaları artık NFS v 4.1 protokolünü destekler](../storage/files/storage-files-how-to-create-nfs-shares.md). Azure dosyaları için NFS 4,1 desteği, yüksek oranda kullanılabilir ve yüksek oranda dayanıklı bir dağıtılmış dayanıklı depolama platformunda oluşturulmuş bir hizmet olarak tam olarak yönetilen bir NFS dosya sistemi sağlar.

 Bu seçenek, yerinde veri güncelleştirmeleriyle rastgele erişim iş yükleri için iyileştirilmiştir ve tam POSIX dosya sistemi desteği sağlar. Bu bölümde, bir AKS kümesinde Azure dosya CSı sürücüsüyle NFS paylaşımlarının nasıl kullanılacağı gösterilmektedir.

Önizleme aşamasında [sınırlamaları](../storage/files/storage-files-compare-protocols.md#limitations) ve [bölge kullanılabilirliğini](../storage/files/storage-files-compare-protocols.md#regional-availability) denetlediğinizden emin olun.

### <a name="register-the-allownfsfileshares-preview-feature"></a>`AllowNfsFileShares`Önizleme özelliğini kaydetme

NFS 4,1 kullanan bir dosya paylaşma oluşturmak için, `AllowNfsFileShares` aboneliğinizde Özellik bayrağını etkinleştirmeniz gerekir.

`AllowNfsFileShares`Aşağıdaki örnekte gösterildiği gibi, [az Feature Register][az-feature-register] komutunu kullanarak özellik bayrağını kaydedin:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Durumun *kayıtlı* gösterilmesi birkaç dakika sürer. [Az Feature List][az-feature-list] komutunu kullanarak kayıt durumunu doğrulayın:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

Hazırlandığınızda, [az Provider Register][az-provider-register] komutunu kullanarak *Microsoft. Storage* kaynak sağlayıcısı kaydını yenileyin:

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>NFS dosya paylaşımında bir depolama hesabı oluşturma

[Oluşturma `Premium_LRS` ](../storage/files/storage-how-to-create-file-share.md) NFS paylaşımlarını desteklemek için aşağıdaki yapılandırmalara sahip Azure depolama hesabı:
- hesap türü: dosya depolama
- güvenli aktarım gerekli (yalnızca HTTPS trafiğini etkinleştir): false
- Güvenlik duvarları ve sanal ağlarda aracı düğümlerinizin sanal ağını seçin. bu nedenle MC_ kaynak grubunda depolama hesabını oluşturmayı tercih edebilirsiniz.

### <a name="create-nfs-file-share-storage-class"></a>NFS dosya paylaşma depolama sınıfı oluştur

`nfs-sc.yaml`İlgili yer tutucuları düzenleyen aşağıdaki bildirimle bir dosyayı kaydedin.

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

Dosyayı düzenledikten ve kaydettikten sonra, [kubectl Apply][kubectl-apply] komutuyla depolama sınıfını oluşturun:

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>NFS ile desteklenen bir dosya paylaşımıyla dağıtım oluşturma
[](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) `data.txt` Aşağıdaki komutu [kubectl Apply][kubectl-apply] komutuyla dağıtarak, zaman damgalarını bir dosyaya kaydeden bir örnek durum bilgisi kümesi dağıtabilirsiniz:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

Şu çalıştırarak birimin içeriğini doğrulayın:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> NFS dosya paylaşımının Premium hesabında olduğundan, en düşük dosya paylaşma boyutunun 100 GB olduğunu unutmayın. Küçük depolama boyutu olan bir PVC oluşturursanız, "dosya paylaşma oluşturulamadı... Boyut (5)... ".


## <a name="windows-containers"></a>Windows kapsayıcıları

Azure Files CSı sürücüsü ayrıca Windows düğümlerini ve kapsayıcıları destekler. Windows kapsayıcıları kullanmak istiyorsanız, Windows düğüm havuzu eklemek için [Windows kapsayıcıları öğreticisini](windows-container-cli.md) izleyin.

Bir Windows düğüm havuzunuz olduktan sonra, gibi yerleşik depolama sınıflarını kullanın `azurefile-csi` veya özel bir tane oluşturun. [](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) `data.txt` Aşağıdaki komutu [kubectl Apply][kubectl-apply] komutuyla dağıtarak, zaman damgalarını bir dosyaya kaydeden örnek bir Windows tabanlı durum belirleme kümesi dağıtabilirsiniz:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

Şu çalıştırarak birimin içeriğini doğrulayın:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Sonraki adımlar

- Azure diskleri için CSı sürücülerini nasıl kullanacağınızı öğrenmek için bkz. [CSI sürücüleriyle Azure disklerini kullanma](azure-disk-csi.md).
- Depolama en iyi uygulamaları hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmetindeki depolama ve yedeklemeler Için en iyi uygulamalar][operator-best-practices-storage].


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md