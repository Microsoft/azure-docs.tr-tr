---
title: Sunucu parametrelerini Yapılandırma-Azure CLı-MySQL için Azure veritabanı esnek sunucu
description: Bu makalede, Azure CLı komut satırı yardımcı programını kullanarak MySQL için Azure veritabanı esnek sunucusu 'nda hizmet parametrelerinin nasıl yapılandırılacağı açıklanır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 21f9b3dcb94be105c8b7279e6ac447540da65447
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110109"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Azure CLı kullanarak MySQL için Azure veritabanı esnek sunucusu 'nda sunucu parametrelerini yapılandırma

> [!IMPORTANT] 
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Azure komut satırı yardımcı programını kullanarak MySQL için Azure veritabanı esnek sunucusu için parametreleri listeleyebilir, gösterebilir ve güncelleştirebilirsiniz. Sunucuyu oluştururken sunucu parametreleri varsayılan ve önerilen değer ile yapılandırılır.  

Bu makalede, Azure CLı kullanılarak sunucu parametrelerini listeleme, gösterme ve güncelleştirme işlemlerinin nasıl yapılacağı açıklanır.

>[!Note]
> Sunucu parametreleri genel olarak sunucu düzeyinde güncelleştirilemeyebilir, [Azure CLI](./how-to-configure-server-parameters-cli.md) veya [Azure Portal](./how-to-configure-server-parameters-portal.md)

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:
- [MySQL için Azure veritabanı esnek sunucusu](quickstart-create-server-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) komut satırı yardımcı programı veya Azure Cloud Shell tarayıcıda kullanın.

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>MySQL için Azure veritabanı esnek sunucusu için sunucu parametrelerini listeleyin
Bir sunucudaki tüm parametreleri ve değerlerini listelemek için [az MySQL esnek-sunucu parametre listesi](/cli/azure/mysql/flexible-server/parameter) komutunu çalıştırın.

Sunucu **mydemoserver.MySQL.Database.Azure.com** sunucu parametrelerini, **myresourcegroup** kaynak grubu altında listeleyebilirsiniz.
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
Listelenen parametrelerin her birinin tanımı için [sunucu sistem değişkenleri](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)' nde MySQL başvurusu bölümüne bakın.

## <a name="show-server-parameter-details"></a>Sunucu parametresi ayrıntılarını göster
Bir sunucu için belirli bir parametre hakkındaki ayrıntıları göstermek için [az MySQL esnek-sunucu parametresi göster](/cli/azure/mysql/flexible-server/parameter) komutunu çalıştırın.

Bu örnekte, **myresourcegroup** kaynak grubu altındaki Server **mydemoserver.MySQL.Database.Azure.com** için **yavaş \_ sorgu \_ günlüğü** sunucu parametresinin ayrıntıları gösterilmektedir.
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>Sunucu parametre değerini değiştirme
Ayrıca, MySQL Server altyapısının temel yapılandırma değerini güncelleştiren belirli bir sunucu parametresinin değerini de değiştirebilirsiniz. Sunucu parametresini güncelleştirmek için [az MySQL esnek-sunucu parametre kümesi](/cli/azure/mysql/flexible-server/parameter) komutunu kullanın. 

Sunucu **mydemoserver.MySQL.Database.Azure.com** 'nin **yavaş \_ sorgu \_ günlüğü** sunucu parametresini, **myresourcegroup** kaynak grubu altında güncelleştirmek için.
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
Bir parametrenin değerini sıfırlamak istiyorsanız, isteğe bağlı `--value` parametresini atlayın ve hizmet varsayılan değeri uygular. Yukarıdaki örnekte, şöyle görünür:
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
Bu kod, **yavaş \_ sorgu \_ günlüğünü** varsayılan değere sıfırlar.  

## <a name="setting-non-modifiable-server-parameters"></a>Değiştirilemeyen sunucu parametreleri ayarlanıyor

Güncelleştirmek istediğiniz sunucu parametresi değiştirilebilir değilse, isteğe bağlı olarak parametresini kullanarak bağlantı düzeyinde ayarlayabilirsiniz `init_connect` . Bu, sunucuya bağlanan her istemci için sunucu parametrelerini ayarlar. 

**Myresourcegroup** kaynak grubu altındaki Server **mydemoserver.MySQL.Database.Azure.com** 'ın **init \_ Connect** sunucu parametresini, karakter kümesi gibi değerleri ayarlamak için güncelleştirin.
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> `init_connect`, SÜPER ayrıcalıklar gerektirmeyen parametreleri oturum düzeyinde değiştirmek için kullanılabilir. Parametreyi `init_connect` kullanarak ayarlayıp ayarlayamayacağınızı doğrulamak için `set session parameter_name=YOUR_DESIRED_VALUE;` komutunu yürütün. **Erişim reddedildi; SÜPER ayrıcalıklar gerekiyor** hatası alırsanız ilgili parametreyi "init_connect" kullanarak ayarlayamazsınız.

## <a name="working-with-the-time-zone-parameter"></a>Saat dilimi parametresiyle çalışma

### <a name="populating-the-time-zone-tables"></a>Saat dilimi tablolarını doldurma

Sunucunuzdaki saat dilimi tabloları, `mysql.az_load_timezone` MySQL komut satırı veya MySQL çalışma ekranı gibi bir araçtan saklı yordam çağırarak doldurulabilir.

> [!NOTE]
> `mysql.az_load_timezone`MySQL çalışma modundan komutunu çalıştırıyorsanız, önce kullanarak güvenli güncelleştirme modunu kapatmanız gerekebilir `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Saat dilimi tablolarının doğru doldurulduğundan emin olmak için sunucuyu yeniden başlatmanız gerekir.<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

Kullanılabilir saat dilimi değerlerini görüntülemek için aşağıdaki komutu çalıştırın:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Genel düzey saat dilimini ayarlama

Genel düzey saat dilimi, [az MySQL esnek-sunucu parametre kümesi](/cli/azure/mysql/flexible-server/parameter) komutu kullanılarak ayarlanabilir.

Aşağıdaki komut, **myresourcegroup** kaynak grubu altındaki Server **mydemoserver.MySQL.Database.Azure.com** 'ın **Saat \_ dilimi** sunucu parametresini **ABD/Pasifik** olarak güncelleştirir.

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Oturum düzeyi saat dilimini ayarlama

Oturum düzeyi saat dilimi, `SET time_zone` MySQL komut satırı veya MySQL çalışma ekranı gibi bir araçtan komutu çalıştırılarak ayarlanabilir. Aşağıdaki örnek saat dilimini **ABD/Pasifik** saati dilimine göre ayarlar.  

```sql
SET time_zone = 'US/Pacific';
```

[Tarih ve saat işlevleri](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)için MySQL belgelerine bakın.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Portal 'da sunucu parametrelerini](./how-to-configure-server-parameters-portal.md) yapılandırma
