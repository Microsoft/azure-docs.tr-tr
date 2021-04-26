---
title: Veritabanı bozulmasını çözümle-MySQL için Azure veritabanı
description: Bu makalede, MySQL için Azure veritabanı 'nda veritabanı bozulması sorunlarını nasıl giderebileceğinizi öğreneceksiniz.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91766885"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>MySQL için Azure Veritabanı'nda bozulma sorunlarını giderme
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Veritabanı bozulması, uygulamanız için kapalı kalma süresine neden olabilir. Veri kaybını önlemek için zaman içinde bozulma sorunlarını çözmek de önemlidir. Veritabanı bozulması oluştuğunda, bu hatayı sunucu günlüklerinizi görürsünüz: `InnoDB: Database page corruption on disk or a failed.`

Bu makalede, veritabanı veya tablo bozulması sorunlarını çözmeyi öğreneceksiniz. MySQL için Azure veritabanı InnoDB altyapısını kullanır. Otomatikleştirilmiş bozulma denetimi ve onarım işlemlerine sahiptir. InnoDB, okuduğu her sayfada sağlama toplamlarını çalıştırarak bozuk sayfaları denetler. Bir sağlama toplamı tutarsızlığı bulursa, MySQL sunucusunu otomatik olarak durdurur.

Veritabanınızın bozulma sorunlarını hızla azaltmak için aşağıdaki seçenekleri deneyin.

## <a name="restart-your-mysql-server"></a>MySQL sunucunuzu yeniden başlatın

Uygulamanız tabloya veya veritabanına eriştiğinde, genellikle bir veritabanı veya tablo bozuk olduğunu fark edersiniz. InnoDB, sunucu yeniden başlatıldığında çoğu sorunu giderebilecek bir kilitlenme kurtarma mekanizması sunar. Bu nedenle sunucunun yeniden başlatılması, veritabanının hatalı durumda kalmasına neden olan bir kilitlenmeden kurtarılmasına yardımcı olabilir.

## <a name="use-the-dump-and-restore-method"></a>Dump ve restore metodunu kullanın

Bir *döküm ve geri yükleme* yöntemi kullanarak bozulma sorunlarını çözmeniz önerilir. Bu yöntem şunları içerir:
1. Bozuk tabloya erişiliyor.
1. Tablonun mantıksal bir yedeğini oluşturmak için mysqldump yardımcı programını kullanma. Yedekleme, tablo yapısını ve içindeki verileri korur.
1. Tabloyu veritabanına yeniden yükleme.

### <a name="back-up-your-database-or-tables"></a>Veritabanınızı veya tablolarınızı yedekleme

> [!Important]
> - İstemci makinenizden sunucuya erişmek için bir güvenlik duvarı kuralı yapılandırdığınızdan emin olun. Daha fazla bilgi için bkz. [tek sunucuda bir güvenlik duvarı kuralı yapılandırma](howto-manage-firewall-using-portal.md) ve [Esnek sunucuda bir güvenlik duvarı kuralı yapılandırma](flexible-server/how-to-connect-tls-ssl.md).
> - `--ssl-cert`SSL etkinse, mysqldump IÇIN SSL seçeneğini kullanın.

Mysqldump kullanarak komut satırından bir yedekleme dosyası oluşturun. Şu komutu çalıştırın:

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parametre açıklamaları:
- `[ssl-cert=/path/to/pem]`: SSL sertifikasının yolu. SSL sertifikasını istemci makinenize indirin ve komutta içindeki yolu ayarlayın. SSL devre dışıysa bu parametreyi kullanmayın.
- `[host]`: MySQL için Azure veritabanı sunucusu.
- `[uname]`: Sunucu Yöneticisi Kullanıcı adınız.
- `[pass]`: Yönetici kullanıcı için parola.
- `[dbname]`: Veritabanınızın adı.
- `[backupfile.sql]`: Veritabanı yedeklemenizin dosya adı.

> [!Important]
> - Tek sunucu için `admin-user@servername` aşağıdaki komutlarda değiştirmek üzere biçimini kullanın `myserveradmin` .
> - Esnek sunucu için `admin-user` aşağıdaki komutlarda değiştirmek üzere biçimini kullanın `myserveradmin` .

Belirli bir tablo bozuksa, veritabanınıza yedeklemek için belirli tablolar ' ı seçin:
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Bir veya daha fazla veritabanını yedeklemek için, anahtarı kullanın `--database` ve boşluklarla ayırarak veritabanı adlarını listeleyin:

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>Veritabanınızı veya tablolarınızı geri yükleme

Aşağıdaki adımlarda veritabanınızın veya tablolarınızın nasıl geri yükleneceği gösterilmektedir. Yedekleme dosyasını oluşturduktan sonra, MySQL yardımcı programını kullanarak tabloları veya veritabanlarını geri yükleyebilirsiniz. Şu komutu çalıştırın:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Aşağıda, `testdb` mysqldump ile oluşturulmuş bir yedekleme dosyasından geri yüklenen bir örnek verilmiştir: 

> [!Important]
> - Tek sunucu için, `admin-user@servername` aşağıdaki komutta değiştirmek üzere biçimini kullanın `myserveradmin` .
> - Esnek sunucu için ```admin-user``` aşağıdaki komutla değiştirmek üzere biçimini kullanın `myserveradmin` . 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Sonraki adımlar
Yukarıdaki adımlar sorunu çözmezse, her zaman sunucunun tamamını geri yükleyebilirsiniz:
- [MySQL için Azure veritabanı 'nda sunucuyu geri yükleme-tek sunucu](howto-restore-server-portal.md)
- [MySQL için Azure veritabanı 'nda sunucuyu geri yükleme-esnek sunucu](flexible-server/how-to-restore-server-portal.md)



