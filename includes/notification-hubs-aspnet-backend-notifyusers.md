---
title: include dosyası
description: Arka uç ASP .NET WebAPI projesi oluşturma kodunu içeren dosyayı ekleyin.
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 60d5d8efb10cce54743038599238cc6f61922369
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "70934128"
---
## <a name="create-the-webapi-project"></a>WebAPI projesi oluşturma

Aşağıdaki bölümlerde, yeni bir ASP.NET WebAPI arka ucu oluşturma işlemi açıklanmaktadır. Bu işlemin üç ana amacı vardır:

- **Istemcilerin kimliğini doğrulama**: İstemci isteklerinin kimliğini doğrulamak ve kullanıcıyı istekle ilişkilendirmek için bir ileti işleyicisi ekleyin.
- **WebAPI arka ucunu kullanarak bildirimlere kaydolun**: Bir istemci cihazının bildirimlerini almak için yeni kayıtları işlemek üzere bir denetleyici eklersiniz. Kimliği doğrulanmış kullanıcı adı, otomatik olarak kayda bir [etiket](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md) halinde eklenir.
- **İstemcilere bildirim gönder**: Kullanıcıların, etiketle ilişkilendirilmiş cihazlara ve istemcilere güvenli bir gönderim tetiklemesi için bir yol sağlamak üzere bir denetleyici eklersiniz.

Aşağıdaki eylemleri yaparak yeni bir ASP.NET WebAPI arka ucu oluşturun:

> [!IMPORTANT]
> Visual Studio 2015 veya önceki bir sürümü kullanıyorsanız, bu öğreticiye başlamadan önce lütfen Visual Studio için NuGet Paket Yöneticisi’nin en son sürümünü yüklediğinizden emin olun.
>
>Denetlemek için Visual Studio’yu başlatın. **Araçlar** menüsünde **Uzantılar ve Güncelleştirmeler**’i seçin. Visual Studio sürümünüz için **NuGet Paket Yöneticisi** araması yapın ve en son sürümü kullandığınızdan emin olun. Sürümünüz en son sürüm değilse, bunu kaldırın ve NuGet Paket Yöneticisi'ni yeniden yükleyin.

![][B4]

> [!NOTE]
> Web sitesi dağıtımı için Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/)’sını yüklediğinizden emin olun.

1. Visual Studio veya Visual Studio Express’i başlatın.

2. **Sunucu Gezgini**’ni seçip Azure hesabınızda oturum açın. Hesabınızda web sitesi kaynakları oluşturmak için oturum açmanız gerekir.

3. Visual Studio’da, Visual Studio çözümüne sağ tıklayın, **Ekle**’nin üzerine gelin ve **Yeni Proje**’ye tıklayın.
4. **Visual C#** seçeneğini genişletin, **Web**’i seçin ve **ASP.NET Web Uygulaması**’na tıklayın.

5. **Ad** kutusuna **AppBackend** yazın ve **Tamam**’ı seçin.

    ![Yeni Proje penceresi][B1]

6. **Yeni ASP.NET Projesi** penceresinde, **Web API** onay kutusunu ve ardından **Tamam**’ı seçin.

    ![Yeni ASP.NET Projesi penceresi][B2]

7. **Microsoft Azure Web App’i yapılandırma** penceresinde, bir abonelik seçin ve **App Service planı** listesinde aşağıdaki eylemlerden birini yapın:

    * Önceden oluşturduğunuz bir App Service planı seçin.
    * **Yeni bir App Service planı oluştur**’u seçin ve yeni bir plan oluşturun.

   Bu öğretici için bir veritabanı gerekmez. App Service planınızı seçtikten sonra projeyi oluşturmak için **Tamam**’ı seçin.

    ![Microsoft Azure Web App’i Yapılandırma penceresi][B5]

    App Service planını Yapılandır için bu sayfayı görmüyorsanız öğreticiye devam edin. Uygulamayı daha sonra yayımlarken yapılandırabilirsiniz. 

## <a name="authenticate-clients-to-the-webapi-backend"></a>WebAPI arka ucunda istemcilerin kimliğini doğrulama

Bu bölümde, yeni arka uç için **AuthenticationTestHandler** adlı yeni bir ileti işleyicisi oluşturacaksınız. Bu sınıf [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx)’dan türetilip bir ileti işleyicisi olarak eklenir, böylece arka uca gelen tüm istekleri işleyebilir.

1. Çözüm Gezgini'nde **AppBackend** projesine sağ tıklayın, **Ekle**'yi ve ardından **Sınıf**'ı seçin.
2. Yeni **AuthenticationTestHandler.cs** sınıfını adlandırıp **Ekle**’yi seçerek sınıfı oluşturun. Bu sınıf, kolaylık için *Temel Kimlik Doğrulaması* kullanarak kullanıcıların kimliklerini doğrular. Uygulamanız herhangi bir kimlik doğrulama şemasını kullanabilir.
3. AuthenticationTestHandler.cs sınıfına aşağıdaki `using` deyimlerini ekleyin:

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. AuthenticationTestHandler.cs sınıfında `AuthenticationTestHandler` sınıf tanımını aşağıdaki kod ile değiştirin:

    Bu işleyici aşağıdaki üç koşul geçerli olduğunda bu isteği yetkilendirir:

   * İstek bir *Yetkilendirme* üst bilgisi içerir.
   * İstek *temel* kimlik doğrulaması kullanır.
   * Kullanıcı adı dizesi ve parola dizesi aynı dizelerdir.

   Aksi takdirde istek reddedilir. Bu kimlik doğrulama işlemi, geçerli bir kimlik doğrulama ve yetkilendirme yaklaşımı değildir. Yalnızca bu öğreticiye yönelik basit bir örnektir.

   İstek iletisi `AuthenticationTestHandler` tarafından kimlik doğrulaması yapılır ve yetkilendirilirse, temel kimlik doğrulaması kullanıcısı [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx) üzerindeki geçerli isteğe eklenir. HttpContext içindeki kullanıcı bilgileri, bildirim kaydı isteğine bir [etiket](https://msdn.microsoft.com/library/azure/dn530749.aspx) eklemek amacıyla daha sonra başka bir denetleyici (RegisterController) tarafından kullanılır.

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            var authorizationHeader = request.Headers.GetValues("Authorization").First();

            if (authorizationHeader != null && authorizationHeader
                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
            {
                string authorizationUserAndPwdBase64 =
                    authorizationHeader.Substring("Basic ".Length);
                string authorizationUserAndPwd = Encoding.Default
                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                string user = authorizationUserAndPwd.Split(':')[0];
                string password = authorizationUserAndPwd.Split(':')[1];

                if (verifyUserAndPwd(user, password))
                {
                    // Attach the new principal object to the current HttpContext object
                    HttpContext.Current.User =
                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
                    System.Threading.Thread.CurrentPrincipal =
                        System.Web.HttpContext.Current.User;
                }
                else return Unauthorized();
            }
            else return Unauthorized();

            return base.SendAsync(request, cancellationToken);
        }

        private bool verifyUserAndPwd(string user, string password)
        {
            // This is not a real authentication scheme.
            return user == password;
        }

        private Task<HttpResponseMessage> Unauthorized()
        {
            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            var tsc = new TaskCompletionSource<HttpResponseMessage>();
            tsc.SetResult(response);
            return tsc.Task;
        }
    }
    ```

    > [!NOTE]
    > Güvenlik notno: Sınıf `AuthenticationTestHandler` , doğru kimlik doğrulaması sağlamaz. Yalnızca temel kimlik doğrulamasını taklit etmek için kullanılır ve güvenli değildir. Üretim uygulamalarınızda ve hizmetlerinizde güvenli bir kimlik doğrulama mekanizması uygulamanız gerekir.
5. İleti işleyicisini kaydetmek için **App_Start/WebApiConfig.cs** sınıfındaki `Register` yönteminin sonuna aşağıdaki kodu ekleyin:

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. Yaptığınız değişiklikleri kaydedin.

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>WebAPI arka ucunu kullanarak bildirimlere kaydolma

Bu bölümde, bildirim hub’ları için istemci kitaplığını kullanarak WebAPI arka ucuna bir kullanıcı ve cihazı bildirimlere kaydetme isteklerini işlemek üzere yeni bir denetleyici ekleyeceksiniz. Denetleyici, kimliği doğrulanmış ve `AuthenticationTestHandler` tarafından HttpContext’e eklenmiş kullanıcı için bir kullanıcı etiketi ekler. Etiket `"username:<actual username>"` dize biçiminde olacaktır.

1. Çözüm Gezgini'nde, **AppBackend** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**'i seçin.

2. Sol bölmede **Çevrimiçi**’ni seçin ve **Arama** kutusuna **Microsoft.Azure.NotificationHubs** yazın.

3. Sonuç listesinde **Microsoft Azure Notification Hubs**’ı ve ardından **Yükle**’yi seçin. Yüklemeyi tamamlayın ve sonra NuGet Paket Yöneticisi penceresini kapatın.

    Bu eylem [Microsoft.Azure.Notification Hubs NuGet paketini](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) kullanarak Azure Notification Hubs SDK'sına bir başvuru ekler.

4. Bildirimleri göndermek için kullanılan bildirim hub'ı bağlantısını temsil eden yeni bir sınıf dosyası oluşturun. Çözüm Gezgini'nde **Modeller** klasörüne sağ tıklayın, **Ekle**'yi ve ardından **Sınıf**'ı seçin. Yeni sınıfı **Notifications.cs** olarak adlandırın, ardından **Ekle**’yi seçerek sınıfı oluşturun.

    ![Yeni Öğe Ekle penceresi][B6]

5. Notifications.cs sınıfında aşağıdaki `using` deyimini dosyanın üst kısmına ekleyin:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. `Notifications` sınıf tanımını aşağıdaki kodla değiştirin ve iki yer tutucuyu bildirim hub’ınızın bağlantı dizesi (tam erişimli) ve hub adı ile değiştirdiğinizden emin olun ([Azure portalında bulunabilir](https://portal.azure.com)):

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
    > [!IMPORTANT]
    > Devam etmeden önce hub 'ınızın **adını** ve **Defaultfullsharedaccesssignature** girin. 
    
7. Ardından **RegisterController** adlı yeni bir denetleyici oluşturun. Çözüm Gezgini'nde **Denetleyiciler** klasörüne sağ tıklayın, **Ekle**'yi ve ardından **Denetleyici**'yi seçin.

8. **Web API 2 Denetleyicisi - Boş**’u ve ardından **Ekle**’yi seçin.

    ![İskele Ekle penceresi][B7]

9. **Denetleyici adı** kutusuna, yeni sınıfı adlandırmak için **RegisterController** yazın ve **Ekle**’yi seçin.

    ![Yeni Denetleyici penceresi][B8]

10. RegisterController.cs sınıfına aşağıdaki `using` deyimlerini ekleyin:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. Aşağıdaki kodu `RegisterController` sınıf tanımına ekleyin. Bu kodda, HttpContext’e eklenen kullanıcı için bir kullanıcı etiketi eklersiniz. Kullanıcının kimliği doğrulanmış ve eklediğiniz `AuthenticationTestHandler` ileti filtresi tarafından HttpContext’e eklenmiştir. Ayrıca, kullanıcının istenen etiketlere kaydolma haklarının olduğunu doğrulamak için isteğe bağlı denetimler ekleyebilirsiniz.

    ```csharp
    private NotificationHubClient hub;

    public RegisterController()
    {
        hub = Notifications.Instance.Hub;
    }

    public class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    // POST api/register
    // This creates a registration id
    public async Task<string> Post(string handle = null)
    {
        string newRegistrationId = null;

        // make sure there are no existing registrations for this push handle (used for iOS and Android)
        if (handle != null)
        {
            var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

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
        }

        if (newRegistrationId == null) 
            newRegistrationId = await hub.CreateRegistrationIdAsync();

        return newRegistrationId;
    }

    // PUT api/register/5
    // This creates or updates a registration (with provided channelURI) at the specified id
    public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
    {
        RegistrationDescription registration = null;
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "wns":
                registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "apns":
                registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                break;
            case "fcm":
                registration = new FcmRegistrationDescription(deviceUpdate.Handle);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

        registration.RegistrationId = id;
        var username = HttpContext.Current.User.Identity.Name;

        // add check if user is allowed to add these tags
        registration.Tags = new HashSet<string>(deviceUpdate.Tags);
        registration.Tags.Add("username:" + username);

        try
        {
            await hub.CreateOrUpdateRegistrationAsync(registration);
        }
        catch (MessagingException e)
        {
            ReturnGoneIfHubResponseIsGone(e);
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }

    // DELETE api/register/5
    public async Task<HttpResponseMessage> Delete(string id)
    {
        await hub.DeleteRegistrationAsync(id);
        return Request.CreateResponse(HttpStatusCode.OK);
    }

    private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
    {
        var webex = e.InnerException as WebException;
        if (webex.Status == WebExceptionStatus.ProtocolError)
        {
            var response = (HttpWebResponse)webex.Response;
            if (response.StatusCode == HttpStatusCode.Gone)
                throw new HttpRequestException(HttpStatusCode.Gone.ToString());
        }
    }
    ```
12. Yaptığınız değişiklikleri kaydedin.

## <a name="send-notifications-from-the-webapi-backend"></a>WebAPI arka ucundan bildirim gönderme

Bu bölümde, istemci cihazlarına bildirim göndermeleri için bir yol sunan yeni bir denetleyici ekleyeceksiniz. Bildirim, ASP.NET WebAPI arka ucundaki Azure Notification Hubs .NET Kitaplığı’nı kullanan kullanıcı adı etiketini temel alır.

1. Önceki bölümde **RegisterController** denetleyicisini oluşturduğunuz gibi **NotificationsController** adlı yeni bir denetleyici oluşturun.

2. NotificationsController.cs sınıfına aşağıdaki `using` deyimlerini ekleyin:

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. Aşağıdaki yöntemi **NotificationsController** sınıfına ekleyin:

    Bu kod, Platform Bildirim Hizmeti (PNS) `pns` parametresini temel alan bir bildirim türü gönderir. `to_tag` değeri, iletideki *kullanıcı adı* etiketini ayarlamak için kullanılır. Bu etiket, etkin bir bildirim hub'ı kaydının kullanıcı etiketi ile eşleşmelidir. Bildirim iletisi, POST isteğinin gövdesinden çekilir ve hedef PNS biçimlendirilir.

    Desteklenen cihazlarınızın bildirimleri almak için kullandığı PNS’e bağlı olarak, bildirimler çeşitli biçimler tarafından desteklenmektedir. Örneğin, Windows cihazlarında başka bir PNS tarafından doğrudan desteklenmeyen bir [WNS ile bildirim](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) kullanabilirsiniz. Böyle bir durumda arka ucunuzun, desteklemeyi planladığınız cihazların PNS’si için bildirimi desteklenen bir bildirim biçimine dönüştürmesi gerekir. Daha sonra [NotificationHubClient sınıfında](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx) uygun gönderme API’sini kullanın.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
    {
        var user = HttpContext.Current.User.Identity.Name;
        string[] userTag = new string[2];
        userTag[0] = "username:" + to_tag;
        userTag[1] = "from:" + user;

        Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
        HttpStatusCode ret = HttpStatusCode.InternalServerError;

        switch (pns.ToLower())
        {
            case "wns":
                // Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                break;
            case "apns":
                // iOS
                var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                break;
            case "fcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendFcmNativeNotificationAsync(notif, userTag);
                break;
        }

        if (outcome != null)
        {
            if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
            {
                ret = HttpStatusCode.OK;
            }
        }

        return Request.CreateResponse(ret);
    }
    ```
4. Uygulamayı çalıştırmak ve o ana kadar gerçekleştirdiğiniz işin doğruluğundan emin olmak için **F5** tuşuna basın. Uygulama bir web tarayıcısı açar ve ASP.NET giriş sayfasında görüntülenir.

## <a name="publish-the-new-webapi-backend"></a>Yeni WebAPI arka ucunu yayımlama

Ardından bu uygulamayı tüm cihazlardan erişilebilir kılmak için bir Azure web sitesine dağıtacaksınız.

1. **AppBackend** projesine sağ tıklayıp **Yayımla**’yı seçin.

2. Yayımlama hedefi olarak **Microsoft Azure App Service**’i ve ardından \*\*Yayımla’yı seçin. App Service Oluştur penceresi açılır. Burada, Azure’da ASP.NET web uygulamasını çalıştırmak için gereken tüm Azure kaynaklarını oluşturabilirsiniz.

    ![Microsoft Azure App Service kutucuğu][B15]

3. **App Service Oluştur** penceresinde Azure hesabınızı seçin. **Türü Değiştir** > **Web App**’i seçin. Varsayılan **Web App Adı**’nı değiştirmeyin ve **Abonelik**, **Kaynak Grubu** ve **App Service Planı**’nı seçin.

4. **Oluştur**’u seçin.

5. **Özet** bölümündeki **Site URL** özelliğini not edin. Bu URL, daha sonra bu öğreticide *arka uca ait uç noktanız* olacaktır.

6. **Yayımla**’yı seçin.

Sihirbazı tamamladıktan sonra ASP.NET web uygulamasını Azure’da yayımlar ve ardından uygulamayı varsayılan tarayıcıda açar.  Uygulamanız Azure App Services’te görüntülenebilir.

URL daha önce belirttiğiniz web uygulaması adını http://<uygulama_adı>.azurewebsites.net biçimiyle kullanır.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
