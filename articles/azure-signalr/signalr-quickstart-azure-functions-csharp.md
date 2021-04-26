---
title: 'Azure SignalR hizmeti sunucusuz hızlı başlangıç-C #'
description: C# kullanarak bir sohbet odası oluşturmak için Azure SignalR hizmetini ve Azure Işlevlerini kullanmaya yönelik hızlı başlangıç.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/25/2020
ms.author: zhshang
ms.openlocfilehash: 1f28058a766144ada3326b3ee4ce09ca503d1896
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94873888"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-c"></a>Hızlı başlangıç: C kullanarak Azure Işlevleri ve SignalR hizmeti ile sohbet odası oluşturma\#

Azure SignalR hizmeti uygulamanıza kolayca gerçek zamanlı işlevsellik eklemenizi sağlar. Azure İşlevleri, herhangi bir altyapı yönetimine gerek kalmadan kodunuzu çalıştırmanıza olanak tanıyan sunucusuz bir platformdur. Bu hızlı başlangıçta, SignalR Hizmeti ve İşlevlerini sunucusuz ve gerçek zamanlı bir sohbet uygulaması oluşturmak için kullanmayı öğrenin.

## <a name="prerequisites"></a>Önkoşullar

Zaten Visual Studio 2019 yüklü değilse, **ücretsiz** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads)' ı indirip kullanabilirsiniz. Visual Studio kurulumu sırasında **Azure dağıtımını** etkinleştirdiğinizden emin olun.

Bu öğreticiyi bir komut satırında (macOS, Windows veya Linux) [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing), [.NET Core SDK](https://dotnet.microsoft.com/download)ve en sevdiğiniz kod düzenleyicinizi kullanarak da çalıştırabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/dotnet) .

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qscsharp).

## <a name="log-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla Azure portalında <https://portal.azure.com/> sayfasında oturum açın.

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qscsharp).

## <a name="configure-and-run-the-azure-function-app"></a>Azure İşlev Uygulamasını yapılandırıp çalıştırma

1. Visual Studio 'Yu (veya başka bir kod düzenleyicisini) başlatın ve kopyalanan deponun *src/chat/CSharp* klasöründe çözümü açın.

1. Azure portalın açık olduğu tarayıcıda portalın üst kısmındaki arama kutusundan adını arayarak önceden dağıttığınız SignalR Hizmeti örneğinin başarılı bir şekilde oluşturulduğundan emin olun. Açmak için örneği seçin.

    ![SignalR Hizmeti örneğini arayın](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. SignalR Hizmeti örneğinin bağlantı dizelerini görüntülemek için **Anahtarlar**’ı seçin.

1. Birincil bağlantı dizesini seçerek kopyalayın.

1. Visual Studio- **Çözüm Gezgini** geri dönüp *local.settings.sample.js* üzerinde *local.settings.js* olarak yeniden adlandırın.

1. *local.settings.json* dosyasının içinde bağlantı dizesini **AzureSignalRConnectionString** ayarının değerine yapıştırın. Dosyayı kaydedin.

1. *Functions.cs*’yi açın. Bu işlev uygulamasında iki adet HTTP ile tetiklenen işlev vardır:

    - **Getsignalrınfo** - `SignalRConnectionInfo` geçerli bağlantı bilgilerini oluşturmak ve döndürmek için giriş bağlamayı kullanır.
    - **SendMessage** - İstek gövdesinde bir sohbet iletisi alır ve iletiyi bağlı olan tüm istemci uygulamalara yaymak için *SignalR* çıkış bağlamasını kullanır.

1. Azure Işlev uygulamasını yerel olarak başlatmak için aşağıdaki seçeneklerden birini kullanın.

    - **Visual Studio**: *Hata Ayıkla* menüsünde, uygulamayı çalıştırmak için *hata ayıklamayı Başlat* ' ı seçin.

        ![Uygulamada hata ayıklama](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-debug-vs.png)

    - **Komut satırı**: işlev konağını başlatmak için aşağıdaki komutu yürütün.

        ```bash
        func start
        ```
Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qscsharp)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Visual Studio 'da gerçek zamanlı sunucusuz bir uygulama oluşturup çalıştırdınız. Bir sonraki adımda Visual Studio ile nasıl Azure İşlevlerini geliştirip dağıtacağınızı öğrenin.

> [!div class="nextstepaction"]
> [Visual Studio ile Azure İşlevleri geliştirme](../azure-functions/functions-develop-vs.md)

