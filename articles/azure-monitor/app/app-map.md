---
title: Azure Application Insights uygulama Haritası | Microsoft Docs
description: Uygulama haritası ile karmaşık uygulama topolojilerini izleme
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: devx-track-csharp
ms.reviewer: sdash
ms.openlocfilehash: db8c84334bfce52d34b9fadf73bb2b070fa93a70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100007117"
---
# <a name="application-map-triage-distributed-applications"></a>Uygulama Haritası: dağıtılmış uygulamaları önceliklendirme

Uygulama Haritası, dağıtılmış uygulamanızın tüm bileşenlerindeki performans sorunlarını veya başarısız etkin noktaları belirlemenize yardımcı olur. Eşlemedeki her düğüm bir uygulama bileşenini veya bağımlılıklarını temsil eder; ve sistem durumu KPI 'si ve uyarı durumu içerir. Herhangi bir bileşenden, Application Insights olayları gibi daha ayrıntılı Tanılamalar 'e tıklayabilirsiniz. Uygulamanız Azure Hizmetleri kullanıyorsa, SQL Veritabanı Danışmanı önerileri gibi Azure tanılama 'ya de tıklayabilirsiniz.

## <a name="what-is-a-component"></a>Bileşen nedir?

Bileşenler, dağıtılmış/mikro hizmetler uygulamanızın bağımsız olarak dağıtılabilir parçalarından oluşur. Geliştiriciler ve işlemler ekiplerinde kod düzeyinde görünürlük veya bu uygulama bileşenleri tarafından oluşturulan telemetri erişimi vardır. 

* Bileşenler, takımınızın/kuruluşunuzun erişimi olmayan (kod veya telemetri) SQL, EventHub vb. gibi "gözlemlenen" dış bağımlılıklardan farklıdır.
* Bileşenler herhangi bir sayıda sunucu/rol/kapsayıcı örneği üzerinde çalışır.
* Bileşenler Application Insights izleme anahtarlarına (abonelikler farklıysa bile) veya tek bir Application Insights izleme anahtarına rapor veren farklı rollere sahip olabilir. Önizleme eşleme deneyimi, nasıl ayarlandıklarından bağımsız olarak bileşenleri gösterir.

## <a name="composite-application-map"></a>Bileşik uygulama eşlemesi

Tüm uygulama topolojisini ilgili uygulama bileşenlerinin birden çok düzeyinde görebilirsiniz. Bileşenler farklı Application Insights kaynaklar veya tek bir kaynaktaki farklı rollerdir. Uygulama Haritası, Application Insights SDK yüklü sunucular arasında yapılan HTTP bağımlılığı çağrılarını izleyerek bileşenleri bulur. 

Bu deneyim, bileşenlerin aşamalı keşfi ile başlar. Uygulama haritasını ilk kez yüklediğinizde, bu bileşenle ilgili bileşenleri bulacak bir sorgu kümesi tetiklenir. Sol üst köşedeki bir düğme, uygulamanızdaki bileşen sayısıyla birlikte güncelleştirilecek. 

"Harita bileşenlerini Güncelleştir" seçeneğine tıkladığınızda, eşleme bu noktaya kadar bulunan tüm bileşenlerle yenilenir. Uygulamanızın karmaşıklığına bağlı olarak bu işlem birkaç dakika sürebilir.

Tüm bileşenlerin tek bir Application Insights kaynağı içinde rolleri varsa, bu bulma adımı gerekli değildir. Böyle bir uygulamanın ilk yükünün tüm bileşenleri olacaktır.

![Ekran görüntüsünde bir uygulama Haritası örneği gösterilmektedir.](media/app-map/app-map-001.png)

Bu deneyimle önemli amaçların biri, yüzlerce bileşen ile karmaşık topolojileri görselleştirebilmelidir.

İlgili öngörüleri görmek için herhangi bir bileşene tıklayın ve bu bileşene ilişkin performans ve başarısızlık önceliklendirme deneyimine gidin.

![Geçici Açılır Öğe](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Sorunları araştırın

Arızalar bölmesini başlatmak için **başarısızlığı araştır** ' ı seçin.

![Sorunları araştır düğmesinin ekran görüntüsü](media/app-map/investigate-failures.png)

![Başarısızlık deneyiminin ekran görüntüsü](media/app-map/failures.png)

### <a name="investigate-performance"></a>Performansı araştır

Performans sorunlarını gidermek için, **performansı araştır**' ı seçin.

![Araştır performans düğmesinin ekran görüntüsü](media/app-map/investigate-performance.png)

![Performans deneyiminin ekran görüntüsü](media/app-map/performance.png)

### <a name="go-to-details"></a>Ayrıntılara git

Uçtan uca işlem deneyimini araştırmak için **ayrıntılara git** ' i seçin. Bu,, çağrı yığını düzeyine görünüm sunabilir.

![Ayrıntıya Git düğmesinin ekran görüntüsü](media/app-map/go-to-details.png)

![Uçtan uca işlem ayrıntılarının ekran görüntüsü](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Günlükleri görüntüleme (Analiz)

Uygulama verilerinizi daha fazla sorgulamak ve araştırmak için **günlüklerde görüntüle (Analiz)** seçeneğine tıklayın.

![Analiz düğmesindeki görünümün ekran görüntüsü](media/app-map/view-logs.png)

![Analiz deneyiminin ekran görüntüsü. Son 12 saat içindeki bir isteğin ortalama yanıt süresini özetleyen çizgi grafik.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Uyarılar

Etkin uyarıları ve uyarıların tetiklenmesi için temel kuralları görüntülemek için, **Uyarılar**' ı seçin.

![Uyarılar düğmesinin ekran görüntüsü](media/app-map/alerts.png)

![Analiz deneyiminin ekran görüntüsü](media/app-map/alerts-view.png)

## <a name="set-or-override-cloud-role-name"></a>Bulut rolü adını ayarlama veya geçersiz kılma

Uygulama Haritası, eşlemedeki bileşenleri tanımlamak için **bulut rolü adı** özelliğini kullanır. Bulut rolü adını el ile ayarlamak veya geçersiz kılmak ve uygulama eşlemesinde görüntülenecek öğeleri değiştirmek için:

> [!NOTE]
> Application Insights SDK veya aracı, bulut rolü adı özelliğini otomatik olarak bir Azure App Service ortamındaki bileşenlere yayılan telemetrisine ekler.

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

**Aşağıdaki gibi özel Telemetryınitializer yazın.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET Apps: Active TelemetryConfiguration 'a Başlatıcı yükleme**

ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

ASP.NET Web Apps için alternatif bir yöntem, örneğin Global. aspx. cs içindeki başlatıcıda başlatıcıyı örnekleyemedi:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> `ApplicationInsights.config`Veya kullanarak Başlatıcı eklemek `TelemetryConfiguration.Active` ASP.NET Core uygulamaları için geçerli değildir. 

**ASP.NET Core uygulamalar: Başlatıcı yükleme TelemetryConfiguration**

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) uygulamalar için, aşağıda gösterildiği gibi, yeni bir ekleme `TelemetryInitializer` işlemi bağımlılık ekleme kapsayıcısına eklenerek yapılır. Bu, `ConfigureServices` sınıfınızın yönteminde yapılır `Startup.cs` .

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Java Aracısı**

[Java aracısı 3,0](./java-in-process-agent.md) için bulut rolü adı şu şekilde ayarlanır:

```json
{
  "role": {
    "name": "my cloud role name"
  }
}
```

Ayrıca, ortam değişkenini kullanarak bulut rolü adını da ayarlayabilirsiniz ```APPLICATIONINSIGHTS_ROLE_NAME``` .

**Java SDK**

Application Insights Java SDK 2.5.0 ile başlayarak SDK kullanıyorsanız, dosyanıza ekleyerek bulut rolü adını belirtebilirsiniz ( `<RoleName>` `ApplicationInsights.xml` Örneğin,

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Spring Boot uygulamasını Application Insights Spring Boot Starter ile kullanırsanız, tek yapmanız gereken tek değişiklik, uygulama. Özellikler dosyasında uygulama için özel adınızı ayarlamanıza yöneliktir.

`spring.application.name=<name-of-app>`

Spring Boot Starter, spring.application.name özelliği için girdiğiniz değere otomatik olarak bulut rolü adı atayacaktır.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Node.js için alternatif Yöntem

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

# <a name="python"></a>[Python](#tab/python)

Python için, [Opencensus Python telemetri işlemcileri](api-filtering-sampling.md#opencensus-python-telemetry-processors) kullanılabilir.

```python
def callback_function(envelope):
   envelope.tags['ai.cloud.role'] = 'new_role_name'
   
# AzureLogHandler
handler.add_telemetry_processor(callback_function)

# AzureExporter
exporter.add_telemetry_processor(callback_function)
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Uygulama Haritası bağlamı içinde bulut rolü adını anlama

**Bulut rolü adı** hakkında ne kadar düşünürken, birden fazla bulut rolü adına sahip bir uygulama eşlemesine bakmak yararlı olabilir:

![Uygulama Haritası ekran görüntüsü](media/app-map/cloud-rolename.png)

Yukarıdaki uygulama haritasında yeşil kutular 'daki adların her biri, bu dağıtılan uygulamanın farklı yönleri için bulut rolü adı değerlerdir. Bu nedenle, bu uygulama için rolleri şunlardan oluşur: `Authentication` , `acmefrontend` , `Inventory Management` , a `Payment Processing Worker Role` . 

Bu uygulama, bu bulut rolü adlarının her biri aynı zamanda kendi izleme anahtarlarına sahip farklı bir benzersiz Application Insights kaynağını da temsil eder. Bu uygulamanın sahibi bu dört farklı Application Insights kaynağın her birine erişime sahip olduğundan, uygulama haritası temel alınan ilişkilerin bir haritasını birlikte birleştirilebilir.

[Resmi tanımlar](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)için:

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Alternatif olarak **, bulut** rolü **adı** , sorunun Web ön uçınızdan bir yerde olduğunu söylediğinden, Web ön uçınızı birden fazla yük dengeli sunucuda çalıştırıyor olabilirsiniz. böylece, kusto sorguları aracılığıyla bir katmanda detaya gidebilmek ve sorunun tüm Web ön uç sunucularını/örneklerini etkileyip etkilemediğini bilmenin yanı sıra son derece önemli olabilir.

Bulut rol örneği için değeri geçersiz kılmak isteyebileceğiniz bir senaryo, uygulamanızın belirli bir sorunu bulmak için yeterli bilgi olmadığını bilmenin bir kapsayıcı ortamda çalışıyor olması olabilir.

Bulut rolü adı özelliğinin telemetri başlatıcılarla nasıl geçersiz kılındığı hakkında daha fazla bilgi için bkz. [Add Properties: Itelemetrybaşlatıcısı](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Sorun giderme

Uygulama haritasını beklenen şekilde çalışacak bir sorun yaşıyorsanız, aşağıdaki adımları deneyin:

### <a name="general"></a>Genel

1. Resmi olarak desteklenen bir SDK kullandığınızdan emin olun. Desteklenmeyen/Topluluk SDK’ları bağıntıyı desteklemeyebilir.

    Desteklenen SDK’ların listesi için [bu makaleye](./platforms.md) bakın.

2. Tüm bileşenleri en son SDK sürümüne yükseltin.

3. C# ile Azure Işlevleri kullanıyorsanız, [Işlevler v2](../../azure-functions/functions-versions.md)'ye yükseltin.

4. [Bulut rolü adının](#set-or-override-cloud-role-name) doğru şekilde yapılandırıldığını onaylayın.

5. Bir bağımlılık eksikse, bunun [otomatik olarak toplanan bağımlılıklar](./auto-collect-dependencies.md) listesinde bulunduğundan emin olun. Listede yoksa, bunu yine de [izleme bağımlılık çağrısı](./api-custom-events-metrics.md#trackdependency) kullanarak el ile izleyebilirsiniz.

### <a name="too-many-nodes-on-the-map"></a>Haritada çok fazla düğüm

Uygulama Haritası, istek telemetrinizde bulunan her benzersiz bulut rolü adı için bir uygulama düğümü ve bağımlılık telemetrinizde her bir benzersiz tür, hedef ve bulut rolü birleşimi için bir bağımlılık düğümü oluşturur. Telemetrinizde 10.000 'den fazla düğüm varsa, uygulama haritası tüm düğümleri ve bağlantıları alıp haritalarınız tamamlanamayacak. Bu durumda, eşleme görüntülenirken bir uyarı iletisi görüntülenir.

Ayrıca, uygulama eşlemesi yalnızca aynı anda oluşturulan en fazla 1000 ayrı Gruplandırılmamış düğümü destekler. Uygulama eşlemesi, bağımlılıkları aynı türe ve çağıranlara sahip olan bir araya getirerek görsel karmaşıklıkları azaltır, ancak telemetrinizde çok sayıda benzersiz bulut rolü adı veya çok fazla bağımlılık türü varsa, bu gruplandırma yetersiz olur ve eşleme işlenemez.

Bunu yapmak için, bulut rolü adı, bağımlılık türü ve bağımlılık hedefi alanlarını düzgün şekilde ayarlamak üzere araçlarınızı değiştirmeniz gerekir.

* Bağımlılık hedefi, bağımlılığın mantıksal adını temsil etmelidir. Çoğu durumda, bağımlılığın sunucu veya kaynak adı ile eşdeğerdir. Örneğin, HTTP bağımlılıkları durumunda ana bilgisayar adına ayarlanır. Bir istekten diğerine değişen benzersiz kimlikler veya parametreler içermemelidir.

* Bağımlılık türü, bağımlılığın mantıksal türünü temsil etmelidir. Örneğin, HTTP, SQL veya Azure blobu tipik bağımlılık türleridir. Benzersiz kimlikler içermemelidir.

* Bulut rolü adının amacı [yukarıdaki bölümde](#set-or-override-cloud-role-name)açıklanmıştır.

## <a name="portal-feedback"></a>Portal geri bildirimi

Geri bildirim sağlamak için geri bildirim seçeneğini kullanın.

![MapLink-1 resmi](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Sonraki adımlar

* Bağıntıdan nasıl çalıştığı hakkında daha fazla bilgi edinmek için Application Insights [telemetri bağıntı makalesine](correlation.md)başvurun.
* [Uçtan uca işlem tanılama deneyimi](transaction-diagnostics.md) , sunucu tarafı telemetrisini tüm Application Insights izlenen bileşenlerinizin tamamında tek bir görünümde ilişkilendirir.
* ASP.NET Core ve ASP.NET ' deki gelişmiş bağıntı senaryoları için [özel işlemleri izleme](custom-operations-tracking.md) makalesine başvurun.
