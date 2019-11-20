---
title: SSIS, veritabanı iş yükü hedefi olarak Azure SQL veritabanı yönetilen örneği ile geçiş
description: SSIS, veritabanı iş yükü hedefi olarak Azure SQL veritabanı yönetilen örneği ile geçiş.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 6ea9134085812b99d59ad64aa2c9ec1b2ff827d9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684405"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>SSIS, veritabanı iş yükü hedefi olarak Azure SQL veritabanı yönetilen örneği ile geçiş

Şirket içindeki SQL Server veritabanı iş yüklerini Azure SQL veritabanı yönetilen örneği 'ne geçirirken Azure [Data Migration hizmeti](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) ve [Azure SQL veritabanı yönetilen örnek geçişleri hakkında bilgi sahibi olmanız gerekir DMS kullanma](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Bu makalede, SSIS kataloğunda (SSSıSDB) depolanan SQL Server Integration Service (SSIS) paketlerinin geçirilmesi ve SSIS paket yürütmelerinin zamanlaması olan SQL Server Agent işleri ele alınmaktadır.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS kataloğunu (SSSıSDB) geçirme

Ssssıs [paketlerini Azure SQL veritabanı yönetilen örneği 'Ne geçirme](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)makalesinde açıklandığı gıbı, SSıSDB geçişi DMS kullanılarak yapılabilir.

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>SSIS işleri Azure SQL veritabanı yönetilen örnek aracısına

Azure SQL veritabanı yönetilen örneği, yalnızca şirket içi SQL Server Agent gibi yerel, birinci sınıf bir Scheduler 'a sahiptir.  SSIS işlerinin geçiş aracı henüz kullanılamadığından, Azure SQL veritabanı yönetilen örnek aracısına betikler/el ile kopyalama aracılığıyla SQL Server Agent geçirilmesi gerekir.

## <a name="additional-resources"></a>Ek kaynaklar

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Veritabanı Geçiş Hizmeti](https://docs.microsoft.com/azure/dms/dms-overview)
- [DMS kullanarak Azure SQL veritabanı yönetilen örnek geçişleri için ağ topolojileri](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [SSIS paketlerini Azure SQL veritabanı yönetilen örneği 'ne geçirme](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'da SSıSDB 'ye bağlanma](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure 'da dağıtılan SSIS paketlerini çalıştırma](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
