---
title: Azure Service Fabric üretim hazırlığı denetim listesi | Microsoft Docs
description: En iyi yöntemleri izleyerek Service Fabric uygulamanızı ve küme üretimini hazırlayın.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/05/2019
ms.author: atsenthi
ms.openlocfilehash: 9e86f7306ee70bee2e084b967867e2a9be5b66e1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599363"
---
# <a name="production-readiness-checklist"></a>Üretim hazırlığı denetim listesi

Uygulamanız ve kümeniz üretim trafiği almaya hazırlanıyor mi? Uygulamanızı ve kümenizi çalıştırmak ve test etmek, üretime geçmeye başlamaya başlamaya yönelik değildir. Aşağıdaki denetim listelerine giderek uygulamanızın ve kümenizin sorunsuz çalışmasını sağlayın. Tüm bu öğelerin kullanıma alınmasını önemle öneririz. Kuşkusuz, belirli bir satır öğesi için alternatif çözümler kullanmayı seçebilirsiniz (örneğin, kendi tanılama çerçevelerinizin).


## <a name="prerequisites-for-production"></a>Üretime yönelik önkoşullar
1. Azure Service Fabric en iyi uygulamalar: [Uygulama tasarımı](./service-fabric-best-practices-applications.md), [güvenlik](./service-fabric-best-practices-security.md), [ağ](./service-fabric-best-practices-networking.md), [Kapasite planlama ve ölçekleme](./service-fabric-best-practices-capacity-scaling.md), [kod olarak altyapı](./service-fabric-best-practices-infrastructure-as-code.md)ve [izleme ve tanılama](./service-fabric-best-practices-monitoring.md). 
1. Aktör programlama modelini kullanıyorsanız Reliable Actors güvenlik yapılandırmasını Uygula
1. 20 ' den fazla çekirdeğe veya 10 düğüme sahip kümeler için sistem hizmetleri için ayrılmış bir birincil düğüm türü oluşturun. Sistem hizmetlerinin birincil düğüm türünü ayırmak için [yerleştirme kısıtlamaları](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) ekleyin.
1. Birincil düğüm türü için bir D2v2 veya daha yüksek SKU kullanın. En az 50 GB sabit disk kapasitesine sahip bir SKU seçmeniz önerilir.
1. Üretim kümeleri [güvenli](service-fabric-cluster-security.md)olmalıdır. Güvenli küme ayarlamaya ilişkin bir örnek için, bu [küme şablonuna](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG)bakın. Sertifikalar için ortak adları kullanın ve kendinden imzalı sertifikaları kullanmaktan kaçının.
1. Düğümlerde [ve hizmetlerde kaynak kısıtlamalarını](service-fabric-resource-governance.md)ekleyerek düğüm kaynaklarının% 75 ' inden fazlasını tüketmez. 
1. [Dayanıklılık düzeyini](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)anlayın ve ayarlayın. Durum bilgisi olan iş yüklerini çalıştıran düğüm türleri için gümüş veya daha yüksek dayanıklılık düzeyi önerilir. Birincil düğüm türü, gümüş veya daha yüksek bir dayanıklılık düzeyine ayarlanmış olmalıdır.
1. Düğüm türünün [güvenilirlik düzeyini](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) anlayın ve seçin. Gümüş veya daha yüksek güvenilirlik önerilir.
1. Kümenizin [kapasite gereksinimlerini](service-fabric-cluster-capacity.md) belirlemek için iş yüklerinizi test edin ve ölçeklendirin. 
1. Hizmetleriniz ve uygulamalarınız izlenir ve uygulama günlükleri, uyarı ile oluşturulur ve depolanır. Örneğin, bkz. [Service Fabric uygulamanıza günlük ekleme](service-fabric-how-to-diagnostics-log.md) ve [Azure Izleyici günlükleriyle kapsayıcıları izleme](service-fabric-diagnostics-oms-containers.md).
1. Küme, uyarı ile izlenir (örneğin, [Azure izleyici günlükleri](service-fabric-diagnostics-event-analysis-oms.md)ile). 
1. Temeldeki sanal makine ölçek kümesi altyapısı, uyarı ile izlenir (örneğin, [Azure izleyici günlükleri](service-fabric-diagnostics-oms-agent.md)ile).
1. Küme, her zaman [birincil ve ikincil sertifikalara](service-fabric-cluster-security-update-certs-azure.md) sahiptir (Bu nedenle kilitlenemez).
1. Geliştirme, hazırlık ve üretim için ayrı kümelerin bakımını yapın. 
1. [Uygulama yükseltmeleri](service-fabric-application-upgrade.md) ve [küme yükseltmeleri](service-fabric-tutorial-upgrade-cluster.md) öncelikle geliştirme ve hazırlama kümelerinde test edilir. 
1. Üretim kümelerinde Otomatik yükseltmeleri devre dışı bırakın ve geliştirme ve hazırlama kümeleri (gerektiğinde geri al) için açın. 
1. Hizmetiniz için bir kurtarma noktası hedefi (RPO) oluşturun ve bir [olağanüstü durum kurtarma işlemi](service-fabric-disaster-recovery.md) kurun ve test edin.
1. Kümenizi el ile veya programlama yoluyla [ölçeklendirmeyi](service-fabric-cluster-scaling.md) planlayın.
1. Küme düğümleriniz için [Düzeltme Eki uygulamayı](service-fabric-patch-orchestration-application.md) planlayın. 
1. En son değişikliklerinizin sürekli olarak test edileceği bir CI/CD işlem hattı oluşturun. Örneğin, [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) veya [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md) kullanma
1. [Hata analizi hizmeti](service-fabric-testability-overview.md) ve Ise kontrollü [Chaos](service-fabric-controlled-chaos.md)ile yük altında geliştirme & hazırlama kümelerinizi test edin. 
1. Uygulamalarınızı [ölçeklendirmeye](service-fabric-concepts-scalability.md) yönelik plan yapın. 


Service Fabric Reliable Services veya Reliable Actors programlama modeli kullanıyorsanız, aşağıdaki öğelerin işaretli olması gerekir:
1. Yerel geliştirme sırasında uygulamaları yükselterek hizmet kodunuzun, `RunAsync` yöntemde iptal belirtecini ele geçirin ve özel iletişim dinleyicilerini kapatmadığını denetleyin.
1. Güvenilir koleksiyonlar kullanırken [yaygın olarak karşılaşılan alt sınırın](service-fabric-work-with-reliable-collections.md) önüne kaçının.
1. Yük testlerini çalıştırırken .NET CLR bellek performansı sayaçlarını izleyin ve yüksek hızların çöp toplama veya ard arda büyümesini kontrol edin.
1. [Reliable Services ve Reliable Actors](service-fabric-reliable-services-backup-restore.md) çevrimdışı yedeklemesini koruyun ve geri yükleme işlemini test edin.
1. Birincil NodeType sanal makine örnek sayınız, kümeleriniz için en düşük düzeye eşit olmalıdır; Katman en düşük değeri aşmaya uygun olduğunda koşullar şunlardır: birincil NodeTypes sanal makine ölçek kümesi SKU 'sunda dikey olarak ölçeklendirirken geçici olarak.

## <a name="optional-best-practices"></a>İsteğe bağlı en iyi uygulamalar

Yukarıdaki listeler üretime gitmek için ön koşullar olsa da aşağıdaki öğeler de göz önünde bulundurulmalıdır:
1. Yerleşik sistem durumu değerlendirmesini ve raporlamayı genişletmek için [Service Fabric sistem durumu modelini](service-fabric-health-introduction.md) takın.
1. [Kaynak Dengeleme](service-fabric-cluster-resource-manager-balancing.md)için uygulamanızı ve raporların [yükünü](service-fabric-cluster-resource-manager-metrics.md) izleyen özel bir izleme dağıtın. 


## <a name="next-steps"></a>Sonraki adımlar
* [Service Fabric Windows kümesi dağıtma](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Service Fabric Linux kümesi dağıtma](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Service Fabric [uygulama yaşam döngüsü](service-fabric-application-lifecycle.md) hakkında bilgi edinin.
