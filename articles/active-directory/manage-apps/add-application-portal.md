---
title: 'Hızlı başlangıç: Azure Active Directory (Azure AD) kiracınıza uygulama ekleme'
description: Bu hızlı başlangıçta Azure portal kullanılarak Azure Active Directory (Azure AD) kiracınıza bir galeri uygulaması eklenmektedir.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: iangithinji
ms.openlocfilehash: d7827d6419e4d820f8aca482df2cfae83eaf319d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378899"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Hızlı başlangıç: Azure Active Directory (Azure AD) kiracınıza uygulama ekleme

Azure Active Directory (Azure AD), önceden tümleştirilmiş binlerce uygulamanın yer aldığı bir galeriye sahiptir. Kuruluşunuzun kullandığı uygulamaların birçoğu büyük olasılıkla galeride zaten var.

Bir uygulamayı Azure AD kiracınıza ekledikten sonra aşağıdaki işlemleri gerçekleştirebilirsiniz:

- Uygulamanın özelliklerini yapılandırın.
- Koşullu erişim ilkesiyle uygulamaya yönelik Kullanıcı erişimini yönetin.
- Kullanıcıların Azure AD kimlik bilgileriyle uygulamada oturum açmasını sağlamak için çoklu oturum açmayı yapılandırın.

## <a name="prerequisites"></a>Önkoşullar

Azure AD kiracınıza bir uygulama eklemek için şunlar gerekir:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aşağıdaki rollerden biri: genel yönetici, bulut uygulaması Yöneticisi, uygulama Yöneticisi veya hizmet sorumlusu sahibi.
- (İsteğe bağlı: [uygulamalarınızı görüntüleme](view-applications-portal.md)işleminin tamamlanması).

>[!IMPORTANT]
>Bu hızlı başlangıçtaki adımları test etmek için üretim dışı bir ortam kullanmanızı öneririz.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Azure AD kiracınıza uygulama ekleme

Azure AD kiracınıza bir uygulama eklemek için:

1. [Azure Portal](https://portal.azure.com)sol gezinti panelinde **Azure Active Directory**' i seçin.
2. **Azure Active Directory** bölmesinde **Kurumsal uygulamalar**' ı seçin. **Tüm uygulamalar** bölmesi açılır ve Azure AD kiracınızdaki uygulamaların rastgele bir örneğini görüntüler.
3. **Kurumsal uygulamalar** bölmesinde **Yeni uygulama**' yı seçin. 
    ![Kiracınıza Galeri uygulaması eklemek için yeni uygulama ' yı seçin](media/add-application-portal/new-application.png)
4. Yeni Galeri önizleme deneyimine geç: **Uygulama Ekle sayfasının** en üstündeki başlıkta, **yeni ve geliştirilmiş uygulama galerisini denemek için buraya tıklayın** bağlantısını seçin.
5. **Azure AD galerisine gözatıp (Önizleme)** bölmesi açılır ve bulut platformları, şirket içi uygulamalar ve öne çıkan uygulamalar için kutucukları görüntüler. **Öne çıkan uygulamalar** bölümünde listelenen uygulamalar, federe çoklu oturum açma (SSO) ve sağlamayı destekleyip desteklemediğini gösteren simgelere sahiptir. 
    ![Bir uygulamayı ada veya kategoriye göre ara](media/add-application-portal/browse-gallery.png)
6. Eklemek istediğiniz uygulamanın galerisine gözatıp, arama kutusuna adını girerek uygulamayı arayabilirsiniz. Sonra sonuçlardan uygulamayı seçin. 
7. Sonraki adım, uygulamanın geliştiricisinin çoklu oturum açma (SSO) ile ilgili olarak değişir. Çoklu oturum açma, uygulama geliştiricileri tarafından dört şekilde uygulanabilir. SAML, OpenID Connect, Password ve Linked dört yolu vardır. Bir uygulama eklediğinizde, ekran görüntüsünde gösterildiği gibi yalnızca belirli bir SSO uygulamasını kullanarak uygulamaları filtreleyip görmeyi seçebilirsiniz. Örneğin, SSO uygulamak için popüler bir standart Security Assertion Markup Language (SAML) olarak adlandırılır. Yaygın olarak kullanılan başka bir standart, OpenID Connect (OıDC) olarak adlandırılır. SSO 'yu bu standartlara göre yapılandırma yönteminiz farklı olduğundan, eklediğiniz uygulama tarafından uygulanan SSO türünü unutmayın.

    :::image type="content" source="media/add-application-portal/sso-types.png" alt-text="Ekran görüntüsü SSO türleri seçicisini gösterir." lightbox="media/add-application-portal/sso-types.png":::

    - Uygulamanın geliştiricisi SSO için **OIDC standardını** kullandıysanız **kaydolun**' ı seçin. Bir kurulum sayfası görüntülenir. Ardından, OıDC tabanlı çoklu oturum açmayı ayarlama hakkında hızlı başlangıç bölümüne gidin.
    :::image type="content" source="media/add-application-portal/sign-up-oidc-sso.png" alt-text="Ekran görüntüsünde, OıDC tabanlı SSO uygulaması ekleme gösterilmektedir.":::

    - Uygulamanın geliştiricisi SSO için **SAML standardını** kullandıysanız **Oluştur**' u seçin. Uygulamayı kuruluşunuza göre yapılandırmak için kullanabileceğiniz seçeneklerin bulunduğu bir başlangıç sayfası açılır. Formunda, kuruluşunuzun gereksinimlerini karşılayacak şekilde uygulamanın adını düzenleyebilirsiniz. Ardından, SAML tabanlı çoklu oturum açma 'yı ayarlamaya yönelik hızlı başlangıç sayfasına gidin.
    :::image type="content" source="media/add-application-portal/create-application.png" alt-text="Ekran görüntüsü SAML tabanlı SSO uygulaması eklemeyi gösterir.":::


> [!IMPORTANT]
> SAML tabanlı ve OıDC tabanlı SSO uygulamaları arasında bazı önemli farklılıklar vardır. SAML tabanlı uygulamalarla aynı uygulamanın birden fazla örneğini ekleyebilirsiniz. Örneğin, GitHub1, GitHub2 vb. OıDC tabanlı uygulamalar için bir uygulamanın yalnızca bir örneğini ekleyebilirsiniz. Zaten bir OıDC tabanlı uygulama eklediyseniz ve aynı uygulamayı yeniden eklemeyi deneyin ve izin iki kez gerekiyorsa, kiracıya yeniden eklenmeyecektir.

Aradığınız uygulama Galeri 'de değilse, **kendi uygulamanızı oluşturma** bağlantısını seçip **uygulamanızla ne yapmak istiyorsunuz?** bölümünde, uygulamanızda **bulamadıysanız herhangi bir uygulamayı tümleştirin**' ı seçin. Microsoft, Azure AD ile çalışacak şekilde önceden yapılandırmak için birçok uygulama geliştiricileriyle zaten çalıştık. Önceden yapılandırılmış uygulamalar galeride gösterilir. Ancak eklemek istediğiniz uygulama listede yoksa, yeni, genel bir uygulama oluşturabilir ve bunu kendiniz veya onu oluşturan geliştiricinin kılavuzumuzu ile yapılandırabilirsiniz.

Uygulama eklemeyi tamamladınız. Sonraki hızlı başlangıçta, logosunun nasıl değiştirileceği ve uygulamanızın diğer özelliklerinin nasıl düzenleneceği gösterilmektedir.

> [!TIP]
> Graph API kullanarak uygulama yönetimini otomatik hale getirebilirsiniz, bkz. [MICROSOFT Graph API ile uygulama yönetimini otomatikleştirme](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıç serisine devam edemeyecekseniz, test kiracınızı temizlemek için uygulamayı silmeyi göz önünde bulundurun. Bu serinin son hızlı başlangıçta uygulamanın silinmesi, bkz. [uygulamayı silme](delete-application-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

Bir uygulamanın nasıl yapılandırılacağını öğrenmek için bir sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [Bir uygulamayı yapılandırma](add-application-portal-configure.md)