---
title: Gelişmiş uygulama yükseltme konuları
description: Bu makalede, Service Fabric uygulamasını yükseltmeyle ilgili bazı gelişmiş konular ele alınmaktadır.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6604300328f2d243077ba341a9028221438dce9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98792057"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Uygulama yükseltmesini Service Fabric: gelişmiş konular

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Uygulama yükseltmesi sırasında hizmet türlerini ekleme veya kaldırma

Bir yükseltmenin parçası olarak yayımlanmış bir uygulamaya yeni bir hizmet türü eklenirse, yeni hizmet türü dağıtılan uygulamaya eklenir. Bu tür bir yükseltme, uygulamanın zaten bir parçası olan hizmet örneklerinden hiçbirini etkilemez, ancak yeni hizmet türünün etkin olması için eklenen hizmet türünün bir örneğinin oluşturulması gerekir (bkz. [New-ServiceFabricService](/powershell/module/servicefabric/new-servicefabricservice)).

Benzer şekilde, hizmet türleri bir yükseltmeden bir uygulamanın parçası olarak kaldırılabilir. Ancak, yükseltme işlemine devam etmeden önce, kaldırılacak olan hizmet türünün tüm hizmet örneklerinin kaldırılması gerekir (bkz. [Remove-ServiceFabricService](/powershell/module/servicefabric/remove-servicefabricservice)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Durum bilgisi olmayan hizmet planlanmış kapalı kalma süresi sırasında bağlantı kesintilerine

Uygulama/küme yükseltme veya düğüm devre dışı bırakma gibi planlı durum bilgisi olmayan örnek için bağlantılar, kullanıma alındıktan sonra kaldırılan uç nokta kaldırılır ve bu da zorla bağlantı kapanışları elde edilir.

Bu durumu önlemek için, hizmet yapılandırmasında bir *örnek kapatma gecikme süresi* ekleyerek, küme içinden mevcut isteklerin ortaya çıkarılan uç noktalar üzerinde boşalmasına izin vermek üzere, *requestdrenajı* özelliğini yapılandırın. Bu, örneği kapatmadan önce gecikmeden *önce* durum bilgisiz örneği tarafından tanıtılan uç nokta kaldırılarak gerçekleştirilir. Bu gecikme, örnek gerçekten kapatmadan önce mevcut isteklerin düzgün şekilde boşaltılabilmesini sağlar. İstemciler, gecikme süresini yeniden çözümleyebilmeleri ve örneğe yeni istekler gönderilmesini önlemek için gecikmeyi başlatma sırasında geri çağırma işlevi tarafından uç nokta değişikliği hakkında bilgilendirilir. Bu istekler, uç noktaları güncelleştirmek için [ters proxy](./service-fabric-reverseproxy.md) kullanan istemcilerden veya bildirim modeliyle ([Servicenocertificate ationfilterdescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)) hizmet uç noktası çözümleme API 'leri kullanılarak kaynak olabilir.

### <a name="service-configuration"></a>Hizmet yapılandırması

Hizmet tarafında gecikmeyi yapılandırmanın birkaç yolu vardır.

 * **Yeni bir hizmet oluştururken** şunu belirtin `-InstanceCloseDelayDuration` :

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>
    ```

 * **Uygulama bildiriminde bulunan varsayılanlar bölümünde hizmeti tanımlarken**, `InstanceCloseDelayDurationSeconds` özelliği atayın:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Mevcut bir hizmeti güncelleştirirken** şunu belirtin `-InstanceCloseDelayDuration` :

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

 * **ARM şablonu aracılığıyla mevcut bir hizmeti oluştururken veya güncelleştirirken**, `InstanceCloseDelayDuration` değeri belirtin (desteklenen en düşük apı sürümü: 2019-11-01-Önizleme):

    ```ARM template to define InstanceCloseDelayDuration of 30seconds
    {
      "apiVersion": "2019-11-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications/services",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
      "location": "[variables('clusterLocation')]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
      ],
      "properties": {
        "provisioningState": "Default",
        "serviceKind": "Stateless",
        "serviceTypeName": "[parameters('serviceTypeName')]",
        "instanceCount": "-1",
        "partitionDescription": {
          "partitionScheme": "Singleton"
        },
        "serviceLoadMetrics": [],
        "servicePlacementPolicies": [],
        "defaultMoveCost": "",
        "instanceCloseDelayDuration": "00:00:30.0"
      }
    }
    ```

### <a name="client-configuration"></a>İstemci yapılandırması

Bir uç nokta değiştiğinde bildirim almak için, istemcilerin bir geri çağırma kaydetmesi için bkz. [Servicenocertificate Ationfilterdescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
Değişiklik bildirimi, uç noktaların değiştiği bir göstergesidir, istemcinin uç noktaları yeniden çözümlemesi gerektiğini ve yakında tanıtılmayacak olan uç noktaları kullanmamalıdır.

### <a name="optional-upgrade-overrides"></a>İsteğe bağlı yükseltme geçersiz kılmaları

Hizmet başına varsayılan gecikme sürelerini ayarlamanın yanı sıra, aynı () seçeneğini kullanarak uygulama/küme yükseltmesi sırasında gecikme süresini de geçersiz kılabilirsiniz `InstanceCloseDelayDurationSec` :

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

Geçersiz kılınan gecikme süresi yalnızca çağrılan yükseltme örneği için geçerlidir ve farklı hizmet gecikmesi yapılandırmalarının hiçbir şekilde değiştirilmesine izin vermez. Örneğin, `0` önceden yapılandırılmış tüm yükseltme gecikmelerini atlamak için bir gecikme belirtmek üzere bunu kullanabilirsiniz.

> [!NOTE]
> * İstekleri boşaltma ayarları, Azure Yük dengeleyicinin, boşaltma yapılmakta olan uç noktalara yeni istekler göndermesini engelleyemez.
> * Şikayet tabanlı bir çözüm mekanizması, bir hatadan sonra bir hizmet çözümlemesini tetiklediği için isteklerin düzgün boşaltılmasına neden olmaz. Daha önce açıklandığı gibi, bu, bunun yerine [Servicenocertificate. Descfilterdescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)kullanılarak Endpoint değişiklik bildirimlerine abone olmak için geliştirilmiştir.
> * Yükseltme, bir saldırgan daha az bir değilse ayarlar dikkate alınmayabilir, yani çoğaltmalar yükseltme sırasında uygulanmaz.
>
>

> [!NOTE]
> Bu özellik, küme kodu sürümü 7.1.XXX veya üzeri olduğunda, yukarıda bahsedilen Update-ServiceFabricService cmdlet 'i veya ARM şablonunu kullanarak mevcut hizmetlerde yapılandırılabilir.
>
>

## <a name="manual-upgrade-mode"></a>El ile yükseltme modu

> [!NOTE]
> *İzlenen* yükseltme modu tüm Service Fabric yükseltmeleri için önerilir.
> *Geriye* doğru yükseltme modu yalnızca başarısız veya askıya alınmış yükseltmeler için düşünülmelidir. 
>
>

*İzlenen* modda, yükseltme ilerledikçe uygulamanın sağlıklı olduğundan emin olmak için Service Fabric sistem durumu ilkeleri uygular. Sistem durumu ilkeleri ihlal edilirse, yükseltme askıya alınır veya belirtilen *FailureAction* öğesine göre otomatik olarak geri alınır.

*Monitortoredmanual* modunda, uygulama yöneticisinin yükseltmenin ilerleme durumu üzerinde toplam denetimi vardır. Bu mod, özel sistem durumu değerlendirme ilkeleri uygularken veya sistem durumu izlemeyi tamamen atlamak için geleneksel olmayan yükseltmeler gerçekleştirmeye çalışırken (örn. uygulama zaten veri kayboluyor) yararlıdır. Bu modda çalışan bir yükseltme, her bir UD tamamladıktan sonra askıya alınır ve [sürdürme-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade)kullanılarak açık bir şekilde sürdürülmelidir. Bir yükseltme askıya alındığında ve Kullanıcı tarafından sürdürülmeye hazırsa, yükseltme durumu *Rollforwardpending* (bkz. [upgradestate](/dotnet/api/system.fabric.applicationupgradestate)) olarak gösterilir.

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

Şimdi **Start-ServiceFabricApplicationUpgrade** cmdlet 'ini kullanarak uygulamayı yükseltin. Bu örnekte izlenen bir yükseltme gösterilmektedir, ancak izlenmeyen bir yükseltme de kullanılabilir. Bu cmdlet tarafından kabul edilen bayrakların tam açıklamasını görmek için bkz. [Azure Service Fabric PowerShell modülü başvurusu](/powershell/module/servicefabric/start-servicefabricapplicationupgrade#parameters)

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

## <a name="roll-back-application-upgrades"></a>Uygulama yükseltmelerini geri alma

Yükseltmeler üç moddan birinde (*izlenen, izlenemez* *veya toplanmadan*) bir şekilde geri alınabilir, ancak bunlar yalnızca *depountoredauya* *ya da* izsiz *moddan geri* alınabilir. *Izizuntoredauto* modunun geri alınması aynı şekilde, varsayılan *yükseltilebilir Dereperepsetchecktimeout* değerinin farklı olduğu özel durum ile aynı şekilde çalışıyor-bkz. [uygulama yükseltme parametreleri](service-fabric-application-upgrade-parameters.md). *İzleme* geri alma modunun geri alınması aynı şekilde çalışır. geri alma işlemi her bir ud tamamladıktan sonra askıya alınır ve geri alma işlemine devam etmek için [sürdürme-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade) kullanılarak açık bir şekilde devam etmelidir.

*Geri alma* *Işlemi bir FailureAction* ile *izlenen* moddaki bir yükseltmenin sistem durumu Ilkeleri Ihlal edildiğinde (bkz. [uygulama yükseltme parametreleri](service-fabric-application-upgrade-parameters.md)) veya açık olarak [Start-servicefabricapplicationrollback](/powershell/module/servicefabric/start-servicefabricapplicationrollback)kullanarak tetiklenebilir.

Geri alma sırasında, Upgrade *Dereperepdchecktimeout* ve modunun değeri [Update-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/update-servicefabricapplicationupgrade)kullanılarak herhangi bir zamanda değiştirilebilir.

## <a name="next-steps"></a>Sonraki adımlar
[Visual Studio 'Yu kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial.md) , Visual Studio kullanarak bir uygulama yükseltme işleminde size yol gösterir.

[PowerShell kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial-powershell.md) , PowerShell kullanarak bir uygulama yükseltme işleminde size yol gösterir.

Uygulamanızın [yükseltme parametrelerini](service-fabric-application-upgrade-parameters.md)kullanarak nasıl yükseltileceğini denetleyin.

[Veri serileştirmesini](service-fabric-application-upgrade-data-serialization.md)nasıl kullanacağınızı öğrenerek uygulamanızın yükseltmelerini uyumlu hale getirin.

Uygulama [yükseltmelerinde sorun giderme](service-fabric-application-upgrade-troubleshooting.md)adımlarını izleyerek uygulama yükseltmelerinde karşılaşılan yaygın sorunları giderin.
