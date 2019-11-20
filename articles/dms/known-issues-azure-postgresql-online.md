---
title: PostgreSQL 'e yönelik çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları, PostgreSQL için Azure veritabanı-tek sunucu | Microsoft Docs
description: PostgreSQL ' den PostgreSQL için Azure veritabanı 'na çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları hakkında bilgi edinin.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/27/2019
ms.openlocfilehash: e25e31a9ed656d625d2025d8d0086d23ecf10682
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043197"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql-single-server"></a>PostgreSQL ' den PostgreSQL için Azure DB 'ye çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları-tek sunucu

PostgreSQL 'e yönelik çevrimiçi geçişlerle ilgili bilinen sorunlar ve sınırlamalar PostgreSQL için Azure veritabanı 'na-tek sunucu aşağıdaki bölümlerde açıklanmıştır.

## <a name="online-migration-configuration"></a>Çevrimiçi geçiş yapılandırması

- Kaynak PostgreSQL sunucusu, 9.5.11, 9.6.7 veya 10,3 veya sonraki bir sürümü çalıştırıyor olmalıdır. Daha fazla bilgi için bkz. [PostgreSQL veritabanı sürümlerini destekleyen](../postgresql/concepts-supported-versions.md)makale.
- Yalnızca aynı sürüm geçişleri desteklenir. Örneğin, PostgreSQL 9.5.11 'in PostgreSQL için Azure veritabanı 9.6.7 'ye geçirilmesi desteklenmez.

    > [!NOTE]
    > PostgreSQL sürüm 10 ' da şu anda DMS yalnızca, sürüm 10,3 ' in PostgreSQL için Azure veritabanı 'na geçirilmesini destekler. Çok yakında PostgreSQL 'in daha yeni sürümlerini desteklemeyi planlıyoruz.

- **Kaynak PostgreSQL PostgreSQL. conf** dosyasında mantıksal çoğaltmayı etkinleştirmek için aşağıdaki parametreleri ayarlayın:
  - **wal_level** = mantıksal
  - **max_replication_slots** = [geçiş için en fazla veritabanı sayısı]; dört veritabanını geçirmek istiyorsanız, değeri 4 olarak ayarlayın
  - **max_wal_senders** = [aynı anda çalışan veritabanlarının sayısı]; Önerilen değer 10 ' dur
- Kaynak PostgreSQL pg_hba. conf dosyasına DMS Aracısı IP 'si ekleyin
  1. DMS 'in bir örneğini sağlamayı tamamladıktan sonra DMS IP adresini bir yere unutmayın.
  2. IP adresini pg_hba. conf dosyasına aşağıda gösterildiği gibi ekleyin:

        Tüm 172.16.136.18/10 MD5 konak çoğaltma Postgres 172.16.136.18/10 MD5 ana bilgisayar

- Kullanıcı, kaynak veritabanını barındıran sunucuda Süper Kullanıcı iznine sahip olmalıdır
- Kaynak veritabanı şemasında ENUM olmadan, kaynak ve hedef veritabanı şemaları eşleşmelidir.
- PostgreSQL için Azure veritabanı 'nın hedef şeması-tek sunucu yabancı anahtarlara sahip olmamalıdır. Yabancı anahtarları bırakmak için aşağıdaki sorguyu kullanın:

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Sorgu sonucunda bırakma yabancı anahtarını (ikinci sütun) çalıştırın.

- PostgreSQL için Azure veritabanı hedef şeması-tek sunucu, herhangi bir tetikleyici içermemelidir. Hedef veritabanında Tetikleyicileri devre dışı bırakmak için aşağıdakileri kullanın:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Veri türü sınırlamaları

- **Kısıtlama**: kaynak PostgreSQL veritabanında bir sabit listesi veri türü varsa, geçiş sürekli eşitleme sırasında başarısız olur.

    **Geçici çözüm**: enum veri türünü PostgreSQL Için Azure veritabanı 'nda değişen karakterle değiştirin.

- **Kısıtlama**: tablolarda birincil anahtar yoksa sürekli eşitleme başarısız olur.

    **Geçici çözüm**: geçiş işleminin devam etmesi için geçici olarak tablo için bir birincil anahtar ayarlayın. Veri geçişi tamamlandıktan sonra birincil anahtarı kaldırabilirsiniz.

- **Kısıtlama**: jsonb veri türü geçiş için desteklenmiyor.

## <a name="lob-limitations"></a>LOB sınırlamaları

Büyük nesne (LOB) sütunları büyük büyüyerek sütunlardır. PostgreSQL için, LOB veri türlerine örnek olarak XML, JSON, IMAGE, metın vb. verilebilir.

- **Sınırlama**: lob veri türleri birincil anahtar olarak kullanılıyorsa, geçiş başarısız olur.

    **Geçici çözüm**: birincil anahtarı diğer veri TÜRLERI veya LOB olmayan sütunlarla değiştirin.

- **Kısıtlama**: büyük nesne (LOB) sütununun uzunluğu 32 KB 'tan büyükse, veriler hedefte kesilebilir. Bu sorguyu kullanarak LOB sütununun uzunluğunu kontrol edebilirsiniz:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Geçici çözüm**: 32 KB 'den büyük bir lob nesneniz varsa [Azure veritabanı geçişleri sorun](mailto:AskAzureDatabaseMigrations@service.microsoft.com)konusunda mühendislik ekibine başvurun.

- **Kısıtlama**: tabloda lob sütunları varsa ve tablo için birincil anahtar kümesi yoksa, veriler bu tablo için geçirilmeyebilir.

    **Geçici çözüm**: geçiş işleminin devam edebilmesi için tablo için geçici olarak bir birincil anahtar ayarlayın. Veri geçişi tamamlandıktan sonra birincil anahtarı kaldırabilirsiniz.

## <a name="postgresql10-workaround"></a>PostgreSQL10 geçici çözüm

PostgreSQL 10. x, pg_xlog klasör adlarında çeşitli değişiklikler yapar ve bu nedenle geçişe beklenen şekilde çalışmadığını neden olur. PostgreSQL 10. x ' den PostgreSQL için Azure veritabanı 10,3 ' den geçiş yapıyorsanız, pg_xlog işlevleri etrafında sarmalayıcı işlevi oluşturmak için kaynak PostgreSQL veritabanında aşağıdaki betiği yürütün.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

  > [!NOTE]
  > Yukarıdaki komut dosyasında, "PG_User", geçiş kaynağına bağlanmak için kullanılan Kullanıcı adını ifade eder.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>AWS RDS PostgreSQL 'ten çevrimiçi geçiş yaparken sınırlamalar

AWS RDS PostgreSQL 'ten PostgreSQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirmeye çalıştığınızda, aşağıdaki hatalarla karşılaşabilirsiniz.

- **Hata**: ' {Database} ' veritabanındaki ' {Table} ' tablosunda bulunan ' {Column} ' sütununun varsayılan değeri kaynak ve hedef sunucularda farklı. Değer kaynakta '{value on source}', hedefte ise '{value on target}'.

  **Kısıtlama**: bir sütun şemasındaki varsayılan değer, kaynak ve hedef veritabanları arasında farklı olduğunda bu hata oluşur.
  **Geçici çözüm**: hedefteki şemanın kaynaktaki şemayla eşleştiğinden emin olun. Şemayı geçirme hakkında ayrıntılı bilgi için [Azure PostgreSQL çevrimiçi geçiş belgelerine](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)bakın.

- **Hata**: ' {Database} ' hedef veritabanında ' {Database} ' kaynak veritabanının ' {tablo sayısı} ' tablosu olduğu için ' {tablo sayısı} ' tablo vardır. Kaynak ve hedef veritabanlarındaki tablo sayısı aynı olmalıdır.

  **Sınırlama**: Bu hata, tablo sayısı kaynak ve hedef veritabanları arasında farklıysa oluşur.
  **Geçici çözüm**: hedefteki şemanın kaynaktaki şemayla eşleştiğinden emin olun. Şemayı geçirme hakkında ayrıntılı bilgi için [Azure PostgreSQL çevrimiçi geçiş belgelerine](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)bakın.

- **Hata:** {Database} kaynak veritabanı boş.

  **Kısıtlama**: kaynak veritabanı boş olduğunda bu hata oluşur. Bunun nedeni büyük olasılıkla kaynak olarak yanlış veritabanını seçmiş olmanızdır.
  **Geçici çözüm**: geçiş için seçtiğiniz kaynak veritabanını çift işaretleyin ve sonra yeniden deneyin.

- **Hata:** Hedef veritabanı {Database} boş. Lütfen şemayı geçirin.

  **Kısıtlama**: hedef veritabanında şema olmadığında bu hata oluşur. Hedefteki şemanın kaynaktaki şemayla eşleştiğinden emin olun.
  **Geçici çözüm**: hedefteki şemanın kaynaktaki şemayla eşleştiğinden emin olun. Şemayı geçirme hakkında ayrıntılı bilgi için [Azure PostgreSQL çevrimiçi geçiş belgelerine](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)bakın.

## <a name="other-limitations"></a>Diğer sınırlamalar

- Veritabanı adı noktalı virgül (;)) içeremez.
- Küme ayraçları açma ve kapatma {} olan parola dizesi desteklenmiyor. Bu sınırlama, kaynak PostgreSQL bağlantısı ve PostgreSQL için Azure veritabanı hedef için geçerlidir.
- Yakalanan bir tablo, birincil anahtara sahip olmalıdır. Bir tablonun birincil anahtarı yoksa, kayıt SILME ve GÜNCELLEŞTIRME işlemlerinin sonucu öngörülemeyen olur.
- Birincil anahtar segmentinin güncelleştirilmesi yok sayılır. Böyle durumlarda, bu tür bir güncelleştirmenin uygulanması, hedef tarafından herhangi bir satırı güncelleştirmediğiniz bir güncelleştirme olarak tanımlanır ve özel durumlar tablosuna yazılmış bir kayıtla sonuçlanır.
- Aynı ada sahip, ancak farklı bir Case (örn. Table1, TABLE1 ve Table1) birden çok tablo geçişi öngörülemeyen davranışlara neden olabilir ve bu nedenle desteklenmez.
- [Oluştur | işlemini değiştirme DEĞIŞTIR | DROP] tablo DDLs, bir iç işlev/yordam gövde bloğunda veya diğer iç içe yapılar içinde tutulmadığı müddetçe desteklenir. Örneğin, aşağıdaki değişiklik yakalanmayacak:

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- KESME işlemlerinde değişiklik işleme (sürekli eşitleme) desteklenmiyor. Bölümlenmiş tabloların geçirilmesi desteklenmez. Bölümlenmiş bir tablo algılandığında, aşağıdaki işlemler gerçekleşir:

  - Veritabanı, üst ve alt tabloların listesini rapor eder.
  - Tablo, seçili tablolarla aynı özelliklere sahip olan normal tablo olarak hedefte oluşturulur.
  - Kaynak veritabanındaki üst tablo, alt tablolarıyla aynı birincil anahtar değerine sahipse, "yinelenen anahtar" hatası oluşturulur.

- DMS 'de, tek bir geçiş etkinliğinde geçirilecek veritabanlarının sınırı dördü.
