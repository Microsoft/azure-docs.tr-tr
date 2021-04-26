---
title: Service Fabric 'de C# kullanarak hizmet uzaktan iletişimi
description: Service Fabric uzaktan iletişim, istemcilerin ve hizmetlerin uzak yordam çağrısı kullanarak C# hizmetleriyle iletişim kurmasına olanak tanır.
ms.topic: conceptual
ms.date: 09/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: a0486a27d76c978a65c4a3cfd81df52a12e4ea1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791586"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Reliable Services Ile C# ' de hizmet uzaktan iletişimi

> [!div class="op_single_selector"]
> * [Windows üzerinde C#](service-fabric-reliable-services-communication-remoting.md)
> * [Linux üzerinde Java](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Bir Web API 'SI, Windows Communication Foundation veya diğerleri gibi belirli bir iletişim protokolüne veya yığına bağlı olmayan hizmetlerde, Reliable Services Framework Hizmetleri için uzak yordam çağrılarını hızlı ve kolay bir şekilde ayarlamak için bir uzaktan iletişim mekanizması sağlar. Bu makalede, C# ile yazılmış hizmetler için uzak yordam çağrılarının nasıl ayarlanacağı açıklanır.

## <a name="set-up-remoting-on-a-service"></a>Bir hizmette uzaktan iletişim kurma

İki basit adımda bir hizmet için uzaktan iletişim kurabilirsiniz:

1. Hizmetinizin uygulanması için bir arabirim oluşturun. Bu arabirim, hizmetinize bir uzak yordam çağrısı için kullanılabilen yöntemleri tanımlar. Yöntemler görev döndüren zaman uyumsuz yöntemler olmalıdır. Arabirimin, `Microsoft.ServiceFabric.Services.Remoting.IService` hizmetin uzaktan iletişim arabirimine sahip olduğunu bildirmek için uygulaması gerekir.
2. Hizmetinizde uzaktan iletişim dinleyicisi kullanın. Uzaktan iletişim dinleyicisi, `ICommunicationListener` Uzaktan iletişim özellikleri sağlayan bir uygulamasıdır. `Microsoft.ServiceFabric.Services.Remoting.Runtime`Ad alanı, `CreateServiceRemotingInstanceListeners` Varsayılan uzaktan iletişim aktarım protokolünü kullanarak uzaktan iletişim dinleyicisi oluşturmak için kullanılabilen hem durum bilgisi olmayan hem de durum bilgisi olan hizmetlerin uzantı yöntemini içerir.

>[!NOTE]
>`Remoting`Ad alanı, adlı ayrı bir NuGet paketi olarak kullanılabilir `Microsoft.ServiceFabric.Services.Remoting` .

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
> Hizmet arabirimindeki bağımsız değişkenler ve dönüş türleri herhangi bir basit, karmaşık veya özel tür olabilir, ancak .NET [DataContractSerializer](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)tarafından seri hale getirilebilir olmaları gerekir.
>
>

## <a name="call-remote-service-methods"></a>Uzak hizmet yöntemlerini çağırma

Uzaktan iletişim yığınını kullanarak bir hizmette yöntemleri çağırmak, sınıfı aracılığıyla hizmete yerel bir proxy kullanılarak yapılır `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . `ServiceProxy`Yöntemi, hizmetin uyguladığı arabirimi kullanarak bir yerel ara sunucu oluşturur. Bu proxy ile, arabirimdeki yöntemleri uzaktan çağırabilirsiniz.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Remoting Framework, hizmet tarafından oluşturulan özel durumları istemciye yayar. Sonuç olarak, `ServiceProxy` kullanıldığında istemci, hizmet tarafından oluşturulan özel durumları işlemekten sorumludur.

## <a name="service-proxy-lifetime"></a>Hizmet proxy 'si ömrü

Hizmet proxy 'si oluşturma hafif bir işlemdir, bu sayede ihtiyacınız olan kadar oluşturabilirsiniz. Hizmet proxy örnekleri, gerektiği sürece yeniden kullanılabilir. Bir uzak yordam çağrısı bir özel durum oluşturursa, yine de aynı proxy örneğini yeniden kullanabilirsiniz. Her hizmet proxy 'si, iletileri kablo üzerinden göndermek için kullanılan bir iletişim istemcisi içerir. Uzaktan aramalar çağrılırken, iletişim istemcisinin geçerli olup olmadığını anlamak için iç denetimler gerçekleştirilir. Bu denetimlerin sonuçlarına bağlı olarak, iletişim istemcisi gerektiğinde yeniden oluşturulur. Bu nedenle, bir özel durum oluşursa, yeniden oluşturmanız gerekmez `ServiceProxy` .

### <a name="service-proxy-factory-lifetime"></a>Hizmet proxy 'si fabrikası ömrü

[Serviceproxyfactory](/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) , farklı uzaktan iletişim arabirimleri için proxy örnekleri oluşturan bir fabrikadır. `ServiceProxyFactory.CreateServiceProxy`Bir proxy oluşturmak IÇIN API kullanıyorsanız, çerçeve bir tek hizmet proxy 'si oluşturur.
[Iviceremotingclientfactory](/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) özelliklerini geçersiz kılmanız gerektiğinde bir el ile oluşturmak yararlı olur.
Fabrika oluşturma maliyetli bir işlemdir. Hizmet proxy fabrikası, iletişim istemcisinin dahili bir önbelleğini tutar.
En iyi yöntem, hizmet proxy fabrikasını mümkün olduğunca uzun süre önbelleğe alma yöntemidir.

## <a name="remoting-exception-handling"></a>Uzaktan iletişim özel durum işleme

Hizmet API 'SI tarafından oluşturulan tüm uzak özel durumlar istemciye AggregateException olarak geri gönderilir. Uzak özel durumlar, DataContract tarafından seri hale getirilebilir. Bunlar yoksa, proxy API 'si, içinde serileştirme hatasıyla [ServiceException](/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) oluşturur.

Hizmet proxy 'si, için oluşturulduğu hizmet bölümünün tüm yük devretme özel durumlarını işler. Yük devretme özel durumları (geçici olmayan özel durumlar) varsa uç noktaları yeniden çözer ve çağrıyı doğru uç noktayla yeniden dener. Yük devretme özel durumları için yeniden deneme sayısı sonsuz.
Geçici özel durumlar oluşursa, ara sunucu çağrıyı yeniden dener.

Varsayılan yeniden deneme parametreleri [Operationretrysettings](/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings)tarafından sağlanır.

Kullanıcı bu değerleri, OperationRetrySettings nesnesini ServiceProxyFactory oluşturucusuna geçirerek yapılandırabilir.

## <a name="use-the-remoting-v2-stack"></a>Remoting v2 yığınını kullanma

NuGet Remoting paketinin 2,8 sürümünden itibaren, remoting v2 yığınını kullanma seçeneğiniz vardır. Remoting v2 yığını daha iyi çalışır. Ayrıca özel serileştirme ve daha fazla takılabilir API 'Ler gibi özellikler de sağlar.
Şablon kodu, uzaktan iletişim v1 yığınını kullanmaya devam eder.
Remoting v2, v1 (önceki uzaktan iletişim yığını) ile uyumlu değildir. Hizmet kullanılabilirliğine yönelik etkileri önlemek için  [v1 'Den v2 'ye yükseltme](#upgrade-from-remoting-v1-to-remoting-v2) makalesindeki yönergeleri izleyin.

V2 yığınını etkinleştirmek için aşağıdaki yaklaşımlar mevcuttur.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>V2 yığınını kullanmak için bir derleme özniteliği kullanın

Bu adımlar, derleme özniteliği kullanarak v2 yığınını kullanmak için şablon kodunu değiştirir.

1. Uç nokta kaynağını `"ServiceEndpoint"` `"ServiceEndpointV2"` hizmet bildiriminde ' dan ' a değiştirin.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners`Uzaktan iletişim dinleyicileri oluşturmak için genişletme yöntemini kullanın (hem v1 hem de v2 için eşittir).

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

1. Uç nokta kaynağını `"ServiceEndpoint"` `"ServiceEndpointV2"` hizmet bildiriminde ' dan ' a değiştirin.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Ad alanından [Fabrictransportserviceremotinglistener](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener) kullanın `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` .

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

3. İstemcileri oluşturmak için ad alanından [Fabrictransportserviceremotingclientfactory](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory) komutunu kullanın `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` .

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

 Remoting v2 (arabirim uyumlu, V2_1 olarak bilinir) yığını, V2 Uzaktan iletişim yığınının tüm özelliklerine sahiptir. Arabirim yığını, uzaktan iletişim v1 Stack ile uyumludur, ancak v2 ve v1 ile geriye dönük olarak uyumlu değildir. V1 'den, hizmet kullanılabilirliğini etkilemeden V2_1 sürümüne yükseltmek için v1 'den v2 'ye yükseltme (arabirim ile uyumlu) makalesindeki adımları izleyin.


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Remoting v2 (arabirim ile uyumlu) yığınını kullanmak için bir derleme özniteliği kullanın

V2_1 yığınına geçmek için aşağıdaki adımları izleyin.

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

3. Uzaktan iletişim arabirimlerine bir [derleme özniteliği](/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute) ekleyin.

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

2. [Remoting v2 dinleyicisini](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener)kullanın. Kullanılan varsayılan hizmet uç noktası kaynak adı "ServiceEndpointV2_1" dir. Hizmet bildiriminde tanımlanmalıdır.

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

3. V2 [istemci fabrikası](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory)' nu kullanın.
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Remoting v1 'den Remoting v2 'ye yükseltme (arabirime uyumlu)

V1 'den v2 'ye yükseltmek için (V2_1 olarak bilinen arabirim uyumlu), iki adımlı yükseltmeler gereklidir. Bu dizideki adımları izleyin.

> [!NOTE]
> V1 'den v2 'ye yükseltirken, `Remoting` ad alanının v2 kullanacak şekilde güncelleştirildiğinden emin olun. Örnek: ' Microsoft. ServiceFabric. Services. Remoting. v2. FabricTransport. Client '
>
>

1. Aşağıdaki özniteliği kullanarak v1 hizmetini V2_1 hizmetine yükseltin.
Bu değişiklik, hizmetin v1 ve V2_1 dinleyicisinden dinleme yaptığını sağlar.

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

1. `IServiceRemotingMessageSerializationProvider`Özel serileştirme için uygulama sağlamak üzere arabirimini uygulayın.
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

2. Uzaktan iletişim dinleyicisi için olan varsayılan serileştirme sağlayıcısını geçersiz kılın `JsonSerializationProvider` .

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

3. Uzaktan iletişim istemci fabrikası için ile varsayılan serileştirme sağlayıcısını geçersiz kılın `JsonSerializationProvider` .

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Sonraki adımlar

* [Reliable Services 'de OWIN ile Web API 'SI](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Reliable Services ile iletişim Windows Communication Foundation](service-fabric-reliable-services-communication-wcf.md)
* [Reliable Services için güvenli iletişim](service-fabric-reliable-services-secure-communication.md)
