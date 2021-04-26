---
title: "Bilinen sorunlar: MySQL için Azure veritabanı 'na çevrimiçi geçişler"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti kullanılırken MySQL için Azure veritabanı 'na çevrimiçi geçişlerle ilgili bilinen sorunlar ve geçiş sınırlamaları hakkında bilgi edinin.
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
ms.openlocfilehash: 11659bcbdf77d04c0f4e6f8bc7aca30c716fc924
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606898"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti ile MySQL için Azure DB 'ye yönelik sınırlamalar & çevrimiçi geçiş sorunları

MySQL 'in MySQL için Azure veritabanı 'na çevrimiçi geçişlerle ilişkili bilinen sorunlar ve sınırlamalar aşağıdaki bölümlerde açıklanmıştır.

## <a name="online-migration-configuration"></a>Çevrimiçi geçiş yapılandırması


- Kaynak MySQL Server sürümü Version 5.6.35, 5.7.18 veya üzeri olmalıdır
- MySQL için Azure veritabanı şunları destekler:
  - MySQL Community sürümü
  - InnoDB altyapısı
- Aynı sürüm geçişi. MySQL 5,6 ' i MySQL için Azure veritabanı 5,7 ' e geçirme desteklenmez. MySQL 8,0 veya 'tan geçiş desteklenmez.
- my.ini (Windows) veya My. cnf (Unix) ' de ikili günlüğü etkinleştir
  - Server_id herhangi bir sayıya daha büyük veya eşittir 1 olarak ayarlayın, örneğin, Server_id = 1 (yalnızca MySQL 5,6 için)
  - Set log-bin = \<path> (yalnızca MySQL 5,6 için)
  - Binlog_format = satırı ayarla
  - Expire_logs_days = 5 (yalnızca MySQL 5,6 için önerilir)
- Kullanıcının ReplicationAdmin rolü olmalıdır.
- Kaynak MySQL veritabanı için tanımlanan harmanlamalar, MySQL için hedef Azure veritabanı 'nda tanımlandıklardır.
- MySQL için Azure veritabanı 'ndaki kaynak MySQL veritabanı ile hedef veritabanı arasında şemanın eşleşmesi gerekir.
- MySQL için Azure veritabanı hedef şemasının yabancı anahtarları olmamalıdır. Yabancı anahtarları bırakmak için aşağıdaki sorguyu kullanın:
    ```sql
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
      CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
        KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
        AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Sorgu sonucunda bırakma yabancı anahtarını (ikinci sütun) çalıştırın.
- MySQL için Azure veritabanı hedef şemasının hiçbir tetikleyicisi olmamalıdır. Tetikleyicileri hedef veritabanında bırakmak için:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Veri türü sınırlamaları

- **Kısıtlama**: kaynak MySQL VERITABANıNDA bir JSON veri türü varsa, sürekli eşitleme sırasında geçiş başarısız olur.

    **Geçici çözüm**: JSON veri türünü, kaynak MySQL veritabanında orta metin veya LONGTEXT olarak değiştirin.

- **Kısıtlama**: tablolarda birincil anahtar yoksa sürekli eşitleme başarısız olur.

    **Geçici çözüm**: geçiş işleminin devam etmesi için geçici olarak tablo için bir birincil anahtar ayarlayın. Veri geçişi tamamlandıktan sonra birincil anahtarı kaldırabilirsiniz.

## <a name="lob-limitations"></a>LOB sınırlamaları

Büyük nesne (LOB) sütunları, boyutu büyük büyüyerek kullanılan sütunlardır. MySQL için, orta metin, LONGTEXT, blob, düz blob, LONGBLOB vb., LOB 'un bazı veri türlerine yöneliktir.

- **Sınırlama**: lob veri türleri birincil anahtar olarak kullanılıyorsa, geçiş başarısız olur.

    **Geçici çözüm**: birincil anahtarı diğer veri TÜRLERI veya LOB olmayan sütunlarla değiştirin.

- **Kısıtlama**: büyük nesne (LOB) sütununun uzunluğu "LOB boyutunu sınırla" parametresinden daha büyükse (64 KB 'den büyük olmamalıdır), veriler hedefte kesilebilir. Bu sorguyu kullanarak LOB sütununun uzunluğunu kontrol edebilirsiniz:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Geçici çözüm**: 64 KB 'den büyük lob nesneniz varsa, "sınırsız lob boyutuna izin ver" parametresini kullanın. "Sınırsız LOB boyutuna Izin ver" parametresi kullanılarak geçişler "LOB boyutunu sınırla" parametresi kullanılarak geçişten daha yavaş olacağını unutmayın.

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>AWS RDS MySQL 'ten çevrimiçi geçiş yapma sınırlamaları

AWS RDS MySQL 'ten MySQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirmeye çalıştığınızda, aşağıdaki hatalarda gelebiliriz.

- **Hata:** ' {0} ' Veritabanında, hedefte yabancı anahtar (ler) vardır. Hedefi düzeltin ve yeni bir veri geçişi etkinliği başlatın. Yabancı anahtar (ler) i listelemek için aşağıdaki betiği hedefle yürütün

  **Kısıtlama**: şemanızda yabancı anahtarlarınız varsa, geçişin ilk yükü ve sürekli eşitlenmesi başarısız olur.
  **Geçici çözüm**: doğrudan dış anahtar betiği çıkarmak ve yabancı anahtar betiği eklemek için MySQL çalışma ekranında aşağıdaki betiği yürütün:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Hata:** ' {0} ' Veritabanı sunucuda yok. Sağlanan MySQL kaynak sunucusu büyük/küçük harfe duyarlı. Lütfen veritabanı adını denetleyin.

  **Kısıtlama**: bir MySQL veritabanını komut satırı ARABIRIMI (CLI) kullanarak Azure 'a geçirirken, kullanıcılar bu hataya gelebilir. Hizmet, kaynak sunucuda veritabanını bulamadı, bu durum yanlış veritabanı adı sağlamış olabilirsiniz veya listelenen sunucuda veritabanı yok olabilir. Note veritabanı adları büyük/küçük harfe duyarlıdır.

  **Geçici çözüm**: tam veritabanı adını girip yeniden deneyin.

- **Hata:** ' {Database} ' veritabanında aynı ada sahip tablolar var. MySQL için Azure Veritabanı, büyük/küçük harfe duyarlı tabloları desteklemez.

  **Kısıtlama**: kaynak veritabanında aynı ada sahip iki tablonuz olduğunda bu hata oluşur. MySQL için Azure veritabanı, büyük/küçük harfe duyarlı tabloları desteklemez.

  **Geçici çözüm**: tablo adlarını benzersiz olacak şekilde güncelleştirip yeniden deneyin.

- **Hata:** Hedef veritabanı {Database} boş. Lütfen şemayı geçirin.

  **Sınırlama**: Bu hata, MySQL Için Azure veritabanı veritabanının gerekli şemasına sahip olmadığı durumlarda oluşur. Hedefinizdeki verilerin geçirilmesini sağlamak için şema geçişi gereklidir.

  **Geçici çözüm**: şemayı kaynak veritabanından hedef veritabanına [geçirin](./tutorial-mysql-azure-mysql-online.md#migrate-the-sample-schema) .

## <a name="other-limitations"></a>Diğer sınırlamalar

- Parola dizesinin başındaki ve sonundaki küme ayracı {} açan ve kapatan bir parola dizesi desteklenmiyor. Bu sınırlama, hem kaynak MySQL 'e bağlanmak hem de MySQL için Azure veritabanı 'na yöneliktir.
- Aşağıdaki DDLs 'ler desteklenmez:
  - Tüm bölüm DDLs 'Leri
  - Tabloyu bırak
  - Tabloyu yeniden adlandır
- *Alter table <table_name> sütun ekle <column_name>* deyimini kullanarak tablonun başına veya ortasına sütun ekleme desteklenmez. *Alter table <table_name sütun ekle <column_name>* tablonun sonundaki sütunu ekler>.
- Yalnızca sütun verilerinin bir kısmında oluşturulan dizinler desteklenmez. Aşağıdaki ifade, yalnızca sütun verilerinin bir kısmını kullanarak bir dizin oluşturan bir örnektir:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- Azure veritabanı geçiş hizmeti 'nde, tek bir geçiş etkinliğinde geçirilecek veritabanlarının sınırı dördü olur.

- Azure DMS, üst tabloda bir satır silindiğinde veya güncelleştirilirse alt tablodaki eşleşen bir satırı otomatik olarak silmeye veya güncelleştirmeye yardımcı olan CASCADE başvuru eylemini desteklemez. Daha fazla bilgi için MySQL belgelerinde [yabancı anahtar kısıtlamaları](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)makalesinin başvurusal Eylemler bölümüne bakın. Azure DMS, ilk veri yükü sırasında hedef veritabanı sunucusunda yabancı anahtar kısıtlamalarını bırakmayı gerektirir ve başvurusal eylemleri kullanamazsınız. İş yükünüz bu başvuru eylemi aracılığıyla ilişkili bir alt tablonun güncelleştirilmesine bağımlıysa bunun yerine bir [döküm ve geri yükleme](../mysql/concepts-migrate-dump-restore.md) gerçekleştirmenizi öneririz. 

- **Hata:** Satır boyutu çok büyük (> 8126). Bazı sütunların metın veya BLOBUN değiştirilmesi yardımcı olabilir. Geçerli satır biçiminde, 0 baytlık BLOB ön eki satır içi olarak depolanır.

  **Sınırlama**: Bu hata, InnoDB depolama altyapısını kullanarak MySQL Için Azure veritabanı 'na geçiş yaparken ve tablo satır boyutu çok büyükse (>8126 bayt) oluşur.

  **Geçici çözüm**: satır boyutu 8126 bayttan büyük olan tablonun şemasını güncelleştirin. Veriler kırpılabilmesi için katı modun değiştirilmesini önermiyoruz. Page_size değiştirme desteklenmiyor.