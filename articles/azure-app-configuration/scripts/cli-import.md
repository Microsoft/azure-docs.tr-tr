---
title: Azure CLı betik örneği-Azure uygulama yapılandırma deposuna aktarma | Microsoft Docs
description: Azure uygulama yapılandırma deposuna içeri aktarmak için bilgi ve örnek betikler sağlar
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: cd1e54fc6cfbf254da010c03dfaa859a0ee8213c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029822"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Azure uygulama yapılandırma deposuna aktarma

Bu örnek betik, anahtar değerlerini bir Azure uygulama yapılandırma deposuna aktarır.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale için Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yi yükleme](/cli/azure/install-azure-cli).

Aşağıdaki komutu yürüterek önce Azure uygulama yapılandırma CLı uzantısını yüklemeniz gerekir:

        az extension add -n appconfig

## <a name="sample-script"></a>Örnek betik

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, bir uygulama yapılandırma deposunu içeri aktarmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az appconfig Import](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-import) | Bir uygulama yapılandırma deposu kaynağına içeri aktarır. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek uygulama yapılandırması CLı betiği örnekleri, [Azure uygulama yapılandırma belgelerinde](../cli-samples.md)bulunabilir.
