---
title: Kaynakları ölçeklendirin
description: Bu makalede, ayrılan kaynakları ekleyerek veya kaldırarak veritabanınızın ölçeklendirilmesi açıklanmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 678096037da69bbddf95933e3fdf988f540ca4a6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819831"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Veritabanı kaynaklarını en az kapalı kalma süresiyle dinamik olarak ölçeklendirin

Azure SQL veritabanı, en az [kapalı kalma süresiyle](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)veritabanınıza dinamik olarak daha fazla kaynak eklemenize olanak sağlar; Ancak, bağlantının veritabanına kısa bir süre boyunca kaybedildiği, yeniden deneme mantığı kullanılarak azaltılan bir süre içinde bir anahtar vardır.

## <a name="overview"></a>Genel Bakış

Uygulamanız için talep bir cihaz ve müşterilerin milyonlarca katına büyüdükçe Azure SQL veritabanı, en az kapalı kalma süresiyle birlikte hareket halindeyken ölçeklendirilir. Ölçeklenebilirlik, gerektiğinde hizmetinize dinamik olarak daha fazla kaynak eklemenizi sağlayan PaaS 'nin en önemli özelliklerinden biridir. Azure SQL veritabanı, veritabanlarınıza ayrılan kaynakları (CPU gücü, bellek, GÇ işleme ve depolama) kolayca değiştirmenize olanak sağlar.

Uygulamanızın dizin oluşturma veya sorgu yeniden yazma yöntemleri kullanılarak düzeltilmeyen kullanımı nedeniyle performans sorunlarını azaltabilirsiniz. Daha fazla kaynak eklemek, veritabanınız geçerli kaynak limitlerini ziyaret ettiğinizde hızlı bir şekilde tepki sağlamanıza olanak sağlar ve gelen iş yükünü işlemek için daha fazla güç gerektirir. Azure SQL veritabanı, maliyeti düşürmek için ihtiyaç duyulmadığında kaynakları ölçeklendirmenize de olanak sağlar.

Donanım satın alma ve temel altyapıyı değiştirme konusunda endişelenmeniz gerekmez. Ölçeklendirme veritabanı, kaydırıcı kullanılarak Azure portal aracılığıyla kolayca yapılabilir.

![Veritabanı performansını ölçeklendirin](media/sql-database-scalability/scale-performance.svg)

Azure SQL veritabanı, [DTU tabanlı satın alma modeli](sql-database-service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md)sunar.

- [DTU tabanlı satın alma modeli](sql-database-service-tiers-dtu.md) , hafif ve ağır veritabanı iş yüklerini desteklemek için üç hizmet katmanında işlem, bellek ve GÇ kaynakları Blend sağlar: temel, standart ve Premium. Her katman içindeki performans düzeyleri, bu kaynakların farklı bir karışımını sağlar ve bunlara ek depolama kaynakları da eklenebilir.
- [Sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md) , sanal çekirdek sayısını, miktarı veya belleği ve depolamanın miktarını ve hızını seçmenize olanak sağlar. Bu satın alma modeli üç hizmet katmanı sunar: Genel Amaçlı, İş Açısından Kritik ve hiper ölçek.

İlk uygulamanızı, temel, standart veya Genel Amaçlı hizmet katmanında aylık düşük maliyette küçük, tek bir veritabanında oluşturabilir ve ardından hizmet katmanını el ile veya İş Açısından Kritik hizmet katmanına dilediğiniz zaman, ne zaman çözümünüzün EDS 'ı. Performansı uygulamanız veya müşterileriniz kesinti yaşamadan ayarlayabilirsiniz. Dinamik ölçeklenebilirlik, veritabanınızın hızla değişen kaynak gereksinimlerine hızlı şekilde yanıt vermesini ve yalnızca ihtiyaç duyduğunuz kaynaklara ve ihtiyaç duyduğunuz süre boyunca ödeme yapmanızı sağlar.

> [!NOTE]
> Dinamik ölçeklenebilirlik, otomatik ölçeklendirmeden farklıdır. Otomatik ölçeklendirme, bir hizmetin ölçütlere göre otomatik olarak ölçeklendirilmesi durumunda dinamik ölçeklenebilirlik, en az kapalı kalma süresiyle el ile ölçeklendirilmesine olanak sağlar.

Tek Azure SQL Veritabanı, el ile dinamik ölçeklenebilirliği destekler, ancak otomatik ölçeklendirmeyi desteklemez. Daha *otomatik* bir deneyim için, veritabanlarının tek tek veritabanı gereksinimlerine göre bir havuzdaki kaynakları paylaşmasına olanak sağlayan elastik havuzları kullanın.
Ancak, tek bir Azure SQL veritabanı için ölçeklenebilirliği otomatik hale getirmeye yardımcı olabilecek betikler vardır. Örnek için bkz. [PowerShell kullanarak tek bir SQL Veritabanını izleme ve ölçeklendirme](scripts/sql-database-monitor-and-scale-database-powershell.md).

Uygulamanızda çok az (genellikle ortalama dört saniyenin altında) kesinti ile dilediğiniz zaman [DTU hizmet katmanlarını](sql-database-service-tiers-dtu.md) veya [sanal çekirdek özelliklerini](sql-database-vcore-resource-limits-single-databases.md) değiştirebilirsiniz. Veritabanı oluşturabilmek ve veritabanı performansını isteğe göre yükseltip düşürebilmek, özellikle kullanım biçimlerinin nispeten tahmin edilebilir olduğu durumlarda birçok işletme ve uygulama için yeterlidir. Ancak tahmin edilemeyen kullanım biçimlerine sahipseniz bu durum maliyetlerin ve iş modelinizin yönetimini zorlaştırabilir. Bu senaryo için, havuzdaki birden çok veritabanı arasında paylaşılan belirli sayıda eDTU 'lar ile elastik bir havuz kullanırsınız.

![SQL Veritabanı'na Giriş: Katmana ve düzeye göre tek veritabanı DTU’ları](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Azure SQL veritabanı 'nın her üç özelliği de veritabanlarınızı dinamik olarak ölçeklendirmeye yönelik bir özellik sunar:

- Tek bir [veritabanı](sql-database-single-database-scale.md)ile her bir veritabanına atanacak en fazla kaynak miktarını tanımlamak için [DTU](sql-database-dtu-resource-limits-single-databases.md) veya [Vcore](sql-database-vcore-resource-limits-single-databases.md) modellerini kullanabilirsiniz.
- [Yönetilen bir örnek](sql-database-managed-instance.md) [vçekirdekler](sql-database-managed-instance.md#vcore-based-purchasing-model) modunu KULLANıR ve en yüksek CPU çekirdeğini ve örneğiniz için ayrılan en fazla depolama alanını tanımlamanıza olanak sağlar. Örnek içindeki tüm veritabanları, örneğe ayrılan kaynakları paylaşacaktır.
- [Elastik havuzlar](sql-database-elastic-pool-scale.md) , havuzdaki veritabanı grubu başına en fazla kaynak sınırını tanımlamanızı sağlar.

Herhangi bir türde ölçek artırma veya ölçek azaltma eylemini başlatmak, veritabanı motoru işlemini yeniden başlatır ve gerekirse farklı bir sanal makineye taşır. Veritabanı altyapısı işleminin yeni bir sanal makineye taşınması, işlem devam ederken mevcut Azure SQL veritabanı hizmetinizi kullanmaya devam edebileceğiniz **çevrimiçi bir işlemdir** . Hedef veritabanı altyapısı tam olarak başlatıldıktan ve sorguları işlemeye hazırlandıktan sonra, bağlantılar [kaynaktan hedef veritabanı altyapısına geçiş](sql-database-single-database-scale.md#impact-of-changing-service-tier-or-rescaling-compute-size)yapılır.

> [!NOTE]
> Ölçek Genişletme/ölçek azaltma işlemi tamamlandığında kısa bir bağlantı kesmesi bekleyebilir. [Standart geçici hatalar Için yeniden deneme mantığı](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)uyguladıysanız, yük devretmeyi fark edersiniz.

## <a name="alternative-scale-methods"></a>Alternatif ölçek yöntemleri

Kaynak Ölçekleme, veritabanı veya uygulama kodunu değiştirmeden veritabanınızın performansını artırmanın en kolay ve en etkili yoludur. Bazı durumlarda, en yüksek hizmet katmanları, işlem boyutları ve performans iyileştirmeleri bile başarılı ve ekonomik bir şekilde iş yükünüzü işleyemeyebilir. Bu durumda, veritabanınızı ölçeklendirmek için aşağıdaki ek seçeneklere sahip olursunuz:

- [Okuma ölçeği](sql-database-read-scale-out.md) genişletme, verilerinizin yalnızca salt okunurdur bir çoğaltmasını aldığınız, raporlar gibi yalnızca yoğun salt okuma sorgularını yürütebileceğiniz bir özelliktir. Salt okuma çoğaltması, birincil Veritabanınızdaki kaynak kullanımını etkilemeden salt okunurdur.
- [Veritabanı](sql-database-elastic-scale-introduction.md) parçaları, verilerinizi çeşitli veritabanlarına bölmenizi ve bunları bağımsız olarak ölçeklendirmenizi sağlayan bir teknikler kümesidir.

## <a name="next-steps"></a>Sonraki adımlar

- Veritabanı kodunu değiştirerek veritabanı performansını artırma hakkında daha fazla bilgi için bkz. [performans önerilerini bulma ve uygulama](sql-database-advisor-portal.md).
- Yerleşik veritabanı zekası hakkında bilgi edinmek için veritabanınızı iyileştirin, bkz. [otomatik ayarlama](sql-database-automatic-tuning.md).
- Azure SQL veritabanı hizmetinde okuma ölçeği genişletme hakkında daha fazla bilgi için bkz. salt [okuma çoğaltmaları nasıl kullanılır, salt okuma sorgusu yükünü dengelemek için](sql-database-read-scale-out.md).
- Veritabanı oluşturma hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı Ile ölçeklendirme](sql-database-elastic-scale-introduction.md).
