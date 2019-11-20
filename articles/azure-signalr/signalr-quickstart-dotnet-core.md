---
title: Azure SignalR hizmetini kullanmayı öğrenmek için hızlı başlangıç
description: Azure SignalR Hizmeti’ni kullanarak ASP.NET Core MVC uygulamaları ile bir sohbet odası oluşturmaya yönelik hızlı başlangıç.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: 022780f2b37c8bed49c81774d443b69bae41e5e7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476753"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Hızlı başlangıç: SignalR hizmetini kullanarak sohbet odası oluşturma


Azure SignalR Hizmeti, geliştiricilerin gerçek zamanlı özelliklerle web uygulamalarını kolayca derlemesine yardımcı olan bir Azure hizmetidir. Bu hizmet [ASP.NET Core 2,1 Için SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1)tabanlıdır, ancak [ASP.NET Core 3,0 için SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0)'yi de destekler.

Bu makalede Azure SignalR Hizmeti ile çalışmaya başlama işlemi gösterilmektedir. Bu hızlı başlangıçta, bir ASP.NET Core MVC web uygulaması kullanarak bir sohbet uygulaması oluşturacaksınız. Bu uygulama, gerçek zamanlı içerik güncelleştirmelerini etkinleştirmek üzere Azure SignalR Hizmeti kaynağınızla bağlantı kuracaktır. Web uygulamasını yerel olarak barındıracak ve birden çok tarayıcı istemcisine bağlanırsınız. Her istemci, diğer tüm istemcilere içerik güncelleştirmeleri gönderebilecektir. 

Bu hızlı başlangıçtaki adımları tamamlamak için herhangi bir kod düzenleyicisini kullanabilirsiniz. Bir seçenek, Windows, macOS ve Linux platformlarında kullanılabilen [Visual Studio Code](https://code.visualstudio.com/).

Bu öğreticinin kodu [AzureSignalR-samples GitHub deposundan](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom) indirilebilir. Ayrıca, bu hızlı başlangıçta kullanılan Azure kaynaklarını [bir SignalR Hizmet betiği oluştur](scripts/signalr-cli-create-service.md)' u izleyerek oluşturabilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Önkoşullar

* [.NET Core SDK](https://www.microsoft.com/net/download/windows)'i yükler.
* [AzureSignalR-Sample](https://github.com/aspnet/AzureSignalR-samples) GitHub deposunu indirin veya kopyalayın. 

## <a name="create-an-azure-signalr-resource"></a>Azure SignalR kaynağı oluşturma

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core web uygulaması oluşturma

Bu bölümde, bir ASP.NET Core MVC web uygulaması projesi oluşturmak için [.NET Core komut satırı arabirimini (CLI)](https://docs.microsoft.com/dotnet/core/tools/) kullanacaksınız. Visual Studio üzerinden .NET Core CLI kullanmanın avantajı, Windows, macOS ve Linux platformları genelinde kullanılabilir hale gelir. 

1. Projeniz için bir klasör oluşturun. Bu hızlı başlangıç, *E:\Testing\chattest* klasörünü kullanır.

2. Yeni klasörde, projeyi oluşturmak için aşağıdaki komutu çalıştırın:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Projeye Gizli Dizi Yöneticisi ekleme

Bu bölümde, projenize [gizli yönetici aracını](https://docs.microsoft.com/aspnet/core/security/app-secrets) ekleyeceksiniz. Gizli dizi Yöneticisi Aracı, geliştirme çalışması için hassas verileri proje ağacınızdaki dışında depolar. Bu yaklaşım, kaynak kodundaki uygulama gizli anahtarlarının yanlışlıkla paylaşılmasını önlemeye yardımcı olur.

1. *.csproj* dosyanızı açın. `DotNetCliToolReference`Microsoft.Extensions.SecretManager.Tools*öğesini dahil etmek için bir* öğesi ekleyin. Ayrıca, *chattest. csproj*için aşağıdaki kodda gösterildiği gibi bir `UserSecretsId` öğesi ekleyin ve dosyayı kaydedin.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Web uygulamasına Azure SignalR ekleme

1. Aşağıdaki komutu çalıştırarak `Microsoft.Azure.SignalR` NuGet paketine bir başvuru ekleyin:

        dotnet add package Microsoft.Azure.SignalR

2. Projenizin paketlerini geri yüklemek için aşağıdaki komutu çalıştırın:

        dotnet restore

3. Gizli Dizi Yöneticisi’ne *Azure:SignalR:ConnectionString* adlı bir gizli dizi ekleyin. 

    Bu gizli dizi, SignalR Hizmetinizin kaynağına erişmeye yarayan bağlantı dizesini içerir. *Azure: SignalR: ConnectionString* , bir bağlantı kurmak Için SignalR 'nin aradığı varsayılan yapılandırma anahtarıdır. Aşağıdaki komutta bulunan değeri, SignalR hizmet kaynağınızın bağlantı dizesiyle değiştirin.

    Bu komutu *. csproj* dosyasıyla aynı dizinde çalıştırmalısınız.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    Gizli dizi Yöneticisi, yalnızca yerel olarak barındırılırken Web uygulamasını test etmek için kullanılacaktır. Daha sonraki bir öğreticide, sohbet web uygulamasını Azure 'a dağıtırsınız. Web uygulaması Azure 'a dağıtıldıktan sonra, bağlantı dizesini gizli yönetici ile depolamak yerine bir uygulama ayarı kullanırsınız.

    Bu gizli dizi Yapılandırma API 'siyle erişilir. İki nokta (:) yapılandırma adında, desteklenen tüm platformlarda Yapılandırma API 'SI ile birlikte kullanılır. Bkz. [ortama göre yapılandırma](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. *Startup.cs* 'i açın ve yalnızca ASP.NET Core 2 için `services.AddSignalR().AddAzureSignalR()` metodunu çağırarak Azure SignalR hizmetini kullanmak üzere `ConfigureServices` yöntemini güncelleştirin:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```
    ASP.NET Core 3 + için, `ConfigureServices` yöntemi için gereken bir değişiklik yoktur.

    `AddAzureSignalR()`bir parametre geçirilmeyen Bu kod, SignalR hizmeti kaynak bağlantı dizesinin varsayılan yapılandırma anahtarını kullanır. Varsayılan yapılandırma anahtarı *Azure: SignalR: ConnectionString*.

5. Ayrıca *Startup.cs*içinde, aşağıdaki kodla `app.UseStaticFiles()` çağrısını değiştirerek `Configure` yöntemi güncelleştirin ve dosyayı yalnızca ASP.NET Core 2 için kaydedin.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    ASP.NET Core 3 + için yukarıdaki kodu ile değiştirin:

    ```csharp
    app.UseFileServer();
    app.UseRouting();
    app.UseAuthorization();

    app.UseEndpoints(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```

### <a name="add-a-hub-class"></a>Hub sınıfı ekleme

SignalR 'de, bir hub, istemciden çağrılabilen bir yöntemler kümesini kullanıma sunan bir çekirdek bileşendir. Bu bölümde, iki yöntemle bir hub sınıf tanımlayacaksınız: 

* `Broadcast`: Bu yöntem bir iletiyi tüm istemcilere yayınlar.
* `Echo`: Bu yöntem bir iletiyi çağırana geri gönderir.

Her iki yöntem de ASP.NET Core SignalR SDK 'nın sağladığı `Clients` arabirimini kullanır. Bu arabirim, istemcilere içerik gönderebilmeniz için tüm bağlı istemcilere erişmenizi sağlar.

1. Proje dizininizde *Hub* adlı yeni bir klasör ekleyin. Yeni klasöre *Chat.cs* adlı yeni bir hub kod dosyası ekleyin.

2. Hub sınıfınızı tanımlamak ve dosyayı kaydetmek için *chat.cs* 'e aşağıdaki kodu ekleyin. 

    *Chattest* dışında bir proje adı kullandıysanız bu sınıfın ad alanını güncelleştirin.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Web uygulaması için istemci arabirimini ekleme

Bu sohbet odası uygulamasının istemci kullanıcı arabirimi, *Wwwroot* dizinindeki *index. html* adlı bir dosyadaki HTML ve JavaScript 'ten oluşur.

[Samples deposunun](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot) *Wwwroot* klasöründen *index. html* dosyasını, *CSS* klasörünü ve *Scripts* klasörünü kopyalayın. Bunları projenizin *Wwwroot* klasörüne yapıştırın.

*İndex. html*' in ana kodu aşağıda verilmiştir: 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

*İndex. html* dosyasındaki kod, Azure SignalR kaynağına http bağlantısı kurmak için `HubConnectionBuilder.build()` çağırır.

Bağlantı başarılı olursa, o bağlantı `bindConnectionMessage` konumuna geçirilir ve istemciye gönderilen gelen içerik için olay işleyicileri ekler. 

`HubConnection.start()`, hub ile iletişim başlatır. Sonra, `onConnected()` düğme olay işleyicilerini ekler. Bu işleyiciler, bağlantıyı kullanarak bu istemcinin tüm bağlı istemcilere içerik güncelleştirmeleri göndermesine olanak tanır.

## <a name="add-a-development-runtime-profile"></a>Geliştirme çalışma zamanı profili ekleme

Bu bölümde, ASP.NET Core için bir geliştirme çalışma zamanı ortamı ekleyeceksiniz. Daha fazla bilgi için bkz. [ASP.NET Core birden çok ortamla çalışma](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Projenizde *Özellikler* adlı bir klasör oluşturun.

2. Aşağıdaki içerikle, *Launchsettings. JSON* adlı yeni bir dosyayı klasöre ekleyin ve dosyayı kaydedin.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. .NET Core CLI kullanarak uygulamayı derlemek için komut kabuğu 'nda aşağıdaki komutu çalıştırın:

        dotnet build

2. Oluşturma başarıyla tamamlandıktan sonra, Web uygulamasını yerel olarak çalıştırmak için aşağıdaki komutu çalıştırın:

        dotnet run

    Uygulama, geliştirme çalışma zamanı profilinizde yapılandırıldığı şekilde 5000 numaralı bağlantı noktasında yerel olarak barındırılır:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. İki tarayıcı penceresi açın. Her tarayıcıda `http://localhost:5000`' a gidin. Adınızı girmeniz istenir. Her iki istemci için bir istemci adı girin ve **Gönder** düğmesini kullanarak ileti içeriğini her iki istemci arasında iletme sınamasını yapın.

    ![Azure SignalR grup sohbeti örneği](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğreticiye devam ederseniz, bu hızlı başlangıçta oluşturulan kaynakları tutabilir ve yeniden kullanabilirsiniz.

Hızlı Başlangıç örnek uygulamasıyla işiniz bittiğinde, ücretlerden kaçınmak için bu hızlı başlangıçta oluşturulan Azure kaynaklarını silebilirsiniz. 

> [!IMPORTANT]
> Bir kaynak grubunun silinmesi geri alınamaz ve bu gruptaki tüm kaynakları içerir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. Bu örneği tutmak istediğiniz kaynakları içeren mevcut bir kaynak grubunda barındırmak için kaynaklar oluşturduysanız, kaynak grubunu silmek yerine her kaynağı dikey penceresinden ayrı ayrı silebilirsiniz.
> 
> 

[Azure portalda](https://portal.azure.com) oturum açın ve **Kaynak grupları**’nı seçin.

**Ada göre filtrele** metin kutusuna kaynak grubunuzun adını yazın. Bu hızlı başlangıçtaki yönergelerde *SignalRTestResources* adlı bir kaynak grubu kullanılmıştır. Sonuç listesindeki kaynak grubunuzda, **kaynak grubunu silmek**> üç nokta ( **...** ) simgesini seçin.

   
![Kaynak grubunu silmeye yönelik seçimler](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Kaynak grubunun silinmesini onaylamanız istenir. Onaylamak için kaynak grubunuzun adını girin ve **Sil**' i seçin.
   
Birkaç dakika sonra kaynak grubu ve bu gruptaki kaynakların tümü silinir.



## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta yeni bir Azure SignalR hizmeti kaynağı oluşturdunuz. Daha sonra, içerik güncelleştirmelerini gerçek zamanlı olarak birden çok bağlı istemciye göndermek için bir ASP.NET Core Web uygulamasıyla kullandınız. Azure SignalR hizmetini kullanma hakkında daha fazla bilgi edinmek için, kimlik doğrulamasını gösteren öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure SignalR Hizmeti kimlik doğrulaması](./signalr-concept-authenticate-oauth.md)


