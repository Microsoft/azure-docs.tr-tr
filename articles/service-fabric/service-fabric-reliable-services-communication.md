---
title: Reliable Services iletişime genel bakış | Microsoft Docs
description: Hizmetlere yönelik dinleyicileri açmak, uç noktaları çözümlemek ve hizmetler arasında iletişim kurmak dahil Reliable Services iletişim modeline genel bakış.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: BharatNarasimman
ms.assetid: 36217988-420e-409d-b0a4-e0e875b6eac8
ms.service: service-fabric
ms.devlang: csharp, java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 4d3deb7f3b7e7fb6334525886c6d5b8787a8f940
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036774"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Reliable Services iletişim API 'Lerini kullanma
Platform olarak Azure Service Fabric, hizmetler arasındaki iletişim hakkında tamamen bağımsızdır. Her protokol ve yığın, UDP 'den HTTP 'ye kadar kabul edilebilir. Hizmetlerin nasıl iletişim kurması gerektiğini seçmek için hizmet geliştiricisi 'nin bir daha vardır. Reliable Services uygulama çerçevesi, yerleşik iletişim yığınlarının yanı sıra özel iletişim bileşenlerinizi oluşturmak için kullanabileceğiniz API 'Ler sağlar.

## <a name="set-up-service-communication"></a>Hizmet iletişimini ayarla
Reliable Services API 'SI, hizmet iletişimi için basit bir arabirim kullanır. Hizmetiniz için bir uç nokta açmak üzere bu arabirimi uygulamanız yeterlidir:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

Daha sonra iletişim dinleyicisi uygulamanızı, hizmet tabanlı bir sınıf yöntemi geçersiz kılma bölümünde döndürerek ekleyebilirsiniz.

Durum bilgisi olmayan hizmetler için:

```csharp
public class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

Durum bilgisi olan hizmetler için:

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

Her iki durumda da bir dinleyici koleksiyonu döndürürler. Bu, hizmetinize birden çok dinleyici kullanarak farklı protokoller kullanan birden çok uç noktayı dinlemesine olanak tanır. Örneğin, bir HTTP dinleyicisine ve ayrı bir WebSocket dinleyicisine sahip olabilirsiniz. Her dinleyici bir ad alır ve sonuç olarak *ad: adres* çiftleri, bir istemci bir hizmet örneği veya bölüm için dinleme adreslerini ISTEDIĞINDE bir JSON nesnesi olarak temsil edilir.

Durum bilgisi olmayan bir hizmette, geçersiz kılma Serviceınstancelisteners koleksiyonunu döndürür. , Oluşturmak için bir işlev içerirvebunabiradverir.`ServiceInstanceListener` `ICommunicationListener(C#) / CommunicationListener(Java)` Durum bilgisi olan hizmetler için, geçersiz kılma bir ServiceReplicaListeners koleksiyonu döndürür. Bu, durum bilgisiz karşılığından biraz farklıdır, çünkü `ServiceReplicaListener` bir ikincil çoğaltmalarda bir `ICommunicationListener` açma seçeneği vardır. Yalnızca bir hizmette birden fazla iletişim dinleyicisi kullanamazsınız, ancak hangi dinleyicilerin ikincil çoğaltmalarda istekleri kabul ettiklerinin ve yalnızca birincil çoğaltmalarda dinleneceğini de belirtebilirsiniz.

Örneğin, yalnızca birincil çoğaltmalarda RPC çağrıları alan bir ServiceRemotingListener ve HTTP üzerinden ikincil çoğaltmalarda okuma isteklerini alan ikinci bir özel dinleyici oluşturabilirsiniz:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> Bir hizmet için birden çok dinleyici oluştururken, her dinleyiciye benzersiz bir ad verilmelidir.
>
>

Son olarak, uç noktaların bölümünde hizmet [bildiriminde](service-fabric-application-and-service-manifests.md) hizmet için gereken uç noktaları tanıtın.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

İletişim dinleyicisi, `CodePackageActivationContext` `ServiceContext`içindeki öğesinden öğesine ayrılmış uç nokta kaynaklarına erişebilir. Daha sonra dinleyici, açıldığında istekleri dinlemeye başlayabilir.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Uç nokta kaynakları tüm hizmet paketi için ortaktır ve hizmet paketi etkinleştirildiğinde Service Fabric tarafından ayrılır. Aynı ServiceHost içinde barındırılan birden çok hizmet çoğaltması aynı bağlantı noktasını paylaşabilir. Bu, iletişim dinleyicisinin bağlantı noktası paylaşımını desteklemesi gerektiği anlamına gelir. Bunu yapmanın önerilen yolu, iletişim dinleyicisinin dinleme adresini oluştururken bölüm KIMLIĞINI ve çoğaltma/örnek KIMLIĞINI kullanması içindir.
>
>

### <a name="service-address-registration"></a>Hizmet adresi kaydı
*Adlandırma hizmeti* adlı bir sistem hizmeti Service Fabric kümelerinde çalışır. Adlandırma Hizmeti, hizmetler için bir kayıt ve hizmetin her örneğinin veya çoğaltmasının dinlediği adresleridir. `OpenAsync(C#) / openAsync(Java)` Bir`ICommunicationListener(C#) / CommunicationListener(Java)` işlemi tamamlandığında, dönüş değeri adlandırma hizmeti kaydedilir. Adlandırma Hizmeti yayımlanan bu dönüş değeri, değeri hiç bir şey olabilecek bir dizedir. Bu dize değeri, istemcilerin Adlandırma Hizmeti hizmetin bir adresini sorduklarında gördükleri şeydir.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

Service Fabric, istemcilerin ve diğer hizmetlerin bu adresi hizmet adına göre sormasını sağlayan API 'Ler sağlar. Hizmet adresi statik olmadığı için bu önemlidir. Hizmetler, kaynak Dengeleme ve kullanılabilirlik amaçlarıyla küme içinde taşınır. Bu, istemcilerin bir hizmetin dinleme adresini çözümlemesine izin veren mekanizmadır.

> [!NOTE]
> İletişim dinleyicisi yazma hakkında tam bir yol için, bkz. C# [Owin Self-HOSTING ile Web API Services Service Fabric](service-fabric-reliable-services-communication-webapi.md) . Java için kendi http sunucu uygulamanızı yazabilir, bkz. https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>Bir hizmetle iletişim kurma
Reliable Services API 'SI, hizmetlerle iletişim kuran istemcileri yazmak için aşağıdaki kitaplıkları sağlar.

### <a name="service-endpoint-resolution"></a>Hizmet uç noktası çözümleme
Bir hizmetle iletişimin ilk adımı, iletişim kurmak istediğiniz bölümün veya hizmet örneğinin bir uç nokta adresini çözmeye yönelik bir hizmettir. `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` Yardımcı program sınıfı, istemcilerin çalışma zamanında bir hizmetin uç noktasını belirlemesine yardımcı olan temel bir temel programdır. Service Fabric terimlerinde, bir hizmetin uç noktasını belirleme işlemi *hizmet uç noktası çözümü*olarak adlandırılır.

Bir küme içindeki hizmetlere bağlanmak için, varsayılan ayarlar kullanılarak ServicePartitionResolver oluşturulabilir. Çoğu durum için önerilen kullanımdır:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Farklı bir kümedeki hizmetlere bağlanmak için bir küme ağ geçidi uç noktası kümesiyle bir ServicePartitionResolver oluşturulabilir. Ağ Geçidi uç noktalarının aynı kümeye bağlanmak için yalnızca farklı uç noktalar olduğunu unutmayın. Örneğin:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Alternatif olarak `ServicePartitionResolver` , dahili olarak kullanılmak `FabricClient` üzere oluşturmak için bir işlev verilebilir:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient`, kümedeki çeşitli yönetim işlemleri için Service Fabric kümesiyle iletişim kurmak için kullanılan nesnedir. Bu, bir hizmet bölümü Çözümleyicisinin kümeniz ile nasıl etkileşime girdiğinin üzerinde daha fazla denetim istediğinizde yararlıdır. `FabricClient`dahili olarak önbelleğe alma işlemini gerçekleştirir ve genellikle oluşturulması çok pahalıdır, bu nedenle örneklerin mümkün `FabricClient` olduğunca yeniden kullanılması önemlidir.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Daha sonra bir çözümleme yöntemi, bölümlenmiş hizmetlere yönelik bir hizmetin veya hizmet bölümünün adresini almak için kullanılır.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

Hizmet adresi bir ServicePartitionResolver kullanılarak kolayca çözülebilir, ancak çözümlenen adresin doğru şekilde kullanılabilmesi için daha fazla iş gerekir. İstemci, geçici bir hata nedeniyle bağlantı girişiminin başarısız olup olmadığını algılamamalıdır (örneğin, hizmet taşınabilir veya geçici olarak kullanılamıyor) veya kalıcı bir hata (ör. hizmet silinmiş veya istenen kaynak artık yok). Hizmet örnekleri veya çoğaltmalar, birden çok nedenden dolayı herhangi bir zamanda düğümden düğüme hareket edebilir. ServicePartitionResolver üzerinden çözümlenen hizmet adresi, istemci kodunuzun bağlanmaya çalıştığı zamandan daha eski olabilir. Bu durumda, istemcinin adresi yeniden çözümlemesi gerekir. Öncekini `ResolvedServicePartition` sağlamak, çözümleyicinin yalnızca önbelleğe alınmış bir adresi almak yerine yeniden denemek gerektiğini gösterir.

Genellikle, istemci kodunun doğrudan ServicePartitionResolver ile çalışması gerekmez. Oluşturma ve Reliable Services API 'sindeki iletişim istemci fabrikalarını iletişim için başarılı oldu. Fabrikalar, hizmetlerle iletişim kurmak için kullanılabilecek bir istemci nesnesi oluşturmak için dahili olarak çözümleyici kullanır.

### <a name="communication-clients-and-factories"></a>İletişim istemcileri ve fabrikalar
İletişim fabrikası kitaplığı, çözümlenmiş hizmet uç noktalarına bağlantıların yeniden denenmesini kolaylaştıran tipik bir hata işleme yeniden deneme modelini uygular. Fabrika kitaplığı, hata işleyicilerini sağlarken yeniden deneme mekanizması sağlar.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`bir Service Fabric hizmetiyle konuşabilecek istemcileri üreten bir iletişim istemci fabrikası tarafından uygulanan temel arabirimi tanımlar. CommunicationClientFactory uygulamasının uygulanması, istemcinin iletişim kurmak istediği Service Fabric hizmeti tarafından kullanılan iletişim yığınına bağlıdır. Reliable Services API 'SI sağlar `CommunicationClientFactoryBase<TCommunicationClient>`. Bu, CommunicationClientFactory arabiriminin temel bir uygulamasını sağlar ve tüm iletişim yığınları için ortak olan görevleri gerçekleştirir. (Bu görevler hizmet uç noktasını belirlemede bir ServicePartitionResolver kullanmayı içerir). İstemciler genellikle iletişim yığınına özgü mantığı işlemek için soyut CommunicationClientFactoryBase sınıfını uygular.

İletişim istemcisi yalnızca bir adres alır ve bir hizmete bağlanmak için onu kullanır. İstemci istediği herhangi bir protokolü kullanabilir.

```csharp
public class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

İstemci fabrikası, birincil olarak iletişim istemcileri oluşturmaktan sorumludur. HTTP istemcisi gibi kalıcı bir bağlantı bulundurmeyen istemciler için, yalnızca fabrika 'nin istemciyi oluşturması ve döndürmesi gerekir. Bazı ikili protokoller gibi kalıcı bir bağlantı sağlayan diğer protokoller de, bağlantının yeniden oluşturulması gerekip gerekmediğini belirleyebilmek için fabrika tarafından doğrulanması gerekir.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Son olarak, özel durum işleyicisi bir özel durum oluştuğunda hangi eylemin yapılacağını belirlemekten sorumludur. Özel durumlar yeniden **denenebilir** ve yeniden **denenmesiz**olarak kategorize edilir.

* Yeniden **denenmeyen** özel durumlar, çağrı yapana tekrar geri oluşturulur.
* yeniden **denenebilir** özel durumlar **geçici** ve **geçici olmayan**bir şekilde kategorilere ayrılır.
  * **Geçici** özel durumlar, hizmet uç noktası adresi yeniden çözümlenmeden yalnızca yeniden denenebilecek olanlardır. Bunlar, hizmet uç noktası adresinin yok olduğunu belirten geçici ağ sorunları veya hizmet hatası yanıtlarını içerir.
  * **Geçici olmayan** özel durumlar, hizmet uç noktası adresinin yeniden çözümlenmesini gerektirir. Bunlar, hizmetin farklı bir düğüme taşındığını belirten hizmet uç noktasına ulaşılamadığını gösteren özel durumlar içerir.

, `TryHandleException` Belirli bir özel durumla ilgili bir karar getirir. Bir özel durum hakkında hangi kararların yapılacağını bilmezse, **false**döndürmelidir. Hangi kararın yapılacağını **biliyorsanız** , sonucu buna göre ayarlamanız ve **true**döndürmelidir.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>Hepsini bir araya getirme
,, Ve `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` bir iletişim protokolü etrafında yerleşik olarak, bir, tümünü bir araya getirir ve bu bileşenler etrafında hata işleme ve hizmet bölümü adres çözümleme döngüsünü sağlar. `IExceptionHandler(C#) / ExceptionHandler(Java)` `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` `ICommunicationClient(C#) / CommunicationClient(Java)`

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Sonraki adımlar
* [Reliable Services ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
* [Reliable Services uzaktan iletişim ile uzak yordam çağrıları](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services kullanarak WCF iletişimi](service-fabric-reliable-services-communication-wcf.md)
