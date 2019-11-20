---
title: Azure SQL veritabanı 'na çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları hakkında makale | Microsoft Docs
description: Azure SQL veritabanı 'na çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları hakkında bilgi edinin.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/27/2019
ms.openlocfilehash: 7cd8b7c2accae097c971aec4b92cf38ed5d3af08
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561506"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Azure SQL veritabanı 'na çevrimiçi geçişlerle ilgili bilinen sorunlar/geçiş sınırlamaları

Azure SQL veritabanı 'na SQL Server çevrimiçi geçişlerle ilişkili bilinen sorunlar ve sınırlamalar aşağıda açıklanmaktadır.

> [!IMPORTANT]
> Azure SQL veritabanı 'na SQL Server çevrimiçi geçişlerde, SQL_variant veri türlerinin geçirilmesi desteklenmez.

### <a name="migration-of-temporal-tables-not-supported"></a>Zamana bağlı tabloların geçirilmesi desteklenmez

**Belirti**

Kaynak veritabanınız bir veya daha fazla zamana bağlı tablodan oluşuyorsa, veritabanı geçişiniz "tam veri yükleme" işlemi sırasında başarısız olur ve aşağıdaki iletiyi görebilirsiniz:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Zamana bağlı tablo hataları örneği](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Geçici çözüm**

Aşağıdaki adımları kullanın.

1. Aşağıdaki sorguyu kullanarak kaynak şemanızda zamana bağlı tabloları bulun.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Geçiş için tabloları belirttiğiniz **geçiş ayarlarını yapılandır** dikey penceresinde bu tabloları hariç tutun.

3. Geçiş etkinliğini yeniden çalıştırın.

**Kaynakları**

Daha fazla bilgi için bkz. [geçici tablolar](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017)makalesi.

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Tabloların geçirilmesi HierarchyId veri türüne sahip bir veya daha fazla sütun içeriyor

**Belirti**

"Tam veri yükleme" işlemi sırasında "ntext, HierarchyId ile uyumlu değil" öneren bir SQL özel durumu görebilirsiniz:

![HierarchyId hataları örneği](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Geçici çözüm**

Aşağıdaki adımları kullanın.

1. Aşağıdaki sorguyu kullanarak HierarchyId veri türüne sahip sütunları içeren Kullanıcı tablolarını bulun.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Geçiş için tabloları belirttiğiniz **geçiş ayarlarını yapılandır** dikey penceresinde bu tabloları hariç tutun.

3. Geçiş etkinliğini yeniden çalıştırın.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>"Tam veri yükleme" veya "artımlı veri eşitleme" sırasında şemadaki etkin tetikleyicilerle çeşitli bütünlük ihlalleriyle geçiş hataları

**Geçici çözüm**

Aşağıdaki adımları kullanın.

1. Aşağıdaki sorguyu kullanarak kaynak veritabanında Şu anda etkin olan Tetikleyicileri bulun:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. [Tetikleyici devre dışı bırak (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017)makalesinde belirtilen adımları kullanarak kaynak veritabanınızdaki Tetikleyicileri devre dışı bırakın.

3. Geçiş etkinliğini yeniden çalıştırın.

### <a name="support-for-lob-data-types"></a>LOB veri türleri için destek

**Belirti**

Büyük nesne (LOB) sütununun uzunluğu 32 KB 'tan büyükse, veriler hedefte kesilebilir. Aşağıdaki sorguyu kullanarak LOB sütununun uzunluğunu kontrol edebilirsiniz:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Geçici çözüm**

32 KB 'den büyük bir LOB sütununuz varsa [Azure veritabanı geçişleri sorun](mailto:AskAzureDatabaseMigrations@service.microsoft.com)' nın mühendislik ekibine başvurun.

### <a name="issues-with-timestamp-columns"></a>Zaman damgası sütunları ile ilgili sorunlar

**Belirti**

Azure veritabanı geçiş hizmeti kaynak zaman damgası değerini geçirmez; Bunun yerine, Azure veritabanı geçiş hizmeti hedef tabloda yeni bir zaman damgası değeri oluşturur.

**Geçici çözüm**

Kaynak tabloda depolanan tam zaman damgası değerini geçirmek için Azure veritabanı geçiş hizmeti 'ne ihtiyacınız varsa [Azure veritabanı geçişleri sorun](mailto:AskAzureDatabaseMigrations@service.microsoft.com)' nda mühendislik ekibine başvurun.

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Veri geçiş hataları veritabanı ayrıntılı durum dikey penceresinde ek ayrıntılar sağlamaz

**Belirti**

Veritabanları ayrıntıları durum görünümünde geçiş hatalarıyla karşılaşdığınızda, üstteki şeritte **veri geçiş hataları** bağlantısını seçtiğinizde geçiş hatalarına özgü ek ayrıntılar sağlayamayabilir.

![veri geçiş hataları ayrıntı yok örnek](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Geçici çözüm**

Belirli hata ayrıntılarına ulaşmak için aşağıdaki adımları kullanın.

1. Geçiş etkinliği ekranını göstermek için veritabanı ayrıntılı durum dikey penceresini kapatın.

     ![geçiş etkinliği ekranı](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Geçiş hatalarını gidermenize yardımcı olacak belirli hata iletilerini görüntülemek için **hata ayrıntılarına bakın** ' ı seçin.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Coğrafya veri türü SQLDB çevrimiçi geçişte desteklenmez

**Belirti**

Geçiş şu metni içeren bir hata iletisiyle başarısız olur:

     “** encountered a fatal error”, "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode."

**Geçici çözüm**

Azure veritabanı geçiş hizmeti, çevrimiçi geçişler için Azure SQL veritabanı 'na çevrimdışı geçişlere yönelik Coğrafya veri türünü destekleirken, Coğrafya veri türü desteklenmez. Bu veritabanının çevrimiçi geçişi için Azure veritabanı geçiş hizmeti 'ni kullanmayı denemeden önce, kaynak veri türünü desteklenen bir tür olarak değiştirmek için alternatif yöntemleri deneyin.

### <a name="supported-editions"></a>Desteklenen sürümler

**Belirti**

Geçiş şu metni içeren bir hata iletisiyle başarısız olur:

    Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].

**Geçici çözüm**

Azure veritabanı geçiş hizmeti kullanılarak Azure SQL veritabanı 'na çevrimiçi geçiş desteği yalnızca Enterprise, Standard ve Developer sürümlerini genişletir. Geçiş işlemine başlamadan önce desteklenen bir sürüm kullandığınızdan emin olun.
