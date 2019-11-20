---
title: Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları PowerShell 'den yapılandırma
description: Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırmak üzere PowerShell 'in nasıl kullanılacağını öğrenin. Müşteri tarafından yönetilen anahtarlar, erişim denetimleri oluşturmanıza, döndürmenize, devre dışı bırakmanızı ve iptal edebilmesini sağlar.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 30fed5a75e368e9d87da54d4c75e6f69759813e7
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249281"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları PowerShell 'den yapılandırma

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Bu makalede, PowerShell kullanarak müşteri tarafından yönetilen anahtarlarla bir anahtar kasasının nasıl yapılandırılacağı gösterilmektedir.

> [!IMPORTANT]
> Azure depolama şifrelemesi ile müşteri tarafından yönetilen anahtarların kullanılması, anahtar kasasında iki özellik ayarlanmasını, **geçici silme** ve **Temizleme işlemi**yapılmasını gerektirir. Bu özellikler varsayılan olarak etkinleştirilmemiştir. Bu özellikleri etkinleştirmek için PowerShell veya Azure CLı kullanın.
> Yalnızca RSA anahtarları ve anahtar boyutu 2048 desteklenir.

## <a name="assign-an-identity-to-the-storage-account"></a>Depolama hesabına bir kimlik atayın

Depolama hesabınız için müşteri tarafından yönetilen anahtarları etkinleştirmek üzere öncelikle depolama hesabına sistem tarafından atanan bir yönetilen kimlik atayın. Bu yönetilen kimliği, depolama hesabı izinlerini anahtar kasasına erişim vermek için kullanacaksınız.

PowerShell kullanarak yönetilen bir kimlik atamak için [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)' ı çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Sistem tarafından atanan yönetilen kimlikleri PowerShell ile yapılandırma hakkında daha fazla bilgi için bkz. [PowerShell kullanarak Azure VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Yeni bir Anahtar Kasası oluşturun

PowerShell kullanarak yeni bir Anahtar Kasası oluşturmak için [New-Azkeykasasını](/powershell/module/az.keyvault/new-azkeyvault)çağırın. Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları depolamak üzere kullandığınız anahtar kasasında iki anahtar koruma ayarı etkinleştirilmiş, **geçici silme** ve **Temizleme işlemi**yapılmalıdır. 

Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Anahtar Kasası erişim ilkesini yapılandırma

Daha sonra, depolama hesabının bu hesaba erişim izinleri olması için Anahtar Kasası için erişim ilkesini yapılandırın. Bu adımda, daha önce depolama hesabına atadığınız yönetilen kimliği kullanacaksınız.

Anahtar Kasası erişim ilkesini ayarlamak için [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)komutunu çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi ve önceki örneklerde tanımlanan değişkenleri kullanmayı unutmayın.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Yeni anahtar oluştur

Ardından, anahtar kasasında yeni bir anahtar oluşturun. Yeni bir anahtar oluşturmak için [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)komutunu çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi ve önceki örneklerde tanımlanan değişkenleri kullanmayı unutmayın.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma

Varsayılan olarak, Azure depolama şifrelemesi Microsoft tarafından yönetilen anahtarları kullanır. Bu adımda, Azure depolama hesabınızı müşteri tarafından yönetilen anahtarları kullanacak şekilde yapılandırın ve depolama hesabıyla ilişkilendirilecek anahtarı belirtin.

Depolama hesabının şifreleme ayarlarını güncelleştirmek için [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) öğesini çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi ve önceki örneklerde tanımlanan değişkenleri kullanmayı unutmayın.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Anahtar sürümünü güncelleştirme

Bir anahtarın yeni bir sürümünü oluşturduğunuzda, yeni sürümü kullanmak için depolama hesabını güncelleştirmeniz gerekir. İlk olarak, anahtarın en son sürümünü almak için [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) öğesini çağırın. Ardından, önceki bölümde gösterildiği gibi, depolama hesabının şifreleme ayarlarını anahtarın yeni sürümünü kullanacak şekilde güncelleştirmek için [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) komutunu çağırın.

## <a name="next-steps"></a>Sonraki adımlar

- [Bekleyen veriler için Azure depolama şifrelemesi](storage-service-encryption.md) 
- [Azure Key Vault nedir](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
