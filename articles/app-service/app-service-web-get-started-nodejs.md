---
title: Node. js web uygulaması oluşturma-Azure App Service | Microsoft Docs
description: Azure App Service Web Uygulamalarında ilk Node.js Hello World uygulamanızı birkaç dakika içinde dağıtın.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 09/30/2019
ms.author: cephalin
ms.custom: seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 98d939c69bb03666dbeed124778b06f8a7e73532
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73471466"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Azure App Service'te Node.js web uygulaması oluşturma 

Azure App Service, yüksek düzeyde ölçeklenebilir, kendini yayama bir Web barındırma hizmeti sağlar. Bu hızlı başlangıçta Azure App Service için bir Node. js uygulamasının nasıl dağıtılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Azure hesabınız yoksa, herhangi bir hizmet birleşimini denemek için bugün Azure kredilerine $200 ile ücretsiz bir hesap için [kaydolun](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) .

Node. [js ve NPM](https://nodejs.org/en/download)için Node. js Paket Yöneticisi ile birlikte yüklü [Visual Studio Code](https://code.visualstudio.com/) gerekir.

Ayrıca, bir hizmet olarak Azure platformunda (PaaS) Linux Web Apps oluşturmak, yönetmek ve dağıtmak için kullanabileceğiniz [Azure App Service uzantısını](vscode:extension/ms-azuretools.vscode-azureappservice)yüklemeniz gerekir.

### <a name="sign-in"></a>Oturum aç

Uzantı yüklendikten sonra Azure hesabınızda oturum açın. Azure **uygulama hizmeti** Gezginini göstermek Için etkinlik çubuğunda Azure logosu ' nda öğesini seçin. **Azure 'Da oturum aç...** ' ı seçin ve yönergeleri izleyin.

![Azure 'da oturum açın](containers/media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Sorun giderme

**"Adı [ABONELIK kimliği] olan abonelik bulunamıyor"** hatasını görürseniz, bunun nedeni bir ara sunucu arkasında olduğunuzdan ve Azure API 'sine ulaşamamanıza kaynaklanabilir. `export`kullanarak terminalinizdeki proxy bilgilerinizi `HTTP_PROXY` ve `HTTPS_PROXY` ortam değişkenlerini yapılandırın.

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

## <a name="create-your-nodejs-application"></a>Node. js uygulamanızı oluşturma

Ardından, buluta dağıtılabilecek bir Node. js uygulaması oluşturun. Bu hızlı başlangıç, uygulamayı bir terminalden hızlı bir şekilde genişletmek için bir uygulama Oluşturucu kullanır.

> [!TIP]
> [Node. js öğreticisini](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)zaten tamamladıysanız, [Azure 'a dağıtım](#deploy-to-azure)aşamasına geçebilirsiniz.

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Hızlı Oluşturucu ile yeni bir uygulama oluşturma

[Express](https://www.expressjs.com) , Node. js uygulamaları oluşturmaya ve çalıştırmaya yönelik popüler bir çerçevedir. [Hızlı Oluşturucu](https://expressjs.com/en/starter/generator.html) aracını kullanarak yeni bir Express uygulaması oluşturabilirsiniz (oluşturma). Hızlı Oluşturucu bir NPM modülü olarak gönderilir ve NPM komut satırı aracı `npx`kullanarak doğrudan (yükleme olmadan) çalıştırılabilir.

```bash
npx express-generator myExpressApp --view pug --git
```

`--view pug --git` parametreler, oluşturucunun [Pug](https://pugjs.org/api/getting-started.html) şablon altyapısını (eski adıyla `jade`) kullanmasını ve bir `.gitignore` dosyası oluşturmasını söyler.

Uygulamanın tüm bağımlılıklarını yüklemek için yeni klasöre gidin ve `npm install`çalıştırın.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Uygulamayı çalıştırma

Sonra, uygulamanın çalıştığından emin olun. Terminalden, sunucuyu başlatmak için `npm start` komutunu kullanarak uygulamayı başlatın.

```bash
npm start
```

Şimdi tarayıcınızı açın ve şuna benzer bir şey görmeniz gereken [http://localhost:3000](http://localhost:3000)gidin:

![Express uygulaması çalıştırılıyor](containers/media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Azure’a Dağıt

Bu bölümde, VS Code ve Azure App Service uzantısını kullanarak Node. js uygulamanızı dağıtırsınız. Bu hızlı başlangıç, uygulamanızın sıkıştırılması ve Linux üzerinde bir Azure Web uygulamasına dağıtıldığı en temel dağıtım modelini kullanır.

### <a name="deploy-using-azure-app-service"></a>Azure App Service kullanarak dağıtma

İlk olarak, uygulama klasörünüzü VS Code açın.

```bash
code .
```

**Azure App SERVICE** Gezgini ' nde, uygulamanızı Azure 'a dağıtmak için mavi yukarı ok simgesini seçin.

![Web uygulamasına dağıtma](containers/media/quickstart-nodejs/deploy.png)

> [!TIP]
> Ayrıca, **komut paletinden** (CTRL + SHIFT + P) ' Web uygulamasına Dağıt ' yazarak ve **Azure App Service: Web uygulamasına dağıt** komutunu çalıştırarak dağıtım yapabilirsiniz.

1. Şu anda açık olan dizini seçin `myExpressApp`.

1. Dağıtmak istediğiniz işletim sistemini temel alan bir oluşturma seçeneği belirleyin:

    - Linux: **Yeni Web uygulaması oluştur**seçeneğini belirleyin.
    - Windows: **Yeni Web uygulaması oluştur** ' u seçin ve **Gelişmiş** seçeneğini belirleyin.

1. Web uygulamanız için genel olarak benzersiz bir ad yazın ve ENTER tuşuna basın. Uygulama adı için geçerli karakterler şunlardır ' a-z ', ' 0-9 ' ve '-'.

1. Linux hedefleniyorsa, sorulduğunda bir Node. js sürümü seçin. **LTS** sürümü önerilir.

1. Windows 'u **Gelişmiş** seçeneğini kullanarak hedefliyorsanız, ek istemleri izleyin:
    1. **Yeni kaynak grubu oluştur**' u seçin ve ardından kaynak grubu için bir ad girin.
    1. İşletim sistemi için **Windows** ' u seçin.
    1. Mevcut bir App Service planı seçin ya da yeni bir tane oluşturun. Yeni bir plan oluştururken bir fiyatlandırma katmanı seçebilirsiniz.
    1. Application Insights sorulduğunda **Şu anda atla** ' yı seçin.
    1. Size yakın bir bölge veya erişmek istediğiniz kaynakları seçin.

1. Tüm istemlere yanıt verdikten sonra bildirim kanalı, uygulamanız için oluşturulmakta olan Azure kaynaklarını gösterir.

1. Yapılandırmanızı, hedef sunucuda `npm install` çalıştıracak şekilde güncellemek isteyip istemediğiniz sorulduğunda **Evet** ' i seçin. Uygulamanız daha sonra dağıtılır.

    ![Yapılandırılmış dağıtım](containers/media/quickstart-nodejs/server-build.png)

1. Dağıtım başladığında, daha sonraki dağıtımlar aynı App Service Web uygulamasını otomatik olarak hedefleyecek şekilde çalışma alanınızı güncelleştirmeniz istenir. Değişikliklerinizin doğru uygulamaya dağıtıldığından emin olmak için **Evet** ' i seçin.

    ![Yapılandırılmış dağıtım](containers/media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Uygulamanızın, bağlantı noktası ortam değişkeni tarafından belirtilen bağlantı noktasında dinleme olduğundan emin olun: `process.env.PORT`.

### <a name="browse-the-app-in-azure"></a>Azure 'da uygulamaya gözatın

Dağıtım tamamlandıktan sonra, yeniden dağıtılan Web uygulamanızı görüntülemek için istemde **Web sitesine gözatamazsınız** ' ı seçin.

### <a name="troubleshooting"></a>Sorun giderme

**"Bu dizini veya sayfayı görüntüleme izniniz yok"** hatasını görürseniz, büyük olasılıkla uygulama düzgün bir şekilde başlatılamadı. Sonraki bölüme gidin ve hatayı bulmak ve onarmak için günlük çıktısını görüntüleyin. Sorunu düzeltemedi, aşağıda **bir sorun yaşıyorum** düğmesini seçerek bizimle iletişim kurun. Yardımımız duyuyoruz!

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Uygulamayı güncelleştirme

Aynı işlemi kullanarak ve yeni bir uygulama oluşturmak yerine var olan uygulamayı seçerek bu uygulamaya yapılan değişiklikleri dağıtabilirsiniz.

## <a name="viewing-logs"></a>Günlükleri görüntüleme

Bu bölümde, çalışan App Service uygulamasındaki günlüklerin nasıl görüntüleneceğini (veya "Tail") öğreneceksiniz. Uygulamadaki `console.log` yapılan çağrılar Visual Studio Code çıktı penceresinde görüntülenir.

Uygulamayı **Azure App SERVICE** Explorer 'da bulun, uygulamaya sağ tıklayın ve **akış günlüklerini görüntüle**' yi seçin.

İstendiğinde, günlüğü etkinleştirmeyi ve uygulamayı yeniden başlatmayı seçin. Uygulama yeniden başlatıldıktan sonra VS Code çıkış penceresi, günlük akışı bağlantısıyla açılır.

![Akış günlüklerini görüntüle](containers/media/quickstart-nodejs/view-logs.png)

![Günlüğe kaydetmeyi etkinleştirme ve yeniden başlatma](containers/media/quickstart-nodejs/enable-restart.png)

Birkaç saniye sonra, günlük akış hizmetine bağlı olduğunuzu belirten bir ileti görürsünüz. Daha fazla etkinlik görmek için sayfayı birkaç kez yenileyin.

    ```bash
    2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    ```

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
