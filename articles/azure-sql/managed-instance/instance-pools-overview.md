---
title: Azure SQL yönetilen örnek havuzu nedir?
titleSuffix: Azure SQL Managed Instance
description: Daha küçük SQL Server veritabanlarını ölçekli buluta geçirmek için kullanışlı ve uygun maliyetli bir yol sağlayan Azure SQL yönetilen örnek havuzları (Önizleme) hakkında bilgi edinin ve birden çok yönetilen örneği yönetin.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: bc345509db1c2a14afb0ae781eccad8f77395c18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97347073"
---
# <a name="what-is-an-azure-sql-managed-instance-pool-preview"></a>Azure SQL yönetilen örnek Havuzu (Önizleme) nedir?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL yönetilen örneğindeki örnek havuzlar, daha küçük SQL Server örnekleri buluta uygun ölçekte geçirmek için kullanışlı ve ekonomik bir yol sağlar.

Örnek havuzları toplam geçiş gereksinimlerinize göre işlem kaynaklarını önceden sağlamanıza olanak tanır. Ardından, önceden sağlanan işlem düzeyinize ulaşana kadar tek tek yönetilen örnekleri dağıtabilirsiniz. Örneğin, 8 sanal çekirdekleri önceden sağlarsanız, 2 2-vCore ve 1 4-vCore örneğini dağıtabilir ve ardından veritabanlarını bu örneklere geçirebilirsiniz. Örnek havuzlarından önce, daha küçük ve daha az işlem yoğunluklu iş yükleri, buluta geçiş yaparken genellikle daha büyük bir yönetilen örneğe birleştirilmelidir. Veritabanı gruplarının büyük bir örneğe geçirilmesi gerekir, genellikle dikkatli kapasite planlama ve kaynak İdaresi, ek güvenlik konuları ve bazı ek veri birleştirme örnek düzeyinde çalışır.

Ayrıca, örnek havuzlar aynı alt ağda birden çok örnek havuzu ve birden çok tek örneği dağıtabilmeniz için yerel VNet tümleştirmesini destekler.

## <a name="key-capabilities"></a>Temel işlevler

Örnek havuzlar aşağıdaki avantajları sağlar:

1. 2 sanal çekirdek örnekleri barındırma özelliği. *\* Yalnızca örnek havuzlardaki örnekler için*.
2. Öngörülebilir ve hızlı örnek dağıtım süresi (5 dakikaya kadar).
3. En az IP adresi ayırması.

Aşağıdaki diyagramda, bir sanal ağ alt ağı içinde dağıtılan birden çok yönetilen örneğe sahip bir örnek havuzu gösterilmektedir.

![birden çok örneğe sahip örnek havuzu](./media/instance-pools-overview/instance-pools1.png)

Örnek havuzlar, sanal makinenin işlem boyutunun havuz için ayrılan toplam sanal çekirdek sayısına bağlı olduğu aynı sanal makinede birden çok örneğinin dağıtılmasını sağlar. Bu mimari, sanal makinenin birden çok örneğe *bölümlemesini* sağlar. Bu, 2 sanal çekirdek (2 sanal çekirdek örnekleri yalnızca havuzlardaki örneklerde mevcuttur) dahil olmak üzere, desteklenen bir boyut olabilir.

İlk dağıtımdan sonra, havuzdaki örneklerde bulunan yönetim işlemleri çok daha hızlıdır. Bunun nedeni, bir [sanal kümenin](connectivity-architecture-overview.md#high-level-connectivity-architecture) dağıtılması veya uzantısının (adanmış sanal makine kümesi) yönetilen örneği sağlamanın bir parçası olmaması nedeniyle oluşur.

Bir havuzdaki tüm örnekler aynı sanal makineyi paylaştığından, Toplam IP ayırması dağıtılan örnek sayısına bağlı değildir, bu da dar IP aralığına sahip alt ağlarda dağıtım için uygundur.

Her havuzun yalnızca dokuz IP adresi sabit bir IP ayırması vardır (alt ağda, kendi ihtiyaçları için ayrılan beş IP adresi dahil değildir). Ayrıntılar için bkz. [tek örnekler için alt ağ boyutu gereksinimleri](vnet-subnet-determine-size.md).

## <a name="application-scenarios"></a>Uygulama senaryoları

Aşağıdaki listede, örnek havuzlarının göz önünde bulundurulması gereken başlıca kullanım durumları verilmiştir:

- *SQL Server örneklerinin bir grubunu* aynı anda (örneğin, 2 veya 4 sanal çekirdekler) büyük bir boyuta geçirme.
- *Öngörülebilir ve kısa örnek oluşturma veya ölçeklendirmenin* önemli olduğu senaryolar. Örneğin, yeni bir kiracının örnek düzeyi yetenekler gerektiren çok kiracılı SaaS uygulama ortamında dağıtılması.
- *Sabit maliyetli* veya *harcama limitine* sahip olan senaryolar önemli olur. Örneğin, bir sabit (veya seyrek değişen) boyutun paylaşılan geliştirme-test veya tanıtım ortamları çalıştırmak, gerektiğinde yönetilen örnekleri düzenli olarak dağıtırsınız.
- VNet alt ağında *en az IP adresi ayırmanın* önemli olduğu senaryolar. Bir havuzdaki tüm örnekler bir sanal makineyi paylaşıyor, bu nedenle ayrılan IP adreslerinin sayısı tek örnek olması durumunda daha düşük.

## <a name="architecture"></a>Mimari

Örnek havuzlarının normal (*tek*) yönetilen örneklere benzer bir mimarisi vardır. [Azure sanal ağları içindeki dağıtımları](../../virtual-network/virtual-network-for-azure-services.md) desteklemek ve müşterilere yalıtım ve güvenlik sağlamak için, örnek havuzları [sanal kümelere](connectivity-architecture-overview.md#high-level-connectivity-architecture)da güvenir. Sanal kümeler, müşterinin sanal ağ alt ağı içinde dağıtılan ayrılmış bir yalıtılmış sanal makine kümesini temsil eder.

İki dağıtım modeli arasındaki temel fark, örnek havuzların [Windows iş nesneleri](/windows/desktop/ProcThread/job-objects)kullanılarak yönetilen aynı sanal makine düğümünde birden çok SQL Server işlem dağıtımına izin verişleridir, tek örnekler her zaman bir sanal makine düğümünde yer alır.

Aşağıdaki diyagramda, aynı alt ağda dağıtılan bir örnek havuz ve iki tekil örnek gösterilmektedir ve her iki dağıtım modeli için de ana mimari ayrıntıları gösterilmektedir:

![Örnek havuzu ve iki tekil örnek](./media/instance-pools-overview/instance-pools2.png)

Her örnek havuz, altında ayrı bir sanal küme oluşturur. Bir havuz içindeki örnekler ve aynı alt ağda dağıtılan tek örnekler, SQL Server işlemlerine ve ağ geçidi bileşenlerine ayrılan işlem kaynaklarını paylaşarak performans öngörülebilirlik sağlar.

## <a name="resource-limitations"></a>Kaynak sınırlamaları

Örnek havuzlarıyla ve havuzların içindeki örneklerle ilgili çeşitli kaynat sınırlamaları vardır:

- Örnek havuzları yalnızca 5. nesil donanımında kullanılabilir.
- Bir havuz içindeki yönetilen örnekler ayrılmış CPU ve RAM 'e sahiptir, bu nedenle tüm örneklerde toplanan sanal çekirdek sayısı, havuza ayrılan sanal çekirdekler sayısından küçük veya ona eşit olmalıdır.
- Tüm [örnek düzeyi sınırları](resource-limits.md#service-tier-characteristics) , bir havuz içinde oluşturulan örneklere uygulanır.
- Örnek düzeyi limitlerinin yanı sıra, *örnek havuzu düzeyinde* uygulanan iki sınır de vardır:
  - Havuz başına toplam depolama boyutu (8 TB).
  - Havuz başına Toplam Kullanıcı veritabanı sayısı. Bu sınır, havuz sanal çekirdekleri değerine bağlıdır:
    - 8 sanal çekirdek havuzu en fazla 200 veritabanını destekler,
    - 16 sanal çekirdek havuzu en fazla 400 veritabanını destekler,
    - 24 ve daha büyük sanal çekirdek havuzu en fazla 500 veritabanını destekler.
- Örnek havuzunda dağıtılan örnekler için AAD Yöneticisi ayarlanamaz, bu nedenle AAD kimlik doğrulaması kullanılamaz.

Toplam depolama alanı ayırma ve tüm örneklerde bulunan veritabanlarının sayısı, örnek havuzlarının açığa çıkarılan limitlere eşit veya ondan daha düşük olmalıdır.

- Örnek havuzları 8, 16, 24, 32, 40, 64 ve 80 sanal çekirdekleri destekler.
- Havuzlar içindeki yönetilen örnekler 2, 4, 8, 16, 24, 32, 40, 64 ve 80 sanal çekirdekleri destekler.
- Havuzlar içindeki yönetilen örnekler, şunlar dışında 32 GB ile 8 TB arasında depolama boyutlarını destekler:
  - 2 sanal çekirdek örneği 32 GB ve 640 GB arasındaki boyutları destekler,
  - 4 sanal çekirdek örneği 32 GB ile 2 TB arasındaki boyutları destekler.
- Havuzlar içindeki yönetilen örneklerin, örnek başına en fazla 50 kullanıcı veritabanını destekleyen 2 sanal çekirdek örneği dışında, örnek başına 100 Kullanıcı veritabanı sınırı vardır.

[Hizmet katmanı özelliği](resource-limits.md#service-tier-characteristics) örnek havuzu kaynağıyla ilişkilendirilir, bu nedenle havuzdaki tüm örneklerin havuzun hizmet katmanıyla aynı hizmet katmanı olması gerekir. Şu anda yalnızca Genel Amaçlı hizmet katmanı kullanılabilir (geçerli Önizlemedeki sınırlamalar hakkında aşağıdaki bölüme bakın).

### <a name="public-preview-limitations"></a>Genel önizleme sınırlamaları

Genel Önizleme aşağıdaki sınırlamalara sahiptir:

- Şu anda yalnızca Genel Amaçlı hizmet katmanı kullanılabilir.
- Örnek havuzları genel önizleme sırasında ölçeklendirilemez, bu nedenle dağıtımın önemli olması için dikkatli bir kapasite planlaması yapmanız gerekir.
- Örnek havuzu oluşturma ve yapılandırma için Azure portal desteği henüz kullanılamıyor. Örnek havuzlardaki tüm işlemler yalnızca PowerShell aracılığıyla desteklenir. Önceden oluşturulmuş havuzda ilk örnek dağıtımı yalnızca PowerShell üzerinden de desteklenir. Bir havuza dağıtıldıktan sonra, yönetilen Örnekler Azure portal kullanılarak güncelleştirilebilecek.
- Havuzun dışında oluşturulan yönetilen örnekler mevcut bir havuza taşınamaz ve bir havuz içinde oluşturulan örnekler, tek bir örnek veya başka bir havuz olarak dışına taşınamaz.
- [Ayrılan kapasite](../database/reserved-capacity-overview.md) örneği fiyatlandırması kullanılamıyor.

## <a name="sql-features-supported"></a>Desteklenen SQL özellikleri

Havuzlarda oluşturulan yönetilen örnekler, [tek yönetilen örneklerde desteklenen aynı uyumluluk düzeylerini ve özellikleri](sql-managed-instance-paas-overview.md#sql-features-supported)destekler.

Bir havuzda dağıtılan her yönetilen örnek SQL aracısının ayrı bir örneğine sahiptir.

Belirli değerleri (örneğin, örnek düzeyi harmanlama, saat dilimi, veri trafiği için ortak uç nokta, yük devretme grupları) seçmenizi gerektiren isteğe bağlı özellikler veya özellikler, örnek düzeyinde yapılandırılır ve bir havuzdaki her örnek için farklı olabilir.

## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Havuzlardaki yönetilen örneklerin ayrılmış sanal çekirdek ve RAM 'i olsa da, yerel disk (tempdb kullanımı için) ve ağ kaynakları paylaşır. Büyük olasılıkla, havuzda birden çok örnek aynı anda yüksek kaynak tüketimine sahip olursa *gürültülü komşu* efektini denemek mümkündür. Bu davranışı gözlemlerseniz, bu örnekleri daha büyük bir havuza veya tek örnek olarak dağıtmaya göz önünde bulundurun.

## <a name="security-considerations"></a>Güvenlik konuları

Bir havuzda dağıtılan örnekler aynı sanal makineyi paylaştığından, daha yüksek güvenlik riskleri sunan özellikleri devre dışı bırakmayı veya bu özelliklere yönelik erişim izinlerini güvenli bir şekilde kontrol etmek isteyebilirsiniz. Örneğin, CLR tümleştirmesi, yerel yedekleme ve geri yükleme, veritabanı e-postası vb.

## <a name="instance-pool-support-requests"></a>Örnek havuzu destek istekleri

[Azure Portal](https://portal.azure.com)örnek havuzları için destek istekleri oluşturun ve yönetin.

Örnek havuzu dağıtımıyla ilgili sorunlar yaşıyorsanız (oluşturma veya silme), **sorun alt türü** alanında **örnek havuzlar** belirttiğinizden emin olun.

![Örnek havuzları destek isteği](./media/instance-pools-overview/support-request.png)

Bir havuz içindeki tek bir yönetilen örnekle veya veritabanıyla ilgili sorunlar yaşıyorsanız, Azure SQL yönetilen örneği için düzenli bir destek bileti oluşturmanız gerekir.

Daha büyük SQL yönetilen örnek dağıtımları oluşturmak için (örnek havuzlarla birlikte veya olmadan), daha büyük bir bölgesel kota edinmeniz gerekebilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı Için istek kotası artışları](../database/quota-increase-request.md). Örnek havuzlarının dağıtım mantığı, kotayı daha fazla artırmadan yeni kaynaklar oluşturmanıza izin verilip verilmeyeceğini öğrenmek için havuz düzeyindeki toplam vCore tüketimini *havuzunuzla* karşılaştırır.

## <a name="instance-pool-billing"></a>Örnek havuzu Faturalaması

Örnek havuzları, işlem ve depolamayı bağımsız olarak ölçeklendirmeye olanak tanır. Müşteriler, sanal çekirdekler ile ölçülen havuz kaynağıyla ilişkili işlem için ve gigabayt cinsinden ölçülen her örnekle ilişkili depolama alanı için ödeme yapar (her örnek için ilk 32 GB ücretsizdir).

Havuzun sanal çekirdek fiyatı, bu havuzda kaç örnek dağıtıldığına bakılmaksızın ücretlendirilir.

İşlem fiyatı (sanal çekirdekler cinsinden ölçülür) için, iki fiyatlandırma seçeneği mevcuttur:

  1. *Lisans dahil*: SQL Server lisans fiyatları dahildir. Bu, Yazılım Güvencesi kapsamındaki mevcut SQL Server lisanslarını uygulamalarını seçen müşteriler içindir.
  2. *Azure hibrit avantajı*: SQL Server için Azure hibrit avantajı içeren daha düşük bir fiyat. Müşteriler, Yazılım Güvencesi kapsamındaki mevcut SQL Server lisanslarını kullanarak bu fiyatı kabul edebilir. Uygunluk ve diğer ayrıntılar için bkz. [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/).

Bir havuzdaki tek tek örnekler için farklı fiyatlandırma seçeneklerinin ayarlanması mümkün değildir. Üst havuzdaki tüm örnekler, lisans dahil fiyattan veya Azure Hibrit Avantajı fiyattan olmalıdır. Havuzun lisans modeli, havuz oluşturulduktan sonra değiştirilebilir.

> [!IMPORTANT]
> Havuzu için havuzdan farklı olan bir lisans modeli belirtirseniz, havuz fiyatı kullanılır ve örnek düzeyi değeri yok sayılır.

[Geliştirme ve test avantajına uygun abonelikler](https://azure.microsoft.com/pricing/dev-test/)üzerinde örnek havuzları oluşturursanız, Azure SQL yönetilen örneği 'nde yüzde 55 ' ye varan indirimli ücretler alırsınız.

Örnek havuzu fiyatlandırmasıyla ilgili tam Ayrıntılar için, [SQL yönetilen örnek fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/sql-database/managed/) *örnek havuzlar* bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- Örnek havuzlarla çalışmaya başlamak için bkz. [SQL yönetilen örnek havuzları nasıl yapılır Kılavuzu](instance-pools-configure.md).
- İlk yönetilen örneğinizi oluşturmayı öğrenmek için bkz. [Hızlı Başlangıç Kılavuzu](instance-create-quickstart.md).
- Özellikler ve karşılaştırma listesi için bkz. [Azure SQL ortak özellikleri](../database/features-comparison.md).
- VNet yapılandırması hakkında daha fazla bilgi için bkz. [SQL yönetilen örnek VNET yapılandırması](connectivity-architecture-overview.md).
- Yönetilen bir örnek oluşturan ve bir veritabanını bir yedekleme dosyasından geri yükleyen hızlı başlangıç için bkz. [yönetilen örnek oluşturma](instance-create-quickstart.md).
- Geçiş için Azure veritabanı geçiş hizmeti 'ni kullanma hakkında bir öğretici için bkz. [veritabanı geçiş hizmeti kullanılarak SQL yönetilen örnek geçişi](../../dms/tutorial-sql-server-to-managed-instance.md).
- Yerleşik sorun giderme zekası ile SQL yönetilen örnek veritabanı performansının gelişmiş izlenmesi için bkz. [Azure SQL Analytics kullanarak Azure SQL yönetilen örneğini izleme](../../azure-monitor/insights/azure-sql.md).
- Fiyatlandırma bilgileri için bkz. [SQL yönetilen örnek fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/managed/).
