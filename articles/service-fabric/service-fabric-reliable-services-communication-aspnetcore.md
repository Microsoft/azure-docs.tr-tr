---
title: ASP.NET Core ile hizmet iletişimi
description: Durum bilgisiz ve durum bilgisi olan Azure Service Fabric Reliable Services uygulamalarında ASP.NET Core nasıl kullanacağınızı öğrenin.
ms.topic: conceptual
ms.date: 10/12/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: a125c6a1972b51f518175a4c69248119f71ada7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791603"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>Azure Service Fabric ASP.NET Core Reliable Services

ASP.NET Core, açık kaynaklı ve platformlar arası bir çerçevedir. Bu çerçeve, Web uygulamaları, IoT uygulamaları ve mobil arka uçlar gibi bulut tabanlı, internet 'e bağlı uygulamalar oluşturmak için tasarlanmıştır.

Bu makale, **Microsoft. ServiceFabric. AspNetCore** kullanarak Service Fabric Reliable Services ASP.NET Core hizmetlerinin barındırılmasına yönelik ayrıntılı bir kılavuzdur. NuGet paketleri kümesi.

Service Fabric ASP.NET Core giriş Öğreticisi ve geliştirme ortamınızı ayarlama hakkında yönergeler için bkz. [öğretici: bir uygulamayı ASP.NET Core Web API ön uç hizmeti ve durum bilgisi olan bir arka uç hizmeti Ile oluşturma ve dağıtma](service-fabric-tutorial-create-dotnet-app.md).

Bu makalenin geri kalanında ASP.NET Core zaten bildiğiniz varsayılmaktadır. Aksi takdirde, lütfen [ASP.NET Core temellerini](/aspnet/core/fundamentals/index)okuyun.

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric ortamında ASP.NET Core

Hem ASP.NET Core hem de Service Fabric uygulamalar .NET Core veya Full .NET Framework üzerinde çalışabilir. ASP.NET Core, Service Fabric iki farklı şekilde kullanabilirsiniz:
 - **Konuk yürütülebilir dosyası olarak barındırılır**. Bu şekilde birincil olarak, mevcut ASP.NET Core uygulamalarını kod değişikliği olmadan Service Fabric çalıştırmak için kullanılır.
 - **Güvenilir bir hizmetin Içinde çalıştırın**. Bu şekilde Service Fabric çalışma zamanına göre daha iyi tümleştirme sağlar ve durum bilgisi olan ASP.NET Core hizmetlerine izin verir.

Bu makalenin geri kalanında, Service Fabric SDK ile birlikte gelen ASP.NET Core tümleştirme bileşenleri aracılığıyla güvenilir bir hizmetin içinde ASP.NET Core nasıl kullanılacağı açıklanmaktadır.

## <a name="service-fabric-service-hosting"></a>Hizmet barındırma Service Fabric

Service Fabric, hizmet *ana bilgisayar işleminde* çalıştırdığınız bir veya daha fazla örnek ve/veya çoğaltma: hizmet kodunuzu çalıştıran yürütülebilir bir dosya. Hizmet yazarı olarak, hizmet ana bilgisayar işleminin sahibi olur ve Service Fabric, sizin için etkinleştirir ve izler.

Geleneksel ASP.NET (MVC 5 ' e kadar) System.Web.dll aracılığıyla IIS 'e sıkı bir şekilde bağlanmış. ASP.NET Core, Web sunucusu ile Web uygulamanız arasında bir ayrım sağlar. Bu ayrım, Web uygulamalarının farklı Web sunucuları arasında taşınabilir olmasını sağlar. Ayrıca, Web sunucularının *kendiliğinden barındırılmasına* izin verir. Bu, IIS gibi adanmış web sunucusu yazılımına ait olan bir işlemin aksine bir Web sunucusunu kendi işleminizde başlatabilmeniz anlamına gelir.

Bir Service Fabric hizmetini ve ASP.NET bir konuk yürütülebilir dosyası ya da güvenilir bir hizmette birleştirmek için, hizmet ana bilgisayar sürecinizin içinde ASP.NET 'i başlatabilmelisiniz. ASP.NET Core kendi kendine barındırma bunu yapmanıza olanak sağlar.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Güvenilir bir hizmette ASP.NET Core barındırma
Genellikle, şirket içinde barındırılan ASP.NET Core uygulamalar, bir uygulamanın giriş noktasında, içindeki yöntemi gibi bir WebHost oluşturur `static void Main()` `Program.cs` . Bu durumda, WebHost 'in yaşam döngüsü işlemin yaşam döngüsüne bağlanır.

![İşlem içinde ASP.NET Core barındırma][0]

Ancak uygulama giriş noktası, güvenilir bir hizmette bir WebHost oluşturmak için doğru yer değildir. Bunun nedeni, uygulama giriş noktasının yalnızca Service Fabric çalışma zamanına sahip bir hizmet türünü kaydetmek için kullanıldığı için kullanılır, böylece bu hizmet türünün örneklerini oluşturabilir. WebHost 'in güvenilir bir hizmetin kendisinde oluşturulması gerekir. Hizmet ana bilgisayarı işlemi içinde, hizmet örnekleri ve/veya çoğaltmalar birden çok yaşam döngüsü üzerinden geçebilir. 

Güvenilir bir hizmet örneği, veya ' den türetilen hizmet sınıfınız tarafından temsil edilir `StatelessService` `StatefulService` . Hizmet için iletişim yığını, `ICommunicationListener` hizmet sınıfınıza bir uygulamada bulunur. `Microsoft.ServiceFabric.AspNetCore.*`NuGet paketleri, `ICommunicationListener` güvenilir bir hizmette Kestrel veya HTTP.sys Için ASP.NET Core Webhost 'ı başlatan ve yöneten uygulamaları içerir.

![Güvenilir bir hizmette ASP.NET Core barındırma diyagramı][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Idimmunicationlisteners ASP.NET Core
`ICommunicationListener`NuGet paketlerindeki Kestrel ve HTTP.sys uygulamaları `Microsoft.ServiceFabric.AspNetCore.*` benzer kullanım düzenlerine sahiptir. Ancak her bir Web sunucusuna özel biraz farklı eylemler gerçekleştirir. 

Her iki iletişim dinleyicisi de aşağıdaki bağımsız değişkenleri alan bir oluşturucu sağlar:
 - **`ServiceContext serviceContext`**: Bu, `ServiceContext` çalışan hizmet hakkında bilgi içeren nesnedir.
 - **`string endpointName`**: Bu, ServiceManifest.xml bir yapılandırmanın adıdır `Endpoint` . Bu, birincil olarak iki iletişim dinleyicilerinin farklı olduğu yerdir. HTTP.sys bir yapılandırma *gerektirir* `Endpoint` , Kestrel değildir.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Bu, öğesini oluşturup döndür, uyguladığınız bir lambda 'dir `IWebHost` . Bu `IWebHost` , normalde ASP.NET Core bir uygulamada yaptığınız şekilde yapılandırmanıza olanak tanır. Lambda, kullandığınız Service Fabric tümleştirme seçeneklerine ve sağladığınız yapılandırmaya bağlı olarak sizin için oluşturulmuş bir URL sağlar `Endpoint` . Daha sonra, Web sunucusunu başlatmak için bu URL 'YI değiştirebilir veya kullanabilirsiniz.

## <a name="service-fabric-integration-middleware"></a>Service Fabric tümleştirme ara yazılımı
`Microsoft.ServiceFabric.AspNetCore`NuGet paketi, `UseServiceFabricIntegration` `IWebHostBuilder` Service Fabric ile uyumlu ara yazılım ekleyen üzerindeki genişletme yöntemini içerir. Bu ara yazılım, `ICommunicationListener` Service Fabric adlandırma hizmeti benzersiz bir hizmet URL 'sini kaydetmek Için Kestrel veya HTTP.sys yapılandırır. Daha sonra istemcilerin doğru hizmete bağlanmasını sağlamak için istemci isteklerini doğrular. 

İstemcilerin yanlış hizmete yanlışlıkla bağlanmasını engellemek için bu adım gereklidir. Bunun nedeni, Service Fabric gibi bir paylaşılan ana bilgisayar ortamında, birden çok Web uygulamasının aynı fiziksel veya sanal makinede çalıştırılabilmesi ancak benzersiz ana bilgisayar adları kullanmaması olabilir. Bu senaryo, sonraki bölümde daha ayrıntılı olarak açıklanmıştır.

### <a name="a-case-of-mistaken-identity"></a>Yanlış bir kimlik olması
Protokolden bağımsız olarak hizmet çoğaltmaları, benzersiz bir IP 'yi dinler: bağlantı noktası birleşimi. Bir hizmet çoğaltması bir IP: bağlantı noktası uç noktası üzerinde dinlemeyi başladıktan sonra, bu uç nokta adresini Service Fabric Adlandırma Hizmeti bildirir. Burada, istemcileri veya diğer hizmetleri bulabilir. Hizmetler dinamik olarak atanan uygulama bağlantı noktalarını kullanıyorsa, bir hizmet çoğaltması, daha önce aynı fiziksel veya sanal makinede bulunan başka bir hizmetin bağlantı noktası uç noktasını tesadüfen sağlayabilir. Bu, istemcinin yanlış hizmete yanlışlıkla bağlanmasına neden olabilir. Bu senaryo, aşağıdaki olay sırası oluşursa ortaya çıkabilir:

 1. A hizmeti, HTTP üzerinden 10.0.0.1:30000 üzerinde dinler. 
 2. İstemci, A hizmetini çözer ve 10.0.0.1:30000 adresini alır.
 3. A hizmeti farklı bir düğüme gider.
 4. Hizmet B, 10.0.0.1 'e yerleştirilir ve tesadüfen aynı bağlantı noktasını kullanır 30000.
 5. İstemci, hizmet A 'ya, 10.0.0.1:30000 adresli bir hizmete bağlanmaya çalışır.
 6. İstemci artık hizmet B 'ye başarıyla bağlandığından, yanlış hizmete bağlı olduğunu gerektirmez.

Bu, tanılanması zor olabilecek Rastgele zamanlarda hatalara neden olabilir.

### <a name="using-unique-service-urls"></a>Benzersiz hizmet URL 'Leri kullanma
Bu hataları engellemek için, hizmetler bir uç noktayı benzersiz bir tanımlayıcıyla Adlandırma Hizmeti gönderebilir ve ardından istemci istekleri sırasında bu benzersiz tanımlayıcıyı doğrulayabilir. Bu, güvenilmeyen kiracı olmayan bir ortamda bulunan hizmetler arasında bir yardımcı eylemdir. Saldırgan kiracı ortamında güvenli hizmet kimlik doğrulaması sağlamaz.

Güvenilen bir ortamda, yöntemi tarafından eklenen ara yazılım, `UseServiceFabricIntegration` adlandırma hizmeti gönderilen adrese otomatik olarak benzersiz bir tanımlayıcı ekler. Her istekte bu tanımlayıcıyı doğrular. Tanımlayıcı eşleşmezse, ara yazılım hemen bir HTTP 410 gitti yanıtı döndürür.

Dinamik olarak atanmış bir bağlantı noktası kullanan hizmetlerin bu ara yazılımı kullanması gerekir.

Sabit benzersiz bir bağlantı noktası kullanan hizmetlerde bu sorun, birlikte çalışmayan bir ortamda yoktur. Genellikle, istemci uygulamalarının bağlanması için iyi bilinen bir bağlantı noktasına ihtiyaç duyulan dışarıdan yönelik hizmetler için sabit benzersiz bir bağlantı noktası kullanılır. Örneğin, Internet 'e yönelik çoğu Web uygulaması, Web tarayıcısı bağlantıları için 80 veya 443 numaralı bağlantı noktasını kullanır. Bu durumda, benzersiz tanımlayıcı etkinleştirilmemelidir.

Aşağıdaki diyagramda, ara yazılım etkinleştirilmiş olan istek akışı gösterilmektedir:

![Service Fabric ASP.NET Core tümleştirmesi][2]

Hem Kestrel hem de HTTP.sys `ICommunicationListener` uygulamaları bu mekanizmayı tamamen aynı şekilde kullanır. HTTP.sys *, temel* alınan **HTTP.sys** bağlantı noktası paylaşma ÖZELLIĞINI kullanarak istekleri benzersiz URL yollarına göre dahili olarak ayırt edebilse de, bu işlev HTTP.sys uygulama tarafından kullanılmaz `ICommunicationListener` . Bunun nedeni, daha önce açıklanan senaryoda HTTP 503 ve HTTP 404 hata durumu kodlarına neden olur. Bu durumda, HTTP 503 ve HTTP 404 yaygın olarak diğer hataları göstermek için kullanıldığından, istemcilerin hatanın amacını belirlemesini zorlaştırır. 

Bu nedenle, hem Kestrel hem de HTTP.sys `ICommunicationListener` uygulamaları genişletme yöntemi tarafından sağlanmış olan ara yazılımlar üzerinde standartlaştırın `UseServiceFabricIntegration` . Bu nedenle, istemcilerin HTTP 410 yanıtları üzerinde yalnızca bir hizmet uç noktası yeniden çözümlemesi yapması gerekir.

## <a name="httpsys-in-reliable-services"></a>Reliable Services HTTP.sys
**Microsoft. ServiceFabric. AspNetCore. HttpSys** NuGet paketini içeri aktararak Reliable Services HTTP.sys kullanabilirsiniz. Bu paket `HttpSysCommunicationListener` , uygulamasının bir uygulamasını içerir `ICommunicationListener` . `HttpSysCommunicationListener` Web sunucusu olarak HTTP.sys kullanarak güvenilir bir hizmetin içinde ASP.NET Core WebHost oluşturmanıza olanak sağlar.

HTTP.sys, [WINDOWS http sunucu API 'si](/windows/win32/http/http-api-start-page)üzerine kurulmuştur. Bu API, HTTP isteklerini işlemek ve bunları Web uygulamaları çalıştıran işlemlere yönlendirmek için **HTTP.sys** çekirdek sürücüsünü kullanır. Bu, aynı fiziksel veya sanal makinedeki birden çok işlemin aynı bağlantı noktasında Web uygulamalarını barındırarak benzersiz bir URL yolu veya ana bilgisayar adıyla ayırt etmesine olanak tanır. Bu özellikler, aynı kümede birden çok Web sitesini barındırmak için Service Fabric yararlıdır.

>[!NOTE]
>HTTP.sys uygulama yalnızca Windows platformunda çalışmaktadır.

Aşağıdaki diyagramda, HTTP.sys bağlantı noktası paylaşımı için Windows üzerinde **HTTP.sys** çekirdek sürücüsünü nasıl kullandığı gösterilmektedir:

![HTTP.sys diyagramı][3]

### <a name="httpsys-in-a-stateless-service"></a>Durum bilgisi olmayan bir hizmette HTTP.sys
`HttpSys`Durum bilgisi olmayan bir hizmette kullanmak için, yöntemini geçersiz kılın `CreateServiceInstanceListeners` ve bir `HttpSysCommunicationListener` örnek döndürün:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>Durum bilgisi olan bir hizmette HTTP.sys

`HttpSysCommunicationListener` , temel alınan **HTTP.sys** bağlantı noktası paylaşma özelliği ile ilgili zorluklar nedeniyle şu anda durum bilgisi olan hizmetlerde kullanılmak üzere tasarlanmamıştır. Daha fazla bilgi için, HTTP.sys ile dinamik bağlantı noktası ayırma üzerinde aşağıdaki bölüme bakın. Durum bilgisi olan hizmetler için, Kestrel önerilen Web sunucusudur.

### <a name="endpoint-configuration"></a>Uç nokta yapılandırması

`Endpoint`HTTP.sys dahil olmak üzere WINDOWS http sunucu API 'sini kullanan Web sunucuları için bir yapılandırma gerekir. Windows HTTP sunucu API 'sini kullanan Web sunucularının öncelikle HTTP.sys URL 'lerini (Bu genellikle [netsh](/windows/win32/http/netsh-commands-for-http) aracıyla gerçekleştirilir) ayırmalıdır. 

Bu eylem, hizmetlerinizin varsayılan olarak sahip olmadığı yükseltilmiş ayrıcalıklar gerektirir. ServiceManifest.xml içindeki yapılandırmanın özelliği için "http" veya "https" seçenekleri, `Protocol` `Endpoint` özellikle de Service Fabric çalışma zamanının HTTP.sys ADıNA bir URL 'ye kaydetmesini bildirmek için kullanılır. Bunu, [*güçlü joker karakter*](/windows/win32/http/urlprefix-strings) URL önekini kullanarak yapar.

Örneğin, `http://+:80` bir hizmet için ayırmak üzere ServiceManifest.xml ' de aşağıdaki yapılandırmayı kullanın:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

Ve uç nokta adının oluşturucuya geçirilmesi gerekir `HttpSysCommunicationListener` :

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Statik bağlantı noktası ile HTTP.sys kullanma
HTTP.sys bir statik bağlantı noktası kullanmak için, yapılandırmadaki bağlantı noktası numarasını sağlayın `Endpoint` :

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Dinamik bağlantı noktası ile HTTP.sys kullanma
HTTP.sys ile dinamik olarak atanmış bir bağlantı noktası kullanmak için, `Port` yapılandırmadaki özelliği atlayın `Endpoint` :

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Bir yapılandırma tarafından ayrılan dinamik bir bağlantı noktası `Endpoint` , *ana bilgisayar işlemi başına* yalnızca bir bağlantı noktası sağlar. Geçerli Service Fabric barındırma modeli, birden çok hizmet örneğinin ve/veya çoğaltmaların aynı işlemde barındırılmasına olanak sağlar. Bu, her birinin yapılandırma yoluyla ayrıldığı aynı bağlantı noktasını paylaşacağı anlamına gelir `Endpoint` . Birden çok **HTTP.sys** örneği, temel alınan **HTTP.sys** bağlantı noktası paylaşma özelliğini kullanarak bir bağlantı noktasını paylaşabilir. Ancak `HttpSysCommunicationListener` , istemci istekleri için sunmakta olduğu karmaşıklıklar nedeniyle tarafından desteklenmez. Dinamik bağlantı noktası kullanımı için, Kestrel önerilen Web sunucusudur.

## <a name="kestrel-in-reliable-services"></a>Reliable Services Kestrel
**Microsoft. ServiceFabric. AspNetCore. Kestrel** NuGet paketini içeri aktararak Reliable Services 'de Kestrel kullanabilirsiniz. Bu paket `KestrelCommunicationListener` , uygulamasının bir uygulamasını içerir `ICommunicationListener` . `KestrelCommunicationListener` Web sunucusu olarak Kestrel kullanarak güvenilir bir hizmetin içinde ASP.NET Core WebHost oluşturmanıza olanak sağlar.

Kestrel, ASP.NET Core için platformlar arası Web sunucusudur. HTTP.sys aksine, Kestrel merkezi bir uç nokta Yöneticisi kullanmaz. Ayrıca, HTTP.sys aksine, Kestrel birden çok işlem arasında bağlantı noktası paylaşmayı desteklemez. Her bir Kestrel örneğinin benzersiz bir bağlantı noktası kullanması gerekir. Kestrel hakkında daha fazla bilgi için [uygulama ayrıntılarına](/aspnet/core/fundamentals/servers/kestrel)bakın.

![Kestrel diyagramı][4]

### <a name="kestrel-in-a-stateless-service"></a>Durum bilgisi olmayan bir hizmette Kestrel
`Kestrel`Durum bilgisi olmayan bir hizmette kullanmak için, yöntemini geçersiz kılın `CreateServiceInstanceListeners` ve bir `KestrelCommunicationListener` örnek döndürün:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Durum bilgisi olan bir hizmette Kestrel
`Kestrel`Durum bilgisi olan bir hizmette kullanmak için, yöntemini geçersiz kılın `CreateServiceReplicaListeners` ve bir `KestrelCommunicationListener` örnek döndürün:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

Bu örnekte, tek bir örneği `IReliableStateManager` Webhost bağımlılığı ekleme kapsayıcısına sağlanır. Bu kesinlikle gerekli değildir, ancak `IReliableStateManager` MVC denetleyici eylem yöntemlerinde ve güvenilir koleksiyonları kullanmanıza olanak tanır.

`Endpoint`  `KestrelCommunicationListener` Durum bilgisi olan bir hizmette yapılandırma adı sağlanmaz. Bu, aşağıdaki bölümde daha ayrıntılı olarak açıklanmıştır.

### <a name="configure-kestrel-to-use-https"></a>Kestrel’i HTTPS kullanacak şekilde yapılandırma
Hizmetinize Kestrel ile HTTPS 'yi etkinleştirirken birkaç dinleme seçeneği ayarlamanız gerekir. `ServiceInstanceListener`Bir *EndpointHttps* uç noktası kullanmak ve belirli bir bağlantı noktasını dinlemek için (bağlantı noktası 443) öğesini güncelleştirin. Web konağını Kestrel Web sunucusunu kullanacak şekilde yapılandırırken, Kestrel 'i tüm ağ arabirimlerinde IPv6 adreslerini dinleyecek şekilde yapılandırmanız gerekir: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Öğreticideki tam bir örnek için bkz. [Configure Kestrel to https Use](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Uç nokta yapılandırması
`Endpoint`Kestrel kullanmak için bir yapılandırma gerekli değildir. 

Kestrel basit bir tek başına Web sunucusudur. HTTP.sys (veya HttpListener) aksine, `Endpoint` başlamadan önce URL kaydı gerektirmediğinden ServiceManifest.xml bir yapılandırmaya gerek yoktur. 

#### <a name="use-kestrel-with-a-static-port"></a>Statik bağlantı noktası ile Kestrel kullanma
`Endpoint`Kestrel ile kullanmak üzere ServiceManifest.xml yapılandırmasında statik bir bağlantı noktası yapılandırabilirsiniz. Bu kesinlikle gerekli olmasa da, iki olası avantaj sunar:
 - Bağlantı noktası uygulama bağlantı noktası aralığına denk değilse, Service Fabric tarafından işletim sistemi güvenlik duvarı üzerinden açılır.
 - Size sağlanacak URL `KestrelCommunicationListener` Bu bağlantı noktasını kullanır.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Bir `Endpoint` yapılandırıldıysa, adının oluşturucuya geçirilmesi gerekir `KestrelCommunicationListener` : 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

ServiceManifest.xml bir `Endpoint` yapılandırma kullanmıyorsa, oluşturucuda adı atlayın `KestrelCommunicationListener` . Bu durumda, dinamik bir bağlantı noktası kullanacaktır. Bunun hakkında daha fazla bilgi için sonraki bölüme bakın.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Dinamik bağlantı noktası ile Kestrel kullanma
Kestrel, ServiceManifest.xml içindeki yapılandırmadan otomatik bağlantı noktası atamasını kullanamaz `Endpoint` . Bunun nedeni, bir yapılandırmanın otomatik bağlantı noktası atamasının `Endpoint` *ana bilgisayar işlemi* başına benzersiz bir bağlantı noktası atamasını ve tek bir konak Işleminin birden çok Kestrel örneği içerebilmesi olabilir. Bağlantı noktası paylaşımını desteklemediğinden bu, Kestrel ile çalışmaz. Bu nedenle, her Kestrel örneğinin benzersiz bir bağlantı noktasında açılması gerekir.

Dinamik bağlantı noktası atamasını Kestrel ile kullanmak için, `Endpoint` ServiceManifest.xml tamamen ' de yapılandırmayı atlayın ve bir uç nokta adını `KestrelCommunicationListener` oluşturucuya aşağıdaki gibi geçirin:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Bu yapılandırmada, `KestrelCommunicationListener` uygulama bağlantı noktası aralığından kullanılmamış bir bağlantı noktasını otomatik olarak seçer.

HTTPS için, bir uç noktanın, ServiceManifest.xml ' de belirtilen bir bağlantı noktası olmadan HTTPS protokolü ile yapılandırılması ve uç nokta adını KestrelCommunicationListener oluşturucusuna geçirmesi gerekir.


## <a name="service-fabric-configuration-provider"></a>Service Fabric yapılandırma sağlayıcısı
ASP.NET Core içindeki uygulama yapılandırması, yapılandırma sağlayıcısı tarafından belirlenen anahtar-değer çiftlerini temel alır. Genel ASP.NET Core yapılandırma desteği hakkında daha fazla bilgi edinmek için [ASP.NET Core yapılandırma](/aspnet/core/fundamentals/configuration/) makalesini okuyun.

Bu bölümde, Service Fabric yapılandırma sağlayıcısının NuGet paketini içeri aktararak ASP.NET Core yapılandırma ile nasıl tümleştirildiğini açıklanmaktadır `Microsoft.ServiceFabric.AspNetCore.Configuration` .

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration başlangıç uzantıları
NuGet paketini içe aktardıktan sonra `Microsoft.ServiceFabric.AspNetCore.Configuration` , Service Fabric yapılandırma kaynağını ASP.NET Core Configuration API 'sine kaydetmeniz gerekir. Bunu, ad alanındaki **Addservicefabricconfiguration** uzantılarını denetleyerek yapabilirsiniz `Microsoft.ServiceFabric.AspNetCore.Configuration` `IConfigurationBuilder` .

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Artık ASP.NET Core hizmeti, diğer uygulama ayarları gibi Service Fabric yapılandırma ayarlarına erişebilir. Örneğin, ayarları türü kesin belirlenmiş nesnelere yüklemek için Seçenekler ' i kullanabilirsiniz.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Varsayılan anahtar eşleme
Varsayılan olarak, Service Fabric yapılandırma sağlayıcısı paket adı, Bölüm adı ve özellik adını içerir. Birlikte, bu ASP.NET Core yapılandırma anahtarını aşağıdaki şekilde oluşturur:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Örneğin, aşağıdaki içerikle adlı bir yapılandırma paketiniz varsa `MyConfigPackage` , yapılandırma değeri `IConfiguration` *myconfigpackage: MyConfigSection: MyParameter* aracılığıyla ASP.NET Core kullanılabilir olacaktır.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric yapılandırma seçenekleri
Service Fabric yapılandırma sağlayıcısı, `ServiceFabricConfigurationOptions` anahtar eşlemesinin varsayılan davranışını değiştirmek için de destekler.

#### <a name="encrypted-settings"></a>Şifrelenmiş ayarlar
Service Fabric, Service Fabric yapılandırma sağlayıcısı gibi şifreli ayarları destekler. Şifrelenmiş ayarların şifresi `IConfiguration` Varsayılan olarak ASP.NET Core. Şifrelenmiş değerler burada depolanır. Ancak ASP.NET Core Iconation 'da depolanacak değerin şifresini çözmek istiyorsanız, aşağıdaki gibi, *DecryptValue* bayrağını uzantısında false olarak ayarlayabilirsiniz `AddServiceFabricConfiguration` :

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Birden çok yapılandırma paketi
Service Fabric birden çok yapılandırma paketini destekler. Varsayılan olarak, paket adı yapılandırma anahtarına dahil edilir. Ancak `IncludePackageName` aşağıdaki gibi bayrağını false olarak ayarlayabilirsiniz:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Özel anahtar eşleme, değer ayıklama ve veri popülasyonu
Service Fabric yapılandırma sağlayıcısı, anahtar eşlemesini özelleştirmek için daha gelişmiş senaryolar da destekler `ExtractKeyFunc` ve değerleri ile özel olarak ayıklar `ExtractValueFunc` . Kullanarak verileri Service Fabric yapılandırmadan ASP.NET Core yapılandırmasına doldurma işlemini de değiştirebilirsiniz `ConfigAction` .

Aşağıdaki örneklerde, `ConfigAction` veri popülasyonu özelleştirmek için nasıl kullanılacağı gösterilmektedir:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Yapılandırma güncelleştirmeleri
Service Fabric yapılandırma sağlayıcısı da yapılandırma güncelleştirmelerini destekler. `IOptionsMonitor`Değişiklik bildirimlerini almak için ASP.NET Core kullanabilir ve sonra `IOptionsSnapshot` yapılandırma verilerini yeniden yüklemek için kullanabilirsiniz. Daha fazla bilgi için bkz. [ASP.NET Core seçenekleri](/aspnet/core/fundamentals/configuration/options).

Bu seçenekler varsayılan olarak desteklenir. Yapılandırma güncelleştirmelerini etkinleştirmek için başka bir kodlama gerekmez.

## <a name="scenarios-and-configurations"></a>Senaryolar ve Konfigürasyonlar
Bu bölümde, aşağıdaki senaryolarda sorun gidermek için önerdiğimiz Web sunucusu, bağlantı noktası yapılandırması, Service Fabric tümleştirme seçenekleri ve çeşitli ayarların birleşimi verilmiştir:
 - Dışarıdan sunulan ASP.NET Core durum bilgisi olmayan hizmetler
 - Yalnızca iç durum bilgisi olmayan ASP.NET Core Hizmetleri
 - Yalnızca iç durum bilgisi olan ASP.NET Core Hizmetleri

**Dışarıdan sunulan bir hizmet** , genellikle yük dengeleyici aracılığıyla küme dışından çağrılan bir uç noktayı kullanıma sunar.

**Yalnızca dahili** bir hizmet, uç noktası yalnızca küme içinden çağrılan bir hizmettir.

> [!NOTE]
> Durum bilgisi olan hizmet uç noktaları genellikle internet 'e sunulmamalıdır. Azure Load Balancer gibi Service Fabric hizmet çözümünden haberdar olmayan yük dengeleyiciler arkasındaki kümeler, durum bilgisi olmayan hizmetleri kullanıma sunmayacak. Bunun nedeni, yük dengeleyicinin trafiği uygun durum bilgisi olan hizmet çoğaltmasına bulabilmeleri ve yönlendirmeyeceği bir yoldur. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Dışarıdan sunulan ASP.NET Core durum bilgisi olmayan hizmetler
Kestrel, dış, internet 'e yönelik HTTP uç noktalarını kullanıma sunan ön uç hizmetleri için önerilen Web sunucusudur. Windows 'da HTTP.sys, aynı bağlantı noktasını kullanarak aynı düğüm kümesinde birden çok Web hizmeti barındırmanıza olanak tanıyan bağlantı noktası paylaşma özelliği sağlayabilir. Bu senaryoda, Web Hizmetleri, HTTP yönlendirmesi sağlamak üzere ön uç ara sunucusuna veya ağ geçidine bağlı olmadan ana bilgisayar adına veya yoluna göre farklılaştırılabilir.
 
İnternet 'e sunulduğunda, durum bilgisi olmayan bir hizmet, yük dengeleyici aracılığıyla erişilebilen iyi bilinen ve kararlı bir uç nokta kullanmalıdır. Bu URL 'YI uygulamanızın kullanıcılarına sağlarsınız. Aşağıdaki yapılandırmayı öneririz:

| Tür | Öneri | Notlar |
| ---- | -------------- | ----- |
| Web sunucusu | Kestrel | Kestrel, Windows ve Linux genelinde desteklendiğinden tercih edilen Web sunucusudur. |
| Bağlantı noktası yapılandırması | static | ServiceManifest.xml yapılandırmasında iyi bilinen bir statik bağlantı noktası `Endpoints` , https için 80, http veya 443 için gibi yapılandırılmalıdır. |
| Servicefabricıntegrationoptions | Yok | `ServiceFabricIntegrationOptions.None`Hizmetin, benzersiz bir tanımlayıcı için gelen istekleri doğrulamaya kalkışmasını sağlamak üzere Service Fabric tümleştirme ara yazılımını yapılandırırken bu seçeneği kullanın. Uygulamanızın dış kullanıcıları, ara yazılım tarafından kullanılan benzersiz tanımlama bilgilerini bilmez. |
| Örnek Sayısı | -1 | Tipik kullanım durumlarında, örnek sayısı ayarı *-1* olarak ayarlanmalıdır. Bu, bir yük dengeleyiciden trafik alan tüm düğümlerde bir örnek kullanılabilmesi için yapılır. |

Birden fazla dışarıdan sunulan hizmet aynı düğüm kümesini paylaşıyorsa, benzersiz ancak kararlı bir URL yoluyla HTTP.sys kullanabilirsiniz. Bunu, ıwebhost yapılandırılırken belirtilen URL 'YI değiştirerek gerçekleştirebilirsiniz. Bunun yalnızca HTTP.sys için geçerli olduğunu unutmayın.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Yalnızca iç durum bilgisi olmayan ASP.NET Core hizmeti
Yalnızca küme içinden çağrılan durum bilgisi olmayan hizmetler, birden çok hizmet arasında ortak işlem sağlamak için benzersiz URL 'Ler ve dinamik olarak atanmış bağlantı noktaları kullanmalıdır. Aşağıdaki yapılandırmayı öneririz:

| Tür | Öneri | Notlar |
| ---- | -------------- | ----- |
| Web sunucusu | Kestrel | Durum bilgisi olmayan hizmetler için HTTP.sys kullanabilseniz de, Kestrel birden çok hizmet örneğinin bir konağı paylaşmasına izin veren en iyi sunucu olur.  |
| Bağlantı noktası yapılandırması | dinamik olarak atandı | Durum bilgisi olan bir hizmetin birden fazla çoğaltması bir konak işlemini veya ana bilgisayar işletim sistemini paylaşabilir ve bu nedenle benzersiz bağlantı noktalarına gerek duyar. |
| Servicefabricıntegrationoptions | UseUniqueServiceUrl 'Si | Dinamik bağlantı noktası atama ile bu ayar, daha önce açıklanan kimlik sorununa engel olur. |
| Yükteki InstanceCount | herhangi biri | Örnek sayısı ayarı, hizmeti çalıştırmak için gereken herhangi bir değere ayarlanabilir. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Yalnızca iç durum bilgisi olan ASP.NET Core hizmeti
Yalnızca küme içinden çağrılan durum bilgisi olan hizmetler, birden çok hizmet arasında ortak işlem sağlamak için dinamik olarak atanmış bağlantı noktaları kullanmalıdır. Aşağıdaki yapılandırmayı öneririz:

| Tür | Öneri | Notlar |
| ---- | -------------- | ----- |
| Web sunucusu | Kestrel | , `HttpSysCommunicationListener` Çoğaltmaların bir konak işlemini paylaştığı durum bilgisi olmayan hizmetler tarafından kullanılmak üzere tasarlanmamıştır. |
| Bağlantı noktası yapılandırması | dinamik olarak atandı | Durum bilgisi olan bir hizmetin birden fazla çoğaltması bir konak işlemini veya ana bilgisayar işletim sistemini paylaşabilir ve bu nedenle benzersiz bağlantı noktalarına gerek duyar. |
| Servicefabricıntegrationoptions | UseUniqueServiceUrl 'Si | Dinamik bağlantı noktası atama ile bu ayar, daha önce açıklanan kimlik sorununa engel olur. |

## <a name="next-steps"></a>Sonraki adımlar
[Visual Studio'yu kullanarak Service Fabric uygulamanızda hata ayıklama](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
