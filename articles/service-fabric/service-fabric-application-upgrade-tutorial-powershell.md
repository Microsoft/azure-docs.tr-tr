---
title: PowerShell kullanarak uygulama yükseltmesini Service Fabric | Microsoft Docs
description: Bu makalede, bir Service Fabric uygulaması dağıtma, kodu değiştirme ve PowerShell 'i kullanarak bir yükseltmeyi kullanıma alma deneyimi anlatılmaktadır.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: db0627c72ea6e7e3b272b818697b8e61b485be78
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167521"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>PowerShell kullanarak uygulama yükseltmesini Service Fabric
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

En sık kullanılan ve önerilen yükseltme yaklaşımı, izlenen yuvarlama yükseltmedir.  Azure Service Fabric, bir sistem durumu ilkeleri kümesine bağlı olarak yükseltilen uygulamanın sistem durumunu izler. Güncelleştirme etki alanı (UD) yükseltildikten sonra, Service Fabric uygulama durumunu değerlendirir ve sonraki güncelleştirme etki alanına ilerler veya sistem durumu ilkelerine bağlı olarak yükseltme işlemi başarısız olur.

Yönetilen veya yerel API 'Ler, PowerShell, Azure CLı, Java veya REST kullanılarak izlenen bir uygulama yükseltmesi gerçekleştirilebilir. Visual Studio kullanarak yükseltme gerçekleştirmeye yönelik yönergeler için bkz. [Visual Studio kullanarak uygulamanızı yükseltme](service-fabric-application-upgrade-tutorial.md).

Service Fabric izlenen yükseltmelerde, uygulama Yöneticisi Service Fabric uygulamanın sağlıklı olup olmadığını belirlemede sistem durumu değerlendirme ilkesini yapılandırabilir. Ayrıca, yönetici, sistem durumu değerlendirmesi başarısız olduğunda (örneğin, otomatik geri alma işlemi yapıldığında) yapılacak eylemi yapılandırabilir. Bu bölüm, PowerShell kullanan SDK örneklerinden biri için izlenen bir yükseltmeyi açıklar. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>1\. Adım: görsel nesneler örneğini derleme ve dağıtma
Uygulama Projesi, **Visualobjectsapplication** öğesine sağ tıklayıp **Yayımla** komutunu seçerek uygulamayı derleyin ve yayımlayın.  Daha fazla bilgi için bkz. [uygulama yükseltme öğreticisini Service Fabric](service-fabric-application-upgrade-tutorial.md).  Alternatif olarak, uygulamanızı dağıtmak için PowerShell 'i de kullanabilirsiniz.

> [!NOTE]
> PowerShell 'de Service Fabric komutlarının herhangi biri kullanılmadan önce, önce `Connect-ServiceFabricCluster` cmdlet 'ini kullanarak kümeye bağlanmanız gerekir. Benzer şekilde, kümenin yerel makinenizde zaten ayarlandığı varsayılır. [Service Fabric geliştirme ortamınızı ayarlama](service-fabric-get-started.md)hakkındaki makaleye bakın.
> 
> 

Visual Studio 'da projeyi oluşturduktan sonra, uygulama paketini ımabir görüntü oluşturmaya kopyalamak için [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) PowerShell komutunu kullanabilirsiniz. Uygulama paketini yerel olarak doğrulamak istiyorsanız, [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) cmdlet 'ini kullanın. Sonraki adım, [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) cmdlet 'ini kullanarak uygulamayı Service Fabric çalışma zamanına kaydetkullanmaktır. Aşağıdaki adım, [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet 'ini kullanarak uygulamanın bir örneğini başlatkullanmaktır.  Bu üç adım, Visual Studio 'da **Dağıt** menü öğesinin kullanılmasıyla benzerdir.  Sağlama tamamlandıktan sonra, tüketilen kaynakları azaltmak için, görüntü deposundan kopyalanmış uygulama paketini temizlemeniz gerekir.  Bir uygulama türü artık gerekmiyorsa, aynı nedenden dolayı bunun kaydı kaldırılmalıdır. Daha fazla bilgi için bkz. [PowerShell kullanarak uygulama dağıtma ve kaldırma](service-fabric-application-upgrade-tutorial-powershell.md) .

Şimdi [kümeyi ve uygulamayı görüntülemek için Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)kullanabilirsiniz. Uygulamanın, adres çubuğuna [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) yazarak Internet Explorer 'da gezinilebiliyor bir Web hizmeti vardır.  Ekranda hareket eden bazı kayan görsel nesneleri görmeniz gerekir.  Ayrıca, uygulama durumunu denetlemek için [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) komutunu kullanabilirsiniz.

## <a name="step-2-update-the-visual-objects-sample"></a>2\. Adım: görsel nesneler örneğini güncelleştirme
Adım 1 ' de dağıtılan sürümde, görsel nesnelerin döndürüleceğini fark edebilirsiniz. Bu uygulamayı görsel nesnelerin de her yerde de döndürelim.

VisualObjects çözüm içindeki VisualObjects. ActorService projesini seçin ve StatefulVisualObjectActor.cs dosyasını açın. Bu dosya içinde `MoveObject` yöntemine gidin, açıklama `this.State.Move()` ' i ve `this.State.Move(true)` ' yi kaldırın. Bu değişiklik, hizmet yükseltildikten sonra nesneleri döndürür.

Ayrıca, Project **Visualobjects. ActorService**projesinin *servicemanifest. xml* dosyasını (PackageRoot altında) güncelleştirmemiz gerekir. *CodePackage* ve hizmet sürümünü 2,0 olarak ve *servicemanifest. xml* dosyasında karşılık gelen satırları güncelleştirin.
Bildirim dosyasının değişiklik yapması için çözüme sağ tıkladıktan sonra Visual Studio *bildirim dosyalarını Düzenle* seçeneğini kullanabilirsiniz.

Değişiklikler yapıldıktan sonra, bildirim aşağıdaki gibi görünmelidir (vurgulanan bölümler değişiklikleri gösterir):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Artık *ApplicationManifest. xml* dosyası ( **Visualobjects** çözümünün altındaki **visualobjects** projesi altında bulunur), **visualobjects. actorservice** projesinin 2,0 sürümüne güncelleştirilir. Ayrıca, uygulama sürümü 1.0.0.0 'den 2.0.0.0 olarak güncelleştirilir. *ApplicationManifest. xml* aşağıdaki kod parçacığı gibi görünmelidir:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Şimdi yalnızca **Actorservice** projesini seçerek projeyi derleyin ve ardından Visual Studio 'da **Build** seçeneğini sağ tıklayıp seçin. **Tümünü yeniden derle**' yi seçerseniz, kod değiştirildiğinden, tüm projeler için sürümleri güncelleştirmeniz gerekir. Daha sonra, ***Visualobjectsapplication***' a sağ tıklayıp Service Fabric menüsünü seçip **paket**' i seçerek güncelleştirilmiş uygulamayı paketlim. Bu eylem, dağıtılabilecek bir uygulama paketi oluşturur.  Güncelleştirilmiş uygulamanız dağıtılmaya hazırlanıyor.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>3\. Adım: sistem durumu ilkelerine ve yükseltme parametrelerine karar verme
Çeşitli yükseltme parametreleri, zaman aşımları ve sistem durumu ölçütünün uygulanmasını iyi anlamak için [uygulama yükseltme parametreleri](service-fabric-application-upgrade-parameters.md) ve [yükseltme süreci](service-fabric-application-upgrade.md) hakkında bilgi edinin. Bu kılavuzda, hizmet durumu değerlendirmesi ölçütü varsayılan (ve önerilen) değerlerine ayarlanır; bu da yükseltme sonrasında tüm hizmetler ve örneklerin *sağlıklı* olması gerektiği anlamına gelir.  

Ancak, *Healthcheckstableduration* değerini 180 saniyeye yükseltirsiniz (Bu sayede, yükseltmeden sonraki güncelleştirme etki alanına geçmeden önce hizmetlerin en az 120 saniye süreyle sağlıklı olması gerekir).  Ayrıca, *Upgradedomaintimeout değerini* 1200 saniye ve *upgradetimeout değeri* 3000 saniye olarak ayarlayalim.

Son olarak, Ayrıca, *Yükselcilureaction* öğesini Rollback olarak ayarlayalım. Bu seçenek, yükseltme sırasında herhangi bir sorunla karşılaştığında uygulamanın önceki sürüme geri dönmesi Service Fabric gerekir. Bu nedenle, yükseltme başlatılırken (adım 4 ' te) aşağıdaki parametreler belirtilmiştir:

FailureAction = geri alma

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>4\. Adım: uygulamayı yükseltme için hazırlama
Artık uygulama oluşturulmuştur ve yükseltilmeye hazırdır. Yönetici olarak bir PowerShell penceresi açar ve [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps)yazarsanız, dağıtılan **visualobjects** 'in uygulama türü 1.0.0.0 olduğunu bilmenizi sağlamalıdır.  

Uygulama paketi, Service Fabric SDK- *Samples\services\stateful\visualobjects\visualobjects\obj\x64\debug*öğesini sıkıştırdığınızda aşağıdaki göreli yol altında depolanır. Bu dizinde uygulama paketinin depolandığı bir "paket" klasörü bulmalısınız. En son derleme olduğundan emin olmak için zaman damgalarını denetleyin (yolları uygun şekilde değiştirmeniz gerekebilir).

Şimdi güncelleştirilmiş uygulama paketini ımagesme Service Fabric kopyalayalim (uygulama paketlerinin Service Fabric tarafından depolandığı yer). *Applicationpackagepathınımaitstore* parametresi, uygulama paketini bulabileceği Service Fabric bilgilendirir. Güncelleştirilmiş uygulamayı aşağıdaki komutla "VisualObjects @ no__t-0V2" içine yerleştirdik (yolları uygun bir şekilde değiştirmeniz gerekebilir).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Sonraki adım, bu uygulamayı [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) komutu kullanılarak gerçekleştirilebilecek Service Fabric kaydetmelidir:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Yukarıdaki komut başarılı olmazsa, büyük olasılıkla tüm hizmetleri yeniden oluşturmanız gerekebilir. 2\. adımda belirtildiği gibi, WebService sürümünüzü de güncelleştirmeniz gerekebilir.

Uygulama başarıyla kaydedildikten sonra uygulama paketini kaldırmanız önerilir.  Uygulama paketlerini görüntü deposundan silme sistem kaynaklarını boşaltır.  Kullanılmayan uygulama paketlerinin tutulması disk depolama alanı tüketir ve uygulama performans sorunlarına yol açar.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>5\. Adım: uygulama yükseltmesini başlatma
Şimdi, [Başlat-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) komutunu kullanarak uygulama yükseltmesini başlatacak şekilde ayarlanıyoruz:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Uygulama adı, *ApplicationManifest. xml* dosyasında açıklananla aynıdır. Service Fabric hangi uygulamanın yükseltildiğini belirlemek için bu adı kullanır. Zaman aşımlarını çok kısa olacak şekilde ayarlarsanız, sorunu belirten bir hata iletisiyle karşılaşabilirsiniz. Sorun giderme bölümüne bakın veya zaman aşımlarını artırın.

Uygulama yükseltmesi devam ettikçe, Service Fabric Explorer kullanarak veya [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell komutunu kullanarak bunu izleyebilirsiniz: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Birkaç dakika içinde, önceki PowerShell komutunu kullanarak aldığınız durum tüm güncelleştirme etki alanlarının yükseltildiğini (tamamlandı) bilmelidir. Ve tarayıcı pencerenize ait görsel nesnelerin döndürme işlemi başladığını de fark etmelisiniz!

Sürüm 2 ' den sürüm 3 ' e veya sürüm 2 ' den sürüm 1 ' e yükseltme yapmayı deneyebilirsiniz. Sürüm 2 ' den sürüm 1 ' e geçmek da bir yükseltme olarak değerlendirilir. Zaman aşımları ve sistem durumu ilkeleriyle oynayın ve bunlarla ilgili bilgi sahibi olun. Bir Azure kümesine dağıtım yaparken parametrelerin uygun şekilde ayarlanması gerekir. Zaman aşımı tasarrufunu doğru ayarlamak iyi bir seçimdir.

## <a name="next-steps"></a>Sonraki adımlar
[Visual Studio 'yu kullanarak uygulamanızı yükseltmek](service-fabric-application-upgrade-tutorial.md) , Visual Studio kullanarak bir uygulama yükseltme işleminde size yol gösterir.

Uygulamanızın [yükseltme parametrelerini](service-fabric-application-upgrade-parameters.md)kullanarak nasıl yükseltileceğini denetleyin.

[Veri serileştirmesini](service-fabric-application-upgrade-data-serialization.md)nasıl kullanacağınızı öğrenerek uygulamanızın yükseltmelerini uyumlu hale getirin.

[Gelişmiş konulara](service-fabric-application-upgrade-advanced.md)başvurarak uygulamanızı yükseltirken gelişmiş işlevselliği nasıl kullanacağınızı öğrenin.

Uygulama [yükseltmelerinde sorun giderme](service-fabric-application-upgrade-troubleshooting.md)adımlarını izleyerek uygulama yükseltmelerinde karşılaşılan yaygın sorunları giderin.

