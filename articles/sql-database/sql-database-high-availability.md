---
title: Yüksek kullanılabilirlik
description: Azure SQL veritabanı hizmeti yüksek kullanılabilirlik özellikleri ve özellikleri hakkında bilgi edinin
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/14/2019
ms.openlocfilehash: 86a3fd7c67dc2e544a1510dc910951452c32245d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811358"
---
# <a name="high-availability-and-azure-sql-database"></a>Yüksek kullanılabilirlik ve Azure SQL veritabanı

Azure SQL veritabanı 'nda yüksek kullanılabilirlik mimarisinin hedefi, bakım işlemlerinin ve kesintilerin etkileri hakkında kaygılanmadan veritabanınızın% 99,99 ' un çalışır durumda olmasını sağlamaktır. Azure, düzeltme eki uygulama, yedeklemeler, Windows ve SQL yükseltmeleri gibi kritik bakım görevlerini ve temel alınan donanım, yazılım veya ağ arızalarına yönelik planlanmamış olayları otomatik olarak işler.  Temel alınan SQL örneği düzeltme eki uygulanmış veya başarısız olduğunda, uygulamanızda [yeniden deneme mantığı](sql-database-develop-overview.md#resiliency) kullanırsanız kesinti olmaz. Azure SQL veritabanı, verilerinizin her zaman kullanılabilir olmasını sağlamak için en kritik koşullarda bile hızlı bir şekilde kurturabilir.

Yüksek kullanılabilirlik çözümü, hatalar nedeniyle kaydedilmiş verilerin kaybolmamasını, bakım işlemlerinin iş yükünüzü etkilememesini ve veritabanının yazılım mimarinizdeki tek hata noktası olmamasını sağlamak üzere tasarlanmıştır. Veritabanı yükseltilirken veya korunurken iş yükünü durdurmanız gereken bakım pencereleri veya daha az zaman yoktur. 

Azure SQL veritabanı 'nda kullanılan iki yüksek kullanılabilirliğe sahip mimari modeli vardır:

- İşlem ve depolama ayrımı temelinde kullanılan standart kullanılabilirlik modeli.  Bu, uzak depolama katmanının yüksek düzeyde kullanılabilirliğini ve güvenilirliğini kullanır. Bu mimari, bakım etkinlikleri sırasında bazı performans düşüşüne neden olan bütçe odaklı iş uygulamalarını hedefler.
- Veritabanı altyapısı işlemlerinin bir kümesini temel alan Premium kullanılabilirlik modeli. Her zaman kullanılabilir veritabanı altyapısı düğümlerinin bir çekirdeği olduğundan emin olur. Bu mimari, yüksek GÇ performansı, yüksek işlem oranı ve bakım etkinlikleri sırasında iş yükünüzün en düşük performans etkisini güvence altına alarak görev açısından kritik uygulamaları hedefler.

Azure SQL veritabanı, SQL Server veritabanı altyapısının ve Windows işletim sisteminin en son kararlı sürümünde çalışır ve çoğu kullanıcı, yükseltmelerin sürekli olarak gerçekleştirildiğini fark etmez.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Temel, standart ve Genel Amaçlı hizmet katmanı kullanılabilirliği

Bu hizmet katmanları standart kullanılabilirlik mimarisinden yararlanır. Aşağıdaki şekilde, ayrılmış işlem ve Depolama katmanlarına sahip dört farklı düğüm gösterilmektedir.

![İşlem ve depolama ayrımı](media/sql-database-high-availability/general-purpose-service-tier.png)

Standart kullanılabilirlik modeli iki katman içerir:

- `sqlservr.exe` sürecini çalıştıran ve yalnızca geçici ve önbelleğe alınmış veriler (örneğin, eklenen SSD üzerinde model veritabanları, bağlı SSD üzerinde model veritabanları, ve bellek olarak plan önbelleği, arabellek havuzu ve columnstore havuzu) içeren durum bilgisiz işlem katmanı. Bu durum bilgisiz düğüm, `sqlservr.exe`Başlatan, düğümün sistem durumunu denetleyen ve gerekirse başka bir düğüme yük devretme gerçekleştiren Azure Service Fabric tarafından çalıştırılır.
- Azure Blob depolamada depolanan veritabanı dosyaları (. mdf/. ldf) ile durum bilgisi olan bir veri katmanı. Azure Blob depolamada yerleşik veri kullanılabilirliği ve artıklık özelliği bulunur. SQL Server işlemi kilitlense bile, veri dosyasındaki günlük dosyasında veya sayfada bulunan her kaydın korunacağından emin olur.

Veritabanı altyapısı veya işletim sistemi yükseltildiğinde veya bir hata algılandığında, Azure Service Fabric durum bilgisiz SQL Server işlemini yeterli boş kapasiteye sahip başka bir durum bilgisi olmayan işlem düğümüne taşıyacaktır. Azure Blob depolama alanındaki veriler taşımadan etkilenmez ve veri/günlük dosyaları yeni başlatılan SQL Server işlemine eklenir. Bu işlem% 99,99 kullanılabilirliği garanti eder, ancak yeni SQL Server örneği soğuk önbellek ile başladığından bu yana ağır bir iş yükü geçiş sırasında bazı performans düşüşüne neden olabilir.

## <a name="premium-and-business-critical-service-tier-availability"></a>Premium ve İş Açısından Kritik hizmet katmanı kullanılabilirliği

Premium ve İş Açısından Kritik hizmet katmanları, işlem kaynaklarını (SQL Server veritabanı altyapısı işlemi) ve depolama alanını (yerel olarak bağlı SSD) tek bir düğümde tümleştiren Premium kullanılabilirlik modelinden yararlanır. Yüksek kullanılabilirlik, üç-dört düğümlü küme oluştururken hem işlem hem de depolamanın ek düğümlere çoğaltılmasıyla elde edilir. 

![Veritabanı altyapısı düğümlerinin kümesi](media/sql-database-high-availability/business-critical-service-tier.png)

Temel alınan veritabanı dosyaları (. mdf/. ldf), iş yükünüze çok düşük gecikmeli GÇ sağlamak üzere eklenmiş SSD depolama alanına yerleştirilir. Yüksek kullanılabilirlik, [her zaman açık kullanılabilirlik grupları](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)SQL Server benzer bir teknoloji kullanılarak uygulanır. Küme, okuma/yazma müşteri iş yükleri için erişilebilen tek bir birincil çoğaltma (SQL Server işlemi) ve verilerin kopyalarını içeren üç ikincil çoğaltma (işlem ve depolama) içerir. Birincil düğüm değişiklikleri sürekli olarak ikincil düğümlere gönderir ve her bir işlem gerçekleştirilmeden önce verilerin en az bir ikincil çoğaltmayla eşitlenmesini sağlar. Bu işlem, birincil düğüm herhangi bir nedenden dolayı kilitlenirse, her zaman yük devretmek için tamamen eşitlenmiş bir düğüm vardır. Yük devretme işlemi Azure Service Fabric tarafından başlatılır. İkincil çoğaltma yeni birincil düğüm olduktan sonra, kümede yeterli düğüm (çekirdek kümesi) olduğundan emin olmak için başka bir ikincil çoğaltma oluşturulur. Yük devretme işlemi tamamlandıktan sonra, SQL bağlantıları otomatik olarak yeni birincil düğüme yönlendirilir.

Ek bir avantaj olarak Premium kullanılabilirlik modeli, salt okunurdur SQL bağlantılarını ikincil çoğaltmalardan birine yeniden yönlendirebilme özelliği içerir. Bu özelliğe [okuma ölçeği](sql-database-read-scale-out.md)genişletme denir. Birincil çoğaltmadan, analitik iş yükleri gibi salt okuma işlemlerini yük dışı bırakmak için ek ücret olmadan %100 ek işlem kapasitesi sağlar.

## <a name="hyperscale-service-tier-availability"></a>Hiper ölçek hizmet katmanı kullanılabilirliği

Hiper ölçek hizmet katmanı mimarisi, [Dağıtılmış işlevler mimarisinde](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture)açıklanmıştır. 

![Hiper ölçekli işlevsel mimari](./media/sql-database-hyperscale/hyperscale-architecture.png)

Hiper ölçekte kullanılabilirlik modeli dört katman içerir:

- `sqlservr.exe` işlemleri çalıştıran ve yalnızca geçici ve önbelleğe alınmış veriler içeren, eklenen SSD 'de kapsayan ve önbellek, arabellek havuzu ve columnstore havuzu gibi durum bilgisi olmayan bir işlem katmanı. Bu durum bilgisiz katmanı, birincil işlem çoğaltmasını ve isteğe bağlı olarak, yük devretme hedefleri olarak kullanılabilecek bir dizi ikincil işlem çoğaltmasını içerir.
- Sayfa sunucuları tarafından biçimlendirilen durum bilgisi olmayan bir depolama katmanı. Bu katman, işlem çoğaltmaları üzerinde çalışan `sqlservr.exe` işlemlerine yönelik dağıtılmış depolama altyapısıdır. Her sayfa sunucusu yalnızca geçici ve önbelleğe alınmış verileri içerir, örneğin takılı SSD üzerinde RBPEX önbelleği ve bellekte önbelleğe alınan veri sayfaları. Her sayfa sunucusunun, Yük Dengeleme, yedeklilik ve yüksek kullanılabilirlik sağlamak için etkin-etkin bir yapılandırmada eşleştirilmiş bir sayfa sunucusu vardır.
- Günlük hizmeti işlemi, işlem günlüğü giriş bölgesi ve işlem günlüğü uzun vadeli depolama alanı çalıştıran işlem düğümü tarafından oluşturulan, durum bilgisi olan işlem günlüğü depolama katmanı. Giriş bölgesi ve uzun süreli depolama, işlem günlüğü için kullanılabilirlik ve [Artıklık](https://docs.microsoft.com/azure/storage/common/storage-redundancy) sağlayan ve kaydedilmiş işlemler için veri dayanıklılığı sağlayan Azure depolama 'yı kullanır.
- Azure depolama 'da depolanan ve sayfa sunucuları tarafından güncelleştirilmiş veritabanı dosyaları (. mdf/. ndf) ile durum bilgisi olan bir veri depolama katmanı. Bu katman, Azure depolama 'nın veri kullanılabilirliği ve [Artıklık](https://docs.microsoft.com/azure/storage/common/storage-redundancy) özelliklerini kullanır. Bir veri dosyasındaki her sayfanın, hiper ölçekli mimarinin diğer katmanlarındaki işlemler çöktüğünde veya işlem düğümleri başarısız olsa bile korunacağından emin olur.

Tüm hiperölçek katmanlarında işlem düğümleri Azure Service Fabric üzerinde çalışır ve her bir düğümün sistem durumunu denetler ve gerektiğinde kullanılabilir sağlıklı düğümlere yük devretme gerçekleştirir.

Hiper ölçekte yüksek kullanılabilirlik hakkında daha fazla bilgi için bkz. [Hyperscale 'de veritabanı yüksek kullanılabilirliği](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Bölge yedekli yapılandırma

Varsayılan olarak, Premium kullanılabilirlik modeli için düğümlerin kümesi aynı veri merkezinde oluşturulur. [Azure kullanılabilirlik alanları](../availability-zones/az-overview.md)tanıtımı Ile SQL veritabanı, iş açısından kritik veritabanının farklı çoğaltmalarını aynı bölgedeki farklı kullanılabilirlik bölgelerine yerleştirebilir. Tek bir başarısızlık noktasını ortadan kaldırmak için, denetim halkası aynı zamanda birden çok bölgede üç ağ geçidi halkaları (GW) olarak da yinelenir. Belirli bir ağ geçidi halkası yönlendirmesi [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM) tarafından denetlenir. Premium veya İş Açısından Kritik hizmet katmanlarında bölge yedekli yapılandırma ek veritabanı yedekliliği oluşturmadığından, ek ücret ödemeden etkinleştirebilirsiniz. Bölgesel olarak yedekli bir yapılandırma seçerek, Premium veya İş Açısından Kritik veritabanlarınızı, uygulama mantığındaki herhangi bir değişiklik yapmadan çok daha büyük bir veri merkezi kesintileri dahil olmak üzere çok daha büyük bir başarısızlık kümesine dayanıklı hale getirebilirsiniz. Ayrıca, mevcut Premium veya İş Açısından Kritik veritabanlarını veya havuzları bölge yedekli yapılandırmasına de dönüştürebilirsiniz.

Bölge yedekli veritabanlarının aralarında biraz uzaklıktan farklı veri merkezlerinde çoğaltmaları olduğundan, artan ağ gecikmesi çalışma süresini artırabilir ve böylece bazı OLTP iş yüklerinin performansını etkileyebilir. Bölge artıklığı ayarını devre dışı bırakarak her zaman tek bölge yapılandırmasına dönebilirsiniz. Bu işlem, normal hizmet katmanı yükseltmesine benzer bir çevrimiçi işlemdir. İşlemin sonunda, veritabanı veya havuz, bölge yedekli halkadan tek bir bölge halkaine geçirilir veya tam tersi de geçerlidir.

> [!IMPORTANT]
> Bölgesel olarak yedekli veritabanları ve elastik havuzlar Şu anda yalnızca Seç bölgelerinde Premium ve İş Açısından Kritik hizmet katmanlarında desteklenir. İş Açısından Kritik katmanını kullanırken, bölge yedekli yapılandırma yalnızca 5. nesil işlem donanımı seçildiğinde kullanılabilir. Bölge yedekli veritabanlarını destekleyen bölgeler hakkında güncel bilgiler için bkz. [bölgeye göre Hizmetler desteği](../availability-zones/az-overview.md#services-support-by-region).  
> Bu özellik yönetilen örnekte kullanılamaz.

Yüksek kullanılabilirlik mimarisinin bölge yedekli sürümü aşağıdaki diyagram tarafından gösterilmiştir:

![yüksek kullanılabilirlik mimarisi bölgesi yedekli](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Hızlandırılmış veritabanı kurtarma (ADR)

[Hızlandırılmış veritabanı kurtarma (ADR)](sql-database-accelerated-database-recovery.md) , özellikle uzun süre çalışan işlemler söz konusu olduğunda veritabanı kullanılabilirliğini büyük ölçüde geliştiren yenı bir SQL veritabanı altyapısı özelliğidir. ADR Şu anda tek veritabanları, elastik havuzlar ve Azure SQL veri ambarı için kullanılabilir.

## <a name="testing-application-fault-resiliency"></a>Uygulama hatası dayanıklılığı sınanıyor

Yüksek kullanılabilirlik, Azure SQL veritabanı platformunun, veritabanı uygulamanız için saydam bir şekilde çalışabilen temel bir parçasıdır. Ancak, planlı veya planlanmamış olaylar sırasında başlatılan otomatik yük devretme işlemlerinin, üretime dağıtmadan önce uygulamayı ne şekilde etkilediğini test etmek isteyebilirsiniz. Bir veritabanını veya elastik havuzu yeniden başlatmak için özel bir API çağırabilirsiniz, bu da bir yük devretmeyi tetikleyecektir. Bölgesel olarak yedekli bir veritabanı veya elastik havuz söz konusu olduğunda, API çağrısı, eski birincil bölge kullanılabilirlik bölgesinden farklı bir kullanılabilirlik bölgesindeki yeni birincil bağlantı ile istemci bağlantılarını yeniden yönlendirmeye neden olur. Bu nedenle, yük devretmenin var olan veritabanı oturumlarını nasıl etkilediğini test etmeye ek olarak, ağ gecikmede yapılan değişiklikler nedeniyle uçtan uca performansı değiştirdiğinizi de doğrulayabilirsiniz. Yeniden başlatma işlemi zorlandığından ve çok sayıda BT platformu stres yaptığından, her veritabanı veya elastik havuz için her 30 dakikada bir yük devretme çağrısına izin verilir. 

Yük devretme, REST API veya PowerShell kullanılarak başlatılabilir. REST API için bkz. [veritabanı yük devretme](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) ve [elastik havuz yük devretme](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover). PowerShell için bkz. [Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) ve [Invoke-Azsqtalakpoolyük devretme](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). REST API çağrısı, [az Rest](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest) komutu KULLANıLARAK Azure CLI 'dan da yapılabilir.

> [!IMPORTANT]
> Yük devretme komutu şu anda hiper ölçek hizmeti katmanında ve yönetilen örnek için kullanılabilir değil.

## <a name="conclusion"></a>Sonuç

Azure SQL veritabanı, Azure platformuyla çok daha tümleşik olan yerleşik bir yüksek oranda kullanılabilir çözüm sunar. Hata algılama ve kurtarma, veri koruma için Azure Blob depolama ve daha yüksek hata toleransı için Kullanılabilirlik Alanları Service Fabric bağımlıdır. Ayrıca, Azure SQL veritabanı, çoğaltma ve yük devretme için SQL Server 'dan her zaman açık kullanılabilirlik grubu teknolojisini kullanır. Bu teknolojilerin birleşimi, uygulamaların karma depolama modelinin avantajlarını tam olarak elde etmesini ve en zorlu SLA 'Ları desteklemesini sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kullanılabilirlik alanları](../availability-zones/az-overview.md) hakkında bilgi edinin
- [Service Fabric](../service-fabric/service-fabric-overview.md) hakkında bilgi edinin
- [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) hakkında bilgi edinin
- Yüksek kullanılabilirlik ve olağanüstü durum kurtarma için daha fazla seçenek için bkz. [Iş sürekliliği](sql-database-business-continuity.md)
