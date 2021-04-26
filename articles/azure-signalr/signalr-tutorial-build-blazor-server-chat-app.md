---
title: 'Öğretici: Blazor Server sohbet uygulaması oluşturma-Azure SignalR'
description: Bu öğreticide, Azure SignalR hizmeti ile bir Blazor Server uygulaması oluşturma ve değiştirme hakkında bilgi edineceksiniz.
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: a1423e8aaf4b50db94cda0b935a7b1658249893e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726354"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>Öğretici: Blazor Server sohbet uygulaması oluşturma

Bu öğreticide bir Blazor sunucu uygulamasını nasıl derleyip değiştireceğiniz gösterilmektedir. Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Blazor Server uygulamasıyla basit bir sohbet odası oluşturun.
> * Razor bileşenlerini değiştirme.
> * Bileşenlerinde olay işleme ve veri bağlamayı kullanın.
> * Visual Studio 'da Azure App Service hızlı dağıtım.
> * Yerel SignalR hizmetini Azure SignalR hizmetine geçirin.

## <a name="prerequisites"></a>Önkoşullar
* [.NET Core 3,0 SDK 'sını](https://dotnet.microsoft.com/download/dotnet-core/3.0) (sürüm >= 3.0.100) yükler
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 'yi (sürüm >= 16,3) yükler
> Visual Studio 2019 Preview sürümü Ayrıca, daha yeni .Net Core sürümünü hedefleyen en son Blazor Server uygulama şablonuyla serbest bırakılıyor.

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Blazor sunucu uygulamasında yerel bir sohbet odası oluşturun

Visual Studio 2019 sürüm 16.2.0 'den Azure SignalR hizmeti, derleme Web uygulaması yayımlama işlemidir ve Web uygulaması ile SignalR hizmeti arasındaki bağımlılıkları yönetmek çok daha kolay olacaktır. Yerel bir SignalR üzerinde, dev yerel ortamında ve kod değişikliği yapmadan aynı anda Azure App Service için Azure SignalR hizmeti üzerinde çalışmaya karşılaşabilirsiniz.

1. Sohbet Blazor uygulaması oluşturma
   
   Visual Studio 'da yeni proje oluştur-> Blazor App-> (uygulamayı adlandırın ve bir klasör seçin)-> Blazor Server uygulamasını seçin. Visual Studio 'Nun hedef çerçevesini doğru bir şekilde tanımasını sağlamak için .NET Core SDK 3.0 + ' ın zaten yüklü olduğundan emin olun.

   [![Yeni proje oluştur ' da, Blazor uygulama şablonları seçilidir. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   Veya cmd Çalıştır
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. `BlazorChatSampleHub.cs`Sohbet için uygulanacak bir dosya ekleyin `Hub` .
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. Hub için bir uç nokta ekleyin `Startup.Configure()` .
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. `Microsoft.AspNetCore.SignalR.Client`SignalR istemcisini kullanmak için paketi yükler.

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. `ChatRoom.razor` `Pages` SignalR istemcisini uygulamak için klasör altında oluşturun. Aşağıdaki adımları izleyin veya [ChatRoom. Razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor)'yi kopyalayın.

   1. Sayfa bağlantısı ve başvuru ekleyin.
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. İleti göndermek ve almak için yeni SignalR istemcisine kod ekleyin.
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. `@code`Kullanıcı arabiriminin SignalR istemcisiyle etkileşime geçmesini sağlamak için işleme bölümü ekleyin.
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. `NavMenu.razor`Rest gibi sohbet odası için bir giriş menüsü eklemek üzere güncelleştirin `NavMenuCssClass` .

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. `site.css`Sohbet alanı balon görünümlerini iyileştirmek için güncelleştirin. Sonundaki kodu sonuna ekleyin.

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. Uygulamayı çalıştırmak için <kbd>F5</kbd> ' e tıklayın. Aşağıdaki gibi sohbet edebilirsiniz.

   [![Bob ve Ayla arasında animasyonlu sohbet gösterilmektedir. Ayla Merhaba, Bob Merhaba diyor. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Azure’da Yayımlama

   Şimdiye kadar, Blazor uygulaması yerel bir SignalR üzerinde çalışıyor ve Azure App Service 'e dağıtırken, bir Blazor Server uygulamasını çok sayıda eşzamanlı SignalR bağlantısına ölçeklendirmeye olanak tanıyan [Azure SignalR hizmetini](/aspnet/core/signalr/scale#azure-signalr-service) kullanmanız önerilir. Ayrıca, SignalR hizmetinin küresel erişim ve yüksek performanslı veri merkezleri Coğrafya nedeniyle gecikme süresini azaltmaya önemli ölçüde yardımcı olur.

> [!IMPORTANT]
> Blazor Server uygulamasında, Kullanıcı arabirimi durumları sunucu tarafında tutulur ve bu durumda sunucu yapışkan 'nin gerekli olduğu anlamına gelir. Tek bir App Server varsa, Server Sticky, tasarım tarafından yapılır. Ancak, birden çok uygulama sunucusu varsa, istemci anlaşması ve bağlantısının farklı sunuculara gidebilme ve Blazor uygulamasındaki UI hatalarına neden olabileceği bir şansınız vardır. Bu nedenle, aşağıdaki gibi sunucu Sticky Server 'ı etkinleştirmeniz gerekir `appsettings.json` :
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. Projeye sağ tıklayıp öğesine gidin `Publish` .

   * Hedef: Azure
   * Belirli hedef: tüm **Azure App Service** türleri desteklenir.
   * App Service: yeni bir tane oluşturun veya var olan bir App Service 'i seçin.

   [![Animasyon, Azure seçimini hedef olarak gösterir ve ardından Azure uygulaması özel hedef olarak oluşur. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Azure SignalR hizmeti bağımlılığı ekleme

   Yayımlama profili oluşturulduktan sonra, **hizmet bağımlılıkları** altında önerilen bir ileti görebilirsiniz. Yeni oluşturmak veya panelde mevcut Azure SignalR hizmetini seçmek için **Yapılandır** ' a tıklayın.

   [![Yayımla ' da, Yapılandır bağlantısı vurgulanır. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   Azure 'da, uygulamanızı otomatik olarak Azure SignalR hizmetine geçiş yapmak için hizmet bağımlılığı aşağıdaki işlemleri yapar.

   * [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration)Azure SignalR hizmetini kullanmak için güncelleştirin.
   * Azure SignalR hizmeti NuGet paketi başvurusunu ekleyin.
   * Bağımlılık ayarlarını kaydetmek için profil özelliklerini güncelleştirin.
   * Gizli dizileri Yapılandır deposu, seçtiğiniz seçeneğe bağlıdır.
   * `appsettings`Uygulamanızın hedefini seçtiğiniz Azure SignalR hizmetini oluşturmak için yapılandırma ekleyin.

   [![Değişikliklerin özeti hakkında onay kutuları, tüm bağımlılıkları seçmek için kullanılır. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. Uygulamayı yayımlama

   Şimdi yayımlamaya hazırsınız. Yayımlama tamamlandıktan sonra sayfayı otomatik olarak tarayıcıya uygular. 
   > [!NOTE]
   > Azure App Service dağıtım başlangıç gecikmesi nedeniyle sayfa ilk kez ziyaret etmeyebilir ve biraz gecikme sağlamak için sayfayı yenilemeyi deneyin.
   > Bunun yanı sıra, trafiği Azure SignalR hizmetine zaten yönlendirdiğini doğrulamak için tarayıcı hata ayıklayıcısı modunu <kbd>F12</kbd> ile birlikte kullanabilirsiniz.

   [![Blazor SignalR sohbet örneğinin adınız için bir metin kutusu ve bir sohbet başlatmak Için sohbet! düğmesi vardır. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>Daha fazla konu: yerel geliştirmede Azure SignalR hizmetini etkinleştirin

1. Azure SignalR SDK 'ya başvuru ekleme

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. İçindeki Azure SignalR hizmetine bir çağrı ekleyin `Startup.ConfigureServices()` .

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. Azure SignalR hizmetini `ConnectionString` `appsetting.json` [gizli yönetici](/aspnet/core/security/app-secrets?tabs=visual-studio#secret-manager) aracında veya içinde yapılandırma

> [!NOTE]
> 2. adım, [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration) SignalR SDK için kullanılarak değiştirilebilir.
> 
> 1. İçinde Azure SignalR hizmetini açmak için yapılandırma Ekle `appsetting.json`
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. Azure SignalR SDK 'Yı kullanmak için barındırma başlangıç derlemesi atayın. `launchSettings.json`Aşağıdaki gibi bir yapılandırmayı düzenleyin ve ekleyin `environmentVariables` .
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici ile oluşturulan kaynakları temizlemek için Azure portalı kullanarak kaynak grubunu silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * Blazor Server uygulamasıyla basit bir sohbet odası oluşturun.
> * Razor bileşenlerini değiştirme.
> * Bileşenlerinde olay işleme ve veri bağlamayı kullanın.
> * Visual Studio 'da Azure App Service hızlı dağıtım.
> * Yerel SignalR hizmetini Azure SignalR hizmetine geçirin.

Yüksek kullanılabilirlik hakkında daha fazla bilgi edinin.
> [!div class="nextstepaction"]
> [Dayanıklılık ve olağanüstü durum kurtarma](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core Blazor](/aspnet/core/blazor)
