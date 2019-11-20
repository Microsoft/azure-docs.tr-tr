---
title: Microsoft hesabı kimlik doğrulamasını Yapılandırma-Azure App Service
description: App Service uygulamanız için Microsoft hesabı kimlik doğrulamasını yapılandırma hakkında bilgi edinin.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 70af534e6bcd0039dbc602a5ebc3fc35fb145e79
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176945"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>App Service uygulamanızı Microsoft hesabı oturum açma bilgilerini kullanacak şekilde yapılandırma

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Bu konuda, Azure App Service bir kimlik doğrulama sağlayıcısı olarak Microsoft hesabı kullanmak üzere nasıl yapılandırılacağı gösterilmektedir. 

## <a name="register-microsoft-account"> </a>Uygulamanızı Microsoft hesabı ile kaydedin

1. Azure portal [**uygulama kayıtları**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) gidin. Gerekirse Microsoft hesabı oturum açın.
1. **Yeni kayıt**' ı seçin ve ardından bir uygulama adı girin.
1. **Yeniden yönlendirme URI 'Lerinde** **Web**' i seçin ve ardından `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application` yazın. *@No__t-1APP-domain-name >* değerini uygulamanızın etki alanı adıyla değiştirin.  Örneğin, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. URL 'de HTTPS şemasını kullandığınızdan emin olun.

1. **Kaydol**' u seçin.
1. **Uygulama (istemci) kimliğini**kopyalayın. Daha sonra ihtiyacınız olacak.
1. Sol bölmeden **sertifikalar & gizlilikler** > **yeni istemci parolası**' nı seçin. Bir açıklama girin, geçerlilik süresini seçin ve **Ekle**' yi seçin.
1. **Sertifikalar & gizlilikler** sayfasında görüntülenen değeri kopyalayın. Sayfadan ayrıldıktan sonra yeniden görüntülenmezler.

    > [!IMPORTANT]
    > Parola, önemli bir güvenlik kimlik bilgileridir. Parolayı kimseyle paylaşmayın veya bir istemci uygulaması içinde dağıtmayın.

## <a name="secrets"> </a>App Service uygulamanıza Microsoft hesabı bilgilerini ekleme

1. [Azure portalı]uygulamanıza gidin.
1. @No__t **ayarları**-1**kimlik doğrulaması/yetkilendirme**' yi seçin ve **App Service kimlik doğrulamasının** **Açık**olduğundan emin olun.
1. **Kimlik doğrulama sağlayıcıları**altında **Microsoft hesabı**' nı seçin. Daha önce edindiğiniz uygulama (istemci) KIMLIĞINI ve istemci gizli anahtarını yapıştırın. Uygulamanız için gereken tüm kapsamları etkinleştirin.
1. **Tamam**’ı seçin.

   App Service kimlik doğrulaması sağlar, ancak site içeriğinize ve API 'lerinize yetkili erişimi kısıtlamaz. Uygulama kodunuzda kullanıcıları yetkilendirmelisiniz.

1. Seçim Microsoft hesabı kullanıcılara erişimi kısıtlamak için, **isteğin kimliği doğrulanmamış olduğunda gerçekleştirilecek eylemi** **Microsoft hesabıyla oturum**açmak üzere ayarlayın. Bu işlevi ayarladığınızda, uygulamanız tüm isteklerin doğrulanmasını gerektirir. Ayrıca, kimlik doğrulaması için tüm kimliği doğrulanmamış istekleri Microsoft hesabı yönlendirir.

   > [!CAUTION]
   > Erişimin bu şekilde kısıtlanması, uygulamanıza yönelik tüm çağrılar için geçerlidir. Bu, birçok tek sayfalı uygulamalarda olduğu gibi, genel kullanıma açık bir giriş sayfasına sahip olan uygulamalar için istenmeyebilir. Bu tür uygulamalar için **anonim Isteklere Izin ver (eylem yok)** , uygulamanın kimlik doğrulamanın kendisini el ile başlattığı şekilde tercih edilebilir. Daha fazla bilgi için bkz. [kimlik doğrulama akışı](overview-authentication-authorization.md#authentication-flow).

1. **Kaydet**’i seçin.

Artık uygulamanızda kimlik doğrulaması için Microsoft hesabı 'nı kullanmaya hazırsınız.

## <a name="related-content"> </a>Sonraki adımlar

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure portalı]: https://portal.azure.com/
