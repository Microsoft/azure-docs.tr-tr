---
title: 'Hızlı başlangıç: Azure Active Directory (Azure AD) kiracınızdaki bir uygulama için OıDC tabanlı çoklu oturum açma (SSO) ayarlama'
description: Bu hızlı başlangıç, Azure Active Directory (Azure AD) kiracınızdaki bir uygulama için OıDC tabanlı çoklu oturum açma (SSO) ayarlama sürecinde size yol gösterir.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: iangithinji
ms.openlocfilehash: 2e0b49a73422a2f71af37e103e9d4fd6a18a1f61
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378950"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Hızlı başlangıç: Azure Active Directory (Azure AD) kiracınızdaki bir uygulama için OıDC tabanlı çoklu oturum açma (SSO) ayarlama

Azure Active Directory (Azure AD) kiracınıza eklediğiniz bir uygulama için çoklu oturum açma (SSO) ayarlayarak Basitleştirilmiş kullanıcı oturum açmaları ile çalışmaya başlayın. SSO 'yu ayarladıktan sonra kullanıcılarınız Azure AD kimlik bilgilerini kullanarak bir uygulamada oturum açabilir. SSO, ücretsiz Azure AD sürümüne dahildir.

OıDC tabanlı SSO hakkında daha fazla bilgi edinmek için bkz. [OıDC tabanlı çoklu oturum açmayı anlama](configure-oidc-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Azure AD kiracınıza eklediğiniz bir uygulamaya yönelik SSO 'yu ayarlamak için şunlar gerekir:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aşağıdaki rollerden biri: genel yönetici, bulut uygulaması Yöneticisi, uygulama Yöneticisi veya hizmet sorumlusu sahibi.
- SSO 'yu destekleyen ve önceden yapılandırılmış ve Azure AD galerisine eklenen bir uygulama. Çoğu uygulama, SSO için Azure AD kullanabilir. Azure AD galerisindeki uygulamalar önceden yapılandırılmıştır. Uygulamanız listede yoksa veya özel olarak geliştirilmiş bir uygulama ise, bunu Azure AD ile kullanmaya devam edebilirsiniz. İçindekiler bölümündeki öğreticilere ve diğer belgelere göz atın. Bu hızlı başlangıç, SSO için önceden yapılandırılmış ve uygulama geliştiricileri tarafından Azure AD galerisine eklenen uygulamalara odaklanır.
- İsteğe bağlı: [uygulamalarınızı görüntülemeyi](view-applications-portal.md)tamamlama.
- İsteğe bağlı: [uygulama ekleme](add-application-portal.md)işleminin tamamlanması.
- İsteğe bağlı: [uygulama yapılandırma](add-application-portal-configure.md)işleminin tamamlanması.
- İsteğe bağlı: [bir uygulamaya Kullanıcı atama](add-application-portal-assign-users.md)tamamlama.

>[!IMPORTANT]
>Bu hızlı başlangıçtaki adımları test etmek için üretim dışı bir ortam kullanın.

## <a name="enable-single-sign-on-for-an-app"></a>Uygulama için çoklu oturum açmayı etkinleştirme

SSO için OıDC standardını kullanan bir uygulama eklediğinizde bir kurulum düğmesidir. Düğmeyi seçtiğinizde, uygulamalar sitesine giderek uygulamanın kaydolma işlemini tamamlayabilirsiniz. Uygulama ekleme işlemi, bu serinin önceki kısımlarında uygulama ekleme hızlı başlangıcı kapsamında ele alınmıştır. Zaten eklenmiş olan bir uygulamayı yapılandırıyorsanız ilk hızlı başlangıca bakın. Kiracınızda zaten bulunan uygulamaları görüntülemeyi adım adım gösterir. 

Bir uygulama için çoklu oturum açmayı ayarlamak için:

1. Bu serideki önceki hızlı başlangıçta kimlik yönetimi için Azure AD kiracınızı kullanacak bir uygulama eklemeyi öğrendiniz. Uygulama geliştiricisi SSO uygulamak için OıDC standardını kullandıysanız, uygulamayı eklerken bir kaydolma düğmesi sunulur. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="Ekran görüntüsünde çoklu oturum açma seçeneği ve kaydolma düğmesi gösterilir." lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. **Kaydolma** ' yı seçin ve uygulama geliştiricileri oturum açma sayfasına yönlendirilirsiniz. Azure Active Directory oturum açma kimlik bilgilerini kullanarak oturum açın. 

   > [!IMPORTANT]
    > Zaten uygulamaya bir aboneliğiniz varsa, Kullanıcı ayrıntılarının doğrulanması ve kiracı/dizin bilgilerinin gerçekleşmesi gerekir. Uygulama kullanıcıyı doğrulayamezse uygulama hizmetine veya hata sayfasına kaydolmak için sizi yönlendirecektir.

3. Başarılı kimlik doğrulamasından sonra, yönetici onayı isteyen bir iletişim kutusu görüntülenir. **Kuruluşunuz adına izin** ' ı seçin ve ardından **kabul et**' i seçin. Kullanıcı ve yönetici onayı hakkında daha fazla bilgi edinmek için bkz. [Kullanıcı ve yönetici onayını anlama](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="Ekran görüntüsü, bir uygulamanın onay ekranını gösterir." lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. Uygulama kiracınıza eklenir ve uygulama giriş sayfası görüntülenir.


> [!TIP]
> Graph API kullanarak uygulama yönetimini otomatik hale getirebilirsiniz, bkz. [MICROSOFT Graph API ile uygulama yönetimini otomatikleştirme](/graph/application-saml-sso-configure-api).

Azure AD 'ye OıDC tabanlı bir uygulama ekleme hakkında ayrıntılı bilgi almak için aşağıdaki bir video aşağıda verilmiştir.

Azure Active Directory bir OıDC tabanlı uygulama ekleme

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıç serisini tamamladığınızda, test kiracınızı temizlemek için uygulamayı silmeyi göz önünde bulundurun. Bu serinin son hızlı başlangıçta uygulamanın silinmesi, bkz. [uygulamayı silme](delete-application-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

Bir uygulamayı silme hakkında bilgi edinmek için sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [Bir uygulamayı silme](delete-application-portal.md)
