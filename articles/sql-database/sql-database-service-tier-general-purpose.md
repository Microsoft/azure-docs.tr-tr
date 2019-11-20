---
title: Genel amaçlı hizmet katmanı
description: Azure SQL veritabanı genel amaçlı katmanı hakkında bilgi edinin
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: 7c57755ae63f8af5a2a4faa4764bc6a9597e8c2d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819223"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Genel amaçlı hizmet katmanı-Azure SQL veritabanı

> [!NOTE]
> Sanal çekirdek tabanlı satın alma modelindeki genel amaçlı hizmet katmanına, DTU tabanlı satın alma modelinde standart hizmet katmanı adı verilir. DTU tabanlı satın alma modeliyle sanal çekirdek tabanlı satın alma modeli karşılaştırması için bkz. [Azure SQL veritabanı satın alma modelleri ve kaynakları](sql-database-purchase-models.md).

Azure SQL veritabanı, altyapı hatalarının durumlarında bile% 99,99 kullanılabilirlik sağlamak için bulut ortamı için SQL Server veritabanı altyapısı mimarisi uyarlanmasını temel alır. Azure SQL veritabanı 'nda, her biri farklı mimari modellerle kullanılan üç hizmet katmanı vardır. Bu hizmet katmanları şunlardır:

- Genel amaçlı
- İş açısından kritik
- Hiper Ölçek

Genel amaçlı hizmet katmanının mimari modeli, işlem ve depolamanın bir ayrımını temel alır. Bu mimari model, veritabanı dosyalarını saydam bir şekilde çoğaltan ve temel alınan altyapı arızası durumunda veri kaybı garanti eden Azure Blob Storage 'ın yüksek kullanılabilirliğe ve güvenilirliğini kullanır.

Aşağıdaki şekilde, standart mimari modelde, ayrılmış işlem ve Depolama katmanlarına sahip dört düğüm gösterilmektedir.

![İşlem ve depolama ayrımı](media/sql-database-managed-instance/general-purpose-service-tier.png)

Genel amaçlı hizmet katmanının mimari modelinde iki katman vardır:

- `sqlservr.exe` işlemini çalıştıran ve yalnızca geçici ve önbelleğe alınmış verileri içeren, durum bilgisi olmayan bir işlem katmanı (örneğin, plan önbelleği, arabellek havuzu, sütun depolama havuzu). Bu durum bilgisiz SQL Server düğüm, işlem başlatan Azure Service Fabric tarafından işletilebilir, düğümün durumunu denetler ve gerekirse başka bir yere yük devretme gerçekleştirir.
- Azure Blob depolamada depolanan veritabanı dosyaları (. mdf/. ldf) ile durum bilgisi olan bir veri katmanı. Azure Blob depolama, herhangi bir veritabanı dosyasına yerleştirilmiş herhangi bir kaydın veri kaybı olmasını garanti eder. Azure depolama 'nın, SQL Server işlem kilitlenmesi durumunda bile, veri dosyasındaki her kaydın günlük dosyası ya da sayfasında korunmasını sağlayan yerleşik veri kullanılabilirliği/artıklığı vardır.

Veritabanı altyapısı veya işletim sistemi yükseltildiğinde, temeldeki altyapının bir bölümü başarısız olur veya SQL Server işlemde bazı kritik sorunlar algılanırsa Azure Service Fabric, durum bilgisiz SQL Server işlemini başka bir durum bilgisi olmayan işlem düğümüne taşıyacaktır. Yük devretme süresini en aza indirmek için birincil düğümün yük devretmesi olursa yeni bir işlem hizmeti çalıştırmak için bekleyen bir yedek düğüm kümesi vardır. Azure depolama katmanındaki veriler etkilenmez ve veri/günlük dosyaları yeni başlatılan SQL Server işleme eklenir. Bu işlem% 99,99 kullanılabilirliği garanti eder, ancak geçiş süresi nedeniyle çalışan ağır iş yükü üzerinde bazı performans etkileri olabilir ve yeni SQL Server düğümü soğuk önbellek ile başlar.

## <a name="when-to-choose-this-service-tier"></a>Bu hizmet katmanını seçme

Genel Amaçlı hizmet katmanı, genel iş yükleri için tasarlanan Azure SQL veritabanı 'nda varsayılan bir hizmet katmandır. % 99,99 SLA ile tam olarak yönetilen bir veritabanı altyapısına ihtiyacınız varsa, Azure SQL IaaS ile eşleşen 5 ve 10 MS arasında depolama gecikme süresi ile, Genel Amaçlı katmanı sizin için seçenektir.

## <a name="next-steps"></a>Sonraki adımlar

- [Yönetilen örnekteki](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)kaynak özelliklerini (çekirdek sayısı, GÇ, bellek) genel amaçlı/standart katmanı, [Vcore modeli](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) veya [DTU modelinde](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)tek bir veritabanı ya da [Vcore modeli](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose---provisioned-compute---gen4) ve [DTU modelinde](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits)esnek havuz bulun.
- [İş açısından kritik](sql-database-service-tier-business-critical.md) ve [hiper ölçek](sql-database-service-tier-hyperscale.md) katmanları hakkında bilgi edinin.
- [Service Fabric](../service-fabric/service-fabric-overview.md)hakkında bilgi edinin.
- Yüksek kullanılabilirlik ve olağanüstü durum kurtarma için daha fazla seçenek için bkz. [Iş sürekliliği](sql-database-business-continuity.md).
