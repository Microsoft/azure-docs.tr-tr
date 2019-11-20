---
title: 'Öğretici: Azure Işlevleri ile kimlik doğrulama-Azure SignalR'
description: Bu öğreticide, Azure Işlevleri bağlama için Azure SignalR hizmeti istemcilerinin kimliğini nasıl doğrulayacağınızı öğreneceksiniz.
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: dfa17720b34962611d240aa7c35ba8092bf99082
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158136"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Öğretici: Azure İşlevleri ile Azure SignalR Hizmeti kimlik doğrulaması

Azure İşlevleri, App Service Kimlik Doğrulaması ve SignalR Hizmeti ile kimlik doğrulaması ve özel mesajlaşma özelliklerine sahip bir sohbet odası oluşturma öğreticisi.

## <a name="introduction"></a>Giriş

### <a name="technologies-used"></a>Kullanılan teknolojiler

* [Azure İşlevleri](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu): Kullanıcıların kimliğini doğrulamak ve sohbet iletisi göndermek için kullanılan arka uç API'si
* [Azure SignalR Hizmeti](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu): Yeni iletileri bağlı sohbet istemcilerine yayınlar
* [Azure Depolama](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu): Sohbet istemcisi arabirimi için statik web sitesini barındırır

### <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi oluşturmak için aşağıdaki yazılımlar gereklidir.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (Sürüm 10.x)
* [.NET SDK](https://www.microsoft.com/net/download) (Sürüm 2.x, İşlevler uzantıları için gereklidir)
* [Azure İşlevleri Temel Araçları](https://github.com/Azure/azure-functions-core-tools) (Sürüm 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) ve aşağıdaki uzantılar
  * [Azure İşlevleri](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) - VS Code'da Azure İşlevleri ile çalışmak için
  * [Canlı Sunucu](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) - Test amacıyla yerel ortamda web sayfası sunmak için

## <a name="sign-into-the-azure-portal"></a>Azure portalda oturum açma

[Azure portala](https://portal.azure.com/) gidin ve kimlik bilgilerinizle oturum açın.

## <a name="create-an-azure-signalr-service-instance"></a>Azure SignalR Hizmeti örneği oluşturma

Azure İşlevleri uygulamasını yerel ortamda derleyecek ve test edeceksiniz. Uygulama, Azure'da önceden oluşturulması gereken bir SignalR Hizmeti örneğine erişecek.

1. Yeni bir Azure kaynağı oluşturmak için **Kaynak oluştur** ( **+** ) düğmesine tıklayın.

1. **SignalR Hizmeti** araması yapın ve sonuçlardan seçin. **Oluştur**’ tıklayın.

    ![Yeni SignalR Service](media/signalr-tutorial-authenticate-azure-functions/signalr-quickstart-new.png)

1. Aşağıdaki bilgileri girin.

    | Ad | Değer |
    |---|---|
    | Kaynak adı | SignalR Hizmeti örneği için benzersiz bir ad |
    | Kaynak grubu | Benzersiz bir ada sahip yeni bir kaynak grubu oluşturun |
    | Konum | Size yakın bir konum seçin |
    | Fiyatlandırma Katmanı | Ücretsiz |

1. **Oluştur**’ tıklayın.

1. Örnek dağıtıldıktan sonra portalda açın ve ayarlar sayfasını bulun. Hizmet modu ayarını *sunucusuz*olarak değiştirin.

    ![SignalR hizmeti modu](media/signalr-concept-azure-functions/signalr-service-mode.png)


## <a name="initialize-the-function-app"></a>İşlev uygulamasını başlatma

### <a name="create-a-new-azure-functions-project"></a>Yeni bir Azure İşlevleri projesi oluşturma

1. Yeni bir VS Code penceresinde menüden `File > Open Folder` seçeneğini kullanarak uygun konumda boş bir klasör oluşturun ve açın. Bu derleyeceğiniz uygulamanın ana proje klasörü olacaktır.

1. VS Code Azure İşlevleri uzantısını kullanarak ana proje klasöründe bir İşlev uygulaması başlatın.
   1. Menüden **Görünüm > Komut Paleti** yolunu izleyerek (kısayol `Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) VS Code'da Komut Paletini açın.
   1. **Azure İşlevleri: Yeni Proje Oluştur** komutunu arayın ve seçin.
   1. Ana proje klasörü görünmelidir. Bu klasörü seçin (veya "Gözat" düğmesini kullanarak bulun).
   1. Dil seçmeniz istendiğinde **JavaScript**'i seçin.

      ![İşlev uygulaması oluşturma](media/signalr-tutorial-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>İşlev uygulaması uzantılarını yükleme

Bu öğreticide Azure SignalR Hizmeti ile etkileşim kurmak için Azure İşlevleri bağlamaları kullanılır. Diğer bağlamalar gibi SignalR Hizmeti bağlamaları da kullanılabilmesi için Azure İşlevleri Temel Araçları CLI aracılığıyla yüklenmesi gereken bir uzantı olarak sunulur.

1. Menüden **> terminali görüntüle** ' ye (Ctrl-\`) seçerek vs Code bir Terminal açın.

1. Geçerli dizinin ana proje dizini olduğundan emin olun.

1. SignalR Hizmeti işlev uygulaması uzantısını yükleyin.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0
    ```

### <a name="configure-application-settings"></a>Uygulama ayarlarını yapılandırma

Azure İşlevleri çalışma zamanını yerel ortamda çalıştırma ve hata ayıklama sırasında uygulama ayarları **local.settings.json** dosyasından okunur. Bu uygulamayı güncelleştirerek oluşturduğunuz SignalR Hizmeti örneğinin bağlantı dizesini ekleyin.

1. VS Code'un Gezgin bölmesinde **local.settings.json** dosyasını seçerek açın.

1. Dosyanın içeriğini aşağıdakilerle değiştirin.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.14.1",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "http://127.0.0.1:5500",
            "CORSCredentials": true
        }
    }
    ```

   * Azure SignalR Hizmeti bağlantı dizesini `AzureSignalRConnectionString` adlı bir ayara girin. Azure portaldan Azure SignalR Hizmeti kaynağının **Anahtarlar** sayfasındaki değeri alın. Birincil veya ikincil bağlantı dizesini kullanabilirsiniz.
   * `WEBSITE_NODE_DEFAULT_VERSION` ayarı yerel ortamda kullanılmaz ancak uygulama Azure'a dağıtıldığında kullanılması gerekir.
   * `Host` bölümü yerel İşlevler ana bilgisayarı için bağlantı noktası ve CORS ayarlarını yapılandırır (Azure'da çalışırken bu ayarın bir etkisi yoktur).

       > [!NOTE]
       > Canlı sunucu genellikle `http://127.0.0.1:5500`içerik sunacak şekilde yapılandırılmıştır. Farklı bir URL kullandığını veya farklı bir HTTP sunucusu kullandığınızı fark ederseniz `CORS` ayarını doğru kaynağı yansıtacak şekilde değiştirin.

     ![SignalR Hizmeti anahtarını alma](media/signalr-tutorial-authenticate-azure-functions/signalr-get-key.png)

1. Dosyayı kaydedin.

    

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Kullanıcıların SignalR Hizmetinde kimlik doğrulamasını sağlayacak işlevi oluşturma

Sohbet uygulaması tarayıcıda ilk açıldığında Azure SignalR Hizmetine bağlanmak için gerekli bağlantı kimlik bilgilerine ihtiyaç duyar. Bu bağlantı bilgilerini döndürmek için işlev uygulamanızda *Negotiate* ADLı bir http ile tetiklenen işlev oluşturacaksınız.

> [!NOTE]
> SignalR istemcisi `/negotiate`biten bir uç nokta gerektirdiğinden bu işlev, *Negotiate* olarak adlandırılmalıdır.

1. VS Code komut paletini açın (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. **Azure İşlevleri: İşlev Oluştur** komutunu arayın ve seçin.

1. İstendiğinde aşağıdaki bilgileri girin.

    | Ad | Değer |
    |---|---|
    | İşlev uygulamasının klasörü | Ana proje klasörünü seçin |
    | Şablon | HTTP Tetikleyicisi |
    | Ad | 'Nin |
    | Yetkilendirme düzeyi | Anonim |

    Yeni işlevi içeren **Negotiate** adlı bir klasör oluşturulur.

1. İşlev için bağlamaları yapılandırmak üzere **Negotiate/function. JSON** açın. Dosyanın içeriğini aşağıdaki kodla değiştirin. Bu kod bir istemcinin `chat` adlı Azure SignalR Hizmeti hub'ına bağlanması için geçerli kimlik bilgileri oluşturan bir giriş bağlaması ekler.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    `userId` bağlamasındaki `signalRConnectionInfo` özelliği kimliği doğrulanmış SignalR Hizmeti bağlantısı oluşturmak için kullanılır. Yerel ortamda geliştirme için bu özelliği boş bırakın. İşlev uygulaması Azure'a dağıtıldığında bu özelliği kullanacaksınız.

1. İşlevin gövdesini görüntülemek için **anlaş/index. js** ' yi açın. Dosyanın içeriğini aşağıdaki kodla değiştirin.

    ```javascript
    module.exports = async function (context, req, connectionInfo) {
        context.res.body = connectionInfo;
    };
    ```

    Bu işlev giriş bağlamasındaki SignalR bağlantısı bilgilerini alır ve HTTP yanıtı gövdesinde istemciye döndürür. SignalR istemcisi bu bilgileri SignalR hizmeti örneğine bağlanmak için kullanır.

## <a name="create-a-function-to-send-chat-messages"></a>Sohbet iletisi göndermek için işlev oluşturma

Web uygulaması, sohbet iletisi göndermek için bir HTTP API'sine ihtiyaç duyar. SignalR Hizmetini kullanarak bağlı olan tüm istemcilere ileti gönderen *SendMessage* adlı bir HTTP ile tetiklenen işlev oluşturacaksınız.

1. VS Code komut paletini açın (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. **Azure İşlevleri: İşlev Oluştur** komutunu arayın ve seçin.

1. İstendiğinde aşağıdaki bilgileri girin.

    | Ad | Değer |
    |---|---|
    | İşlev uygulamasının klasörü | ana proje klasörünü seçin |
    | Şablon | HTTP Tetikleyicisi |
    | Ad | SendMessage |
    | Yetkilendirme düzeyi | Anonim |

    Yeni işlevi içeren **SendMessage** adlı bir klasör oluşturulur.

1. İşlev bağlamalarını yapılandırmak için **SendMessage/function.json** dosyasını açın. Dosyanın içeriğini aşağıdaki kodla değiştirin.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "$return",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Bu kod özgün işlevde iki değişiklik yapar:
    * Yolu `messages` olarak değiştirir ve HTTP tetikleyicisini **POST** HTTP metoduyla sınırlar.
    * `chat`adlı bir SignalR hizmeti merkezine bağlı tüm istemcilere işlev tarafından döndürülen bir ileti gönderen bir SignalR hizmeti çıkış bağlaması ekler.

1. Dosyayı kaydedin.

1. İşlevin gövdesini görüntülemek için **SendMessage/index.js** dosyasını açın. Dosyanın içeriğini aşağıdaki kodla değiştirin.

    ```javascript
    module.exports = async function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        return {
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        };
    };
    ```

    Bu işlev HTTP isteğinin gövdesini alır ve SignalR Hizmetine bağlı istemcilere göndererek her istemcide `newMessage` adlı bir işlevi çağırır.

    İşlev gönderenin kimliğini okuyabilir ve ileti gövdesine eklenebilecek bir *alıcı* değeriyle iletinin tek bir kullanıcıya gönderilmesini sağlayabilir. Bu işlevler öğreticinin ilerleyen bölümlerinde kullanılacaktır.

1. Dosyayı kaydedin.

## <a name="create-and-run-the-chat-client-web-user-interface"></a>Sohbet istemcisi web kullanıcı arabirimini oluşturma ve çalıştırma

Sohbet uygulamasının arabirimi, Vue JavaScript çerçevesiyle oluşturulan basit bir tek sayfalı uygulamadır (SPA). İşlev uygulamasından ayrı barındırılacaktır. Yerel ortamda web arabirimini çalıştırmak için Canlı Sunucu VS Code uzantısını kullanacaksınız.

1. VS Code'da ana proje klasörünün kök dizininde **content** adlı yeni bir klasör oluşturun.

1. **content** klasöründe **index.html** adlı bir dosya oluşturun.

1. Aşağıdaki içeriği kopyalayıp **[index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/2720a9a565e925db09ef972505e1c5a7a3765be4/docs/demo/chat-with-auth/index.html)** dosyasına yapıştırın.

1. Dosyayı kaydedin.

1. **F5** tuşuna basarak işlev uygulamasını yerel ortamda çalıştırın ve bir hata ayıklayıcısı ekleyin.

1. **index.html** dosyası açıkken VS Code komut paletini açarak (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) ve **Canlı Sunucu: Canlı Sunucu ile Aç**'ı seçerek Canlı Sunucuyu başlatın. Canlı Sunucu uygulamayı bir tarayıcıda açar.

1. Uygulama açılır. Sohbet kutusuna ileti yazıp Enter tuşuna basın. Yeni iletileri görmek için uygulamayı yenileyin. Kimlik doğrulaması yapılandırılmadığından tüm iletiler "anonim" olarak gönderilir.

## <a name="deploy-to-azure-and-enable-authentication"></a>Azure'a dağıtma ve kimlik doğrulamasını etkinleştirme

Buraya kadar işlev uygulamasını ve sohbet uygulamasını yerel ortamda çalıştırdınız. Şimdi bunları Azure'a dağıtıp kimlik doğrulamasını ve uygulamanın özel mesajlaşma özelliğini etkinleştireceksiniz.

### <a name="log-into-azure-with-vs-code"></a>VS Code ile Azure'da oturum açma

1. VS Code komut paletini açın (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. **Azure: Oturum aç** komutunu bulun ve seçin.

1. Yönergeleri izleyerek tarayıcınızda oturum açma işlemlerini tamamlayın.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Azure depolama hesabı, Azure 'da çalışan bir işlev uygulaması için gereklidir. Ayrıca, Azure Storage 'ın statik Web siteleri özelliğini kullanarak sohbet kullanıcı arabirimi için Web sayfasını barıncaksınız.

1. Azure portal yeni bir Azure kaynağı oluşturmak için **kaynak oluştur** ( **+** ) düğmesine tıklayın.

1. **Depolama** kategorisini seçin ve **depolama hesabı**' nı seçin.

1. Aşağıdaki bilgileri girin.

    | Ad | Değer |
    |---|---|
    | Abonelik | SignalR hizmet örneğini içeren aboneliği seçin |
    | Kaynak grubu | Aynı kaynak grubunu seçin |
    | Kaynak adı | Depolama hesabı için benzersiz bir ad |
    | Konum | Diğer kaynaklarınızla aynı konumu seçin |
    | Performans | Standart |
    | Hesap türü | StorageV2 (genel amaçlı V2) |
    | Çoğaltma | Yerel olarak yedekli depolama (LRS) |
    | Erişim katmanı | Sık Erişimli |

1. **Gözden geçir + oluştur**ve sonra **Oluştur**' a tıklayın.

### <a name="configure-static-websites"></a>Statik Web sitelerini yapılandırma

1. Depolama hesabı oluşturulduktan sonra, Azure portal açın.

1. **Statik Web sitesi**seçin.

1. Statik Web sitesi özelliğini etkinleştirmek için **etkin** ' i seçin.

1. **Dizin belgesi adı**' nda *index. html*girin.

1. **Save (Kaydet)** düğmesine tıklayın.

1. **Birincil uç nokta** görünür. Bu değeri aklınızda edin. İşlev uygulamasını yapılandırmak için gerekli olacaktır.

### <a name="configure-function-app-for-authentication"></a>İşlev uygulamasını kimlik doğrulaması için yapılandırma

Şu ana kadar sohbet uygulaması anonim çalışıyordu. Kullanıcıların kimliğini doğrulamak için Azure'da [App Service Kimlik Doğrulamasını](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) kullanacaksınız. Kimliği doğrulanmış olan kullanıcının kimliği veya kullanıcı adı *SignalRConnectionInfo* bağlamasına iletilerek kullanıcı olarak kimliği doğrulanan bağlantı bilgileri oluşturulabilir.

İleti gönderirken uygulama bağlı tüm istemcilere veya yalnızca belirli bir kullanıcı için kimliği doğrulanmış olan istemcilere gönderme seçenekleri arasında seçim yapabilir.

1. VS Code ' de **anlaş/function. JSON**' ı açın.

1. [SignalRConnectionInfo](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings) bağlamasının *userId* özelliğine bir *bağlama ifadesi* ekleyin: `{headers.x-ms-client-principal-name}`. Bu ifade değeri kimliği doğrulanmış kullanıcının kullanıcı adı olarak ayarlar. Öznitelik şimdi aşağıdaki gibi görünmelidir.

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Dosyayı kaydedin.


### <a name="deploy-function-app-to-azure"></a>İşlev uygulamasını Azure 'a dağıtma

1. VS Code komut paletini açın (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) ve **Azure İşlevleri: İşlev Uygulamasına Dağıt** komutunu seçin.

1. İstendiğinde aşağıdaki bilgileri girin.

    | Ad | Değer |
    |---|---|
    | Dağıtılacak klasör | Ana proje klasörünü seçin |
    | Abonelik | Aboneliğinizi seçme |
    | İşlev uygulaması | **Yeni İşlev Uygulaması Oluştur**'u seçin |
    | İşlev uygulamasının adı | Benzersiz bir ad girin |
    | Kaynak grubu | SignalR Hizmeti örneğinin bulunduğu kaynak grubunu seçin |
    | Depolama hesabı | Daha önce oluşturduğunuz depolama hesabını seçin |

    Azure'da yeni bir işlev uygulaması oluşturulur ve dağıtım başlar. Dağıtımın tamamlanmasını bekleyin.

### <a name="upload-function-app-local-settings"></a>İşlev uygulaması yerel ayarlarını karşıya yükleme

1. VS Code komut paletini açın (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. **Azure İşlevleri: Yerel ayarları karşıya yükle** komutunu arayın ve seçin.

1. İstendiğinde aşağıdaki bilgileri girin.

    | Ad | Değer |
    |---|---|
    | Yerel ayarlar dosyası | local.settings.json |
    | Abonelik | Aboneliğinizi seçme |
    | İşlev uygulaması | Önceden dağıtılmış işlev uygulamasını seçin |

Yerel ayarlar, Azure'daki işlev uygulamasına yüklenir. Geçerli ayarların üzerine yazmak isteyip istemediğiniz sorulduğunda **Tümüne evet**'i seçin.


### <a name="enable-app-service-authentication"></a>App Service Kimlik Doğrulamayı Etkinleştirme

App Service Kimlik Doğrulaması; Azure Active Directory, Facebook, Twitter, Microsoft hesabı ve Google ile kimlik doğrulamayı destekler.

1. VS Code komut paletini açın (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. **Azure İşlevleri: Portalda aç** komutunu arayın ve seçin.

1. İşlev uygulamasını Azure portalda açmak için aboneliği ve işlev uygulaması adını seçin.

1. Portalda açılan işlev uygulamasında **platform özellikleri** sekmesini bulun, **kimlik doğrulama/yetkilendirme**' yi seçin.

1. App Service Kimlik Doğrulama ayarını **Açık** duruma getirin.

1. **İsteğin kimliği doğrulanmamış olduğunda gerçekleştirilecek eylem** menüsünde "{Seçtiğiniz kimlik doğrulaması sağlayıcısı} ile oturum aç"ı seçin.

1. **İzin Verilen Dış Yönlendirme URL'leri** bölümünde önceden not ettiğiniz depolama hesabı birincil web uç noktası URL'sini girin.

1. Yapılandırmayı tamamlamak için seçtiğiniz oturum açma hizmeti sağlayıcısının belgelerini inceleyin.

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)
    - [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook)
    - [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)
    - [Microsoft hesabı](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)
    - [Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google)

### <a name="update-the-web-app"></a>Web uygulamasını güncelleştirme

1. Azure portalda işlev uygulamasının genel bakış sayfasına gidin.

1. İşlev uygulamasının URL'sini kopyalayın.

    ![URL'yi alın](media/signalr-tutorial-authenticate-azure-functions/signalr-get-url.png)

1. VS Code'da **index.html** dosyasını açın ve `apiBaseUrl` değerini işlev uygulamasının URL'siyle değiştirin.

1. Uygulama Azure Active Directory, Facebook, Twitter, Microsoft hesabı veya Google ile kimlik doğrulaması gerçekleştirilecek şekilde yapılandırılabilir. `authProvider` değerini ayarlayarak kullanmak istediğiniz kimlik doğrulaması sağlayıcısını seçin.

1. Dosyayı kaydedin.

### <a name="deploy-the-web-application-to-blob-storage"></a>Web uygulamasını blob depolamaya dağıtma

Web uygulamasını Azure Blob Depolama'nın statik web siteleri özelliğini kullanarak barındıracağız.

1. VS Code komut paletini açın (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. **Azure depolama: statik Web sitesine dağıt** komutuna arayın ve seçin.

1. Aşağıdaki değerleri girin:

    | Ad | Değer |
    |---|---|
    | Abonelik | Aboneliğinizi seçme |
    | Depolama hesabı | Daha önce oluşturduğunuz depolama hesabını seçin |
    | Dağıtılacak klasör | **Araştır** ' ı seçin ve *içerik* klasörünü seçin |

*İçerik* klasöründeki dosyalar artık statik Web sitesine dağıtılmalıdır.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>İşlev uygulamasında çıkış noktaları arası kaynak paylaşımını (CORS) etkinleştirme

**local.settings.json** dosyasında CORS seçeneği mevcut olsa da Azure'daki işlev uygulamasına yüklenmez. Bunu ayrıca ayarlamanız gerekir.

1. İşlev uygulamasını Azure portal açın.

1. **Platform özellikleri** sekmesinde **CORS**' yi seçin.

    ![CORS'yi bulun](media/signalr-tutorial-authenticate-azure-functions/signalr-find-cors.png)

1. *Izin verilen* kaynaklar bölümünde, değer olarak statik Web sitesi *birincil uç noktasına* sahip bir giriş ekleyin (sondaki */* kaldırın).

1. SignalR JavaScript SDK 'Sı, işlev uygulamanızı bir tarayıcıdan çağırmak için, CORS 'de kimlik bilgileri desteğinin etkinleştirilmesi gerekir. "Erişim-denetim-Izin-kimlik bilgilerini etkinleştir" onay kutusunu seçin.

    ![Erişim-denetim-Izin-kimlik bilgilerini etkinleştir](media/signalr-tutorial-authenticate-azure-functions/signalr-cors-credentials.png)

1. CORS ayarlarını kalıcı hale getirmek için **Kaydet**'e tıklayın.

### <a name="try-the-application"></a>Uygulamayı deneyin

1. Bir tarayıcıdan depolama hesabınızın birincil web uç noktasına gidin.

1. Seçtiğiniz kimlik doğrulaması sağlayıcısıyla kimlik doğrulaması gerçekleştirmek için **Oturum aç**'ı seçin.

1. Ana sohbet kutusunu kullanarak genel ileti gönderin.

1. Sohbet geçmişindeki kullanıcı adlarından birine tıklayarak özel ileti gönderin. İletiler yalnızca seçilen alıcıya gönderilir.

Tebrikler! Gerçek zamanlı bir sunucusuz sohbet uygulaması dağıttınız!

![Tanıtım](media/signalr-tutorial-authenticate-azure-functions/signalr-serverless-chat.gif)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici ile oluşturulan kaynakları temizlemek için Azure portalı kullanarak kaynak grubunu silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure İşlevleri’ni Azure SignalR Hizmeti ile birlikte kullanmayı öğrendiniz. Azure İşlevleri için SignalR Hizmeti bağlamalarıyla gerçek zamanlı sunucusuz uygulama derleme hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure İşlevleri ile Gerçek Zamanlı Uygulama Derleme](signalr-concept-azure-functions.md)
