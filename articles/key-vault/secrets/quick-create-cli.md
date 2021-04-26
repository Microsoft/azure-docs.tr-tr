---
title: Hızlı başlangıç-Azure Key Vault bir gizli dizi ayarlama ve alma
description: Azure CLI kullanarak Azure Key Vault'tan gizli dizi ayarlamayı ve almayı gösteren hızlı başlangıç
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: dc5fa0eeabc437f16ca64c34b4bb72e62c0c3b21
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815045"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Hızlı başlangıç: Azure CLI kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma

Bu hızlı başlangıçta Azure CLı ile Azure Key Vault bir Anahtar Kasası oluşturacaksınız. Azure Key Vault, güvenli bir gizli dizi deposu olarak çalışan bir bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Key Vault hakkında daha fazla bilgi için [genel bakışı](../general/overview.md)gözden geçirebilirsiniz. Azure CLI, komut veya betikler kullanarak Azure kaynakları oluşturup yönetmek için kullanılır. Bu işlemi tamamladıktan sonra bir gizli dizli depolayacaksınız.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu hızlı başlangıç, Azure CLı 'nin sürüm 2.0.4 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Anahtar kasası oluşturma

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>Key Vault’a gizli dizi ekleme

Kasaya bir gizli dizi eklemek için birkaç ek adım uygulamanız gerekir. Bu parola bir uygulama tarafından kullanılabilir. Parola, **Examplepassword** olarak adlandırılır ve **hVFkk965BuUv** değerini bunun içinde depolayacaktır.

**HVFkk965BuUv** değerini depolayacak **examplepassword** adlı Key Vault bir gizli dizi oluşturmak için aşağıdaki Azure CLI [az keykasası Secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) komutunu kullanın:

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

## <a name="retrieve-a-secret-from-key-vault"></a>Key Vault bir gizli dizi alma

Artık Azure Key Vault'a eklediğiniz bu parolaya URI'sini kullanarak başvurabilirsiniz. Geçerli sürümü almak için **' https://<-Unique-keykasa-adı>. Vault.Azure.net/Secrets/ExamplePassword '** kullanın.

Gizli dizi içindeki değeri düz metin olarak görüntülemek için:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Artık bir Key Vault oluşturdunuz, bir gizli dizli depoladınız ve bunu aldınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Key Vault oluşturup bir gizli dizi depoladınız. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Çok satırlı gizli dizileri Key Vault nasıl depolayacağınızı](multiline-secrets.md) öğrenin
- Azure CLı için başvuruya bakın [az keykasa komutları](/cli/azure/keyvault)
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin
