---
title: 'Hızlı başlangıç: Node.js Web uygulaması oluşturma'
description: İlk Node.js Merhaba Dünya Azure App Service dakikalar içinde dağıtın.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-nodejs-uiex
ms.openlocfilehash: 3b1ff02d6a9b3e8c7b7abaf1b185a9becd2be12b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105842"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Azure App Service'te Node.js web uygulaması oluşturma

::: zone pivot="platform-windows"  

Visual Studio Code kullanarak yerel olarak bir Node.js/Express uygulaması oluşturarak ve sonra uygulamayı buluta dağıtarak Azure App Service kullanmaya başlayın. Ücretsiz bir App Service katmanı kullandığınız için, bu hızlı başlangıcı tamamlamaya yönelik bir ücret ödeirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Git'i yükleyin</a>
- [Node.js ve npm](https://nodejs.org). `node --version`Node.js yüklendiğini doğrulamak için komutunu çalıştırın.
- [Visual Studio Code](https://code.visualstudio.com/).
- Visual Studio Code için [Azure App Service uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) .

## <a name="clone-and-run-a-local-nodejs-application"></a>Yerel bir Node.js uygulamasını kopyalama ve çalıştırma

1. Yerel bilgisayarınızda bir Terminal açın ve örnek depoyu kopyalayın:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Yeni uygulama klasörüne gidin:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Uygulamayı yerel olarak test etmek için başlatın:

    ```bash
    npm start
    ```
    
1. Tarayıcınızı açın ve adresine gidin `http://localhost:1337` . Tarayıcının "Merhaba Dünya!" görüntülemesi gerekir.

1.  + Sunucuyu durdurmak için terminalde CTRL **C** tuşlarına basın.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Uygulamayı Azure’da dağıtma

Bu bölümde, VS Code ve Azure App Service uzantısı aracılığıyla Node.js uygulamanızı Azure 'a dağıtırsınız.

1. Terminalde *NodeJS-docs-Hello-World* klasöründe olduğunuzdan emin olun ve Visual Studio Code aşağıdaki komutla başlatın:

    ```bash
    code .
    ```

1. **Azure uygulama hizmeti** Gezginini göstermek için vs Code etkinlik çubuğunda Azure logosu ' nu seçin. **Azure 'Da oturum aç...** ' ı seçin ve yönergeleri izleyin. (Hata halinde çalıştırırsanız bkz. [Azure oturum açma sorunlarını giderme](#troubleshooting-azure-sign-in) .) Oturum açıldıktan sonra gezgin, Azure aboneliğinizin adını göstermelidir.

    ![Azure'da oturum açma](media/quickstart-nodejs/sign-in.png)

1. VS Code **Azure App SERVICE** Gezgini ' nde, uygulamanızı Azure 'a dağıtmak için mavi yukarı ok simgesini seçin. (' Web uygulamasınaDağıt ' yazıp  +  + **Azure App Service: Web uygulamasına dağıt**) seçeneğini belirleyerek komut paletinden aynı komutu da çağırabilirsiniz (CTRL SHIFT **P**).

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="Azure App Service 'in seçili mavi ok simgesini gösteren VS Code ekran görüntüsü.":::
        
1. *NodeJS-docs-Hello-World* klasörünü seçin.

1. Dağıtmak istediğiniz işletim sistemini temel alan bir oluşturma seçeneği belirleyin:

    - Linux: **Yeni Web uygulaması oluştur** seçeneğini belirleyin
    - Windows: **Yeni Web uygulaması oluştur öğesini seçin... Gelişmiş**

1. Web uygulamanız için genel olarak benzersiz bir ad yazın ve **ENTER** tuşuna basın. Ad tüm Azure genelinde benzersiz olmalıdır ve yalnızca alfasayısal karakterler (' A-Z ', ' a-z ' ve ' 0-9 ') ve tireler ('-') kullanılmalıdır.

1. Linux 'u hedefliyorsanız, sorulduğunda Node.js bir sürüm seçin. **LTS** sürümü önerilir.

1. Windows 'u hedefliyorsanız, ek istemleri izleyin:
    1. **Yeni kaynak grubu oluştur**' u seçin, ardından kaynak grubu için gibi bir ad girin `AppServiceQS-rg` .
    1. İşletim sistemi için **Windows** ' u seçin.
    1. **Yeni App Service planı oluştur**' u seçin, sonra plan için bir ad girin (gibi `AppServiceQS-plan` ) ve ardından fiyatlandırma katmanı için **F1 ücretsiz** ' i seçin.
    1. Application Insights sorulduğunda **Şu anda atla** ' yı seçin.
    1. Size yakın bir bölge veya erişmek istediğiniz kaynakları seçin.

1. Tüm istemlere yanıt verdikten sonra, VS Code bildirim açılan penceresinde uygulamanız için oluşturulmakta olan Azure kaynaklarını gösterir.

    Linux 'a dağıtım yaparken, yapılandırmanızı hedef Linux sunucusunda çalışacak şekilde güncellemek isteyip istemediğiniz sorulduğunda **Evet** ' i seçin `npm install` .

    ![Hedef Linux sunucusunda yapılandırmayı güncelleştirmek için sor](media/quickstart-nodejs/server-build.png)

1. **Her zaman "NodeJS-docs-Hello-World" çalışma alanını (uygulama adı) "olarak dağıtmak** isteyip Istemediğiniz sorulduğunda **Evet** ' i seçin. **Evet** seçildiğinde, sonraki dağıtımlarla aynı App Service Web uygulamasını otomatik olarak hedeflemek vs Code söyler.

1. Linux 'a dağıtıyorsanız, dağıtım tamamlandıktan sonra yeniden dağıtılan Web uygulamanızı görüntülemek için istemde **Web sitesine gözatıp** ' yi seçin. Tarayıcının "Merhaba Dünya!" görüntülemesi gerekir

1. Windows 'a dağıtıyorsanız, önce Web uygulaması için Node.js sürüm numarasını ayarlamanız gerekir:

    1. VS Code ' de, yeni App Service düğümünü genişletin, **uygulama ayarları**' na sağ tıklayın ve **yeni ayar Ekle...**' yi seçin:

        ![Uygulama ayarı komutu Ekle](media/quickstart-nodejs/add-setting.png)

    1. `WEBSITE_NODE_DEFAULT_VERSION`Ayar anahtarı için girin.
    1. `10.15.2`Ayar değeri için girin.
    1. App Service düğümüne sağ tıklayın ve **Yeniden Başlat** ' ı seçin.

        ![App Service komutunu yeniden Başlat](media/quickstart-nodejs/restart.png)

    1. App Service 'e daha fazla düğüm sağ tıklayın ve **Web sitesine gidin**' i seçin.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Azure oturum açma sorunlarını giderme

Azure 'da oturum açarken **"adı [ABONELIK kimliği] olan abonelik bulunamıyor"** hatasını görürseniz, bunun nedeni bir ara sunucu arkasında olduğunuzdan ve Azure API 'sine ulaşamamanıza kaynaklanabilir. `HTTP_PROXY` `HTTPS_PROXY` Kullanarak terminalinizdeki proxy bilgileriniz ile değişkenleri yapılandırın ve ortam değişkenlerini kullanın `export` .

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Ortam değişkenlerinin ayarlanması sorunu düzeltmezse Yukarıdaki **bir sorunla karşılaştım** düğmesini seçerek bizimle iletişim kurun.

### <a name="update-the-app"></a>Uygulamayı güncelleştirme

VS Code düzenleme yaparak, dosyalarınızı kaydederek ve sonra yalnızca var olan uygulamayı yeni bir tane oluşturmak yerine yalnızca seçmeden aynı işlemi kullanarak bu uygulamaya yapılan değişiklikleri dağıtabilirsiniz.

## <a name="viewing-logs"></a>Günlükleri görüntüleme

Günlük çıktısını (çağrıları `console.log` ) uygulamadan doğrudan vs Code çıkış penceresinde görüntüleyebilirsiniz.

1. **Azure App SERVICE** Gezgini ' nde uygulama düğümüne sağ tıklayın ve **akış günlüklerini Başlat**' ı seçin.

    ![Akış günlüklerini başlatma](media/quickstart-nodejs/view-logs.png)

1. İstendiğinde, günlüğü etkinleştirmeyi ve uygulamayı yeniden başlatmayı seçin. Uygulama yeniden başlatıldıktan sonra VS Code çıkış penceresi, günlük akışı bağlantısıyla açılır. 

    :::image type="content" source="media/quickstart-nodejs/enable-restart.png" alt-text="Günlüğe kaydetmeyi etkinleştirmek için Visual Studio Code isteminin ekran görüntüsü ve Evet düğmesi seçili olarak uygulamayı yeniden başlatın.":::

1. Birkaç saniye sonra çıkış penceresinde, günlük akış hizmetine bağlı olduğunuzu belirten bir ileti gösterilir. Tarayıcıdaki sayfayı yenileyerek daha fazla çıkış etkinliği oluşturabilirsiniz.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Sonraki adımlar

Tebrikler, bu hızlı başlangıcı başarıyla tamamladınız!

> [!div class="nextstepaction"]
> [Öğretici: MongoDB ile uygulama Node.js](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Node.js uygulamasını yapılandırma](configure-language-nodejs.md)

Diğer Azure uzantılarına göz atın.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure İşlevleri](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker Araçları](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLı araçları](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager araçları](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ya da Azure Uzantı paketi [Için düğüm paketini](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) yükleyerek tümünü alın.
::: zone-end

::: zone pivot="platform-linux"  
## <a name="prerequisites"></a>Önkoşullar

Azure hesabınız yoksa, herhangi bir hizmet birleşimini denemek için bugün Azure kredilerine $200 ile ücretsiz bir hesap için [kaydolun](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) .

Node.js Paket Yöneticisi [Node.js ve NPM](https://nodejs.org/en/download)ile birlikte [Visual Studio Code](https://code.visualstudio.com/) gerekir.

Ayrıca, bir hizmet olarak Azure platformunda (PaaS) Linux Web Apps oluşturmak, yönetmek ve dağıtmak için kullanabileceğiniz [Azure App Service uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)yüklemeniz gerekir.

### <a name="sign-in"></a>Oturum açın

Uzantı yüklendikten sonra Azure hesabınızda oturum açın. **Azure uygulama hizmeti** Gezginini göstermek Için etkinlik çubuğunda Azure logosu ' nu seçin. **Azure 'Da oturum aç...** ' ı seçin ve yönergeleri izleyin.

![Azure 'da oturum açın](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Sorun giderme

**"Adı [ABONELIK kimliği] olan abonelik bulunamıyor"** hatasını görürseniz, bunun nedeni bir ara sunucu arkasında olduğunuzdan ve Azure API 'sine ulaşamamanıza kaynaklanabilir. `HTTP_PROXY` `HTTPS_PROXY` Kullanarak terminalinizdeki proxy bilgileriniz ile değişkenleri yapılandırın ve ortam değişkenlerini kullanın `export` .

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Ortam değişkenlerinin ayarlanması sorunu düzeltmezse, aşağıdan **bir sorunla karşılaştım** düğmesini seçerek bizimle iletişime geçin.

### <a name="prerequisite-check"></a>Önkoşul denetimi

Devam etmeden önce tüm önkoşulların yüklü ve yapılandırılmış olduğundan emin olun.

VS Code, Azure e-posta adresinizi **Azure App SERVICE** Explorer 'Da durum çubuğunda ve aboneliğinizde görmeniz gerekir.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Node.js uygulamanızı oluşturma

Ardından, buluta dağıtılabilecek bir Node.js uygulaması oluşturun. Bu hızlı başlangıç, uygulamayı bir terminalden hızlı bir şekilde genişletmek için bir uygulama Oluşturucu kullanır.

> [!TIP]
> [Node.js öğreticisini](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)zaten tamamladıysanız [Azure 'a dağıtım](#deploy-to-azure)aşamasına geçebilirsiniz.

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Hızlı Oluşturucu ile yeni bir uygulama oluşturma

[Express](https://www.expressjs.com) , Node.js uygulamaları oluşturmaya ve çalıştırmaya yönelik popüler bir çerçevedir. [Hızlı Oluşturucu](https://expressjs.com/en/starter/generator.html) aracını kullanarak yeni bir Express uygulaması oluşturabilirsiniz (oluşturma). Hızlı Oluşturucu bir NPM modülü olarak gönderilir ve NPM komut satırı aracı kullanılarak doğrudan (yükleme olmadan) çalıştırılabilir `npx` .

```bash
npx express-generator myExpressApp --view pug --git
```

`--view pug --git`Parametreler, oluşturucuya [Pug](https://pugjs.org/api/getting-started.html) şablon altyapısını (eski adıyla olarak bilinen `jade` ) kullanmasını ve bir dosya oluşturmayı söyler `.gitignore` .

Uygulamanın tüm bağımlılıklarını yüklemek için yeni klasöre gidin ve çalıştırın `npm install` .

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Uygulamayı çalıştırma

Sonra, uygulamanın çalıştığından emin olun. Terminalden, `npm start` sunucuyu başlatmak için komutunu kullanarak uygulamayı başlatın.

```bash
npm start
```

Şimdi tarayıcınızı açın ve ' a gidin `http://localhost:3000` . burada şöyle bir şey görmeniz gerekir:

![Express uygulaması çalıştırılıyor](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Azure’a dağıtın

Bu bölümde, VS Code ve Azure App Service uzantısını kullanarak Node.js uygulamanızı dağıtırsınız. Bu hızlı başlangıç, uygulamanızın sıkıştırılması ve Linux üzerinde bir Azure Web uygulamasına dağıtıldığı en temel dağıtım modelini kullanır.

### <a name="deploy-using-azure-app-service"></a>Azure App Service kullanarak dağıtma

İlk olarak, uygulama klasörünüzü VS Code açın.

```bash
code .
```

**Azure App SERVICE** Gezgini ' nde, uygulamanızı Azure 'a dağıtmak için mavi yukarı ok simgesini seçin.

:::image type="content" source="./media/quickstart-nodejs/deploy.png" alt-text="Mavi dağıtım oku seçiliyken Visual Studio Code Azure App Service menüsünün ekran görüntüsü.":::

> [!TIP]
> Ayrıca, **komut paletinden** (CTRL + SHIFT + P) ' Web uygulamasına Dağıt ' yazarak ve **Azure App Service: Web uygulamasına dağıt** komutunu çalıştırarak dağıtım yapabilirsiniz.

1. Şu anda açık olan dizini seçin `myExpressApp` .

1. Varsayılan olarak Linux üzerinde App Service dağıtan **Yeni Web uygulaması oluştur**' u seçin.

1. Web uygulamanız için genel olarak benzersiz bir ad yazın ve ENTER tuşuna basın. Uygulama adı için geçerli karakterler şunlardır ' a-z ', ' 0-9 ' ve '-'.

1. **Node.js sürümünüzü** seçin, LTS önerilir.

    Bildirim kanalı, uygulamanız için oluşturulmakta olan Azure kaynaklarını gösterir.

1. Yapılandırmanızı hedef sunucuda çalışacak şekilde güncellemek isteyip istemediğiniz sorulduğunda **Evet** ' i seçin `npm install` . Uygulamanız daha sonra dağıtılır.

    :::image type="content" source="./media/quickstart-nodejs/server-build.png" alt-text="Hedef sunucuda yapılandırmanızı güncelleştirmek için Evet düğmesi seçili olan komut isteminin ekran görüntüsü.":::

1. Dağıtım başladığında, daha sonraki dağıtımlar aynı App Service Web uygulamasını otomatik olarak hedefleyecek şekilde çalışma alanınızı güncelleştirmeniz istenir. Değişikliklerinizin doğru uygulamaya dağıtıldığından emin olmak için **Evet** ' i seçin.

    :::image type="content" source="./media/quickstart-nodejs/save-configuration.png" alt-text="Çalışma alanınızı Evet düğmesi seçili olarak güncelleştirme isteminin ekran görüntüsü.":::

> [!TIP]
> Uygulamanızın bağlantı noktası ortam değişkeni tarafından belirtilen bağlantı noktasında dinleme yaptığını doğrulayın: `process.env.PORT` .

### <a name="browse-the-app-in-azure"></a>Azure 'da uygulamaya gözatın

Dağıtım tamamlandıktan sonra, yeniden dağıtılan Web uygulamanızı görüntülemek için istemde **Web sitesine gözatamazsınız** ' ı seçin.

### <a name="troubleshooting"></a>Sorun giderme

**"Bu dizini veya sayfayı görüntüleme izniniz yok"** hatasını görürseniz, büyük olasılıkla uygulama düzgün bir şekilde başlatılamadı. Sonraki bölüme gidin ve hatayı bulmak ve onarmak için günlük çıktısını görüntüleyin. Sorunu düzeltemedi, aşağıda **bir sorun yaşıyorum** düğmesini seçerek bizimle iletişim kurun. Yardımımız duyuyoruz!

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Uygulamayı güncelleştirme

Aynı işlemi kullanarak ve yeni bir uygulama oluşturmak yerine var olan uygulamayı seçerek bu uygulamaya yapılan değişiklikleri dağıtabilirsiniz.

## <a name="viewing-logs"></a>Günlükleri görüntüleme

Bu bölümde, çalışan App Service uygulamasındaki günlüklerin nasıl görüntüleneceğini (veya "Tail") öğreneceksiniz. Uygulamada yapılan tüm çağrılar `console.log` Visual Studio Code çıkış penceresinde görüntülenir.

Uygulamayı **Azure App SERVICE** Explorer 'da bulun, uygulamaya sağ tıklayın ve **akış günlüklerini Başlat**' ı seçin.

VS Code çıkış penceresi, günlük akışı bağlantısıyla açılır.

![Akış günlüklerini başlatma](./media/quickstart-nodejs/view-logs.png)

:::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Dosya günlüğünü etkinleştirmek ve Evet düğmesi seçili olarak Web uygulamasını yeniden başlatmak için VS Code isteminin ekran görüntüsü.":::

Birkaç saniye sonra, günlük akış hizmetine bağlı olduğunuzu belirten bir ileti görürsünüz. Daha fazla etkinlik görmek için sayfayı birkaç kez yenileyin.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Sonraki adımlar

Tebrikler, bu hızlı başlangıcı başarıyla tamamladınız!

Daha sonra diğer Azure uzantılarına göz atın.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure İşlevleri](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker Araçları](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLı araçları](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager araçları](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ya da Azure Uzantı paketi [Için düğüm paketini](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) yükleyerek tümünü alın.


::: zone-end
