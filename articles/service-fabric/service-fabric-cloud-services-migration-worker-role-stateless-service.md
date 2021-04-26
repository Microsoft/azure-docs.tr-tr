---
title: Azure Cloud Services uygulamalarını Service Fabric Dönüştür
description: Bu kılavuzda Cloud Services Web ve çalışan rolleri ve durum bilgisi olmayan hizmetler Service Fabric karşılaştırılmaktadır Cloud Services ' den Service Fabric 'ye geçiş yapılır.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: cf593f793aabf2a0650684ed8d02fe02d756ec2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575746"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Durum bilgisi olmayan hizmetlere Service Fabric Web ve çalışan rollerini dönüştürmeye yönelik kılavuz
Bu makalede, Cloud Services Web ve çalışan rollerinizin durum bilgisi olmayan hizmetlere Service Fabric nasıl geçirileceği açıklanır. Bu, genel mimarinin kabaca aynı kalmasını sağlayan uygulamalar için Cloud Services Service Fabric en basit geçiş yoludur.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Uygulama projesine Service Fabric bulut hizmeti projesi
 Bir bulut hizmeti projesi ve bir Service Fabric uygulama projesi benzer bir yapıya sahiptir ve her ikisi de uygulamanızın dağıtım birimini temsil eder. Bu, her biri uygulamanızı çalıştırmak için dağıtılan tüm paketi tanımlar. Bir bulut hizmeti projesi bir veya daha fazla Web ya da çalışan rolü içerir. Benzer şekilde, bir Service Fabric uygulama projesi bir veya daha fazla hizmet içerir. 

Bu fark, bulut hizmeti projesinin, uygulama dağıtımını bir VM dağıtımıyla bağlamasını ve bu nedenle, Service Fabric uygulama projesinin yalnızca bir Service Fabric kümesinde var olan bir VM kümesine dağıtılacak bir uygulama tanımladığı şekilde tanımlar. Service Fabric kümesi, bir Kaynak Yöneticisi şablonu veya Azure portal aracılığıyla yalnızca bir kez dağıtılır ve bu uygulamaya birden çok Service Fabric uygulama dağıtılabilir.

![Service Fabric ve Cloud Services projesi karşılaştırması][3]

## <a name="worker-role-to-stateless-service"></a>Çalışan rolünün durum bilgisiz hizmetine
Kavramsal olarak, bir çalışan rolü durum bilgisiz iş yükünü temsil eder, yani iş yükünün her örneği özdeş olur ve istekler dilediğiniz zaman herhangi bir örneğe yönlendirilebilir. Her örneğin önceki isteği hatırlamaları beklenmez. İş yükünün üzerinde çalıştığı durum, Azure Tablo depolama veya Azure Cosmos DB gibi bir dış durum deposu tarafından yönetilir. Service Fabric, bu iş yükü türü, durum bilgisi olmayan bir hizmet tarafından temsil edilir. Bir çalışan rolünü Service Fabric geçirmek için en basit yaklaşım, çalışan rolü kodu durumsuz bir hizmete dönüştürülerek yapılabilir.

![Çalışan rolünün durum bilgisiz hizmetine][4]

## <a name="web-role-to-stateless-service"></a>Web rolünün durum bilgisiz hizmetine
Çalışan rolüne benzer şekilde, bir Web rolü de durum bilgisiz iş yükünü temsil eder ve bu nedenle kavramsal olarak Service Fabric durum bilgisi olmayan bir hizmetle eşleştirilebilir. Ancak, Web rollerinin aksine Service Fabric IIS 'yi desteklemez. Web rolünden bir web rolünden durum bilgisiz olmayan bir hizmete geçirmek için önce kendiliğinden barındırılabilen ve IIS ya da System. Web 'e bağlı olmayan ASP.NET Core 1 gibi bir Web çerçevesine geçmeyi gerektirir.

| **Uygulama** | **Desteklenir** | **Geçiş yolu** |
| --- | --- | --- |
| ASP.NET Web Forms |No |ASP.NET Core 1 MVC 'ye Dönüştür |
| ASP.NET MVC |Geçişle |ASP.NET Core 1 MVC 'ye yükselt |
| ASP.NET Web API |Geçişle |Şirket içinde barındırılan sunucu veya ASP.NET Core 1 kullanın |
| ASP.NET Core 1 |Yes |Yok |

## <a name="entry-point-api-and-lifecycle"></a>Giriş noktası API 'SI ve yaşam döngüsü
Çalışan rolü ve Service Fabric hizmeti API 'Leri benzer giriş noktaları sunar: 

| **Girdi Noktası** | **Çalışan rolü** | **Service Fabric hizmeti** |
| --- | --- | --- |
| İşleniyor |`Run()` |`RunAsync()` |
| VM başlatma |`OnStart()` |Yok |
| VM durdur |`OnStop()` |Yok |
| İstemci istekleri için açık dinleyicisi |Yok |<ul><li> `CreateServiceInstanceListener()` durum bilgisiz için</li><li>`CreateServiceReplicaListener()` durum bilgisi için</li></ul> |

### <a name="worker-role"></a>Çalışan rolü
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Durum bilgisi olmayan Service Fabric hizmeti
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Her ikisinde de işlemeye başlamak için bir birincil "Çalıştır" geçersiz kılması vardır. Service Fabric Hizmetleri, `Run` `Start` ve `Stop` tek bir giriş noktasına birleştirir `RunAsync` . Hizmetiniz başladığında çalışmaya başlayabilir `RunAsync` ve `RunAsync` yöntemin CancellationToken 'a sinyal geldiğinde çalışmayı durdurmalıdır. 

Çalışan rollerinin yaşam döngüsü ve yaşam süresi ile Service Fabric hizmetleri arasında birkaç temel fark vardır:

* **Yaşam döngüsü:** En büyük fark, bir çalışan rolünün bir sanal makine olduğu ve bu nedenle yaşam döngüsünün VM 'nin başladığı ve durdurduğu olayları içeren VM 'ye bağlı olması gerektiğidir. Bir Service Fabric hizmeti VM yaşam döngüsüyle ayrı bir yaşam döngüsüne sahiptir, bu nedenle, ilişkili olmadıkları için konak VM veya makinenin başladığı ve durdurulacağı olayları içermez.
* **Ömür:** Yöntem çıkış olursa bir çalışan rolü örneği geri dönüştürülecek `Run` . `RunAsync`Service Fabric bir hizmette yöntemi, ancak tamamlanmayı çalıştırabilir ve hizmet örneği kalır. 

Service Fabric, istemci isteklerini dinleyen hizmetler için isteğe bağlı bir iletişim kurulum giriş noktası sağlar. RunAsync ve Communication giriş noktası, Service Fabric Hizmetleri 'nde isteğe bağlı geçersiz kılmalardır. hizmetiniz yalnızca istemci isteklerini dinleyebilir veya yalnızca bir işleme döngüsü çalıştırabilir ya da her ikisi de çalıştırılabilir, çünkü istemci isteklerini dinlemeye devam edebilir.

## <a name="application-api-and-environment"></a>Uygulama API 'SI ve ortamı
Cloud Services Environment API 'SI, geçerli sanal makine örneği ve diğer VM rolü örnekleri hakkındaki bilgileri ve işlevleri sağlar. Service Fabric, çalışma zamanı ile ilgili bilgileri ve hizmetin Şu anda üzerinde çalıştığı düğüm hakkında bazı bilgileri sağlar. 

| **Ortam görevi** | **Bulut Hizmetleri** | **Service Fabric** |
| --- | --- | --- |
| Yapılandırma ayarları ve değişiklik bildirimi |`RoleEnvironment` |`CodePackageActivationContext` |
| Yerel depolama |`RoleEnvironment` |`CodePackageActivationContext` |
| Uç nokta bilgileri |`RoleInstance` <ul><li>Geçerli örnek: `RoleEnvironment.CurrentRoleInstance`</li><li>Diğer roller ve örnek: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` Geçerli düğüm adresi için</li><li>`FabricClient` ve `ServicePartitionResolver` hizmet uç noktası bulma için</li> |
| Ortam öykünmesi |`RoleEnvironment.IsEmulated` |Yok |
| Eşzamanlı değişiklik olayı |`RoleEnvironment` |Yok |

## <a name="configuration-settings"></a>Yapılandırma ayarları
Cloud Services içindeki yapılandırma ayarları bir VM rolü için ayarlanır ve bu VM rolünün tüm örneklerine uygulanır. Bu ayarlar, ServiceConfiguration. *. cscfg dosyalarında ayarlanan anahtar-değer çiftleridir ve doğrudan RoleEnvironment üzerinden erişilebilir. Service Fabric, bir sanal makine birden çok hizmet ve uygulamayı barındırabildiğinden, ayarlar her bir hizmete ve her bir uygulamaya tek tek uygulanır. Bir hizmet üç paketten oluşur:

* **Kod:** hizmet yürütülebilir dosyalarını, ikili dosyaları, dll 'leri ve bir hizmetin çalışması gereken diğer dosyaları içerir.
* **Yapılandırma:** bir hizmetin tüm yapılandırma dosyaları ve ayarları.
* **Veri:** hizmetle ilişkili statik veri dosyaları.

Bu paketlerin her biri bağımsız olarak sürümlenebilir ve yükseltilebilir. Cloud Services benzer şekilde, bir yapılandırma paketi bir API aracılığıyla programlı olarak erişilebilir ve bir yapılandırma paketi değişikliği hizmetine bildirimde bulunan olaylar kullanılabilir. Bir Settings.xml dosyası, anahtar-değer yapılandırması ve bir App.config dosyasının uygulama ayarları bölümüne benzer programlı erişim için kullanılabilir. Ancak, Cloud Services aksine, bir Service Fabric yapılandırma paketi herhangi bir biçimde herhangi bir biçimde, XML, JSON, YAML veya özel bir ikili biçimi olmak üzere herhangi bir biçimde herhangi bir yapılandırma dosyası içerebilir. 

### <a name="accessing-configuration"></a>Yapılandırmaya erişme
#### <a name="cloud-services"></a>Cloud Services
ServiceConfiguration. *. cscfg öğesinden yapılandırma ayarlarına aracılığıyla erişilebilir `RoleEnvironment` . Bu ayarlar, aynı bulut hizmeti dağıtımında tüm rol örnekleri için genel kullanıma sunulmuştur.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Her hizmet kendi kendine ait ayrı yapılandırma paketine sahiptir. Genel yapılandırma ayarları için bir kümedeki tüm uygulamalar tarafından erişilebilen yerleşik bir mekanizma yoktur. Bir yapılandırma paketi içinde Service Fabric özel Settings.xml yapılandırma dosyasını kullanırken, Settings.xml değerlerinin uygulama düzeyinde üzerine yazılabilir ve uygulama düzeyinde yapılandırma ayarları mümkün hale getirebilirsiniz.

Yapılandırma ayarlarına, her hizmet örneği içinde hizmet aracılığıyla erişilir `CodePackageActivationContext` .

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Yapılandırma güncelleştirme olayları
#### <a name="cloud-services"></a>Cloud Services
`RoleEnvironment.Changed`Bu olay, bir yapılandırma değişikliği gibi, ortamda bir değişiklik olduğunda tüm rol örneklerine bildirimde bildirmek için kullanılır. Bu, yapılandırma güncelleştirmelerini, rol örneklerini geri dönüşüme açmadan veya bir çalışan işlemi yeniden başlatmadan kullanmak için kullanılır.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Bir hizmet kodu, yapılandırma ve veri içindeki üç paket türünün her biri, bir paketin güncelleştirildiği, eklendiği veya kaldırıldığı durumlarda hizmet örneğine bildirimde bulunan olaylardır. Bir hizmet, her türde birden çok paket içerebilir. Örneğin, bir hizmet birden fazla yapılandırma paketine sahip olabilir, her biri ayrı ayrı sürümlü ve yükseltilebilir. 

Bu olaylar, hizmet paketlerini yeniden başlatmanıza gerek kalmadan hizmet paketlerinde yapılan değişiklikleri kullanmak için kullanılabilir.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Başlangıç görevleri
Başlangıç görevleri, bir uygulama başlamadan önce gerçekleştirilen eylemlerdir. Bir başlangıç görevi, genellikle yükseltilmiş ayrıcalıklar kullanılarak kurulum betiklerini çalıştırmak için kullanılır. Hem Cloud Services hem de Service Fabric başlatma görevlerini destekler. Ana fark Cloud Services, bir başlangıç görevinin bir rol örneğinin parçası olduğu için bir sanal makineye bağlı olması, Service Fabric bir başlangıç görevinin belirli bir sanal makineye bağlı olmayan bir hizmete bağlı olması durumunda.

| Service Fabric | Cloud Services |
| --- | --- |
| Yapılandırma konumu |ServiceDefinition. csdef |
| Ayrıcalıklar |"sınırlı" veya "yükseltilmiş" |
| Sıralama |"basit", "arka plan", "ön plan" |

### <a name="cloud-services"></a>Cloud Services
Cloud Services, ServiceDefinition. csdef içindeki her rol için bir başlangıç giriş noktası yapılandırılır. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
Service Fabric, ServiceManifest.xml hizmetinde hizmet başına bir başlangıç giriş noktası yapılandırılır:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>Geliştirme ortamı hakkında bir göz
Hem Cloud Services hem de Service Fabric, Visual Studio ile birlikte proje şablonları ve hata ayıklama, yapılandırma ve hem yerel olarak hem de Azure 'da dağıtma desteğiyle tümleşiktir. Hem Cloud Services hem de Service Fabric yerel bir geliştirme çalışma zamanı ortamı sağlar. Bunun farkı, bulut hizmeti geliştirme çalışma zamanının çalıştığı Azure ortamına öykünücüken bir öykünücü kullanmamasından Service Fabric, tam Service Fabric çalışma zamanını kullanır. Yerel geliştirme makinenizde çalıştırdığınız Service Fabric ortamı, üretimde çalışan aynı ortamdır.

## <a name="next-steps"></a>Sonraki adımlar
Service Fabric Reliable Services hakkında daha fazla bilgi edinin ve Cloud Services ile Service Fabric uygulama Service Fabric mimarisi arasındaki temel farklılıklar hakkında daha fazla bilgi edinin.

* [Service Fabric Reliable Services kullanmaya başlama](service-fabric-reliable-services-quick-start.md)
* [Cloud Services ve Service Fabric arasındaki farklara kavramsal kılavuz](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
