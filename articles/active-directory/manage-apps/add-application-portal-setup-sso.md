---
title: 'Hızlı başlangıç: Azure Active Directory (Azure AD) kiracınızdaki bir uygulama için SAML tabanlı çoklu oturum açma (SSO) ayarlama'
description: Bu hızlı başlangıç, Azure Active Directory (Azure AD) kiracınızdaki bir uygulama için SAML tabanlı çoklu oturum açma (SSO) ayarlama sürecinde size yol gösterir.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: iangithinji
ms.openlocfilehash: 802cc744063ba2bf1110915ae9b22c9c00e09cfa
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378916"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Hızlı başlangıç: Azure Active Directory (Azure AD) kiracınızdaki bir uygulama için SAML tabanlı çoklu oturum açma (SSO) ayarlama

Azure Active Directory (Azure AD) kiracınıza eklediğiniz bir uygulama için çoklu oturum açma (SSO) ayarlayarak Basitleştirilmiş kullanıcı oturum açmaları ile çalışmaya başlayın. SSO 'yu ayarladıktan sonra kullanıcılarınız Azure AD kimlik bilgilerini kullanarak bir uygulamada oturum açabilir. SSO, ücretsiz Azure AD sürümüne dahildir.

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

Azure AD kiracınıza bir uygulama eklemeyi bitirdikten sonra genel bakış sayfası görüntülenir. Zaten eklenmiş olan bir uygulamayı yapılandırıyorsanız ilk hızlı başlangıca bakın. Kiracınıza eklenen uygulamaları görüntülemeyi adım adım gösterir.

Bir uygulama için çoklu oturum açmayı ayarlamak için:

1. Azure AD portalında **Kurumsal uygulamalar**' ı seçin. Ardından, çoklu oturum açma için ayarlamak istediğiniz uygulamayı bulun ve seçin.
1. **Yönet** bölümünde, **tek oturum aç ' ı seçerek,** düzenlemenin **Çoklu oturum** açma bölmesini açın.

    > [!IMPORTANT]
    > Uygulama SSO için OpenID Connect (OıDC) standardını kullanıyorsa, gezintide çoklu oturum açma seçeneği görmezsiniz. Nasıl ayarlanacağını öğrenmek için OıDC tabanlı SSO 'daki hızlı başlangıç bölümüne bakın.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Ekran görüntüsü, Azure AD portalındaki çoklu oturum açma yapılandırma sayfasını gösterir.":::

1. SSO yapılandırma sayfasını açmak için **SAML** ' yi seçin. Bu örnekte, SSO için yapılandırdığımız uygulama GitHub ' dır. GitHub kurulduktan sonra, kullanıcılarınız Azure AD kiracınızdaki kimlik bilgilerini kullanarak GitHub 'da oturum açabilirler.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Ekran görüntüsü, GitHub 'daki çoklu oturum açma yapılandırma sayfasını gösterir.":::

1. SAML tabanlı SSO için Azure AD kullanmak üzere bir uygulamayı yapılandırma işlemi uygulamaya bağlı olarak değişir. GitHub Kılavuzu için bir bağlantı vardır. Diğer uygulamalara yönelik kılavuzlar bulmak için bkz. [SaaS uygulamalarını Azure Active Directory ile tümleştirmek Için öğreticiler](/azure/active-directory/saas-apps/).
1. Uygulamanın SSO 'yu ayarlamak için Kılavuzu izleyin. Birçok uygulamanın SSO işlevselliği için belirli abonelik gereksinimleri vardır. Örneğin, GitHub bir kurumsal abonelik gerektirir.
    > [!TIP]
    > SAML yapılandırma seçenekleri hakkında daha fazla bilgi edinmek için bkz. [SAML tabanlı çoklu oturum açmayı yapılandırma](configure-saml-single-sign-on.md).

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Ekran görüntüsü, GitHub fiyatlandırma sayfasının kurumsal aboneliğindeki çoklu oturum açma seçeneğini gösterir.":::

> [!TIP]
> Graph API kullanarak uygulama yönetimini otomatik hale getirebilirsiniz, bkz. [MICROSOFT Graph API ile uygulama yönetimini otomatikleştirme](/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıç serisini tamamladığınızda, test kiracınızı temizlemek için uygulamayı silmeyi göz önünde bulundurun. Bu serinin son hızlı başlangıçta uygulamanın silinmesi, bkz. [uygulamayı silme](delete-application-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

Bir uygulamayı silme hakkında bilgi edinmek için sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [Bir uygulamayı silme](delete-application-portal.md)
