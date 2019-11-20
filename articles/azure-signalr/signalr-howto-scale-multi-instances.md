---
title: Birden çok örnek ile ölçeklendirme-Azure SignalR hizmeti
description: Birçok ölçeklendirme senaryosunda, müşterinin genellikle birden çok örnek sağlaması ve büyük ölçekli bir dağıtım oluşturmak için bunları birlikte kullanmak üzere yapılandırması gerekir. Örneğin, parça birden çok örnek desteği gerektirir.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158164"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>SignalR hizmetini birden çok örnek ile ölçeklendirme
En son SignalR hizmeti SDK 'Sı, SignalR hizmet örnekleri için birden çok uç noktayı destekler. Bu özelliği, eş zamanlı bağlantıları ölçeklendirmek veya bölgeler arası mesajlaşma için kullanmak üzere kullanabilirsiniz.

## <a name="for-aspnet-core"></a>ASP.NET Core için

### <a name="how-to-add-multiple-endpoints-from-config"></a>Yapılandırmadan birden fazla uç nokta nasıl eklenir?

SignalR hizmeti bağlantı dizesi için anahtar `Azure:SignalR:ConnectionString` veya `Azure:SignalR:ConnectionString:` yapılandırma.

Anahtar `Azure:SignalR:ConnectionString:`ile başlıyorsa, `Name` ve `EndpointType` `ServiceEndpoint` nesnesinin özellikleri olduğu ve koddan erişilebilen `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`biçiminde olmalıdır.

Aşağıdaki `dotnet` komutlarını kullanarak birden çok örnek bağlantı dizesi ekleyebilirsiniz:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Koddan birden fazla uç nokta nasıl eklenir?

Bir Azure SignalR hizmeti uç noktasının özelliklerini açıklamaya yönelik bir `ServicEndpoint` sınıfı sunulmuştur.
Azure SignalR Service SDK kullanırken şu şekilde birden çok örnek uç noktası yapılandırabilirsiniz:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Uç nokta yönlendirici nasıl özelleştirilir?

Varsayılan olarak SDK, son noktaları almak için [Defaultendpointrouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) kullanır.

#### <a name="default-behavior"></a>Varsayılan davranış 
1. İstemci isteği yönlendirme

    İstemci, uygulama sunucusuyla `/negotiate`. Varsayılan olarak SDK, kullanılabilir hizmet uç noktaları kümesinden bir uç noktayı **rastgele seçer** .

2. Sunucu iletisi yönlendirme

    \* Belirli bir * * bağlantısına ileti gönderiyor * * * ve hedef bağlantı geçerli sunucuya yönlendirilse, ileti doğrudan o bağlı uç noktaya gider. Aksi takdirde, iletiler her Azure SignalR uç noktasına alınır.

#### <a name="customize-routing-algorithm"></a>Yönlendirme algoritmasını özelleştirme
İletilerin gitmesi gereken uç noktaları belirlemek için özel bilginiz varsa, kendi yönlendiricinizi oluşturabilirsiniz.

Bir özel yönlendirici aşağıda, `east-` ile başlayan gruplar her zaman `east`adlı uç noktaya gidecebir örnek olarak tanımlanmıştır:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Aşağıdaki diğer bir örnek, uç noktaların seçimi için varsayılan anlaşma davranışını geçersiz kılan, uygulama sunucusunun bulunduğu yere bağlıdır.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Şunu kullanarak şu şekilde yönlendirici için olan yönlendiriciyi kaydetmeyi unutmayın:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>ASP.NET için

### <a name="how-to-add-multiple-endpoints-from-config"></a>Yapılandırmadan birden fazla uç nokta nasıl eklenir?

SignalR hizmeti bağlantı dizesi için anahtar `Azure:SignalR:ConnectionString` veya `Azure:SignalR:ConnectionString:` yapılandırma.

Anahtar `Azure:SignalR:ConnectionString:`ile başlıyorsa, `Name` ve `EndpointType` `ServiceEndpoint` nesnesinin özellikleri olduğu ve koddan erişilebilen `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`biçiminde olmalıdır.

`web.config`, birden çok örnek bağlantı dizesi ekleyebilirsiniz:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Koddan birden fazla uç nokta nasıl eklenir?

Bir Azure SignalR hizmeti uç noktasının özelliklerini açıklamaya yönelik bir `ServicEndpoint` sınıfı sunulmuştur.
Azure SignalR Service SDK kullanırken şu şekilde birden çok örnek uç noktası yapılandırabilirsiniz:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Yönlendirici nasıl özelleştirilir?

ASP.NET SignalR ve ASP.NET Core SignalR arasındaki tek fark, `GetNegotiateEndpoint`için http bağlam türüdür. ASP.NET SignalR için, bu, [ıowincontext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) türüdür.

Aşağıda ASP.NET SignalR için özel anlaşma örneği verilmiştir:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Şunu kullanarak şu şekilde yönlendirici için olan yönlendiriciyi kaydetmeyi unutmayın:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Bölgeler arası senaryolarda yapılandırma

`ServiceEndpoint` nesnesi, `primary` veya `secondary`değeri olan bir `EndpointType` özelliğine sahiptir.

`primary` uç noktaları, istemci trafiği almak için tercih edilen uç noktalardır ve daha güvenilir ağ bağlantılarına sahip olduğu kabul edilir; `secondary` uç noktaları, daha az güvenilir ağ bağlantılarına sahip olduğu kabul edilir ve yalnızca sunucuyu istemci trafiğine almak için kullanılır; Örneğin, istemci sunucu trafiği almak için değil.

Bölgeler arası durumlarda ağ kararsız olabilir. *Doğu ABD*bulunan bir uygulama sunucusu için, aynı *Doğu ABD* bölgesinde bulunan SignalR hizmeti uç noktası, `secondary`olarak işaretlenen diğer bölgelerde `primary` ve uç noktalar olarak yapılandırılabilir. Bu yapılandırmada, diğer bölgelerdeki hizmet uç noktaları bu *Doğu ABD* App Server 'dan iletiler **alabilir** , ancak bu uygulama sunucusuna hiçbir **çapraz bölge** istemcisi yönlendirilmeyecektir. Mimari aşağıdaki diyagramda gösterilmiştir:

![Çapraz coğrafi bölgeler](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Bir istemci App Server ile `/negotiate` denediğinde varsayılan yönlendirici ile SDK, kullanılabilir `primary` uç noktaları kümesinden bir uç nokta **rastgele seçer** . Birincil uç nokta kullanılabilir olmadığında, SDK, tüm kullanılabilir `secondary` uç noktalarından **rastgele seçer** . Uç nokta, sunucu ve hizmet uç noktası arasındaki bağlantı etkin olduğunda **kullanılabilir** olarak işaretlenir.

Bölgeler arası senaryoda, bir istemci *Doğu ABD*' de barındırılan uygulama sunucusuyla `/negotiate` denediğinde, varsayılan olarak her zaman aynı bölgedeki `primary` uç noktasını döndürür. Tüm *Doğu ABD* uç noktaları kullanılabilir olmadığında, istemci diğer bölgelerdeki uç noktalara yönlendirilir. Aşağıdaki yük devretme bölümünde senaryo ayrıntılı olarak açıklanmaktadır.

![Normal anlaşma](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Yük devretme

Tüm `primary` uç noktaları kullanılabilir olmadığında, istemci `/negotiate` kullanılabilir `secondary` uç noktalarından seçer. Bu yük devretme mekanizması her bir uç noktanın en az bir uygulama sunucusuna `primary` uç nokta olarak sunması gerekir.

![Yük devretme](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, aynı uygulamada ölçeklendirme, parçalama ve çapraz bölge senaryoları için birden çok örneği nasıl yapılandıracağınızı öğrendiniz.

Yüksek kullanılabilirlik ve olağanüstü durum kurtarma senaryolarında de birden fazla uç nokta desteklenir.

> [!div class="nextstepaction"]
> [Olağanüstü durum kurtarma ve yüksek kullanılabilirlik için SignalR hizmetini kurma](./signalr-concept-disaster-recovery.md)
