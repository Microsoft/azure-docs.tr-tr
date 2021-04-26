---
title: Kaynakları ölçeklendirme
description: Bu makalede, ayrılan kaynakları ekleyerek veya kaldırarak Azure SQL veritabanı ve SQL yönetilen örneği 'nde veritabanınızın ölçeklendirilmesi açıklanmaktadır.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 06/25/2019
ms.openlocfilehash: ca1a2edec70b13f111ffd89278aa39d1ddea7f67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105035651"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Veritabanı kaynaklarını en az kapalı kalma süresiyle dinamik olarak ölçeklendirin
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve SQL yönetilen örneği, en az [kapalı kalma süresiyle](https://azure.microsoft.com/support/legal/sla/sql-database)veritabanınıza dinamik olarak daha fazla kaynak eklemenize olanak sağlar; Ancak, bağlantının veritabanına kısa bir süre boyunca kaybedildiği, yeniden deneme mantığı kullanılarak azaltılan bir süre içinde bir anahtar vardır.

## <a name="overview"></a>Genel Bakış

Uygulamanız için talepler, çok sayıda cihazdan ve müşteriden milyonlarca, Azure SQL veritabanı ve SQL yönetilen örnek ölçeğinde en az kapalı kalma süresine kadar artar. Ölçeklenebilirlik, hizmet olarak platform (PaaS) ' ın en önemli özelliklerinden biridir ve gerektiğinde hizmetinize dinamik olarak daha fazla kaynak eklemenizi sağlar. Azure SQL veritabanı, veritabanlarınıza ayrılan kaynakları (CPU gücü, bellek, GÇ işleme ve depolama) kolayca değiştirmenize olanak sağlar.

Uygulamanızın dizin oluşturma veya sorgu yeniden yazma yöntemleri kullanılarak düzeltilmeyen kullanımı nedeniyle performans sorunlarını azaltabilirsiniz. Daha fazla kaynak eklemek, veritabanınız geçerli kaynak limitlerini ziyaret ettiğinizde hızlı bir şekilde tepki sağlamanıza olanak sağlar ve gelen iş yükünü işlemek için daha fazla güç gerektirir. Azure SQL veritabanı, maliyeti düşürmek için ihtiyaç duyulmadığında kaynakları ölçeklendirmenize de olanak sağlar.

Donanım satın alma ve temel altyapıyı değiştirme konusunda endişelenmeniz gerekmez. Bir veritabanının ölçeklendirilmesi, kaydırıcı kullanılarak Azure portal aracılığıyla kolayca yapılabilir.

![Veritabanı performansını ölçeklendirin](./media/scale-resources/scale-performance.svg)

Azure SQL veritabanı, [DTU tabanlı satın](service-tiers-dtu.md) alma modeli ve [sanal çekirdek tabanlı satın alma MODELI](service-tiers-vcore.md)sunarak Azure SQL yönetilen örneği yalnızca [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md)sunar. 

- [DTU tabanlı satın alma modeli](service-tiers-dtu.md) , hafif ve ağır veritabanı iş yüklerini desteklemek için üç hizmet katmanında işlem, bellek ve g/ç kaynakları Blend sağlar: temel, standart ve Premium. Her katman içindeki performans düzeyleri, bu kaynakların farklı bir karışımını sağlar ve bunlara ek depolama kaynakları da eklenebilir.
- [Sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md) , sanal çekirdek sayısını, miktarı veya belleği ve depolamanın miktarını ve hızını seçmenize olanak sağlar. Bu satın alma modeli üç hizmet katmanı sunar: Genel Amaçlı, İş Açısından Kritik ve hiper ölçek.

İlk uygulamanızı, temel, standart veya Genel Amaçlı hizmet katmanında aylık düşük maliyette küçük, tek bir veritabanında oluşturabilir ve sonra, çözümünüzün ihtiyaçlarını karşılamak için bu hizmet katmanını, Premium veya İş Açısından Kritik hizmet katmanına dilediğiniz zaman el ile veya programlama yoluyla değiştirebilirsiniz. Performansı uygulamanız veya müşterileriniz kesinti yaşamadan ayarlayabilirsiniz. Dinamik ölçeklenebilirlik, veritabanınızın hızla değişen kaynak gereksinimlerine hızlı şekilde yanıt vermesini ve yalnızca ihtiyaç duyduğunuz kaynaklara ve ihtiyaç duyduğunuz süre boyunca ödeme yapmanızı sağlar.

> [!NOTE]
> Dinamik ölçeklenebilirlik, otomatik ölçeklendirmeden farklıdır. Otomatik ölçeklendirme, bir hizmetin ölçütlere göre otomatik olarak ölçeklendirilmesi durumunda dinamik ölçeklenebilirlik, en az kapalı kalma süresiyle el ile ölçeklendirilmesine olanak sağlar.

Azure SQL veritabanı 'ndaki tek veritabanları el ile dinamik ölçeklenebilirliği destekler, ancak otomatik ölçeklendirmeyi desteklemez. Daha *otomatik* bir deneyim için, veritabanlarının tek tek veritabanı gereksinimlerine göre bir havuzdaki kaynakları paylaşmasına olanak sağlayan elastik havuzları kullanın.
Ancak, Azure SQL veritabanı 'nda tek bir veritabanı için ölçeklenebilirliği otomatikleştirmeye yardımcı olabilecek betikler vardır. Örnek için bkz. [PowerShell kullanarak tek bir SQL Veritabanını izleme ve ölçeklendirme](scripts/monitor-and-scale-database-powershell.md).

Uygulamanızda çok az (genellikle ortalama dört saniyenin altında) kesinti ile dilediğiniz zaman [DTU hizmet katmanlarını](service-tiers-dtu.md) veya [sanal çekirdek özelliklerini](resource-limits-vcore-single-databases.md) değiştirebilirsiniz. Veritabanı oluşturabilmek ve veritabanı performansını isteğe göre yükseltip düşürebilmek, özellikle kullanım biçimlerinin nispeten tahmin edilebilir olduğu durumlarda birçok işletme ve uygulama için yeterlidir. Ancak tahmin edilemeyen kullanım biçimlerine sahipseniz bu durum maliyetlerin ve iş modelinizin yönetimini zorlaştırabilir. Bu senaryo için, havuzdaki birden çok veritabanı arasında paylaşılan belirli sayıda eDTU 'lar ile elastik bir havuz kullanırsınız.

![SQL Veritabanı'na Giriş: Katmana ve düzeye göre tek veritabanı DTU’ları](./media/scale-resources/single_db_dtus.png)

Azure SQL veritabanı, veritabanlarınızı dinamik olarak ölçeklendirmenize olanak sağlar:

- Tek bir [veritabanı](single-database-scale.md)ile her bir veritabanına atanacak en fazla kaynak miktarını tanımlamak için [DTU](resource-limits-dtu-single-databases.md) veya [Vcore](resource-limits-vcore-single-databases.md) modellerini kullanabilirsiniz.
- [Elastik havuzlar](elastic-pool-scale.md) , havuzdaki veritabanı grubu başına en fazla kaynak sınırını tanımlamanızı sağlar.

Azure SQL yönetilen örneği, ölçeklendirmenize de olanak tanır: 

- [SQL yönetilen örneği](../managed-instance/sql-managed-instance-paas-overview.md) [vçekirdekler](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) modunu KULLANıR ve en yüksek CPU çekirdeğini ve örneğiniz için ayrılan en fazla depolama alanını tanımlamanıza olanak sağlar. Yönetilen örnek içindeki tüm veritabanları örneğe ayrılan kaynakları paylaşır.

Herhangi bir türde ölçek artırma veya ölçek azaltma eylemini başlatmak, veritabanı motoru işlemini yeniden başlatır ve gerekirse farklı bir sanal makineye taşır. Veritabanı altyapısı işleminin yeni bir sanal makineye taşınması, işlem devam ederken mevcut Azure SQL veritabanı hizmetinizi kullanmaya devam edebileceğiniz **çevrimiçi bir işlemdir** . Hedef veritabanı altyapısı tam olarak başlatıldıktan ve sorguları işlemeye hazırlandıktan sonra, bağlantılar [kaynaktan hedef veritabanı altyapısına geçiş](single-database-scale.md#impact)yapılır.

> [!NOTE]
> Verilerin içeri aktarılması, veri işleme işleri, dizin yeniden oluşturma, vb. gibi uzun süreli bir işlem çalışıyorsa veya örnek üzerinde etkin bir bağlantınız varsa, yönetilen örneğinizin ölçeklendirilmesi önerilmez. Ölçeklendirmeyi normalden uzun zaman almasını engellemek için, uzun süre çalışan tüm işlemlerin tamamlanmasından sonra örneği ölçeklendirmelisiniz.

> [!NOTE]
> Ölçek Genişletme/ölçek azaltma işlemi tamamlandığında kısa bir bağlantı kesmesi bekleyebilir. [Standart geçici hatalar Için yeniden deneme mantığı](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)uyguladıysanız, yük devretmeyi fark edersiniz.

## <a name="alternative-scale-methods"></a>Alternatif ölçek yöntemleri

Kaynak Ölçekleme, veritabanı veya uygulama kodunu değiştirmeden veritabanınızın performansını artırmanın en kolay ve en etkili yoludur. Bazı durumlarda, en yüksek hizmet katmanları, işlem boyutları ve performans iyileştirmeleri bile iş yükünüzü başarılı ve ekonomik bir şekilde işleyemeyebilir. Bu durumda, veritabanınızı ölçeklendirmek için aşağıdaki ek seçeneklere sahip olursunuz:

- [Okuma ölçeği](read-scale-out.md) genişletme, verilerinizin yalnızca salt okunurdur bir çoğaltmasını aldığınız, raporlar gibi yalnızca salt okuma sorgularını yürütebileceğiniz kullanılabilir bir özelliktir. Salt okuma çoğaltması, birincil Veritabanınızdaki kaynak kullanımını etkilemeden salt okunurdur.
- [Veritabanı](elastic-scale-introduction.md) parçaları, verilerinizi çeşitli veritabanlarına bölmenizi ve bunları bağımsız olarak ölçeklendirmenizi sağlayan bir teknikler kümesidir.

## <a name="next-steps"></a>Sonraki adımlar

- Veritabanı kodunu değiştirerek veritabanı performansını artırma hakkında daha fazla bilgi için bkz. [performans önerilerini bulma ve uygulama](database-advisor-find-recommendations-portal.md).
- Yerleşik veritabanı zekası hakkında bilgi edinmek için veritabanınızı iyileştirin, bkz. [otomatik ayarlama](automatic-tuning-overview.md).
- Azure SQL veritabanı 'nda okuma ölçeği genişletme hakkında daha fazla bilgi için bkz. salt [okuma çoğaltmaları nasıl kullanılır, salt okuma sorgusu yükünü dengelemek için](read-scale-out.md).
- Veritabanı oluşturma hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı Ile ölçeklendirme](elastic-scale-introduction.md).
