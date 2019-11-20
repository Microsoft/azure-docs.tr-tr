---
title: Azure Işlevleri için Notification Hubs bağlamaları
description: Azure 'da Azure Notification Hub bağlamasını nasıl kullanacağınızı anlayın.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure işlevleri, işlevler, olay işleme, dinamik işlem, sunucusuz mimari
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: cde565fbafec7f1209d0c65d6f3ebc121f38e6f5
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991388"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Azure Işlevleri için çıkış bağlamasını Notification Hubs

Bu makalede Azure Işlevleri 'nde [azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) bağlamaları kullanılarak anında iletme bildirimlerinin nasıl gönderileceği açıklanır. Azure Işlevleri Notification Hubs için çıkış bağlamalarını destekler.

Kullanmak istediğiniz platform bildirimleri hizmeti (PNS) için Azure Notification Hubs yapılandırılmalıdır. İstemci uygulamanızda Notification Hubs anında iletme bildirimleri alma hakkında bilgi edinmek için bkz. [Notification Hubs kullanmaya](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) başlama ve sayfanın üst kısmındaki açılan listeden hedef istemci platformunuzu seçme.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Google, [Firebase Cloud Messaging (FCM) lehine Google Cloud Messaging (GCM) kullanım dışıdır](https://developers.google.com/cloud-messaging/faq). Bu çıkış bağlaması FCM 'yi desteklemez. FCM kullanarak bildirim göndermek için, doğrudan işlevinizdeki [Firebase API](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) 'sini kullanın veya [şablon bildirimleri](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)kullanın.

## <a name="packages---functions-1x"></a>Paketler-Işlevler 1. x

Notification Hubs bağlamaları [Microsoft. Azure. WebJobs. Extensions. Notificationhub](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet paketi, sürüm 1. x içinde verilmiştir. Paketin kaynak kodu, [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) GitHub deposunda bulunur.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Paketler-Işlevler 2. x

Bu bağlama 2. x Işlevleri 'nde kullanılamaz.

## <a name="example---template"></a>Örnek-şablon

Göndereceğiniz bildirimler yerel bildirimler veya [şablon bildirimleri](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)olabilir. Yerel bildirimler, çıkış bağlamasının `platform` özelliğinde yapılandırıldığı şekilde belirli bir istemci platformunu hedefler. Şablon bildirimi, birden çok platformu hedeflemek için kullanılabilir.   

Dile özgü örneğe bakın:

* [C#betik genişletme parametresi](#c-script-template-example---out-parameter)
* [C#Betik-zaman uyumsuz](#c-script-template-example---asynchronous)
* [C#betik-JSON](#c-script-template-example---json)
* [C#betik kitaplığı türleri](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C#betik şablonu örnek genişletme parametresi

Bu örnek, şablonda `message` yer tutucu içeren bir [şablon kaydı](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) için bir bildirim gönderir.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>C#betik şablonu örneği-zaman uyumsuz

Zaman uyumsuz kod kullanıyorsanız, çıkış parametrelerine izin verilmez. Bu durumda, şablon bildiriinizi döndürmek için `IAsyncCollector` kullanın. Aşağıdaki kod, Yukarıdaki kodun zaman uyumsuz bir örneğidir. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>C#betik şablonu örneği-JSON

Bu örnek, geçerli bir JSON dizesi kullanarak şablonda `message` yer tutucu içeren [şablon kaydı](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) için bir bildirim gönderir.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C#betik şablonu örneği-kitaplık türleri

Bu örnek, [Microsoft Azure Notification Hubs kitaplığında](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)tanımlanan türlerin nasıl kullanılacağını gösterir. 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>F#Şablon örneği

Bu örnek, `location` ve `message`içeren bir [şablon kaydı](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) için bir bildirim gönderir.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>JavaScript şablon örneği

Bu örnek, `location` ve `message`içeren bir [şablon kaydı](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) için bir bildirim gönderir.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Örnek-APNS yerel

Bu C# betik örneği, yerel bir APNs bildiriminin nasıl gönderileceğini gösterir. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Örnek-WNS Native

Bu C# betik örneği, yerel WNS bildirim bildirimi göndermek Için [Microsoft Azure Notification Hubs kitaplığında](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) tanımlanan türlerin nasıl kullanılacağını gösterir. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [notificationhub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) özniteliğini kullanın.

Özniteliğin Oluşturucu parametreleri ve özellikleri [yapılandırma](#configuration) bölümünde açıklanmıştır.

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `NotificationHub` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır:

|function. JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type** |Yok| `notificationHub`olarak ayarlanmalıdır. |
|**direction** |Yok| `out`olarak ayarlanmalıdır. | 
|**ada** |Yok| Bildirim Hub 'ı iletisi için işlev kodunda kullanılan değişken adı. |
|**tagExpression** |**TagExpression** | Etiket ifadeleri, bu bildirimlerin etiket ifadesiyle eşleşen bildirimleri almak için kayıtlı bir cihaz kümesine teslim edilmesini belirtmenize olanak tanır.  Daha fazla bilgi için bkz. [Yönlendirme ve etiket ifadeleri](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Azure portal Bildirim Hub 'ı kaynağının adı. |
|**bağlanma** | **ConnectionStringSetting** | Notification Hubs bağlantı dizesi içeren bir uygulama ayarının adı.  Bağlantı dizesinin, Bildirim Hub 'ınız için *Defaultfullsharedaccesssignature* değerine ayarlanması gerekir. Bu makalenin ilerleyen kısımlarında [bağlantı dizesi kurulumuna](#connection-string-setup) bakın.|
|**platformunun** | **Platformunun** | Platform özelliği, bildirimin hedeflediği istemci platformunu gösterir. Varsayılan olarak, platform özelliği çıkış bağlamalarından atlanırsa, Azure Notification Hub 'ında yapılandırılmış herhangi bir platformu hedeflemek için şablon bildirimleri kullanılabilir. Şablonları genel olarak kullanma hakkında daha fazla bilgi için, bkz. [Şablonlar](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Ayarlandığında, **Platform** aşağıdaki değerlerden biri olmalıdır: <ul><li><code>apns</code>&mdash;Apple Anında İletilen Bildirim Servisi. APNS için Bildirim Hub 'ı yapılandırma ve bir istemci uygulamasında bildirimi alma hakkında daha fazla bilgi için bkz. [Azure Notification Hubs Ile iOS 'a anında iletme bildirimleri gönderme](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). ADM için Bildirim Hub 'ını yapılandırma ve bir bir bir uygulama için bildirim alma hakkında daha fazla bilgi için, bkz. [lütfen Notification Hubs kullanmaya](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)başlama.</li><li>Windows platformlarını hedefleyen [Windows Push Bildirim Hizmetleri](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview)&mdash;<code>wns</code>. Windows Phone 8,1 ve üzeri, WNS tarafından da desteklenir. Daha fazla bilgi için bkz. [Windows Evrensel platform uygulamaları için Notification Hubs kullanmaya](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)başlama.</li><li>&mdash;[Microsoft anında bildirim hizmeti](/previous-versions/windows/apps/ff402558(v=vs.105))<code>mpns</code>. Bu platform Windows Phone 8 ve önceki Windows Phone platformları destekler. Daha fazla bilgi için bkz. [Azure Notification Hubs ile anında iletme bildirimleri gönderme Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>function. JSON dosyası örneği

Bir *function. JSON* dosyasında Notification Hubs bağlamaya bir örnek aşağıda verilmiştir.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Bağlantı dizesi kurulumu

Bir Bildirim Hub 'ı çıkış bağlamayı kullanmak için, Hub için bağlantı dizesini yapılandırmanız gerekir. Mevcut bir Bildirim Hub 'ını seçebilir veya Azure portal *tümleştir* sekmesinden yeni bir tane oluşturabilirsiniz. Bağlantı dizesini el ile de yapılandırabilirsiniz. 

Bağlantı dizesini var olan bir Bildirim Hub 'ına yapılandırmak için:

1. [Azure Portal](https://portal.azure.com)Bildirim Hub 'ınıza gidin, **erişim ilkeleri**' ni seçin ve **Defaultfullsharedaccesssignature** ilkesinin yanındaki Kopyala düğmesini seçin. Bu, *Defaultfullsharedaccesssignature* ilkesinin bağlantı dizesini Notification Hub 'ınıza kopyalar. Bu bağlantı dizesi, işlevinizin hub 'a bildirim iletileri göndermesini sağlar.
    ![Bildirim Hub 'ı bağlantı dizesini kopyalayın](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Azure portal işlev uygulamanıza gidin, **uygulama ayarları**' nı seçin, **myhubconnectionstring**gibi bir anahtar ekleyin, Bildirim Hub 'ınızın kopyalanmış *Defaultfullsharedaccesssignature* değerini değer olarak yapıştırın ve ardından  **Kaydedin**.

Bu uygulama ayarının adı, *function. JSON* veya .net özniteliğinde çıkış bağlama bağlantısı ayarında ne olur? Bu makalenin önceki bölümlerinde bulunan [yapılandırma bölümüne](#configuration) bakın.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Özel durumlar ve dönüş kodları

| Bağlayıcısı | Başvuru |
|---|---|
| Notification Hub'ı | [İşlemler Kılavuzu](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)

