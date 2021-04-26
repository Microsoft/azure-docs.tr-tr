---
title: Azure SignalR hizmetini kullanmayı öğrenmek için hızlı başlangıç
description: Azure SignalR Hizmeti’ni kullanarak ASP.NET Core MVC uygulamaları ile bir sohbet odası oluşturmaya yönelik hızlı başlangıç.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 9099a8620f6e4d87ec38c10226d94b1b3cd3462f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865990"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Hızlı başlangıç: SignalR hizmetini kullanarak sohbet odası oluşturma

Azure SignalR Hizmeti, geliştiricilerin gerçek zamanlı özelliklerle web uygulamalarını kolayca derlemesine yardımcı olan bir Azure hizmetidir. Bu hizmet başlangıçta [ASP.NET Core 2,1 Için SignalR](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1)tabanlıdır, ancak bundan sonra daha sonra sürümleri desteklemektedir.

Bu makalede Azure SignalR Hizmeti ile çalışmaya başlama işlemi gösterilmektedir. Bu hızlı başlangıçta, bir ASP.NET Core MVC web uygulaması kullanarak bir sohbet uygulaması oluşturacaksınız. Bu uygulama, gerçek zamanlı içerik güncelleştirmelerini etkinleştirmek üzere Azure SignalR Hizmeti kaynağınızla bağlantı kuracaktır. Web uygulamasını yerel olarak barındıracak ve birden çok tarayıcı istemcisine bağlanırsınız. Her istemci, diğer tüm istemcilere içerik güncelleştirmeleri gönderebilecektir. 

Bu hızlı başlangıçtaki adımları tamamlamak için herhangi bir kod düzenleyicisini kullanabilirsiniz. Bir seçenek, Windows, macOS ve Linux platformlarında kullanılabilen [Visual Studio Code](https://code.visualstudio.com/).

Bu öğreticinin kodu [AzureSignalR-samples GitHub deposundan](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom) indirilebilir. Ayrıca, bu hızlı başlangıçta kullanılan Azure kaynaklarını [bir SignalR Hizmet betiği oluştur](scripts/signalr-cli-create-service.md)' u izleyerek oluşturabilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Önkoşullar

* [.NET Core SDK](https://dotnet.microsoft.com/download)'i yükler.
* [AzureSignalR-Sample](https://github.com/aspnet/AzureSignalR-samples) GitHub deposunu indirin veya kopyalayın. 

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-azure-signalr-resource"></a>Azure SignalR kaynağı oluşturma

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core web uygulaması oluşturma

Bu bölümde, bir ASP.NET Core MVC web uygulaması projesi oluşturmak için [.NET Core komut satırı arabirimini (CLI)](/dotnet/core/tools/) kullanacaksınız. Visual Studio üzerinden .NET Core CLI kullanmanın avantajı, Windows, macOS ve Linux platformları genelinde kullanılabilir hale gelir. 

1. Projeniz için bir klasör oluşturun. Bu hızlı başlangıç, *E:\Testing\chattest* klasörünü kullanır.

2. Yeni klasörde, projeyi oluşturmak için aşağıdaki komutu çalıştırın:

    ```dotnetcli
    dotnet new mvc
    ```

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnetcore).

## <a name="add-secret-manager-to-the-project"></a>Projeye Gizli Dizi Yöneticisi ekleme

Bu bölümde, projenize [gizli yönetici aracını](/aspnet/core/security/app-secrets) ekleyeceksiniz. Gizli dizi Yöneticisi Aracı, geliştirme çalışması için hassas verileri proje ağacınızdaki dışında depolar. Bu yaklaşım, kaynak kodundaki uygulama gizli anahtarlarının yanlışlıkla paylaşılmasını önlemeye yardımcı olur.

1. *.csproj* dosyanızı açın. *Microsoft.Extensions.SecretManager.Tools* öğesini dahil etmek için bir `DotNetCliToolReference` öğesi ekleyin. Ayrıca `UserSecretsId` , *chattest. csproj* için aşağıdaki kodda gösterildiği gibi bir öğesi ekleyin ve dosyayı kaydedin.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.2" />
    </ItemGroup>

    </Project>
    ```

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnetcore).

## <a name="add-azure-signalr-to-the-web-app"></a>Web uygulamasına Azure SignalR ekleme

1. `Microsoft.Azure.SignalR`Aşağıdaki komutu çalıştırarak NuGet paketine bir başvuru ekleyin:

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. Projenizin paketlerini geri yüklemek için aşağıdaki komutu çalıştırın:

    ```dotnetcli
    dotnet restore
    ```

3. Gizli bir yöneticiye *Azure: SignalR: ConnectionString* adlı bir gizli dizi ekleyin. 

    Bu gizli dizi, SignalR Hizmetinizin kaynağına erişmeye yarayan bağlantı dizesini içerir. *Azure: SignalR: ConnectionString* , bir bağlantı kurmak Için SignalR 'nin aradığı varsayılan yapılandırma anahtarıdır. Aşağıdaki komutta bulunan değeri, SignalR hizmet kaynağınızın bağlantı dizesiyle değiştirin.

    Bu komutu *. csproj* dosyasıyla aynı dizinde çalıştırmalısınız.

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"
    ```

    Gizli dizi Yöneticisi, yalnızca yerel olarak barındırılırken Web uygulamasını test etmek için kullanılacaktır. Daha sonraki bir öğreticide, sohbet web uygulamasını Azure 'a dağıtırsınız. Web uygulaması Azure 'a dağıtıldıktan sonra, bağlantı dizesini gizli yönetici ile depolamak yerine bir uygulama ayarı kullanırsınız.

    Bu gizli dizi Yapılandırma API 'siyle erişilir. İki nokta (:) yapılandırma adında, desteklenen tüm platformlarda Yapılandırma API 'SI ile birlikte kullanılır. Bkz. [ortama göre yapılandırma](/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider).


4. *Başlangıç. cs* ' i açın ve `ConfigureServices` ve yöntemlerini çağırarak Azure SignalR hizmetini kullanmak üzere yöntemi `AddSignalR()` güncelleştirin `AddAzureSignalR()` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR()
                .AddAzureSignalR();
    }
    ```

    Parametresi öğesine geçirilmeyen `AddAzureSignalR()` Bu kod, SignalR hizmeti kaynak bağlantı dizesinin varsayılan yapılandırma anahtarını kullanır. Varsayılan yapılandırma anahtarı *Azure: SignalR: ConnectionString*.

5. *Startup. cs* dosyasında, `Configure` aşağıdaki kodla değiştirerek yöntemi güncelleştirin.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();
        app.UseFileServer();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ChatHub>("/chat");
        });
    }
    ```

### <a name="add-a-hub-class"></a>Hub sınıfı ekleme

SignalR 'de, bir hub, istemciden çağrılabilen bir yöntemler kümesini kullanıma sunan bir çekirdek bileşendir. Bu bölümde, iki yöntemle bir hub sınıf tanımlayacaksınız:

* `Broadcast`: Bu yöntem bir iletiyi tüm istemcilere yayınlar.
* `Echo`: Bu yöntem bir iletiyi çağırana geri gönderir.

Her iki yöntem de `Clients` ASP.NET Core SignalR SDK 'nın sağladığı arabirimi kullanır. Bu arabirim, istemcilere içerik gönderebilmeniz için tüm bağlı istemcilere erişmenizi sağlar.

1. Proje dizininizde *Hub* adlı yeni bir klasör ekleyin. Yeni klasöre *ChatHub. cs* adlı yeni bir merkez kodu dosyası ekleyin.

2. Hub sınıfınızı tanımlamak ve dosyayı kaydetmek için *ChatHub. cs* dosyasına aşağıdaki kodu ekleyin.

    *SignalR. Mvc*'den farklı bir proje adı kullandıysanız, bu sınıf için ad alanını güncelleştirin.

    ```csharp
    using Microsoft.AspNetCore.SignalR;
    using System.Threading.Tasks;
    
    namespace SignalR.Mvc
    {
        public class ChatHub : Hub
        {
            public Task BroadcastMessage(string name, string message) =>
                Clients.All.SendAsync("broadcastMessage", name, message);
    
            public Task Echo(string name, string message) =>
                Clients.Client(Context.ConnectionId)
                       .SendAsync("echo", name, $"{message} (echo from server)");
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Web uygulaması için istemci arabirimini ekleme

Bu sohbet odası uygulamasının istemci kullanıcı arabirimi, *Wwwroot* dizinindeki *index.html* adlı bir dosyada HTML ve JavaScript 'ten oluşur.

*CSS/site. css* dosyasını [örnekler deposunun](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot) *Wwwroot* klasöründen kopyalayın. Projenizin *CSS/site. css* ' i kopyaladığınız bir ile değiştirin.

*index.html*'nin ana kodu aşağıda verilmiştir:

*Wwwroot* dizininde *index.html* adlı yeni bir dosya oluşturun, kopyalayın ve aşağıdaki HTML 'yi yeni oluşturulan dosyaya yapıştırın:

```html
<!DOCTYPE html>
<html>
<head>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="css/site.css" rel="stylesheet" />
    <title>Azure SignalR Group Chat</title>
</head>
<body>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px; padding-bottom: 30px;">Azure SignalR Group Chat</h2>
    <div class="container" style="height: calc(100% - 110px);">
        <div id="messages" style="background-color: whitesmoke; "></div>
        <div style="width: 100%; border-left-style: ridge; border-right-style: ridge;">
            <textarea id="message"
                      style="width: 100%; padding: 5px 10px; border-style: hidden;"
                      placeholder="Type message and press Enter to send..."></textarea>
        </div>
        <div style="overflow: auto; border-style: ridge; border-top-style: hidden;">
            <button class="btn-warning pull-right" id="echo">Echo</button>
            <button class="btn-success pull-right" id="sendmessage">Send</button>
        </div>
    </div>
    <div class="modal alert alert-danger fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <div>Connection Error...</div>
                    <div><strong style="font-size: 1.5em;">Hit Refresh/F5</strong> to rejoin. ;)</div>
                </div>
            </div>
        </div>
    </div>

    <!--Reference the SignalR library. -->
    <script src="https://cdn.jsdelivr.net/npm/@microsoft/signalr@3.1.8/dist/browser/signalr.min.js"></script>

    <!--Add script to update the page and send messages.-->
    <script type="text/javascript">
        document.addEventListener('DOMContentLoaded', function () {

            const generateRandomName = () =>
                Math.random().toString(36).substring(2, 10);

            let username = generateRandomName();
            const promptMessage = 'Enter your name:';
            do {
                username = prompt(promptMessage, username);
                if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
                    username = '';
                    promptMessage = 'Invalid input. Enter your name:';
                }
            } while (!username)

            const messageInput = document.getElementById('message');
            messageInput.focus();

            function createMessageEntry(encodedName, encodedMsg) {
                var entry = document.createElement('div');
                entry.classList.add("message-entry");
                if (encodedName === "_SYSTEM_") {
                    entry.innerHTML = encodedMsg;
                    entry.classList.add("text-center");
                    entry.classList.add("system-message");
                } else if (encodedName === "_BROADCAST_") {
                    entry.classList.add("text-center");
                    entry.innerHTML = `<div class="text-center broadcast-message">${encodedMsg}</div>`;
                } else if (encodedName === username) {
                    entry.innerHTML = `<div class="message-avatar pull-right">${encodedName}</div>` +
                        `<div class="message-content pull-right">${encodedMsg}<div>`;
                } else {
                    entry.innerHTML = `<div class="message-avatar pull-left">${encodedName}</div>` +
                        `<div class="message-content pull-left">${encodedMsg}<div>`;
                }
                return entry;
            }

            function bindConnectionMessage(connection) {
                var messageCallback = function (name, message) {
                    if (!message) return;
                    var encodedName = name;
                    var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
                    var messageEntry = createMessageEntry(encodedName, encodedMsg);

                    var messageBox = document.getElementById('messages');
                    messageBox.appendChild(messageEntry);
                    messageBox.scrollTop = messageBox.scrollHeight;
                };
                connection.on('broadcastMessage', messageCallback);
                connection.on('echo', messageCallback);
                connection.onclose(onConnectionError);
            }

            function onConnected(connection) {
                console.log('connection started');
                connection.send('broadcastMessage', '_SYSTEM_', username + ' JOINED');
                document.getElementById('sendmessage').addEventListener('click', function (event) {
                    if (messageInput.value) {
                        connection.send('broadcastMessage', username, messageInput.value);
                    }

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
                    connection.send('echo', username, messageInput.value);

                    messageInput.value = '';
                    messageInput.focus();
                    event.preventDefault();
                });
            }

            function onConnectionError(error) {
                if (error && error.message) {
                    console.error(error.message);
                }
                var modal = document.getElementById('myModal');
                modal.classList.add('in');
                modal.style = 'display: block;';
            }

            const connection = new signalR.HubConnectionBuilder()
                .withUrl('/chat')
                .build();
            bindConnectionMessage(connection);
            connection.start()
                .then(() => onConnected(connection))
                .catch(error => console.error(error.message));
        });
    </script>
</body>
</html>
```

*index.html* içindeki kod, `HubConnectionBuilder.build()` Azure SIGNALR kaynağına http bağlantısı kurmak için çağırır.

Bağlantı başarılı olursa, o bağlantı `bindConnectionMessage` konumuna geçirilir ve istemciye gönderilen gelen içerik için olay işleyicileri ekler. 

`HubConnection.start()`, hub ile iletişim başlatır. Ardından, `onConnected()` düğme olay işleyicilerini ekler. Bu işleyiciler, bağlantıyı kullanarak bu istemcinin tüm bağlı istemcilere içerik güncelleştirmeleri göndermesine olanak tanır.

## <a name="add-a-development-runtime-profile"></a>Geliştirme çalışma zamanı profili ekleme

Bu bölümde, ASP.NET Core için bir geliştirme çalışma zamanı ortamı ekleyeceksiniz. Daha fazla bilgi için bkz. [ASP.NET Core birden çok ortamla çalışma](/aspnet/core/fundamentals/environments).

1. Projenizde *Özellikler* adlı bir klasör oluşturun.

2. Klasöre aşağıdaki içerikle *launchSettings.js* adlı yeni bir dosya ekleyin ve dosyayı kaydedin.

    ```json
    {
        "profiles" : {
            "ChatRoom": {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnetcore).

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. .NET Core CLI kullanarak uygulamayı derlemek için komut kabuğu 'nda aşağıdaki komutu çalıştırın:

    ```dotnetcli
    dotnet build
    ```

1. Oluşturma başarıyla tamamlandıktan sonra, Web uygulamasını yerel olarak çalıştırmak için aşağıdaki komutu çalıştırın:

    ```dotnetcli
    dotnet run
    ```

    Uygulama, geliştirme çalışma zamanı profilinizde yapılandırıldığı şekilde 5000 numaralı bağlantı noktasında yerel olarak barındırılır:

    ```output
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: https://localhost:5001
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: http://localhost:5000
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: E:\Testing\chattest
    ```

1. İki tarayıcı penceresi açın. Her tarayıcıda öğesine gidin `http://localhost:5000` . Adınızı girmeniz istenir. Her iki istemci için bir istemci adı girin ve **Gönder** düğmesini kullanarak ileti içeriğini her iki istemci arasında iletme sınamasını yapın.

    ![Azure SignalR grup sohbeti örneği](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnetcore).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğreticiye devam ederseniz, bu hızlı başlangıçta oluşturulan kaynakları tutabilir ve yeniden kullanabilirsiniz.

Hızlı Başlangıç örnek uygulamasıyla işiniz bittiğinde, ücretlerden kaçınmak için bu hızlı başlangıçta oluşturulan Azure kaynaklarını silebilirsiniz. 

> [!IMPORTANT]
> Bir kaynak grubunun silinmesi geri alınamaz ve bu gruptaki tüm kaynakları içerir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. Bu örneği tutmak istediğiniz kaynakları içeren mevcut bir kaynak grubunda barındırmak için kaynaklar oluşturduysanız, kaynak grubunu silmek yerine her kaynağı dikey penceresinden ayrı ayrı silebilirsiniz.

[Azure portalda](https://portal.azure.com) oturum açın ve **Kaynak grupları**’nı seçin.

**Ada göre filtrele** metin kutusuna kaynak grubunuzun adını yazın. Bu hızlı başlangıçtaki yönergelerde *SignalRTestResources* adlı bir kaynak grubu kullanılmıştır. Sonuç listesindeki kaynak grubunuzda, **kaynak grubunu silmek**> üç nokta (**...**) simgesini seçin.

![Kaynak grubunu silmeye yönelik seçimler](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Kaynak grubunun silinmesini onaylamanız istenir. Onaylamak için kaynak grubunuzun adını girin ve **Sil**' i seçin.

Birkaç dakika sonra kaynak grubu ve bu gruptaki kaynakların tümü silinir.

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnetcore).

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta yeni bir Azure SignalR hizmeti kaynağı oluşturdunuz. Daha sonra, içerik güncelleştirmelerini gerçek zamanlı olarak birden çok bağlı istemciye göndermek için bir ASP.NET Core Web uygulamasıyla kullandınız. Azure SignalR hizmetini kullanma hakkında daha fazla bilgi edinmek için, kimlik doğrulamasını gösteren öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure SignalR hizmeti kimlik doğrulaması](./signalr-concept-authenticate-oauth.md)
