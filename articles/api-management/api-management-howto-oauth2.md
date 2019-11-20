---
title: Azure API Management 'de OAuth 2,0 kullanarak Geliştirici hesaplarını yetkilendir | Microsoft Docs
description: API Management 'de OAuth 2,0 kullanarak kullanıcılara yetki verme hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 99a49aa4627dc23d5f7531ac961d63e3e75ccff9
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176620"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Azure API Management 'de OAuth 2,0 kullanarak Geliştirici hesaplarını yetkilendirme

Birçok API, API 'YI güvenli hale getirmek ve yalnızca geçerli kullanıcıların erişimi olduğundan emin olmak için [OAuth 2,0](https://oauth.net/2/) ' i destekler ve yalnızca hak kazanabilecekleri kaynaklara erişebilir. Azure API Management 'in etkileşimli geliştirici konsolunu bu tür API 'lerle kullanabilmek için, hizmet örneğinizi OAuth 2,0 özellikli API 'niz ile çalışacak şekilde yapılandırmanıza olanak tanır.

> [!IMPORTANT]
> OAuth 2,0 yetkilendirmesi, yeni geliştirici portalının etkileşimli konsolunda henüz kullanılamamaktadır.

## <a name="prerequisites"> </a>Önkoşullar

Bu kılavuzda, API Management hizmeti örneğinizi geliştirici hesapları için OAuth 2,0 yetkilendirmesi kullanacak şekilde nasıl yapılandırabileceğiniz, ancak bir OAuth 2,0 sağlayıcısının nasıl yapılandırılacağı gösterilmeyebilir. Her bir OAuth 2,0 sağlayıcısı için yapılandırma farklıdır, ancak adımlar benzerdir ve API Management hizmet Örneğinizde OAuth 2,0 ' i yapılandırmada kullanılan gerekli bilgi parçaları aynı olur. Bu konuda, OAuth 2,0 sağlayıcısı olarak Azure Active Directory kullanan örnekler gösterilmektedir.

> [!NOTE]
> Azure Active Directory kullanarak OAuth 2,0 yapılandırma hakkında daha fazla bilgi için bkz. [WebApp-Graphapı-DotNet][WebApp-GraphAPI-DotNet] örneği.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>API Management bir OAuth 2,0 yetkilendirme sunucusu yapılandırma

> [!NOTE]
> Henüz bir API Management hizmet örneği oluşturmadıysanız, bkz. [API Management hizmet örneği oluşturma][Create an API Management service instance].

1. Sol taraftaki menüdeki OAuth 2,0 sekmesine tıklayın ve **+ Ekle**' ye tıklayın.

    ![OAuth 2,0 menüsü](./media/api-management-howto-oauth2/oauth-01.png)

2. **Ad** ve **Açıklama** alanlarına bir ad ve isteğe bağlı bir açıklama girin.

    > [!NOTE]
    > Bu alanlar, OAuth 2,0 yetkilendirme sunucusunu geçerli API Management hizmet örneği içinde tanımlamak için kullanılır ve bunların değerleri OAuth 2,0 sunucusundan gelmiyor.

3. **İstemci kayıt sayfası URL 'sini**girin. Bu sayfa, kullanıcıların hesaplarını oluşturup yönetebilecekleri ve kullanılan OAuth 2,0 sağlayıcısına bağlı olarak farklılık gösterdiği yerdir. **İstemci kayıt sayfası URL 'si** , kullanıcıların, hesapların Kullanıcı yönetimini destekleyen OAuth 2,0 sağlayıcıları için kendi hesaplarını oluşturmak ve yapılandırmak üzere kullanabileceği sayfayı işaret eder. Bazı kuruluşlar, OAuth 2,0 sağlayıcısı tarafından desteklendiği halde bu işlevselliği yapılandırmaz veya kullanmaz. OAuth 2,0 sağlayıcınızda yapılandırılmış hesapların Kullanıcı yönetimi yoksa, şirketinizin URL 'SI veya `https://placeholder.contoso.com`gibi bir URL gibi bir yer tutucu URL 'si girin.

    ![OAuth 2,0 yeni sunucu](./media/api-management-howto-oauth2/oauth-02.png)

4. Formun sonraki bölümü **Yetkilendirme verme türlerini**, **Yetkilendirme uç noktası URL 'sini**ve **yetkilendirme isteği yöntemi** ayarlarını içerir.

    İstenen türleri denetleyerek **Yetkilendirme verme türlerini** belirtin. **Yetkilendirme kodu** varsayılan olarak belirtilir.

    **Yetkilendirme uç noktası URL 'sini**girin. Azure Active Directory için bu URL aşağıdaki URL 'ye benzer, burada `<tenant_id>` Azure AD kiracınızın KIMLIĞIYLE birlikte değişir.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    **Yetkilendirme isteği yöntemi** , yetkilendirme isteğinin OAuth 2,0 sunucusuna nasıl gönderileceğini belirtir. Varsayılan olarak **Al** seçilidir.

5. Ardından, **belirteç uç noktası URL 'si**, **istemci kimlik doğrulama yöntemleri**, **erişim belirteci gönderme yöntemi** ve **varsayılan kapsam** belirtilmelidir.

    ![OAuth 2,0 yeni sunucu](./media/api-management-howto-oauth2/oauth-03.png)

    Azure Active Directory OAuth 2,0 sunucusu için, **belirteç uç noktası URL 'si** aşağıdaki biçimde olacaktır, burada `<TenantID>` `yourapp.onmicrosoft.com`biçimindedir.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    **İstemci kimlik doğrulama yöntemlerinin** varsayılan ayarı **temel**ve **erişim belirteci gönderme yöntemi** **Yetkilendirme üst bilgisi**olur. Bu değerler formun bu bölümünde, **varsayılan kapsamla**birlikte yapılandırılır.

6. **İstemci kimlik bilgileri** bölümü, OAuth 2,0 sunucunuzun oluşturma ve yapılandırma işlemi sırasında elde EDILEN **Istemci kimliğini** ve **istemci gizli**anahtarını içerir. **ISTEMCI kimliği** ve **istemci parolası** belirtildiğinde, **yetkilendirme kodu** için **redirect_uri** oluşturulur. Bu URI, OAuth 2,0 sunucu yapılandırmanızda yanıt URL 'sini yapılandırmak için kullanılır.

    ![OAuth 2,0 yeni sunucu](./media/api-management-howto-oauth2/oauth-04.png)

    **Yetkilendirme verme türleri** **kaynak sahibi parolası**olarak ayarlandıysa, bu kimlik bilgilerini belirtmek için **kaynak sahibi parolası kimlik bilgileri** bölümü kullanılır; Aksi takdirde, boş bırakabilirsiniz.

    Form tamamlandıktan sonra, API Management OAuth 2,0 yetkilendirme sunucusu yapılandırmasını kaydetmek için **Oluştur** ' a tıklayın. Sunucu yapılandırması kaydedildikten sonra, API 'Leri, sonraki bölümde gösterildiği gibi bu yapılandırmayı kullanacak şekilde yapılandırabilirsiniz.

## <a name="step2"> </a>API 'yi OAuth 2,0 kullanıcı yetkilendirmesi kullanacak şekilde yapılandırma

1. Soldaki **API Management** menüsünde **API 'ler** ' e tıklayın.

    ![OAuth 2,0 API 'Leri](./media/api-management-howto-oauth2/oauth-05.png)

2. İstenen API 'nin adına tıklayın ve **Ayarlar**' a tıklayın. **Güvenlik** bölümüne gidip **OAuth 2,0**kutusunu işaretleyin.

    ![OAuth 2,0 ayarları](./media/api-management-howto-oauth2/oauth-06.png)

3. Açılan listeden istenen **Yetkilendirme sunucusunu** seçin ve **Kaydet**' e tıklayın.

    ![OAuth 2,0 ayarları](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="step3"> </a>Eski geliştirici portalı-OAuth 2,0 Kullanıcı yetkilendirmesini test etme

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

OAuth 2,0 yetkilendirme sunucunuzu yapılandırdıktan ve API 'nizi bu sunucuyu kullanacak şekilde yapılandırdıktan sonra, geliştirici Portalına gidip bir API 'yi çağırarak test edebilirsiniz. Azure API Management örneğine **genel bakış** sayfasından üstteki menüden **Geliştirici Portalı (eski)** seçeneğine tıklayın.

Üstteki menüde **API 'ler** ' e tıklayın ve **echo API**' yi seçin.

![Echo API’si][api-management-apis-echo-api]

> [!NOTE]
> Yapılandırılmış ya da hesabınıza görünen yalnızca bir API’niz varsa, API’lere tıklamak sizi doğrudan bu API’nin işlemlerine götürür.

**Kaynak al** işlemini seçin, **Konsolu Aç**' a tıklayın ve ardından açılan listeden **yetkilendirme kodu** ' nu seçin.

![Konsolu açma][api-management-open-console]

**Yetkilendirme kodu** seçildiğinde, OAuth 2,0 sağlayıcısı 'nın oturum açma formuyla bir açılır pencere görüntülenir. Bu örnekte, oturum açma formu Azure Active Directory tarafından sağlanır.

> [!NOTE]
> Açılır pencereleri devre dışı bırakırsanız, tarayıcı tarafından etkinleştirmeniz istenir. Bunları etkinleştirdikten sonra, **yetkilendirme kodu** ' nu yeniden seçin ve oturum açma formu görüntülenir.

![Oturum aç][api-management-oauth2-signin]

Oturum açtıktan sonra, **istek üstbilgileri** isteği yetkilendiren bir `Authorization : Bearer` üstbilgisiyle doldurulur.

![İstek üst bilgisi belirteci][api-management-request-header-token]

Bu noktada, kalan parametreler için istenen değerleri yapılandırabilir ve isteği gönderebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

OAuth 2,0 ve API Management kullanma hakkında daha fazla bilgi için aşağıdaki videoya ve ilgili [makaleye](api-management-howto-protect-backend-with-aad.md)bakın.

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

