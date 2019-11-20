---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857522"
---
Bu bölümde, yeni bir öğe eklendiğinde anında iletme bildirimi göndermek için mevcut Mobile Apps arka uç projenizdeki kodu güncelleştirdiniz. Bu işlem, platformlar arası gönderim sağlayan Azure Notification Hubs [şablon](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) özelliği tarafından desteklenir. Çeşitli istemciler şablonlar kullanılarak anında iletme bildirimleri için kaydedilir ve tek bir evrensel gönderim tüm istemci platformlarına alabilir.

Arka uç proje türü&mdash; [.net Back end](#dotnet) veya [Node. js arka ucu](#nodejs)ile eşleşen aşağıdaki yordamlardan birini seçin.

### <a name="dotnet"></a>.NET arka uç projesi

1. Visual Studio 'da sunucu projesine sağ tıklayın. Ardından **NuGet Paketlerini Yönet**' i seçin. Arama yapın ve ardından yüklemeyi seçin. `Microsoft.Azure.NotificationHubs` Bu işlem arka uçta bildirim göndermek için Notification Hubs kitaplığını kurar.
2. Sunucu projesinde, **Controllers** > **TodoItemController.cs**öğesini açın. Ardından aşağıdaki using deyimlerini ekleyin:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. **PostTodoItem** yönteminde, **ınsertasync**çağrısından sonra aşağıdaki kodu ekleyin:  

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the mobile app.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Send the message so that all template registrations that contain "messageParam"
    // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string,string> templateParams = new Dictionary<string,string>();
    templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    Bu işlem, öğeyi içeren bir şablon bildirimi gönderir. Yeni bir öğe eklendiğinde metin.

4. Sunucu projesini yeniden yayımlayın.

### <a name="nodejs"></a>Node. js arka uç projesi

1. Arka uç projenizi ayarlayın.
2. TodoItem. js içindeki mevcut kodu şu kodla değiştirin:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs.
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert. The insert returns the results as a promise.
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured.
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute().
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    Bu işlem, yeni bir öğe eklendiğinde Item. Text içeren bir şablon bildirimi gönderir.

3. Yerel bilgisayarınızda dosyayı düzenlediğinizde, sunucu projesini yeniden yayımlayın.
