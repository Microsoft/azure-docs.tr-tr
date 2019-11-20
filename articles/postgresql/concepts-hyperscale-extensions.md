---
title: PostgreSQL için Azure veritabanı 'nda PostgreSQL uzantıları – hiper ölçek (Citus)
description: PostgreSQL için Azure veritabanı 'ndaki uzantıları kullanarak veritabanınızın işlevselliğini genişletme özelliğini açıklar.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 41edcc40bae7915e321a278592dd2f73621aae05
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482421"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nda PostgreSQL uzantıları – hiper ölçek (Citus)

PostgreSQL, uzantıları kullanarak veritabanınızın işlevselliğini genişletmenizi sağlar. Uzantılar, tek bir komutla veritabanınızdan yüklenebilecek veya kaldırılabileceği tek bir pakette birden fazla ilgili SQL nesnesini paketlemeye olanak tanır. Veritabanına yüklendikten sonra uzantılar, yerleşik özellikler gibi çalışabilir. PostgreSQL uzantıları hakkında daha fazla bilgi için bkz. [Package ile ilgili nesneler bir uzantıya](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>PostgreSQL uzantılarını kullanma

PostgreSQL uzantılarının kullanabilmeniz için veritabanınıza yüklenmesi gerekir. Belirli bir uzantıyı yüklemek için, paketlenmiş nesneleri veritabanınıza yüklemek üzere psql aracından [uzantı oluştur](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) komutunu çalıştırın.

PostgreSQL için Azure veritabanı-hiper ölçek (Citus) Şu anda burada listelenen bir anahtar uzantıları alt kümesini desteklemektedir. Listelenenler dışındaki uzantılar desteklenmez. PostgreSQL için Azure veritabanı ile kendi uzantınızı oluşturamazsınız.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı tarafından desteklenen uzantılar

Aşağıdaki tablolarda, şu anda PostgreSQL için Azure veritabanı tarafından desteklenen standart PostgreSQL uzantıları listelenmektedir. Bu bilgiler `SELECT * FROM pg_available_extensions;`çalıştırılarak da kullanılabilir.

### <a name="data-types-extensions"></a>Veri türleri uzantıları

> [!div class="mx-tableFixed"]
> | **Uzantının** | **Açıklama** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Büyük/küçük harf duyarsız bir karakter dizesi türü sağlar. |
> | [ünüzde](https://www.postgresql.org/docs/9.6/static/cube.html) | Çok boyutlu küpler için bir veri türü sağlar. |
> | [HStore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Anahtar-değer çiftlerinin kümelerini depolamak için bir veri türü sağlar. |
> | [değilse](https://www.postgresql.org/docs/9.6/static/isn.html) | Uluslararası ürün numaralandırma standartları için veri türleri sağlar. |
> | [verilsin](https://www.postgresql.org/docs/current/lo.html) | Büyük nesne bakımı. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Hiyerarşik ağaç benzeri yapılar için bir veri türü sağlar. |
> | [SEG](https://www.postgresql.org/docs/current/seg.html) | Satır parçalarını veya kayan nokta aralıklarını göstermek için veri türü. |
> | [üst n](https://github.com/citusdata/postgresql-topn/) | Top-n JSONB için yazın. |

### <a name="full-text-search-extensions"></a>Tam metin arama uzantıları

> [!div class="mx-tableFixed"]
> | **Uzantının** | **Açıklama** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Tamsayılar için bir metin arama sözlüğü şablonu sağlar. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Genişletilmiş eş anlamlı işleme için metin arama sözlüğü şablonu. |
> | [vurgu kaldır](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Lexemes 'ten vurguları (aksan işaretleri) kaldıran bir metin arama sözlüğü. |

### <a name="functions-extensions"></a>İşlev uzantıları

> [!div class="mx-tableFixed"]
> | **Uzantının** | **Açıklama** |
> |---|---|
> | [Oto](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Alanları tekrar arttırın işlevleri. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Dünya yüzeyinde büyük daire mesafelerini hesaplamak için bir yol sağlar. |
> | [belirsizlik zystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | , Dizeler arasındaki benzerlikleri ve mesafeyi belirlemede çeşitli işlevler sağlar. |
> | [\_Kullanıcı adı Ekle](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Bir tablonun kimin tarafından değiştirildiğini izlemeye yönelik işlevler. |
> | [ıntagg](https://www.postgresql.org/docs/current/intagg.html) | Tamsayı toplayıcısı ve Numaralandırıcı (geçersiz). |
> | [ıntarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Tamsayıların null ve boş dizilerini işlemek için işlevler ve işleçler sağlar. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Son değiştirme süresini izlemek için işlevler. |
> | [pgşifre](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Şifreleme işlevleri sağlar. |
> | [PG\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Bölümlenmiş tabloları zamana veya KIMLIĞE göre yönetir. |
> | [PG\_TRGM](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Trigram eşleştirmeye göre alfasayısal metnin benzerliğini belirlemek için işlevler ve işleçler sağlar. |
> | [refınt](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Bilgi tutarlılığı (kullanım dışı) uygulamak için işlevler. |
> | oturum\_Analizi | HStore dizilerini sorgulamak için işlevler. |
> | [tablofunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Çapraz tablo dahil olmak üzere tüm tabloları düzenleyen işlevler sağlar. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Tetiklenmiş değişiklik bildirimleri. |
> | [timetralevel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Saat seyahati uygulama işlevleri. |
> | [UUID-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Evrensel benzersiz tanımlayıcılar (UUID 'ler) oluşturur. |

### <a name="hyperscale-extensions"></a>Hiper ölçek uzantıları

> [!div class="mx-tableFixed"]
> | **Uzantının** | **Açıklama** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus dağıtılmış veritabanı. |
> | parça\_yeniden dengeleyici | Düğüm ekleme veya kaldırma durumunda bir sunucu grubundaki verileri güvenli bir şekilde yeniden dengeleyin. |

### <a name="index-types-extensions"></a>Dizin türleri uzantıları

> [!div class="mx-tableFixed"]
> | **Uzantının** | **Açıklama** |
> |---|---|
> | [Bloom](https://www.postgresql.org/docs/current/bloom.html) | Bloom erişim yöntemi-imza dosya tabanlı dizin. |
> | [BTREE\_gın](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Belirli veri türleri için B-ağacı benzeri davranışı uygulayan örnek bir işleç sınıfları sağlar. |
> | [BTREE\_GİST](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | B-ağacı uygulayan GiST Dizin işleci sınıfları sağlar. |

### <a name="language-extensions"></a>Dil uzantıları

> [!div class="mx-tableFixed"]
> | **Uzantının** | **Açıklama** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL yüklenebilir yordam dili. |

### <a name="miscellaneous-extensions"></a>Çeşitli uzantılar

> [!div class="mx-tableFixed"]
> | **Uzantının** | **Açıklama** |
> |---|---|
> | [Yönetim Paketi](https://www.postgresql.org/docs/current/adminpack.html) | PostgreSQL için yönetim işlevleri. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | İlişki bütünlüğünü doğrulamaya yönelik işlevler. |
> | [dosya\_FDW](https://www.postgresql.org/docs/current/file-fdw.html) | Düz dosya erişimi için yabancı veri sarmalayıcısı. |
> | [pageincele](https://www.postgresql.org/docs/current/pageinspect.html) | Veritabanı sayfalarının içeriğini düşük bir düzeyde inceleyin. |
> | [PG\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | , Paylaşılan arabellek önbelleğinde gerçek zamanlı olarak neler olduğunu incelemek için bir yol sağlar. |
> | [PG\_cron](https://github.com/citusdata/pg_cron) | PostgreSQL için iş Zamanlayıcısı. |
> | [PG\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Boş alan haritasını inceleyin (FSD). |
> | [PG\_ön sıcak](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Arabellek önbelleğine ilişki verileri yüklemek için bir yol sağlar. |
> | [PG\_stat\_deyimleri](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Bir sunucu tarafından yürütülen tüm SQL deyimlerinin yürütme istatistiklerini izlemek için bir yol sağlar. Bu uzantı hakkında bilgi için "pg_stat_statements" bölümüne bakın. |
> | [sayfalık\_görünürlüğü](https://www.postgresql.org/docs/current/pgvisibility.html) | Görünürlük haritasını (VM) ve sayfa düzeyi görünürlük bilgilerini inceleyin. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Satır düzeyinde kilitleme bilgilerini göstermek için bir yol sağlar. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Demet düzeyi istatistiklerini göstermek için bir yol sağlar. |
> | [Postgres\_FDW](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Dış PostgreSQL sunucularında depolanan verilere erişmek için kullanılan yabancı veri sarmalayıcı. Bu uzantı hakkında bilgi için "dblink ve postgres_fdw" bölümüne bakın.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | SSL sertifikaları hakkında bilgi. |
> | [TSD\_sistem\_satırları](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Bir sınır olarak satır sayısını kabul eden, bu yöntem. |
> | [TSD\_sistem\_saati](https://www.postgresql.org/docs/current/tsm-system-time.html) | Bir sınır olarak milisaniye olarak zaman kabul eden, bu yöntem. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | CPU veya disk maliyeti olmayan kuramsal dizinler oluşturmak için bir yol sağlar. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Bir veritabanı oturumunda diğer PostgreSQL veritabanlarına bağlantıları destekleyen bir modül. Bu uzantı hakkında bilgi için "dblink ve postgres_fdw" bölümüne bakın. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath sorgulama ve XSLT. |


### <a name="postgis-extensions"></a>PostGIS uzantıları

> [!div class="mx-tableFixed"]
> | **Uzantının** | **Açıklama** |
> |---|---|
> | [PostGIS](https://www.postgis.net/)postgıs\_topolojisi, postgıs\_Tiger\_Geocoder, postgıs\_sfcgal | PostgreSQL için uzamsal ve coğrafi nesneler. |
> | Adres\_standartlayıcı, adres\_standartlayıcı\_veri\_ABD | Bir adresi bileşen öğelerine ayrıştırmak için kullanılır. Coğrafi kodlama adres normalleştirme adımını desteklemek için kullanılır. |
> | PostGIS\_sfcgal | Postgısfcgal işlevleri. |
> | PostGIS\_Tiger\_Geocoder | PostGIS kocoder ve ters Geocoder. |
> | PostGIS\_topolojisi | PostGIS topolojisi uzamsal türleri ve işlevleri. |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg\_stat\_deyimleri uzantısı](https://www.postgresql.org/docs/current/pgstatstatements.html) , SQL deyimlerinin yürütme istatistiklerini izlemek Için her PostgreSQL Için Azure veritabanı sunucusuna önceden yüklenir.

Ayar `pg_stat_statements.track`, uzantı tarafından hangi deyimlerin sayıldığını denetler. Varsayılan olarak, istemci tarafından doğrudan verilen tüm deyimlerin izlendiği anlamına gelen `top`. Diğer iki izleme düzeyi `none` ve `all`. Bu ayar, [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) veya [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)aracılığıyla bir sunucu parametresi olarak yapılandırılabilir.

Sorgu yürütme bilgileri pg_stat_statements 'nin sağladığı ve her SQL bildirisini günlüğe kaydettiği için sunucu performansı üzerindeki etkilerden oluşan bir zorunluluğunu getirir vardır. Pg_stat_statements uzantısını etkin bir şekilde kullanmıyorsanız, `pg_stat_statements.track` `none`ayarlamanızı öneririz. Bazı üçüncü taraf izleme Hizmetleri, sorgu performansı öngörülerini sunabilmeniz için pg_stat_statements 'i kullanabilir, bu nedenle bunun sizin için mi olduğunu doğrulayın.

## <a name="dblink-and-postgres_fdw"></a>dblink ve postgres_fdw
Tek bir PostgreSQL sunucusundan diğerine veya aynı sunucuda başka bir veritabanına bağlanmak için dblink ve postgres_fdw kullanabilirsiniz. Alıcı sunucunun, gönderme sunucusundan güvenlik duvarı aracılığıyla bağlantılara izin verilmesi gerekir. Bu uzantıları, PostgreSQL için Azure veritabanı sunucuları arasında bağlanmak üzere kullanmak için **Azure hizmetlerine erişime Izin ver** ' i açık olarak ayarlayın. Aynı sunucuya geri dönmek için uzantıları kullanmak istiyorsanız, bu ayarı da açmanız gerekir. **Azure hizmetlerine erişime Izin ver** ayarı, **bağlantı güvenliği**altındaki postgres sunucusunun Azure Portal sayfasında bulunabilir. **Azure hizmetlerine erişime Izin ver** ' in etkinleştirilmesi, tüm Azure IP 'lerini beyaz listeler.

Şu anda PostgreSQL için Azure veritabanı 'na giden bağlantılar, PostgreSQL için Azure veritabanı sunucuları için bağlantılar dışında desteklenmez.
