---
title: Microsoft kimlik doğrulamasını yapılandırma
description: Microsoft hesabı kimlik doğrulamasını App Service veya Azure Işlevleri uygulamanız için bir kimlik sağlayıcısı olarak yapılandırmayı öğrenin.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 5e7b4c7ed1950b2fa585639a3097cc1f5688d739
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077967"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>App Service veya Azure Işlevleri uygulamanızı Microsoft hesabı oturum açma bilgilerini kullanacak şekilde yapılandırma

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Bu konu başlığı altında, kişisel Microsoft hesabı oturumlarını desteklemek için AAD 'yi kullanmak üzere Azure App Service veya Azure Işlevlerinin nasıl yapılandırılacağı gösterilmektedir.

> [!IMPORTANT]
> Microsoft hesabı sağlayıcısı hala desteklenirken, bunun yerine [Microsoft Identity platform sağlayıcısını (Azure AD)](./configure-authentication-provider-aad.md)kullanmanız önerilir. Microsoft Identity platformu hem kurumsal hesaplar hem de kişisel Microsoft hesapları için destek sunar.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Uygulamanızı Microsoft hesabı ile kaydedin

1. Azure portal [**uygulama kayıtları**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) gidin. Gerekirse Microsoft hesabı oturum açın.
1. **Yeni kayıt**' ı seçin ve ardından bir uygulama adı girin.
1. **Desteklenen hesap türleri** altında **herhangi bir kuruluş dizininde (HERHANGI bir Azure ad dizini-çoklu kiracı) ve kişisel Microsoft hesaplarından (örn. Skype, Xbox) hesaplar** ' ı seçin
1. **Yeniden yönlendirme URI 'lerinde**, **Web**' i seçin ve ardından girin `https://<app-domain-name>/.auth/login/aad/callback` . *\<app-domain-name>* Uygulamanızın etki alanı adıyla değiştirin.  Örneğin, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. URL 'de HTTPS şemasını kullandığınızdan emin olun.

1. **Kaydet**’i seçin.
1. **Uygulama (istemci) kimliğini** kopyalayın. Buna daha sonra ihtiyacınız olacak.
1. Sol bölmeden **Sertifikalar**' ı  >  **yeni istemci gizli**& seçin. Bir açıklama girin, geçerlilik süresini seçin ve **Ekle**' yi seçin.
1. **Sertifikalar & gizlilikler** sayfasında görüntülenen değeri kopyalayın. Sayfadan ayrıldıktan sonra yeniden görüntülenmezler.

    > [!IMPORTANT]
    > İstemci gizli değeri (parola) önemli bir güvenlik kimlik bilgileridir. Parolayı kimseyle paylaşmayın veya bir istemci uygulaması içinde dağıtmayın.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>App Service uygulamanıza Microsoft hesabı bilgilerini ekleme

1. [Azure Portal]uygulamanıza gidin.
1. **Ayarlar**  >  **kimlik doğrulaması/yetkilendirme**' yi seçin ve **App Service kimlik doğrulamasının** **Açık** olduğundan emin olun.
1. **Kimlik doğrulama sağlayıcıları** altında **Azure Active Directory**' yi seçin. **Yönetim modu** altında **Gelişmiş** ' i seçin. Daha önce edindiğiniz uygulama (istemci) KIMLIĞINI ve istemci gizli anahtarını yapıştırın. **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** **Issuer URL 'si** alanı için kullanın.
1. **Tamam**’ı seçin.

   App Service kimlik doğrulaması sağlar, ancak site içeriğinize ve API 'lerinize yetkili erişimi kısıtlamaz. Uygulama kodunuzda kullanıcıları yetkilendirmelisiniz.

1. Seçim Microsoft hesabı kullanıcılarına erişimi kısıtlamak için, **isteğin kimliği doğrulanmadığı zaman gerçekleştirilecek eylemi** **Azure Active Directory ile oturum** açmak için ayarlayın. Bu işlevi ayarladığınızda, uygulamanız tüm isteklerin doğrulanmasını gerektirir. Ayrıca kimlik doğrulaması için AAD kullanmak üzere tüm kimliği doğrulanmamış istekleri yeniden yönlendirir. **Sertifikayı veren URL** 'Nizi Microsoft hesabı kiracısını kullanacak şekilde yapılandırdığınıza göre, yalnızca kişisel hesapların kimlik doğrulaması başarılı olur.

   > [!CAUTION]
   > Erişimin bu şekilde kısıtlanması, uygulamanıza yönelik tüm çağrılar için geçerlidir. Bu, birçok tek sayfalı uygulamalarda olduğu gibi, genel kullanıma açık bir giriş sayfasına sahip olan uygulamalar için istenmeyebilir. Bu tür uygulamalar için **anonim Isteklere Izin ver (eylem yok)** , uygulamanın kimlik doğrulamanın kendisini el ile başlattığı şekilde tercih edilebilir. Daha fazla bilgi için bkz. [kimlik doğrulama akışı](overview-authentication-authorization.md#authentication-flow).

1. **Kaydet**’i seçin.

Artık uygulamanızda kimlik doğrulaması için Microsoft hesabı 'nı kullanmaya hazırsınız.

## <a name="next-steps"></a><a name="related-content"> </a>Sonraki adımlar

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure portalı]: https://portal.azure.com/
