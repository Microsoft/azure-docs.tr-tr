---
title: Küresel olarak kullanılabilir hizmetler tasarlama
description: Azure SQL veritabanı 'nı kullanarak yüksek oranda kullanılabilir hizmetler için uygulama tasarımı hakkında bilgi edinin.
keywords: bulut olağanüstü durum kurtarma, olağanüstü durum kurtarma çözümleri, uygulama veri yedekleme, coğrafi çoğaltma, iş sürekliliği planlama
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 8eb115497427338599db08e8c7bbdd55c5a158fc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73807960"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Azure SQL veritabanı 'nı kullanarak küresel olarak kullanılabilir hizmetler tasarlama

Azure SQL veritabanı ile bulut hizmetleri oluştururken ve dağıttığınızda, bölgesel kesintiler ve çok önemli hatalara esnekliği sağlamak için [etkin coğrafi çoğaltma](sql-database-active-geo-replication.md) veya [otomatik yük devretme grupları](sql-database-auto-failover-group.md) kullanırsınız. Aynı özellik, verilere yerel erişim için iyileştirilmiş, global olarak dağıtılmış uygulamalar oluşturmanıza olanak sağlar. Bu makalede, her seçeneğin avantajları ve avantajları dahil olmak üzere yaygın uygulama desenleri ele alınmaktadır.

> [!NOTE]
> Premium veya İş Açısından Kritik veritabanları ve elastik havuzlar kullanıyorsanız, bunları bölge yedekli dağıtım yapılandırmasına dönüştürerek bölge kesintilerine dayanıklı hale getirebilirsiniz. Bkz. bölgesel olarak [yedekli veritabanları](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Senaryo 1: en az kapalı kalma süresiyle iş sürekliliği için iki Azure bölgesi kullanma

Bu senaryoda, uygulamalar aşağıdaki özelliklere sahiptir:

* Uygulama, bir Azure bölgesinde etkin
* Tüm veritabanı oturumları, verilere okuma ve yazma erişimi (RW) gerektirir
* Gecikme ve trafik maliyetini azaltmak için Web katmanı ve veri katmanının birlikte bulunması gerekir
* Temelde, kapalı kalma süresi bu uygulamalar için veri kaybına kıyasla daha yüksek bir iş riskidir

Bu durumda, uygulama dağıtım topolojisi, tüm uygulama bileşenlerinin birlikte yük devretmesi gerektiğinde bölgesel olağanüstü durumları işlemek için iyileştirilmiştir. Aşağıdaki diyagramda bu topoloji gösterilmektedir. Coğrafi artıklık için, uygulamanın kaynakları A ve B bölgesine dağıtılır. Ancak, bölge B 'deki kaynaklar başarısız olana kadar kullanılmaz. Veritabanı bağlantısı, çoğaltma ve yük devretme yönetimi için iki bölge arasında bir yük devretme grubu yapılandırılır. Her iki bölgedeki Web hizmeti, **&lt;yük-grup-adı&gt;. Database.Windows.net** (1) ile okuma-yazma dinleyicisi aracılığıyla veritabanına erişecek şekilde yapılandırılmıştır. Traffic Manager, [Öncelik yönlendirme yöntemini](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2) kullanacak şekilde ayarlanır.  

> [!NOTE]
> [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) , bu makale boyunca yalnızca çizim amaçlarıyla kullanılır. Öncelik yönlendirme yöntemini destekleyen herhangi bir yük dengeleme çözümünü kullanabilirsiniz.

Aşağıdaki diyagramda bir kesinti olmadan önce bu yapılandırma gösterilmektedir:

![Senaryo 1. Kesinti olmadan önce yapılandırma.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Birincil bölgedeki bir kesinti sonrasında SQL veritabanı hizmeti, birincil veritabanının erişilebilir olduğunu algılar ve otomatik yük devretme ilkesinin (1) parametrelerine göre ikincil bölgeye yük devretmeyi tetikler. Uygulamanızın SLA 'sına bağlı olarak, kesinti ve yük devretme algılaması arasındaki süreyi denetleyen bir yetkisiz kullanım süresi yapılandırabilirsiniz. Yük devretme grubu, veritabanının yük devretmesini tetiklemesini yapmadan önce Traffic Manager 'ın uç nokta yük devretmesini başlattığı olasıdır. Bu durumda, Web uygulaması veritabanına hemen yeniden bağlanamaz. Ancak yeniden bağlantı, veritabanının yük devretmesi tamamlandıktan hemen sonra otomatik olarak başarılı olur. Başarısız bölge geri yüklendiğinde ve tekrar çevrimiçi olduğunda eski birincil, yeni bir ikincil olarak otomatik olarak yeniden bağlanır. Aşağıdaki diyagramda, yük devretmeden sonra yapılandırma gösterilmektedir.

> [!NOTE]
> Yük devretme işleminden sonra kaydedilen tüm işlemler, yeniden bağlantı sırasında kaybedilir. Yük devretme tamamlandıktan sonra, B bölgesindeki uygulama yeniden bağlanabilir ve Kullanıcı isteklerini işlemeye yeniden başlatılabilir. Hem Web uygulaması hem de birincil veritabanı artık B bölgesinde ve birlikte bulunan olarak kalır.

![Senaryo 1. Yük devretmeden sonra yapılandırma](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

B bölgesinde bir kesinti olursa, birincil ve ikincil veritabanı arasındaki çoğaltma işlemi askıya alınır ancak ikisi arasındaki bağlantı değişmeden kalır (1). Yönetilen trafik, B bölgesine giden bağlantının kesildiğini algılar ve uç nokta Web uygulaması 2 ' nin düşürülmüş (2) olarak işaret eder. Uygulamanın performansı Bu durumda etkilenmez, ancak veritabanı açık duruma gelir ve bu nedenle, büyük/küçük harfli bir şekilde veri kaybı durumunda veri kaybı daha yüksek risklidir.

> [!NOTE]
> Olağanüstü durum kurtarma için, uygulama dağıtımı ile yapılandırmanın iki bölgeyle sınırlı olması önerilir. Bunun nedeni, Azure coğrafi bölümlerinin çoğunun yalnızca iki bölgedir. Bu yapılandırma, uygulamanızı her iki bölgenin de eş zamanlı olmayan arızasından korumaz. Bu tür bir başarısızlığın olası bir olayında, [coğrafi geri yükleme işlemini](sql-database-disaster-recovery.md#recover-using-geo-restore)kullanarak veritabanlarınızı üçüncü bir bölgede kurtarabilirsiniz.
>

 Kesinti azaltıldıktan sonra, ikincil veritabanı otomatik olarak birincil ile yeniden eşitlenir. Eşitleme sırasında birincili performansı etkilenebilir. Belirli etki, yük devretmeden bu yana yeni birincil alınan veri miktarına bağlıdır. Aşağıdaki diyagramda, İkincil bölgedeki bir kesinti gösterilmektedir:

![Senaryo 1. İkincil bölgedeki bir kesinti sonrasında yapılandırma.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

Bu tasarım deseninin başlıca **avantajları** şunlardır:

* Aynı Web uygulaması, bölgeye özgü hiçbir yapılandırma olmadan her iki bölgeye de dağıtılır ve yük devretmeyi yönetmek için ek mantık gerektirmez.
* Web uygulaması ve veritabanı her zaman birlikte bulunan için uygulama performansı yük devretmeden etkilenmez.

Ana **zorunluluğunu getirir** , B bölgesindeki uygulama kaynaklarının çoğu zaman aşırı kullanıldığı yerdedir.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Senaryo 2: en yüksek veri koruma ile iş sürekliliği için Azure bölgeleri

Bu seçenek, aşağıdaki özelliklere sahip uygulamalar için idealdir:

* Veri kaybı yüksek iş riskidir. Veritabanı yük devretmesi yalnızca kesinti çok önemli bir hatadan kaynaklanmasından önce son çare olarak kullanılabilir.
* Uygulama, salt okuma ve okuma yazma modlarını destekler ve bir süre için "salt okunurdur" modunda çalışabilir.

Bu düzende, okuma yazma bağlantıları zaman aşımı hataları almaya başladığınızda uygulama salt okuma moduna geçer. Web uygulaması her iki bölgeye de dağıtılır ve okuma-yazma dinleyicisi uç noktasına bir bağlantı ve salt okunurdur (1) dinleyici uç noktasına farklı bağlantı içerir. Traffic Manager profili [öncelikli yönlendirmeyi](../traffic-manager/traffic-manager-configure-priority-routing-method.md)kullanmalıdır. [Uç nokta izlemenin](../traffic-manager/traffic-manager-monitoring.md) her bölgedeki uygulama uç noktası için etkinleştirilmesi gerekir (2).

Aşağıdaki diyagramda bir kesinti olmadan önce bu yapılandırma gösterilmektedir:

![Senaryo 2. Kesinti olmadan önce yapılandırma.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Traffic Manager A bölgesine bir bağlantı hatası algıladığında, Kullanıcı trafiğini otomatik olarak B bölgesinde uygulama örneğine geçirir. Bu düzende, yetkisiz kullanım süresini veri kaybı ile, 24 saat gibi yeterince yüksek bir değere ayarlamanız önemlidir. Kesinti bu süre içinde azaltıldığında veri kaybının engellenmesini sağlar. B bölgesindeki Web uygulaması etkinleştirildiğinde okuma yazma işlemleri başarısız olur. Bu noktada, salt okuma moduna (1) geçiş yapılmalıdır. Bu modda, istekler otomatik olarak ikincil veritabanına yönlendirilir. Kesinti çok önemli bir hatadan kaynaklanıyorsa, büyük olasılıkla yetkisiz kullanım süresi içinde azaltılamaz. Süresi dolmuşsa, yük devretme grubu yük devretmeyi tetikler. Okuma-yazma dinleyicisi kullanılabilir hale geldikten sonra bağlantı kurulan bağlantılar başarısız olur (2). Aşağıdaki diyagramda, kurtarma işleminin iki aşaması gösterilmektedir.

> [!NOTE]
> Birincil bölgedeki kesinti yetkisiz kullanım süresi içinde azaltıldığında, Traffic Manager birincil bölgedeki bağlantının geri yüklenmesini algılar ve Kullanıcı trafiğini A bölgesindeki uygulama örneğine geri geçirir. Bu uygulama örneği, önceki diyagramda gösterildiği gibi A bölgesindeki birincil veritabanını kullanarak okuma-yazma modunda devam eder ve çalışır.

![Senaryo 2. Olağanüstü durum kurtarma aşamaları.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

B bölgesinde bir kesinti olursa Traffic Manager, B bölgesinde Web-App-2 bitiş noktası başarısızlığını algılar ve düzeyi (1) olarak işaretler. Bu sırada, yük devretme grubu salt okuma dinleyicisini A (2) bölgesine geçirir. Bu kesinti, son kullanıcı deneyimini etkilemez, ancak birincil veritabanı kesinti sırasında gösterilir. Aşağıdaki diyagramda ikincil bölgedeki bir hata gösterilmektedir:

![Senaryo 2. İkincil bölgenin kesintisi.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Kesinti azaltıldıktan sonra, ikincil veritabanı hemen birincil ile eşitlenir ve salt okuma dinleyicisi B bölgesinde ikincil veritabanına geri getirilir. Birincil öğesinin eşitleme performansı sırasında eşitlenmesi gereken veri miktarına bağlı olarak biraz etkilenebilir.

Bu tasarım deseninin çeşitli **avantajları**vardır:

* Geçici kesintiler sırasında veri kaybını önler.
* Kesinti süresi yalnızca, Traffic Manager 'ın yapılandırılabilir olduğu bağlantı hatasını ne kadar hızlı algıladığına bağlıdır.

**Zorunluluğunu getirir** , uygulamanın salt okunurdur modunda çalışabilebilmelidir.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Senaryo 3: uygulama, veri kaybı olmadan ve sıfıra yakın kapalı kalma süresi olmadan farklı bir Coğrafya 'ya yeniden konumlandırma

Bu senaryoda, uygulama aşağıdaki özelliklere sahiptir:

* Son kullanıcılar uygulamaya farklı coğrafi bölgelerde erişir
* Uygulama, en son güncelleştirmelerle tam eşitlemeye bağımlı olmayan salt okuma iş yüklerini içerir
* Verilere yazma erişimi, kullanıcıların çoğunluğu için aynı coğrafya içinde desteklenmelidir
* Son Kullanıcı deneyimi için okuma gecikmesi kritiktir

Bu gereksinimleri karşılamak için, Kullanıcı cihazının, veri tarama, analiz vb. gibi salt okuma işlemleri için aynı coğrafya 'da dağıtılan uygulamaya **her zaman** bağlandığından emin olmanız gerekir. Öte yandan, OLTP işlemleri **zaman**içinde aynı coğrafya içinde işlenir. Örneğin, OLTP işlemlerinin gün içinde aynı coğrafya 'da işlendiği, ancak kapalı saatlerde farklı bir Coğrafya içinde işlenebilecekleri zaman içinde. Son Kullanıcı etkinliği genellikle çalışma saatlerinde gerçekleşdiğinde, çoğu kullanıcının çoğu için en iyi performansı garanti edebilirsiniz. Aşağıdaki diyagramda bu topoloji gösterilmektedir.

Uygulamanın kaynakları, önemli kullanım talebi olan her bir Coğrafya üzerinde dağıtılmalıdır. Örneğin, uygulamanız Birleşik Devletler etkin olarak kullanılıyorsa, Avrupa Birliği ve Güney Doğu Asya, uygulamanın tüm bu coğrafi ormallara dağıtılması gerekir. Birincil veritabanı, çalışma saatlerinin sonunda bir Coğrafya 'dan bir sonrakine dinamik olarak yerleştirilmelidir. Bu yöntem "Güneş izle" olarak adlandırılır. OLTP iş yükü her zaman **&lt;yük-grup-adı&gt;. Database.Windows.net** (1) okuma-yazma dinleyicisi aracılığıyla veritabanına bağlanır. Salt okuma iş yükü, **&lt;Server-name&gt;. Database.Windows.net** (2) adlı veritabanları sunucu uç noktasını kullanarak doğrudan yerel veritabanına bağlanır. Traffic Manager, [performans yönlendirme yöntemiyle](../traffic-manager/traffic-manager-configure-performance-routing-method.md)yapılandırılır. Son kullanıcının cihazının en yakın bölgede Web hizmetine bağlı olmasını sağlar. Traffic Manager, her Web hizmeti uç noktası (3) için uç nokta izleme etkinleştirilmiş olarak ayarlanmalıdır.

> [!NOTE]
> Yük devretme grubu yapılandırması, yük devretme için hangi bölgenin kullanıldığını tanımlar. Yeni birincil konum farklı bir Coğrafya içinde olduğundan, etkilenen bölge yeniden çevrimiçi olana kadar hem OLTP hem de salt okuma iş yükleri için yük devretme sonuçları daha uzun gecikme süresine sahiptir.

![Senaryo 3. Doğu ABD birincil ile yapılandırma.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Günün sonunda (örneğin, 11PM yerel saati) etkin veritabanlarının bir sonraki bölgeye (Kuzey Avrupa) geçiş yapılmalıdır. Bu görev, [Azure zamanlama hizmeti](../scheduler/scheduler-intro.md)kullanılarak tamamen otomatikleştirilebilir.  Görev aşağıdaki adımları içerir:

* Kolay yük devretme (1) kullanarak Kuzey Avrupa yük devretme grubundaki birincil sunucuyu değiştirin
* Doğu ABD ve Kuzey Avrupa arasında yük devretme grubunu kaldırma
* Aynı ada sahip ancak Kuzey Avrupa ve Doğu Asya (2) arasında yeni bir yük devretme grubu oluşturun.
* Bu yük devretme grubuna (3) Doğu Asya Kuzey Avrupa birincili ve ikincil öğesine ekleyin.

Aşağıdaki diyagramda, planlı yük devretmenin ardından yeni yapılandırma gösterilmektedir:

![Senaryo 3. Birincil Kuzey Avrupa geçişi yapılıyor.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Örneğin Kuzey Avrupa bir kesinti olursa, otomatik veritabanı yük devretmesi yük devretme grubu tarafından başlatılır ve bu, uygulamayı zamanlamanın (1) bir sonraki bölgesine taşımaya etkili bir şekilde neden olur.  Bu durumda, Kuzey Avrupa yeniden çevrimiçi olana kadar ABD Doğu kalan tek ikincil bölgedir. Kalan iki bölge, roller arasında geçiş yaparak müşterileri üç coğrafi bölgede sunar. Azure Scheduler uygun şekilde ayarlanmalıdır. Kalan bölgelerde Avrupa 'dan ek Kullanıcı trafiği alındığından, uygulamanın performansı yalnızca ek gecikme süresi ve ayrıca daha fazla sayıda Son Kullanıcı bağlantısı tarafından etkilenmez. Kesinti Kuzey Avrupa bir kez azaltıldıktan sonra, ikincil veritabanı geçerli birincil ile hemen eşitlenir. Aşağıdaki diyagramda Kuzey Avrupa bir kesinti gösterilmektedir:

![Senaryo 3. Kuzey Avrupa kesinti.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Son kullanıcının Avrupa 'daki deneyiminin uzun gecikme süresine göre düştüğü süreyi azaltabilirsiniz. Bunu yapmak için, bir uygulama kopyasını önceden dağıtmanız ve ikincil veritabanlarını başka bir yerel bölgede (Batı Avrupa) oluşturmanız gerekir ve Kuzey Avrupa çevrimdışı uygulama örneğinin yerini alır. İkincisi yeniden çevrimiçi olduğunda, Batı Avrupa kullanmaya devam edilip edilmeyeceğini ya da uygulamanın kopyasını kaldırıp Kuzey Avrupa kullanmaya geri dönmek için karar verebilirsiniz.

Bu tasarımın başlıca **avantajları** şunlardır:

* Salt okuma uygulama iş yükü, verileri her zaman ve alt kümeler bölgesinde erişir.
* Okuma-yazma uygulaması iş yükü, her Coğrafya 'daki en yüksek Etkinliğin süresi boyunca en yakın bölgedeki verilere erişir
* Uygulama birden çok bölgeye dağıtıldığından, önemli kapalı kalma süresi olmadan bölgelerden birinin kaybedilmesi devam edebilir.

Ancak bazı **dengeler**vardır:

* Bölgesel bir kesinti, Coğrafya 'nın daha uzun bir gecikmeyle etkilenmesine neden olur. Okuma-yazma ve salt okuma iş yükleri, uygulama tarafından farklı bir Coğrafya içinde sunulur.
* Salt okuma iş yükleri her bölgede farklı bir uç noktasına bağlanmalıdır.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>İş sürekliliği planlama: bulut olağanüstü durum kurtarma için bir uygulama tasarımı seçme

Özel bulut olağanüstü durum kurtarma stratejiniz, uygulamanızın ihtiyaçlarını en iyi şekilde karşılayacak şekilde bu tasarım düzenlerini birleştirebilir veya genişletebilir.  Daha önce belirtildiği gibi, seçtiğiniz strateji müşterilerinize ve uygulama dağıtım topolojisine sunmak istediğiniz SLA 'yı temel alır. Kararmanıza yardımcı olmak için aşağıdaki tabloda, kurtarma noktası hedefi (RPO) ve tahmini kurtarma süresi (ERT) temelinde seçimler karşılaştırılmaktadır.

| Desen | RPO | ERT |
|:--- |:--- |:--- |
| Birlikte bulunan veritabanı erişimiyle olağanüstü durum kurtarma için etkin-Pasif dağıtım |Okuma-yazma erişimi < 5 sn |Hata algılama zamanı + DNS TTL |
| Uygulama yük dengelemesi için etkin-etkin dağıtım |Okuma-yazma erişimi < 5 sn |Hata algılama zamanı + DNS TTL |
| Etkin-Pasif dağıtım veri koruma |Salt okuma erişimi < 5 sn | Salt okuma erişimi = 0 |
||Okuma-yazma erişimi = sıfır | Okuma-yazma erişimi = hata algılama süresi + yetkisiz kullanım süresi veri kaybı |
|||

## <a name="next-steps"></a>Sonraki adımlar

* İş sürekliliği için genel bakış ve senaryolar için bkz. [iş sürekliliği genel bakış](sql-database-business-continuity.md)
* Etkin coğrafi çoğaltma hakkında bilgi edinmek için bkz. [etkin coğrafi çoğaltma](sql-database-active-geo-replication.md).
* Otomatik yük devretme grupları hakkında bilgi edinmek için bkz. [otomatik yük devretme grupları](sql-database-auto-failover-group.md).
* Esnek havuzlarla etkin coğrafi çoğaltma hakkında daha fazla bilgi için bkz. [elastik havuz olağanüstü durum kurtarma stratejileri](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
