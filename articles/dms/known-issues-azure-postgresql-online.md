---
title: "Bilinen sorunlar: PostgreSQL 'ten PostgreSQL için Azure veritabanı 'na çevrimiçi geçişler"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni kullanarak PostgreSQL için Azure veritabanı 'na PostgreSQL 'ten çevrimiçi geçişlerle ilgili bilinen sorunlar ve geçiş sınırlamaları hakkında bilgi edinin.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: fdefcabdda64402610f115832976ec9f7af81b80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258838"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>PostgreSQL ile PostgreSQL için Azure DB 'ye çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları

PostgreSQL 'e yönelik çevrimiçi geçişlerle ilgili bilinen sorunlar ve sınırlamalar, PostgreSQL için Azure veritabanı 'na aşağıdaki bölümlerde açıklanmaktadır.

## <a name="online-migration-configuration"></a>Çevrimiçi geçiş yapılandırması

- Kaynak PostgreSQL sunucusu, 9,4, 9,5, 9,6, 10 veya 11 sürümünü çalıştırıyor olmalıdır. Daha fazla bilgi için bkz. [PostgreSQL veritabanı sürümlerini destekleyen](../postgresql/concepts-supported-versions.md)makale.
- Yalnızca aynı veya daha yüksek bir sürüme geçiş desteklenir. Örneğin, PostgreSQL 9,5 ' den PostgreSQL için Azure veritabanı 9,6 veya 10 ' a geçiş desteklenir, ancak PostgreSQL 11 ' den PostgreSQL 9,6 ' ye geçiş desteklenmez.
- **Kaynak PostgreSQL PostgreSQL. conf** dosyasında mantıksal çoğaltmayı etkinleştirmek için aşağıdaki parametreleri ayarlayın:
  - **wal_level** = mantıksal
  - **max_replication_slots** = [geçiş için en az veritabanı sayısı]; dört veritabanını geçirmek istiyorsanız, değeri en az 4 olarak ayarlayın.
  - **max_wal_senders** = [eşzamanlı olarak çalışan veritabanlarının sayısı]; Önerilen değer 10 ' dur
- Kaynak PostgreSQL pg_hba. conf dosyasına DMS Aracısı IP 'si ekleyin
  1. Azure veritabanı geçiş hizmeti 'nin bir örneğini sağlamayı tamamladıktan sonra DMS IP adresini bir yere unutmayın.
  2. IP adresini pg_hba. conf dosyasına aşağıda gösterildiği gibi ekleyin:

      ```
          host    all    172.16.136.18/10    md5
          host    replication postgres    172.16.136.18/10     md5
      ```

- Kullanıcının, kaynak veritabanını barındıran sunucuda çoğaltma rolü olmalıdır.
- Kaynak ve hedef veritabanı şemaları eşleşmelidir.
- PostgreSQL-Single sunucusu için hedef Azure veritabanında bulunan şemanın yabancı anahtarları olmamalıdır. Yabancı anahtarları bırakmak için aşağıdaki sorguyu kullanın:

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

- PostgreSQL-Single Server için hedef Azure veritabanı şemasında hiçbir tetikleyici olmamalıdır. Hedef veritabanında Tetikleyicileri devre dışı bırakmak için aşağıdakileri kullanın:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="size-limitations"></a>Boyut sınırlamaları
- Tek bir DMS hizmeti kullanarak PostgreSQL 'ten PostgreSQL için Azure DB 'ye kadar 2 TB 'a kadar veri geçirebilirsiniz.
## <a name="datatype-limitations"></a>Veri türü sınırlamaları

  **Kısıtlama**: tablolarda birincil anahtar yoksa, değişiklikler hedef veritabanıyla eşitlenmeyebilir.

  **Geçici çözüm**: geçiş işleminin devam etmesi için geçici olarak tablo için bir birincil anahtar ayarlayın. Veri geçişi tamamlandıktan sonra birincil anahtarı kaldırabilirsiniz.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>AWS RDS PostgreSQL 'ten çevrimiçi geçiş yaparken sınırlamalar

AWS RDS PostgreSQL 'ten PostgreSQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirmeye çalıştığınızda, aşağıdaki hatalarla karşılaşabilirsiniz.

- **Hata**: '{database}' veritabanının '{table}' tablosundaki '{column}' sütununun Varsayılan değeri kaynak ve hedef sunucularda farklı. Değer kaynakta '{value on source}', hedefte ise '{value on target}'.

  **Kısıtlama**: bir sütun şemasındaki varsayılan değer, kaynak ve hedef veritabanları arasında farklı olduğunda bu hata oluşur.
  **Geçici çözüm**: hedefteki şemanın kaynaktaki şemayla eşleştiğinden emin olun. Şemayı geçirme hakkında ayrıntılı bilgi için [Azure PostgreSQL çevrimiçi geçiş belgelerine](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema)bakın.

- **Hata**: '{database}' adlı hedef veritabanında '{number of tables}' tablo varken '{database}' adlı kaynak veritabanında '{number of tables}' tablo var. Kaynak ve hedef veritabanlarındaki tablo sayısı aynı olmalıdır.

  **Sınırlama**: Bu hata, tablo sayısı kaynak ve hedef veritabanları arasında farklıysa oluşur.

  **Geçici çözüm**: hedefteki şemanın kaynaktaki şemayla eşleştiğinden emin olun. Şemayı geçirme hakkında ayrıntılı bilgi için [Azure PostgreSQL çevrimiçi geçiş belgelerine](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema)bakın.

- **Hata:** {Database} kaynak veritabanı boş.

  **Kısıtlama**: kaynak veritabanı boş olduğunda bu hata oluşur. Büyük olasılıkla kaynak olarak yanlış veritabanını seçmiş olmanız gerekir.

  **Geçici çözüm**: geçiş için seçtiğiniz kaynak veritabanını çift işaretleyin ve sonra yeniden deneyin.

- **Hata:** Hedef veritabanı {Database} boş. Lütfen şemayı geçirin.

  **Kısıtlama**: hedef veritabanında şema olmadığında bu hata oluşur. Hedefteki şemanın kaynaktaki şemayla eşleştiğinden emin olun.
  **Geçici çözüm**: hedefteki şemanın kaynaktaki şemayla eşleştiğinden emin olun. Şemayı geçirme hakkında ayrıntılı bilgi için [Azure PostgreSQL çevrimiçi geçiş belgelerine](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema)bakın.

## <a name="other-limitations"></a>Diğer sınırlamalar

- Veritabanı adı noktalı virgül (;)) içeremez.
- Yakalanan bir tablo, birincil anahtara sahip olmalıdır. Bir tablonun birincil anahtarı yoksa, kayıt SILME ve GÜNCELLEŞTIRME işlemlerinin sonucu öngörülemeyen olur.
- Birincil anahtar segmentinin güncelleştirilmesi yok sayılır. Böyle durumlarda, bu tür bir güncelleştirmenin uygulanması, hedef tarafından herhangi bir satırı güncelleştirmediğiniz bir güncelleştirme olarak tanımlanır ve özel durumlar tablosuna yazılmış bir kayıtla sonuçlanır.
- Aynı ada sahip, ancak farklı bir Case (örn. Table1, TABLE1 ve Table1) birden çok tablo geçişi öngörülemeyen davranışlara neden olabilir ve bu nedenle desteklenmez.
- [Oluştur | işlemini değiştirme DEĞIŞTIR | BıRAK | TRUNCATE] tablo DDLs desteklenmiyor.
- Azure veritabanı geçiş hizmeti 'nde tek bir geçiş etkinliği yalnızca en fazla dört veritabanı içerebilir.
- Pg_largeobject tablosunun geçirilmesi desteklenmez. 
