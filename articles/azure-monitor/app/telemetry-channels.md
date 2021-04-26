---
title: Azure Application Insights telemetri kanalları | Microsoft Docs
description: .NET ve .NET Core için Azure Application Insights SDK 'larda telemetri kanallarını özelleştirme.
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: a22a0d112671019d73eb4c9a3853462e4e9c8c75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98937361"
---
# <a name="telemetry-channels-in-application-insights"></a>Application Insights telemetri kanalları

Telemetri kanalları, [Azure Application Insights SDK](./app-insights-overview.md)'larının ayrılmaz bir parçasıdır. Bu kişiler Application Insights hizmetine telemetri arabelleğini ve aktarımını yönetir. SDK 'ların .NET ve .NET Core sürümlerinde iki yerleşik telemetri kanalı vardır: `InMemoryChannel` ve `ServerTelemetryChannel` . Bu makalede kanal davranışının nasıl özelleştirileceği dahil olmak üzere her bir kanal ayrıntılı olarak açıklanmaktadır.

## <a name="what-are-telemetry-channels"></a>Telemetri kanalları nelerdir?

Telemetri kanalları, telemetri öğelerini arabelleğe almayı ve bunları sorgulamak ve analiz etmek için depolanacağı Application Insights hizmetine göndermekten sorumludur. Telemetri kanalı arabirimini uygulayan herhangi bir sınıftır [`Microsoft.ApplicationInsights.ITelemetryChannel`](/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel) .

`Send(ITelemetry item)`Telemetri kanalının yöntemi, tüm telemetri başlatıcıları ve telemetri işlemcileri çağrıldıktan sonra çağrılır. Bu nedenle, bir telemetri işlemcisi tarafından bırakılan tüm öğeler kanala erişmez. `Send()` genellikle öğeleri arka uca anında göndermez. Genellikle, bunları bellekte arabelleğe alır ve etkili iletim için bunları toplu halde gönderir.

[Canlı ölçüm akışı](live-stream.md) Ayrıca telemetri canlı akışını destekleyen özel bir kanala sahiptir. Bu kanal, normal telemetri kanalından bağımsızdır ve bu belge kendisine uygulanmaz.

## <a name="built-in-telemetry-channels"></a>Yerleşik telemetri kanalları

Application Insights .NET ve .NET Core SDK 'Ları, iki yerleşik kanala sahiptir:

* `InMemoryChannel`: Bellekteki öğeleri gönderilene kadar arabelleğe alan hafif bir kanal. Öğelerin belleği arabelleğe alınır ve 30 saniyede bir kez veya 500 öğe arabelleğe alındıktan sonra temizlenir. Bu kanal, bir hatadan sonra telemetri göndermeyi yeniden denemediğinden en düşük güvenilirlik garantisi sunar. Bu kanal Ayrıca öğeleri diskte tutmadığından, hiçbir gönderilmemiş öğe, uygulama kapatıldıktan sonra kalıcı olarak kaybedilir (düzgün veya değil). Bu kanal, `Flush()` bellek içi telemetri öğelerini eşzamanlı olarak zorlamak için kullanılabilecek bir yöntemi uygular. Bu kanal, zaman uyumlu temizleme ideal olduğunda kısa süreli uygulamalar için uygundur.

    Bu kanal, Microsoft. ApplicationInsights NuGet paketinin daha büyük bir parçasıdır ve başka hiçbir şey yapılandırılmadığı zaman SDK 'nın kullandığı varsayılan kanaldır.

* `ServerTelemetryChannel`: Yeniden deneme ilkelerine sahip daha gelişmiş bir kanal ve verileri yerel bir diskte depolama özelliği. Bu kanal, geçici hatalar oluşursa telemetri göndermeyi yeniden dener. Bu kanal Ayrıca, ağ kesintileri veya yüksek telemetri birimleri sırasında öğeleri diskte tutmak için yerel disk depolama alanını kullanır. Bu yeniden deneme mekanizmaları ve yerel disk depolaması nedeniyle, bu kanal daha güvenilir kabul edilir ve tüm üretim senaryolarında önerilir. Bu kanal, resmi belgelere göre yapılandırılan [ASP.net](./asp-net.md) ve [ASP.NET Core](./asp-net-core.md) uygulamaları için varsayılandır. Bu kanal, uzun süre çalışan işlemlerle sunucu senaryoları için iyileştirilmiştir. [`Flush()`](#which-channel-should-i-use)Bu kanal tarafından uygulanan yöntem zaman uyumlu değil.

    Bu kanal Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel NuGet paketi olarak gönderilir ve Microsoft. ApplicationInsights. Web ya da Microsoft. ApplicationInsights. AspNetCore NuGet paketini kullandığınızda otomatik olarak alınır.

## <a name="configure-a-telemetry-channel"></a>Telemetri kanalını yapılandırma

Bir telemetri kanalını etkin telemetri yapılandırmasına ayarlayarak yapılandırırsınız. ASP.NET uygulamalar için, yapılandırma telemetri kanalı örneğinin `TelemetryConfiguration.Active` ' ı ' ya ayarlamayı ya da değiştirmesini içerir `ApplicationInsights.config` . ASP.NET Core uygulamalar için, yapılandırma kanalı bağımlılık ekleme kapsayıcısına eklemeyi içerir.

Aşağıdaki bölümlerde, `StorageFolder` çeşitli uygulama türlerinde kanal için ayar yapılandırma örnekleri gösterilmektedir. `StorageFolder` , yapılandırılabilir ayarlardan yalnızca biridir. Yapılandırma ayarlarının tam listesi için, bu makalenin ilerleyen bölümlerinde bulunan [Ayarlar bölümüne](#configurable-settings-in-channels) bakın.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>ASP.NET uygulamaları için ApplicationInsights.config kullanarak yapılandırma

[ApplicationInsights.config](configuration-with-applicationinsights-config.md) aşağıdaki bölümünde, `ServerTelemetryChannel` `StorageFolder` özel bir konuma ayarlanmış olarak yapılandırılan kanal gösterilmektedir:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>ASP.NET uygulamaları için koddaki yapılandırma

Aşağıdaki kod, özel bir konuma ayarlanmış bir ' ServerTelemetryChannel ' örneği oluşturur `StorageFolder` . Bu kodu, genellikle `Application_Start()` Global. aspx. cs içindeki yönteminde, uygulamanın başına ekleyin.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET Core uygulamalar için koddaki yapılandırma

`ConfigureServices` `Startup.cs` Sınıfının yöntemini burada gösterildiği gibi değiştirin:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> Kanalı kullanılarak yapılandırma `TelemetryConfiguration.Active` ASP.NET Core uygulamalar için önerilmez.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>.NET/.NET Core konsol uygulamaları için koddaki yapılandırma

Konsol uygulamaları için kod hem .NET hem de .NET Core için aynıdır:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel işletimsel ayrıntıları

`ServerTelemetryChannel` gelen öğeleri bellek içi arabellekte depolar. Öğeler, her 30 saniyede bir örnek olarak serileştirilir, sıkıştırılır ve saklanır `Transmission` , ya da 500 öğe ara belleğe alındıktan sonra. Tek bir `Transmission` örnek en fazla 500 öğe içerir ve Application Insights hizmetine tek BIR https çağrısıyla gönderilen bir telemetri toplu işini temsil eder.

Varsayılan olarak, en fazla 10 `Transmission` örnek paralel olarak gönderilebilir. Telemetri daha hızlı bir şekilde veya ağ veya Application Insights arka ucu yavaşsa, `Transmission` örnekler bellekte depolanır. Bu bellek içi arabelleğin varsayılan kapasitesi `Transmission` 5 MB 'tır. Bellek içi kapasite aşıldığında, `Transmission` örnekler 50 MB 'lik bir sınıra kadar yerel diskte depolanır. `Transmission` örnekler yerel diskte ağ sorunları olduğunda da depolanır. Yalnızca yerel bir diskte depolanan öğeler, uygulama kilitlenmesinin varlığını sürdürmez. Bunlar, uygulama her başlatıldığında gönderilir.

## <a name="configurable-settings-in-channels"></a>Kanallarda yapılandırılabilir ayarlar

Her kanala yönelik yapılandırılabilir ayarların tam listesi için bkz.:

* [Inmemorychannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Aşağıda, için en yaygın olarak kullanılan ayarlar verilmiştir `ServerTelemetryChannel` :

1. `MaxTransmissionBufferCapacity`: Kanal tarafından bellekteki aktarımları arabelleğe almak için kullanılan bayt cinsinden en yüksek bellek miktarı. Bu kapasiteye ulaşıldığında, yeni öğeler doğrudan yerel diske depolanır. Varsayılan değer 5 MB 'tır. Daha yüksek bir değer ayarlandığında disk kullanımı daha az olur, ancak uygulama kilitlenirse bellekteki öğelerin kaybolacağını unutmayın.

1. `MaxTransmissionSenderCapacity`: `Transmission` Aynı anda Application Insights gönderilecek en fazla örnek sayısı. Varsayılan değer 10'dur. Bu ayar daha yüksek bir sayıya yapılandırılabilir ve bu, çok büyük bir telemetri hacmi oluşturulması önerilir. Yüksek hacim genellikle yük testi sırasında veya örnekleme kapalıyken oluşur.

1. `StorageFolder`: Kanal tarafından öğeleri gerektiği şekilde diske depolamak için kullanılan klasör. Açık olarak başka hiçbir yol belirtilmemişse, Windows 'ta% LOCALAPPDATA% veya% TEMP% kullanılır. Windows dışındaki ortamlarda, geçerli bir konum belirtmeniz gerekir veya telemetri yerel diske depolanmaz.

## <a name="which-channel-should-i-use"></a>Hangi kanalı kullanmalıyım?

`ServerTelemetryChannel` uzun süre çalışan uygulamalarla ilgili birçok üretim senaryosu için önerilir. `Flush()`Tarafından uygulanan yöntem `ServerTelemetryChannel` zaman uyumlu değildir ve ayrıca, tüm bekleyen öğelerin bellekten veya diskten gönderilmesini garanti etmez. Uygulamanın kapanmak üzere olduğu senaryolarda bu kanalı kullanırsanız, çağrıldıktan sonra biraz gecikme yapmanızı öneririz `Flush()` . Gerekebilecek tam gecikme miktarı tahmin edilebilir değildir. Bu, kaç öğe veya `Transmission` örnek bellek içinde, kaç tane diskte olduğunu, arka uca kaç tane iletildiğini ve kanalın üstel geri alma senaryolarında ortalanıp içinde olup olmadığını gösteren faktörlere bağlıdır.

Zaman uyumlu bir temizleme yapmanız gerekiyorsa kullanmanızı öneririz `InMemoryChannel` .

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Application Insights kanal telemetri teslimini garanti eder mi? Aksi takdirde, telemetri kaybı olabilecek senaryolar nelerdir?

Kısa yanıt, yerleşik kanalların hiçbirinin arka uca telemetri tesliminde işlem türü garantisi sunamayacağı bir işlemdir. `ServerTelemetryChannel``InMemoryChannel`, güvenilir teslimat için ile karşılaştırıldığında daha gelişmiş bir işlem yapmaktadır, ancak aynı zamanda Telemetriyi yalnızca en iyi şekilde göndermeye çalışır. Telemetri, şu yaygın senaryolar da dahil olmak üzere çeşitli durumlarda kaybolabilir:

1. Uygulama kilitlenirse bellekteki öğeler kaybedilir.

1. Telemetri, ağ sorunlarının genişletilmiş dönemlerinde kaybedilir. Telemetri, ağ kesintileri sırasında veya Application Insights arka ucunda sorun oluştuğunda yerel diske depolanır. Ancak, 48 saatten eski olan öğeler atılır.

1. Windows 'da telemetri depolamaya yönelik varsayılan disk konumları% LOCALAPPDATA% veya% TEMP%. Bu konumlar genellikle makineye yereldir. Uygulama fiziksel olarak bir konumdan diğerine geçerse, özgün konumda depolanan tüm telemetri kaybedilir.

1. Windows üzerinde Web Apps, varsayılan disk depolama konumu D:\local\localappdata' dir. Bu konum kalıcı değil. Bu, uygulama yeniden başlatmaları, ölçek aşımları ve bu gibi diğer işlemlerde, orada depolanan hiçbir Telemetriyi yitirerek temizlenir. Varsayılan ayarı geçersiz kılabilir ve D:\homegibi kalıcı bir konuma depolamayı belirtebilirsiniz. Ancak, bu tür kalıcı konumlar uzak depolama tarafından sunulur ve bu nedenle yavaş olabilir.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel, Windows dışındaki sistemlerde çalışır mı?

Paketinin ve ad alanının adı "WindowsServer" içerse de, bu kanal Windows dışındaki sistemlerde aşağıdaki özel durumla desteklenir. Windows dışındaki sistemlerde, kanal varsayılan olarak yerel bir depolama klasörü oluşturmaz. Yerel bir depolama klasörü oluşturmanız ve kanalı kullanacak şekilde yapılandırmanız gerekir. Yerel depolama yapılandırıldıktan sonra kanal tüm sistemlerde aynı şekilde çalışacaktır.

> [!NOTE]
> 2.15.0-Beta3 ve daha büyük yerel depolama sürümü ile artık Linux, Mac ve Windows için otomatik olarak oluşturulur. Windows dışı sistemler için SDK, aşağıdaki mantığa göre otomatik olarak yerel bir depolama klasörü oluşturacaktır:
> - `${TMPDIR}` - `${TMPDIR}` ortam değişkeni ayarlanmışsa bu konum kullanılır.
> - `/var/tmp` -önceki konum yoksa, denememiz önerilir `/var/tmp` .
> - `/tmp` -bir önceki konumların her ikisi de yoksa, denememiz önerilir `tmp` . 
> - Bu konumların hiçbiri yoksa yerel depolama alanı oluşturulmaz ve el ile yapılandırma yine de gereklidir. [Tam uygulama ayrıntıları için](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860).

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK geçici yerel depolama alanı mı oluşturur? Veriler depolamada şifrelendi mı?

SDK, telemetri öğelerini ağ sorunları sırasında veya daraltma sırasında yerel depolama alanında depolar. Bu veriler yerel olarak şifrelenmez.

Windows sistemlerinde, SDK otomatik olarak% TEMP% veya% LOCALAPPDATA% dizininde geçici bir yerel klasör oluşturur ve yöneticilere ve yalnızca geçerli kullanıcıya erişimi kısıtlar.

Windows dışındaki sistemler için, SDK tarafından otomatik olarak hiçbir yerel depolama oluşturulmaz ve varsayılan olarak hiçbir veri yerel olarak depolanmaz.

> [!NOTE]
> 2.15.0-Beta3 ve daha büyük yerel depolama sürümü ile artık Linux, Mac ve Windows için otomatik olarak oluşturulur. 

 Kendiniz bir depolama dizini oluşturabilir ve kanalı kullanmak üzere yapılandırabilirsiniz. Bu durumda, dizinin güvenliğinin sağlanması sağlanmasından sorumlu olursunuz.
[Veri koruma ve gizlilik](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)hakkında daha fazla bilgi edinin.

## <a name="open-source-sdk"></a>Açık kaynaklı SDK
Application Insights için her SDK gibi kanallar açık kaynaktır. [Resmi GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet)deposunda kodu okuyun ve katkıda bulunun veya sorunları raporla.

## <a name="next-steps"></a>Sonraki adımlar

* [Örnekleme](./sampling.md)
* [SDK sorunlarını giderme](./asp-net-troubleshoot-no-data.md)

