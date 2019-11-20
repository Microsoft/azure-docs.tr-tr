---
title: Azure SignalR hizmeti sunucusuz hızlı başlangıç-Python
description: Azure SignalR hizmeti ve Azure Işlevleri 'ni kullanarak bir sohbet odası oluşturmaya yönelik hızlı başlangıç.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: antchu
ms.openlocfilehash: 728111548176a0a3212b1677eeb192ccdc47fe88
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709506"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Hızlı başlangıç: Python kullanarak Azure Işlevleri ve SignalR hizmeti ile sohbet odası oluşturma

Azure SignalR hizmeti, uygulamanıza kolayca gerçek zamanlı işlevselliği eklemenize olanak tanır. Azure Işlevleri, herhangi bir altyapıyı yönetmeden kodunuzu çalıştırmanıza olanak tanıyan sunucusuz bir platformdur. Bu hızlı başlangıçta, bir sunucusuz ve gerçek zamanlı sohbet uygulaması oluşturmak için SignalR hizmetini ve Işlevlerini nasıl kullanacağınızı öğrenin.

## <a name="prerequisites"></a>Prerequisites

Bu hızlı başlangıç, macOS, Windows veya Linux üzerinde çalıştırılabilir.

[Visual Studio Code](https://code.visualstudio.com/) yüklü gibi bir kod düzenleyicisine sahip olduğunuzdan emin olun.

Python Azure Işlev uygulamalarını yerel olarak çalıştırmak için [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing) (sürüm 2.7.1505 veya üzeri) yüklemesini yapın.

Azure Işlevleri [Python 3,6](https://www.python.org/downloads/)gerektirir.

Uzantıları yüklemek için Azure Functions Core Tools Şu anda [.NET Core SDK](https://www.microsoft.com/net/download) yüklü olması gerekir. Ancak, Python Azure Işlev uygulamaları oluşturmak için .NET bilgisi gerekmez.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Azure 'da oturum açma

Azure hesabınızla Azure portal <https://portal.azure.com/> ' da oturum açın.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Azure Işlevi uygulamasını yapılandırma ve çalıştırma

1. Azure portal açıldığı tarayıcıda, daha önce dağıttığınız SignalR hizmeti örneğinin, portalın en üstündeki arama kutusunda adı arayarak başarıyla oluşturulduğunu doğrulayın. Açmak için örneği seçin.

    ![SignalR hizmeti örneğini arayın](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. SignalR hizmeti örneği için bağlantı dizelerini görüntülemek için **anahtarlar** ' ı seçin.

1. Birincil bağlantı dizesini seçin ve kopyalayın.

    ![SignalR hizmeti oluşturma](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Kod Düzenleyicinizde, kopyalanmış depodaki *src/chat/Python* klasörünü açın.

1. Python işlevlerini yerel olarak geliştirmek ve test etmek için bir Python 3,6 ortamında çalışmanız gerekir. @No__t-0 adlı bir sanal ortam oluşturmak ve etkinleştirmek için aşağıdaki komutları çalıştırın.

    **Linux veya macOS:**

    ```bash
    python3.6 -m venv .venv
    source .venv/bin/activate
    ```

    **Pencerelerin**

    ```powershell
    py -3.6 -m venv .venv
    .venv\scripts\activate
    ```

1. *Local. Settings. Sample. JSON* öğesini *Local. Settings. JSON*olarak yeniden adlandırın.

1. **Local. Settings. JSON**dosyasında bağlantı dizesini **AzureSignalRConnectionString** ayarının değerine yapıştırın. Dosyayı kaydedin.

1. Python işlevleri klasörler halinde düzenlenir. Her bir klasörde iki dosya vardır: *function. JSON* işlevde kullanılan bağlamaları tanımlar ve *\_ @ no__t-3init @ no__t-4\_.py* işlevin gövdesidir. Bu işlev uygulamasında iki HTTP ile tetiklenen işlev vardır:

    - **anlaş** -geçerli bağlantı bilgilerini oluşturmak ve döndürmek Için *Signalrconnectionınfo* giriş bağlamasını kullanır.
    - **iletiler** -istek gövdesinde bir sohbet iletisi alır ve iletiyi tüm bağlı istemci uygulamalarına yayımlamak Için *SignalR* çıkış bağlamasını kullanır.

1. Terminalde, *src/chat/Python* klasöründe olduğunuzdan emin olun. Uygulamayı çalıştırmak için gereken uzantıları yüklemek için Azure Functions Core Tools kullanın.

    ```bash
    func extensions install
    ```

1. İşlev uygulamasını çalıştırın.

    ```bash
    func start
    ```

    ![İşlev uygulamasını Çalıştır](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, VS Code ' de gerçek zamanlı sunucusuz bir uygulama oluşturup çalıştırdınız. Daha sonra VS Code Azure Işlevlerinin nasıl dağıtılacağı hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure Işlevlerini VS Code ile dağıtma](/azure/javascript/tutorial-vscode-serverless-node-01)
