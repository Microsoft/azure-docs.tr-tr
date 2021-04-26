---
title: Çok satırlı bir gizli dizi Azure Key Vault depolayın
description: Azure CLı ve PowerShell kullanarak Azure Key Vault çok satırlı parolaların nasıl ayarlanacağını gösteren öğretici
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: e320795d5e8623dea9b97ac6371a0ffc6e6117f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610293"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>Azure Key Vault çoklu satırlık bir gizli dizi depolayın

[Azure CLI hızlı başlangıç](quick-create-cli.md) ve [Azure PowerShell hızlı başlangıç](quick-create-powershell.md) , tek satırlık bir gizli dizi depolamanın nasıl depolanacağını göstermektedir.   JSON dosyası veya RSA özel anahtarı gibi çok satırlı bir gizli dizi depolamak için Key Vault de kullanabilirsiniz.

Çok satırlı gizli diziler, CommandLine aracılığıyla Azure CLı [az keykasası Secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) komutuna veya Azure PowerShell [set-azkeyvaultsecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet 'ine geçirilemez. Bunun yerine, önce çok satırlı gizli anahtarı bir metin dosyası olarak depolamanız gerekir. 

Örneğin, aşağıdaki satırları içeren "secretfile.txt" adlı bir metin dosyası oluşturabilirsiniz:

```bash
This is my
multi-line
secret
```

Daha sonra bu dosyayı, parametresini kullanarak Azure CLı [az keykasası Secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) komutuna geçirebilirsiniz `--file` .

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

Azure PowerShell, önce [Get-Content](/powershell/module/microsoft.powershell.management/get-content) cmdlet 'ini kullanarak dosyada okumanız, sonra [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)' i kullanarak güvenli bir dizeye dönüştürmeniz gerekir. 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

Son olarak, [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet 'ini kullanarak gizli anahtarı depoladığınızda.

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

Her iki durumda da, Azure CLı [az keykasası Secret Show](/cli/azure/keyvault/secret#az_keyvault_secret_show) komutunu veya Azure PowerShell [Get-azkeyvaultsecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) cmdlet 'ini kullanarak depolanan gizli anahtarı görüntüleyebilirsiniz.

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Gizli dizi, ekli newlines ile döndürülür:

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- Bkz. [Azure CLI hızlı başlangıç](quick-create-cli.md)
- Bkz. [Azure CLI az keykasası Commands](/cli/azure/keyvault)
- Bkz. [Azure PowerShell hızlı başlangıç](quick-create-powershell.md)
- Bkz [. Azure PowerShell az. Keykasa cmdlet 'leri](/powershell/module/az.keyvault#key-vault)
