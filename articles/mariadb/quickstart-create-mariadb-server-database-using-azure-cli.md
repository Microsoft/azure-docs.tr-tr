---
title: 'Hızlı başlangıç: sunucu oluşturma-Azure CLı-MariaDB için Azure veritabanı'
description: Bu hızlı başlangıçta, Azure CLI aracını kullanarak bir Azure kaynak grubunda nasıl MariaDB için Azure Veritabanı sunucusu oluşturabileceğiniz açıklanır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 042c70fdd08a6de2b97c4560eb2b6a24eec0bb34
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789996"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak MariaDB sunucusu için Azure veritabanı oluşturma

Azure CLI ile komut satırından veya betik içindeki Azure kaynaklarını oluşturabilir ve yönetebilirsiniz. Bu hızlı başlangıçta, Azure CLI aracını kullanarak bir Azure kaynak grubunda yaklaşık beş dakikada nasıl MariaDB için Azure Veritabanı sunucusu oluşturabileceğiniz açıklanır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

Birden fazla aboneliğiniz varsa kaynağı içeren aboneliği veya faturalandırıldığınız aboneliği seçin. Hesabınızdaki belirli bir abonelik kimliğini seçmek için [az account set](/cli/azure/account#az_account_set) komutunu kullanın:

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group#az_group_create) komutunu kullanarak bir [Azure kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek `westus` konumunda `myresourcegroup` adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>MariaDB için Azure Veritabanı sunucusu oluşturma

[az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) komutunu kullanarak MariaDB için Azure Veritabanı sunucusu oluşturun. Bir sunucu birden çok veritabanını yönetebilir. Genellikle her proje veya kullanıcı için farklı bir veritabanı kullanılır.

Ayar | Örnek değer | Description
---|---|---
name | **mydemoserver** | MariaDB için Azure Veritabanı sunucunuzu tanımlayan benzersiz bir ad girin. Sunucu adı yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. 3 ile 63 arasında karakter içermelidir.
resource-group | **myresourcegroup** | Azure kaynak grubunun adını girin.
sku-name | **GP_Gen5_2** | SKU'nun adı. *Fiyatlandırma katmanı* \_ *işlem oluşturma* \_ *sanal çekirdeklerini* toplu olarak izleyin. **sku-name** parametresi hakkında daha fazla bilgi için bu tablonun altındaki bölüme bakın.
backup-retention | **7** | Yedeklemenin ne kadar süreyle tutulacağı. Birim olarak gün kullanılır. Aralık: 7-35. 
geo-redundant-backup | **Devre dışı** | Coğrafi olarak yedekli yedeklemelerin bu sunucu için etkinleştirilip etkinleştirilmeyeceği. İzin verilen değerler: **Etkin**, **Devre Dışı**.
location | **westus** | Sunucu için Azure konumu.
ssl-enforcement | **Etkin** | Bu sunucu için SSL'in etkinleştirilip etkinleştirilmeyeceği. İzin verilen değerler: **Etkin**, **Devre Dışı**.
storage-size | **51200** | Sunucunun depolama kapasitesi (birim olarak megabayt kullanılır). Geçerli depolama boyutları 5.120 MB (minimum) ile başlar ve 1.024 MB artar. Depolama boyutu sınırları hakkında daha fazla bilgi için bkz. [Fiyatlandırma katmanları](./concepts-pricing-tiers.md). 
sürüm | **10,2** | MariaDB büyük altyapı sürümü.
admin-user | **myadmin** | Yöneticinin oturum açma kullanıcı adı. **admin-user** parametresi **azure_superuser**, **admin**, **administrator**, **root**, **guest** veya **public** olamaz.
admin-password | *Parolanız* | Yönetici kullanıcının parolası. Parolanız 8 ile 128 arasında karakter içermelidir. Şu üç kategoride yer alan karakterlerden oluşmalıdır: İngilizce büyük ve küçük harfler, sayılar ve alfasayısal olmayan karakterler.

sku-name parametresi değeri aşağıdaki örneklerde gösterildiği gibi {fiyatlandırma katmanı}\_{işlem oluşturma}\_{sanal çekirdek} kuralını kullanır:
+ `--sku-name B_Gen5_1` Temel, Gen 5 ve 1 sanal çekirdekle eşlenir. Bu seçenek, kullanılabilen en küçük SKU ' dır.
+ `--sku-name GP_Gen5_32` Genel Amaçlı, Gen 5 ve 32 sanal çekirdekle eşleşir.
+ `--sku-name MO_Gen5_2` Bellek için iyileştirilmiş, Gen 5 ve 2 sanal çekirdekle eşleşir.

Bölgelere ve katmanlara göre geçerli değerler hakkında bilgi için bkz. [Fiyatlandırma katmanları](./concepts-pricing-tiers.md).

Aşağıdaki örnekte Batı ABD bölgesinde **mydemoserver** adında bir sunucu oluşturulmaktadır. Sunucu **myresourcegroup** adlı kaynak grubundadır ve sunucu yöneticisi oturum açma adı **myadmin** şeklindedir. Sunucu, Genel Amaçlı fiyatlandırma katmanında bulunan 5. Nesil bir sunucudur ve 2 sanal çekirdeğe sahiptir. Sunucu adı bir DNS adıyla eşleşir ve bunun Azure'da benzersiz olması gerekir. `<server_admin_password>` değerini kendi sunucu yöneticisi parolanızla değiştirin.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

> [!NOTE]
> Hafif işlem ve g/ç iş yükünüz için yeterli ise temel fiyatlandırma katmanını kullanmayı düşünün. Temel fiyatlandırma katmanında oluşturulan sunucuların daha sonra Genel Amaçlı veya bellek için Iyileştirilmiş olarak ölçeklenmeyeceğini unutmayın. Daha fazla bilgi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/mariadb/) .

## <a name="configure-a-firewall-rule"></a>Güvenlik duvarı kuralını yapılandırma

[az maridb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_create) komutunu kullanarak MariaDB için Azure Veritabanı sunucusu düzeyinde bir güvenlik duvarı kuralı oluşturun. Sunucu düzeyindeki bir güvenlik duvarı kuralı, mysql komut satırı aracı veya MySQL Workbench gibi bir dış uygulamanın MariaDB için Azure Veritabanı hizmetinin güvenlik duvarı üzerinden sunucunuza bağlanmasına imkan tanır.

Aşağıdaki örnek `AllowMyIP` adında ve 192.168.0.1 IP adresinden gelen bağlantılara izin veren bir güvenlik duvarı kuralı oluşturur. Bağlandığınız konuma ait IP adresi veya IP adresi aralığıyla değiştirin.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> MariaDB için Azure Veritabanı bağlantıları, 3306 bağlantı noktası üzerinden iletişim kurar. Kurumsal ağ içinden bağlanmaya çalışıyorsanız, 3306 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, sunucunuza ancak BT departmanınız 3306 numaralı bağlantı noktasını açarsa bağlanabilirsiniz.

## <a name="configure-ssl-settings"></a>SSL ayarlarını yapılandırma

Varsayılan olarak sunucunuz ile istemci uygulamaları arasında SSL bağlantıları zorunlu tutulur. Bu varsayılan ayar, İnternet üzerinden veri akışını şifreleyerek “hareket halindeki” verilerinizin güvenli olmasını sağlar. Bu hızlı başlangıç için sunucunuzda SSL bağlantılarını devre dışı bırakın. Üretim sunucuları için SSL’in devre dışı bırakılması önerilmez. Daha ayrıntılı bilgi için bkz. [MariaDB için Azure Veritabanı'na güvenli bir şekilde bağlanmak üzere uygulamanızda SSL bağlantısını yapılandırma](./howto-configure-ssl.md).

Aşağıdaki örnek, MariaDB için Azure Veritabanı sunucunuzda SSL zorlamasını devre dışı bırakır:

```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>Bağlantı bilgilerini alma

Sunucunuza bağlanmak için ana bilgisayar bilgilerini ve erişim kimlik bilgilerini sağlamanız gerekir. Bağlantı bilgilerini almak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Sonuç JSON biçimindedir. **fullyQualifiedDomainName** ve **administratorLogin** değerlerini not alın.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>mysql komut satırı aracını kullanarak sunucuya bağlanma

mysql komut satırı aracını kullanarak sunucunuza bağlanın. Komut satırı aracını [indirebilir](https://dev.mysql.com/downloads/) ve bilgisayarınıza yükleyebilirsiniz. Komut satırı aracına erişmek için bu makaledeki kod örneklerinden birinin üzerindeki **Deneyin** düğmesini de seçebilirsiniz. Komut satırı aracına erişmenin bir diğer yolu da Azure portalda sağ üstten **>_** düğmesini seçerek **Azure Cloud Shell**'i açmaktır.

mysql komut satırı aracını kullanarak sunucuya bağlanmak için:

1. Sunucuya bağlanın:

   ```azurecli-interactive
   mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   ```

2. `mysql>` isteminde sunucu durumun görüntüleyin:

   ```sql
   status
   ```

   Aşağıdaki metne benzer bir sonuç görmeniz gerekir:

   ```cmd
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: ***********
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 65512
   Server version: 5.6.39.0 MariaDB Server

   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

   Connection id:          64681
   Current database:
   Current user:           myadmin@40.118.201.21
   SSL:                    Cipher in use is AES256-SHA
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 1 day 3 hours 28 min 50 sec

   Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
   --------------

   mysql>
   ```

> [!TIP]
> Ek komutlar için bkz. [MySQL 5.7 Başvuru Kılavuzu - Bölüm 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>MySQL Workbench kullanarak sunucuya bağlanma

1. İstemci bilgisayarınızda MySQL Workbench uygulamasını açın. Uygulama yüklü değilse [indirin](https://dev.mysql.com/downloads/workbench/) ve yükleyin.

2. **Setup New Connection** (Yeni Bağlantı Oluştur) iletişim kutusundaki **Parameters** (Parametreler) sekmesine aşağıdaki bilgileri girin:

   ![Yeni bağlantı oluşturma](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

   | Ayar | Önerilen değer | Açıklama |
   |---|---|---|
   | Bağlantı Adı | **Tanıtım bağlantısı** | Bu bağlantı için bir etiket girin (bağlantı adı herhangi bir şey olabilir) |
   | Bağlantı Yöntemi | **Standart (TCP/IP)** | MariaDB için Azure Veritabanı'na bağlanmak için TCP/IP protokolünü kullanın |
   | Konak adı | **mydemoserver.mariadb.database.azure.com** | Daha önceden not aldığınız sunucu adı. |
   | Bağlantı noktası | **3306** | MariaDB için Azure Veritabanı'nın varsayılan bağlantı noktası. |
   | Kullanıcı adı | **myadmin \@ demosunucum** | Daha önceden not aldığınız sunucu yöneticisi oturum açma bilgileri. |
   | Parola | *Parolanız* | Önceden belirlediğiniz yönetici parolasını kullanın. |

3. Tüm parametrelerin doğru yapılandırılıp yapılandırılmadığını denetlemek için **Test Connection** (Bağlantıyı Sına) öğesini seçin.

4. Sunucuya başarıyla bağlanmak için bağlantıya tıklayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta kullandığınız kaynaklara başka bir hızlı başlangıç veya öğretici için gereksinim duymuyorsanız aşağıdaki komutu çalıştırarak kaynakları silebilirsiniz: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Yalnızca bu hızlı başlangıçta oluşturduğunuz sunucuyu silmek istiyorsanız [az mariadb server delete](/cli/azure/mariadb/server#az_mariadb_server_delete) komutunu çalıştırın:

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure CLı ile bir MariaDB veritabanı tasarlama](tutorial-design-database-cli.md)
