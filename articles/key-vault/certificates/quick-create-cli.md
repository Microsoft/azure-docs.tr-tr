---
title: Hızlı başlangıç-Azure CLı ile & görüntüleme Azure Key Vault sertifikaları ayarlama
description: Azure CLı kullanarak Azure Key Vault bir sertifikanın nasıl ayarlanacağını ve alınacağını gösteren hızlı başlangıç
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: dc3abe43c2cdf4d0ee58ac9aed570a2c507b0d7f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815279"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak Azure Key Vault bir sertifikayı ayarlama ve alma

Bu hızlı başlangıçta Azure CLı ile Azure Key Vault bir Anahtar Kasası oluşturacaksınız. Azure Key Vault, güvenli bir gizli dizi deposu olarak çalışan bir bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Key Vault hakkında daha fazla bilgi için [genel bakışı](../general/overview.md)gözden geçirebilirsiniz. Azure CLI, komut veya betikler kullanarak Azure kaynakları oluşturup yönetmek için kullanılır. Bunu tamamladıktan sonra bir sertifika depolayacaksınız.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu hızlı başlangıç, Azure CLı 'nin sürüm 2.0.4 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Anahtar kasası oluşturma

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>Key Vault bir sertifika ekleyin

Kasaya bir sertifika eklemek için, birkaç ek adım yapmanız yeterlidir. Bu sertifika bir uygulama tarafından kullanılabilir. 

**Örnek sertifika** adlı varsayılan ilkeyle otomatik olarak imzalanan bir sertifika oluşturmak için aşağıdaki komutları yazın:

```azurecli
az keyvault certificate create --vault-name "<your-unique-keyvault-name>" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

Artık Azure Key Vault ' a eklediğiniz sertifikaya, URI 'sini kullanarak başvurabilirsiniz. Geçerli sürümü almak için **' https://<-Unique-keykasa-adı>. Vault.Azure.net/Certificates/ExampleCertificate '** kullanın. 

Daha önce depolanan sertifikayı görüntülemek için:

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "<your-unique-keyvault-name>"
```

Şimdi bir Key Vault oluşturdunuz, bir sertifikayı depolamıştır ve geri almıştır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Key Vault oluşturup içinde bir sertifika depoladığınız. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- Azure CLı için başvuruya bakın [az keykasa komutları](/cli/azure/keyvault)
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin
