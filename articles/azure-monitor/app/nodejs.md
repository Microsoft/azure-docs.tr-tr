---
title: Node.js hizmetlerini Azure Application Insights ile izleme | Microsoft Docs
description: Application Insights ile Node.js hizmetlerindeki performansı izleyin ve sorunları tanılayın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.openlocfilehash: 81f63380c041ae743a2b38e6ba89558b83e7497a
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820714"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Application Insights ile Node.js hizmetlerinizi ve uygulamalarınızı izleme

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) , performansı ve diğer sorunları keşfetmenize ve hızlı bir şekilde tanılamanıza yardımcı olması için arka uç hizmetlerinizi ve bileşenlerinizi dağıtımdan sonra izler. Application Insights'ı veri merkezinizde, Azure VM'lerinde, web uygulamalarında ve hatta diğer genel bulutlarda barındırılan Node.js hizmetleri için kullanabilirsiniz.

İzleme verilerinizi almak, depolamak ve araştırmak için SDK'yı kodunuza ekleyin ve Azure'da karşılık gelen Application Insights kaynağını ayarlayın. SDK daha fazla analiz ve araştırma için verileri bu kaynağa gönderir.

Node.js SDK'sı gelen ve giden HTTP isteklerini, özel durumları ve bazı sistem ölçümlerini otomatik olarak izleyebilir. SDK, sürüm 0.20'dan itibaren MongoDB, MySQL ve Redis gibi bazı sık kullanılan üçüncü taraf paketlerini izlemek için de kullanılabilir. Gelen bir HTTP isteği ile ilgili tüm olaylar, daha hızlı sorun giderme için birbiriyle ilişkilendirilir.

TelemetryClient API'sini kullanarak uygulamanızın ve sisteminizin ek özelliklerini el ile işaretleyebilir ve izleyebilirsiniz. TelemetryClient API'si bu makalenin ilerleyen bölümlerinde ayrıntılı bir şekilde anlatılmıştır.

## <a name="get-started"></a>Kullanmaya Başlayın

Bir uygulama veya hizmet için izlemeyi ayarlamak üzere aşağıdaki görevleri tamamlayın.

### <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, bir Azure aboneliğiniz olduğundan emin olun veya ücretsiz olarak yeni bir [tane alın][azure-free-offer]. Kuruluşunuzun zaten bir Azure aboneliği varsa, bir yönetici sizi size eklemek için [Bu talimatları][add-aad-user] izleyebilir.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: https://docs.microsoft.com/azure/active-directory/active-directory-users-create-azure-portal


### <a name="resource"></a> Bir Application Insights kaynağını ayarlama


1. [Azure Portal][portal]’ında oturum açın.
2. **Kaynak oluştur** > **Geliştirici araçları** > **Application Insights** seçeneğini belirleyin. Kaynak; telemetri verilerini, bu veriler için depolamayı, kayıtlı rapor ve panoları, kural ve uyarı yapılandırmasını almak ve diğer işlemlere yönelik bir uç nokta içerir.

3. Kaynak oluşturma sayfasındaki **Uygulama Türü** kutusundan **Node.js Uygulaması**'nı seçin. Uygulama türü oluşturulan varsayılan pano ve raporları belirler. (Herhangi bir Application Insights kaynağı herhangi bir dil ve platformdan veri toplayabilir.)

### <a name="sdk"></a> Node.js SDK'sını ayarlama

Veri toplayabilmesi için SDK'yı uygulamanıza ekleyin. 

1. Kaynağınızın İzleme Anahtarını (*ikey* olarak da adlandırılır) Azure portalından kopyalayın. Application Insights, verileri Azure kaynağınızla eşlemek için ikey değerini kullanır. SDK'nın ikey değerini kullanabilmesi için ikey değerini bir ortam değişkeninde veya kodunuzda belirtmeniz gerekir.  

   ![İzleme anahtarını kopyalama](./media/nodejs/instrumentation-key-001.png)

2. Node.js SDK kitaplığını package.json aracılığıyla uygulamanızın bağımlılıklarına ekleyin. Uygulamanızın kök klasöründen şunu çalıştırın:

   ```bash
   npm install applicationinsights --save
   ```

3. Kitaplığı kodunuza açıkça yükleyin. SDK diğer birçok kitaplığa izleme eklediği için kitaplığı diğer `require` deyimlerinden de önce olmak üzere mümkün olduğunca erken yükleyin. 

   İlk .js dosyanızın üstüne aşağıdaki kodu ekleyin. `setup` yöntemi, ikey değerini (ve bu nedenle Azure kaynağını) izlenen tüm öğeler için varsayılan olarak kullanılacak şekilde yapılandırır.

   ```javascript
   const appInsights = require("applicationinsights");
   appInsights.setup("<instrumentation_key>");
   appInsights.start();
   ```
   
   Ayrıca `setup()` veya `new appInsights.TelemetryClient()` konumuna el ile geçirmek yerine APPINSIGHTS\_INSTRUMENTATIONKEY ortam değişkeni aracılığıyla bir ikey sağlayabilirsiniz. Bu uygulama, ikey değerlerini yürütülen kaynak kodunun dışında tutmanıza ve farklı ortamlar için farklı ikey değerleri belirtmenize olanak tanır.

   Ek yapılandırma seçenekleri için aşağıdaki bölümlere bakın.

   SDK'yı telemetri verileri göndermeden denemek için `appInsights.defaultClient.config.disableAppInsights = true` ayarını yapın.

### <a name="monitor"></a> Uygulamanızı izleme

SDK, Node.js çalışma zamanı ve bazı yaygın üçüncü taraf modülleriyle ilgili telemetriyi otomatik olarak toplar. Uygulamanızı kullanarak bu verilerin bazılarını oluşturun.

Ardından [Azure Portal][portal] , daha önce oluşturduğunuz Application Insights kaynağına gidin. **Genel bakış zaman çizelgesinde** ilk birkaç veri noktasını bulun. Ayrıntılı verileri görmek için grafikteki farklı bileşenleri seçin.

Uygulamanız için bulunan topolojiyi görüntülemek için **Uygulama eşlemesi** düğmesini seçin. Ayrıntılı bilgi için eşlemedeki bileşenleri seçin.

![Basit uygulama eşlemesi](./media/nodejs/application-map-002.png)

Uygulamanız hakkında daha fazla bilgi almak ve sorunları gidermek için **ARAŞTIR** bölümündeki diğer görünümleri seçin.

![Araştır bölümü](./media/nodejs/007-investigate-pane.png)

#### <a name="no-data"></a>Veri yok mu?

SDK gönderilecek verileri toplu hale getirdiği için öğelerin portalde gösterilmesi biraz gecikebilir. Verileri kaynağınızda görmüyorsanız aşağıdaki düzeltmelerden bazılarını deneyin:

* Uygulamayı kullanmaya devam edin. Daha fazla telemetri oluşturmak için daha fazla eylem gerçekleştirin.
* Portal kaynak görünümünde **Yenile**’ye tıklayın. Grafikler belirli aralıklarla otomatik olarak yenilenir ancak el ile yenilerseniz anında yenilenir.
* [Gerekli giden bağlantı noktalarının](../../azure-monitor/app/ip-addresses.md) açık olduğunu doğrulayın.
* Belirli olayları aramak için [Arama](../../azure-monitor/app/diagnostic-search.md) sekmesini kullanın.
* [SSS][FAQ] sayfasını inceleyin.


## <a name="sdk-configuration"></a>SDK yapılandırması

SDK'nın yapılandırma yöntemleri ve varsayılan değerleri aşağıdaki örnek kodda listelenmiştir.

Bir hizmetteki olayları tam olarak ilişkilendirmek için `.setAutoDependencyCorrelation(true)` ayarını yaptığınızdan emin olun. Bu seçenek belirlenmiş durumda olduğunda SDK Node.js içindeki zaman uyumsuz geri çağırmalar arasında içeriği izleyebilir.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

## <a name="telemetryclient-api"></a>TelemetryClient API'si

TelemetryClient API'sinin tam açıklaması için bkz. [Özel olaylar ve ölçümler için Application Insights API'si](../../azure-monitor/app/api-custom-events-metrics.md).

Application Insights Node.js SDK'sını kullanarak herhangi bir istek, olay, ölçüm veya özel durumu izleyebilirsiniz. Aşağıdaki kod örneği, kullanabileceğiniz API'lerden bazılarını göstermektedir:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey is in env var
let client = appInsights.defaultClient;

client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Bağımlılıklarınızı izleme

Bağımlılıklarınızı izlemek için aşağıdaki kodu kullanın:

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.defaultClient;

var success = false;
let startTime = Date.now();
// Execute dependency call here...
let duration = Date.now() - startTime;
success = true;

client.trackDependency({dependencyTypeName: "dependency name", name: "command name", duration: duration, success: success});
```

### <a name="add-a-custom-property-to-all-events"></a>Tüm olaylara özel bir özellik ekleme

Tüm olaylara özel bir özellik eklemek için aşağıdaki kodu kullanın:

```javascript
appInsights.defaultClient.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>HTTP GET isteklerini izleme

HTTP GET isteklerini izlemek için aşağıdaki kodu kullanın:

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.defaultClient.trackNodeHttpRequest({request: req, response: res});
    }
    // Other work here...
    res.end();
});
```

### <a name="track-server-startup-time"></a>Sunucu başlangıç saatini izleme

Sunucu başlangıç saatini izlemek için aşağıdaki kodu kullanın:

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

## <a name="next-steps"></a>Sonraki adımlar

* [Portalda telemetrinizi izleme](../../azure-monitor/app/overview-dashboard.md)
* [Telemetriniz üzerinden Analiz sorguları yazma](../../azure-monitor/log-query/get-started-portal.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../../azure-monitor/app/troubleshoot-faq.md

