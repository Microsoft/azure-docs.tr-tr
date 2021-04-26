---
title: "Azure CLı: BACPAC dosyasını Azure SQL veritabanı 'nda veritabanına aktarma"
description: Bir BACPAC dosyasını Azure SQL veritabanı 'nda bir veritabanına aktarmak için Azure CLı örnek betiği
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: load & move data, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 05/24/2019
ms.openlocfilehash: a76a2e72533068f37613d801e39f9451098b89e5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786612"
---
# <a name="use-cli-to-import-a-bacpac-file-into-a-database-in-sql-database"></a>CLı kullanarak BACPAC dosyasını SQL veritabanı 'ndaki bir veritabanına aktarma

Bu Azure CLı betik örneği bir *. bacpac* dosyasından BIR veritabanını SQL veritabanı 'ndaki bir veritabanına aktarır.  

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale için Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Örnek betik

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Betiği çalıştırın

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/import-from-bacpac/import-from-bacpac.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Dağıtımı temizleme

Kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu kullanın.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Örnek başvuru

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Açıklama |
|---|---|
| [az sql server](/cli/azure/sql/server) | Sunucu komutları. |
| [az SQL DB Import](/cli/azure/sql/db#az_sql_db_import) | Veritabanı içeri aktarma komutu. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek SQL Veritabanı CLI betiği örnekleri, [Azure SQL Veritabanı belgeleri](../../azure-sql/database/az-cli-script-samples-content-guide.md) içinde bulunabilir.
