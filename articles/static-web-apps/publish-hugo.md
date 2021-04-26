---
title: 'Öğretici: Azure statik Web Apps bir Hugo sitesi yayımlama'
description: Azure statik Web Apps bir Hugo uygulamasını dağıtmayı öğrenin.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 4539c32a367bb0974212d989176a96b530da21a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652364"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Öğretici: Azure statik Web Apps önizleme 'ye bir Hugo sitesi yayımlama

Bu makalede, [Azure statik Web Apps](overview.md)bir [Hugo](https://gohugo.io/) Web uygulaması oluşturma ve dağıtma gösterilmektedir. Nihai sonuç, uygulamanın nasıl oluşturulduğunu ve yayımlanabileceğini denetlemenize olanak tanıyan, ilişkili GitHub eylemlerine sahip yeni bir Azure statik Web uygulamasıdır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> - Kugo uygulaması oluşturma
> - Azure statik Web Apps ayarlama
> - Kugo uygulamasını Azure 'a dağıtma

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. Bir hesabınız yoksa, [ücretsiz olarak bir hesap oluşturabilirsiniz](https://azure.microsoft.com/free/).
- GitHub hesabı. Bir hesabınız yoksa, [ücretsiz olarak bir hesap oluşturabilirsiniz](https://github.com/join).

## <a name="create-a-hugo-app"></a>Kugo uygulaması oluşturma

Kugo komut satırı arabirimini (CLı) kullanarak bir Hugo uygulaması oluşturma:

1. IŞLETIM sisteminize Kugo için [Yükleme kılavuzunu](https://gohugo.io/getting-started/installing/) izleyin.

1. Bir Terminal açın

1. Yeni bir uygulama oluşturmak için Kugo CLı 'yi çalıştırın.

   ```bash
   hugo new site static-app
   ```

1. Yeni oluşturulan uygulamaya gidin.

   ```bash
   cd static-app
   ```

1. Git deposu başlatın.

   ```bash
    git init
   ```

1. Ardından, git alt modülü olarak bir tema yükleyip ardından Kugo yapılandırma dosyasında belirterek siteye bir tema ekleyin.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Değişiklikleri işleyin.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Uygulamanızı GitHub 'a gönderme

Azure statik Web Apps bağlanmak için GitHub 'da bir depoya ihtiyacınız vardır. Aşağıdaki adımlarda siteniz için nasıl bir depo oluşturacağınız gösterilmektedir.

1. [https://github.com/new](https://github.com/new)Adlı **kugo-static-App**' den boş bir GITHUB deposu (Benioku dosyası oluşturmayın) oluşturun.

1. GitHub deposunu yerel depoya uzak olarak ekleyin. Aşağıdaki komutta yer tutucu yerine GitHub Kullanıcı adınızı eklediğinizden emin olun `<YOUR_USER_NAME>` .

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Yerel depoyu GitHub 'a iletin.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Web uygulamanızı dağıtın

Aşağıdaki adımlarda, yeni bir statik site uygulamasının nasıl oluşturulacağı ve bir üretim ortamına nasıl dağıtılacağı gösterilmektedir.

### <a name="create-the-application"></a>Uygulama oluşturma

1. [Azure Portal](https://portal.azure.com) gidin
1. **Kaynak oluştur** seçeneğine tıklayın
1. **Statik Web Uygulamaları** için arama yapın
1. **Statik Web Uygulamaları (Önizleme)** seçeneğine tıklayın
1. **Oluştur** seçeneğine tıklayın

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Portalda Azure statik Web Apps kaynağı oluşturma":::

1. **Abonelik** için, listelenen aboneliği kabul edin veya açılan listeden yeni bir tane seçin.

1. _Kaynak grubu_' nda **Yeni**' yi seçin. _Yeni kaynak grubu adı_' nda **Hugo-static-App** yazın ve **Tamam**' ı seçin.

1. Ardından, **ad** kutusunda uygulamanız için bir ad. Geçerli karakterler `a-z` ,, `A-Z` ve `0-9` içerir `-` .

1. _Bölge_ için bir kullanılabilir bölge seçin.

1. _SKU_ için **ücretsiz**' i seçin.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Ayrıntılar dolduruldu":::

1. **GitHub Ile oturum aç** düğmesine tıklayın.

1. Depoyu oluşturduğunuz **kuruluşu** seçin.

1. _Depo_ olarak **Hugo-static-App** ' i seçin.

1. _Dal_ için **ana** öğesini seçin.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Tamamlanan GitHub bilgileri":::

### <a name="build"></a>Derleme

Daha sonra, yapı işleminin uygulamanızı derlemek için kullandığı yapılandırma ayarlarını eklersiniz. Aşağıdaki ayarlar GitHub eylemi iş akışı dosyasını yapılandırır.

1. Derleme yapılandırmasını düzenlemek için **İleri: Derleme >** düğmesine tıklayın

1. _Uygulama konumunu_ olarak ayarlayın **/** .

1. _Uygulama yapıtı konumunu_ **ortak** olarak ayarlayın.

   Şu anda bir API dağıtmadığından, _API konumu_ için bir değer gerekli değildir.

### <a name="review-and-create"></a>Gözden geçir ve oluştur

1. Ayrıntıların tümünün doğru olduğunu doğrulamak için **gözden geçir + oluştur** düğmesine tıklayın.

1. Azure statik Web Apps oluşturulmasını başlatmak ve dağıtıma yönelik bir GitHub eylemi sağlamak için **Oluştur** ' a tıklayın.

1. GitHub eyleminin tamamlanmasını bekleyin.

1. Yeni oluşturulan Azure statik Web Apps kaynağının Azure portal _genel bakış_ penceresinde, dağıtılan uygulamanızı açmak için _URL_ bağlantısına tıklayın.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Dağıtılan uygulama":::

#### <a name="custom-hugo-version"></a>Özel Kugo sürümü

Statik bir Web uygulaması oluşturduğunuzda, uygulama için yayımlama yapılandırma ayarlarını içeren bir [iş akışı dosyası](./github-actions-workflow.md) oluşturulur. Bölümünde için bir değer sağlayarak iş akışı dosyasında belirli bir Hugo sürümünü belirleyebilirsiniz `HUGO_VERSION` `env` . Aşağıdaki örnek yapılandırmasında, set Hugo 'nun belirli bir sürüme nasıl ayarlanacağı gösterilmektedir.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel etki alanı ekleme](custom-domain.md)
