---
title: Yüksek kullanılabilirlik
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Azure SQL veritabanı ve SQL yönetilen örnek hizmeti yüksek kullanılabilirlik özellikleri ve özellikleri hakkında bilgi edinin
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: sstein, emlisa
ms.date: 10/28/2020
ms.openlocfilehash: 21ac73b461ebcb171f48621aa27a16dfc0e8c936
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781748"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL veritabanı ve SQL yönetilen örneği için yüksek kullanılabilirlik
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve SQL yönetilen örneği 'nin yüksek kullanılabilirlik mimarisinin hedefi, veritabanınızın en az% 99,99 ' un çalışır durumda olmasını sağlamaktır (farklı katmanlara yönelik belirli SLA hakkında daha fazla bilgi Için, lütfen [Azure SQL veritabanı ve SQL yönetilen örneği Için SLA](https://azure.microsoft.com/support/legal/sla/sql-database/)'ya başvurun), bakım işlemlerinin ve kesintilerin etkisi hakkında endişelenmeden emin olmaktır. Azure, düzeltme eki uygulama, yedeklemeler, Windows ve Azure SQL yükseltmeleri gibi kritik bakım görevlerini ve temel alınan donanım, yazılım veya ağ arızaları gibi planlanmamış olayları otomatik olarak işler.  Azure SQL veritabanı 'nda temel alınan veritabanı düzeltme eki uygulanmış veya başarısız olduğunda, uygulamanızda [yeniden deneme mantığı](develop-overview.md#resiliency) kullanırsanız kesinti olmaz. SQL veritabanı ve SQL yönetilen örneği, verilerinizin her zaman kullanılabilir olmasını sağlamak için en kritik koşullarda bile hızlı bir şekilde kurturabilir.

Yüksek kullanılabilirlik çözümü, hatalar nedeniyle kaydedilmiş verilerin kaybolmamasını, bakım işlemlerinin iş yükünüzü etkilememesini ve veritabanının yazılım mimarinizdeki tek hata noktası olmamasını sağlamak üzere tasarlanmıştır. Veritabanı yükseltilirken veya korunurken iş yükünü durdurmanız gereken bakım pencereleri veya daha az zaman yoktur.

İki yüksek kullanılabilirliğe sahip mimari modeli vardır:

- İşlem ve depolama ayrımı temelinde kullanılan **Standart kullanılabilirlik modeli** .  Bu, uzak depolama katmanının yüksek düzeyde kullanılabilirliğini ve güvenilirliğini kullanır. Bu mimari, bakım etkinlikleri sırasında bazı performans düşüşüne neden olan bütçe odaklı iş uygulamalarını hedefler.
- Veritabanı altyapısı işlemlerinin bir kümesini temel alan **Premium kullanılabilirlik modeli** . Her zaman kullanılabilir veritabanı altyapısı düğümlerinin bir çekirdeği olduğundan emin olur. Bu mimari, yüksek GÇ performansı, yüksek işlem oranı ve bakım etkinlikleri sırasında iş yükünüzün en düşük performans etkisini güvence altına alarak görev açısından kritik uygulamaları hedefler.

SQL veritabanı ve SQL yönetilen örneği her ikisi de SQL Server veritabanı altyapısının ve Windows işletim sisteminin en son kararlı sürümünde çalışır ve çoğu kullanıcı, yükseltmelerin sürekli olarak gerçekleştirildiğini fark etmez.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Temel, standart ve Genel Amaçlı hizmet katmanı yerel olarak yedekli kullanılabilirlik

Temel, standart ve Genel Amaçlı hizmet katmanları hem sunucusuz hem de sağlanan işlem için standart kullanılabilirlik mimarisinden yararlanır. Aşağıdaki şekilde, ayrılmış işlem ve Depolama katmanlarına sahip dört farklı düğüm gösterilmektedir.

![İşlem ve depolama ayrımı](./media/high-availability-sla/general-purpose-service-tier.png)

Standart kullanılabilirlik modeli iki katman içerir:

- `sqlservr.exe`İşlemi çalıştıran ve yalnızca geçici ve önbelleğe alınmış verileri içeren, örneğin tempdb, eklenen SSD üzerinde model veritabanları ve bellekteki önbellek, arabellek havuzu ve columnstore havuzu gibi durum bilgisi olmayan bir işlem katmanı. Bu durum bilgisiz düğüm, Azure Service Fabric tarafından başlatılır `sqlservr.exe` , düğümün sistem durumunu denetler ve gerekirse başka bir düğüme yük devretme işlemi gerçekleştirir.
- Azure Blob depolamada depolanan veritabanı dosyaları (. mdf/. ldf) ile durum bilgisi olan bir veri katmanı. Azure Blob depolamada yerleşik veri kullanılabilirliği ve artıklık özelliği bulunur. İşlem kilitlense bile, veri dosyasındaki günlük dosyasında veya sayfada bulunan her kaydın korunacağından emin olur `sqlservr.exe` .

Veritabanı altyapısı veya işletim sistemi yükseltildiğinde ya da bir hata algılandığında, Azure Service Fabric durum bilgisiz `sqlservr.exe` işlemi yeterli boş kapasiteye sahip başka bir durum bilgisi olmayan işlem düğümüne taşıyacaktır. Azure Blob depolama alanındaki veriler taşımadan etkilenmez ve veri/günlük dosyaları yeni başlatılmış `sqlservr.exe` işleme eklenir. Bu işlem% 99,99 kullanılabilirliği garanti eder, ancak yeni `sqlservr.exe` işlem soğuk önbellek ile başladığından bu yana yoğun bir iş yükü geçiş sırasında bazı performans düşüşüne neden olabilir.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Genel Amaçlı hizmet katmanı bölgesi gereksiz kullanılabilirliği (Önizleme)

Genel amaçlı hizmet katmanı için bölgesel olarak yedekli yapılandırma hem sunucusuz hem de sağlanan işlem için sunulmaktadır. Bu yapılandırma, [](../../availability-zones/az-overview.md)   bir Azure bölgesindeki birden çok fiziksel konumda bulunan veritabanlarını çoğaltmak için Azure kullanılabilirlik alanları kullanır.Bölge artıklığı ' nı seçerek, yeni ve mevcut sunuculesss 'leri ve sağlanan genel amaçlı tek veritabanlarını ve esnek havuzları, uygulama mantığındaki herhangi bir değişiklik yapmadan çok daha büyük bir veri merkezi kesintileri dahil olmak üzere çok daha büyük bir başarısızlık kümesiyle dayanıklı hale getirebilirsiniz.

Genel amaçlı katmanın bölge yedekli yapılandırması iki katmana sahiptir:  

- ZRS 'de (bölge yedekli depolama) depolanan veritabanı dosyaları (. mdf/. ldf) ile durum bilgisi olan bir veri katmanı. [ZRS](../../storage/common/storage-redundancy.md) kullanma veri ve günlük dosyaları, fiziksel olarak yalıtılmış üç Azure kullanılabilirlik alanı arasında zaman uyumlu olarak kopyalanır.
- sqlservr.exe sürecini çalıştıran ve yalnızca geçici ve önbelleğe alınmış veriler (örneğin, eklenen SSD üzerinde model veritabanları, bağlı SSD üzerinde model veritabanları, ve bellek olarak plan önbelleği, arabellek havuzu ve columnstore havuzu) içeren durum bilgisiz işlem katmanı. Bu durum bilgisiz düğüm, sqlservr.exe Başlatan, düğümün sistem durumunu denetleyen ve gerekirse başka bir düğüme yük devretme gerçekleştiren Azure Service Fabric tarafından çalıştırılır. Bölgesel olarak yedekli sunucusuz ve sağlanan genel amaçlı veritabanları için, yedek kapasiteye sahip düğümler diğer Kullanılabilirlik Alanları yük devretme için kullanıma hazırdır.

Genel amaçlı hizmet katmanı için yüksek kullanılabilirlik mimarisinin bölge yedekli sürümü aşağıdaki diyagramda gösterilmiştir:

![Genel amaçlı bölge yedekli yapılandırması](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> Bölge yedekli yapılandırma yalnızca 5. nesil işlem donanımı seçildiğinde kullanılabilir. Bu özellik SQL yönetilen örneği 'nde kullanılamaz. Sunucusuz ve sağlanan genel amaçlı katman için bölge yedekli yapılandırma yalnızca şu bölgelerde kullanılabilir: Doğu ABD, Doğu ABD 2, Batı ABD 2, Kuzey Avrupa, Batı Avrupa, Güneydoğu Asya, Avustralya Doğu, Japonya Doğu, UK Güney ve Fransa Orta.

> [!NOTE]
> 80 sanal çekirdek boyutundaki Genel Amaçlı veritabanları, bölgesel olarak yedekli yapılandırma ile performans düşüşü yaşayabilir. Ayrıca, yedekleme, geri yükleme, veritabanı kopyalama, coğrafi-DR ilişkilerini ayarlama ve bir bölge yedekli veritabanını İş Açısından Kritik 'ten Genel Amaçlı daha eski sürüme indirme gibi işlemler, 1 TB 'tan daha büyük veritabanları için daha yavaş performans yaşayabilir. Daha fazla bilgi için lütfen [bir veritabanını ölçeklendirirken gecikme süreli belgelerimize](single-database-scale.md) bakın.
> 
> [!NOTE]
> Önizleme, ayrılmış örnek kapsamında değil

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Premium ve İş Açısından Kritik hizmet katmanı yerel olarak yedekli kullanılabilirlik

Premium ve İş Açısından Kritik hizmet katmanları, işlem kaynaklarını ( `sqlservr.exe` işlem) ve depolamayı (yerel olarak bağlı SSD) tek bir düğümde tümleştiren Premium kullanılabilirlik modelinden yararlanır. Yüksek kullanılabilirlik, üç-dört düğümlü küme oluştururken hem işlem hem de depolamanın ek düğümlere çoğaltılmasıyla elde edilir.

![Veritabanı altyapısı düğümlerinin kümesi](./media/high-availability-sla/business-critical-service-tier.png)

Temel alınan veritabanı dosyaları (. mdf/. ldf), iş yükünüze çok düşük gecikmeli GÇ sağlamak üzere eklenmiş SSD depolama alanına yerleştirilir. Yüksek kullanılabilirlik, [her zaman açık kullanılabilirlik grupları](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)SQL Server benzer bir teknoloji kullanılarak uygulanır. Küme, okuma/yazma müşteri iş yükleri için erişilebilen tek bir birincil çoğaltma ve verilerin kopyalarını içeren üç ikincil çoğaltma (işlem ve depolama) içerir. Birincil düğüm değişiklikleri sürekli olarak ikincil düğümlere gönderir ve her bir işlem gerçekleştirilmeden önce verilerin en az bir ikincil çoğaltmayla eşitlenmesini sağlar. Bu işlem, birincil düğüm herhangi bir nedenden dolayı kilitlenirse, her zaman yük devretmek için tamamen eşitlenmiş bir düğüm vardır. Yük devretme işlemi Azure Service Fabric tarafından başlatılır. İkincil çoğaltma yeni birincil düğüm olduktan sonra, kümede yeterli düğüm (çekirdek kümesi) olduğundan emin olmak için başka bir ikincil çoğaltma oluşturulur. Yük devretme işlemi tamamlandıktan sonra Azure SQL bağlantıları otomatik olarak yeni birincil düğüme yönlendirilir.

Ek bir avantaj olarak Premium kullanılabilirlik modeli, salt okunurdur Azure SQL bağlantılarını ikincil çoğaltmalardan birine yeniden yönlendirebilme özelliği içerir. Bu özelliğe [okuma ölçeği](read-scale-out.md)genişletme denir. Birincil çoğaltmadan, analitik iş yükleri gibi salt okuma işlemlerini yük dışı bırakmak için ek ücret olmadan %100 ek işlem kapasitesi sağlar.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Premium ve İş Açısından Kritik hizmet katmanı bölgesi gereksiz kullanılabilirliği 

Varsayılan olarak, Premium kullanılabilirlik modeli için düğümlerin kümesi aynı veri merkezinde oluşturulur. [Azure kullanılabilirlik alanları](../../availability-zones/az-overview.md)tanıtımı Ile SQL veritabanı, iş açısından kritik veritabanının farklı çoğaltmalarını aynı bölgedeki farklı kullanılabilirlik bölgelerine yerleştirebilir. Tek bir başarısızlık noktasını ortadan kaldırmak için, denetim halkası aynı zamanda birden çok bölgede üç ağ geçidi halkaları (GW) olarak da yinelenir. Belirli bir ağ geçidi halkası yönlendirmesi [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (ATM) tarafından denetlenir. Premium veya İş Açısından Kritik hizmet katmanlarında bölge yedekli yapılandırma ek veritabanı yedekliliği oluşturmadığından, ek ücret ödemeden etkinleştirebilirsiniz. Bölgesel olarak yedekli bir yapılandırma seçerek, Premium veya İş Açısından Kritik veritabanlarınızı, uygulama mantığındaki herhangi bir değişiklik yapmadan çok daha büyük bir veri merkezi kesintileri dahil olmak üzere çok daha büyük bir başarısızlık kümesine dayanıklı hale getirebilirsiniz. Ayrıca, mevcut Premium veya İş Açısından Kritik veritabanlarını veya havuzları bölge yedekli yapılandırmasına de dönüştürebilirsiniz.

Bölge yedekli veritabanlarının aralarında biraz uzaklıktan farklı veri merkezlerinde çoğaltmaları olduğundan, artan ağ gecikmesi çalışma süresini artırabilir ve böylece bazı OLTP iş yüklerinin performansını etkileyebilir. Bölge artıklığı ayarını devre dışı bırakarak her zaman tek bölge yapılandırmasına dönebilirsiniz. Bu işlem, normal hizmet katmanı yükseltmesine benzer bir çevrimiçi işlemdir. İşlemin sonunda, veritabanı veya havuz, bölge yedekli halkadan tek bir bölge halkaine geçirilir veya tam tersi de geçerlidir.

> [!IMPORTANT]
> İş Açısından Kritik katmanını kullanırken, bölge yedekli yapılandırma yalnızca 5. nesil işlem donanımı seçildiğinde kullanılabilir. Bölge yedekli veritabanlarını destekleyen bölgeler hakkında güncel bilgiler için bkz. [bölgeye göre Hizmetler desteği](../../availability-zones/az-region.md).

> [!NOTE]
> Bu özellik SQL yönetilen örneği 'nde kullanılamaz.

Yüksek kullanılabilirlik mimarisinin bölge yedekli sürümü aşağıdaki diyagram tarafından gösterilmiştir:

![yüksek kullanılabilirlik mimarisi bölgesi yedekli](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Hiper ölçek hizmet katmanı kullanılabilirliği

Hiper ölçek hizmet katmanı mimarisi, [Dağıtılmış işlevler mimarisinde](./service-tier-hyperscale.md#distributed-functions-architecture) açıklanmıştır ve yalnızca SQL veritabanı için KULLANILABILIR, SQL yönetilen örneği değildir.

![Hiper ölçekli işlevsel mimari](./media/high-availability-sla/hyperscale-architecture.png)

Hiper ölçekte kullanılabilirlik modeli dört katman içerir:

- `sqlservr.exe`İşlemleri çalıştıran ve yalnızca geçici ve önbelleğe alınmış verileri içeren, bağlı SSD 'de kapsayan ve önbellek, arabellek havuzu ve columnstore havuzu gibi durum bilgisi olmayan bir işlem katmanı. Bu durum bilgisiz katmanı, birincil işlem çoğaltmasını ve isteğe bağlı olarak, yük devretme hedefleri olarak kullanılabilecek bir dizi ikincil işlem çoğaltmasını içerir.
- Sayfa sunucuları tarafından biçimlendirilen durum bilgisi olmayan bir depolama katmanı. Bu katman, `sqlservr.exe` işlem çoğaltmaları üzerinde çalışan işlemler için dağıtılmış depolama altyapısıdır. Her sayfa sunucusu yalnızca geçici ve önbelleğe alınmış verileri içerir, örneğin takılı SSD üzerinde RBPEX önbelleği ve bellekte önbelleğe alınan veri sayfaları. Her sayfa sunucusunun, Yük Dengeleme, yedeklilik ve yüksek kullanılabilirlik sağlamak için etkin-etkin bir yapılandırmada eşleştirilmiş bir sayfa sunucusu vardır.
- Günlük hizmeti işlemi, işlem günlüğü giriş bölgesi ve işlem günlüğü uzun vadeli depolama alanı çalıştıran işlem düğümü tarafından oluşturulan, durum bilgisi olan işlem günlüğü depolama katmanı. Giriş bölgesi ve uzun süreli depolama, işlem günlüğü için kullanılabilirlik ve [Artıklık](../../storage/common/storage-redundancy.md) sağlayan ve kaydedilmiş işlemler için veri dayanıklılığı sağlayan Azure depolama 'yı kullanır.
- Azure depolama 'da depolanan ve sayfa sunucuları tarafından güncelleştirilmiş veritabanı dosyaları (. mdf/. ndf) ile durum bilgisi olan bir veri depolama katmanı. Bu katman, Azure depolama 'nın veri kullanılabilirliği ve [Artıklık](../../storage/common/storage-redundancy.md) özelliklerini kullanır. Bir veri dosyasındaki her sayfanın, hiper ölçekli mimarinin diğer katmanlarındaki işlemler çöktüğünde veya işlem düğümleri başarısız olsa bile korunacağından emin olur.

Tüm hiperölçek katmanlarında işlem düğümleri Azure Service Fabric üzerinde çalışır ve her bir düğümün sistem durumunu denetler ve gerektiğinde kullanılabilir sağlıklı düğümlere yük devretme gerçekleştirir.

Hiper ölçekte yüksek kullanılabilirlik hakkında daha fazla bilgi için bkz. [Hyperscale 'de veritabanı yüksek kullanılabilirliği](./service-tier-hyperscale.md#database-high-availability-in-hyperscale).


## <a name="accelerated-database-recovery-adr"></a>Hızlandırılmış veritabanı kurtarma (ADR)

[Hızlandırılmış veritabanı kurtarma (ADR)](../accelerated-database-recovery.md) , özellikle uzun süre çalışan işlemler söz konusu olduğunda veritabanı kullanılabilirliğini büyük ölçüde geliştiren yeni bir veritabanı altyapısı özelliğidir. ADR Şu anda Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics için kullanılabilir.

## <a name="testing-application-fault-resiliency"></a>Uygulama hatası dayanıklılığı sınanıyor

Yüksek kullanılabilirlik, veritabanı uygulamanız için saydam bir şekilde çalışan SQL Veritabanı ve SQL Yönetilen Örneği platformunun temel bir parçasıdır. Bununla birlikte uygulamayı üretime dağıtmadan önce, planlanmış veya planlanmamış olaylar sırasında başlatılan otomatik yük devretme işlemlerinin uygulamayı nasıl etkileyebileceğini test etmek isteyeceğinizin farkındayız. Bir veritabanını, elastik havuzu veya yönetilen örneği yeniden başlatmak için özel bir API çağırarak, yük devretmeyi el ile tetikleyebilirsiniz. Bir bölge yedekli sunucusuz veya sağlanmış Genel Amaçlı veritabanı ya da elastik havuz durumunda, API çağrısı, eski birincil bölge kullanılabilirlik bölgesinden farklı bir kullanılabilirlik bölgesindeki yeni birincil bağlantı ile istemci bağlantılarını yeniden yönlendirmeye neden olur. Bu nedenle, yük devretmenin var olan veritabanı oturumlarını nasıl etkilediğini test etmeye ek olarak, ağ gecikmede yapılan değişiklikler nedeniyle uçtan uca performansı değiştirdiğinizi de doğrulayabilirsiniz. Yeniden başlatma işlemi zorlandığından ve çok sayıda, platformu vurgulamada, her veritabanı, elastik havuz veya yönetilen örnek için yalnızca bir yük devretme çağrısına her 15 dakikada bir izin verilir.

PowerShell, REST API veya Azure CLı kullanılarak yük devretme başlatılabilir:

|Dağıtım türü|PowerShell|REST API| Azure CLI|
|:---|:---|:---|:---|
|Veritabanı|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Veritabanı yük devretmesi](/rest/api/sql/databases/failover)|[az Rest](/cli/azure/reference-index#az_rest) , Azure CLI 'dan bir REST API çağrısı çağırmak için kullanılabilir|
|Elastik havuz|[Invoke-Azsqtalayapışpoolfailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Elastik havuz yük devretme](/javascript/api/@azure/arm-sql/elasticpools#failover_string__string__string__msRest_RequestOptionsBase)|[az Rest](/cli/azure/reference-index#az_rest) , Azure CLI 'dan bir REST API çağrısı çağırmak için kullanılabilir|
|Yönetilen Örnek|[Invoke-Azsqlınstancefailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Yönetilen örnekler-yük devretme](/rest/api/sql/managed%20instances%20-%20failover/failover)|[az SQL mı yük devretme](/cli/azure/sql/mi/#az_sql_mi_failover)|

> [!IMPORTANT]
> Hiper ölçek veritabanlarının okunabilir ikincil çoğaltmaları için yük devretme komutu kullanılamaz.

## <a name="conclusion"></a>Sonuç

Azure SQL veritabanı ve Azure SQL yönetilen örnek özelliği, Azure platformuyla derin bir şekilde tümleştirilmiş yerleşik yüksek kullanılabilirlik çözümüdür. Hata algılama ve kurtarma, veri koruma için Azure Blob depolama 'da ve daha yüksek hata toleransı (belgede daha önce Azure SQL yönetilen örneği için geçerli değildir) için Kullanılabilirlik Alanları için Service Fabric bağımlıdır. Ayrıca, SQL veritabanı ve SQL yönetilen örneği, çoğaltma ve yük devretme için SQL Server örneğinden her zaman açık kullanılabilirlik grubu teknolojisini de devreder. Bu teknolojilerin birleşimi, uygulamaların karma depolama modelinin avantajlarını tam olarak elde etmesini ve en zorlu SLA 'Ları desteklemesini sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kullanılabilirlik alanları](../../availability-zones/az-overview.md) hakkında bilgi edinin
- [Service Fabric](../../service-fabric/service-fabric-overview.md) hakkında bilgi edinin
- [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) hakkında bilgi edinin
- [SQL yönetilen örneği üzerinde el ile yük devretme başlatmayı](../managed-instance/user-initiated-failover.md) öğrenin
- Yüksek kullanılabilirlik ve olağanüstü durum kurtarma için daha fazla seçenek için bkz. [Iş sürekliliği](business-continuity-high-availability-disaster-recover-hadr-overview.md)
