---
title: Veritabanı geçişi senaryo durumu | Microsoft Docs
description: Azure veritabanı geçiş hizmeti tarafından desteklenen geçiş senaryolarının durumu hakkında bilgi edinin.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9ea070ea86a4d0d5e7101e82450235a59c936d2d
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376402"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti tarafından desteklenen geçiş senaryolarının durumu

Azure veritabanı geçiş hizmeti, çevrimdışı (tek seferlik) ve çevrimiçi (sürekli eşitleme) geçişleri için farklı geçiş senaryolarını (kaynak/hedef çiftleri) destekleyecek şekilde tasarlanmıştır. Azure veritabanı geçiş hizmeti tarafından sunulan senaryo kapsamı zamana göre genişletildi. Yeni senaryolar düzenli olarak eklenmektedir. Bu makalede, Azure veritabanı geçiş hizmeti tarafından şu anda desteklenen geçiş senaryoları ve her senaryo için durum (özel önizleme, genel önizleme veya genel kullanılabilirlik) tanımlanmaktadır.

## <a name="offline-versus-online-migrations"></a>Çevrimdışı ve çevrimiçi geçişlere karşı

Azure veritabanı geçiş hizmeti ile çevrimdışı veya çevrimiçi geçiş yapabilirsiniz. *Çevrimdışı* geçişlerde, uygulama kapalı kalma süresi, geçiş başladığı anda başlar. Geçiş tamamlandığında, kapalı kalma süresini yeni ortama kesmek için gereken süre ile sınırlamak için *çevrimiçi* geçiş kullanın. Kapalı kalma süresinin kabul edilip edilmeyeceğini anlamak için çevrimdışı bir geçişi test etmek önerilir; Aksi takdirde, çevrimiçi geçiş yapın.

## <a name="migration-scenario-status"></a>Geçiş senaryosu durumu

Azure veritabanı geçiş hizmeti tarafından desteklenen geçiş senaryolarının durumu zamana göre değişir. Genellikle senaryolar ilk olarak **özel önizlemede**yayımlanır. Özel önizlemeye katılmak için müşterilerin [DMS önizleme sitesi](https://aka.ms/dms-preview)aracılığıyla bir aday göndermesi gerekir. Özel Önizleme sonrasında, senaryo durumu **genel önizlemeye**göre değişir. Azure veritabanı geçiş hizmeti kullanıcıları doğrudan kullanıcı arabiriminden genel önizlemede geçiş senaryolarını deneyebilir. Kaydolma gerekmez.  Bununla birlikte, genel önizlemede geçiş senaryoları, tüm bölgelerde kullanılamayabilir ve son sürümden önce ek değişikliklere karşı devam edebilir. Genel Önizleme sonrasında, senaryo durumu **genel kullanıma**açık olarak değişir. Genel kullanılabilirlik (GA) son sürüm durumudur ve işlevler tümüyle tüm kullanıcılar tarafından erişilebilir olur.

## <a name="migration-scenario-support"></a>Geçiş senaryosu desteği

Aşağıdaki tablolarda, Azure veritabanı geçiş hizmeti kullanılırken hangi geçiş senaryolarının desteklendiği gösterilmektedir.

> [!NOTE]
> Aşağıda desteklendiği şekilde listelenen bir senaryo, Kullanıcı arabirimi içinde görünmezse, daha fazla bilgi için lütfen [Azure veritabanı geçişleri](mailto:AskAzureDatabaseMigrations@service.microsoft.com) hakkında diğer ad bölümüne başvurun.

> [!IMPORTANT]
> Azure veritabanı geçiş hizmeti tarafından şu anda desteklenen tüm senaryoları özel önizlemede görüntülemek için, [DMS önizleme sitesine](https://aka.ms/dms-preview)bakın.

### <a name="offline-one-time-migration-support"></a>Çevrimdışı (tek seferlik) geçiş desteği

Aşağıdaki tabloda çevrimdışı geçişler için Azure veritabanı geçiş hizmeti desteği gösterilmektedir.

| Hedef  | Source | Destek | Durum |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL VM** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **MySQL için Azure DB** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **PostgreSQL için Azure DB** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Çevrimiçi (sürekli eşitleme) geçiş desteği

Aşağıdaki tabloda, çevrimiçi geçişler için Azure veritabanı geçiş hizmeti desteği gösterilmektedir.

| Hedef  | Source | Destek | Durum |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | Özel Önizleme |
| **Azure SQL VM** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **MySQL için Azure DB** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **PostgreSQL için Azure DB** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Genel önizleme |

## <a name="next-steps"></a>Sonraki adımlar

Azure veritabanı geçiş hizmeti ve bölgesel kullanılabilirliğine genel bakış için [Azure veritabanı geçiş hizmeti nedir](dms-overview.md)makalesine bakın.
