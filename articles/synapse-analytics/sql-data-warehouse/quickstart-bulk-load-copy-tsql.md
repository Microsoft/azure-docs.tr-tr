---
title: 'Hızlı başlangıç: tek bir T-SQL ifadesini kullanarak verileri toplu yükleme'
description: COPY ifadesini kullanarak verileri toplu yükleme
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 2b41342ab7d267c37b8e68fdbcaa9d570034ac17
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568446"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Hızlı başlangıç: COPY ifadesini kullanarak verileri toplu yükleme

Bu hızlı başlangıçta, yüksek aktarım hızı veri alımı için basit ve esnek [kopyalama ifadesini](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) kullanarak adanmış SQL havuzunuza verileri toplu olarak yükleyebilirsiniz. KOPYALAMA deyimleri, aşağıdakileri yapmak için işlevler sağlayarak, verileri sorunsuzca ve esnek bir şekilde yüklemeyi sağlayan önerilen yükleme yardımcı programıdır:

- Veri ambarında kesin DENETIM izinlerine gerek duymadan daha düşük ayrıcalıklı kullanıcıların yüklenmesine izin ver
- Ek veritabanı nesneleri oluşturmak zorunda kalmadan yalnızca tek bir T-SQL ifadesiyle yararlanın
- Erişim Imzalarını paylaşma (SAS) kullanarak depolama hesabı anahtarlarını kullanıma açmadan daha hassas bir izin modelinden yararlanın
- ERRORFILE konumu (REJECTED_ROW_LOCATION) için farklı bir depolama hesabı belirtin
- Her bir hedef sütun için varsayılan değerleri özelleştirin ve belirli hedef sütunlara yüklenecek kaynak veri alanlarını belirtin
- CSV dosyaları için özel bir satır Sonlandırıcı belirtin
- CSV dosyaları için kaçış dizesi, alan ve satır sınırlayıcıları
- CSV dosyaları için SQL Server Tarih biçimlerinden yararlanın
- Depolama konumu yolunda joker karakter ve birden çok dosya belirtin

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç, zaten adanmış bir SQL havuzunuz olduğunu varsayar. Adanmış bir SQL havuzu oluşturulmadıysa, [oluşturma ve bağlanma-Portal](create-data-warehouse-portal.md) hızlı başlangıcı ' nı kullanın.

## <a name="set-up-the-required-permissions"></a>Gerekli izinleri ayarlama

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>Hedef tabloyu oluşturma

Bu örnekte, New York TAXI veri kümesinden veri yükleyeceğiz. Tek bir yıl içinde alınan TAXI 'yi temsil eden seyahat adlı bir tablo yükleyeceğiz. Tabloyu oluşturmak için aşağıdakileri çalıştırın:

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>COPY ifadesini Çalıştır

Azure Blob depolama hesabından seyahat tablosuna veri yükleyecek aşağıdaki kopyalama ifadesini çalıştırın.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Yükü izleme

Yükün düzenli olarak aşağıdaki sorguyu çalıştırarak ilerleme yapıp yapmadığını denetleyin:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Sonraki adımlar

- Veri yükleme konusunda en iyi uygulamalar için bkz. [veri yükleme Için En Iyi uygulamalar](./guidance-for-loading-data.md).
- Veri yüklerinizin kaynaklarını yönetme hakkında daha fazla bilgi için bkz. [Iş yükü yalıtımı](./quickstart-configure-workload-isolation-tsql.md).