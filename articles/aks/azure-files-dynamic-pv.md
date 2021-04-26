---
title: Azure dosya paylaşımının dinamik olarak oluşturulması
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes hizmetinde (aks) birden çok eş zamanlı Pod ile kullanmak üzere Azure dosyaları ile kalıcı olarak kalıcı bir birim oluşturmayı öğrenin
services: container-service
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: f301a01e479d03647bebf7cb042564a258e9250e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776132"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service'te (AKS) Azure Dosyalar ile dinamik olarak kalıcı birim oluşturma ve kullanma

Kalıcı bir birim, Kubernetes pods ile kullanılmak üzere sağlanmış bir depolama parçasını temsil eder. Kalıcı bir birim bir veya daha fazla sayıda pods tarafından kullanılabilir ve dinamik veya statik olarak sağlanabilir. Aynı depolama birimine eşzamanlı olarak birden çok Pod erişimi gerekiyorsa, [sunucu ileti bloğu (SMB) protokolünü][smb-overview]kullanarak bağlanmak için Azure dosyalarını kullanabilirsiniz. Bu makalede, bir Azure Kubernetes Service (AKS) kümesinde birden çok düğüm tarafından kullanılmak üzere Azure dosya paylaşımının nasıl dinamik olarak oluşturulacağı gösterilmektedir.

Kubernetes birimleri hakkında daha fazla bilgi için bkz. [AKS 'de uygulamalar Için depolama seçenekleri][concepts-storage].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

## <a name="create-a-storage-class"></a>Depolama sınıfı oluşturma

Bir Azure dosya paylaşımının nasıl oluşturulduğunu tanımlamak için bir depolama sınıfı kullanılır. Depolama hesabı, Azure dosya paylaşımlarını tutmak üzere depolama sınıfıyla kullanılmak üzere [düğüm kaynak grubunda][node-resource-group] otomatik olarak oluşturulur. *Skuname* Için aşağıdaki [Azure depolama yedekliliği][storage-skus] arasından seçim yapın:

* *Standard_LRS* -standart yerel olarak yedekli depolama (LRS)
* *Standard_GRS* -standart coğrafi olarak yedekli depolama (GRS)
* *Standard_ZRS* -standart bölge yedekli depolama (ZRS)
* *Standard_RAGRS* -standart Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS)
* *Premium_LRS* -Premium yerel olarak yedekli depolama (LRS)
* *Premium_ZRS* -Premium bölge yedekli depolama (ZRS)

> [!NOTE]
> Azure dosyaları, Kubernetes 1,13 veya üstünü çalıştıran AKS kümelerinde Premium depolamayı destekler, en düşük Premium dosya paylaşma 100 GB 'dir

Azure dosyaları için Kubernetes Depolama sınıfları hakkında daha fazla bilgi için bkz. [Kubernetes Depolama sınıfları][kubernetes-storage-classes].

Aşağıdaki örnek bildiriminde adlı bir dosya oluşturun `azure-file-sc.yaml` ve kopyalayın. *Mountoptions* hakkında daha fazla bilgi için [bağlama seçenekleri][mount-options] bölümüne bakın.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

[Kubectl Apply][kubectl-apply] komutuyla depolama sınıfını oluşturun:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Kalıcı bir birim talebi oluşturun

Kalıcı bir birim talebi (PVC), bir Azure dosya paylaşımının dinamik olarak sağlanması için depolama sınıfı nesnesini kullanır. Aşağıdaki YAML, *Readwritemany* ERIŞIMIYLE *5 GB* boyutunda kalıcı bir birim talebi oluşturmak için kullanılabilir. Erişim modları hakkında daha fazla bilgi için bkz. [Kubernetes kalıcı birimi][access-modes] belgeleri.

Şimdi `azure-file-pvc.yaml` aşağıdaki YAML 'de adlı bir dosya oluşturun ve kopyalayın. *Storageclassname* 'in, son adımda oluşturulan depolama sınıfıyla eşleştiğinden emin olun:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: my-azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> Depolama sınıfınız için *Premium_LRS* SKU kullanıyorsanız, *depolama* Için en düşük değer *100gi* olmalıdır.

[Kubectl Apply][kubectl-apply] komutuyla kalıcı birim talebi oluşturun:

```console
kubectl apply -f azure-file-pvc.yaml
```

İşlem tamamlandıktan sonra dosya paylaşma oluşturulur. Bağlantı bilgilerini ve kimlik bilgilerini içeren bir Kubernetes gizli dizisi de oluşturulur. PVC 'nin durumunu görüntülemek için [kubectl Get][kubectl-get] komutunu kullanabilirsiniz:

```console
$ kubectl get pvc my-azurefile

NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
my-azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            my-azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Kalıcı birimi kullan

Aşağıdaki YAML, */mnt/Azure* yolundaki Azure dosya paylaşımının bağlanması için *My-azurefile* kalıcı Volume Claim 'i kullanan bir pod oluşturur. Windows Server kapsayıcıları için, Windows yol kuralını kullanarak *":"* gibi bir *bağlamayolu* belirtin.

Adlı bir dosya oluşturun `azure-pvc-files.yaml` ve aşağıdaki YAML 'ye kopyalayın. *Claimname* 'in, son ADıMDA oluşturulan PVC ile eşleştiğinden emin olun.

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
        claimName: my-azurefile
```

[Kubectl Apply][kubectl-apply] komutuyla Pod 'ı oluşturun.

```console
kubectl apply -f azure-pvc-files.yaml
```

Artık, */mnt/Azure* dizininde oluşturulmuş Azure dosya paylaşımınızla çalışan bir pod sahipsiniz. Bu yapılandırma, Pod 'niz aracılığıyla incelenirken görülebilir `kubectl describe pod mypod` . Aşağıdaki sıkıştırılmış örnek çıktı, kapsayıcıya takılan birimi gösterir:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  my-azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Bağlama seçenekleri

*FileMode* ve *dirmode* Için varsayılan değer, Kubernetes sürüm 1.13.0 ve üzeri için *0777* ' dir. Kalıcı birimi bir depolama sınıfıyla dinamik olarak oluşturduğunuzda, bağlama seçenekleri depolama sınıfı nesnesinde belirtilebilir. Aşağıdaki örnek *0777* olarak ayarlanır:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Sonraki adımlar

İlişkili en iyi uygulamalar için bkz. [AKS 'de depolama ve yedeklemeler Için en iyi uygulamalar][operator-best-practices-storage].

Depolama sınıfı parametreleri için bkz. [dinamik sağlama](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#dynamic-provision).

Azure dosyalarını kullanarak Kubernetes kalıcı birimleri hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure dosyaları için Kubernetes eklentisi][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/aks#az_aks_show
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
