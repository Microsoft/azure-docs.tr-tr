---
title: 'Öğretici: Azure statik Web Apps üzerinde sunucu tarafından işlenen Nuxt.js Web siteleri dağıtma'
description: Azure statik Web Apps ile Nuxt.js dinamik siteler oluşturun ve dağıtın.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: 8f21f5fa8ee9035fe594cecff37a63b1ef2115cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97563475"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps-preview"></a>Azure statik Web Apps önizlemede sunucu tarafından işlenen Nuxt.js Web sitelerini dağıtma

Bu öğreticide, [Nuxt.js](https://nuxtjs.org) üretilmiş bir statik Web sitesini [Azure statik Web Apps](overview.md)dağıtmayı öğreneceksiniz. Başlamak için Nuxt.js uygulamasını ayarlamayı, yapılandırmayı ve dağıtmayı öğreneceksiniz. Bu işlem sırasında, genellikle Nuxt.js ile statik sayfalar oluştururken ortaya çıkacak yaygın güçlüklerle uğraşmak üzere de öğrenirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).
- GitHub hesabı. [Ücretsiz hesap oluşturun](https://github.com/join).
- [Node.js](https://nodejs.org) yüklendi.

## <a name="set-up-a-nuxtjs-app"></a>Nuxt.js uygulaması ayarlama

Kullanarak yeni bir Nuxt.js projesi ayarlayabilirsiniz `create-nuxt-app` . Yeni bir proje yerine, bu öğreticide mevcut bir depoyu kopyalayarak başlarsınız. Bu depo, dinamik bir Nuxt.js uygulamasının statik bir site olarak nasıl dağıtılacağını göstermek üzere ayarlanır.

1. Bir şablon deposundan GitHub hesabınızda yeni bir depo oluşturun.
1. Şuraya gidin [http://github.com/staticwebdev/nuxtjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nuxtjs-starter/generate)
1. Depoyu adlandırın **nuxtjs-Starter**
1. Sonra, yeni depoyu makinenize kopyalayın. <YOUR_GITHUB_ACCOUNT_NAME> hesap adınızla değiştirdiğinizden emin olun.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. Yeni kopyalanmış Nuxt.js uygulamasına gidin:

   ```bash
   cd nuxtjs-starter
   ```

1. Bağımlılıkları yükler:

    ```bash
    npm install
    ```

1. Geliştirme aşamasında Nuxt.js uygulamayı Başlat:

    ```bash
    npm run dev
    ```

`http://localhost:3000`Aşağıdaki Web sitesini tercih ettiğiniz tarayıcınızda açık olarak görmeniz gereken uygulamayı açmak için şuraya gidin:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="Nuxt.js uygulamayı Başlat":::

Bir çerçeveye/kitaplığa tıkladığınızda, seçili öğe hakkında bir Ayrıntılar sayfası görmeniz gerekir:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="Ayrıntılar sayfası":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>Nuxt.js derlemeden statik bir Web sitesi oluşturma

Kullanarak bir Nuxt.js sitesi oluşturduğunuzda `npm run build` , uygulama statik bir site değil, geleneksel bir Web uygulaması olarak oluşturulur. Statik bir site oluşturmak için aşağıdaki uygulama yapılandırmasını kullanın.

1. Derleme betikindeki _package.js_ komutu kullanarak yalnızca statik bir site oluşturacak şekilde güncelleştirin `nuxt generate` :

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate",
    },
    ```

    Artık bu komutla birlikte, statik Web Apps, `build` her bir yürütmeyi her gönderişinizde betiği çalıştıracaktır.

1. Statik site oluştur:

    ```bash
    npm run build
    ```

    Nuxt.js statik siteyi oluşturacak ve çalışma dizininizin kökündeki bir _dağ_ klasörüne kopyalayacaktır.

    > [!NOTE]
    > Dağıtım sırasında CI/CD tarafından oluşturulması gerektiğinden, bu klasör _. gitignore_ dosyasında listelenir.

## <a name="push-your-static-website-to-github"></a>Statik Web sitenizi GitHub 'a gönderme

Azure statik Web Apps, uygulamanızı bir GitHub deposundan dağıtır ve belirli bir dala gönderilen her bir yürütmeye devam eder. Aşağıdaki komutları kullanarak yaptığınız değişiklikleri GitHub 'a eşitleyin.

1. Değiştirilen tüm dosyaları aşamalandırın:

    ```bash
    git add .
    ```

1. Tüm Değişiklikleri Kaydet

    ```bash
    git commit -m "Update build config"
    ```

1. Değişikliklerinizi GitHub 'a gönderin.

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>Statik Web sitenizi dağıtın

Aşağıdaki adımlarda, az önce GitHub 'a gönderdiğiniz uygulamayı Azure statik Web Apps bağlama gösterilmektedir. Azure 'da bir kez uygulamayı bir üretim ortamına dağıtabilirsiniz.

### <a name="create-an-azure-static-web-apps-preview-resource"></a>Azure statik Web Apps önizleme kaynağı oluşturma

1. [Azure Portal](https://portal.azure.com) gidin
1. **Kaynak oluştur** seçeneğine tıklayın
1. **Statik Web Uygulamaları** için arama yapın
1. **Statik Web Uygulamaları (Önizleme)** seçeneğine tıklayın
1. **Oluştur** seçeneğine tıklayın

1. *Abonelik* açılan listesinden bir abonelik seçin veya varsayılan değeri kullanın.
1. *Kaynak grubu açılır grubunun* altındaki **Yeni** bağlantıya tıklayın. *Yeni kaynak grubu adı*' nda **mystaticsite** yazın ve **Tamam** ' a tıklayın.
1. **Ad** metin kutusuna uygulamanız için genel olarak benzersiz bir ad sağlayın. Geçerli karakterler, `a-z` , `A-Z` `0-9` ve içerir `-` . Bu değer, statik uygulamanız için biçiminde URL öneki olarak kullanılır `https://<APP_NAME>.azurestaticapps.net` .
1. *Bölge* açılır penceresinde, size en yakın bölgeyi seçin.
1. SKU açılır listesinden **ücretsiz** ' ı seçin.

   :::image type="content" source="media/deploy-nuxtjs/create-static-web-app.png" alt-text="Statik web uygulaması oluşturma":::

### <a name="add-a-github-repository"></a>GitHub deposu ekleme

Yeni statik Web Apps hesabının, işleme otomatik olarak dağıtabilmesi için Nuxt.js uygulamanızla depoya erişmesi gerekir.

1. **GitHub Ile oturum aç düğmesine** tıklayın
1. GitHub Kullanıcı adınız olabilecek Nuxt.js projeniz için depoyu oluşturduğunuz **kuruluşu** seçin.
1. Daha önce oluşturduğunuz deponun adını bulun ve seçin.
1. *Dal* açılır listesinden dal olarak **ana** ' yı seçin.

   :::image type="content" source="media/deploy-nuxtjs/connect-github.png" alt-text="GitHub'ı bağlama":::

### <a name="configure-the-build-process"></a>Yapı işlemini yapılandırma

Azure statik Web Apps, NPM modüllerini yükleme ve her dağıtım sırasında çalıştırma gibi genel görevleri otomatik olarak yürütmek üzere oluşturulmuştur `npm run build` . Bununla birlikte, el ile yapılandırmanız gereken uygulama kaynak klasörü ve derleme hedef klasörü gibi birkaç ayar vardır.

1. Statik çıkış klasörünü yapılandırmak için **derleme** sekmesine tıklayın.

      :::image type="content" source="media/deploy-nuxtjs/build-tab.png" alt-text="Derleme sekmesi":::

1. *Uygulama yapıtı konumu* metin kutusuna **Dist** yazın.

### <a name="review-and-create"></a>Gözden geçir ve oluştur

1. Ayrıntıların tümünün doğru olduğunu doğrulamak için **gözden geçir + oluştur** düğmesine tıklayın.
1. Kaynak oluşturma Işlemini başlatmak için **Oluştur** ' a tıklayın ve ayrıca dağıtım Için bir GitHub eylemi sağlayın.
1. Dağıtım tamamlandıktan sonra **Kaynağa Git ' e** tıklayın.
1. _Genel bakış_ penceresinde, dağıtılan uygulamanızı açmak için *URL* bağlantısına tıklayın. 

Web sitesi hemen yük alıyorsa, arka plan GitHub eylemleri iş akışı çalışmaya devam eder. İş akışı tamamlandıktan sonra Web uygulamanızı görüntülemek için tarayıcıyı Yenile ' ye tıklayabilirsiniz.

Deponuzdaki eylemlere giderek eylemler iş akışlarının durumunu kontrol edebilirsiniz:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>Değişiklikleri Eşitle

Uygulamayı oluştururken Azure static Web Apps, deponuzda bir GitHub eylemleri iş akışı dosyası oluşturdu. Git geçmişinizin eşitlenmesi için bu dosyayı yerel deponuza getirmeniz gerekir.

Terminale dönün ve aşağıdaki komutu çalıştırın `git pull origin main` .

## <a name="configure-dynamic-routes"></a>Dinamik yolları yapılandırma

Yeni dağıtılan siteye gidin ve Framework veya kitaplık logolarının birine tıklayın. Ayrıntılar sayfası almak yerine 404 hata sayfasına sahip olursunuz.

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="Dinamik yollarla 404":::

Bunun nedeni, Nuxt.js statik site tarafından oluşturulmuştur, yalnızca giriş sayfası için. Nuxt.js `.html` her sayfa dosyası için eşdeğer statik dosyalar üretebilir `.vue` , ancak bir özel durum vardır. 

Sayfa dinamik bir sayfa ise, örneğin `_id.vue` , bu dinamik sayfadan statik BIR HTML oluşturmak için yeterli bilgi olmayacaktır. Dinamik yollar için olası yolları açıkça sağlamanız gerekir.

## <a name="generate-static-pages-from-dynamic-routes"></a>Dinamik yollardan statik sayfalar oluşturma

1. _nuxt.config.js_ dosyasını, Nuxt.js her bir çerçeve/kitaplık için statik sayfalar oluşturmak üzere kullanılabilir tüm verilerin bir listesini kullanacak şekilde güncelleştirin:

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` zaman uyumsuz bir işlevdir, bu nedenle bu işlevdeki bir API 'ye istek yapabilir ve yollar oluşturmak için döndürülen listeyi kullanabilirsiniz.

2. Yeni değişiklikleri GitHub deponuza gönderin ve GitHub eylemleri sitenizi yeniden oluşturup birkaç dakika bekleyin. Derleme tamamlandıktan sonra 404 hatası kaybolur.

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="Dinamik yollar üzerinde 404 düzeltildi":::

> [!div class="nextstepaction"]
> [Özel etki alanı ayarlama](custom-domain.md)
