---
title: Hızlı başlangıç-Azure CLı ile Azure Key Vault oluşturma
description: Azure CLı kullanarak Azure Key Vault oluşturmayı gösteren hızlı başlangıç
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 588bee2922ee44f3f89b5d252b5b4a6991d26b82
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815135"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak bir Anahtar Kasası oluşturma

Azure Key Vault [anahtarlar](../keys/index.yml), [gizli](../secrets/index.yml)diziler ve [Sertifikalar](../certificates/index.yml)için güvenli bir mağaza sağlayan bir bulut hizmetidir. Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault hakkında](overview.md); anahtar kasasında nelerin depolanabileceği hakkında daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](about-keys-secrets-certificates.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu hızlı başlangıç, Azure CLı 'nin sürüm 2.0.4 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Anahtar kasası oluşturma

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Key Vault oluşturup silmiş olursunuz. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](overview.md) okuyun
- [Azure Key Vault güvenliğine genel bakış](security-features.md) konusunu gözden geçirin
- Azure CLı için başvuruya bakın [az keykasa komutları](/cli/azure/keyvault)

