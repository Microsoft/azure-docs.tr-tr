---
title: Azure SignalR hizmeti için sorun giderme yöntemi
description: Bağlantı ve ileti teslimi sorunlarını giderme hakkında bilgi edinin
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: ed8775c6544791571746b0f3784a60ce2af0de7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726983"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>Bağlantı ve ileti teslimi sorunlarını giderme

Bu kılavuzda, kök nedeni doğrudan bulmak veya sorunu daraltmak için kendi kendine tanılama yapmanıza yardımcı olacak çeşitli yollar tanıtılmaktadır. Kendi kendine Tanılama sonucu, daha fazla araştırma için bize bildirirken de yararlı olur.

İlk olarak, hangi [Servicemode](./concept-service-mode.md) 'ın ( **ASRS** olarak da bilinir) yapılandırıldığı Azure Portal kontrol etmeniz gerekir.

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* `Default`Mod için, [varsayılan mod sorun giderme](#default_mode_tsg) bölümüne bakın

* `Serverless`Mod için [sunucusuz mod sorun giderme](#serverless_mode_tsg) bölümüne bakın

* `Classic`Mod için [Klasik mod sorun giderme](#classic_mode_tsg) bölümüne bakın

<a name="default_mode_tsg"></a>

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="default-mode-troubleshooting"></a>Varsayılan mod sorunlarını giderme

**ASRS** *varsayılan* moddayken **üç** rol vardır: *istemci*, *sunucu* ve *hizmet*:

* *İstemci*: *Istemci* , **ASRS**'ye bağlı istemcileri temsil eder. İstemci ve **ASRS** 'ye bağlanan kalıcı bağlantılara bu kılavuzda *istemci bağlantıları* denir.

* *Sunucu*: *sunucu* , istemci anlaşmasına hizmet veren ve SignalR mantığını barındıran sunucuyu temsil eder `Hub` . Ve *sunucu* ve **ASRS** arasındaki kalıcı bağlantılara bu kılavuzda *sunucu bağlantıları* denir.

* *Service*: *Service* , bu kılavuzdaki **ASRS** için kısa bir addır.

Tüm mimarinin ve iş akışının ayrıntılı tanıtımı için [Azure SignalR hizmeti 'Nin Iç işlevleri](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) bölümüne bakın.

Sorunu daraltmanıza yardımcı olabilecek çeşitli yollar vardır. 

* Sorun bir şekilde doğru gerçekleşirse veya yeniden üretme yapılacağından, düz iletme yöntemi açık olan trafiği görüntülemek için kullanılır. 

* Sorun yeniden üretme zor olursa izlemeler ve Günlükler yardımcı olabilir.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>Trafiği görüntüleme ve sorunu daraltma

Devam eden trafiği yakalamak, sorunu daraltmak için en basit yoldur. [Ağ izlemelerini](/aspnet/core/signalr/diagnostics#network-traces) aşağıda açıklanan seçenekleri kullanarak yakalayabilirsiniz:

* [Fiddler ile ağ izlemesi toplama](/aspnet/core/signalr/diagnostics#network-traces)

* [Tcpdump ile ağ izlemesi toplama](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [Tarayıcıda bir ağ izlemesi toplayın](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>İstemci istekleri

Bir SignalR kalıcı bağlantısı için, önce `/negotiate` barındırılan uygulama sunucunuza yönlendirilir ve ardından Azure SignalR hizmetine yeniden yönlendirilir ve ardından Azure SignalR hizmetine gerçek kalıcı bağlantıyı kurar. Ayrıntılı adımlar için [Azure SignalR hizmeti Iç işlevleri](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) bölümüne bakın.

İstemci tarafı ağ izleme ile, hangi isteğin hangi durum kodu ve Yanıt ile başarısız olduğunu kontrol edin ve [sorun giderme kılavuzu](./signalr-howto-troubleshoot-guide.md)içindeki çözümleri arayın.

#### <a name="server-requests"></a>Sunucu istekleri

SignalR *Server* *sunucu ve* *hizmet* arasında *sunucu bağlantısını* korur. Uygulama sunucusu başlatıldığında, Azure SignalR hizmetine **WebSocket** bağlantısı başlatılır. Tüm istemci traffics, bu *sunucu bağlantısına* Azure SignalR hizmeti aracılığıyla yönlendirilir ve ardından öğesine dağıtılır `Hub` . *Sunucu bağlantısı* düşerse, bu *sunucu bağlantısına* yönlendirilen istemciler etkilenecektir. Azure SignalR SDK 'mız, *sunucu bağlantısını* en fazla 1 dakikalık gecikmeyle yeniden bağlamak Için "her zaman yeniden dene" mantığını içerir.

*Sunucu bağlantısı*, Azure SignalR hizmeti 'nin ağ kararsızlığına veya düzenli olarak bakımına veya barındırılan App Server güncelleştirme/bakım nedeniyle düşürülemiyor. İstemci tarafında bir bağlantı kesme/yeniden bağlanma mekanizması olduğu sürece, etki tüm istemci tarafı bağlantı kesilmesinin başarısız olması gibi en düşük düzeyde etki olur.

Sunucu tarafı ağ izlemesini görüntüleyerek, durum kodunu ve *sunucu bağlantısının* neden hizmet tarafından reddedildiğini veya *hizmetten* kaynaklandığını öğrenmek Için hata ayrıntılarını ve [sorun giderme kılavuzu](./signalr-howto-troubleshoot-guide.md)içinde kök nedeni arayın.

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

### <a name="how-to-add-logs"></a>Günlükleri ekleme

Günlükler sorunları tanılamak ve çalışma durumunu izlemek için yararlı olabilir.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>İstemci tarafı günlüğünü etkinleştirme

İstemci tarafı günlüğe kaydetme deneyimi, şirket içinde barındırılan SignalR kullanırken tam olarak aynıdır.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>İçin istemci tarafı günlüğe kaydetmeyi etkinleştir `ASP.NET Core SignalR`

* [JavaScript istemci günlüğü](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [.NET istemci günlüğü](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>İçin istemci tarafı günlüğe kaydetmeyi etkinleştir `ASP.NET SignalR`

* [.NET istemcisi](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Windows Phone 8 istemcilerde izlemeyi etkinleştirme](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [JavaScript istemcisinde izlemeyi etkinleştirme](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>Sunucu tarafı günlüğünü etkinleştirme

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Sunucu tarafı günlük kaydını etkinleştirme `ASP.NET Core SignalR`

İçin sunucu tarafında günlüğe kaydetme `ASP.NET Core SignalR` `ILogger` , çerçevede belirtilen tabanlı [günlük](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&preserve-view=true&view=aspnetcore-2.1) ile tümleşir `ASP.NET Core` . Aşağıdaki gibi bir örnek kullanım kullanarak sunucu tarafı günlük kaydını etkinleştirebilirsiniz `ConfigureLogging` :

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Azure SignalR için günlükçü kategorileri her zaman ile başlar `Microsoft.Azure.SignalR` . Azure SignalR 'den ayrıntılı günlükleri etkinleştirmek için, yukarıdaki ön ekleri `Information` aşağıdaki gibi **appsettings.js** dosya düzeyinde yapılandırın:

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

Olağan dışı bir uyarı/hata günlüğü kaydedilip kaydedilmediğini kontrol edin. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>İçin sunucu tarafı izlemelerini etkinleştir `ASP.NET SignalR`

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

Olağan dışı bir uyarı/hata günlüğü kaydedilip kaydedilmediğini kontrol edin. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Azure SignalR hizmeti içinde günlükleri etkinleştirme

Ayrıca, Azure SignalR hizmeti için [tanılama günlüklerini etkinleştirebilirsiniz](./signalr-howto-diagnostic-logs.md) , bu Günlükler Azure SignalR hizmetine bağlı her bağlantıyla ilgili ayrıntılı bilgi sağlar.

<a name="serverless_mode_tsg"></a>

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="serverless-mode-troubleshooting"></a>Sunucusuz mod sorunlarını giderme

**ASRS** *sunucusuz* moddayken yalnızca **ASP.NET Core signalr** `Serverless` modunu **destekler** ve **ASP.NET SignalR** bu modu desteklemez.

Bağlantı sorunlarını modunda tanılamak için `Serverless` , en basit iletme, [istemci tarafı trafiğini görüntüleme](#view_traffic_client)yöntemidir. [İstemci tarafı günlüklerini](#add_logs_client) ve [hizmet tarafı günlüklerini](#add_logs_server) etkinleştir de yararlı olabilir.

<a name="classic_mode_tsg"></a>

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="classic-mode-troubleshooting"></a>Klasik mod sorunlarını giderme

`Classic` modun kullanım dışı bırakılmış ve kullanılması önerilir. Bu modda, Azure SignalR hizmeti, geçerli hizmetin modda mı yoksa modda mı olduğunu anlamak için bağlı *sunucu bağlantılarını* kullanır `default` `serverless` . Bu, bazı ara istemci bağlantı sorunlarına yol açabilir, çünkü Örneğin, ağ kararsızlık *nedeniyle, Azure* SignalR, artık moda geçmekte `serverless` ve bu süre boyunca bağlanan istemciler hiçbir zaman barındırılan uygulama sunucusuna yönlendirilmeyecektir. [Hizmet tarafı günlüklerini](#add_logs_server) etkinleştirin ve `ServerlessModeEntered` App Server 'ı barındırdıysanız, ancak bazı istemcilerin App Server tarafına hiç ulaşmadığını denetleyin. Varsa, [Bu istemci bağlantılarını iptal](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) edin ve istemcilerin yeniden başlatılmasına izin verin.

Sorun giderme `classic` modu bağlantısı ve ileti teslimi sorunları, [varsayılan mod sorunlarını gidermeye](#default_mode_tsg)benzer.

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="service-health"></a>Hizmet durumu

Hizmet durumu için sistem sağlığı API 'sini kontrol edebilirsiniz.

* İstek: Al `https://{instance_name}.service.signalr.net/api/v1/health`

* Yanıt durum kodu:
  * 200: sağlıklı.
  * 503: hizmetiniz sağlıklı değil. Seçenekleriniz şunlardır:
    * Otomatik kurtarma için birkaç dakika bekleyin.
    * IP adresinin portaldan IP ile aynı olup olmadığını denetleyin.
    * Veya örneği yeniden başlatın.
    * Yukarıdaki tüm seçenekler çalışmazsa, Azure portal yeni destek isteği ekleyerek bizimle iletişim kurun.

[Olağanüstü durum kurtarma](./signalr-concept-disaster-recovery.md)hakkında daha fazla bilgi.

[Sorun giderme hakkında sorunlar veya geri bildirimler mi var? Bize bilgi verin.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, bağlantı ve ileti teslimi sorunlarını giderme hakkında bilgi edindiniz. Ortak sorunları nasıl ele alabileceğinizi de öğrenebilirsiniz. 

> [!div class="nextstepaction"]
> [Sorun giderme kılavuzu](./signalr-howto-troubleshoot-guide.md)