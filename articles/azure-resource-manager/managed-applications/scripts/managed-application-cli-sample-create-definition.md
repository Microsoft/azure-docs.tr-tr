---
title: Yönetilen uygulama tanımı oluşturma-Azure CLı
description: Abonelikte yönetilen uygulama tanımı oluşturan bir Azure CLı betik örneği sağlar.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2430b14ce3a3ba578787cefa85d95475c3e9b920
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775466"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Azure CLI ile yönetilen uygulama tanımı oluşturma

Bu betik bir hizmet kataloğunda yönetilen bir uygulama tanımını yayımlar. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Betik açıklaması

Bu betik, yönetilen uygulama tanımını oluşturmak için aşağıdaki komutu kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az managedapp definition create](/cli/azure/managedapp/definition#az_managedapp_definition_create) | Yönetilen uygulama tanımı oluşturur. Gerekli dosyaları içeren paketi sağlar. |


## <a name="next-steps"></a>Sonraki adımlar

* Yönetilen uygulamalara giriş için [Azure Yönetilen Uygulamalara genel bakış](../overview.md) konusunu inceleyin.
* Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).
