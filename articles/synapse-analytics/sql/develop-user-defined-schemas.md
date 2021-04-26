---
title: SYNAPSE SQL içindeki kullanıcı tanımlı şemalar
description: Aşağıdaki bölümlerde, Azure SYNAPSE Analytics 'in SYNAPSE SQL özelliği ile çözümler geliştirmek için T-SQL Kullanıcı tanımlı şemaları kullanmaya yönelik çeşitli ipuçları bulacaksınız.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6920d6cd190927747a69fc4dc0f0791278665abe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451698"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>SYNAPSE SQL içindeki kullanıcı tanımlı şemalar

Aşağıdaki bölümlerde, SYNAPSE SQL 'de çözüm geliştirmek için T-SQL Kullanıcı tanımlı şemaları kullanmaya yönelik çeşitli ipuçları bulacaksınız.

## <a name="schemas-for-application-boundaries"></a>Uygulama sınırları şemaları

Geleneksel analiz mimarisi genellikle iş yüküne, etki alanına veya güvenliğe göre uygulama sınırları oluşturmak için ayrı veritabanlarını kullanır. Örneğin, geleneksel bir SQL Server Analiz altyapısı, hazırlama veritabanı, analiz veritabanı ve veri reyonı veritabanları içerebilir. Bu topolojide, her veritabanı mimaride bir iş yükü ve güvenlik sınırı olarak çalışır.

Bunun yerine, SYNAPSE SQL tüm analiz iş yükünü tek bir veritabanı içinde çalıştırır. Çapraz veritabanı birleştirmelerine izin verilmez. SYNAPSE SQL, ambarın tarafından kullanılan tüm tabloların tek bir veritabanı içinde depolanmasını bekler.

> [!NOTE]
> Adanmış SQL havuzları, herhangi bir türdeki çapraz veritabanı sorgularını desteklemez. Sonuç olarak, bu düzenin faydalarından yararlanan analiz uygulamalarının yeniden gözden geçirilmesi gerekir. Sunucusuz SQL havuzu, çapraz veritabanı sorgularını destekler.

## <a name="user-defined-schema-recommendations"></a>Kullanıcı tanımlı şema önerileri

, Kullanıcı tanımlı şemalar kullanarak iş yüklerini, güvenliği, etki alanını ve işlevsel sınırları birleştirme önerileridir:

- Tüm analiz iş yüklerinizi çalıştırmak için tek bir veritabanı kullanın.
- Mevcut analiz ortamınızı tek bir veritabanı kullanacak şekilde birleştirin.
- Daha önce veritabanları kullanılarak uygulanan sınırı sağlamak için **Kullanıcı tanımlı şemalardan** yararlanın.

Daha önce Kullanıcı tanımlı şemalar kullanılmazsa, temiz bir tablet görürsünüz. SYNAPSE SQL veritabanında kullanıcı tanımlı şemalarınızın temeli olarak eski veritabanı adını kullanın.

Şemalar zaten kullanıldıysa, birkaç seçeneğiniz vardır:

- Eski şema adlarını kaldır ve yeni Başlat
- Eski şema adı ' nı tablo adına ön bekliyor olarak tutarak eski şema adlarını koruyun
- Eski şema yapısını yeniden oluşturan ek bir şemadaki tablo üzerinde görünümler uygulayarak eski şema adlarını koruyun.

> [!NOTE]
> İlk denetim sırasında 3. seçenek en çekici seçim gibi görünebilir. Görünümler, SYNAPSE SQL 'de salt okunurdur. Herhangi bir veri veya tablo değişikliğini temel tabloya karşı gerçekleştirilmesi gerekir. Seçenek 3 Ayrıca sisteminizde bir görünüm katmanı da sunar. Mimarinizdeki görünümleri zaten kullanıyorsanız buna ek bir fikir vermek isteyebilirsiniz.
> 
> 

### <a name="examples"></a>Örnekler

Veritabanı adlarına göre Kullanıcı tanımlı şemaları uygulayın.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Eski şema adlarını tablo adına ön bekliyor olarak bırakın. İş yükü sınırı için şemaları kullanın.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Görünümleri kullanarak eski şema adlarını saklayın.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
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
> Şema stratejisindeki herhangi bir değişiklik, veritabanının güvenlik modelinin gözden geçirilmesini gerektirir. Birçok durumda, şema düzeyinde izinler atayarak güvenlik modelini basitleştirebileceksiniz.

Daha ayrıntılı izinler gerekliyse, veritabanı rollerini kullanabilirsiniz. Veritabanı rolleri hakkında daha fazla bilgi için bkz. [veritabanı rollerini ve kullanıcıları yönetme](../../analysis-services/analysis-services-database-users.md) makalesi.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [SYNAPSE SQL geliştirmesine genel bakış](develop-overview.md).
