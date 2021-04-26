---
title: 'Test Edilebilirlik: hizmet iletişimi'
description: Hizmetten hizmete iletişim, Service Fabric bir uygulamanın kritik bir tümleştirme noktasıdır. Bu makalede tasarım konuları ve test teknikleri açıklanmaktadır.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: fbf889422d69d031d777acd80cf3becfb3184568
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575933"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Service Fabric test edilebilir senaryolar: hizmet iletişimi
Azure Service Fabric 'de doğal olarak mikro hizmetler ve hizmet odaklı mimari stilleri yüzeyi. Bu dağıtılmış mimarilerde, bileşen, mikro hizmet uygulamaları genellikle birbirleriyle konuşmak zorunda olan birden çok hizmetten oluşur. En basit durumlar da genellikle en az bir durum bilgisiz Web hizmetiniz ve iletişim kurması gereken durum bilgisi olan bir veri depolama hizmetidir.

Hizmetten hizmete iletişim, her hizmet diğer hizmetlere uzak bir API kullanıma sunduğundan, uygulamanın kritik bir tümleştirme noktasıdır. G/ç içeren bir API sınırları kümesiyle çalışmak, iyi bir test ve doğrulama miktarı ile bazı önemli işlemleri gerektirir.

Bu hizmet sınırlarının dağıtılmış bir sistemde birbirine kablolu olması durumunda yapmanız gereken birçok önemli noktalar vardır:

* *Aktarım Protokolü*. Artan birlikte çalışabilirlik için HTTP veya en yüksek aktarım hızı için özel bir ikili protokol kullanacaksınız misiniz?
* *Hata işleme*. Kalıcı ve geçici hatalar nasıl ele alınacaktır? Bir hizmet farklı bir düğüme taşınırsa ne olur?
* *Zaman aşımları ve gecikme süresi*. Çoklu katmanlı uygulamalarda, her bir hizmet katmanı yığın ve kullanıcıya gecikme süresini nasıl işler?

Service Fabric tarafından sunulan yerleşik hizmet iletişim bileşenlerinden birini kullanıp kullanmayacağınızı veya kendi kendinize inşa etmeksizin, hizmetleriniz arasındaki etkileşimleri test etmeniz, uygulamanızda dayanıklılık sağlamak açısından önemlidir.

## <a name="prepare-for-services-to-move"></a>Hizmetlerin taşınmasına hazırlanma
Hizmet örnekleri zaman içinde hareket edebilir. Bu durum özellikle, özel olarak tasarlanmış en iyi kaynak Dengeleme için yük ölçümleriyle yapılandırıldıklarında geçerlidir. Service Fabric, hizmet örneklerinizi yükseltmeler, yük devretme, genişleme ve dağıtılmış bir sistemin kullanım ömrü boyunca oluşan diğer durumlar gibi kullanılabilirliği en üst düzeye çıkarmak üzere taşıyın.

Hizmetler kümede bir gezindiğinden, istemcileriniz ve diğer hizmetleriniz bir hizmetle iletişim kurduklarında iki senaryoyu işlemeye hazır olmalıdır:

* Hizmet örneği veya bölüm çoğaltması, son olarak bu yana taşındığınız zamandan beri taşındı. Bu, hizmet yaşam döngüsünün normal bir parçasıdır ve uygulamanızın ömrü boyunca gerçekleşmesi beklenmelidir.
* Hizmet örneği veya bölüm çoğaltması taşıma sürecinde. Bir hizmetin bir düğümden diğerine yük devretmesi Service Fabric çok hızlı bir şekilde gerçekleşse de, hizmetinizin iletişim bileşeninin başlaması yavaşsa bir gecikme olabilir.

Düzgün çalışan bir sistem için bu senaryoları sorunsuz bir şekilde işlemek önemlidir. Bunu yapmak için aşağıdakileri göz önünde bulundurun:

* Bağlanılan her hizmet, dinlediği bir *adrese* sahiptir (ÖRNEĞIN, http veya WebSockets). Bir hizmet örneği veya bölümü taşındığınızda, adres uç noktası değişir. (Farklı bir IP adresi olan farklı bir düğüme gider.) Yerleşik iletişim bileşenleri kullanıyorsanız, hizmet adreslerini sizin için yeniden çözümlemeyi işleyeceğiz.
* Hizmet örneği dinleyiciyi yeniden başlattığı için hizmet gecikmesi durumunda geçici bir artış olabilir. Bu, hizmet örneği taşındıktan sonra hizmetin dinleyiciyi ne kadar hızlı açtığı üzerine bağlıdır.
* Yeni bir düğümde hizmet açıldıktan sonra, mevcut tüm bağlantıların kapatılıp yeniden açılması gerekir. Düzgün bir şekilde kapatma veya yeniden başlatma, mevcut bağlantıların düzgün şekilde kapatılmasını sağlar.

### <a name="test-it-move-service-instances"></a>Test et: hizmet örneklerini taşıma
Service Fabric yük kararlılığı araçlarını kullanarak, bu durumları farklı yollarla test etmek için bir test senaryosu yazabilirsiniz:

1. Durum bilgisi olan bir hizmetin birincil çoğaltmasını taşıyın.
   
    Durum bilgisi olan bir hizmet bölümünün birincil çoğaltması herhangi bir sayıda nedenden dolayı taşınabilir. Hizmetlerinizin, çok denetimli bir biçimde taşımaya nasıl tepki vermesini görmek için, belirli bir bölümün birincil çoğaltmasını hedeflemek üzere bunu kullanın.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Bir düğümü durdurun.
   
    Bir düğüm durdurulduğunda Service Fabric, bu düğümdeki tüm hizmet örneklerinin veya bölümlerinin kümedeki diğer kullanılabilir düğümlerden birine taşınması. Bu işlemi, bir düğümün kümenizdeki kaybedildiği ve bu düğümdeki tüm hizmet örneklerinin ve çoğaltmaların taşınması gereken bir durumu sınamak için kullanın.
   
    PowerShell **stop-ServiceFabricNode** cmdlet 'ini kullanarak bir düğümü durdurabilirsiniz:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Hizmet kullanılabilirliğini sürdürme
Bir platform olarak Service Fabric, hizmetlerinizin yüksek kullanılabilirlik sağlamak için tasarlanmıştır. Ancak olağanüstü durumlarda, temel alınan altyapı sorunları yine de kullanılamamasına neden olabilir. Bu senaryolar için de test edilmesi önemlidir.

Durum bilgisi olan hizmetler, yüksek kullanılabilirlik için durumu çoğaltmak üzere çekirdek tabanlı bir sistem kullanır. Bu, bir çoğaltma çekirdeğinin yazma işlemleri gerçekleştirmek için kullanılabilir olması gerektiği anlamına gelir. Yaygın olarak karşılaşılan bir donanım arızası gibi nadir durumlarda, çoğaltmaların bir çekirdeği kullanılamayabilir. Bu durumlarda, yazma işlemleri gerçekleştiremeyeceksiniz, ancak yine de okuma işlemleri yapabileceksiniz.

### <a name="test-it-write-operation-unavailability"></a>Test et: yazma işlemi kullanılamaz
Service Fabric ' de, yük kararlılığı araçları 'nı kullanarak, çekirdek kaybını test olarak karşılayan bir hata ekleyebilirsiniz. Böyle bir senaryo nadir olsa da, durum bilgisi olan bir hizmete bağlı olan istemci ve hizmetlerin, bunlara yazma istekleri yapamadığı durumları işlemek için hazır olması önemlidir. Ayrıca, durum bilgisi olan hizmetin kendisi bu olasılığa karşı farkında ve arayanlara sorunsuz bir şekilde iletişim kurabildiğinden de önemlidir.

PowerShell **Invoke-Servicefabricpartitionquorumkaybetme** cmdlet 'ini kullanarak çekirdek kaybını sağlayabilirsiniz:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Bu örnekte, `QuorumLossMode` `QuorumReplicas` tüm çoğaltmaları kapatmadan önce çekirdek kaybına ulaşılmasını istediğinizi belirtmek için olarak ayarlandık. Bu şekilde, okuma işlemleri yine de mümkündür. Bölümün tamamının kullanılamadığı bir senaryoyu test etmek için, bu anahtarı olarak ayarlayabilirsiniz `AllReplicas` .

## <a name="next-steps"></a>Sonraki adımlar
[Test edilebilirlik eylemleri hakkında daha fazla bilgi edinin](service-fabric-testability-actions.md)

[Test edilebilir senaryolar hakkında daha fazla bilgi edinin](service-fabric-testability-scenarios.md)

