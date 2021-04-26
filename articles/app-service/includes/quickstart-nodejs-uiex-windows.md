---
title: 'Hızlı başlangıç: Node.js Web uygulaması oluşturma-Windows'
description: İlk Node.js Merhaba Dünya Windows platformu için dakikalar içinde Azure App Service dağıtın.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 324919c1d085302e675e0bc9a370ce35f52a0f1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102109031"
---
<!-- advanced for windows -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. Visual Studio Code Azure App Service dağıtım

1. Uygulama klasörünüzü Visual Studio Code açın.

    ```bash
    code .
    ```

1. **Azure App SERVICE** Gezgini ' nde **Azure 'da oturum aç...** öğesini seçin ve yönergeleri izleyin. Oturum açıldıktan sonra gezgin, Azure aboneliğinizin adını göstermelidir.

    ![Azure'da oturum açma](../media/quickstart-nodejs/sign-in.png)

    <details>
    <summary>Azure oturum açma sorunlarını giderme</summary>
    
    Azure 'da oturum açarken **"adı [ABONELIK kimliği] olan abonelik bulunamıyor"** hatasını görürseniz, bunun nedeni bir ara sunucu arkasında olduğunuzdan ve Azure API 'sine ulaşamamanıza kaynaklanabilir. `HTTP_PROXY` `HTTPS_PROXY` Kullanarak terminalinizdeki proxy bilgileriniz ile değişkenleri yapılandırın ve ortam değişkenlerini kullanın `export` .
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [Sorun bildirin](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. **Azure App SERVICE** Gezgini ' nde, uygulamanızı Azure 'a dağıtmak için mavi yukarı ok simgesini seçin. 

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="Azure App Service 'in seçili mavi ok simgesini gösteren VS Code ekran görüntüsü.":::

1. Şu anda açık olan dizini seçin `nodejs-docs-hello-world` .

1. **Yeni Web uygulaması oluştur öğesini seçin... Gelişmiş**, Windows üzerinde App Service dağıtmak için.

1. Genel olarak benzersiz bir tür <abbr title="Uygulama adı için geçerli karakterler şunlardır ' a-z ', ' 0-9 ' ve '-'.">name</abbr> Web uygulamanız için **yazın ve ENTER** tuşuna basın. 
1. **Yeni kaynak grubu oluştur**' u seçin, ardından kaynak grubu için gibi bir ad girin `AppServiceQS-rg` .
1. **Node.js sürümünüzü** seçin, LTS önerilir.

    Bildirim kanalı, uygulamanız için oluşturulmakta olan Azure kaynaklarını gösterir.
1. İşletim sistemi için **Windows** ' u seçin.
1. **Yeni App Service planı oluştur**' u seçin, sonra plan için bir ad girin (gibi `AppServiceQS-plan` ) ve ardından fiyatlandırma katmanı için **F1 ücretsiz** ' i seçin.
1. Application Insights sorulduğunda **Şu anda atla** ' yı seçin.
1. Size yakın bir bölge veya erişmek istediğiniz kaynakları seçin.

1. Daha sonraki dağıtımlar aynı App Service Web uygulamasını otomatik olarak hedefleyecek şekilde, çalışma alanınızı güncelleştirmeniz istendiğinde **Evet** ' i seçin. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Çalışma alanınızı Evet düğmesi seçili olarak güncelleştirme isteminin ekran görüntüsü.":::

1. App Service 'e daha fazla düğüm sağ tıklayın ve **Web uygulamasına dağıt**' ı seçin.

1. App Service 'e daha fazla düğüm sağ tıklayın ve **Web sitesine gidin**' i seçin.

    [Sorun bildirin](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

1. Dağıtım tamamlandıktan sonra, yeniden dağıtılan Web uygulamanızı görüntülemek için istemde **Web sitesine gözatamazsınız** ' ı seçin.

<br/>
<details>
<summary><strong>Sorun giderme</strong></summary>

Bu adımları tamamlayamadıysanız aşağıdakileri denetleyin:

* Uygulamanızın bağlantı noktası ortam değişkeni tarafından belirtilen bağlantı noktasında dinleme yaptığını doğrulayın: `process.env.PORT` .

* **"Bu dizini veya sayfayı görüntüleme izniniz yok"** hatasını görürseniz, büyük olasılıkla uygulama düzgün bir şekilde başlatılamadı. Hatayı bulmak ve onarmak için günlük çıkışını gözden geçirin. 

</details>

<br>

[Sorun bildirin](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br/>
<hr/>
