---
title: Azure SignalR hizmeti istemcilerinin kimlik doğrulamasına yönelik kılavuz
description: E2e örneğini izleyerek kendi kimlik doğrulamasını nasıl uygulayacağınızı ve Azure SignalR hizmeti ile tümleştirmeyi öğrenin.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: cc955adffbe7df5809f9c4c860877ad22df3e99b
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158286"
---
# <a name="azure-signalr-service-authentication"></a>Azure SignalR hizmeti kimlik doğrulaması

Bu öğretici, hızlı başlangıçta tanıtılan sohbet odası uygulamasını temel alır. [SignalR Hizmeti ile sohbet odası oluşturma](signalr-quickstart-dotnet-core.md) alıştırmasını tamamlamadıysanız, önce o alıştırmayı tamamlayın.

Bu öğreticide, kendi kimlik doğrulamanızı uygulamayı ve bunu Microsoft Azure SignalR Hizmeti ile tümleştirmeyi öğreneceksiniz.

İlk olarak hızlı başlangıcın sohbet odası uygulamasında kullanılan kimlik doğrulaması gerçek dünya senaryoları için fazla basittir. Uygulama her istemcinin kim olduğunu belirtmesine ve basit bir şekilde sunucunun bunu kabul etmesine izin verir. Bu yaklaşım hilekar bir kullanıcının hassas verilere erişmek için başkalarının kimliğine bürünebildiği gerçek dünya uygulamalarında pek kullanışlı değildir.

[GitHub](https://github.com/), endüstri standardında [OAuth](https://oauth.net/) adlı popüler protokolü temel alan kimlik doğrulama API'leri sağlar. Bu API'ler üçüncü taraf uygulamalarında GitHub hesaplarının kimliğini doğrulamaya olanak tanır. Bu öğreticide, istemcinin sohbet odası uygulamasında oturum açmasına izin vermeden önce GitHub hesabı üzerinden kimlik doğrulaması yapmak için bu API'leri kullanacaksınız. GitHub hesabının kimliğini doğruladıktan sonra, hesap bilgileri web istemcisinin kimlik doğrulamasında kullanılmak üzere tanımlama bilgisi olarak eklenecektir.

GitHub aracılığıyla sağlanan OAuth kimlik doğrulama API'leri hakkında daha fazla bilgi için bkz. [Kimlik Doğrulamasının Temelleri](https://developer.github.com/v3/guides/basics-of-authentication/).

Bu hızlı başlangıçtaki adımları tamamlamak için herhangi bir kod düzenleyicisini kullanabilirsiniz. Ancak, Windows, macOS ve Linux platformlarında sağlanan [Visual Studio Code](https://code.visualstudio.com/) mükemmel bir seçenektir.

Bu öğreticinin kodu [AzureSignalR-samples GitHub deposundan](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat) indirilebilir.

![Azure'da barındırılan OAuth](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * GitHub hesabınızla yeni bir OAuth uygulaması kaydetme
> * GitHub kimlik doğrulamasını desteklemek için kimlik doğrulama denetleyicisi ekleme
> * ASP.NET Core web uygulamanızı Azure'a dağıtma

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdaki ön koşullara sahip olmanız gerekir:

* [GitHub](https://github.com/)'da oluşturulan bir hesap
* [Git](https://git-scm.com/)
* [.NET Core SDK](https://www.microsoft.com/net/download/windows)
* [Yapılandırılmış Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* [AzureSignalR-Sample](https://github.com/aspnet/AzureSignalR-samples) GitHub deposunu indirin veya kopyalayın.

## <a name="create-an-oauth-app"></a>OAuth uygulaması oluşturma

1. Web tarayıcısını açın, `https://github.com` adresine gidin ve hesabınızda oturum açın.

2. Hesabınız için, **Ayarlar** > **Geliştirici ayarları**'na gidin ve **Yeni uygulama kaydet**'e veya **OAuth Uygulamaları**'nın altında *Yeni OAuth Uygulaması*'na tıklayın.

3. Yeni OAuth Uygulaması için aşağıdaki ayarları kullanın ve ardından **Uygulamayı kaydet**'e tıklayın:

    | Ayar Adı | Önerilen Değer | Açıklama |
    | ------------ | --------------- | ----------- |
    | Uygulama adı | *Azure SignalR Sohbeti* | GitHub kullanıcısı, kimlik doğrulama yaptıkları uygulamayı tanıyabilmelidir ve güvenmelidir.   |
    | Giriş sayfası URL'si | `http://localhost:5000/home` | |
    | Uygulama açıklaması | *GitHub kimlik doğrulamasıyla Azure SignalR hizmetini kullanan bir sohbet odası örneği* | Uygulama kullanıcılarınızın kullanılan kimlik doğrulamanın bağlamını anlayabilmesine yardımcı olacak, yararlı bir uygulama açıklaması. |
    | Yetkilendirme geri çağırma URL'si | `http://localhost:5000/signin-github` | Bu ayar, OAuth uygulamanız için en önemli ayardır. Bu, başarılı bir kimlik doğrulamasının ardından GitHub'ın kullanıcıyı döndürdüğü geri çağırma URL'sidir. Bu öğreticide, *AspNet.Security.OAuth.GitHub* paketi için varsayılan geri çağırma URL'sini ( */signin-github*) kullanmalısınız.  |

4. Yeni OAuth uygulama kaydı tamamlandıktan sonra, aşağıdaki komutları kullanarak *İstemci Kimliği* ve *İstemci Parolası*'nı Parola Yöneticisi'ne ekleyin. *Your_GitHub_Client_Id* ve *Your_GitHub_Client_Secret* değerlerini OAuth uygulamanızın değerleriyle değiştirin.

        dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
        dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret

## <a name="implement-the-oauth-flow"></a>OAuth akışını uygulama

### <a name="update-the-startup-class-to-support-github-authentication"></a>Startup sınıfını GitHub kimlik doğrulamasını destekleyecek şekilde güncelleştirme

1. En son *Microsoft.AspNetCore.Authentication.Cookies* ve *AspNet.Security.OAuth.GitHub* paketlerine başvuru ekleyin ve tüm paketleri geri yükleyin.

        dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
        dotnet add package AspNet.Security.OAuth.GitHub -v 2.0.0-rc2-final
        dotnet restore

1. *Startup.cs*'yi açın ve aşağıdaki ad alanları için `using` deyimlerini ekleyin:

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. `Startup` sınıfının üst kısmında, GitHub OAuth uygulamasının parolalarını barındıran Parola Yöneticisi anahtarları için sabitler ekleyin.

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. GitHub OAuth uygulamasıyla kimlik doğrulamasını desteklemek için `ConfigureServices` yöntemine aşağıdaki kodu ekleyin:

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. `GetUserCompanyInfoAsync` sınıfına `Startup` yardımcı yöntemini ekleyin.

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }
    ```

5. Startup sınıfının `Configure` yöntemini aşağıdaki kod satırıyla güncelleştirin ve dosyayı kaydedin.

    ```csharp
    app.UseAuthentication();
    ```

### <a name="add-an-authentication-controller"></a>Kimlik doğrulama denetleyicisi ekleme

Bu bölümde, GitHub OAuth uygulamasını kullanarak istemcilerin kimliğini doğrulayan bir `Login` API'si uygulayacaksınız. Kimlik doğrulaması yapıldıktan sonra, API istemciyi sohbet uygulamasına yeniden yönlendirmeden önce web istemcisi yanıtına bir tanımlama bilgisi ekleyecek. Daha sonra bu tanımlama bilgisi istemciyi tanımlamak için kullanılacak.

1. *chattest\Controllers* dizinine yeni bir denetleyici kod dosyası ekleyin. Dosyayı *AuthController.cs* olarak adlandırın.

2. Kimlik doğrulama denetleyicisi için aşağıdaki kodu ekleyin. Proje dizininiz *chattest* değilse ad alanını güncelleştirdiğinizden emin olun:

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }
    ```

3. Yaptığınız değişiklikleri kaydedin.

### <a name="update-the-hub-class"></a>Hub sınıfını güncelleştirme

Varsayılan olarak bir web istemcisi SignalR Hizmeti'ne bağlanmaya çalıştığında, dahili olarak sağlanan bir erişim belirteci temelinde bağlantı verilir. Bu erişim belirteci doğrulanmış bir kimlikle ilişkilendirilmemiştir. Bu erişim aslında anonim erişimdir.

Bu bölümde, hub sınıfına `Authorize` özniteliğini ekleyerek ve hub yöntemlerini kimliği doğrulanmış kullanıcının talebinden kullanıcı adını okuyacak şekilde güncelleştirerek, gerçek kimlik doğrulamasını açacaksınız.

1. *Hub\Chat.cs*'yi açın ve şu ad alanlarına başvurular ekleyin:

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. Hub kodunu aşağıda gösterildiği gibi güncelleştirin. Bu kod `Authorize` sınıfına `Chat` özniteliğini ekler ve hub yöntemlerinde kullanıcının doğrulanmış kimliğini kullanır. Ayrıca, her yeni istemci bağlantısında sohbet odasında günlüğe bir sistem iletisi kaydedecek olan `OnConnectedAsync` yöntemini ekler.

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. Yaptığınız değişiklikleri kaydedin.

### <a name="update-the-web-client-code"></a>Web istemcisi kodunu güncelleştirme

1. *wwwroot\index.html*'yi açın ve kullanıcı adı isteyen kodu kimlik doğrulama denetleyicisi tarafından döndürülen tanımlama bilgisini kullanacak olan kodla değiştirin.

    Aşağıdaki kodu *index.html*'den çıkarın:

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    Tanımlama bilgisini kullanmak için yukarıdaki kodun yerine aşağıdaki kodu ekleyin:

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');
    ```

2. Tanımlama bilgisini kullanmaya yönelik kodu eklediğiniz satırın hemen altına, `appendMessage` işlevi için aşağıdaki açıklamayı ekleyin:

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. `bindConnectionMessage` ve `onConnected` işlevlerini, `appendMessage` kullanmak üzere aşağıdaki kodla güncelleştirin.

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }
    ```

4. *index.html*'nin en altında, kullanıcıdan oturum açmasını istemek için `connection.start()` hata işleyicisini aşağıda gösterildiği gibi güncelleştirin.

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. Yaptığınız değişiklikleri kaydedin.

## <a name="build-and-run-the-app-locally"></a>Uygulamayı derleme ve yerel olarak çalıştırma

1. Tüm dosyalardaki değişiklikleri kaydedin.

2. .NET Core CLI kullanarak uygulamayı derleyin, komut kabuğunda aşağıdaki komutu yürütün:

        dotnet build

3. Derleme başarıyla tamamlandıktan sonra web uygulamasını yerel olarak çalıştırmak için aşağıdaki komutu yürütün:

        dotnet run

    Varsayılan değer uygulamanın yerel olarak 5000 numaralı bağlantı noktasında barındırılmasıdır:

        E:\Testing\chattest>dotnet run
        Hosting environment: Production
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.

4. Bir tarayıcı penceresi başlatın ve `http://localhost:5000` adresine gidin. GitHub ile oturum açmak için en üstteki **burada** bağlantısına tıklayın.

    ![Azure'da barındırılan OAuth](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

    Sohbet uygulamasının GitHub hesabınıza erişimini yetkilendirmeniz istenir. **Yetkilendir** düğmesine tıklayın.

    ![OAuth Uygulamasını yetkilendirme](media/signalr-concept-authenticate-oauth/signalr-authorize-oauth-app.png)

    Geriye sohbet uygulamasına yönlendirilirsiniz ve GitHub hesap adınızla oturum açarsınız. Web uygulaması, eklediğiniz yeni kimlik doğrulamasını kullanıp kimliğinizi doğrulayarak hesap adınızı saptar.

    ![Tanımlanan hesap](media/signalr-concept-authenticate-oauth/signalr-oauth-account-identified.png)

    Artık sohbet uygulaması GitHub ile kimlik doğrulaması yaptığından ve kimlik doğrulama bilgilerini tanımlama bilgileri olarak depoladığından, diğer kullanıcıların kendi hesaplarıyla kimlik doğrulaması yapabilmesi ve diğer iş istasyonlarıyla iletişim kurabilmesi için bunu Azure'a dağıtmanız gerekir.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-app-to-azure"></a>Uygulamayı Azure’da dağıtma

Bu bölümde, Azure 'da ASP.NET uygulamanızı barındırmak üzere [Azure App Service](https://docs.microsoft.com/azure/app-service/) yeni bir Web uygulaması oluşturmak Için Azure Cloud Shell Azure komut satırı arabirimi 'NI (CLI) kullanacaksınız. Web uygulaması, yerel Git dağıtımını kullanacak şekilde yapılandırılacak. Web uygulaması ayrıca SignalR bağlantı dizenizle, GitHub OAuth uygulaması parolalarıyla ve dağıtım kullanıcısıyla da yapılandırılacak.

Bu bölümdeki adımlarda Azure CLI için *signalr* uzantısı kullanılır. Aşağıdaki komutu yürüterek Azure CLI için *signalr* uzantısını yükleyin:

```azurecli-interactive
az extension add -n signalr
```

Aşağıdaki kaynakları oluştururken, SignalR Hizmeti kaynağınızın içinde bulunduğu kaynak grubunun aynısı kullandığınızdan emin olun. Bu yaklaşım, daha sonra tüm kaynakları kaldırmak istediğinizde temizleme işlemini çok kolaylaştırır. Verilen örneklerde, önceki öğreticilerde önerilen grup adını (*SignalRTestResources*) kullandığınız varsayılır.

### <a name="create-the-web-app-and-plan"></a>Web uygulamasını ve planı oluşturma

Aşağıdaki komutların metnini kopyalayın ve parametreleri güncelleştirin. Güncelleştirilmiş betiği Azure Cloud Shell’e yapıştırın ve yeni App Service planıyla web uygulamasını oluşturmak için **Enter** tuşuna basın.

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan
```

| Parametre | Açıklama |
| -------------------- | --------------- |
| ResourceGroupName | Bu kaynak grubu adı, önceki öğreticilerde önerilmiştir. Tüm öğretici kaynaklarını aynı grup altında tutmak iyi bir fikirdir. Önceki öğreticilerde kullandığınız kaynak grubunun aynısını kullanın. |
| WebAppPlan | Yeni ve benzersiz bir App Service Planı adı girin. |
| WebAppName | Bu, yeni web uygulamasının adı ve URL'nin bir parçası olacaktır. Benzersiz bir ad kullanın. Örneğin, signalrtestwebapp22665120.   |

### <a name="add-app-settings-to-the-web-app"></a>Web uygulamasına uygulama ayarlarını ekleme

Bu bölümde, aşağıdaki bileşenler için uygulama ayarlarını ekleyeceksiniz:

* SignalR Hizmeti kaynak bağlantı dizesi
* GitHub OAuth uygulaması istemci kimliği
* GitHub OAuth uygulaması istemci parolası

Aşağıdaki komutların metnini kopyalayın ve parametreleri güncelleştirin. Güncelleştirilmiş betiği Azure Cloud Shell’e yapıştırın ve uygulama ayarlarını eklemek için **Enter** tuşuna basın:

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $SignalRServiceResource \
  --resource-group $ResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure__SignalR__ConnectionString=$primaryConnectionString"

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId"
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret"
```

| Parametre | Açıklama |
| -------------------- | --------------- |
| GitHubClientId | GitHub OAuth Uygulamanız için bu değişken gizli İstemci Kimliğini atayın. |
| GitHubClientSecret | GitHub OAuth Uygulamanız için bu değişken gizli parolayı atayın. |
| ResourceGroupName | Bu değişkeni önceki bölümde kullandığınız kaynak grubu adının aynısını olacak şekilde güncelleştirin. |
| SignalRServiceResource | Bu değişkeni hızlı başlangıçta oluşturduğunuz SignalR Hizmeti kaynağının adıyla güncelleştirin. Örneğin, signalrtestsvc48778624. |
| WebAppName | Bu değişkeni önceki bölümde oluşturduğunuz yeni web uygulamasının adıyla güncelleştirin. |

### <a name="configure-the-web-app-for-local-git-deployment"></a>Web uygulamasını yerel Git dağıtımı için yapılandırma

Azure Cloud Shell'de aşağıdaki betiği yapıştırın. Bu betik, kodunuzu Git ile web uygulamasına dağıtırken kullanılacak yeni bir dağıtım kullanıcı adı ve parolası oluşturur. Ayrıca betik, dağıtm için web uygulamasını yerel Git deposuyla yapılandırır ve Git dağıtım URL'sine döner.

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv
```

| Parametre | Açıklama |
| -------------------- | --------------- |
| DeploymentUserName | Yeni bir dağıtım kullanıcı adı seçin. |
| DeploymentUserPassword | Yeni dağıtım kullanıcısı için parola seçin. |
| ResourceGroupName | Önceki bölümde kullandığınız kaynak grubu adının aynısını kullanın. |
| WebAppName | Bu, daha önce oluşturduğunuz yeni web uygulamasının adı olacaktır. |

Bu komuttan döndürülen Git dağıtım URL'sini not edin. Bu URL'yi daha sonra kullanacaksınız.

### <a name="deploy-your-code-to-the-azure-web-app"></a>Kodunuzu Azure web uygulamasına dağıtma

Kodunuzu dağıtmak için, Git kabuğunda aşağıdaki komutları yürütün.

1. Proje dizininizin köküne gidin. Projeniz Git deposuyla başlatılmadıysa, şu komutu yürütün:

    ```bash
    git init
    ```

2. Daha önce not aldığınız Git dağıtım URL'si için uzak bağlantı ekleyin:

    ```bash
    git remote add Azure <your git deployment url>
    ```

3. Başlatılan depodaki tüm dosyaları hazırlayın ve bir işleme ekleyin.

    ```bash
    git add -A
    git commit -m "init commit"
    ```

4. Kodunuzu Azure'da web uygulamasına dağıtın.

    ```bash
    git push Azure master
    ```

    Kodu Azure'a dağıtmak için kimlik doğrulaması yapmanız istenir. Yukarıda oluşturduğunuz dağıtım kullanıcısının kullanıcı adını ve parolasını girin.

### <a name="update-the-github-oauth-app"></a>GitHub OAuth uygulamasını güncelleştirme

Yapmanız gereken son işlem GitHub OAuth uygulamasının **Giriş sayfası URL'si** ve **Yetkilendirme geri çağırma URL'si** değerlerini yeni barındırılan uygulamaya işaret edecek şekilde güncelleştirmektir.

1. Tarayıcıda [https://github.com](https://github.com) bağlantısını açın ve hesabınızın **Ayarlar** > **Geliştirici Ayarları** > **Oauth Uygulamaları** bölümüne gidin.

2. Kimlik doğrulama uygulamanıza tıklayın ve **Giriş sayfası URL'si** ve **Yetkilendirme geri çağırma URL'si** değerlerini aşağıda gösterildiği gibi güncelleştirin:

    | Ayar | Örnek |
    | ------- | ------- |
    | Giriş sayfası URL'si | https://signalrtestwebapp22665120.azurewebsites.net/home |
    | Yetkilendirme geri çağırma URL'si | https://signalrtestwebapp22665120.azurewebsites.net/signin-github |

3. Web uygulamanızın URL'sine gidin ve uygulamayı test edin.

    ![Azure'da barındırılan OAuth](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğretici ile devam edecekseniz, bu hızlı başlangıçta oluşturulan kaynakları tutabilir ve sonraki öğreticide yeniden kullanabilirsiniz.

Aksi takdirde, hızlı başlangıç örnek uygulamasını tamamladıysanız ücret yansıtılmaması için bu hızlı başlangıçta oluşturulan Azure kaynaklarını silebilirsiniz.

> [!IMPORTANT]
> Bir kaynak grubunu silme işlemi geri alınamaz ve kaynak grubunun ve içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. Bu örneği, tutmak istediğiniz kaynakları içeren mevcut bir kaynak grubunda barındırmak için kaynaklar oluşturduysanız, kaynak grubunu silmek yerine her kaynağı kendi ilgili dikey penceresinden tek tek silebilirsiniz.

[Azure portalında](https://portal.azure.com) oturum açın ve **Kaynak grupları**’na tıklayın.

**Ada göre filtrele...** metin kutusuna kaynak grubunuzun adını girin. Bu makaledeki yönergelerde *SignalRTestResources* adlı bir kaynak grubu kullanılmıştır. Sonuç listesindeki kaynak grubunuzda **...** ve sonra **Kaynak grubunu sil**’e tıklayın.

![Sil](./media/signalr-concept-authenticate-oauth/signalr-delete-resource-group.png)

Kaynak grubunun silinmesini onaylamanız istenir. Onaylamak için kaynak grubunuzun adını yazın ve **Sil**’e tıklayın.

Birkaç dakika sonra kaynak grubu ve içerdiği kaynakların tümü silinir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure SignalR Hizmeti ile kimlik doğrulamasına daha iyi bir yaklaşım sağlamak için OAuth ile kimlik doğrulaması eklediniz. Azure SignalR Sunucusu’nu kullanma hakkında daha fazla bilgi almak için SignalR Hizmetine yönelik Azure CLI örnekleri bölümüne devam edin.

> [!div class="nextstepaction"]
> [Azure SignalR CLI Örnekleri](./signalr-reference-cli.md)