---
title: Kullanıcı tanımlı şemaları kullanma
description: Azure SYNAPSE Analytics 'te adanmış SQL havuzları için çözümler geliştirmek üzere T-SQL Kullanıcı tanımlı şemaları kullanma ipuçları.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3204c77dd076d9aac6eb5a60b489280caefcbf4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460455"
---
# <a name="user-defined-schemas-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzları için Kullanıcı tanımlı şemalar
Bu makale, adanmış SQL havuzunda çözüm geliştirmek için T-SQL Kullanıcı tanımlı şemaları kullanmaya yönelik birkaç ipucu sağlamaya odaklanır.

## <a name="schemas-for-application-boundaries"></a>Uygulama sınırları şemaları

Geleneksel veri ambarları genellikle iş yüküne, etki alanına veya güvenliğe göre uygulama sınırları oluşturmak için ayrı veritabanları kullanır. 

Örnek olarak, geleneksel bir SQL Server veri ambarı hazırlama veritabanı, veri ambarı veritabanı ve bazı Data Mart veritabanlarını içerebilir. Bu topolojide, her veritabanı mimaride bir iş yükü ve güvenlik sınırı olarak çalışır.

Buna karşılık, adanmış bir SQL havuzu tüm veri ambarı iş yükünü tek bir veritabanı içinde çalıştırır. Çapraz veritabanı birleştirmelerine izin verilmez. Adanmış SQL havuzu, ambarın tarafından kullanılan tüm tabloların tek bir veritabanı içinde depolanmasını bekler.

> [!NOTE]
> SQL havuzu herhangi bir türdeki çapraz veritabanı sorgularını desteklemez. Sonuç olarak, bu düzenin faydalarından yararlanan veri ambarı uygulamalarının yeniden gözden geçirilmesi gerekir.
> 
> 

## <a name="recommendations"></a>Öneriler
Aşağıda, Kullanıcı tanımlı şemaları kullanarak iş yüklerini, güvenliği, etki alanını ve işlevsel sınırları birleştirme önerileri verilmiştir:

- Tüm veri ambarı iş yükünüzü çalıştırmak için adanmış bir SQL havuzundaki bir veritabanını kullanın.
- Ayrılmış bir SQL havuzu veritabanı kullanmak için mevcut veri ambarı ortamınızı birleştirin.
- Daha önce veritabanları kullanılarak uygulanan sınırı sağlamak için **Kullanıcı tanımlı şemalardan** yararlanın.

Daha önce Kullanıcı tanımlı şemalar kullanılmazsa, temiz bir tablet görürsünüz. Adanmış SQL havuzu veritabanında kullanıcı tanımlı şemalarınızın temeli olarak eski veritabanı adını kullanın.

Şemalar zaten kullanıldıysa, birkaç seçeneğiniz vardır:

- Eski şema adlarını kaldırın ve yeni bir başlangıç yapın.
- Eski şema adlarını, eski şema adının tablo adına ön bekliyor olarak tutun.
- Eski şema yapısını yeniden oluşturmak için ek bir şemadaki tablo üzerinde görünümler uygulayarak eski şema adlarını koruyun.

> [!NOTE]
> İlk denetleme seçeneği 3 en çekici seçenek gibi görünebilir. Ancak, DEVIL ayrıntılandırıyor. Görünümler, adanmış SQL havuzunda salt okunurdur. Herhangi bir veri veya tablo değişikliğini temel tabloya karşı gerçekleştirilmesi gerekir. Seçenek 3 Ayrıca sisteminizde bir görünüm katmanı da sunar. Mimarinizdeki görünümleri zaten kullanıyorsanız, buna ek bir fikir vermek isteyebilirsiniz.
> 
> 

### <a name="examples"></a>Örnekler:
Veritabanı adlarına göre Kullanıcı tanımlı şemaları Uygula:

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

Eski şema adlarını tablo adına ön bekliyor olarak bırakın. İş yükü sınırı için şemaları kullanın:

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

Görünümleri kullanarak eski şema adlarını tut:

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
> Şema stratejisindeki herhangi bir değişiklik, veritabanı için güvenlik modelinin gözden geçirilmesini gerektirir. Birçok durumda, şema düzeyinde izinler atayarak güvenlik modelini basitleştirebileceksiniz. Daha ayrıntılı izinler gerekiyorsa, veritabanı rollerini kullanabilirsiniz.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).

