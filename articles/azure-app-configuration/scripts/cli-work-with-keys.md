---
title: Azure CLı betik örneği-uygulama yapılandırma deposundaki anahtar değerleriyle çalışma
titleSuffix: Azure App Configuration
description: Azure CLı betiği kullanarak uygulama yapılandırma deposundan anahtar değerleri oluşturun, görüntüleyin, güncelleştirin ve silin
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: bc329feffbf22d63774ab0f3779d62d3ccf53adb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774149"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Azure uygulama yapılandırma deposundaki anahtar değerleriyle çalışma

Bu örnek betik nasıl yapılacağını göstermektedir:
* Yeni bir anahtar-değer çifti oluşturun
* Tüm mevcut anahtar-değer çiftlerini Listele
* Yeni oluşturulan bir anahtarın değerini Güncelleştir
* Yeni anahtar-değer çiftini Sil

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.
## <a name="sample-script"></a>Örnek betik

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu tabloda, örnek betiğimizde kullanılan komutlar listelenmektedir. 

| Komut | Notlar |
|---|---|
| [az appconfig kV set](/cli/azure/appconfig/kv#az_appconfig_kv_set) | Anahtar-değer çifti oluşturun veya güncelleştirin. |
| [az appconfig kV List](/cli/azure/appconfig/kv#az_appconfig_kv_list) | Bir uygulama yapılandırma deposundaki anahtar-değer çiftlerini listeleyin. |
| [az appconfig kV Delete](/cli/azure/appconfig/kv#az_appconfig_kv_delete) | Anahtar-değer çiftini silin. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek uygulama yapılandırması CLı betiği örnekleri, [Azure Uygulama YAPıLANDıRMASı CLI örneklerinde](../cli-samples.md)bulunabilir.
