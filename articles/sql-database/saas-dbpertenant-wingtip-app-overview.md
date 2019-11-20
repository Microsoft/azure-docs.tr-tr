---
title: Çok kiracılı uygulama örneği-Wingtip SaaS
description: Wingtip SaaS örneği olan Azure SQL veritabanı 'nı kullanan örnek çok kiracılı bir uygulama kullanarak öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 46cdcd5f768278dbc729f48e450c68a63be604be
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822157"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>SQL veritabanı ile kiracı başına veritabanı modelini kullanan çok kiracılı bir SaaS uygulamasına giriş

Wingtip SaaS uygulaması, örnek bir çoklu kiracı uygulamasıdır. Uygulama, birden çok kiracıya hizmet vermek için kiracı başına veritabanı SaaS uygulaması modelini kullanır. Azure SQL veritabanı 'nın, çeşitli SaaS tasarım ve yönetim düzenlerini kullanarak SaaS senaryolarına olanak sağlayan özellikleri. Hızlı bir şekilde çalışmaya başlayın, Wingtip SaaS uygulaması beş dakikadan kısa bir süre içinde dağıtılır.

Uygulama kaynak kodu ve yönetim betikleri [Wingtipbilet ssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub deposunda mevcuttur. Başlamadan önce, Wingtip bilet yönetim betikleri indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) bakın.

## <a name="application-architecture"></a>Uygulama mimarisi

Wingtip SaaS uygulaması, kiracı başına veritabanı modelini kullanır. Verimliliği en üst düzeye çıkarmak için SQL elastik havuzlarını kullanır. Kiracılar sağlamak ve verilerine eşlemek için bir Katalog veritabanı kullanılır. Core Wingtip SaaS uygulaması, üç örnek kiracıya ve Katalog veritabanına sahip bir havuz kullanır. Katalog ve kiracı sunucuları DNS diğer adları ile sağlandı. Bu diğer adlar, Wingtip uygulaması tarafından kullanılan etkin kaynaklara yönelik bir başvuruyu sürdürmek için kullanılır. Bu diğer adlar, olağanüstü durum kurtarma öğreticilerinde kurtarma kaynaklarını işaret etmek üzere güncelleştirilir. Wingtip SaaS öğreticilerinin çoğunu tamamlamak, ilk dağıtımın eklentilerine neden olur. Analitik veritabanları ve veritabanları arası şema yönetimi gibi eklentiler tanıtılmıştır.


![Wingtip SaaS mimarisi](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Öğreticilerde gidip uygulamayla birlikte çalışarak, veri katmanıyla ilgili olarak SaaS desenlerine odaklanın. Diğer bir deyişle, veri katmanına odaklanın ve uygulamanın kendisini fazla analiz etmeyin. Bu SaaS desenlerinin uygulanmasını anlamak, uygulamalarınızda bu desenleri uygulamanın anahtarıdır. Ayrıca, belirli iş gereksinimleriniz için gerekli tüm değişiklikleri göz önünde bulundurun.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL veritabanı Wingtip SaaS öğreticileri

Uygulamayı dağıttıktan sonra, ilk dağıtımda derleme yapan aşağıdaki öğreticilere göz atın. Bu öğreticiler, SQL veritabanı, Azure SQL veri ambarı ve diğer Azure hizmetlerinin yerleşik özelliklerinden faydalanan yaygın SaaS düzenlerini keşfedebilir. Öğreticiler, ayrıntılı açıklamalar içeren PowerShell betikleri içerir. Açıklamalar, uygulamalarınızda aynı SaaS yönetimi desenlerinin anlaşılmasına ve uygulanmasını basitleştirir.


| Öğretici | Açıklama |
|:--|:--|
| [SQL Database çok kiracılı SaaS uygulaması örneği için rehberlik ve ipuçları](saas-tenancy-wingtip-app-guidance-tips.md) | Uygulamanın bölümlerini hazırlamak için PowerShell betiklerini indirip çalıştırın. |
|[Wingtip SaaS uygulamasını dağıtma ve araştırma](saas-dbpertenant-get-started-deploy.md)|  Azure aboneliğiniz ile Wingtip SaaS uygulamasını dağıtın ve araştırın. |
|[Kiracılar sağlama ve Katalog](saas-dbpertenant-provision-and-catalog.md)| Uygulamanın kiracılar için bir Katalog veritabanı kullanarak nasıl bağlanacağını ve kataloğun kiracılar verilerini nasıl eşlediğini öğrenin. |
|[Performansı izleme ve yönetme](saas-dbpertenant-performance-monitoring.md)| SQL Database 'in izleme özelliklerini kullanmayı ve performans eşikleri aşıldığında uyarıları ayarlamayı öğrenin. |
|[Azure Izleyici günlükleri ile izleme](saas-dbpertenant-log-analytics.md) | Birden çok havuzda büyük miktarlarda kaynağı izlemek için [Azure izleyici günlüklerini](../log-analytics/log-analytics-overview.md) nasıl kullanacağınızı öğrenin. |
|[Tek bir kiracıyı geri yükleme](saas-dbpertenant-restore-single-tenant.md)| Bir kiracı veritabanını zaman içinde önceki bir noktaya geri yüklemeyi öğrenin. Ayrıca, var olan kiracı veritabanını çevrimiçi bırakan bir paralel veritabanına nasıl geri yükleyeceğinizi öğrenin. |
|[Kiracı veritabanı şemasını yönet](saas-tenancy-schema-management.md)| Tüm kiracı veritabanlarında şemayı güncelleştirme ve başvuru verilerini güncelleştirme hakkında bilgi edinin. |
|[Çapraz kiracı dağıtılmış sorguları Çalıştır](saas-tenancy-cross-tenant-reporting.md) | Geçici analiz veritabanı oluşturun ve tüm kiracılar genelinde gerçek zamanlı dağıtılmış sorgular çalıştırın.  |
|[Ayıklanan kiracı verileri üzerinde analiz Çalıştır](saas-tenancy-tenant-analytics.md) | Kiracı verilerini bir analiz veritabanına veya çevrimdışı analiz sorguları için veri ambarına ayıklayın. |


## <a name="next-steps"></a>Sonraki adımlar

- [Wingtip bilet SaaS uygulaması örneğini dağıtırken ve kullandığınızda genel rehberlik ve ipuçları](saas-tenancy-wingtip-app-guidance-tips.md)
- [Wingtip SaaS uygulamasını dağıtma](saas-dbpertenant-get-started-deploy.md)
