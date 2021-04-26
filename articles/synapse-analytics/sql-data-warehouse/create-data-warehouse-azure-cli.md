---
title: 'Hızlı başlangıç: Azure CLı ile SYNAPSE SQL havuzu oluşturma'
description: Azure CLı kullanarak sunucu düzeyinde güvenlik duvarı kuralıyla hızlıca bir Synapse SQL havuzu oluşturun.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.custom: azure-synapse, devx-track-azurecli
ms.openlocfilehash: 712405ec7ba61e05bb587efc3e6393192d820e97
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865504"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>Hızlı başlangıç: Azure CLı ile SYNAPSE SQL havuzu oluşturma

Azure CLı kullanarak Azure SYNAPSE Analytics 'te bir Synapse SQL Havuzu (veri ambarı) oluşturun.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>Kullanmaya başlama

Azure 'da oturum açmak ve bir kaynak grubu ayarlamak için bu komutları kullanın.

1. Yerel bir yüklemesi kullanıyorsanız, Azure 'da oturum açmak için [az Login](/cli/azure/reference-index#az_login) komutunu çalıştırın:

   ```azurecli
   az login
   ```

1. Gerekirse, aboneliğinizi seçmek için [az Account set](/cli/azure/account#az_account_set) komutunu kullanın:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Bir kaynak grubu oluşturmak için [az Group Create](/cli/azure/group#az_group_create) komutunu çalıştırın:

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. [Az SQL Server Create](/cli/azure/sql/server#az_sql_server_create) komutunu kullanarak [mantıksal bir SQL Server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) oluşturun:

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   Sunucu, grup olarak yönetilen bir veritabanı grubu içerir.

## <a name="configure-a-server-level-firewall-rule"></a>Sunucu düzeyinde güvenlik duvarı kuralı oluşturma

[Sunucu düzeyinde bir güvenlik duvarı kuralı](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)oluşturun. Sunucu düzeyinde bir güvenlik duvarı kuralı, SQL havuzu hizmeti güvenlik duvarı aracılığıyla bir SQL havuzuna bağlanmak üzere SQL Server Management Studio veya SQLCMD yardımcı programı gibi bir dış uygulamanın bir SQL havuzuna bağlanmasını sağlar.

Bir güvenlik duvarı kuralı oluşturmak için [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) komutunu çalıştırın:

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

Bu örnekte, güvenlik duvarı yalnızca diğer Azure kaynakları için açılır. Dışarıdan bağlantı kurulabilmesi için IP adresini ortamınız için uygun bir adres olarak değiştirin. Tüm IP adreslerini açmak için başlangıç IP adresi olarak 0.0.0.0’ı, bitiş adresi olaraksa 255.255.255.255’i kullanın.

> [!NOTE]
> SQL uç noktaları 1433 bağlantı noktası üzerinden iletişim kurar. Bir kurumsal ağ içinden bağlanmaya çalışıyorsanız, ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, BT departmanınız 1433 numaralı bağlantı noktasını açmadığı müddetçe sunucunuza bağlanamazsınız.
>

## <a name="create-and-manage-your-sql-pool"></a>SQL havuzunuzu oluşturma ve yönetme

SQL havuzunu oluşturun. Bu örnek, SQL havuzunuz için daha düşük maliyetli bir başlangıç noktası olan hizmet hedefi olarak DW100c kullanır.

> [!NOTE]
> Daha önce oluşturulmuş bir çalışma alanına ihtiyacınız vardır. Daha fazla bilgi için bkz. [hızlı başlangıç: Azure CLI Ile Azure SYNAPSE çalışma alanı oluşturma](../quickstart-create-workspace-cli.md).

SQL havuzu oluşturmak için [az SYNAPSE SQL Pool Create](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_create) komutunu kullanın:

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

Parametre seçenekleri hakkında daha fazla bilgi için bkz. [az SYNAPSE SQL Pool](/cli/azure/synapse/sql/pool).

SQL havuzlarınızı [az SYNAPSE SQL Pool List](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_list) komutunu kullanarak görebilirsiniz:

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

Var olan bir havuzu güncelleştirmek için [az SYNAPSE SQL Pool Update](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_update) komutunu kullanın:

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Havuzunuzu duraklatmak için [az SYNAPSE SQL Pool Pause](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_pause) komutunu kullanın:

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Duraklatılmış bir havuz başlatmak için [az SYNAPSE SQL Pool özgeçmişi](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_resume) komutunu kullanın:

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Mevcut bir SQL havuzunu kaldırmak için [az SYNAPSE SQL Pool Delete](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_delete) komutunu kullanın:

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu koleksiyondaki diğer hızlı başlangıç öğreticileri, bu hızlı başlangıcı temel alır.

> [!TIP]
> Sonraki hızlı başlangıç öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için [az Group Delete](/cli/azure/group#az_group_delete) komutunu kullanın.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Artık bir SQL havuzu oluşturdunuz, bir güvenlik duvarı kuralı oluşturdunuz ve SQL havuzunuza bağladınız. Daha fazla bilgi edinmek için [VERILERI SQL havuzu 'Na yükleme](./load-data-from-azure-blob-storage-using-copy.md) makalesine ilerleyin.
