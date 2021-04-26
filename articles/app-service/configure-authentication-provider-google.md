---
title: Google kimlik doğrulamasını yapılandırma
description: Google kimlik doğrulamasını App Service veya Azure Işlevleri uygulamanız için bir kimlik sağlayıcısı olarak yapılandırmayı öğrenin.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: f6bec32fa928e840569ed95c35a056db91ea9737
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078001"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>App Service veya Azure Işlevleri uygulamanızı Google oturum açma bilgilerini kullanacak şekilde yapılandırma

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Bu konu, bir kimlik doğrulama sağlayıcısı olarak Google 'ı kullanmak üzere Azure App Service veya Azure Işlevlerinin nasıl yapılandırılacağını gösterir.

Bu konudaki yordamı tamamlayabilmeniz için, doğrulanmış bir e-posta adresine sahip bir Google hesabınızın olması gerekir. Yeni bir Google hesabı oluşturmak için [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) adresine gidin.

## <a name="register-your-application-with-google"></a><a name="register"> </a>Uygulamanızı Google ile kaydedin

1. İstemci KIMLIĞI ve istemci parolası oluşturmak için, [google Sign-In 'teki sunucu tarafı uygulamalar Için](https://developers.google.com/identity/sign-in/web/server-side-flow) Google belgelerini izleyin. Herhangi bir kod değişikliği yapmanız gerekmez. Yalnızca aşağıdaki bilgileri kullanın:
    - **Yetkili JavaScript kaynakları** için ' `https://<app-name>.azurewebsites.net` de uygulamanızın adıyla birlikte kullanın *\<app-name>* .
    - **Yetkili yeniden yönlendirme URI 'si** için kullanın `https://<app-name>.azurewebsites.net/.auth/login/google/callback` .
1. Uygulama KIMLIĞI ve uygulama gizli anahtarı değerlerini kopyalayın.

    > [!IMPORTANT]
    > Uygulama gizli anahtarı önemli bir güvenlik kimlik bilgileridir. Bu parolayı kimseyle paylaşmayın veya bir istemci uygulaması içinde dağıtmayın.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Uygulamanıza Google bilgilerini ekleyin

1. [Azure Portal] oturum açın ve uygulamanıza gidin.
1. Soldaki menüden **kimlik doğrulaması** ' nı seçin. **Kimlik sağlayıcısı ekle**' ye tıklayın.
1. Kimlik sağlayıcısı açılan listesinde **Google** ' ı seçin. Daha önce edindiğiniz uygulama KIMLIĞI ve uygulama gizli anahtarı değerlerini yapıştırın.

    Gizli anahtar, adlı bir yuva yapışkan [uygulama ayarı](./configure-common.md#configure-app-settings) olarak depolanır `GOOGLE_PROVIDER_AUTHENTICATION_SECRET` . Azure Key Vault gizli anahtarı yönetmek istiyorsanız bu ayarı daha sonra [Key Vault başvurularını](./app-service-key-vault-references.md) kullanacak şekilde güncelleştirebilirsiniz.

1. Uygulama için yapılandırılan ilk kimlik sağlayıcısıysanız, **App Service kimlik doğrulama ayarları** bölümü de istenir. Aksi halde, bir sonraki adıma geçebilirsiniz.
    
    Bu seçenekler, uygulamanızın kimliği doğrulanmamış isteklere nasıl yanıt vereceğini ve varsayılan seçimlerin bu yeni sağlayıcı ile oturum açmak için tüm istekleri yeniden yönlenceğini belirtir. Bu davranışı şimdi özelleştirmeyi, **kimlik doğrulama ayarları**' nın yanındaki **Düzenle** ' ye tıklayarak bu ayarları daha sonra ana **kimlik doğrulama** ekranından ayarlayabilirsiniz. Bu seçenekler hakkında daha fazla bilgi edinmek için bkz. [kimlik doğrulama akışı](overview-authentication-authorization.md#authentication-flow).

1. Seçim **İleri: kapsamlar** ' a tıklayın ve uygulama için gereken kapsamları ekleyin. Bu işlem, tarayıcı tabanlı akışlar için oturum açma zamanında istenir.
1. **Ekle**'ye tıklayın.

Artık uygulamanızda kimlik doğrulaması için Google 'ı kullanmaya hazırsınız. Sağlayıcı, **kimlik doğrulama** ekranında listelenecektir. Buradan, bu sağlayıcı yapılandırmasını düzenleyebilir veya silebilirsiniz.

## <a name="next-steps"></a><a name="related-content"> </a>Sonraki adımlar

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure portalı]: https://portal.azure.com/

