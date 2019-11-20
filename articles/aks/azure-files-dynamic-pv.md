---
title: Azure Kubernetes Service (AKS) içinde birden çok düğüm için dinamik olarak dosya birimi oluşturma
description: Azure Kubernetes hizmetinde (aks) birden çok eş zamanlı Pod ile kullanmak üzere Azure dosyaları ile kalıcı olarak kalıcı bir birim oluşturmayı öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 09/12/2019
ms.author: mlearned
ms.openlocfilehash: 045fcb3286c89097459a4a8405d22ee70e44c205
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018830"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ile Azure dosyaları ile kalıcı bir birimi dinamik olarak oluşturma ve kullanma

Kalıcı bir birim, Kubernetes pods ile kullanılmak üzere sağlanmış bir depolama parçasını temsil eder. Kalıcı bir birim bir veya daha fazla sayıda pods tarafından kullanılabilir ve dinamik veya statik olarak sağlanabilir. Aynı depolama birimine eşzamanlı olarak birden çok Pod erişimi gerekiyorsa, [sunucu ileti bloğu (SMB) protokolünü][smb-overview]kullanarak bağlanmak için Azure dosyalarını kullanabilirsiniz. Bu makalede, bir Azure Kubernetes Service (AKS) kümesinde birden çok düğüm tarafından kullanılmak üzere Azure dosya paylaşımının nasıl dinamik olarak oluşturulacağı gösterilmektedir.

Kubernetes birimleri hakkında daha fazla bilgi için bkz. [AKS 'de uygulamalar Için depolama seçenekleri][concepts-storage].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü `az --version` bulmak için ' i çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek][install-azure-cli].

## <a name="create-a-storage-class"></a>Depolama sınıfı oluşturma

Bir Azure dosya paylaşımının nasıl oluşturulduğunu tanımlamak için bir depolama sınıfı kullanılır. Depolama hesabı, Azure dosya paylaşımlarını tutmak üzere depolama sınıfıyla kullanılmak üzere [düğüm kaynak grubunda][node-resource-group] otomatik olarak oluşturulur. *Skuname*Için aşağıdaki [Azure depolama yedekliliği][storage-skus] arasından seçim yapın:

* *Standard_LRS* -standart yerel olarak yedekli depolama (LRS)
* *Standard_GRS* -standart coğrafi olarak yedekli depolama (GRS)
* *Standard_RAGRS* -standart Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS)
* *Premium_LRS* -Premium yerel olarak yedekli depolama (LRS)

> [!NOTE]
> Azure dosyaları, Kubernetes 1,13 veya üstünü çalıştıran AKS kümelerindeki Premium depolamayı destekler.

Azure dosyaları için Kubernetes Depolama sınıfları hakkında daha fazla bilgi için bkz. [Kubernetes Depolama sınıfları][kubernetes-storage-classes].

Aşağıdaki örnek bildiriminde adlı `azure-file-sc.yaml` bir dosya oluşturun ve kopyalayın. *Mountoptions*hakkında daha fazla bilgi için [bağlama seçenekleri][mount-options] bölümüne bakın.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

[Kubectl Apply][kubectl-apply] komutuyla depolama sınıfını oluşturun:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Küme rolü oluşturma ve bağlama

AKS kümeleri, gerçekleştirilebilecek eylemleri sınırlandırmak için Kubernetes rol tabanlı erişim denetimi 'ni (RBAC) kullanır. *Roller* , verilecek izinleri tanımlar ve *bağlamalar* onları istenen kullanıcılara uygular. Bu atamalar, belirli bir ad alanına veya tüm küme genelinde uygulanabilir. Daha fazla bilgi için bkz. [RBAC yetkilendirmesi kullanma][kubernetes-rbac].

Azure platformunun gerekli depolama kaynaklarını oluşturmasına izin vermek için bir *Clusterrole* ve *clusterrolebinding*oluşturun. Aşağıdaki YAML 'de `azure-pvc-roles.yaml` adlı bir dosya oluşturun ve kopyalayın:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

[Kubectl Apply][kubectl-apply] komutuyla izinleri atayın:

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Kalıcı bir birim talebi oluşturun

Kalıcı bir birim talebi (PVC), bir Azure dosya paylaşımının dinamik olarak sağlanması için depolama sınıfı nesnesini kullanır. Aşağıdaki YAML, *Readwritemany* ERIŞIMIYLE *5 GB* boyutunda kalıcı bir birim talebi oluşturmak için kullanılabilir. Erişim modları hakkında daha fazla bilgi için bkz. [Kubernetes kalıcı birimi][access-modes] belgeleri.

Şimdi aşağıdaki YAML 'de `azure-file-pvc.yaml` adlı bir dosya oluşturun ve kopyalayın. *Storageclassname* 'in, son adımda oluşturulan depolama sınıfıyla eşleştiğinden emin olun:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> Depolama sınıfınız için *Premium_LRS* SKU 'su kullanılıyorsa, *depolama* Için en düşük değer *100gi*olmalıdır.

[Kubectl Apply][kubectl-apply] komutuyla kalıcı birim talebi oluşturun:

```console
kubectl apply -f azure-file-pvc.yaml
```

İşlem tamamlandıktan sonra dosya paylaşma oluşturulur. Bağlantı bilgilerini ve kimlik bilgilerini içeren bir Kubernetes gizli dizisi de oluşturulur. PVC 'nin durumunu görüntülemek için [kubectl Get][kubectl-get] komutunu kullanabilirsiniz:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Kalıcı birimi kullan

Aşağıdaki YAML, */mnt/Azure* yolundaki Azure dosya paylaşımının bağlanması için kalıcı birim talebi *azurefile* kullanan bir pod oluşturur. Windows Server kapsayıcıları için (Şu anda AKS 'de önizlemededir), Windows yol kuralını kullanarak *":"* gibi bir *bağlamayolu* belirtin.

Adlı `azure-pvc-files.yaml`bir dosya oluşturun ve aşağıdaki YAML 'ye kopyalayın. *Claimname* 'in, son ADıMDA oluşturulan PVC ile eşleştiğinden emin olun.

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
        claimName: azurefile
```

[Kubectl Apply][kubectl-apply] komutuyla Pod 'ı oluşturun.

```console
kubectl apply -f azure-pvc-files.yaml
```

Artık, */mnt/Azure* dizininde oluşturulmuş Azure dosya paylaşımınızla çalışan bir pod sahipsiniz. Bu yapılandırma, Pod 'niz aracılığıyla `kubectl describe pod mypod`incelenirken görülebilir. Aşağıdaki sıkıştırılmış örnek çıktı, kapsayıcıya takılan birimi gösterir:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
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
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Bağlama seçenekleri

*FileMode* ve *dirmode* Için varsayılan değer, Kubernetes sürüm 1.9.1 ve üzeri için *0755* ' dir. Kuberetes sürüm 1.8.5 veya üzerini içeren bir küme kullanıyorsanız ve kalıcı birimi bir depolama sınıfıyla dinamik olarak oluşturursanız, depolama sınıfı nesnesinde bağlama seçenekleri belirtilebilir. Aşağıdaki örnek *0777*olarak ayarlanır:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

1\.8.0-1.8.4 sürümünün bir kümesini kullanıyorsanız, *RunAsUser* değeri *0*olarak ayarlanmış bir güvenlik bağlamı belirtilebilir. Pod güvenlik bağlamı hakkında daha fazla bilgi için bkz. [güvenlik bağlamını yapılandırma][kubernetes-security-context].

## <a name="next-steps"></a>Sonraki adımlar

İlişkili en iyi uygulamalar için bkz. [AKS 'de depolama ve yedeklemeler Için en iyi uygulamalar][operator-best-practices-storage].

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
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks