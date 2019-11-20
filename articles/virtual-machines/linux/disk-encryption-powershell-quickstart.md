---
title: Azure PowerShell ile Linux VM oluşturma ve şifreleme
description: Bu hızlı başlangıçta, bir Linux sanal makinesi oluşturmak ve şifrelemek için Azure PowerShell 'in nasıl kullanılacağını öğrenirsiniz.
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 14cdd1e15212141e8c631b589c9c470bbdfe38a9
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829910"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Hızlı başlangıç: Azure 'da Azure PowerShell ile Linux VM oluşturma ve şifreleme

Azure PowerShell modülü, PowerShell komut satırından veya betiklerden Azure kaynakları oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıçta, Azure PowerShell modülünü kullanarak bir Linux sanal makinesi (VM) oluşturma, şifreleme anahtarlarının depolanması için bir Key Vault oluşturma ve VM 'yi şifreleme işlemlerinin nasıl yapılacağı gösterilir. Bu hızlı başlangıçta, kurallı ve VM Standard_D2S_V3 boyutundan Ubuntu 16,04 LTS Market görüntüsü kullanılmaktadır. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)Ile bir Azure Kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

[New-azvm](/powershell/module/az.compute/new-azvm)Ile bir Azure sanal makinesi oluşturun ve yukarıda oluşturduğunuz VM yapılandırma nesnesini kullanın.

```powershell-interactive
$cred = = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:16.04-LTS:latest -Size Standard_D2S_V3
```

VM 'nizin dağıtılması birkaç dakika sürer. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Şifreleme anahtarları için yapılandırılmış bir Key Vault oluşturma

Azure disk şifrelemesi, şifreleme anahtarını bir Azure Key Vault depolar. [New-Azkeykasasıyla](/powershell/module/az.keyvault/new-azkeyvault)bir Key Vault oluşturun. Şifreleme anahtarlarını depolamak için Key Vault etkinleştirmek üzere-EnabledForDiskEncryption parametresini kullanın.

> [!Important]
> Her anahtar kasasının Azure genelinde benzersiz bir adı olmalıdır. Aşağıdaki örneklerde-Unique-keykasa-adı > < seçtiğiniz adla değiştirin.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Sanal makineyi şifreleyin

[Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension)ile sanal makineyi şifreleyin. 

Set-AzVmDiskEncryptionExtension, Key Vault nesneniz için bazı değerler gerektirir. Bu değerleri, anahtar kasanızın benzersiz adını [Get-Azkeykasasına](/powershell/module/az.keyvault/get-azkeyvault)geçirerek elde edebilirsiniz.

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Birkaç dakika sonra işlem şunları döndürür:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

[Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus)komutunu çalıştırarak şifreleme işlemini doğrulayabilirsiniz.

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Şifreleme etkin olduğunda, döndürülen çıktıda aşağıdaki işlemi görürsünüz:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Kaynakları Temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet 'ini kullanarak kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırabilirsiniz:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir sanal makine oluşturdunuz, şifreleme anahtarları için etkin bir Key Vault oluşturdunuz ve VM 'yi şifrelemiş olursunuz.  Linux VM 'Leri için Azure disk şifrelemesi hakkında daha fazla bilgi edinmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Azure disk şifrelemeye genel bakış](disk-encryption-overview.md)