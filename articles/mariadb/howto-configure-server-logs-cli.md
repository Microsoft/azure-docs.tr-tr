---
title: Azure CLı kullanarak MariaDB için Azure veritabanı 'nda sunucu günlüklerine erişme
description: Bu makalede, Azure CLı komut satırı yardımcı programı kullanılarak MariaDB için Azure veritabanı 'nda sunucu günlüklerine nasıl erişebileceğiniz açıklanır.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: ffc724ef5133ee25643a966d2b6d8448a4c3a920
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023604"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Azure CLı kullanarak sunucu günlüklerini yapılandırma ve erişme
Azure komut satırı yardımcı programı olan Azure CLı 'yı kullanarak MariaDB sunucu günlükleri için Azure veritabanı 'nı indirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:
- [MariaDB sunucusu için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Tarayıcıda [Azure CLI](/cli/azure/install-azure-cli) veya Azure Cloud Shell

## <a name="configure-logging-for-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı günlüğünü yapılandırma
Aşağıdaki adımları gerçekleştirerek, sunucuyu MariaDB yavaş sorgu günlüğüne erişecek şekilde yapılandırabilirsiniz:
1. **Yavaş @ no__t-1query @ no__t-2Log** parametresini açık olarak ayarlayarak günlüğe kaydetmeyi etkinleştirin.
2. **Long @ no__t-1query @ no__t-2time** ve **log @ no__t-4yavaş @ no__t-5admin @ no__t-6deyimlerini**gibi diğer parametreleri ayarlayın.

Bu parametrelerin değerini Azure CLı aracılığıyla ayarlamayı öğrenmek için bkz. [sunucu parametrelerini yapılandırma](howto-configure-server-parameters-cli.md).

Örneğin, aşağıdaki CLı komutu yavaş sorgu günlüğünü açar, uzun sorgu süresini 10 saniye olarak ayarlar ve sonra yavaş yönetici bildiriminin günlüğe kaydedilmesini kapatır. Son olarak, gözden geçirmeniz için yapılandırma seçeneklerini listeler.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>MariaDB sunucusu için Azure veritabanı günlüklerini listeleme
Sunucunuzun kullanılabilir yavaş sorgu günlük dosyalarını listelemek için [az MariaDB Server-Logs List](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) komutunu çalıştırın.

Sunucu **mydemoserver.MariaDB.Database.Azure.com** için günlük dosyalarını **myresourcegroup**kaynak grubu altında listeleyebilirsiniz. Ardından günlük dosyaları listesini **log @ no__t-1files\_list.txt**adlı bir metin dosyasına yönlendirin.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Günlükleri sunucudan indir
[Az MariaDB Server-Logs Download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) komutuyla, sunucunuza ait günlük dosyalarını tek tek indirebilirsiniz.

Yerel ortamınıza **myresourcegroup** kaynak grubu altında sunucu **mydemoserver.MariaDB.Database.Azure.com** için belirli günlük dosyasını indirmek üzere aşağıdaki örneği kullanın.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Sonraki adımlar
- [MariaDB Için Azure veritabanı 'nda yavaş sorgu günlükleri](concepts-server-logs.md)hakkında bilgi edinin.
