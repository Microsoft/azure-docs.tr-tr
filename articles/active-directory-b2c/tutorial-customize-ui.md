---
title: Öğretici-kullanıcı deneyimleri arabirimini özelleştirme-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C Azure portal kullanarak uygulamalarınızın Kullanıcı arabirimini özelleştirmeyi öğrenin.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 08edf6e841dc7d389573d5e5b5ea7e043f750e76
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71291102"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C Kullanıcı deneyimlerinin arabirimini özelleştirme

Kaydolma, oturum açma ve profil düzenlemesi gibi daha yaygın kullanıcı deneyimleri için [Kullanıcı akışlarını](active-directory-b2c-reference-policies.md) Azure Active Directory B2C (Azure AD B2C) kullanabilirsiniz. Bu öğreticideki bilgiler, kendi HTML ve CSS dosyalarınızı kullanarak bu deneyimlerin [Kullanıcı arabirimini (UI)](customize-ui-overview.md) nasıl özelleştireceğinizi öğrenmenize yardımcı olur.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * UI özelleştirme dosyaları oluştur
> * Dosyaları kullanmak için kullanıcı akışını güncelleştirme
> * Özelleştirilmiş kullanıcı arabirimini test etme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Kullanıcıların uygulamanızda kaydolup oturum açmasını sağlamak için [bir Kullanıcı akışı oluşturun](tutorial-create-user-flows.md) .

## <a name="create-customization-files"></a>Özelleştirme dosyaları oluştur

Bir Azure depolama hesabı ve kapsayıcısı oluşturup, temel HTML ve CSS dosyalarını kapsayıcıya yerleştirebilirsiniz.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Dosyalarınızı birçok şekilde depolayabilmenize karşın, bu öğreticide bunları [Azure Blob depolama](../storage/blobs/storage-blobs-introduction.md)alanında depoaktarabilirsiniz.

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. Azure aboneliğinizi içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve aboneliğinizi içeren dizini seçin. Bu dizin, Azure B2C kiracınızı içeren olandan farklı.
3. Azure portal sol üst köşesindeki tüm hizmetler ' i seçin, **depolama hesapları**' nı arayıp seçin.
4. **Add (Ekle)** seçeneğini belirleyin.
5. **Kaynak grubu**altında **Yeni oluştur**' u seçin, yeni kaynak grubu için bir ad girin ve ardından **Tamam**' a tıklayın.
6. Depolama hesabı için bir ad girin. Seçtiğiniz ad Azure’da benzersiz olmalı, uzunluğu 3 ile 24 karakter arasında olmalı ve yalnızca sayı ile küçük harf içermelidir.
7. Depolama hesabının konumunu seçin veya varsayılan konumu kabul edin.
8. Diğer tüm varsayılan değerleri kabul edin, **gözden geçir + oluştur**' u seçin ve ardından **Oluştur**' a tıklayın.
9. Depolama hesabı oluşturulduktan sonra **Kaynağa Git**' i seçin.

### <a name="create-a-container"></a>Bir kapsayıcı oluşturma

1. Depolama hesabının genel bakış sayfasında, Bloblar ' ıseçin.
2. **Kapsayıcı**' yı seçin, kapsayıcı için bir ad girin, blob ' u seçin **(yalnızca blob 'lar için anonim okuma erişimi)** ve ardından **Tamam**' a tıklayın.

### <a name="enable-cors"></a>CORS'yi etkinleştirme

 Bir tarayıcıda Azure AD B2C kod, Kullanıcı akışında belirttiğiniz bir URL 'den özel içerik yüklemek için modern ve standart bir yaklaşım kullanır. Çıkış noktaları arası kaynak paylaşımı (CORS), bir Web sayfasındaki kısıtlanmış kaynakların diğer etki alanlarından istenmesinin yapılmasına izin verir.

1. Menüsünde **CORS**' yi seçin.
2. **Izin verilen çıkış noktaları**için `https://your-tenant-name.b2clogin.com`girin. Değiştirin `your-tenant-name` Azure AD B2C kiracınızın adı. Örneğin, `https://fabrikam.b2clogin.com`. Kiracı adınızı girerken tüm küçük harfleri kullanmanız gerekir.
3. **İzin verilen Yöntemler**için, `GET``PUT`ve `OPTIONS`seçin.
4. **Izin verilen üst bilgiler**için bir yıldız işareti (*) girin.
5. **Gösterilen üstbilgiler**için bir yıldız işareti (*) girin.
6. **Maksimum yaş**için 200 girin.

    ![Azure Blob depolama 'daki CORS yapılandırma sayfası Azure portal](./media/tutorial-customize-ui/enable-cors.png)

5. **Kaydet**’e tıklayın.

### <a name="create-the-customization-files"></a>Özelleştirme dosyalarını oluşturma

Kaydolma deneyiminin Kullanıcı arabirimini özelleştirmek için, basit bir HTML ve CSS dosyası oluşturarak başlayın. HTML 'nizi istediğiniz şekilde yapılandırabilirsiniz, ancak tanımlayıcı `api`içeren bir **div** öğesi olmalıdır. Örneğin, `<div id="api"></div>`. Sayfa görüntülenirken öğeleri `api` kapsayıcıya Azure AD B2C çıkartır.

1. Yerel bir klasörde aşağıdaki dosyayı oluşturun ve depolama hesabının adına ve `your-storage-account` `your-container` oluşturduğunuz kapsayıcının adına değişiklik yaptığınızdan emin olun. Örneğin, `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    Sayfa istediğiniz şekilde tasarlanabilir, ancak oluşturduğunuz herhangi bir HTML özelleştirme dosyası için **API** div öğesi gereklidir.

3. Dosyayı *Custom-ui. html*olarak kaydedin.
4. Kaydolma veya oturum açma sayfasındaki tüm öğeleri, satır içine alan Azure AD B2C öğeler de dahil olmak üzere, aşağıdaki basit CSS 'yi oluşturun.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Dosyayı *Style. css*olarak kaydedin.

### <a name="upload-the-customization-files"></a>Özelleştirme dosyalarını karşıya yükle

Bu öğreticide, Azure AD B2C, depolama hesabında oluşturduğunuz dosyaları depoladığınızda, bu sayede bunlara erişebilir.

1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin, **depolama hesapları**' nı arayıp seçin.
2. Oluşturduğunuz depolama hesabını seçin, Bloblar 'ı seçin ve ardından oluşturduğunuz kapsayıcıyı seçin.
3. **Karşıya yükle**' yi seçin, *Custom-ui. html* dosyasını seçin ve ardından **karşıya yükle**' ye tıklayın.

    ![Karşıya yükleme düğmesi ve vurgulanan dosyalar için portalda blob sayfasını karşıya yükle](./media/tutorial-customize-ui/upload-blob.png)

4. Daha sonra öğreticide kullanmak üzere karşıya yüklediğiniz dosyanın URL 'sini kopyalayın.
5. *Style. css* dosyası için 3. ve 4. adımı yineleyin.

## <a name="update-the-user-flow"></a>Kullanıcı akışını güncelleştirme

1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
2. **Kullanıcı akışları ' nı (ilkeler)** seçin ve ardından *B2C_1_signupsignin1* Kullanıcı akışını seçin.
3. **Sayfa düzenlerini**seçin ve ardından **Birleşik kaydolma veya oturum açma sayfası**altında, **özel sayfa içeriğini kullanmak**için **Evet** ' i tıklatın.
4. **Özel sayfa URI 'si**içinde, daha önce kaydettiğiniz *Custom-ui. html* dosyasının URI 'sini girin.
5. Sayfanın en üstünde **Kaydet**' i seçin.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Azure AD B2C kiracınızda **Kullanıcı akışları** ' nı seçin ve *B2C_1_signupsignin1* Kullanıcı akışı ' nı seçin.
2. Sayfanın üst kısmında, **Kullanıcı akışını Çalıştır**' a tıklayın.
3. **Kullanıcı akışını Çalıştır** düğmesine tıklayın.

    ![Kaydolma veya oturum açma Kullanıcı akışı için Kullanıcı akış sayfasını Çalıştır](./media/tutorial-customize-ui/run-user-flow.png)

    Aşağıdaki örneğe benzer bir sayfa, oluşturduğunuz CSS dosyasına göre ortalanan öğeleri görmeniz gerekir:

    ![Özel UI öğeleriyle kaydolma veya oturum açma sayfasını gösteren Web tarayıcısı](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, şu şekilde nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * UI özelleştirme dosyaları oluştur
> * Dosyaları kullanmak için kullanıcı akışını güncelleştirme
> * Özelleştirilmiş kullanıcı arabirimini test etme

> [!div class="nextstepaction"]
> [Azure Active Directory B2C dil özelleştirmesi](active-directory-b2c-reference-language-customization.md)
