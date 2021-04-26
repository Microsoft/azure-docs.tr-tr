---
title: Azure veritabanı geçiş hizmeti araçları matrisi
description: Veritabanlarını geçirmek ve geçiş sürecinin çeşitli aşamalarını desteklemek için kullanılabilen hizmetler ve araçlar hakkında bilgi edinin.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: reference
ms.date: 03/03/2020
ms.openlocfilehash: 07cf2695f4d6256e717c7ce1a456d32b25c34ec9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643752"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Veri geçişi senaryolarında kullanılabilen hizmetler ve araçlar

Bu makalede çeşitli veritabanı ve veri geçişi senaryolarında ve özel görevlerde size yardımcı olmak için kullanabileceğiniz Microsoft ve üçüncü taraf hizmet ve araçların bir matrisi sunulmaktadır.

Aşağıdaki tablolar, veri geçişini başarıyla planlamak ve çeşitli aşamaları gerçekleştirmek için kullanabileceğiniz Hizmetleri ve araçları belirler.

> [!NOTE]
> Aşağıdaki tablolarda, yıldız işareti (*) ile işaretlenen öğeler üçüncü taraf araçları temsil eder.

## <a name="business-justification-phase"></a>İş gerekçe aşaması

| Kaynak | Hedef | Bulmak<br/>Envanter | Hedef ve SKU<br/>Önerilen | TCO/ROı ve<br/>İş örneği |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [HARITA araç seti](/previous-versions//bb977556(v=technet.10))<br/>[Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview)<br/>[Bulut Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [TCO Hesaplayıcı](https://azure.microsoft.com/pricing/tco/calculator/) |
 SQL Server | Azure SQL DB MI | [HARITA araç seti](/previous-versions//bb977556(v=technet.10))<br/>[Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview)<br/>[Bulut Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [TCO Hesaplayıcı](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure SQL VM | [HARITA araç seti](/previous-versions//bb977556(v=technet.10))<br/>[Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize](https://www.cloudamize.com/) | [Bulut Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [TCO Hesaplayıcı](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure Synapse Analytics | [HARITA araç seti](/previous-versions//bb977556(v=technet.10))<br/>[Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize](https://www.cloudamize.com/) |  | [TCO Hesaplayıcı](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS SQL | Azure SQL DB, mı, VM |  | [DMA](/sql/dma/dma-overview) | [TCO Hesaplayıcı](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Azure SQL DB, mı, VM | [HARITA araç seti](/previous-versions//bb977556(v=technet.10))<br/>[Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Migvizörü *](https://www.migvisor.com/) |  |
| Oracle | Azure Synapse Analytics | [HARITA araç seti](/previous-versions//bb977556(v=technet.10))<br/>[Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Oracle | PostgreSQL için Azure DB-<br/>Tek sunucu | [HARITA araç seti](/previous-versions//bb977556(v=technet.10))<br/>[Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| MongoDB | Cosmos DB | [Cloudamize](https://www.cloudamize.com/) | [Cloudamize](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, mı, VM | [Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Bulut Atlas *](https://www.unifycloud.com/cloud-migration-tool/) | [TCO Hesaplayıcı](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | MySQL için Azure DB | [Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/) |  | [TCO Hesaplayıcı](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | MySQL için Azure DB |  |  | [TCO Hesaplayıcı](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | PostgreSQL için Azure DB-<br/>Tek sunucu | [Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/) |  | [TCO Hesaplayıcı](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS PostgreSQL | PostgreSQL için Azure DB-<br/>Tek sunucu |  |  | [TCO Hesaplayıcı](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Azure SQL DB, mı, VM | [Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Access | Azure SQL DB, mı, VM | [Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Sybase-SAP ATıCı | Azure SQL DB, mı, VM | [Azure Geçişi](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Sybase-SAP IQ | Azure SQL DB, mı, VM |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Geçiş öncesi aşaması

| Kaynak | Hedef | Uygulama verileri erişimi<br/>Katman değerlendirmesi | Veritabanı<br/>Değerlendirme | Performans<br/>Değerlendirme |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [Davmt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview)<br/>[Bulut Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB MI | [Davmt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview)<br/>[Bulut Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Azure SQL VM | [Davmt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview)<br/>[Bulut Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Azure Synapse Analytics | [DAVMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) |  |  |
| RDS SQL | Azure SQL DB, mı, VM | [Davmt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Azure SQL DB, mı, VM | [Davmt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) dili | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Oracle | Azure Synapse Analytics | [Davmt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) dili | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Oracle | PostgreSQL için Azure DB-<br/>Tek sunucu |  | [Ora2Pg*](http://ora2pg.darold.net/start.html) |  |
| MongoDB | Cosmos DB |  | [Cloudamize](https://www.cloudamize.com/) | [Cloudamize](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, mı, VM | [Davmt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) dili | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Bulut Atlas *](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | MySQL için Azure DB |  |  |  |
| RDS MySQL | MySQL için Azure DB |  |  |  |
| PostgreSQL | PostgreSQL için Azure DB-<br/>Tek sunucu |  |  |  |
| RDS PostgreSQL | PostgreSQL için Azure DB-<br/>Tek sunucu |  |  |  |
| DB2 | Azure SQL DB, mı, VM | [Davmt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) dili | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Access | Azure SQL DB, mı, VM |  | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Sybase-SAP ATıCı | Azure SQL DB, mı, VM | [Davmt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant) dili | [SSMA](/sql/ssma/sql-server-migration-assistant) |  |
| Sybase-SAP IQ | Azure SQL DB, mı, VM |  | |  |
| | | | | |

## <a name="migration-phase"></a>Geçiş aşaması

| Kaynak | Hedef | Şema | Veriler<br/>Çevrimdışı | Veriler<br/>Çevrimiç |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [DMA](/sql/dma/dma-overview)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL DB MI | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL VM | [DMA](/sql/dma/dma-overview)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure Synapse Analytics |  |  |  |
| RDS SQL | Azure SQL DB, mı, VM | [DMA](/sql/dma/dma-overview) | [DMA](/sql/dma/dma-overview)<br/>[DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure SQL DB, mı, VM | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[SharePlex *](https://www.quest.com/products/shareplex/)<br/>[Ispirer](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[SharePlex *](https://www.quest.com/products/shareplex/)<br/>[Ispirer](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex *](https://www.quest.com/products/shareplex/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure Synapse Analytics | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Ispirer](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Ispirer](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex *](https://www.quest.com/products/shareplex/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | PostgreSQL için Azure DB-<br/>Tek sunucu | [Ispirer](https://www.ispirer.com/solutions) | [Ispirer](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/) |
| MongoDB | Cosmos DB | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[İmansıs verileri *](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[İmansıs verileri *](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[İmansıs verileri *](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [İmansıs verileri *](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) | [İmansıs verileri *](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) | [İmansıs verileri *](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/talena-inc.talena-solution-template?tab=Overview) |
| MySQL | Azure SQL DB, mı, VM | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Ispirer](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Ispirer](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | MySQL için Azure DB | [MySQL dökümü *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | MySQL için Azure DB | [MySQL dökümü *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | PostgreSQL için Azure DB-<br/>Tek sunucu | [SAYFA dökümü *](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS PostgreSQL | PostgreSQL için Azure DB-<br/>Tek sunucu | [SAYFA dökümü *](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Azure SQL DB, mı, VM | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Ispirer](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Ispirer](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Access | Azure SQL DB, mı, VM | [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) | [SSMA](/sql/ssma/sql-server-migration-assistant) |
| Sybase-SAP ATıCı | Azure SQL DB, mı, VM | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Ispirer](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant)<br/>[Ispirer](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Anlık ileti *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Sybase-SAP IQ | Azure SQL DB, mı, VM | [Ispirer](https://www.ispirer.com/solutions) | [Ispirer](https://www.ispirer.com/solutions) | |
| | | | | |

## <a name="post-migration-phase"></a>Geçiş sonrası aşaması

| Kaynak | Hedef | İyileştirme |
| --- | --- | --- |
| SQL Server | Azure SQL DB | [Bulut Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB MI | [Bulut Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Azure SQL VM | [Bulut Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Azure Synapse Analytics |  |
| RDS SQL | Azure SQL DB, mı, VM |  |
| Oracle | Azure SQL DB, mı, VM |  |
| Oracle | Azure Synapse Analytics |  |
| Oracle | PostgreSQL için Azure DB-<br/>Tek sunucu |  |
| MongoDB | Cosmos DB | [Cloudamize](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Azure SQL DB, mı, VM |  |
| MySQL | MySQL için Azure DB |  |
| RDS MySQL | MySQL için Azure DB |  |
| PostgreSQL | PostgreSQL için Azure DB-<br/>Tek sunucu |  |
| RDS PostgreSQL | PostgreSQL için Azure DB-<br/>Tek sunucu |  |
| DB2 | Azure SQL DB, mı, VM |  |
| Access | Azure SQL DB, mı, VM |  |
| Sybase-SAP ATıCı | Azure SQL DB, mı, VM |  |
| Sybase-SAP IQ | Azure SQL DB, mı, VM |  |
| | | |

## <a name="next-steps"></a>Sonraki adımlar

Azure veritabanı geçiş hizmeti 'ne genel bakış için [Azure veritabanı geçiş hizmeti nedir](dms-overview.md)makalesine bakın.