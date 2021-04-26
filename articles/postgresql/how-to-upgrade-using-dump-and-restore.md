---
title: Döküm ve geri yükleme kullanarak yükseltme-PostgreSQL için Azure veritabanı-tek sunucu
description: Daha yüksek bir PostgreSQL için Azure veritabanı-tek sunucu sürümüne geçiş yapmak için döküm ve geri yükleme veritabanlarını kullanan çevrimdışı yükseltme yöntemlerini açıklar.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 42bbe1c9f4056ae0dae0ccd59b452db90a7c63c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96493670"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Döküm ve geri yükleme kullanarak PostgreSQL veritabanınızı yükseltme

Aşağıdaki yöntemleri kullanarak veritabanlarınızı daha yüksek bir ana sürüm sunucusuna geçirerek PostgreSQL için Azure veritabanı 'nda dağıtılan PostgreSQL sunucunuzu yükseltebilirsiniz.
* PostgreSQL kullanan **çevrimdışı** Yöntem [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) ve [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) verileri geçirmek için kapalı kalma süresi. Bu belge, bu yükseltme/geçiş yöntemini ele alınmaktadır.
* [Veritabanı geçiş hizmeti](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) (DMS) kullanan **çevrimiçi** yöntem. Bu yöntem, daha düşük kapalı kalma süresi geçişini sağlar ve hedef veritabanını kaynakla eşitlenmiş halde tutar ve ne zaman kesilmesini istediğinizi seçebilirsiniz. Ancak, DMS kullanılarak değinilmesi gereken birkaç önkoşul ve kısıtlama vardır. Ayrıntılar için [DMS belgelerine](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)bakın. 

 Aşağıdaki tabloda, veritabanı boyutlarına ve senaryolarına göre bazı öneriler verilmektedir.

| **Veritabanı/senaryo** | **Döküm/geri yükleme (çevrimdışı)** | **DMS (çevrimiçi)** |
| ------ | :------: | :-----: |
| Küçük bir veritabanınız var ve yükseltmek için kapalı kalma süresine sahip olabilirsiniz  | X | |
| Küçük veritabanları (< 10 GB)  | X | X | 
| Küçük orta ölçekli DBs (10 GB – 100 GB) | X | X |
| Büyük veritabanları (> 100 GB) |  | X |
| Yükseltme için kapalı kalma süresi uygulanabilir (veritabanı boyutundan bağımsız olarak) | X |  |
| Yeniden başlatma dahil, DMS [ön gereksinimleri](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites)ele alabilir mi? |  | X |
| Yükseltme işlemi sırasında DDLs ve günlüğe kaydedilmeyeceğini kaldırmak olabilir mi? | |  X |

Bu kılavuzda, kaynak sunucudan PostgreSQL 'in daha yüksek bir sürümünü çalıştıran hedef sunucuya nasıl geçirebileceğiniz gösterilmektedir.

> [!NOTE]
> PostgreSQL için döküm ve geri yükleme birçok şekilde gerçekleştirilebilir. Bu kılavuzda belirtilen yöntemlerden birini kullanarak geçirmeyi seçebilirsiniz veya gereksinimlerinize uyacak alternatif yollar seçebilirsiniz. Ek parametrelerle ayrıntılı döküm ve geri yükleme söz dizimi için, [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) makalelere bakın ve [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı ile dökümünü ve geri yüklemeyi kullanma önkoşulları
 
Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:

- Yükseltmek istediğiniz 9,5, 9,6 veya 10 çalıştıran bir **kaynak** PostgreSQL veritabanı
- İstenen ana sürümü [PostgreSQL Için Azure veritabanı sunucusu](quickstart-create-server-database-portal.md)olan bir **hedef** PostgreSQL veritabanı sunucusu. 
- Döküm ve geri yükleme komutlarını çalıştırmak için bir PostgreSQL istemci sistemi.
  - Bu, PostgreSQL yüklü bir Linux veya Windows istemcisi olabilir ve [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) ve [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) komut satırı yardımcı programları yüklü olabilir. 
  - Alternatif olarak, [Azure Cloud Shell](https://shell.azure.com) kullanabilir veya [Azure Portal](https://portal.azure.com)sağ üst köşesindeki menü çubuğunda Azure Cloud Shell tıklayabilirsiniz. `az login`Döküm ve geri yükleme komutlarını çalıştırmadan önce hesabınızda oturum açmanız gerekir.
- PostgreSQL istemciniz, tercihen kaynak ve hedef sunucularla aynı bölgede çalışmaktadır. 


## <a name="additional-details-and-considerations"></a>Ek ayrıntılar ve önemli noktalar
- Portalda "bağlantı dizeleri" ne tıklayarak bağlantı dizesini kaynak ve hedef veritabanlarına bulabilirsiniz. 
- Sunucunuzda birden fazla veritabanı çalıştırıyor olabilirsiniz. Kaynak sunucunuza bağlanarak ve çalıştıran veritabanlarının listesini bulabilirsiniz `\l` .
- Hedef veritabanı sunucusunda karşılık gelen veritabanları oluşturun.
- Yükseltme `azure_maintenance` veya şablon veritabanlarını atlayabilirsiniz.
- Veritabanının bu geçiş moduna uygun olduğunu belirtmek için yukarıdaki tablolara bakın.
- Azure Cloud Shell kullanmak istiyorsanız, oturumun 20 dakika sonra zaman aşımına uğraydığına lütfen emin olun. Veritabanınızın boyutu 10 GB <, oturum zaman aşımına uğramadan yükseltmeyi tamamlayabilirsiniz. Aksi takdirde, <Enter> 10-15 dakika içinde tuşu bir kez basmak gibi, oturumu başka yollarla açık tutmanız gerekebilir. 


## <a name="example-database-used-in-this-guide"></a>Bu kılavuzda kullanılan örnek veritabanı

Bu kılavuzda, örnekleri göstermek için aşağıdaki kaynak ve hedef sunucular ve veritabanı adları kullanılır.

 | **Açıklama** | **Değer** |
 | ------- | ------- |
 | Kaynak sunucu (v 9.5) | pg-95.postgres.database.azure.com |
 | Kaynak veritabanı | bench5gb |
 | Kaynak veritabanı boyutu | 5 GB |
 | Kaynak Kullanıcı adı | pg@pg-95 |
 | Hedef sunucu (v11) | pg-11.postgres.database.azure.com |
 | Hedef veritabanı | bench5gb |
 | Hedef Kullanıcı adı | pg@pg-11 |

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>Çevrimdışı geçiş yöntemlerini kullanarak veritabanlarınızı yükseltin
Yükseltmeleriniz için bu bölümde açıklanan yöntemlerden birini kullanmayı tercih edebilirsiniz. Görevleri gerçekleştirirken aşağıdaki ipuçlarını kullanabilirsiniz.

- Kaynak ve hedef veritabanı için aynı parolayı kullanıyorsanız, `PGPASSWORD=yourPassword` ortam değişkenini ayarlayabilirsiniz.  Ardından psql, pg_dump ve pg_restore gibi komutları her çalıştırdığınızda parola sağlamanız gerekmez.  Benzer şekilde, vb. gibi ek değişkenler de ayarlayabilirsiniz. `PGUSER` `PGSSLMODE` [PostgreSQL ortam değişkenlerine](https://www.postgresql.org/docs/11/libpq-envars.html)bakın.
  
- PostgreSQL sunucunuz TLS/SSL bağlantıları gerektiriyorsa (PostgreSQL için Azure veritabanı sunucuları için varsayılan olarak açık), `PGSSLMODE=require` pg_restore ARACıNıN TLS ile bağlanacağı bir ortam değişkeni ayarlayın. TLS olmadan hata okunmayabilir  `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- Windows komut satırında, `SET PGSSLMODE=require` pg_restore komutunu çalıştırmadan önce komutunu çalıştırın. Linux veya bash içinde `export PGSSLMODE=require` pg_restore komutunu çalıştırmadan önce komutunu çalıştırın.

### <a name="method-1-migrate-using-dump-file"></a>Yöntem 1: döküm dosyası kullanarak geçirme

Bu yöntem iki adımdan oluşur. İlk olarak kaynak sunucudan bir döküm oluşturmaktır. İkinci adım, döküm dosyasını hedef sunucuya geri yüklemektir. Daha fazla ayrıntı için lütfen bkz. [döküm ve geri yükleme belgelerini kullanarak geçirme](howto-migrate-using-dump-and-restore.md) . Bu, büyük veritabanlarınız varsa ve istemci sisteminiz döküm dosyasını depolamak için yeterli depolama alanı içeriyorsa önerilen yöntemdir.

### <a name="method-2-migrate-using-streaming-the-dump-data-to-the-target-database"></a>Yöntem 2: döküm verilerini hedef veritabanına akışı kullanarak geçirme

Bir PostgreSQL istemciniz yoksa veya Azure Cloud Shell kullanmak istiyorsanız, bu yöntemi kullanabilirsiniz. Veritabanı dökümü doğrudan hedef veritabanı sunucusuna akışla kaydedilir ve dökümünü istemciye depolamaz. Bu nedenle, bu, sınırlı depolamaya sahip bir istemciyle birlikte kullanılabilir ve hatta Azure Cloud Shell çalıştırılabilir. 

1. Veritabanının hedef sunucuda, komutunu kullanarak bulunduğundan emin olun `\l` . Veritabanı yoksa, veritabanını oluşturun.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Dökümünü ve geri yüklemeyi, kanal kullanarak tek bir komut satırı olarak çalıştırın. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Örneğin,

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Yükseltme (geçiş) işlemi tamamlandıktan sonra, uygulamanızı hedef sunucuyla test edebilirsiniz. 
4. Sunucu içindeki tüm veritabanları için bu işlemi tekrarlayın.

 Örnek olarak, aşağıdaki tabloda akış dökümü yöntemi kullanılarak geçiş yapmak için geçen süre gösterilmektedir. Örnek veriler [pgbench](https://www.postgresql.org/docs/10/pgbench.html)kullanılarak doldurulur. Veritabanınız, pgbench tarafından üretilen tablo ve dizinlerden değişen boyutlarda farklı sayıda nesneye sahip olduğundan, veritabanınızı yükseltmek için gereken gerçek süreyi anlamak üzere veritabanınızın dökümünü ve geri yüklemesini test etmek önemle önerilir. 

| **Veritabanı boyutu** | **Yaklaşık olarak geçen süre** | 
| ----- | ------ |
| 1 GB  | 1-2 dakika |
| 5 GB | 8-10 dakika |
| 10 GB | 15-20 dakika |
| 50 GB | 1-1.5 saat |
| 100 GB | 2,5-3 saat|
   
### <a name="method-3-migrate-using-parallel-dump-and-restore"></a>Yöntem 3: paralel döküm ve geri yükleme kullanarak geçirme 

Veritabanınızda daha büyük tablolar varsa ve bu veritabanı için döküm ve geri yükleme işlemini paralel hale getirmek istiyorsanız bu yöntemi düşünebilirsiniz. Ayrıca, istemci sisteminizde yedekleme dökümlerini barındırmak için yeterli depolama alanı gerekir. Bu paralel döküm ve geri yükleme işlemi, tüm geçişi tamamlamaya yönelik zaman tüketimini azaltır. Örneğin, geçiş için 1-1.5 saat geçen 50 GB pgbench veritabanı, Yöntem 1 ve 2 ' nin bu yöntem kullanılarak 30 dakikadan kısa sürme tamamlandı.

1. Kaynak sunucunuzdaki her bir veritabanı için hedef sunucuda karşılık gelen bir veritabanını oluşturun.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Örneğin,
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Pg_dump komutunu, iş sayısı = 4 (veritabanındaki tablo sayısı) ile bir dizin biçiminde çalıştırın. Daha büyük işlem katmanıyla ve daha fazla tablo ile, daha yüksek bir sayı ile artırabilirsiniz. Bu pg_dump, her iş için sıkıştırılmış dosyaları depolamak üzere bir dizin oluşturacak.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Örneğin,
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Ardından, hedef sunucuda yedeklemeyi geri yükleyin.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Örneğin,
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> Bu belgede bahsedilen işlem, önizleme aşamasında olan PostgreSQL için Azure veritabanınızı (esnek sunucu) yükseltmek için de kullanılabilir. Temel fark, esnek sunucu hedefinin bağlantı dizesidir `@dbName` .  Örneğin, Kullanıcı adı ise `pg` , bağlantı dizesindeki tek sunuculu Kullanıcı adı olacaktır `pg@pg-95` , esnek sunucu ile kullanmanız yeterlidir `pg` .

## <a name="next-steps"></a>Sonraki adımlar

- Hedef veritabanı işlevinden memnun kaldıktan sonra eski veritabanı sunucunuzu bırakabilirsiniz. 
- Kaynak sunucuyla aynı veritabanı uç noktasını kullanmak istiyorsanız eski kaynak veritabanı sunucunuzu sildikten sonra eski veritabanı sunucu adıyla bir okuma çoğaltması oluşturabilirsiniz. Kararlı durum kurulduktan sonra çoğaltmayı durdurabilirsiniz, bu da Çoğaltma sunucusunu bağımsız bir sunucu olacak şekilde yükseltir. Daha fazla ayrıntı için bkz. [çoğaltma](./concepts-read-replicas.md) .
- Bu komutları üretimde kullanmadan önce test ortamında sınamayı ve doğrulamayı unutmayın.