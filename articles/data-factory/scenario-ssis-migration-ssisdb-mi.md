---
title: SSIS, veritabanı iş yükü hedefi olarak Azure SQL yönetilen örneği ile geçiş
description: SSIS, veritabanı iş yükü hedefi olarak Azure SQL yönetilen örneği ile geçiş.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 3d2bc60f8ba7120f8d962500c06be50e905c11a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373598"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>SSIS, veritabanı iş yükü hedefi olarak Azure SQL yönetilen örneği ile geçiş

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Veritabanı iş yüklerini bir SQL Server örneğinden Azure SQL yönetilen örneği 'ne geçirirken, [Azure Data Migration hizmeti](../dms/dms-overview.md)(DMS) ve [DMS kullanarak SQL yönetilen örnek geçişleri için ağ topolojileri](../dms/resource-network-topologies.md)hakkında bilgi sahibi olmanız gerekir.

Bu makalede, SSIS kataloğunda (SSSıSDB) depolanan SQL Server Integration Service (SSIS) paketlerinin geçirilmesi ve SSIS paket yürütmelerinin zamanlaması olan SQL Server Agent işleri ele alınmaktadır.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS kataloğunu (SSSıSDB) geçirme

Ssssıs [PAKETLERINI SQL yönetilen örneği 'Ne geçirme](../dms/how-to-migrate-ssis-packages-managed-instance.md)makalesinde açıklandığı gıbı, SSISDB geçişi DMS kullanılarak yapılabilir.

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS işleri SQL yönetilen örnek aracısına

SQL yönetilen örneği, yalnızca şirket içi SQL Server Agent gibi yerel, birinci sınıf bir Scheduler 'a sahiptir.  [SSIS paketlerini Azure SQL yönetilen örnek Aracısı aracılığıyla çalıştırabilirsiniz](how-to-invoke-ssis-package-managed-instance-agent.md).

SSIS işlerinin geçiş aracı henüz kullanılamadığından, betikler/el ile kopyalama aracılığıyla şirket içi SQL Server Agent içinden SQL yönetilen örnek aracısına geçirilmesi gerekir.

## <a name="additional-resources"></a>Ek kaynaklar

- [Azure Data Factory](./introduction.md)
- [Azure-SSIS Integration Runtime](./create-azure-ssis-integration-runtime.md)
- [Azure Veritabanı Geçiş Hizmeti](../dms/dms-overview.md)
- [DMS kullanarak SQL yönetilen örnek geçişleri için ağ topolojileri](../dms/resource-network-topologies.md)
- [SSIS paketlerini SQL yönetilen örneği 'ne geçirme](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'da SSıSDB 'ye bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure 'da dağıtılan SSIS paketlerini çalıştırma](/sql/integration-services/lift-shift/ssis-azure-run-packages)