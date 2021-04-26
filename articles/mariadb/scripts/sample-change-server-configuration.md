---
title: CLı betiği-sunucu parametrelerini değiştirme-MariaDB için Azure veritabanı
description: Bu örnek CLı betiği, tüm kullanılabilir sunucu yapılandırmalarının ve MariaDB için Azure veritabanı güncelleştirmelerinin listesini listeler.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 3504f1221c501b997b04d9c81c721aba2903fba6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777068"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure CLı kullanarak MariaDB sunucusu için Azure veritabanı 'nın yapılandırmasını listeleme ve güncelleştirme
Bu örnek CLı betiği, tüm kullanılabilir yapılandırma parametrelerini ve ayrıca, MariaDB sunucusu için Azure veritabanı için izin verilen değerleri listeler ve *innodb_lock_wait_timeout* varsayılan değer dışında bir değere ayarlar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik
Bu örnek betikte, vurgulanan satırları düzenleyerek yönetici kullanıcı adını ve parolasını kendi değerlerinizle güncelleştirin.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Betik çalıştırıldıktan sonra aşağıdaki komutu kullanarak kaynak grubunu ve bununla ilişkili tüm kaynakları kaldırın.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Betik açıklaması
Bu betik, aşağıdaki tabloda ana hatları verilen komutları kullanır:

| **Komutundaki** | **Notlar** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az MariaDB sunucu oluştur](/cli/azure/mariadb/server#az_mariadb_server_create) | Veritabanlarını barındıran bir MariaDB sunucusu oluşturur. |
| [az MariaDB sunucu yapılandırma listesi](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_list) | MariaDB sunucusu için Azure veritabanı 'nın yapılandırmasını listeleyin. |
| [az MariaDB sunucu yapılandırma kümesi](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set) | MariaDB sunucusu için Azure veritabanı yapılandırmasını güncelleştirin. |
| [az MariaDB sunucu yapılandırma Show](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_show) | MariaDB sunucusu için Azure veritabanı yapılandırmasını gösterir. |
| [az group delete](/cli/azure/group#az_group_delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar
- Azure CLI hakkında daha fazla bilgi okuyun: [Azure CLI belgeleri](/cli/azure).
- Ek betikleri deneyin: [MariaDB Için Azure veritabanı Azure CLI örnekleri](../sample-scripts-azure-cli.md)
- Sunucu parametreleri hakkında daha fazla bilgi için bkz. [MariaDB Için Azure veritabanı 'Nda sunucu parametrelerini yapılandırma](../howto-server-parameters.md).
