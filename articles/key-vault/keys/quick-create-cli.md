---
title: Azure Key Vault-Azure CLı içindeki bir anahtarın özniteliklerini oluşturma ve alma
description: Azure CLı kullanarak Azure Key Vault bir anahtarın nasıl ayarlanacağını ve alınacağını gösteren hızlı başlangıç
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: f5281c02f68598b1cf121a6ab8a574f0694f5ea2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813461"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak Azure Key Vault bir anahtar ayarlama ve alma

Bu hızlı başlangıçta Azure CLı ile Azure Key Vault bir Anahtar Kasası oluşturacaksınız. Azure Key Vault, güvenli bir gizli dizi deposu olarak çalışan bir bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Key Vault hakkında daha fazla bilgi için [genel bakışı](../general/overview.md)gözden geçirebilirsiniz. Azure CLI, komut veya betikler kullanarak Azure kaynakları oluşturup yönetmek için kullanılır. Bunu tamamladıktan sonra bir anahtar depolayacaksınız.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu hızlı başlangıç, Azure CLı 'nin sürüm 2.0.4 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Anahtar kasası oluşturma

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Key Vault bir anahtar ekleyin

Kasaya bir anahtar eklemek için, birkaç ek adım yapmanız yeterlidir. Bu anahtar bir uygulama tarafından kullanılabilir. 

**Örnek anahtar** adlı bir anahtar oluşturmak için aşağıdaki komutları yazın:

```azurecli
az keyvault key create --vault-name "<your-unique-keyvault-name>" -n ExampleKey --protection software
```

Artık Azure Key Vault ' a eklediğiniz anahtara, URI 'sini kullanarak başvurabilirsiniz. Geçerli sürümü almak için **' https://<-Unique-keykasa-adı>. Vault.Azure.net/Keys/ExampleKey '** kullanın. 

Daha önce depolanan anahtarı görüntülemek için:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "<your-unique-keyvault-name>"
```

Şimdi bir Key Vault oluşturdunuz, bir anahtar depolamıştır ve geri aldı.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Key Vault oluşturdunuz ve içinde bir anahtar depotamamladınız. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- Azure CLı için başvuruya bakın [az keykasa komutları](/cli/azure/keyvault)
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin
