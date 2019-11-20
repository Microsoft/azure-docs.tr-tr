---
title: PostgreSQL için Azure veritabanı 'nda Içeri ve dışarı aktarma kullanarak bir veritabanını geçirme-tek sunucu
description: Bir PostgreSQL veritabanının bir betik dosyasına nasıl ayıklanacağını ve verilerin hedef veritabanına bu dosyadan nasıl aktarılacağını açıklar.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 0803f56312ca9b650987c2203c4271cff21df9f8
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260356"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Dışarı aktarma ve içeri aktarma kullanarak PostgreSQL veritabanınızı geçirme
Bir PostgreSQL veritabanını bir betik dosyasına ve [psql](https://www.postgresql.org/docs/current/static/app-psql.html) ' i bu dosyadaki hedef veritabanına aktarmak için [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:
- Üzerinde erişime ve veritabanına izin vermek için güvenlik duvarı kuralları içeren bir [PostgreSQL sunucusu Için Azure veritabanı](quickstart-create-server-database-portal.md) .
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) komut satırı yardımcı programı yüklendi
- [psql](https://www.postgresql.org/docs/current/static/app-psql.html) komut satırı yardımcı programı yüklendi

PostgreSQL veritabanınızı dışa ve içe aktarmak için aşağıdaki adımları izleyin.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Yüklenecek verileri içeren pg_dump kullanarak bir betik dosyası oluşturma
Mevcut PostgreSQL veritabanınızı şirket içinde veya bir VM 'de bir SQL betik dosyasına aktarmak için, mevcut ortamınızda aşağıdaki komutu çalıştırın:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Örneğin, yerel bir sunucunuz ve içinde **TestDB** adlı bir veritabanınız varsa:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Hedef PostgreSQL için Azure veritabanı 'ndaki verileri içeri aktarın
PostgreSQL için Azure veritabanı sunucusuna verileri içeri aktarmak ve SQL dosyasından verileri yüklemek için psql komut satırını ve--dbname parametresini (-d) kullanabilirsiniz.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Bu örnek, **mydemoserver.Postgres.Database.Azure.com**hedef sunucuda **mypgsqldb** veritabanına veri aktarmak için psql yardımcı programını ve önceki adımdan **TestDB. SQL** adlı bir betik dosyasını kullanır.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Sonraki adımlar
- Döküm ve geri yükleme kullanarak bir PostgreSQL veritabanını geçirmek için bkz. [döküm ve geri yükleme kullanarak PostgreSQL veritabanınızı geçirme](howto-migrate-using-dump-and-restore.md).
- Veritabanını PostgreSQL için Azure veritabanı 'na geçirme hakkında daha fazla bilgi için bkz. [veritabanı geçiş kılavuzu](https://aka.ms/datamigration). 
