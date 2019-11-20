---
title: Azure Service Fabric olağanüstü durum kurtarma | Microsoft Docs
description: Azure Service Fabric, tüm olağanüstü durumlar ile uğraşmak için gereken özellikleri sunar. Bu makalede, oluşabilecek olağanüstü durum türleri ve bunlarla nasıl uğraşmanız açıklanmaktadır.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 1be891d38eb918d65cd8efda86e9a81fa629cf38
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73883983"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Azure Service Fabric olağanüstü durum kurtarma
Yüksek kullanılabilirlik sunmaya yönelik kritik bir bölüm, hizmetlerin tüm farklı türdeki hataların varlığını sürdürmesini sağlamaktır. Bu özellikle, denetim için planlanmamış ve dışındaki hatalarda önemlidir. Bu makalede, doğru Modellenmemiş ve yönetilmiyorsa olağanüstü durumlar olabilecek bazı yaygın hata modları açıklanmaktadır. Ayrıca, bir olağanüstü durum gerçekleştiyse gerçekleştirilecek azaltmaları ve eylemleri tartışır. Amaç, bir veya daha fazla durum oluştuğunda, kapalı kalma süresi veya veri kaybı riskini sınırlandırmaktır veya ortadan kaldırır.

## <a name="avoiding-disaster"></a>Olağanüstü durumdan kaçınma
Service Fabric birincil amacı, hem ortamınızı hem de hizmetlerinizi ortak hata türleri olağanüstü durumlar olmayan bir şekilde modeletmenize yardımcı olmaktır. 

Genel olarak iki tür olağanüstü durum/başarısızlık senaryosu vardır:

1. Donanım veya yazılım hataları
2. İşletimsel hatalar

### <a name="hardware-and-software-faults"></a>Donanım ve yazılım hataları
Donanım ve yazılım hataları tahmin edilemez. Arızaların en kolay yolu, hizmetin donanım veya yazılım hatası sınırları arasında yayılmış daha fazla kopyasını çalıştırıyor. Örneğin, hizmetiniz yalnızca belirli bir makinede çalışıyorsa, o makinenin arızası bu hizmet için bir olağanüstü durum olur. Bu olağanüstü durumdan kaçınmanın basit yolu, hizmetin gerçekten birden fazla makinede çalıştığından emin olmak. Ayrıca test, bir makinenin başarısızlığının çalışan hizmeti kesintiye uğramasını sağlamak için de gereklidir. Kapasite planlama, bir değiştirme örneğinin başka bir yerde oluşturulmasını ve kapasiteden azaltmanın kalan Hizmetleri aşırı yükmesini sağlar. Aynı model, arızasından kaçınmak için ne olursa olsun, işe yarar. Örneğin. SAN hatası hakkında endişeleriniz varsa, birden çok San arasında çalıştırırsınız. Bir sunucu rafı kaybı hakkında endişeleriniz varsa, birden çok rafta çalıştırılırsınız. Veri merkezlerinin kaybedilmesi konusunda endişeli varsa, hizmetiniz birden çok Azure bölgesinde veya veri merkezinde çalışmalıdır. 

Bu tür bir yayılmış modda çalışırken, yine de bazı eş zamanlı hata hatalarıyla karşılaşmış olursunuz, ancak belirli bir türün (örn. tek bir VM veya ağ bağlantısı başarısız olması) tek ve hatta birden çok hata (yani, artık "olağanüstü durum") otomatik olarak işlenir. Service Fabric, kümeyi genişletmek için birçok mekanizma sağlar ve başarısız olan düğümleri ve hizmetleri geri getiren işler. Service Fabric Ayrıca, bu planlanmamış hataların gerçek felaketlere dönmesini önlemek için hizmetlerinizin birçok örneğinin çalıştırılmasına izin verir.

Bir dağıtımı, hatalara göre yayılmak için yeterince büyük bir süre çalıştırmanın olası nedenleri olabilir. Örneğin, hata ihtimaline göre ödeme yapmayı amaçlamadan daha fazla donanım kaynağı alabilir. Dağıtılmış uygulamalarla ilgilenirken, coğrafi mesafelerde ek iletişim atlamaları veya durum çoğaltma maliyetleri kabul edilemez gecikmeye neden olabilir. Bu çizginin her bir uygulama için farklı çizilir. Özellikle yazılım hatalarında hata, ölçeklendirmeye çalıştığınız hizmette olabilir. Bu durumda, hata koşulu tüm örneklerde bağıntılı olduğundan daha fazla kopya olağanüstü durum oluşmasını engellemez.

### <a name="operational-faults"></a>İşletimsel hatalar
Hizmetiniz birçok artıklıkları sahip dünya genelinde yayılsa bile, felaket olayları yaşamaya devam edebilir. Örneğin, birisi hizmetin DNS adını yanlışlıkla yeniden yapılandırırsa veya sağ üst öğeyi silerse. Örnek olarak, durum bilgisi olan bir Service Fabric hizmetiniz olduğunu ve birisi bu hizmeti yanlışlıkla silmiş olduğunu varsayalım. Başka bir risk azaltma işlemi olmadıkça, bu hizmet ve tüm durumu artık kayboldu. Bu tür işlemsel olağanüstü durumlar ("Yani"), düzenli plansız hatalardan farklı azaltmaları ve işlemler gerektirir. 

Bu tür işletimsel hataların oluşmaması için en iyi yollar şunlardır
1. ortama işlemsel erişimi kısıtla
2. tehlikeli işlemleri kesinlikle denetleyin
3. Otomasyonu, el ile veya bant dışı değişiklikleri engelle ve gerçek ortama göre belirli değişiklikleri uygulamadan önce doğrulama
4. bozucu işlemlerin "geçici" olduğundan emin olun. Geçici işlemler hemen etkili olmaz veya bir zaman penceresi içinde geri alınabilir

Service Fabric, küme işlemleri için [rol tabanlı](service-fabric-cluster-security-roles.md) erişim denetimi sağlama gibi işletimsel hataların önlenmesi için bazı mekanizmalar sağlar. Ancak, bu işletimsel hataların çoğu kuruluş çalışmalarını ve diğer sistemleri gerektirir. Service Fabric, işlem hatalarının büyük bir kısmı için yedekleme ve durum bilgisi olmayan hizmetler için geri yükleme için bir mekanizma sağlar.

## <a name="managing-failures"></a>Sorunları yönetme
Service Fabric amacı, hataların neredeyse her zaman otomatik olarak yönetimi olur. Ancak, bazı başarısızlık türlerini işlemek için hizmetlerin ek kodu olmalıdır. Diğer başarısızlık türleri, güvenlik ve iş sürekliliği nedeniyle otomatik olarak _değinmemelidir._ 

### <a name="handling-single-failures"></a>Tek başarısızlık işleme
Tek makineler, tüm nedenlerden dolayı başarısız olabilir. Bunlar, güç kaynakları ve ağ donanım arızaları gibi donanım nedenleridir. Diğer sorunlar yazılımda bulunur. Bunlar, gerçek işletim sisteminin ve hizmetin kendisinin başarısızlıklarını içerir. Service Fabric, ağ sorunları nedeniyle makinenin diğer makinelerden yalıtıldığı durumlar da dahil olmak üzere bu tür hataları otomatik olarak algılar.

Hizmetin türü ne olursa olsun, tek bir örnek çalıştırıldığında kodun tek bir kopyası herhangi bir nedenle başarısız olursa, bu hizmet için kapalı kalma süresi oluşur. 

Tek bir hatayı işlemek için yapabileceğiniz en basit şey, hizmetlerinizin varsayılan olarak birden fazla düğüm üzerinde çalışmasını sağlamaktır. Durum bilgisi olmayan hizmetler için, bu, 1 ' den büyük bir `InstanceCount` elde ederek gerçekleştirilebilir. Durum bilgisi olan hizmetler için en düşük öneri her zaman bir `TargetReplicaSetSize` ve en az 3 `MinReplicaSetSize`. Hizmet kodunuzun daha fazla kopyasını çalıştırmak, hizmetinizin tek bir hatayı otomatik olarak işlemesini sağlar. 

### <a name="handling-coordinated-failures"></a>Eşgüdümlü sorunları işleme
Planlı veya planlanmamış altyapı hatalarından ve değişikliklerinden ya da planlı yazılım değişikliklerinden dolayı, bir kümede Eşgüdümlü hatalara neden olabilir. Service Fabric, hata etki alanları olarak Eşgüdümlü hatalarla karşılaşan altyapı bölgelerini modelleyin. Koordine edilen yazılım değişikliklerini deneyime yönelik alanlara yükseltme etki alanları olarak modellenir. Hata ve yükseltme etki alanları hakkında daha fazla bilgi [Bu belgede](service-fabric-cluster-resource-manager-cluster-description.md) küme topolojisini ve tanımını açıklar.

Varsayılan olarak Service Fabric, hizmetlerinizin nerede çalışacağını planlarken hata ve yükseltme etki alanlarını kabul eder. Varsayılan olarak Service Fabric, hizmetlerinizin çeşitli hata ve yükseltme etki alanlarında çalıştığından emin olmaya çalışır, böylece planlanmış veya planlanmamış değişiklikler hizmetlerinizin kullanılabilir kalmasını sağlar. 

Örneğin, bir güç kaynağı hatasının bir makinenin aynı anda başarısız olmasına neden olduğunu varsayalım. Hata etki alanı hatasında çok sayıda makinenin kaybını çalıştıran birden çok makine kopyası varsa, belirli bir hizmet için yalnızca başka bir hata örneğini döndürür. Bu, hizmetlerinizin yüksek kullanılabilirlik sağlamak için hata etki alanlarını yönetmenin kritik öneme sahiptir. Azure 'da Service Fabric çalıştırırken, hata etki alanları otomatik olarak yönetilir. Diğer ortamlarda, bu olmayabilir. Şirket içinde kendi kümelerinizi oluşturuyorsanız, hata etki alanı düzeninizi doğru şekilde eşleştirdiğinizden ve planladığınızdan emin olun.

Yükseltme etki alanları, yazılımın aynı anda yükseltilebileceği modelleme alanları için faydalıdır. Bu nedenle, yükseltme etki alanları da genellikle planlanan yükseltmeler sırasında yazılımın kapatıldığı sınırları tanımlar. Hem Service Fabric hem de hizmetlerinizin yükseltmeleri aynı modeli izler. Sıralı yükseltmeler, yükseltme etki alanları ve istenmeyen değişikliklerin kümeyi ve hizmetinizi etkilemesini önlemeye yardımcı olan Service Fabric sistem durumu modeli hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

 - [Uygulama yükseltme](service-fabric-application-upgrade.md)
 - [Uygulama yükseltme öğreticisi](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric sistem durumu modeli](service-fabric-health-introduction.md)

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)' de belirtilen küme haritasını kullanarak kümenizin yerleşimini görselleştirebilirsiniz:

<center>

![düğümler, Service Fabric Explorer][sfx-cluster-map]
hata etki alanları arasında yayılır </center>

> [!NOTE]
> Hata ve yükseltme etki alanlarında hizmet kodu ve durumlarınızın birçok örneğini çalıştırma,, hizmetlerinizin hata ve yükseltme etki alanlarında çalıştığından emin olmak için yerleştirme kuralları ve yerleşik sistem durumu izleme, normal işlem sorunlarını ve hatalarının olağanüstü duruma dönmesini önlemek için Service Fabric sağladığı özelliklerden **bazılarıdır** . 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Eşzamanlı donanım veya yazılım başarısızlıklarını işleme
Yukarıdaki tek bir başarısızlık hakkında konuşduk. Görebileceğiniz gibi, hata ve yükseltme etki alanlarında çalışan kodun (ve durumun) daha fazla kopyasını tutarak hem durum bilgisi olmayan hem de durum bilgisi olan hizmetler için kolayca idare edilebilir. Birden çok eşzamanlı rastgele başarısızlık da oluşabilir. Bunlar gerçek bir olağanüstü duruma neden olabilir.


### <a name="random-failures-leading-to-service-failures"></a>Rastgele hatalarda hizmet hatalarıyla başa dön
Hizmetin `InstanceCount` 5 olduğunu ve bu örnekleri çalıştıran birkaç düğümün aynı anda başarısız olduğunu varsayalım. Service Fabric, diğer düğümlerde otomatik olarak değiştirme örnekleri oluşturarak yanıt verir. Hizmet istenen örnek sayısına geri alınana kadar değişiklik oluşturmaya devam edecektir. Başka bir örnek olarak,-1 `InstanceCount`durum bilgisi olmayan bir hizmet olduğunu varsayalım. Bu, kümedeki tüm geçerli düğümlerde çalıştığı anlamına gelir. Bu örneklerden bazılarının başarısız olduğunu varsayalım. Bu durumda, Service Fabric hizmetin istenen durumunda olmadığından ve içerdikleri düğümlerde örnekleri oluşturmaya çalıştığını fark eder. 

Durum bilgisi olan hizmetler için durum hizmetin kalıcı duruma sahip olup olmamasına bağlıdır. Ayrıca, hizmetin kaç çoğaltmasını ve kaç tane başarısız olduğunu da bağlı olarak değişir. Durum bilgisi olan bir hizmet için olağanüstü durum oluşup oluşmadığını belirleme ve bu hizmetin yönetilmesi üç aşamadan oluşur:

1. Çekirdek kaybı olup olmadığını belirleme
   - Çekirdek kaybı, durum bilgisi olan bir hizmetin çoğaltmalarının çoğunluğunun, birincil dahil olmak üzere aynı anda kapatılabilen bir süredir.
2. Çekirdek kaybının kalıcı olup olmadığını belirleme
   - Çoğu zaman, başarısızlıklar geçicidir. İşlem yeniden başlatıldı, düğümler yeniden başlatıldı, VM 'Ler yeniden başlatılıyor, ağ bölümlerinin sistem bölümlerini geri al. Yine de, sorunlar kalıcı olabilir. 
     - Kalıcı duruma sahip olmayan hizmetler için, çekirdek veya daha fazla çoğaltmalardan oluşan bir hata _hemen_ kalıcı çekirdek kaybına neden olur. Service Fabric, durum bilgisi olmayan kalıcı olmayan bir hizmette çekirdek kaybını algıladığında, anında (potansiyel) veri kaybı bildirerek 3. adıma geçer. Veri kaybına devam etmek, Service Fabric çoğaltmalarının geri dönmesi beklenmediği, ancak kurtarılsa bile boş olacağından emin olun.
     - Durum bilgisi olmayan kalıcı hizmetler için bir çekirdekte veya daha fazla çoğaltmalardan oluşan bir hata, çoğaltmaların geri dönüp çekirdeği geri yükleme için Service Fabric başlatılmasına neden olur. Bu durum, hizmetin etkilenen bölüme (veya "çoğaltma kümesi") herhangi bir _yazma işlemleri_ için bir hizmet kesintisine neden olur. Ancak, daha düşük tutarlılık garantisi ile okuma yapılabilmeye devam edebilir. Service Fabric, çekirdeğin geri yüklenmesini bekleyeceği varsayılan süre sonsuzdur, çünkü devam eden bir (potansiyel) veri kaybı olayı ve diğer riskleri taşır. Varsayılan `QuorumLossWaitDuration` değerinin geçersiz kılınması mümkün olsa da önerilmez. Bunun yerine, aşağı çoğaltmaları geri yüklemek için tüm çabalar yapılmalıdır. Bu, yedeklenecek düğümlerin yapılmasını ve yerel kalıcı durumu depolarsa sürücüleri yeniden takabilecekleri şekilde emin olmanızı gerektirir. Çekirdek kaybını işlem arızasından kaynaklandıysanız, Service Fabric otomatik olarak işlemleri yeniden oluşturmayı dener ve çoğaltmaları bunların içinde yeniden başlatır. Bu başarısız olursa, Service Fabric sistem durumu hatalarını raporlar. Bunlar çözülebiliyorsa çoğaltmalar genellikle geri gelir. Bazen çoğaltmalar geri alınamaz. Örneğin, sürücülerin hepsi başarısız olabilir veya makineler fiziksel olarak bir şekilde yok edilir. Bu gibi durumlarda artık kalıcı bir çekirdek kaybı olayımız vardır. Service Fabric, aşağı çoğaltmaların geri dönmesi beklenmesini durdurmak için, bir Küme Yöneticisi hangi hizmetlerin etkilendiğini saptamalıdır ve `Repair-ServiceFabricPartition -PartitionId` veya `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API 'sini çağırır.  Bu API, Quorumkaybetme dışına ve olası datalom 'ye taşımak için bölüm KIMLIĞININ belirtilmesine izin verir.

   > [!NOTE]
   > Belirli bölümlerde hedeflenen bir şekilde bu API 'nin kullanılması _hiçbir_ daha güvenli değildir. 
   >

3. Gerçek veri kaybı olup olmadığını belirleme ve yedeklerden geri yükleme
   - Service Fabric `OnDataLossAsync` yöntemini çağırdığında, bu her zaman _şüpheli_ veri kaybı nedeniyle oluşur. Service Fabric, bu çağrının kalan _en iyi_ çoğaltmaya teslim edilmesini sağlar. Bu, en son ilerlemeyi yapan bir yinelemedir. Her zaman _şüpheli_ veri kaybını söyliyoruz, kalan çoğaltmanın, birincil yaptığı sırada gerçekten aynı duruma sahip olduğundan emin olma olasılığı vardır. Bununla birlikte, bu durum ile karşılaştırılabilmesi için, Service Fabric veya operatörlerin emin olup olmadığını bilmek için iyi bir yol yoktur. Bu noktada, Service Fabric diğer çoğaltmaların geri gelmediği de biliyor. Bu, çekirdek kaybının kendisini çözümlemek için beklemeyi durdurduğumızda yapılan karardır. Hizmet için en iyi eylem, genellikle donabilir ve belirli yönetim müdahalesini bekler. Bu nedenle `OnDataLossAsync` yönteminin tipik bir uygulanması ne yapar?
   - İlk olarak, `OnDataLossAsync` tetiklendiğini ve gerekli tüm yönetim uyarılarını tetiklediğini unutmayın.
   - Genellikle bu noktada, daha fazla kararlar ve el ile gerçekleştirilen eylemler için duraklama ve bekleme. Bunun nedeni, yedeklemelerin kullanılabilir olduğu durumlarda bile hazırlanmaları gerekebilir. Örneğin, iki farklı hizmetin bilgileri koordine etmesidir, geri yükleme işleminin iki hizmetin ilgilendiğinden emin olmak için bu yedeklerin değiştirilmesi gerekebilir. 
   - Genellikle başka bir telemetri veya hizmetten de daha fazla yer vardır. Bu meta veriler diğer hizmetlerde veya günlüklerde bulunabilir. Bu bilgiler, yedekte bulunmayan ve bu belirli çoğaltmaya çoğaltılan birincil konumda alınan ve işlenen herhangi bir çağrı olup olmadığını belirlemede kullanılabilir. Geri yükleme uygulanabilir olmadan önce bunların yeniden yürütülmesi veya yedeklemeye eklenmesi gerekebilir.  
   - Kalan çoğaltmanın durumunun, kullanılabilir olan tüm yedeklemelerde yer alan karşılaştırmaları. Service Fabric güvenilir koleksiyonlar kullanılıyorsa, [Bu makalede](service-fabric-reliable-services-backup-restore.md)açıklanan araçlar ve süreçler vardır. Amaç, çoğaltmanın içindeki durumun yeterli olup olmadığını ya da yedeklemenin eksik olabileceğini görmektir.
   - Karşılaştırma yapıldıktan sonra ve geri yükleme tamamlandıktan sonra, herhangi bir durum değişikliği yapılırsa hizmet kodu true döndürmelidir. Çoğaltma, durumun en iyi kullanılabilir kopyası olduğunu tespit ederseniz ve hiçbir değişiklik yapmamışsa, false döndürün. Doğru, kalan _diğer_ çoğaltmaların artık bununla tutarsız olabileceğini gösterir. Bunlar bu çoğaltmadan bırakılır ve yeniden oluşturulur. False, hiçbir durum değişikliği yapılmadığını gösterir, bu nedenle diğer çoğaltmalar onların sahip oldukları öğeleri tutabilir. 

Hizmet yazarlarının, hizmetler üretime dağıtılmadan önce olası veri kaybını ve hata senaryolarını uygulaması önemli ölçüde önemlidir. Veri kaybı olasılığa karşı korunmak için, durum bilgisi olan hizmetlerinizin herhangi birinin [durumunu](service-fabric-reliable-services-backup-restore.md) coğrafi olarak yedekli bir mağazaya düzenli aralıklarla yedeklemeniz önemlidir. Ayrıca, geri yükleme olanağına sahip olduğunuzdan emin olmanız gerekir. Birçok farklı hizmetin yedekleri farklı zamanlarda alındığından, hizmetlerinizin bir sonraki geri yükleme işleminden sonra tutarlı bir görünüm olduğundan emin olmanız gerekir. Örneğin, bir hizmetin bir sayı oluşturduğu ve depolandığı bir durum düşünün, sonra da onu depolayan başka bir hizmete gönderir. Geri yükleme sonrasında ikinci hizmetin numaranın olduğunu, ancak Birincisi, bu işlemi içermediği için birinci öğenin ne olduğunu fark edebilirsiniz.

Kalan çoğaltmaların bir veri kaybı senaryosunda devam etmek için yetersiz olduğunu fark ederseniz ve hizmet durumunu telemetri veya tüketen yeniden oluşturamazsınız. yedeklemelerinizin sıklığı, mümkün olan en iyi kurtarma noktası hedefini (RPO) belirler. Service Fabric, kalıcı çekirdek ve yedeklemeden geri yükleme gerektiren veri kaybı dahil çeşitli hata senaryolarını test etmek için birçok araç sağlar. Bu senaryolar, hata analizi hizmeti tarafından yönetilen Service Fabric 'nin test araçlarının bir parçası olarak dahil edilmiştir. Bu araçlar ve desenler hakkında daha fazla bilgiye [buradan](service-fabric-testability-overview.md)ulaşabilirsiniz. 

> [!NOTE]
> Sistem Hizmetleri, söz konusu hizmete özel etkiyle aynı zamanda çekirdek kaybını da etkileyebilir. Örneğin, adlandırma hizmetindeki çekirdek kaybı ad çözümlemesini etkiler, ancak yük devretme Yöneticisi hizmetindeki çekirdek kaybı yeni hizmet oluşturma ve yük devretmeyi engeller. Service Fabric sistem hizmetleri, durum yönetimi için hizmetlerinizde aynı modele sahip olsa da, bunları çekirdek kaybını ve olası veri kaybına taşımayı denemeniz önerilmez. Öneri, belirli durumunuza hedeflenmiş bir çözümü belirlemede [destek](service-fabric-support.md) almak için kullanılır.  Genellikle, aşağı çoğaltmalar döndürülene kadar beklemeniz tercih edilir.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Service Fabric kümesinin kullanılabilirliği
Genellikle Service Fabric kümenin kendisi, tek hata noktaları olmayan, yüksek oranda dağıtılmış bir ortamdır. Birincil olarak, Service Fabric sistem hizmetleri daha önce sunulan yönergeleri izlediğinden, herhangi bir düğüm, küme için kullanılabilirlik veya güvenilirlik sorunlarına neden olmaz: her zaman varsayılan olarak üç veya daha fazla çoğaltma ile çalışır ve bu sistem tüm düğümlerde durum bilgisiz çalıştırılan hizmetler. Temel Service Fabric ağ oluşturma ve hata algılama katmanları tamamen dağıtılır. Çoğu sistem hizmeti kümedeki meta verilerden yeniden oluşturulabilir veya durumlarını diğer yerlerden yeniden eşitleme yapabilir. Sistem Hizmetleri yukarıda açıklananlar gibi çekirdek kaybı durumlarına geldiğinde kümenin kullanılabilirliği tehlikeye girebilir. Bu gibi durumlarda, küme üzerinde yükseltme başlatma veya yeni hizmetleri dağıtma gibi bazı işlemleri gerçekleştiremeyebilirsiniz, ancak kümenin kendisi hala çalışır durumda. Çalışmakta olan hizmetler, çalışmaya devam etmek için sistem hizmetlerine yazma gerektirmedikleri takdirde bu koşullarda çalışmaya devam edecektir. Örneğin, Yük Devretme Yöneticisi çekirdek kaybolduysa, tüm hizmetler çalışmaya devam eder, ancak bu, Yük Devretme Yöneticisi katılımına ihtiyaç duyduğundan, başarısız olan tüm hizmetler otomatik olarak yeniden başlatılamaz. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Veri merkezi veya Azure bölgesinin başarısızlığı
Nadir durumlarda, güç veya ağ bağlantısı kaybı nedeniyle fiziksel bir veri merkezi geçici olarak kullanılamaz hale gelebilir. Bu gibi durumlarda, bu veri merkezinde veya Azure bölgesindeki Service Fabric kümeleriniz ve hizmetleriniz kullanılamaz olur. Ancak _verileriniz korunur_. Azure 'da çalışan kümeler için, [Azure durum sayfasında][azure-status-dashboard]kesintiler üzerinde güncelleştirmeleri görüntüleyebilirsiniz. Fiziksel bir veri merkezinin kısmen veya tamamen yok edildiği yüksek düzeyde etkilenmeyen bir olayda, burada barındırılan Service Fabric kümeleri veya içerdikleri hizmetler kaybolabilir. Bu, söz konusu veri merkezinin veya bölgenin dışında yedeklenen tüm durumları içerir.

Tek bir veri merkezinde veya bölgede kalıcı veya sürekli arızası olması için iki farklı strateji vardır. 

1. Bu tür bölgelerde ayrı Service Fabric kümelerini çalıştırın ve bu ortamlar arasında yük devretme ve geri dönme mekanizmalarına yönelik bir mekanizmaya yararlanın. Bu çok küme etkin-etkin veya aktif-pasif modeli sıralaması ek yönetim ve operasyon kodu gerektirir. Bu aynı zamanda, bir veri merkezinde veya bölgede hizmetlerden, bir hata oluştuğunda diğer veri merkezlerinde veya bölgelerde kullanılabilir olmaları için yedeklemelerin koordine edilmesini gerektirir. 
2. Birden çok veri merkezine veya bölgeye yayılan tek bir Service Fabric kümesi çalıştırın. Bunun için desteklenen en düşük yapılandırma üç veri merkezi veya bölgedir. Önerilen bölge veya veri merkezi sayısı beş ' dir. Bu, daha karmaşık bir küme topolojisi gerektirir. Bununla birlikte, bu modelin avantajı bir veri merkezinde veya bölgede hata oluşması durumunda normal bir hataya dönüştürülmesidir. Bu arızalar, tek bir bölgedeki kümeler için çalışan mekanizmalar tarafından işlenebilir. Hata etki alanları, yükseltme etki alanları ve Service Fabric yerleştirme kuralları, iş yüklerinin normal hatalara neden olacak şekilde dağıtılmasını güvence altına alırlar. Bu tür kümede Hizmetleri işletmek için yardımcı olabilecek ilkeler hakkında daha fazla bilgi için [yerleştirme ilkelerine](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) göre oku

### <a name="random-failures-leading-to-cluster-failures"></a>Küme hatalarıyla başa çıkabilecek rastgele sorunlar
Service Fabric çekirdek düğümleri kavramıdır. Bunlar, temel alınan kümenin kullanılabilirliğini sürdürdüğüm olan düğümlerdir. Bu düğümler, diğer düğümlerle Kiralama kurarak ve belirli ağ başarısızlığı türleri sırasında tiebreaklikler gören kümenin devam ettiğinden emin olmaya yardımcı olur. Rastgele arızalar kümedeki çekirdek düğümlerin çoğunu kaldırırsa ve geri getirilmezse, çekirdek düğüm çekirdeğini kaybettiğiniz ve küme başarısız olursa, küme Federasyon halkası daraltılır. Azure 'da Service Fabric kaynak sağlayıcısı Service Fabric küme yapılandırmasını yönetir ve varsayılan olarak çekirdek düğümlerini birincil düğüm tür hatası ve yükseltme etki alanlarına dağıtır; Birincil NodeType gümüş veya altın dayanıklılık olarak işaretlenmişse, birincil NodeType ' i ölçeklendirerek veya bir çekirdek düğümünü el ile kaldırarak bir çekirdek düğümünü kaldırdığınızda, küme kullanılabilir birincil NodeType ile başka bir çekirdek olmayan düğümü yükseltmeyi dener kapasite ve küme güvenilirlik düzeyinden daha az kullanılabilir kapasiteye sahipseniz, birincil düğüm türü için ihtiyaç duydıysanız bu işlem başarısız olur.

Tek başına Service Fabric kümelerinde ve Azure 'da, "birincil düğüm türü", Seeds 'yi çalıştıran bir ektir. Birincil düğüm türü tanımlarken, Service Fabric, her bir sistem hizmetinin en fazla 9 tohum düğümü ve 7 çoğaltmasını oluşturarak, belirtilen düğüm sayısından otomatik olarak yararlanır. Rastgele bir başarısızlık kümesi bu sistem hizmeti çoğaltmalarının çoğunu aynı anda alırsa, yukarıda açıklanan şekilde sistem hizmetleri çekirdek kaybına girer. Çekirdek düğümlerin çoğunluğu kaybolmuşsa, küme yakında kapatılacak.

## <a name="next-steps"></a>Sonraki adımlar
- [Test edilebilirlik çerçevesini](service-fabric-testability-overview.md) kullanarak çeşitli hataların benzetimini yapmayı öğrenin
- Diğer olağanüstü durum kurtarma ve yüksek kullanılabilirlik kaynaklarını okuyun. Microsoft bu konularda büyük miktarda kılavuz yayımlamıştır. Bu belgelerden bazıları diğer ürünlerde kullanılmak üzere belirli tekniklerin başvurduğu sürece, Service Fabric bağlamına uygulayabileceğiniz birçok genel en iyi uygulama de vardır:
  - [Kullanılabilirlik denetim listesi](/azure/architecture/checklist/resiliency-per-service)
  - [Olağanüstü durum kurtarma detayına gitme](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Azure uygulamaları için olağanüstü durum kurtarma ve yüksek kullanılabilirlik][dr-ha-guide]
- [Service Fabric destek seçenekleri](service-fabric-support.md) hakkında bilgi edinin


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
