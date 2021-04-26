---
title: CLı örneği-yük devretme grubu-Azure SQL veritabanı elastik havuz
description: Azure CLı örnek betiği bir Azure SQL veritabanı elastik havuzu oluşturun, bir yük devretme grubuna ekleyin ve yük devretmeyi test edin.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 07/16/2019
ms.openlocfilehash: a5814bfe3bd6ec2d97a068ea8ce71fa7ffea8ec0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323609"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>CLı kullanarak bir yük devretme grubuna Azure SQL veritabanı elastik havuzu ekleme

Bu Azure CLı betik örneği tek bir veritabanı oluşturur, bunu elastik bir havuza ekler, bir yük devretme grubu oluşturur ve yük devretmeyi sınar.

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale için Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Örnek betik

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Betiği çalıştırın

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-cli.sh "Add elastic pool to a failover group")]

### <a name="clean-up-deployment"></a>Dağıtımı temizleme

Kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu kullanın.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Örnek başvuru

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Açıklama |
|---|---|
| [az SQL elastik havuz](/cli/azure/sql/elastic-pool) | Elastik havuz komutları. |
| [az SQL yük devretme-Grup ](/cli/azure/sql/failover-group) | Yük devretme grubu komutları. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure/overview).

Ek SQL veritabanı Azure CLı betiği örnekleri, [Azure SQL veritabanı Azure CLI betikleri](../../azure-sql/database/az-cli-script-samples-content-guide.md)içinde bulunabilir.
