---
title: Facebook kimlik doğrulamasını yapılandırma
description: Facebook kimlik doğrulamasını App Service veya Azure Işlevleri uygulamanız için bir kimlik sağlayıcısı olarak yapılandırmayı öğrenin.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: bc639eaea76b3309d6ed047e73c726040da19639
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078018"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>App Service veya Azure Işlevleri uygulamanızı Facebook oturum açma bilgilerini kullanacak şekilde yapılandırma

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Bu makalede, bir kimlik doğrulama sağlayıcısı olarak Facebook kullanmak üzere Azure App Service veya Azure Işlevlerinin nasıl yapılandırılacağı gösterilmektedir.

Bu makaledeki yordamı tamamlayabilmeniz için, doğrulanmış bir e-posta adresine ve cep telefonu numarasına sahip bir Facebook hesabınızın olması gerekir. Yeni bir Facebook hesabı oluşturmak için [Facebook.com]adresine gidin.

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Uygulamanızı Facebook ile kaydetme

1. [Facebook geliştiricileri] Web sitesine gidin ve Facebook hesabı kimlik bilgilerinizle oturum açın.

   Geliştiriciler için Facebook hesabınız yoksa, **kullanmaya** başlayın ' ı seçin ve kayıt adımlarını izleyin.
1. **Uygulamalarım**  >  **Yeni uygulama ekle**' yi seçin.
1. **Görünen ad** alanında:
   1. Uygulamanız için benzersiz bir ad yazın.
   1. **Iletişim e-postanızı** girin.
   1. **Uygulama kimliği oluştur**' u seçin.
   1. Güvenlik denetimini doldurun.

   Yeni Facebook uygulamanız için geliştirici panosu açılır.
1. **Pano**  >  **Facebook oturum açma**  >  **kurulumu**  >  **Web**'i seçin.
1. **Facebook oturum açma** altındaki sol gezinti bölmesinde **Ayarlar**' ı seçin.
1. **Geçerli OAuth yeniden yönlendirme URI 'leri** alanında, girin `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` . `<app-name>`Azure App Service uygulamanızın adıyla değiştirmeyi unutmayın.
1. **Değişiklikleri Kaydet**' i seçin.
1. Sol bölmede **Ayarlar**  >  **temel**' yı seçin. 
1. **Uygulama gizli** alanından **göster**' i seçin. **Uygulama kimliği** ve **uygulama gizli anahtarı** değerlerini kopyalayın. Azure 'da App Service uygulamanızı yapılandırmak için bunları daha sonra kullanırsınız.

   > [!IMPORTANT]
   > Uygulama gizli anahtarı önemli bir güvenlik kimlik bilgileridir. Bu parolayı kimseyle paylaşmayın veya bir istemci uygulaması içinde dağıtmayın.
   >

1. Uygulamayı kaydetmek için kullandığınız Facebook hesabı, uygulamanın bir yöneticisidir. Bu noktada, yalnızca Yöneticiler bu uygulamada oturum açabilir.

   Diğer Facebook hesaplarının kimliğini doğrulamak için, **uygulama gözden geçirmesi** ' nı seçin ve genel kullanıma **\<your-app-name> Açık yap** ' ı seçerek Facebook kimlik doğrulamasını kullanarak uygulamaya erişim sağlayabilirsiniz.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Uygulamanıza Facebook bilgilerini ekleyin

1. [Azure Portal] oturum açın ve uygulamanıza gidin.
1. Soldaki menüden **kimlik doğrulaması** ' nı seçin. **Kimlik sağlayıcısı ekle**' ye tıklayın.
1. Kimlik sağlayıcısı açılan listesinde **Facebook** ' u seçin. Daha önce edindiğiniz uygulama KIMLIĞI ve uygulama gizli anahtarı değerlerini yapıştırın.

    Gizli anahtar, adlı bir yuva yapışkan [uygulama ayarı](./configure-common.md#configure-app-settings) olarak depolanır `FACEBOOK_PROVIDER_AUTHENTICATION_SECRET` . Azure Key Vault gizli anahtarı yönetmek istiyorsanız bu ayarı daha sonra [Key Vault başvurularını](./app-service-key-vault-references.md) kullanacak şekilde güncelleştirebilirsiniz.

1. Uygulama için yapılandırılan ilk kimlik sağlayıcısıysanız, **App Service kimlik doğrulama ayarları** bölümü de istenir. Aksi halde, bir sonraki adıma geçebilirsiniz.
    
    Bu seçenekler, uygulamanızın kimliği doğrulanmamış isteklere nasıl yanıt vereceğini ve varsayılan seçimlerin bu yeni sağlayıcı ile oturum açmak için tüm istekleri yeniden yönlenceğini belirtir. Bu davranışı şimdi özelleştirmeyi, **kimlik doğrulama ayarları**' nın yanındaki **Düzenle** ' ye tıklayarak bu ayarları daha sonra ana **kimlik doğrulama** ekranından ayarlayabilirsiniz. Bu seçenekler hakkında daha fazla bilgi edinmek için bkz. [kimlik doğrulama akışı](overview-authentication-authorization.md#authentication-flow).

1. Seçim **İleri: kapsamlar** ' a tıklayın ve uygulama için gereken kapsamları ekleyin. Bu işlem, tarayıcı tabanlı akışlar için oturum açma zamanında istenir.
1. **Ekle**'ye tıklayın.

Artık uygulamanızda kimlik doğrulaması için Facebook kullanmaya hazırsınız. Sağlayıcı, **kimlik doğrulama** ekranında listelenecektir. Buradan, bu sağlayıcı yapılandırmasını düzenleyebilir veya silebilirsiniz.

## <a name="next-steps"></a><a name="related-content"> </a>Sonraki adımlar

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->
[Facebook geliştiricileri]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure portalı]: https://portal.azure.com/
