---
title: Kullanıcı tanımlı şemaları kullanma
description: Azure SQL veri ambarı 'nda çözüm geliştirmek için T-SQL Kullanıcı tanımlı şemaları kullanma ipuçları.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 697bffa36e9b208c1a027654df81fb356ddfc8ed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685810"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>SQL veri ambarı 'nda Kullanıcı tanımlı şemaları kullanma
Azure SQL veri ambarı 'nda çözüm geliştirmek için T-SQL Kullanıcı tanımlı şemaları kullanma ipuçları.

## <a name="schemas-for-application-boundaries"></a>Uygulama sınırları şemaları

Geleneksel veri ambarları genellikle iş yüküne, etki alanına veya güvenliğe göre uygulama sınırları oluşturmak için ayrı veritabanları kullanır. Örneğin, geleneksel bir SQL Server veri ambarı hazırlama veritabanı, veri ambarı veritabanı ve bazı Data Mart veritabanlarını içerebilir. Bu topolojide her veritabanı mimaride bir iş yükü ve güvenlik sınırı olarak çalışır.

Buna karşılık, SQL veri ambarı tüm veri ambarı iş yükünü tek bir veritabanı içinde çalıştırır. Çapraz veritabanı birleştirmelerine izin verilmez. Bu nedenle SQL veri ambarı, ambarın tarafından kullanılan tüm tabloların tek bir veritabanı içinde depolanmasını bekler.

> [!NOTE]
> SQL veri ambarı herhangi bir türdeki çapraz veritabanı sorgularını desteklemez. Sonuç olarak, bu düzenin faydalarından yararlanan veri ambarı uygulamalarının yeniden gözden geçirilmesi gerekir.
> 
> 

## <a name="recommendations"></a>Öneriler
Bunlar, Kullanıcı tanımlı şemalar kullanarak iş yüklerini, güvenliği, etki alanını ve işlevsel sınırları birleştirme önerileridir

1. Tüm veri ambarı iş yükünüzü çalıştırmak için bir SQL veri ambarı veritabanı kullanın
2. Mevcut veri ambarı ortamınızı tek bir SQL veri ambarı veritabanı kullanacak şekilde birleştirin
3. Daha önce veritabanları kullanılarak uygulanan sınırı sağlamak için **Kullanıcı tanımlı şemalardan** yararlanın.

Daha önce Kullanıcı tanımlı şemalar kullanılmazsa, temiz bir tablet görürsünüz. SQL veri ambarı veritabanında kullanıcı tanımlı şemalarınızın temeli olarak eski veritabanı adını kullanmanız yeterlidir.

Şemalar zaten kullanıldıysa, birkaç seçeneğiniz vardır:

1. Eski şema adlarını kaldır ve yeni Başlat
2. Eski şema adlarını, eski şema adını tablo adına ön bekliyor olarak bekletme
3. Eski şema yapısını yeniden oluşturmak için ek bir şemadaki tablo üzerinde görünümler uygulayarak eski şema adlarını koruyun.

> [!NOTE]
> İlk denetleme seçeneği 3 en çekici seçenek gibi görünebilir. Ancak, DEVIL ayrıntılandırıyor. Görünümler yalnızca SQL veri ambarı 'nda salt okunurdur. Herhangi bir veri veya tablo değişikliğini temel tabloya karşı gerçekleştirilmesi gerekir. Seçenek 3 Ayrıca sisteminizde bir görünüm katmanı da sunar. Mimarinizdeki görünümleri zaten kullanıyorsanız, buna ek bir fikir vermek isteyebilirsiniz.
> 
> 

### <a name="examples"></a>Örnekler:
Veritabanı adlarına göre Kullanıcı tanımlı şemaları uygulama

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Eski şema adlarını tablo adına ön bekleyen olarak tutun. İş yükü sınırı için şemaları kullanın.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Görünümleri kullanarak eski şema adlarını koruma

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Şema stratejisindeki herhangi bir değişiklik, veritabanı için güvenlik modelinin gözden geçirilmesini gerektirir. Çoğu durumda, şema düzeyinde izinler atayarak güvenlik modelini basitleştirebileceksiniz. Daha ayrıntılı izinler gerekiyorsa, veritabanı rollerini kullanabilirsiniz.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).

