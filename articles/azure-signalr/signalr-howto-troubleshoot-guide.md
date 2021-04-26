---
title: Azure SignalR Hizmeti için sorun giderme kılavuzu
description: Sık karşılaşılan sorunları nasıl giderebileceğinizi öğrenin
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: e26def56fbd03626c3efc660db57012ee1b767ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048213"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Azure SignalR hizmeti yaygın sorunları için sorun giderme kılavuzu

Bu kılavuz, müşterilerin geçen yıllarda karşıladığı ve çözdüğü yaygın sorunlar temelinde yararlı sorun giderme kılavuzu sağlamaktır.

## <a name="access-token-too-long"></a>Erişim belirteci çok uzun

### <a name="possible-errors"></a>Olası hatalar

* İstemci tarafı `ERR_CONNECTION_`
* 414 URI çok uzun
* 413 yükü çok büyük
* Erişim belirteci 4K 'den daha uzun olmamalıdır. 413 istek varlığı çok büyük

### <a name="root-cause"></a>Kök neden

HTTP/2 için, tek bir üst bilgi için maksimum uzunluk **4 KB**'dir, bu nedenle Azure hizmetine erişmek için tarayıcı kullanılıyorsa bu sınırlama için bir hata olur `ERR_CONNECTION_` .

HTTP/1.1 veya C# istemcileri için en fazla URI uzunluğu **12 k**, en fazla üst bilgi uzunluğu **16 k** olur.

SDK sürümü **1.0.6** veya üzeri ile, `/negotiate` `413 Payload Too Large` oluşturulan erişim belirtecinin **4 K**'den büyük olduğu durumlarda oluşturulacaktır.

### <a name="solution"></a>Çözüm

Varsayılan olarak, talepleri,, `context.User.Claims` talepler korunabilmesi veistemci öğesine bağlandığı zaman **ASRS** 'den öğesine geçirilebilmesi IÇIN, **IRS**'ye JWT erişim belirteci oluştururken (zure **s** ıgnal **R** **s** ervice) dahil edilir `Hub` `Hub` .

Bazı durumlarda, `context.User.Claims` çoğu uygulama sunucusu için, `Hub` ancak diğer bileşenler tarafından kullanılmayan çok fazla bilgi depolamak için kullanılır.

Oluşturulan erişim belirteci ağ üzerinden geçirilir ve WebSocket/SSE bağlantıları için erişim belirteçleri sorgu dizeleri aracılığıyla geçirilir. Bu nedenle, en iyi yöntem olarak, hub gerektiğinde yalnızca istemciden **ASRS** aracılığıyla uygulama sunucunuza **gerekli** talepleri geçirmeyi öneririz.

`ClaimsProvider`Erişim belirtecinin Içindeki **ASRS** 'ye geçen talepleri özelleştirmeniz için bir, vardır.

ASP.NET Core için:

```csharp
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

ASP.NET için:

```csharp
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tls-12-required"></a>TLS 1,2 gerekiyor

### <a name="possible-errors"></a>Olası hatalar

* ASP.NET "sunucu kullanılamıyor" hatası [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET "bağlantı etkin değil, hizmete veri gönderilemiyor." hata [#324](https://github.com/Azure/azure-signalr/issues/324)
* "Https://için HTTP isteği kurulurken bir hata oluştu <API endpoint> . Bu hata, sunucu sertifikasının HTTPS durumda HTTP.SYS ile düzgün şekilde yapılandırılmadığı durumdur. Bu hata, istemci ve sunucu arasındaki güvenlik bağlamasının eşleşmemesi nedeniyle de oluşabilir. "

### <a name="root-cause"></a>Kök neden

Azure hizmeti yalnızca güvenlik sorunları için TLS 1.2 'yi destekler. .NET Framework ile, TLS 1.2 varsayılan protokol değildir. Sonuç olarak, ASRS 'ye sunucu bağlantıları başarıyla oluşturulamaz.

### <a name="troubleshooting-guide"></a>Sorun giderme kılavuzu

1. Bu hata yerel olarak yeniden üretilenebilir, *yalnızca kendi kodum* işaretini kaldırın ve tüm CLR özel durumlarını oluşturun ve özel durumun ne olduğunu görmek için uygulama sunucusunda yerel olarak hata ayıklayın.
    * *Yalnızca kendi kodum* işaretini kaldırın

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="Yalnızca kendi kodum işaretini kaldırın":::

    * CLR özel durumları oluştur

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="CLR özel durumları oluştur":::

    * Uygulama sunucu tarafı kodunda hata ayıklarken throw özel durumlarını görün:
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="Özel durum oluşturur":::

2. ASP.NET ler için, `Startup.cs` ayrıntılı izlemeyi etkinleştirmek ve günlükteki hataları görmek için aşağıdaki kodu ' e de ekleyebilirsiniz.

    ```cs
    app.MapAzureSignalR(this.GetType().FullName);
    // Make sure this switch is called after MapAzureSignalR
    GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
    ```

### <a name="solution"></a>Çözüm

Aşağıdaki kodu, başlangıç uygulamanıza ekleyin:

```csharp
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="400-bad-request-returned-for-client-requests"></a>400 istemci istekleri için hatalı Istek döndürüldü

### <a name="root-cause"></a>Kök neden

İstemci isteğinizin birden çok sorgu dizesine sahip olup olmadığını denetleyin `hub` . `hub` korunan bir sorgu parametresi olur ve hizmet sorguda birden fazla algıladığında, 400 oluşturur `hub` .

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="401-unauthorized-returned-for-client-requests"></a>İstemci istekleri için 401 Yetkisiz hatası döndürüldü

### <a name="root-cause"></a>Kök neden

Şu anda JWT belirtecinin yaşam süresinin varsayılan değeri 1 saattir.

ASP.NET Core SignalR için, WebSocket aktarım türü kullanılırken bu, tamam olur.

ASP.NET Core SignalR 'nin diğer aktarım türü, SSE ve uzun yoklama için bu, varsayılan olarak 1 saat boyunca en çok kalıcı hale getirebileceği anlamına gelir.

ASP.NET SignalR için, istemci `/ping` hizmete zaman zaman bir KeepAlive isteği gönderir, `/ping` başarısız olduğunda istemci bağlantıyı **iptal eder** ve hiçbir zaman yeniden bağlanmaz. Bu, ASP.NET SignalR için varsayılan belirtecin yaşam süresinin, bağlantının tüm aktarım türü için **en fazla** 1 saat sürer hale geldiğini gösterir.

### <a name="solution"></a>Çözüm

Güvenlik sorunları için genişletme TTL 'si önerilir. Bu 401 gerçekleştiğinde bağlantıyı yeniden başlatmak için istemciden yeniden bağlama mantığı eklenmesini öneririz. İstemci bağlantıyı yeniden başlattığında, JWT belirtecini yeniden almak ve yenilenmiş bir belirteç almak için App Server ile anlaşacaktır.

İstemci bağlantılarını yeniden başlatma hakkında [buraya](#restart_connection) bakın.

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-client-requests"></a>İstemci istekleri için 404 döndürüldü

Bir SignalR kalıcı bağlantısı için, önce `/negotiate` Azure SignalR hizmeti ' ne ve ardından Azure SignalR hizmetine gerçek bağlantı kurar.

### <a name="troubleshooting-guide"></a>Sorun giderme kılavuzu

* İstemciden hizmete istek almak için [giden Isteklerin nasıl görüntüleneceği](#view_request) aşağıda verilmiştir.
* 404 oluştuğunda isteğin URL 'sini kontrol edin. URL, Web uygulamanıza hedefleniyorsa ve buna benzer şekilde `{your_web_app}/hubs/{hubName}` , istemcinin olup olmadığını kontrol edin `SkipNegotiation` `true` . Azure SignalR kullanırken istemci, uygulama sunucusuna ilk kez odaklandığınızda yeniden yönlendirme URL 'sini alır. İstemci, Azure SignalR kullanırken **anlaşmayı atmamalıdır** .
* Başka bir 404, bağlantı isteği çağrıldıktan sonra **5** saniyeden daha uzun süre işlendiği zaman oluşabilir `/negotiate` . İstemci isteğinin zaman damgasını denetleyin ve hizmete yönelik istekte yavaş bir yanıt varsa bize bir sorun açın.

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>ASP.NET SignalR 'nin reconnect isteği için 404 döndürüldü

ASP.NET SignalR için, [istemci bağlantısı düşerse](#client_connection_drop) `connectionId` bağlantıyı durdurmadan önce onu üç kez kullanarak yeniden bağlanır. `/reconnect` kalıcı bağlantıyı başarıyla yeniden kurmasına neden olan ağ aralıklı sorunları nedeniyle bağlantının bırakılmadığı konusunda yardımcı olabilir `/reconnect` . Diğer koşullarda, örneğin, yönlendirilmiş sunucu bağlantısı bırakılmadığı için istemci bağlantısı bırakılır veya SignalR hizmeti örnek yeniden başlatma/yük devretme/dağıtım gibi bazı iç hatalara sahip olduğundan bağlantı artık mevcut değildir, bu nedenle `/reconnect` geri döner `404` . Bu, üç kez için beklenen `/reconnect` ve daha sonra bağlantı duraklarının yeniden denenme davranışıdır. Bağlantı durdurulduğunda [bağlantı yeniden başlatma](#restart_connection) mantığını öneririz.

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="429-too-many-requests-returned-for-client-requests"></a>istemci istekleri için 429 (çok fazla Istek) döndürüldü

İki durum vardır.

### <a name="concurrent-connection-count-exceeds-limit"></a>**Eşzamanlı** bağlantı sayısı sınırı aşıyor

**Ücretsiz** örnekler Için, **eşzamanlı** bağlantı sayısı sınırı **Standart** örnekler için 20, **birim başına** **eşzamanlı** bağlantı sayısı sınırı 1 K olur, bu da Unit100 100-K eş zamanlı bağlantılara izin verir.

Bağlantılar hem istemci hem de sunucu bağlantılarını içerir. bağlantıların nasıl sayılacağını [kontrol edin](./signalr-concept-messages-and-connections.md#how-connections-are-counted) .

### <a name="too-many-negotiate-requests-at-the-same-time"></a>Aynı anda çok fazla anlaşma isteği

Yeniden bağlanmadan önce rastgele bir gecikme önermesi önerilir, yeniden deneme örnekleri için [buraya](#restart_connection) bakın.

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>500 anlaşması sırasında hata oluştu: Azure SignalR hizmeti henüz bağlı değil, lütfen daha sonra yeniden deneyin

### <a name="root-cause"></a>Kök neden

Bu hata, Azure SignalR hizmetine bağlı bir sunucu bağlantısı olmadığında raporlanır.

### <a name="troubleshooting-guide"></a>Sorun giderme kılavuzu

Sunucu Azure SignalR hizmetine bağlanmayı denediğinde hata ayrıntılarını bulmak için sunucu tarafı izlemeyi etkinleştirin.

### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>ASP.NET Core SignalR için sunucu tarafında günlüğe kaydetmeyi etkinleştirme

ASP.NET Core SignalR için sunucu tarafında günlüğe kaydetme `ILogger` , ASP.NET Core çerçevesinde sunulan temel [günlük](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1&preserve-view=true) kaydıyla tümleştirilir. Aşağıdaki gibi bir örnek kullanım kullanarak sunucu tarafı günlük kaydını etkinleştirebilirsiniz `ConfigureLogging` :

```csharp
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Azure SignalR için günlükçü kategorileri her zaman ile başlar `Microsoft.Azure.SignalR` . Azure SignalR 'den ayrıntılı günlükleri etkinleştirmek için, yukarıdaki ön ekleri `Debug` aşağıdaki gibi **appsettings.js** dosya düzeyinde yapılandırın:

```json
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>ASP.NET SignalR için sunucu tarafı izlemelerini etkinleştirme

SDK sürümü >= kullanırken `1.0.0` , aşağıdakileri öğesine ekleyerek izlemeleri etkinleştirebilirsiniz `web.config` : ([Ayrıntılar](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))

```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

<a name="client_connection_drop"></a>

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-drops"></a>İstemci bağlantısı düşme

İstemci Azure SignalR 'ye bağlıyken, istemci ve Azure SignalR arasındaki kalıcı bağlantı bazen farklı nedenlerle ortaya çıkabilir. Bu bölümde, böyle bir bağlantı bırakmaya neden olan çeşitli olanaklar açıklanmakta ve kök nedenin nasıl tanımlanacağına ilişkin yönergeler sunulmaktadır.

### <a name="possible-errors-seen-from-the-client-side"></a>İstemci tarafında görülen olası hatalar

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>Kök neden

İstemci bağlantıları çeşitli koşullarda bırakabilir:
* `Hub`, Gelen istek ile özel durumlar oluşturduğunda.
* İstemcinin yönlendirildiği sunucu bağlantısı, [sunucu bağlantısı düşmesinde](#server_connection_drop)Ayrıntılar için aşağıdaki bölüme bakın.
* İstemci ile SignalR hizmeti arasında bir ağ bağlantısı sorunu olduğunda.
* SignalR hizmeti örneği yeniden başlatma, yük devretme, dağıtım vb. gibi bazı iç hatalara sahip olduğunda.

### <a name="troubleshooting-guide"></a>Sorun giderme kılavuzu

1. Her şeyin olağan dışı olup olmadığını görmek için uygulama sunucusu tarafı günlüğünü açın
2. App Server 'ın yeniden başlatılıp başlatılmayacağını görmek için uygulama sunucusu tarafında olay günlüğünü denetleyin
3. Bize zaman çerçevesini sağlamaktan bir sorun oluşturun ve kaynak adını bize e-posta ile gönderin

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-increases-constantly"></a>İstemci bağlantısı sürekli artar

İstemci bağlantısının yanlış kullanımı nedeniyle oluşmuş olabilir. Birisi SignalR istemcisini durdurmayı/atmayı unutursa bağlantı açık kalır.

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>SignalR 'nin Azure portal kaynak menüsünün Izleme bölümündeki ölçümlerinden görülen olası hatalar

İstemci bağlantıları, Azure SignalR 'nin ölçümlerinde uzun bir süre boyunca sürekli olarak artar.

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="İstemci bağlantısı sürekli artıyor":::

### <a name="root-cause"></a>Kök neden

SignalR istemci bağlantısı `DisposeAsync` hiçbir şekilde çağrılmaz, bağlantı açık kalır.

### <a name="troubleshooting-guide"></a>Sorun giderme kılavuzu

SignalR istemcisinin **hiç** kapanmayacağını denetleyin.

### <a name="solution"></a>Çözüm

Bağlantıyı kapatmayı denetleyin. `HubConnection.DisposeAsync()`Bağlantıyı kullandıktan sonra durdurmak için el ile çağrı yapın.

Örnek:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>Yaygın olmayan istemci bağlantı kullanımı

#### <a name="azure-function-example"></a>Azure Işlevi örneği 

Bu sorun genellikle, birisi Işlev sınıfınıza statik üye yapmak yerine Azure Işlev yönteminde SignalR istemci bağlantısı kurduğunda oluşur. Yalnızca bir istemci bağlantısının kurulu olduğunu bekleyebilir, ancak Azure portal kaynak menüsündeki Izleme bölümündeki ölçümlerde istemci bağlantı sayısı sürekli olarak artar, tüm bu bağlantılar yalnızca Azure Işlevinden veya Azure SignalR hizmeti yeniden başlatıldıktan sonra açılır. Bunun nedeni, **her** Istek Için Azure işlevi **bir** Istemci bağlantısı oluşturduğunda, işlev yönteminde istemci bağlantısını durdurmazsanız, Istemci bağlantıları Azure SignalR hizmetine canlı tutar.

#### <a name="solution"></a>Çözüm

* Azure işlevinde SignalR istemcileri kullanıyorsanız veya SignalR istemcisini tek bir olarak kullanıyorsanız, istemci bağlantısını kapatmayı unutmayın.
* Azure işlevinde SignalR istemcileri kullanmak yerine, diğer her yerde SignalR istemcileri oluşturabilir ve istemciye Azure SignalR hizmeti ile [anlaşmak](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) Için Azure [SignalR hizmeti Için Azure işlevleri bağlamalarını](https://github.com/Azure/azure-functions-signalrservice-extension) kullanabilirsiniz. Ayrıca, [ileti göndermek](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40)için bağlamayı de kullanabilirsiniz. İstemciye anlaşma ve ileti gönderme örnekleri [burada](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples)bulunabilir. [Burada](https://github.com/Azure/azure-functions-signalrservice-extension)daha fazla bilgi bulabilirsiniz.
* Azure işlevinde SignalR istemcileri kullandığınızda senaryonuz için daha iyi bir mimari olabilir. Uygun bir sunucusuz mimari tasarlamanızı denetleyin. [Azure işlevleri 'Ndeki SignalR hizmeti bağlamalarıyla gerçek zamanlı sunucusuz uygulamalara](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService)başvurabilirsiniz.

<a name="server_connection_drop"></a>

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="server-connection-drops"></a>Sunucu bağlantısı düşme

Uygulama sunucusu başlatıldığında, arka planda Azure SDK, uzak Azure SignalR 'ye yönelik sunucu bağlantılarını başlatır. Azure SignalR [hizmeti 'Nin Iç yapıları](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)bölümünde açıklandığı gibi, Azure SignalR gelen istemci traffics bu sunucu bağlantılarına yönlendirir. Sunucu bağlantısı bırakıldıktan sonra, hizmet verdiği tüm istemci bağlantıları da kapatılır.

App Server ve SignalR hizmeti arasındaki bağlantılar kalıcı bağlantılardır, bunlar ağ bağlantısı sorunlarıyla karşılaşabilir. Sunucu SDK 'sında, sunucu bağlantılarına **her zaman yeniden bağlandık** . En iyi uygulama olarak, Ayrıca, sunucuya çok sayıda eşzamanlı istek oluşmasını önlemek için kullanıcıların rastgele bir gecikme süresi ile istemcilere sürekli yeniden bağlanma mantığı eklemesini öneririz.

Düzenli olarak, Azure SignalR hizmeti için yeni sürüm sürümleri ve bazen Azure genelinde işletim sistemi düzeltme eki uygulama ya da yükseltme ya da bağımlı hizmetlerimizden zaman zaman kesintiye uğramasız bir kesinti vardır. Bunlar kısa bir hizmet kesintisi yaşar, ancak istemci tarafında bağlantıyı kes/yeniden bağlan mekanizmasına sahip olduğu sürece, etki tüm istemci tarafı bağlantısı kesilmesinin başarısız olması gibi en az bir işlem olur.

Bu bölümde sunucu bağlantısı bırakmaya yönelik birkaç olasılık açıklanmakta ve kök nedenin nasıl tanımlanacağına ilişkin yönergeler sunulmaktadır.

### <a name="possible-errors-seen-from-the-server-side"></a>Sunucu tarafında görülen olası hatalar

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>Kök neden

Sunucu-hizmet bağlantısı **ASRS****(** zure **s** ıgnal **R** **s**) tarafından kapalıdır.

Ping zaman aşımı için, sunucu tarafında yüksek CPU kullanımı veya iş parçacığı havuzu yüzünden olabilir.

ASP.NET SignalR için, SDK 1.6.0 ' de bilinen bir sorun düzeltildi. SDK 'nizi en yeni sürüme yükseltin.

## <a name="thread-pool-starvation"></a>İş parçacığı havuzu başlangıçlan

Sunucunuz başlatılıyor, bu, ileti işleme üzerinde hiçbir iş parçacığı çalışmaması anlamına gelir. Tüm iş parçacıkları belirli bir yöntemde yanıt vermez.

Normalde, bu senaryoya eşitleme üzerinden veya `Task.Result` / `Task.Wait()` zaman uyumsuz metotlarda zaman uyumsuz olarak neden olur.

Bkz. [ASP.NET Core Performance en iyi uygulamaları](/aspnet/core/performance/performance-best-practices#avoid-blocking-calls).

[İş parçacığı havuzu](https://docs.microsoft.com/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall)hakkında daha fazla bilgi için bkz..

### <a name="how-to-detect-thread-pool-starvation"></a>İş parçacığı havuzu oluşturma sorunları nasıl algılanır

İş parçacığı sayınız olup olmadığını denetleyin. Bu sırada ani artışlar yoksa şu adımları uygulayın:
* Azure App Service kullanıyorsanız, ölçümlerde iş parçacığı sayısını denetleyin. Toplamayı denetleyin `Max` :
    
  :::image type="content" source="media/signalr-howto-troubleshoot-guide/metrics-thread-count.png" alt-text="Azure App Service en fazla iş parçacığı sayısı bölmesinin ekran görüntüsü.":::

* .NET Framework kullanıyorsanız, [ölçümleri](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/performance-counters#lock-and-thread-performance-counters) sunucu sanal makinenizde performans izleyicisinde bulabilirsiniz.
* Bir kapsayıcıda .NET Core kullanıyorsanız bkz. [kapsayıcılarda tanılamayı toplama](https://docs.microsoft.com/dotnet/core/diagnostics/diagnostics-in-containers).

Ayrıca, iş parçacığı havuzunu algılamak için kodu kullanabilirsiniz:

```csharp
public class ThreadPoolStarvationDetector : EventListener
{
    private const int EventIdForThreadPoolWorkerThreadAdjustmentAdjustment = 55;
    private const uint ReasonForStarvation = 6;

    private readonly ILogger<ThreadPoolStarvationDetector> _logger;

    public ThreadPoolStarvationDetector(ILogger<ThreadPoolStarvationDetector> logger)
    {
        _logger = logger;
    }

    protected override void OnEventSourceCreated(EventSource eventSource)
    {
        if (eventSource.Name == "Microsoft-Windows-DotNETRuntime")
        {
            EnableEvents(eventSource, EventLevel.Informational, EventKeywords.All);
        }
    }

    protected override void OnEventWritten(EventWrittenEventArgs eventData)
    {
        // See: https://docs.microsoft.com/en-us/dotnet/framework/performance/thread-pool-etw-events#threadpoolworkerthreadadjustmentadjustment
        if (eventData.EventId == EventIdForThreadPoolWorkerThreadAdjustmentAdjustment &&
            eventData.Payload[3] as uint? == ReasonForStarvation)
        {
            _logger.LogWarning("Thread pool starvation detected!");
        }
    }
}
```
    
Bu hizmeti hizmetinize ekleyin:
    
```csharp
service.AddSingleton<ThreadPoolStarvationDetector>();
```

Ardından, sunucu bağlantısının ping zaman aşımı ile bağlantısı kesildiğinde günlüğlerinizi kontrol edin.

### <a name="how-to-find-the-root-cause-of-thread-pool-starvation"></a>İş parçacığı havuzunun kök nedenini bulma

İş parçacığı havuzunun kök nedenini bulmak için:

* Belleği dökümünü alın ve ardından çağrı yığınını çözümleyin. Daha fazla bilgi için bkz. [bellek dökümlerini toplayın ve çözümleyin](https://devblogs.microsoft.com/dotnet/collecting-and-analyzing-memory-dumps/).
* İş parçacığı havuzu algılandığında belleğin dökümünü almak için [clrmd](https://github.com/microsoft/clrmd) kullanın. Ardından, çağrı yığınını günlüğe kaydedin.

### <a name="troubleshooting-guide"></a>Sorun giderme kılavuzu

1. Anormal bir şeyin olup olmadığını görmek için uygulama sunucusu tarafı günlüğünü açın.
2. Uygulama sunucusunun yeniden başlatılıp başlatılmayacağını görmek için uygulama sunucusu tarafında olay günlüğünü kontrol edin.
3. Sorun oluşturun. Zaman çerçevesini sağlayın ve kaynak adını bize e-posta ile gönderin.

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tips"></a>İpuçları

<a name="view_request"></a>

* İstemciden giden istek nasıl görüntülenir?
ASP.NET Core bir örnek alın (ASP.NET One benzerdir):
    * Tarayıcıdan:

        Chrome 'u örnek olarak alın; **F12** kullanarak konsol penceresini açabilir ve **ağ** sekmesine geçebilirsiniz. Ağı çok başlayarak yakalamak için **F5** 'i kullanarak sayfayı yenilemeniz gerekebilir.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Chrome görünüm ağı":::

    * C# istemcisinden:

        [Fiddler](https://www.telerik.com/fiddler)kullanarak yerel Web traffics görüntüleyebilirsiniz. WebSocket traffics, Fiddler 4,5 ' den itibaren desteklenmektedir.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Fiddler görüntüleme ağı" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* İstemci bağlantısı nasıl yeniden başlatılır?
    
    *Her zaman yeniden deneme* stratejisiyle bağlantı mantığını yeniden başlatmayı içeren [örnek kodlar](https://github.com/Azure/azure-signalr/tree/dev/samples) şunlardır:

    * [ASP.NET Core C# istemcisi](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [JavaScript Istemcisi ASP.NET Core](https://github.com/Azure/azure-signalr/blob/release/1.0.0-preview1/samples/ChatSample/wwwroot/index.html#L164)

    * [ASP.NET C# istemcisi](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [ASP.NET JavaScript Istemcisi](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, yaygın sorunları nasıl işleyeceğinizi öğrendiniz. Daha fazla genel sorun giderme yöntemi de öğrenebilirsiniz. 

> [!div class="nextstepaction"]
> [Bağlantı ve ileti teslimi sorunlarını giderme](./signalr-howto-troubleshoot-method.md)
