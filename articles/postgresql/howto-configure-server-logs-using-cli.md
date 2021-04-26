---
title: Günlükleri yönetme-Azure CLı-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede, Azure CLı kullanarak PostgreSQL için Azure veritabanı-tek sunucu içindeki sunucu günlüklerinin (. log dosyaları) nasıl yapılandırılacağı ve erişebileceği açıklanır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 64582e7655ef2a3cf72547bfa8c3269f1624c890
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604184"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Azure CLı kullanarak sunucu günlüklerini yapılandırma ve erişme
PostgreSQL sunucusu hata günlüklerini komut satırı arabirimi (Azure CLı) kullanarak indirebilirsiniz. Ancak, işlem günlüklerine erişim desteklenmez. 

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:
- [PostgreSQL için Azure veritabanı sunucusu](quickstart-create-server-database-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) komut satırı yardımcı programı veya Azure Cloud Shell tarayıcıda

## <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma
Sunucuyu sorgu günlüklerine ve hata günlüklerine erişecek şekilde yapılandırabilirsiniz. Hata günlükleri otomatik vakum, bağlantı ve denetim noktası bilgilerine sahip olabilir.
1. Günlüğe kaydetmeyi açın.
2. Sorgu günlüğünü etkinleştirmek için **log \_ ifadesini** ve **günlük \_ Min \_ Duration \_ ifadesini** güncelleştirin.
3. Güncelleştirme bekletme süresi.

Daha fazla bilgi için bkz. [sunucu yapılandırma parametrelerini özelleştirme](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Günlükleri Listele
Sunucunuzun kullanılabilir günlük dosyalarını listelemek için [az Postgres Server-Logs List](/cli/azure/postgres/server-logs) komutunu çalıştırın.

Sunucu **mydemoserver.Postgres.Database.Azure.com** için günlük dosyalarını **myresourcegroup** kaynak grubu altında listeleyebilirsiniz. Ardından günlük dosyaları listesini **günlük \_ dosyaları \_list.txt** adlı bir metin dosyasına yönlendirin.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Günlükleri sunucudan yerel olarak indir
[Az Postgres Server-Logs Download](/cli/azure/postgres/server-logs) komutuyla, sunucunuza ait günlük dosyalarını tek tek indirebilirsiniz. 

Yerel ortamınıza **myresourcegroup** kaynak grubu altında sunucu **mydemoserver.Postgres.Database.Azure.com** için belirli günlük dosyasını indirmek üzere aşağıdaki örneği kullanın.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Sonraki adımlar
- Sunucu günlükleri hakkında daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı 'Nda sunucu günlükleri](concepts-server-logs.md).
- Sunucu parametreleri hakkında daha fazla bilgi için bkz. [Azure CLI kullanarak sunucu yapılandırma parametrelerini özelleştirme](howto-configure-server-parameters-using-cli.md).
