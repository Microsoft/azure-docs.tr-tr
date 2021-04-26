---
title: 'Hızlı başlangıç: Azure portal kullanarak Azure statik Web Apps ile ilk statik Web uygulamanızı oluşturma'
description: Azure portal ile Azure statik Web Apps statik bir site dağıtmayı öğrenin.
services: static-web-apps
author: craigshoemaker
ms.author: cshoe
ms.date: 09/03/2020
ms.topic: quickstart
ms.service: static-web-apps
ms.custom:
- mode-portal
ms.openlocfilehash: 78af2290a2e71d349303d3913f8a40510eb9c6a0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531581"
---
# <a name="quickstart-building-your-first-static-site-in-the-azure-portal"></a>Hızlı başlangıç: Azure portal ilk statik sitenizi oluşturma

Azure statik Web Apps bir GitHub deposundan uygulama oluşturarak bir Web sitesini üretim ortamına yayınlar. Bu hızlı başlangıçta, Azure portal kullanarak bir Web uygulamasını Azure statik Web uygulamalarına dağıtırsınız.

Azure aboneliğiniz yoksa [ücretsiz bir deneme hesabı oluşturun](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Önkoşullar

- [GitHub](https://github.com) hesabı
- [Azure](https://portal.azure.com) hesabı

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Statik web uygulaması oluşturma

Artık depo oluşturulduktan sonra, Azure portal statik bir Web uygulaması oluşturabilirsiniz.

1. [Azure portalına](https://portal.azure.com) gidin
1. **Kaynak oluştur** ' u seçin
1. **Statik Web Uygulamaları** için arama yapın
1. **Statik Web Apps seçin (Önizleme)**
1. **Oluştur**’u seçin

_Temel bilgiler_ bölümünde yeni uygulamanızı yapılandırıp bir GitHub deposuna bağlayarak başlayın.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Temel bilgiler bölümü":::

1. _Azure aboneliğinizi_ seçin
1. Yeni bir _kaynak grubu_ seçin veya oluşturun
1. Uygulamanın **-First-static-Web-App**' i adlandırın.
      1. Geçerli karakterler şunlardır: `a-z` (büyük/küçük harf duyarsız), `0-9` ve `-`.
1. Size en yakın _bölgeyi_ seçin
1. **Ücretsiz** _SKU 'yu_ seçin
1. GitHub ile **oturum açma** seçeneğini belirleyin ve GitHub ile kimlik doğrulaması yapın

GitHub ile oturum açtıktan sonra depo bilgilerini girin.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Depo ayrıntıları":::

1. Tercih ettiğiniz _kuruluşu_ seçin
1. _Depo_ açılır listesinden **My-First-Web-static-App** ' i seçin
1. _Dal_ açılan listesinden **Main** ' i seçin

> [!NOTE]
> Herhangi bir depo görmüyorsanız, GitHub 'da Azure statik Web Apps yetkilendirmeniz gerekebilir. GitHub deponuza gidin ve **yetkili OAuth uygulamaları > ayarlar > uygulamalar**' a gidip **Azure statik Web Apps**' yı seçin ve ardından **ver**' i seçin. Kuruluş depolarında, izinleri vermek için kuruluşun sahibi olmanız gerekir.

1. _Yapı ayrıntıları_ bölümünde, tercih ettiğiniz ön uç çerçevesine özgü yapılandırma ayrıntılarını ekleyin.

    # <a name="no-framework"></a>[Çerçeve yok](#tab/vanilla-javascript)

    1. _Derleme ön ayarları_ açılır listesinden **özel** ' i seçin
    1. Varsayılan değeri _uygulama konumu_ kutusunda tut
    1. _API konum_ kutusundan varsayılan değeri temizle
    1. _Uygulama yapıtı konum_ kutusunu boş bırakın

    # <a name="angular"></a>[Angular](#tab/angular)

    1. _Derleme ön ayarları_ açılır listesinden **angular** seçin
    1. Varsayılan değeri _uygulama konumu_ kutusunda tut
    1. _API konum_ kutusundan varsayılan değeri temizle
    1. _Uygulama yapıt konumu_ kutusuna **Dist/angular-temel** yazın

    # <a name="react"></a>[React](#tab/react)

    1. _Derleme ön ayarları_ açılır listesinden **tepki** ' ı seçin
    1. Varsayılan değeri _uygulama konumu_ kutusunda tut
    1. _API konum_ kutusundan varsayılan değeri temizle
    1. _Uygulama yapıtı konum_ kutusunda **Oluştur** yazın

    # <a name="vue"></a>[Vue](#tab/vue)

    1. _Derleme ön ayarları_ açılır listesinden **Vue.js** seçin
    1. Varsayılan değeri _uygulama konumu_ kutusunda tut
    1. _API konum_ kutusundan varsayılan değeri temizle
    1. Varsayılan değeri _uygulama yapıtı konum_ kutusunda tut

    ---

1. **Gözden geçir ve oluştur**’u seçin.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="İnceleme oluştur düğmesi":::

    > [!NOTE]
    > Uygulamayı oluşturduktan sonra bu değerleri değiştirmek için [iş akışı dosyasını](github-actions-workflow.md) düzenleyebilirsiniz.

1. **Oluştur**’u seçin.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Oluştur düğmesi":::

1. **Kaynağa git**’i seçin.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Kaynağa git düğmesi":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekinizde, Azure statik Web Apps örneğini aşağıdaki adımlarla silebilirsiniz:

1. [Azure portalı](https://portal.azure.com) açın
1. En üst arama çubuğundan **My-First-Web-static-App** araması yapın
1. Uygulama adını seçin
1. **Sil** düğmesini seçin
1. Silme eylemini onaylamak için **Evet** ' i seçin (işlemin tamamlanması birkaç dakika sürebilir)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [API ekleme](add-api.md)
