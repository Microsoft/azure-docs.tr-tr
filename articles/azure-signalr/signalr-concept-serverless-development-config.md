---
title: Azure Işlevleri uygulamasını geliştirme & yapılandırma uygulaması-Azure SignalR
description: Azure Işlevleri ve Azure SignalR hizmeti kullanılarak sunucusuz gerçek zamanlı uygulamalar geliştirmeye ve yapılandırmaya ilişkin ayrıntılar
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 68ada90699fe9a9db6faeb32a04e8eb02c176944
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157659"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure SignalR hizmeti ile Azure Işlevleri geliştirme ve yapılandırma

Azure Işlevleri uygulamaları, gerçek zamanlı yetenekler eklemek için [Azure SignalR hizmeti bağlamalarından](../azure-functions/functions-bindings-signalr-service.md) faydalanabilir. İstemci uygulamaları, Azure SignalR hizmetine bağlanmak ve gerçek zamanlı iletiler almak için birkaç dilde sunulan istemci SDK 'larını kullanır.

Bu makalede, SignalR hizmeti ile tümleştirilmiş bir Azure Işlev uygulaması geliştirmeye ve yapılandırmaya yönelik kavramlar açıklanmaktadır.

## <a name="signalr-service-configuration"></a>SignalR hizmeti yapılandırması

Azure SignalR hizmeti, farklı modlarda yapılandırılabilir. Azure Işlevleri ile kullanıldığında hizmetin *sunucusuz* modda yapılandırılması gerekir.

Azure portal, SignalR hizmeti kaynağınızın *Ayarlar* sayfasını bulun. *Hizmet modunu* *sunucusuz*olarak ayarlayın.

![SignalR hizmeti modu](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Işlevleri geliştirme

Azure Işlevleri ve Azure SignalR hizmeti ile oluşturulan sunucusuz gerçek zamanlı bir uygulama, genellikle iki Azure Işlevi gerektirir:

* İstemcinin geçerli bir SignalR hizmeti erişim belirteci ve hizmet uç noktası URL 'SI almak için çağırdığı "anlaş" işlevi
* İleti gönderen veya grup üyeliğini yöneten bir veya daha fazla işlev

### <a name="negotiate-function"></a>Negotiate işlevi

İstemci uygulaması, Azure SignalR hizmetine bağlanmak için geçerli bir erişim belirteci gerektirir. Erişim belirteci, belirli bir kullanıcı KIMLIĞI için anonim veya kimliği doğrulanmış olabilir. Sunucusuz SignalR hizmeti uygulamaları, bir belirteç ve SignalR hizmeti uç noktası URL 'SI gibi diğer bağlantı bilgilerini almak için "Negotiate" adlı bir HTTP uç noktası gerektirir.

Bağlantı bilgileri nesnesini oluşturmak için HTTP ile tetiklenen bir Azure Işlevi ve *Signalrconnectionınfo* giriş bağlaması kullanın. İşlevin `/negotiate`biten bir HTTP yolu olması gerekir.

Negotiate işlevinin nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [ *Signalrconnectionınfo* giriş bağlama başvurusu](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Kimliği doğrulanmış bir belirteç oluşturma hakkında bilgi edinmek için [App Service kimlik doğrulaması kullanma](#using-app-service-authentication)konusuna bakın.

### <a name="sending-messages-and-managing-group-membership"></a>İleti gönderme ve grup üyeliğini yönetme

Azure SignalR hizmetine bağlı istemcilere ileti göndermek için *SignalR* çıkış bağlamasını kullanın. İletileri tüm istemcilere yayınlayabilirsiniz veya belirli bir kullanıcı KIMLIĞI ile kimliği doğrulanmış veya belirli bir gruba eklenmiş bir istemci alt kümesine gönderebilirsiniz.

Kullanıcılar, bir veya daha fazla gruba eklenebilir. Ayrıca, Grup gruplarına/gruplardan Kullanıcı eklemek veya kaldırmak için *SignalR* çıktı bağlamasını da kullanabilirsiniz.

Daha fazla bilgi için bkz. [ *SignalR* çıkış bağlama başvurusu](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>SignalR hub 'Ları

SignalR 'nin bir "Hub" kavramı vardır. Her istemci bağlantısı ve Azure Işlevlerinden gönderilen her ileti, belirli bir hub 'ın kapsamına alınır. Hub 'ları, bağlantılarınızı ve iletilerinizi mantıksal ad alanlarına ayırmak için bir yol olarak kullanabilirsiniz.

## <a name="client-development"></a>İstemci geliştirme

SignalR istemci uygulamaları, Azure SignalR hizmetine kolayca bağlanmak ve ileti almak için çeşitli dillerden birindeki SignalR istemci SDK 'sinden yararlanabilir.

### <a name="configuring-a-client-connection"></a>İstemci bağlantısı yapılandırma

SignalR hizmetine bağlanmak için, bir istemcinin aşağıdaki adımlardan oluşan başarılı bir bağlantı görüşmesini tamamlaması gerekir:

1. Geçerli bağlantı bilgilerini almak için yukarıda ele alınan *Negotiate* HTTP uç noktasına bir istek yapın
1. Hizmet uç noktası URL 'sini ve *Negotiate* uç noktasından alınan erişim belirtecini kullanarak SignalR hizmetine bağlanma

SignalR istemci SDK 'Ları, anlaşma anlaşmasını gerçekleştirmek için gereken mantığı zaten içeriyor. Anlaşma uç noktasının URL 'sini, `negotiate` segmentini, SDK 'nın `HubConnectionBuilder`olarak geçirin. JavaScript 'te bir örnek aşağıda verilmiştir:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Kural gereği, SDK otomatik olarak URL 'ye `/negotiate` ekler ve anlaşmayı başlatmak için onu kullanır.

> [!NOTE]
> JavaScript/TypeScript SDK bir tarayıcıda kullanıyorsanız, İşlev Uygulaması için [çıkış noktaları arası kaynak paylaşımı 'nı (CORS) etkinleştirmeniz](#enabling-cors) gerekir.

SignalR istemci SDK 'sını kullanma hakkında daha fazla bilgi için, dilinize yönelik belgelere bakın:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>İstemciden hizmete ileti gönderme

SignalR SDK 'Sı, istemci uygulamalarının bir SignalR hub 'ında arka uç mantığını çağırmasına izin verse de, Azure Işlevleri ile SignalR hizmetini kullandığınızda bu işlevsellik henüz desteklenmez. Azure Işlevleri 'ni çağırmak için HTTP isteklerini kullanın.

## <a name="azure-functions-configuration"></a>Azure Işlevleri yapılandırması

Azure SignalR hizmeti ile tümleştirilen Azure Işlevi uygulamaları, [sürekli dağıtım](../azure-functions/functions-continuous-deployment.md), [ZIP dağıtımı](../azure-functions/deployment-zip-push.md)ve [paketten Çalıştır](../azure-functions/run-functions-from-deployment-package.md)gibi teknikler kullanılarak tipik bir Azure işlev uygulaması gibi dağıtılabilir.

Ancak, SignalR hizmeti bağlamalarını kullanan uygulamalar için birkaç özel dikkat edilmesi gerekir. İstemci bir tarayıcıda çalışıyorsa CORS 'nin etkinleştirilmesi gerekir. Uygulama kimlik doğrulaması gerektiriyorsa, Negotiate uç noktasını App Service kimlik doğrulamasıyla tümleştirebilirsiniz.

### <a name="enabling-cors"></a>CORS etkinleştiriliyor

JavaScript/TypeScript istemcisi, bağlantı anlaşmasını başlatmak için Negotiate işlevine HTTP istekleri yapar. İstemci uygulaması, Azure Işlev uygulamasından farklı bir etki alanında barındırılıyorsa, Işlev uygulamasında çıkış noktaları arası kaynak paylaşımı (CORS) etkinleştirilmelidir veya tarayıcı istekleri engeller.

#### <a name="localhost"></a>E

Yerel bilgisayarınızda Işlev uygulamasını çalıştırırken CORS 'yi etkinleştirmek için *yerel. Settings. JSON* öğesine bir `Host` bölümü ekleyebilirsiniz. `Host` bölümünde iki özellik ekleyin:

* `CORS`-istemci uygulamanın kaynağı olan temel URL 'YI girin
* `CORSCredentials`, "withCredentials" isteklerine izin vermek için bunu `true` ayarla

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

Yapılandırıldıktan sonra kimliği doğrulanmış HTTP istekleri, sırasıyla kimliği doğrulanmış kimliğin Kullanıcı adını ve kullanıcı KIMLIĞINI içeren `x-ms-client-principal-name` ve `x-ms-client-principal-id` üst bilgilerini içerir.

Kimliği doğrulanmış bağlantılar oluşturmak için, *Signalrconnectionınfo* bağlama yapılandırmanızda bu üst bilgileri kullanabilirsiniz. `x-ms-client-principal-id` üst bilgisini kullanan C# örnek bir Negotiate işlevi aşağıda verilmiştir.

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

Daha sonra bir SignalR iletisinin `UserId` özelliğini ayarlayarak bu kullanıcıya iletiler gönderebilirsiniz.

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