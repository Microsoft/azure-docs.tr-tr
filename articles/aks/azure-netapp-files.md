---
title: Azure NetApp Files Azure Kubernetes hizmeti ile tümleştirme
description: Azure NetApp Files Azure Kubernetes hizmeti ile tümleştirme hakkında bilgi edinin
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 84192a831e3b1f24e20eb07a6c8695516c28970f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329337"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure NetApp Files Azure Kubernetes hizmeti ile tümleştirme

[Azure NetApp Files][anf] , Azure üzerinde çalışan kurumsal sınıf, yüksek performanslı ve tarifeli bir dosya depolama hizmetidir. Bu makalede Azure NetApp Files Azure Kubernetes Service (AKS) ile nasıl tümleştirileceği gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce
Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

> [!IMPORTANT]
> AKS kümeniz de [Azure NetApp Files destekleyen bir bölgede][anf-regions]olmalıdır.

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü `az --version` bulmak için ' i çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek][install-azure-cli].

### <a name="limitations"></a>Sınırlamalar

Azure NetApp Files kullandığınızda aşağıdaki sınırlamalar geçerlidir:

* Azure NetApp Files yalnızca [Seçili Azure bölgelerinde][anf-regions]kullanılabilir.
* Azure NetApp Files kullanabilmeniz için, Azure NetApp Files hizmetine erişim verilmesi gerekir. Erişim için uygulamak üzere [Azure NetApp Files eklenebileceğinizi gönderim formunu][anf-waitlist]kullanabilirsiniz. Azure NetApp Files ekibinden resmi onay e-postasını yapana kadar Azure NetApp Files hizmetine erişemezsiniz.
* Azure NetApp Files hizmetinizin AKS kümeniz ile aynı sanal ağda oluşturulması gerekir.
* AKS 'de yalnızca Azure NetApp Files statik sağlama desteklenir.

## <a name="configure-azure-netapp-files"></a>Azure NetApp Files Yapılandır

> [!IMPORTANT]
> *Microsoft. NetApp* kaynak sağlayıcısını kaydedebilmeniz için, aboneliğiniz için [Azure NetApp Files eklenebileceğinizi gönderim formunu][anf-waitlist] doldurmanız gerekir. Azure NetApp Files ekibinden resmi onay e-postasını yapana kadar kaynağı kaydedemezsiniz.

*Microsoft. NetApp* kaynak sağlayıcısını kaydedin:

```azure-cli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Bu işlem biraz zaman alabilir.

AKS ile kullanılmak üzere bir Azure NetApp hesabı oluşturduğunuzda, hesabı **düğüm** kaynak grubunda oluşturmanız gerekir. İlk olarak, [az aks Show][az-aks-show] komutuyla kaynak grubu adını alın ve `--query nodeResourceGroup` sorgu parametresini ekleyin. Aşağıdaki örnek, *Myresourcegroup*kaynak grubu adında *Myakscluster* adlı aks kümesi için düğüm kaynak grubunu alır:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

[Az netappfiles hesap Create][az-netappfiles-account-create]kullanarak aks kümeniz ile aynı bölgede, **düğüm** kaynak grubunda bir Azure NetApp Files hesabı oluşturun. Aşağıdaki örnek *MC_myResourceGroup_myAKSCluster_eastus* kaynak grubunda ve *eastus* bölgesinde *myaccount1* adlı bir hesap oluşturur:

```azure-cli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

[Az netappfiles Havuz oluştur][az-netappfiles-pool-create]kullanarak yeni bir kapasite havuzu oluşturun. Aşağıdaki örnek, boyut ve *Premium* hizmet DÜZEYINDE 4 TB ile *mypool1* adlı yeni bir kapasite havuzu oluşturur:

```azure-cli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

[Az Network VNET subnet Create][az-network-vnet-subnet-create]kullanarak [Azure NetApp Files temsilci seçmek][anf-delegate-subnet] için bir alt ağ oluşturun. *Bu alt ağ, AKS kümeniz ile aynı sanal ağda olmalıdır.*

```azure-cli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

[Az netappfiles birim oluştur][az-netappfiles-volume-create]kullanarak bir birim oluşturun.

```azure-cli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>PersistentVolume oluşturma

[Az netappfiles birim Show][az-netappfiles-volume-show] kullanarak biriminizin ayrıntılarını listeleyin
```azure-cli
$ az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"

{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

@No__t oluşturun-bir PersistentVolume tanımlayarak 0. @No__t-0 ' i *Creationtoken* ve `server` ' den önceki komuttan *IPAddress* ile değiştirin. Örneğin:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Önceki adımda oluşturduğunuz NFS (ağ dosya sistemi) hacminin *sunucu* ve *yolunu* güncelleştirin. [Kubectl Apply][kubectl-apply] komutuyla PersistentVolume oluşturun:

```console
kubectl apply -f pv-nfs.yaml
```

[Kubectl açıkla][kubectl-describe] komutunu kullanarak PersistentVolume *durumunun* *kullanılabilir* olduğunu doğrulayın:

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>PersistentVolumeClaim oluşturma

@No__t oluşturun-bir PersistentVolume tanımlayarak 0. Örneğin:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

[Kubectl Apply][kubectl-apply] komutuyla PersistentVolumeClaim oluşturun:

```console
kubectl apply -f pvc-nfs.yaml
```

PersistentVolumeClaim *durumunun* , [kubectl açıkla][kubectl-describe] komutunu kullanarak *bağlandığını* doğrulayın:

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Pod ile bağlama

PersistentVolumeClaim kullanan bir pod tanımlayan bir @no__t oluşturun-0. Örneğin:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

[Kubectl Apply][kubectl-apply] komutuyla Pod oluşturun:

```console
kubectl apply -f nginx-nfs.yaml
```

[Kubectl açıkla][kubectl-describe] komutunu kullanarak Pod 'un *çalıştığını* doğrulayın:

```console
kubectl describe pod nginx-nfs
```

[Kubectl exec][kubectl-exec] kullanarak Pod 'a bağlanıp daha sonra birimin bağlanıp bağlanmayacağını denetlemek için `df -h` ' i kullanarak hacminin Pod 'a bağlandığından emin olun.

```console
$ kubectl exec -it nginx-nfs -- bash

root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Sonraki adımlar

Azure NetApp Files hakkında daha fazla bilgi için bkz. [Azure NetApp Files nedir][anf]. AKS ile NFS kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes Service (aks) Ile El Ile NFS (ağ dosya sistemi) Linux sunucu birimi oluşturma ve kullanma][aks-nfs].


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
