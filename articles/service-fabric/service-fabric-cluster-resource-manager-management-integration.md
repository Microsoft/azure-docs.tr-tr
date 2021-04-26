---
title: Küme Kaynak Yöneticisi yönetimi tümleştirmesi
description: Küme Kaynak Yöneticisi ve Service Fabric Yönetimi arasındaki tümleştirme noktalarına genel bakış.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ae80ac5833e90164fc4ff92010fd1830ae932cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92174042"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Küme Kaynak Yöneticisi Service Fabric küme yönetimi ile tümleştirme
Service Fabric Küme Kaynak Yöneticisi Service Fabric yükseltmeleri değil, ancak buna dahil değildir. Küme Kaynak Yöneticisi, yönetim ile ilgili olarak, kümenin istenen durumunu ve içindeki hizmetleri izlemeye yardımcı olur. Küme Kaynak Yöneticisi, kümeyi istenen yapılandırmaya koyane zaman sistem durumu raporları gönderir. Örneğin, yeterli kapasite yoksa, Küme Kaynak Yöneticisi durum uyarıları ve sorunu belirten hatalar gönderilir. Diğer bir tümleştirme parçasının, yükseltmelerin nasıl çalıştığı ile ilgili olması vardır. Küme Kaynak Yöneticisi, yükseltmeler sırasında davranışını biraz daha değiştirir.  

## <a name="health-integration"></a>Sistem durumu tümleştirmesi
Küme Kaynak Yöneticisi, hizmetlerinizin yerleştirilmesi için tanımladığınız kuralları sürekli izler. Ayrıca, düğümlerde ve kümedeki her ölçüm için kalan kapasiteyi ve kümenin tamamını izler. Bu kuralları karşılayamaz veya yeterli kapasite yoksa, sistem durumu uyarıları ve hatalar yayınlanır. Örneğin, bir düğüm kapasiteden fazla olursa ve Küme Kaynak Yöneticisi Hizmetleri taşıyarak durumu gidermeye çalışır. Durumu düzeltemediği takdirde, hangi düğümün kapasite üzerinde olduğunu ve hangi ölçümlerle ilgili olduğunu belirten bir sistem durumu uyarısı yayar.

Kaynak Yöneticisi sistem durumu uyarılarından başka bir örnek, yerleştirme kısıtlamaları için ihlal örneğidir. Örneğin, bir yerleştirme kısıtlaması (gibi `“NodeColor == Blue”` ) tanımladıysanız ve Kaynak Yöneticisi bu kısıtlamayı ihlal ederse, bir sistem durumu uyarısı yayar. Bu, özel kısıtlamalar ve varsayılan kısıtlamalar (hata etki alanı ve yükseltme etki alanı kısıtlamaları gibi) için geçerlidir.

Bu tür bir sistem durumu raporuna bir örnek aşağıda verilmiştir. Bu durumda sistem durumu raporu, sistem hizmeti bölümlerinin birine yöneliktir. Sistem durumu iletisi, bu bölümün çoğaltmaların geçici olarak çok az yükseltme etki alanına paketlediği anlamına gelir.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Bu durum iletisinin bize söyledikleriniz aşağıdadır:

1. Kopyaların hepsi sağlıklı: her biri `AggregatedHealthState : Ok`
2. Yükseltme etki alanı dağıtım kısıtlaması Şu anda ihlal ediliyor. Bu, belirli bir yükseltme etki alanının bu bölümden daha fazla çoğaltma olduğu anlamına gelir.
3. Çoğaltmayı içeren düğüm, ihlalin oluşmasına neden olur. Bu durumda, *Node. 8* adlı düğüm budur
4. Şu anda bu bölüm için bir yükseltmenin olup olmadığı ("Şu anda yükseltiliyor--false")
5. Bu hizmet için dağıtım ilkesi: "dağıtım Ilkesi--paketleme". Bu, `RequireDomainDistribution` [yerleştirme ilkesine](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)tabidir. *Paketleme* bu durumda domaindistribution ' ın gerekmediği anlamına gelir, bu nedenle yerleştirme ilkesinin bu hizmet için _belirtilmediğini biliyoruz_ . 
6. Rapor gerçekleşti-8/10/2015 7:13:02 PM

Bu şekilde, bir şeyin yanlış olduğunu ve aynı zamanda hatalı yükseltmeleri tespit etmek ve durdurmak için kullanıldığını bildirmek üzere üretimde başlatılan bu güçle ilgili bilgiler vardır. Bu durumda, Kaynak Yöneticisi neden çoğaltmaları yükseltme etki alanına paketleyebileceğinizi öğrenmek istiyoruz. Genellikle paketleme geçicidir çünkü diğer yükseltme etki alanlarındaki düğümler kapanmıştır, örneğin.

Küme Kaynak Yöneticisi bazı hizmetleri yerleştirmeyi denediğizin verlim, ancak çalışan hiçbir çözüm yok. Hizmetler yerleştirilemediğiniz zaman, genellikle aşağıdaki nedenlerden biri için olur:

1. Bazı geçici bir durum, bu hizmet örneğini veya çoğaltmayı doğru bir şekilde yerleştirmeyi olanaksız hale yaptı
2. Hizmetin yerleştirme gereksinimleri unsatisfiable ' dir.

Bu durumlarda, kümeden gelen sistem durumu raporları hizmetin neden yerleştirilebileceğini belirlemenize yardımcı olur Kaynak Yöneticisi. Bu işlemi kısıtlama eliminasyon sırası olarak çağırıyoruz. Sistem sırasında, sistem hizmeti etkileyen yapılandırılmış kısıtlamalara yol gösterir ve neleri ortadan kaldırabileceklerini kaydeder. Bu şekilde, hizmetler yerleştirilemediğiniz zaman hangi düğümlerin ne olduğunu ve neden olduğunu görebilirsiniz.

## <a name="constraint-types"></a>Kısıtlama türleri
Bu durum raporlarında farklı kısıtlamaların her biri hakkında konuşalım. Çoğaltmalar yerleştirilebileceği zaman bu kısıtlamalarla ilgili durum iletilerini görürsünüz.

* **Replicaexclusionstatic** ve **replicaexclusiondynamic**: Bu kısıtlamalar, aynı bölümdeki iki hizmet nesnesinin aynı düğüme yerleştirilmesi gerektiğinden bir çözümün reddedildiğini belirtir. Bu, bu düğümün başarısızlığı bu bölümü aşırı etkilediği için buna izin verilmez. ReplicaExclusionStatic ve ReplicaExclusionDynamic neredeyse aynı kuraldır ve farklar oldukça önemlidir. ReplicaExclusionStatic veya ReplicaExclusionDynamic kısıtlaması içeren bir kısıtlama eleme sırası görüyorsanız, küme, yeterli düğüm olmadığını Kaynak Yöneticisi. Bu, izin verilmeyen bu geçersiz yerleşimi kullanmak için geri kalan çözümler gerektirir. Dizideki diğer kısıtlamalar genellikle düğümlerin neden ilk yerde ortadan kaldırıldığına ilişkin bilgi sağlayacaktır.
* **Placementconstraint**: Bu iletiyi görürseniz, hizmetin yerleştirme kısıtlamalarıyla eşleşmediği için bazı düğümleri ortadan kaldırdık. Şu anda yapılandırılmış olan yerleştirme kısıtlamalarını bu iletinin bir parçası olarak izliyoruz. Tanımlı bir yerleştirme kısıtlaması varsa, bu normaldir. Ancak, yerleştirme kısıtlaması yanlışlıkla çok fazla düğümün ortadan kaldırılmasına neden oluyorsa, bunu fark edebilirsiniz.
* **Nodecapacity**: Bu kısıtlama, küme kaynak yöneticisi çoğaltmaları, bu düğümleri kapasiteye koyacağından, belirtilen düğümlere yerleştiremediği anlamına gelir.
* **Benzeşim**: Bu kısıtlama, benzeşim kısıtlamasının ihlaline yol açacağından, çoğaltmayı etkilenen düğümlere yerleştiremedik anlamına gelir. [Bu makalede](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) benzeşim hakkında daha fazla bilgi
* **Faultdomain** ve **upgradedomain**: Bu kısıtlama, çoğaltmanın belirtilen düğümlere yerleştirilmesi belirli bir hata veya yükseltme etki alanında paketlemeye neden olacaksa düğümleri ortadan kaldırır. Bu kısıtlamayı tartışan birkaç örnek, [hata ve yükseltme etki alanı kısıtlamaları ve sonuç davranışı](service-fabric-cluster-resource-manager-cluster-description.md) konularında sunulmaktadır
* **Preferredlocation**: Normalde bu kısıtlamayı, varsayılan olarak en iyi duruma getirme olarak çalıştığı için, çözümden düğümleri kaldırarak görmemelisiniz. Tercih edilen konum kısıtlaması yükseltmeler sırasında da mevcuttur. Yükseltme sırasında, Hizmetleri yükseltmenin başlatıldığı yere geri taşımak için kullanılır.

## <a name="blocklisting-nodes"></a>Blok listeleme düğümleri
Başka bir sistem durumu iletisi Küme Kaynak Yöneticisi raporları düğümlerin blok listesinde olduğunu bildirir. Blocklist ' i sizin için otomatik olarak uygulanan geçici bir kısıtlama olarak düşünebilirsiniz. Düğümler, bu hizmet türünün örneklerini başlatırken yinelenen hatalarla karşılaştıklarında, blok alır. Düğümler, her hizmet türü temelinde listelenir. Bir düğüm, bir hizmet türü için bir blok listelenmiş ancak başka bir işlem olabilir. 

Geliştirme sırasında engelleme listesini hemen kullanmaya başlacaksınız: bazı hatalar hizmet ana bilgisayarın başlangıçta kilitlenmesine neden olur, Service Fabric hizmet konağını birkaç kez oluşturmaya çalışır ve hata devam eder. Birkaç denemeden sonra düğüm blocklistelenir ve Küme Kaynak Yöneticisi hizmeti başka bir yerde oluşturmaya çalışacaktır. Bu hata birden çok düğümde gerçekleşiyorsa, küme sonunda geçerli düğümlerin tümünün engellenmiş olması mümkündür. Blok listeleme, istenen ölçeği karşılamak için hizmeti başarıyla başlatabilecek çok sayıda düğümü de kaldırabilir. Genellikle, bir hizmetin istenen çoğaltma veya örnek sayısının altında olduğunu belirten Kaynak Yöneticisi ve hatanın ilk yerde blok listesine ne olduğunu belirten sağlık iletilerinin yanı sıra kümedeki ek hataları veya uyarıları görürsünüz.

Blok listeleme kalıcı bir durum değil. Birkaç dakika sonra, düğüm engelleme listesi öğesinden kaldırılır ve Service Fabric Bu düğümdeki hizmetleri yeniden etkinleştirebilir. Hizmetler başarısız olmaya devam ederse, düğüm bu hizmet türü için yeniden listelenir. 

### <a name="constraint-priorities"></a>Kısıtlama öncelikleri

> [!WARNING]
> Kısıtlama önceliklerini değiştirme önerilmez ve kümenizde önemli olumsuz etkileri olabilir. Aşağıdaki bilgiler, varsayılan kısıtlama önceliklerinin ve bunların davranışlarının başvurusu için verilmiştir. 
>

Bu kısıtlamaların tümünde, "Hey: hata etki alanı kısıtlamalarının sistemimde en önemli şeyler olduğunu düşündüm. Hata etki alanı kısıtlamasının ihlal olmamasını sağlamak için diğer kısıtlamaları ihlal etmek istiyorum. "

Kısıtlamalar, farklı öncelik düzeyleriyle yapılandırılabilir. Bunlar:

   - "Hard" (0)
   - "Soft" (1)
   - "iyileştirme" (2)
   - "off" (-1). 
   
Kısıtlamaların çoğu, varsayılan olarak sabit kısıtlamalar olarak yapılandırılır.

Kısıtlamaların önceliğini değiştirmek yaygın bir durumdur. Genellikle ortamı etkileyen başka bir hata veya davranışa geçici bir çözüm için, kısıtlama önceliklerinin değiştirilmesi gereken süreler vardır. Genellikle kısıtlama öncelik altyapısının esnekliği çok iyi çalıştık, ancak bu genellikle gerekli değildir. Her şeyin varsayılan önceliklerinde yer aldığı zaman. 

Öncelik düzeyleri, belirli bir kısıtlamanın ihlal edildiğini veya her zaman karşılanacağını _ifade eder._ Kısıtlama öncelikleri kısıtlamaların hangi sırayla uygulanacağını tanımlar. Öncelikler, tüm kısıtlamaları karşılamak mümkün olduğunda, avantajları tanımlar. Genellikle, ortamda başka bir şey olmadığı müddetçe tüm kısıtlamalar karşılanabilir. Kısıtlama ihlallerine yol açabilecek bazı senaryolar örnekleri, çakışan kısıtlamalardır veya çok sayıda eşzamanlı hatalardan oluşur.

Gelişmiş durumlarda kısıtlama önceliklerini değiştirebilirsiniz. Örneğin, düğüm kapasitesi sorunlarını çözmek için gerektiğinde benzeşim 'in her zaman ihlal edildiğini güvence altına almak istediğinizi varsayalım. Bunu başarmak için, benzeşim kısıtlamasının önceliğini "Soft" (1) olarak ayarlayabilir ve kapasite kısıtlamasını "Hard" (0) olarak bırakabilirsiniz.

Farklı kısıtlamalar için varsayılan öncelik değerleri aşağıdaki yapılandırmada belirtilmiştir:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

Tek başına dağıtımlar veya Azure 'da barındırılan kümeler için Template.jsClusterConfig.jsaracılığıyla:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Hata etki alanı ve yükseltme etki alanı kısıtlamaları
Küme Kaynak Yöneticisi, hizmetlerin hata ve yükseltme etki alanları arasında yayılmasını sağlamak istiyor. Bunu, Küme Kaynak Yöneticisi altyapısının içindeki bir kısıtlama olarak modeller. Nasıl kullanıldıkları ve belirli davranışları hakkında daha fazla bilgi için, [küme yapılandırmasındaki](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior)makaleye göz atın.

Yükseltme, başarısızlık veya diğer kısıtlama ihlalleriyle uğraşmak için, Küme Kaynak Yöneticisi bir yükseltme etki alanına birkaç yinelemeyi paketlendirmek gerekebilir. Hata veya yükseltme etki alanlarına göre paketleme, normal olarak yalnızca birkaç hata oluşması veya sistemde doğru yerleşimi engellemek durumunda oluşur. Bu durumlar boyunca paketlemeyi engellemek isterseniz, `RequireDomainDistribution` [yerleştirme ilkesini](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)kullanabilirsiniz. Bunun yapılması, hizmet kullanılabilirliğini ve güvenilirliği yan bir efekt olarak etkileyebileceğinden, dikkatlice düşünün.

Ortam doğru yapılandırılmışsa, yükseltmeler sırasında bile tüm kısıtlamalar tam olarak işlenir. Temel şey, Küme Kaynak Yöneticisi kısıtlamalarınız için izlenemez. İhlalin ihlal algıladığında, hemen rapor eder ve sorunu gidermeye çalışır.

## <a name="the-preferred-location-constraint"></a>Tercih edilen konum kısıtlaması
PreferredLocation kısıtlaması, iki farklı kullanım içerdiğinden biraz farklıdır. Bu kısıtlamanın bir kullanımı uygulama yükseltmeleri sırasında yapılır. Küme Kaynak Yöneticisi, yükseltmeler sırasında bu kısıtlamayı otomatik olarak yönetir. Çoğaltmaların ilk konumlarına geri döndürdüğü yükseltmelerin tamamlandığında emin olmak için kullanılır. PreferredLocation kısıtlamasının diğer kullanımı [ `PreferredPrimaryDomain` yerleştirme ilkesine](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)yöneliktir. Bunların her ikisi de iyileştirmedir ve bu nedenle PreferredLocation kısıtlaması varsayılan olarak "Iyileştirme" olarak ayarlanmış tek kısıtlamadır.

## <a name="upgrades"></a>Güncelleştirmelerini
Küme Kaynak Yöneticisi, uygulama ve küme yükseltmeleri sırasında iki işi olduğu için de yardımcı olur:

* küme kurallarının güvenliğinin aşılmadığından emin olun
* yükseltmenin sorunsuz bir şekilde çalışmasına yardımcı olmaya çalışın

### <a name="keep-enforcing-the-rules"></a>Kuralların zorlanmasına devam edin
Göz önünde bulundurulması gereken ana şey, kuralların (yerleştirme kısıtlamaları ve kapasiteleri gibi katı kısıtlamalar) yükseltmeler sırasında zorlanmaya devam etmektedir. Yerleştirme kısıtlamaları, iş yüklerinizin, yükseltmeler sırasında bile yalnızca izin verilen yerlerde çalıştırılmasını sağlar. Hizmetler yüksek kısıtlandığı zaman, yükseltmeler daha uzun sürebilir. Hizmet veya düğümü bir güncelleştirme için açıldığında, nereye gidebileceği konusunda birkaç seçenek olabilir.

### <a name="smart-replacements"></a>Akıllı değişiklikler
Bir yükseltme başladığında Kaynak Yöneticisi kümenin geçerli düzenlemenin anlık görüntüsünü alır. Her yükseltme etki alanı tamamlandığında, bu yükseltme etki alanında bulunan Hizmetleri özgün düzenleriyle döndürmeye çalışır. Bu şekilde, yükseltme sırasında bir hizmet için en fazla iki geçiş vardır. Etkilenen düğümden bir tane çıkar ve tek bir taşı geri taşınır. Yükseltme, kümenin yerleşimini etkilememesini sağlamak için küme veya hizmeti yükseltmenin önüne döndürülüyor. 

### <a name="reduced-churn"></a>Azaltılan dalgalanma
Yükseltmeler sırasında oluşan başka bir şey, kümenin Kaynak Yöneticisi dengelemeyi kapatmasından oluşur. Dengelemeyi önlemek, Hizmetleri yükseltme için boşalmış düğümlere taşımak gibi, yükseltmenin kendisi için gereksiz yeniden eylemlerin yapılmasını önler. Söz konusu yükseltme bir küme yükseltmeyse, yükseltme sırasında kümenin tamamı dengelenemez. Kısıtlama denetimleri etkin kalır, yalnızca ölçümlerin öngörülü dengelenmesini temel alan hareket devre dışıdır.

### <a name="buffered-capacity--upgrade"></a>Arabelleğe alınan kapasite & yükseltmesi
Genellikle, küme kısıtlı veya tam kapalı olsa bile yükseltmenin tamamlanmasını istersiniz. Küme kapasitesini yönetmek, her zamanki yükseltmeler sırasında daha da önemlidir. Yükseltme, küme üzerinde yer aldığı için yükseltme etki alanlarının sayısına bağlı olarak kapasitenin yüzde 5 ve %20 ' si arasında geçiş yapılmalıdır. Bu işin bir yere gitmesi gereken bir yerde. Bu, [arabelleğe alınmış kapasitelerin](service-fabric-cluster-resource-manager-cluster-description.md#node-buffer-and-overbooking-capacity) kavram kavramının yararlı olduğu yerdir. Ara belleğe alınan kapasite normal işlem sırasında kullanılır. Küme Kaynak Yöneticisi, gerekli olduğunda yükseltmeler sırasında düğümleri toplam kapasiteye (arabelleği kullanan) doldurabilir.

## <a name="next-steps"></a>Sonraki adımlar
* Baştan başlayın ve [Service Fabric kümesine giriş yapın Kaynak Yöneticisi](service-fabric-cluster-resource-manager-introduction.md)
