---
title: Hızlı başlangıç-PowerShell kullanarak Key Vault gizli dizi alma & ayarlama
description: Bu hızlı başlangıçta, Azure PowerShell kullanarak Azure Key Vault gizli dizileri oluşturmayı, almayı ve silmeyi öğrenin.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 8a0ebfc90fb57a6e67d7c9e41b78d9db502b2720
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814649"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Hızlı başlangıç: PowerShell kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma

Azure Key Vault, güvenli bir gizli dizi deposu olarak çalışan bir bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Key Vault hakkında daha fazla bilgi için [Genel Bakış](../general/overview.md) bölümünü inceleyebilirsiniz. Bu hızlı başlangıçta PowerShell kullanarak bir anahtar kasası oluşturacaksınız. Ardından yeni oluşturulan kasada bir gizli dizi depolayacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici, Azure PowerShell modül sürümü 5.0.0 veya üzeri bir sürüm gerektirir. `$PSVersionTable.PSVersion`Sürümü bulmak için yazın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Anahtar kasası oluşturma

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>Key Vault içinde gizli dizileri yönetmek için Kullanıcı hesabınıza izin verin

Key Vault erişim ilkesini güncelleştirmek ve Kullanıcı hesabınıza gizli izinler vermek için Azure PowerShell Set-AzKeyVaultAccessPolicy cmdlet 'ini kullanın.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -UserPrincipalName "user@domain.com" -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>Key Vault’a gizli dizi ekleme

Kasaya bir gizli dizi eklemek için birkaç adım uygulamanız gerekir. Bu örnekte, bir uygulama tarafından kullanılabilecek bir parola ekleyeceksiniz. Parola, **Examplepassword** olarak adlandırılır ve **hVFkk965BuUv** değerini bu içinde depolar.

Önce şunu yazarak **hVFkk965BuUv** değerini güvenli bir dizeye dönüştürün:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString "hVFkk965BuUv" -AsPlainText -Force
```

Ardından, **hVFkk965BuUv** değeriyle **examplepassword** adlı bir Key Vault gizli dizi oluşturmak Için Azure PowerShell [set-azkeyvaultsecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet 'ini kullanın:


```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword" -SecretValue $secretvalue
```

## <a name="retrieve-a-secret-from-key-vault"></a>Key Vault bir gizli dizi alma

Gizli dizi içindeki değeri düz metin olarak görüntülemek için:

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword"
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Artık bir Key Vault oluşturdunuz, bir gizli dizli depoladınız ve bunu aldınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

 Bu koleksiyondaki diğer hızlı başlangıçlar ve öğreticiler bu hızlı başlangıcı temel alır. Diğer hızlı başlangıç ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu kaynakları yerinde bırakmak isteyebilirsiniz.

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu, Key Vault ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Key Vault oluşturup bir gizli dizi depoladınız. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Çok satırlı gizli dizileri Key Vault nasıl depolayacağınızı](multiline-secrets.md) öğrenin
- [Azure PowerShell Key Vault cmdlet 'lerine](/powershell/module/az.keyvault/#key_vault) yönelik başvuruya bakın
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin
