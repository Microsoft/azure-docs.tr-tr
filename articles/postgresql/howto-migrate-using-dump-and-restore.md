---
title: Dökümünü al ve geri yükle-PostgreSQL için Azure veritabanı-tek sunucu
description: Bir PostgreSQL veritabanını bir döküm dosyasına ayıklayabilirsiniz. Daha sonra, PostgreSQL için Azure veritabanı tek sunucu 'da pg_dump tarafından oluşturulan bir dosyadan geri yükleme yapabilirsiniz.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 809ff06fe460a06a689d7bbc11cdbd5ee247f585
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450064"
---
# <a name="migrate-your-postgresql-database-by-using-dump-and-restore"></a>Dökümünü ve geri yüklemeyi kullanarak PostgreSQL veritabanınızı geçirme
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

Bir PostgreSQL veritabanını bir döküm dosyasına ayıklamak için [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) kullanabilirsiniz. Sonra, tarafından oluşturulan arşiv dosyasından PostgreSQL veritabanını geri yüklemek için [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) kullanın `pg_dump` .

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:
- Erişim izni vermek için güvenlik duvarı kuralları da dahil olmak üzere [PostgreSQL Için Azure veritabanı sunucusu](quickstart-create-server-database-portal.md).
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) ve [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) komut satırı yardımcı programları yüklendi.

## <a name="create-a-dump-file-that-contains-the-data-to-be-loaded"></a>Yüklenecek verileri içeren bir döküm dosyası oluşturun

Mevcut bir PostgreSQL veritabanını şirket içinde veya bir VM 'de yedeklemek için şu komutu çalıştırın:

```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Örneğin, yerel bir sunucunuz ve **TestDB** adlı bir veritabanınız varsa şunu çalıştırın:

```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```

## <a name="restore-the-data-into-the-target-database"></a>Verileri hedef veritabanına geri yükleme

Hedef veritabanını oluşturduktan sonra, `pg_restore`  `--dbname` verileri döküm dosyasından hedef veritabanına geri yüklemek için komutunu ve parametresini kullanabilirsiniz.

```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

Parametresi dahil, `--no-owner` geri yükleme sırasında oluşturulan tüm nesnelerin, ile belirtilen kullanıcıya ait olmasını sağlar `--username` . Daha fazla bilgi için bkz. [PostgreSQL belgeleri](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> PostgreSQL sunucuları için Azure veritabanı 'nda, TLS/SSL bağlantıları varsayılan olarak açık. PostgreSQL sunucunuz TLS/SSL bağlantıları gerektiriyorsa, ancak yoksa, `PGSSLMODE=require` pg_restore ARACıNıN TLS ile bağlanacağı bir ortam değişkeni ayarlayın. TLS olmadan hata şu şekilde okunabilir: "ÖNEMLI: SSL bağlantısı gereklidir. Lütfen SSL seçeneklerini belirtin ve yeniden deneyin. " Windows komut satırında komutunu `SET PGSSLMODE=require` çalıştırmadan önce komutunu çalıştırın `pg_restore` . Linux veya bash içinde `export PGSSLMODE=require` komutunu çalıştırmadan önce komutunu çalıştırın `pg_restore` . 
>

Bu örnekte, **TestDB. dump** döküm dosyasındaki verileri hedef sunucuda **mydemoserver.Postgres.Database.Azure.com** adlı **mypgsqldb** veritabanına geri yükleyin.

Bunun tek bir sunucu için nasıl kullanılacağına ilişkin bir örnek aşağıda verilmiştir `pg_restore` :

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

Bunu esnek sunucu için nasıl kullanacağınızı gösteren bir örnek aşağıda verilmiştir `pg_restore` :

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```

## <a name="optimize-the-migration-process"></a>Geçiş işlemini iyileştirin

Mevcut PostgreSQL veritabanınızı PostgreSQL için Azure veritabanı 'na geçirmenin bir yolu, kaynağı kaynak üzerinde yedeklemeli ve Azure 'da geri yüklemektir. Geçişi gerçekleştirmek için gereken süreyi en aza indirmek için yedekleme ve geri yükleme komutlarıyla aşağıdaki parametreleri kullanmayı göz önünde bulundurun.

> [!NOTE]
> Ayrıntılı sözdizimi bilgileri için bkz. [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) ve [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Yedekleme için

`-Fc`Geri yüklemeyi hızlandırmak üzere paralel olarak gerçekleştirebilmek için anahtarı anahtarla birlikte gerçekleştirin. Örnek:

```bash
pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
```

### <a name="for-the-restore"></a>Geri yükleme için

- Yedekleme dosyasını, geçiş yaptığınız PostgreSQL için Azure veritabanı sunucusu ile aynı bölgedeki bir Azure VM 'sine taşıyın. `pg_restore`Ağ gecikmesini azaltmak için bu VM 'den gerçekleştirin. [Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-powershell.md) etkinken VM 'yi oluşturun.

- CREATE INDEX deyimlerinin veri ekleme deyimlerinden sonra olduğunu doğrulamak için döküm dosyasını açın. Böyle değilse, veri eklendikten sonra CREATE INDEX deyimlerini taşıyın. Bu, varsayılan olarak zaten yapılmalıdır, ancak doğrulamak iyi bir fikirdir.

- `-Fc` `-j` Geri yüklemeyi paralel hale getirmek için anahtarlarla ve (bir sayıyla) geri yükleyin. Belirttiğiniz sayı, hedef sunucudaki çekirdekler sayısıdır. Etkiyi görmek için hedef sunucunun çekirdek sayısını iki katına da ayarlayabilirsiniz.

    Bunun tek bir sunucu için nasıl kullanılacağına ilişkin bir örnek aşağıda verilmiştir `pg_restore` :

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

    Bunu esnek sunucu için nasıl kullanacağınızı gösteren bir örnek aşağıda verilmiştir `pg_restore` :

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

- Ayrıca, yukarıdaki komutu ve sonundaki komutu ekleyerek döküm dosyasını düzenleyebilirsiniz `set synchronous_commit = off;` `set synchronous_commit = on;` . Son olarak, uygulamalar verileri değiştirmeden önce verilerin daha sonra kaybedilmesine neden olabilir.

- PostgreSQL için Azure veritabanı sunucusunda, geri yüklemeden önce aşağıdakileri yapmayı deneyin:
    
  - Sorgu performansını izlemeyi devre dışı bırakın. Bu istatistikler geçiş sırasında gerekli değildir. Bunu, ve ' i ayarlayarak `pg_stat_statements.track` yapabilirsiniz `pg_qs.query_capture_mode` `pgms_wait_sampling.query_capture_mode` `NONE` .

  - Geçişi hızlandırmak için 32 sanal çekirdek bellek için Iyileştirilmiş gibi yüksek bir işlem ve yüksek bellek SKU 'SU kullanın. Geri yükleme tamamlandıktan sonra tercih ettiğiniz SKU 'nuzu kolayca azaltabilirsiniz. SKU 'nun daha yüksek olması, komutta karşılık gelen parametreyi artırarak elde edebilirsiniz `-j` `pg_restore` .

  - Hedef sunucuda daha fazla ıOPS, geri yükleme performansını iyileştirebilirler. Sunucunun depolama boyutunu artırarak daha fazla ıOPS sağlayabilirsiniz. Bu ayar geri alınamaz, ancak daha yüksek bir ıOPS 'nin gelecekte gerçek iş yükünüzün avantajına sahip olup olmadığını düşünün.

Bu komutları üretimde kullanmadan önce test ortamında sınamayı ve doğrulamayı unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

- Dışarı ve içeri aktarma kullanarak bir PostgreSQL veritabanını geçirmek için, bkz. [dışarı aktarma ve içeri aktarma kullanarak PostgreSQL veritabanınızı geçirme](howto-migrate-using-export-and-import.md).
- Veritabanını PostgreSQL için Azure veritabanı 'na geçirme hakkında daha fazla bilgi için bkz. [veritabanı geçiş kılavuzu](https://aka.ms/datamigration).


