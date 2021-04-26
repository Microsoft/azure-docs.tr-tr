---
title: Ticari Market 'te ücretsiz veya deneme SaaS teklifinizin giriş sayfasını oluşturun
description: Ücretsiz veya deneme SaaS teklifiniz için bir giriş sayfası oluşturmayı öğrenin.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: e7cee47e90e6484a4258ba82e47af03725c41d34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559299"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>Ticari Market 'te ücretsiz veya deneme SaaS teklifinizin giriş sayfasını oluşturun

Bu makalede, Microsoft ticari Market 'te satılacak ücretsiz veya deneme SaaS uygulaması için bir giriş sayfası oluşturma sürecinde size kılavuzluk edilir.

## <a name="overview"></a>Genel Bakış

Giriş sayfasını, hizmet olarak yazılım (SaaS) teklifi için "LOB" olarak düşünebilirsiniz. Müşteri uygulamanızı almayı seçtikten sonra, ticari Market bunları SaaS uygulamanıza yönelik aboneliklerini etkinleştirmek ve yapılandırmak için giriş sayfasına yönlendirir. Hizmet olarak yazılım (SaaS) teklifi oluşturduğunuzda, Iş Ortağı Merkezi 'nde, [Microsoft üzerinden satış](plan-saas-offer.md#listing-options)yapılıp yapılmayacağını seçebilirsiniz. Teklifinizi yalnızca Microsoft ticari Market 'te listelemek ve Microsoft ile satış yapmak istiyorsanız, potansiyel müşterilerin teklifle nasıl etkileşime girebileceği belirtebilirsiniz. **Şimdi al (ücretsiz)** veya **ücretsiz deneme** listesi seçeneğini etkinleştirdiğinizde, kullanıcının ücretsiz aboneliğe veya deneme sürümüne erişmek için gidebileceği BIR giriş sayfası URL 'si belirtmeniz gerekir.

Giriş sayfasının amacı, kullanıcının ücretsiz deneme sürümünü veya ücretsiz aboneliği etkinleştirebilmeleri için yalnızca kullanıcıyı alması yeterlidir. Azure Active Directory (Azure AD) ve Microsoft Graph kullanarak, Kullanıcı için çoklu oturum açma (SSO) özelliğini etkinleştirir ve Kullanıcı hakkında, kendi ad, e-posta adresi ve organizasyonu dahil ücretsiz deneme sürümünü veya ücretsiz aboneliğini etkinleştirmek için kullanabileceğiniz önemli ayrıntıları alırsınız.

Aboneliği etkinleştirmek için gereken bilgiler, Azure AD ve Microsoft Graph tarafından sınırlandırıldı ve sağlandığı için temel onay gerektiren bilgileri isteme gereksinimi olmamalıdır. Uygulamanız için ek onay gerektiren Kullanıcı ayrıntılarına ihtiyacınız varsa, abonelik etkinleştirme tamamlandıktan sonra bu bilgileri istemeniz gerekir. Böylece Kullanıcı için frictionuz abonelik etkinleştirmesi etkinleştirilir ve terk olma riski azalır.

Giriş sayfası genellikle aşağıdaki bilgileri ve listeleme seçeneklerini içerir:

- Ücretsiz deneme veya ücretsiz aboneliğin adını ve ayrıntılarını sunun. Örneğin, kullanım sınırlarını veya deneme süresini belirtin.
- Kullanıcının hesap ayrıntılarını, ilk adı ve soyadı, organizasyonu ve e-posta ile birlikte sunun.
- Kullanıcıdan farklı hesap ayrıntılarını onaylamasını veya yerini sormasını ister.
- Etkinleştirme sonrasında kullanıcıya sonraki adımlarda kılavuzluk edin. Örneğin, bir hoş geldiniz e-postası alın, aboneliği yönetin, destek alın veya belgeleri okuyun.

Bu makaledeki aşağıdaki bölümler, bir giriş sayfası oluşturma işleminde size kılavuzluk eder:

1. Giriş sayfası için [bir Azure AD uygulama kaydı oluşturun](#create-an-azure-ad-app-registration) .
2. Uygulamanız için [Başlangıç noktası olarak bir kod örneği kullanın](#use-a-code-sample-as-a-starting-point) .
3. Oturum açtıktan sonra Azure AD 'den alınan, [kimlik belirtecinde kodlanan taleplerden bilgi okuyun](#read-information-from-claims-encoded-in-the-id-token), bu, istekle birlikte gönderilir.
4. Gerektiğinde ek bilgi toplamak için [MICROSOFT Graph API 'Sini kullanın](#use-the-microsoft-graph-api) .

## <a name="create-an-azure-ad-app-registration"></a>Azure AD uygulama kaydı oluşturma

Ticari Market, Azure AD ile tamamen tümleşiktir. Kullanıcılar bir [Azure AD hesabı veya Microsoft hesabı (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology)ile kimliği doğrulanan Market 'e ulaşır. Yalnızca liste teklifiniz aracılığıyla ücretsiz veya ücretsiz bir deneme aboneliği aldıktan sonra, Kullanıcı, SaaS uygulamanıza yönelik aboneliklerini etkinleştirmek ve yönetmek için ticari Market 'ten giriş sayfası URL 'sine gider. Kullanıcının Azure AD SSO ile uygulamanızda oturum açmasını sağlamalısınız. (Giriş sayfası URL 'SI, teklifin [Teknik yapılandırma](plan-saas-offer.md#technical-information) sayfasında belirtilir.

Kimliği kullanmanın ilk adımı, giriş sayfanızın bir Azure AD uygulaması olarak kaydedildiğinden emin olmak için kullanılır. Uygulamayı kaydetmek, kullanıcıların kimliğini doğrulamak ve Kullanıcı kaynaklarına erişim istemek için Azure AD 'yi kullanmanıza olanak sağlar. Uygulamanın tanım olarak düşünülebilir ve bu da hizmetin uygulama ayarlarına bağlı olarak uygulamaya belirteç verme konusunda bilgi verir.

### <a name="register-a-new-application-using-the-azure-portal"></a>Yeni bir uygulamayı Azure portalını kullanarak kaydetme

Başlamak için [Yeni bir uygulama kaydetme](../active-directory/develop/quickstart-register-app.md)yönergelerini izleyin. Diğer şirketlere ait kullanıcıların uygulamayı ziyaret etmesini sağlamak için, uygulamayı ne zaman kullanabilecelerine ilişkin **bir kurumsal dizinde (herhangi bir Azure ad dizini — çok kiracılı) ve kişisel Microsoft hesaplarından (Skype veya Xbox gibi) hesapları** seçmeniz gerekir.

Microsoft Graph API 'sini sorgulamak istiyorsanız, [Yeni uygulamanızı Web API 'lerine erişecek şekilde yapılandırın](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Bu uygulama için API izinlerini seçtiğinizde, **Kullanıcı. Read** 'in varsayılan örneği, ekleme işlemini sorunsuz ve otomatik hale getirmek için Kullanıcı hakkındaki temel bilgileri toplamak üzere yeterlidir. Yönetici olmayan tüm kullanıcıların giriş sayfanızı ziyaret etmesini engelleyecek şekilde, yönetici **izni olması gereken** HERHANGI bir API izni isteme.

Ekleme veya sağlama işleminizin bir parçası olarak yükseltilmiş izinlere ihtiyacınız varsa, marketten gönderilen tüm kullanıcıların başlangıçta giriş sayfasıyla etkileşime girebilmesi için Azure AD 'nin [artımlı onay](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) işlevlerini kullanmayı düşünün.

## <a name="use-a-code-sample-as-a-starting-point"></a>Başlangıç noktası olarak bir kod örneği kullanma

Microsoft, Azure AD oturum açma özelliği etkinken basit bir Web sitesi uygulayan birkaç örnek uygulama sağlamıştır. Uygulamanız Azure AD 'ye kaydedildikten sonra, **hızlı başlangıç** dikey penceresi ortak uygulama türlerinin ve geliştirme yığınlarının bir listesini sunar (Şekil 1). Ortamınızla eşleşen birini seçin ve indirme ve kurulum yönergelerini izleyin.

***Şekil 1: Azure portal hızlı başlangıç dikey penceresi***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Azure portal hızlı başlangıç dikey penceresini gösterir.":::

Kodu indirip geliştirme ortamınızı ayarladıktan sonra, uygulamadaki yapılandırma ayarlarını uygulama KIMLIĞI, kiracı KIMLIĞI ve önceki yordamda kaydettiğiniz istemci gizliliğini yansıtacak şekilde değiştirin. Tam adımların hangi örneğe kullandığınıza bağlı olarak farklı olacağını unutmayın.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>KIMLIK belirtecinde kodlanan taleplerden bilgi okuyun

[OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) Flow 'un bir parçası olarak, Azure AD, Kullanıcı giriş sayfasına gönderildiğinde Isteğe bir [kimlik belirteci](../active-directory/develop/id-tokens.md) ekler. Bu belirteç, bu tabloda görülen bilgiler de dahil olmak üzere etkinleştirme işleminde yararlı olabilecek birçok temel bilgi parçasını içerir.

| Değer | Açıklama |
| ------------ | ------------- |
| aud | Bu belirteç için amaçlanan hedef kitle. Bu durumda, uygulama KIMLIĞINIZLE eşleşmesi ve doğrulanması gerekir. |
| preferred_username | Ziyaret eden kullanıcının birincil Kullanıcı adı. Bu bir e-posta adresi, telefon numarası veya başka bir tanımlayıcı olabilir. |
| e-posta | Kullanıcının e-posta adresi. Bu alanın boş olabileceğini unutmayın. |
| name | Belirtecin konusunu tanımlayan insan tarafından okunabilen değer. Bu durumda, kullanıcının adı olacaktır. |
| id | Microsoft Identity sisteminde, kullanıcıyı uygulamalar arasında benzersiz bir şekilde tanımlayan tanımlayıcı. Microsoft Graph, bu değeri belirli bir kullanıcı hesabı için ID özelliği olarak döndürür. |
| değeri | Kullanıcının kimden olduğu Azure AD kiracısını temsil eden tanımlayıcı. MSA kimliği söz konusu olduğunda, bu her zaman olur `9188040d-6c67-4c5b-b112-36a304b66dad` . Daha fazla bilgi için sonraki bölümdeki nota bakın: Microsoft Graph API kullanın. |
| alt | Bu özel uygulamada kullanıcıyı benzersiz bir şekilde tanımlayan tanımlayıcı. |
|||

## <a name="use-the-microsoft-graph-api"></a>Microsoft Graph API’sini kullanma

KIMLIK belirteci, kullanıcıyı tanımlamak için temel bilgileri içerir, ancak etkinleştirme işleminiz ekleme işlemini tamamlamaya yönelik ek ayrıntılar gerektirebilir (örneğin, kullanıcının şirketi). Kullanıcı bu ayrıntıları yeniden girmemek için bu bilgileri istemek üzere [MICROSOFT Graph API](/graph/use-the-api) 'sini kullanın. Standart **Kullanıcı. okuma** izinleri, varsayılan olarak aşağıdaki bilgileri içerir:

| Değer | Açıklama |
| ------------ | ------------- |
| displayName | Kullanıcının adres defterinde görünen ad. |
| givenName | Kullanıcının ilk adı. |
| jobTitle | Kullanıcının iş unvanı. |
| posta | Kullanıcının SMTP adresi. |
| mobilePhone | Kullanıcının birincil cep telefonu numarası. |
| preferredLanguage | Kullanıcının tercih ettiği dil için ISO 639-1 kodu. |
| surname | Kullanıcının soyadı. |
|||

Kullanıcı şirketinin adı veya Kullanıcı konumu (ülke) gibi ek özellikler, isteğe eklenmek üzere seçilebilir. Daha ayrıntılı bilgi için bkz. [Kullanıcı kaynak türü özellikleri](/graph/api/resources/user#properties).

Azure AD 'ye kayıtlı uygulamaların çoğu, kullanıcının şirketinin Azure AD kiracısından bilgilerini okumak için temsilci izinleri verir. Bu bilgilere yönelik Microsoft Graph yönelik istekler, kimlik doğrulama olarak bir erişim belirteciyle birlikte gelmelidir. Erişim belirtecini oluşturmaya yönelik belirli adımlar, kullanmakta olduğunuz teknoloji yığınına bağlıdır, ancak örnek kod bir örnek içerir. Daha fazla bilgi için bkz. [Kullanıcı adına erişim edinme](/graph/auth-v2-user).

> [!NOTE]
> MSA kiracısından (kiracı KIMLIĞI olan) hesaplar, `9188040d-6c67-4c5b-b112-36a304b66dad` kimlik belirteciyle toplanmamış olandan daha fazla bilgi döndürmez. Bu nedenle, bu hesaplar için Graph API bu çağrıyı atlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
- [Ticari Market 'te SaaS teklifi oluşturma](create-new-saas-offer.md)