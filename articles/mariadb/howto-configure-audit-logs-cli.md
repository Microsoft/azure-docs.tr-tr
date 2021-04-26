---
title: Erişim denetim günlükleri-Azure CLı-MariaDB için Azure veritabanı
description: Bu makalede, Azure CLı 'dan MariaDB için Azure veritabanı 'nda denetim günlüklerinin nasıl yapılandırılacağı ve erişebileceği açıklanır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c1d446d8ee2863077ad84c361876758336f5a3cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665183"
---
# <a name="configure-and-access-azure-database-for-maria-db-audit-logs-in-the-azure-cli"></a>Azure CLı 'de Maria DB denetim günlükleri için Azure veritabanı 'nı yapılandırma ve erişme

[MariaDB Için Azure veritabanı denetim günlüklerini](concepts-audit-logs.md) Azure CLI 'dan yapılandırabilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu kılavuzu gerçekleştirmek için:

- [MariaDB sunucusu Için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-portal.md)gerekir.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="configure-audit-logging"></a>Denetim günlüğünü yapılandırma

>[!IMPORTANT]
> Sunucunuzun performansının yoğun bir şekilde etkilenmemesini sağlamak için, yalnızca denetim amaçlarıyla gerekli olay türlerini ve kullanıcıları günlüğe kaydetmek önerilir.

Aşağıdaki adımları kullanarak denetim günlüğünü etkinleştirin ve yapılandırın: 

1. **Audit_logs_enabled** PARAMETRESINI "on" olarak ayarlayarak denetim günlüklerini açın. 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. **Audit_log_events** parametresini güncelleştirerek günlüğe kaydedilecek [olay türlerini](concepts-audit-logs.md#configure-audit-logging) seçin.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. **Audit_log_exclude_users** parametresi güncelleştirilerek günlüğe kaydetme dışında tutulacak MariaDB kullanıcılarını ekleyin. Kullanıcıları, MariaDB kullanıcı adlarını girerek belirtin.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. **Audit_log_include_users** parametresini güncelleştirerek günlüğe kaydetmek için dahil edilecek herhangi bir özel MariaDB kullanıcısı ekleyin. Kullanıcıları, MariaDB kullanıcı adlarını girerek belirtin.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Sonraki adımlar

- MariaDB için Azure veritabanı 'nda [Denetim günlükleri](concepts-audit-logs.md) hakkında daha fazla bilgi edinin
- [Azure Portal](howto-configure-audit-logs-portal.md) denetim günlüklerini yapılandırma hakkında bilgi edinin
