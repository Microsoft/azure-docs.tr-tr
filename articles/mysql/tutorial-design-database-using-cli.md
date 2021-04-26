---
title: 'Öğretici: sunucu tasarlama-Azure CLı-MySQL için Azure veritabanı'
description: Bu öğreticide, MySQL sunucusu ve veritabanı için komut satırından Azure CLI kullanarak Azure Veritabanının nasıl oluşturulup yönetileceği açıklanır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 19779ffa8bab045e03161475646c802f464a7e41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791634"
---
# <a name="tutorial-design-an-azure-database-for-mysql-using-azure-cli"></a>Öğretici: Azure CLI’yı kullanarak MySQL için Azure Veritabanı tasarlama

MySQL için Azure Veritabanı, MySQL Community Edition veritabanı altyapısını temel alan, Microsoft bulutunda bulunan ilişkisel bir veritabanı hizmetidir. Bu öğreticide, şunları nasıl yapacağınızı öğrenmek için Azure CLI (komut satırı arabirimi) ve diğer yardımcı programları kullanırsınız:

> [!div class="checklist"]
> * MySQL için Azure Veritabanı oluşturma
> * Sunucu güvenlik duvarını yapılandırma
> * Veritabanı oluşturmak için [mysql komut satırı aracı](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) kullanma
> * Örnek verileri yükleme
> * Verileri sorgulama
> * Verileri güncelleştirme
> * Verileri geri yükleme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

Birden fazla aboneliğiniz varsa kaynağın mevcut olduğu ve faturalandırıldığı uygun aboneliği seçin. [az account set](/cli/azure/account#az_account_set) komutunu kullanarak hesabınız altındaki belirli bir abonelik kimliğini seçin.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
[az group create](/cli/azure/group#az_group_create) komutuyla bir [Azure kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek `westus` konumunda `myresourcegroup` adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>MySQL için Azure Veritabanı sunucusu oluşturma
az mysql server create komutunu kullanarak MySQL için Azure Veritabanı sunucusu oluşturun. Bir sunucu birden çok veritabanını yönetebilir. Genellikle her proje veya kullanıcı için farklı bir veritabanı kullanılır.

Aşağıdaki örnekte, `westus` bölgesinde bulunan `myresourcegroup` kaynak grubundaki `mydemoserver` adlı MySQL sunucusu için bir Azure Veritabanı oluşturulur. Sunucuda adlı bir yönetici kullanıcısı vardır `myadmin` . 2 sanal çekirdeğe sahip bir Genel Amaçlı, Gen 5 sunucusudur. `<server_admin_password>` değerini kendi değerinizle değiştirin.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```
sku-name parametresi değeri aşağıdaki örneklerde gösterildiği gibi {fiyatlandırma katmanı}\_{işlem oluşturma}\_{sanal çekirdek} kuralını kullanır:
+ `--sku-name B_Gen5_2` Temel, Gen 5 ve 2 sanal çekirdekler ile eşlenir.
+ `--sku-name GP_Gen5_32` Genel Amaçlı, Gen 5 ve 32 sanal çekirdekle eşleşir.
+ `--sku-name MO_Gen5_2` Bellek için iyileştirilmiş, Gen 5 ve 2 sanal çekirdekle eşleşir.

Bölgeler ve katmanlar için geçerli olan değerleri anlamak için lütfen [fiyatlandırma katmanları](./concepts-pricing-tiers.md) belgesini inceleyin.

> [!IMPORTANT]
> Burada belirttiğiniz sunucu yöneticisi kullanıcı adı ve parolası, bu hızlı başlangıcın sonraki bölümlerinde sunucuda ve veritabanlarında oturum açmak için gereklidir. Bu bilgileri daha sonra kullanmak üzere aklınızda tutun veya kaydedin.


## <a name="configure-firewall-rule"></a>Güvenlik duvarı kuralını yapılandırma
az mysql server firewall-rule create komutunu kullanarak MySQL için Azure Veritabanı sunucusu düzeyinde bir güvenlik duvarı kuralı oluşturun. Sunucu düzeyindeki bir güvenlik duvarı kuralı, **mysql** komut satırı aracı veya MySQL Workbench gibi bir dış uygulamanın Azure MySQL hizmetinin güvenlik duvarı üzerinden sunucunuza bağlanmasına imkan tanır. 

Aşağıdaki örnek `AllowMyIP` adında ve 192.168.0.1 IP adresinden gelen bağlantılara izin veren bir güvenlik duvarı kuralı oluşturur. IP adresini veya IP adresi aralıklarını bağlandığınız adreslere göre değiştirin. 

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma

Sunucunuza bağlanmak için ana bilgisayar bilgilerini ve erişim kimlik bilgilerini sağlamanız gerekir.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Sonuç JSON biçimindedir. **fullyQualifiedDomainName** ve **administratorLogin** bilgilerini not alın.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>mysql kullanarak sunucuya bağlanma
MySQL sunucusu için Azure Veritabanınız ile bağlantı kurmak üzere [mysql komut satırı aracını](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) kullanın. Bu örnekte, komut şöyledir:
```cmd
mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Boş veritabanı oluşturma
Sunucuya bağlandıktan sonra, boş bir veritabanı oluşturun.
```sql
mysql> CREATE DATABASE mysampledb;
```

İstemde, bağlantıyı bu yeni oluşturulan veritabanına değiştirmek için şu komutu çalıştırın:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Veritabanında tablo oluşturma
Artık MySQL veritabanı için Azure Veritabanına nasıl bağlanacağınızı bildiğinize göre bazı temel görevleri tamamlayın.

İlk olarak, bir tablo oluşturun ve bu tabloya bazı veriler yükleyin. Envanter bilgilerini depolayan bir tablo oluşturalım.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Tablolara veri yükleme
Bir tablonuz olduğuna göre içine bazı veriler ekleyin. Açık komut istemi penceresinde, birkaç veri satırı eklemek için şu sorguyu çalıştırın.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Daha önce oluşturduğunuz tabloda artık iki satırlık örnek verileriniz var.

## <a name="query-and-update-the-data-in-the-tables"></a>Tablolardaki verileri sorgulama ve güncelleştirme
Veritabanı tablosundan bilgi almak için şu sorguyu yürütün.
```sql
SELECT * FROM inventory;
```

Ayrıca tablolardaki verileri de güncelleştirebilirsiniz.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Siz veri aldıkça satır güncelleştirilir.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Bir veritabanını daha önceki bir noktaya geri yükleme
Bu tabloyu yanlışlıkla sildiğinizi düşünün. Bu işlemi kolayca geri alamazsınız. MySQL için Azure Veritabanı, son 35 günde zamanın herhangi bir noktasına geri dönmenize ve bu zaman noktasını yeni bir sunucuya geri yüklemenize olanak tanır. Bu yeni sunucuyu silinen verilerinizi kurtarmak için kullanabilirsiniz. Şu adımlar, örnek sunucuyu tablo eklenmeden önceki bir noktaya geri yükler.

Geri yüklemek için şu bilgiler gereklidir:

- Geri yükleme noktası: Sunucu değiştirilmeden önce gerçekleşen bir zaman seçin. Kaynak veritabanının En eski yedekleme değerinden daha büyük veya bu değere eşit olmalıdır.
- Hedef sunucu: Geri yüklemek istediğiniz yeni bir sunucu adı sağlayın
- Kaynak sunucu: Geri yüklemek istediğiniz sunucunun adını belirtin
- Konum: Bölgeyi seçemezsiniz, varsayılan olarak kaynak sunucuyla aynıdır

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

`az mysql server restore` komutu için aşağıdaki parametreler gerekir:

| Ayar | Önerilen değer | Açıklama  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Kaynak sunucunun bulunduğu kaynak grubu.  |
| name | mydemoserver-restored | Geri yükleme komutu tarafından oluşturulan yeni sunucunun adı. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Zaman içinde geri yüklenecek bir nokta seçin. Bu tarih ve saat, kaynak sunucunun yedekleme saklama dönemi içinde olmalıdır. ISO8601 tarih ve saat biçimini kullanın. Örneğin, `2017-04-13T05:59:00-08:00` şeklinde kendi yerel saat diliminizi ya da `2017-04-13T13:59:00Z` şeklindeki UTC Zulu biçimini kullanabilirsiniz. |
| source-server | mydemoserver | Geri yükleme kaynağı olarak kullanılacak sunucunun adı veya kimliği. |

Bir sunucu zaman içinde bir noktaya geri yüklendiğinde, özgün sunucunun belirttiğiniz noktadaki durumunun kopyalanmasıyla yeni bir sunucu oluşturulur. Geri yüklenen sunucunun konum ve fiyatlandırma katmanı değerleri kaynak sunucu ile aynı olur.

Komut zaman uyumludur ve sunucu geri yüklendikten sonra döndürülür. Geri yükleme tamamlandığında, oluşturulan yeni sunucuyu bulun. Verilerin beklenen şekilde geri yüklendiğini doğrulayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu kaynaklara başka bir hızlı başlangıç/öğretici için gereksinim duymuyorsanız aşağıdaki komutu çalıştırarak kaynakları silebilirsiniz: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Yeni oluşturulan sunucuyu silmek istiyorsanız [az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete) komutunu kullanabilirsiniz.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:
> [!div class="checklist"]
> * MySQL için Azure Veritabanı sunucusu oluşturma
> * Sunucu güvenlik duvarını yapılandırma
> * Veritabanı oluşturmak için mysql komut satırı aracı kullanma
> * Örnek verileri yükleme
> * Verileri sorgulama
> * Verileri güncelleştirme
> * Verileri geri yükleme

> [!div class="nextstepaction"]
> [MySQL için Azure Veritabanı - Azure CLI örnekleri](./sample-scripts-azure-cli.md)