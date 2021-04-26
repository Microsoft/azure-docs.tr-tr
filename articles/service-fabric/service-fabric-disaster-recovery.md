---
title: Azure Service Fabric olağanüstü durum kurtarma
description: Azure Service Fabric, olağanüstü kişilerle uğraşmak için yetenekler sunar. Bu makalede, oluşabilecek olağanüstü durum türleri ve bunlarla nasıl uğraşmanız açıklanmaktadır.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8d99b4d1fbf227d850de387b7ca24dcd3fd40646
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791164"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Azure Service Fabric olağanüstü durum kurtarma
Yüksek kullanılabilirlik sunmaya yönelik kritik bir bölüm, hizmetlerin tüm farklı türdeki hataların varlığını sürdürmesini sağlamaktır. Bu özellikle, planlanmamış ve denetiminizin dışından oluşan hatalarda önemlidir. 

Bu makalede, doğru Modellenmemiş ve yönetilmiyorsa olağanüstü durumlar olabilecek bazı yaygın hata modları açıklanmaktadır. Ayrıca, bir olağanüstü durum yine de gerçekleşse de hafifletmede gerçekleştirilecek işlemler açıklanmaktadır. Amaç, gerçekleşen veya aksi takdirde meydana gelme durumunda kapalı kalma süresi veya veri kaybı riskini sınırlandırmaktır veya ortadan kaldırır.

## <a name="avoiding-disaster"></a>Olağanüstü durumdan kaçınma
Azure Service Fabric 'nin ana amacı, hem ortamınızı hem de hizmetlerinizi ortak hata türleri olağanüstü durumlar olmayan bir şekilde modeletmenize yardımcı olmaktır. 

Genel olarak, iki tür olağanüstü durum/başarısızlık senaryosu vardır:
- Donanım ve yazılım hataları
- İşletimsel hatalar

### <a name="hardware-and-software-faults"></a>Donanım ve yazılım hataları
Donanım ve yazılım hataları tahmin edilemez. Arızaların en kolay yolu, hizmetin donanım veya yazılım hatası sınırları genelinde daha fazla kopyasını çalıştırıyor. 

Örneğin, hizmetiniz yalnızca bir makine üzerinde çalışıyorsa, söz konusu hizmet için bu makinenin bir olağanüstü durum olması gerekir. Bu olağanüstü durumdan kaçınmanın basit yolu, hizmetin birden fazla makinede çalıştığından emin olmak. Test, bir makinenin başarısızlığının çalışan hizmeti kesintiye uğramasını sağlamak için de gereklidir. Kapasite planlaması, bir değiştirme örneğinin başka bir yerde oluşturulmasını ve kapasiteden azaltmanın kalan Hizmetleri aşırı yükmesini sağlar. 

Aynı model, arızasından kaçınmak için ne olursa olsun, işe yarar. Örneğin, bir SAN hatası hakkında endişeleriniz varsa, birden çok San arasında çalıştırırsınız. Bir sunucu rafı kaybı hakkında endişeleriniz varsa, birden çok rafta çalıştırılırsınız. Veri merkezlerinin kaybedilmesi konusunda endişeli varsa, hizmetiniz birden fazla Azure bölgesinde, birden çok Azure Kullanılabilirlik Alanları veya kendi veri merkezlerinizde çalışmalıdır. 

Bir hizmet birden çok fiziksel örneğe (makineler, raflar, veri merkezi, bölge) yayıldığınızda, yine de bazı eşzamanlı arızalara tabi olursunuz. Ancak, belirli bir türdeki (örneğin, tek bir sanal makine veya ağ bağlantısı başarısız) birden çok hata, otomatik olarak işlenir ve bu nedenle artık "olağanüstü durum" olmaz. 

Service Fabric, kümeyi genişletmeye yönelik mekanizmalar sağlar ve başarısız olan düğümleri ve hizmetleri geri getiren işler. Service Fabric Ayrıca, planlanmamış hataların gerçek felaketlere dönmesini engellemek için hizmetlerinizin birçok örneğinin çalıştırılmasına izin verir.

Dağıtım başarısızlıklar için yeterince büyük bir dağıtımı çalıştırmanın olası nedenleri olabilir. Örneğin, hata ihtimaline göre ödeme yapmayı amaçlamadan daha fazla donanım kaynağı alabilir. Dağıtılmış uygulamalarla ilgilenirken, coğrafi uzaklıklarda ek iletişim atlamaları veya durum çoğaltma maliyetleri kabul edilemez gecikmeye neden olabilir. Bu çizginin her bir uygulama için farklı çizilir. 

Özellikle yazılım hatalarında hata, ölçeklendirmeye çalıştığınız hizmette olabilir. Bu durumda, hata koşulu tüm örneklerde bağıntılı olduğundan, çok sayıda kopya olağanüstü durum oluşmasını engellemez.

### <a name="operational-faults"></a>İşletimsel hatalar
Hizmetiniz birçok artıklıkları sahip dünya genelinde yayılsa bile, felaket olayları yaşamaya devam edebilir. Örneğin, birisi hizmetin DNS adını yanlışlıkla yeniden yapılandırabilir veya onu silebilir. 

Örnek olarak, durum bilgisi olan bir Service Fabric hizmetiniz olduğunu ve birisi bu hizmeti yanlışlıkla silmiş olduğunu varsayalım. Başka bir risk azaltma, bu hizmet ve sahip olduğu tüm durumlar dışında. Bu tür işlemsel olağanüstü durumlar ("Yani"), düzenli plansız hatalardan farklı azaltmaları ve işlemler gerektirir. 

Bu tür işletimsel hataların oluşmaması için en iyi yollar şunlardır:
- Ortama işlemsel erişimi kısıtlayın.
- Tehlikeli işlemleri kesinlikle denetleyin.
- Otomasyon yapın, el ile veya bant dışı değişiklikleri önleyin ve ortama göre belirli değişiklikleri ortama göre doğrular.
- Bozucu işlemlerin "Soft" olduğundan emin olun. Geçici işlemler hemen etkili olmaz veya bir zaman penceresi içinde geri alınamaz.

Service Fabric, küme işlemleri için [rol tabanlı](service-fabric-cluster-security-roles.md) erişim denetimi sağlama gibi işletimsel hataların önlenmesi için mekanizmalar sağlar. Ancak, bu işletimsel hataların çoğu kuruluş çalışmalarını ve diğer sistemleri gerektirir. Service Fabric, işlem hatalarının büyük bir şekilde [yedekleme ve durum bilgisi olan hizmetler için geri yükleme](service-fabric-backuprestoreservice-quickstart-azurecluster.md)mekanizmaları sağlar.

## <a name="managing-failures"></a>Sorunları yönetme
Service Fabric amacı, hataların otomatik olarak yönetimi olur. Ancak bazı başarısızlık türlerini işlemek için hizmetlerin ek kodu olmalıdır. Diğer başarısızlık türleri, güvenlik ve iş sürekliliği nedenleriyle otomatik olarak _değinmemelidir._ 

### <a name="handling-single-failures"></a>Tek başarısızlık işleme
Tek makineler, tüm nedenlerden dolayı başarısız olabilir. Bazen güç kaynakları ve ağ donanım arızaları gibi donanım nedenleridir. Diğer sorunlar yazılımda bulunur. Bunlar, işletim sistemi ve hizmetin arızalarını içerir. Service Fabric, ağ sorunları nedeniyle makinenin diğer makinelerden yalıtıldığı durumlar da dahil olmak üzere bu tür hataları otomatik olarak algılar.

Hizmetin türü ne olursa olsun, tek bir örnek çalıştırıldığında kodun tek bir kopyası herhangi bir nedenle başarısız olursa, bu hizmet için kapalı kalma süresi oluşur. 

Tek bir hatayı işlemek için, yapabileceğiniz en basit şey, hizmetlerinizin varsayılan olarak birden fazla düğüm üzerinde çalışmasını sağlamaktır. Durum bilgisi olmayan hizmetler için 1 ' `InstanceCount` den büyük olduğundan emin olun. Durum bilgisi olan hizmetler için en düşük öneri, `TargetReplicaSetSize` `MinReplicaSetSize` her ikisi de 3 olarak ayarlanmıştır. Hizmet kodunuzun daha fazla kopyasını çalıştırmak, hizmetinizin tek bir hatayı otomatik olarak işlemesini sağlar. 

### <a name="handling-coordinated-failures"></a>Eşgüdümlü sorunları işleme
Küme içindeki Eşgüdümlü hatalara, planlı veya planlanmamış altyapı hatalarından ve değişikliklerden veya planlı yazılım değişikliklerinden kaynaklanabilir. Service Fabric, *hata etki alanları* olarak Eşgüdümlü hatalarla karşılaşan altyapı bölgelerini modelleyin. Koordine edilen yazılım değişikliklerini deneyime yönelik alanlara *yükseltme etki alanları* olarak modellenir. Hata etki alanları, yükseltme etki alanları ve küme topolojisi hakkında daha fazla bilgi için bkz. [küme kaynak yöneticisi kullanarak Service Fabric kümesi tanımlama](service-fabric-cluster-resource-manager-cluster-description.md).

Service Fabric, hizmetlerinizin nerede çalışacağını planlarken, hata ve yükseltme etki alanlarını varsayılan olarak değerlendirir. Service Fabric, varsayılan olarak, hizmetlerinizin birkaç hata ve yükseltme etki alanında çalıştığından emin olmaya çalışır, böylece planlanmış veya planlanmamış değişiklikler meydana geliyorsa, hizmetleriniz kullanılabilir kalır. 

Örneğin, bir güç kaynağı hatasının, bir rafa ait tüm makinelerin aynı anda başarısız olmasına neden olduğunu varsayalım. Hizmetin birden fazla kopyası çalışırken, hata etki alanı hatasında çok sayıda makinenin kaybolması, bir hizmet için tek bir başarısızlığın yalnızca başka bir örneğini döndürür. Bu, hizmetlerinizin yüksek kullanılabilirlik sağlamak için hata ve yükseltme etki alanlarının yönetilmesine neden önemlidir. 

Azure 'da Service Fabric çalıştırırken, hata etki alanları ve yükseltme etki alanları otomatik olarak yönetilir. Diğer ortamlarda, bu olmayabilir. Şirket içinde kendi kümelerinizi oluşturuyorsanız, hata etki alanı düzeninizi doğru şekilde eşleştirdiğinizden ve planladığınızdan emin olun.

Yükseltme etki alanları, yazılımın aynı anda yükseltilebileceği modelleme alanları için faydalıdır. Bu nedenle, yükseltme etki alanları da genellikle planlanan yükseltmeler sırasında yazılımın kapatıldığı sınırları tanımlar. Hem Service Fabric hem de hizmetlerinizin yükseltmeleri aynı modeli izler. Sıralı yükseltmeler, yükseltme etki alanları ve istenmeyen değişikliklerin kümeyi ve hizmetinizi etkilemesini önlemeye yardımcı olan Service Fabric sistem durumu modeli hakkında daha fazla bilgi için, bkz.:

 - [Uygulama yükseltme](service-fabric-application-upgrade.md)
 - [Uygulama yükseltme öğreticisi](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric sistem durumu modeli](service-fabric-health-introduction.md)

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)' de belirtilen küme eşlemesini kullanarak kümenizin yerleşimini görselleştirebilirsiniz:

<center>

![Service Fabric Explorer, düğüm hata etki alanları arasında yayılır][sfx-cluster-map]
</center>

> [!NOTE]
> Hizmetlerin hata ve yükseltme etki alanlarında çalıştığından emin olmak için hata, dağıtım, yükseltme, hizmet kodunuzun ve durumlarınızın birçok örneğini çalıştırma, yerleştirme kuralları ve yerleşik sistem durumu izleme alanları, normal işlem sorunlarını ve hatalarının olağanüstü duruma dönmesini sağlamak için Service Fabric sağladığı özelliklerden *bazılarıdır* . 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Eşzamanlı donanım veya yazılım başarısızlıklarını işleme
Tek hatalardan bahsetik. Gördüğünüz gibi, hata ve yükseltme etki alanlarında çalışan kodun (ve durumun) daha fazla kopyasını tutarak hem durum bilgisi olmayan hem de durum bilgisi olan hizmetler için kolayca idare edilebilir. 

Birden çok eşzamanlı rastgele başarısızlık da oluşabilir. Bunlar, kapalı kalma süresine veya gerçek bir olağanüstü duruma neden olabilir.


#### <a name="stateless-services"></a>Durum bilgisi olmayan hizmetler
Durum bilgisi olmayan bir hizmetin örnek sayısı, çalışması gereken birkaç örnek sayısını gösterir. Örneklerin herhangi biri (veya tümü) başarısız olduğunda, Service Fabric diğer düğümlerde otomatik olarak değiştirme örnekleri oluşturarak yanıt verir. Service Fabric, hizmet istenen örnek sayısına geri alınana kadar değişiklik oluşturmaya devam eder.

Örneğin, durum bilgisi olmayan hizmetin `InstanceCount` -1 değerine sahip olduğunu varsayalım. Bu değer, kümedeki her düğümde bir örnek çalıştırılması gereken anlamına gelir. Bu örneklerden bazıları başarısız olursa, Service Fabric hizmetin istenen durumunda olmadığını algılar ve örnekleri eksik oldukları düğümlerde oluşturmaya çalışır.

#### <a name="stateful-services"></a>Durum bilgisi olan hizmetler
İki tür durum bilgisi olan hizmetler vardır:
- Kalıcı durumla durum bilgisi.
- Kalıcı olmayan durumla durum bilgisi. (Durum bellekte depolanır.)

Durum bilgisi olan bir hizmetin hatasından kurtarma, durum bilgisi olan hizmetin türüne, hizmetin kaç çoğaltmasını olduğunu ve kaç yinelemenin başarısız olduğunu bağlıdır.

Durum bilgisi olan bir hizmette, gelen veriler çoğaltmalar arasında çoğaltılır (birincil ve tüm etkin ikincil öğeler). Çoğaltmaların bir çoğunluğu verileri alıyorsa, veriler *çekirdek* olarak kabul edilir. (Beş çoğaltma için, üç bir çekirdek olacaktır.) Bu, herhangi bir noktada, en son verileri en az bir çoğaltma çekirdeği olacağı anlamına gelir. Çoğaltmalar başarısız olursa (beş ' dan fazla bilgi alırsanız), kurtaramadığımızda hesaplamak için çekirdek değerini kullanabiliriz. (En fazla beş çoğaltma hala devam ettiğinden, en az bir çoğaltmanın tüm verileri alacak olması garanti edilir.)

Kopyaların bir çekirdeği başarısız olduğunda, bölüm bir *çekirdek kaybı* durumunda olacak şekilde bildirilmiştir. Bir bölümde beş çoğaltma olduğunu varsayalım. Bu, en az üçünün tüm verilerin bulunduğu garantiden fazla garanti olduğu anlamına gelir. Bir çekirdek (beş/beş) çoğaltma başarısız olursa, Service Fabric kalan çoğaltmaların (beş çıkış) bölümü geri yüklemek için yeterli veri olup olmadığını belirleyemez. Service Fabric çekirdek kaybını algıladığı durumlarda, varsayılan davranışı bölüme ek yazma işlemlerini önlemektir, çekirdek kaybını bildirir ve çoğaltmaların bir çekirdeğin geri yüklenmesini bekler.

Durum bilgisi olan bir hizmet için olağanüstü durum olup olmadığını belirleme ve sonra üç aşamada yönetme:

1. Çekirdek kaybı olup olmadığını belirleme.
   
   Durum bilgisi olan bir hizmetin çoğaltmalarının çoğunluğu aynı anda kapalıysa çekirdek kaybı bildirilmiştir.
2. Çekirdek kaybının kalıcı olup olmadığını belirleme.
   
   Çoğu zaman, başarısızlıklar geçicidir. İşlem yeniden başlatıldı, düğümler yeniden başlatıldı, sanal makineler geri alınıyor ve ağ bölümleri de aynı. Bazen, sorunlar kalıcı olabilir. Hataların kalıcı olup olmadığı veya olmaması, durum bilgisi olan hizmetin durumunu veya yalnızca bellekte tutulmasını ister. 
   
   - Kalıcı duruma sahip olmayan hizmetler için, çekirdek veya daha fazla çoğaltmalardan oluşan bir hata _hemen_ kalıcı çekirdek kaybına neden olur. Service Fabric, durum bilgisi olmayan kalıcı olmayan bir hizmette çekirdek kaybını algıladığında, anında (potansiyel) veri kaybı bildirerek 3. adıma geçer. Service Fabric çoğaltmaların geri dönmesi beklenmediğini bildiğinden, veri kaybına devam etmek anlamlı hale gelir. Kurtarıldıklarında bile, hizmetin kalıcı olmayan yapısı nedeniyle veriler kaybolur.
   - Durum bilgisi olmayan kalıcı hizmetler için bir çekirdekte veya daha fazla çoğaltmalardan oluşan bir hata, çoğaltmaların geri dönüp çekirdeği geri yükleme işleminin beklemesini Service Fabric neden olur. Bu durum, hizmetin etkilenen bölüme (veya "çoğaltma kümesi") herhangi bir _yazma işlemleri_ için bir hizmet kesintisine neden olur. Ancak, daha düşük tutarlılık garantisi ile okuma yapılabilmeye devam edebilir. Service Fabric, çekirdeğin geri yüklenmesini bekleyeceği varsayılan süre *sonsuzdur* çünkü devam eden bir (potansiyel) veri kaybı olayı ve diğer riskleri taşır. Bu, bir yönetici veri kaybını bildirmek için işlem yapmadığınız takdirde Service Fabric sonraki adıma ilerleyemez.
3. Verilerin kayıp olup olmadığını belirleme ve yedeklerden geri yükleme.

   Çekirdek kaybı bildirilirse (otomatik olarak veya yönetim eylemi aracılığıyla), Service Fabric ve hizmetler, verilerin gerçekten kayıp olup olmadığını belirlemek için taşınır. Bu noktada, Service Fabric diğer çoğaltmaların geri geldiğini de biliyor. Bu, çekirdek kaybının kendisini çözümlemek için beklemeyi durdurduğumızda yapılan karardır. Hizmet için en iyi eylem, genellikle donabilir ve belirli yönetim müdahalesini bekler.
   
   Service Fabric `OnDataLossAsync` yöntemi çağırdığında, bu her zaman _şüpheli_ veri kaybı nedeniyle oluşur. Service Fabric, bu çağrının kalan _en iyi_ çoğaltmaya teslim edilmesini sağlar. Bu, en son ilerlemeyi yapan bir yinelemedir. 
   
   Her zaman _şüpheli_ veri kaybını söyliyoruz, kalan çoğaltmanın çekirdek kaybedildiğinde birincil was ile aynı duruma sahip olma olasılığı vardır. Bununla birlikte, bu durum ile karşılaştırılabilmesi için, Service Fabric veya operatörlerin emin olup olmadığını bilmek için iyi bir yol yoktur.     
   
   Bu nedenle yöntemin tipik bir uygulanması ne yapar `OnDataLossAsync` ?
   1. Tetiklenen uygulama günlükleri `OnDataLossAsync` ve gerekli yönetim uyarılarını tetikledi.
   1. Genellikle, uygulama duraklatılır ve daha fazla kararlar ve el ile gerçekleştirilen eylemler için bekler. Bunun nedeni, yedeklemeler kullanılabilir olsa bile bunların hazırlanmaları gerekebilir. 
   
      Örneğin, iki farklı hizmetin bilgileri koordine etmesidir, geri yükleme yapıldıktan sonra, bu iki hizmetin ilgilendiğinden emin olmak için bu yedeklemelerin değiştirilmesi gerekebilir. 
   1. Genellikle başka bir telemetri veya hizmetten söz konusu olabilir. Bu meta veriler diğer hizmetlerde veya günlüklerde bulunabilir. Bu bilgiler, yedekte bulunmayan ve bu belirli çoğaltmaya çoğaltılan birincil konumda alınan ve işlenen herhangi bir çağrı olup olmadığını öğrenmek için gerektiği şekilde kullanılabilir. Geri yükleme mümkün olmadan önce bu çağrıların yeniden yürütülmesi veya yedeklemeye eklenmesi gerekebilir.  
   1. Uygulama, kalan çoğaltmanın durumunu kullanılabilir yedeklemelerde bulunan ile karşılaştırır. Service Fabric güvenilir koleksiyonlar kullanıyorsanız, bu işlemi gerçekleştirmek için kullanabileceğiniz [Araçlar ve süreçler](service-fabric-reliable-services-backup-restore.md) vardır. Amaç, çoğaltmanın içindeki durumunun yeterli olup olmadığını ve yedeklemenin eksik olabileceğini görmenizi sağlamaktır.
   1. Karşılaştırma yapıldıktan sonra ve geri yükleme tamamlandıktan sonra (gerekliyse), herhangi bir durum değişikliği yapılırsa hizmet kodu **true** döndürmelidir. Çoğaltma, durumun en iyi kullanılabilir kopyası olduğunu tespit ederseniz ve hiçbir değişiklik yapmamışsa, kod **false** döndürür. 
   
      **True** değeri, kalan _diğer_ çoğaltmaların artık bununla tutarsız olabileceğini gösterir. Bunlar bu çoğaltmadan bırakılır ve yeniden oluşturulur. **Yanlış** değeri, hiçbir durum değişikliği yapılmadığını gösterir, bu nedenle diğer çoğaltmalar onların neleri tutabileceklerini tutabilir. 

Hizmet yazarlarının, hizmetler üretimde dağıtılmadan önce olası veri kaybını ve hata senaryolarını uygulaması önemli ölçüde önemlidir. Veri kaybı olasılığa karşı korunmak için, durum bilgisi olan hizmetlerinizin herhangi birinin [durumunu](service-fabric-reliable-services-backup-restore.md) coğrafi olarak yedekli bir mağazaya düzenli aralıklarla yedeklemeniz önemlidir. 

Ayrıca, durumu geri yükleme olanağına sahip olduğunuzdan emin olmanız gerekir. Birçok farklı hizmetin yedekleri farklı zamanlarda alındığından, bir geri yüklemeden sonra hizmetlerinizin tutarlı bir görünümü olduğundan emin olmanız gerekir. 

Örneğin, bir hizmetin bir sayı oluşturduğu ve depolandığı bir durum düşünün ve sonra da onu depolayan başka bir hizmete gönderir. Geri yükleme sonrasında ikinci hizmetin sayının olduğunu, ancak ilki bu işlemi içermediği için bu işlemin başarısız olduğunu fark edebilirsiniz.

Kalan çoğaltmaların bir veri kaybı senaryosunda devam etmek için yetersiz olduğunu fark ederseniz ve hizmet durumunu telemetri veya tüketen yeniden oluşturamazsınız, yedeklemelerinizin sıklığı mümkün olan en iyi kurtarma noktası hedefini (RPO) belirler. Service Fabric, kalıcı çekirdek ve yedekten geri yükleme gerektiren veri kaybı dahil çeşitli hata senaryolarını test etmek için birçok araç sağlar. Bu senaryolar, hata analizi hizmeti tarafından yönetilen Service Fabric 'nin test edilebilirlik araçlarının bir parçası olarak dahil edilmiştir. Bu araçlar ve desenler hakkında daha fazla bilgi için bkz. [hata analiz hizmetine giriş](service-fabric-testability-overview.md). 

> [!NOTE]
> Sistem Hizmetleri, çekirdek kaybını da etkilenebilir. Etkisi, söz konusu hizmete özgüdür. Örneğin, adlandırma hizmetindeki çekirdek kaybı ad çözümlemesini etkiler, ancak Yük Devretme Yöneticisi hizmette çekirdek kaybı yeni hizmet oluşturma ve yük devretme işlemleri engeller. 
> 
> Service Fabric sistem hizmetleri, durum yönetimi için hizmetlerinizde aynı kalıbı izler, ancak bunları çekirdek kaybını ve olası veri kaybına taşımayı öneririz. Bunun yerine, durumunuza hedeflenmiş bir çözüm bulmak için  [destek](service-fabric-support.md) bulmanıza önerilir. Genellikle aşağı çoğaltmalar döndürülene kadar beklemek tercih edilir.
>

#### <a name="troubleshooting-quorum-loss"></a>Çekirdek kaybı sorunlarını giderme

Yinelemeler geçici bir hata nedeniyle aralıklı olarak kalabilir. Service Fabric bir süre bekleyin. Çoğaltmalar beklenen süreden uzun süredir kapalıysa, şu sorun giderme eylemlerini izleyin:
- Çoğaltmalar kilitlenen olabilir. Çoğaltma düzeyi sistem durumu raporlarını ve uygulama günlüklerinizi denetleyin. Kilitlenme dökümünü toplayın ve kurtarmak için gerekli işlemleri yapın.
- Çoğaltma işlemi yanıt veremez duruma gelebilir. Bunu doğrulamak için uygulama günlüklerinizi inceleyin. İşlem dökümlerini toplayın ve ardından yanıt vermeyen işlemi durdurun. Service Fabric, bir değiştirme işlemi oluşturacak ve çoğaltmayı geri getirmeye çalışacaktır.
- Çoğaltmaları barındıran düğümler çalışmıyor olabilir. Düğümleri taşımak için temeldeki sanal makineyi yeniden başlatın.

Bazen, çoğaltmaları kurtarmak mümkün olmayabilir. Örneğin, sürücüler başarısız olmuş veya makineler fiziksel olarak yanıt vermiyor. Bu durumlarda, Service Fabric çoğaltma kurtarması beklenemedi.

Hizmeti çevrimiçi duruma getirmek için olası veri kaybı kabul edilemez ise bu *yöntemleri kullanmayın.* Bu durumda, fiziksel makineleri kurtarmaya yönelik tüm çabalar yapılmalıdır.

Aşağıdaki eylemler veri kaybına neden olabilir. İzlemeden önce denetleyin.
   
> [!NOTE]
> Belirli bölümlerde hedeflenen bir şekilde bu yöntemleri kullanmak _hiç_ güvenli değildir. 
>

- `Repair-ServiceFabricPartition -PartitionId`Veya `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API 'yi kullanın. Bu API, çekirdek kaybını ve olası veri kaybını taşımak için bölüm KIMLIĞININ belirtilmesine izin verir.
- Kümeniz, hizmetlerin bir çekirdek kaybı durumuna geçmesine neden olan ve olası _veri kaybı kabul edilebilir olduğunda_, uygun bir [QuorumLossWaitDuration](/powershell/module/servicefabric/update-servicefabricservice) değeri belirtmek hizmetinizin otomatik olarak kurtarılmasına yardımcı olabilir. Service Fabric, `QuorumLossWaitDuration` Kurtarma gerçekleştirilmeden önce, girilen değeri (varsayılan değer sonsuzdur) bekleyecek. Beklenmedik veri kayıplarının oluşmasına neden olabileceğinden, bu *yöntemi önermiyoruz.*

## <a name="availability-of-the-service-fabric-cluster"></a>Service Fabric kümesinin kullanılabilirliği
Genel olarak Service Fabric kümesi, tek hata noktası olmayan, yüksek oranda dağıtılmış bir ortamdır. Birincil olarak, Service Fabric sistem hizmetleri daha önce sunulan yönergeleri izlediğinden, herhangi bir düğüm, kümede kullanılabilirlik veya güvenilirlik sorunlarına yol açmaz. Diğer bir deyişle, her zaman varsayılan olarak üç veya daha fazla çoğaltma ve durum bilgisiz olmayan sistem hizmetleri tüm düğümlerde çalışır. 

Temel Service Fabric ağ oluşturma ve hata algılama katmanları tamamen dağıtılır. Çoğu sistem hizmeti kümedeki meta verilerden yeniden oluşturulabilir veya durumlarını diğer yerlerden yeniden eşitleme yapabilir. Sistem Hizmetleri daha önce açıklananlar gibi çekirdek kaybı durumlarına geldiğinde kümenin kullanılabilirliği tehlikeye girebilir. Bu gibi durumlarda, küme üzerinde belirli işlemler gerçekleştiremeyebilirsiniz (yükseltme başlatma veya yeni hizmetleri dağıtma gibi), ancak kümenin kendisi hala çalışıyor olabilir. 

Çalışan bir kümedeki hizmetler, çalışmaya devam etmek için sistem hizmetlerine yazma gerektirmedikleri takdirde bu koşullarda çalışmaya devam edecektir. Örneğin, Yük Devretme Yöneticisi çekirdek kaybolduysa, tüm hizmetler çalışmaya devam edecektir. Ancak, Yük Devretme Yöneticisi katılımına ihtiyaç duyduğundan, başarısız olan tüm hizmetler otomatik olarak yeniden başlatılamaz. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Veri merkezi veya Azure bölgesi sorunları
Nadir durumlarda, fiziksel bir veri merkezi güç veya ağ bağlantısı kaybından geçici olarak kullanılamaz hale gelebilir. Bu gibi durumlarda, bu veri merkezinde veya Azure bölgesindeki Service Fabric kümeleriniz ve hizmetleriniz kullanılamaz olur. Ancak _verileriniz korunur_. 

Azure 'da çalışan kümeler için, [Azure durum sayfasında][azure-status-dashboard]kesintiler üzerinde güncelleştirmeleri görüntüleyebilirsiniz. Fiziksel bir veri merkezinin kısmen veya tamamen yok edildiği çok önemli olmayan bir olayda, orada barındırılan Service Fabric kümeleri veya bunların içindeki hizmetler kaybolabilir. Bu kayıp, bu veri merkezinin veya bölgenin dışında yedeklenen tüm durumları içerir.

Tek bir veri merkezinde veya bölgede kalıcı veya sürekli başarısızlığını yerine döndürme için iki farklı strateji vardır: 

- Birden çok bölgede ayrı Service Fabric kümelerini çalıştırın ve bu ortamlar arasında yük devretme ve yeniden çalışma için bazı mekanizmayı kullanın. Birden çok küme etkin/etkin ya da etkin/Pasif modelin bu sıralaması ek yönetim ve operasyon kodu gerektirir. Bu model aynı zamanda bir veri merkezinde veya bölgede hizmetlerden, bir hata olduğunda diğer veri merkezlerinde veya bölgelerde kullanılabilir olmaları için yedeklemelerin koordine edilmesini gerektirir. 
- Birden çok veri merkezine veya bölgeye yayılan tek bir Service Fabric kümesi çalıştırın. Bu strateji için desteklenen en düşük yapılandırma üç veri merkezidir veya bölgedir. Önerilen bölge veya veri merkezi sayısı beş ' dir. 
  
  Bu model daha karmaşık bir küme topolojisi gerektirir. Ancak avantajı, bir veri merkezinde veya bölgede oluşan başarısızlığın bir olağanüstü durumdan normal bir hataya dönüştürülmesidir. Bu arızalar, tek bir bölgedeki kümeler için çalışan mekanizmalar tarafından işlenebilir. Hata etki alanları, yükseltme etki alanları ve Service Fabric yerleştirme kuralları, iş yüklerinin normal hatalara neden olacak şekilde dağıtılmasını güvence altına alırlar. 
  
  Bu tür kümede Hizmetleri çalıştırabilmek için ilkeler hakkında daha fazla bilgi için bkz. [Service Fabric Hizmetleri Için yerleştirme ilkeleri](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>Küme hatalarına neden olan rastgele sorunlar
Service Fabric *çekirdek düğümleri* kavramıdır. Bunlar, temel alınan kümenin kullanılabilirliğini sürdürdüğüm olan düğümlerdir. 

Çekirdek düğümleri, diğer düğümlerle Kiralama kurarak ve belirli başarısızlık türleri sırasında tiebreaklikler gören kümenin açık kalmasını sağlamaya yardımcı olur. Rastgele arızalar kümedeki çekirdek düğümlerin çoğunu kaldırlarsa ve hızlı bir şekilde geri getirilmezse kümeniz otomatik olarak kapanır. Küme daha sonra başarısız olur. 

Azure 'da Service Fabric kaynak sağlayıcısı Service Fabric küme yapılandırmasını yönetir. Varsayılan olarak, kaynak sağlayıcısı çekirdek düğümlerini hata ve yükseltme etki alanlarına *birincil düğüm türü* boyunca dağıtır. Birincil düğüm türü gümüş veya altın dayanıklılık olarak işaretlenmişse, bir çekirdek düğümünü kaldırdığınızda (birincil düğüm türünde ölçeklendirerek veya el ile kaldırarak), küme, birincil düğüm türünün kullanılabilir kapasitesinden başka çekirdek olmayan bir düğümü yükseltmeye çalışır. Bu deneme, küme güvenilirlik düzeyinden daha az kullanılabilir kapasiteye sahipseniz, birincil düğüm türü için gerekli olduğundan başarısız olur.

Tek başına Service Fabric kümelerinde ve Azure 'da, birincil düğüm türü, Seeds 'yi çalıştıran bir ektir. Birincil düğüm türünü tanımlarken Service Fabric, en fazla dokuz temel düğüm ve her sistem hizmeti için yedi çoğaltma oluşturarak, belirtilen düğüm sayısından otomatik olarak yararlanır. Rastgele bir başarısızlık kümesi bu çoğaltmaların büyük bir bölümünü aynı anda alırsa, sistem hizmetleri çekirdek kaybına girer. Çekirdek düğümlerin çoğunluğu kaybolmuşsa, küme yakında kapatılacak.

## <a name="next-steps"></a>Sonraki adımlar
- [Test edilebilirlik çerçevesini](service-fabric-testability-overview.md)kullanarak çeşitli hataların benzetimini yapmayı öğrenin.
- Diğer olağanüstü durum kurtarma ve yüksek kullanılabilirlik kaynaklarını okuyun. Microsoft bu konularda büyük miktarda kılavuz yayımlamıştır. Bu kaynakların bazıları diğer ürünlerde kullanılmak üzere belirli tekniklerin başvurmakla birlikte, Service Fabric bağlamına uygulayabileceğiniz birçok genel en iyi yöntem içerirler:
  - [Kullanılabilirlik denetim listesi](/azure/architecture/checklist/resiliency-per-service)
  - [Olağanüstü durum kurtarma detayına gitme](../azure-sql/database/disaster-recovery-drills.md)
  - [Azure uygulamaları için olağanüstü durum kurtarma ve yüksek kullanılabilirlik][dr-ha-guide]
- [Service Fabric destek seçenekleri](service-fabric-support.md)hakkında bilgi edinin.


<!-- External links -->

[repair-partition-ps]: /windows/win32/perfctrs/specifying-a-counter-path
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: /previous-versions/azure/dn251004(v=azure.100)


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
