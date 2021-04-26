---
title: Azure CLı-özel bağlantılarla yönetilen disklere içeri/dışarı aktarma erişimini kısıtlama
description: Azure CLı ile yönetilen diskleriniz için özel bağlantıları etkinleştirin. Diskleri yalnızca sanal ağınızda güvenli bir şekilde dışa ve içeri aktarmanıza olanak tanır.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 68b96401fc4fc6ea8916664978dbd4c094d9e5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684534"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure CLı-özel bağlantılarla yönetilen diskler için içeri/dışarı aktarma erişimini kısıtlama

[Özel uç noktaları](../../private-link/private-endpoint-overview.md) , yönetilen disklerin dışarı ve içeri aktarılmasını kısıtlamak ve Azure sanal ağınızdaki istemcilerden [özel bir bağlantı](../../private-link/private-link-overview.md) üzerinden güvenli bir şekilde erişmek için kullanabilirsiniz. Özel uç nokta, yönetilen diskler hizmetiniz için sanal ağ adres alanından bir IP adresi kullanır. Sanal ağı ve yönetilen disklerdeki istemciler arasındaki ağ trafiği, Microsoft omurga ağı üzerinde yalnızca sanal ağ ve özel bir bağlantı üzerinden geçiş yaparken, genel İnternet 'ten etkilenme olasılığını ortadan kaldırır.

Yönetilen diskleri içeri/dışarı aktarmaya yönelik özel bağlantıları kullanmak için, önce bir disk erişim kaynağı oluşturup, özel bir uç nokta oluşturarak bu aboneliği aynı abonelikte bir sanal ağa bağlarsınız. Daha sonra bir disk veya anlık görüntüyü disk erişimi örneğiyle ilişkilendirin. Son olarak, diskin NetworkAccessPolicy özelliğini veya anlık görüntüsünü olarak ayarlayın `AllowPrivate` . Bu, sanal ağınıza erişimi sınırlandırır. 

NetworkAccessPolicy özelliğini, `DenyAll` herhangi bir gövdenin bir disk veya anlık görüntü verilerini dışarı aktarmaya engel olacak şekilde ayarlayabilirsiniz. NetworkAccessPolicy özelliği için varsayılan değer `AllowAll` .

## <a name="limitations"></a>Sınırlamalar

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Aboneliğinizde oturum açın ve değişkenlerinizi ayarlayın

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Azure CLı kullanarak disk erişimi oluşturma
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Sanal Ağ Oluşturma

Ağ güvenlik grupları (NSG) gibi ağ ilkeleri özel uç noktalar için desteklenmez. Belirli bir alt ağda özel bir uç nokta dağıtmak için bu alt ağda açık devre dışı bırakma ayarı gereklidir. 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Alt ağ özel uç nokta ilkelerini devre dışı bırak

Azure, bir sanal ağ içindeki bir alt ağa kaynak dağıtır, bu nedenle özel uç nokta ağ ilkelerini devre dışı bırakmak için alt ağı güncelleştirmeniz gerekir. 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Disk erişimi nesnesi için özel bir uç nokta oluşturma

```azurecli
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>Özel DNS bölgesini yapılandırma

Depolama Blobu etki alanı için bir Özel DNS bölgesi oluşturun, sanal ağla bir ilişki bağlantısı oluşturun ve özel uç noktasını Özel DNS bölgesi ile ilişkilendirmek için bir DNS bölge grubu oluşturun. 

```azurecli
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>Özel bağlantılarla korunan bir disk oluşturma
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Özel bağlantılarla korunan bir diskin anlık görüntüsünü oluşturma
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>Sonraki adımlar

- [Özel bağlantılarla ilgili SSS](../faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [CLI ile farklı bölgedeki bir depolama hesabına VHD olarak yönetilen anlık görüntüler gönderme/kopyalama](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)