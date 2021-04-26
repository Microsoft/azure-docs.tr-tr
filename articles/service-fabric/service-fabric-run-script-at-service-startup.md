---
title: Bir Azure Service Fabric hizmeti başlatıldığında betik çalıştırma
description: Service Fabric Service Setup giriş noktası için bir ilkeyi yapılandırmayı ve hizmet Başlangıç zamanında bir betiği çalıştırmayı öğrenin.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012536"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Yerel kullanıcı veya sistem hesabı olarak bir hizmet başlangıcı komut dosyası çalıştırma
Bir Service Fabric hizmeti yürütülebilir dosyası başlamadan önce, bazı yapılandırma veya kurulum çalışmaları çalıştırmak gerekebilir.  Örneğin, ortam değişkenlerini yapılandırma. Hizmet yürütülebilir dosyası hizmetin hizmet bildiriminde başlatılmadan önce çalıştırılacak bir betik belirtebilirsiniz. Hizmet kurulumu giriş noktası için bir RunAs ilkesi yapılandırarak, kurulum yürütülebilirinin altında çalıştığı hesabı değiştirebilirsiniz.  Ayrı bir kurulum giriş noktası, yüksek ayrıcalıklı yapılandırmayı kısa bir süre içinde çalıştırmanıza olanak tanır; böylece hizmet ana bilgisayarı yürütülebilir dosyasının uzun süreler boyunca yüksek ayrıcalıklarla çalışması gerekmez.

Kurulum giriş noktası ( [hizmet bildiriminde](service-fabric-application-and-service-manifests.md)**setupentrypoint** ), varsayılan olarak, diğer herhangi bir giriş noktasından önce Service Fabric (genellikle *NetworkService* hesabı) ile aynı kimlik bilgileriyle çalışan ayrıcalıklı bir giriş noktasıdır. **Giriş noktası** tarafından belirtilen yürütülebilir dosya genellikle uzun süre çalışan hizmet ana bilgisayarı olur. **Setupentrypoint** yürütülebilir dosyası başarıyla çıktıktan sonra **entryPoint** çalıştırılabilir dosyası çalıştırılır. Ortaya çıkan işlem izlenir ve yeniden başlatılır ve sonlandırıldığında veya kilitlenirse **Setupentrypoint** ile yeniden başlar. 

## <a name="configure-the-service-setup-entry-point"></a>Hizmet kurulumu giriş noktasını yapılandırma
Aşağıda, Service **Setupentrypoint** içinde *MySetup.bat* bir kurulum betiği belirten, durum bilgisi olmayan bir hizmet için basit bir hizmet bildirimi örneği verilmiştir.  **Bağımsız değişkenler** çalıştırıldığında bağımsız değişkenleri komut dosyasına geçirmek için kullanılır.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Bir hizmet kurulumu giriş noktası için ilkeyi yapılandırma
Varsayılan olarak, hizmet kurulumu giriş noktası yürütülebilir dosyası Service Fabric (genellikle *NetworkService* hesabı) ile aynı kimlik bilgileri altında çalışır.  Uygulama bildiriminde, bir yerel sistem hesabı veya bir yönetici hesabı altında Başlangıç betiğini çalıştırmak için güvenlik izinlerini değiştirebilirsiniz.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>İlkeyi bir yerel sistem hesabı kullanarak yapılandırma
Aşağıdaki uygulama bildirimi örneği, Service Setup giriş noktasının Kullanıcı Yöneticisi hesabı (SetupAdminUser) altında çalışacak şekilde nasıl yapılandırılacağını gösterir.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

İlk olarak, SetupAdminUser gibi bir Kullanıcı adı ile bir **sorumlular** bölümü oluşturun. SetupAdminUser Kullanıcı hesabı, Administrators sistem grubunun bir üyesidir.

Ardından, **servicemanifestımport** bölümünün altında, bu sorumluyu **setupentrypoint**'ye uygulamak için bir ilke yapılandırın. Bu ilke, **MySetup.bat** dosya çalıştırıldığında SetupAdminUser (yönetici ayrıcalıklarıyla) olarak çalışmalıdır Service Fabric söyler. Ana giriş noktasına bir ilke *uygulanmadığımızdan* **MyServiceHost.exe** kod, sistem **NetworkService** hesabı altında çalışır. Bu, tüm hizmet giriş noktalarının çalıştırıldığı varsayılan hesaptır.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>İlkeyi yerel sistem hesapları kullanarak yapılandırma
Genellikle, başlangıç betiğini yönetici hesabı yerine yerel bir sistem hesabı kullanılarak çalıştırmak tercih edilir. RunAs ilkesini Yöneticiler grubunun bir üyesi olarak çalıştırmak, bilgisayarların Kullanıcı Access Control (UAC) varsayılan olarak etkinleştirildiği için genellikle iyi çalışmaz. Bu gibi durumlarda, Administrators grubuna yerel kullanıcı eklemek yerine SetupEntryPoint 'yi LocalSystem olarak çalıştırmanız önerilir. Aşağıdaki örnek, SetupEntryPoint 'in LocalSystem olarak çalışacağı ayarı gösterir:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> Linux kümeleri için, bir hizmeti veya kurulum giriş noktasını **kök** olarak çalıştırmak Için,  **AccountType** öğesini **LocalSystem** olarak belirtebilirsiniz.

## <a name="run-a-script-from-the-setup-entry-point"></a>Kurulum giriş noktasından komut dosyası çalıştırma
Şimdi, yönetici ayrıcalıkları altında çalıştırmak için projeye bir başlangıç betiği ekleyin. 

Visual Studio 'da, hizmet projesine sağ tıklayın ve *MySetup.bat* adlı yeni bir dosya ekleyin.

Daha sonra, *MySetup.bat* dosyasının hizmet paketine eklendiğinden emin olun. Varsayılan olarak, bu değildir. Dosyayı seçin, bağlam menüsünü almak için sağ tıklayın ve **Özellikler**' i seçin. Özellikler iletişim kutusunda, **Çıkış Dizinine Kopyala** ' nın **daha yeniyse kopyala** olarak ayarlandığından emin olun. Aşağıdaki ekran görüntüsüne bakın.

![SetupEntryPoint toplu iş dosyası için Visual Studio CopyToOutput][image1]

Şimdi *MySetup.bat* dosyasını düzenleyin ve aşağıdaki komutları ekleyerek bir sistem ortam değişkeni ayarlayın ve bir metin dosyası çıkışı yapın:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Ardından, çözümü derleyin ve yerel bir geliştirme kümesine dağıtın. Hizmet başladıktan sonra, [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)gösterildiği gibi, MySetup.bat dosyasının başarılı olduğunu iki şekilde görebilirsiniz. Bir PowerShell komut istemi açın ve şunu yazın:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Ardından, hizmetin dağıtıldığına ve [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)başlatıldığı düğümün adına göz önünde. Örneğin, düğüm 2. Ardından, **testvariable** değerini gösteren out.txt dosyasını bulmak için uygulama örneği çalışma klasörüne gidin. Örneğin, bu hizmet düğüm 2 ' ye dağıtılmışsa, **MyApplicationType** için bu yola gidebilirsiniz:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Bir kurulum giriş noktasından PowerShell komutlarını çalıştırma
PowerShell 'i **Setupentrypoint** noktasından çalıştırmak için, bir PowerShell dosyasına işaret eden bir toplu iş dosyasında **PowerShell.exe** çalıştırabilirsiniz. İlk olarak, hizmet projesine bir PowerShell dosyası ekleyin; örneğin, **MySetup.ps1**. Dosyanın hizmet paketine da dahil olması için *daha yeni bir özellik varsa kopyayı* ayarlamayı unutmayın. Aşağıdaki örnek, **testvariable** adlı bir sistem ortam değişkenini ayarlayan MySetup.ps1 adlı bir PowerShell dosyasını Başlatan bir örnek toplu iş dosyası gösterir.

Bir PowerShell dosyası başlatmak için MySetup.bat:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

PowerShell dosyasında, bir sistem ortam değişkeni ayarlamak için aşağıdakini ekleyin:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Varsayılan olarak, toplu iş dosyası çalıştırıldığında dosyalar için **iş** adlı uygulama klasörüne bakar. Bu durumda MySetup.bat çalıştığında, bu dosyanın, uygulama **kodu paketi** klasörü olan aynı klasörde MySetup.ps1 dosyasını bulmasını istiyoruz. Bu klasörü değiştirmek için çalışma klasörünü ayarlayın:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Konsol yeniden yönlendirme kullanarak bir başlangıç komut dosyasında yerel olarak hata ayıklama
Bazen, konsol çıkışının bir kurulum betiği çalıştırmasını görmek için hata ayıklama amacıyla yararlı olur. Hizmet bildiriminde, çıktıyı bir dosyaya yazan kurulum giriş noktasında konsol yeniden yönlendirme ilkesi ayarlayabilirsiniz. Dosya çıktısı, uygulamanın dağıtıldığı ve çalıştırıldığı küme düğümünde **log** adlı uygulama klasörüne yazılır. 

> [!WARNING]
> Uygulama yük devretmesini etkileyebileceğinden, bu uygulamayı üretimde dağıtılan bir uygulamada hiçbir şekilde kullanmayın. Bunu *yalnızca* yerel geliştirme ve hata ayıklama amacıyla kullanın.  
> 
> 

Aşağıdaki hizmet bildirimi örneği, bir FileRetentionCount değeri ile konsol yeniden yönlendirmeyi ayarlamayı gösterir:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Şimdi bir **echo** komutu yazmak için MySetup.ps1 dosyasını değiştirirseniz, bu işlem hata ayıklama amacıyla çıktı dosyasına yazılır:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Betiğiniz hata ayıkladıktan sonra bu konsol yeniden yönlendirme ilkesini hemen kaldırın.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Sonraki adımlar
* [Uygulama ve hizmet güvenliği hakkında bilgi edinin](service-fabric-application-and-service-security.md)
* [Uygulama modelini anlama](service-fabric-application-model.md)
* [Hizmet bildiriminde kaynakları belirtme](service-fabric-service-manifest-resources.md)
* [Uygulama dağıtma](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
