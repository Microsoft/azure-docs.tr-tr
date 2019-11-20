---
title: include dosyası
description: include dosyası
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 299f92484000cb5c59291a5af87f24d89a771fee
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296777"
---
Bu bölümde, son dakika haberlerini .NET konsol uygulamasından etiketli şablon bildirimleri olarak yollarsınız.

1. Visual Studio'da yeni bir Visual C# konsol uygulaması oluşturun:
    1. Menüde **dosya** > **Yeni** > **Proje**' yi seçin.
    1. **Yeni proje oluştur**' da, şablonlar listesinde Için C# **konsol uygulaması (.NET Framework)** öğesini seçin ve **İleri**' yi seçin.
    1. Uygulama için bir ad girin.
    1. **Çözüm**için, **çözüme Ekle**' yi seçin ve projeyi oluşturmak için **Oluştur** ' u seçin.

1. **Araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** ' nu seçin ve ardından konsol penceresinde aşağıdaki komutu çalıştırın:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Bu eylem, [Microsoft. Azure. Notificationhub 'Lar] paketini kullanarak Azure Notification Hubs SDK 'sına bir başvuru ekler.

1. *Program.cs* dosyasını açın ve aşağıdaki `using` ifadesini ekleyin:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. `Program` sınıfında, aşağıdaki yöntemi ekleyin veya zaten mevcutsa değiştirin:

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   Bu kod, dize dizisindeki altı etiketin her biri için bir şablon bildirimi gönderir. Etiketlerin kullanılması, cihazların yalnızca kayıtlı kategoriler için bildirim almasını sağlar.

1. Önceki kodda, `<hub name>` ve `<connection string with full access>` yer tutucularını bildirim hub'ı adınız ve bildirim hub’ınızın panosundaki *DefaultFullSharedAccessSignature* bağlantı dizeniz ile değiştirin.

1. @No__t-0 yönteminde aşağıdaki satırları ekleyin:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Konsol uygulamasını oluşturun.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft. Azure. Notificationhub 'Lar]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
