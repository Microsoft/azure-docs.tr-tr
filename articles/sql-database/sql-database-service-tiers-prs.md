---
title: Hizmet katmanı kullanımdan kaldırma Premium RS
description: Premium RS hizmet katmanı kullanımdan kaldırılıyor ve bu hizmet için destek sonlandırılıyor-bkz. geçiş seçenekleri.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: f00ecd19877ba6236bde5de73d450967abc1fe15
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821037"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Azure SQL Veritabanı Premium RS hizmet katmanı (Önizleme) kullanımdan kaldırılıyor-geçiş seçenekleri

Microsoft, Şubat 2018 ' de Azure SQL veritabanı 'ndaki Premium RS hizmet katmanının genel kullanıma sunulmayacak olduğunu ve 31 Ocak 2019 ' den sonra desteklenmediğini duyurmuştur. Destek son tarihinin bu sonu 30 Haziran 2019 ' e genişletilmiştir. Bu makalede Premium RS hizmet katmanından başka bir hizmet katmanına geçiş için kullanabileceğiniz seçenekler açıklanmaktadır. Microsoft, 30 Haziran 2019 ' den sonra, Premium RS veritabanlarınızı Premium RS veritabanınızın performans gereksinimleriyle en yakından eşleşen genel kullanıma açık bir hizmet katmanına otomatik olarak geçirecektir.

Premium RS müşterileri için uygun olabilecek geçiş hedefleri ve fiyatlandırma seçenekleri şunlardır:

- Sanal çekirdek hizmet katmanları

  [Sanal çekirdek tabanlı satın alma modelindeki](sql-database-service-tiers-vcore.md) **genel amaçlı** ve **iş açısından kritik** hizmet katmanları. Bu iki hizmet katmanı genel kullanıma sunulduğuna göre yapılır. Sanal çekirdek tabanlı satın alma modeli Ayrıca, iş yükünüzün ihtiyaçlarına göre isteğe bağlı olarak, veritabanı başına 100 TB 'a kadar otomatik ölçeklendirme sağlayan **hiper ölçekli** hizmet katmanını da sunar. Hiper ölçek hizmeti katmanı, Premium RS hizmet katmanına daha yakın bir fiyata [DTU tabanlı satın alma modelindeki](sql-database-service-tiers-dtu.md) Premium hizmet KATMANıNA benzer GÇ performansı sağlar.
- Geliştirme ve Test fiyatlandırması

  [Geliştirme ve test fiyatlandırması](https://azure.microsoft.com/pricing/dev-test/) , Visual Studio aboneliğinizle lisans dahil olmak üzere %55 ' e kadar tasarruf sağlar.
- Azure Hibrit Avantajı ve ayrılmış kapasite fiyatlandırması

  [Azure hibrit avantajı ve ayrılmış kapasite fiyatlandırması,](https://azure.microsoft.com/pricing/details/sql-database/) %80 ve lisans dahil ücretlere kadar tasarruf sağlar. Bu seçenekler hakkında daha fazla bilgi için bkz. SQL Server ve [Azure SQL veritabanı ayrılmış kapasitesi](sql-database-reserved-capacity.md) [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) .

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Artık Premium RS veritabanlarınızı alternatif SQL veritabanı hizmet katmanlarına geçirmeye çalışır

Premium RS iş yüklerinize yönelik doğru geçiş hedefini öğrenmek için bu makaledeki Kılavuzu ve fiyatlarımızla birlikte gözden geçirin.

## <a name="migrate-compute-intensive-workloads-and-save"></a>İşlem yoğunluğu yoğun iş yüklerini geçirme ve kaydetme

İşlem yoğunluğu yoğun Premium RS iş yükleriniz için, genel olarak kullanılabilir sanal çekirdek tabanlı Genel Amaçlı hizmet katmanlarımıza geçiş yapmanızı ve SQL Server ve ayrılmış kapasite teklifleri için Azure Hibrit Avantajı kullanarak daha fazla lisans dahil ücret tasarrufu yapmanızı öneririz. DTU tabanlı bir satın alma seçeneğinde kalırsa, işlem yoğunluklu Premium RS veritabanlarınızı standart bir hizmet katmanına geçirebilir ve yine de Premium RS genel kullanım fiyatlandırmasına karşı tasarruf edebilirsiniz (genel kullanıma sunulmasaydı).

> [!WARNING]
> Premium RS iş yüklerinizi DTU tabanlı Premium hizmet katmanlarına geçirmek, aylık maliyetleri ve geçerli Premium RS fiyatlandırmaya karşı artırabilir. Premium RS kıyasla benzer veya daha düşük maliyetleri korumak için, Azure Hibrit Avantajı ve ayrılmış kapasite fiyatlandırmasına sahip Hyperscale veya İş Açısından Kritik katmanlarını dikkate etmenizi öneririz.

### <a name="premium-rs-databases"></a>Premium RS veritabanları

|**Şu anda açık...**|**Karşılaştırılabilir sanal çekirdek tabanlı ' a geçiş yapın...**|**Karşılaştırılabilir DTU tabanlı ' a geçiş yapın...**|
|---|---|---|
|Premium RS 1|Genel Amaçlı 1 sanal çekirdek (4. nesil)|Standart 3|
|Premium RS 2|Genel Amaçlı 2 sanal çekirdekler (4. nesil)|Standart 4|
|Premium RS 4|Genel Amaçlı 4 sanal çekirdek (4. nesil)|Standart 6|
|Premium RS 6|Genel Amaçlı 6 sanal çekirdek (4. nesil)|Standart 7|

### <a name="premium-rs-pools"></a>Premium RS havuzları

|**Şu anda açık...**|**Karşılaştırılabilir sanal çekirdek tabanlı ' a geçiş yapın...**|**Karşılaştırılabilir DTU tabanlı ' a geçiş yapın...**|
|---|---|---|
|Premium RS havuzu 125 DTU|Genel Amaçlı 1 sanal çekirdek (4. nesil)|Standart Havuz 100 eDTU|
|Premium RS havuzu 250 DTU|Genel Amaçlı 2 sanal çekirdekler (4. nesil)|Standart Havuz 250 eDTU|
|Premium RS havuzu 500 DTU|Genel Amaçlı 4 sanal çekirdek (4. nesil)|Standart Havuz 500 eDTU|
|Premium RS havuzu 1000 DTU|Genel Amaçlı 8 sanal çekirdekler (4. nesil)|Standart Havuz 1000 eDTU|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>GÇ yoğun iş yükleriniz için tasarruf ve performansı iyileştirin

En iyi performans ve maliyet birleşimi için, GÇ yoğun tek veritabanlarınızı sanal çekirdek tabanlı hiper ölçek katmanımızı, şu anda önizleme aşamasında olan ve GÇ yoğun veritabanı havuzlarınızı genel kullanıma açık İş Açısından Kritik katmanımızla geçirmeyi öneririz.  Aşağıdaki vCore tabanlı seçenekler, geçerli performanslarını korur veya iyileştirir ve Azure Hibrit Avantajı ve ayrılmış kapasite fiyatlandırmasıyla birlikte ne kadar tasarruf etmenizi sağlayabilir.

|**Şu anda açık...**|**Karşılaştırılabilir sanal çekirdek tabanlı ' a geçiş yapın...**|**Karşılaştırılabilir DTU tabanlı ' a geçiş yapın...**|
|---|---|---|
|Premium RS 1| Hiperscale 1 sanal çekirdek (4. nesil) veya İş Açısından Kritik 1 sanal çekirdek (4. nesil)|Premium 1|
|Premium RS 2| Hiper ölçek 2 sanal çekirdekler (4. nesil) veya İş Açısından Kritik 2 sanal çekirdek (4. nesil|Premium 2|
|Premium RS 4| Hiper ölçek 4 sanal çekirdek (4. nesil) veya İş Açısından Kritik 4 sanal çekirdek (4. nesil)|Premium 4
|Premium RS 6| Hyperscale 6 sanal çekirdekleri (4. nesil) veya İş Açısından Kritik 6 sanal çekirdek (4. nesil)|Premium 6|

|**Şu anda açık...**|**Karşılaştırılabilir sanal çekirdek tabanlı ' a geçiş yapın...**|**Karşılaştırılabilir DTU tabanlı ' a geçiş yapın...**|
|---|---|---|
|Premium RS havuzu 125 DTU|İş Açısından Kritik 2 sanal çekirdekler (4. nesil)|Premium havuz 125 eDTU|
|Premium RS havuzu 250 DTU|İş Açısından Kritik 2 sanal çekirdekler (4. nesil)|Premium havuz 250 eDTU|
|Premium RS havuzu 500 DTU|İş Açısından Kritik 4 sanal çekirdek (4. nesil)|Premium havuz 500 eDTU|
|Premium RS havuzu 1000 DTU|İş Açısından Kritik 8 sanal çekirdekler (4. nesil)|Premium havuz 1000 eDTU|

## <a name="take-advantage-of-our-new-offers"></a>Yeni tekliflerimizden yararlanın

Sanal çekirdek tabanlı satın alma modelindeki hizmet katmanlarımız, size %80 ve lisans dahil fiyatlandırmaya karşı tasarruf sağlayan özel teklifler için uygun değildir. [SQL Server için Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)ve lisans dahil olmak üzere %55 ' e varan bir lisansa sahip SQL Server Standard veya Enterprise Edition lisanslarını kullanın. Karma avantajını [Azure SQL veritabanı ayrılmış kapasite](sql-database-reserved-capacity.md) fiyatlandırmasıyla birleştirebilir ve en öne bir veya üç yıllık bir dönem boyunca kaydolduğunuzda %80 ' e kadar tasarruf edebilirsiniz.  Azure portal avantajlarından her ikisi de hemen etkinleştirin.

Bu değişiklik ile ilgili sorularınız veya endişeleriniz varsa veya geçiş yardımı gerekiyorsa, [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)'a başvurun.

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>DTU veya vCore modelinde bir Premium RS hizmet katmanından bir hizmet katmanına geçiş

### <a name="migration-of-a-database"></a>Bir veritabanının geçirilmesi

Bir veritabanını Premium RS hizmet katmanından DTU veya vCore modelindeki bir hizmet katmanına geçirmek, Premium RS hizmet katmanındaki hizmet katmanları arasında yükseltme veya eski sürüme düşürme konusunda da benzerdir.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Premium RS veritabanını DTU tabanlı veya sanal çekirdek tabanlı veritabanına dönüştürmek için veritabanı kopyası kullanma

Hedef işlem boyutu, kaynak veritabanının en büyük veritabanı boyutunu desteklediği sürece, kısıtlama tabanlı veya sanal çekirdek tabanlı işlem boyutu olmadan bir veritabanına Premium RS işlem boyutu olan bir veritabanını kopyalayabilir. Veritabanı kopyalama işlemi, kopyalama işleminin başlangıç saatinden itibaren verilerin anlık görüntüsünü oluşturur ve kaynak ile hedef arasında veri eşitlemesi gerçekleştirmez.

## <a name="next-steps"></a>Sonraki adımlar

- Tek veritabanı için kullanılabilen belirli işlem boyutları ve depolama boyutu seçimleri hakkındaki ayrıntılar için bkz. [tek veritabanları Için SQL veritabanı sanal çekirdek tabanlı kaynak sınırları](sql-database-vcore-resource-limits-single-databases.md)
- Elastik havuzlarda bulunan belirli işlem boyutları ve depolama boyutu seçimleri hakkındaki ayrıntılar için bkz. [elastik havuzlar Için SQL veritabanı sanal çekirdek tabanlı kaynak sınırları](sql-database-vcore-resource-limits-elastic-pools.md).
