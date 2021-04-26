---
title: Node.js hizmetlerini Azure Application Insights ile izleme | Microsoft Docs
description: Application Insights ile Node.js hizmetlerindeki performansı izleyin ve sorunları tanılayın.
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9eba74bcf2b4b047b3ed881342db4f7b1011f928
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585759"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Application Insights ile Node.js hizmetlerinizi ve uygulamalarınızı izleme

[Application Insights](./app-insights-overview.md) , performansı ve diğer sorunları keşfetmenize ve hızlı bir şekilde tanılamanıza yardımcı olması için arka uç hizmetlerinizi ve bileşenlerinizi dağıtımdan sonra izler. Veri merkezinizde, Azure VM 'lerinde ve Web uygulamalarınızda ve hatta diğer genel bulutlarda barındırılan Node.js Hizmetleri için Application Insights kullanabilirsiniz.

İzleme verilerinizi almak, depolamak ve araştırmak için SDK'yı kodunuza ekleyin ve Azure'da karşılık gelen Application Insights kaynağını ayarlayın. SDK daha fazla analiz ve araştırma için verileri bu kaynağa gönderir.

Node.js SDK'sı gelen ve giden HTTP isteklerini, özel durumları ve bazı sistem ölçümlerini otomatik olarak izleyebilir. Sürüm 0,20 ' den başlayarak, SDK, MongoDB, MySQL ve redde gibi bazı yaygın [üçüncü taraf paketleri](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules)de izleyebilir. Gelen bir HTTP isteği ile ilgili tüm olaylar, daha hızlı sorun giderme için birbiriyle ilişkilendirilir.

TelemetryClient API'sini kullanarak uygulamanızın ve sisteminizin ek özelliklerini el ile işaretleyebilir ve izleyebilirsiniz. TelemetryClient API'si bu makalenin ilerleyen bölümlerinde ayrıntılı bir şekilde anlatılmıştır.

## <a name="get-started"></a>başlarken

Bir uygulama veya hizmet için izlemeyi ayarlamak üzere aşağıdaki görevleri tamamlayın.

### <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, bir Azure aboneliğine sahip olduğunuzdan emin olun veya [ücretsiz olarak yeni bir tane edinin][azure-free-offer]. Kuruluşunuzun bir Azure aboneliğini zaten varsa, yöneticiniz [bu yönergeleri][add-aad-user] izleyerek sizi aboneliğe ekleyebilir.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a> Application Insights kaynağı ayarlama

1. [Azure portalında][portal] oturum açın.
2. [Application Insights kaynağı oluşturma](create-new-resource.md)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a> Node.js SDK'sını ayarlama

Veri toplayabilmesi için SDK'yı uygulamanıza ekleyin.

> [!IMPORTANT]
> Yeni Azure bölgeleri, izleme anahtarları yerine bağlantı dizelerinin kullanılmasını **gerektirir** . [Bağlantı dizesi](./sdk-connection-string.md?tabs=nodejs) , telemetri verilerinizi ilişkilendirmek istediğiniz kaynağı tanımlar. Ayrıca, kaynağınızın telemetri için hedef olarak kullanacağı uç noktaları değiştirmenize de olanak tanır. Bağlantı dizesini kopyalamanız ve uygulamanızın koduna veya bir ortam değişkenine eklemeniz gerekir.

1. Yeni oluşturulan kaynağından kaynağınızın izleme anahtarını ( *Ikey* olarak da bilinir) kopyalayın. Application Insights, verileri Azure kaynağınızla eşlemek için ikey değerini kullanır. SDK'nın ikey değerini kullanabilmesi için ikey değerini bir ortam değişkeninde veya kodunuzda belirtmeniz gerekir.  

   ![İzleme anahtarını kopyalama](./media/nodejs/instrumentation-key-001.png)

2. Node.js SDK kitaplığını package.json aracılığıyla uygulamanızın bağımlılıklarına ekleyin. Uygulamanızın kök klasöründen şunu çalıştırın:

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > TypeScript kullanıyorsanız, ayrı "typler" paketleri yüklemeyin. Bu NPM paketi yerleşik yazma işlemleri içerir.

3. Kitaplığı kodunuza açıkça yükleyin. SDK diğer birçok kitaplığa izleme eklediği için kitaplığı diğer `require` deyimlerinden de önce olmak üzere mümkün olduğunca erken yükleyin.

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  Ayrıca `APPINSIGHTS_INSTRUMENTATIONKEY` , veya ' ye el ile geçirmek yerine, ortam değişkeni aracılığıyla bir Ikey sağlayabilirsiniz  `setup()` `new appInsights.TelemetryClient()` . Bu uygulama, ikey değerlerini yürütülen kaynak kodunun dışında tutmanıza ve farklı ortamlar için farklı ikey değerleri belirtmenize olanak tanır. El ile aramayı yapılandırmak için `appInsights.setup('[your ikey]');` .

    Ek yapılandırma seçenekleri için aşağıdaki bölümlere bakın.

    SDK'yı telemetri verileri göndermeden denemek için `appInsights.defaultClient.config.disableAppInsights = true` ayarını yapın.

5. Çağırarak verileri otomatik olarak toplamayı ve göndermeyi başlatın `appInsights.start();` .

### <a name="monitor-your-app"></a><a name="monitor"></a> Uygulamanızı izleme

SDK, Node.js çalışma zamanı ve bazı yaygın üçüncü taraf modülleriyle ilgili Telemetriyi otomatik olarak toplar. Uygulamanızı kullanarak bu verilerin bazılarını oluşturun.

Ardından [Azure portalında][portal] önceden oluşturduğunuz Application Insights kaynağına gidin. **Genel bakış zaman çizelgesinde** ilk birkaç veri noktasını bulun. Ayrıntılı verileri görmek için grafikteki farklı bileşenleri seçin.

Uygulamanız için bulunan topolojiyi görüntülemek için [uygulama haritasını](app-map.md)kullanabilirsiniz.

#### <a name="no-data"></a>Veri yok

SDK gönderilecek verileri toplu hale getirdiği için öğelerin portalde gösterilmesi biraz gecikebilir. Verileri kaynağınızda görmüyorsanız aşağıdaki düzeltmelerden bazılarını deneyin:

* Uygulamayı kullanmaya devam edin. Daha fazla telemetri oluşturmak için daha fazla eylem gerçekleştirin.
* Portal kaynak görünümünde **Yenile**’ye tıklayın. Grafikler belirli aralıklarla otomatik olarak yenilenir ancak el ile yenilerseniz anında yenilenir.
* [Gerekli giden bağlantı noktalarının](./ip-addresses.md) açık olduğunu doğrulayın.
* Belirli olayları aramak için [Arama](./diagnostic-search.md) sekmesini kullanın.
* [SSS][FAQ]'yi denetleyin.

## <a name="basic-usage"></a>Temel kullanım

HTTP isteklerinin kullanıma hazır toplaması, popüler üçüncü taraf kitaplık olayları, işlenmemiş özel durumlar ve sistem ölçümleri için:

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> Ortam değişkeninde izleme anahtarı ayarlandıysa `APPINSIGHTS_INSTRUMENTATIONKEY` , `.setup()` bağımsız değişken olmadan çağrılabilir. Bu, farklı ortamlar için farklı Ikey 'leri kullanmayı kolaylaştırır.

`require("applicationinsights")`Diğer paketleri yüklemeden önce betiklerinizde olabildiğince erken Application Insights kitaplığı yükleyin. Bu, Application Insights kitaplığının sonraki paketleri izlemeye hazırlayabilmesi için gereklidir. Benzer hazırlık yaparak diğer kitaplıklarla çakışmalar yaşarsanız Application Insights kitaplığı yüklemeyi deneyin.

JavaScript 'in geri çağırmaları işleme yöntemi nedeniyle, dış bağımlılıklar ve sonraki geri çağırmalar genelinde bir isteği izlemek için ek çalışma gerekir. Varsayılan olarak, bu ek izleme etkindir; `setAutoDependencyCorrelation(false)` aşağıdaki [yapılandırma](#sdk-configuration) bölümünde açıklandığı gibi çağırarak devre dışı bırakın.

## <a name="migrating-from-versions-prior-to-022"></a>0,22 ' den önceki sürümlerden geçiş

Sürüm 0,22 ve sonrası sürümler arasında son değişiklikler var. Bu değişiklikler diğer Application Insights SDK 'lar ile tutarlılığı getirmek ve gelecekteki genişletilebilirliğe izin vermek için tasarlanmıştır.

Genel olarak, aşağıdaki ile geçiş yapabilirsiniz:

- Başvuruları ile değiştirin `appInsights.client` `appInsights.defaultClient` .
- Başvuruları ile değiştirin `appInsights.getClient()``new appInsights.TelemetryClient()`
- Tüm bağımsız değişkenleri, adlandırılmış özellikleri bağımsız değişken olarak içeren tek bir nesneyle birlikte Client. Track * yöntemlerine değiştirin. Her telemetri türü için, hariç tutulan nesne için IDE 'nin yerleşik tür ipuçlarınızı veya [TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes) bakın.

SDK yapılandırma işlevlerine, üzerinde zincirleme olmadan eriştiğinizde `appInsights.setup()` , artık bu işlevleri bulabilirsiniz `appInsights.Configurations` (örneğin, `appInsights.Configuration.setAutoCollectDependencies(true)` ). Sonraki bölümde varsayılan yapılandırmadaki değişiklikleri gözden geçirin.

## <a name="sdk-configuration"></a>SDK yapılandırması

`appInsights`Nesnesi bir dizi yapılandırma yöntemi sağlar. Bunlar varsayılan değerleriyle aşağıdaki kod parçacığında listelenir.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

Bir hizmetteki olayları tam olarak ilişkilendirmek için `.setAutoDependencyCorrelation(true)` ayarını yaptığınızdan emin olun. Bu seçenek belirlenmiş durumda olduğunda SDK Node.js içindeki zaman uyumsuz geri çağırmalar arasında içeriği izleyebilir.

Bu denetimin ne olduğuna ve isteğe bağlı ikincil bağımsız değişkenlerle ilgili ayrıntılı bilgi için, IDE 'nin yerleşik tür ipuçlarındaki açıklamalarını veya [ApplicationInsights. TS](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) ' yi inceleyin.

> [!NOTE]
>  Varsayılan olarak `setAutoCollectConsole` , (ve diğer konsol yöntemlerine) çağrıları *dışarıda bırakacak* şekilde yapılandırılmıştır `console.log` . Yalnızca desteklenen üçüncü taraf Günlükçüler için çağrılar (örneğin, Winston ve Bunyan) toplanacak. Kullanarak yöntemlere çağrı dahil etmek için bu davranışı değiştirebilirsiniz `console` `setAutoCollectConsole(true, true)` .

### <a name="sampling"></a>Örnekleme

Varsayılan olarak SDK, toplanan tüm verileri Application Insights hizmetine gönderir. Çok fazla veri topluyorsanız, gönderilen veri miktarını azaltmak için örneklemesi etkinleştirmek isteyebilirsiniz. `samplingPercentage` `config` Bunu gerçekleştirmek için bir istemcinin nesnesindeki alanı ayarlayın. `samplingPercentage`100 ' e (varsayılan) ayarlandığında, tüm veriler gönderilir ve 0, hiçbir şeyin gönderilmeyeceği anlamına gelir.

Otomatik bağıntı kullanıyorsanız, tek bir istekle ilişkili tüm veriler birim olarak dahil edilir veya hariç tutulur.

Örneklemeyi etkinleştirmek için aşağıdaki gibi bir kod ekleyin:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>Çoklu bileşen uygulamaları için birden çok rol

Uygulamanız, tümünü aynı izleme anahtarıyla işaretlemek istediğiniz birden çok bileşenden oluşuyorsa ve yine de bu bileşenleri portalda ayrı birimler olarak görmeye devam ediyorsa, ayrı izleme anahtarları (örneğin, uygulama haritasında ayrı düğümler olarak) kullanılıyorsa, rol adı alanını, bir bileşenin telemetrisini Application Insights kaynağına veri gönderen diğer bileşenlerden ayırt etmek için el ile yapılandırmanız gerekebilir.

RoleName alanını ayarlamak için aşağıdakileri kullanın:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>Otomatik üçüncü taraf izleme

Zaman uyumsuz çağrılar genelinde bağlamı izlemek için, MongoDB ve Redsıs gibi üçüncü taraf kitaplıklarında bazı değişiklikler yapmanız gerekir. Application Insights, varsayılan olarak, [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) Bu kitaplıkların bazılarını yaması için kullanır. Bu, ortam değişkeni ayarlanarak devre dışı bırakılabilir `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` .

> [!NOTE]
> Bu ortam değişkenini ayarlayarak, olaylar artık doğru işlemle düzgün şekilde ilişkilendirilemeyebilir.

 Bireysel maymun-yama `APPLICATION_INSIGHTS_NO_PATCH_MODULES` `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis` , ve paketlerine yama yapmaktan kaçınmak için ortam değişkeni devre dışı bırakılacak paketlerin virgülle ayrılmış bir listesine ayarlanarak devre dışı bırakılabilir `console` `redis` .

Şu anda,,,,,, `bunyan` ,, `console` `mongodb` `mongodb-core` `mysql` `redis` `winston` `pg` ve `pg-pool` tarafından işaretlenmiş dokuz paket bulunur: Bu paketlerin tam olarak hangi sürümü için düzeltme eki eklenen hakkında bilgi için bkz. [Tanılama-kanal-Yayımcılar ' BENIOKU dosyası](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md) .

`bunyan`, `winston` , Ve `console` yamaları, etkin olup olmadığına bağlı olarak Application Insights izleme olayları oluşturacaktır `setAutoCollectConsole` . Rest, etkin olup olmadığına bağlı olarak Application Insights bağımlılık olayları oluşturacak `setAutoCollectDependencies` .

### <a name="live-metrics"></a>Canlı Ölçümler

Uygulamanızı Azure 'a canlı ölçümler göndermeyi etkinleştirmek için kullanın `setSendLiveMetrics(true)` . Portalda canlı ölçümlerin filtrelenmesi Şu anda desteklenmiyor.

### <a name="extended-metrics"></a>Genişletilmiş ölçümler

> [!NOTE]
> Genişletilmiş yerel ölçümleri gönderme özelliği 1.4.0 sürümüne eklendi

Uygulamanızı Azure 'a genişletilmiş yerel ölçümler göndermeyi etkinleştirmek için, ayrı yerel ölçüm paketini de yüklersiniz. SDK, yüklendiğinde otomatik olarak yüklenir ve Node.js yerel ölçümleri toplamaya başlar.

```bash
npm install applicationinsights-native-metrics
```

Şu anda yerel ölçüm paketi, çöp toplama CPU zamanı, olay döngüsü işaretleri ve yığın kullanımının oto koleksiyonunu gerçekleştirir:

- **Çöp toplama**: her bir çöp toplama türünde harcanan CPU süresi ve her türün kaç kez yinelendiği.
- **Olay döngüsü**: kaç tane işaret meydana geldi ve toplamda ne kadar CPU süresi harcanmıştı.
- **Yığın ve yığın olmayan**: uygulamanızın bellek kullanımının ne kadarının yığında veya yığın halinde olması.

### <a name="distributed-tracing-modes"></a>Dağıtılmış Izleme modları

Varsayılan olarak SDK, bir Application Insights SDK 'Sı ile işaretlenmiş diğer uygulamalar/hizmetler tarafından anlaşılan üst bilgileri gönderir. İsteğe bağlı olarak, mevcut AI üst bilgilerine ek olarak [W3C Izleme bağlamı](https://github.com/w3c/trace-context) üst bilgilerinin gönderilmesini/alınmasını etkinleştirebilirsiniz. bu nedenle, mevcut eski hizmetlerinizin hiçbiriyle bağıntısı kesintiye uğramaz. W3C üst bilgilerini etkinleştirmek, uygulamanızın Application Insights ile işaretlenmeyen diğer hizmetlerle ilişkilendirilmesi için izin verir, ancak bu W3C standardını benimseyin.

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>TelemetryClient API'si

TelemetryClient API'sinin tam açıklaması için bkz. [Özel olaylar ve ölçümler için Application Insights API'si](./api-custom-events-metrics.md).

Application Insights Node.js SDK'sını kullanarak herhangi bir istek, olay, ölçüm veya özel durumu izleyebilirsiniz. Aşağıdaki kod örneği, kullanabileceğiniz API'lerden bazılarını göstermektedir:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
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
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

`trackMetric`Olay döngüsü zamanlaması süresini ölçmek için kullanılan örnek bir yardımcı program:  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>Tüm olaylara özel bir özellik ekleme

Tüm olaylara özel bir özellik eklemek için aşağıdaki kodu kullanın:

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>HTTP GET isteklerini izleme

HTTP GET isteklerini el ile izlemek için aşağıdaki kodu kullanın:

> [!NOTE]
> Tüm istekler varsayılan olarak izlenir. Otomatik toplamayı devre dışı bırakmak için Start () çağrısından önce. setAutoCollectRequests (false) çağırın.

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

Alternatif olarak, yöntemi kullanarak istekleri izleyebilirsiniz `trackNodeHttpRequest` :

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
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

### <a name="flush"></a>Temizlenemiyor

Varsayılan olarak, telemetri, giriş sunucusuna gönderilmeden önce 15 saniye boyunca arabelleğe kaydedilir. Uygulamanızın kısa bir süre (örn. bir CLı aracı) varsa, uygulama sonlandırıldığında, arabelleğe alınmış Telemetriyi el ile temizlemek gerekli olabilir `appInsights.defaultClient.flush()` .

SDK uygulamanızın kilitlenme olduğunu algılarsa, sizin için Temizleme işlemi çağırır `appInsights.defaultClient.flush({ isAppCrashing: true })` . Temizleme seçeneği ile `isAppCrashing` uygulamanızın anormal bir durumda olduğu varsayılır, telemetri göndermek için uygun değildir. Bunun yerine, SDK tüm arabelleğe alınmış telemetrileri [kalıcı depolamaya](./data-retention-privacy.md#nodejs) kaydeder ve uygulamanızın sonlandırılmasına izin verir. Uygulama yeniden başlatıldığında, kalıcı depolamaya kaydedilen herhangi bir Telemetriyi göndermeye çalışır.

### <a name="preprocess-data-with-telemetry-processors"></a>Telemetri işlemcileriyle verileri önceden işleme

Toplanan verileri, *telemetri işlemcileri* kullanılarak bekletme için gönderilmeden önce işleyebilir ve filtreleyebilirsiniz. Telemetri işlemcisi, telemetri öğesi buluta gönderilmeden önce eklendikleri sırada birer birer çağırılır.

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

Bir telemetri işlemcisi yanlış döndürürse, bu telemetri öğesi gönderilmez.

Tüm telemetri işlemcileri, İnceleme ve değiştirme için telemetri verilerini ve zarfını alır. Ayrıca bir bağlam nesnesi alırlar. Bu nesnenin içeriği, `contextObjects` el ile izlenen telemetri için bir izleme yöntemi çağrılırken parametresi tarafından tanımlanır. Otomatik olarak toplanan telemetri için, bu nesne kullanılabilir istek bilgileri ve tarafından sağlanan kalıcı istek içeriğiyle doldurulur `appInsights.getCorrelationContext()` (otomatik bağımlılık bağıntısı etkinse).

Telemetri işlemcisi için TypeScript türü:

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

Örneğin, özel durumların yığın izleme verilerini kaldıran bir işlemci, aşağıdaki gibi yazılabilir ve eklenebilir:

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>Birden çok izleme anahtarı kullanma

Birden çok Application Insights kaynağı oluşturabilir ve bunların her birine, ilgili izleme anahtarlarını ("Ikey") kullanarak farklı veriler gönderebilirsiniz.

 Örnek:

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>Gelişmiş yapılandırma seçenekleri

İstemci nesnesi, `config` Gelişmiş senaryolar için birçok isteğe bağlı ayarı içeren bir özellik içerir. Bunlar, aşağıdaki gibi ayarlanabilir:

```javascript
client.config.PROPERTYNAME = VALUE;
```

Bu özellikler istemciye özeldir, bu sayede `appInsights.defaultClient` ile oluşturulan istemcilerden ayrı olarak yapılandırabilirsiniz `new appInsights.TelemetryClient()` .

| Özellik                        | Açıklama                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| ınstrumentationkey              | Application Insights kaynağınız için bir tanımlayıcı.                                                      |
| endpointUrl                     | Telemetri yüklerini göndermek için alım uç noktası.                                                      |
| quickPulseHost                  | Canlı ölçüm telemetrisini göndermek için Canlı Ölçüm Akışı ana bilgisayarı.                                            |
| proxyHttpUrl 'Si                    | SDK HTTP trafiği için bir proxy sunucusu (Isteğe bağlı, varsayılan, `http_proxy` ortam değişkeninden alınır).     |
| proxyHttpsUrl 'Si                   | SDK HTTPS trafiği için bir proxy sunucusu (Isteğe bağlı, varsayılan, `https_proxy` ortam değişkeninden alınır).   |
| httpAgent                       | Bir http. SDK HTTP trafiği için kullanılacak aracı (Isteğe bağlı, varsayılan tanımsız).                                   |
| httpsAgent                      | Bir https. SDK HTTPS trafiği için kullanılacak aracı (Isteğe bağlı, varsayılan tanımsız).                                 |
| maxBatchSize                    | Alma uç noktasına (varsayılan) bir yüke dahil edilecek telemetri öğesi sayısı üst sınırı `250` .   |
| Maxbatchınterms              | Bir yükün maxBatchSize ulaşması için beklenecek en uzun süre (varsayılan `15000` ).               |
| Disableappınsights              | Telemetri iletiminin devre dışı olup olmadığını belirten bayrak (varsayılan `false` ).                                 |
| samplingPercentage              | İletilmesi gereken izlenen telemetri öğelerinin yüzdesi (varsayılan `100` ).                      |
| Correlationıdretryınterıms    | Çapraz bileşen bağıntısı için KIMLIĞI almaya yeniden denemeden önce beklenecek süre (varsayılan `30000` ).     |
| correlationHeaderExcludedDomains| Bileşenler arası bağıntı üst bilgisi ekleme dışında tutulacak etki alanlarının listesi (varsayılan bkz. [config. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)).|

## <a name="next-steps"></a>Sonraki adımlar

* [Portalda telemetrinizi izleyin](./overview-dashboard.md)
* [Telemetriniz üzerinden Analiz sorguları yazma](../logs/log-analytics-tutorial.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md
