---
title: El ile Azure dosyaları paylaşma oluşturma
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes hizmetinde (aks) birden çok eş zamanlı Pod ile kullanmak üzere Azure dosyaları ile bir birimi el ile oluşturmayı öğrenin
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 7f3c8ae63e908f440740277084293a011b80b9d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776097"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde Azure dosya paylaşımıyla bir birimi el ile oluşturma ve kullanma

Kapsayıcı tabanlı uygulamalar genellikle dış veri hacminde verileri erişmesi ve kalıcı hale getirmeniz gerekir. Aynı depolama birimine eşzamanlı olarak birden çok Pod erişimi gerekiyorsa, [sunucu ileti bloğu (SMB) protokolünü][smb-overview]kullanarak bağlanmak için Azure dosyalarını kullanabilirsiniz. Bu makalede, bir Azure dosya paylaşımının el ile nasıl oluşturulacağı ve AKS 'teki Pod 'a nasıl ekleneceği gösterilmektedir.

Kubernetes birimleri hakkında daha fazla bilgi için bkz. [AKS 'de uygulamalar Için depolama seçenekleri][concepts-storage].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma

Azure dosyalarını bir Kubernetes birimi olarak kullanabilmeniz için önce bir Azure depolama hesabı ve dosya paylaşma oluşturmanız gerekir. Aşağıdaki komutlar *Myaksshare* adlı bir kaynak grubu, bir depolama hesabı ve *Aksshare* adlı bir dosya paylaşımıyla oluşturulur:

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Komut dosyası çıktısının sonunda gösterilen depolama hesabı adını ve anahtarını bir yere göz önüne alın. Bu değerler, aşağıdaki adımlardan birinde Kubernetes birimini oluştururken gereklidir.

## <a name="create-a-kubernetes-secret"></a>Kubernetes gizli dizisi oluşturma

Kubernetes, önceki adımda oluşturulan dosya paylaşımının erişimine yönelik kimlik bilgilerine ihtiyaç duyuyor. Bu kimlik bilgileri, bir Kubernetes Pod oluşturduğunuzda başvurulan bir [Kubernetes gizli][kubernetes-secret]dizisi içinde depolanır.

Gizli dizi `kubectl create secret` oluşturmak için komutunu kullanın. Aşağıdaki örnekte paylaşılan bir adlandırılmış *Azure-Secret* oluşturulur ve önceki adımdan *azurestokıgeaccountname* ve *azurestorampaaccountkey* değeri doldurulur. Mevcut bir Azure Depolama hesabını kullanmak için hesap adı ve anahtarı sağlayın.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>Dosya paylaşımından satır içi birim olarak bağlama
> Note: 1.18.15, 1.19.7, 1.20.2, 1.21.0, satır içi birimdeki gizli ad alanı `azureFile` yalnızca ad alanı olarak ayarlanabilir, `default` farklı bir gizli ad alanı belirtmek için lütfen bunun yerine aşağıdaki kalıcı birim örneğini kullanın.

Azure dosya paylaşımının Pod 'nize bağlanması için, birimi kapsayıcı belirtiminde yapılandırın. Aşağıdaki içerikle adlı yeni bir dosya oluşturun `azure-files-pod.yaml` . Dosya paylaşımının veya gizli dosyanın adını değiştirdiyseniz, *PaylaşımAdı* ve *secretname*' i güncelleştirin. İsterseniz, `mountPath` dosya paylaşımının Pod 'a bağlandığı yol olan öğesini güncelleştirin. Windows Server kapsayıcıları için, Windows yol kuralını kullanarak *":"* gibi bir *bağlamayolu* belirtin.

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
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

`kubectl`Pod 'u oluşturmak için komutunu kullanın.

```console
kubectl apply -f azure-files-pod.yaml
```

Şimdi */mnt/Azure* konumunda bağlanmış bir Azure dosyaları paylaşımında çalışan bir pod var. `kubectl describe pod mypod`Paylaşımın başarıyla takıldığını doğrulamak için ' i kullanabilirsiniz. Aşağıdaki sıkıştırılmış örnek çıktı, kapsayıcıya takılan birimi gösterir:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-file-share-as-an-persistent-volume"></a>Dosya paylaşımının kalıcı birim olarak bağlanması
 - Bağlama seçenekleri

*FileMode* ve *dirmode* Için varsayılan değer, Kubernetes sürüm 1,15 ve üzeri için *0777* ' dir. Aşağıdaki örnek, *Persistentvolume* nesnesinde *0755* değerini ayarlar:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    secretNamespace: default
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0755
  - file_mode=0755
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Bağlama seçeneklerinizi güncelleştirmek için, bir *azurefile-Mount-Options-BD. YAML* dosyasını *Persistentvolume* ile oluşturun. Örnek:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

*Persistentvolume* kullanan bir *Persistentvolumeclaim* ile *azurefile-Mount-Options-PVC. YAML* dosyası oluşturun. Örnek:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 5Gi
```

`kubectl` *Persistentvolume* ve *Persistentvolumeclaim* oluşturmak için komutları kullanın.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

*Persistentvolumeclaim* 'Nin oluşturulup *Persistentvolume*'e bağlandığını doğrulayın.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Kalıcı olarak, *Persistentvolumeclaim* 'nize başvuracak ve pod 'nizi güncelleştiren kapsayıcı belirtimini güncelleştirin. Örnek:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Sonraki adımlar

İlişkili en iyi uygulamalar için bkz. [AKS 'de depolama ve yedeklemeler Için en iyi uygulamalar][operator-best-practices-storage].

AKS kümeleri hakkında daha fazla bilgi için Azure dosyaları [Için Kubernetes eklentisine][kubernetes-files]bakın.

Depolama sınıfı parametreleri için bkz. [statik sağlama (kendi dosya paylaşımınızı getir)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share).

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
