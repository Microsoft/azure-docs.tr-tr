---
title: 'Azure CLı: yönetilen örnek oluşturma'
description: Azure SQL yönetilen örneği 'nde yönetilen bir örnek oluşturmak için Azure CLı örnek betiği
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 54e1aa993f177a4d3bc255287ae8c2fb14cf65af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87497282"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>CLı kullanarak Azure SQL yönetilen örneği oluşturma

Bu Azure CLı betik örneği, yeni bir sanal ağ içindeki adanmış bir alt ağda Azure SQL yönetilen örneği oluşturur. Ayrıca, bir yol tablosu ve sanal ağ için bir ağ güvenlik grubu yapılandırır. Betik başarıyla çalıştırıldığında, yönetilen örneğe sanal ağ içinden veya şirket içi bir ortamdan erişilebilir. Bkz. [Azure VM 'yi Azure SQL yönetilen örneğine bağlanmak için yapılandırma].. /.. /Azure-SQL/Managed-instance/Connect-VM-instance-configure.MD) ve [Şirket içinden bir Azure SQL yönetilen örneğine Noktadan siteye bağlantı yapılandırın](../../azure-sql/managed-instance/point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Sınırlamalar için bkz. [Desteklenen bölgeler](../../azure-sql/managed-instance/resource-limits.md#supported-regions) ve [desteklenen Abonelik türleri](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types).

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale için Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Örnek betik

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Betiği çalıştırın

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Dağıtımı temizleme

Kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu kullanın.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Örnek başvuru

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Açıklama |
|---|---|
| [az Network VNET](/cli/azure/network/vnet) | Sanal ağ komutları. |
| [az Network VNET subnet](/cli/azure/network/vnet/subnet) | Sanal ağ alt ağ komutları. |
| [az Network Route-Table](/cli/azure/network/route-table) | Ağ yol tablosu komutları. |
| [az SQL mi](/cli/azure/sql/mi) | SQL yönetilen örnek komutları. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek SQL Veritabanı CLI betiği örnekleri, [Azure SQL Veritabanı belgeleri](../../azure-sql/database/az-cli-script-samples-content-guide.md) içinde bulunabilir.
