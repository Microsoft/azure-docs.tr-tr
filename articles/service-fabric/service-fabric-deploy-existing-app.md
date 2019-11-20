---
title: Mevcut bir yürütülebiliri Azure Service Fabric dağıtma | Microsoft Docs
description: Mevcut bir uygulamayı Konuk yürütülebilir dosyası olarak paketlemeyi öğrenin, bu nedenle bir Service Fabric kümesine dağıtılabilir.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: atsenthi
ms.openlocfilehash: 575303cc2ec3e880187bac64da06d05721df14e6
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599656"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Service Fabric için mevcut bir yürütülebiliri paketleme ve dağıtma
Mevcut bir yürütülebiliri [Konuk yürütülebilir dosyası](service-fabric-guest-executables-introduction.md)olarak paketlerken, bir Visual Studio proje şablonu kullanmayı veya [uygulama paketini el ile oluşturmayı](#manually)seçebilirsiniz. Visual Studio 'yu kullanarak uygulama paketi yapısı ve bildirim dosyaları sizin için yeni proje şablonu tarafından oluşturulur.

> [!TIP]
> Mevcut bir Windows yürütülebiliri bir hizmete paketlemek için en kolay yol, Visual Studio ve Linux üzerinde Yeumman kullanmak için kullanılır
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Mevcut bir yürütülebilir dosyayı paketlemek ve dağıtmak için Visual Studio 'Yu kullanma
Visual Studio, bir Service Fabric kümesine Konuk yürütülebilir dosyası dağıtmanıza yardımcı olmak için Service Fabric bir hizmet şablonu sağlar.

1. **Dosya** > **Yeni proje**' yi seçin ve bir Service Fabric uygulaması oluşturun.
2. Hizmet şablonu olarak **Konuk yürütülebilir dosyasını** seçin.
3. Yürütülebilir dosyanızı içeren klasörü seçmek için, **Araştır** ' a tıklayın ve hizmeti oluşturmak için parametrelerin geri kalanını girin.
   * *Kod paketi davranışı*. , Klasörünüzün tüm içeriğini Visual Studio projesine kopyalamak üzere ayarlanabilir, bu, yürütülebilir dosya değişmezse yararlı olur. Yürütülebilir dosyanın değiştirilmesini beklediğinizi ve dinamik olarak yeni derlemeler seçebilmesini istiyorsanız, bunun yerine klasörü bağlamayı tercih edebilirsiniz. Visual Studio 'da uygulama projesi oluştururken bağlantılı klasörleri kullanabilirsiniz. Bu, projenin içinden kaynak konumuna bağlantı sağlar ve bu, Konuk yürütülebilir dosyasını kaynak hedefinde güncelleştirmenizi mümkün hale getirir. Bu güncelleştirmeler derlemede uygulama paketinin bir parçası olur.
   * *Program* hizmeti başlatmak için çalıştırılması gereken yürütülebilir dosyayı belirtir.
   * *Bağımsız değişkenler* çalıştırılabilire geçirilmesi gereken bağımsız değişkenleri belirtir. Bağımsız değişkenlere sahip parametrelerin listesi olabilir.
   * *WorkingFolder* , başlatılacak işlemin çalışma dizinini belirtir. Üç değer belirtebilirsiniz:
     * `CodeBase`çalışma dizininin uygulama paketindeki (`Code` önceki dosya yapısında gösterilen dizin) kod dizinine ayarlanacağım olduğunu belirtir.
     * `CodePackage`çalışma dizininin uygulama paketinin köküne ayarlanacağım olduğunu belirtir (`GuestService1Pkg` önceki dosya yapısında gösterilir).
     * `Work`dosyaların iş adlı bir alt dizine yerleştirileceğini belirtir.
4. Hizmetinize bir ad verin ve **Tamam**’a tıklayın.
5. Hizmetiniz iletişim için bir uç nokta ihtiyacı varsa, artık ServiceManifest. xml dosyasına protokol, bağlantı noktası ve tür ekleyebilirsiniz. Örneğin: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`
6. Artık, Visual Studio 'da çözümü hata ayıklaması yaparak yerel kümenizde paket ve yayımlama eylemini kullanabilirsiniz. Hazırsa, uygulamayı uzak bir kümeye yayımlayabilir veya çözümü kaynak denetimine iade edebilirsiniz.
7. Service Fabric Explorer ' de çalışan konuk yürütülebilir hizmetinizi nasıl görüntüleyekullanacağınızı görmek için [çalışan uygulamanızı denetleyin](#check-your-running-application) .

Örnek bir anlatım için bkz. [Visual Studio kullanarak ilk Konuk yürütülebilir uygulamanızı oluşturma](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Linux 'ta mevcut bir yürütülebilir dosyayı paketlemek ve dağıtmak için Yeumman kullanın

Linux üzerinde Konuk yürütülebilir dosyası oluşturma ve dağıtma yordamı, bir CSharp veya Java uygulamasını dağıtmaya benzer.

1. Bir terminal penceresinde `yo azuresfguest` yazın.
2. Uygulamanızı adlandırın.
3. Hizmetinizi adlandırın ve çalıştırılabilir dosyanın yolu ve ile çağrılması gereken parametreler dahil olmak üzere ayrıntıları sağlayın.

Yeumman, yükleme ve kaldırma betikleri ile birlikte uygun uygulama ve bildirim dosyalarıyla birlikte bir uygulama paketi oluşturur.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Mevcut bir yürütülebiliri el ile paketleme ve dağıtma
Konuk yürütülebiliri el ile paketleme işlemi aşağıdaki genel adımlara dayanır:

1. Paket dizin yapısını oluşturun.
2. Uygulamanın kodunu ve yapılandırma dosyalarını ekleyin.
3. Hizmet bildirim dosyasını düzenleyin.
4. Uygulama bildirim dosyasını düzenleyin.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](https://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Paket dizin yapısını oluşturma
[Azure Service Fabric uygulaması paketleme](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps)bölümünde açıklandığı gibi, dizin yapısını oluşturarak başlayabilirsiniz.

### <a name="add-the-applications-code-and-configuration-files"></a>Uygulamanın kodunu ve yapılandırma dosyalarını ekleme
Dizin yapısını oluşturduktan sonra uygulamanın kodunu ve yapılandırma dosyalarını kod ve yapılandırma dizinleri altına ekleyebilirsiniz. Ayrıca, kod veya yapılandırma dizinleri altında ek dizinler veya alt dizinler de oluşturabilirsiniz.

Uygulama kök dizininin `xcopy` içeriğini Service Fabric, bu nedenle, iki üst dizin oluşturma, kod ve ayar dışında başka bir şekilde kullanılmak üzere önceden tanımlanmış bir yapı yoktur. (İsterseniz farklı adlar seçebilirsiniz. Sonraki bölümde daha fazla ayrıntı vardır.)

> [!NOTE]
> Uygulamanın ihtiyaç duyacağı tüm dosya ve bağımlılıkları eklediğinizden emin olun. Service Fabric, uygulamanın hizmetlerinin dağıtılacağı kümedeki tüm düğümlerde uygulama paketinin içeriğini kopyalar. Paketin, uygulamanın çalışması için gereken tüm kodu içermesi gerekir. Bağımlılıkların zaten yüklü olduğunu varsaymayın.
>
>

### <a name="edit-the-service-manifest-file"></a>Hizmet bildirim dosyasını düzenleme
Sonraki adım, hizmet bildirim dosyasını aşağıdaki bilgileri içerecek şekilde düzenleyeceğiniz:

* Hizmet türünün adı. Bu, Service Fabric bir hizmeti tanımlamak için kullandığı bir KIMLIĞIDIR.
* Uygulamayı başlatmak için kullanılacak komut (ExeHost).
* Uygulamayı (SetupEntrypoint) ayarlamak için çalıştırılması gereken herhangi bir betik.

Aşağıda bir `ServiceManifest.xml` dosya örneği verilmiştir:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Aşağıdaki bölümler, dosyanın güncelleştirmeniz gereken farklı bölümlerinin üzerine gider.

#### <a name="update-servicetypes"></a>ServiceType 'yi Güncelleştir
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* İstediğiniz herhangi bir adı `ServiceTypeName`seçebilirsiniz. Bu değer, hizmeti tanımlamak için `ApplicationManifest.xml` dosyasında kullanılır.
* Belirtin `UseImplicitHost="true"`. Bu öznitelik, hizmetin bağımsız bir uygulamayı temel aldığı Service Fabric söyler. bu nedenle, tüm Service Fabric tek bir işlem olarak başlatmak ve sistem durumunu izlemek için gerekir.

#### <a name="update-codepackage"></a>CodePackage 'i Güncelleştir
CodePackage öğesi, hizmet kodunun konumunu (ve sürümünü) belirtir.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` Öğesi, hizmet kodunu içeren uygulama paketindeki dizinin adını belirtmek için kullanılır. `CodePackage`Ayrıca `version` özniteliğine sahiptir. Bu kod sürümünü belirtmek için kullanılabilir ve ayrıca, Service Fabric ' de uygulama yaşam döngüsü yönetim altyapısını kullanarak hizmetin kodunu yükseltmek için de kullanılabilir.

#### <a name="optional-update-setupentrypoint"></a>İsteğe bağlı: SetupEntrypoint güncelleştirme
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntryPoint öğesi, hizmetin kodu başlatılmadan önce yürütülmesi gereken çalıştırılabilir veya toplu iş dosyalarını belirtmek için kullanılır. Bu, isteğe bağlı bir adımdır; bu nedenle, başlatma gerekli olmadığında dahil edilmemelidir. SetupEntryPoint hizmeti her yeniden başlatıldığında yürütülür.

Yalnızca bir SetupEntryPoint vardır; bu nedenle, uygulamanın kurulumu birden çok komut dosyası gerektiriyorsa Kurulum betiklerinin tek bir toplu iş dosyasında gruplanmaları gerekir. SetupEntryPoint herhangi bir dosya türünü yürütebilir: çalıştırılabilir dosyalar, toplu iş dosyaları ve PowerShell cmdlet 'leri. Daha ayrıntılı bilgi için bkz. [SetupEntryPoint yapılandırma](service-fabric-application-runas-security.md).

Yukarıdaki örnekte, setupentrypoint, kod dizininin `LaunchConfig.cmd` `scripts` alt dizininde bulunan adlı bir toplu iş dosyası çalıştırır (WorkingFolder öğesinin codebase olarak ayarlandığı varsayılarak).

#### <a name="update-entrypoint"></a>Giriş noktasını Güncelleştir
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Hizmet bildirimi dosyasındaki öğesi, hizmeti nasıl başlatakullanacağınızı belirtmek için kullanılır. `EntryPoint`

`ExeHost` Öğesi hizmeti başlatmak için kullanılması gereken yürütülebilir dosyayı (ve bağımsız değişkenleri) belirtir. İsteğe bağlı olarak, programın `IsExternalExecutable="true"` kod paketinin `ExeHost` dışında bir dış yürütülebilir dosya olduğunu göstermek için özniteliğini ekleyebilirsiniz. Örneğin: `<ExeHost IsExternalExecutable="true">`.

* `Program`hizmeti başlatması gereken yürütülebilir dosyanın adını belirtir.
* `Arguments`yürütülebilir dosyaya geçirilmesi gereken bağımsız değişkenleri belirtir. Bağımsız değişkenlere sahip parametrelerin listesi olabilir.
* `WorkingFolder`başlatılacak işlemin çalışma dizinini belirtir. Üç değer belirtebilirsiniz:
  * `CodeBase`çalışma dizininin uygulama paketindeki (`Code` önceki dosya yapısındaki dizin) kod dizinine ayarlanacağım olduğunu belirtir.
  * `CodePackage`çalışma dizininin uygulama paketinin köküne ayarlanacağım olduğunu belirtir (`GuestService1Pkg` önceki dosya yapısında).
    * `Work`dosyaların iş adlı bir alt dizine yerleştirileceğini belirtir.

WorkingFolder, göreli yolların uygulama veya başlatma betikleri tarafından kullanılabilmesi için doğru çalışma dizinini ayarlamak için yararlıdır.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Uç noktaları güncelleştirme ve iletişim için Adlandırma Hizmeti kaydetme
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
Yukarıdaki örnekte, `Endpoint` öğesi uygulamanın dinleyebileceği uç noktaları belirtir. Bu örnekte, Node. js uygulaması 3000 numaralı bağlantı noktasında http 'yi dinler.

Ayrıca, diğer hizmetlerin bu hizmete yönelik uç nokta adresini bulabilmesi için bu uç noktayı Adlandırma Hizmeti yayımlamasını Service Fabric isteyebilirsiniz. Bu, Konuk yürütülebilir dosyaları olan hizmetler arasında iletişim kurabilmenizi sağlar.
Yayınlanan bitiş noktası adresi formundadır `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme`ve `PathSuffix` isteğe bağlı özniteliklerdir. `IPAddressOrFQDN`Bu yürütülebilir dosyanın yerleştirildiği düğümün IP adresi veya tam etki alanı adıdır ve sizin için hesaplanır.

Aşağıdaki örnekte, hizmet dağıtıldıktan sonra, Service Fabric Explorer hizmet örneği için `http://10.1.4.92:3000/myapp/` yayımlananlara benzer bir uç nokta görürsünüz. Ya da yerel bir makinedir, bkz `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Bu adresleri, hizmetler arasında iletişim kurmak için [ters ara sunucu](service-fabric-reverseproxy.md) ile birlikte kullanabilirsiniz.

### <a name="edit-the-application-manifest-file"></a>Uygulama bildirim dosyasını düzenleme
`Servicemanifest.xml` Dosyayı yapılandırdıktan sonra, doğru hizmet türü ve adının kullanıldığından emin olmak için `ApplicationManifest.xml` dosyada bazı değişiklikler yapmanız gerekir.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>Servicemanifestımport
`ServiceManifestImport` Öğesinde, uygulamaya dahil etmek istediğiniz bir veya daha fazla hizmeti belirtebilirsiniz. Hizmetlerine, `ServiceManifest.xml` dosyanın bulunduğu `ServiceManifestName`dizinin adını belirten ile başvurulur.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Günlüğe kaydetmeyi ayarlama
Konuk yürütülebilir dosyalarında, uygulama ve yapılandırma betiklerinin herhangi bir hata gösterip gösteruygulanmadığını öğrenmek için konsol günlüklerini görmeniz yararlı olur.
Konsol yeniden yönlendirme, `ServiceManifest.xml` dosyasında `ConsoleRedirection` öğesi kullanılarak yapılandırılabilir.

> [!WARNING]
> Uygulama yük devretmesini etkileyebileceğinden, bu uygulamayı üretimde dağıtılan bir uygulamada hiçbir şekilde kullanmayın. Bunu *yalnızca* yerel geliştirme ve hata ayıklama amacıyla kullanın.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection`konsol çıkışını (STDOUT ve stderr) çalışma dizinine yeniden yönlendirmek için kullanılabilir. Bu, Service Fabric kümesinde uygulamanın kurulumu veya yürütülmesi sırasında hata olmadığını doğrulama olanağı sağlar.

`FileRetentionCount`çalışma dizinine kaç dosya kaydedildiğini belirler. Örneğin, 5 değeri, önceki beş yürütmeler için günlük dosyalarının çalışma dizininde depolandığı anlamına gelir.

`FileMaxSizeInKb`günlük dosyalarının en büyük boyutunu belirtir.

Günlük dosyaları hizmetin çalışma dizinlerinin birine kaydedilir. Dosyaların nerede bulunduğunu öğrenmek için, hizmetin üzerinde çalıştığı düğümü ve hangi çalışma dizininin kullanıldığını öğrenmek için Service Fabric Explorer kullanın. Bu işlem, bu makalenin ilerleyen kısımlarında ele alınmıştır.

## <a name="deployment"></a>Dağıtım
Son adım, [uygulamanızı dağıtmaktır](service-fabric-deploy-remove-applications.md). Aşağıdaki PowerShell betiği, uygulamanızın yerel geliştirme kümesine nasıl dağıtılacağını gösterir ve yeni bir Service Fabric hizmetini başlatır.

```powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> Paket büyükse veya çok sayıda dosya içeriyorsa, görüntü deposuna kopyalamadan önce [paketi sıkıştırın](service-fabric-package-apps.md#compress-a-package) . [Buradan](service-fabric-deploy-remove-applications.md#upload-the-application-package)daha fazla bilgi edinin.
>

Bir Service Fabric hizmeti çeşitli "yapılandırmalarda" dağıtılabilir. Örneğin, tek veya birden çok örnek olarak dağıtılabilir veya Service Fabric kümesinin her bir düğümünde hizmetin bir örneği olacak şekilde dağıtılabilir.

Cmdlet 'in parametresi, Service Fabric kümesinde hizmetin kaç örneğinin başlatılması gerektiğini belirtmek için kullanılır. `InstanceCount` `New-ServiceFabricService` Dağıttığınız uygulamanın türüne bağlı `InstanceCount` olarak değeri ayarlayabilirsiniz. En yaygın iki senaryo şunlardır:

* `InstanceCount = "1"`. Bu durumda, bir hizmetin yalnızca bir örneği kümeye dağıtılır. Service Fabric Zamanlayıcı, hizmetin hangi düğüme dağıtılacağını belirler.
* `InstanceCount ="-1"`. Bu durumda, bir hizmetin bir örneği Service Fabric kümesindeki her düğüme dağıtılır. Sonuç, kümedeki her düğüm için hizmetin bir (ve yalnızca bir) örneğine sahip olur.

Bu, ön uç uygulamaları (örneğin, bir REST uç noktası) için yararlı bir yapılandırmadır, çünkü istemci uygulamaların uç noktasını kullanmak üzere kümedeki düğümlere "bağlanması" gerekir. Bu yapılandırma, örneğin, Service Fabric kümesinin tüm düğümleri bir yük dengeleyiciye bağlı olduğunda da kullanılabilir. İstemci trafiği daha sonra kümedeki tüm düğümlerde çalışan hizmet arasında dağıtılabilir.

## <a name="check-your-running-application"></a>Çalışan uygulamanızı denetleyin
Service Fabric Explorer, hizmetin çalıştığı düğümü belirler. Bu örnekte, Düğüm1 üzerinde çalışır:

![Hizmetin çalıştığı düğüm](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Düğüme gidip uygulamaya gözattığınızda, disk üzerindeki konumu da dahil olmak üzere temel düğüm bilgilerini görürsünüz.

![Diskteki konum](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Sunucu Gezgini kullanarak dizine gözattığınızda, çalışma dizinini ve hizmetin günlük klasörünü aşağıdaki ekran görüntüsünde gösterildiği gibi bulabilirsiniz: 

![Günlüğün konumu](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, bir konuk yürütülebilir dosyasını paketlemeyi ve Service Fabric için dağıtmayı öğrendiniz. İlgili bilgi ve görevler için aşağıdaki makalelere bakın.

* Paketleme aracının ön sürümüne bir bağlantı dahil olmak üzere, [Konuk yürütülebilir dosyası paketleme ve dağıtma örneği](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [REST kullanarak adlandırma hizmeti üzerinden iletişimC# kuran iki konuk yürütülebilir dosya (ve NodeJS) örneği](https://github.com/Azure-Samples/service-fabric-containers)
* [Konuk tarafından yürütülebilir birden çok uygulama dağıtma](service-fabric-deploy-multiple-apps.md)
* [Visual Studio kullanarak ilk Service Fabric uygulamanızı oluşturma](service-fabric-tutorial-create-dotnet-app.md)
