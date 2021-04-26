---
title: CLı betiği-restore Server-MariaDB için Azure veritabanı
description: Bu örnek Azure CLı betiği, MariaDB sunucusu ve veritabanlarının veritabanları için Azure veritabanı 'nın zaman içinde önceki bir noktaya nasıl geri yükleneceğini gösterir.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 3c56c7d933f840e4418bd481cce0db1bc2216e3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785630"
---
# <a name="restore-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure CLı kullanarak bir MariaDB sunucusu için Azure veritabanı 'nı geri yükleme
Bu örnek CLı betiği, MariaDB sunucusu için tek bir Azure veritabanını zaman içinde önceki bir noktaya geri yükler.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 

## <a name="sample-script"></a>Örnek betik
Bu örnek betikte, vurgulanan satırları düzenleyerek yönetici kullanıcı adını ve parolasını kendi değerlerinizle güncelleştirin. `az monitor` komutlarında kullanılan abonelik kimliğini kendi abonelik kimliğinizle değiştirin.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Betik çalıştırıldıktan sonra aşağıdaki komutu kullanarak kaynak grubunu ve bununla ilişkili tüm kaynakları kaldırın. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/backup-restore-pitr/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Betik açıklaması
Bu betik, aşağıdaki tabloda ana hatları verilen komutları kullanır:

| **Komutundaki** | **Notlar** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az MariaDB sunucu oluştur](/cli/azure/mariadb/server#az_mariadb_server_create) | Veritabanlarını barındıran bir MariaDB sunucusu oluşturur. |
| [az MariaDB sunucu geri yükleme](/cli/azure/mariadb/server#az_mariadb_server_restore) | Bir sunucuyu yedekten geri yükler. |
| [az group delete](/cli/azure/group#az_group_delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar
- Azure CLI hakkında daha fazla bilgi okuyun: [Azure CLI belgeleri](/cli/azure).
- Ek betikleri deneyin: [MariaDB Için Azure veritabanı Azure CLI örnekleri](../sample-scripts-azure-cli.md)
