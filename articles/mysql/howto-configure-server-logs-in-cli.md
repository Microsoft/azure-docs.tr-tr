---
title: Yavaş sorgu günlüklerine erişme-Azure CLı-MySQL için Azure veritabanı
description: Bu makalede, Azure CLı kullanarak MySQL için Azure veritabanı 'ndaki yavaş sorgu günlüklerine nasıl erişebileceğiniz açıklanır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d5fc2b14a655251e59a9209e078b0534f08baf9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763244"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Azure CLı kullanarak yavaş sorgu günlüklerini yapılandırma ve erişme
Azure komut satırı yardımcı programı olan Azure CLı 'yı kullanarak MySQL için Azure veritabanı yavaş sorgu günlüklerini indirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:
- [MySQL için Azure veritabanı sunucusu](quickstart-create-mysql-server-database-using-azure-cli.md)
- Tarayıcıda [Azure CLI](/cli/azure/install-azure-cli) veya Azure Cloud Shell

## <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma
Aşağıdaki adımları gerçekleştirerek, sunucuyu MySQL yavaş sorgu günlüğüne erişecek şekilde yapılandırabilirsiniz:
1. **Yavaş \_ sorgu \_ günlüğü** parametresini açık olarak ayarlayarak yavaş sorgu günlüğünü açın.
2. **Günlük \_ çıkışını** kullanarak günlüklerin çıktısının kaydedileceği yeri seçin. Günlükleri hem yerel depolama hem de Azure Izleyici tanılama günlüklerine göndermek için **Dosya**' yı seçin. Günlükleri yalnızca Azure Izleyici günlüklerine göndermek için **hiçbiri** ' ni seçin.
3. **Uzun \_ sorgu \_ süresi** ve **günlük \_ yavaş \_ Yönetim \_ deyimleri** gibi diğer parametreleri ayarlayın.

Bu parametrelerin değerini Azure CLı aracılığıyla ayarlamayı öğrenmek için bkz. [sunucu parametrelerini yapılandırma](howto-configure-server-parameters-using-cli.md).

Örneğin, aşağıdaki CLı komutu yavaş sorgu günlüğünü açar, uzun sorgu süresini 10 saniye olarak ayarlar ve sonra yavaş yönetici bildiriminin günlüğe kaydedilmesini kapatır. Son olarak, gözden geçirmeniz için yapılandırma seçeneklerini listeler.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>MySQL için Azure veritabanı sunucusu için günlükleri listeleme
**Log_output** "dosya" olarak yapılandırılırsa, günlüklere doğrudan sunucunun yerel depolama alanından erişebilirsiniz. Sunucunuzun kullanılabilir yavaş sorgu günlük dosyalarını listelemek için [az MySQL Server-Logs List](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) komutunu çalıştırın.

Sunucu **mydemoserver.MySQL.Database.Azure.com** için günlük dosyalarını **myresourcegroup** kaynak grubu altında listeleyebilirsiniz. Ardından günlük dosyaları listesini **günlük \_ dosyaları \_list.txt** adlı bir metin dosyasına yönlendirin.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Günlükleri sunucudan indir
**Log_output** "dosya" olarak yapılandırıldıysa, [az MySQL Server-Logs Download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) komutuyla sunucunuzdaki tek tek günlük dosyalarını indirebilirsiniz.

Yerel ortamınıza **myresourcegroup** kaynak grubu altında sunucu **mydemoserver.MySQL.Database.Azure.com** için belirli günlük dosyasını indirmek üzere aşağıdaki örneği kullanın.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Sonraki adımlar
- [MySQL Için Azure veritabanı 'nda yavaş sorgu günlükleri](concepts-server-logs.md)hakkında bilgi edinin.
