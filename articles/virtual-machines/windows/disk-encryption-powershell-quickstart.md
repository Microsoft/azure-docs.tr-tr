---
title: Azure PowerShell ile Windows VM oluşturma ve şifreleme
description: Bu hızlı başlangıçta, bir Windows sanal makinesi oluşturmak ve şifrelemek için Azure PowerShell kullanmayı öğrenirsiniz.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4aed2ce182e535ebb60eae0007353c9c7bddef78
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555273"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Hızlı başlangıç: Azure 'da PowerShell ile Windows sanal makinesi oluşturma ve şifreleme

Azure PowerShell modülü, PowerShell komut satırından veya betik içinden Azure kaynakları oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıçta, bir Windows sanal makinesi (VM) oluşturmak, şifreleme anahtarlarının depolanması için bir Key Vault oluşturmak ve VM 'yi şifrelemek için Azure PowerShell modülünün nasıl kullanılacağı gösterilmektedir. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.


## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)Ile bir Azure Kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

[New-AzVM](/powershell/module/az.compute/new-azvm)Ile bir Azure sanal makinesi oluşturun. Cmdlet 'e kimlik bilgilerini sağlamanız gerekir. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

VM'nizin dağıtılması birkaç dakika sürer. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Şifreleme anahtarları için yapılandırılmış bir Key Vault oluşturma

Azure disk şifrelemesi, şifreleme anahtarını bir Azure Key Vault depolar. [New-Azkeykasasıyla](/powershell/module/az.keyvault/new-azkeyvault)bir Key Vault oluşturun. Şifreleme anahtarlarını depolamak için Key Vault etkinleştirmek üzere-EnabledForDiskEncryption parametresini kullanın.

> [!Important]
> Her Key Vault benzersiz bir adı olmalıdır. Aşağıdaki örnek *Mykv* adlı bir Key Vault oluşturur, ancak sizinkilerle farklı bir ad vermelisiniz.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Sanal makineyi şifreleyin

[Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension)ile sanal makineyi şifreleyin. 

Set-AzVmDiskEncryptionExtension Key Vault nesneniz için bazı değerler gerektirir. Bu değerleri, anahtar kasanızın benzersiz adını [Get-Azkeykasasına](/powershell/module/az.keyvault/get-azkeyvault)geçirerek elde edebilirsiniz.

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Birkaç dakika sonra işlem şunları döndürür:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

[Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus)komutunu çalıştırarak şifreleme işlemini doğrulayabilirsiniz.

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Şifreleme etkin olduğunda, döndürülen çıktıda aşağıdaki işlemi görürsünüz:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet 'ini kullanarak kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırabilirsiniz:

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir sanal makine oluşturdunuz, şifreleme anahtarları için etkin bir Key Vault oluşturdunuz ve VM 'yi şifrelemiş olursunuz.  IaaS VM'leri için Azure Disk Şifrelemesi önkoşulları hakkında daha fazla bilgi edinmek üzere bir sonraki makaleye geçin.

> [!div class="nextstepaction"]
> [Azure disk şifrelemeye genel bakış](disk-encryption-overview.md)
