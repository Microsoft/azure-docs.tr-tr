---
title: ASP.NET Core hizmet iletişimi | Microsoft Docs
description: ASP.NET Core durum bilgisiz ve durum bilgisi olan Reliable Services nasıl kullanacağınızı öğrenin.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: b2a1b1426af3e72756a7a85a173ef4a2a5671b02
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900204"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>Azure Service Fabric ASP.NET Core Reliable Services

ASP.NET Core, açık kaynaklı ve platformlar arası bir çerçevedir. Bu çerçeve, Web uygulamaları, IoT uygulamaları ve mobil arka uçlar gibi bulut tabanlı, internet 'e bağlı uygulamalar oluşturmak için tasarlanmıştır.

Bu makale, **Microsoft. ServiceFabric. AspNetCore** kullanarak Service Fabric Reliable Services ASP.NET Core hizmetlerinin barındırılmasına yönelik ayrıntılı bir kılavuzdur. NuGet paketleri kümesi.

Service Fabric ASP.NET Core giriş Öğreticisi ve geliştirme ortamınızı ayarlama hakkında yönergeler için bkz. [öğretici: bir uygulamayı ASP.NET Core Web API ön uç hizmeti ve durum bilgisi olan bir arka uç hizmeti Ile oluşturma ve dağıtma](service-fabric-tutorial-create-dotnet-app.md).

Bu makalenin geri kalanında ASP.NET Core zaten bildiğiniz varsayılmaktadır. Aksi takdirde, lütfen [ASP.NET Core temellerini](https://docs.microsoft.com/aspnet/core/fundamentals/index)okuyun.

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric ortamında ASP.NET Core

Hem ASP.NET Core hem de Service Fabric uygulamalar .NET Core veya Full .NET Framework üzerinde çalışabilir. ASP.NET Core, Service Fabric iki farklı şekilde kullanabilirsiniz:
 - **Konuk yürütülebilir dosyası olarak barındırılır**. Bu şekilde birincil olarak, mevcut ASP.NET Core uygulamalarını kod değişikliği olmadan Service Fabric çalıştırmak için kullanılır.
 - **Güvenilir bir hizmetin Içinde çalıştırın**. Bu şekilde Service Fabric çalışma zamanına göre daha iyi tümleştirme sağlar ve durum bilgisi olan ASP.NET Core hizmetlerine izin verir.

Bu makalenin geri kalanında, Service Fabric SDK ile birlikte gelen ASP.NET Core tümleştirme bileşenleri aracılığıyla güvenilir bir hizmetin içinde ASP.NET Core nasıl kullanılacağı açıklanmaktadır.

## <a name="service-fabric-service-hosting"></a>Hizmet barındırma Service Fabric

Service Fabric, hizmet *ana bilgisayar işleminde*çalıştırdığınız bir veya daha fazla örnek ve/veya çoğaltma: hizmet kodunuzu çalıştıran yürütülebilir bir dosya. Hizmet yazarı olarak, hizmet ana bilgisayar işleminin sahibi olur ve Service Fabric, sizin için etkinleştirir ve izler.

Geleneksel ASP.NET (MVC 5 ' e kadar), System. Web. dll aracılığıyla IIS 'e sıkı bir şekilde bağlanmış. ASP.NET Core, Web sunucusu ile Web uygulamanız arasında bir ayrım sağlar. Bu ayrım, Web uygulamalarının farklı Web sunucuları arasında taşınabilir olmasını sağlar. Ayrıca, Web sunucularının *kendiliğinden barındırılmasına*izin verir. Bu, IIS gibi adanmış web sunucusu yazılımına ait olan bir işlemin aksine bir Web sunucusunu kendi işleminizde başlatabilmeniz anlamına gelir.

Bir Service Fabric hizmetini ve ASP.NET bir konuk yürütülebilir dosyası ya da güvenilir bir hizmette birleştirmek için, hizmet ana bilgisayar sürecinizin içinde ASP.NET 'i başlatabilmelisiniz. ASP.NET Core kendi kendine barındırma bunu yapmanıza olanak sağlar.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Güvenilir bir hizmette ASP.NET Core barındırma
Genellikle, şirket içinde barındırılan ASP.NET Core Uygulamaları, `Program.cs` ' deki `static void Main()` yöntemi gibi bir uygulamanın giriş noktasında bir WebHost oluşturur. Bu durumda, WebHost 'in yaşam döngüsü işlemin yaşam döngüsüne bağlanır.

![İşlem içinde ASP.NET Core barındırma][0]

Ancak uygulama giriş noktası, güvenilir bir hizmette bir WebHost oluşturmak için doğru yer değildir. Bunun nedeni, uygulama giriş noktasının yalnızca Service Fabric çalışma zamanına sahip bir hizmet türünü kaydetmek için kullanıldığı için kullanılır, böylece bu hizmet türünün örneklerini oluşturabilir. WebHost 'in güvenilir bir hizmetin kendisinde oluşturulması gerekir. Hizmet ana bilgisayarı işlemi içinde, hizmet örnekleri ve/veya çoğaltmalar birden çok yaşam döngüsü üzerinden geçebilir. 

Güvenilir bir hizmet örneği, `StatelessService` veya `StatefulService` ' den türetilen hizmet sınıfınız tarafından temsil edilir. Hizmetin iletişim yığını, hizmet sınıfınıza `ICommunicationListener` uygulamasında bulunur. `Microsoft.ServiceFabric.AspNetCore.*` NuGet paketleri, güvenilir bir hizmette Kestrel ya da HTTP. sys için ASP.NET Core WebHost 'yi başlatan ve yöneten `ICommunicationListener` uygulamalarını içerir.

![Güvenilir bir hizmette ASP.NET Core barındırma diyagramı][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Idimmunicationlisteners ASP.NET Core
`Microsoft.ServiceFabric.AspNetCore.*` NuGet paketlerindeki Kestrel ve HTTP. sys `ICommunicationListener` uygulamaları benzer kullanım desenlerine sahiptir. Ancak her bir Web sunucusuna özel biraz farklı eylemler gerçekleştirir. 

Her iki iletişim dinleyicisi de aşağıdaki bağımsız değişkenleri alan bir oluşturucu sağlar:
 - **`ServiceContext serviceContext`** : Bu, çalışan hizmet hakkında bilgi içeren `ServiceContext` nesnedir.
 - **`string endpointName`** : Bu, servicemanifest. xml dosyasında bir `Endpoint` yapılandırmasının adıdır. Bu, birincil olarak iki iletişim dinleyicilerinin farklı olduğu yerdir. HTTP. sys `Endpoint` yapılandırması *gerektirir* , Kestrel değildir.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : Bu bir lambda olan ve bir `IWebHost` öğesini oluşturduğunuz ve döndüren bir lambda. `IWebHost`, normalde ASP.NET Core uygulamada yaptığınız şekilde yapılandırmanıza olanak tanır. Lambda, kullandığınız Service Fabric tümleştirme seçeneklerine ve sağladığınız `Endpoint` yapılandırmasına bağlı olarak sizin için oluşturulmuş bir URL sağlar. Daha sonra, Web sunucusunu başlatmak için bu URL 'YI değiştirebilir veya kullanabilirsiniz.

## <a name="service-fabric-integration-middleware"></a>Service Fabric tümleştirme ara yazılımı
`Microsoft.ServiceFabric.AspNetCore` NuGet paketi, Service Fabric ile uyumlu ara yazılım ekleyen `IWebHostBuilder` `UseServiceFabricIntegration` uzantısı yöntemini içerir. Bu ara yazılım, Service Fabric Adlandırma Hizmeti benzersiz bir hizmet URL 'sini kaydetmek için Kestrel veya HTTP. sys `ICommunicationListener` yapılandırır. Daha sonra istemcilerin doğru hizmete bağlanmasını sağlamak için istemci isteklerini doğrular. 

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

Güvenilen bir ortamda, `UseServiceFabricIntegration` yöntemiyle eklenen ara yazılım, Adlandırma Hizmeti gönderilen adrese otomatik olarak benzersiz bir tanımlayıcı ekler. Her istekte bu tanımlayıcıyı doğrular. Tanımlayıcı eşleşmezse, ara yazılım hemen bir HTTP 410 gitti yanıtı döndürür.

Dinamik olarak atanmış bir bağlantı noktası kullanan hizmetlerin bu ara yazılımı kullanması gerekir.

Sabit benzersiz bir bağlantı noktası kullanan hizmetlerde bu sorun, birlikte çalışmayan bir ortamda yoktur. Genellikle, istemci uygulamalarının bağlanması için iyi bilinen bir bağlantı noktasına ihtiyaç duyulan dışarıdan yönelik hizmetler için sabit benzersiz bir bağlantı noktası kullanılır. Örneğin, Internet 'e yönelik çoğu Web uygulaması, Web tarayıcısı bağlantıları için 80 veya 443 numaralı bağlantı noktasını kullanır. Bu durumda, benzersiz tanımlayıcı etkinleştirilmemelidir.

Aşağıdaki diyagramda, ara yazılım etkinleştirilmiş olan istek akışı gösterilmektedir:

![Service Fabric ASP.NET Core tümleştirmesi][2]

Hem Kestrel hem de HTTP. sys `ICommunicationListener` uygulamaları bu mekanizmayı tamamen aynı şekilde kullanır. HTTP. sys, temel **http. sys** bağlantı noktası paylaşma özelliğini kullanarak ISTEKLERI benzersiz URL yollarına göre dahili olarak ayırt edebilse de, bu işlev http. sys `ICommunicationListener` *uygulamasında kullanılmaz.* Bunun nedeni, daha önce açıklanan senaryoda HTTP 503 ve HTTP 404 hata durumu kodlarına neden olur. Bu durumda, HTTP 503 ve HTTP 404 yaygın olarak diğer hataları göstermek için kullanıldığından, istemcilerin hatanın amacını belirlemesini zorlaştırır. 

Bu nedenle, hem Kestrel hem de HTTP. sys `ICommunicationListener` uygulamaları, `UseServiceFabricIntegration` genişletme yöntemi tarafından sunulan ara yazılımlar üzerinde standartlaştırın. Bu nedenle, istemcilerin HTTP 410 yanıtları üzerinde yalnızca bir hizmet uç noktası yeniden çözümlemesi yapması gerekir.

## <a name="httpsys-in-reliable-services"></a>Reliable Services 'de HTTP. sys
**Microsoft. ServiceFabric. AspNetCore. HttpSys** NuGet paketini içeri aktararak RELIABLE SERVICES içindeki http. sys ' i kullanabilirsiniz. Bu paket, `ICommunicationListener` ' in bir uygulamasını `HttpSysCommunicationListener` içerir. `HttpSysCommunicationListener`, Web sunucusu olarak HTTP. sys kullanarak güvenilir bir hizmetin içinde bir ASP.NET Core WebHost oluşturmanıza olanak sağlar.

HTTP. sys, [WINDOWS http sunucu API 'si](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx)üzerine kurulmuştur. Bu API, HTTP isteklerini işlemek ve bunları Web uygulamaları çalıştıran işlemlere yönlendirmek için **http. sys** çekirdek sürücüsünü kullanır. Bu, aynı fiziksel veya sanal makinedeki birden çok işlemin aynı bağlantı noktasında Web uygulamalarını barındırarak benzersiz bir URL yolu veya ana bilgisayar adıyla ayırt etmesine olanak tanır. Bu özellikler, aynı kümede birden çok Web sitesini barındırmak için Service Fabric yararlıdır.

>[!NOTE]
>HTTP. sys uygulamasının yalnızca Windows platformunda çalışması.

Aşağıdaki diyagramda, HTTP. sys ' nin bağlantı noktası paylaşımı için Windows 'ta **http. sys** çekirdek sürücüsünü nasıl kullandığı gösterilmektedir:

![HTTP. sys diyagramı][3]

### <a name="httpsys-in-a-stateless-service"></a>Durum bilgisi olmayan bir hizmette HTTP. sys
Durum bilgisi olmayan bir hizmette `HttpSys` ' ı kullanmak için, `CreateServiceInstanceListeners` yöntemini geçersiz kılın ve bir `HttpSysCommunicationListener` örneği döndürün:

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

### <a name="httpsys-in-a-stateful-service"></a>Durum bilgisi olan bir hizmette HTTP. sys

`HttpSysCommunicationListener`, temel alınan **http. sys** bağlantı noktası paylaşma özelliği ile ilgili zorluklar nedeniyle şu anda durum bilgisi olan hizmetlerde kullanılmak üzere tasarlanmamıştır. Daha fazla bilgi için, HTTP. sys ile dinamik bağlantı noktası ayırma üzerinde aşağıdaki bölüme bakın. Durum bilgisi olan hizmetler için, Kestrel önerilen Web sunucusudur.

### <a name="endpoint-configuration"></a>Uç nokta yapılandırması

HTTP. sys dahil olmak üzere Windows HTTP sunucu API 'sini kullanan Web sunucuları için `Endpoint` yapılandırması gerekir. Windows HTTP sunucu API 'sini kullanan Web sunucularının öncelikle URL 'lerini HTTP. sys ile ayrılması gerekir (Bu, normalde [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) aracıyla gerçekleştirilir). 

Bu eylem, hizmetlerinizin varsayılan olarak sahip olmadığı yükseltilmiş ayrıcalıklar gerektirir. ServiceManifest. xml dosyasındaki `Endpoint` yapılandırmasının `Protocol` özelliği için "http" veya "https" seçenekleri, özellikle Service Fabric çalışma zamanına, sizin adınıza HTTP. sys ile bir URL kaydetmeye yönlendirmek için kullanılır. Bunu, [*güçlü joker karakter*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL önekini kullanarak yapar.

Örneğin, bir hizmet için `http://+:80` ayırmak için ServiceManifest. xml dosyasında aşağıdaki yapılandırmayı kullanın:

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

Ve uç nokta adının `HttpSysCommunicationListener` oluşturucusuna geçirilmesi gerekir:

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

#### <a name="use-httpsys-with-a-static-port"></a>HTTP. sys ' i statik bağlantı noktasıyla kullanma
HTTP. sys ile bir statik bağlantı noktası kullanmak için `Endpoint` yapılandırmasında bağlantı noktası numarasını girin:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>HTTP. sys dosyasını dinamik bir bağlantı noktasıyla kullanma
HTTP. sys ile dinamik olarak atanmış bir bağlantı noktası kullanmak için, `Endpoint` yapılandırmasındaki `Port` özelliğini atlayın:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Bir `Endpoint` yapılandırması tarafından ayrılan dinamik bir bağlantı noktası, *ana bilgisayar işlemi başına*yalnızca bir bağlantı noktası sağlar. Geçerli Service Fabric barındırma modeli, birden çok hizmet örneğinin ve/veya çoğaltmaların aynı işlemde barındırılmasına olanak sağlar. Bu, her birinin `Endpoint` yapılandırması aracılığıyla ayrıldığı aynı bağlantı noktasını paylaşacağı anlamına gelir. Birden çok **http. sys** örneği, temel alınan **http. sys** bağlantı noktası paylaşma özelliğini kullanarak bir bağlantı noktası paylaşabilir. Ancak istemci istekleri için tanıtan karmaşıklıklar nedeniyle `HttpSysCommunicationListener` tarafından desteklenmez. Dinamik bağlantı noktası kullanımı için, Kestrel önerilen Web sunucusudur.

## <a name="kestrel-in-reliable-services"></a>Reliable Services Kestrel
**Microsoft. ServiceFabric. AspNetCore. Kestrel** NuGet paketini içeri aktararak Reliable Services 'de Kestrel kullanabilirsiniz. Bu paket, `ICommunicationListener` ' in bir uygulamasını `KestrelCommunicationListener` içerir. `KestrelCommunicationListener`, Web sunucusu olarak Kestrel kullanarak güvenilir bir hizmetin içinde bir ASP.NET Core WebHost oluşturmanıza izin verir.

Kestrel, ASP.NET Core için platformlar arası Web sunucusudur. HTTP. sys ' nin aksine, Kestrel merkezi bir uç nokta Yöneticisi kullanmaz. Ayrıca, HTTP. sys ' nin aksine, Kestrel birden çok işlem arasında bağlantı noktası paylaşımını desteklemez. Her bir Kestrel örneğinin benzersiz bir bağlantı noktası kullanması gerekir. Kestrel hakkında daha fazla bilgi için [uygulama ayrıntılarına](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2)bakın.

![Kestrel diyagramı][4]

### <a name="kestrel-in-a-stateless-service"></a>Durum bilgisi olmayan bir hizmette Kestrel
Durum bilgisi olmayan bir hizmette `Kestrel` ' ı kullanmak için, `CreateServiceInstanceListeners` yöntemini geçersiz kılın ve bir `KestrelCommunicationListener` örneği döndürün:

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
Durum bilgisi olan bir hizmette `Kestrel` ' ı kullanmak için, `CreateServiceReplicaListeners` yöntemini geçersiz kılın ve bir `KestrelCommunicationListener` örneği döndürün:

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

Bu örnekte, WebHost bağımlılığı ekleme kapsayıcısına `IReliableStateManager` ' ın tek bir örneği verilmiştir. Bu kesinlikle gerekli değildir, ancak MVC denetleyici eylem yöntemlerinde `IReliableStateManager` ve güvenilir koleksiyonlar kullanmanıza izin verir.

Bir `Endpoint` yapılandırma adı, durum bilgisi olan bir hizmette `KestrelCommunicationListener` ' *ye sağlanmaz.* Bu, aşağıdaki bölümde daha ayrıntılı olarak açıklanmıştır.

### <a name="configure-kestrel-to-use-https"></a>Kestrel’i HTTPS kullanacak şekilde yapılandırma
Hizmetinize Kestrel ile HTTPS 'yi etkinleştirirken birkaç dinleme seçeneği ayarlamanız gerekir. *EndpointHttps* uç noktası kullanmak ve belirli bir bağlantı noktasını dinlemek için `ServiceInstanceListener` ' i güncelleştirin (bağlantı noktası 443). Web konağını Kestrel Web sunucusunu kullanacak şekilde yapılandırırken, Kestrel 'i tüm ağ arabirimlerinde IPv6 adreslerini dinleyecek şekilde yapılandırmanız gerekir: 

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
Kestrel kullanmak için `Endpoint` yapılandırması gerekli değildir. 

Kestrel basit bir tek başına Web sunucusudur. HTTP. sys (veya HttpListener) aksine, başlamadan önce URL kaydı gerektirmediğinden, ServiceManifest. xml dosyasında `Endpoint` yapılandırmasına gerek yoktur. 

#### <a name="use-kestrel-with-a-static-port"></a>Statik bağlantı noktası ile Kestrel kullanma
Kestrel ile kullanmak üzere ServiceManifest. xml ' in `Endpoint` yapılandırmasında statik bir bağlantı noktası yapılandırabilirsiniz. Bu kesinlikle gerekli olmasa da, iki olası avantaj sunar:
 - Bağlantı noktası uygulama bağlantı noktası aralığına denk değilse, Service Fabric tarafından işletim sistemi güvenlik duvarı üzerinden açılır.
 - `KestrelCommunicationListener` üzerinden size girilen URL Bu bağlantı noktasını kullanacaktır.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

`Endpoint` yapılandırılmışsa, adının `KestrelCommunicationListener` oluşturucusuna geçirilmesi gerekir: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

ServiceManifest. xml `Endpoint` yapılandırması kullanmıyorsa, `KestrelCommunicationListener` yapıcısında adı atlayın. Bu durumda, dinamik bir bağlantı noktası kullanacaktır. Bunun hakkında daha fazla bilgi için sonraki bölüme bakın.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Dinamik bağlantı noktası ile Kestrel kullanma
Kestrel, ServiceManifest. xml dosyasındaki `Endpoint` yapılandırmasından otomatik bağlantı noktası atamasını kullanamaz. Bunun nedeni, `Endpoint` yapılandırmasından otomatik bağlantı noktası atamasının *ana bilgisayar işlemi*başına benzersiz bir bağlantı noktası atamasını ve tek bir ana bilgisayar işleminin birden çok Kestrel örneği içerebilmesi. Bağlantı noktası paylaşımını desteklemediğinden bu, Kestrel ile çalışmaz. Bu nedenle, her Kestrel örneğinin benzersiz bir bağlantı noktasında açılması gerekir.

Kestrel ile dinamik bağlantı noktası atamasını kullanmak için, ServiceManifest. xml dosyasında tamamen `Endpoint` yapılandırmasını atlayın ve `KestrelCommunicationListener` oluşturucusuna bir uç nokta adı şu şekilde geçirmeyin:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Bu yapılandırmada `KestrelCommunicationListener`, uygulama bağlantı noktası aralığından kullanılmamış bir bağlantı noktasını otomatik olarak seçer.

HTTPS için, ServiceManifest. xml dosyasında belirtilen bir bağlantı noktası olmadan HTTPS protokolü ile yapılandırılmış uç nokta olmalıdır ve uç nokta adını KestrelCommunicationListener oluşturucusuna geçirin.


## <a name="service-fabric-configuration-provider"></a>Service Fabric yapılandırma sağlayıcısı
ASP.NET Core içindeki uygulama yapılandırması, yapılandırma sağlayıcısı tarafından belirlenen anahtar-değer çiftlerini temel alır. Genel ASP.NET Core yapılandırma desteği hakkında daha fazla bilgi edinmek için [ASP.NET Core yapılandırma](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) makalesini okuyun.

Bu bölümde, Service Fabric yapılandırma sağlayıcısının `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet paketini içeri aktararak ASP.NET Core yapılandırma ile nasıl tümleştirildiğini açıklar.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration başlangıç uzantıları
`Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet paketini içeri aktardıktan sonra, Service Fabric yapılandırma kaynağını ASP.NET Core Configuration API 'sine kaydetmeniz gerekir. Bunu, `Microsoft.ServiceFabric.AspNetCore.Configuration` ad alanındaki **Addservicefabricconfiguration** uzantılarını `IConfigurationBuilder` ' a karşı denetleyerek yapabilirsiniz.

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

Örneğin, aşağıdaki içeriğe sahip `MyConfigPackage` adlı bir yapılandırma paketiniz varsa, yapılandırma değeri `IConfiguration` ASP.NET Core ' den *Myconfigpackage: MyConfigSection: MyParameter*aracılığıyla kullanılabilir olacaktır.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric yapılandırma seçenekleri
Service Fabric yapılandırma sağlayıcısı, anahtar eşlemesinin varsayılan davranışını değiştirmek için `ServiceFabricConfigurationOptions` ' ı de destekler.

#### <a name="encrypted-settings"></a>Şifrelenmiş ayarlar
Service Fabric, Service Fabric yapılandırma sağlayıcısı gibi şifreli ayarları destekler. Şifrelenmiş ayarların şifresi varsayılan olarak, ASP.NET Core `IConfiguration` ' a çözülür. Şifrelenmiş değerler burada depolanır. Ancak ASP.NET Core Iconation 'da depolanacak değerin şifresini çözmek istiyorsanız, `AddServiceFabricConfiguration` uzantısında şu şekilde *DecryptValue* bayrağını false olarak ayarlayabilirsiniz:

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
Service Fabric birden çok yapılandırma paketini destekler. Varsayılan olarak, paket adı yapılandırma anahtarına dahil edilir. Ancak `IncludePackageName` bayrağını aşağıdaki gibi false olarak ayarlayabilirsiniz:
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
Service Fabric yapılandırma sağlayıcısı Ayrıca anahtar eşlemesini `ExtractKeyFunc` ile özelleştirmek için daha gelişmiş senaryoları destekler ve değerleri `ExtractValueFunc` ile özel olarak ayıklayın. Hatta `ConfigAction` kullanarak Service Fabric yapılandırmasından ASP.NET Core yapılandırmaya veri doldurma sürecini de değiştirebilirsiniz.

Aşağıdaki örneklerde, veri popülasyonu özelleştirmek için `ConfigAction` kullanımı gösterilmektedir:
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
Service Fabric yapılandırma sağlayıcısı da yapılandırma güncelleştirmelerini destekler. Değişiklik bildirimlerini almak için ASP.NET Core `IOptionsMonitor` ' ı kullanabilir ve sonra yapılandırma verilerini yeniden yüklemek için `IOptionsSnapshot` ' i kullanabilirsiniz. Daha fazla bilgi için bkz. [ASP.NET Core seçenekleri](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

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
Kestrel, dış, internet 'e yönelik HTTP uç noktalarını kullanıma sunan ön uç hizmetleri için önerilen Web sunucusudur. Windows 'da, HTTP. sys, aynı bağlantı noktasını kullanarak aynı düğüm kümesinde birden çok Web hizmeti barındırmanıza olanak sağlayan bağlantı noktası paylaşma özelliği sağlayabilir. Bu senaryoda, Web Hizmetleri, HTTP yönlendirmesi sağlamak üzere ön uç ara sunucusuna veya ağ geçidine bağlı olmadan ana bilgisayar adına veya yoluna göre farklılaştırılabilir.
 
İnternet 'e sunulduğunda, durum bilgisi olmayan bir hizmet, yük dengeleyici aracılığıyla erişilebilen iyi bilinen ve kararlı bir uç nokta kullanmalıdır. Bu URL 'YI uygulamanızın kullanıcılarına sağlarsınız. Aşağıdaki yapılandırmayı öneririz:

|  |  | **Notlar** |
| --- | --- | --- |
| Web sunucusu | Kestrel | Kestrel, Windows ve Linux genelinde desteklendiğinden tercih edilen Web sunucusudur. |
| Bağlantı noktası yapılandırması | Se | ServiceManifest. xml ' in `Endpoints` yapılandırması, örneğin HTTP için 80 veya HTTPS için 443 gibi iyi bilinen bir statik bağlantı noktası yapılandırılmalıdır. |
| Servicefabricıntegrationoptions | Hiçbiri | Service Fabric tümleştirme ara yazılımını yapılandırırken hizmetin, benzersiz bir tanımlayıcı için gelen istekleri doğrulamaya kalkışmaması için `ServiceFabricIntegrationOptions.None` seçeneğini kullanın. Uygulamanızın dış kullanıcıları, ara yazılım tarafından kullanılan benzersiz tanımlama bilgilerini bilmez. |
| Örnek Sayısı | -1 | Tipik kullanım durumlarında, örnek sayısı ayarı *-1*olarak ayarlanmalıdır. Bu, bir yük dengeleyiciden trafik alan tüm düğümlerde bir örnek kullanılabilmesi için yapılır. |

Birden fazla dışarıdan açığa çıkarılan hizmet aynı düğüm kümesini paylaşıyorsa, HTTP. sys ' yi benzersiz ancak kararlı bir URL yolu ile kullanabilirsiniz. Bunu, ıwebhost yapılandırılırken belirtilen URL 'YI değiştirerek gerçekleştirebilirsiniz. Bunun yalnızca HTTP. sys için geçerli olduğunu unutmayın.

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

|  |  | **Notlar** |
| --- | --- | --- |
| Web sunucusu | Kestrel | İç durum bilgisi olmayan hizmetler için HTTP. sys ' i kullanabilseniz de, Kestrel birden çok hizmet örneğinin bir konağı paylaşmasına izin veren en iyi sunucu olur.  |
| Bağlantı noktası yapılandırması | dinamik olarak atandı | Durum bilgisi olan bir hizmetin birden fazla çoğaltması bir konak işlemini veya ana bilgisayar işletim sistemini paylaşabilir ve bu nedenle benzersiz bağlantı noktalarına gerek duyar. |
| Servicefabricıntegrationoptions | UseUniqueServiceUrl 'Si | Dinamik bağlantı noktası atama ile bu ayar, daha önce açıklanan kimlik sorununa engel olur. |
| Yükteki InstanceCount | Kaydedilmemiş | Örnek sayısı ayarı, hizmeti çalıştırmak için gereken herhangi bir değere ayarlanabilir. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Yalnızca iç durum bilgisi olan ASP.NET Core hizmeti
Yalnızca küme içinden çağrılan durum bilgisi olan hizmetler, birden çok hizmet arasında ortak işlem sağlamak için dinamik olarak atanmış bağlantı noktaları kullanmalıdır. Aşağıdaki yapılandırmayı öneririz:

|  |  | **Notlar** |
| --- | --- | --- |
| Web sunucusu | Kestrel | `HttpSysCommunicationListener`, çoğaltmaların bir konak işlemini paylaştığı durum bilgisi olan hizmetler tarafından kullanılmak üzere tasarlanmamıştır. |
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
