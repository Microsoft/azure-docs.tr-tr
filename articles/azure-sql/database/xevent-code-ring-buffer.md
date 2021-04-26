---
title: XEvent halka arabellek kodu
description: Azure SQL veritabanı 'nda halka arabelleği hedefini kullanarak kolay ve hızlı hale getirilen bir Transact-SQL kod örneği sağlar.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 12/19/2018
ms.openlocfilehash: a646588616b874e40b1ed2a5a0b5e691b075075d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96487312"
---
# <a name="ring-buffer-target-code-for-extended-events-in-azure-sql-database"></a>Azure SQL veritabanı 'nda genişletilmiş olaylar için halka arabelleği hedef kodu
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Sınama sırasında genişletilmiş bir olay için en kolay hızlı bir şekilde bilgi yakalamak ve raporlamak için bir kod örneği istersiniz. Genişletilmiş olay verileri için en kolay hedef, [halka arabelleği hedefidir](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130)).

Bu konuda şu şekilde bir Transact-SQL kod örneği sunulmaktadır:

1. Göstermek üzere verileri içeren bir tablo oluşturur.
2. Var olan genişletilmiş bir olay için bir oturum oluşturur, yani **SqlServer.sql_statement_starting**.

   * Olay, belirli bir Update dize içeren SQL deyimleriyle sınırlıdır: **'% Update tabEmployee% ' gibi bir deyim**.
   * Olayın çıkışını halka arabelleği türünde bir hedefe göndermeyi seçer, yani  **package0.ring_buffer**.
3. Olay oturumunu başlatır.
4. Birkaç basit SQL UPDATE deyimi yayınlar.
5. Halka arabelleğinden olay çıktısını almak için bir SQL SELECT ifadesiyle karşılaşır.

   * **sys.dm_xe_database_session_targets** ve diğer dinamik yönetim görünümleri (DMVs) birleştirilir.
6. Olay oturumunu sonlandırır.
7. , Kaynaklarını serbest bırakmak için halka arabelleği hedefini bırakır.
8. Olay oturumunu ve tanıtım tablosunu bırakır.

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı ve aboneliği [Ücretsiz deneme sürümü](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.
* İçinde tablo oluşturabileceğiniz herhangi bir veritabanı.
  
  * İsteğe bağlı olarak, dakikalar içinde [bir **AdventureWorksLT** demo veritabanı oluşturabilirsiniz](single-database-create-quickstart.md) .
* SQL Server Management Studio (ssms.exe), ideal olarak en son aylık güncelleştirme sürümü.
  En son ssms.exe şuradan indirebilirsiniz:
  
  * [SQL Server Management Studio indirme](/sql/ssms/download-sql-server-management-studio-ssms)başlıklı konu.
  * [İndirmenin doğrudan bağlantısı.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Kod örneği

Çok küçük değişikliklerle, aşağıdaki halka arabelleği kod örneği Azure SQL veritabanı veya Microsoft SQL Server üzerinde çalıştırılabilir. Bu fark, 5. adımdaki FROM yan tümcesinde kullanılan, bazı dinamik yönetim görünümlerinin (DMVs) adında bulunan ' _database ' düğümünün varlığına sahip olur. Örnek:

* sys.dm_xe<strong>_database</strong>_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```

&nbsp;

## <a name="ring-buffer-contents"></a>Halka arabelleği içerikleri

`ssms.exe`Kod örneğini çalıştırmak için kullandık.

Sonuçları görüntülemek için **target_data_XML** sütun başlığı altındaki hücreyi tıkladık.

Ardından sonuçlar bölmesinde, sütun üst bilgisi **target_data_XML** altındaki hücreyi tıkladık. Bu, sonuç hücresinin içeriğinin XML olarak görüntülendiği ssms.exe başka bir dosya sekmesi oluşturulmasını ister.

Çıktı aşağıdaki blokta gösterilmiştir. Uzun görünüyor ancak yalnızca iki **\<event>** öğe.

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```

### <a name="release-resources-held-by-your-ring-buffer"></a>Zil arabelleğinde tutulan yayın kaynakları

Halka arabelleğinden işiniz bittiğinde, bunu kaldırabilir ve aşağıdaki gibi bir **değişiklik** veren kaynaklarını serbest bırakabilirsiniz:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```

Olay oturumunuzun tanımı güncelleştirildi, ancak bırakılmadı. Daha sonra, bir halka arabelleğinin başka bir örneğini olay oturumunuza ekleyebilirsiniz:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```

## <a name="more-information"></a>Daha fazla bilgi

Azure SQL veritabanı 'nda genişletilmiş olaylara yönelik birincil konu:

* Azure SQL veritabanı 'nda farklı olan genişletilmiş olayların bazı yönlerini kapsayan Azure [SQL veritabanı 'Nda genişletilmiş olay konuları](xevent-db-diff-from-svr.md)Microsoft SQL Server karşılaştırması.

Genişletilmiş olaylara yönelik diğer kod örnek konuları aşağıdaki bağlantılarda bulunabilir. Ancak, örnek hedeflerin Azure SQL veritabanı 'na karşı Microsoft SQL Server için her bir örneğe düzenli olarak göz atın. Daha sonra örneği çalıştırmak için küçük değişiklikler gerekip gerekmediğine karar verebilirsiniz.

* Azure SQL veritabanı için kod örneği: [Azure SQL veritabanı 'nda genişletilmiş olaylar Için Olay dosyası hedef kodu](xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->