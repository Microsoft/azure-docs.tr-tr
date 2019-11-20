---
title: Service Fabric ' de kullanarak C# hizmet uzaktan iletişimi Microsoft Docs
description: Service Fabric uzaktan iletişim, istemcilerin ve hizmetlerin uzak yordam C# çağrısı kullanarak hizmetlerle iletişim kurmasına olanak tanır.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 1654a7be8c3aba4efa6fcf96024ea987e2957e73
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173453"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Reliable Services C# ile hizmet uzaktan iletişimi

> [!div class="op_single_selector"]
> * [C#Windows 'da](service-fabric-reliable-services-communication-remoting.md)
> * [Linux üzerinde Java](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Bir Web API 'SI, Windows Communication Foundation veya diğerleri gibi belirli bir iletişim protokolüne veya yığına bağlı olmayan hizmetler için, Reliable Services Framework, için uzak yordam çağrılarını hızlı ve kolay bir şekilde ayarlamaya yönelik bir uzaktan iletişim mekanizması sağlar servislere. Bu makalede, ile C#yazılmış hizmetler için uzak yordam çağrılarının nasıl ayarlanacağı açıklanır.

## <a name="set-up-remoting-on-a-service"></a>Bir hizmette uzaktan iletişim kurma

İki basit adımda bir hizmet için uzaktan iletişim kurabilirsiniz:

1. Hizmetinizin uygulanması için bir arabirim oluşturun. Bu arabirim, hizmetinize bir uzak yordam çağrısı için kullanılabilen yöntemleri tanımlar. Yöntemler görev döndüren zaman uyumsuz yöntemler olmalıdır. Hizmetin bir uzaktan iletişim arabirimine sahip olduğunu bildirmek için arabirimin `Microsoft.ServiceFabric.Services.Remoting.IService` uygulaması gerekir.
2. Hizmetinizde uzaktan iletişim dinleyicisi kullanın. Uzaktan iletişim dinleyicisi, uzaktan iletişim özellikleri sağlayan `ICommunicationListener` uygulamasıdır. @No__t-0 ad alanı, varsayılan uzaktan iletişim aktarım protokolünü kullanarak uzaktan iletişim dinleyicisi oluşturmak için kullanılabilecek hem durum bilgisiz hem de durum bilgisi olan hizmetler için `CreateServiceRemotingInstanceListeners` uzantı yöntemini içerir.

>[!NOTE]
>@No__t-0 ad alanı, `Microsoft.ServiceFabric.Services.Remoting` adlı ayrı bir NuGet paketi olarak kullanılabilir.

Örneğin, aşağıdaki durum bilgisi olmayan hizmet bir uzak yordam çağrısı üzerinden "Merhaba Dünya" almak için tek bir yöntem sunar.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> Hizmet arabirimindeki bağımsız değişkenler ve dönüş türleri herhangi bir basit, karmaşık veya özel tür olabilir, ancak .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx)tarafından seri hale getirilebilir olmaları gerekir.
>
>

## <a name="call-remote-service-methods"></a>Uzak hizmet yöntemlerini çağırma

Uzaktan iletişim yığınını kullanarak bir hizmette Yöntemler çağırmak, `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` sınıfı aracılığıyla hizmete yerel bir proxy kullanılarak yapılır. @No__t-0 yöntemi, hizmetin uyguladığı arabirimi kullanarak bir yerel ara sunucu oluşturur. Bu proxy ile, arabirimdeki yöntemleri uzaktan çağırabilirsiniz.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Remoting Framework, hizmet tarafından oluşturulan özel durumları istemciye yayar. Sonuç olarak, `ServiceProxy`kullanıldığında, istemci, hizmet tarafından oluşturulan özel durumları işlemekten sorumludur.

## <a name="service-proxy-lifetime"></a>Hizmet proxy 'si ömrü

Hizmet proxy 'si oluşturma hafif bir işlemdir, bu sayede ihtiyacınız olan kadar oluşturabilirsiniz. Hizmet proxy örnekleri, gerektiği sürece yeniden kullanılabilir. Bir uzak yordam çağrısı bir özel durum oluşturursa, yine de aynı proxy örneğini yeniden kullanabilirsiniz. Her hizmet proxy 'si, iletileri kablo üzerinden göndermek için kullanılan bir iletişim istemcisi içerir. Uzaktan aramalar çağrılırken, iletişim istemcisinin geçerli olup olmadığını anlamak için iç denetimler gerçekleştirilir. Bu denetimlerin sonuçlarına bağlı olarak, iletişim istemcisi gerektiğinde yeniden oluşturulur. Bu nedenle, bir özel durum oluşursa @no__t yeniden oluşturmanız gerekmez-0.

### <a name="service-proxy-factory-lifetime"></a>Hizmet proxy 'si fabrikası ömrü

[Serviceproxyfactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) , farklı uzaktan iletişim arabirimleri için proxy örnekleri oluşturan bir fabrikadır. Bir proxy oluşturmak için API 'YI `ServiceProxyFactory.CreateServiceProxy` kullanıyorsanız, çerçeve bir tek hizmet proxy 'si oluşturur.
[Iviceremotingclientfactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) özelliklerini geçersiz kılmanız gerektiğinde bir el ile oluşturmak yararlı olur.
Fabrika oluşturma maliyetli bir işlemdir. Hizmet proxy fabrikası, iletişim istemcisinin dahili bir önbelleğini tutar.
En iyi yöntem, hizmet proxy fabrikasını mümkün olduğunca uzun süre önbelleğe alma yöntemidir.

## <a name="remoting-exception-handling"></a>Uzaktan iletişim özel durum işleme

Hizmet API 'SI tarafından oluşturulan tüm uzak özel durumlar istemciye AggregateException olarak geri gönderilir. Uzak özel durumlar, DataContract tarafından seri hale getirilebilir. Bunlar yoksa, proxy API 'si, içinde serileştirme hatasıyla [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) oluşturur.

Hizmet proxy 'si, için oluşturulduğu hizmet bölümünün tüm yük devretme özel durumlarını işler. Yük devretme özel durumları (geçici olmayan özel durumlar) varsa uç noktaları yeniden çözer ve çağrıyı doğru uç noktayla yeniden dener. Yük devretme özel durumları için yeniden deneme sayısı sonsuz.
Geçici özel durumlar oluşursa, ara sunucu çağrıyı yeniden dener.

Varsayılan yeniden deneme parametreleri [Operationretrysettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings)tarafından sağlanır.

Kullanıcı bu değerleri, OperationRetrySettings nesnesini ServiceProxyFactory oluşturucusuna geçirerek yapılandırabilir.

## <a name="use-the-remoting-v2-stack"></a>Remoting v2 yığınını kullanma

NuGet Remoting paketinin 2,8 sürümünden itibaren, remoting v2 yığınını kullanma seçeneğiniz vardır. Remoting v2 yığını daha iyi çalışır. Ayrıca özel serileştirme ve daha fazla takılabilir API 'Ler gibi özellikler de sağlar.
Şablon kodu, uzaktan iletişim v1 yığınını kullanmaya devam eder.
Remoting v2, v1 (önceki uzaktan iletişim yığını) ile uyumlu değildir. Hizmet kullanılabilirliğine yönelik etkileri önlemek için [v1 'Den v2 'ye yükseltme](#upgrade-from-remoting-v1-to-remoting-v2) makalesindeki yönergeleri izleyin.

V2 yığınını etkinleştirmek için aşağıdaki yaklaşımlar mevcuttur.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>V2 yığınını kullanmak için bir derleme özniteliği kullanın

Bu adımlar, derleme özniteliği kullanarak v2 yığınını kullanmak için şablon kodunu değiştirir.

1. Uç nokta kaynağını, hizmet bildiriminde `"ServiceEndpoint"` ' dan `"ServiceEndpointV2"` ' e değiştirin.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Uzaktan iletişim dinleyicileri oluşturmak için `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` genişletme yöntemini kullanın (hem v1 hem de v2 için eşittir).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Uzaktan iletişim arabirimlerini içeren derlemeyi bir `FabricTransportServiceRemotingProvider` özniteliğiyle işaretleyin.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

İstemci projesinde kod değişikliği gerekli değildir.
Daha önce gösterilen derleme özniteliğinin kullanıldığından emin olmak için arabirim derlemesi ile istemci derlemesini derleyin.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>V2 yığınını kullanmak için açık v2 sınıfları kullanma

Derleme özniteliği kullanmanın bir alternatifi olarak, v2 yığını da açık v2 sınıfları kullanılarak etkinleştirilebilir.

Bu adımlar, açık v2 sınıfları kullanarak v2 yığınını kullanmak için şablon kodunu değiştirir.

1. Uç nokta kaynağını, hizmet bildiriminde `"ServiceEndpoint"` ' dan `"ServiceEndpointV2"` ' e değiştirin.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. @No__t-1 ad alanından [Fabrictransportserviceremotinglistener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) kullanın.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
   ```

3. İstemcileri oluşturmak için `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` ad alanından [Fabrictransportserviceremotingclientfactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) komutunu kullanın.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Remoting v1 'den Remoting v2 'ye yükseltme

V1 'den v2 'ye yükseltme yapmak için iki adımlı yükseltmeler gereklidir. Bu dizideki adımları izleyin.

1. Bu özniteliği kullanarak v1 hizmetini v2 hizmetine yükseltin.
Bu değişiklik, hizmetin v1 ve v2 dinleyicisinden dinlediği şekilde emin olmanızı sağlar.

    a. Hizmet bildiriminde "ServiceEndpointV2" adlı bir uç nokta kaynağı ekleyin.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Bir uzaktan iletişim dinleyicisi oluşturmak için aşağıdaki genişletme yöntemini kullanın.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. V1 ve v2 dinleyicisini ve v2 istemcisini kullanmak için uzaktan iletişim arabirimlerine bir derleme özniteliği ekleyin.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. V2 istemci özniteliğini kullanarak v1 istemcisini v2 istemcisine yükseltin.
Bu adım, istemcinin v2 yığınını kullandığından emin olur.
İstemci projesinde/hizmette hiçbir değişiklik yapılması gerekmez. Güncelleştirilmiş arabirim derlemesi olan istemci projeleri oluşturma yeterlidir.

3. Bu adım isteğe bağlıdır. V2 dinleyicisi özniteliğini kullanın ve ardından v2 hizmetini yükseltin.
Bu adım, hizmetin yalnızca v2 dinleyicisine dinlediğinden emin olmanızı sağlar.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Remoting v2 (arabirim ile uyumlu) yığınını kullanın

 Remoting v2 (Interface uyumlu, V2_1 olarak bilinir) yığını, V2 Uzaktan iletişim yığınının tüm özelliklerine sahiptir. Arabirim yığını, uzaktan iletişim v1 Stack ile uyumludur, ancak v2 ve v1 ile geriye dönük olarak uyumlu değildir. Hizmet kullanılabilirliğini etkilemeden v1 'den V2_1 'e yükseltmek için v1 'den v2 'ye yükseltme (arabirim ile uyumlu) makalesindeki adımları izleyin.


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Remoting v2 (arabirim ile uyumlu) yığınını kullanmak için bir derleme özniteliği kullanın

V2_1 yığınına geçmek için bu adımları izleyin.

1. Hizmet bildiriminde "ServiceEndpointV2_1" adlı bir uç nokta kaynağı ekleyin.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Uzaktan iletişim dinleyicisi oluşturmak için uzaktan iletişim uzantısı yöntemini kullanın.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Uzaktan iletişim arabirimlerine bir [derleme özniteliği](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) ekleyin.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

İstemci projesinde değişiklik yapılması gerekmez.
Önceki derleme özniteliğinin kullanıldığından emin olmak için arabirim derlemesi ile istemci derlemesini derleyin.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>V2 (arabirim ile uyumlu) sürümü için bir dinleyici/istemci fabrikası oluşturmak üzere açık uzaktan iletişim sınıfları kullanın

Şu adımları izleyin:

1. Hizmet bildiriminde "ServiceEndpointV2_1" adlı bir uç nokta kaynağı ekleyin.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. [Remoting v2 dinleyicisini](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet)kullanın. Kullanılan varsayılan hizmet uç noktası kaynak adı "ServiceEndpointV2_1" dir. Hizmet bildiriminde tanımlanmalıdır.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. V2 [istemci fabrikası](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet)' nu kullanın.
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Remoting v1 'den Remoting v2 'ye yükseltme (arabirime uyumlu)

V1 'den v2 'ye yükseltmek için (arabirim uyumlu, V2_1 olarak bilinir), iki adımlı yükseltmeler gereklidir. Bu dizideki adımları izleyin.

> [!NOTE]
> V1 'den v2 'ye yükseltirken `Remoting` ad alanının v2 kullanacak şekilde güncelleştirildiğinden emin olun. Örnek: ' Microsoft. ServiceFabric. Services. Remoting. v2. FabricTransport. Client '
>
>

1. Aşağıdaki özniteliği kullanarak v1 hizmetini V2_1 Service 'e yükseltin.
Bu değişiklik, hizmetin v1 ve V2_1 dinleyicisini dinlediğinden emin olmanızı sağlar.

    a. Hizmet bildiriminde "ServiceEndpointV2_1" adlı bir uç nokta kaynağı ekleyin.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Bir uzaktan iletişim dinleyicisi oluşturmak için aşağıdaki genişletme yöntemini kullanın.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. V1, V2_1 dinleyicisi ve V2_1 istemcisini kullanmak için uzaktan iletişim arabirimlerine bir derleme özniteliği ekleyin.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. V2_1 Client özniteliğini kullanarak v1 istemcisini V2_1 istemcisine yükseltin.
Bu adım, istemcinin V2_1 yığınını kullanıyor olmasını sağlar.
İstemci projesinde/hizmette hiçbir değişiklik yapılması gerekmez. Güncelleştirilmiş arabirim derlemesi olan istemci projeleri oluşturma yeterlidir.

3. Bu adım isteğe bağlıdır. V1 dinleyicisi sürümünü özniteliğinden kaldırın ve ardından v2 hizmetini yükseltin.
Bu adım, hizmetin yalnızca v2 dinleyicisine dinlediğinden emin olmanızı sağlar.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Uzaktan, Sarmalanan bir ileti ile özel serileştirme kullanma

Uzaktan iletişim sarmalanmış bir ileti için, içinde bir alan olarak tüm parametreleri içeren tek bir Sarmalanan nesne oluşturacağız.
Şu adımları izleyin:

1. Özel serileştirme için uygulama sağlamak üzere `IServiceRemotingMessageSerializationProvider` arabirimini uygulayın.
    Bu kod parçacığı, uygulamanın nasıl göründüğünü gösterir.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. Uzaktan iletişim dinleyicisi için `JsonSerializationProvider` ile varsayılan serileştirme sağlayıcısını geçersiz kılın.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
   ```

3. Uzaktan iletişim istemci fabrikası için varsayılan serileştirme sağlayıcısını `JsonSerializationProvider` ile geçersiz kılın.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Sonraki adımlar

* [Reliable Services 'de OWIN ile Web API 'SI](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services ile iletişim Windows Communication Foundation](service-fabric-reliable-services-communication-wcf.md)
* [Reliable Services için güvenli iletişim](service-fabric-reliable-services-secure-communication.md)
