---
title: Azure Service Fabric olay listesi
description: Kümeleri izlemeye yardımcı olmak için Azure Service Fabric tarafından sağlanan olayların kapsamlı listesi.
ms.topic: reference
ms.date: 2/25/2019
ms.openlocfilehash: a1ba21aaac831abb07544944420f2b77e1753955
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279791"
---
# <a name="list-of-service-fabric-events"></a>Service Fabric olaylarının listesi 

Service Fabric, kümenizin durumunu [Service Fabric olaylar](service-fabric-diagnostics-events.md)olarak bildirmek için birincil küme olayları kümesini kullanıma sunar. Bunlar, düğümleriniz ve küme sahibi/operatörü tarafından yapılan kümeniz veya yönetim kararlarında Service Fabric tarafından gerçekleştirilen eylemlere dayalıdır. Bu olaylara, [Azure izleyici günlüklerini kümemenizle](service-fabric-diagnostics-oms-setup.md)yapılandırma veya [eventstore](service-fabric-diagnostics-eventstore.md)'u sorgulama gibi çeşitli yollarla yapılandırarak erişilebilir. Windows makinelerde, bu olaylar olay günlüğüne aktarılır. böylece Olay Görüntüleyicisi Service Fabric olayları görebilirsiniz. 

Bu olayların bazı özellikleri şunlardır
* Her olay, kümedeki belirli bir varlığa (örn. uygulama, hizmet, düğüm, çoğaltma) bağlanır.
* Her olay bir dizi ortak alan içerir: Eventınstanceıd, EventName ve category.
* Her olay, olayın ilişkilendirildiği varlığa geri bağlayan alanları içerir. Örneğin, ApplicationCreated olayının oluşturulan uygulamanın adını tanımlayan alanları vardır.
* Olaylar, daha fazla analiz yapmak için çeşitli araçlarda tüketilebilen şekilde yapılandırılır. Ayrıca, bir olay için ilgili ayrıntılar, uzun bir dizenin aksine ayrı özellikler olarak tanımlanır. 
* Olaylar, Service Fabric farklı alt sistemler tarafından yazılır ve aşağıdaki kaynak (görev) tarafından tanımlanır. Bu alt sistemlerde [Service Fabric mimaride](service-fabric-architecture.md) ve [teknik genel bakış Service Fabric](service-fabric-technical-overview.md)daha fazla bilgi bulabilirsiniz.

Varlığa göre düzenlenmiş bu Service Fabric olaylarının listesi aşağıda verilmiştir.

## <a name="cluster-events"></a>Küme olayları

**Küme yükseltme olayları**

Küme yükseltmeleri hakkında daha fazla ayrıntı için [burada](service-fabric-cluster-upgrade-windows-server.md)bulunabilir.

| Even | Name | Kategori | Açıklama |Kaynak (görev) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Yükseltme | Bir küme yükseltmesi başlatıldı | CM | Bilgilendirici |
| 29628 | ClusterUpgradeCompleted | Yükseltme | Küme yükseltmesi tamamlandı | CM | Bilgilendirici | 
| 29629 | Clusteryükselderollbackstarted | Yükseltme | Bir küme yükseltmesi geri alınmaya başladı  | CM | Uyarı | 
| 29630 | Clusteryükselderollbackcompleted | Yükseltme | Bir küme yükseltmesinin geri alınması tamamlandı | CM | Uyarı | 
| 29631 | ClusterUpgradeDomainCompleted | Yükseltme | Bir yükseltme etki alanı, bir küme yükseltmesi sırasında yükseltmeyi bitirdi | CM | Bilgilendirici | 

**Yerleştirme olayları**
| Even | Name | Kategori | Açıklama |Kaynak (görev) | Level |
| --- | --- | ---| --- | --- | --- |
| 17616 | Karar |StateTransition | Yerleştirme işlemi, yeni çoğaltmaların yerleştirilmesiyle ilgili karar vermek üzere zamanlandı. | CRM | Bilgilendirici |


## <a name="node-events"></a>Düğüm olayları

**Düğüm yaşam döngüsü olayları** 

| Even | Name | Kategori | Açıklama |Kaynak (görev) | Level |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | Bir düğümün devre dışı bırakılması tamamlandı | FM | Bilgilendirici | 
| 18603 | NodeUp | StateTransition | Küme bir düğümün başlatıldığını algıladı | FM | Bilgilendirici | 
| 18604 | Düğüm aşağı | StateTransition | Küme bir düğümün kapatıldığını algıladı. Düğüm yeniden başlatma sırasında, bir NodeUp olayı izleyen bir Nodeup olayı görürsünüz |  FM | Hata | 
| 18605 | NodeAddedToCluster | StateTransition |  Kümeye yeni bir düğüm eklenmiştir ve bu düğüme uygulama dağıtabilir Service Fabric | FM | Bilgilendirici | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Kümeden bir düğüm kaldırıldı. Service Fabric artık bu düğüme uygulama dağıtmayacak | FM | Bilgilendirici | 
| 18607 | NodeDeactivateStarted | StateTransition |  Bir düğümü devre dışı bırakma işlemi başlatıldı | FM | Bilgilendirici | 
| 25621 | NodeOpenSucceeded | StateTransition |  Bir düğüm başarıyla başlatıldı | FabricNode | Bilgilendirici | 
| 25622 | NodeOpenFailed | StateTransition |  Bir düğüm başlatılamadı ve halkaya katılamadı | FabricNode | Hata | 
| 25624 | NodeClosed | StateTransition |  Düğüm başarıyla kapatıldı | FabricNode | Bilgilendirici | 
| 25626 | Nodedurdurulan | StateTransition |  Bir düğüm düzgün şekilde kapatıldı | FabricNode | Hata | 

**KTL günlükçü düğüm olayları** 

| Even | Name | Kategori | Açıklama |Kaynak (görev) | Level |
| --- | --- | --- | --- | --- | --- | 
| 50187 | Sharedlogwritekısıtlanıyor | Sağlık | Paylaşılan günlük yazma işlemleri azaltıyor | KtlLoggerNode | Bilgilendirici | 
| 50188 | Sharedlogwriteunkısıtlanıyor | Sağlık | Paylaşılan günlük yazma işlemleri daraltıldı | KtlLoggerNode | Bilgilendirici | 

## <a name="application-events"></a>Uygulama olayları

**Uygulama yaşam döngüsü olayları**

| Even | Name | Kategori | Açıklama |Kaynak (görev) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | Uyor | Yeni bir uygulama oluşturuldu | CM | Bilgilendirici | 
| 29625 | ApplicationDeleted | Uyor | Var olan bir uygulama silindi | CM | Bilgilendirici | 
| 23083 | Applicationprocessexted | Uyor | Bir uygulama içindeki bir işlem çıkış yaptı | Barındırma | Bilgilendirici | 

**Uygulama yükseltme olayları**

Uygulama yükseltmeleri hakkında daha fazla ayrıntı için [burada](service-fabric-application-upgrade.md)bulunabilir.

| Even | Name | Kategori | Açıklama |Kaynak (görev) | Level | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Yükseltme | Uygulama yükseltmesi başlatıldı | CM | Bilgilendirici | 
| 29622 | Applicationupgradetamamlandı | Yükseltme | Uygulama yükseltmesi tamamlandı | CM | Bilgilendirici | 
| 29623 | Applicationyükselderollbackstarted | Yükseltme | Uygulama yükseltmesi geri alınmaya başladı |CM | Uyarı | 
| 29624 | Applicationyükselderollbackcompleted | Yükseltme | Uygulama yükseltmesinin geri alınması tamamlandı | CM | Uyarı | 
| 29626 | ApplicationUpgradeDomainCompleted | Yükseltme | Yükseltme etki alanı, bir uygulama yükseltmesi sırasında yükseltmeyi bitirdi | CM | Bilgilendirici | 

## <a name="service-events"></a>Hizmet olayları

**Hizmet yaşam döngüsü olayları**

| Even | Name | Kategori | Açıklama |Kaynak (görev) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Uyor | Yeni bir hizmet oluşturuldu | FM | Bilgilendirici | 
| 18658 | ServiceDeleted | Uyor | Var olan bir hizmet silindi | FM | Bilgilendirici | 

## <a name="partition-events"></a>Bölüm olayları

**Bölüm taşıma olayları**

| Even | Name | Kategori | Açıklama |Kaynak (görev) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18940 | Partitionyapılandırdı | Uyor | Bölüm yeniden yapılandırması tamamlandı | RA | Bilgilendirici | 

## <a name="replica-events"></a>Çoğaltma olayları

**Çoğaltma yaşam döngüsü olayları**

| Even | Name | Kategori | Açıklama |Kaynak (görev) | Level |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Uyor | Güvenilir sözlük açıldı | Distributedsözlüğü | Bilgilendirici |
| 61702 | ReliableDictionaryClosed | Uyor | Güvenilir sözlük kapatıldı | Distributedsözlüğü | Bilgilendirici |
| 61703 | Reliabledictionarycheckpointkurtarılan | Uyor | Güvenilir sözlük, denetim noktasını kurtardı | Distributedsözlüğü | Bilgilendirici |
| 61704 | ReliableDictionaryCheckpointFilesSent | Uyor | Çoğaltma, güvenilir sözlüğün denetim noktası dosyalarını gönderdi | Distributedsözlüğü | Bilgilendirici |
| 61705 | Reliabledictionarycheckpointfilesalındı | Uyor | Çoğaltma, güvenilir sözlüğün denetim noktası dosyalarını aldı | Distributedsözlüğü | Bilgilendirici |
| 61963 | Reliablequeueaçıldı | Uyor | Güvenilir sıra açıldı | Distributedkuyruğu | Bilgilendirici |
| 61964 | ReliableQueueClosed | Uyor | Güvenilir sıra kapatıldı | Distributedkuyruğu | Bilgilendirici |
| 61965 | Reliablequeuecheckpointkurtarılan | Uyor | Güvenilir sıra, denetim noktasını kurtardı | Distributedkuyruğu | Bilgilendirici |
| 61966 | ReliableQueueCheckpointFilesSent | Uyor | Çoğaltma, güvenilir sıranın denetim noktası dosyalarını gönderdi | Distributedkuyruğu | Bilgilendirici |
| 63647 | Reliablequeuecheckpointfilesalındı | Uyor | Çoğaltma, güvenilir sıranın denetim noktası dosyalarını aldı | Distributedkuyruğu | Bilgilendirici |
| 63648 | Reliableconcurrentqueueaçıldı | Uyor | Güvenilir eşzamanlı sıra açıldı | ReliableConcurrentQueue | Bilgilendirici |
| 63649 | ReliableConcurrentQueueClosed | Uyor | Güvenilir eşzamanlı sıra kapatıldı | ReliableConcurrentQueue | Bilgilendirici |
| 63650 | Reliableconcurrentqueuecheckpointkurtarıldı | Uyor | Güvenilir eşzamanlı sıra, denetim noktasını kurtardı | ReliableConcurrentQueue | Bilgilendirici |
| 61687 | TStoreError | Hata | Güvenilir koleksiyonda beklenmeyen bir hata alındı | TStore | Hata |
| 63831 | Primaryfullcopybaşlatıldı | Uyor | Birincil çoğaltma tam bir kopya başlattı | TReplicator | Bilgilendirici |
| 63832 | Her ne kadar Ypartialcopybaşlatıldı | Uyor | Birincil çoğaltma kısmi bir kopya başlattı | TReplicator | Bilgilendirici |
| 16831 | Buildıdlereperepstarted | Uyor | Birincil çoğaltma, boşta çoğaltma oluşturmaya başladı | Çoğaltma | Bilgilendirici |
| 16832 | Buildidlereperepcompleted | Uyor | Birincil çoğaltma, boşta çoğaltma oluşturmayı tamamladı | Çoğaltma | Bilgilendirici |
| 16833 | Buildıdlereperepfailed | Uyor | Birincil çoğaltma, boşta çoğaltma oluşturmayı başaramadı | Çoğaltma | Uyarı |
| 16834 | PrimaryReplicationQueueFull | Sağlık | Birincil çoğaltmanın çoğaltma kuyruğu dolu | Çoğaltma | Uyarı |
| 16835 | PrimaryReplicationQueueWarning | Sağlık | Birincil çoğaltmanın çoğaltma kuyruğu dolmak üzere | Çoğaltma | Uyarı |
| 16836 | Primaryreplicationqueuewarningazaltıldığında | Sağlık | Birincil çoğaltmanın çoğaltma kuyruğu sorunsuz | Çoğaltma | Bilgilendirici |
| 16837 | SecondaryReplicationQueueFull | Sağlık | İkincil çoğaltmanın çoğaltma kuyruğu dolu | Çoğaltma | Uyarı |
| 16838 | SecondaryReplicationQueueWarning | Sağlık | İkincil çoğaltmanın çoğaltma kuyruğu dolmak üzere | Çoğaltma | Uyarı |
| 16839 | Secondaryreplicationqueuewarningazaltıldığında | Sağlık | İkincil çoğaltmanın çoğaltma kuyruğu sorunsuz | Çoğaltma | Bilgilendirici |
| 16840 | Primaryfaultedyavaştali | Sağlık | Birincil çoğaltma, yavaş bir ikincil çoğaltmada hata verdi | Çoğaltma | Uyarı |
| 16841 | Replicatorhata verdi | Sağlık | Çoğaltma hata verdi | Çoğaltma | Uyarı |

## <a name="container-events"></a>Kapsayıcı olayları

**Kapsayıcı yaşam döngüsü olayları** 

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Bir kapsayıcı başlatıldı | Barındırma | Bilgilendirici | 1 |
| 23075 | Containercode devre dışı | Bir kapsayıcı durdu | Barındırma | Bilgilendirici | 1 |
| 23082 | Kapsayıcıdan çıkıldı | Bir kapsayıcıda çıkıldı-UnexpectedTermination bayrağını denetleyin | Barındırma | Bilgilendirici | 1 |

## <a name="health-reports"></a>Sistem durumu raporları

[Service Fabric sistem durumu modeli](service-fabric-health-introduction.md) , zengin, esnek ve genişletilebilir bir sistem durumu değerlendirmesi ve raporlama sağlar. Service Fabric sürüm 6,2 ' den başlayarak sistem durumu verileri, geçmiş durum kayıtlarının sağlanması için platform olayları olarak yazılır. Sistem durumu olaylarının hacminin düşük kalmasını sağlamak için, yalnızca aşağıdaki Service Fabric olaylar olarak yazılır:

* Tüm `Error` veya `Warning` sistem durumu raporları
* `Ok` geçişler sırasında durum raporları
* Bir `Error` veya `Warning` sistem durumu olayının süresi dolarsa. Bu, bir varlığın sağlıksız olduğu süreyi belirlemede kullanılabilir

**Küme durumu raporu olayları**

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Yeni bir küme durumu raporu kullanılabilir | HM | Bilgilendirici | 1 |
| 54437 | ClusterHealthReportExpired | Var olan bir küme durumu raporunun süresi doldu | HM | Bilgilendirici | 1 |

**Düğüm durumu raporu olayları**

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Yeni bir düğüm durumu raporu kullanılabilir | HM | Bilgilendirici | 1 |
| 54432 | NodeHealthReportExpired | Mevcut bir düğüm durumu raporunun süresi doldu | HM | Bilgilendirici | 1 |

**Uygulama durumu raporu olayları**

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Yeni bir uygulama sistem durumu raporu oluşturuldu. Bu, dağıtılmamış uygulamalar içindir. | HM | Bilgilendirici | 1 |
| 54426 | Deployedadnewhealthreport | Yeni bir dağıtılmış uygulama sistem durumu raporu oluşturuldu | HM | Bilgilendirici | 1 |
| 54427 | Deployedservicepackasoy Whealthreport | Yeni dağıtılan bir hizmet sistem durumu raporu oluşturuldu | HM | Bilgilendirici | 1 |
| 54434 | ApplicationHealthReportExpired | Mevcut bir uygulama durumu raporunun süresi doldu | HM | Bilgilendirici | 1 |
| 54435 | Deployedavpplicationhealthreportexpired | Mevcut dağıtılan bir uygulama sistem durumu raporunun süresi doldu | HM | Bilgilendirici | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Mevcut dağıtılan bir hizmet durumu raporunun süresi doldu | HM | Bilgilendirici | 1 |

**Hizmet durumu raporu olayları**

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Yeni bir hizmet sistem durumu raporu oluşturuldu | HM | Bilgilendirici | 1 |
| 54433 | ServiceHealthReportExpired | Mevcut bir hizmet durumu raporunun süresi doldu | HM | Bilgilendirici | 1 |

**Bölüm durumu raporu olayları**

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Yeni bir bölüm sistem durumu raporu oluşturuldu | HM | Bilgilendirici | 1 |
| 54431 | PartitionHealthReportExpired | Mevcut bir bölüm sistem durumu raporunun süresi doldu | HM | Bilgilendirici | 1 |

**Çoğaltma sistem durumu raporu olayları**

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Durum bilgisi olan bir çoğaltma sistem durumu raporu oluşturuldu | HM | Bilgilendirici | 1 |
| 54430 | Statelessınstancenewhealthreport | Yeni bir durum bilgisi olmayan örnek sistem durumu raporu oluşturuldu | HM | Bilgilendirici | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Mevcut durum bilgisi olan bir çoğaltma sistem durumu raporunun süresi doldu | HM | Bilgilendirici | 1 |
| 54439 | Statelessınstancehealthreportexpired | Var olan bir durum bilgisi olmayan örnek sistem durumu raporunun süresi doldu | HM | Bilgilendirici | 1 |

## <a name="chaos-testing-events"></a>Chaos test olayları 

**Chaos oturum olayları**

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Bir Chaos test oturumu başlatıldı | Test edilebilirlik | Bilgilendirici | 1 |
| 50023 | Chaosdurduruldu | Bir Chaos test oturumu durdu | Test edilebilirlik | Bilgilendirici | 1 |

**Chaos düğüm olayları**

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | ---| --- | --- | --- |
| 50033 | Chaosnoderestartzamanlandı | Bir düğüm, bir Chaos test oturumunun bir parçası olarak yeniden başlatılacak şekilde zamanlandı | Test edilebilirlik | Bilgilendirici | 1 |
| 50087 | ChaosNodeRestartCompleted | Bir düğümün bir Chaos test oturumunun parçası olarak yeniden başlatılması tamamlandı | Test edilebilirlik | Bilgilendirici | 1 |

**Chaos uygulama olayları**

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | ---| --- | --- | --- |
| 50053 | Chaoscodepackagerestartzamanlandı | Bir Chaos test oturumu sırasında bir kod paketi yeniden başlatması zamanlandı | Test edilebilirlik | Bilgilendirici | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Bir Chaos test oturumu sırasında bir kod paketi yeniden başlatması tamamlandı | Test edilebilirlik | Bilgilendirici | 1 |

**Chaos bölüm olayları**

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Bir birincil bölüm, bir Chaos test oturumunun parçası olarak taşınmaya zamanlandı | Test edilebilirlik | Bilgilendirici | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Bir Chaos test oturumunun parçası olarak bir ikincil bölüm ilerlemek üzere zamanlandı | Test edilebilirlik | Bilgilendirici | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Birincil bölüm taşımanın daha derin analizi kullanılabilir | Test edilebilirlik | Bilgilendirici | 1 |

**Chaos çoğaltma olayları**

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | ---| --- | --- | --- |
| 50047 | Chaosreplicarestartzamanlandı | Bir Chaos test oturumunun parçası olarak bir çoğaltma yeniden başlatması zamanlandı | Test edilebilirlik | Bilgilendirici | 1 |
| 50051 | Chaosreplicaremovalzamanlandı | Bir çoğaltma kaldırma işlemi, bir Chaos test oturumunun parçası olarak zamanlandı | Test edilebilirlik | Bilgilendirici | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Bir Chaos test oturumunun parçası olarak bir çoğaltma kaldırma işlemi tamamlandı | Test edilebilirlik | Bilgilendirici | 1 |

## <a name="other-events"></a>Diğer olaylar

**Bağıntı olayları**

| Even | Ad | Açıklama |Kaynak (görev) | Level | Sürüm |
| --- | --- | ---| --- | --- | --- |
| 65011 | Correlationoperasyonel | Bağıntı algılandı | Test edilebilirlik | Bilgilendirici | 1 |

## <a name="events-prior-to-version-62"></a>Sürüm 6,2 ' den önceki olaylar

Sürüm 6,2 ' den önceki Service Fabric tarafından sunulan olayların kapsamlı bir listesi aşağıda verilmiştir.

| Even | Name | Kaynak (görev) | Level |
| --- | --- | --- | --- |
| 25620 | Nodeaçýlýþ | FabricNode | Bilgilendirici |
| 25621 | NodeOpenedSuccess | FabricNode | Bilgilendirici |
| 25622 | NodeOpenedFailed | FabricNode | Bilgilendirici |
| 25623 | NodeClosing | FabricNode | Bilgilendirici |
| 25624 | NodeClosed | FabricNode | Bilgilendirici |
| 25625 | Nodedurduruluyor | FabricNode | Bilgilendirici |
| 25626 | Nodedurdurulan | FabricNode | Bilgilendirici |
| 29627 | ClusterUpgradeStart | CM | Bilgilendirici |
| 29628 | Clusterupgradetamamlanmıştır | CM | Bilgilendirici |
| 29629 | Clusteryükselderollback | CM | Bilgilendirici |
| 29630 | Clusteryükselderollbacktamamlanmıştır | CM | Bilgilendirici |
| 29631 | Clusterupgradedomaintamamlanmıştır | CM | Bilgilendirici |
| 23074 | ContainerActivated | Barındırma | Bilgilendirici |
| 23075 | Containercode devre dışı | Barındırma | Bilgilendirici |
| 29620 | ApplicationCreated | CM | Bilgilendirici |
| 29621 | ApplicationUpgradeStart | CM | Bilgilendirici |
| 29622 | Applicationupgradetamamlandı | CM | Bilgilendirici |
| 29623 | Applicationyükselderollback | CM | Bilgilendirici |
| 29624 | Applicationyükselderollbacktamamlandı | CM | Bilgilendirici |
| 29625 | ApplicationDeleted | CM | Bilgilendirici |
| 29626 | Applicationupgradedomaintamamlandı | CM | Bilgilendirici |
| 18566 | ServiceCreated | FM | Bilgilendirici |
| 18567 | ServiceDeleted | FM | Bilgilendirici |

## <a name="next-steps"></a>Sonraki adımlar

* [Service Fabric tanılama](service-fabric-diagnostics-overview.md) 'ya genel bakış alın
* [Service Fabric Eventstore 'A genel bakış](service-fabric-diagnostics-eventstore.md) bölümünde eventstore hakkında daha fazla bilgi edinin
* [Azure tanılama](service-fabric-diagnostics-event-aggregation-wad.md) yapılandırmanızı daha fazla günlük toplamak üzere değiştirme
* Işletimsel kanal günlüklerinizi görmek için [Application Insights ayarlama](service-fabric-diagnostics-event-analysis-appinsights.md)
