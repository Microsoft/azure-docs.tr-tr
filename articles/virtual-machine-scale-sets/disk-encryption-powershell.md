---
title: Azure ölçek kümeleri için diskleri Azure PowerShell ile şifreleyin | Microsoft Docs
description: Windows sanal makine ölçek kümesindeki sanal makine örneklerini ve bağlı diskleri şifrelemek için Azure PowerShell kullanmayı öğrenin
services: virtual-machine-scale-sets
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c228dd3f2e408c97e684a2cc1490903ac7a2eb0
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530829"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Azure PowerShell ile sanal makine ölçek kümesindeki işletim sistemini ve bağlı veri disklerini şifreleyin

Azure PowerShell modülü, PowerShell komut satırından veya betik içinden Azure kaynakları oluşturmak ve yönetmek için kullanılır.  Bu makalede, bir sanal makine ölçek kümesi oluşturmak ve şifrelemek için Azure PowerShell nasıl kullanılacağı gösterilmektedir. Azure disk şifrelemesini bir sanal makine ölçek kümesine uygulama hakkında daha fazla bilgi için bkz. [Sanal Makine Ölçek Kümeleri Için Azure disk şifrelemesi](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Disk şifrelemesi için etkin bir Azure Key Vault oluşturma

Azure Key Vault, uygulamaları ve hizmetlerinize güvenli bir şekilde uygulamanızı sağlayan anahtarları, parolaları veya parolaları depolayabilirler. Şifreleme anahtarları yazılım korumasını kullanarak Azure Key Vault depolanır veya anahtarlarınızı FIPS 140-2 düzey 2 standartlarına sertifikalı donanım güvenlik modüllerinde (HSM 'ler) içeri aktarabilir veya oluşturabilirsiniz. Bu şifreleme anahtarları, VM 'nize bağlı sanal diskleri şifrelemek ve şifrelerini çözmek için kullanılır. Bu şifreleme anahtarlarının denetimini koruyabilir ve kullanımlarını denetleyebilir.

[New-Azkeykasasıyla](/powershell/module/az.keyvault/new-azkeyvault)bir Key Vault oluşturun. Key Vault disk şifrelemesi için kullanılmasına izin vermek için, *Enabledfordiskencryption* parametresini ayarlayın. Aşağıdaki örnek ayrıca kaynak grubu adı, Key Vault adı ve konum için değişkenleri tanımlar. Kendi benzersiz Key Vault adınızı sağlayın:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Mevcut bir Key Vault kullan

Bu adım yalnızca disk şifrelemesi ile kullanmak istediğiniz mevcut bir Key Vault varsa gereklidir. Önceki bölümde bir Key Vault oluşturduysanız bu adımı atlayın.

[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy)ile disk şifrelemesi için ölçek kümesiyle aynı abonelikte ve bölgede bulunan mevcut bir Key Vault etkinleştirebilirsiniz. *$VaultName* değişkeninde mevcut Key Vault adını aşağıdaki gibi tanımlayın:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Ölçek kümesi oluşturma

İlk olarak, VM örnekleri için [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) ile bir yönetici kullanıcı adı ve parola ayarlayın:

```azurepowershell-interactive
$cred = Get-Credential
```

Şimdi [New-AzVmss](/powershell/module/az.compute/new-azvmss)ile bir sanal makine ölçek kümesi oluşturun. Her bir sanal makine örneklerine trafiği dağıtmak için bir yük dengeleyici de oluşturulur. Yük dengeleyici hem TCP bağlantı noktası 80 üzerinden trafiği dağıtmak hem de TCP bağlantı noktası 3389 üzerinden uzak masaüstü trafiğine hem de TCP bağlantı noktası 5985 üzerinden PowerShell uzaktan iletişimine olanak tanımak için kurallar içerir:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Şifrelemeyi etkinleştir

Bir ölçek kümesindeki sanal makine örneklerini şifrelemek için, ilk olarak [Get-Azkeykasasıyla](/powershell/module/az.keyvault/Get-AzKeyVault)Key Vault URI ve kaynak kimliği hakkında bazı bilgiler alın. Bu değişkenler, [set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension)ile şifreleme işlemini başlatmak için kullanılır:


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

İstendiğinde, ölçek kümesi sanal makine örneklerinde disk şifreleme işlemine devam etmek için *y* yazın.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Anahtarı kaydırmak için KEK kullanarak şifrelemeyi etkinleştirme

Ayrıca, sanal makine ölçek kümesini şifrelerken ek güvenlik için anahtar şifreleme anahtarını da kullanabilirsiniz.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

> [!NOTE]
>  Disk-Encryption-keykasası parametresinin değeri için sözdizimi tam tanımlayıcı dizesidir:</br>
/Subscriptions/[abonelik-kimliği-GUID]/resourceGroups/[resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[keykasaadı]</br></br>
> Anahtar şifreleme-anahtar parametresinin değeri için sözdizimi, KEK içinde olduğu gibi tam URI 'dir:</br>
https://[keykasaadı]. kasa. Azure. net/Keys/[kekname]/[kek-Unique-ID]

## <a name="check-encryption-progress"></a>Şifreleme ilerlemesini denetleme

Disk şifrelemenin durumunu denetlemek için [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption)komutunu kullanın:


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

VM örnekleri şifrelendiğinde, *Encryptionsummary* kodu aşağıdaki örnek çıktıda gösterildiği gibi *provisioningstate/başarılı* bildirir:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Şifrelemeyi devre dışı bırak

Artık şifrelenmiş VM örnekleri disklerini kullanmak istemiyorsanız, [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) ile şifrelemeyi şu şekilde devre dışı bırakabilirsiniz:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Sonraki adımlar

- Bu makalede, bir sanal makine ölçek kümesini şifrelemek için Azure PowerShell kullandınız. [Azure CLI](disk-encryption-cli.md) veya [Azure Resource Manager şablonlarını](disk-encryption-azure-resource-manager.md)da kullanabilirsiniz.
- Başka bir uzantı sağlandıktan sonra Azure disk şifrelemesi 'nin uygulanmasını istiyorsanız [uzantı sıralamasını](virtual-machine-scale-sets-extension-sequencing.md)kullanabilirsiniz.
