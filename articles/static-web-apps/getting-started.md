---
title: 'Hızlı başlangıç: Azure statik Web Apps ilk statik sitenizi oluşturma'
description: Statik bir siteyi Azure statik Web Apps dağıtmayı öğrenin.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: fb874c25ab688cc5e6723d1023157b8acd9478b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483860"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Hızlı başlangıç: Azure statik Web Apps ilk statik sitenizi oluşturma

Azure statik Web Apps, bir kod deposundan uygulama oluşturarak bir Web sitesi yayımlar. Bu hızlı başlangıçta, Visual Studio Code uzantısını kullanarak bir uygulamayı Azure statik Web uygulamalarına dağıtırsınız.

Azure aboneliğiniz yoksa [ücretsiz bir deneme hesabı oluşturun](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Önkoşullar

- [GitHub](https://github.com) hesabı
- [Azure](https://portal.azure.com) hesabı
- [Visual Studio Code](https://code.visualstudio.com)
- [Visual Studio Code için Azure Static Web Apps uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Git'i yükleyin](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Sonra, Visual Studio Code açın ve **dosya > klasöre** giderek, düzenleyicide makinenize kopyaladığınız depoyu açın.

## <a name="create-a-static-web-app"></a>Statik web uygulaması oluşturma

1. Visual Studio Code'un etkinlik çubuğundan Azure logosunu seçerek Azure uzantıları penceresini açın.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure Logosu":::

    > [!NOTE]
    > Azure ve GitHub'da oturum açmanız gerekir. Daha önce Visual Studio Code'da Azure ve GitHub oturumu açmadıysanız oluşturma işlemi sırasında uzantı ikisinde de oturum açmanızı isteyecektir.

1. _Statik Web Apps_ etiketi altında **artı işaretini** seçin.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Uygulama adı":::

1. Pageç, düzenleyicinin en üstünde açılır ve uygulamanızı ad vermenizi ister.

    **my-first-static-web-app** yazıp **Enter** tuşuna basın.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Statik web uygulaması oluşturma":::

1. Uygulama türü ile eşleşen ön ayarları seçin.

    # <a name="no-framework"></a>[Çerçeve yok](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Uygulama önayarları: çerçeve yok":::

    Uygulama dosyalarının konumu olarak **./** girin.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Uygulama dosyaları konumu":::

    Azure Işlevleri API 'sinin konumu olarak **Şimdi atla** ' yı seçin.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API konumu":::

    Yapı çıkış konumu olarak **./** girin.

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Uygulama derlemesi çıkış konumu":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Bir angular önayarı olsa da, bu uygulama için uygun bir çıkış konumu sağlayabilmeniz için **özel** seçeneğini belirleyin.

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Uygulama önayarları: angular":::

    Uygulama dosyalarının konumu olarak **./** girin.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Uygulama dosyalarının konumu: angular":::

    Azure Işlevleri API 'sinin konumu olarak **Şimdi atla** ' yı seçin.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API konumu: angular":::

    Derleme çıkış konumu olarak **Dist/angular-Basic** girin.

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="Uygulama derleme çıkış konumu: angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Uygulama önayarları: tepki verme":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Uygulama önayarları: Vue":::

    ---

1. Size en yakın konumu seçin ve **Enter** tuşuna basın.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Kaynak konumu":::

1. Uygulama oluşturulduktan sonra Visual Studio Code'da bir onay bildirimi gösterilir.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Oluşturma onayı":::

    Sonra, **GitHub 'Daki açık eylemler** düğmesine tıklayın. Bu sayfa, uygulamanın yapı durumunu gösterir.

    GitHub eylemi tamamlandıktan sonra yayınlanan web sitesine gidebilirsiniz.

1. Web sitesini tarayıcıda görüntülemek için statik Web Apps uzantısında projeye sağ tıklayın ve **siteye gözatma**' yı seçin.

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Siteye gözatamıyorum":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, Azure statik Web Apps örneğini uzantı aracılığıyla silebilirsiniz.

Visual Studio Code Gezgin penceresinde _statik Web Apps_ bölümüne dönüp, **ilk-statik-Web-uygulaması** ' na sağ tıklayın ve **Sil**' i seçin.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Uygulamayı Sil":::

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [API ekleme](add-api.md)
