---
title: Azure Service Fabric uygulama gizli dizilerini yönetme | Microsoft Docs
description: Service Fabric uygulamasındaki gizli değerlerin güvenliğini nasıl sağlayacağınızı öğrenin (platformdan bağımsız).
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: a24f670314d2f6679e37b438a74421e0e84604e2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075495"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Service Fabric uygulamalarında şifreli gizli dizileri yönetme
Bu kılavuz, Service Fabric uygulamasında gizli dizileri yönetme adımlarında size yol gösterir. Gizlilikler, depolama bağlantı dizeleri, parolalar veya düz metin olarak işlenmemelidir diğer değerler gibi herhangi bir hassas bilgi olabilir.

Service Fabric uygulamasında şifrelenmiş gizli dizileri kullanmak üç adımdan oluşur:
* Bir şifreleme sertifikası kurun ve gizli dizileri şifreleyin.
* Bir uygulamada şifrelenmiş gizli dizileri belirtin.
* Şifrelenmiş gizli dizileri hizmet kodundan çözün.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Şifreleme sertifikası ayarlama ve gizli dizileri şifreleme
Şifreleme sertifikası ayarlama ve parolaları şifrelemek için kullanma, Windows ve Linux arasında farklılık gösterir.
* [Windows kümelerinde şifreleme sertifikası ayarlayın ve gizli dizileri şifreleyin.][secret-management-windows-specific-link]
* [Bir şifreleme sertifikası kurun ve Linux kümelerinde gizli dizileri şifreleyin.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Bir uygulamada şifrelenmiş gizli dizileri belirtme
Önceki adımda, bir sertifika ile gizli dizi şifrelemesini ve bir uygulamada kullanılmak üzere bir Base-64 kodlu dizeyi oluşturmayı açıklar. Bu Base-64 kodlu dize, bir hizmetin Settings. xml dosyasında şifrelenmiş bir [parametre][parameters-link] olarak veya bir hizmetin servicemanifest. xml dosyasında şifrelenmiş bir [ortam değişkeni][environment-variables-link] olarak belirtilebilir.

Hizmetinizin Settings. xml yapılandırma dosyasında, `IsEncrypted` özniteliği `true`olarak ayarlanan şifreli bir [parametre][parameters-link] belirtin:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Hizmetinizin ServiceManifest. xml dosyasında `Type` özniteliği `Encrypted`olarak ayarlanan bir şifrelenmiş [ortam değişkeni][environment-variables-link] belirtin:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

Gizli dizi, uygulama bildiriminde bir sertifika belirtilerek Service Fabric uygulamanıza da eklenmelidir. **ApplicationManifest. xml** ' ye bir **Secretscercertificate** öğesi ekleyin ve istenen sertifikanın parmak izini ekleyin.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

### <a name="inject-application-secrets-into-application-instances"></a>Uygulama örneklerine uygulama gizli dizilerini Ekle
İdeal olarak, farklı ortamlara dağıtım mümkün olduğunca otomatikleştirilebilir. Bu, bir derleme ortamında gizli şifreleme gerçekleştirerek ve uygulama örnekleri oluştururken şifreli gizli dizileri parametre olarak sağlayarak gerçekleştirilebilir.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Settings. xml dosyasında geçersiz kılınabilir parametreler kullan
Settings. xml yapılandırma dosyası, uygulama oluşturma zamanında sağlanabilen geçersiz kılınabilir parametrelere izin verir. Bir parametre için değer sağlamak yerine `MustOverride` özniteliğini kullanın:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Settings. xml ' deki değerleri geçersiz kılmak için, ApplicationManifest. xml dosyasında hizmet için bir geçersiz kılma parametresi bildirin:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Şimdi, uygulamanın bir örneği oluşturulurken değer bir *uygulama parametresi* olarak belirtilebilir. Bir yapı sürecinde kolay tümleştirme için, bir uygulama örneğinin oluşturulması PowerShell kullanılarak C#betiklenebilir veya içinde yazılabilir.

PowerShell kullanarak, parametresi `New-ServiceFabricApplication` komutuna [karma tablo](https://technet.microsoft.com/library/ee692803.aspx)olarak sağlanır:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Kullanarak C#, uygulama parametreleri bir `ApplicationDescription` `NameValueCollection`olarak belirtilir:

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Şifrelenmiş gizli dizileri hizmet kodundan çöz
[Parametrelere][parameters-link] ve [ortam değişkenlerine][environment-variables-link] erişmek için API 'ler, şifrelenmiş değerlerin kolay bir şekilde çözülmesi için izin verir. Şifrelenmiş dize, şifreleme için kullanılan sertifikayla ilgili bilgiler içerdiğinden, sertifikayı el ile belirtmeniz gerekmez. Sertifikanın yalnızca hizmetin üzerinde çalıştığı düğüme yüklenmesi gerekir.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Sonraki adımlar
* Service Fabric [gizli dizi deposu](service-fabric-application-secret-store.md) 
* [Uygulama ve hizmet güvenliği](service-fabric-application-and-service-security.md) hakkında daha fazla bilgi edinin

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md