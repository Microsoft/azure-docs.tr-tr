---
title: Cloud Services ve Service Fabric arasındaki farklar
description: Cloud Services 'ten Service Fabric uygulamasına uygulama geçirmeye yönelik kavramsal genel bakış.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: c7e7d346b5a39a262d1d55265becadb1c718cc04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575780"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Uygulamaları geçirmeden önce Cloud Services ve Service Fabric arasındaki farklar hakkında bilgi edinin.
Microsoft Azure Service Fabric, yüksek düzeyde ölçeklenebilir, yüksek oranda güvenilir dağıtılmış uygulamalar için yeni nesil bulut uygulama platformudur. Dağıtılmış bulut uygulamalarının paketlenmesi, dağıtılması, yükseltilmesi ve yönetilmesi için birçok yeni özellik sunar. 

Bu, Cloud Services uygulamaları Service Fabric 'e geçirmeye yönelik tanıtım kılavuzudur. Birincil olarak Cloud Services ve Service Fabric arasındaki mimari ve tasarım farklarına odaklanır.

## <a name="applications-and-infrastructure"></a>Uygulamalar ve altyapı
Cloud Services ve Service Fabric arasında temel bir farklılık, VM 'Ler, iş yükleri ve uygulamalar arasındaki ilişkidir. Buradaki bir iş yükü, belirli bir görevi gerçekleştirmek veya bir hizmet sağlamak için yazdığınız kod olarak tanımlanır.

* **Cloud Services, uygulamaları VM olarak dağıtma ile ilgilidir.** Yazdığınız kod, Web veya çalışan rolü gibi bir sanal makine örneğiyle sıkı bir şekilde bağlanmış. Cloud Services bir iş yükünü dağıtmak için, iş yükünü çalıştıran bir veya daha fazla sanal makine örneği dağıtmaktır. Uygulama ve VM 'lerin bir ayrımı yoktur ve bu nedenle bir uygulamanın resmi bir tanımı yoktur. Bir uygulama, bir Cloud Services dağıtımı içinde Web veya çalışan rolü örnekleri olarak veya tüm Cloud Services dağıtımı olarak düşünülebilir. Bu örnekte, bir uygulama bir rol örnekleri kümesi olarak gösterilir.

![Uygulamaları ve topolojiyi Cloud Services][1]

* **Service Fabric, mevcut VM 'lere veya Windows ya da Linux üzerinde Service Fabric çalıştıran makinelere uygulama dağıtmaya yönelik.** Yazdığınız hizmetler temel altyapıdan tamamen ayrılır, bu da Service Fabric uygulama platformu tarafından soyutlanarak bir uygulama birden çok ortama dağıtılabilir. Service Fabric bir iş yüküne "hizmet" denir ve bir veya daha fazla hizmet, Service Fabric uygulama platformunda çalışan, resmi olarak tanımlanmış bir uygulamada gruplandırılır. Birden çok uygulama tek bir Service Fabric kümesine dağıtılabilir.

![Uygulamaları ve topolojiyi Service Fabric][2]

Service Fabric, Windows veya Linux üzerinde çalışan bir uygulama platformu katmanıdır, Cloud Services, Azure tarafından yönetilen VM 'Leri bağlı iş yükleriyle dağıtmaya yönelik bir sistemdir.
Service Fabric uygulama modelinde birçok avantaj vardır:

* Hızlı dağıtım süreleri. VM örneklerinin oluşturulması zaman alabilir. Service Fabric, VM 'Ler, Service Fabric uygulama platformunu barındıran bir küme oluşturmak için yalnızca bir kez dağıtılır. Bu noktadan itibaren uygulama paketleri kümeye çok hızlı dağıtılabilir.
* Yüksek yoğunluklu barındırma. Cloud Services, bir çalışan rolü VM bir iş yükü barındırır. Service Fabric, uygulamalar onları çalıştıran VM 'lerden ayrıdır, yani çok sayıda uygulamayı az sayıda VM 'ye dağıtabilmeniz ve bu da daha büyük dağıtımlar için genel maliyetten daha düşük maliyetli olabilir.
* Service Fabric platformu, Azure 'un veya şirket içinde olsun, Windows Server veya Linux makineleri olan her yerde çalıştırılabilir. Platform, uygulamanızın farklı ortamlarda çalışabilmesi için temel alınan altyapıda bir Özet katman sağlar. 
* Dağıtılmış uygulama yönetimi. Service Fabric, yalnızca dağıtılmış uygulamaları barındırmayan, aynı zamanda yaşam döngüsünün barındırma VM veya makine yaşam döngüsünün bağımsız olarak yönetilmesine yardımcı olan bir platformdur.

## <a name="application-architecture"></a>Uygulama mimarisi
Bir Cloud Services uygulamasının mimarisi genellikle Service Bus, Azure Tablo ve BLOB depolama, SQL, Redis ve bir uygulamanın durumunu ve verilerini yönetmek için bir Cloud Services dağıtımında Web ve çalışan rolleri arasında iletişim gibi çok sayıda dış hizmet bağımlılığı içerir. Cloud Services bir uygulamanın tamamı şöyle görünebilir:  

![Cloud Services mimarisi][9]

Service Fabric uygulamalar Ayrıca, aynı dış Hizmetleri tüm uygulamalarda kullanmayı da tercih edebilir. Bu örnek Cloud Services mimaride, Cloud Services en basit geçiş yolu Service Fabric ' e kadar olan en basit geçiş yoludur. Service Fabric bir uygulamayla yalnızca Cloud Services dağıtımını değiştirmek, genel mimarinin aynı kalmasını sağlar. Web ve çalışan rollerine, en az kod değişiklikleriyle ilgili olmayan Service Fabric hizmetler eklenebilir.

![Basit geçişten sonra Service Fabric mimarisi][10]

Bu aşamada, sistem daha önce olduğu gibi çalışmaya devam etmelidir. Service Fabric durum bilgisi olan özelliklerden yararlanarak dış durum depoları, uygun olduğunda durum bilgisi olan hizmetler olarak internalized olabilir. Bu, Web ve çalışan rollerinin basit bir şekilde (durum bilgisi olmayan hizmetler Service Fabric), ancak dış hizmetler daha önce olduğundan uygulamanıza eşdeğer işlevler sağlayan özel hizmetler yazılmasını gerektirdiğinden daha karmaşıktır. Bunu yapmanın avantajları şunlardır: 

* Dış bağımlılıklar kaldırılıyor 
* Dağıtım, yönetim ve yükseltme modellerini birleştirme. 

Bu hizmetleri elde etmek için ortaya çıkan bir mimari aşağıdaki gibi görünebilir:

![Tam geçişten sonra Service Fabric mimarisi][11]

## <a name="communication-and-workflow"></a>İletişim ve iş akışı
Çoğu bulut hizmeti uygulaması birden çok katmandan oluşur. Benzer şekilde, bir Service Fabric uygulama birden fazla hizmetten (genellikle birçok hizmet) oluşur. İki yaygın iletişim modeli doğrudan iletişimdir ve dış dayanıklı bir depolama aracılığıyla yapılır.

### <a name="direct-communication"></a>Doğrudan iletişim
Doğrudan iletişim sayesinde, Katmanlar her katman tarafından kullanıma sunulan uç nokta aracılığıyla doğrudan iletişim kurabilir. Cloud Services gibi durum bilgisi olmayan ortamlarda bu, yük dengeleme için rastgele veya hepsini bir kez deneme ve kendi uç noktasına doğrudan bağlanma gibi bir VM rolü örneğinin seçilmesi anlamına gelir.

![Doğrudan iletişim Cloud Services][5]

 Doğrudan iletişim, Service Fabric ortak bir iletişim modelidir. Service Fabric ve Cloud Services arasındaki önemli fark, bir hizmete bağlandığınız Service Fabric bir sanal makineye Cloud Services. Bu, birkaç nedenden dolayı önemli bir ayırım örneğidir:

* Service Fabric hizmetler, bunları barındıran VM 'lere bağlanmamış; Hizmetler kümede hareket edebilir ve aslında çeşitli nedenlerle taşınmasının beklenirken, kaynak Dengeleme, yük devretme, uygulama ve altyapı yükseltmeleri ve yerleştirme ya da yükleme kısıtlamaları vardır. Bu, hizmet örneğinin adresinin istediği zaman değiştirebileceği anlamına gelir. 
* Service Fabric bir VM, her biri benzersiz uç noktalarla birden çok hizmeti barındırabilir.

Service Fabric, hizmetlerin uç nokta adreslerini çözümlemek için kullanılabilecek Adlandırma Hizmeti olarak adlandırılan bir hizmet bulma mekanizması sağlar. 

![Service Fabric, hizmetlerin uç nokta adreslerini çözümlemek için kullanılabilecek Adlandırma Hizmeti olarak adlandırılan bir hizmet bulma mekanizmasını nasıl sağladığını gösteren diyagram.][6]

### <a name="queues"></a>Kuyruklar
Cloud Services gibi durum bilgisi olmayan ortamlarda Katmanlar arasındaki ortak bir iletişim mekanizması, çalışma görevlerini bir katmandan diğerine aktarmak için bir dış depolama kuyruğu kullanmaktır. Yaygın bir senaryo, Azure kuyruğuna iş gönderen veya çalışan rolü örneklerinin işleri sıradan ve işleyebildiği Service Bus bir Web katmandır.

![Cloud Services kuyruğu iletişimi][7]

Aynı iletişim modeli Service Fabric kullanılabilir. Bu, mevcut bir Cloud Services uygulamasını Service Fabric geçirirken yararlı olabilir. 

![Doğrudan iletişim Service Fabric][8]

## <a name="parity"></a>Parity
[Cloud Services, denetim ve kullanım kolaylığı açısından Service Fabric benzerdir, ancak artık eski bir hizmettir ve yeni geliştirme için Service Fabric önerilir](/azure/architecture/guide/technology-choices/compute-decision-tree). Aşağıda bir API karşılaştırması verilmiştir:


| **Bulut hizmeti API 'SI** | **Service Fabric API 'SI** | **Notlar** |
| --- | --- | --- |
| Roleınstance. GetId | FabricRuntime. GetNodeContext. NodeId veya. NodeName | ID, Düğü'nin bir özelliğidir |
| Roleınstance. GetFaultDomain | FabricClient. QueryManager. GetNodeList | Düğüye filtre uygulayın ve FD özelliğini kullanın |
| Roleınstance. GetUpgradeDomain | FabricClient. QueryManager. GetNodeList | Düğüye filtre uygulayın ve yükseltme özelliğini kullanın |
| Roleınstance. Getınstanceendpoints | FabricRuntime. GetActivationContext veya Naming (ResolveService) | Hem FabricRuntime. GetActivationContext hem de ' de Serviceınitializationparameters. CodePackageActivationContext aracılığıyla bulunan çoğaltmalar içinde sunulan CodePackageActivationContext. Öbek |
| RoleEnvironment. GetRoles | FabricClient. QueryManager. GetNodeList | Türe göre aynı filtreleme sıralamasını yapmak istiyorsanız, FabricClient. ClusterManager. GetClusterManifest aracılığıyla küme bildiriminden düğüm türleri listesini alabilir ve buradan rol/düğüm türlerini alabilirsiniz. |
| RoleEnvironment. Getısavailable | Belirli bir düğüme işaret eden bir FabricRuntime Connect-WindowsFabricCluster veya oluşturun | * |
| RoleEnvironment. GetLocalResource | CodePackageActivationContext. log/Temp/Work | * |
| RoleEnvironment. Getcurrentrotaınstance | CodePackageActivationContext. log/Temp/Work | * |
| LocalResource. GetRootPath | CodePackageActivationContext. log/Temp/Work | * |
| Role. GetInstances | FabricClient. QueryManager. GetNodeList veya ResolveService | * |
| Roleınstanceendpoint. Getıpendpoint | FabricRuntime. GetActivationContext veya Naming (ResolveService) | * |

## <a name="next-steps"></a>Sonraki Adımlar
Cloud Services ' den Service Fabric en basit geçiş yolu, yalnızca Cloud Services dağıtımının bir Service Fabric uygulamayla değiştirilmesini ve uygulamanızın genel mimarisini kabaca aynı şekilde bulundurmamak. Aşağıdaki makale, bir Web veya çalışan rolünü Service Fabric durum bilgisi olmayan bir hizmete dönüştürmeye yardımcı olacak bir kılavuz sağlar.

* [Basit geçiş: bir Web veya çalışan rolünü Service Fabric durum bilgisi olmayan bir hizmete dönüştürme](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png