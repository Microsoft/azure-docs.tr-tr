---
title: Yavaş sorgu günlüklerine erişme-Azure CLı-MariaDB için Azure veritabanı
description: Bu makalede, Azure CLı komut satırı yardımcı programı kullanılarak MariaDB için Azure veritabanı 'ndaki yavaş günlüklere nasıl erişebileceğiniz açıklanır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 42382076b6c14989eb153725e991c8ef047ad15b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774800"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Azure CLı kullanarak Maria DB yavaş sorgu günlükleri için Azure veritabanı 'nı yapılandırma ve erişme

Azure komut satırı yardımcı programı olan Azure CLı 'yı kullanarak MariaDB yavaş sorgu günlükleri için Azure veritabanı 'nı indirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:
- [MariaDB sunucusu için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Tarayıcıda [Azure CLI](/cli/azure/install-azure-cli) veya Azure Cloud Shell

## <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma
Aşağıdaki adımları gerçekleştirerek, sunucuyu MySQL yavaş sorgu günlüğüne erişecek şekilde yapılandırabilirsiniz:
1. **Yavaş \_ sorgu \_ günlüğü** parametresini açık olarak ayarlayarak yavaş sorgu günlüğünü açın.
2. **Günlük \_ çıkışını** kullanarak günlüklerin çıktısının kaydedileceği yeri seçin. Günlükleri hem yerel depolama hem de Azure Izleyici tanılama günlüklerine göndermek için **Dosya**' yı seçin. Günlükleri yalnızca Azure Izleyici günlüklerine göndermek için **hiçbiri** ' ni seçin.
3. **Uzun \_ sorgu \_ süresi** ve **günlük \_ yavaş \_ Yönetim \_ deyimleri** gibi diğer parametreleri ayarlayın.

Bu parametrelerin değerini Azure CLı aracılığıyla ayarlamayı öğrenmek için bkz. [sunucu parametrelerini yapılandırma](howto-configure-server-parameters-cli.md).

Örneğin, aşağıdaki CLı komutu yavaş sorgu günlüğünü açar, uzun sorgu süresini 10 saniye olarak ayarlar ve sonra yavaş yönetici bildiriminin günlüğe kaydedilmesini kapatır. Son olarak, gözden geçirmeniz için yapılandırma seçeneklerini listeler.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>MariaDB sunucusu için Azure veritabanı günlüklerini listeleme
**Log_output** "dosya" olarak yapılandırılırsa, günlüklere doğrudan sunucunun yerel depolama alanından erişebilirsiniz. Sunucunuzun kullanılabilir yavaş sorgu günlük dosyalarını listelemek için [az MariaDB Server-Logs List](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_list) komutunu çalıştırın.

Sunucu **mydemoserver.MariaDB.Database.Azure.com** için günlük dosyalarını **myresourcegroup** kaynak grubu altında listeleyebilirsiniz. Ardından günlük dosyaları listesini **günlük \_ dosyaları \_list.txt** adlı bir metin dosyasına yönlendirin.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Günlükleri sunucudan indir
**Log_output** "dosya" olarak yapılandırıldıysa, [az MariaDB Server-Logs Download](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_download) komutuyla sunucunuzdaki tek tek günlük dosyalarını indirebilirsiniz.

Yerel ortamınıza **myresourcegroup** kaynak grubu altında sunucu **mydemoserver.MariaDB.Database.Azure.com** için belirli günlük dosyasını indirmek üzere aşağıdaki örneği kullanın.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Sonraki adımlar
- [MariaDB Için Azure veritabanı 'nda yavaş sorgu günlükleri](concepts-server-logs.md)hakkında bilgi edinin.
