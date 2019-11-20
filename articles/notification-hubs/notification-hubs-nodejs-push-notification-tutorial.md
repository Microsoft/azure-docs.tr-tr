---
title: Azure Notification Hubs ve Node. js ile anında iletme bildirimleri gönderme
description: Bir Node. js uygulamasından anında iletme bildirimleri göndermek için Notification Hubs nasıl kullanacağınızı öğrenin.
keywords: anında iletme bildirimi, anında iletme bildirimleri, Node. js push, iOS anında iletme
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
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 6e109c5a7f4911893c81c88ae84322fb962fff6e
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213199"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Azure Notification Hubs ve Node. js ile anında iletme bildirimleri gönderme

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Genel Bakış

> [!IMPORTANT]
> Bu öğreticiyi tamamlamak için etkin bir Azure hesabınızın olması gerekir. Hesabınız yoksa [Azure Ücretsiz deneme sürümü](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)ile yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturun.

Bu kılavuz, doğrudan bir [Node. js](https://nodejs.org) uygulamasından Azure Notification Hubs yardımıyla anında iletme bildirimleri göndermeyi gösterir.

Kapsamındaki senaryolar aşağıdaki platformlarda uygulamalara anında iletme bildirimleri göndermeyi içerir:

- Android
- iOS
- Evrensel Windows Platformu
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs, mobil cihazlara anında iletme bildirimleri göndermek için kullanımı kolay, çok platformlu ve ölçeklenebilir bir altyapı sağlar. Hizmet altyapısı hakkında daha fazla bilgi için bkz. [Azure Notification Hubs](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) sayfası.

## <a name="create-a-nodejs-application"></a>Node. js uygulaması oluşturma

Bu öğreticideki ilk adım yeni bir boş Node. js uygulaması oluşturmaktır. Node. js uygulaması oluşturmayla ilgili yönergeler için bkz. [Node. js uygulaması oluşturma ve dağıtma][nodejswebsite]. Windows PowerShell kullanarak Node. [js bulut hizmeti][Node.js Cloud Service] veya [WebMatrix ile Web sitesi][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Uygulamanızı kullanmak için yapılandırma Notification Hubs

Azure Notification Hubs kullanmak için, anında iletme bildirimi REST hizmetleriyle iletişim kuran yerleşik bir dizi yardımcı kitaplık içeren node. js [Azure paketini](https://www.npmjs.com/package/azure)indirip kullanmanız gerekir.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Paketi edinmek için düğüm Paket Yöneticisi 'Ni (NPM) kullanın

1. **PowerShell** (Windows), **Terminal** (Mac) veya **Bash** (Linux) gibi bir komut satırı arabirimi kullanın ve boş uygulamanızı oluşturduğunuz klasöre gidin.
2. Komut `npm install azure-sb` penceresinde yürütün.
3. Bir `dir` `ls` klasörün`node_modules` oluşturulduğunu doğrulamak için veya komutunu el ile çalıştırabilirsiniz.
4. Bu klasörün içinde, Bildirim Hub 'ına erişmeniz gereken kitaplıkları içeren **Azure** paketini bulun.

> [!NOTE]
> Resmi [NPM bloguna](https://blog.npmjs.org/post/85484771375/how-to-install-npm)NPM 'yi yükleme hakkında daha fazla bilgi edinebilirsiniz.

### <a name="import-the-module"></a>Modülü içeri aktar
Bir metin düzenleyicisi kullanarak, uygulamanın `server.js` dosyasının en üstüne aşağıdakileri ekleyin:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Azure Notification Hub bağlantısı kurma

Nesnesi `NotificationHubService` , Bildirim Hub 'ları ile çalışmanıza olanak sağlar. Aşağıdaki kod, adlı `NotificationHubService` `hubname`Bildirim Hub 'ı için bir nesne oluşturur. Azure modülünü içeri aktarma ifadesinden sonra `server.js` dosyanın üst kısmına yakın bir şekilde ekleyin:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Aşağıdaki adımları gerçekleştirerek `connectionstring` [Azure Portal] bağlantı değerini alın:

1. Sol gezinti bölmesinde, **Araştır**' a tıklayın.
2. **Notification Hubs**' yi seçin ve ardından örnek için kullanmak istediğiniz hub 'ı bulun. Yeni bir Bildirim Hub 'ı oluşturmaya yönelik yardıma ihtiyacınız varsa [Windows Mağazası başlangıç öğreticisine](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) başvurabilirsiniz.
3. Seçin **ayarları**.
4. **Erişim ilkeleri**' ne tıklayın. Hem paylaşılan hem de tam erişimli bağlantı dizelerini görürsünüz.

![Azure portal Notification Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Bağlantı dizesini, [Azure PowerShell](/powershell/azureps-cmdlets-docs) tarafından sunulan **Get-AzureSbNamespace** cmdlet 'Ini veya Azure [komut satırı arabirimi (Azure CLI)](../cli-install-nodejs.md)ile **Azure SB ad alanı göster** komutunu kullanarak da alabilirsiniz.

## <a name="general-architecture"></a>Genel mimari

Nesnesi `NotificationHubService` , belirli cihazlara ve uygulamalara anında iletme bildirimleri göndermek için aşağıdaki nesne örneklerini kullanıma sunar:

- **Android** -şu adreste `GcmService` bulunan nesnesini kullanın:`notificationHubService.gcm`
- **iOS** -şu adresten `ApnsService` erişilebilen nesnesini kullanın:`notificationHubService.apns`
- **Windows Phone** -şu adreste `MpnsService` bulunan nesnesini kullanın:`notificationHubService.mpns`
- **Evrensel Windows platformu** -şu adreste `WnsService` bulunan nesnesini kullanın:`notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Nasıl yapılır: Android uygulamalarına anında iletme bildirimleri gönderme

Nesnesi `GcmService` , Android uygulamalarına `send` anında iletme bildirimleri göndermek için kullanılabilecek bir yöntem sağlar. `send` Yöntemi aşağıdaki parametreleri kabul eder:

- **Etiketler** -etiket tanımlayıcısı. Hiçbir etiket sağlanmazsa, bildirim tüm istemcilere gönderilir.
- **Yük** -iletinin JSON veya ham dize yükü.
- **Geri arama** -geri çağırma işlevi.

Yük biçimi hakkında daha fazla bilgi için bkz. [Yük belgeleri](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload).

Aşağıdaki kod, `NotificationHubService` tüm kayıtlı `GcmService` istemcilere anında iletme bildirimi göndermek için tarafından sunulan örneğini kullanır.

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

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Nasıl yapılır: İOS uygulamalarına anında iletme bildirimleri gönderme

Yukarıda açıklanan Android uygulamalarıyla aynı şekilde, `ApnsService` nesnesi iOS uygulamalarına anında iletme bildirimleri göndermek için kullanılabilecek bir `send` yöntem sağlar. `send` Yöntemi aşağıdaki parametreleri kabul eder:

- **Etiketler** -etiket tanımlayıcısı. Hiçbir etiket sağlanmazsa, bildirim tüm istemcilere gönderilir.
- **Yük** -iletinin JSON veya dize yükü.
- **Geri arama** -geri çağırma işlevi.

Yük biçimi hakkında daha fazla bilgi için, [yerel ve anında Iletme bildirimi Programlama Kılavuzu](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) belgesinin **bildirim yükü** bölümüne bakın.

Aşağıdaki kod, `NotificationHubService` tüm istemcilere `ApnsService` bir uyarı iletisi göndermek için tarafından sunulan örneği kullanır:

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

Nesnesi `MpnsService` , Windows Phone uygulamalarına `send` anında iletme bildirimleri göndermek için kullanılabilecek bir yöntem sağlar. `send` Yöntemi aşağıdaki parametreleri kabul eder:

- **Etiketler** -etiket tanımlayıcısı. Hiçbir etiket sağlanmazsa, bildirim tüm istemcilere gönderilir.
- **Yük** -iletinin XML yükü.
-  -  Bildirimler için TargetName`toast` . `token`kutucuk bildirimleri için.
- **Notificationclass** -bildirimin önceliği. Geçerli değerler için [bir sunucu belgesinden anında iletme bildirimlerinin](https://msdn.microsoft.com/library/hh221551.aspx) **http üst bilgi öğeleri** bölümüne bakın.
- **Seçenekler** -isteğe bağlı istek üst bilgileri.
- **Geri arama** -geri çağırma işlevi.

`TargetName` Geçerli`NotificationClass` ve üst bilgi seçeneklerinin bir listesi için, [bir sunucu sayfasından anında iletme bildirimlerine](https://msdn.microsoft.com/library/hh221551.aspx) göz atın.

Aşağıdaki örnek kod, `MpnsService` `NotificationHubService` bildirim gönderimi bildirimi göndermek için tarafından sunulan örneği kullanır:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Nasıl yapılır: Evrensel Windows Platformu (UWP) uygulamalarına anında iletme bildirimleri gönderme

Nesnesi `WnsService` , Evrensel Windows platformu uygulamalarına `send` anında iletme bildirimleri göndermek için kullanılabilecek bir yöntem sağlar.  `send` Yöntemi aşağıdaki parametreleri kabul eder:

- **Etiketler** -etiket tanımlayıcısı. Hiçbir etiket sağlanmazsa, bildirim tüm kayıtlı istemcilere gönderilir.
- **Yük** -XML iletisi yükü.
- **Tür** -bildirim türü.
- **Seçenekler** -isteğe bağlı istek üst bilgileri.
- **Geri arama** -geri çağırma işlevi.

Geçerli türlerin ve İstek üstbilgilerinin listesi için bkz. [anında iletme bildirimi hizmeti isteği ve yanıt üst bilgileri](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Aşağıdaki kod, `NotificationHubService` UWP uygulamasına `WnsService` bildirim anında iletme bildirimi göndermek için tarafından sunulan örneğini kullanır:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Sonraki Adımlar

Yukarıdaki örnek kod parçacıkları, çok çeşitli cihazlara anında iletme bildirimleri iletmek için kolayca hizmet altyapısını oluşturmanızı sağlar. Artık Node. js ile Notification Hubs kullanmanın temellerini öğrendiğinize göre, bu özellikleri daha fazla nasıl genişletebileceğinizi öğrenmek için bu bağlantıları izleyin.

- Bkz. [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx)Için MSDN Başvurusu.
- Daha fazla örnek ve uygulama ayrıntıları için GitHub 'daki [Node için Azure SDK] ziyaret edin.

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
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com
