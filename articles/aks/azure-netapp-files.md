---
title: Azure NetApp Files Azure Kubernetes hizmeti ile tümleştirme
description: Azure NetApp Files Azure Kubernetes hizmeti ile tümleştirme hakkında bilgi edinin
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 28c5b77f06bc48bf06575e45194adfaed068b30f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776060"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure NetApp Files Azure Kubernetes hizmeti ile tümleştirme

[Azure NetApp Files][anf] , Azure üzerinde çalışan kurumsal sınıf, yüksek performanslı ve tarifeli bir dosya depolama hizmetidir. Bu makalede Azure NetApp Files Azure Kubernetes Service (AKS) ile nasıl tümleştirileceği gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce
Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

> [!IMPORTANT]
> AKS kümeniz de [Azure NetApp Files destekleyen bir bölgede][anf-regions]olmalıdır.

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

### <a name="limitations"></a>Sınırlamalar

Azure NetApp Files kullandığınızda aşağıdaki sınırlamalar geçerlidir:

* Azure NetApp Files yalnızca [Seçili Azure bölgelerinde][anf-regions]kullanılabilir.
* Azure NetApp Files kullanabilmeniz için, Azure NetApp Files hizmetine erişim verilmesi gerekir. Erişim için uygulamak üzere [Azure NetApp Files eklenebileceğinizi gönderim formunu][anf-waitlist] kullanabilir veya adresine gidebilirsiniz https://azure.microsoft.com/services/netapp/#getting-started . Azure NetApp Files ekibinden resmi onay e-postasını yapana kadar Azure NetApp Files hizmetine erişemezsiniz.
* Bir AKS kümesinin ilk dağıtımından sonra, yalnızca Azure NetApp Files statik sağlama desteklenir.
* Azure NetApp Files ile dinamik sağlamayı kullanmak için [NetApp Trident](https://netapp-trident.readthedocs.io/) sürüm 19,07 veya üstünü yükleyip yapılandırın.

## <a name="configure-azure-netapp-files"></a>Azure NetApp Files Yapılandır

> [!IMPORTANT]
> *Microsoft. NetApp* kaynak sağlayıcısını kaydedebilmek için [Azure NetApp Files eklenebileceğinizi gönderim formunu][anf-waitlist] doldurmanız veya aboneliğiniz için adresine gitmeniz gerekir https://azure.microsoft.com/services/netapp/#getting-started . Azure NetApp Files ekibinden resmi onay e-postasını yapana kadar kaynağı kaydedemezsiniz.

*Microsoft. NetApp* kaynak sağlayıcısını kaydedin:

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Bu işlem biraz zaman alabilir.

AKS ile kullanılmak üzere bir Azure NetApp hesabı oluşturduğunuzda, hesabı **düğüm** kaynak grubunda oluşturmanız gerekir. İlk olarak, [az aks Show][az-aks-show] komutuyla kaynak grubu adını alın ve `--query nodeResourceGroup` sorgu parametresini ekleyin. Aşağıdaki örnek, *Myresourcegroup* kaynak grubu adında *Myakscluster* adlı aks kümesi için düğüm kaynak grubunu alır:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

[Az netappfiles hesap Create][az-netappfiles-account-create]kullanarak aks kümeniz ile aynı bölgede, **düğüm** kaynak grubunda bir Azure NetApp Files hesabı oluşturun. Aşağıdaki örnek, *MC_myResourceGroup_myAKSCluster_eastus* kaynak grubunda ve *eastus* bölgesinde *myaccount1* adlı bir hesap oluşturur:

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

[Az netappfiles Havuz oluştur][az-netappfiles-pool-create]kullanarak yeni bir kapasite havuzu oluşturun. Aşağıdaki örnek, boyut ve *Premium* hizmet DÜZEYINDE 4 TB ile *mypool1* adlı yeni bir kapasite havuzu oluşturur:

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

[Az Network VNET subnet Create][az-network-vnet-subnet-create]kullanarak [Azure NetApp Files temsilci seçmek][anf-delegate-subnet] için bir alt ağ oluşturun. *Bu alt ağ, AKS kümeniz ile aynı sanal ağda olmalıdır.*

```azurecli
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

```azurecli
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
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

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
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>PersistentVolume oluşturma

[Az netappfiles birim Show][az-netappfiles-volume-show] kullanarak biriminizin ayrıntılarını listeleyin

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
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

Bir `pv-nfs.yaml` PersistentVolume tanımlama oluşturma. `path`Önceki komuttan *creationtoken* ile ve `server` *IPAddress* ile değiştirin. Örnek:

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
  mountOptions:
    - vers=3
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

Bir `pvc-nfs.yaml` PersistentVolume tanımlama oluşturma. Örnek:

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

`nginx-nfs.yaml`PersistentVolumeClaim kullanan bir pod tanımlama oluşturma. Örnek:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

[Kubectl exec][kubectl-exec] ' i kullanarak hacminin Pod 'a bağlandığından emin olun ve sonra `df -h` birimin bağlanıp bağlanmayacağını kontrol edin.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
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
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
