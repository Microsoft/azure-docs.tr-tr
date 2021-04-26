---
title: Service Fabric içindeki RunToCompletion semantiği
description: Service Fabric içindeki RunToCompletion semantiğini açıklar.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000275"
---
# <a name="runtocompletion"></a>RunToCompletion

Sürüm 7,1 ' den başlayarak, [kapsayıcılar][containers-introduction-link] ve [Konuk yürütülebilir][guest-executables-introduction-link] uygulamalar için **runtocompletion** semantiğini destekler Service Fabric. Bu semantikler, her zaman uygulama ve Hizmetleri çalıştırmanın yanı sıra bir görevi ve çıkışı tamamlamak için uygulama ve hizmetleri etkinleştirir.

Bu makaleye devam etmeden önce [Service Fabric uygulama modeli][application-model-link] ve [Service Fabric barındırma modeli][hosting-model-link]hakkında bilgi sahibi olmanız önerilir.

> [!NOTE]
> RunToCompletion semantiği Şu anda [Reliable Services][reliable-services-link] programlama modeli kullanılarak yazılan hizmetler için desteklenmez.
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion semantiği ve belirtimi
[ServiceManifest içeri aktarılırken][application-and-service-manifests-link]RunToCompletion semantiği bir **ExecutionPolicy** olarak belirtilebilir. Belirtilen ilke, ServiceManifest 'i kapsayan tüm kod paketleri tarafından devralınır. Aşağıdaki ApplicationManifest.xml kod parçacığı bir örnek sağlar.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** aşağıdaki iki özniteliğe izin verir:
* **Tür:** **runtocompletion** Şu anda bu öznitelik için izin verilen tek değerdir.
* **Yeniden Başlat:** Bu öznitelik, hata durumunda ServicePackage 'i kapsayan kod paketlerine uygulanan yeniden başlatma ilkesini belirtir. **Sıfır olmayan çıkış kodu** ile çıkış yapılan bir CodePackage başarısız olduğu kabul edilir. Bu öznitelik için izin verilen değerler **OnFailure** ve **hiçbir** şekilde varsayılan olarak **OnFailure** .

Yeniden başlatma ilkesi **OnFailure** olarak ayarlandığında, herhangi bir CodePackage **(sıfır olmayan çıkış kodu)** başarısız olursa, yinelenen hatalar arasında geri dönerek yeniden başlatılır. Yeniden başlatma ilkesi **hiçbir** şekilde ayarlandığında, herhangi bir CodePackage başarısız olursa, DeployedServicePackage dağıtım durumu **başarısız** olarak Işaretlenir, ancak diğer kod paketlerinin yürütmeye devam etmesine izin verilir. ServicePackage 'i kapsayan tüm kod paketleri başarıyla tamamlanarak **(çıkış kodu 0)** çalışıyorsa, DeployedServicePackage dağıtım durumu **RanToCompletion** olarak işaretlenir. 

## <a name="complete-example-using-runtocompletion-semantics"></a>RunToCompletion semantiğini kullanarak örnek tamamlama

RunToCompletion semantiğini kullanarak komple bir örneğe bakalım.

> [!IMPORTANT]
> Aşağıdaki örnek, [Service Fabric ve Docker kullanarak Windows kapsayıcı uygulamaları][containers-getting-started-link]oluşturma konusunda benzerlik olduğunu varsayar.
>
> Bu örnek, mcr.microsoft.com/windows/nanoserver:1809 öğesine başvurur. Windows Server kapsayıcıları, bir konak işletim sisteminin tüm sürümleri arasında uyumlu değildir. Daha fazla bilgi için bkz. [Windows kapsayıcı sürümü uyumluluğu](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Aşağıdaki ServiceManifest.xml, kapsayıcıları temsil eden iki CodePackage oluşan bir ServicePackage 'i açıklar. *RunToCompletionCodePackage1* yalnızca **stdout** 'a bir ileti kaydeder ve çıkılıyor. *RunToCompletionCodePackage2* bir süre için geri döngü adresine ping atar ve sonra **0**, **1** veya **2** çıkış kodu ile çıkar.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

Aşağıdaki ApplicationManifest.xml, yukarıda açıklanan ServiceManifest.xml dayalı bir uygulamayı açıklar. **OnFailure** yeniden başlatma Ilkesiyle *Windowsruntocompletionservicepackage* için **runtocompletion** **ExecutionPolicy** öğesini belirtir. *Windowsruntocompletionservicepackage*'in etkinleştirilmesinden sonra, bileşen kod paketleri başlatılır. *RunToCompletionCodePackage1* ilk etkinleştirme sırasında başarıyla çıkış olmalıdır. Bununla birlikte, *RunToCompletionCodePackage2* başarısız olabilir **(sıfır olmayan çıkış kodu)**, bu durumda yeniden başlatma ilkesi **OnFailure** olduğundan yeniden başlatılır.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>DeployedServicePackage dağıtım durumu sorgulanıyor
Bir DeployedServicePackage dağıtım durumu, [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] kullanılarak PowerShell 'Den veya [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync (String, Uri, String)][deployed-service-package-fabricclient-link] kullanılarak C# ' den sorgulanabilir

## <a name="considerations-when-using-runtocompletion-semantics"></a>RunToCompletion semantiğinin kullanılmasıyla ilgili konular

Geçerli RunToCompletion desteği için aşağıdaki noktaların not edilmelidir.
* Bu [semantikler yalnızca kapsayıcılar][containers-introduction-link] ve [Konuk yürütülebilir][guest-executables-introduction-link] uygulamalar için desteklenir.
* RunToCompletion semantiği olan uygulamalara yönelik yükseltme senaryolarına izin verilmez. Kullanıcılar gerekirse bu uygulamaları silip yeniden oluşturur.
* Yük devretme olayları, başarıyla tamamlandıktan sonra, aynı düğümdeki veya kümedeki diğer düğümlerde kod paketlerinin yeniden yürütülmesine neden olabilir. Yük devretme olayları örnekleri, düğüm yeniden başlatılır ve bir düğümdeki çalışma zamanı yükseltmeleri Service Fabric.

## <a name="next-steps"></a>Sonraki adımlar

İlgili bilgiler için aşağıdaki makalelere bakın.

* [Service Fabric ve kapsayıcılar.][containers-introduction-link]
* [Service Fabric ve konuk yürütülebilir dosyaları.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync
