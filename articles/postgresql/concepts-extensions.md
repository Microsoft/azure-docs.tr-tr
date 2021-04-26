---
title: Uzantılar-PostgreSQL için Azure veritabanı-tek sunucu
description: PostgreSQL için Azure veritabanı 'nda kullanılabilen Postgres uzantıları-tek sunucu hakkında bilgi edinin
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 765c3653dae4f514263cd8db636d9cec42d56d15
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107627"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure Veritabanı'ndaki PostgreSQL uzantıları - Tek Sunucu
PostgreSQL, uzantıları kullanarak veritabanınızın işlevselliğini genişletmenizi sağlar. Uzantılar birden çok ilgili SQL nesnesini tek bir komutla veritabanınıza yüklenip kaldırılabilecek bir paket haline getirir. Veritabanınıza yüklenen uzantılar, yerleşik özellikler gibi çalışır.

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL uzantılarını kullanma
PostgreSQL uzantılarının kullanabilmeniz için veritabanınıza yüklenmesi gerekir. Belirli bir uzantıyı yüklemek için paketlenmiş nesneleri veritabanınıza yüklemek üzere psql aracından [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) komutunu çalıştırın.

PostgreSQL için Azure veritabanı aşağıda listelenen bir anahtar uzantıları alt kümesini destekler. Bu bilgiler çalıştırılarak da kullanılabilir `SELECT * FROM pg_available_extensions;` . Listelenenlerin ötesinde uzantılar desteklenmez. PostgreSQL için Azure veritabanı 'nda kendi uzantınızı oluşturamazsınız.

## <a name="postgres-11-extensions"></a>Postgres 11 uzantıları

Aşağıdaki uzantılar Postgres sürüm 11 ' in PostgreSQL sunucuları için Azure veritabanı 'nda mevcuttur. 

> [!div class="mx-tableFixed"]
> | **Dahili numara**| **Uzantı sürümü** | **Açıklama** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Bir adresi bileşen öğelerine ayrıştırmak için kullanılır. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Adres Standartlayıcı ABD veri kümesi örneği|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | GıN 'te ortak veri türlerini dizinlemeye yönelik destek|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1,5             | GiST 'de ortak veri türlerini dizinlemeye yönelik destek|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1,5             | büyük/küçük harf duyarsız karakter dizeleri için veri türü|
> |[ünüzde](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | çok boyutlu küpler için veri türü|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | bir veritabanı içinden diğer PostgreSQL veritabanlarına bağlanma|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | tamsayılar için metin arama sözlük şablonu|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | Dünya yüzeyinde büyük daire mesafelerini hesaplama|
> |[belirsizlik zystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | dizeler arasındaki benzerlikleri ve mesafeyi belirleme|
> |[HStore](https://www.postgresql.org/docs/11/hstore.html)                       | 1,5             | (anahtar, değer) çiftleri kümelerini depolamak için veri türü|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | PostgreSQL için kuramsal dizinler|
> |[ıntarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | tamsayı dizileri için işlevler, işleçler ve Dizin desteği|
> |[değilse](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | Uluslararası ürün numaralandırma standartları için veri türleri|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | hiyerarşik ağaç benzeri yapılar için veri türü|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Ticari RDBMS 'den işlevlerin ve paketlerin bir alt kümesini taklit eden işlevler ve işleçler|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | Denetim işlevselliği sağlar|
> |[pgşifre](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | şifreleme işlevleri|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2 Critical           | pgRouting uzantısı|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | satır düzeyinde kilitleme bilgilerini göster|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1,5             | demet düzeyi istatistiklerini göster|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | Paylaşılan arabellek önbelleğini inceleyin|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Bölümlenmiş tabloları zamana veya KIMLIĞE göre yönetme uzantısı|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | ön sıcak ilişki verileri|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | yürütülen tüm SQL deyimlerinin yürütme istatistiklerini izleyin|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | trigram temelinde metin benzerliği ölçümü ve Dizin arama|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL yordam dili|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (V8) güvenilen yordamsal dil|
> |[PostGIS](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometrisi, coğrafya ve raster uzamsal türleri ve işlevleri|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Postgısfcgal işlevleri|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger Geocoder ve ters coğrafi dişli|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS topolojisi uzamsal türleri ve işlevleri|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | uzak PostgreSQL sunucuları için yabancı veri sarmalayıcısı|
> |[tablofunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | çapraz tablo dahil olmak üzere tüm tabloları işleyen işlevler|
> |[timescaledb](https://docs.timescale.com/latest)                    |1.7.4             | Zaman serisi verilerine yönelik ölçeklenebilir eklemeleri ve karmaşık sorguları sunar|
> |[vurgu kaldır](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | vurguları kaldıran metin arama sözlüğü|
> |[UUID-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | evrensel benzersiz tanımlayıcılar oluşturma (UUID 'ler)|

## <a name="postgres-10-extensions"></a>Postgres 10 uzantıları 

Aşağıdaki uzantılar Postgres sürüm 10 ' a sahip PostgreSQL için Azure veritabanı sunucuları 'nda mevcuttur.

> [!div class="mx-tableFixed"]
> | **Dahili numara**| **Uzantı sürümü** | **Açıklama** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Bir adresi bileşen öğelerine ayrıştırmak için kullanılır. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Adres Standartlayıcı ABD veri kümesi örneği|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | GıN 'te ortak veri türlerini dizinlemeye yönelik destek|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1,5             | GiST 'de ortak veri türlerini dizinlemeye yönelik destek|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | Otomatik şifreli parolalar için veri türü|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | büyük/küçük harf duyarsız karakter dizeleri için veri türü|
> |[ünüzde](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | çok boyutlu küpler için veri türü|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | bir veritabanı içinden diğer PostgreSQL veritabanlarına bağlanma|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | tamsayılar için metin arama sözlük şablonu|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | Dünya yüzeyinde büyük daire mesafelerini hesaplama|
> |[belirsizlik zystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | dizeler arasındaki benzerlikleri ve mesafeyi belirleme|
> |[HStore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | (anahtar, değer) çiftleri kümelerini depolamak için veri türü|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL için kuramsal dizinler|
> |[ıntarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | tamsayı dizileri için işlevler, işleçler ve Dizin desteği|
> |[değilse](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | Uluslararası ürün numaralandırma standartları için veri türleri|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | hiyerarşik ağaç benzeri yapılar için veri türü|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Ticari RDBMS 'den işlevlerin ve paketlerin bir alt kümesini taklit eden işlevler ve işleçler|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | Denetim işlevselliği sağlar|
> |[pgşifre](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | şifreleme işlevleri|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2 Parallel sections           | pgRouting uzantısı|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | satır düzeyinde kilitleme bilgilerini göster|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1,5             | demet düzeyi istatistiklerini göster|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | Paylaşılan arabellek önbelleğini inceleyin|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Bölümlenmiş tabloları zamana veya KIMLIĞE göre yönetme uzantısı|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | ön sıcak ilişki verileri|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | yürütülen tüm SQL deyimlerinin yürütme istatistiklerini izleyin|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | trigram temelinde metin benzerliği ölçümü ve Dizin arama|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL yordam dili|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (V8) güvenilen yordamsal dil|
> |[PostGIS](https://www.postgis.net/)                      | 2.4.3           | PostGIS geometrisi, coğrafya ve raster uzamsal türleri ve işlevleri|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | Postgısfcgal işlevleri|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS Tiger Geocoder ve ters coğrafi dişli|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS topolojisi uzamsal türleri ve işlevleri|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | uzak PostgreSQL sunucuları için yabancı veri sarmalayıcısı|
> |[tablofunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | çapraz tablo dahil olmak üzere tüm tabloları işleyen işlevler|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.7.4             | Zaman serisi verilerine yönelik ölçeklenebilir eklemeleri ve karmaşık sorguları sunar|
> |[vurgu kaldır](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | vurguları kaldıran metin arama sözlüğü|
> |[UUID-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | evrensel benzersiz tanımlayıcılar oluşturma (UUID 'ler)|

## <a name="postgres-96-extensions"></a>Postgres 9,6 uzantıları 

Postgres sürüm 9,6 ' den PostgreSQL için Azure veritabanı sunucuları 'nda aşağıdaki uzantılar mevcuttur.

> [!div class="mx-tableFixed"]
> | **Dahili numara**| **Uzantı sürümü** | **Açıklama** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Bir adresi bileşen öğelerine ayrıştırmak için kullanılır. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Adres Standartlayıcı ABD veri kümesi örneği|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | GıN 'te ortak veri türlerini dizinlemeye yönelik destek|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | GiST 'de ortak veri türlerini dizinlemeye yönelik destek|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | Otomatik şifreli parolalar için veri türü|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | büyük/küçük harf duyarsız karakter dizeleri için veri türü|
> |[ünüzde](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | çok boyutlu küpler için veri türü|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | bir veritabanı içinden diğer PostgreSQL veritabanlarına bağlanma|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | tamsayılar için metin arama sözlük şablonu|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | Dünya yüzeyinde büyük daire mesafelerini hesaplama|
> |[belirsizlik zystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | dizeler arasındaki benzerlikleri ve mesafeyi belirleme|
> |[HStore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | (anahtar, değer) çiftleri kümelerini depolamak için veri türü|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL için kuramsal dizinler|
> |[ıntarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | tamsayı dizileri için işlevler, işleçler ve Dizin desteği|
> |[değilse](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | Uluslararası ürün numaralandırma standartları için veri türleri|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | hiyerarşik ağaç benzeri yapılar için veri türü|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Ticari RDBMS 'den işlevlerin ve paketlerin bir alt kümesini taklit eden işlevler ve işleçler|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | Denetim işlevselliği sağlar|
> |[pgşifre](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | şifreleme işlevleri|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting uzantısı|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | satır düzeyinde kilitleme bilgilerini göster|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | demet düzeyi istatistiklerini göster|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | Paylaşılan arabellek önbelleğini inceleyin|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Bölümlenmiş tabloları zamana veya KIMLIĞE göre yönetme uzantısı|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | ön sıcak ilişki verileri|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | yürütülen tüm SQL deyimlerinin yürütme istatistiklerini izleyin|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | trigram temelinde metin benzerliği ölçümü ve Dizin arama|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL yordam dili|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (V8) güvenilen yordamsal dil|
> |[PostGIS](https://www.postgis.net/)                      | 2.3.2           | PostGIS geometrisi, coğrafya ve raster uzamsal türleri ve işlevleri|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | Postgısfcgal işlevleri|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS Tiger Geocoder ve ters coğrafi dişli|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS topolojisi uzamsal türleri ve işlevleri|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | uzak PostgreSQL sunucuları için yabancı veri sarmalayıcısı|
> |[tablofunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | çapraz tablo dahil olmak üzere tüm tabloları işleyen işlevler|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.7.4             | Zaman serisi verilerine yönelik ölçeklenebilir eklemeleri ve karmaşık sorguları sunar|
> |[vurgu kaldır](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | vurguları kaldıran metin arama sözlüğü|
> |[UUID-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | evrensel benzersiz tanımlayıcılar oluşturma (UUID 'ler)|

## <a name="postgres-95-extensions"></a>Postgres 9,5 uzantıları

>[!NOTE]
> PostgreSQL sürüm 9,5 kullanımdan kaldırıldı.

Postgres sürüm 9,5 ' den PostgreSQL için Azure veritabanı sunucuları 'nda aşağıdaki uzantılar mevcuttur.

> [!div class="mx-tableFixed"]
> | **Dahili numara**| **Uzantı sürümü** | **Açıklama** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Bir adresi bileşen öğelerine ayrıştırmak için kullanılır. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Adres Standartlayıcı ABD veri kümesi örneği|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | GıN 'te ortak veri türlerini dizinlemeye yönelik destek|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | GiST 'de ortak veri türlerini dizinlemeye yönelik destek|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | Otomatik şifreli parolalar için veri türü|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | büyük/küçük harf duyarsız karakter dizeleri için veri türü|
> |[ünüzde](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | çok boyutlu küpler için veri türü|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | bir veritabanı içinden diğer PostgreSQL veritabanlarına bağlanma|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | tamsayılar için metin arama sözlük şablonu|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | Dünya yüzeyinde büyük daire mesafelerini hesaplama|
> |[belirsizlik zystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | dizeler arasındaki benzerlikleri ve mesafeyi belirleme|
> |[HStore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | (anahtar, değer) çiftleri kümelerini depolamak için veri türü|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL için kuramsal dizinler|
> |[ıntarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | tamsayı dizileri için işlevler, işleçler ve Dizin desteği|
> |[değilse](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | Uluslararası ürün numaralandırma standartları için veri türleri|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | hiyerarşik ağaç benzeri yapılar için veri türü|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Ticari RDBMS 'den işlevlerin ve paketlerin bir alt kümesini taklit eden işlevler ve işleçler|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | Denetim işlevselliği sağlar|
> |[pgşifre](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | şifreleme işlevleri|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting uzantısı|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | satır düzeyinde kilitleme bilgilerini göster|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | demet düzeyi istatistiklerini göster|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | Paylaşılan arabellek önbelleğini inceleyin|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Bölümlenmiş tabloları zamana veya KIMLIĞE göre yönetme uzantısı|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | ön sıcak ilişki verileri|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | yürütülen tüm SQL deyimlerinin yürütme istatistiklerini izleyin|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | trigram temelinde metin benzerliği ölçümü ve Dizin arama|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL yordam dili|
> |[PostGIS](https://www.postgis.net/)                      | 2.3.0           | PostGIS geometrisi, coğrafya ve raster uzamsal türleri ve işlevleri|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | Postgısfcgal işlevleri|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS Tiger Geocoder ve ters coğrafi dişli|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS topolojisi uzamsal türleri ve işlevleri|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | uzak PostgreSQL sunucuları için yabancı veri sarmalayıcısı|
> |[tablofunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | çapraz tablo dahil olmak üzere tüm tabloları işleyen işlevler|
> |[vurgu kaldır](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | vurguları kaldıran metin arama sözlüğü|
> |[UUID-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | evrensel benzersiz tanımlayıcılar oluşturma (UUID 'ler)|


## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg_stat_statements uzantısı](https://www.postgresql.org/docs/current/pgstatstatements.html) , SQL deyimlerinin yürütme istatistiklerini izlemek Için her PostgreSQL Için Azure veritabanı sunucusuna önceden yüklenir.
`pg_stat_statements.track`Uzantı tarafından hangi deyimlerin sayıldığını denetleyen ayar, varsayılan olarak `top` , istemciler tarafından doğrudan verilen tüm deyimler izlenir. Diğer iki izleme düzeyi `none` ve ' dir `all` . Bu ayar, [Azure Portal](./howto-configure-server-parameters-using-portal.md) veya [Azure CLI](./howto-configure-server-parameters-using-cli.md)aracılığıyla bir sunucu parametresi olarak yapılandırılabilir.

Pg_stat_statements sorgu yürütme bilgileri ile her SQL bildirisini günlüğe kaydettiği için sunucu performansı üzerindeki etki arasında bir zorunluluğunu getirir vardır. Pg_stat_statements uzantısını etkin bir şekilde kullanmıyorsanız, ' ye ayarlamanızı öneririz `pg_stat_statements.track` `none` . Bazı üçüncü taraf izleme hizmetlerinin sorgu Performans öngörüleri sunmak için pg_stat_statements güvenebileceğini unutmayın. bu nedenle, sizin için bu durum olup olmadığını onaylayın.

## <a name="dblink-and-postgres_fdw"></a>dblink ve postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) ve [Postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) , bir PostgreSQL sunucusundan diğerine veya aynı sunucudaki başka bir veritabanına bağlanmanızı sağlar. Alıcı sunucunun, gönderme sunucusundan güvenlik duvarı aracılığıyla bağlantılara izin verilmesi gerekir. PostgreSQL için Azure veritabanı sunucuları arasında bağlanmak üzere bu uzantıları kullanırken, bu, "Azure hizmetlerine erişime Izin ver" ayarı kullanılarak yapılabilir. Ayrıca, uzantıları aynı sunucuya geri dönmek için kullanmak istiyorsanız bu da gereklidir. "Azure hizmetlerine erişime Izin ver" ayarı, Postgres sunucusunun Azure portal sayfasında, bağlantı güvenliği altında bulunabilir. "Azure hizmetlerine erişime Izin ver" seçeneği açıldığında tüm Azure IP 'Leri izin verilenler listesine koyar.

Şu anda, PostgreSQL için Azure veritabanı 'na giden bağlantılar, aynı bölgedeki PostgreSQL sunucuları için diğer Azure veritabanı bağlantıları dışında desteklenmez.

## <a name="uuid"></a>uuid
`uuid_generate_v4()` [UUID-ossp uzantısından](https://www.postgresql.org/docs/current/uuid-ossp.html)kullanmayı planlıyorsanız `gen_random_uuid()` performans avantajları için [pgşifre uzantısı](https://www.postgresql.org/docs/current/pgcrypto.html) ile karşılaştırmayı göz önünde bulundurun.

## <a name="pgaudit"></a>pgAudit
[Pgaudit uzantısı](https://github.com/pgaudit/pgaudit/blob/master/README.md) , oturum ve nesne denetim günlüğü sağlar. Bu uzantıyı PostgreSQL için Azure veritabanı 'nda nasıl kullanacağınızı öğrenmek için [Denetim kavramları makalesini](concepts-audit.md)ziyaret edin. 

## <a name="pg_prewarm"></a>pg_prewarm
Pg_prewarm uzantısı, ilişkisel verileri önbelleğe yükler. Önbellekler önceden ısındıktan sonra, bir yeniden başlatmanın ardından sorgularınızın ilk çalıştırmaları için daha iyi yanıt süresi olduğu anlamına gelir. Postgres 10 ve sonrasında, ön [sıcak işlevi](https://www.postgresql.org/docs/10/pgprewarm.html)kullanılarak el ile yapılır.

Postgres 11 ve üzeri sürümlerde, önısını [otomatik olarak](https://www.postgresql.org/docs/current/pgprewarm.html)gerçekleşecek şekilde yapılandırabilirsiniz. Parametrenin listesine pg_prewarm dahil etmeniz `shared_preload_libraries` ve değişikliği uygulamak için sunucuyu yeniden başlatmanız gerekir. Parametreler [Azure Portal](howto-configure-server-parameters-using-portal.md), [CLI](howto-configure-server-parameters-using-cli.md), REST API veya ARM şablonundan ayarlanabilir. 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB, PostgreSQL için bir uzantı olarak paketlenmiş bir zaman serisi veritabanıdır. TimescaleDB zamana dayalı analitik işlevler, iyileştirmeler ve zaman serisi iş yükleri için Postgres ölçekleme sağlar.

[TimescaleDB hakkında daha fazla bilgi](https://docs.timescale.com/latest)için bkz. zaman ölçeğinin tescilli ticari marka [, Inc.](https://www.timescale.com/). PostgreSQL için Azure veritabanı TimescaleDB [Apache-2 sürümünü](https://www.timescale.com/legal/licenses)sağlar.

### <a name="installing-timescaledb"></a>TimescaleDB yükleniyor
TimescaleDB yüklemek için, bunu sunucunun paylaşılan önyükleme kitaplıklarına dahil etmeniz gerekir. Postgres 'nin parametresinin bir değişikliği, `shared_preload_libraries` **sunucu yeniden başlatmanın** etkili olmasını gerektirir. [Azure Portal](howto-configure-server-parameters-using-portal.md) veya [Azure CLI](howto-configure-server-parameters-using-cli.md)kullanarak parametreleri değiştirebilirsiniz.

[Azure Portal](https://portal.azure.com/)kullanarak:

1. PostgreSQL için Azure Veritabanı sunucunuzu seçin.

2. Kenar çubuğunda **sunucu parametreleri**' ni seçin.

3. Parametresi için arama yapın `shared_preload_libraries` .

4. **TimescaleDB** öğesini seçin.

5. Değişikliklerinizi korumak için **Kaydet** ' i seçin. Değişiklik kaydedildikten sonra bir bildirim alırsınız. 

6. Bildirimden sonra, bu değişiklikleri uygulamak için sunucuyu **yeniden başlatın** . Bir sunucuyu yeniden başlatmayı öğrenmek için bkz. [PostgreSQL Için Azure veritabanını yeniden başlatma sunucusu](howto-restart-server-portal.md).


Artık Postgres veritabanınızda TimescaleDB 'ı etkinleştirebilirsiniz. Veritabanına bağlanın ve aşağıdaki komutu verin:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Bir hata görürseniz, shared_preload_libraries kaydettikten sonra [sunucunuzu yeniden başlattığınızdan](howto-restart-server-portal.md) emin olun. 

Artık, [sıfırdan](https://docs.timescale.com/getting-started/creating-hypertables) bir TimescaleDB hypertable oluşturabilir veya [PostgreSQL içindeki mevcut zaman serisi verilerini](https://docs.timescale.com/getting-started/migrating-data)geçirebilirsiniz.

### <a name="restoring-a-timescale-database"></a>Zaman ölçeği veritabanını geri yükleme
Pg_dump ve pg_restore kullanarak bir zaman ölçeği veritabanını geri yüklemek için hedef veritabanında iki yardımcı yordam çalıştırmanız gerekir: `timescaledb_pre_restore()` ve `timescaledb_post restore()` .

İlk olarak hedef veritabanını hazırlayın:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Artık özgün veritabanında pg_dump çalıştırabilir ve sonra pg_restore yapabilirsiniz. Geri yüklemeden sonra, geri yüklenen veritabanında aşağıdaki komutu çalıştırmayı unutmayın:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Sonraki adımlar
Kullanmak istediğiniz bir uzantıyı görmüyorsanız bize bize izin verin. Mevcut istekleri oylayın veya [geri bildirim forumumuzda](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)yeni geri bildirim istekleri oluşturun.