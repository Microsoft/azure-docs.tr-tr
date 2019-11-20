---
title: Sqlcmd ile bağlanma
description: Azure SQL veri ambarına bağlanmak ve bu ambarı sorgulamak için sqlcmd komut satırı yardımcı programını kullanın.
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
ms.openlocfilehash: e4b432e0be0cdded5089965b9d272aa82e31bd36
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685742"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>sqlcmd ile SQL Data Warehouse'a bağlanma
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Azure SQL veri ambarına bağlanmak ve bu ambarı sorgulamak için [sqlcmd][sqlcmd] komut satırı yardımcı programını kullanın.  

## <a name="1-connect"></a>1. Bağlan
[Sqlcmd][sqlcmd] kullanmaya başlamadan önce komut istemini açın ve **sqlcmd** öğesinden sonra SQL Veri Ambarı veritabanınızın bağlantı dizesini girin. Bağlantı dizesi için aşağıdaki parametreler gereklidir:

* **Server (-S):** `<`Sunucu Adı`>`.database.windows.net biçiminde belirtilmiş sunucu
* **Database (-d):** Veritabanı adı.
* **Tırnak İşaretli Tanımlayıcıları Etkinleştir (-I):** Bir SQL Veri Ambarı örneğine bağlanmak için tırnak işaretli tanımlayıcıların etkinleştirilmesi gerekir.

SQL Server Kimlik Doğrulamasını kullanmak için kullanıcı adı/parola parametrelerini eklemeniz gerekir:

* **User (-U):** `<`Kullanıcı`>` biçimindeki sunucu kullanıcısı
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
> 
> 

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
Sqlcmd 'de kullanılabilen seçenekler hakkında daha fazla bilgi için bkz. [sqlcmd belgeleri][sqlcmd] .

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
