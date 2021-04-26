---
title: CLı betiği-yavaş sorgu günlüklerini Indirme-MariaDB için Azure veritabanı
description: Bu örnek Azure CLı betiği, MariaDB sunucusu için Azure veritabanı 'nın yavaş sorgu günlüklerini etkinleştirme ve indirme işlemini gösterir.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 42b1806d3aa1235dfd976dd5700204aa0b9e4700
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785586"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure CLı kullanarak MariaDB için Azure veritabanı sunucusunun yavaş sorgu günlüklerini etkinleştirme ve indirme
Bu örnek CLı betiği, MariaDB sunucusu için tek bir Azure veritabanı 'nın yavaş sorgu günlüklerini sunar ve indirir.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="sample-script"></a>Örnek betik
Bu örnek betikte, vurgulanan satırları düzenleyerek yönetici kullanıcı adını ve parolasını kendi değerlerinizle güncelleştirin. &lt; &gt; `az monitor` Komutdaki Log_file_name kendi sunucu günlük dosyası adınızla değiştirin.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Betik çalıştırıldıktan sonra aşağıdaki komutu kullanarak kaynak grubunu ve bununla ilişkili tüm kaynakları kaldırın. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Betik açıklaması
Bu betik, aşağıdaki tabloda ana hatları verilen komutları kullanır:

| **Komutundaki** | **Notlar** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az MariaDB sunucu oluştur](/cli/azure/mariadb/server#az_mariadb_server_create) | Veritabanlarını barındıran bir MariaDB sunucusu oluşturur. |
| [az MariaDB sunucu yapılandırma listesi](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_list) | Bir sunucu için yapılandırma değerlerini listeleyin. |
| [az MariaDB sunucu yapılandırma kümesi](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set) | Bir sunucunun yapılandırmasını güncelleştirin. |
| [az MariaDB Server-Logs List](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_list) | Bir sunucunun günlük dosyalarını listeleyin. |
| [az MariaDB Server-Günlükler indir](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_download) | Günlük dosyalarını indirin. |
| [az group delete](/cli/azure/group#az_group_delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar
- Azure CLI hakkında daha fazla bilgi okuyun: [Azure CLI belgeleri](/cli/azure).
- Ek betikleri deneyin: [MariaDB Için Azure veritabanı Azure CLI örnekleri](../sample-scripts-azure-cli.md)
