---
title: Konakta şifreleme kullanarak uçtan uca şifrelemeyi etkinleştirme-Azure CLı ile yönetilen diskler
description: Azure yönetilen disklerinizde uçtan uca şifrelemeyi etkinleştirmek için konaktaki şifrelemeyi kullanın.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 45cdb9217eebf6e3129718a96d9f7b72a3ab62b3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533607"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Konakta şifrelemeyi kullanarak uçtan uca şifrelemeyi etkinleştirmek için Azure CLı 'yi kullanma

Konakta şifrelemeyi etkinleştirdiğinizde, VM konağında depolanan veriler, REST ve depolama hizmetine şifrelenen akışlara şifrelenir. Konakta şifreleme ve diğer yönetilen disk şifreleme türleri hakkında kavramsal bilgiler için bkz. [VM verileriniz için konak-uçtan uca şifreleme](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Kısıtlamalar

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>Desteklenen VM boyutları

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Desteklenen VM boyutlarının tüm listesi programlı olarak çekebilirler. Programlı olarak nasıl alınacağını öğrenmek için [desteklenen VM boyutlarını bulma](#finding-supported-vm-sizes) bölümüne bakın.
VM boyutunu yükseltme, yeni VM boyutunun EncryptionAtHost özelliğini destekleyip desteklemediğini denetbir doğrulamaya neden olur.

## <a name="prerequisites"></a>Önkoşullar

VM/VMSS 'niz için EncryptionAtHost özelliğini kullanmadan önce aboneliğiniz için özelliği etkinleştirmeniz gerekir. Aboneliğiniz için özelliği etkinleştirmek üzere lütfen aşağıdaki adımları izleyin:

1.  Aboneliğinizi için özelliği kaydetmek üzere aşağıdaki komutu yürütün

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  Özelliği denemeden önce aşağıdaki komutu kullanarak kayıt durumunun kayıtlı olduğundan (birkaç dakika sürer) emin olun.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault ve DiskEncryptionSet oluşturma

Özellik etkinleştirildikten sonra, henüz yapmadıysanız bir Azure Key Vault ve DiskEncryptionSet ayarlamanız gerekir.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Örnekler

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlarla etkinleştirilmiş konakta şifreleme ile bir VM oluşturun. 

Daha önce oluşturulan DiskEncryptionSet 'in Kaynak URI 'sini kullanarak yönetilen disklere sahip bir VM oluşturun ve müşteri tarafından yönetilen anahtarlarla işletim sistemi ve veri diskleri önbelleğini şifreleyin. Geçici diskler, platform tarafından yönetilen anahtarlarla şifrelenir. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Platform tarafından yönetilen anahtarlarla etkinleştirilmiş konakta şifreleme ile bir VM oluşturun. 

İşletim sisteminde şifrelemeye sahip bir sanal makine oluşturun ve işletim sistemi/veri disklerinin ve platformlar arası anahtarlarla geçici disklerin önbelleğini şifreleyin. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Konakta şifrelemeyi etkinleştirmek için bir VM 'yi güncelleştirin. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>VM için konaktaki şifreleme durumunu denetleme

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Ana bilgisayarda, müşteri tarafından yönetilen anahtarlarla etkinleştirilmiş bir sanal makine ölçek kümesi oluşturun. 

Daha önce oluşturulan DiskEncryptionSet Kaynak URI 'sini kullanarak yönetilen disklere sahip bir sanal makine ölçek kümesi oluşturun ve müşteri tarafından yönetilen anahtarlarla işletim sistemi ve veri diskleri önbelleğini şifreleyin. Geçici diskler, platform tarafından yönetilen anahtarlarla şifrelenir. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Platform tarafından yönetilen anahtarlarla etkinleştirilmiş konakta şifreleme ile bir sanal makine ölçek kümesi oluşturun. 

İşletim sisteminde şifrelemeye sahip bir sanal makine ölçek kümesi oluşturun ve işletim sistemi/veri disklerinin önbelleğini ve platform tarafından yönetilen anahtarlarla geçici diskleri şifreler. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Konakta şifrelemeyi etkinleştirmek için bir sanal makine ölçek kümesini güncelleştirin. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Sanal makine ölçek kümesi için konaktaki şifreleme durumunu denetle

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Desteklenen VM boyutlarını bulma

Eski VM boyutları desteklenmez. Desteklenen VM boyutlarının listesini şunlardan biriyle bulabilirsiniz:

[Kaynak SKU 'larını](/rest/api/compute/resourceskus/list) çağırma ve `EncryptionAtHostSupported` özelliğin **true** olarak ayarlandığını denetleme.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Veya [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) PowerShell cmdlet 'ini çağırarak.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu kaynakları oluşturup yapılandırdığınıza göre, bunları yönetilen disklerinizin güvenliğini sağlamak için kullanabilirsiniz. Aşağıdaki bağlantı, yönetilen disklerinizin güvenliğini sağlamak için kullanabileceğiniz, her biri ilgili senaryoya sahip örnek betikler içerir.

[Azure Resource Manager şablonu örnekleri](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
