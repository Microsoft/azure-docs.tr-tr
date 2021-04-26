---
title: Azure Işlevleri uygulamasını geliştirme & yapılandırma uygulaması-Azure SignalR
description: Azure Işlevleri ve Azure SignalR hizmeti kullanılarak sunucusuz gerçek zamanlı uygulamalar geliştirmeye ve yapılandırmaya ilişkin ayrıntılar
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3d69b72012819e3d9099e447b9048fe07aea86d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97858714"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure SignalR Hizmeti ile Azure İşlevleri geliştirme ve yapılandırma

Azure Işlevleri uygulamaları, gerçek zamanlı yetenekler eklemek için [Azure SignalR hizmeti bağlamalarından](../azure-functions/functions-bindings-signalr-service.md) faydalanabilir. İstemci uygulamaları, Azure SignalR hizmetine bağlanmak ve gerçek zamanlı iletiler almak için birkaç dilde sunulan istemci SDK 'larını kullanır.

Bu makalede, SignalR hizmeti ile tümleştirilmiş bir Azure Işlev uygulaması geliştirmeye ve yapılandırmaya yönelik kavramlar açıklanmaktadır.

## <a name="signalr-service-configuration"></a>SignalR hizmeti yapılandırması

Azure SignalR hizmeti, farklı modlarda yapılandırılabilir. Azure Işlevleri ile kullanıldığında hizmetin *sunucusuz* modda yapılandırılması gerekir.

Azure portal, SignalR hizmeti kaynağınızın *Ayarlar* sayfasını bulun. *Hizmet modunu* *sunucusuz* olarak ayarlayın.

![SignalR hizmeti modu](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure İşlevleri geliştirme

Azure İşlevleri ve Azure SignalR Hizmeti ile oluşturulan sunucusuz bir gerçek zamanlı uygulama için genelde iki Azure İşlevi gerekir:

* Müşterinin geçerli bir SignalR Hizmeti erişim belirteci ve hizmet uç noktası URL’si almak için çağırdığı “negotiate” işlevi
* SignalR hizmetinden iletileri işleyen ve ileti gönderen veya grup üyeliğini yöneten bir veya daha fazla işlev

### <a name="negotiate-function"></a>Negotiate işlevi

İstemci uygulaması, Azure SignalR hizmetine bağlanmak için geçerli bir erişim belirteci gerektirir. Erişim belirteci, belirli bir kullanıcı KIMLIĞI için anonim veya kimliği doğrulanmış olabilir. Sunucusuz SignalR hizmeti uygulamaları, bir belirteç ve SignalR hizmeti uç noktası URL 'SI gibi diğer bağlantı bilgilerini almak için "Negotiate" adlı bir HTTP uç noktası gerektirir.

Bağlantı bilgileri nesnesini oluşturmak için HTTP ile tetiklenen bir Azure Işlevi ve *Signalrconnectionınfo* giriş bağlaması kullanın. İşlevin ' de sonlanan bir HTTP yolu olması gerekir `/negotiate` .

C# dilinde [sınıf tabanlı model](#class-based-model) Ile, *Signalrconnectionınfo* giriş bağlamaya gerek kalmaz ve özel talepler ekleyebilir. Bkz. [sınıf tabanlı modelde anlaşma deneyimi](#negotiate-experience-in-class-based-model)

Negotiate işlevinin nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [ *Signalrconnectionınfo* giriş bağlama başvurusu](../azure-functions/functions-bindings-signalr-service-input.md).

Kimliği doğrulanmış bir belirteç oluşturma hakkında bilgi edinmek için [App Service kimlik doğrulaması kullanma](#using-app-service-authentication)konusuna bakın.

### <a name="handle-messages-sent-from-signalr-service"></a>SignalR hizmetinden gönderilen iletileri işle

SignalR hizmetinden gönderilen iletileri işlemek için *SignalR tetikleme* bağlamasını kullanın. İstemciler ileti gönderirken veya istemcileri bağlandığında veya bağlantıyı kestikten sonra bildirim alabilirsiniz.

Daha fazla bilgi için bkz. [ *SignalR tetikleyicisi* bağlama başvurusu](../azure-functions/functions-bindings-signalr-service-trigger.md).

Ayrıca, hizmetin istemciden ileti olduğunda işlevi tetikleyeceği şekilde işlev uç noktanızı bir yukarı akış olarak yapılandırmanız gerekir. Yukarı akışı yapılandırma hakkında daha fazla bilgi için lütfen bu [belgeye](concept-upstream.md)başvurun.

### <a name="sending-messages-and-managing-group-membership"></a>İleti gönderme ve grup üyeliğini yönetme

Azure SignalR hizmetine bağlı istemcilere ileti göndermek için *SignalR* çıkış bağlamasını kullanın. İletileri tüm istemcilere yayınlayabilirsiniz veya belirli bir kullanıcı KIMLIĞI ile kimliği doğrulanmış veya belirli bir gruba eklenmiş bir istemci alt kümesine gönderebilirsiniz.

Kullanıcılar, bir veya daha fazla gruba eklenebilir. Ayrıca, Grup gruplarına/gruplardan Kullanıcı eklemek veya kaldırmak için *SignalR* çıktı bağlamasını da kullanabilirsiniz.

Daha fazla bilgi için bkz. [ *SignalR* çıkış bağlama başvurusu](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>SignalR hub 'Ları

SignalR 'nin bir "Hub" kavramı vardır. Her istemci bağlantısı ve Azure Işlevlerinden gönderilen her ileti, belirli bir hub 'ın kapsamına alınır. Hub 'ları, bağlantılarınızı ve iletilerinizi mantıksal ad alanlarına ayırmak için bir yol olarak kullanabilirsiniz.

## <a name="class-based-model"></a>Sınıf tabanlı model

Sınıf tabanlı model C# için ayrılmıştır. Sınıf tabanlı model ile tutarlı bir SignalR sunucu tarafı programlama deneyimine sahip olabilir. Aşağıdaki özelliklere sahiptir.

* Daha az yapılandırma işi: sınıf adı olarak kullanılır `HubName` , yöntem adı olarak kullanılır `Event` ve `Category` Yöntem adına göre otomatik olarak karar verilir.
* Auto parametresi bağlama: ne `ParameterNames` de öznitelik `[SignalRParameter]` gerekli değildir. Parametreler, Azure Function yönteminin bağımsız değişkenlerine sırayla otomatik olarak bağlanır.
* Uygun çıkış ve anlaşma deneyimi.

Aşağıdaki kodlar şu özellikleri göstermektedir:

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("negotiate")]
    public SignalRConnectionInfo Negotiate([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req)
    {
        return Negotiate(req.Headers["x-ms-signalr-user-id"], GetClaims(req.Headers["Authorization"]));
    }

    [FunctionName(nameof(OnConnected))]
    public async Task OnConnected([SignalRTrigger]InvocationContext invocationContext, ILogger logger)
    {
        await Clients.All.SendAsync(NewConnectionTarget, new NewConnection(invocationContext.ConnectionId));
        logger.LogInformation($"{invocationContext.ConnectionId} has connected");
    }

    [FunctionName(nameof(Broadcast))]
    public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        await Clients.All.SendAsync(NewMessageTarget, new NewMessage(invocationContext, message));
        logger.LogInformation($"{invocationContext.ConnectionId} broadcast {message}");
    }

    [FunctionName(nameof(OnDisconnected))]
    public void OnDisconnected([SignalRTrigger]InvocationContext invocationContext)
    {
    }
}
```

Sınıf tabanlı modelden yararlanmak isteyen tüm işlevlerin **Serverlesshub**'dan devralan sınıf yöntemi olması gerekir. Örnekteki sınıf adı `SignalRTestHub` hub adıdır.

### <a name="define-hub-method"></a>Hub yöntemini tanımla

Tüm Hub yöntemlerinin bir  bağımsız değişkeni `InvocationContext` olarak düzenlenmiş `[SignalRTrigger]` ve parametresiz Oluşturucu kullanması gerekir. Sonra **Yöntem adı** parametre **olayı** olarak değerlendirilir.

Varsayılan olarak, `category=messages` Yöntem adı dışında aşağıdaki adlardan biridir:

* **OnConnected**: olarak kabul edilir `category=connections, event=connected`
* **OnConnected**: olarak değerlendirildi `category=connections, event=disconnected`

### <a name="parameter-binding-experience"></a>Parametre bağlama deneyimi

Sınıf tabanlı modelde, `[SignalRParameter]` Tüm bağımsız değişkenler varsayılan olarak olarak işaretlendiğinden, `[SignalRParameter]` aşağıdaki durumlardan biri dışında, bu gerekli değildir:

* Bağımsız değişken bir bağlama özniteliği tarafından tasarlanmalıdır.
* Bağımsız değişkenin türü `ILogger` veya `CancellationToken`
* Bağımsız değişken özniteliğe göre düzenlenmiş `[SignalRIgnore]`

### <a name="negotiate-experience-in-class-based-model"></a>Sınıf tabanlı modelde anlaşma deneyimi

SignalR giriş bağlamasını kullanmak yerine `[SignalR]` , sınıf tabanlı modelde anlaşma daha esnek olabilir. Temel sınıfta `ServerlessHub` bir yöntemi vardır

```cs
SignalRConnectionInfo Negotiate(string userId = null, IList<Claim> claims = null, TimeSpan? lifeTime = null)
```

Bu özellikler Kullanıcı `userId` `claims` işlev yürütme sırasında veya öğesini özelleştirir.

## <a name="use-signalrfilterattribute"></a>`SignalRFilterAttribute` komutunu kullanma

Kullanıcı soyut sınıfı alabilir ve uygulayabilir `SignalRFilterAttribute` . İçinde özel durumlar oluşturulursa `FilterAsync` , `403 Forbidden` istemcilere geri gönderilir.

Aşağıdaki örnek, yalnızca ' ın çağrılmasını sağlayan bir müşteri filtresinin nasıl uygulanacağını gösterir `admin` `broadcast` .

```cs
[AttributeUsage(AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
internal class FunctionAuthorizeAttribute: SignalRFilterAttribute
{
    private const string AdminKey = "admin";

    public override Task FilterAsync(InvocationContext invocationContext, CancellationToken cancellationToken)
    {
        if (invocationContext.Claims.TryGetValue(AdminKey, out var value) &&
            bool.TryParse(value, out var isAdmin) &&
            isAdmin)
        {
            return Task.CompletedTask;
        }

        throw new Exception($"{invocationContext.ConnectionId} doesn't have admin role");
    }
}
```

İşlevi yetkilendirmek için özniteliğiyle yararlanın.

```cs
[FunctionAuthorize]
[FunctionName(nameof(Broadcast))]
public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
{
}
```

## <a name="client-development"></a>İstemci geliştirme

SignalR istemci uygulamaları, Azure SignalR hizmetine kolayca bağlanmak ve ileti almak için çeşitli dillerden birindeki SignalR istemci SDK 'sinden yararlanabilir.

### <a name="configuring-a-client-connection"></a>İstemci bağlantısı yapılandırma

SignalR hizmetine bağlanmak için, bir istemcinin aşağıdaki adımlardan oluşan başarılı bir bağlantı görüşmesini tamamlaması gerekir:

1. Geçerli bağlantı bilgilerini almak için yukarıda ele alınan *Negotiate* HTTP uç noktasına bir istek yapın
1. Hizmet uç noktası URL 'sini ve *Negotiate* uç noktasından alınan erişim belirtecini kullanarak SignalR hizmetine bağlanma

SignalR istemci SDK 'Ları, anlaşma anlaşmasını gerçekleştirmek için gereken mantığı zaten içeriyor. Negotiate uç noktasının URL 'sini, `negotiate` segmenti, SDK 'nın ' ye geçirin `HubConnectionBuilder` . JavaScript 'te bir örnek aşağıda verilmiştir:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Kural gereği, SDK otomatik olarak `/negotiate` URL 'ye ekler ve anlaşmayı başlatmak için onu kullanır.

> [!NOTE]
> JavaScript/TypeScript SDK bir tarayıcıda kullanıyorsanız, İşlev Uygulaması için [çıkış noktaları arası kaynak paylaşımı 'nı (CORS) etkinleştirmeniz](#enabling-cors) gerekir.

SignalR istemci SDK 'sını kullanma hakkında daha fazla bilgi için, dilinize yönelik belgelere bakın:

* [.NET Standard](/aspnet/core/signalr/dotnet-client)
* [JavaScript](/aspnet/core/signalr/javascript-client)
* [Java](/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>İstemciden hizmete ileti gönderme

SignalR kaynağınız için [yukarı akış](concept-upstream.md) yapılandırılmış ise, herhangi bir SignalR istemcisini kullanarak Istemciden Azure işlevlerinizi iletiler gönderebilirsiniz. JavaScript 'te bir örnek aşağıda verilmiştir:

```javascript
connection.send('method1', 'arg1', 'arg2');
```

## <a name="azure-functions-configuration"></a>Azure Işlevleri yapılandırması

Azure SignalR hizmeti ile tümleştirilen Azure Işlevi uygulamaları, [sürekli dağıtım](../azure-functions/functions-continuous-deployment.md), [ZIP dağıtımı](../azure-functions/deployment-zip-push.md)ve [paketten Çalıştır](../azure-functions/run-functions-from-deployment-package.md)gibi teknikler kullanılarak tipik bir Azure işlev uygulaması gibi dağıtılabilir.

Ancak, SignalR hizmeti bağlamalarını kullanan uygulamalar için birkaç özel dikkat edilmesi gerekir. İstemci bir tarayıcıda çalışıyorsa CORS 'nin etkinleştirilmesi gerekir. Uygulama kimlik doğrulaması gerektiriyorsa, Negotiate uç noktasını App Service kimlik doğrulamasıyla tümleştirebilirsiniz.

### <a name="enabling-cors"></a>CORS etkinleştiriliyor

JavaScript/TypeScript istemcisi, bağlantı anlaşmasını başlatmak için Negotiate işlevine HTTP istekleri yapar. İstemci uygulaması, Azure Işlev uygulamasından farklı bir etki alanında barındırılıyorsa, Işlev uygulamasında çıkış noktaları arası kaynak paylaşımı (CORS) etkinleştirilmelidir veya tarayıcı istekleri engeller.

#### <a name="localhost"></a>E

Yerel bilgisayarınızda Işlev uygulamasını çalıştırırken `Host` CORS 'yi etkinleştirmek için *local.settings.js* bölümüne bir bölüm ekleyebilirsiniz. `Host`Bölümünde iki özellik ekleyin:

* `CORS` -istemci uygulamanın kaynağı olan temel URL 'YI girin
* `CORSCredentials` -Bunu `true` "withCredentials" isteklerine izin verecek şekilde ayarlayın

Örnek:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>Bulut-Azure Işlevleri CORS

Azure Işlev uygulamasında CORS 'yi etkinleştirmek için, Azure portal Işlev uygulamanızın *platform özellikleri* SEKMESI altındaki CORS yapılandırma ekranına gidin.

> [!NOTE]
> CORS yapılandırması henüz Azure Işlevleri Linux tüketim planında kullanılamıyor. CORS 'yi etkinleştirmek için [Azure API Management](#cloud---azure-api-management) kullanın.

Erişim-denetim-Izin-kimlik bilgileri ile CORS, SignalR istemcisinin Negotiate işlevini çağırması için kimlik bilgilerinin etkinleştirilmesi gerekir. Etkinleştirmek için onay kutusunu işaretleyin.

*Izin verilen* kaynaklar bölümünde, Web uygulamanızın kaynak temel URL 'sine sahip bir giriş ekleyin.

![CORS 'yi yapılandırma](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Bulut-Azure API Management

Azure API Management, mevcut arka uç hizmetlerine yetenekler ekleyen bir API ağ geçidi sağlar. Bunu, işlev uygulamanıza CORS eklemek için kullanabilirsiniz. Bu, eylem başına ödeme fiyatlandırmasına ve aylık ücretsiz erişime sahip bir tüketim katmanı sunar.

[Azure işlevi uygulamasının nasıl içeri aktarılacağı](../api-management/import-function-app-as-api.md)hakkında bilgi edinmek için API Management belgelerine bakın. İçeri aktarıldıktan sonra, erişim-denetim-Izin verme kimlik bilgileri desteğiyle CORS 'yi etkinleştirmek için bir gelen ilkesi ekleyebilirsiniz.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

SignalR istemcilerinizi API Management URL 'sini kullanacak şekilde yapılandırın.

### <a name="using-app-service-authentication"></a>App Service kimlik doğrulaması kullanma

Azure Işlevleri, Facebook, Twitter, Microsoft hesabı, Google ve Azure Active Directory gibi popüler sağlayıcıları destekleyerek yerleşik kimlik doğrulaması içerir. Bu özellik, bir Kullanıcı KIMLIĞINDE kimlik doğrulaması yapılmış Azure SignalR hizmeti bağlantıları oluşturmak için *Signalrconnectionınfo* bağlamasıyla tümleştirilebilir. Uygulamanız, bu kullanıcı KIMLIĞINI hedefleyen *SignalR* çıkış bağlamasını kullanarak ileti gönderebilir.

Azure portal, Işlev uygulamanızın *platform özellikleri* sekmesinde *kimlik doğrulama/yetkilendirme* ayarları penceresini açın. Seçtiğiniz kimlik sağlayıcısını kullanarak kimlik doğrulaması yapılandırmak için [App Service kimlik doğrulama](../app-service/overview-authentication-authorization.md) belgelerini izleyin.

Yapılandırıldıktan sonra kimliği doğrulanmış HTTP istekleri, `x-ms-client-principal-name` `x-ms-client-principal-id` sırasıyla kimliği doğrulanmış kimliğin Kullanıcı adı ve Kullanıcı kimliği bilgilerini içerir.

Kimliği doğrulanmış bağlantılar oluşturmak için, *Signalrconnectionınfo* bağlama yapılandırmanızda bu üst bilgileri kullanabilirsiniz. Üstbilgiyi kullanan örnek bir C# Negotiate işlevi aşağıda verilmiştir `x-ms-client-principal-id` .

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

Daha sonra, `UserId` bir SignalR iletisinin özelliğini ayarlayarak bu kullanıcıya iletiler gönderebilirsiniz.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Diğer diller hakkında daha fazla bilgi için bkz. Azure için [Azure SignalR hizmeti bağlamaları](../azure-functions/functions-bindings-signalr-service.md) başvurusu.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Işlevleri 'ni kullanarak sunucusuz SignalR hizmeti uygulamaları geliştirmeyi ve yapılandırmayı öğrendiniz. [SignalR hizmetine genel bakış sayfasında](index.yml)hızlı başlayan veya öğreticilerden birini kullanarak kendiniz bir uygulama oluşturmayı deneyin.
