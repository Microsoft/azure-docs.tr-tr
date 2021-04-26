---
title: Twitter kimlik doğrulamasını yapılandırma
description: Twitter kimlik doğrulamasını App Service veya Azure Işlevleri uygulamanız için bir kimlik sağlayıcısı olarak yapılandırmayı öğrenin.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6ecce954991d9f3901c54a6f87fc803b32469862
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077984"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>App Service veya Azure Işlevleri uygulamanızı Twitter oturum açma bilgilerini kullanacak şekilde yapılandırma

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Bu makalede, bir kimlik doğrulama sağlayıcısı olarak Twitter kullanmak üzere Azure App Service veya Azure Işlevlerinin nasıl yapılandırılacağı gösterilmektedir.

Bu makaledeki yordamı tamamlayabilmeniz için, doğrulanmış bir e-posta adresi ve telefon numarası olan bir Twitter hesabına ihtiyacınız vardır. Yeni bir Twitter hesabı oluşturmak için [Twitter.com]adresine gidin.

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Uygulamanızı Twitter 'a kaydetme

1. [Azure Portal] oturum açın ve uygulamanıza gidin. **URL**'nizi kopyalayın. Twitter uygulamanızı yapılandırmak için kullanacaksınız.
1. [Twitter geliştiricileri] Web sitesine gidin, Twitter hesabı kimlik bilgilerinizle oturum açın ve **uygulama oluştur**' u seçin.
1. Yeni uygulamanız için **uygulama adı** ve **uygulama açıklaması** girin. Uygulamanızın **URL** 'Sini **Web sitesi URL 'si** alanına yapıştırın. **Geri arama URL 'leri** bölümünde app SERVICE uygulamanızın https URL 'sini girin ve yolu ekleyin `/.auth/login/twitter/callback` . Örneğin, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. Sayfanın alt kısmındaki en az 100 karakter yazarak **Bu uygulamanın nasıl kullanılacağını bize söyleyin**, sonra **Oluştur**' u seçin. Açılır pencerede yeniden **Oluştur** ' a tıklayın. Uygulama ayrıntıları görüntülenir.
1. **Anahtarlar ve erişim belirteçleri** sekmesini seçin.

   Şu değerleri bir yere göz önünde oluşturun:
   - API anahtarı
   - API gizli anahtarı

   > [!IMPORTANT]
   > API gizli anahtarı önemli bir güvenlik kimlik bilgileridir. Bu parolayı herkesle paylaşmayın veya uygulamanızla dağıtın.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Uygulamanıza Twitter bilgilerini ekleyin

1. [Azure Portal] oturum açın ve uygulamanıza gidin.
1. Soldaki menüden **kimlik doğrulaması** ' nı seçin. **Kimlik sağlayıcısı ekle**' ye tıklayın.
1. Kimlik sağlayıcısı açılan menüsünde **Twitter** ' ı seçin. `API key` `API secret key` Daha önce edindiğiniz ve değerlerini yapıştırın.

    Gizli anahtar, adlı bir yuva yapışkan [uygulama ayarı](./configure-common.md#configure-app-settings) olarak depolanır `TWITTER_PROVIDER_AUTHENTICATION_SECRET` . Azure Key Vault gizli anahtarı yönetmek istiyorsanız bu ayarı daha sonra [Key Vault başvurularını](./app-service-key-vault-references.md) kullanacak şekilde güncelleştirebilirsiniz.

1. Uygulama için yapılandırılan ilk kimlik sağlayıcısıysanız, **App Service kimlik doğrulama ayarları** bölümü de istenir. Aksi halde, bir sonraki adıma geçebilirsiniz.
    
    Bu seçenekler, uygulamanızın kimliği doğrulanmamış isteklere nasıl yanıt vereceğini ve varsayılan seçimlerin bu yeni sağlayıcı ile oturum açmak için tüm istekleri yeniden yönlenceğini belirtir. Bu davranışı şimdi özelleştirmeyi, **kimlik doğrulama ayarları**' nın yanındaki **Düzenle** ' ye tıklayarak bu ayarları daha sonra ana **kimlik doğrulama** ekranından ayarlayabilirsiniz. Bu seçenekler hakkında daha fazla bilgi edinmek için bkz. [kimlik doğrulama akışı](overview-authentication-authorization.md#authentication-flow).

1. **Ekle**'ye tıklayın.

Artık uygulamanızda kimlik doğrulaması için Twitter kullanmaya hazırsınız. Sağlayıcı, **kimlik doğrulama** ekranında listelenecektir. Buradan, bu sağlayıcı yapılandırmasını düzenleyebilir veya silebilirsiniz.

## <a name="next-steps"></a><a name="related-content"> </a>Sonraki adımlar

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Twitter geliştiricileri]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure portalı]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
