---
title: Azure 'da Windows VM 'de diskleri şifreleme
description: Azure PowerShell kullanarak gelişmiş güvenlik için Windows VM 'de sanal diskleri şifreleme
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 096fe779c077424b01df3ead5965cb799866a03b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033539"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Windows VM 'de sanal diskleri şifreleme
Gelişmiş sanal makine (VM) güvenliği ve uyumluluğu için, Azure 'daki sanal diskler şifrelenebilir. Diskler, bir Azure Key Vault güvenli hale getirilmiş şifreleme anahtarları kullanılarak şifrelenir. Bu şifreleme anahtarlarını kontrol edersiniz ve kullanımlarını denetleyebilir. Bu makalede, Azure PowerShell kullanarak bir Windows VM 'de sanal disklerin nasıl şifreleneceği açıklanır. Ayrıca, [Linux sanal makinelerini şifreleyebilirsiniz](../linux/disk-encryption-overview.md).

 

## <a name="overview-of-disk-encryption"></a>Disk şifrelemeye genel bakış
Windows VM 'lerinde sanal diskler, BitLocker kullanılarak geri kalanı şifrelenir. Azure 'da sanal disklerin şifrelenmesi ücretsizdir. Şifreleme anahtarları yazılım koruması kullanılarak bir Azure Key Vault depolanır veya anahtarlarınızı FIPS 140-2 düzey 2 standartlarına sertifikalı donanım güvenlik modüllerinde (HSM 'ler) içeri aktarabilir veya oluşturabilirsiniz. Şifreleme anahtarları, VM 'nize bağlı sanal diskleri şifrelemek ve şifrelerini çözmek için kullanılır. Bu şifreleme anahtarlarının denetimini tutar ve kullanımlarını denetleyebilir. 

Bir VM 'yi şifreleme işlemi aşağıdaki gibidir:

1. Azure Key Vault bir şifreleme anahtarı oluşturun.
1. Şifreleme anahtarını, diskleri şifrelemek için kullanılabilir olacak şekilde yapılandırın.
1. Sanal diskleriniz için disk şifrelemeyi etkinleştirin.
1. Gerekli şifreleme anahtarları Azure Key Vault istenir.
1. Sanal diskler, belirtilen şifreleme anahtarı kullanılarak şifrelenir.


## <a name="requirements-and-limitations"></a>Gereksinimler ve sınırlamalar

Disk şifrelemesi için desteklenen senaryolar ve gereksinimler:

* Azure Marketi görüntülerinden veya özel VHD görüntülerinden yeni Windows VM 'lerinde şifrelemeyi etkinleştirme.
* Azure 'da mevcut Windows VM 'lerinde şifrelemeyi etkinleştirme.
* Depolama alanları kullanılarak yapılandırılan Windows VM 'lerinde şifrelemeyi etkinleştirme.
* Windows VM 'Leri için işletim sistemi ve veri sürücülerinde şifrelemeyi devre dışı bırakma.
* , D, DS, G ve GS serisi VM 'Ler gibi standart katman VM 'Leri.

    > [!NOTE]
    > Tüm kaynaklar (Key Vault, depolama hesabı ve VM dahil) aynı Azure bölgesinde ve abonelikte olmalıdır.

Aşağıdaki senaryolarda disk şifrelemesi Şu anda desteklenmemektedir:

* Temel katman VM 'Leri.
* Klasik dağıtım modeli kullanılarak oluşturulan VM 'Ler.
* Zaten şifrelenmiş bir VM 'deki şifreleme anahtarları güncelleştiriliyor.
* Şirket içi anahtar yönetim hizmeti ile tümleştirme.


## <a name="create-an-azure-key-vault-and-keys"></a>Azure Key Vault ve anahtarlar oluşturma
Başlamadan önce, Azure PowerShell modülünün en son sürümünün yüklendiğinden emin olun. Daha fazla bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/overview). Aşağıdaki komut örneklerinde, tüm örnek parametreleri *Myresourcegroup*, *mykeykasa,* *myvm*gibi kendi adlarınız, konumunuz ve anahtar değerlerinizle değiştirin.

İlk adım, şifreleme anahtarlarınızın depolanması için bir Azure Key Vault oluşturmaktır. Azure Anahtar kasaları, uygulama ve hizmetlerinize güvenli bir şekilde uygulamanızı sağlayan anahtarları, parolaları veya parolaları depolayabilirler. Sanal disk şifrelemesi için, sanal disklerinizi şifrelemek veya şifresini çözmek için kullanılan bir şifreleme anahtarını depolamak üzere bir Key Vault oluşturacaksınız. 

[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)ile Azure aboneliğinizde Azure Key Vault sağlayıcıyı etkinleştirin ve ardından [New-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun. Aşağıdaki örnek, *Doğu ABD* konumunda *myresourcegroup* adlı bir kaynak grubu adı oluşturur:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Depolama ve VM gibi şifreleme anahtarlarını ve ilişkili bilgi işlem kaynaklarını tutan Azure Key Vault hepsi aynı bölgede olmalıdır. [New-Azkeykasasıyla](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) bir Azure Key Vault oluşturun ve Key Vault disk şifrelemesi ile kullanım için etkinleştirin. *Keyvaultname* için aşağıdaki gibi benzersiz bir Key Vault adı belirtin:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Şifreleme anahtarlarını, yazılım veya donanım güvenlik modeli (HSM) koruması kullanarak saklayabilirsiniz.  Standart Key Vault yalnızca yazılım korumalı anahtarları depolar. HSM kullanmak için ek bir ücret için Premium Key Vault gerekir. Bir Premium Key Vault oluşturmak için, önceki adımda *-SKU "Premium"* parametresini ekleyin. Aşağıdaki örnek, standart bir Key Vault oluşturduğumuz için yazılımla korunan anahtarları kullanır. 

Her iki koruma modelinde da, sanal disklerin şifresini çözmek üzere VM önyüklendiğinde, şifreleme anahtarlarını istemek için Azure platformuna erişim verilmesi gerekir. [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey)ile Key Vault bir şifreleme anahtarı oluşturun. Aşağıdaki örnek *MyKey*adlı bir anahtar oluşturur:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma
Şifreleme işlemini test etmek için [New-azvm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)Ile bir VM oluşturun. Aşağıdaki örnek, *Windows Server 2016 Datacenter* görüntüsü kullanan *MYVM* adlı bir VM oluşturur. Kimlik bilgileri istendiğinde, VM 'niz için kullanılacak kullanıcı adını ve parolayı girin:

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>Bir sanal makineyi şifreleme
Azure Key Vault anahtarını kullanarak VM 'nizi [set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) ile şifreleyin. Aşağıdaki örnek, tüm anahtar bilgilerini alır ve *myvm*adlı VM 'yi şifreler:

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

VM şifrelemesiyle devam etmek için istemi kabul edin. İşlem sırasında VM yeniden başlatılır. Şifreleme işlemi tamamlandıktan ve VM yeniden başlatıldıktan sonra, [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus)ile şifreleme durumunu gözden geçirin:

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Çıktı aşağıdaki örneğe benzer:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Sonraki adımlar
* Azure Key Vault yönetme hakkında daha fazla bilgi için bkz. [sanal makineler için bir Key Vault ayarlama](key-vault-setup.md).
* Azure 'a yüklemek üzere şifrelenmiş bir özel VM hazırlama gibi disk şifreleme hakkında daha fazla bilgi için bkz. [Azure disk şifrelemesi](../../security/fundamentals/encryption-overview.md).
