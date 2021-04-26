---
title: Sistem durum raporlarıyla ilgili sorunları giderme
description: Azure Service Fabric bileşenleri tarafından gönderilen sistem durumu raporlarını ve küme veya uygulama sorunlarını gidermeye yönelik kullanımları açıklar
ms.topic: conceptual
ms.date: 2/28/2018
ms.openlocfilehash: 483483746b2cce66588e9481bca7e0de391070b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625900"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Sorun gidermek için sistem durum raporlarını kullanma
Azure Service Fabric bileşenleri, kümedeki tüm varlıklarda sistem durumu raporlarını doğrudan kutudan çıkar. Sistem [durumu deposu](service-fabric-health-introduction.md#health-store) , sistem raporlarına göre varlıkları oluşturur ve siler. Ayrıca, bunları varlık etkileşimleri yakalayan bir hiyerarşide düzenler.

> [!NOTE]
> Sağlık ile ilgili kavramları anlamak için [Service Fabric sistem durumu modelinde](service-fabric-health-introduction.md)daha fazla bilgi edinin.
> 
> 

Sistem durumu raporları, küme ve uygulama işlevlerine ilişkin görünürlük sağlar ve sorunları bayrak olarak verir. Uygulamalar ve hizmetler için sistem durumu raporları varlıkların uygulandığını ve Service Fabric perspektifinden doğru şekilde çalıştığını doğrular. Raporlar, hizmetin iş mantığını veya yanıt vermeyen işlemlerin algılanmasına ilişkin bir sistem durumu izleme sağlamaz. Kullanıcı Hizmetleri, sistem durumu verilerini mantığına özgü bilgilerle zenginleştirebilirsiniz.

> [!NOTE]
> Kullanıcı Watchdogs tarafından gönderilen durum raporları yalnızca sistem bileşenleri bir varlık oluşturduktan *sonra* görünür. Bir varlık silindiğinde, sistem durumu deposu onunla ilişkili tüm sistem durumu raporlarını otomatik olarak siler. Varlığın yeni bir örneği oluşturulduğunda aynı değeri de geçerlidir. Yeni bir durum bilgisi olan kalıcı hizmet çoğaltması örneği oluşturulduğunda örnek bir örnektir. Eski örnekle ilişkili tüm raporlar silinir ve mağazadan temizlenir.
> 
> 

Sistem bileşen raporları, kaynak tarafından tanımlanır ve "sistem" ile başlar **.** ekleyin. Geçersiz parametrelere sahip raporlar reddedildiği için Watchdogs, kaynakları için aynı öneki kullanamaz.

Ne tetikleyeceğini anlamak ve bunların gösterdiği olası sorunları nasıl düzeltebileceğinizi öğrenmek için bazı sistem raporlarına göz atalım.

> [!NOTE]
> Service Fabric,, kümede ve uygulamalarda meydana gelen görünürlüğü artıran ilgilendiğiniz koşullara rapor eklemeye devam eder. Mevcut raporlar sorunu daha hızlı gidermeye yardımcı olmak için daha fazla ayrıntıya sahip olabilir.
> 
> 

## <a name="cluster-system-health-reports"></a>Küme sistem durumu raporları
Küme durumu varlığı, sistem durumu deposunda otomatik olarak oluşturulur. Her şey düzgün çalışıyorsa, bir sistem raporuna sahip değildir.

### <a name="neighborhood-loss"></a>Komşu kaybı
**System. Federation** , bir komşu kayıp algıladığında bir hata bildiriyor. Rapor bağımsız düğümlerden, düğüm KIMLIĞI ise özellik adına dahil edilir. Service Fabric halkasının tamamında bir komşu kaybolursa, genellikle boşluk raporunun her iki tarafını temsil eden iki olayı bekleyebilir. Daha fazla neighborhoods kaybolursa, daha fazla olay vardır.

Rapor, genel kira zaman aşımını yaşam süresi (TTL) olarak belirtir. Rapor, koşulun etkin kaldığı sürece TTL süresinin her yarısında yeniden gönderilir. Etkinlik süresi dolduktan sonra otomatik olarak kaldırılır. Kaldırma-süre sonu davranışı, raporlama düğümü çalışmıyor olsa bile raporun sistem durumu deposundan doğru şekilde temizlenmesini sağlar.

* **SourceId**: System. FEDERATION
* **Özellik**: **komşuları** ile başlar ve düğüm bilgilerini içerir.
* **Sonraki adımlar**: komşularınızın neden kaybolduğunu araştırın. Örneğin, küme düğümleri arasındaki iletişimi denetleyin.

### <a name="rebuild"></a>Yeniden derleme

Yük Devretme Yöneticisi (FM) hizmeti, küme düğümleri hakkındaki bilgileri yönetir. FM, verilerini kaybeder ve veri kaybına geçtiğinde, küme düğümleri hakkında en güncel bilgilere sahip olduğunu garanti etmez. Bu durumda, sistem yeniden derleme yoluyla geçer ve System.FM durumunu yeniden derlemek için kümedeki tüm düğümlerden verileri toplar. Bazen ağ veya düğüm sorunlarından dolayı yeniden oluşturma, takılmış veya durdurulmuş olabilir. Aynı durum Yük Devretme Yöneticisi Master (FMM) hizmeti ile de oluşabilir. FMM, tüm FMs 'nin kümede nerede olduğunu izlemeye devam eden, durum bilgisi olmayan bir sistem hizmetidir. FMM 'nin birincili, KIMLIĞI 0 ' a yakın olan düğümdür. Bu düğüm atıldığında yeniden derleme tetiklenir.
Önceki koşullardan biri gerçekleştiğinde, **System.FM** veya **System. FMM** bunu bir hata raporuyla işaretler. Yeniden oluşturma iki aşamadan birinde takılmış olabilir:

* **Yayın bekleniyor**: FM/FDD diğer düğümlerden gelen yayın iletisi yanıtı için bekler.

  * **Sonraki adımlar**: düğümler arasında bir ağ bağlantısı sorunu olup olmadığını araştırın.
* **Düğüm bekleniyor**: FM/FMM zaten diğer düğümlerden bir yayın yanıtı aldı ve belirli düğümlerden yanıt bekliyor. Sistem durumu raporu, FM/FMM 'nin bir yanıt beklediği düğümleri listeler.
   * **Sonraki adımlar**: FM/FDD ve listelenen düğümler arasındaki ağ bağlantısını araştırın. Diğer olası sorunlar için listelenen her düğümü araştırın.

* **SourceId**: System.FM veya System. FMM
* **Özellik**: yeniden derleyin.
* **Sonraki adımlar**: düğümler arasındaki ağ bağlantısını ve sistem durumu raporunun açıklamasında listelenen belirli düğümlerin durumunu araştırın.

### <a name="seed-node-status"></a>Çekirdek düğüm durumu
**System.FM** , bazı çekirdek düğümlerin sağlıksız olması durumunda küme düzeyinde bir uyarı bildirir. Çekirdek düğümleri, temel alınan kümenin kullanılabilirliğini sürdürdüğüm olan düğümlerdir. Bu düğümler, diğer düğümlerle Kiralama kurarak ve belirli ağ başarısızlığı türleri sırasında tiebreaklikler gören kümenin devam ettiğinden emin olmaya yardımcı olur. Çekirdek düğümlerin bir çoğunluğu kümede kapalıysa ve geri getirilmezse, küme otomatik olarak kapanır. 

Düğüm durumu kapalıysa, kaldırılırsa veya bilinmiyorsa, çekirdek düğüm sağlıksız olur.
Çekirdek düğüm durumu için uyarı raporu, tüm sağlıksız çekirdek düğümlerini ayrıntılı bilgilerle listelecektir.

* **SourceId**: System.FM
* **Özellik**: seednodestatus
* **Sonraki adımlar**: Bu uyarı kümede görünüyorsa, bunları onarmak için aşağıdaki yönergeleri izleyin: Service Fabric 6,5 veya üzeri bir sürümü çalıştıran küme Için: Azure 'da Service Fabric kümesi için, çekirdek düğüm kapatıldıktan sonra, Service Fabric onu otomatik olarak çekirdek olmayan bir düğüm olarak değiştirmeye çalışır. Bunu yapmak için, birincil düğüm türündeki çekirdek olmayan düğümlerin sayısının aşağı çekirdek düğümlerin sayısına eşit veya ondan daha büyük olduğundan emin olun. Gerekirse, bunu elde etmek için birincil düğüm türüne daha fazla düğüm ekleyin.
Küme durumuna bağlı olarak, sorunun düzeltilmesi biraz zaman alabilir. Bu işlem tamamlandığında, uyarı raporu otomatik olarak temizlenir.

Tek başına küme Service Fabric, uyarı raporunu temizlemek için tüm çekirdek düğümlerin sağlıklı olması gerekir. Çekirdek düğümlerin neden sağlıksız olduğuna bağlı olarak, farklı eylemlerin alınması gerekir: çekirdek düğüm kapalıysa, kullanıcıların bu çekirdek düğümü yukarı getirmelerini sağlar; çekirdek düğüm kaldırılırsa veya bilinmiyorsa, bu çekirdek düğümün [kümeden kaldırılması gerekir](./service-fabric-cluster-windows-server-add-remove-nodes.md).
Tüm çekirdek düğümleri sağlıklı hale geldiğinde uyarı raporu otomatik olarak temizlenir.

6,5 'den eski Service Fabric sürümü çalıştıran küme için: Bu durumda, uyarı raporunun el ile temizlenmesi gerekir. **Kullanıcılar, raporu temizlemeden önce tüm çekirdek düğümlerin sağlıklı hale gelmesini sağlamalıdır**: çekirdek düğüm kapalıysa, kullanıcıların bu çekirdek düğümü yukarı getirmeleri gerekir; çekirdek düğüm kaldırılırsa veya bilinmiyorsa, bu çekirdek düğümün kümeden kaldırılması gerekir.
Tüm çekirdek düğümleri sağlıklı olduktan sonra, [Uyarı raporunu temizlemek](/powershell/module/servicefabric/send-servicefabricclusterhealthreport)için PowerShell 'de aşağıdaki komutu kullanın:

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### Node up/down
System.FM reports as OK when the node joins the ring (it's up and running). It reports an error when the node departs the ring (it's down, either for upgrading or simply because it has failed). The health hierarchy built by the health store acts on deployed entities in correlation with System.FM node reports. It considers the node a virtual parent of all deployed entities. The deployed entities on that node are exposed through queries if the node is reported as up by System.FM, with the same instance as the instance associated with the entities. When System.FM reports that the node is down or restarted, as a new instance, the health store automatically cleans up the deployed entities that can exist only on the down node or on the previous instance of the node.

* **SourceId**: System.FM
* **Property**: State.
* **Next steps**: If the node is down for an upgrade, it should come back up after it's been upgraded. In this case, the health state should switch back to OK. If the node doesn't come back or it fails, the problem needs more investigation.

The following example shows the System.FM event with a health state of OK for node up:

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Sertifika süre sonu
**System. fabricnode** , düğüm tarafından kullanılan sertifikaların süresi dolmak üzere bir uyarı bildirir. Düğüm başına üç sertifika vardır: **Certificate_cluster**, **Certificate_server** ve **Certificate_default_client**. Süre sonu en az iki hafta sonra, raporun sistem durumu Tamam olur. Süre sonu iki hafta içinde olduğunda, rapor türü bir uyarıdır. Bu olayların TTL 'si sonsuzdur ve düğüm kümeden ayrıldığında kaldırılır.

* **SourceId**: System. fabricnode
* **Özellik**: **sertifikayla** başlar ve sertifika türü hakkında daha fazla bilgi içerir.
* **Sonraki adımlar**: süresi dolmak üzere olan sertifikaları güncelleştirin.

### <a name="load-capacity-violation"></a>Yük kapasitesi ihlali
Service Fabric Load Balancer düğüm kapasitesi ihlali algıladığında bir uyarı bildirir.

* **SourceId**: System. PLB
* **Özellik**: **kapasiteyle** başlar.
* **Sonraki adımlar**: belirtilen ölçümleri denetleyin ve düğümdeki geçerli kapasiteyi görüntüleyin.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Kaynak idare ölçümleri için düğüm kapasitesi uyumsuzluğu
System. Hosting, küme bildiriminde tanımlanan düğüm kapasiteleri, kaynak idare ölçümleri (bellek ve CPU çekirdekleri) için gerçek düğüm kapasitelerine kıyasla bir uyarı bildirir. Bir sistem durumu raporu, [kaynak idare](service-fabric-resource-governance.md) kullanan ilk hizmet paketi belirtilen bir düğümde kaydolduğunda görüntülenir.

* **SourceId**: System. Hosting
* **Özellik**: **resourceidare**.
* **Sonraki adımlar**: Bu sorun bir sorun olabilir çünkü hizmet paketlerinin yönetimi beklenen şekilde zorlanmaz ve [kaynak](service-fabric-resource-governance.md) İdaresi düzgün şekilde çalışmıyor. Bu ölçümler için doğru düğüm kapabilgileriyle küme bildirimini güncelleştirin veya bunları belirtmeyin ve Service Fabric kullanılabilir kaynakları otomatik olarak algılamaya izin verin.

## <a name="application-system-health-reports"></a>Uygulama sistem durumu raporları
Küme Yöneticisi hizmetini temsil eden System.CM, bir uygulamayla ilgili bilgileri yöneten yetkildir.

### <a name="state"></a>Durum
Uygulama oluşturulduğunda veya güncelleştirilirken raporları Tamam olarak System.CM. Mağaza 'dan kaldırılabilmesi için uygulama silindiğinde sistem durumu deposuna bildirir.

* **SourceId**: System.cm
* **Özellik**: durum.
* **Sonraki adımlar**: uygulama oluşturulduysa veya güncelleştirilirse, Küme Yöneticisi sistem durumu raporunu içermelidir. Aksi takdirde, bir sorgu vererek uygulamanın durumunu kontrol edin. Örneğin, **Get-ServiceFabricApplication-ApplicationName** *ApplicationName* PowerShell cmdlet 'ini kullanın.

Aşağıdaki örnek, **Fabric:/WordCount** uygulamasındaki durum olayını gösterir:

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Hizmet sistem durumu raporları
Yük Devretme Yöneticisi hizmetini temsil eden System.FM, hizmetlerle ilgili bilgileri yöneten yetkilisdir.

### <a name="state"></a>Durum
Hizmet oluşturulduğunda raporları Tamam olarak System.FM. Hizmet silindiğinde varlığı sistem durumu deposundan siler.

* **SourceId**: System.FM
* **Özellik**: durum.

Aşağıdaki örnek Service Fabric 'teki durum olayını gösterir **:/WORDCOUNT/WordCountWebService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Hizmet bağıntı hatası
**System. PLB** , bir hizmetin güncelleştirme bir benzeşim zinciri oluşturan başka bir hizmetle bağıntılı olduğunu algıladığında bir hata bildirir. Başarılı bir güncelleştirme gerçekleştiğinde rapor temizlenir.

* **SourceId**: System. PLB
* **Özellik**: **ServiceDescription**.
* **Sonraki adımlar**: bağıntılı hizmet açıklamalarını denetleyin.

## <a name="partition-system-health-reports"></a>Sistem durumu raporlarını bölümle
Yük Devretme Yöneticisi hizmetini temsil eden System.FM, hizmet bölümleri hakkındaki bilgileri yöneten yetkilisdir.

### <a name="state"></a>Durum
System.FM, bölüm oluşturulduğunda ve sağlıklı olduğunda raporları Tamam olarak bildirir. Bölüm silindiğinde varlığı sistem durumu deposundan siler.

Bölüm en düşük çoğaltma sayısının altındaysa bir hata bildirir. Bölüm en düşük çoğaltma sayısının altında değilse, ancak hedef çoğaltma sayısının altındaysa, bir uyarı bildirir. Bölüm çekirdek kaybolduysa, System.FM bir hata bildirir.

Diğer önemli olaylar, yeniden yapılandırma beklenenden uzun sürerse ve derleme beklenenden uzun sürerse bir uyarı içerir. Derleme ve yeniden yapılandırma için beklenen zamanlar, hizmet senaryolarına göre yapılandırılabilir. Örneğin, bir hizmetin Azure SQL veritabanı gibi terabaytlık bir durumu varsa, derleme az miktarda duruma sahip bir hizmet için daha uzun sürer.

* **SourceId**: System.FM
* **Özellik**: durum.
* **Sonraki adımlar**: sistem durumu Tamam değilse, bazı çoğaltmaların oluşturulması, açılmadığının veya birincil ya da ikinciye yükseltilme olasılığı vardır. 

Açıklama çekirdek kaybını tanımlıysa, kapatılmış olan ve yedeklenen çoğaltmalar için ayrıntılı sistem durumu raporunu incelemek, bölümü yeniden çevrimiçi duruma getirmeye yardımcı olur.

Açıklama, yeniden [yapılandırma](service-fabric-concepts-reconfiguration.md)sırasında takılmış bir bölümü tanımlıyorsa, birincil çoğaltmadaki sistem durumu raporu daha fazla bilgi sağlar.

Diğer System.FM sistem durumu raporları için, diğer sistem bileşenlerinden çoğaltmalar veya bölüm ya da hizmet üzerinde raporlar vardır. 

Aşağıdaki örneklerde bu raporların bazıları açıklanır. 

Aşağıdaki örnekte sağlıklı bir bölüm gösterilmektedir:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Aşağıdaki örnek, hedef çoğaltma sayısı altındaki bir bölümün sistem durumunu gösterir. Bir sonraki adım, nasıl yapılandırıldığını gösteren bölüm açıklamasını almak için kullanılır: **Minreplicasetsize** üç ve **targetreplicasetsize** yedi. Daha sonra kümedeki düğümlerin sayısını alın, bu durumda beş olur. Bu nedenle, hedef çoğaltma sayısı kullanılabilir düğüm sayısından daha yüksek olduğundan, bu durumda iki çoğaltma yerleştirilemez.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

Aşağıdaki örnek, Kullanıcı, **RunAsync** yönteminde iptal belirtecini göstermediğinden, yeniden yapılandırma sırasında takılmış bir bölümün sistem durumunu gösterir. Birincil (P) olarak işaretlenen herhangi bir çoğaltmanın sistem durumu raporunun araştırılmasına yardımcı olabilir.

```powershell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Bu sistem durumu raporu, yeniden yapılandırma yapılmakta olan bölüm çoğaltmalarının durumunu gösterir: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Her çoğaltma için sistem durumu raporu şunları içerir:
- Önceki yapılandırma rolü
- Geçerli yapılandırma rolü
- [Çoğaltma durumu](service-fabric-concepts-replica-lifecycle.md)
- Çoğaltmanın çalıştığı düğüm
- Çoğaltma KIMLIĞI

Örnek gibi bir durumda, daha fazla araştırma yapmanız gerekir. `Primary` `Secondary` Önceki örnekte, ve (131482789658160654 ve 131482789688598467) olarak işaretlenen yinelemelerle başlayarak her bir çoğaltmanın sistem durumunu araştırın.

### <a name="replica-constraint-violation"></a>Çoğaltma kısıtlaması ihlali
**System. PLB** , bir çoğaltma kısıtlaması ihlali algılarsa bir uyarı bildirir ve tüm bölüm çoğaltmalarını yerleştiremiyor. Rapor ayrıntıları hangi kısıtlamaların ve özelliklerin çoğaltma yerleşimini engellediğini gösterir.

* **SourceId**: System. PLB
* **Özellik**: **ReplicaConstraintViolation** ile başlar.

## <a name="replica-system-health-reports"></a>Çoğaltma sistem durumu raporları
Yeniden yapılandırma aracı bileşenini temsil eden **System. ra**, çoğaltma durumu için yetkilidir.

### <a name="state"></a>Durum
Çoğaltma oluşturulduğunda System. RA raporları tamam.

* **SourceId**: System. ra
* **Özellik**: durum.

Aşağıdaki örnekte sağlıklı bir çoğaltma gösterilmektedir:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Bu özellik, bir çoğaltmayı açmaya çalışırken, bir çoğaltmayı kapatan veya bir rolden bir çoğaltmayı diğerine geçirmeye çalışırken uyarıları veya sorunları göstermek için kullanılır. Daha fazla bilgi için bkz. [çoğaltma yaşam döngüsü](service-fabric-concepts-replica-lifecycle.md). Bu süre boyunca, API çağrılarından veya hizmet ana bilgisayarı işleminin kilitlenmelerinden oluşan özel durumlar olabilir. C# kodundan gelen API çağrıları nedeniyle oluşan hatalarda, Service Fabric özel durum ve yığın izlemesini sistem durumu raporuna ekler.

Bu sistem durumu uyarıları, eyleme yerel olarak birkaç kez yeniden denendikten sonra tetiklenir (ilkeye bağlı olarak). Service Fabric en yüksek eşiğe kadar yeniden dener. En fazla eşiğe ulaşıldığında, durumu düzeltmek için işlem yapmayı deneyebilir. Bu girişim, bu düğümdeki eylemde bulunduğu için bu uyarıların temizlenmesine neden olabilir. Örneğin, bir çoğaltma bir düğümde açılmazsa, Service Fabric bir sistem durumu uyarısı oluşturur. Çoğaltma açık olmaya devam ederse, Service Fabric kendi kendine onarım işlevi görür. Bu eylem, aynı işlemi başka bir düğümde denemeyi gerektirebilir. Bu girişim, bu çoğaltma için oluşturulan uyarının temizlenmesine neden olur. 

* **SourceId**: System. ra
* **Özellik**: **replicaopenstatus**, **Replicaclosestatus** ve **replicachangerolestatus**.
* **Sonraki adımlar**: işlemin neden başarısız olduğunu belirlemek için hizmet kodunu veya kilitlenme dökümlerini araştırın.

Aşağıdaki örnek, Open yönteminden alınan bir çoğaltmanın sistem durumunu gösterir `TargetInvocationException` . Açıklama hata noktasını, **Istatefulservicereplica. Open**, **TargetInvocationException** özel durum türünü ve yığın izlemesini içerir.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

Aşağıdaki örnekte, kapatma sırasında sürekli olarak kilitlenen bir çoğaltma gösterilmektedir:

```powershell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Yeniden yapılandırma
Bu özellik yeniden [yapılandırma](service-fabric-concepts-reconfiguration.md) gerçekleştiren bir çoğaltmanın, yeniden yapılandırma durdurulduğunu veya takıldığını algıladığında kullanılır. Bu sistem durumu raporu geçerli rolü birincil olan çoğaltma üzerinde olabilir. bu durum, birincil olan bir değiştirme birincil yeniden yapılandırması, birincisinin birincil ' dan etkin ikincil 'e indirgenmiş olması durumunda olabilir.

Yeniden yapılandırma aşağıdaki nedenlerden biri için takılmış olabilir:

- Yerel çoğaltma üzerinde, yeniden yapılandırması gerçekleştirmeyle aynı çoğaltma tamamlanırken bir eylem tamamlanmıyor. Bu durumda, System. RAP veya System.RE diğer bileşenlerinden bu çoğaltmadaki sistem durumu raporlarının araştırmasından daha fazla bilgi sağlayabiliriz.

- Bir eylem, uzak bir çoğaltmada tamamlanmıyor. Eylemlerin beklemede olduğu çoğaltmalar sistem durumu raporunda listelenir. Bu uzak çoğaltmaların sistem durumu raporlarında daha fazla araştırma yapılmalıdır. Bu düğüm ile uzak düğüm arasında iletişim sorunları da olabilir.

Nadir durumlarda, bu düğüm ve Yük Devretme Yöneticisi hizmeti arasındaki iletişim veya diğer sorunlar nedeniyle yeniden yapılandırma yapılabilir.

* **SourceId**: System. ra
* **Özellik**: yeniden yapılandırma.
* **Sonraki adımlar**: sistem durumu raporunun açıklamasına bağlı olarak yerel veya uzak çoğaltmaları araştırın.

Aşağıdaki örnek, bir yeniden yapılandırma yerel çoğaltmada takılı bir sistem durumu raporu gösterir. Bu örnekte, bunun nedeni, iptal belirtecini içermeyen bir hizmettir.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

Aşağıdaki örnek, iki uzak çoğaltmalardan yanıt bekleyen bir yeniden yapılandırma takılmasına neden olan bir sistem durumu raporunu gösterir. Bu örnekte, bölümde geçerli birincil dahil olmak üzere üç çoğaltma vardır. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Bu sistem durumu raporu, yeniden yapılandırma işleminin iki çoğaltmalardan yanıt beklerken takılmasını gösterir: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Her çoğaltma için aşağıdaki bilgiler verilmiştir:
- Önceki yapılandırma rolü
- Geçerli yapılandırma rolü
- [Çoğaltma durumu](service-fabric-concepts-replica-lifecycle.md)
- Düğüm KIMLIĞI
- Çoğaltma KIMLIĞI

Yeniden yapılandırma engelini kaldırmak için:
- **Aşağı** çoğaltmaların oluşturulması gerekir. 
- **InBuild** çoğaltmalarının derleme ve geçiş için hazırlık yapması gerekir.

### <a name="slow-service-api-call"></a>Yavaş hizmet API çağrısı
Kullanıcı Hizmeti koduna yapılan bir çağrı yapılandırılan süreden uzun sürerse **System. rap** ve **System. Replicator** bir uyarı bildirir. Çağrı tamamlandığında uyarı temizlenir.

* **SourceId**: System. rap veya System. Replicator
* **Özellik**: yavaş API 'nin adı. Açıklama, API 'nin beklenme zamanı hakkında daha fazla ayrıntı sağlar.
* **Sonraki adımlar**: çağrının beklenenden uzun sürmesinin nedenini araştırın.

Aşağıdaki örnek, **RunAsync** içindeki iptal belirtecini içermeyen güvenilir bir hizmet için System. rap 'deki sistem durumu olayını gösterir:

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

Özellik ve metin hangi API 'nin takılı olduğunu gösterir. Farklı takılmış API 'Ler için gerçekleştirilecek sonraki adımlar farklıdır. *Istatefulservicereplica* veya *Istatelessserviceınstance* üzerinde herhangi bir API genellikle hizmet kodundaki bir hatadır. Aşağıdaki bölümde [Reliable Services modeline](service-fabric-reliable-services-lifecycle.md)nasıl çevireceği açıklanmaktadır:

- **Istatefulservicereplica. Open**: Bu uyarı,, veya üzerine bir `CreateServiceInstanceListeners` çağrının `ICommunicationListener.OpenAsync` `OnOpenAsync` takıldığını gösterir.

- **Istatefulservicereplica. Close** ve **Istatefulservicereplica. Abort**: en yaygın durum, ' a geçirilen iptal belirtecini temel alan bir hizmettir `RunAsync` . Ayrıca, `ICommunicationListener.CloseAsync` ya da geçersiz kılınırsa, bu da olabilir `OnCloseAsync` .

- **Istatefulservicereplica. ChangeRole (S)** ve **istatefulservicereplica. Changerole (N)**: en yaygın durum, ' a geçirilen iptal belirtecini temel alan bir hizmettir `RunAsync` . Bu senaryoda, en iyi çözüm çoğaltmayı yeniden başlatmasıdır.

- **Istatefulservicereplica. ChangeRole (P)**: en yaygın durum, hizmetin öğesinden bir görev döndürmediğinde olur `RunAsync` .

Takılmış olan diğer API çağrıları **ıreplicator** arabirimidir. Örnek:

- **Ireplicator. catch Upreplicaset**: Bu uyarı iki işlemlerden birini gösterir. Yetersiz çoğaltma yok. Bu durumun bu olup olmadığını görmek için, bir veya daha fazla yeniden yapılandırma için bölümdeki çoğaltmaların çoğaltma durumuna veya System.FM sistem durumu raporuna bakın. Veya çoğaltmalar, işleme işlemleri değildir. PowerShell cmdlet 'i `Get-ServiceFabricDeployedReplicaDetail` Tüm çoğaltmaların ilerlemesini belirlemede kullanılabilir. Sorun, `LastAppliedReplicationSequenceNumber` değeri birincil değerinin arkasında olan çoğaltmalarla birlikte kalıyor `CommittedSequenceNumber` .

- **Ireplicator. BuildReplica ( \<Remote ReplicaId> )**: Bu uyarı, derleme işlemindeki bir sorunu gösterir. Daha fazla bilgi için bkz. [çoğaltma yaşam döngüsü](service-fabric-concepts-replica-lifecycle.md). Bunun nedeni, Replicator adresinin yanlış yapılandırılması olabilir. Daha fazla bilgi için bkz. [durum bilgisi Reliable Services yapılandırma](service-fabric-reliable-services-configuration.md) ve [hizmet bildiriminde kaynakları belirtme](service-fabric-service-manifest-resources.md). Uzak düğümde de sorun olabilir.

### <a name="replicator-system-health-reports"></a>Çoğaltıcı sistem durumu raporları
**Çoğaltma sırası dolu:** 
 **System. Replicator** , çoğaltma kuyruğu dolduğunda bir uyarı bildirir. Birincil üzerinde, bir veya daha fazla ikincil çoğaltmanın işlemleri bildirmek için yavaş olduğu için çoğaltma kuyruğu genellikle tam hale gelir. İkincil üzerinde bu durum genellikle hizmetin işlemleri uygulamak için yavaş olduğu durumlarda oluşur. Sıra artık dolduğunda uyarı temizlenir.

* **SourceId**: System. Replicator
* **Özellik**: çoğaltma rolüne bağlı olarak **primaryreplicationqueuestatus** veya **secondaryreplicationqueuestatus**.
* **Sonraki adımlar**: rapor birincil ise, kümedeki düğümler arasındaki bağlantıyı denetleyin. Tüm bağlantılar sağlıklı ise, işlemleri uygulamak için yüksek disk gecikmesi olan en az bir adet yavaş ikincil olabilir. Rapor ikincikse, önce düğüm üzerinde disk kullanımı ve performansını denetleyin. Ardından yavaş düğümden birincil bağlantı bağlantısını birincil olarak denetleyin.

**Remoterepereptorconnectionstatus:** 
 Birincil çoğaltmadaki **System. Replicator** , ikincil (uzak) çoğaltıcının bağlantısı sağlıklı olmadığında bir uyarı bildirir. Uzak çoğaltıcı adresi raporun iletisinde gösterilir, bu, yanlış yapılandırmanın geçtiğini veya replicators arasında ağ sorunları olup olmadığını algılamaya daha kolay hale getirir.

* **SourceId**: System. Replicator
* **Özellik**: **remoterepereptorconnectionstatus**.
* **Sonraki adımlar**: hata iletisini denetleyin ve uzak çoğaltıcı adresinin doğru bir şekilde yapılandırıldığından emin olun. Örneğin, uzak çoğaltıcı "localhost" dinleme adresiyle açılırsa, dışından ulaşılamaz. Adres doğru görünüyorsa, olası ağ sorunlarını bulmak için birincil düğüm ile uzak adres arasındaki bağlantıyı denetleyin.

### <a name="replication-queue-full"></a>Çoğaltma kuyruğu dolu
**System. Replicator** , çoğaltma kuyruğu dolduğunda bir uyarı bildirir. Birincil üzerinde, bir veya daha fazla ikincil çoğaltmanın işlemleri bildirmek için yavaş olduğu için çoğaltma kuyruğu genellikle tam hale gelir. İkincil üzerinde bu durum genellikle hizmetin işlemleri uygulamak için yavaş olduğu durumlarda oluşur. Sıra artık dolduğunda uyarı temizlenir.

* **SourceId**: System. Replicator
* **Özellik**: çoğaltma rolüne bağlı olarak **primaryreplicationqueuestatus** veya **secondaryreplicationqueuestatus**.

### <a name="slow-naming-operations"></a>Yavaş adlandırma işlemleri
**System. NamingService** , bir adlandırma işlemi kabul edilebilir ' den daha uzun sürerse, birincil çoğaltmadaki sistem durumunu raporlar. Adlandırma işlemlerine örnek olarak [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) veya [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync)verilebilir. FabricClient altında daha fazla Yöntem bulunabilir. Örneğin, [hizmet yönetimi yöntemleri](/dotnet/api/system.fabric.fabricclient.servicemanagementclient) veya [özellik yönetimi yöntemleri](/dotnet/api/system.fabric.fabricclient.propertymanagementclient)altında bulunabilir.

> [!NOTE]
> Adlandırma hizmeti, hizmet adlarını kümedeki bir konuma çözer. Kullanıcılar bu hizmeti, hizmet adlarını ve özelliklerini yönetmek için kullanabilir. Bu, Service Fabric bölümlenmiş kalıcı bir hizmettir. Bölümlerden biri, tüm Service Fabric adları ve hizmetleriyle ilgili meta verileri içeren *yetkili sahibini* temsil eder. Service Fabric adları, *ad sahibi* bölümleri olarak adlandırılan farklı bölümlerle eşleştirilir, bu nedenle hizmet Genişletilebilir olur. [Adlandırma hizmeti](service-fabric-architecture.md)hakkında daha fazla bilgi edinin.
> 
> 

Bir adlandırma işlemi beklenenden uzun sürerse, işleme işlem gören adlandırma hizmeti bölümünün birincil çoğaltmasında, işlem bir uyarı raporuyla işaretlenir. İşlem başarıyla tamamlanırsa, uyarı temizlenir. İşlem bir hata ile tamamlanırsa, sistem durumu raporu hata hakkındaki ayrıntıları içerir.

* **SourceId**: System. NamingService
* **Özellik**: "**Duration_**" önekiyle başlar ve yavaş işlemi ve işlemin uygulandığı Service Fabric adını tanımlar. Örneğin, **Fabric:/MyApp/hizmetim** adlı hizmet oluşturma işlemi çok uzun sürerse, özellik **Duration_AOCreateService. Fabric:/MyApp/hizmetim**. "AO" Bu ad ve işlem için adlandırma bölümünün rolüne işaret eder.
* **Sonraki adımlar**: adlandırma işleminin neden başarısız olduğunu görmek için denetleyin. Her işlemin farklı kök nedenleri olabilir. Örneğin, silme hizmeti takılmış olabilir. Hizmet kodundaki Kullanıcı hatası nedeniyle uygulama ana bilgisayarı bir düğümde kilitlendiğinden, hizmet takılmış olabilir.

Aşağıdaki örnekte, bir hizmet oluşturma işlemi gösterilmektedir. İşlem yapılandırılan süreden daha uzun sürdü. "AO" yeniden denemeler yapın ve "Hayır" a iş gönderir. "Hayır" zaman AŞıMı ile son işlemi tamamladı. Bu durumda, aynı çoğaltma hem "AO" hem de "NO" rollerinin birincili olur.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Deployedadpplication sistem durumu raporları
**System. Hosting** , dağıtılan varlıklarda yetkilisdir.

### <a name="activation"></a>Etkinleştirme
System. bir uygulama, düğümde başarıyla etkinleştirildiğinde raporları Tamam olarak barınıyor. Aksi halde, bir hata bildirir.

* **SourceId**: System. Hosting
* **Özellik**: **etkinleştirme**, dağıtım sürümü dahil.
* **Sonraki adımlar**: uygulama sağlıksız ise, etkinleştirmenin neden başarısız olduğunu araştırın.

Aşağıdaki örnekte başarılı bir etkinleştirme gösterilmektedir:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>İndir
Uygulama paketi indirmesi başarısız olursa System. Hosting bir hata bildirir.

* **SourceId**: System. Hosting
* **Özellik**: dağıtım sürümü de dahil olmak üzere **indirme**.
* **Sonraki adımlar**: düğümde indirmenin neden başarısız olduğunu araştırın.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage sistem durumu raporları
**System. Hosting** , dağıtılan varlıklarda yetkilisdir.

### <a name="service-package-activation"></a>Hizmet paketi etkinleştirme
Node üzerinde hizmet paketi etkinleştirmesi başarılı olursa sistem., raporları Tamam olarak barındırıyor. Aksi halde, bir hata bildirir.

* **SourceId**: System. Hosting
* **Özellik**: etkinleştirme.
* **Sonraki adımlar**: etkinleştirmenin neden başarısız olduğunu araştırın.

### <a name="code-package-activation"></a>Kod paketi etkinleştirme
Etkinleştirme başarılı olursa, her kod paketi için sistem. Etkinleştirme başarısız olursa, yapılandırılan bir uyarı bildirir. **CodePackage** , yapılandırılmış **CodePackageHealthErrorThreshold** daha büyük bir hata ile etkinleştirilemez veya sonlanamazsa, barındırma bir hata bildirir. Bir hizmet paketi birden çok kod paketi içeriyorsa, her biri için bir etkinleştirme raporu oluşturulur.

* **SourceId**: System. Hosting
* **Özellik**: **codepackageactivation** önekini kullanır ve kod paketinin adını ve giriş noktasını *Codepackageactivation: codepackagename: setupentrypoint/EntryPoint* olarak içerir. Örneğin, **Codepackageactivation: Code: SetupEntryPoint**.

### <a name="service-type-registration"></a>Hizmet türü kaydı
System., hizmet türü başarıyla kaydedilmişse, raporları Tamam olarak barındırıyor. Kayıt, **Servicetyperyumuristrationtimeout** kullanılarak yapılandırıldığı şekilde zamanında yapılmadıysa bir hata bildirir. Çalışma zamanı kapalıysa, hizmet türünün düğümünden kaydı yapılır ve bir uyarı raporlar.

* **SourceId**: System. Hosting
* **Özellik**: **ServiceTypeRegistration** önekini kullanır ve hizmet türü adını içerir. Örneğin, **ServiceTypeRegistration: Filestorezervicetype**.

Aşağıdaki örnekte sağlıklı olarak dağıtılan bir hizmet paketi gösterilmektedir:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>İndir
System. Hosting, hizmet paketi indirmesi başarısız olursa bir hata bildiriyor.

* **SourceId**: System. Hosting
* **Özellik**: dağıtım sürümü de dahil olmak üzere **indirme**.
* **Sonraki adımlar**: düğümde indirmenin neden başarısız olduğunu araştırın.

### <a name="upgrade-validation"></a>Yükseltme doğrulaması
System. Hosting, yükseltme sırasında doğrulama başarısız olursa veya yükseltme düğümde başarısız olursa bir hata bildirir.

* **SourceId**: System. Hosting
* **Özellik**: **fabricupgradevalidation** önekini kullanır ve yükseltme sürümünü içerir.
* **Açıklama**: karşılaşılan hatayı işaret eder.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Kaynak idare ölçümleri için tanımsız düğüm kapasitesi
System. Hosting, düğüm kapasiteleri küme bildiriminde tanımlanmamışsa ve otomatik algılama yapılandırması kapatılırsa bir uyarı bildirir. Service Fabric, belirtilen bir düğümde [Resource idare](service-fabric-resource-governance.md) kaydı kullanan hizmet paketi her seferinde bir sistem durumu uyarısı başlatır.

* **SourceId**: System. Hosting
* **Özellik**: **resourceidare**.
* **Sonraki adımlar**: Bu sorunu aşmak için tercih edilen yol, kullanılabilir kaynakların otomatik olarak algılanmasını sağlamak üzere küme bildirimini değiştirkullanmaktır. Bu ölçümler için, küme bildirimini doğru belirtilen düğüm kapasiteleri ile güncelleştirmek daha farklı bir yoldur.

## <a name="next-steps"></a>Sonraki adımlar
* [Service Fabric sistem durumu raporlarını görüntüleme](service-fabric-view-entities-aggregated-health.md)

* [Hizmet durumunu raporlama ve denetleme](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Hizmetleri yerel olarak izleme ve tanılama](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Uygulama yükseltmesini Service Fabric](service-fabric-application-upgrade.md)
