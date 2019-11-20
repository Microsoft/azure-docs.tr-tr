---
title: 'Hızlı başlangıç: PowerShell kullanarak Key Vault bir gizli anahtar alma & ayarlama'
description: Bu hızlı başlangıçta, PowerShell kullanarak bir Azure anahtar kasasında gizli dizileri oluşturmayı, almayı ve silmeyi öğrenin
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 2d33d04bfaaccf3e7bcaefc7eec98b04a5ffc2e8
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901438"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Hızlı başlangıç: PowerShell kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Key Vault, güvenli bir gizli dizi deposu olarak çalışan bir bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Key Vault hakkında daha fazla bilgi için [Genel Bakış](key-vault-overview.md) bölümünü inceleyebilirsiniz. Bu hızlı başlangıçta PowerShell kullanarak bir anahtar kasası oluşturacaksınız. Ardından yeni oluşturulan kasada bir gizli dizi depolayacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üzerini gerektirir. Sürümü bulmak için `$PSVersionTable.PSVersion` yazın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Login-AzAccount` komutunu da çalıştırmanız gerekir.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)Ile bir Azure Kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Anahtar kasası oluşturma

Ardından bir Key Vault oluşturacaksınız. Bu adımı uygularken bazı bilgiler gereklidir:

Bu hızlı başlangıçta Anahtar Kasamızın adı olarak “Contoso KeyVault2” kullansak da sizin benzersiz bir ad kullanmanız gerekir.

- **Kasa adı** Contoso-Vault2.
- **Kaynak grubu adı** ContosoResourceGroup.
- **Konum** Doğu ABD.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Bu cmdlet’in çıktısı, yeni oluşturulan anahtar kasasının özelliklerini gösterir. Aşağıda listelenen iki özelliği not edin:

* **Kasa Adı**: Örnekte bu değer **Contoso-Vault2** şeklindedir. Bu adı diğer Anahtar Kasası cmdlet'leri için kullanacaksınız.
* **Kasa URI’si**: Örnekte bu değer https://contosokeyvault.vault.azure.net/ şeklindedir. REST API'si aracılığıyla kasanızı kullanan uygulamaların bu URI'yi kullanması gerekir.

Kasa oluşturma sonrasında Azure hesabınız bu yeni kasa üzerinde herhangi bir işlem yapmasına izin verilen tek hesaptır.

![Anahtar Kasası oluşturma komutu tamamlandıktan sonraki çıktı](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Key Vault’a gizli dizi ekleme

Kasaya bir gizli dizi eklemek için birkaç adım uygulamanız gerekir. Bu örnekte, bir uygulama tarafından kullanılabilecek bir parola ekleyeceksiniz. Parola, **Examplepassword** olarak adlandırılır ve **hVFkk965BuUv** değerini bu içinde depolar.

Önce şunu yazarak **hVFkk965BuUv** değerini güvenli bir dizeye dönüştürün:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Daha sonra, **hVFkk965BuUv** değeriyle **examplepassword** adlı Key Vault bir gizli dizi oluşturmak için aşağıdaki PowerShell komutlarını yazın:

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Gizli dizi içindeki değeri düz metin olarak görüntülemek için:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
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

- [Azure Key Vault genel bakışını](key-vault-overview.md) okuyun
- [Azure PowerShell Key Vault cmdlet 'lerine](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault) yönelik başvuruya bakın
- [Anahtarlar, gizli diziler ve sertifikalar](about-keys-secrets-and-certificates.md) hakkında bilgi edinin
- [En iyi uygulamaları](key-vault-best-practices.md) gözden geçirin Azure Key Vault
