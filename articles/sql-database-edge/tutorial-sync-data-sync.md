---
title: SQL Data Sync kullanarak Azure SQL veritabanı kenarından verileri eşitleyin | Microsoft Docs
description: Azure SQL Data Sync Azure SQL veritabanı Edge arasında veri eşitleme hakkında bilgi edinin
keywords: SQL veritabanı Edge, SQL veritabanı kenarından veri eşitleme, SQL veritabanı Edge veri eşitleme
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 857cee30ac4c1002fb7ca57d6be5fa461a14e9ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501319"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-using-sql-data-sync"></a>Öğretici: SQL Data Sync kullanarak SQL veritabanı kenarından Azure SQL veritabanı 'na veri eşitleme

Bu öğreticide, Azure SQL veritabanı kenarından Azure SQL veritabanı 'na artımlı olarak veri eşitlemek için SQL Data Sync *eşitleme grubunu* nasıl kullanacağınızı öğreneceksiniz. SQL Data Sync, Azure SQL veritabanı 'nda yerleşik olarak bulunan ve birden çok SQL veritabanı ve SQL Server örneği arasında çift yönlü olan verileri eşitlemenize olanak tanıyan bir hizmettir. Azure SQL Data Sync hakkında daha fazla bilgi için bkz. [azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Azure SQL veritabanı Edge, [Microsoft SQL Server veritabanı altyapısının](/sql/sql-server/sql-server-technical-documentation/)en son sürümlerinde oluşturulduğundan, bir şirket içi SQL Server örneği için geçerli olan herhangi bir veri eşitleme mekanizması, VERILERI bir SQL veritabanı kenarından veya bir kenara eşitlemek için de kullanılabilir bir Edge cihazında çalışan örnek.

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici, [Azure SQL Data Sync aracısıyla](../sql-database/sql-database-data-sync-agent.md)yapılandırılmış bir Windows bilgisayarı gerektirir.

## <a name="before-you-begin"></a>Başlamadan önce

* Azure SQL veritabanı oluşturun. Azure portal kullanarak Azure SQL veritabanı oluşturma hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda tek bir veritabanı oluşturma](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Azure SQL veritabanı dağıtımınızda tabloları ve diğer gerekli nesneleri oluşturun.

* Azure SQL veritabanı Edge dağıtımınızda gerekli tabloları ve nesneleri oluşturun. Daha fazla bilgi için bkz. SQL veritabanı [Edge Ile SQL VERITABANı dac paketlerini kullanma](stream-analytics.md).

* Azure SQL veritabanı Edge örneğini Azure SQL Data Sync Aracısı ile kaydedin. Daha fazla bilgi için bkz. Şirket [içi SQL Server veritabanı ekleme](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Verileri bir Azure SQL veritabanı ve SQL veritabanı kenarı arasında eşitleme

SQL Data Sync kullanarak bir Azure SQL veritabanı ile SQL veritabanı Edge örneği arasında eşitleme ayarlamak üç temel adımdan oluşur.  

1. Bir eşitleme grubu oluşturmak için Azure portal kullanın. Eşitleme grubunu oluşturmak için, bkz. [Azure Portal kullanarak eşitleme grubu oluşturma](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Aynı *hub* veritabanı, farklı SQL veritabanı Edge örneklerinden verileri Azure 'daki bir veya daha fazla SQL veritabanına eşitlemek için birden çok farklı eşitleme grubu oluşturmak üzere kullanılabilir.

2. Eşitleme grubuna eşitleme üyeleri ekleyin. Eşitleme grubuna üye eklemek için, [SQL Data Sync gruba üye ekleme](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)bölümüne bakın.

3. Eşitleme grubunu, bu eşitlemenin parçası olacak tabloları seçmek üzere yapılandırın. Eşitleme grubunu yapılandırmak için, bkz. [eşitleme grubunu yapılandırma](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Yukarıdaki adımları tamamladıktan sonra, bir Azure SQL veritabanı ve SQL veritabanı Edge örneği içeren bir eşitleme grubunuz vardır.

SQL Data Sync hakkında daha fazla bilgi için aşağıdaki makaleleri gördünüz:

* [Azure SQL Data Sync için veri eşitleme Aracısı](../sql-database/sql-database-data-sync-agent.md)

* [En iyi uygulamalar](../sql-database/sql-database-best-practices-data-sync.md) ve [Azure SQL Data Sync sorunları nasıl giderilir](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Azure Izleyici günlükleriyle SQL Data Sync izleme](../sql-database/sql-database-sync-monitor-oms.md)

* [Transact-SQL veya PowerShell ile eşitleme şemasını güncelleştirme](../sql-database/sql-database-update-sync-schema.md) [](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure SQL veritabanı ve Azure SQL veritabanı kenarı arasında eşitleme yapmak Için PowerShell 'ı kullanın](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). Öğreticide, *OnPremiseServer* veritabanı AYRıNTıLARıNı Azure SQL veritabanı Edge ayrıntıları ile değiştirin.
