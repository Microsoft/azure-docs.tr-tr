---
title: Uzantılar-PostgreSQL için Azure veritabanı-esnek sunucu
description: PostgreSQL için Azure veritabanı 'nda kullanılabilir olan Postgres uzantıları-esnek sunucu hakkında bilgi edinin
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: e8f71eb120b86f35672c9123b52f7f19c9fee662
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608468"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı 'nda PostgreSQL uzantıları-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

PostgreSQL, uzantıları kullanarak veritabanınızın işlevselliğini genişletmenizi sağlar. Uzantılar, bir komutla birlikte, tek bir pakette birden fazla ilgili SQL nesnesini bir araya toplayarak, veritabanınıza yüklenebilir veya kaldırılabilir. Veritabanınıza yüklenen uzantılar, yerleşik özellikler gibi çalışır.

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL uzantılarını kullanma
PostgreSQL uzantılarının kullanabilmeniz için veritabanınıza yüklenmesi gerekir. Belirli bir uzantıyı yüklemek için [Uzantı Oluştur](https://www.postgresql.org/docs/current/sql-createextension.html) komutunu çalıştırın. Bu komut, paketlenmiş nesneleri veritabanınıza yükler.

PostgreSQL için Azure veritabanı aşağıda listelenen bir anahtar uzantıları alt kümesini destekler. Bu bilgiler çalıştırılarak da kullanılabilir `SHOW azure.extensions;` . Bu belgede listelenmeyen uzantılar PostgreSQL için Azure veritabanı-esnek sunucu 'da desteklenmiyor. PostgreSQL için Azure veritabanı 'nda kendi uzantınızı oluşturamaz veya yükleyemezsiniz.


## <a name="postgres-12-extensions"></a>Postgres 12 uzantıları

PostgreSQL için Azure veritabanı 'nda, Postgres sürüm 12 ' ye sahip olan esnek sunucular için aşağıdaki uzantılar mevcuttur. 

> [!div class="mx-tableFixed"]
> | **Dahili numara**| **Uzantı sürümü** | **Açıklama** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.0.0           | Bir adresi bileşen öğelerine ayrıştırmak için kullanılır. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.0.0           | Adres Standartlayıcı ABD veri kümesi örneği|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | ilişki bütünlüğünü doğrulamaya yönelik işlevler|
> |[Bloom](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | Bloom erişim yöntemi-imza dosya tabanlı dizin|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | GıN 'te ortak veri türlerini dizinlemeye yönelik destek|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1,5             | GiST 'de ortak veri türlerini dizinlemeye yönelik destek|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | büyük/küçük harf duyarsız karakter dizeleri için veri türü|
> |[ünüzde](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | çok boyutlu küpler için veri türü|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | bir veritabanı içinden diğer PostgreSQL veritabanlarına bağlanma|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | tamsayılar için metin arama sözlük şablonu|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | Genişletilmiş eş anlamlı işleme için metin arama sözlüğü şablonu|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | Dünya yüzeyinde büyük daire mesafelerini hesaplama|
> |[belirsizlik zystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | dizeler arasındaki benzerlikleri ve mesafeyi belirleme|
> |[HStore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | (anahtar, değer) çiftleri kümelerini depolamak için veri türü|
> |[ıntagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | tamsayı toplayıcısı ve Numaralandırıcı. Dışı|
> |[ıntarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | tamsayı dizileri için işlevler, işleçler ve Dizin desteği|
> |[değilse](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | Uluslararası ürün numaralandırma standartları için veri türleri|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | hiyerarşik ağaç benzeri yapılar için veri türü|
> |[pageincele](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | veritabanı sayfalarının içeriğini düşük bir düzeyde inceleyin|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | Paylaşılan arabellek önbelleğini inceleyin|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | boş alan haritasını inceleyin (FSD)|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | ön sıcak ilişki verileri|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | yürütülen tüm SQL deyimlerinin yürütme istatistiklerini izleyin|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | trigram temelinde metin benzerliği ölçümü ve Dizin arama|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | görünürlük haritasını (VM) ve sayfa düzeyi görünürlük bilgilerini inceleyin|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | Denetim işlevselliği sağlar|
> |[pgşifre](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | şifreleme işlevleri|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | satır düzeyinde kilitleme bilgilerini göster|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1,5             | demet düzeyi istatistiklerini göster|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | PL/pgSQL yordam dili|
> |[PostGIS](https://www.postgis.net/)                      | 3.0.0           | PostGIS geometrisi, Coğrafya |
> |[postgis_raster](https://www.postgis.net/)               | 3.0.0           | PostGIS raster türleri ve işlevleri| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.0.0           | Postgısfcgal işlevleri|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.0.0           | PostGIS Tiger Geocoder ve ters coğrafi dişli|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.0.0           | PostGIS topolojisi uzamsal türleri ve işlevleri|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | uzak PostgreSQL sunucuları için yabancı veri sarmalayıcısı|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | SSL sertifikaları hakkında bilgi|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  Limit olarak satır sayısı kabul eden bir bu yöntem|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  Süre olarak milisaniye olarak zaman kabul eden bir limit yöntemi|
> |[vurgu kaldır](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | vurguları kaldıran metin arama sözlüğü|
> |[UUID-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             | evrensel benzersiz tanımlayıcılar oluşturma (UUID 'ler)|

## <a name="postgres-11-extensions"></a>Postgres 11 uzantıları

PostgreSQL için Azure veritabanı 'nda, Postgres sürüm 11 ' i içeren esnek sunucular için aşağıdaki uzantılar kullanılabilir. 

> [!div class="mx-tableFixed"]
> | **Dahili numara**| **Uzantı sürümü** | **Açıklama** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Bir adresi bileşen öğelerine ayrıştırmak için kullanılır. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Adres Standartlayıcı ABD veri kümesi örneği|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | ilişki bütünlüğünü doğrulamaya yönelik işlevler|
> |[Bloom](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | Bloom erişim yöntemi-imza dosya tabanlı dizin|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | GıN 'te ortak veri türlerini dizinlemeye yönelik destek|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1,5             | GiST 'de ortak veri türlerini dizinlemeye yönelik destek|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1,5             | büyük/küçük harf duyarsız karakter dizeleri için veri türü|
> |[ünüzde](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | çok boyutlu küpler için veri türü|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | bir veritabanı içinden diğer PostgreSQL veritabanlarına bağlanma|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | tamsayılar için metin arama sözlük şablonu|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | Genişletilmiş eş anlamlı işleme için metin arama sözlüğü şablonu|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | Dünya yüzeyinde büyük daire mesafelerini hesaplama|
> |[belirsizlik zystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | dizeler arasındaki benzerlikleri ve mesafeyi belirleme|
> |[HStore](https://www.postgresql.org/docs/11/hstore.html)                       | 1,5             | (anahtar, değer) çiftleri kümelerini depolamak için veri türü|
> |[ıntagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | tamsayı toplayıcısı ve Numaralandırıcı. Dışı|
> |[ıntarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | tamsayı dizileri için işlevler, işleçler ve Dizin desteği|
> |[değilse](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | Uluslararası ürün numaralandırma standartları için veri türleri|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | hiyerarşik ağaç benzeri yapılar için veri türü|
> |[pageincele](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | veritabanı sayfalarının içeriğini düşük bir düzeyde inceleyin|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | Paylaşılan arabellek önbelleğini inceleyin|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | boş alan haritasını inceleyin (FSD)|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | ön sıcak ilişki verileri|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | yürütülen tüm SQL deyimlerinin yürütme istatistiklerini izleyin|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | trigram temelinde metin benzerliği ölçümü ve Dizin arama|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | görünürlük haritasını (VM) ve sayfa düzeyi görünürlük bilgilerini inceleyin|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | Denetim işlevselliği sağlar|
> |[pgşifre](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | şifreleme işlevleri|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | satır düzeyinde kilitleme bilgilerini göster|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1,5             | demet düzeyi istatistiklerini göster|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL yordam dili|
> |[PostGIS](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometrisi, coğrafya ve raster uzamsal türleri ve işlevleri|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Postgısfcgal işlevleri|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger Geocoder ve ters coğrafi dişli|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS topolojisi uzamsal türleri ve işlevleri|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | uzak PostgreSQL sunucuları için yabancı veri sarmalayıcısı|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | SSL sertifikaları hakkında bilgi|
> |[tablofunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | çapraz tablo dahil olmak üzere tüm tabloları işleyen işlevler|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  Limit olarak satır sayısı kabul eden bir bu yöntem|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  Süre olarak milisaniye olarak zaman kabul eden bir limit yöntemi|
> |[vurgu kaldır](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | vurguları kaldıran metin arama sözlüğü|
> |[UUID-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | evrensel benzersiz tanımlayıcılar oluşturma (UUID 'ler)|


## <a name="dblink-and-postgres_fdw"></a>dblink ve postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) ve [Postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) , bir PostgreSQL sunucusundan diğerine veya aynı sunucudaki başka bir veritabanına bağlanmanızı sağlar. Esnek sunucu, herhangi bir PostgreSQL sunucusuna hem gelen hem de giden bağlantıları destekler. Gönderen sunucunun, alıcı sunucuya giden bağlantılara izin sağlaması gerekir. Benzer şekilde, alıcı sunucunun gönderme sunucusundan gelen bağlantılara izin verilmesi gerekir. 

Bu iki uzantıyı kullanmayı planlıyorsanız, sunucularınızı [VNET tümleştirmesiyle](concepts-networking.md) dağıtmanız önerilir. VNet tümleştirmesi varsayılan olarak VNET 'teki sunucular arasında bağlantılara izin verir. Ayrıca, erişimi özelleştirmek için [VNET ağ güvenlik gruplarını](../../virtual-network/manage-network-security-group.md) kullanmayı da seçebilirsiniz.

## <a name="pg_prewarm"></a>pg_prewarm

Pg_prewarm uzantısı, ilişkisel verileri önbelleğe yükler. Önbellekler önceden ısındıktan sonra, bir yeniden başlatmanın ardından sorgularınızın ilk çalıştırmaları için daha iyi yanıt süresi olduğu anlamına gelir. Otomatik etkin işlevselliği, PostgreSQL için Azure veritabanı-esnek sunucu 'da Şu anda kullanılamıyor.

## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg_stat_statements uzantısı](https://www.postgresql.org/docs/current/pgstatstatements.html) , SQL deyimlerinin yürütme istatistiklerini izlemeye yönelik bir yol sağlamak üzere PostgreSQL Için Azure veritabanı esnek sunucusuna önceden yüklenir.
`pg_stat_statements.track`Uzantı tarafından hangi deyimlerin sayıldığını denetleyen ayar, varsayılan olarak `top` , istemciler tarafından doğrudan verilen tüm deyimler izlenir. Diğer iki izleme düzeyi `none` ve ' dir `all` . Bu ayar bir sunucu parametresi olarak yapılandırılabilir.

Pg_stat_statements sorgu yürütme bilgileri ile her SQL bildirisini günlüğe kaydettiği için sunucu performansı üzerindeki etki arasında bir zorunluluğunu getirir vardır. Pg_stat_statements uzantısını etkin bir şekilde kullanmıyorsanız, ' ye ayarlamanızı öneririz `pg_stat_statements.track` `none` . Bazı üçüncü taraf izleme hizmetlerinin sorgu Performans öngörüleri sunmak için pg_stat_statements güvenebileceğini unutmayın. bu nedenle, sizin için bu durum olup olmadığını onaylayın.


## <a name="next-steps"></a>Sonraki adımlar

Kullanmak istediğiniz bir uzantıyı görmüyorsanız bize bize izin verin. Mevcut istekleri oylayın veya [geri bildirim forumumuzda](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)yeni geri bildirim istekleri oluşturun.