---
title: Azure CLı betik örneği-Azure uygulama yapılandırma deposundaki anahtar değerleriyle çalışma | Microsoft Docs
description: Azure uygulama yapılandırma deposundaki anahtar değerleriyle çalışma hakkında bilgi sağlar
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 11/08/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: d89fa4c067e511e6210e8c1473bf1856297fc1de
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904085"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Azure uygulama yapılandırma deposundaki anahtar değerleriyle çalışma

Bu örnek betik, bir Azure uygulama yapılandırma deposunda yeni bir anahtar değeri oluşturur, tüm mevcut anahtar değerlerini listeler, yeni oluşturulan anahtarın değerini güncelleştirir ve son olarak onu siler.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale için Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

Aşağıdaki komutu yürüterek önce Azure uygulama yapılandırma CLı uzantısını yüklemeniz gerekir:

        az extension add -n appconfig

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

Bu betik, bir uygulama yapılandırma deposundaki anahtar değerlerinde işlem yapmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az appconfig kV set](/cli/azure/ext/appconfig/appconfig) | Anahtar-değer oluşturur veya güncelleştirir. |
| [az appconfig kV List](/cli/azure/ext/appconfig/appconfig) | Bir uygulama yapılandırma deposundaki anahtar değerlerini listeler. |
| [az appconfig kV Delete](/cli/azure/ext/appconfig/appconfig) | Anahtar değerini siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek uygulama yapılandırması CLı betiği örnekleri, [Azure uygulama yapılandırma belgelerinde](../cli-samples.md)bulunabilir.
