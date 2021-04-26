---
title: PostgreSQL uzantılarını kullanma
description: PostgreSQL uzantılarını kullanma
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e247e372237572586e5a4647d24d9ed6067ea823
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104949796"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc etkin PostgreSQL hiper ölçek sunucu grubunda PostgreSQL uzantılarını kullanma

PostgreSQL, uzantıları ile kullandığınızda en iyi seçenektir. Aslında, kendi Hyperscale işlevsellikten oluşan bir anahtar öğesi, `citus` Varsayılan olarak yüklenen Microsoft tarafından sunulan uzantıdır. Bu, Postgres 'in verileri birden çok düğüm arasında saydam bir şekilde parçalamayı sağlar.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>Desteklenen uzantılar
Standart [`contrib`](https://www.postgresql.org/docs/12/contrib.html) Uzantılar ve aşağıdaki uzantılar, Azure Arc etkin PostgreSQL hiper ölçek sunucu grubunuzun kapsayıcılarında zaten dağıtılır:
- [`citus`](https://github.com/citusdata/citus), v: 9,4. [Citus verileri](https://www.citusdata.com/) tarafından Citus uzantısı, PostgreSQL altyapısına hiper ölçekli özelliği getirdikçe varsayılan olarak yüklenir. Citus uzantısını Azure Arc PostgreSQL hiper ölçek sunucu grubundan bırakma desteklenmiyor.
- [`pg_cron`](https://github.com/citusdata/pg_cron), v: 1,2
- [`pgaudit`](https://www.pgaudit.org/), v: 1,4
- plpgsql, v: 1,0
- [`postgis`](https://postgis.net), v: 3.0.2
- [`plv8`](https://plv8.github.io/), v: 2.3.14

Bu listedeki güncelleştirmeler zamanla geliştikçe gönderilir.

> [!IMPORTANT]
> Sunucu grubunuza yukarıda listelenenlerin dışında bir uzantı getirebiliriz, bu önizlemede sisteminizde kalıcı olmayacaktır. Bu, sistemin yeniden başlatıldıktan sonra kullanılabilir olmayacağı anlamına gelir ve bunu tekrar almanız gerekir.

Bu kılavuz, bu uzantıların ikisini kullanmak için bir senaryo alır:
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>Hangi uzantıların shared_preload_libraries eklenmesi ve oluşturulması gerekir?

|Uzantıları   |Shared_preload_libraries için eklenmesi gerekir  |Oluşturulması gerekir |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |Hayır       |Yes        |
|`pg_audit`     |Yes       |Yes        |
|`plpgsql`      |Yes       |Yes        |
|`postgis`      |Hayır       |Yes        |
|`plv8`      |Hayır       |Yes        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>Shared_preload_libraries uzantıları ekleme
Bunun hakkındaki ayrıntılar için shared_preload_libraries lütfen PostgreSQL [belgelerini okuyun](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES):
- Bu adım, bir parçası olan uzantılar için gerekli değildir `contrib`
- shared_preload_libraries tarafından önceden yüklenmesi gerekmeyen uzantılar için bu adım gerekli değildir. Bu uzantılar için sonraki sonraki paragrafa [Uzantı oluşturma](#create-extensions)' ya geçebilirsiniz.

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>Sunucu grubunun oluşturulma zamanına uzantı ekleme
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>Zaten var olan bir örneğe uzantı ekleme
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>Shared_preload_libraries eklenen uzantıların listesini göster
Aşağıdaki komuttan birini çalıştırın.

### <a name="with-an-azdata-cli-command"></a>Azdata CLı komutuyla
```console
azdata arc postgres server show -n <server group name>
```
Çıktıyı kaydırın ve sunucu grubunuzun belirtimlerindeki engine\extensions bölümlerine dikkat edin. Örnek:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
### <a name="with-kubectl"></a>Kubectl ile
```console
kubectl describe postgresql-12s/postgres02
```
Çıktıyı kaydırın ve sunucu grubunuzun belirtimlerindeki engine\extensions bölümlerine dikkat edin. Örnek:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


## <a name="create-extensions"></a>Uzantı Oluştur
İstediğiniz istemci aracıyla sunucu grubunuza bağlanın ve standart PostgreSQL sorgusunu çalıştırın:
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>Oluşturulan uzantıların listesini göster
İstediğiniz istemci aracıyla sunucu grubunuza bağlanın ve standart PostgreSQL sorgusunu çalıştırın:
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>Uzantı bırakma
İstediğiniz istemci aracıyla sunucu grubunuza bağlanın ve standart PostgreSQL sorgusunu çalıştırın:
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>`PostGIS`Uzantı
`PostGIS`Uzantısını öğesine eklemeniz gerekmez `shared_preload_libraries` .
Planlama & planlamadaki şehir departmanından [örnek verileri](http://duspviz.mit.edu/tutorials/intro-postgis/) alın. `apt-get install unzip`Gerektiğinde unzip 'i yüklemek için çalıştırın.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Veritabanımıza bağlanalım ve uzantıyı oluşturalım `PostGIS` :

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Paketteki uzantılardan birini (örneğin,,,...) kullanmak isterseniz, `postgis` `postgis_raster` `postgis_topology` `postgis_sfcgal` `fuzzystrmatch` önce PostGIS uzantısını oluşturmanız ve ardından diğer uzantıyı oluşturmanız gerekir. Örneğin: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

Ve şemayı oluşturun:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

Şimdi, `PostGIS` coffee_shops tablosunu dağıtılmış hale getirerek ölçek genişletme işleviyle birleştirebiliriz:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Bazı verileri yükleyelim:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

Ve `geom` veri türünde doğru kodlanmış Enlem ve Boylam ile alanı doldurabilirsiniz `PostGIS` `geometry` :

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Şimdi, MıT 'e en yakın kafeterleri listelebiliriz (77 Massachusetts Ave, 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>`pg_cron`Uzantı

Şimdi `pg_cron` PostgreSQL sunucu grubumuzu shared_preload_libraries ekleyerek etkinleştirelim:

```console
azdata postgres server update -n pg2 -ns arc --extensions pg_cron
```

Sunucu grubunuz, uzantıların yüklenmesinin tamamlandığını yeniden başlatacak. Bu işlem 2 ila 3 dakika sürebilir.

Şimdi yeniden bağlanabiliyoruz ve uzantıyı oluşturuyoruz `pg_cron` :

```sql
CREATE EXTENSION pg_cron;
```

Test amacıyla, `the_best_coffee_shop` önceki tablomızdan rastgele bir ad alan bir tablo oluşturma `coffee_shops` ve tablo içeriğini ekleme olanağı sağlar:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

`cron.schedule`Rastgele bir tablo adı almak için, daha az SAYıDA SQL deyimi kullanabilir (dağıtılmış bir sorgu sonucunu depolamak için geçici bir tablo kullanımına dikkat edin) ve şu şekilde saklayın `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

Şimdi de bir dakika sonra farklı bir ad alacaksınız:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Sözdizimi hakkında tam Ayrıntılar için [pg_cron Benioku dosyasına](https://github.com/citusdata/pg_cron) bakın.


## <a name="next-steps"></a>Sonraki adımlar
- Belgeleri okuyun [`plv8`](https://plv8.github.io/)
- Belgeleri okuyun [`PostGIS`](https://postgis.net/)
- Belgeleri okuyun [`pg_cron`](https://github.com/citusdata/pg_cron)