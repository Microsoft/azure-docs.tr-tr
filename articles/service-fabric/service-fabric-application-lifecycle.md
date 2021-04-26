---
title: Service Fabric 'de uygulama yaşam döngüsü
description: Service Fabric uygulamalarını geliştirme, dağıtma, test etme, yükseltme, sürdürme ve kaldırma işlemleri açıklanmaktadır.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: ae0c79cdaafc8fc016d463a01046f0a02121330a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785744"
---
# <a name="service-fabric-application-lifecycle"></a>Service Fabric uygulaması yaşam döngüsü
Diğer platformlarda olduğu gibi, Azure Service Fabric bir uygulama genellikle şu aşamalardan geçer: tasarım, geliştirme, test, dağıtım, yükseltme, bakım ve kaldırma. Service Fabric bulut uygulamalarının tam uygulama yaşam döngüsü için, dağıtım, günlük yönetim ve son kullanımdan kaldırma ile bakım aracılığıyla geliştirme aşamasından ilk sınıf destek sağlar. Hizmet modeli birçok farklı rolün uygulama yaşam döngüsüne bağımsız olarak katılmasına olanak sağlar. Bu makalede, API 'lerin yanı sıra Service Fabric uygulama yaşam döngüsünün aşamaları boyunca farklı roller tarafından nasıl kullanıldıkları hakkında genel bakış sunulmaktadır.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Hizmet modeli rolleri
Hizmet modeli rolleri şunlardır:

* **Service Developer**: aynı türdeki veya farklı türlerde birden çok uygulamada kullanılabilen ve kullanılan modüler ve genel hizmetler geliştirir. Örneğin, bir kuyruk hizmeti bir anahtar oluşturma uygulaması (Yardım Masası) veya bir e-ticaret uygulaması (alışveriş sepeti) oluşturmak için kullanılabilir.
* **Uygulama geliştiricisi**: belirli belirli gereksinimleri veya senaryoları karşılamak üzere bir hizmet koleksiyonunu tümleştirerek uygulamalar oluşturur. Örneğin, bir e-ticaret Web sitesi, "JSON durum bilgisiz Front-End hizmeti", "açık eksiltme hizmeti" ve "sıra dışı hizmeti" olarak bir Auctioning çözümü oluşturmak için tümleştirebilir.
* **Uygulama Yöneticisi**: uygulama yapılandırması (yapılandırma şablonu parametrelerini doldurma), dağıtım (kullanılabilir kaynaklarla eşleme) ve hizmet kalitesi hakkında kararlar verir. Örneğin, bir uygulama Yöneticisi, uygulamanın dil yerel ayarına (örneğin, Birleşik Devletler için Ingilizce veya Japonya için Japonca) karar verir. Farklı bir dağıtılmış uygulama farklı ayarlara sahip olabilir.
* **İşleç**: uygulama Yöneticisi tarafından belirtilen uygulama yapılandırmasına ve gereksinimlerine göre uygulamaları dağıtır. Örneğin, bir operatör uygulamayı hazırlar ve dağıtır ve Azure 'da çalışmasını sağlar. İşleçler, uygulama durumu ve performans bilgilerini izler ve fiziksel altyapıyı gerektiği gibi korur.

## <a name="develop"></a>Geliştirme
1. *Hizmet geliştiricisi* [Reliable Actors](service-fabric-reliable-actors-introduction.md) veya [Reliable Services](service-fabric-reliable-services-introduction.md) programlama modelini kullanarak farklı türlerde hizmetler geliştirir.
2. *Hizmet geliştiricisi* , bir veya daha fazla kod, yapılandırma ve veri paketi içeren bir hizmet bildirim dosyasında geliştirilmiş hizmet türlerini bildirimli olarak açıklar.
3. Daha sonra bir *uygulama geliştiricisi* farklı hizmet türleri kullanarak bir uygulama oluşturur.
4. *Uygulama geliştiricisi* , bileşen hizmetlerinin hizmet bildirimlerine başvurarak ve bileşen hizmetlerinin farklı yapılandırma ve dağıtım ayarlarını doğru şekilde geçersiz kılarak ve parametreleştirerek uygulama bildiriminde uygulama türünü bildirimli olarak açıklar.

Bkz. [Reliable Actors](service-fabric-reliable-actors-get-started.md) kullanmaya başlayın ve örnekler için [Reliable Services kullanmaya](service-fabric-reliable-services-quick-start.md) başlayın.

## <a name="deploy"></a>Dağıtma
1. Bir *Uygulama Yöneticisi* , uygulama bildiriminde **ApplicationType** öğesinin uygun parametrelerini belirterek, uygulama türünü bir Service Fabric kümesine dağıtılacak belirli bir uygulamaya uyarkılar.
2. Bir *operatör* , [ **CopyApplicationPackage** yöntemini](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) veya [ **Copy-servicefabricapplicationpackage** cmdlet 'ini](/powershell/module/servicefabric/copy-servicefabricapplicationpackage)kullanarak uygulama paketini küme görüntü deposuna yükler. Uygulama paketi, uygulama bildirimini ve hizmet paketleri koleksiyonunu içerir. Service Fabric, bir Azure Blob deposu veya Service Fabric sistem hizmeti olabilecek görüntü deposunda depolanan uygulama paketinden uygulamaları dağıtır.
3. *İşleci* daha sonra, [ **Provisionapplicationasync** yöntemi](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **register-Servicefabricapplicationtype** cmdlet](/powershell/module/servicefabric/register-servicefabricapplicationtype)'i veya [ **bir uygulama Rest sağlama** işlemi](/rest/api/servicefabric/provision-an-application)kullanarak karşıya yüklenen uygulama paketinden uygulama türünü sağlar.
4. Uygulama sağlandıktan sonra bir *operatör* , uygulamayı [ **Createapplicationasync** yöntemi](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **New-Servicefabricapplication** cmdlet](/powershell/module/servicefabric/new-servicefabricapplication)'i veya [ **uygulama Rest oluştur** işlemini](/rest/api/servicefabric/create-an-application)kullanarak *Uygulama Yöneticisi* tarafından sağlanan parametrelerle başlatır.
5. Uygulama dağıtıldıktan sonra bir *operatör* , kullanılabilir hizmet türlerine göre uygulama için yeni hizmet örnekleri oluşturmak üzere [ **CreateServiceAsync** yöntemini](/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [ **New-servicefabricservice** cmdlet 'ini](/powershell/module/servicefabric/new-servicefabricservice)veya [ **hizmet Rest oluşturma** işlemini](/rest/api/servicefabric/create-a-service) kullanır.
6. Uygulama artık Service Fabric kümesinde çalışıyor.

Örnekler için bkz. [uygulama dağıtma](service-fabric-deploy-remove-applications.md) .

## <a name="test"></a>Test
1. Yerel geliştirme kümesine veya bir test kümesine dağıttıktan sonra, bir *hizmet geliştiricisi* , yerleşik yük devretme testi senaryosunu [**FailoverTestScenarioParameters**](/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) ve [**Failovertestscenario**](/dotnet/api/system.fabric.testability.scenario.failovertestscenario) sınıflarını veya [ **Invoke-servicefabricfailovertestscenario** cmdlet 'ini](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario)kullanarak çalıştırır. Yük devretme testi senaryosu, hala kullanılabilir ve çalışır durumda olduğundan emin olmak için önemli geçişler ve yük devretme işlemleri aracılığıyla belirtilen bir hizmeti çalıştırır.
2. Daha sonra *hizmet geliştiricisi* , [**ChaosTestScenarioParameters**](/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) ve [**chaostestscenario**](/dotnet/api/system.fabric.testability.scenario.chaostestscenario) sınıflarını veya [ **Invoke-servicefabricchaostestscenario** cmdlet 'ini](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario)kullanarak yerleşik Chaos test senaryosunu çalıştırır. Chaos test senaryosu, kümeye birden çok düğüm, kod paketi ve çoğaltma hatası rastgele bir şekilde sahiptir.
3. *Hizmet geliştiricisi* , birincil çoğaltmaları küme etrafına taşıyarak test senaryoları yazarak [hizmetten hizmete iletişimi sınar](service-fabric-testability-scenarios-service-communication.md) .

Daha fazla bilgi için bkz. [hata analiz hizmetine giriş](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Yükseltme
1. Bir *hizmet geliştiricisi* , Örneklenmiş uygulamanın bileşen hizmetlerini güncelleştirir ve/veya hataları düzeltir ve hizmet bildiriminin yeni bir sürümünü sağlar.
2. *Uygulama geliştiricisi* tutarlı hizmetlerin yapılandırma ve dağıtım ayarlarını geçersiz kılar ve uygulama bildiriminin yeni bir sürümünü sağlar. Daha sonra uygulama geliştiricisi, hizmet bildirimlerinin yeni sürümlerini uygulamaya ekler ve güncelleştirilmiş bir uygulama paketinde uygulama türünün yeni bir sürümünü sağlar.
3. *Uygulama Yöneticisi* , uygun parametreleri güncelleştirerek uygulama türünün yeni sürümünü hedef uygulamaya ekler.
4. Bir *operatör* , [ **CopyApplicationPackage** yöntemini](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) veya [ **Copy-servicefabricapplicationpackage** cmdlet 'ini](/powershell/module/servicefabric/copy-servicefabricapplicationpackage)kullanarak güncelleştirilmiş uygulama paketini küme görüntü deposuna yükler. Uygulama paketi, uygulama bildirimini ve hizmet paketleri koleksiyonunu içerir.
5. Bir *işleç* , [ **Provisionapplicationasync** yöntemi](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **register-servicefabricapplicationtype** cmdlet](/powershell/module/servicefabric/register-servicefabricapplicationtype)'i veya [ **bir uygulama Rest sağlama** işlemi](/rest/api/servicefabric/provision-an-application)kullanarak hedef kümedeki uygulamanın yeni sürümünü sağlar.
6. *Operatör* , hedef uygulamayı [ **Upgradeapplicationasync** yöntemi](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Start-Servicefabricapplicationupgrade** cmdlet](/powershell/module/servicefabric/start-servicefabricapplicationupgrade)'i veya [ **bir uygulama Rest yükseltme** işlemini](/rest/api/servicefabric/upgrade-an-application)kullanarak yeni sürüme yükseltir.
7. *Operatör* , [ **GetApplicationUpgradeProgressAsync** yöntemi](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Get-servicefabricapplicationupgrade** cmdlet](/powershell/module/servicefabric/get-servicefabricapplicationupgrade)'i veya [ **uygulama yükseltme ilerleme durumu** Rest işlemini](/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1)kullanarak yükseltmenin ilerlemesini denetler.
8. Gerekirse, *işleç* geçerli uygulama yükseltmesinin parametrelerini, [ **updateapplicationupgradeasync** yöntemini](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Update-Servicefabricapplicationupgrade** cmdlet 'ini](/powershell/module/servicefabric/update-servicefabricapplicationupgrade)veya [ **güncelleştirme uygulaması yükseltme** Rest işlemini](/rest/api/servicefabric/update-an-application-upgrade)kullanarak değiştirir ve yeniden uygular.
9. Gerekirse, *işleç* geçerli uygulama yükseltmesini [ **Rollbackapplicationupgradeasync** yöntemi](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Start-Servicefabricapplicationrollback** cmdlet](/powershell/module/servicefabric/start-servicefabricapplicationrollback)'i veya [ **geri alma uygulama yükseltme** Rest işlemini](/rest/api/servicefabric/rollback-an-application-upgrade)kullanarak geri kaydeder.
10. Service Fabric, bileşen hizmetlerinin herhangi birinin kullanılabilirliğini kaybetmeksizin kümede çalışan hedef uygulamayı yükseltir.

Örnekler için [uygulama yükseltme öğreticisine](service-fabric-application-upgrade-tutorial.md) bakın.

## <a name="maintain"></a>Bakım
1. İşletim sistemi yükseltmeleri ve düzeltme ekleri için, kümede çalışan tüm uygulamaların kullanılabilirliğini garanti etmek üzere Azure altyapısına sahip arabirimleri Service Fabric.
2. Service Fabric platformunun yükseltmeleri ve düzeltme ekleri için, küme üzerinde çalışan herhangi bir uygulamanın kullanılabilirliğini kaybetmeden Service Fabric kendisini yükseltir.
3. Bir *Uygulama Yöneticisi* , geçmiş kapasite kullanım verilerini ve tahmini gelecek talebi analiz ettikten sonra bir kümeden düğümlerin eklenmesini veya kaldırılmasını onaylar.
4. Bir *işleç* , *Uygulama Yöneticisi* tarafından belirtilen düğümleri ekler ve kaldırır.
5. ' Ye yeni düğümler eklendiğinde veya var olan düğümleri kümeden kaldırıldığında, en iyi performansı elde etmek için çalışan uygulamaların kümedeki tüm düğümlerde otomatik olarak yük dengelenip Service Fabric.

## <a name="remove"></a>Kaldır
1. *İşleci* , [ **DeleteServiceAsync** yöntemini](/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [ **Remove-Servicefabricservice** cmdlet 'ini](/powershell/module/servicefabric/remove-servicefabricservice)veya [ **hizmet Rest silme** işlemini](/rest/api/servicefabric/delete-a-service)kullanarak tüm uygulamayı kaldırmadan kümedeki çalışan bir hizmetin belirli bir örneğini silebilir.  
2. Bir *işleç* Ayrıca, [ **Deleteapplicationasync** yöntemi](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Remove-Servicefabricapplication** cmdlet](/powershell/module/servicefabric/remove-servicefabricapplication)'i veya [ **uygulama Rest silme** işlemini](/rest/api/servicefabric/delete-an-application)kullanarak bir uygulama örneğini ve tüm hizmetlerini silebilir.
3. Uygulama ve hizmetler durdurulduktan sonra, *Işleç* [ **unprovisionapplicationasync** yöntemini](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Unregister-servicefabricapplicationtype** cmdlet 'ini](/powershell/module/servicefabric/unregister-servicefabricapplicationtype)veya [ **bir uygulama Rest işlemini sağlamayı kaldırmayı**](/rest/api/servicefabric/unprovision-an-application)kullanarak uygulama türünü sağlamasını yapabilir. Uygulama türünün sağlanması, uygulama paketini Imaal 'dan kaldırmaz. Uygulama paketini el ile kaldırmanız gerekir.
4. Bir *işleç* , [ **removeapplicationpackage** yöntemi](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) veya [ **Remove-Servicefabricapplicationpackage** cmdlet 'ini](/powershell/module/servicefabric/remove-servicefabricapplicationpackage)kullanarak, ımabir uygulama paketini çıkartır.

Örnekler için bkz. [uygulama dağıtma](service-fabric-deploy-remove-applications.md) .

## <a name="next-steps"></a>Sonraki adımlar
Service Fabric uygulamaları ve hizmetleri geliştirme, test etme ve yönetme hakkında daha fazla bilgi için bkz.:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Uygulama dağıtma](service-fabric-deploy-remove-applications.md)
* [Uygulama yükseltme](service-fabric-application-upgrade.md)
* [Test edilebilirlik genel bakış](service-fabric-testability-overview.md)
