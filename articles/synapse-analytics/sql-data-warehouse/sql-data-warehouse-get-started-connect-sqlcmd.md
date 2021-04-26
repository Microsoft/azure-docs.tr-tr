---
title: Sqlcmd ile bağlanma
description: Azure SYNAPSE Analytics 'te adanmış bir SQL havuzuna bağlanmak ve sorgu yapmak için sqlcmd komut satırı yardımcı programını kullanın.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f8b4d54585bc70c3ee5f24846e216f75e985cf84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675728"
---
# <a name="connect-to-a-dedicated-sql-pool-in-azure-synapse-analytics-with-sqlcmd"></a>Sqlcmd ile Azure SYNAPSE Analytics 'te adanmış bir SQL havuzuna bağlanma

> [!div class="op_single_selector"]
>
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Adanmış bir SQL havuzuna bağlanmak ve sorgu eklemek için [sqlcmd] [sqlcmd] komut satırı yardımcı programını kullanın.  

## <a name="1-connect"></a>1. Bağlan

[Sqlcmd] [sqlcmd] kullanmaya başlamak için, komut istemi ' ni açın ve **sqlcmd** öğesini ve ardından adanmış SQL havuzunuzun bağlantı dizesini girin. Bağlantı dizesi için aşağıdaki parametreler gereklidir:

* **Server (-S):**`<`Sunucu Adı`>`.database.windows.net biçiminde belirtilmiş sunucu
* **Veritabanı (-d):** adanmış SQL havuzu adı.
* **Alıntılanmış tanımlayıcıları etkinleştir (-ı):** Adanmış tanımlayıcıların ayrılmış bir SQL havuzu örneğine bağlanması için etkinleştirilmesi gerekir.

SQL Server Kimlik Doğrulamasını kullanmak için kullanıcı adı/parola parametrelerini eklemeniz gerekir:

* **User (-U):**`<`Kullanıcı`>` biçimindeki sunucu kullanıcısı
* **Password (-P):** Kullanıcıyla ilişkili parola.

Örneğin, bağlantı dizeniz aşağıdaki gibi görünebilir:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Azure Active Directory Tümleşik kimlik doğrulamasını kullanmak için Azure Active Directory parametrelerini eklemeniz gerekir:

* **Azure Active Directory Kimlik Doğrulaması (-G):** Kimlik doğrulaması için Azure Active Directory kullanın

Örneğin, bağlantı dizeniz aşağıdaki gibi görünebilir:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Active Directory kullanarak kimlik doğrulaması yapmak için [Azure Active Directory Kimlik Doğrulamasını etkinleştirmeniz](sql-data-warehouse-authentication.md) gerekir.

## <a name="2-query"></a>2. sorgu

Bağlantının ardından desteklenen herhangi bir Transact-SQL deyimini örnekte yayımlayabilirsiniz.  Bu örnekte sorgular etkileşimli modda gönderilir.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Bu sonraki örnekler -Q seçeneği kullanarak veya SQL’i sqlcmd öğesine ekleyerek sorgularınızı toplu iş modunda nasıl çalıştırabileceğinizi gösterir.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Sonraki adımlar

Sqlcmd 'de kullanılabilen seçenekler hakkında daha fazla bilgi için bkz. [sqlcmd belgeleri](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).