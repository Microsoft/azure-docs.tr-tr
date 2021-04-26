---
title: 'Öğretici: bir Jekyll sitesini Azure statik Web Apps yayımlama'
description: Azure statik Web Apps bir Jekyıll uygulamasını dağıtmayı öğrenin.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: cshoe
ms.openlocfilehash: 8c6764ad5b63aa2fde07326ab986404ea4312316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99585185"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>Öğretici: bir Jekyll sitesini Azure statik Web Apps önizleme 'ye yayımlama

Bu makalede, bir [Jekyıll](https://jekyllrb.com/) Web uygulamasının nasıl oluşturulacağı ve [azure Azure statik Web Apps](overview.md)nasıl dağıtılacağı gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> - Jekyıll Web sitesi oluşturma
> - Azure statik Web Apps ayarlama
> - Jekyıll uygulamasını Azure 'a dağıtma

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

- [Jekyıll](https://jekyllrb.com/docs/installation/) 'yi yükler
  - Linux için Windows alt sistemini kullanabilir ve gerekirse Ubuntu yönergelerini izleyebilirsiniz.
- Etkin aboneliği olan bir Azure hesabı. Bir hesabınız yoksa, [ücretsiz olarak bir hesap oluşturabilirsiniz](https://azure.microsoft.com/free/).
- GitHub hesabı. Bir hesabınız yoksa, [ücretsiz olarak bir hesap oluşturabilirsiniz](https://github.com/join).

## <a name="create-jekyll-app"></a>Jekyıll uygulaması oluşturma

Jekyll komut satırı arabirimini (CLı) kullanarak bir Jekyll uygulaması oluşturun:

1. Terminalden yeni bir uygulama oluşturmak için Jekyıll CLı 'yi çalıştırın.

   ```bash
   jekyll new static-app
   ```

1. Yeni oluşturulan uygulamaya gidin.

   ```bash
   cd static-app
   ```

1. Yeni bir git deposu başlatın.

   ```bash
    git init
   ```

1. Değişiklikleri işleyin.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Uygulamanızı GitHub 'a gönderme

Azure statik Web Apps, Web sitenizi yayımlamak için GitHub 'ı kullanır. Aşağıdaki adımlarda bir GitHub deposunun nasıl oluşturulacağı gösterilmektedir.

1. [https://github.com/new](https://github.com/new) **Jekyıll-Azure-static** adlı dosyadan boş bir GITHUB deposu (Benioku dosyası oluşturmayın) oluşturun.

1. GitHub deposunu yerel depoya uzak olarak ekleyin. Aşağıdaki komutta yer tutucu yerine GitHub Kullanıcı adınızı eklediğinizden emin olun `<YOUR_USER_NAME>` .

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. Yerel depoyu GitHub 'a iletin.

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > Git dalınız farklı şekilde adlandırılmış olabilir `main` . `main`Bu komutta yerine doğru değeri koyun.

## <a name="deploy-your-web-app"></a>Web uygulamanızı dağıtın

Aşağıdaki adımlarda, yeni bir statik site uygulamasının nasıl oluşturulacağı ve bir üretim ortamına nasıl dağıtılacağı gösterilmektedir.

### <a name="create-the-application"></a>Uygulama oluşturma

1. [Azure Portal](https://portal.azure.com)gidin.

1. **Kaynak oluştur ' a** tıklayın.

1. **Statik Web Apps** arayın.

1. **Statik Web Apps (Önizleme)** seçeneğine tıklayın.

1. **Oluştur**’a tıklayın.

1. **Abonelik** için, listelenen aboneliği kabul edin veya açılan listeden yeni bir tane seçin.

1. _Kaynak grubu_' nda **Yeni**' yi seçin. _Yeni kaynak grubu adı_ alanına **jekyıll-static-App** girin ve **Tamam**' ı seçin.

1. Ardından, _ad_ kutusuna uygulamanız için bir ad girin. Geçerli karakterler `a-z` ,, `A-Z` ve `0-9` içerir `-` .

1. _Bölge_ için bir kullanılabilir bölge seçin.

1. _SKU_ için **ücretsiz**' i seçin.

    :::image type="content" source="./media/publish-jekyll/basic-app-details.png" alt-text="Ayrıntılar dolduruldu":::

1. **GitHub Ile oturum aç** düğmesine tıklayın.

1. Depoyu oluşturduğunuz **kuruluşu** seçin.

1. _Depo_ olarak **jekyıll-static-App** ' i seçin.

1. _Dal_ için **ana** öğesini seçin.

    :::image type="content" source="./media/publish-jekyll/completed-github-info.png" alt-text="Tamamlanan GitHub bilgileri":::

### <a name="build"></a>Derleme

Daha sonra, yapı işleminin uygulamanızı derlemek için kullandığı yapılandırma ayarlarını eklersiniz. Aşağıdaki ayarlar GitHub eylemi iş akışı dosyasını yapılandırır.

1. Derleme yapılandırmasını düzenlemek için **İleri: oluştur >** düğmesine tıklayın.

1. _Uygulama konumunu_ **/_Site** olarak ayarlayın.

1. _Uygulama yapıtı konumunu_ boş bırakın.

   Şu anda bir API dağıtmadığından, _API konumu_ için bir değer gerekli değildir.

### <a name="review-and-create"></a>Gözden geçir ve oluştur

1. Ayrıntıların tümünün doğru olduğunu doğrulamak için **gözden geçir + oluştur** düğmesine tıklayın.

1. Azure statik Web Apps oluşturulmasını başlatmak ve dağıtıma yönelik bir GitHub eylemi sağlamak için **Oluştur** ' a tıklayın.

1. İş akışı dosyasında bazı jeki 'ye özgü ayarlar gerektiğinden dağıtım ilk olarak başarısız olur. Bu ayarları eklemek için terminalinize gidin ve kayıt işlemini makinenize GitHub eylemiyle çekin.

   ```bash
   git pull
   ```

1. Jekyıll uygulamasını bir metin düzenleyicisinde açın ve _. GitHub/iş akışları/Azure-Pages-<WORKFLOW_NAME>. yıml_ dosyasını açın.

1. Satırı, `- name: Build And Deploy` aşağıdaki yapılandırma bloğunu eklemeden önce.

    ```yml
    - name: Set up Ruby
      uses: ruby/setup-ruby@v1.59.1
      with:
        ruby-version: 2.6
    - name: Install dependencies
      run: bundle install
    - name: Jekyll build
      run: jekyll build
    ```

1. Güncelleştirilmiş iş akışını işleyin ve GitHub 'a gönderin.

    ```bash
    git add -A
    git commit -m "Updating GitHub Actions workflow"
    git push
    ```

1. GitHub eyleminin tamamlanmasını bekleyin.

1. Azure portal _genel bakış_ penceresinde, dağıtılan uygulamanızı açmak için _URL_ bağlantısına tıklayın.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="Dağıtılan uygulama":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel etki alanı ekleme](custom-domain.md)
