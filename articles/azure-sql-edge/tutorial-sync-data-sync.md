---
title: SQL Data Sync kullanarak Azure SQL Edge 'ten veri eşitleme
description: Azure SQL Data Sync kullanarak Azure SQL Edge 'ten veri eşitleme hakkında bilgi edinin
keywords: SQL Edge, SQL Edge 'ten veri eşitleme, SQL Edge veri eşitleme
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5659ce5fa6f99463f58a33662563d768248fd8cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394909"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Öğretici: SQL Data Sync kullanarak SQL Edge 'den Azure SQL veritabanı 'na veri eşitleme

Bu öğreticide, Azure SQL Edge 'ten Azure SQL veritabanı 'na artımlı olarak veri eşitlemek için bir Azure SQL Data Sync *eşitleme grubu* kullanmayı öğreneceksiniz. SQL Data Sync, Azure SQL veritabanı 'nda yerleşik olarak bulunan ve Azure SQL veritabanı ve SQL Server örnekleri için birden çok veritabanı üzerinde seçtiğiniz verileri eşitlemenize olanak sağlayan bir hizmettir. SQL Data Sync hakkında daha fazla bilgi için bkz. [Azure SQL Data Sync](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md).

SQL Edge, [SQL Server veritabanı altyapısının](/sql/sql-server/sql-server-technical-documentation/)en son sürümlerinde oluşturulduğundan, bir SQL Server örneği için geçerli olan herhangi bir veri eşitleme mekanizması, bir kenar cihazında çalışan bir SQL Edge örneğinden veya buradan veri eşitlemek için de kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici, [Azure SQL Data Sync Için veri eşitleme aracısıyla](../azure-sql/database/sql-data-sync-agent-overview.md)yapılandırılmış bir Windows bilgisayarı gerektirir.

## <a name="before-you-begin"></a>Başlamadan önce

* Azure SQL veritabanında bir veritabanı oluşturun. Azure portal kullanarak veritabanı oluşturma hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda tek bir veritabanı oluşturma](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

* Azure SQL veritabanı dağıtımınızda tabloları ve diğer gerekli nesneleri oluşturun.

* Azure SQL Edge dağıtımınızda gerekli tabloları ve nesneleri oluşturun. Daha fazla bilgi için bkz. SQL [VERITABANı dac PAKETLERINI SQL Edge Ile kullanma](deploy-dacpac.md).

* Azure SQL Edge örneğini Azure SQL Data Sync için veri eşitleme aracısına kaydedin. Daha fazla bilgi için bkz. [SQL Server veritabanı ekleme](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="sync-data-between-a-database-in-azure-sql-database-and-sql-edge"></a>Azure SQL veritabanı ve SQL Edge 'deki bir veritabanı arasında veri eşitleme

SQL Data Sync kullanarak Azure SQL veritabanı 'ndaki bir veritabanı ile bir SQL Edge örneği arasında eşitleme ayarlamak üç temel adımdan oluşur:  


1. Bir eşitleme grubu oluşturmak için Azure portal kullanın. Daha fazla bilgi için bkz. [eşitleme grubu oluşturma](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group). Çeşitli SQL Edge örneklerinden verileri Azure SQL veritabanı 'ndaki bir veya daha fazla veritabanına eşitlemek için birden çok eşitleme grubu oluşturmak üzere tek bir *hub* veritabanı kullanabilirsiniz. 

2. Eşitleme grubuna eşitleme üyeleri ekleyin. Daha fazla bilgi için bkz. [eşitleme üyeleri ekleme](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

3. Eşitlemenin parçası olacak tabloları seçmek için eşitleme grubunu ayarlayın. Daha fazla bilgi için bkz. [eşitleme grubunu yapılandırma](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

Yukarıdaki adımları tamamladıktan sonra, Azure SQL veritabanı 'nda bir veritabanı ve bir SQL Edge örneği içeren bir eşitleme grubunuz olacaktır.

SQL Data Sync hakkında daha fazla bilgi için şu makalelere bakın:

* [Azure SQL Data Sync için veri eşitleme Aracısı](../azure-sql/database/sql-data-sync-agent-overview.md)

* [En iyi uygulamalar](../azure-sql/database/sql-data-sync-best-practices.md) ve [Azure SQL Data Sync sorunları nasıl giderilir](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [Azure Izleyici günlükleriyle SQL Data Sync izleme](../azure-sql/database/monitor-tune-overview.md)

* [Transact-SQL veya PowerShell ile eşitleme şemasını güncelleştirme](../azure-sql/database/sql-data-sync-update-sync-schema.md) [](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md)

## <a name="next-steps"></a>Sonraki adımlar


* [Azure SQL veritabanı Ile Azure SQL Edge arasında eşitleme yapmak Için PowerShell 'ı kullanın](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md). Bu öğreticide, `OnPremiseServer` veritabanı ayrıntılarını Azure SQL Edge ayrıntıları ile değiştirin.