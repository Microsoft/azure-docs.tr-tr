---
title: Uzantılar – Hyperscale (Citus)-PostgreSQL için Azure veritabanı
description: PostgreSQL için Azure veritabanı-hiper ölçek (Citus) içindeki uzantıları kullanarak veritabanınızın işlevselliğini genişletme yeteneğini açıklar
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 221d8b1d9fdd40a71bcfdeed57c02451e44052f2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012771"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nda PostgreSQL uzantıları – hiper ölçek (Citus)

PostgreSQL, uzantıları kullanarak veritabanınızın işlevselliğini artırma imkanı sunar. Uzantılar, tek bir komutla veritabanınızdan yüklenebilecek veya kaldırılabileceği tek bir pakette birden fazla ilgili SQL nesnesini paketlemeye olanak tanır. Veritabanınıza yüklenen uzantılar, yerleşik özellikler gibi çalışabilir. PostgreSQL uzantıları hakkında daha fazla bilgi için bkz. [Package ile ilgili nesneler bir uzantıya](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>PostgreSQL uzantılarını kullanma

PostgreSQL uzantılarının kullanabilmeniz için veritabanınıza yüklenmesi gerekir. Belirli bir uzantıyı yüklemek için, paketlenmiş nesneleri veritabanınıza yüklemek üzere psql aracından [Uzantı Oluştur](https://www.postgresql.org/docs/current/static/sql-createextension.html) komutunu çalıştırın.

PostgreSQL için Azure veritabanı-hiper ölçek (Citus) Şu anda burada listelenen bir anahtar uzantıları alt kümesini desteklemektedir. Listelenenler dışındaki uzantılar desteklenmez. PostgreSQL için Azure veritabanı ile kendi uzantınızı oluşturamazsınız.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı tarafından desteklenen uzantılar

Aşağıdaki tablolarda, şu anda PostgreSQL için Azure veritabanı tarafından desteklenen standart PostgreSQL uzantıları listelenmektedir. Bu bilgiler çalıştırılarak da kullanılabilir `SELECT * FROM pg_available_extensions;` .

Bir sunucu grubuna yüklenen her uzantının sürümleri bazen PostgreSQL sürümüne (11, 12 veya 13) göre farklılık gösterir. Tablolar, veritabanı sürümüne göre uzantı sürümlerini listeler.

### <a name="citus-extension"></a>Citus uzantısı

> [!div class="mx-tableFixed"]
> | **Dahili numara** | **Açıklama** | **SAYFA 11** | **SAYFA 12** | **SAYFA 13** |
> |---|---|---|---|---|
> | [citus](https://github.com/citusdata/citus) | Citus dağıtılmış veritabanı. | 9,5-1 | 9,5-1 | 10.0-2 |

### <a name="data-types-extensions"></a>Veri türleri uzantıları

> [!div class="mx-tableFixed"]
> | **Dahili numara** | **Açıklama** | **SAYFA 11** | **SAYFA 12** | **SAYFA 13** |
> |---|---|---|---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Büyük/küçük harf duyarsız bir karakter dizesi türü sağlar. | 1,5 | 1.6 | 1.6 |
> | [ünüzde](https://www.postgresql.org/docs/current/static/cube.html) | Çok boyutlu küpler için bir veri türü sağlar. | 1.4 | 1.4 | 1.4 |
> | [hll](https://github.com/citusdata/postgresql-hll) | Bir HyperLogLog veri yapısı sağlar. | 2,14 | 2.15 | 2.15 |
> | [HStore](https://www.postgresql.org/docs/current/static/hstore.html) | Anahtar-değer çiftlerinin kümelerini depolamak için bir veri türü sağlar. | 1,5 | 1.6 | 1.7 |
> | [değilse](https://www.postgresql.org/docs/current/static/isn.html) | Uluslararası ürün numaralandırma standartları için veri türleri sağlar. | 1.2 | 1.2 | 1.2 |
> | [verilsin](https://www.postgresql.org/docs/current/lo.html) | Büyük nesne bakımı. | 1.1 | 1.1 | 1.1 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Hiyerarşik ağaç benzeri yapılar için bir veri türü sağlar. | 1.1 | 1.1 | 1.2 |
> | [SEG](https://www.postgresql.org/docs/current/seg.html) | Satır parçalarını veya kayan nokta aralıklarını göstermek için veri türü. | 1.3 | 1.3 | 1.3 |
> | [tdigest](https://github.com/tvondra/tdigest) | Quantiles ve kırpılmış ortalamalar gibi sıralama tabanlı istatistiklerin satır içi birikmesi için veri türü. | 1.0 | 1.0 | 1.0 |
> | [üst n](https://github.com/citusdata/postgresql-topn/) | Top-n JSONB için yazın. | 2.2.2 | 2.3.1 | 2.3.1 |

### <a name="full-text-search-extensions"></a>Tam metin arama uzantıları

> [!div class="mx-tableFixed"]
> | **Dahili numara** | **Açıklama** | **SAYFA 11** | **SAYFA 12** | **SAYFA 13** |
> |---|---|---|---|---|
> | [Dict \_ int](https://www.postgresql.org/docs/current/static/dict-int.html) | Tamsayılar için bir metin arama sözlüğü şablonu sağlar. | 1.0 | 1.0 | 1.0 |
> | [Dict \_ xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Genişletilmiş eş anlamlı işleme için metin arama sözlüğü şablonu. | 1.0 | 1.0 | 1.0 |
> | [vurgu kaldır](https://www.postgresql.org/docs/current/static/unaccent.html) | Lexemes 'ten vurguları (aksan işaretleri) kaldıran bir metin arama sözlüğü. | 1.1 | 1.1 | 1.1 |

### <a name="functions-extensions"></a>İşlev uzantıları

> [!div class="mx-tableFixed"]
> | **Dahili numara** | **Açıklama** | **SAYFA 11** | **SAYFA 12** | **SAYFA 13** |
> |---|---|---|---|---|
> | [Oto](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Alanları tekrar arttırın işlevleri. | 1.0 | 1.0 | 1.0 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Dünya yüzeyinde büyük daire mesafelerini hesaplamak için bir yol sağlar. | 1.1 | 1.1 | 1.1 |
> | [belirsizlik zystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | , Dizeler arasındaki benzerlikleri ve mesafeyi belirlemede çeşitli işlevler sağlar. | 1.1 | 1.1 | 1.1 |
> | [\_Kullanıcı adı Ekle](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Bir tablonun kimin tarafından değiştirildiğini izlemeye yönelik işlevler. | 1.0 | 1.0 | 1.0 |
> | [ıntagg](https://www.postgresql.org/docs/current/intagg.html) | Tamsayı toplayıcısı ve Numaralandırıcı (geçersiz). | 1.1 | 1.1 | 1.1 |
> | [ıntarray](https://www.postgresql.org/docs/current/static/intarray.html) | Tamsayıların null ve boş dizilerini işlemek için işlevler ve işleçler sağlar. | 1.2 | 1.2 | 1.3 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Son değiştirme süresini izlemek için işlevler. | 1.0 | 1.0 | 1.0 |
> | [sayfalık \_ partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Bölümlenmiş tabloları zamana veya KIMLIĞE göre yönetir. | 4.1 | 4.4.1 | 4.4.1 |
> | [sayfalık \_ TRGM](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Trigram eşleştirmeye göre alfasayısal metnin benzerliğini belirlemek için işlevler ve işleçler sağlar. | 1.4 | 1.4 | 1,5 |
> | [pgşifre](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Şifreleme işlevleri sağlar. | 1.3 | 1.3 | 1.3 |
> | [refınt](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Bilgi tutarlılığı (kullanım dışı) uygulamak için işlevler. | 1.0 | 1.0 | 1.0 |
> | oturum \_ Analizi | HStore dizilerini sorgulamak için işlevler. | | | |
> | [tablofunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Çapraz tablo dahil olmak üzere tüm tabloları düzenleyen işlevler sağlar. | 1.0 | 1.0 | 1.0 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Tetiklenmiş değişiklik bildirimleri. | 1.0 | 1.0 | 1.0 |
> | [timetralevel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Saat seyahati uygulama işlevleri. | 1.0 | | |
> | [UUID-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Evrensel benzersiz tanımlayıcılar (UUID 'ler) oluşturur. | 1.1 | 1.1 | 1.1 |

### <a name="index-types-extensions"></a>Dizin türleri uzantıları

> [!div class="mx-tableFixed"]
> | **Dahili numara** | **Açıklama** | **SAYFA 11** | **SAYFA 12** | **SAYFA 13** |
> |---|---|---|---|---|
> | [Bloom](https://www.postgresql.org/docs/current/bloom.html) | Bloom erişim yöntemi-imza dosya tabanlı dizin. | 1.0 | 1.0 | 1.0 |
> | [BTREE \_ gın](https://www.postgresql.org/docs/current/static/btree-gin.html) | Belirli veri türleri için B-ağacı benzeri davranışı uygulayan örnek bir işleç sınıfları sağlar. | 1.3 | 1.3 | 1.3 |
> | [BTREE \_ GİST](https://www.postgresql.org/docs/current/static/btree-gist.html) | B-ağacı uygulayan GiST Dizin işleci sınıfları sağlar. | 1,5 | 1,5 | 1,5 |

### <a name="language-extensions"></a>Dil uzantıları

> [!div class="mx-tableFixed"]
> | **Dahili numara** | **Açıklama** | **SAYFA 11** | **SAYFA 12** | **SAYFA 13** |
> |---|---|---|---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL yüklenebilir yordam dili. | 1.0 | 1.0 | 1.0 |

### <a name="miscellaneous-extensions"></a>Çeşitli uzantılar

> [!div class="mx-tableFixed"]
> | **Dahili numara** | **Açıklama** | **SAYFA 11** | **SAYFA 12** | **SAYFA 13** |
> |---|---|---|---|---|
> | [Yönetim Paketi](https://www.postgresql.org/docs/current/adminpack.html) | PostgreSQL için yönetim işlevleri. | 2.0 | 2.0 | 2.1 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | İlişki bütünlüğünü doğrulamaya yönelik işlevler. | 1.1 | 1.2 | 1.2 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Bir veritabanı oturumunda diğer PostgreSQL veritabanlarına bağlantıları destekleyen bir modül. Bu uzantı hakkında bilgi için bkz. "dblink and postgres_fdw" bölümü. | 1.2 | 1.2 | 1.2 |
> | [Dosya \_ FDW](https://www.postgresql.org/docs/current/file-fdw.html) | Düz dosya erişimi için yabancı veri sarmalayıcısı. | 1.0 | 1.0 | 1.0 |
> | [pageincele](https://www.postgresql.org/docs/current/pageinspect.html) | Veritabanı sayfalarının içeriğini düşük bir düzeyde inceleyin. | 1.7 | 1.7 | 1.8 |
> | [PG \_ buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | , Paylaşılan arabellek önbelleğinde gerçek zamanlı olarak neler olduğunu incelemek için bir yol sağlar. | 1.3 | 1.3 | 1.3 |
> | [sayfalık \_ cron](https://github.com/citusdata/pg_cron) | PostgreSQL için iş Zamanlayıcısı. | 1.1 | 1.3 | 1.3 |
> | [PG \_ freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Boş alan haritasını inceleyin (FSD). | 1.2 | 1.2 | 1.2 |
> | [sayfalık \_ ön sıcak](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Arabellek önbelleğine ilişki verileri yüklemek için bir yol sağlar. | 1.2 | 1.2 | 1.2 |
> | [PG \_ stat \_ deyimleri](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Bir sunucu tarafından yürütülen tüm SQL deyimlerinin yürütme istatistiklerini izlemek için bir yol sağlar. Bu uzantı hakkında bilgi için "pg_stat_statements" bölümüne bakın. | 1.6 | 1.7 | 1.8 |
> | [sayfalık \_ görünürlük](https://www.postgresql.org/docs/current/pgvisibility.html) | Görünürlük haritasını (VM) ve sayfa düzeyi görünürlük bilgilerini inceleyin. | 1.2 | 1.2 | 1.2 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Satır düzeyinde kilitleme bilgilerini göstermek için bir yol sağlar. | 1.2 | 1.2 | 1.2 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Demet düzeyi istatistiklerini göstermek için bir yol sağlar. | 1,5 | 1,5 | 1,5 |
> | [Postgres \_ FDW](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Dış PostgreSQL sunucularında depolanan verilere erişmek için kullanılan yabancı veri sarmalayıcı. Bu uzantı hakkında bilgi için bkz. "dblink and postgres_fdw" bölümü.| 1.0 | 1.0 | 1.0 |
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | TLS/SSL sertifikaları hakkında bilgi. | 1.2 | 1.2 | 1.2 |
> | [TSD \_ sistem \_ satırları](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Bir sınır olarak satır sayısını kabul eden, bu yöntem. | 1.0 | 1.0 | 1.0 |
> | [TSD \_ sistem \_ saati](https://www.postgresql.org/docs/current/tsm-system-time.html) | Bir sınır olarak milisaniye olarak zaman kabul eden, bu yöntem. | 1.0 | 1.0 | 1.0 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath sorgulama ve XSLT. | 1.1 | 1.1 | 1.1 |


### <a name="postgis-extensions"></a>PostGIS uzantıları

> [!div class="mx-tableFixed"]
> | **Dahili numara** | **Açıklama** | **SAYFA 11** | **SAYFA 12** | **SAYFA 13** |
> |---|---|---|---|---|
> | [PostGIS](https://www.postgis.net/), PostGIS \_ topolojisi, \_ \_ postgıal Tiger Geocoder, \_ postgısfcgal | PostgreSQL için uzamsal ve coğrafi nesneler. | 2.5.1 | 3.0.3 | 3.0.3 |
> | Adres \_ standartlayıcı, adres \_ standartlayıcısı \_ veri \_ ABD | Bir adresi bileşen öğelerine ayrıştırmak için kullanılır. Coğrafi kodlama adres normalleştirme adımını desteklemek için kullanılır. | 2.5.1 | 3.0.3 | 3.0.3 |
> | \_postgısfcgal | Postgısfcgal işlevleri. | 2.5.1 | 3.0.3 | 3.0.3 |
> | PostGIS \_ Tiger \_ Geocoder | PostGIS kocoder ve ters Geocoder. | 2.5.1 | 3.0.3 | 3.0.3 |
> | PostGIS \_ topolojisi | PostGIS topolojisi uzamsal türleri ve işlevleri. | 2.5.1 | 3.0.3 | 3.0.3 |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg \_ stat \_ deyimleri uzantısı](https://www.postgresql.org/docs/current/pgstatstatements.html) , her PostgreSQL için Azure veritabanı sunucusuna önceden yüklenir ve SQL deyimlerinin yürütme istatistiklerini izlemeye yönelik bir yol sağlar.

Ayar, `pg_stat_statements.track` uzantı tarafından hangi deyimlerin sayıldığını denetler. Varsayılan olarak `top` , istemci tarafından doğrudan verilen tüm deyimlerin izlendiği anlamına gelir. Diğer iki izleme düzeyi `none` ve ' dir `all` . Bu ayar, [Azure Portal](./howto-configure-server-parameters-using-portal.md) veya [Azure CLI](./howto-configure-server-parameters-using-cli.md)aracılığıyla bir sunucu parametresi olarak yapılandırılabilir.

Pg_stat_statements sorgu yürütme bilgileri ile her SQL bildirisini günlüğe kaydettiği için sunucu performansı üzerindeki etki arasında bir zorunluluğunu getirir vardır. Pg_stat_statements uzantısını etkin bir şekilde kullanmıyorsanız, ' ye ayarlamanızı öneririz `pg_stat_statements.track` `none` . Bazı üçüncü taraf izleme Hizmetleri sorgu Performans öngörüleri sunmak için pg_stat_statements kullanabilir, bu nedenle bunun sizin için mi olduğunu doğrulayın.

## <a name="dblink-and-postgres_fdw"></a>dblink ve postgres_fdw

\_Tek bir PostgreSQL sunucusundan diğerine veya aynı sunucuda başka bir veritabanına bağlanmak için dblink ve Postgres FDW kullanabilirsiniz.  Alıcı sunucunun, gönderme sunucusundan güvenlik duvarı aracılığıyla bağlantılara izin verilmesi gerekir.  Bu uzantıları, PostgreSQL için Azure veritabanı sunucuları veya hiper ölçek (Citus) sunucu grupları arasında bağlanmak üzere kullanmak için, **Azure hizmetlerinin ve kaynaklarının bu sunucu grubuna (veya sunucusuna) erişmesine Izin ver** ' i ayarlayın.  Aynı sunucuya geri dönmek için uzantıları kullanmak istiyorsanız, bu ayarı da açmanız gerekir.
**Azure hizmetlerinin ve kaynaklarının bu sunucu grubuna erişmesine Izin ver** ayarı, **ağ** altındaki hiper ölçek (Citus) sunucu grubunun Azure Portal sayfasında bulunabilir.  Şu anda, PostgreSQL için Azure veritabanı tek sunuculu ve hiper ölçek (Citus) ile giden bağlantılar, PostgreSQL sunucuları ve hiper ölçek (Citus) sunucu grupları için diğer Azure veritabanı bağlantıları dışında desteklenmez.
