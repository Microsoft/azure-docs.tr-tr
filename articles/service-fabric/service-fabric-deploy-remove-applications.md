---
title: PowerShell ile Azure Service Fabric dağıtımı
description: PowerShell kullanarak Service Fabric uygulamaları dağıtma ve kaldırma.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: atsenthi
ms.openlocfilehash: 0080ba0807a4cb31fedeb132932e2e08137dd40b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013283"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>PowerShell kullanarak uygulama dağıtma ve kaldırma

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API’leri](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Bir [uygulama türü paketlendikten][10]sonra Azure Service Fabric kümesine dağıtıma hazırız. Dağıtım aşağıdaki üç adımdan oluşur:

1. Uygulama paketini görüntü deposuna yükleyin.
2. Uygulama türünü görüntü deposu göreli yolu ile kaydedin.
3. Uygulama örneğini oluşturun.

Dağıtılan uygulamaya artık gerek duyulmazsa, uygulama örneğini ve uygulama türünü silebilirsiniz. Bir uygulamayı kümeden tamamen kaldırmak için aşağıdaki adımları uygulamanız gerekir:

1. Çalışan uygulama örneğini kaldırın (veya silin).
2. Artık gerekmiyorsa uygulama türünün kaydını silin.
3. Uygulama paketini görüntü deposundan kaldırın.

Yerel geliştirme kümenizdeki uygulamaları dağıtmak ve hata ayıklamak için Visual Studio kullanıyorsanız, önceki adımların tümü bir PowerShell betiği aracılığıyla otomatik olarak işlenir.  Bu betik, uygulama projesinin *betikler* klasöründe bulunur. Bu makalede, Visual Studio dışında aynı işlemleri gerçekleştirebilmeniz için betiğin ne yaptığını gösteren arka plan sunulmaktadır. 

Bir uygulamayı dağıtmanın diğer bir yolu da dış sağlama kullanmaktır. Uygulama paketi [`sfpkg`olarak paketlenebilir](service-fabric-package-apps.md#create-an-sfpkg) ve bir dış depoya yüklenebilir. Bu durumda, görüntü deposuna yükleme gerekli değildir. Dağıtım aşağıdaki adımlara ihtiyaç duyuyor:

1. `sfpkg` bir dış depoya yükleyin. Dış depo, REST http veya HTTPS uç noktası sunan herhangi bir mağaza olabilir.
2. Dış indirme URI 'sini ve uygulama türü bilgilerini kullanarak uygulama türünü kaydedin.
2. Uygulama örneğini oluşturun.

Temizleme için uygulama örneklerini kaldırın ve uygulama türünün kaydını silin. Paket görüntü deposuna kopyalanmadığından, temizlenecek geçici bir konum yoktur. Dış depodan sağlama, Service Fabric sürüm 6,1 ' den başlayarak kullanılabilir.

>[!NOTE]
> Visual Studio şu anda dış sağlamayı desteklemiyor.

 

## <a name="connect-to-the-cluster"></a>Kümeye bağlanma

Bu makalede herhangi bir PowerShell komutunu çalıştırmadan önce, Service Fabric kümesine bağlanmak için [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) ' ı kullanarak her zaman başlayın. Yerel geliştirme kümesine bağlanmak için aşağıdakileri çalıştırın:

```powershell
Connect-ServiceFabricCluster
```

Azure Active Directory, x509 sertifikaları veya Windows Active Directory kullanarak güvenli bir şekilde bir uzak kümeye veya kümeye bağlanma örnekleri için bkz. [güvenli bir kümeye bağlanma](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Uygulama paketini karşıya yükle

Uygulama paketini karşıya yüklemek, iç Service Fabric bileşenleri tarafından erişilebilen bir konuma koyar.
Uygulama paketini yerel olarak doğrulamak istiyorsanız, [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet 'ini kullanın.

[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) komutu, uygulama paketini küme görüntü deposuna yükler.

Visual Studio 2015 ' de *MyApplication* adlı bir uygulama oluşturup paketlediğinizi varsayalım. Varsayılan olarak, ApplicationManifest. xml dosyasında listelenen uygulama türü adı "MyApplicationType" dir.  Gerekli uygulama bildirimi, hizmet bildirimleri ve kod/yapılandırma/veri paketleri içeren uygulama paketi, *C:\Users\<Kullanıcı adı\>\Ist Studio 2015 \ Projects\MyApplication\MyApplication\pkg\Debug*konumunda bulunur. 

Aşağıdaki komut, uygulama paketinin içeriğini listeler:

```powershell
$path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Uygulama paketi büyükse ve/veya çok sayıda dosya içeriyorsa, [bu dosyayı sıkıştırabilirsiniz](service-fabric-package-apps.md#compress-a-package). Sıkıştırma, boyut ve dosya sayısını azaltır.
Yan etkisi, uygulama türünün kaydedilmesi ve kaydının geri kullanılmasından daha hızlıdır. Yükleme saati şu anda daha yavaş olabilir, özellikle de paketin sıkıştırılması için zaman dahil olur. 

Bir paketi sıkıştırmak için aynı [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) komutunu kullanın. Sıkıştırma, `SkipCopy` bayrağını kullanarak veya karşıya yükleme işlemiyle birlikte karşıya yüklemeden ayrı yapılabilir. Sıkıştırılmış bir pakette sıkıştırma uygulamak, Op değildir.
Sıkıştırılmış bir paketin sıkıştırmasını açmak için, `UncompressPackage` anahtarıyla aynı [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) komutunu kullanın.

Aşağıdaki cmdlet, paketi görüntü deposuna kopyalamadan sıkıştırır. Paket artık `Code` ve `Config` paketlerine ait daraltılmış dosyaları içerir. Birçok iç işlem (örneğin, paket paylaşımı, uygulama türü adı ve belirli doğrulamalar için sürüm ayıklama gibi) için gerekli olduklarından, uygulama ve hizmet bildirimleri sıkıştırıldı. Bildirimleri sıkıştırma işlemi bu işlemleri verimsiz hale getirir.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

Büyük uygulama paketleri için sıkıştırma zaman alır. En iyi sonuçlar için bir Fast SSD sürücüsü kullanın. Sıkıştırılmış paketin sıkıştırma zamanları ve boyutu, paket içeriğine göre farklılık gösterir.
Örneğin, ilk ve sıkıştırılmış paket boyutunu, sıkıştırma zamanına göre gösteren bazı paketlere yönelik sıkıştırma istatistikleri aşağıda verilmiştir.

|Başlangıç boyutu (MB)|Dosya sayısı|Sıkıştırma süresi|Sıkıştırılmış paket boyutu (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Bir paket sıkıştırıldıktan sonra, gerektiğinde bir veya birden çok Service Fabric kümesine yüklenebilir. Dağıtım mekanizması, sıkıştırılmış ve sıkıştırılmamış paketler için aynıdır. Sıkıştırılmış paketler, küme görüntü deposunda olduğu gibi depolanır. Paketler, uygulama çalıştırılmadan önce düğüm üzerinde sıkıştıralınır.


Aşağıdaki örnek, paketi görüntü deposuna "MyApplicationV1" adlı bir klasöre yükler:

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

*-Applicationpackagepathınımaslationparametresini* belirtmezseniz, uygulama paketi görüntü deposundaki "hata ayıklama" klasörüne kopyalanır.

>[!NOTE]
>PowerShell oturumu bir Service Fabric kümesine bağlıysa **Copy-ServiceFabricApplicationPackage** uygun görüntü deposu bağlantı dizesini otomatik olarak algılar. 5,6 'den eski Service Fabric sürümler için **-ımatoreconnectionstring** bağımsız değişkeni açıkça sağlanmalıdır.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>Service Fabric SDK PowerShell modülünün bir parçası olan **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet 'i, görüntü deposu bağlantı dizesini almak için kullanılır.  SDK modülünü içeri aktarmak için şunu çalıştırın:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Görüntü deposu ve görüntü deposu bağlantı dizesi hakkında ek bilgi için bkz. [görüntü deposu bağlantı dizesi](service-fabric-image-store-connection-string.md) .
>
>
>

Bir paketin karşıya yüklenmesi için gereken süre, birden fazla etkene göre farklılık gösterir. Bu faktörlerden bazıları paketteki dosya sayısı, paket boyutu ve dosya boyutlarıdır. Kaynak makine ile Service Fabric kümesi arasındaki ağ hızı, karşıya yükleme süresini de etkiler. [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) için varsayılan zaman aşımı 30 dakikadır.
Açıklanan faktörlere bağlı olarak, zaman aşımını artırmanız gerekebilir. Paketi kopyalama çağrısında sıkıştırıyorsanız, sıkıştırma süresini de göz önünde bulundurmanız gerekir.



## <a name="register-the-application-package"></a>Uygulama paketini Kaydet

Uygulama bildiriminde belirtilen uygulama türü ve sürümü, uygulama paketi kaydedildiğinde kullanım için kullanılabilir hale gelir. Sistem, önceki adımda karşıya yüklenen paketi okur, paketi doğrular, paket içeriğini işler ve işlenen paketi bir iç sistem konumuna kopyalar.  

Uygulama türünü kümeye kaydetmek ve dağıtım için kullanılabilir hale getirmek için [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) cmdlet 'ini çalıştırın:

### <a name="register-the-application-package-copied-to-image-store"></a>Görüntü deposuna kopyalanmış uygulama paketini Kaydet

Bir paket daha önce görüntü deposuna kopyalandığında, kayıt işlemi görüntü deposundaki göreli yolu belirtir.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

"MyApplicationV1", uygulama paketinin bulunduğu görüntü deposundaki klasördür. "MyApplicationType" adlı uygulama türü ve "1.0.0" sürümü (her ikisi de uygulama bildiriminde bulunur) artık kümeye kaydedilir.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Bir dış depoya kopyalanmış uygulama paketini Kaydet

Service Fabric sürüm 6,1 ' den başlayarak, sağlama paketin bir dış depodan indirilmesini destekler. Download URI, uygulama paketinin HTTP veya HTTPS protokolleri kullanılarak indirilebileceği [`sfpkg` uygulama paketinin](service-fabric-package-apps.md#create-an-sfpkg) yolunu temsil eder. Paketin daha önce bu dış konuma yüklenmiş olması gerekir. Service Fabric dosyayı indirebilmesi için URI okuma erişimine izin vermelidir. `sfpkg` dosya ". sfpkg" uzantısına sahip olmalıdır. Sağlama işlemi uygulama bildiriminde bulunan uygulama türü bilgilerini içermelidir.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) komutu, yalnızca sistem uygulama paketini başarıyla kaydettikten sonra döndürülür. Kaydın ne kadar süreceği, uygulama paketinin boyutuna ve içeriğine bağlıdır. Gerekirse, **-timeoutsec** parametresi daha uzun bir zaman aşımı sağlamak için kullanılabilir (varsayılan zaman aşımı 60 saniyedir).

Büyük bir uygulama paketiniz varsa veya zaman aşımları yaşıyorsanız, **-Async** parametresini kullanın. Bu komut, küme Register komutunu kabul ettiğinde döndürür. Kayıt işlemi gerektiği şekilde devam eder.
[Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) komutu, uygulama türü sürümlerini ve bunların kayıt durumlarını listeler. Kaydın ne zaman yapıldığını öğrenmek için bu komutu kullanabilirsiniz.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Bir uygulama paketini görüntü deposundan kaldırma

Görüntü deposuna bir paket kopyalandıysa, uygulama başarıyla kaydedildikten sonra dosyayı geçici konumdan kaldırmanız gerekir. Uygulama paketlerini görüntü deposundan silme sistem kaynaklarını boşaltır. Kullanılmayan uygulama paketlerinin tutulması disk depolama alanı tüketir ve uygulama performans sorunlarına yol açar.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Uygulama oluşturma

[Yeni-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet 'ini kullanarak başarıyla kaydedilmiş herhangi bir uygulama türü sürümünden bir uygulama örneği oluşturabilirsiniz. Her uygulamanın adı *"Fabric:"* şeması ile başlamalı ve her uygulama örneği için benzersiz olmalıdır. Hedef uygulama türünün uygulama bildiriminde tanımlanan varsayılan hizmetler de oluşturulur.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Kayıtlı uygulama türünün belirli bir sürümü için birden çok uygulama örneği oluşturulabilir. Her uygulama örneği, kendi iş diziniyle ve işlemiyle yalıtımda çalışır.

Kümede hangi adlandırılmış uygulamaların ve hizmetlerin çalıştığını görmek için [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) ve [Get-servicefabricservice](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) cmdlet 'lerini çalıştırın:

```powershell
Get-ServiceFabricApplication  
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}
```

```powershell
Get-ServiceFabricApplication | Get-ServiceFabricService
```

```Output
ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Uygulamayı kaldırma

Bir uygulama örneği artık gerekli olmadığında [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) cmdlet 'ini kullanarak bunu ada göre kalıcı olarak kaldırabilirsiniz. [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) , uygulamaya ait tüm hizmetleri otomatik olarak kaldırır ve tüm hizmet durumlarını kalıcı olarak kaldırır. 

> [!WARNING]
> Bu işlem ters çevrilemez ve uygulama durumu kurtarılamıyor.

```powershell
Remove-ServiceFabricApplication fabric:/MyApp
```

```Output
Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded
```

```powershell
Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Uygulama türünün kaydını silme

Uygulama türünün belirli bir sürümüne artık ihtiyaç duyulmadığında, [kayıt silme-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) cmdlet 'ini kullanarak uygulama türünün kaydını kaldırmanız gerekir. Kullanılmayan uygulama türlerinin kaydını silme, uygulama türü dosyalarını kaldırarak görüntü deposu tarafından kullanılan depolama alanını yayınlar. Bir uygulama türünün kaydının kaldırılması, görüntü deposuna kopyalama kullanılmışsa, görüntü deposu geçici konumuna kopyalanmış uygulama paketini kaldırmaz. Uygulama türü, kendisine karşı hiçbir uygulama örneği oluşturulmadığından ve bekleyen uygulama yükseltmeleri buna başvurduğundan, kaydı silinir.

Kümede kayıtlı olan uygulama türlerini görmek için [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) komutunu çalıştırın:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Belirli bir uygulama türünün kaydını silmek için [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) komutunu çalıştırın:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Sorun giderme

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage, ımatoreconnectionstring ister

Service Fabric SDK ortamında doğru varsayılanlar ayarlanmış olmalıdır. Ancak gerekirse, tüm komutlar için ımatoreconnectionstring, Service Fabric kümesinin kullandığı değerle eşleşmelidir. Imabtoreconnectionstring ' i küme bildiriminde bulabilirsiniz, [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) ve Get-ImageStoreConnectionStringFromClusterManifest komutlarını kullanarak elde edebilirsiniz:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Service Fabric SDK PowerShell modülünün bir parçası olan **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet 'i, görüntü deposu bağlantı dizesini almak için kullanılır.  SDK modülünü içeri aktarmak için şunu çalıştırın:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Imatoreconnectionstring, küme bildiriminde bulunur:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Görüntü deposu ve görüntü deposu bağlantı dizesi hakkında ek bilgi için bkz. [görüntü deposu bağlantı dizesi](service-fabric-image-store-connection-string.md) .

### <a name="deploy-large-application-package"></a>Büyük uygulama paketini dağıtma

Sorun: büyük bir uygulama paketi (GB sırası) için [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) zaman aşımına uğruyor.
Deneyin:
- [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) komutu için `TimeoutSec` parametresiyle daha büyük bir zaman aşımı belirtin. Varsayılan olarak, zaman aşımı 30 dakikadır.
- Kaynak makineniz ve kümeniz arasındaki ağ bağlantısını kontrol edin. Bağlantı yavaşsa, daha iyi bir ağ bağlantısına sahip bir makine kullanmayı düşünün.
İstemci makine kümeden başka bir bölgedeyse, kümeyle daha yakın bir bölgede bir istemci makine kullanmayı düşünün.
- Dış azaltmayı vurarak göz atın. Örneğin, görüntü deposu Azure Storage 'ı kullanacak şekilde yapılandırıldığında karşıya yükleme kısıtlanmış olabilir.

Sorun: yükleme paketi başarıyla tamamlandı, ancak [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) zaman aşımına uğruyor. Almaya
- Görüntü deposuna kopyalamadan önce [paketi sıkıştırın](service-fabric-package-apps.md#compress-a-package) .
Sıkıştırma, dosyanın boyutunu ve sayısını azaltır ve bu da Service Fabric olması gereken trafik miktarını ve çalışmayı azaltır. Karşıya yükleme işlemi daha yavaş olabilir (özellikle sıkıştırma süresini eklerseniz), ancak kayıt ve kayıt kaldırma işlemleri daha hızlıdır.
- [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) için `TimeoutSec` parametresiyle daha büyük bir zaman aşımı belirtin.
- [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)için `Async` anahtarı belirtin. Komut, küme komutu kabul ettiğinde ve uygulama türünün kaydı zaman uyumsuz olarak devam ederse ' i döndürür. Bu nedenle, bu durumda daha yüksek bir zaman aşımı belirtmeniz gerekmez. [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) komutu, başarıyla kaydedilen tüm uygulama türü sürümlerini ve bunların kayıt durumlarını listeler. Kaydın ne zaman yapıldığını öğrenmek için bu komutu kullanabilirsiniz.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Birçok dosya içeren uygulama paketini dağıtma

Sorun: çok sayıda dosya (binlerce sırada) olan bir uygulama paketi için [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) zaman aşımına uğruyor.
Deneyin:
- Görüntü deposuna kopyalamadan önce [paketi sıkıştırın](service-fabric-package-apps.md#compress-a-package) . Sıkıştırma, dosya sayısını azaltır.
- [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) için `TimeoutSec` parametresiyle daha büyük bir zaman aşımı belirtin.
- [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)için `Async` anahtarı belirtin. Komut, küme komutu kabul ettiğinde ve uygulama türünün kaydı zaman uyumsuz olarak devam ederse ' i döndürür.
Bu nedenle, bu durumda daha yüksek bir zaman aşımı belirtmeniz gerekmez. [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) komutu, başarıyla kaydedilen tüm uygulama türü sürümlerini ve bunların kayıt durumlarını listeler. Kaydın ne zaman yapıldığını öğrenmek için bu komutu kullanabilirsiniz.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Sonraki adımlar

[Uygulamaları paketleme](service-fabric-package-apps.md)

[Uygulama yükseltmesini Service Fabric](service-fabric-application-upgrade.md)

[Service Fabric sistem durumu tanıtımı](service-fabric-health-introduction.md)

[Service Fabric hizmeti Tanılama ve sorunlarını giderme](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric bir uygulama modelleme](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md