---
title: Örnek havuzları (Önizleme)
description: Bu makalede Azure SQL veritabanı örnek havuzları (Önizleme) açıklanmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 8738d1ad54d3ab63d8d2efc939aa9daacbe91c13
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810400"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>SQL veritabanı örnek havuzları (Önizleme) nedir?

Örnek havuzlar, Azure SQL veritabanı 'nda daha küçük SQL örneklerini buluta daha küçük bir şekilde geçirmek için kullanışlı ve uygun maliyetli bir yol sağlayan yeni bir kaynaktır.

Örnek havuzları toplam geçiş gereksinimlerinize göre işlem kaynaklarını önceden sağlamanıza olanak tanır. Ardından, önceden sağlanan işlem düzeyinize ulaşana kadar tek tek yönetilen örnekleri dağıtabilirsiniz. Örneğin, 8 sanal çekirdekleri önceden sağlarsanız iki 2 sanal çekirdek ve bir 4 sanal çekirdek örneği dağıtabilir ve ardından veritabanlarını bu örneklere geçirebilirsiniz. Örnek havuzlarından önce, daha küçük ve daha az işlem yoğunluklu iş yükleri, buluta geçiş yaparken genellikle daha büyük bir yönetilen örneğe birleştirilmelidir. Veritabanı gruplarının büyük bir örneğe geçirilmesi gerekir, genellikle dikkatli kapasite planlama ve kaynak İdaresi, ek güvenlik konuları ve bazı ek veri birleştirme örnek düzeyinde çalışır.

Ayrıca, örnek havuzlar aynı alt ağda birden çok örnek havuzu ve birden çok tek örneği dağıtabilmeniz için yerel VNet tümleştirmesini destekler.


## <a name="key-capabilities-of-instance-pools"></a>Örnek havuzlarının temel özellikleri

Örnek havuzlar aşağıdaki avantajları sağlar:

1. 2 sanal çekirdek örnekleri barındırma özelliği. *yalnızca örnek havuzlardaki örneklere\** .
2. Öngörülebilir ve hızlı örnek dağıtım süresi (5 dakikaya kadar).
3. En az IP adresi ayırması.

Aşağıdaki diyagramda, bir sanal ağ alt ağı içinde dağıtılan birden çok örneğe sahip bir örnek havuzu gösterilmektedir.

![birden çok örneğe sahip örnek havuzu](./media/sql-database-instance-pools/instance-pools1.png)

Örnek havuzlar, sanal makinenin işlem boyutunun, havuz için ayrılan toplam sanal çekirdek sayısına bağlı olarak aynı sanal makinede birden çok örneğinin dağıtılmasını sağlar. Bu mimari, sanal makinenin birden çok örneğe *bölümlenmesini* sağlar. Bu, 2 sanal çekirdek (2 sanal çekirdek örnekleri yalnızca havuzlardaki örneklerde kullanılabilir) dahil olmak üzere, desteklenen bir boyut olabilir.

Havuz başlangıçta dağıtıldığında, havuzdaki örneklerde bulunan yönetim işlemleri çok daha hızlıdır. Bu işlemler, bir [sanal kümenin](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) dağıtımı veya uzantısı (adanmış sanal makine kümesi) yönetilen örneği sağlamanın bir parçası olmadığından daha hızlıdır.

Bir havuzdaki tüm örnekler aynı sanal makineyi paylaştığından, Toplam IP ayırması dağıtılan örnek sayısına bağlı değildir, bu da dar IP aralığına sahip alt ağlarda dağıtım için uygundur.

Her havuzun yalnızca dokuz IP adresi sabit bir IP ayırması vardır (alt ağda, kendi ihtiyaçları için ayrılan beş IP adresi dahil değildir). Ayrıntılar için bkz. [tek örnekler için alt ağ boyutu gereksinimleri](sql-database-managed-instance-determine-size-vnet-subnet.md).

## <a name="application-scenarios-for-instance-pools"></a>Örnek havuzları için uygulama senaryoları

Aşağıdaki listede, örnek havuzlarının göz önünde bulundurulması gereken başlıca kullanım durumları verilmiştir:

- *BIR SQL örnekleri grubunun* , çoğunluğu daha küçük bir boyut (örneğin, 2 veya 4 sanal çekirdek) olduğu bir kez geçirilmesi.
- *Öngörülebilir ve kısa örnek oluşturma veya ölçeklendirmenin* önemli olduğu senaryolar. Örneğin, yeni bir kiracının örnek düzeyi yetenekler gerektiren çok kiracılı SaaS uygulama ortamında dağıtılması.
- *Sabit maliyetli* veya *harcama limitine* sahip olan senaryolar önemli olur. Örneğin, bir sabit (veya seyrek değişen) boyutun paylaşılan geliştirme-test veya tanıtım ortamları çalıştırmak, gerektiğinde yönetilen örnekleri düzenli olarak dağıtırsınız.
- VNet alt ağında *en az IP adresi ayırmanın* önemli olduğu senaryolar. Bir havuzdaki tüm örnekler bir sanal makineyi paylaşıyor, bu nedenle ayrılan IP adreslerinin sayısı tek örnek olması durumunda daha düşük.


## <a name="architecture-of-instance-pools"></a>Örnek havuzlarının mimarisi

Örnek havuzlarının normal yönetilen örneklere benzer mimarisi vardır (*tek örnekler*).  [Azure sanal ağları (VNet) içindeki dağıtımları](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) desteklemek ve müşterilere yalıtım ve güvenlik sağlamak için, örnek havuzları [sanal kümelere](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)da güvenir. Sanal kümeler, müşterinin sanal ağ alt ağı içinde dağıtılan ayrılmış bir yalıtılmış sanal makine kümesini temsil eder.

İki dağıtım modeli arasındaki temel fark, örnek havuzların, [Windows Iş nesneleri](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects)kullanılarak yönetilen kaynak olan aynı sanal makine düğümünde birden çok SQL Server işlem dağıtımına izin vermesinin, tek örneklerin her zaman açık olduğu durumlar bir sanal makine düğümü.

Aşağıdaki diyagramda, aynı alt ağda dağıtılan bir örnek havuz ve iki tekil örnek gösterilmektedir ve her iki dağıtım modeli için de ana mimari ayrıntıları gösterilmektedir:

![örnek havuzu ve iki tekil örnek](./media/sql-database-instance-pools/instance-pools2.png)

Her örnek havuz, altında ayrı bir sanal küme oluşturur. Bir havuz içindeki örnekler ve aynı alt ağda dağıtılan tek örnekler, SQL Server işlemlerine ve ağ geçidi bileşenlerine ayrılan işlem kaynaklarını paylaşmaz, bu performans öngörülebilirlik sağlar.

## <a name="instance-pools-resource-limitations"></a>Örnek havuzları kaynak sınırlamaları

Örnek havuzlarıyla ve havuzların içindeki örneklerle ilgili çeşitli kaynat sınırlamaları vardır:

- Örnek havuzları yalnızca 5. nesil donanımında kullanılabilir.
- Bir havuzdaki örneklerin ayrılmış CPU ve RAM 'i vardır; bu nedenle, tüm örneklerde toplanan sanal çekirdek sayısı, havuza ayrılan Vcore sayısından küçük veya ona eşit olmalıdır.
- Tüm [örnek düzeyi sınırları](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) , bir havuz içinde oluşturulan örneklere uygulanır.
- Örnek düzeyi sınırlarına ek olarak, *örnek havuzu düzeyinde*uygulanan iki sınır da vardır:
  - Havuz başına toplam depolama boyutu (8 TB).
  - Havuz başına toplam veritabanı sayısı (100).

Tüm örneklerin tamamında toplam depolama alanı ayırma ve veritabanı sayısı, örnek havuzlarının açığa çıkarılan sınırlara eşit veya daha düşük olmalıdır.

- Örnek havuzları 8, 16, 24, 32, 40, 64 ve 80 sanal çekirdekleri destekler.
- Havuzlar içindeki yönetilen örnekler 2, 4, 8, 16, 24, 32, 40, 64 ve 80 sanal çekirdekleri destekler.
- Havuzlar içindeki yönetilen örnekler, şunlar dışında 32 GB ile 8 TB arasında depolama boyutlarını destekler:
  - 2 sanal çekirdek örneği 32 GB ile 640 GB arasındaki boyutları destekler
  - 4 sanal çekirdek örneği 32 GB ile 2 TB arasında boyutları destekler

[Hizmet katmanı özelliği](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) örnek havuzu kaynağıyla ilişkilendirilir, bu nedenle havuzdaki tüm örneklerin havuzun hizmet katmanıyla aynı hizmet katmanı olması gerekir. Şu anda yalnızca Genel Amaçlı hizmet katmanı kullanılabilir (geçerli Önizlemedeki sınırlamalar hakkında aşağıdaki bölüme bakın).

### <a name="public-preview-limitations"></a>Genel önizleme sınırlamaları

Genel Önizleme aşağıdaki sınırlamalara sahiptir:

- Şu anda yalnızca Genel Amaçlı hizmet katmanı kullanılabilir.
- Örnek havuzlar genel önizleme sırasında ölçeklendirilemez, böylece dağıtım önemli olduğundan dikkatli bir kapasite planlaması yapmanız önerilir.
- Örnek havuzu oluşturma ve yapılandırma için Azure portal desteği henüz kullanılamıyor. Örnek havuzlardaki tüm işlemler yalnızca PowerShell aracılığıyla desteklenir. Önceden oluşturulmuş havuzda ilk örnek dağıtımı yalnızca PowerShell üzerinden de desteklenir. Bir havuza dağıtıldıktan sonra, yönetilen Örnekler Azure portal kullanılarak güncellenebilir.
- Havuzun dışında oluşturulan yönetilen örnekler var olan bir havuza taşınamaz ve bir havuz içinde oluşturulan örnekler tek bir örnek veya başka bir havuz olarak dışına taşınamaz.
- Ayrılmış örnek fiyatlandırması (lisans dahil veya Azure Hibrit Avantajı) kullanılamıyor.

## <a name="sql-features-supported"></a>Desteklenen SQL özellikleri

Havuzlarda oluşturulan örnekler, [tek yönetilen örneklerde desteklenen uyumluluk düzeylerini ve özellikleri](sql-database-managed-instance.md#sql-features-supported)destekler.

Bir havuzda dağıtılan her yönetilen örnek SQL aracısının ayrı bir örneğine sahiptir.

Belirli değerleri (örneğin, örnek düzeyi harmanlama, saat dilimi, veri trafiği için ortak uç nokta, yük devretme grupları) seçmenizi gerektiren isteğe bağlı özellikler veya özellikler, örnek düzeyinde yapılandırılır ve bir havuzdaki her örnek için farklı olabilir.

## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Havuzlardaki yönetilen örneklerin ayrılmış sanal çekirdek ve RAM 'i olsa da, yerel disk (tempdb kullanımı için) ve ağ kaynakları paylaşır. Büyük olasılıkla, havuzda birden çok örnek aynı anda yüksek kaynak tüketimine sahip olursa *gürültülü komşu* efektini denemek mümkündür. Bu davranışı gözlemlerseniz, bu örnekleri daha büyük bir havuza veya tek örnek olarak dağıtmaya göz önünde bulundurun.

## <a name="security-considerations"></a>Güvenlikle ilgili dikkat edilmesi gerekenler

Bir havuzda dağıtılan örnekler aynı sanal makineyi paylaştığından, daha yüksek güvenlik riskleri sunan özellikleri devre dışı bırakmayı veya bu özelliklere yönelik erişim izinlerini güvenli bir şekilde kontrol etmek isteyebilirsiniz. Örneğin, CLR tümleştirmesi, yerel yedekleme ve geri yükleme, veritabanı e-postası vb.

## <a name="instance-pool-support-requests"></a>Örnek havuzu destek istekleri

[Azure Portal](https://portal.azure.com)örnek havuzları için destek istekleri oluşturun ve yönetin.

Örnek havuzu dağıtımıyla ilgili sorunlar yaşıyorsanız (oluşturma veya silme), **sorun alt türü** alanında **örnek havuzlar** belirttiğinizden emin olun.

![örnek havuzları destek isteği](./media/sql-database-instance-pools/support-request.png)

Bir havuz içindeki tek örneklerle veya veritabanlarıyla ilgili sorunlar yaşıyorsanız, Azure SQL veritabanı yönetilen örnekleri için normal bir destek bileti oluşturmanız gerekir.

Daha büyük yönetilen örnek dağıtımları oluşturmak için (örnek havuzlarla veya bunlarla birlikte), daha büyük bir bölgesel kota edinmeniz gerekebilir. [Daha büyük bir kota istemek için Standart yönetilen örnek yordamını](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance)kullanın, ancak örnek havuzlar kullanıyorsanız dağıtım mantığının, *Havuz düzeyindeki* toplam Vcore tüketimini, sizin olup olmadığınızı belirleme kotasıyla karşılaştırarak kotayı daha fazla artırmadan yeni kaynaklar oluşturmalarına izin verildi.

## <a name="instance-pool-billing"></a>Örnek havuzu Faturalaması

Örnek havuzları, işlem ve depolamayı bağımsız olarak ölçeklendirmeye olanak tanır. Müşteriler, sanal çekirdekler ile ölçülen havuz kaynağıyla ilişkili işlem için ve gigabayt cinsinden ölçülen her örnekle ilişkili depolama alanı için ödeme yapar (her örnek için ilk 32 GB ücretsizdir).

Havuzun sanal çekirdek fiyatı, bu havuzda kaç örnek dağıtıldığına bakılmaksızın ücretlendirilir.

Işlem fiyatı (sanal çekirdekler cinsinden ölçülür) için, iki fiyatlandırma seçeneği mevcuttur:

  1. *Lisans dahil*: mevcut SQL Server lisanslarını yazılım güvencesi ile uygulayın.
  2. *Azure hibrit avantajı*: SQL Server için Azure hibrit avantajı içeren daha düşük bir fiyat. Müşteriler, Yazılım Güvencesi kapsamındaki mevcut SQL Server lisanslarını kullanarak bu fiyatı kabul edebilir. Uygunluk ve diğer ayrıntılar için bkz. [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/).

Bir havuzdaki tek tek örnekler için farklı fiyatlandırma seçeneklerinin ayarlanması mümkün değildir. Üst havuzdaki tüm örnekler, lisans dahil fiyattan veya Azure Hibrit Avantajı fiyattan olmalıdır. Havuzun lisans modeli, havuz oluşturulduktan sonra değiştirilebilir.

> [!IMPORTANT]
> Havuzu için havuzdan farklı olan bir lisans modeli belirtirseniz, havuz fiyatı kullanılır ve örnek düzeyi değeri yok sayılır.

[Geliştirme ve test avantajına uygun abonelikler](https://azure.microsoft.com/pricing/dev-test/)üzerinde örnek havuzları oluşturursanız, Azure SQL yönetilen örneği 'nde yüzde 55 ' ye varan indirimli ücretler alırsınız.

Örnek havuzu fiyatlandırmasıyla ilgili tam Ayrıntılar için, [yönetilen örnek fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/sql-database/managed/) *örnek havuzlar* bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- Örnek havuzlarla çalışmaya başlamak için bkz. [SQL veritabanı örnek havuzları nasıl yapılır Kılavuzu](sql-database-instance-pools-how-to.md).
- İlk yönetilen örneğinizi oluşturmayı öğrenmek için bkz. [Hızlı Başlangıç Kılavuzu](sql-database-managed-instance-get-started.md).
- Özellikler ve karşılaştırma listesi için bkz. [SQL ortak özellikleri](sql-database-features.md).
- VNet yapılandırması hakkında daha fazla bilgi için bkz. [yönetilen örnek VNET yapılandırması](sql-database-managed-instance-connectivity-architecture.md).
- Yönetilen bir örnek oluşturan ve bir veritabanını bir yedekleme dosyasından geri yükleyen hızlı başlangıç için bkz. [yönetilen örnek oluşturma](sql-database-managed-instance-get-started.md).
- Geçiş için Azure veritabanı geçiş hizmeti 'ni (DMS) kullanan bir öğretici için bkz. [DMS kullanarak yönetilen örnek geçişi](../dms/tutorial-sql-server-to-managed-instance.md).
- Yerleşik sorun giderme zekası ile yönetilen örnek veritabanı performansının gelişmiş izlenmesi için bkz. [Azure SQL Analytics kullanarak Azure SQL veritabanını izleme](../azure-monitor/insights/azure-sql.md).
- Fiyatlandırma bilgileri için bkz. [SQL veritabanı yönetilen örnek fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/managed/).