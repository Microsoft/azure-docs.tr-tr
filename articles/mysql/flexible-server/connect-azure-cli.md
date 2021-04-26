---
title: 'Hızlı başlangıç: Azure CLı kullanarak bağlanma-esnek sunucu için Azure veritabanı'
description: Bu hızlı başlangıçta, MySQL için Azure veritabanı-esnek sunucu ile Azure CLı ile bağlantı kurmak için çeşitli yollar sunulmaktadır.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: e0fd5969a3c4f84b6e8f98e99335bf120179e7af
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481098"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Hızlı başlangıç: MySQL için Azure veritabanı ile Azure CLı ile bağlanma ve sorgulama-esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bu hızlı başlangıçta, komutuyla Azure CLı kullanarak MySQL için Azure veritabanı esnek sunucusuna nasıl bağlanacağı gösterilmektedir ```az mysql flexible-server connect``` . Bu komut, veritabanı sunucunuza yönelik bağlantıyı test etmenizi ve sorguları doğrudan sunucunuza karşı çalıştırmanızı sağlar.  Ayrıca, birden çok sorgu çalıştırmak için komutu etkileşimli modda çalıştırabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure hesabı. Hiç kimse yoksa [ücretsiz deneme sürümü alın](https://azure.microsoft.com/free/).
- [Azure CLI](/cli/azure/install-azure-cli) 'nın en son sürümünü (2.20.0 veya üzeri) yükler
- Azure CLı kullanarak komut ile oturum açma ```az login``` 
- Parametresiyle parametre kalıcılığını açın ```az config param-persist on``` . Parametre kalıcılığı, kaynak grubu veya konum gibi çok sayıda bağımsız değişkeni tekrarlamanız gerekmeden yerel bağlam kullanmanıza yardımcı olur.

## <a name="create-an-mysql-flexible-server"></a>MySQL esnek sunucusu oluşturma

İlk olarak, yönetilen bir MySQL sunucusu oluşturacağız. [Azure Cloud Shell](https://shell.azure.com/), aşağıdaki betiği çalıştırın ve bu komuttan oluşturulan **sunucu adı**, **Kullanıcı adı** ve **parolayı** bir yere unutmayın.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

Bu komut için özelleştirmek üzere ek bağımsız değişkenler sağlayabilirsiniz. [Az MySQL esnek-sunucu oluşturma](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)için tüm bağımsız değişkenlere bakın.

## <a name="create-a-database"></a>Veritabanı oluşturma
Bir veritabanı oluşturmak için aşağıdaki komutu çalıştırın, henüz bir tane oluşturmadıysanız **NewDataBase** .

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Tüm bağımsız değişkenleri görüntüle
Bu komutun tüm bağımsız değişkenlerini ```--help``` bağımsız değişkenle görüntüleyebilirsiniz. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Veritabanı sunucusu bağlantısını test et
Geliştirme ortamınızdan veritabanına bağlantıyı sınamak ve doğrulamak için aşağıdaki betiği çalıştırın.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Örnek:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

Başarılı bağlantı için aşağıdaki çıktıyı görmeniz gerekir:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Bağlantı başarısız olursa, şu çözümleri deneyin:
- İstemci makinenizde 3306 bağlantı noktasının açık olup olmadığını denetleyin.
- sunucu yöneticinizin Kullanıcı adı ve parolası doğruysa
- istemci makineniz için güvenlik duvarı kuralı yapılandırdıysanız
- sunucunuzu sanal ağ 'da özel erişim ile yapılandırdıysanız, istemci makinenizin aynı sanal ağda bulunduğundan emin olun.

## <a name="run-single-query"></a>Tek sorgu Çalıştır
Bağımsız değişkenini kullanarak tek bir sorgu yürütmek için aşağıdaki komutu çalıştırın ```--querytext``` ```-q``` .

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Örnek:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

Aşağıda gösterildiği gibi bir çıkış göreceksiniz:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-multiple-queries-using-interactive-mode"></a>Etkileşimli mod kullanarak birden çok sorgu çalıştırma
**Etkileşimli** modu kullanarak birden çok sorgu çalıştırabilirsiniz. Etkileşimli modu etkinleştirmek için aşağıdaki komutu çalıştırın

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Örnek:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

**MySQL** Shell deneyimini aşağıda gösterildiği gibi göreceksiniz:

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Sonraki Adımlar

> [!div class="nextstepaction"]
* [Şifrelenmiş bağlantılarla, MySQL için Azure veritabanı 'na bağlanma-esnek sunucu](how-to-connect-tls-ssl.md)
* [Sunucuyu yönetme](./how-to-manage-server-cli.md)

