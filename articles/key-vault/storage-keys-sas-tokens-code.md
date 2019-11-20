---
title: Azure Key Vault yönetilen depolama hesabı-PowerShell sürümü
description: Yönetilen depolama hesabı özelliği, Azure Key Vault ile Azure depolama hesabı arasında sorunsuz bir tümleştirme sağlar.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 75561c643c2b8881e4b390be885b77f2fff4971d
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673463"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Kodda paylaşılan erişim imza belirteçlerini getirme

Depolama hesabınızı Anahtar Kasanızda [paylaşılan erişim imzası belirteçleriyle](../storage/common/storage-dotnet-shared-access-signature-part-1.md) yönetebilirsiniz. Bu makalede bir SAS belirteci C# getiren ve ile işlem gerçekleştiren kod örnekleri sağlanmaktadır.  SAS belirteçlerini oluşturma ve depolama hakkında daha fazla bilgi için bkz. [Key Vault ve Azure CLI ile depolama hesabı anahtarlarını yönetme](key-vault-ovw-storage-keys.md) veya [Key Vault ve Azure PowerShell depolama hesabı anahtarlarını yönetme](key-vault-overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Kod örnekleri

Bu örnekte, kod anahtar kasaınızdan bir SAS belirteci getirir, bunu yeni bir depolama hesabı oluşturmak için kullanır ve yeni bir blob hizmeti istemcisi oluşturur.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Paylaşılan erişim imza belirtecinizin kullanım süreleri dolarsa, anahtar kasaınızdan paylaşılan erişim imza belirtecini getirip kodu güncelleştirebilirsiniz.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Sonraki adımlar
- [](key-vault-overview-storage-keys-powershell.md) [Key Vault ve Azure CLI veya Azure PowerShell ile depolama hesabı anahtarlarını yönetmeyi](key-vault-ovw-storage-keys.md) öğrenin.
- Bkz. [yönetilen depolama hesabı anahtar örnekleri](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Anahtarlar, gizli diziler ve sertifikalar hakkında](about-keys-secrets-and-certificates.md)
- [PowerShell başvurusunu Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
