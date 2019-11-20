---
title: Azure Active Directory B2C-Azure API Management kullanarak Geliştirici hesapları yetkilendir | Microsoft Docs
description: API Management Azure Active Directory B2C kullanarak kullanıcılara yetki verme hakkında bilgi edinin.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 55a9cbcfdb9766c6adc6d10f975fa4d074ec3aaf
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011660"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>API Management Azure 'da Azure Active Directory B2C kullanarak Geliştirici hesaplarını yetkilendirme

## <a name="overview"></a>Genel Bakış

Azure Active Directory B2C, tüketicilere yönelik web ve mobil uygulamalar için bir bulut kimlik yönetimi çözümü ' dir. Bunu, geliştirici portalınıza erişimi yönetmek için kullanabilirsiniz. Bu kılavuzda, Azure Active Directory B2C tümleştirilecek API Management hizmetinizde gereken yapılandırma gösterilmektedir. Klasik Azure Active Directory kullanarak Geliştirici portalına erişimi etkinleştirme hakkında daha fazla bilgi için, bkz. [Azure Active Directory kullanarak Geliştirici hesaplarını yetkilendirme].

> [!NOTE]
> Bu kılavuzdaki adımları tamamlayabilmeniz için öncelikle ' de bir uygulama oluşturmak üzere bir Azure Active Directory B2C kiracısına sahip olmanız gerekir. Ayrıca kaydolma ve oturum açma ilkelerinizi hazırlayın. Daha fazla bilgi için bkz. [Azure Active Directory B2C genel bakış].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak Geliştirici hesaplarını yetkilendirme

1. Başlamak için [Azure Portal](https://portal.azure.com) oturum açın ve API Management örneğinizi bulun.

   > [!NOTE]
   > Henüz bir API Management hizmet örneği oluşturmadıysanız, [Azure ile çalışmaya başlama API Management öğreticisinde][Get started with Azure API Management] [API Management hizmet örneği oluşturma][Create an API Management service instance] bölümüne bakın.

2. **Kimlikler**altında. Üstteki **+ Ekle** ' ye tıklayın.

   Sağ tarafta **kimlik sağlayıcı ekle** bölmesi görünür. **Azure Active Directory B2C**seçin.
    
   ![Kimlik sağlayıcısı olarak AAD B2C ekleme][api-management-howto-add-b2c-identity-provider]

3. **Yeniden YÖNLENDIRME URL**'sini kopyalayın.

   ![AAD B2C Identity Provider yeniden yönlendirme URL 'SI][api-management-howto-copy-b2c-identity-provider-redirect-url]

4. Yeni bir sekmede, Azure portal Azure Active Directory B2C kiracınıza erişin ve **uygulamalar** dikey penceresini açın.

   ![Yeni bir uygulama kaydet 1][api-management-howto-aad-b2c-portal-menu]

5. Yeni bir Azure Active Directory B2C uygulaması oluşturmak için **Ekle** düğmesine tıklayın.

   ![Yeni bir uygulama kaydet 2][api-management-howto-aad-b2c-add-button]

6. **Yeni uygulama** dikey penceresinde uygulama için bir ad girin. **Web uygulaması/Web API 'si**altında **Evet** ' i seçin ve **örtük akışa izin ver**altında **Evet** ' i seçin Ardından adım 3 ' te kopyalanmış **yeniden YÖNLENDIRME URL** 'sini **yanıt URL 'si** metin kutusuna yapıştırın.

   ![Yeni bir uygulama kaydetme 3][api-management-howto-aad-b2c-app-details]

7. **Oluştur** düğmesine tıklayın. Uygulama oluşturulduğunda, **uygulamalar** dikey penceresinde görünür. Ayrıntılarını görmek için uygulamanın adına tıklayın.

   ![Yeni bir uygulama kaydetme 4][api-management-howto-aad-b2c-app-created]

8. **Özellikler** dikey penceresinde, **uygulama kimliğini** panoya kopyalayın.

   ![Uygulama KIMLIĞI 1][api-management-howto-aad-b2c-app-id]

9. API Management **kimlik sağlayıcısı ekle** bölmesine dönün ve kimliği **istemci kimliği** metin kutusuna yapıştırın.
    
10. B2C uygulaması kaydına geri dönün, **anahtarlar** düğmesine tıklayın ve ardından **anahtar oluştur**' a tıklayın. Yapılandırmayı kaydetmek ve **uygulama anahtarını**göstermek için **Kaydet** ' e tıklayın. Anahtarı panoya kopyalayın.

    ![Uygulama anahtarı 1][api-management-howto-aad-b2c-app-key]

11. API Management **kimlik sağlayıcısı ekle** bölmesine dönün ve anahtarı **istemci gizli** metin kutusuna yapıştırın.
    
12. **Oturum açma kiracısında**Azure Active Directory B2C kiracının etki alanı adını belirtin.

13. **Yetkili** alanı, kullanılacak Azure AD B2C oturum açma URL 'sini denetlemenize olanak tanır. Değeri **< your_b2c_tenant_name >. b2clogin. com**olarak ayarlayın.

14. B2C kiracı ilkelerindeki **kaydolma ilkesini** ve **oturum açma ilkesini** belirtin. İsteğe bağlı olarak, **Profil Düzenle ilke** ve **parola sıfırlama ilkesini**de sağlayabilirsiniz.

15. İstenen yapılandırmayı belirledikten sonra **Kaydet**' e tıklayın.

    Değişiklikler kaydedildikten sonra, geliştiriciler yeni hesaplar oluşturup Azure Active Directory B2C kullanarak Geliştirici Portalında oturum açabilir.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Geliştirici portalı-Azure AD B2C hesabı kimlik doğrulaması ekleme

Geliştirici Portalında AAD B2C ile oturum açma, **OAuth düğmeleri** pencere öğesi ile mümkündür. Pencere öğesi, varsayılan geliştirici portalı içeriğinin oturum açma sayfasında zaten bulunur.

![AAD düğmeleri pencere öğesi](./media/api-management-howto-aad/portal-oauth-widget.png)

Yeni bir Kullanıcı AAD B2C her oturum açtığında yeni bir hesap otomatik olarak oluşturulabilse de, kaydolma sayfasına aynı pencere öğesini eklemeyi göz önünde bulundurmanız gerekebilir.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Eski geliştirici portalı-Azure AD B2C ile kaydolma

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Azure Active Directory B2C kullanarak bir Geliştirici hesabına kaydolmak için, yeni bir tarayıcı penceresi açın ve geliştirici portalına gidin. **Kaydolun** düğmesine tıklayın.

   ![Geliştirici portalı 1][api-management-howto-aad-b2c-dev-portal]

2. **Azure Active Directory B2C**kaydolmak için seçin.

   ![Geliştirici portalı 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Önceki bölümde yapılandırdığınız kaydolma ilkesine yönlendirilirsiniz. E-posta adresinizi veya var olan sosyal hesaplarınızdan birini kullanarak kaydolmayı seçin.

   > [!NOTE]
   > Yayımcı portalındaki **kimlikler** sekmesinde etkin olan tek seçenek Azure Active Directory B2C, doğrudan kaydolma ilkesine yönlendirilirsiniz.

   ![Geliştirici portalı][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Kaydolma tamamlandığında, geliştirici portalına geri yönlendirilirsiniz. Artık API Management hizmet örneğiniz için Geliştirici Portalında oturum açtınız.

    ![Kayıt Tamam][api-management-registration-complete]

## <a name="next-steps"></a>Sonraki adımlar

*  [Azure Active Directory B2C genel bakış]
*  [Azure Active Directory B2C: Genişletilebilir ilke çerçevesi]
*  [Microsoft hesabı bir kimlik sağlayıcısı olarak bir Azure Active Directory B2C kullanın]
*  [Bir Google hesabını Azure Active Directory B2C bir kimlik sağlayıcısı olarak kullanın]
*  [Azure Active Directory B2C bir LinkedIn hesabını bir kimlik sağlayıcısı olarak kullanın]
*  [Azure Active Directory B2C ' de bir Facebook hesabını bir kimlik sağlayıcısı olarak kullanın]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Azure Active Directory B2C genel bakış]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Azure Active Directory kullanarak Geliştirici hesaplarını yetkilendirme]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: Genişletilebilir ilke çerçevesi]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Microsoft hesabı bir kimlik sağlayıcısı olarak bir Azure Active Directory B2C kullanın]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Bir Google hesabını Azure Active Directory B2C bir kimlik sağlayıcısı olarak kullanın]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Azure Active Directory B2C ' de bir Facebook hesabını bir kimlik sağlayıcısı olarak kullanın]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Azure Active Directory B2C bir LinkedIn hesabını bir kimlik sağlayıcısı olarak kullanın]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
