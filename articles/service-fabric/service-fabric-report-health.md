---
title: Özel Service Fabric sistem durumu raporları ekleme
description: Azure Service Fabric sistem durumu varlıklarına özel durum raporlarının nasıl gönderileceğini açıklar. Kalite sistem durumu raporlarının tasarlanması ve uygulanması için öneriler sağlar.
ms.topic: conceptual
ms.date: 2/28/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: c87c063ac9d3b4810657f72c46c17725b8899c77
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626178"
---
# <a name="add-custom-service-fabric-health-reports"></a>Özel Service Fabric sistem durumu raporları ekleme
Azure Service Fabric, belirli varlıklara uygun olmayan kümeyi ve uygulama koşullarını işaretlemek için tasarlanan bir [sistem durumu modeli](service-fabric-health-introduction.md) sunar. Sistem durumu modeli, sistem **durumu reporlarını** (sistem bileşenleri ve Watchdogs) kullanır. Amaç kolay ve hızlı tanılama ve onarma. Hizmet yazıcılarının sistem durumu hakkında düşünmesine ihtiyacı vardır. Özellikle, sorunların köke yakın olduğunu bayrakladığında, sistem durumunu etkileyebilecek herhangi bir koşul tarihinde bildirilmelidir. Sistem durumu bilgileri, hata ayıklama ve araştırma konusunda zaman ve çaba tasarrufu sağlayabilir. Hizmet, bulutta (özel veya Azure) bir ölçekte çalışmaya başladıktan sonra özellikle net bir şekilde belirlenir.

Service Fabric Raporlayıcıları, belirtilen ilgi koşullarını izler. Bunlar yerel görünümüne göre bu koşullara göre raporlarlar. [Sistem durumu deposu](service-fabric-health-introduction.md#health-store) , varlıkların küresel olarak sağlıklı olup olmadığını öğrenmek için tüm raporlayıcılar tarafından gönderilen sistem durumu verilerini toplar. Modelin zengin, esnek ve kullanımı kolay olması amaçlanmıştır. Sistem durumu raporlarının kalitesi, kümenin sistem durumu görünümünün doğruluğunu belirler. Hatalı olmayan sorunları yanlışlıkla gösteren yanlış pozitif sonuçlar, yükseltmeleri veya sistem durumu verilerini kullanan diğer hizmetleri olumsuz yönde etkileyebilir. Bu hizmetlere örnek olarak onarım hizmetleri ve uyarı mekanizmaları verilebilir. Bu nedenle, olası en iyi şekilde ilgilendiğiniz koşulları yakalayan raporlar sağlamak için bazı düşünce gerekir.

Sistem durumu raporlamasını tasarlamak ve uygulamak için, Watchdogs ve sistem bileşenleri şunları gerçekleştirmelidir:

* İlgilendikleri koşulu, izlenen şekli ve küme ya da uygulama işlevindeki etkiyi tanımlayın. Bu bilgilere bağlı olarak, sistem durumu raporu özelliği ve sistem durumu ' na karar verin.
* Raporun geçerli olduğu [varlığı](service-fabric-health-introduction.md#health-entities-and-hierarchy) belirleme.
* Raporlama 'nın, hizmet içinden veya bir iç veya harici bir izleme tarafından yapıldığını belirleme.
* Raporlayıcı tanımlamak için kullanılan bir kaynak tanımlayın.
* Düzenli aralıklarla veya geçişlerde bir raporlama stratejisi seçin. Önerilen yol, daha basit bir kod gerektirdiğinden ve hatalara karşı daha az maliyetli olduğundan düzenli olarak yapılır.
* Sağlıksız koşulların raporun sistem durumu deposunda ne kadar süreyle kalması gerektiğini ve nasıl temizlenmelidir. Bu bilgileri kullanarak raporun yaşam süresi ve sona erme tarihi kaldırma davranışına karar verin.

Belirtildiği gibi, raporlama şunları yapabilir:

* İzlenen Service Fabric hizmeti çoğaltması.
* Service Fabric bir hizmet olarak dağıtılan iç Watchdogs (örneğin, koşulları ve sorun raporlarını izleyen bir durum bilgisi olmayan Service Fabric hizmeti). Watchdogs tüm düğümler dağıtılabilir veya izlenen hizmete eklenebilir.
* Service Fabric düğümlerinde çalışan ancak Service Fabric Hizmetleri *olarak uygulanmayan iç* Watchdogs.
* Kaynağı Service Fabric kümenin *dışından* yoklamış dış Watchdogs (örneğin, Gomez gibi izleme hizmeti).

> [!NOTE]
> Bu, küme dışında, sistem bileşenleri tarafından gönderilen durum raporları ile doldurulur. [Sorun giderme için sistem durumu raporlarını kullanma](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)hakkında daha fazla bilgi edinin. Kullanıcı raporlarının, sistem tarafından zaten oluşturulmuş olan [sistem durumu varlıklarına](service-fabric-health-introduction.md#health-entities-and-hierarchy) gönderilmesi gerekir.
> 
> 

Sistem durumu raporlama tasarımı açık olduktan sonra, sistem durumu raporları kolayca gönderilebilir. Küme [güvenli](service-fabric-cluster-security.md) değilse veya doku istemcisinin yönetici ayrıcalıklarına sahip olması durumunda, sistem durumunu raporlamak Için [FabricClient](/dotnet/api/system.fabric.fabricclient) 'ı kullanabilirsiniz. Raporlama, [FabricClient. HealthManager. Reporthegizlilik](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), POWERSHELL veya REST aracılığıyla API aracılığıyla yapılabilir. Yapılandırma, daha iyi performans için toplu iş raporlarını dönüştürür.

> [!NOTE]
> Rapor durumu zaman uyumludur ve yalnızca istemci tarafında doğrulama işini temsil eder. Raporun sistem durumu istemcisi tarafından kabul edildiği veya `Partition` ya da `CodePackageActivationContext` nesnelerin mağazaya uygulandığı anlamına gelir. Zaman uyumsuz olarak gönderilir ve büyük olasılıkla diğer raporlarla toplanmış olur. Sunucu üzerindeki işleme yine de başarısız olabilir: sıra numarası eskimiş, raporun uygulanması gereken varlık silinmiş, vb.
> 
> 

## <a name="health-client"></a>Sistem durumu istemcisi
Sistem durumu raporları, yapı istemcisinin içinde yer aldığı bir sistem durumu istemcisi aracılığıyla sistem durumu yöneticisine gönderilir. Health Manager, raporları sistem durumu deposuna kaydeder. Sistem durumu istemcisi aşağıdaki ayarlarla yapılandırılabilir:

* **Healthreportsendınterval**: raporun istemciye eklendiği zaman ve sistem durumu yöneticisine gönderildiği zaman arasındaki gecikme. Her rapor için bir ileti göndermek yerine tek bir ileti halinde raporları toplu olarak kullanır. Toplu işlem performansı geliştirir. Varsayılan: 30 saniye.
* **Healthreportretrysendınterval**: sistem durumu istemcisinin, birikmiş sistem durumu raporlarını sistem durumu yöneticisine yeniden sonlandıran Aralık. Varsayılan: 30 saniye, en az: 1 saniye.
* **HealthOperationTimeout**: sistem durumu yöneticisine gönderilen rapor iletisi için zaman aşımı süresi. Bir ileti zaman aşımına uğrarsa, sistem durumu Yöneticisi raporun işlendiğini onaylaana kadar sistem durumu istemcisi yeniden dener. Varsayılan: iki dakika.

> [!NOTE]
> Raporlar toplu olduğunda, gönderildiğinden emin olmak için yapı istemcisinin en az Healthreportsendınterval için etkin tutulması gerekir. İleti kaybolursa veya sistem durumu Yöneticisi geçici hatalar nedeniyle bunları uygulayamazsa, doku istemcisinin yeniden denenme şansı vermesi için daha uzun süre canlı tutulması gerekir.
> 
> 

İstemcideki arabelleğe alma, raporların benzersizlik düzeyini dikkate alır. Örneğin, belirli bir hatalı Raporlayıcı, aynı varlığın aynı özelliğinde saniyede 100 rapor bildiriyorsa, raporların son sürümü ile değiştirilmiştir. İstemci kuyruğunda bu tür bir rapor var. Toplu işlem yapılandırılırsa, durum yöneticisine gönderilen raporların sayısı, gönderme aralığı başına yalnızca bir tane olur. Bu rapor, varlığın en güncel durumunu yansıtan son eklenen rapordur.
Bir `FabricClient` sistem sağlığı ile ilgili girişler için istenen değerlerle [Fabricclientsettings](/dotnet/api/system.fabric.fabricclientsettings) geçirerek oluşturulduğunda yapılandırma parametrelerini belirtin.

Aşağıdaki örnek bir yapı istemcisi oluşturur ve raporların eklendiklerinde gönderilmesi gerektiğini belirtir. Yeniden denenebilecek zaman aşımları ve hatalarda, yeniden denemeler her 40 saniyede gerçekleşir.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

30 saniyeye ayarlanmış varsayılan yapı istemci ayarlarını tutmanız önerilir `HealthReportSendInterval` . Bu ayar, toplu işlem nedeniyle en iyi performansı sağlar. En kısa sürede gönderilmesi gereken kritik raporlar için `HealthReportSendOptions` `true` [FabricClient. Healthclient. reporthegizli](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API ' de anında kullanın. Anında raporlar toplu işlem aralığını atlar. Bu bayrağı dikkatli kullanın; mümkün olan her durumda sistem durumu istemci toplu işlem avantajlarından faydalanmak istiyoruz. Anında gönderme, yapı istemcisi kapatılırken da yararlıdır (örneğin, işlem geçersiz durumu tespit etti ve yan etkileri engellemek için kapanması gerekir). Birikmiş raporların en iyi şekilde gönderilmesini sağlar. Anında işaretle bir rapor eklendiğinde, sistem durumu istemcisi son gönderimden bu yana tüm birikmiş raporları işler.

PowerShell aracılığıyla bir kümeyle bağlantı oluşturulduğunda aynı parametreler belirtilebilir. Aşağıdaki örnek, yerel bir kümeye bir bağlantı başlatır:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

API 'ye benzer şekilde, raporlar, `-Immediate` değerden bağımsız olarak hemen gönderilmek üzere anahtar kullanılarak gönderilebilir `HealthReportSendInterval` .

REST için raporlar, dahili bir doku istemcisine sahip olan Service Fabric ağ geçidine gönderilir. Varsayılan olarak, bu istemci, her 30 saniyede bir toplu rapor gönderecek şekilde yapılandırılmıştır. Toplu iş aralığını küme yapılandırma ayarı ile değiştirebilirsiniz `HttpGatewayHealthReportSendInterval` `HttpGateway` . Belirtildiği gibi, raporların doğru bir şekilde gönderilmesi daha iyi bir seçenektir `Immediate` . 

> [!NOTE]
> Yetkisiz hizmetlerin kümedeki varlıklara karşı sistem durumunu bildirememesini sağlamak için, sunucuyu yalnızca güvenli istemcilerden gelen istekleri kabul edecek şekilde yapılandırın. `FabricClient`Raporlama için kullanılan, kümeyle iletişim kurabilmesi için güvenliğin etkinleştirilmiş olması gerekir (örneğin, Kerberos veya sertifika kimlik doğrulaması ile). [Küme güvenliği](service-fabric-cluster-security.md)hakkında daha fazla bilgi edinin.
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Düşük ayrıcalıklı hizmetler içinden rapor
Service Fabric hizmetlerin kümeye yönetici erişimi yoksa, geçerli bağlamdaki varlıklarda sistem durumunu veya ile rapor edebilirsiniz `Partition` `CodePackageActivationContext` .

* Durum bilgisi olmayan hizmetler için, geçerli hizmet örneği hakkında raporlamak üzere [Istatelessservicepartition. Reportınstancehealth](/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) komutunu kullanın.
* Durum bilgisi olan hizmetler için, geçerli çoğaltma hakkında raporlamak üzere [Istatefulservicepartition. ReportReplicaHealth](/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) komutunu kullanın.
* Geçerli bölüm varlığını raporlamak için [IServiceProvider. ReportPartitionHealth](/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) kullanın.
* Geçerli uygulamayı raporlamak için [Codepackageactivationcontext. ReportApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) kullanın.
* Geçerli düğümde dağıtılan geçerli uygulamayı raporlamak için [Codepackageactivationcontext. Reportdeployedadpplicationhealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) komutunu kullanın.
* Geçerli düğümde dağıtılan uygulamanın hizmet paketini raporlamak için [Codepackageactivationcontext. ReportDeployedServicePackageHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) komutunu kullanın.

> [!NOTE]
> Dahili olarak, `Partition` ve `CodePackageActivationContext` bir sistem durumu istemcisini varsayılan ayarlarla yapılandırılmış olarak tutar. [Sistem sağlığı istemcisi](service-fabric-report-health.md#health-client)için açıklandığı gibi, raporlar toplu olarak oluşturulur ve bir zamanlayıcıya gönderilir. Rapor gönderme şansı sağlamak için nesnelerin etkin tutulması gerekir.
> 
> 

`HealthReportSendOptions` `Partition` Ve `CodePackageActivationContext` sistem sağlığı API 'leri aracılığıyla rapor gönderirken belirtebilirsiniz. En kısa sürede gönderilmesi gereken kritik raporlarınız varsa, `HealthReportSendOptions` anında kullanın `true` . Anında raporlar iç sistem durumu istemcisinin toplu işlem aralığını atlar. Daha önce bahsedildiği gibi, bu bayrağı dikkatli kullanın; mümkün olan her durumda sistem durumu istemci toplu işlem avantajlarından faydalanmak istiyoruz.

## <a name="design-health-reporting"></a>Tasarım sistem durumu raporlaması
Yüksek kaliteli raporlar oluşturmanın ilk adımı, hizmetin sistem durumunu etkileyebilecek koşulları belirlemektir. Bir sorun yapılmadan önce, hizmet veya kümedeki sorunları bayrağa ya da daha iyi bir şekilde, bir sorun gerçekleşmenizden önce (büyük olasılıkla milyarlarca dolar tasarruf edebilir) yardım eden herhangi bir koşul. Avantajlar, daha az gece süresi, sorunları araştırmaya ve onarmaya harcanan daha az gece saati ve müşteri memnuniyetini daha yüksektir.

Koşullar tanımlandıktan sonra, izleme yazarları, bunları ek yük ve kullanışlılığı arasında dengelemek üzere izlemenin en iyi yolunu belirlemelidirler. Örneğin, bir paylaşımda bazı geçici dosyaları kullanan karmaşık hesaplamalar yapan bir hizmeti göz önünde bulundurun. Bir izleme, yeterli alanın kullanılabilir olduğundan emin olmak için paylaşımdan izleme sağlayabilir. Dosya veya dizin değişikliklerinin bildirimlerini dinleyebilir. Ön eşiğe ulaşıldığında bir uyarı bildirebilir ve paylaşma doluysa bir hata bildirir. Bir uyarı durumunda, bir onarım sistemi paylaşımdaki eski dosyaları temizlemeye başlayabilir. Bir hata durumunda, bir onarım sistemi, hizmet çoğaltmasını başka bir düğüme taşıyabilir. Durum durumu açısından koşul durumlarının nasıl açıklandığına dikkat edin: sağlıklı (Tamam) veya sağlıksız (uyarı veya hata) olarak kabul edilebilir durum durumu.

İzleme ayrıntıları ayarlandıktan sonra, bir izleme yazıcısının izizlemenin nasıl uygulanacağını öğrenilmesi gerekir. Koşullar hizmetin içinden belirlenebileceği takdirde, izleme izlenen hizmetin bir parçası olabilir. Örneğin, hizmet kodu, paylaşma kullanımını denetleyebilir ve sonra bir dosya yazmayı her denediğinde rapor verebilir. Bu yaklaşımın avantajı, raporlama 'nın basittir. İzleme hatalarının hizmet işlevselliğini etkileyerek engel olmak için dikkatli olunmalıdır.

İzlenen hizmet içinden raporlama her zaman bir seçenek değildir. Hizmet içindeki bir izleme, koşulları algılayamayabilir. Belirleme yapmak için mantık veya veri olmayabilir. Koşulları izlemenin ek yükü yüksek olabilir. Koşullar da bir hizmete özgü olmayabilir, bunun yerine hizmetler arasındaki etkileşimleri etkiler. Başka bir seçenek de kümede ayrı süreçler olarak Watchdogs. Watchdogs, koşulları ve raporu, ana Hizmetleri herhangi bir şekilde etkilemeden izler. Örneğin, bu Watchdogs, tüm düğümlere veya hizmetle aynı düğümlerde dağıtılan aynı uygulamada durum bilgisi olmayan hizmetler olarak uygulanabilir.

Bazen, kümede çalışan bir izleme, herhangi bir seçenek değildir. İzlenen koşul, kullanıcılar tarafından görülediğinde hizmetin kullanılabilirliğine veya işlevselliğine sahip ise, Watchdogs Kullanıcı istemcileriyle aynı yerde olması en iyisidir. Burada, kullanıcılar onları çağıran şekilde işlemleri test edebilirler. Örneğin, kümenin dışında bulunan bir izleme, hizmete yönelik istekler ve sonucun gecikme süresini ve doğruluğunu kontrol edebilirsiniz. (Örneğin, bir Hesaplayıcı hizmeti için 2 + 2, makul bir süre içinde 4 döndürür mi?)

İzleme ayrıntıları sonlandırıldıktan sonra, onu benzersiz bir şekilde tanımlayan bir kaynak KIMLIĞIYLE karar vermeniz gerekir. Aynı türde birden çok Watchdogs kümede yaşayan, farklı varlıklara rapor olmaları gerekir, aksi takdirde aynı varlığa raporlarsa farklı kaynak KIMLIĞI veya özellik kullanın. Bu şekilde, raporları bir arada bulunabilir. Sistem durumu raporunun özelliği izlenen koşulu yakalemelidir. (Yukarıdaki örnek için, özelliği bir **parça esize** olabilir.) Aynı koşula birden çok rapor uygulanacaksa, özelliği raporların birlikte kullanılmasına izin veren bazı dinamik bilgileri içermelidir. Örneğin, birden çok paylaşım izleniyorsa, özellik adı, **Sharesize-ShareName** olabilir.

> [!NOTE]
> Durum bilgilerini tutmak için sistem durumu *deposunu kullanmayın.* Bu bilgiler bir varlığın sistem durumu değerlendirmesini etkilediği için yalnızca sağlık ile ilgili bilgilerin sistem durumu olarak bildirilmesi gerekir. Sistem durumu deposu, genel amaçlı bir depo olarak tasarlanmamıştır. Tüm verileri sistem durumuna toplamak için sistem durumu değerlendirme mantığını kullanır. Sağlık durumu ile ilgisi olmayan bilgilerin gönderilmesi (sistem durumu Tamam olan raporlama durumu gibi), toplanmış sistem durumunu etkilemez, ancak sistem durumu deposunun performansını olumsuz yönde etkileyebilir.
> 
> 

Sonraki karar noktası, rapor edilecek tüzel kişilidir. Çoğu zaman koşulun varlığı açıkça tanımlar. En iyi ayrıntı düzeyi olan varlığı seçin. Bir koşul bir bölümdeki tüm çoğaltmaları etkirse, hizmette değil, bölüm üzerinde rapor edin. Ancak, daha fazla düşünce olması gereken köşe durumları vardır. Koşul, bir çoğaltma gibi bir varlığı etkilediğinde, ancak bu koşul, çoğaltma ömrü süresinden daha uzun bir süre için işaretlenmek isterse, bölüm üzerinde bildirilmesi gerekir. Aksi takdirde, çoğaltma silindiğinde, sistem durumu deposu tüm raporlarını temizler. İzleme yazarları, varlığın ve raporun yaşam sürelerini düşünmelidir. Bir rapor bir mağazadan temizlenmelidir (örneğin, bir varlıkta bildirilen bir hata, artık geçerli olmadığında), bu açık olmalıdır.

Açıklandığım noktaları birlikte yerleştiren bir örneğe bakalım. Ana durum bilgisiz kalıcı hizmetinden oluşan bir Service Fabric uygulamayı ve tüm düğümlerde dağıtılan ikincil durum bilgisi olmayan hizmetleri (her görev türü için bir ikincil hizmet türü) ele alalım. Ana öğe, ikincgöre yürütülecek komutları içeren bir işleme kuyruğuna sahiptir. İkincil öğeler gelen istekleri yürütür ve geri bildirim sinyalleri gönderir. İzlenecek bir durum, ana işleme sırasının uzunluğudur. Ana sıra uzunluğu bir eşiğe ulaşırsa, bir uyarı bildirilir. Uyarı, ikinc'nin yükü işleyemediğini belirtir. Sıra, en fazla uzunluğa ulaşırsa ve komutlar atılıyorsa, hizmet kurtarılamadığında bir hata bildirilir. Raporlar **QueueStatus** özelliği üzerinde olabilir. İzleme hizmeti içinde yaşar ve ana birincil çoğaltmada düzenli olarak gönderilir. Yaşam süresi iki dakikadır ve her 30 saniyede bir düzenli olarak gönderilir. Birincil değer kapalıysa, rapor mağazadan otomatik olarak temizlenir. Hizmet çoğaltması çalışır durumda, ancak kilitlenmişse veya başka sorunlar varsa, raporun sistem durumu deposunda süresi dolar. Bu durumda, varlık hata durumunda değerlendirilir.

İzlenebilecek başka bir koşul görev yürütme zamanı ' dır. Ana, görevleri görev türüne göre ikincil öğeler 'e dağıtır. Tasarıma bağlı olarak, ana öğe, görev durumunun ikincil öğesini yoklayayacak. Ayrıca, ikincillerin tamamlandığında bildirim sinyalleri geri göndermesini de bekleyebilir. İkinci durumda, ikincil zar veya mesajların kaybedildiği durumları tespit etmek için dikkatli olunması gerekir. Bir seçenek, ana öğenin durumunu geri gönderen aynı ikincil sunucuya bir ping isteği göndermesi için kullanılır. Durum alınmadığında, ana öğe bir hata olduğunu varsayar ve görevi müşteri sizinle randevusunu. Bu davranış, görevlerin ıdempotent olduğunu varsayar.

Görev belirli bir **zamanda (örneğin**, 10 dakika) yapılmadığından izlenen koşul bir uyarı olarak çevrilebilir. Görev zamanında tamamlanmazsa (örneğin, 20 dakika)**, izlenen** koşul hata olarak çevrilebilir. Bu raporlama, birden çok şekilde yapılabilir:

* Ana birincil çoğaltma, düzenli aralıklarla düzenli olarak raporlar. Kuyruktaki tüm bekleyen görevler için bir özelliğe sahip olabilirsiniz. En az bir görev daha uzun sürerse, **Pendingtasks** özelliğindeki rapor durumu, uygun şekilde bir uyarı veya hatadır. Bekleyen görev yoksa veya tüm görevler yürütmeyi başlatmışsa, raporun durumu Tamam ' dır. Görevler kalıcıdır. Birincil ise, yeni yükseltilen birincil, doğru rapor almaya devam edebilir.
* Başka bir izleme işlemi (bulutta veya dış), görevlerin tamamlanıp tamamlanmadığını görmek için görevleri (istenen görev sonucuna göre dışında) denetler. Eşiklere uymadığı durumlarda ana hizmette bir rapor gönderilir. Ayrıca, **pendingtask + TaskID** gibi görev tanımlayıcısını içeren her bir görevde bir rapor gönderilir. Raporların yalnızca sağlıksız durumlar üzerinde gönderilmesi gerekir. Birkaç dakika içinde yaşam süresi belirleyin ve Temizleme işleminin süresi dolduğunda kaldırılacak raporları işaretleyin.
* Bir görevi çalıştırmak beklenenden uzun sürerse, bir görev raporu yürüten ikincil değer. Bu, **Pendingtasks** özelliğindeki hizmet örneği hakkında rapor bildiriyor. Rapor, sorun olan hizmet örneğini işaret eder, ancak örneğin bu durum, örneğin olduğu durumu yakalamaz. Raporlar daha sonra temizlenir. İkincil hizmette rapor verebilir. İkincil, görevi tamamlarsa, ikincil örnek raporu mağazadan temizler. Rapor, onay iletisinin kaybolduğu ve görevin ana görünümün bir noktasından bitmediği durumu yakalamaz.

Ancak raporlama yukarıda açıklanan durumlarda yapılır, sistem durumu değerlendirildiğinde raporlar uygulama durumu 'nda yakalanır.

## <a name="report-periodically-vs-on-transition"></a>Düzenli aralıklarla rapor geçişe karşı
Watchdogs, sistem durumu raporlama modelini kullanarak raporları düzenli aralıklarla veya geçişlerde gönderebilir. Kod çok daha basit ve hatalara karşı daha basit olduğundan, izleme raporlaması için önerilen yol düzenli aralıklarla yapılır. Yanlış raporları tetikleyen hataların oluşmaması için Watchdogs ve mümkün olduğunca basit olması gerekir. Kötü *sağlıksız* raporlar, yükseltmeler dahil olmak üzere sistem durumuna göre sistem durumu değerlendirmesini ve senaryolarını etkiler. Hatalı *sağlıklı* raporlar, kümedeki sorunları gizler, bu istenen değildir.

Düzenli raporlama için, izleme bir zamanlayıcı ile uygulanabilir. Bir zamanlayıcı geri aramasında, izleme durumu denetleyebilir ve geçerli duruma göre rapor gönderebilir. Daha önce hangi raporun gönderildiğini görmeniz veya mesajlaşma açısından herhangi bir iyileştirme yapmanız gerekmez. Sistem durumu istemcisinde performans sağlamaya yardımcı olmak için toplu işlem mantığı vardır. Sistem durumu istemcisi etkin tutulurken, rapor sistem durumu deposu tarafından onaylanana veya izleme aynı varlık, özellik ve kaynak ile daha yeni bir rapor üretene kadar dahili olarak yeniden dener.

Geçişler üzerinde raporlama, durumun dikkatli işlenmesini gerektirir. İzleme, bazı koşulları ve raporları yalnızca koşullar değiştiğinde izler. Bu yaklaşımın üstü, daha az raporun gerekli olduğu bir yaklaşımdır. Bulunacağından, izleme mantığının karmaşık olması. İzleme, durum değişikliklerini tespit etmek üzere düzenlenebilmeleri için koşulları veya raporları korumalıdır. Yük devretme sırasında, uygunluk, ancak henüz sistem durumu deposuna gönderilmemiş raporlarla alınmalıdır. Sıra numarası, her zaman arttırılmalıdır. Aksi takdirde, raporlar eski olarak reddedilir. Veri kaybının gerçekleştiği nadir durumlarda, Raporlayıcı durumu ve sistem durumu deposunun durumu arasında eşitleme gerekebilir.

Geçişlerde raporlama, veya üzerinden kendi başlarına raporlama için anlamlı hale `Partition` gelir `CodePackageActivationContext` . Yerel nesne (çoğaltma veya dağıtılan hizmet paketi/dağıtılan uygulama) kaldırıldığında, tüm raporları da kaldırılır. Bu otomatik temizleme, Raporlayıcı ve sistem durumu deposu arasında eşitleme gereksinimini ortadan temizler. Rapor üst bölüm veya üst uygulama için ise, sistem durumu deposundaki eski raporların oluşmasını önlemek için yük devretmede dikkatli olunmalıdır. Doğru durumu korumak için Logic eklenmelidir ve artık gerekli olmadığında raporun depolamadan işaretini kaldırın.

## <a name="implement-health-reporting"></a>Sistem durumu raporlamasını Uygula
Varlık ve rapor ayrıntıları net olduktan sonra, sistem durumu raporlarının gönderilmesi API, PowerShell veya REST aracılığıyla yapılabilir.

### <a name="api"></a>API
API aracılığıyla raporlamak için, raporlamak istedikleri varlık türüne özgü bir sistem durumu raporu oluşturmanız gerekir. Bir sistem durumu istemcisine rapor verin. Alternatif olarak, bir sistem durumu bilgisi oluşturup `Partition` `CodePackageActivationContext` geçerli varlıkları raporlamak için veya üzerinde doğru raporlama yöntemlerine geçiş yapın.

Aşağıdaki örnek, küme içindeki bir izleme 'den düzenli olarak raporlamayı gösterir. İzleme, bir dış kaynağa bir düğüm içinden erişilebilir olup olmadığını denetler. Kaynak, uygulama içindeki bir hizmet bildirimi için gereklidir. Kaynak kullanılamıyorsa, uygulama içindeki diğer hizmetler yine de düzgün şekilde çalışabilir. Bu nedenle rapor, dağıtılan hizmet paketi varlığında her 30 saniyede bir gönderilir.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
**Send-ServiceFabric *EntityType* HealthReport** ile sistem durumu raporları gönderin.

Aşağıdaki örnek, bir düğümdeki CPU değerleri üzerinde düzenli raporlamayı gösterir. Raporların her 30 saniyede gönderilmesi ve iki dakikalık yaşam süresi vardır. Süreleri dolarsa, Raporlayıcı sorunlara sahiptir, bu nedenle düğüm hata durumunda değerlendirilir. CPU bir eşiğin üstünde olduğunda, raporun sistem durumu uyarısı olur. CPU, yapılandırılan süreden daha fazla süre boyunca bir eşiğin üzerinde kaldığında hata olarak bildirilir. Aksi takdirde, Raporlayıcı Tamam bir sistem durumu gönderir.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

Aşağıdaki örnek bir çoğaltmada geçici bir uyarı bildirir. Önce bölüm KIMLIĞINI ve ardından ilgilendiğiniz hizmetin çoğaltma KIMLIĞINI alır. Ardından, bir **Powershellizleyici** ' den, **resourcedependency**' den bir rapor gönderir. Rapor yalnızca iki dakika boyunca ilgilenir ve mağazadan otomatik olarak kaldırılır.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
İstenen varlığa gitmeyen POST istekleri ile REST kullanarak sistem durumu raporları gönderin ve sistem durumu raporu açıklamasını gövdesinde olması gerekir. Örneğin, bkz. REST [kümesi durum raporları](/rest/api/servicefabric/report-the-health-of-a-cluster) veya [hizmet durumu raporları](/rest/api/servicefabric/report-the-health-of-a-service)gönderme. Tüm varlıklar desteklenir.

## <a name="next-steps"></a>Sonraki adımlar
Sistem durumu verilerine bağlı olarak, hizmet yazarları ve küme/uygulama yöneticileri bu bilgileri kullanmanın yollarını düşünebilir. Örneğin, sistem kesintilerini gerçekleştirmeden önce ciddi sorunları yakalamak için sistem durumuna göre uyarıları ayarlayabilir. Yöneticiler, sorunları otomatik olarak çözmek için onarım sistemlerini de ayarlayabilir.

[Service Fabric sistem durumu Izlemeye giriş](service-fabric-health-introduction.md)

[Service Fabric sistem durumu raporlarını görüntüleme](service-fabric-view-entities-aggregated-health.md)

[Hizmet durumunu raporlama ve denetleme](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Sorun giderme için sistem durumu raporlarını kullanma](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Hizmetleri yerel olarak izleme ve tanılama](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uygulama yükseltmesini Service Fabric](service-fabric-application-upgrade.md)
