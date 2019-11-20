---
title: Kayıt yönetimi
description: Bu konuda, anında iletme bildirimleri almak için cihazların Notification Hub 'larına nasıl kaydedileceği açıklanmaktadır.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 04/08/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 0725b4fc80fc3a41491bdb9ed084d33b36b490b8
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213094"
---
# <a name="registration-management"></a>Kayıt yönetimi

## <a name="overview"></a>Genel Bakış

Bu konuda, anında iletme bildirimleri almak için cihazların Notification Hub 'larına nasıl kaydedileceği açıklanmaktadır. Konu başlığı yüksek düzeyde kayıtları açıklar ve cihazları kaydetmek için iki ana deseni tanıtır: cihazdan doğrudan Bildirim Hub 'ına kaydetme ve bir uygulama arka ucu üzerinden kaydetme.

## <a name="what-is-device-registration"></a>Cihaz kaydı nedir?

Bir Bildirim Hub 'ına sahip cihaz kaydı, **kayıt** veya **yükleme**kullanılarak gerçekleştirilir.

### <a name="registrations"></a>Kayıtlar

Kayıt bir cihaz için platform bildirim hizmeti (PNS) tanıtıcısını Etiketler ve muhtemelen bir şablon ile ilişkilendirir. PNS tanıtıcısı, bir ChannelURI, cihaz belirteci veya FCM kayıt kimliği olabilir. Etiketler, bildirimleri doğru cihaz tutamaçları kümesine yönlendirmek için kullanılır. Daha fazla bilgi için bkz. [Yönlendirme ve etiket ifadeleri](notification-hubs-tags-segment-push-message.md). Şablonlar, kayıt başına dönüştürmeyi uygulamak için kullanılır. Daha fazla bilgi için bkz. [Şablonlar](notification-hubs-templates-cross-platform-push-messages.md).

> [!NOTE]
> Azure Notification Hubs, cihaz başına en fazla 60 etiketi destekler.

### <a name="installations"></a>Yükleme

Yükleme, gönderim ile ilgili özelliklerin bir paketini içeren gelişmiş bir kayıttır. Cihazlarınızı kaydetmek için en son ve en iyi yaklaşım bu. Ancak, istemci tarafı .NET SDK ([arka uç işlemleri Için Bildirim Hub 'ı SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) tarafından henüz itibarıyla desteklenmez.  Bu, istemci cihazın kendisinden kayıt yapıyorsanız, yüklemeleri desteklemek için [Notification Hubs REST API](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) yaklaşımını kullanmanız gerektiği anlamına gelir. Bir arka uç hizmeti kullanıyorsanız, [arka uç işlemleri Için Notification Hub SDK 'sını](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)kullanabilmeniz gerekir.

Yüklemeler kullanmanın bazı önemli avantajları aşağıda verilmiştir:

- Bir yüklemenin oluşturulması veya güncelleştirilmesi tamamen ıdempotent. Bu sayede yinelenen kayıtlar hakkında herhangi bir kaygısız yeniden deneyebilirsiniz.
- Yükleme modeli, doğrudan belirli bir cihaza bildirim gönderilmesini`$InstallationId:{INSTALLATION_ID}`sağlayan özel bir etiket biçimi () destekler. Örneğin, uygulamanın kodu bu belirli bir cihaz `joe93developer` için bir yükleme kimliği ayarladıysanız, bir geliştirici `$InstallationId:{joe93developer}` etikete bildirim gönderirken bu cihazı hedefleyebilir. Bu, herhangi bir ek kodlama yapmak zorunda kalmadan belirli bir cihazı hedeflemenize olanak sağlar.
- Yüklemeleri kullanmak, kısmi kayıt güncelleştirmeleri yapmanızı de sağlar. Bir yüklemenin kısmi güncelleştirmesi, [JSON-Patch standardını](https://tools.ietf.org/html/rfc6902)kullanan bir yama yöntemiyle istenir. Bu, kayıt üzerindeki etiketleri güncelleştirmek istediğinizde yararlıdır. Tüm kaydı geri çekmek ve sonra önceki tüm etiketleri yeniden göndermek zorunda kalmazsınız.

Yükleme, aşağıdaki özellikleri içerebilir. Yükleme özelliklerinin tüm listesi için bkz. REST API veya [yükleme özellikleri](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation) [ile yükleme oluşturma veya üzerine yazma](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) .

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Varsayılan olarak, kayıt ve yüklemelerin kullanım süreleri dolmaz.

Kayıtlar ve yüklemeler her cihaz/kanal için geçerli bir PNS tanıtıcısı içermelidir. PNS tanıtıcıları yalnızca cihazdaki bir istemci uygulamasında alınabileceğinden, tek bir model doğrudan bu cihaza istemci uygulaması ile kaydolmalıdır. Öte yandan, etiketlerle ilgili güvenlik konuları ve iş mantığı, uygulama arka ucunda cihaz kaydını yönetmenizi gerektirebilir.

> [!NOTE]
> Yüklemeler API 'SI, Baidu hizmetini desteklemez (ancak, kayıt API 'SI olsa da). 

### <a name="templates"></a>Şablonlar

[Şablonları](notification-hubs-templates-cross-platform-push-messages.md)kullanmak istiyorsanız, cihaz yüklemesi bu cihazla ilişkili tüm ŞABLONLARı bir JSON biçiminde de barındırır (Yukarıdaki örneğe bakın). Şablon adları, aynı cihaz için farklı şablonların hedeflemesini sağlamaya yardımcı olur.

Her şablon adı bir şablon gövdesine ve isteğe bağlı bir Etiketler kümesiyle eşlenir. Üstelik, her platformun ek şablon özellikleri olabilir. Windows Mağazası (WNS kullanarak) ve Windows Phone 8 (MPNS kullanarak) için ek bir başlık kümesi, şablonun bir parçası olabilir. APNs durumunda, bir süre sonu özelliğini bir sabit ya da bir şablon ifadesi olarak ayarlayabilirsiniz. Yükleme özelliklerinin tüm listesi için bkz. [rest ile bir yükleme oluşturma veya üzerine yazma](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) .

### <a name="secondary-tiles-for-windows-store-apps"></a>Windows Mağazası uygulamaları için ikincil kutucuklar

Windows Mağazası istemci uygulamaları için, ikincil kutucuklara bildirim göndermek, birincil birine göndermelerle aynıdır. Bu, yüklemelerde de desteklenir. İkincil kutucukların, istemci uygulamanızdaki SDK 'nın saydam şekilde işlediği farklı bir ChannelUri 'Si vardır.

Secondarykutucukları sözlüğü, Windows Mağazası uygulamanızda Secondarykutucukları nesnesini oluşturmak için kullanılan Tileıd 'yi kullanır. Birincil Channelurı 'Sinde olduğu gibi, ikincil kutucukların Channeluri 'Leri herhangi bir anda değişebilir. Bildirim Hub 'ında yüklemelerin güncelleştirilmesini sağlamak için, cihazın bunları ikincil kutucukların geçerli ChannelUris ile yenilemesi gerekir.

## <a name="registration-management-from-the-device"></a>Cihazdan kayıt yönetimi

İstemci uygulamalarından cihaz kaydını yönetirken arka uç yalnızca bildirim göndermekten sorumludur. İstemci uygulamaları, PNS 'lerin güncel ve kayıt etiketlerini tutacağız. Aşağıdaki resimde bu desenler gösterilmektedir.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Cihaz önce PNS tanıtıcılarını PNS 'den alır, ardından doğrudan Bildirim Hub 'ına kaydeder. Kayıt başarılı olduktan sonra, uygulama arka ucu kaydı hedefleyen bir bildirim gönderebilir. Bildirimlerin nasıl gönderileceği hakkında daha fazla bilgi için bkz. [Yönlendirme ve etiket ifadeleri](notification-hubs-tags-segment-push-message.md).

Bu durumda, cihazdaki bildirim hub 'larına erişmek için yalnızca dinleme haklarını kullanırsınız. Daha fazla bilgi için bkz. [güvenlik](notification-hubs-push-notification-security.md).

Cihazdan kayıt en basit yöntemdir, ancak bazı dezavantajları vardır:

- Bir istemci uygulaması, yalnızca uygulama etkin olduğunda bu etiketlerin güncelleştirilmesini sağlayabilir. Örneğin, bir kullanıcının spor ekipleriyle ilgili etiketleri kaydeden iki cihazı varsa, ilk cihaz ek bir etiket için (örneğin, deniz Hawks) kaydolduğunda ikinci cihaz, ikinci cihazdaki uygulama ikinci bir kez yürütüldü. Daha genel olarak, Etiketler birden fazla cihazdan etkileniyorsa, arka ucun etiketlerinin yönetilmesi, istenen seçenektir.
- Uygulamalar hacdiğinden, kaydın belirli etiketlere sağlanması, "etiket düzeyinde güvenlik" bölümünde açıklandığı gibi ek bakım gerektirir.

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Yükleme kullanarak bir cihazdan Bildirim Hub 'ına kaydolmak için örnek kod

Şu anda bu yalnızca [Notification Hubs REST API](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation)kullanılarak desteklenir.

Yüklemeyi güncelleştirmek için [JSON-Patch standardını](https://tools.ietf.org/html/rfc6902) kullanarak da Patch yöntemini kullanabilirsiniz.

```
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }
}

private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
{
    if (deviceInstallation.installationId == null)
        return HttpStatusCode.BadRequest;

    // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
    ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
    string hubResource = "installations/" + deviceInstallation.installationId + "?";
    string apiVersion = "api-version=2015-04";

    // Determine the targetUri that we will sign
    string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

    //=== Generate SaS Security Token for Authorization header ===
    // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
    string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

    using (var httpClient = new HttpClient())
    {
        string json = JsonConvert.SerializeObject(deviceInstallation);

        httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

        var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
        return response.StatusCode;
    }
}

var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

string installationId = null;
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored an installation id in application data, create and store as application data.
if (!settings.ContainsKey("__NHInstallationId"))
{
    installationId = Guid.NewGuid().ToString();
    settings.Add("__NHInstallationId", installationId);
}

installationId = (string)settings["__NHInstallationId"];

var deviceInstallation = new DeviceInstallation
{
    installationId = installationId,
    platform = "wns",
    pushChannel = channel.Uri,
    //tags = tags.ToArray<string>()
};

var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

if (statusCode != HttpStatusCode.Accepted)
{
    var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
else
{
    var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Bir cihazdan kayıt kullanarak bir Bildirim Hub 'ına kaydolmak için örnek kod

Bu yöntemler, çağrıldığı cihaz için bir kayıt oluşturur veya güncelleştirir. Yani, tanıtıcıyı veya etiketleri güncelleştirmek için kaydın tamamının üzerine yazmanız gerekir. Kayıtların geçici olduğunu unutmayın, bu nedenle her zaman belirli bir cihazın ihtiyaç duyacağı geçerli etiketlere sahip güvenilir bir mağazaya sahip olmanız gerekir.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device id from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration id in device
// storage. Then when the app starts, you can check if a registration id already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration id in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
    foreach (RegistrationDescription registration in registrations)
    {
        if (newRegistrationId == null)
        {
            newRegistrationId = registration.RegistrationId;
        }
        else
        {
            await hub.DeleteRegistrationAsync(registration);
        }
    }

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Arka uçtan kayıt yönetimi

Arka uca kayıtları yönetmek için ek kod yazılması gerekir. Cihazdaki uygulamanın, uygulama her başlatıldığında (Etiketler ve şablonlar ile birlikte), arka uca güncelleştirilmiş PNS tanıtıcısı sağlaması gerekir ve arka uç, Bildirim Hub 'ında bu tanıtıcıyı güncelleştirmelidir. Aşağıdaki resimde bu tasarım gösterilmektedir.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Arka uca kayıtları yönetmenin avantajları, cihazdaki ilgili uygulama etkin olmadığında bile kayıtları kayıt için değiştirme ve kayda etiket eklemeden önce istemci uygulamasının kimliğini doğrulama imkanını içerir.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Bir yükleme kullanarak arka uçta bir Bildirim Hub 'ına kaydolmak için örnek kod

İstemci cihaz hala PNS tanıtıcısını ve ilgili yükleme özelliklerini daha önce olduğu gibi alır ve arka uçta kayıt ve yetkilendirme etiketlerini gerçekleştirebilen özel bir API 'yi çağırır. Arka uç, [arka uç işlemleri Için Bildirim Hub 'ı SDK 'Sının üzerinden](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)yararlanabilir.

Yüklemeyi güncelleştirmek için [JSON-Patch standardını](https://tools.ietf.org/html/rfc6902) kullanarak da Patch yöntemini kullanabilirsiniz.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Kayıt KIMLIĞI kullanarak bir cihazdan Bildirim Hub 'ına kaydolmak için örnek kod

Uygulama arka ucunuzdaki kayıtlar üzerinde temel CRUDS işlemlerini gerçekleştirebilirsiniz. Örneğin:

```
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by id
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

Arka uç, kayıt güncelleştirmeleri arasında eşzamanlılık işleyecek olmalıdır. Service Bus, kayıt yönetimi için iyimser eşzamanlılık denetimi sağlar. HTTP düzeyinde, bu, kayıt yönetimi işlemlerinde ETag kullanımı ile uygulanır. Bu özellik, Microsoft SDK 'Ları tarafından şeffaf bir şekilde bir güncelleştirme reddedildiğinde bir özel durum oluşturan, saydam bir şekilde kullanılır. Uygulama arka ucu, bu özel durumları işlemekten ve gerekirse güncelleştirmeyi yeniden denemeye yöneliktir.
