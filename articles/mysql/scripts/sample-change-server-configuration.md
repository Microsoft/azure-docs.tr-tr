---
title: CLı betiği-sunucu parametrelerini değiştirme-MySQL için Azure veritabanı
description: Bu örnek CLI betiği, kullanılabilir tüm sunucu yapılandırmalarını listeler ve innodb_lock_wait_timeout değerini güncelleştirir.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: eea11eb79cda47a11456178100941db05f0f2fa1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791692"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI kullanarak bir MySQL için Azure Veritabanı sunucusunun yapılandırmalarını listeleme ve güncelleştirme
Bu örnek CLI betiği, MySQL için Azure Veritabanı sunucusuna ait tüm kullanılabilir yapılandırma parametrelerini izin verilen değerleriyle birlikte listeler ve *innodb_lock_wait_timeout* öğesini varsayılandan farklı bir değere ayarlar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 

## <a name="sample-script"></a>Örnek betik
Bu örnek betikte, vurgulanan satırları düzenleyerek yönetici kullanıcı adını ve parolasını kendi değerlerinizle güncelleştirin.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Betik çalıştırıldıktan sonra aşağıdaki komutu kullanarak kaynak grubunu ve bununla ilişkili tüm kaynakları kaldırın. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Betik açıklaması
Bu betik, aşağıdaki tabloda ana hatları verilen komutları kullanır:

| **Komutundaki** | **Notlar** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Veritabanlarını barındıran bir MySQL sunucusu oluşturur. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) | Bir MySQL için Azure Veritabanı sunucusunun yapılandırmalarını listeleyin. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) | Bir MySQL için Azure Veritabanı sunucusunun yapılandırmasını güncelleştirin. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) | Bir MySQL için Azure Veritabanı sunucusunun yapılandırmasını görüntüleyin. |
| [az group delete](/cli/azure/group#az_group_delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar
- Azure CLI hakkında daha fazla bilgi okuyun: [Azure CLI belgeleri](/cli/azure).
- Ek betikleri deneyin: [MySQL için Azure Veritabanı Azure CLI örnekleri](../sample-scripts-azure-cli.md)
- Sunucu parametreleri hakkında daha fazla bilgi için bkz. [MySQL için Azure Veritabanı’nda Sunucu Parametrelerini Yapılandırma](../howto-server-parameters.md).
