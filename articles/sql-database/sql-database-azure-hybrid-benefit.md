---
title: Azure Hibrit Avantajı
description: SQL veritabanı indirimleri için mevcut SQL Server lisanslarını kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: 4aab699ff0141b648066fae0de445c4467be7509
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048386"
---
# <a name="azure-hybrid-benefit"></a>Azure Hibrit Avantajı

Sanal çekirdek tabanlı satın alma modelinin sağlanan işlem katmanında, [SQL Server için Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)kullanarak SQL veritabanı 'nda indirimli ücretler için mevcut lisanslarınızı Exchange 'e dönüştürebilirsiniz. Bu Azure avantajı, Yazılım Güvencesi kapsamındaki şirket içi SQL Server lisanslarınızı kullanarak Azure SQL veritabanında yüzde 30 ' a varan tasarruf etmenize olanak tanır. 

> [!NOTE]
> Azure Hibrit Avantajı değiştirmek için kapalı kalma süresi gerekmez.

![Fiyat](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Bir lisans modeli seçin

Azure Hibrit Avantajı ile yalnızca SQL veritabanı altyapısının kendisi için mevcut SQL Server lisansınızı kullanarak (temel Işlem fiyatlandırması) yalnızca temeldeki Azure altyapısı için ödeme yapmayı seçebilirsiniz veya hem temel alınan altyapı hem de SQL Server için ödeme yapabilirsiniz Lisans (lisans dahil fiyatlar).

Azure portal kullanarak veya aşağıdaki API 'lerden birini kullanarak lisanslama modelinizi seçebilir veya değiştirebilirsiniz:

- PowerShell kullanarak lisans türünü ayarlamak veya güncelleştirmek için:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-Azsqlınstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-Azsqlınstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Azure CLı kullanarak lisans türünü ayarlamak veya güncelleştirmek için:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az SQL mı oluştur](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az SQL mı güncelleştirmesi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- REST API kullanarak lisans türünü ayarlamak veya güncelleştirmek için:

  - [Veritabanları-oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Veritabanları-Güncelleştir](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Yönetilen örnekler-oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Yönetilen örnekler-güncelleştirme](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="next-steps"></a>Sonraki adımlar

- SQL veritabanı dağıtım seçenekleri arasında seçim yapmak için bkz. [Azure SQL 'de doğru dağıtım seçeneğini belirleyin](sql-database-paas-vs-sql-server-iaas.md).
- SQL veritabanı özelliklerinin karşılaştırması için bkz. [Azure SQL veritabanı özellikleri](sql-database-features.md).
