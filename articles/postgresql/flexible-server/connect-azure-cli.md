---
title: 'Hızlı başlangıç: Azure CLı kullanarak bağlanma-PostgreSQL için Azure veritabanı-esnek sunucu'
description: Bu hızlı başlangıç, Azure CLı ile PostgreSQL için Azure veritabanı-esnek sunucu arasında bağlantı kurmak için çeşitli yollar sağlar.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: 7526644e02b0ed4d0522ad00a27b691ece98754a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479245"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Hızlı başlangıç: Azure CLı ile PostgreSQL için Azure veritabanı-esnek sunucu ile bağlanma ve sorgulama

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bu hızlı başlangıçta, Azure CLı komutunu kullanarak PostgreSQL için Azure veritabanı esnek sunucusuna nasıl bağlanacağı gösterilmektedir ```az postgres flexible-server connect``` . Bu komut, veritabanı sunucunuza olan bağlantıyı test etmenizi ve sorguları çalıştırmanızı sağlar. Etkileşimli modu kullanarak birden çok sorgu da çalıştırabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar
- Azure hesabı. Hiç kimse yoksa [ücretsiz deneme sürümü alın](https://azure.microsoft.com/free/).
- [Azure CLI](/cli/azure/install-azure-cli) 'nın en son sürümünü (2.20.0 veya üzeri) yükler
- Azure CLı kullanarak komut ile oturum açma ```az login``` 
- Parametresiyle parametre kalıcılığını açın ```az config param-persist on``` . Parametre kalıcılığı, kaynak grubu veya konum gibi çok sayıda bağımsız değişkeni tekrarlamanız gerekmeden yerel bağlam kullanmanıza yardımcı olur.

## <a name="create-an-postgresql-flexible-server"></a>PostgreSQL esnek sunucusu oluşturma

Oluşturacağız ilk şey, yönetilen bir PostgreSQL sunucusudur. [Azure Cloud Shell](https://shell.azure.com/), aşağıdaki betiği çalıştırın ve bu komuttan oluşturulan **sunucu adı**, **Kullanıcı adı** ve **parolayı** bir yere unutmayın.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
Bu komut için özelleştirmek üzere ek bağımsız değişkenler sağlayabilirsiniz. [Az Postgres esnek-sunucu oluşturma](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create)için tüm bağımsız değişkenlere bakın.

## <a name="view-all-the-arguments"></a>Tüm bağımsız değişkenleri görüntüle
Bu komutun tüm bağımsız değişkenlerini ```--help``` bağımsız değişkenle görüntüleyebilirsiniz. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Veritabanı sunucusu bağlantısını test et
Komutunu kullanarak geliştirme ortamınızdan veritabanına bağlantıyı test edebilir ve doğrulayabilirsiniz.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Örnek:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Bağlantı başarılı olursa çıktıyı görürsünüz.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Bağlantı başarısız olursa, şu çözümleri deneyin:
- İstemci makinenizde 5432 bağlantı noktasının açık olup olmadığını denetleyin.
- sunucu yöneticinizin Kullanıcı adı ve parolası doğruysa
- istemci makineniz için güvenlik duvarı kuralı yapılandırdıysanız
- sunucunuzu sanal ağ 'da özel erişim ile yapılandırdıysanız, istemci makinenizin aynı sanal ağda bulunduğundan emin olun.

## <a name="run-single-query"></a>Tek sorgu Çalıştır
Bağımsız değişkenini kullanarak komutuyla tek bir sorgu çalıştırabilirsiniz ```--querytext``` ```-q``` .

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Örnek:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

Aşağıda gösterildiği gibi bir çıkış göreceksiniz:

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
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
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Örnek:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

**Psql** kabuğu deneyimini aşağıda gösterildiği gibi göreceksiniz:

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Sonraki Adımlar

> [!div class="nextstepaction"]
> [Sunucuyu yönetme](./how-to-manage-server-cli.md)
