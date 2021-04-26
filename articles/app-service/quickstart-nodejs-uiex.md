---
title: 'Hızlı başlangıç: Node.js Web uygulaması oluşturma'
description: İlk Node.js Merhaba Dünya Azure App Service dakikalar içinde dağıtın.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7cd9add699d9bd4a3eff9cdcf1e65af0d754b70a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748862"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Azure App Service'te Node.js web uygulaması oluşturma

Kullanmaya başlayın <abbr title="Web uygulamalarını, REST API 'Leri ve mobil arka uç uygulamalarını barındırmak için HTTP tabanlı bir hizmet.">Azure App Service</abbr> Visual Studio Code kullanarak yerel olarak Node.js/Express uygulaması oluşturup uygulamayı Azure bulutuna dağıtma. Kullandığınız için <abbr title="Azure App Service, uygulamanızın diğer müşterilerin uygulamaları da dahil diğer uygulamalarla aynı VM 'lerde çalıştığı temel bir katman. Bu katman geliştirme ve test amaçlıdır.">ücretsiz katman</abbr>, bu hızlı başlangıcı tamamlamaya yönelik bir ücret ödeirsiniz.

## <a name="1-prepare-your-environment"></a>1. ortamınızı hazırlayın

- Etkin bir Azure hesabı <abbr title="Azure aboneliği, Azure’da kaynakların sağlanması için kullanılan mantıksal bir kapsayıcıdır. Sanal makineler (VM) ve veritabanları gibi tüm kaynaklarınızın ayrıntılarını içerir.">aboneliği</abbr>. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Git'i yükleyin</a>
- [Node.js ve npm](https://nodejs.org). `node --version`Node.js yüklendiğini doğrulamak için komutunu çalıştırın.
- [Visual Studio Code](https://code.visualstudio.com/).
- Visual Studio Code için [Azure App Service uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) .

[Sorun bildirin](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)




<br>
<hr/>

## <a name="2-clone-and-run-a-local-nodejs-application"></a>2. yerel bir Node.js uygulamasını kopyalama ve çalıştırma

1. Yerel bilgisayarınızda bir Terminal açın ve örnek depoyu kopyalayın:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Yeni uygulama klasörüne gidin:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Bağımlılıkları yükler:

    ```bash
    npm install
    ```

1. Uygulamayı yerel olarak test etmek için başlatın:

    ```bash
    npm start
    ```
    
1. Tarayıcınızı açın ve adresine gidin `http://localhost:1337` . Tarayıcının "Merhaba Dünya!" görüntülemesi gerekir.

1. <kbd></kbd>  +  Sunucuyu durdurmak için terminalde CTRL<kbd>C</kbd> tuşlarına basın.

[Sorun bildirin](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>




<!-- VS Code extension works differently for Windows/Linus - Step 3 -->

::: zone pivot="platform-windows"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-windows.md)]


::: zone-end

::: zone pivot="platform-linux"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-linux.md)]

::: zone-end


## <a name="4-viewing-logs-from-visual-studio-code"></a>4. Visual Studio Code günlükleri görüntüleme

Şunu görüntüleyin <abbr title="Uygulamada yapılan tüm çağrılar `console.log` Visual Studio Code çıkış penceresinde görüntülenir.">açmasını</abbr> çalışan App Service uygulamasının.

1. Uygulamayı **Azure App SERVICE** Explorer 'da bulun, uygulama adına sağ tıklayın ve **akış günlüklerini Başlat**' ı seçin.

1. Visual Studio Code çıkış penceresi açılır.

    ![Akış günlüklerini görüntüle](./media/quickstart-nodejs/view-logs.png)

    :::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Dosya günlüğünü etkinleştirmek ve Evet düğmesi seçili olarak Web uygulamasını yeniden başlatmak için VS Code isteminin ekran görüntüsü.":::

1. Birkaç saniye sonra, günlük akış hizmetine bağlı olduğunuzu belirten bir ileti görürsünüz. 
1. Daha fazla etkinlik görmek için sayfayı birkaç kez yenileyin.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    2020-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2020-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2020-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2020-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    </pre>

<br>

[Sorun bildirin](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. Kaynakları Temizleme

Uygulamayı **Azure App SERVICE** Explorer 'da bulun, uygulama adına sağ tıklayın ve **Sil**' i seçin. 

:::image type="content" source="./media/quickstart-nodejs/delete-resource-ieux.png" alt-text="' * * ' AZURE APP SERVICE ' * * ' Gezgininde uygulamayı bulun, uygulama adına sağ tıklayın ve ' Sil ' seçeneğini belirleyin":::

## <a name="next-steps"></a>Sonraki adımlar

Tebrikler, bu hızlı başlangıcı başarıyla tamamladınız! Aynı işlemi kullanarak ve yeni bir uygulama oluşturmak yerine var olan uygulamayı seçerek bu uygulamaya yapılan değişiklikleri dağıtabilirsiniz.

Daha sonra diğer Azure uzantılarına göz atın.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure İşlevleri](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker Araçları](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLı araçları](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager araçları](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ya da Azure Uzantı paketi [Için düğüm paketini](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) yükleyerek tümünü alın.