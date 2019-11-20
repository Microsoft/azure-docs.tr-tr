---
title: Azure Işlevleri SignalR hizmeti bağlamaları
description: Azure Işlevleri ile SignalR hizmeti bağlamalarını nasıl kullanacağınızı anlayın.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
editor: ''
tags: ''
keywords: Azure işlevleri, İşlevler, olay işleme dinamik işlem, sunucusuz mimari
ms.service: azure-functions
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 82bbd728bfd51595f0b6eacf344bc95ea5db1ea8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086169"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure İşlevleri için SignalR Service bağlamaları

Bu makalede, Azure Işlevleri 'nde SignalR hizmeti bağlamaları kullanılarak [Azure SignalR hizmetine](https://azure.microsoft.com/services/signalr-service/) bağlı istemcilere nasıl kimlik doğrulaması yapılacağını ve gerçek zamanlı iletilerin gönderilmesi açıklanmaktadır. Azure Işlevleri, SignalR hizmeti için giriş ve çıkış bağlamalarını destekler.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Paketler - 2.x işlevleri

SignalR hizmeti bağlamaları [Microsoft. Azure. WebJobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet paketi, sürüm 1. * içinde verilmiştir. Paketin kaynak kodu, [Azure-Functions-signalrservice-Extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub deposunda bulunur.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java ek açıklamaları

Java işlevlerinde SignalR hizmeti ek açıklamalarını kullanmak için, POM. xml dosyanıza *Azure-Functions-Java-Library-SignalR* yapıtı (sürüm 1,0 veya üzeri) bir bağımlılık eklemeniz gerekir.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Java 'da SignalR hizmeti bağlamalarını kullanmak için Azure Functions Core Tools (ana bilgisayar sürümü 2.0.12332) sürüm 2.4.419 veya üstünü kullandığınızdan emin olun.

## <a name="using-signalr-service-with-azure-functions"></a>Azure Işlevleri ile SignalR hizmetini kullanma

SignalR hizmetini ve Azure Işlevlerini birlikte yapılandırma ve kullanma hakkında ayrıntılı bilgi için Azure [SignalR hizmeti Ile Azure işlevleri geliştirme ve yapılandırma](../azure-signalr/signalr-concept-serverless-development-config.md)konusuna bakın.

## <a name="signalr-connection-info-input-binding"></a>SignalR bağlantı bilgileri giriş bağlama

Bir istemcinin Azure SignalR hizmetine bağlanabilmesi için, hizmet uç noktası URL 'sini ve geçerli bir erişim belirtecini alması gerekir. *Signalrconnectionınfo* giriş bağlaması, SignalR hizmeti uç noktası URL 'sini ve hizmete bağlanmak için kullanılan geçerli bir belirteci üretir. Belirteç zaman sınırlı olduğundan ve belirli bir kullanıcının kimlik doğrulaması için kullanılabilir olduğundan, belirteci önbelleğe almalısınız veya istemciler arasında paylaşamazsınız. Bu bağlamayı kullanan bir HTTP tetikleyicisi, istemciler tarafından bağlantı bilgilerini almak için kullanılabilir.

Dile özgü örneğe bakın:

* [2.xC#](#2x-c-input-examples)
* [2. x JavaScript](#2x-javascript-input-examples)
* [2. x Java](#2x-java-input-examples)

Bu bağlamanın bir SignalR istemci SDK 'Sı tarafından tüketilen bir "Negotiate" işlevi oluşturmak için nasıl kullanıldığı hakkında daha fazla bilgi için, SignalR hizmeti kavramları belgelerindeki [Azure işlevleri geliştirme ve yapılandırma makalesine](../azure-signalr/signalr-concept-serverless-development-config.md) bakın.

### <a name="2x-c-input-examples"></a>2. x C# giriş örnekleri

Aşağıdaki örnek, giriş bağlamasını kullanarak SignalR bağlantı bilgilerini alan ve http üzerinden döndüren bir [ C# işlevi](functions-dotnet-class-library.md) gösterir.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Kimliği doğrulanmış belirteçler

İşlev kimliği doğrulanmış bir istemci tarafından tetikleniyorsa, oluşturulan belirtece bir kullanıcı KIMLIĞI talebi ekleyebilirsiniz. [App Service kimlik doğrulaması](../app-service/overview-authentication-authorization.md)kullanarak bir işlev uygulamasına kolayca kimlik doğrulaması ekleyebilirsiniz.

App Service kimlik doğrulaması, sırasıyla kimliği `x-ms-client-principal-id` doğrulanmış `x-ms-client-principal-name` kullanıcının istemci asıl kimliğini ve adını içeren ve adlı HTTP üstbilgilerini ayarlar. Bağlama ifadesini kullanarak, `UserId` bağlama [ifadesi](./functions-bindings-expressions-patterns.md)ile herhangi bir başlıktaki değere bağlamayı ayarlayabilirsiniz: `{headers.x-ms-client-principal-id}` veya. `{headers.x-ms-client-principal-name}` 

```cs
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

### <a name="2x-javascript-input-examples"></a>2. x JavaScript girişi örnekleri

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir SignalR bağlantı bilgileri giriş bağlamasını ve bağlantı bilgilerini döndürmek için bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir.

İşte, *function. JSON* dosyasındaki veri bağlama:

Örnek Function. JSON:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript kod aşağıdaki gibidir:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>Kimliği doğrulanmış belirteçler

İşlev kimliği doğrulanmış bir istemci tarafından tetikleniyorsa, oluşturulan belirtece bir kullanıcı KIMLIĞI talebi ekleyebilirsiniz. [App Service kimlik doğrulaması](../app-service/overview-authentication-authorization.md)kullanarak bir işlev uygulamasına kolayca kimlik doğrulaması ekleyebilirsiniz.

App Service kimlik doğrulaması, sırasıyla kimliği `x-ms-client-principal-id` doğrulanmış `x-ms-client-principal-name` kullanıcının istemci asıl kimliğini ve adını içeren ve adlı HTTP üstbilgilerini ayarlar. Bağlama ifadesini kullanarak, `userId` bağlama [ifadesi](./functions-bindings-expressions-patterns.md)ile herhangi bir başlıktaki değere bağlamayı ayarlayabilirsiniz: `{headers.x-ms-client-principal-id}` veya. `{headers.x-ms-client-principal-name}` 

Örnek Function. JSON:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript kod aşağıdaki gibidir:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>2. x Java giriş örnekleri

Aşağıdaki örnek, giriş bağlamayı kullanarak SignalR bağlantı bilgilerini alan ve HTTP üzerinden döndüren bir [Java işlevini](functions-reference-java.md) gösterir.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Kimliği doğrulanmış belirteçler

İşlev kimliği doğrulanmış bir istemci tarafından tetikleniyorsa, oluşturulan belirtece bir kullanıcı KIMLIĞI talebi ekleyebilirsiniz. [App Service kimlik doğrulaması](../app-service/overview-authentication-authorization.md)kullanarak bir işlev uygulamasına kolayca kimlik doğrulaması ekleyebilirsiniz.

App Service kimlik doğrulaması, sırasıyla kimliği `x-ms-client-principal-id` doğrulanmış `x-ms-client-principal-name` kullanıcının istemci asıl kimliğini ve adını içeren ve adlı HTTP üstbilgilerini ayarlar. Bağlama ifadesini kullanarak, `UserId` bağlama [ifadesi](./functions-bindings-expressions-patterns.md)ile herhangi bir başlıktaki değere bağlamayı ayarlayabilirsiniz: `{headers.x-ms-client-principal-id}` veya. `{headers.x-ms-client-principal-name}`

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

## <a name="signalr-output-binding"></a>SignalR çıkış bağlama

Azure SignalR hizmetini kullanarak bir veya daha fazla ileti göndermek için *SignalR* çıkış bağlamasını kullanın. Tüm bağlı istemcilere bir ileti yayınlayabilirsiniz veya yalnızca belirli bir kullanıcıya kimliği doğrulanmış bağlı istemcilere yayınlayabilirsiniz.

Ayrıca, bir kullanıcının ait olduğu grupları yönetmek için de kullanabilirsiniz.

Dile özgü örneğe bakın:

* [2.xC#](#2x-c-send-message-output-examples)
* [2. x JavaScript](#2x-javascript-send-message-output-examples)
* [2. x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2. x C# ileti gönderme çıkışı örnekleri

#### <a name="broadcast-to-all-clients"></a>Tüm istemcilere yayınla

Aşağıdaki örnek, tüm bağlı istemcilere çıkış bağlamayı kullanarak bir ileti gönderen bir [ C# işlevi](functions-dotnet-class-library.md) gösterir. , `Target` Her istemcide çağrılacak yöntemin adıdır. `Arguments` Özelliği, istemci metoduna geçirilecek sıfır veya daha fazla nesne dizisidir.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>Kullanıcıya gönder

SignalR iletisinin `UserId` özelliğini ayarlayarak yalnızca bir kullanıcıya kimliği doğrulanmış bağlantılara ileti gönderebilirsiniz.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

#### <a name="send-to-a-group"></a>Bir gruba gönder

SignalR iletisinin `GroupName` özelliğini ayarlayarak yalnızca bir gruba eklenmiş bağlantılara bir ileti gönderebilirsiniz.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

### <a name="2x-c-group-management-output-examples"></a>2. x C# Grup yönetimi çıkış örnekleri

SignalR hizmeti kullanıcıların gruplara eklenmesine izin verir. İletiler daha sonra bir gruba gönderilebilir. Bir kullanıcının grup üyeliğini `SignalRGroupAction` yönetmek için `SignalR` çıkış bağlaması ile sınıfını kullanabilirsiniz.

#### <a name="add-user-to-a-group"></a>Gruba kullanıcı ekleme

Aşağıdaki örnek bir gruba bir kullanıcı ekler.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Kullanıcıyı bir gruptan Kaldır

Aşağıdaki örnek bir kullanıcıyı bir gruptan kaldırır.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> `ClaimsPrincipal` Doğru bir şekilde bağlanmasını sağlamak için Azure işlevleri 'nde kimlik doğrulama ayarlarını yapılandırmış olmanız gerekir.

### <a name="2x-javascript-send-message-output-examples"></a>2. x JavaScript ileti çıkış örnekleri gönder

#### <a name="broadcast-to-all-clients"></a>Tüm istemcilere yayınla

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir SignalR çıkış bağlamasını ve Azure SignalR hizmeti ile ileti göndermek için bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. Çıkış bağlamayı bir veya daha fazla SignalR iletisi dizisine ayarlayın. Bir SignalR iletisi, her istemcide `target` çağrılacak yöntemin adını `arguments` ve istemci metoduna bağımsız değişken olarak geçirilecek nesne dizisi olan bir özelliği içerir.

İşte, *function. JSON* dosyasındaki veri bağlama:

Örnek Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

JavaScript kod aşağıdaki gibidir:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>Kullanıcıya gönder

SignalR iletisinin `userId` özelliğini ayarlayarak yalnızca bir kullanıcıya kimliği doğrulanmış bağlantılara ileti gönderebilirsiniz.

*function. JSON* aynı kalır. JavaScript kod aşağıdaki gibidir:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-group"></a>Bir gruba gönder

SignalR iletisinin `groupName` özelliğini ayarlayarak yalnızca bir gruba eklenmiş bağlantılara bir ileti gönderebilirsiniz.

*function. JSON* aynı kalır. JavaScript kod aşağıdaki gibidir:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

### <a name="2x-javascript-group-management-output-examples"></a>2. x JavaScript Grup yönetimi çıkış örnekleri

SignalR hizmeti kullanıcıların gruplara eklenmesine izin verir. İletiler daha sonra bir gruba gönderilebilir. Bir kullanıcının grup üyeliğini `SignalR` yönetmek için çıkış bağlamayı kullanabilirsiniz.

#### <a name="add-user-to-a-group"></a>Gruba kullanıcı ekleme

Aşağıdaki örnek bir gruba bir kullanıcı ekler.

*function. JSON*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index. js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>Kullanıcıyı bir gruptan Kaldır

Aşağıdaki örnek bir kullanıcıyı bir gruptan kaldırır.

*function. JSON*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index. js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2. x Java ileti gönderme çıkışı örnekleri

#### <a name="broadcast-to-all-clients"></a>Tüm istemcilere yayınla

Aşağıdaki örnekte, tüm bağlı istemcilere çıkış bağlamayı kullanarak bir ileti gönderen bir [Java işlevi](functions-reference-java.md) gösterilmektedir. , `target` Her istemcide çağrılacak yöntemin adıdır. `arguments` Özelliği, istemci metoduna geçirilecek sıfır veya daha fazla nesne dizisidir.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-user"></a>Kullanıcıya gönder

SignalR iletisinin `userId` özelliğini ayarlayarak yalnızca bir kullanıcıya kimliği doğrulanmış bağlantılara ileti gönderebilirsiniz.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-group"></a>Bir gruba gönder

SignalR iletisinin `groupName` özelliğini ayarlayarak yalnızca bir gruba eklenmiş bağlantılara bir ileti gönderebilirsiniz.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

### <a name="2x-java-group-management-output-examples"></a>2. x Java Grup yönetimi çıkış örnekleri

SignalR hizmeti kullanıcıların gruplara eklenmesine izin verir. İletiler daha sonra bir gruba gönderilebilir. Bir kullanıcının grup üyeliğini `SignalRGroupAction` yönetmek için `SignalROutput` çıkış bağlaması ile sınıfını kullanabilirsiniz.

#### <a name="add-user-to-a-group"></a>Gruba kullanıcı ekleme

Aşağıdaki örnek bir gruba bir kullanıcı ekler.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>Kullanıcıyı bir gruptan Kaldır

Aşağıdaki örnek bir kullanıcıyı bir gruptan kaldırır.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

## <a name="configuration"></a>Yapılandırma

### <a name="signalrconnectioninfo"></a>Signalrconnectionınfo

Aşağıdaki tabloda ayarladığınız bağlama yapılandırma özelliklerini açıklayan *function.json* dosya ve `SignalRConnectionInfo` özniteliği.

|Function.JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type**|| Ayarlanmalıdır `signalRConnectionInfo`.|
|**direction**|| Ayarlanmalıdır `in`.|
|**name**|| Bağlantı bilgisi nesnesi için işlev kodunda kullanılan değişken adı. |
|**hubName**|**HubName**| Bu değer, bağlantı bilgilerinin oluşturulduğu SignalR hub 'ının adına ayarlanmalıdır.|
|**userId**|**UserID**| İsteğe bağlı: Erişim anahtarı belirtecinde ayarlanacak Kullanıcı tanımlayıcı talebinin değeri. |
|**connectionStringSetting**|**connectionStringSetting**| SignalR hizmeti bağlantı dizesini içeren uygulama ayarının adı (varsayılan olarak "AzureSignalRConnectionString" olarak belirlenmiştir) |

### <a name="signalr"></a>SignalR

Aşağıdaki tabloda ayarladığınız bağlama yapılandırma özelliklerini açıklayan *function.json* dosya ve `SignalR` özniteliği.

|Function.JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type**|| Ayarlanmalıdır `signalR`.|
|**direction**|| Ayarlanmalıdır `out`.|
|**name**|| Bağlantı bilgisi nesnesi için işlev kodunda kullanılan değişken adı. |
|**hubName**|**HubName**| Bu değer, bağlantı bilgilerinin oluşturulduğu SignalR hub 'ının adına ayarlanmalıdır.|
|**connectionStringSetting**|**connectionStringSetting**| SignalR hizmeti bağlantı dizesini içeren uygulama ayarının adı (varsayılan olarak "AzureSignalRConnectionString" olarak belirlenmiştir) |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure SignalR Hizmeti ile Azure İşlevleri geliştirme ve yapılandırma](../azure-signalr/signalr-concept-serverless-development-config.md)
