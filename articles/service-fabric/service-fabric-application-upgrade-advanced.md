---
title: Gelişmiş uygulama yükseltme konuları | Microsoft Docs
description: Bu makalede, Service Fabric uygulamasını yükseltmeyle ilgili bazı gelişmiş konular ele alınmaktadır.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: c37ee8177ba31ac8a5da90fef175a6fbd63a6d75
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167595"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Uygulama yükseltmesini Service Fabric: gelişmiş konular
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Uygulama yükseltmesi sırasında hizmet türlerini ekleme veya kaldırma
Bir yükseltmenin parçası olarak yayımlanmış bir uygulamaya yeni bir hizmet türü eklenirse, yeni hizmet türü dağıtılan uygulamaya eklenir. Bu tür bir yükseltme, uygulamanın zaten bir parçası olan hizmet örneklerinden hiçbirini etkilemez, ancak yeni hizmet türünün etkin olması için eklenen hizmet türünün bir örneğinin oluşturulması gerekir (bkz. [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Benzer şekilde, hizmet türleri bir yükseltmeden bir uygulamanın parçası olarak kaldırılabilir. Ancak, yükseltme işlemine devam etmeden önce, kaldırılacak olan hizmet türünün tüm hizmet örneklerinin kaldırılması gerekir (bkz. [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>El ile yükseltme modu
> [!NOTE]
> *İzlenen* yükseltme modu tüm Service Fabric yükseltmeleri için önerilir.
> *Geriye* doğru yükseltme modu yalnızca başarısız veya askıya alınmış yükseltmeler için düşünülmelidir. 
>
>

*İzlenen* modda, yükseltme ilerledikçe uygulamanın sağlıklı olduğundan emin olmak için Service Fabric sistem durumu ilkeleri uygular. Sistem durumu ilkeleri ihlal edilirse, yükseltme askıya alınır veya belirtilen *FailureAction*öğesine göre otomatik olarak geri alınır.

*Monitortoredmanual* modunda, uygulama yöneticisinin yükseltmenin ilerleme durumu üzerinde toplam denetimi vardır. Bu mod, özel sistem durumu değerlendirme ilkeleri uygularken veya sistem durumu izlemeyi tamamen atlamak için geleneksel olmayan yükseltmeler gerçekleştirmeye çalışırken (örn. uygulama zaten veri kayboluyor) yararlıdır. Bu modda çalışan bir yükseltme, her bir UD tamamladıktan sonra askıya alınır ve [sürdürme-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps)kullanılarak açık bir şekilde sürdürülmelidir. Bir yükseltme askıya alındığında ve Kullanıcı tarafından sürdürülmeye hazırsa, yükseltme durumu *Rollforwardpending* (bkz. [upgradestate](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)) olarak gösterilir.

Son olarak, hiçbir Kullanıcı girişi gerekmediği ve hiçbir uygulama sistem durumu ilkesi değerlendirilmeden bu yana hizmet geliştirme veya test sırasında hızlı yükseltme yinelemeleri gerçekleştirmek için, *tasgeli olmayan* mod modu kullanışlıdır.

## <a name="upgrade-with-a-diff-package"></a>Fark paketiyle yükseltme
Tüm uygulama paketini sağlamak yerine, yükseltmeler ve yalnızca güncelleştirilmiş kod/yapılandırma/veri paketleri içeren fark paketleri, tüm uygulama bildirimi ve tamamlanmış hizmet bildirimleri ile birlikte sağlanarak da gerçekleştirilebilir. Tüm uygulama paketleri yalnızca bir uygulamanın kümeye ilk yüklemesi için gereklidir. Sonraki yükseltmeler, tüm uygulama paketlerinden veya fark paketlerinden olabilir.  

Uygulama paketinde bulunamayan bir fark paketinin uygulama bildiriminde veya hizmet bildirimlerinde herhangi bir başvuru, geçerli olarak sağlanan sürümle otomatik olarak değiştirilmiştir.

Fark paketini kullanmaya yönelik senaryolar şunlardır:

* Çeşitli hizmet bildirim dosyalarına ve/veya çeşitli kod paketlerine, yapılandırma paketlerine veya veri paketlerine başvuran büyük bir uygulama paketiniz olduğunda.
* Doğrudan uygulama derleme sürecinizden yapı düzeni üreten bir dağıtım sistemine sahip olduğunuzda. Bu durumda, kod değiştirilmese de, yeni oluşturulan derlemeler farklı bir sağlama toplamı alır. Tam uygulama paketinin kullanılması, tüm kod paketlerinde sürümü güncelleştirmenizi gerektirir. Fark paketini kullanarak yalnızca değiştirilen dosyaları ve sürümün değiştiği bildirim dosyalarını sağlarsınız.

Bir uygulama Visual Studio kullanılarak yükseltildiğinde, fark paketi otomatik olarak yayımlanır. Bir fark paketini el ile oluşturmak için, uygulama bildirimi ve hizmet bildirimlerinin güncellenmesi gerekir, ancak son uygulama paketine yalnızca değiştirilen paketler eklenmelidir.

Örneğin, aşağıdaki uygulama ile başlayalım (daha kolay anlamak için sunulan sürüm numaraları):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Yalnızca fark paketini kullanarak Service1 kod paketini güncelleştirmek istediğinizi varsayalım. Güncelleştirilmiş uygulamanız aşağıdaki sürüm değişikliklerine sahiptir:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Bu durumda, uygulama bildirimini 2.0.0 olarak ve Service1 için hizmet bildirimini, kod paketi güncelleştirmesini yansıtacak şekilde güncelleşdirecektir. Uygulama paketinizin klasörü aşağıdaki yapıya sahip olacaktır:

```text
app1/
  service1/
    code/
```

Diğer bir deyişle, normal olarak tüm uygulama paketini oluşturun ve ardından sürüm değişmedi tüm kod/yapılandırma/veri paketi klasörlerini kaldırın.

## <a name="upgrade-application-parameters-independently-of-version"></a>Uygulama parametrelerini sürümden bağımsız olarak yükselt

Bazen, bildirim sürümünü değiştirmeden bir Service Fabric uygulamasının parametrelerini değiştirmek tercih edilir. Bu işlem, **Start-ServiceFabricApplicationUpgrade** Azure Service Fabric PowerShell cmdlet 'i ile **-applicationparameter** bayrağıyla kullanılması kolay bir şekilde yapılabilir. Aşağıdaki özelliklerle bir Service Fabric uygulaması varsayın:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Şimdi **Start-ServiceFabricApplicationUpgrade** cmdlet 'ini kullanarak uygulamayı yükseltin. Bu örnekte izlenen bir yükseltme gösterilmektedir, ancak izlenmeyen bir yükseltme de kullanılabilir. Bu cmdlet tarafından kabul edilen bayrakların tam açıklamasını görmek için bkz. [Azure Service Fabric PowerShell modülü başvurusu](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Yükseltmeden sonra, uygulamanın güncelleştirilmiş parametrelere ve aynı sürüme sahip olduğunu doğrulayın:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="rolling-back-application-upgrades"></a>Uygulama yükseltmeleri geri alınıyor

Yükseltmeler üç moddan birinde (*izlenen, izlenemez* *veya toplanmadan*) bir şekilde geri alınabilir, ancak bunlar yalnızca *depountoredauya* *ya da*izsiz *moddan geri* alınabilir. *Izizuntoredauto* modunun geri alınması aynı şekilde, varsayılan *yükseltilebilir Dereperepsetchecktimeout* değerinin farklı olduğu özel durum ile aynı şekilde çalışıyor-bkz. [uygulama yükseltme parametreleri](service-fabric-application-upgrade-parameters.md). *Monitortoredmanual* modunun geri alınması aynı şekilde çalışır. geri alma işlemi, her bir ud tamamlandıktan sonra kendisini askıya alır ve şu işleme devam etmek için [sürdürme-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) kullanılarak açık bir şekilde devam etmelidir. etkin.

*Geri alma* işlemi, bir *FailureAction* ile *izlenen* moddaki bir yükseltmenin sistem durumu ilkeleri ihlal edildiğinde otomatik olarak tetiklenebilir (bkz. [uygulama yükseltme parametreleri](service-fabric-application-upgrade-parameters.md)) veya açıkça kullanarak [ Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Geri alma sırasında, Upgrade *Dereperepdchecktimeout* ve modunun değeri [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps)kullanılarak herhangi bir zamanda değiştirilebilir.

## <a name="next-steps"></a>Sonraki adımlar
[Visual Studio 'Yu kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial.md) , Visual Studio kullanarak bir uygulama yükseltme işleminde size yol gösterir.

[PowerShell kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial-powershell.md) , PowerShell kullanarak bir uygulama yükseltme işleminde size yol gösterir.

Uygulamanızın [yükseltme parametrelerini](service-fabric-application-upgrade-parameters.md)kullanarak nasıl yükseltileceğini denetleyin.

[Veri serileştirmesini](service-fabric-application-upgrade-data-serialization.md)nasıl kullanacağınızı öğrenerek uygulamanızın yükseltmelerini uyumlu hale getirin.

Uygulama [yükseltmelerinde sorun giderme](service-fabric-application-upgrade-troubleshooting.md)adımlarını izleyerek uygulama yükseltmelerinde karşılaşılan yaygın sorunları giderin.
