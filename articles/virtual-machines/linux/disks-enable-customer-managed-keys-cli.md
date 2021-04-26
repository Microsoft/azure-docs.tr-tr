---
title: Azure CLı-SSE ile yönetilen disklerle müşteri tarafından yönetilen anahtarları etkinleştirin
description: Azure CLı ile yönetilen disklerinizde müşteri tarafından yönetilen anahtarları etkinleştirin.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: fe0fef8c52a913f18faeb8cdad4a68776d8a6277
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562597"
---
# <a name="use-the-azure-cli-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Yönetilen diskler için müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifrelemeyi etkinleştirmek için Azure CLı 'yi kullanma

Azure Disk Depolama, ' yi seçerseniz yönetilen diskler için sunucu tarafı şifreleme (SSE) kullanırken kendi anahtarlarınızı yönetmenizi sağlar. Müşteri tarafından yönetilen anahtarlar ve diğer yönetilen disk şifreleme türleri ile SSE hakkında kavramsal bilgiler için, disk şifreleme makalemize ait [müşteri tarafından yönetilen anahtarlar](../disk-encryption.md#customer-managed-keys) bölümüne bakın.

## <a name="restrictions"></a>Kısıtlamalar

Şimdilik, müşteri tarafından yönetilen anahtarlar aşağıdaki kısıtlamalara sahiptir:

- Diskiniz için bu özellik etkinleştirilirse, devre dışı bırakılamaz.
    Bu sorunu geçici olarak çözmek için, [tüm verileri](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) , müşteri tarafından yönetilen anahtarları kullanmayan tamamen farklı bir yönetilen diske kopyalamanız gerekir.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="set-up-your-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault ve DiskEncryptionSet 'nizi ayarlama

İlk olarak, bir Azure Key Vault ve diskencryptionset kaynağı ayarlamanız gerekir.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

Bu kaynakları oluşturup yapılandırdığınıza göre, bunları yönetilen disklerinizin güvenliğini sağlamak için kullanabilirsiniz. Aşağıdaki bağlantılar, her biri ilgili senaryoya sahip olan, yönetilen disklerinizin güvenliğini sağlamak için kullanabileceğiniz örnek betikler içerir.

## <a name="examples"></a>Örnekler

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Market görüntüsü kullanarak VM oluşturma, işletim sistemi ve veri disklerini müşteri tarafından yönetilen anahtarlarla şifreleme

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="encrypt-existing-managed-disks"></a>Mevcut yönetilen diskleri şifreleyin 

Mevcut disklerinizin aşağıdaki betiği kullanarak şifrelemeniz için çalışan bir VM 'ye bağlı olmaması gerekir:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Market görüntüsü kullanarak bir sanal makine ölçek kümesi oluşturma, işletim sistemi ve veri disklerini müşteri tarafından yönetilen anahtarlarla şifreleme

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifrelemeyi kullanarak şifrelenen boş bir disk oluşturma ve bunu bir VM 'ye iliştirme

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=westcentralus
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Diskencryptionset 'e başvuran tüm kaynakların anahtarını döndürmek için DiskEncryptionSet anahtarını değiştirin

```azurecli

rgName=yourResourceGroupName
keyVaultName=yourKeyVaultName
keyName=yourKeyName
diskEncryptionSetName=yourDiskEncryptionSetName


keyVaultId=$(az keyvault show --name $keyVaultName--query [id] -o tsv)

keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

az disk-encryption-set update -n keyrotationdes -g keyrotationtesting --key-url $keyVaultKeyUrl --source-vault $keyVaultId

```

### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Bir diskin sunucu tarafı şifrelemesinin durumunu bulma

[!INCLUDE [virtual-machines-disks-encryption-status-cli](../../../includes/virtual-machines-disks-encryption-status-cli.md)]

> [!IMPORTANT]
> Müşteri tarafından yönetilen anahtarlar, Azure Active Directory (Azure AD) bir özelliği olan Azure kaynakları için yönetilen kimliklere bağımlıdır. Müşteri tarafından yönetilen anahtarları yapılandırırken, bir yönetilen kimlik, kapsamakta olan kaynaklara otomatik olarak atanır. Daha sonra aboneliği, kaynak grubunu veya yönetilen diski bir Azure AD dizininden diğerine taşırsanız, yönetilen disklerle ilişkili yönetilen kimlik yeni kiracıya aktarılmaz, bu nedenle müşterinin yönettiği anahtarlar artık çalışmayabilir. Daha fazla bilgi için bkz. [Azure AD dizinleri arasında abonelik aktarma](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Sonraki adımlar

- [Müşteri tarafından yönetilen anahtarlarla şifrelenmiş diskler oluşturmak için Azure Resource Manager şablonlarını keşfet](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Makineleri, müşteri tarafından yönetilen anahtarlar etkinleştirilmiş disklerle Çoğalt](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell ile VMware VM'lerinin Azure'a olağanüstü durum kurtarmayı ayarlama](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell ve Azure Resource Manager kullanarak Hyper-V VM'leri için Azure'a olağanüstü durum kurtarmayı ayarlama](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)