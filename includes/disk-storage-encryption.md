---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0659e57f5a5b223c199becf492b27c7a70cbdc63
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612193"
---
Azure yönetilen diskler, buluta kalıcı hale geldiğinde verilerinizi varsayılan olarak otomatik olarak şifreler. Sunucu tarafı şifreleme, verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur. Azure yönetilen disklerindeki veriler, 256 bit [AES şifrelemesi](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)kullanılarak saydam bir şekilde şifrelenir, en güçlü blok şifrelemeleri KULLANILABILIR ve FIPS 140-2 uyumludur.   

Şifreleme, yönetilen disklerin performansını etkilemez. Şifreleme için ek bir maliyet yoktur.

Azure yönetilen diskleri temel alan şifreleme modülleri hakkında daha fazla bilgi için bkz [. şifreleme API 'si: yeni nesil](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Şifreleme anahtarı yönetimi hakkında

Yönetilen diskinizin şifrelenmesi için platform tarafından yönetilen anahtarları kullanabilir veya kendi anahtarlarınızı (Genel Önizleme) kullanarak şifrelemeyi yönetebilirsiniz. Şifrelemeyi kendi anahtarlarınız ile yönetmeyi seçerseniz, yönetilen disklerdeki tüm verileri şifrelemek ve şifrelerini çözmek için kullanılacak *müşteri tarafından yönetilen bir anahtar* belirtebilirsiniz. 

Aşağıdaki bölümlerde, anahtar yönetimiyle ilgili seçeneklerin her biri daha ayrıntılı olarak açıklanır.

## <a name="platform-managed-keys"></a>Platform tarafından yönetilen anahtarlar

Varsayılan olarak, yönetilen diskler platform tarafından yönetilen şifreleme anahtarlarını kullanır. 10 Haziran 2017 itibariyle, mevcut yönetilen disklere yazılan tüm yeni yönetilen diskler, anlık görüntüler, görüntüler ve yeni veriler, platform tarafından yönetilen anahtarlarla otomatik olarak şifrelenir. 

## <a name="customer-managed-keys-public-preview"></a>Müşteri tarafından yönetilen anahtarlar (Genel Önizleme)

Şifrelemeyi, her yönetilen disk düzeyinde, kendi anahtarlarınız ile yönetmeyi tercih edebilirsiniz. Müşteri tarafından yönetilen anahtarlarla yönetilen diskler için sunucu tarafı şifreleme, Azure Key Vault ile tümleşik bir deneyim sunar. [RSA anahtarlarınızı](../articles/key-vault/key-vault-hsm-protected-keys.md) Key Vault içeri aktarabilir ya da Azure Key Vault yeni RSA anahtarları oluşturabilirsiniz. Azure yönetilen diskler, [zarf şifrelemesini](../articles/storage/common/storage-client-side-encryption.md#encryption-via-the-envelope-technique)kullanarak tam saydam bir biçimde şifrelemeyi ve şifre çözmeyi işler. Bir [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 tabanlı veri şifreleme anahtarı (dek) kullanarak verileri şifreler, bu, sırasıyla anahtarlarınız kullanılarak korunur. Key Vault yönetilen disklere erişim sağlamanız gerekir. bu tuşları şifrelemek ve bu DEK şifresini çözmek için anahtarlarınızı kullanın. Bu, veri ve anahtarlarınızın tam denetimini sağlar. Anahtarlarınızı devre dışı bırakabilir veya istediğiniz zaman yönetilen disklere erişimi iptal edebilirsiniz. Yalnızca yönetilen disklerin veya diğer güvenilen Azure hizmetlerinin Anahtarlarınıza erişebildiğinden emin olmak için Azure Key Vault izlemeye sahip şifreleme anahtarı kullanımını da denetleyebilirsiniz.

Aşağıdaki diyagramda, yönetilen disklerin, müşteri tarafından yönetilen anahtarı kullanarak istek yapmak için Azure Active Directory ve Azure Key Vault nasıl kullandığı gösterilmektedir:

![Yönetilen diskler müşteri tarafından yönetilen anahtarlar iş akışı](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


Aşağıdaki listede, diyagramdaki numaralandırılmış adımlar açıklanmaktadır:

1. Azure Key Vault Yöneticisi, Anahtar Kasası kaynakları oluşturur.
1. Anahtar Kasası Yöneticisi, RSA anahtarlarını Key Vault veya Key Vault yeni RSA anahtarları olarak içeri aktarır.
1. Bu yönetici, bir Azure Key Vault KIMLIĞI ve bir anahtar URL 'SI belirterek bir disk şifreleme kümesi kaynağı örneği oluşturur. Disk şifreleme kümesi, yönetilen diskler için anahtar yönetimini basitleştirmek üzere sunulan yeni bir kaynaktır. 
1. Bir disk şifreleme kümesi oluşturulduğunda, Azure Active Directory 'de (AD) [sistem tarafından atanan yönetilen bir kimlik](../articles/active-directory/managed-identities-azure-resources/overview.md) oluşturulur ve disk şifreleme kümesiyle ilişkilendirilir. 
1. Azure Anahtar Kasası Yöneticisi, anahtar kasasında işlem gerçekleştirmek için yönetilen kimlik iznini verir.
1. Bir VM kullanıcısı disk şifreleme kümesiyle ilişkilendirerek diskler oluşturur. VM kullanıcısı aynı zamanda, mevcut kaynaklar için müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifrelemeyi, disk şifreleme kümesiyle ilişkilendirerek da etkinleştirebilir. 
1. Yönetilen diskler Azure Key Vault istekleri göndermek için yönetilen kimliği kullanır.
1. Yönetilen diskler verileri okumak veya yazmak için, verileri şifrelemeyi ve şifre çözmeyi gerçekleştirmek üzere veri şifreleme anahtarını şifrelemek (sarmalamak) ve şifresini çözmek (kaydırmak) için Azure Key Vault istek gönderir. 

Müşteri tarafından yönetilen anahtarlara erişimi iptal etmek için bkz. [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) ve [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)Azure Key Vault. Erişimi iptal etmek, şifreleme anahtarına Azure depolama tarafından erişilemediğinden, depolama hesabındaki tüm verilere erişimi etkin bir şekilde engeller.

### <a name="supported-scenarios-and-restrictions"></a>Desteklenen senaryolar ve kısıtlamalar

Önizleme sırasında yalnızca aşağıdaki senaryolar desteklenir:

- Azure Marketi görüntüsünden bir sanal makine (VM) oluşturun ve müşteri tarafından yönetilen anahtarları kullanarak sunucu tarafı şifreleme ile işletim sistemi diskini şifreleyin.
- Sunucu tarafı şifreleme ve müşteri tarafından yönetilen anahtarlarla şifrelenen özel bir görüntü oluşturun.
- Özel görüntüden bir VM oluşturun ve sunucu tarafı şifrelemeyi ve müşteri tarafından yönetilen anahtarları kullanarak işletim sistemi diskini şifreleyin.
- Sunucu tarafı şifreleme ve müşteri tarafından yönetilen anahtarlar kullanılarak şifrelenmiş veri diskleri oluşturun.
- Sunucu tarafı şifreleme ve müşteri tarafından yönetilen anahtarlar kullanılarak şifrelenen anlık görüntüler oluşturun.
- Sunucu tarafı şifreleme ve müşteri tarafından yönetilen anahtarlarla şifrelenen sanal makine ölçek kümeleri oluşturun.

Önizleme Ayrıca aşağıdaki kısıtlamalara sahiptir:

- Yalnızca Orta Batı ABD kullanılabilir.
- Sunucu tarafı şifreleme ve müşterinin yönettiği anahtarlar kullanılarak şifrelenen özel görüntülerden oluşturulan diskler, müşteri tarafından yönetilen aynı anahtar kullanılarak şifrelenmelidir ve aynı abonelikte olmalıdır.
- Sunucu tarafı şifreleme ve müşteri tarafından yönetilen anahtarlarla şifrelenen disklerden oluşturulan anlık görüntüler, müşteri tarafından yönetilen aynı anahtarlarla şifrelenmelidir.
- Sunucu tarafı şifreleme kullanılarak şifrelenen özel görüntüler ve müşteri tarafından yönetilen anahtarlar paylaşılan görüntü galerisinde kullanılamaz.
- Key Vault, müşteri tarafından yönetilen anahtarlarınız ile aynı abonelikte ve bölgede olmalıdır.
- Müşteri tarafından yönetilen anahtarlarla şifrelenen diskler, anlık görüntüler ve görüntüler başka bir aboneliğe taşınamaz.

### <a name="setting-up-your-azure-key-vault"></a>Azure Key Vault ayarlama

1.  Azure Key Vault ve şifreleme anahtarının bir örneğini oluşturun.

    Key Vault örneğini oluştururken, geçici silme ve Temizleme korumasını etkinleştirmeniz gerekir. Geçici silme, Key Vault belirli bir bekletme süresi (90 gün varsayılanı) için silinen bir anahtar bulundurmasını sağlar. Temizleme koruması, silinen bir anahtarın saklama süresi boşalıncaya kadar kalıcı olarak silinememesini sağlar. Bu ayarlar yanlışlıkla silme nedeniyle verileri kaybetmenize karşı korur. Bu ayarlar, yönetilen diskleri şifrelemek için bir Key Vault kullanılırken zorunludur.

    ```powershell
    $keyVault = New-AzKeyVault -Name myKeyVaultName ` 
    -ResourceGroupName myRGName ` 
    -Location westcentralus ` 
    -EnableSoftDelete ` 
    -EnablePurgeProtection 
     
    $key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName ` 
    -Name myKeyName ` 
    -Destination Software `  
    ```

1.  DiskEncryptionSet 'in bir örneğini oluşturun. 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName myRGName ` 
      -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateDiskEncryptionSet.json" ` 
      -diskEncryptionSetName "myDiskEncryptionSet1" ` 
      -keyVaultId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.KeyVault/vaults/myKeyVaultName" ` 
      -keyVaultKeyUrl "https://myKeyVaultName.vault.azure.net/keys/myKeyName/403445136dee4a57af7068cab08f7d42" ` 
      -region "WestCentralUS"
    ```

1.  DiskEncryptionSet kaynağına anahtar kasasına erişim izni verin.

    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy ` 
        -VaultName $keyVault.VaultName ` 
        -ObjectId $identity.Id ` 
        -PermissionsToKeys wrapkey,unwrapkey,get 
     
    New-AzRoleAssignment ` 
        -ObjectId $identity.Id ` 
        -RoleDefinitionName "Reader" ` 
        -ResourceName $keyVault.VaultName ` 
        -ResourceType "Microsoft.KeyVault/vaults" ` 
        -ResourceGroupName myRGName `  
    ```

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys-via-a-resource-manager-template"></a>Market görüntüsü kullanarak VM oluşturma, işletim sistemi ve veri disklerini bir Kaynak Yöneticisi şablonu aracılığıyla müşteri tarafından yönetilen anahtarlarla şifreleme

```
$password=ConvertTo-SecureString -String "myVMPassword" `
  -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName CMKTesting `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateVMWithDisksEncryptedWithCMK.json" `
  -virtualMachineName "myVMName" `
  -adminPassword $password `
  -vmSize "Standard_DS3_V2" `
  -diskEncryptionSetId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.Compute/diskEncryptionSets/myDiskEncryptionSet1" `
  -region "westcentralus" 
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifrelemeyi kullanarak şifrelenen boş bir disk oluşturma ve bunu bir VM 'ye iliştirme

```PowerShell
$vmName = "yourVMName"
$rgName = "yourRGName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = "30"
$diskEncryptionSetId = "/subscriptions/<subscriptionID>/resourceGroups/yourRGName/providers/Microsoft.Compute/diskEncryptionSets/<yourDiskEncryptionSetName>"
$region = "westcentralus"
$diskLUN = 1

New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateEmptyDataDiskEncryptedWithSSECMK.json" `
  -diskName $diskName `
  -diskSkuName $diskSKU `
  -dataDiskSizeInGb $diskSizeinGiB `
  -diskEncryptionSetId $diskEncryptionSetId `
  -region $region 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun 1
Update-AzVM -ResourceGroupName $rgName -VM $vm
```


> [!IMPORTANT]
> Müşteri tarafından yönetilen anahtarlar, Azure Active Directory (Azure AD) bir özelliği olan Azure kaynakları için yönetilen kimliklere bağımlıdır. Müşteri tarafından yönetilen anahtarları yapılandırırken, bir yönetilen kimlik, kapsamakta olan kaynaklara otomatik olarak atanır. Daha sonra aboneliği, kaynak grubunu veya yönetilen diski bir Azure AD dizininden diğerine taşırsanız, yönetilen disklerle ilişkili yönetilen kimlik yeni kiracıya aktarılmaz, böylelikle müşterinin yönettiği anahtarlar artık çalışmayabilir. Daha fazla bilgi için bkz. [Azure AD dizinleri arasında abonelik aktarma](../articles/active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Sunucu tarafı şifrelemesi ile Azure disk şifrelemesi karşılaştırması

[Azure disk şifrelemesi](../articles/security/fundamentals/azure-disk-encryption-vms-vmss.md) , Windows 'un [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) özelliğinden yararlanır ve Linux 'un [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) ÖZELLIĞINI kullanarak yönetilen diskleri, Konuk VM 'deki müşteri tarafından yönetilen anahtarlarla şifreleyebilirsiniz.  Müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifreleme, depolama hizmetindeki verileri şifreleyerek VM 'niz için herhangi bir işletim sistemi türünü ve görüntüsünü kullanmanızı sağlayarak ADE 'yi geliştirir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Key Vault nedir?](../articles/key-vault/key-vault-overview.md)
