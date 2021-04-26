---
title: 'Öğretici: Azure statik Web Apps bir VuePress sitesi yayımlama'
description: Bu öğreticide, Azure statik Web Apps bir VuePress uygulamasının nasıl dağıtılacağı gösterilmektedir.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 6f0616df885a7f8fcd76337c810bc368aa02f3c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650460"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>Öğretici: Azure statik Web Apps önizleme 'ye VuePress sitesi yayımlama

Bu makalede, [Azure Azure statik Web Apps](overview.md)bir [vuepress](https://vuepress.vuejs.org/) Web uygulaması oluşturma ve dağıtma işlemlerinin nasıl yapılacağı gösterilir. Nihai sonuç, uygulamanın nasıl oluşturulduğunu ve yayımlanabileceğini denetlemenize olanak tanıyan, ilişkili GitHub eylemlerine sahip yeni bir Azure statik Web Apps uygulamasıdır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> - VuePress uygulaması oluşturma
> - Azure statik Web Apps ayarlama
> - VuePress uygulamasını Azure 'a dağıtma

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. Bir hesabınız yoksa, [ücretsiz olarak bir hesap oluşturabilirsiniz](https://azure.microsoft.com/free/).
- GitHub hesabı. Bir hesabınız yoksa, [ücretsiz olarak bir hesap oluşturabilirsiniz](https://github.com/join).
- [Node.js](https://nodejs.org) yüklendi.

## <a name="create-a-vuepress-app"></a>VuePress uygulaması oluşturma

Komut satırı arabiriminden (CLı) bir VuePress uygulaması oluşturun:

1. VuePress uygulaması için yeni bir klasör oluşturun.

   ```bash
   mkdir static-site
   ```

1. Klasöre bir _README.MD_ dosyası ekleyin.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. Dosyadaki _package.js_ başlatın.

   ```bash
   npm init -y
   ```

1. Olarak VuePress ekleyin `devDependency` .

   ```bash
   npm install --save-dev vuepress
   ```

1. Dosyayı bir metin düzenleyicisinde _package.js_ açın ve bölümüne bir yapı komutu ekleyin [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html) .

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. _Düğüm \_ modülleri_ klasörünü hariç tutmak için bir _. gitignore_ dosyası oluşturun.

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Git deposu başlatın.

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Uygulamanızı GitHub 'a gönderme

Azure statik Web Apps bağlanmak için GitHub 'da bir depoya ihtiyacınız vardır. Aşağıdaki adımlarda siteniz için nasıl bir depo oluşturacağınız gösterilmektedir.

1. Boş bir GitHub deposu (BENIOKU oluşturma) [https://github.com/new](https://github.com/new) adlı **vuepress-static-App** öğesinden oluşturun.

1. GitHub deposunu yerel depoya uzak olarak ekleyin. Aşağıdaki komutta yer tutucu yerine GitHub Kullanıcı adınızı eklediğinizden emin olun `<YOUR_USER_NAME>` .

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. Yerel depoyu GitHub 'a iletin.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Web uygulamanızı dağıtın

Aşağıdaki adımlarda, yeni bir statik Web Apps uygulamasının nasıl oluşturulacağı ve bir üretim ortamına nasıl dağıtılacağı gösterilmektedir.

### <a name="create-the-application"></a>Uygulama oluşturma

1. [Azure Portal](https://portal.azure.com) gidin
1. **Kaynak oluştur** seçeneğine tıklayın
1. **Statik Web Uygulamaları** için arama yapın
1. **Statik Web Uygulamaları (Önizleme)** seçeneğine tıklayın
1. **Oluştur** seçeneğine tıklayın

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="Portalda statik bir Web Apps (Önizleme) oluşturma":::

1. **Abonelik** için, listelenen aboneliği kabul edin veya açılan listeden yeni bir tane seçin.

1. _Kaynak grubu_' nda **Yeni**' yi seçin. _Yeni kaynak grubu adı_ alanına **vuepress-static-App** girin ve **Tamam**' ı seçin.

1. Ardından, **ad** kutusunda uygulamanız için bir ad. Geçerli karakterler `a-z` ,, `A-Z` ve `0-9` içerir `-` .

1. _Bölge_ için bir kullanılabilir bölge seçin.

1. _SKU_ için **ücretsiz**' i seçin.

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="Ayrıntılar dolduruldu":::

1. **GitHub Ile oturum aç** düğmesine tıklayın.

1. Depoyu oluşturduğunuz **kuruluşu** seçin.

1. _Depo_ olarak **vuepress-static-App** ' i seçin.

1. _Dal_ için **ana** öğesini seçin.

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="Tamamlanan GitHub bilgileri":::

### <a name="build"></a>Derleme

Daha sonra, yapı işleminin uygulamanızı derlemek için kullandığı yapılandırma ayarlarını eklersiniz. Aşağıdaki ayarlar GitHub eylemi iş akışı dosyasını yapılandırır.

1. Derleme yapılandırmasını düzenlemek için **İleri: Derleme >** düğmesine tıklayın

1. _Uygulama konumunu_ olarak ayarlayın **/** .

1. _Uygulama yapıtı konumunu_ **. vuepress/Dist** olarak ayarlayın.

Şu anda bir API dağıtmadığından, _API konumu_ için bir değer gerekli değildir.

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="Derleme ayarları":::

### <a name="review-and-create"></a>Gözden geçir ve oluştur

1. Ayrıntıların tümünün doğru olduğunu doğrulamak için **gözden geçir + oluştur** düğmesine tıklayın.

1. Azure statik Web Apps oluşturulmasını başlatmak ve dağıtıma yönelik bir GitHub eylemi sağlamak için **Oluştur** ' a tıklayın.

1. Dağıtım tamamlandıktan sonra, kaynak ' a **gidin**.

1. Kaynak ekranında, dağıtılan uygulamanızı açmak için _URL_ bağlantısına tıklayın. GitHub eyleminin tamamlanabilmesi için bir dakika veya iki dakika beklemeniz gerekebilir.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="Dağıtılan uygulama":::

### <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel etki alanı ekleme](custom-domain.md)
