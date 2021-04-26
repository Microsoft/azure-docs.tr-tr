---
title: Sunucu parametrelerini Yapılandırma-Azure CLı-MySQL için Azure veritabanı
description: Bu makalede, Azure CLı komut satırı yardımcı programını kullanarak MySQL için Azure veritabanı 'nda hizmet parametrelerinin nasıl yapılandırılacağı açıklanır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 94dd7f72f6411934587c76850a05d6885e9f6862
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763226"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-cli"></a>Azure CLı kullanarak MySQL için Azure veritabanı 'nda sunucu parametrelerini yapılandırma
Azure komut satırı yardımcı programını kullanarak MySQL için Azure veritabanı sunucusu için yapılandırma parametrelerini listeleyebilir, gösterebilir ve güncelleştirebilirsiniz. Altyapı yapılandırmalarının bir alt kümesi sunucu düzeyinde sunulur ve değiştirilebilir. 

>[!Note]
> Sunucu parametreleri genel olarak sunucu düzeyinde güncelleştirilemeyebilir, [Azure CLI](./howto-configure-server-parameters-using-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md)veya [Azure Portal](./howto-server-parameters.md) kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:
- [MySQL için Azure veritabanı sunucusu](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) komut satırı yardımcı programı veya Azure Cloud Shell tarayıcıda kullanın.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>MySQL için Azure veritabanı sunucusu için sunucu yapılandırma parametrelerini listeleme
Bir sunucudaki tüm değiştirilebilir parametreleri ve değerlerini listelemek için [az MySQL Server Configuration List](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) komutunu çalıştırın.

Sunucu **mydemoserver.MySQL.Database.Azure.com** için sunucu yapılandırma parametrelerini, **myresourcegroup** kaynak grubu altında listeleyebilirsiniz.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Listelenen parametrelerin her birinin tanımı için [sunucu sistem değişkenleri](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)' nde MySQL başvurusu bölümüne bakın.

## <a name="show-server-configuration-parameter-details"></a>Sunucu yapılandırma parametresi ayrıntılarını göster
Bir sunucu için belirli bir yapılandırma parametresi ayrıntılarını göstermek üzere [az MySQL Server Configuration Show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) komutunu çalıştırın.

Bu örnekte, **myresourcegroup** kaynak grubu altındaki Server **mydemoserver.MySQL.Database.Azure.com** için **yavaş \_ sorgu \_ günlüğü** sunucusu yapılandırma parametresinin ayrıntıları gösterilmektedir.
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Sunucu yapılandırma parametre değerini değiştirme
Ayrıca, MySQL Server altyapısının temel yapılandırma değerini güncelleştiren belirli bir sunucu yapılandırma parametresinin değerini de değiştirebilirsiniz. Yapılandırmayı güncelleştirmek için [az MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) komutunu kullanın. 

Sunucu **mydemoserver.MySQL.Database.Azure.com** 'nin **yavaş \_ sorgu \_ günlüğü** sunucu yapılandırma parametresini **myresourcegroup** kaynak grubu altında güncelleştirmek için.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Bir yapılandırma parametresinin değerini sıfırlamak istiyorsanız, isteğe bağlı `--value` parametresini atlayın ve hizmet varsayılan değeri uygular. Yukarıdaki örnekte, şöyle görünür:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Bu kod, **yavaş \_ sorgu \_ günlüğü** yapılandırmasını varsayılan değere sıfırlar.  

## <a name="setting-parameters-not-listed"></a>Ayar parametreleri listelenmedi
Güncelleştirmek istediğiniz sunucu parametresi Azure portal listelenmemişse, isteğe bağlı olarak, parametresini kullanarak bağlantı düzeyinde ayar yapabilirsiniz `init_connect` . Bu, sunucuya bağlanan her istemci için sunucu parametrelerini ayarlar. 

**Myresourcegroup** kaynak grubu altındaki Server **mydemoserver.MySQL.Database.Azure.com** 'ın **init \_ Connect** Server yapılandırma parametresini, karakter kümesi gibi değerleri ayarlamak için güncelleştirin.
```azurecli-interactive
az mysql server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Saat dilimi parametresiyle çalışma

### <a name="populating-the-time-zone-tables"></a>Saat dilimi tablolarını doldurma

Sunucunuzdaki saat dilimi tabloları, `mysql.az_load_timezone` MySQL komut satırı veya MySQL çalışma ekranı gibi bir araçtan saklı yordam çağırarak doldurulabilir.

> [!NOTE]
> `mysql.az_load_timezone`MySQL çalışma modundan komutunu çalıştırıyorsanız, önce kullanarak güvenli güncelleştirme modunu kapatmanız gerekebilir `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Saat dilimi tablolarının doğru doldurulduğundan emin olmak için sunucuyu yeniden başlatmanız gerekir. Sunucuyu yeniden başlatmak için [Azure Portal](howto-restart-server-portal.md) veya [CLI](howto-restart-server-cli.md)kullanın.

Kullanılabilir saat dilimi değerlerini görüntülemek için aşağıdaki komutu çalıştırın:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Genel düzey saat dilimini ayarlama

Genel düzey saat dilimi, [az MySQL Server yapılandırma kümesi](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) komutu kullanılarak ayarlanabilir.

Aşağıdaki komut, **myresourcegroup** kaynak grubu altındaki Server **mydemoserver.MySQL.Database.Azure.com** 'ın **Saat \_ dilimi** sunucu yapılandırma parametresini **ABD/Pasifik** olarak güncelleştirir.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Oturum düzeyi saat dilimini ayarlama

Oturum düzeyi saat dilimi, `SET time_zone` MySQL komut satırı veya MySQL çalışma ekranı gibi bir araçtan komutu çalıştırılarak ayarlanabilir. Aşağıdaki örnek saat dilimini **ABD/Pasifik** saati dilimine göre ayarlar.  

```sql
SET time_zone = 'US/Pacific';
```

[Tarih ve saat işlevleri](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)için MySQL belgelerine bakın.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Portal 'da sunucu parametrelerini](howto-server-parameters.md) yapılandırma
