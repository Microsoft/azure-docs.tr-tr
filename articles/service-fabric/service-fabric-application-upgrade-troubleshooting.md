---
title: Uygulama yükseltmeleri sorunlarını giderme | Microsoft Docs
description: Bu makalede, bir Service Fabric uygulamasının yükseltilme ve bunların nasıl çözümleneceği konularında karşılaşılan bazı yaygın sorunlar ele alınmaktadır.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: f5df528c7e46a5cb2a5df98f0088a451eb08cd6a
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167527"
---
# <a name="troubleshoot-application-upgrades"></a>Uygulama yükseltme ile ilgili sorunları giderme

Bu makalede, bir Azure Service Fabric uygulamasını yükseltme ve bunları çözme konularında karşılaşılan bazı yaygın sorunlar ele alınmaktadır.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Başarısız bir uygulama yükseltmesinde sorun giderme

Bir yükseltme başarısız olduğunda, **Get-ServiceFabricApplicationUpgrade** komutunun çıkışı hata ayıklama için ek bilgiler içerir.  Aşağıdaki liste, ek bilgilerin nasıl kullanılabileceğini belirtir:

1. Hata türünü belirler.
2. Hata nedenini belirler.
3. Daha fazla araştırma için bir veya daha fazla başarısız bileşeni yalıtın.

Bu bilgiler, Service Fabric **FailureAction** 'ın yükseltmeyi geri alıp almamasından bağımsız olarak hatayı algıladığında kullanılabilir.

### <a name="identify-the-failure-type"></a>Hata türünü tanımla

**Get-ServiceFabricApplicationUpgrade**çıkışında, **failuretimestamp UTC** , Service Fabric tarafından bir yükseltme hatasının ALGıLANDıĞı zaman damgasını (UTC olarak) tanımlar ve **FailureAction** tetiklenir. **FailureReason** , başarısız olan üç olası yüksek düzey nedenden birini tanımlar:

1. UpgradeDomainTimeout-belirli bir yükseltme etki alanının tamamlanması çok uzun sürdüğü ve **Upgradedomaintimeout** süresinin dolmadığını gösterir.
2. OverallUpgradeTimeout-genel yükseltmenin tamamlanması çok uzun sürdüğü ve **Upgradetimeout** süresinin dolmadığını gösterir.
3. HealthCheck-bir güncelleştirme etki alanı yükseltildikten sonra uygulamanın, belirtilen sistem durumu ilkelerine göre sağlıksız olarak kaldığını ve **Healthcheckretrytimeout** süresinin dolmadığını belirtir.

Bu girişler yalnızca yükseltme başarısız olduğunda çıktıyı gösterir ve geri alma işlemi başlar. Hatanın türüne bağlı olarak daha fazla bilgi görüntülenir.

### <a name="investigate-upgrade-timeouts"></a>Yükseltme zaman aşımlarını araştır

Yükseltme zaman aşımı hataları genellikle hizmet kullanılabilirliği sorunlarından kaynaklanır. Bu paragrafı izleyen çıkış, hizmet çoğaltmalarının veya örneklerinin yeni kod sürümünde başlamamasının gerçekleştiği tipik yükseltmelerdir. **UpgradeDomainProgressAtFailure** alanı, hata sırasında bekleyen herhangi bir yükseltme işinin anlık görüntüsünü yakalar.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

Bu örnekte, yükseltme etki alanı *MYUD1* üzerinde başarısız oldu ve iki bölüm (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* ve *4b43f4d8-b26b-424e-9307-7a7a62e79750*) takıldı. Çalışma zamanı, *Düğüm1* ve *Düğüm4*hedef düğümlerine birincil çoğaltmaları (*waitforprimaryyerleştirmesini*) yerleştiremediği için bölümler takılmıştı.

**Get-ServiceFabricNode** komutu, bu iki düğümün yükseltme etki alanı *MYUD1*' de olduğunu doğrulamak için kullanılabilir. *Yükseltilebilir depette* , yükseltme etki alanındaki tüm düğümlerin yükseltme işlemi tamamlandıktan sonra bu güvenlik denetimlerinin meydana geldiğini gösteren *Postupgradesafetycheck*diyor. Tüm bu bilgiler, uygulama kodunun yeni sürümüyle ilgili olası bir sorunu işaret eder. En yaygın sorunlar, birincil kod yollarındaki açık veya yükseltme içindeki hizmet hatalarıdır.

*Preupgradesafetycheck* 'in *yükselmiş* olması, yükseltme etki alanının gerçekleştirilmeden önce hazırlanmasında sorun olduğunu gösterir. Bu durumda en yaygın sorunlar, birincil kod yollarından yakın veya İndirgemedeki hizmet hatalarıdır.

Geçerli **Upgradestate** , geri alma işlemi başarısız *oldu, bu*nedenle özgün yükseltmenin hata durumunda yükseltmeyi otomatik olarak geri alan bir Rollback **FailureAction**ile gerçekleştirilmiş olması gerekir. Özgün yükseltme el ile bir **FailureAction**ile gerçekleştirilirse, uygulamanın canlı hata ayıklamasına izin vermek için yükseltme askıya alınmış durumda olur.

Nadir durumlarda, sistem geçerli yükseltme etki alanı için tüm işleri tamamlarsa, genel yükseltmenin tam olarak zaman aşımına uğrayışında **UpgradeDomainProgressAtFailure** alanı boş olabilir. Bu durumda, **upgradetimeout** ve **upgradedomaintimeout** yükseltme parametre değerlerini artırmayı deneyin ve yükseltmeyi yeniden deneyin.

### <a name="investigate-health-check-failures"></a>Sistem durumu denetimi başarısızlıklarını araştır

Durum denetimi hataları, bir yükseltme etki alanındaki tüm düğümler yükseltmeyi tamamladıktan ve tüm güvenlik denetimlerini geçirdikten sonra oluşabilecek çeşitli sorunlar tarafından tetiklenebilir. Bu paragrafı izleyen çıkış, başarısız durum denetimleri nedeniyle bir yükseltme hatasının tipik bir noktadır. **Unhealthyevaluations** alanı, belirtilen [sistem durumu ilkesine](service-fabric-health-introduction.md)göre yükseltme sırasında başarısız olan sistem durumu denetimlerinin anlık görüntüsünü yakalar.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Sistem durumu denetimi hatalarının araştırmasından önce Service Fabric durum modelinin anlaşılmasına gerek vardır. Bunun yanı sıra, bu tür derinlemesine bir bilgi sahibi olmak zorunda kalmadan, iki hizmetin sağlıksız olduğunu görebiliriz: *Fabric:/DemoApp/Svc3* ve *Fabric:/DemoApp/Svc2*, hata durumu raporlarının yanı sıra (Bu durumda "ınjectedfault"). Bu örnekte, en fazla dört hizmet sağlam değildir ve bu,% 0 ' ın varsayılan hedefinin altında (*Maxyüztunhealthi Hizmetleri*) oluşur.

Yükseltme işlemi başlatılırken el ile yapılan bir **FailureAction** belirtilerek yükseltme işlemi başarısız olduğunda askıya alındı. Bu mod, başka bir işlem yapmadan önce, başarısız durumunda canlı sistemi araştırmamızı sağlar.

### <a name="recover-from-a-suspended-upgrade"></a>Askıya alınmış bir yükseltmeden kurtarma

Bir geri alma **FailureAction**ile, yükseltme otomatik olarak başarısız olduğunda geri alındığında gerekli bir kurtarma yoktur. El ile bir **FailureAction**ile birkaç kurtarma seçeneği vardır:

1.  geri alma tetikleyin
2. Yükseltmenin geri kalanında el ile devam edin
3. İzlenen yükseltmeyi sürdürür

**Start-ServiceFabricApplicationRollback** komutu, uygulamayı geri almaya başlamak için dilediğiniz zaman kullanılabilir. Komut başarılı bir şekilde döndüğünde geri alma isteği sisteme kaydedilir ve kısa süre sonra başlar.

**Sürdürme-ServiceFabricApplicationUpgrade** komutu, tek seferde bir yükseltme etki alanı olan yükseltmenin geri kalanında el ile devam etmek için kullanılabilir. Bu modda, sistem tarafından yalnızca güvenlik denetimleri gerçekleştirilir. Daha fazla durum denetimi yapılmaz. Bu komut yalnızca *Upgradestate* , geçerli yükseltme etki alanının yükseltmeyi bitirdiğini, ancak bir sonraki birinin başlatılmadığını (bekleyen *) göstermediği*durumlarda kullanılabilir.

**Güncelleştirme-ServiceFabricApplicationUpgrade** komutu, hem güvenlik hem de durum denetimleri gerçekleştirilmesiyle izlenen yükseltmeyi sürdürmekte kullanılabilir.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Yükseltme, son askıya alındığı yükseltme etki alanından devam eder ve daha önce olduğu gibi aynı yükseltme parametrelerini ve sistem durumu ilkelerini kullanır. Gerekirse, önceki çıktıda gösterilen yükseltme parametreleri ve sistem durumu ilkeleri, yükseltme devam ettiğinde aynı komutta değiştirilebilir. Bu örnekte, yükseltme, parametreler ve sistem durumu ilkeleri değiştirilmeden Izlenen modda sürdürüldü.

## <a name="further-troubleshooting"></a>Daha fazla sorun giderme

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric belirtilen sistem durumu ilkelerini takip etmez

Olası neden 1:

Service Fabric, tüm yüzdeleri sistem durumu değerlendirmesi için gerçek varlık numaralarına (örneğin çoğaltmalar, bölümler ve hizmetler) çevirir ve her zaman tüm varlıklara yuvarlanır. Örneğin, en fazla *Maxyüztunhealthyıreplicasperpartition* değeri% 21 ise ve beş çoğaltma varsa Service Fabric, en fazla sağlıksız çoğaltmaya (yani, `Math.Ceiling (5*0.21)`) izin verir. Bu nedenle, sistem durumu ilkeleri uygun şekilde ayarlanmalıdır.

Olası neden 2:

Sistem durumu ilkeleri, belirli hizmet örneklerinin değil, toplam hizmet yüzdeleriyle belirtilmiştir. Örneğin, bir yükseltmeden önce, bir uygulamanın dört hizmet örneği varsa, B, C ve D ise, hizmet D 'nin sağlıksız olduğu ancak uygulamaya çok az etkisi olan. Yükseltme sırasında sağlıksız olan sağlıklı hizmeti D 'yi yoksaymak ve *Maxyüztunhealthyıservices* parametresini yalnızca A, B ve C 'nin sağlıklı olması gerektiğini varsayarak% 25 olarak ayarlamak istiyoruz.

Bununla birlikte, yükseltme sırasında C iyi durumda olduğunda D iyi hale gelebilir. Hizmetin yalnızca% 25 ' i sağlıksız olduğu için yükseltme işlemi yine de başarılı olur. Ancak, C yerine beklenmedik şekilde sağlıksız olması nedeniyle beklenmeyen hatalara neden olabilir. Bu durumda, D, B ve C 'den farklı bir hizmet türü olarak modellenmelidir. Sistem durumu ilkeleri hizmet türü başına belirtildiğinden, farklı hizmetlere uygun olmayan farklı yüzde eşikleri uygulanabilir. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Uygulama yükseltme için bir sistem durumu ilkesi belirtmedi, ancak hiçbir zaman belirtilmediği zaman aşımları için yükseltme yine de başarısız oluyor

Durum ilkeleri yükseltme isteğine sağlanmazsa, geçerli uygulama sürümünün *ApplicationManifest. xml* dosyasından alınır. Örneğin, sürüm 1,0 ' den sürüm 2,0 ' ye yükseltme yapıyorsanız, sürüm 1,0 ' de için belirtilen uygulama sistem durumu ilkeleri kullanılır. Yükseltme için farklı bir sistem durumu ilkesi kullanılması gerekiyorsa, ilkenin uygulama yükseltme API 'SI çağrısının bir parçası olarak belirtilmesi gerekir. API çağrısının bir parçası olarak belirtilen ilkeler yalnızca yükseltme sırasında geçerlidir. Yükseltme tamamlandıktan sonra, *ApplicationManifest. xml* dosyasında belirtilen ilkeler kullanılır.

### <a name="incorrect-time-outs-are-specified"></a>Yanlış zaman aşımları belirtildi

Zaman aşımları sürekli olarak ayarlandığında ne olacağını merak etmiş olabilirsiniz. Örneğin, *Upgradedomaintimeout*öğesinden daha az bir *upgradetimeout* olabilir. Yanıt bir hata döndürülür. *Upgradedomaintimeout* , *healthcheckwaitduration* ve *healthcheckretrytimeout*toplamından daha azsa veya *Upgradedomaintimeout* , *healthcheckwaitduration* 'ın toplamından azsa ve *Healthcheckstableduration*.

### <a name="my-upgrades-are-taking-too-long"></a>Yükseltmelerim çok uzun sürüyor

Yükseltmenin tamamlanma süresi, belirtilen sistem durumu denetimlerine ve zaman aşımlarını bağlıdır. Durum denetimleri ve zaman aşımları, uygulamayı kopyalama, dağıtma ve sabitme süresini temel alır. Zaman aşımları ile çok ısrarlı olması, daha fazla başarısız yükseltmeler anlamına gelebilir, bu nedenle daha uzun zaman aşımları ile daha fazla koruma önerilir.

Aşağıda, zaman aşımının yükseltme süreleriyle nasıl etkileşime gireceğini gösteren hızlı bir yenileyici verilmiştir:

Yükseltme etki alanına yönelik yükseltmeler *Healthcheckwaitduration* + *Healthcheckstableduration*değerinden daha hızlı tamamlanamaz.

Yükseltme hatası, *Healthcheckwaitduration* + *Healthcheckretrytimeout*değerinden daha hızlı bir şekilde oluşamaz.

Yükseltme etki alanı için yükseltme saati, *Upgradedomaintimeout*ile sınırlıdır.  *Healthcheckretrytimeout* ve *Healthcheckstableduration* her ikisi de sıfır değil ve uygulamanın sistem durumu geri ve ileri geçiş devam ederse, yükseltme son kez *upgradedomaintimeout*tarihinde zaman aşımına uğrar. Geçerli yükseltme etki alanı için yükseltme başladıktan sonra *Upgradedomaintimeout, bir süre sonra zaman aşımına uğramaya* başlar.

## <a name="next-steps"></a>Sonraki adımlar

[Visual Studio 'Yu kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial.md) , Visual Studio kullanarak bir uygulama yükseltme işleminde size yol gösterir.

[PowerShell kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial-powershell.md) , PowerShell kullanarak bir uygulama yükseltme işleminde size yol gösterir.

Uygulamanızın [yükseltme parametrelerini](service-fabric-application-upgrade-parameters.md)kullanarak nasıl yükseltileceğini denetleyin.

[Veri serileştirmesini](service-fabric-application-upgrade-data-serialization.md)nasıl kullanacağınızı öğrenerek uygulamanızın yükseltmelerini uyumlu hale getirin.

[Gelişmiş konulara](service-fabric-application-upgrade-advanced.md)başvurarak uygulamanızı yükseltirken gelişmiş işlevselliği nasıl kullanacağınızı öğrenin.