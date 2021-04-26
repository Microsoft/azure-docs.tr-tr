---
title: Eski geliştirici portalından yeni geliştirici portalına geçiş
titleSuffix: Azure API Management
description: Eski geliştirici portalından API Management yeni geliştirici portalına nasıl geçeceğinizi öğrenin.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: e4f9f3822b58886f7d453d52402b078d8401133f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738891"
---
# <a name="migrate-to-the-new-developer-portal"></a>Yeni geliştirici portalına geçirme

Bu makalede, kullanımdan kaldırılan eski portaldan API Management yeni geliştirici portalına geçiş yapmak için gerçekleştirmeniz gereken adımlar açıklanır.

> [!IMPORTANT]
> Eski geliştirici portalı artık kullanım dışıdır ve yalnızca güvenlik güncelleştirmelerini alacaktır. Bu hizmeti, her zamanki gibi kullanmaya devam edebilirsiniz, bu, tüm API Management hizmetlerinden kaldırılacak olan 2023 Ekim 'de devre dışı kalır.

![API Management geliştirici portalı](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Yeni geliştirici portalındaki geliştirmeler

Yeni geliştirici portalı, kullanımdan kaldırılan portalın birçok sınırlamalarını ele alınmaktadır. [İçerik düzenleme için görsel bir sürükle ve bırak Düzenleyicisi](api-management-howto-developer-portal-customize.md) ve tasarımcıların Web sitesini stilindeki özel bir panel sunar. Sayfalar, özelleştirmeler ve yapılandırma API Management hizmetinize Azure Resource Manager kaynaklar olarak kaydedilir ve bu da [Portal dağıtımlarını otomatik hale getirmenizi](automate-portal-deployments.md)sağlar. Son olarak, portalın kod temeli açık kaynaktır, [Bu sayede özel işlevlerle genişletebilirsiniz](api-management-howto-developer-portal.md#managed-vs-self-hosted).

## <a name="how-to-migrate-to-new-developer-portal"></a>Yeni geliştirici portalına geçirme

Yeni geliştirici portalı kullanımdan kaldırılan portalla uyumlu değildir ve otomatik geçiş mümkün değildir. İçeriği (sayfalar, metin, medya dosyaları) el ile yeniden oluşturmanız ve yeni portalın görünümünü özelleştirmeniz gerekir. Tam adımlar, portalınızın özelleştirmeleri ve karmaşıklığına bağlı olarak değişir. Rehberlik için [Geliştirici Portalı öğreticisine](api-management-howto-developer-portal-customize.md) bakın. API 'Ler, ürünler, kullanıcılar ve kimlik sağlayıcıları gibi kalan yapılandırma her iki Portal arasında otomatik olarak paylaşılır.

> [!IMPORTANT]
> Yeni geliştirici portalını daha önce başlattınız, ancak herhangi bir değişiklik yapmadıysanız, en son sürüme güncelleştirmek için varsayılan içeriği sıfırlayın.

Kullanımdan kaldırılan portaldan geçiş yaptığınızda aşağıdaki değişiklikleri aklınızda bulundurun:

- Geliştirici portalınızı özel bir etki alanı aracılığıyla kullanıma sunadıysanız, yeni geliştirici portalına [bir etki alanı atayın](configure-custom-domain.md) . Azure portal açılır listesinden **Geliştirici Portalı** seçeneğini kullanın.
- Etkileşimli test konsolunu etkinleştirmek için API 'lerinize [BIR CORS Ilkesi uygulayın](developer-portal-faq.md#cors) .
- Portala stil eklemek için özel CSS eklerseniz, stili [yerleşik tasarım panelini kullanarak çoğaltmanız](api-management-howto-developer-portal-customize.md)gerekir. Yeni portalda CSS eklenmesine izin verilmiyor.
- Özel JavaScript 'ı yalnızca [Yeni portalın şirket içinde barındırılan sürümüne](api-management-howto-developer-portal.md#managed-vs-self-hosted)ekleyebilirsiniz.
- API Management bir sanal ağda yer alıyorsa ve Application Gateway aracılığıyla Internet 'e açık ise, kesin yapılandırma adımları için [Bu belge makalesine başvurun](api-management-howto-integrate-internal-vnet-appgateway.md) . Şunları yapmanız gerekir:

    - API Management yönetim uç noktasına bağlantıyı etkinleştirin.
    - Yeni Portal uç noktasına bağlantıyı etkinleştirin.
    - Seçili Web uygulaması güvenlik duvarı kurallarını devre dışı bırakın.

- Varsayılan e-posta bildirim şablonlarını açıkça tanımlanmış bir kaldırılan Portal URL 'SI içerecek şekilde değiştirdiyseniz, bunları Portal URL parametresini kullanacak şekilde değiştirin ya da yeni Portal URL 'sini işaret edin. Şablonlar bunun yerine yerleşik Portal URL parametresini kullanıyorsa, hiçbir değişiklik yapılması gerekmez.
- Yeni geliştirici portalında *sorunlar* ve *uygulamalar* desteklenmez.
- Yeni geliştirici portalında Facebook, Microsoft, Twitter ve Google as kimlik sağlayıcıları ile doğrudan tümleştirme desteklenmez. Bu sağlayıcılarla Azure AD B2C aracılığıyla tümleştirebilirsiniz.
- Temsili kullanıyorsanız, uygulamalarınızda dönüş URL 'sini değiştirin ve *SSO URL 'Si oluştur* uç noktası yerine, [ *paylaşılan erişim belirteci al* API](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) 'sini kullanın.
- Azure AD 'yi bir kimlik sağlayıcısı olarak kullanıyorsanız:

    - Uygulamanızdaki dönüş URL 'sini yeni geliştirici portalı etki alanına işaret etmek üzere değiştirin.
    - Uygulamanızdaki dönüş URL 'sinin sonekini ' dan ' a değiştirin `/signin-aad` `/signin` .

- Kimlik sağlayıcısı olarak Azure AD B2C kullanıyorsanız:

    - Uygulamanızdaki dönüş URL 'sini yeni geliştirici portalı etki alanına işaret etmek üzere değiştirin.
    - Uygulamanızdaki dönüş URL 'sinin sonekini ' dan ' a değiştirin `/signin-aad` `/signin` .
    - *Verilen adı*, *Soyadı* ve *kullanıcının nesne kimliğini* uygulama taleplerine ekleyin.

- Etkileşimli test konsolunda OAuth 2,0 kullanıyorsanız, uygulamanızdaki dönüş URL 'sini yeni geliştirici portalı etki alanına işaret etmek ve soneki değiştirmek için değiştirin:

    - `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` `/signin-oauth/code/callback/[serverName]` Yetkilendirme kodu verme akışı için öğesinden öğesine.
    - `/docs/services/[serverName]/console/oauth2/implicit/callback` `/signin-oauth/implicit/callback` Örtük izin akışı için öğesinden öğesine.
- Etkileşimli test konsolunda OpenID Connect kullanırsanız, uygulamanızdaki dönüş URL 'sini yeni geliştirici portalı etki alanına işaret etmek ve son eki değiştirmek için değiştirin:

    - `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` `/signin-oauth/code/callback/[serverName]` Yetkilendirme kodu verme akışı için öğesinden öğesine.
    - `/docs/services/[serverName]/console/openidconnect/implicit/callback` `/signin-oauth/implicit/callback` Örtük izin akışı için öğesinden öğesine.

## <a name="next-steps"></a>Sonraki adımlar

Geliştirici portalı hakkında daha fazla bilgi edinin:

- [Azure API Management geliştirici portalına genel bakış](api-management-howto-developer-portal.md)
- [Geliştirici portalına erişme ve bunları özelleştirme](api-management-howto-developer-portal-customize.md)