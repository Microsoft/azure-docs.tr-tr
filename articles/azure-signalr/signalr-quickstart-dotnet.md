---
title: ASP.NET ile geliştirme-Azure SignalR hizmeti
description: ASP.NET Framework ile sohbet odası oluşturmak için Azure SignalR hizmetini kullanmaya yönelik hızlı başlangıç.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 0ac17de3f73424994fc39ef0044d17ef83b501b7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866152"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Hızlı başlangıç: ASP.NET ve SignalR hizmeti ile sohbet odası oluşturma

Azure SignalR hizmeti, ASP.NET SignalR ile %100 uyumlu **olmayan** [ASP.NET Core 2,1 için SignalR](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1)'yi temel alır. Azure SignalR hizmeti, en son ASP.NET Core teknolojilerine göre ASP.NET SignalR veri protokolünü yeniden uyguladık. ASP.NET SignalR için Azure SignalR hizmeti kullanılırken, bazı ASP.NET SignalR özellikleri artık desteklenmez. Örneğin, Azure SignalR istemci yeniden bağlandığında iletileri yeniden oynamaz. Ayrıca, süresiz çerçeve taşıması ve JSONP desteklenmez. ASP.NET SignalR uygulamasının SignalR hizmeti ile çalışmasını sağlamak için bazı kod değişiklikleri ve bağımlı kitaplıkların uygun sürümü gerekir.

ASP.NET SignalR ve ASP.NET Core SignalR arasındaki özellik karşılaştırmasının tamamen listesi için [sürüm farklılıkları](/aspnet/core/signalr/version-differences?preserve-view=true&view=aspnetcore-3.1) bölümüne bakın.

Bu hızlı başlangıçta, benzer bir [sohbet odası uygulaması](./signalr-quickstart-dotnet-core.md)için ASP.net ve Azure SignalR hizmetini kullanmaya nasıl başlacağınızı öğreneceksiniz.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET Framework 4.6.1](https://dotnet.microsoft.com/download/dotnet-framework/net461)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnet).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla [Azure portalında](https://portal.azure.com/) oturum açın.

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnet).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*Sunucusuz* mod, ASP.NET SignalR uygulamaları için desteklenmez. Her zaman Azure SignalR hizmeti örneği için *varsayılan* veya *Klasik* ' i kullanın.

Bu hızlı başlangıçta, [bir SignalR Hizmet betiği oluştur](scripts/signalr-cli-create-service.md)Ile kullanılan Azure kaynakları da oluşturabilirsiniz.

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnet).

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Hizmet dağıtılırken kod ile çalışmaya geçiş yapalım. [GitHub'dan örnek uygulamayı](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom) kopyalayın, SignalR Hizmetinin bağlantı dizesini ayarlayın ve uygulamayı yerel olarak çalıştırın.

1. Bir git terminal penceresi açın. Örnek projeyi kopyalamak istediğiniz klasöre gidin.

1. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnet).

## <a name="configure-and-run-chat-room-web-app"></a>Sohbet odası Web uygulamasını yapılandırma ve çalıştırma

1. Visual Studio 'Yu başlatın ve kopyalanan deponun *ASPNET-Samples/ChatRoom/* klasöründe çözümü açın.

1. Azure portal açıldığı tarayıcıda, oluşturduğunuz örneği bulun ve seçin.

1. SignalR Hizmeti örneğinin bağlantı dizelerini görüntülemek için **Anahtarlar**’ı seçin.

1. Birincil bağlantı dizesini seçerek kopyalayın.

1. Şimdi *web.config* dosyasında bağlantı dizesini ayarlayın.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. *Başlangıç. cs*' de çağırmak yerine, `MapSignalR()` `MapAzureSignalR({YourApplicationName})` uygulamanın kendi kendine bir SignalR barındırmak yerine hizmete bağlanmasını sağlamak için bağlantı dizesini çağırmanız ve geçirmeniz gerekir. `{YourApplicationName}`Uygulamanızın adıyla değiştirin. Bu ad, bu uygulamayı diğer uygulamalarınızdan ayırt etmek için benzersiz bir addır. Değer olarak ' i kullanabilirsiniz `this.GetType().FullName` .

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Ayrıca, bu API 'Leri kullanmadan önce hizmet SDK 'sına başvurmanız gerekir. Araçları açın **| NuGet Paket Yöneticisi | Paket Yöneticisi konsolu** ve Çalıştır komutu:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Bu değişiklikler dışında, diğer her şey aynı kalır, iş mantığını yazmak için zaten bildiğiniz hub arabirimini kullanmaya devam edebilirsiniz.

    > [!NOTE]
    > Uygulamada, bir uç nokta `/signalr/negotiate` Azure SignalR hizmeti SDK 'sı tarafından anlaşmaya sunuldu. İstemciler bağlanmayı ve bağlantı dizesinde tanımlanan hizmet uç noktasına yeniden yönlendirmeyi denediğinde, özel bir anlaşma yanıtı döndürür.

1. Projeyi hata ayıklama modunda çalıştırmak için <kbd>F5</kbd> tuşuna basın. Uygulamanın yerel olarak çalıştığını görebilirsiniz. Bir SignalR çalışma zamanını uygulamanın kendisi için barındırmak yerine artık Azure SignalR hizmetine bağlanır.

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnet).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

> [!IMPORTANT]
> Bir kaynak grubunu silme işlemi geri alınamaz ve kaynak grubunun ve içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. Bu örneği, tutmak istediğiniz kaynakları içeren mevcut bir kaynak grubunda barındırmak için kaynaklar oluşturduysanız, kaynak grubunu silmek yerine her kaynağı kendi ilgili dikey penceresinden tek tek silebilirsiniz.

[Azure portalında](https://portal.azure.com) oturum açın ve **Kaynak grupları**’na tıklayın.

**Ada göre filtrele...** metin kutusuna kaynak grubunuzun adını girin. Bu hızlı başlangıçtaki yönergelerde *SignalRTestResources* adlı bir kaynak grubu kullanılmıştır. Sonuç listesindeki kaynak grubunuzda **...** ve sonra **Kaynak grubunu sil**’e tıklayın.

![Sil](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Birkaç dakika sonra kaynak grubu ve içerdiği kaynakların tümü silinir.

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsnet).

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta yeni bir Azure SignalR hizmeti kaynağı oluşturdunuz ve bunu bir ASP.NET Web uygulamasıyla kullandınız. Daha sonra, ASP.NET Core ile Azure SignalR hizmetini kullanarak gerçek zamanlı uygulamalar geliştirmeyi öğrenin.

> [!div class="nextstepaction"]
> [ASP.NET Core ile Azure SignalR hizmeti](./signalr-quickstart-dotnet-core.md)
