---
title: Purview bağlayıcısına genel bakış
description: Bu makalede, purview 'da desteklenen farklı veri depoları ve işlevleri özetlenmektedir
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 08b22af8743082bab1d547205e51917cb9d92a11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695779"
---
# <a name="supported-data-stores"></a>Desteklenen veri depoları

Takip görünümü aşağıdaki veri depolarını destekler. Desteklenen özellikleri ve Ayrıntılar ' daki ilgili konfigürasyonları öğrenmek için her bir veri deposuna tıklayın.

## <a name="purview-data-sources"></a>Veri kaynaklarını takip edin

|**Kategori**|  **Veri deposu**  |**Meta veri ayıklama**|**Tam tarama**|**Artımlı tarama**|**Kapsamlı tarama**|**Sınıflandırma**|**Köken**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Depolama](register-scan-azure-blob-storage-source.md)| Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Veri Gezgini](register-scan-azure-data-explorer.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Data Lake Storage 2. Nesil](register-scan-adls-gen2.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure SQL Veritabanı](register-scan-azure-sql-database.md)|Yes| Yes| Hayır| Yes| Yes| Yes|
||[Azure SQL veritabanı yönetilen örneği](register-scan-azure-sql-database-managed-instance.md)|Yes| Yes| Hayır| Yes| Yes| Yes|
||[Azure Synapse Analytics (eski adı SQL DW)](register-scan-azure-synapse-analytics.md)|Yes| Yes| Hayır| Yes| Yes| Yes|
|Veritabanı|[Oracle DB](register-scan-oracle-source.md)|Yes| Yes| Hayır| Hayır| Hayır| Yes|
||[SQL Server](register-scan-on-premises-sql-server.md)|Yes| Yes| Hayır| Yes| Yes| Yes|
||[Teradata](register-scan-teradata-source.md)|Yes| Yes| Hayır| Hayır| Hayır| Yes|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Yes| Yes| Hayır| Hayır| Hayır| Yes|
|Hizmetler ve uygulamalar|[SAP ECC](register-scan-sapecc-source.md)|Yes| Yes| Hayır| Yes| Yes| Yes|
||[SAP S4HANA](register-scan-saps4hana-source.md)|Yes| Yes| Hayır| Yes| Yes| Evet|

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Blob depolama kaynağını kaydetme ve tarama](register-scan-azure-blob-storage-source.md)