---
title: Azure Kubernetes hizmetinde (AKS) birden çok düğüm için statik birim oluşturma
description: Azure Kubernetes hizmetinde (aks) birden çok eş zamanlı Pod ile kullanmak üzere Azure dosyaları ile bir birimi el ile oluşturmayı öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 009da6c16d446f2b0d4d3f402c1c1ec63dde34d8
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018725"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde Azure dosya paylaşımıyla bir birimi el ile oluşturma ve kullanma

Kapsayıcı tabanlı uygulamalar genellikle dış veri hacminde verileri erişmesi ve kalıcı hale getirmeniz gerekir. Aynı depolama birimine eşzamanlı olarak birden çok Pod erişimi gerekiyorsa, [sunucu ileti bloğu (SMB) protokolünü][smb-overview]kullanarak bağlanmak için Azure dosyalarını kullanabilirsiniz. Bu makalede, bir Azure dosya paylaşımının el ile nasıl oluşturulacağı ve AKS 'teki Pod 'a nasıl ekleneceği gösterilmektedir.

Kubernetes birimleri hakkında daha fazla bilgi için bkz. [AKS 'de uygulamalar Için depolama seçenekleri][concepts-storage].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü `az --version` bulmak için ' i çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma

Azure dosyalarını bir Kubernetes birimi olarak kullanabilmeniz için önce bir Azure depolama hesabı ve dosya paylaşma oluşturmanız gerekir. Aşağıdaki komutlar *Myaksshare*adlı bir kaynak grubu, bir depolama hesabı ve *Aksshare*adlı bir dosya paylaşımıyla oluşturulur:

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
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

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

Gizli dizi oluşturmak için komutunukullanın.`kubectl create secret` Aşağıdaki örnekte paylaşılan bir adlandırılmış *Azure-Secret* oluşturulur ve önceki adımdan *azurestokıgeaccountname* ve *azurestorampaaccountkey* değeri doldurulur. Mevcut bir Azure Depolama hesabını kullanmak için hesap adı ve anahtarı sağlayın.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Dosya paylaşımından birim olarak bağlama

Azure dosya paylaşımının Pod 'nize bağlanması için, birimi kapsayıcı belirtiminde yapılandırın. Aşağıdaki içerikle adlı `azure-files-pod.yaml` yeni bir dosya oluşturun. Dosya paylaşımının veya gizli dosyanın adını değiştirdiyseniz, *PaylaşımAdı* ve *secretname*' i güncelleştirin. İsterseniz, dosya paylaşımının Pod `mountPath`'a bağlandığı yol olan öğesini güncelleştirin. Windows Server kapsayıcıları için (Şu anda AKS 'de önizlemededir), Windows yol kuralını kullanarak *":"* gibi bir *bağlamayolu* belirtin.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
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

Pod 'u oluşturmak için komutunukullanın.`kubectl`

```console
kubectl apply -f azure-files-pod.yaml
```

Şimdi */mnt/Azure*konumunda bağlanmış bir Azure dosyaları paylaşımında çalışan bir pod var. Paylaşımın başarıyla takıldığını `kubectl describe pod mypod` doğrulamak için ' i kullanabilirsiniz. Aşağıdaki sıkıştırılmış örnek çıktı, kapsayıcıya takılan birimi gösterir:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          nginx:1.15.5
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

## <a name="mount-options"></a>Bağlama seçenekleri

*FileMode* ve *dirmode* Için varsayılan değer, Kubernetes sürüm 1.9.1 ve üzeri için *0755* ' dir. Kuberetes sürüm 1.8.5 veya üzerini içeren bir küme kullanılıyorsa ve kalıcı birim nesnesini statik olarak oluşturduğunuzda, *Persistentvolume* nesnesinde bağlama seçeneklerinin belirtilmesi gerekir. Aşağıdaki örnek *0777*olarak ayarlanır:

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
  storageClassName: azurefile
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

1\.8.0-1.8.4 sürümünün bir kümesini kullanıyorsanız, *RunAsUser* değeri *0*olarak ayarlanmış bir güvenlik bağlamı belirtilebilir. Pod güvenlik bağlamı hakkında daha fazla bilgi için bkz. [güvenlik bağlamını yapılandırma][kubernetes-security-context].

Bağlama seçeneklerinizi güncelleştirmek için, bir *azurefile-Mount-Options-BD. YAML* dosyasını *Persistentvolume*ile oluşturun. Örneğin:

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
  storageClassName: azurefile
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

*Persistentvolume*kullanan bir *Persistentvolumeclaim* ile *azurefile-Mount-Options-PVC. YAML* dosyası oluşturun. Örneğin:

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

*Persistentvolume* ve *persistentvolumeclaim*oluşturmak için komutlarıkullanın.`kubectl`

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

Kalıcı olarak, *Persistentvolumeclaim* 'nize başvuracak ve pod 'nizi güncelleştiren kapsayıcı belirtimini güncelleştirin. Örneğin:

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

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
