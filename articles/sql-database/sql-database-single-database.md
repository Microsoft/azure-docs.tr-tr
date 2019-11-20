---
title: Tek bir veritabanı nedir?
description: Azure SQL veritabanı 'nda tek veritabanı hakkında bilgi edinin
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: 7b084954cb53d19739a0f633661e0c76fa82dd13
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820932"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Azure SQL veritabanı 'nda tek bir veritabanı nedir?

Tek veritabanı dağıtım seçeneği, Azure SQL veritabanı 'nda kendi kaynak kümesiyle bir veritabanı oluşturur ve bir SQL veritabanı sunucusu üzerinden yönetilir. Tek bir veritabanı ile, her veritabanı birbirleriyle ve taşınabilir, her biri [DTU tabanlı satın alma modeli](sql-database-service-tiers-dtu.md) veya [sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md) ve garantili bir işlem boyutu içindeki kendi hizmet katmanına sahip ve taşınabilir.

> [!IMPORTANT]
> Tek veritabanı, Azure SQL veritabanı 'nın üç dağıtım seçeneğinden biridir. Diğer ikisi [elastik havuzlardır](sql-database-elastic-pool.md) ve [yönetilen örneğidir](sql-database-managed-instance.md).
> [!NOTE]
> Azure SQL veritabanı 'nda terimler sözlüğü için bkz. [SQL veritabanı terimleri sözlüğü](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Dinamik ölçeklenebilirlik

İlk uygulamanızı sunucusuz işlem katmanında düşük maliyetli küçük, tek bir veritabanında veya sağlanan işlem katmanındaki küçük bir işlem boyutu üzerinde oluşturabilirsiniz. [İşlem veya hizmet katmanını](sql-database-single-database-scale.md) , çözümünüzün ihtiyaçlarını karşılamak üzere dilediğiniz zaman el ile veya programlama yoluyla değiştirirsiniz. Performansı uygulamanız veya müşterileriniz kesinti yaşamadan ayarlayabilirsiniz. Dinamik ölçeklenebilirlik, veritabanınızın hızla değişen kaynak gereksinimlerine hızlı şekilde yanıt vermesini ve yalnızca ihtiyaç duyduğunuz kaynaklara ve ihtiyaç duyduğunuz süre boyunca ödeme yapmanızı sağlar.

## <a name="single-databases-and-elastic-pools"></a>Tek veritabanları ve elastik havuzlar

Tek bir veritabanı, kaynak paylaşımı için [elastik havuzun](sql-database-elastic-pool.md) içine veya dışına taşınabilir. Tek veritabanı oluşturabilmek ve veritabanı performansını isteğe göre yükseltip düşürebilmek, özellikle kullanım biçimlerinin nispeten tahmin edilebilir olduğu durumlarda birçok işletme ve uygulama için yeterlidir. Ancak tahmin edilemeyen kullanım biçimlerine sahipseniz bu durum maliyetlerin ve iş modelinizin yönetimini zorlaştırabilir. Elastik havuzlar, bu sorunu çözmek için tasarlanmıştır. Esnek havuzların işleyiş mantığı gayet basittir. Performans kaynaklarını tek bir veritabanı yerine bir havuza ayırır ve tek veritabanı performansı yerine havuzun ortak performans kaynakları için ödeme yaparsınız.

## <a name="monitoring-and-alerting"></a>İzleme ve uyarı

Yerleşik [performans izleme](sql-database-performance.md) ve [Uyarı araçlarını](sql-database-insights-alerts-portal.md), performans derecelendirmeleri ile birlikte kullanırsınız. Bu araçları kullanarak geçerli veya projeye özgü performans ihtiyaçlarınıza göre ölçek büyütme veya küçültme işlemlerinin etkisini hızlı bir şekilde değerlendirebilirsiniz. SQL Veritabanı ayrıca izlemeyi kolaylaştırmak için [ölçümler ve tanılama günlükleri oluşturabilir](sql-database-metrics-diag-logging.md).

## <a name="availability-capabilities"></a>Kullanılabilirlik özellikleri

Tek veritabanları, elastik havuzlar ve yönetilen örnekler hepsi birçok kullanılabilirlik özelliği sağlar. Bilgi için bkz. [kullanılabilirlik özellikleri](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Transact-SQL farklılıkları

Uygulamaların kullandığı çoğu Transact-SQL özelliği hem Microsoft SQL Server hem de Azure SQL veritabanı 'nda tam olarak desteklenmektedir. Örneğin, veri türleri, işleçler, dize, aritmetik, mantıksal ve imleç işlevleri gibi çekirdek SQL bileşenleri, SQL Server ve SQL veritabanı 'nda aynı şekilde çalışır. Bununla birlikte, DDL (veri tanımlama dili) ve DML (veri işleme dili) öğelerinde yalnızca kısmen desteklenen (Bu makalede daha sonra tartıştığımız) bir T-SQL farkı vardır.
Bunlara ek olarak, Azure SQL veritabanı, ana veritabanı ve işletim sistemi bağımlılıklarındaki özellikleri yalıtmak üzere tasarlandığından, hiçbir şekilde desteklenmeyen bazı özellikler ve söz dizimi vardır. Bu nedenle, çoğu sunucu düzeyi etkinlik SQL veritabanı için uygun değildir. T-SQL deyimleri ve seçenekleri, sunucu düzeyindeki seçenekleri, işletim sistemi bileşenlerini yapılandırıp veya dosya sistemi yapılandırması belirttiğinizde kullanılamaz. Bu tür özellikler gerektiğinde, SQL veritabanından veya başka bir Azure özelliğinden ya da hizmetinden başka bir şekilde, uygun bir alternatif vardır.

Daha fazla bilgi için bkz. [SQL veritabanına geçiş sırasında Transact-SQL farklılıklarını çözümleme](sql-database-transact-sql-information.md).

## <a name="security"></a>Güvenlik

SQL veritabanı, uygulamanızın çeşitli güvenlik ve uyumluluk gereksinimlerini karşılamasına yardımcı olmak için bir dizi [yerleşik güvenlik ve uyumluluk](sql-database-security-overview.md) özelliği sağlar.

> [!IMPORTANT]
> Azure SQL veritabanı (tüm dağıtım seçenekleri), bir dizi uyumluluk standartlarına karşı sertifikalandırilmiştir. Daha fazla bilgi için SQL veritabanı uyumluluk sertifikalarının en güncel listesini bulabileceğiniz [Microsoft Azure Güven Merkezi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) ' ne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- Tek bir veritabanını hızlıca kullanmaya başlamak için [tek veritabanı hızlı başlangıç kılavuzu](sql-database-single-database-quickstart-guide.md)ile başlayın.
- SQL Server bir veritabanını Azure 'a geçirme hakkında bilgi edinmek için bkz. [Azure SQL veritabanı 'Na geçiş](sql-database-single-database-migrate.md).
- Desteklenen özellikler hakkında bilgi edinmek için bkz. [Özellikler](sql-database-features.md).
