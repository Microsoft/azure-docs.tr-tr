---
title: Eklenmemiş Azure tarafından yönetilen ve yönetilmeyen diskleri bulma ve silme
description: Azure CLı kullanarak eklenmemiş Azure yönetilen ve yönetilmeyen (VHD/sayfa Blobları) disklerini bulma ve silme.
author: roygara
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e475fcc5812177366b8c27ae2cca6b45d6c00709
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036147"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Eklenmemiş Azure tarafından yönetilen ve yönetilmeyen diskleri bulma ve silme
Azure 'da bir sanal makineyi (VM) sildiğinizde, varsayılan olarak, VM 'ye bağlı olan tüm diskler silinmez. Bu özellik, VM 'lerin istenmeden silinmesinden dolayı veri kaybını önlemeye yardımcı olur. Bir VM silindikten sonra, eklenmemiş diskler için ödeme yapmaya devam edersiniz. Bu makalede, eklenmemiş disklerin nasıl bulunacağını ve silineceğini ve gereksiz maliyetlerin nasıl azaltalacağı gösterilmektedir. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Yönetilen diskler: eklenmemiş diskleri bulma ve silme 

Aşağıdaki betik, **ManagedBy** özelliğinin değerini inceleyerek eklenmemiş [yönetilen diskleri](managed-disks-overview.md) arar. Yönetilen bir disk bir sanal makineye eklendiğinde, **ManagedBy** özelliği sanal makınenın kaynak kimliğini içerir. Yönetilen bir disk eklendiği zaman, **ManagedBy** özelliği null olur. Betik, bir Azure aboneliğindeki tüm yönetilen diskleri inceler. Betik, **ManagedBy** özelliği null olarak ayarlanmış bir yönetilen disk bulduktan sonra, komut dosyası diskin eklenmemiş olduğunu belirler.

>[!IMPORTANT]
>İlk olarak, **Deleteunattacheddisks** değişkenini 0 olarak ayarlayarak betiği çalıştırın. Bu eylem, tüm ekli yönetilen diskleri bulmanıza ve görüntülemenize olanak sağlar.
>
>Eklenmemiş tüm diskleri gözden geçirdikten sonra, betiği yeniden çalıştırın ve **Deleteunattacheddisks** değişkenini 1 olarak ayarlayın. Bu eylem, eklenmemiş tüm yönetilen diskleri silmenize olanak sağlar.
>

```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Yönetilmeyen diskler: eklenmemiş diskleri bulma ve silme 

Yönetilmeyen diskler, [Azure depolama hesaplarında](../../storage/common/storage-create-storage-account.md) [sayfa BLOBLARı](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) olarak depolanan VHD dosyalarıdır. Aşağıdaki betik, **Leasestatus** özelliğinin değerini inceleyerek eklenmemiş yönetilmeyen diskleri (sayfa Blobları) arar. Bir sanal makineye yönetilmeyen bir disk eklendiğinde, **Leasestatus** özelliği **kilitli**olarak ayarlanır. Yönetilmeyen bir disk bağlı değilken, **Leasestatus** özelliği **kilitlenmemiş**olarak ayarlanır. Betik, bir Azure aboneliğindeki tüm Azure depolama hesaplarında yönetilmeyen tüm diskleri inceler. Betik, **Leasestatus** özelliği **kilitlenmemiş**olarak ayarlanmış bir yönetilmeyen disk bulduktan sonra, komut dosyası diskin eklenmemiş olduğunu belirler.

>[!IMPORTANT]
>İlk olarak, **Deleteunattachedvhd** değişkenini 0 olarak ayarlayarak betiği çalıştırın. Bu eylem, tüm eklenmemiş yönetilmeyen VHD 'leri bulup görüntülemenizi sağlar.
>
>Eklenmemiş tüm diskleri gözden geçirdikten sonra, betiği yeniden çalıştırın ve **Deleteunattachedvhd** değişkenini 1 olarak ayarlayın. Bu eylem, tüm eklenmemiş yönetilmeyen VHD 'leri silmenize olanak sağlar.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Sonraki adımlar

[Depolama hesabını Sil](../../storage/common/storage-create-storage-account.md)



