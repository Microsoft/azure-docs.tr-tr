---
title: Azure Data Studio ile SYNAPSE SQL 'e bağlanma
description: Azure SYNAPSE Analytics 'te SYNAPSE SQL 'e bağlanmak ve sorgulamak için Azure Data Studio kullanın.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 1529e45bd7f799a727a29c8c2e26f7ed77c4e2a0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565602"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio"></a>Azure Data Studio ile SYNAPSE SQL 'e bağlanma

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Azure SYNAPSE Analytics 'te SYNAPSE SQL 'e bağlanmak ve sorgu eklemek için [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio?view=azure-sqldw-latest&preserve-view=true) kullanabilirsiniz. 

## <a name="connect"></a>Bağlanma

SYNAPSE SQL 'e bağlanmak için Azure Data Studio açın ve **Yeni bağlantı**' yı seçin.

![Azure Data Studio açın](./media/get-started-azure-data-studio/1-start.png)

**Bağlantı türü** olarak **Microsoft SQL Server** seçin.

Bağlantı aşağıdaki parametreleri gerektirir:

* **Sunucu:** Formdaki sunucu `<Azure Synapse workspace name>` -OnDemand.Sql.azuresynapse.net
* **Veritabanı:** Veritabanı adı

> [!NOTE]
> **Sunucusuz SQL havuzu** kullanmak istiyorsanız URL şöyle görünmelidir:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> **ADANMıŞ SQL havuzu** kullanmak istiyorsanız URL şöyle görünmelidir:
>
> - `<Azure Synapse workspace name>`. sql.azuresynapse.net

**Kimlik doğrulama türü** olarak **Windows kimlik doğrulaması**, **Azure Active Directory** veya **SQL oturum açma** seçeneklerini belirleyin.

Kimlik doğrulama türü olarak **SQL oturum açma** 'yı kullanmak için, Kullanıcı adı/parola parametrelerini ekleyin:

* **Kullanıcı:** Formdaki sunucu kullanıcısı `<User>`
* **Parola:** Kullanıcıyla ilişkili parola

Azure Active Directory kullanmak için, gerekli kimlik doğrulama türünü seçmeniz gerekir.

![AAD Kimlik Doğrulaması](./media/get-started-azure-data-studio/3-aad-auth.png)

Aşağıdaki ekran görüntüsünde, **Windows kimlik doğrulaması** Için **Bağlantı ayrıntıları** gösterilmektedir:

![Windows Kimlik Doğrulaması](./media/get-started-azure-data-studio/3-windows-auth.png)

Aşağıdaki ekran görüntüsünde **SQL oturum açma** kullanılarak **Bağlantı ayrıntıları** gösterilmektedir:

![SQL Oturum Açma](./media/get-started-azure-data-studio/2-database-details.png)

Başarılı oturum açma işleminden sonra şu şekilde bir pano görmeniz gerekir: ![ Pano](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Sorgu

Bağlandıktan sonra, örnek olarak desteklenen [Transact-SQL (T-SQL)](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) deyimlerini kullanarak SYNAPSE SQL 'i sorgulama yapabilirsiniz. Başlamak için Pano görünümünden **Yeni sorgu** ' yı seçin.

![Yeni Sorgu](./media/get-started-azure-data-studio/5-new-query.png)

Örneğin, sunucusuz SQL havuzu kullanarak [Parquet dosyalarını sorgulamak](query-parquet-files.md) Için aşağıdaki Transact-SQL ifadesini kullanabilirsiniz:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Sonraki adımlar 
SYNAPSE SQL 'e bağlanmanın diğer yollarını keşfet: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [sqlcmd](get-started-connect-sqlcmd.md)

Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics 'te adanmış BIR SQL havuzunu kullanarak bağlanmak ve verileri sorgulamak için Azure Data Studio kullanın](/sql/azure-data-studio/quickstart-sql-dw).
