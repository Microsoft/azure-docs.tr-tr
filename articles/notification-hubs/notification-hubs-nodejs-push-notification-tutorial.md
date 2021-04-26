---
title: Azure Notification Hubs ve Node.js anında iletme bildirimleri gönderme
description: Node.js uygulamasından anında iletme bildirimleri göndermek için Notification Hubs nasıl kullanacağınızı öğrenin.
keywords: anında iletme bildirimi, anında iletme bildirimleri, node.js push, iOS anında iletme
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-js
ms.openlocfilehash: eb41593938c670199be38140118f276142ceed43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453232"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Azure Notification Hubs ve Node.js anında iletme bildirimleri gönderme

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Genel Bakış

> [!IMPORTANT]
> Bu öğreticiyi tamamlamak için etkin bir Azure hesabınızın olması gerekir. Hesabınız yoksa [Azure Ücretsiz deneme sürümü](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)ile yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturun.

Bu kılavuzda, Azure Notification Hubs yardımıyla doğrudan [Node.js](https://nodejs.org) uygulamasından anında iletme bildirimleri gönderme işlemlerinin nasıl yapılacağı gösterilmektedir.

Kapsamındaki senaryolar aşağıdaki platformlarda uygulamalara anında iletme bildirimleri göndermeyi içerir:

- Android
- iOS
- Evrensel Windows Platformu
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs, mobil cihazlara anında iletme bildirimleri göndermek için kullanımı kolay, çok platformlu ve ölçeklenebilir bir altyapı sağlar. Hizmet altyapısı hakkında daha fazla bilgi için bkz. [Azure Notification Hubs](/previous-versions/azure/azure-services/jj927170(v=azure.100)) sayfası.

## <a name="create-a-nodejs-application"></a>Node.js uygulaması oluşturma

Bu öğreticideki ilk adım yeni bir boş Node.js uygulaması oluşturmaktır. Node.js uygulaması oluşturma hakkında yönergeler için bkz. [Azure Web sitesine Node.js uygulama oluşturma ve dağıtma][nodejswebsite], Windows PowerShell kullanarak [ bulut hizmetiNode.js][Node.js Cloud Service] veya [WebMatrix ile Web sitesi][webmatrix]kullanma.

## <a name="configure-your-application-to-use-notification-hubs"></a>Uygulamanızı kullanmak için yapılandırma Notification Hubs

Azure Notification Hubs kullanmak için, anında iletme bildirimi REST hizmetleriyle iletişim kuran yerleşik bir yardımcı kitaplıklar kümesini içeren Node.js [Azure paketini](https://www.npmjs.com/package/azure)indirmeniz ve kullanmanız gerekir.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Paketi edinmek için düğüm Paket Yöneticisi 'Ni (NPM) kullanın

1. **PowerShell** (Windows), **Terminal** (Mac) veya **Bash** (Linux) gibi bir komut satırı arabirimi kullanın ve boş uygulamanızı oluşturduğunuz klasöre gidin.
2. `npm install azure-sb`Komut penceresinde yürütün.
3. `ls` `dir` Bir klasörün oluşturulduğunu doğrulamak için veya komutunu el ile çalıştırabilirsiniz `node_modules` .
4. Bu klasörün içinde, Bildirim Hub 'ına erişmeniz gereken kitaplıkları içeren **Azure** paketini bulun.

> [!NOTE]
> Resmi [NPM bloguna](https://blog.npmjs.org/post/85484771375/how-to-install-npm)NPM 'yi yükleme hakkında daha fazla bilgi edinebilirsiniz.

### <a name="import-the-module"></a>Modülü içeri aktar
Bir metin düzenleyicisi kullanarak, uygulamanın dosyasının en üstüne aşağıdakileri ekleyin `server.js` :

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Azure Notification Hub bağlantısı kurma

`NotificationHubService`Nesnesi, Bildirim Hub 'ları ile çalışmanıza olanak sağlar. Aşağıdaki kod, `NotificationHubService` adlı Bildirim Hub 'ı için bir nesne oluşturur `hubname` . `server.js`Azure modülünü içeri aktarma ifadesinden sonra dosyanın üst kısmına yakın bir şekilde ekleyin:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

`connectionstring`Aşağıdaki adımları gerçekleştirerek [Azure Portal] bağlantı değerini alın:

1. Sol gezinti bölmesinde, **Araştır**' a tıklayın.
2. **Notification Hubs**' yi seçin ve ardından örnek için kullanmak istediğiniz hub 'ı bulun. Yeni bir Bildirim Hub 'ı oluşturmaya yönelik yardıma ihtiyacınız varsa [Windows Mağazası başlangıç öğreticisine](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) başvurabilirsiniz.
3. **Ayarlar**'ı seçin.
4. **Erişim ilkeleri**' ne tıklayın. Hem paylaşılan hem de tam erişimli bağlantı dizelerini görürsünüz.

![Azure portal Notification Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Bağlantı dizesini, [Azure PowerShell](/powershell/azure/) tarafından sunulan **Get-AzureSbNamespace** cmdlet 'Ini veya Azure [Command-Line arabirimi (Azure CLI)](/cli/azure/install-classic-cli)ile **Azure SB ad alanı göster** komutunu kullanarak da alabilirsiniz.

## <a name="general-architecture"></a>Genel mimari

`NotificationHubService`Nesnesi, belirli cihazlara ve uygulamalara anında iletme bildirimleri göndermek için aşağıdaki nesne örneklerini kullanıma sunar:

- **Android** - `GcmService` Şu adreste bulunan nesnesini kullanın: `notificationHubService.gcm`
- **iOS** - `ApnsService` Şu adresten erişilebilen nesnesini kullanın: `notificationHubService.apns`
- **Windows Phone** - `MpnsService` Şu adreste bulunan nesnesini kullanın: `notificationHubService.mpns`
- **Evrensel Windows platformu** - `WnsService` Şu adreste bulunan nesnesini kullanın: `notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Nasıl yapılır: Android uygulamalarına anında iletme bildirimleri gönderme

`GcmService`Nesnesi, `send` Android uygulamalarına anında iletme bildirimleri göndermek için kullanılabilecek bir yöntem sağlar. `send`Yöntemi aşağıdaki parametreleri kabul eder:

- **Etiketler** -etiket tanımlayıcısı. Hiçbir etiket sağlanmazsa, bildirim tüm istemcilere gönderilir.
- **Yük** -iletinin JSON veya ham dize yükü.
- **Geri arama** -geri çağırma işlevi.

Yük biçimi hakkında daha fazla bilgi için bkz. [Yük belgeleri](https://payload.readthedocs.io/en/latest/).

Aşağıdaki kod `GcmService` , `NotificationHubService` Tüm kayıtlı istemcilere anında iletme bildirimi göndermek için tarafından sunulan örneğini kullanır.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Nasıl yapılır: iOS uygulamalarına anında iletme bildirimleri gönderme

Yukarıda açıklanan Android uygulamalarıyla aynı şekilde, `ApnsService` nesnesi `send` iOS uygulamalarına anında iletme bildirimleri göndermek için kullanılabilecek bir yöntem sağlar. `send`Yöntemi aşağıdaki parametreleri kabul eder:

- **Etiketler** -etiket tanımlayıcısı. Hiçbir etiket sağlanmazsa, bildirim tüm istemcilere gönderilir.
- **Yük** -iletinin JSON veya dize yükü.
- **Geri arama** -geri çağırma işlevi.

Yük biçimi hakkında daha fazla bilgi için, [Usernotifications kılavuzunun](https://developer.apple.com/documentation/usernotifications) **bildirim içeriği** bölümüne bakın.

Aşağıdaki kod `ApnsService` , `NotificationHubService` tüm istemcilere bir uyarı iletisi göndermek için tarafından sunulan örneği kullanır:

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Nasıl yapılır: Windows Phone uygulamalarına anında iletme bildirimleri gönderme

`MpnsService`Nesnesi, `send` Windows Phone uygulamalarına anında iletme bildirimleri göndermek için kullanılabilecek bir yöntem sağlar. `send`Yöntemi aşağıdaki parametreleri kabul eder:

- **Etiketler** -etiket tanımlayıcısı. Hiçbir etiket sağlanmazsa, bildirim tüm istemcilere gönderilir.
- **Yük** -iletinin XML yükü.
-   -  TargetName `toast` bildirim için. `token` kutucuk bildirimleri için.
- **Notificationclass** -bildirimin önceliği. Geçerli değerler için [bir sunucu belgesinden anında iletme bildirimlerinin](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41)) **http üst bilgi öğeleri** bölümüne bakın.
- **Seçenekler** -isteğe bağlı istek üst bilgileri.
- **Geri arama** -geri çağırma işlevi.

Geçerli `TargetName` `NotificationClass` ve üst bilgi seçeneklerinin bir listesi için, [bir sunucu sayfasından anında iletme bildirimlerine](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41)) göz atın.

Aşağıdaki örnek kod `MpnsService` , bildirim gönderimi bildirimi göndermek için tarafından sunulan örneği kullanır `NotificationHubService` :

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Nasıl yapılır: Evrensel Windows Platformu (UWP) uygulamalarına anında iletme bildirimleri gönderme

`WnsService`Nesnesi, `send` Evrensel Windows platformu uygulamalarına anında iletme bildirimleri göndermek için kullanılabilecek bir yöntem sağlar.  `send`Yöntemi aşağıdaki parametreleri kabul eder:

- **Etiketler** -etiket tanımlayıcısı. Hiçbir etiket sağlanmazsa, bildirim tüm kayıtlı istemcilere gönderilir.
- **Yük** -XML iletisi yükü.
- **Tür** -bildirim türü.
- **Seçenekler** -isteğe bağlı istek üst bilgileri.
- **Geri arama** -geri çağırma işlevi.

Geçerli türlerin ve İstek üstbilgilerinin listesi için bkz. [anında iletme bildirimi hizmeti isteği ve yanıt üst bilgileri](/previous-versions/windows/apps/hh465435(v=win.10)).

Aşağıdaki kod `WnsService` , `NotificationHubService` UWP uygulamasına bildirim anında iletme bildirimi göndermek için tarafından sunulan örneğini kullanır:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Sonraki Adımlar

Yukarıdaki örnek kod parçacıkları, çok çeşitli cihazlara anında iletme bildirimleri iletmek için kolayca hizmet altyapısını oluşturmanızı sağlar. Artık node.js Notification Hubs kullanmanın temellerini öğrendiğinize göre, bu özellikleri daha fazla nasıl genişletebileceğinizi öğrenmek için bu bağlantıları izleyin.

- Bkz. [Azure Notification Hubs](/previous-versions/azure/azure-services/jj927170(v=azure.100))Için MSDN Başvurusu.
- Daha fazla örnek ve uygulama ayrıntıları için GitHub 'daki [düğüm deposu Için Azure SDK 'sını] ziyaret edin.

[Node için Azure SDK]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter_sqlexpression
[Azure Service Bus Notification Hubs]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: ../app-service/quickstart-nodejs.md
[webmatrix]: /aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portalı]: https://portal.azure.com
