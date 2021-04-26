---
title: Ticari Market 'te transactable SaaS teklifinizin giriş sayfasını oluşturun
description: Transactable SaaS teklifiniz için bir giriş sayfası oluşturmayı öğrenin.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 4bfc29472373a53bcebb2ba59134d1f3702d4793
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549881"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>Ticari Market 'te transactable SaaS teklifinizin giriş sayfasını oluşturun

Bu makalede, Microsoft ticari Market 'te satılacak bir transactable SaaS uygulaması için giriş sayfası oluşturma sürecinde size kılavuzluk edilir.

## <a name="overview"></a>Genel Bakış

Giriş sayfasını, hizmet olarak yazılım (SaaS) teklifi için "LOB" olarak düşünebilirsiniz. Alıcı bir teklifine abone olduktan sonra, ticari Market bunları SaaS uygulamanıza yönelik aboneliklerini etkinleştirmek ve yapılandırmak için giriş sayfasına yönlendirir. Bunu, alıcının satın aldıkları öğeleri görmesini ve hesap ayrıntılarını onaylamasını sağlayan bir sipariş onay adımı olarak düşünün. Azure Active Directory (Azure AD) ve Microsoft Graph kullanarak, alıcı için çoklu oturum açma (SSO) özelliğini etkinleştirir ve kendi ad, e-posta adresi ve organizasyonu dahil olmak üzere aboneliğini onaylamak ve etkinleştirmek için kullanabileceğiniz, alıcı hakkındaki önemli ayrıntıları alacaksınız.

Aboneliği etkinleştirmek için gereken bilgiler, Azure AD ve Microsoft Graph tarafından sınırlandırıldı ve sağlandığı için temel onay gerektiren bilgileri isteme gereksinimi olmamalıdır. Uygulamanız için ek onay gerektiren Kullanıcı ayrıntılarına ihtiyacınız varsa, abonelik etkinleştirme tamamlandıktan sonra bu bilgileri istemeniz gerekir. Bu, alıcı için frictionsız abonelik etkinleştirmesini ve bırakma riskini azaltır.

Giriş sayfası genellikle aşağıdakileri içerir:

- Teklifin ve satın alınan planın adını ve fatura şartlarını sunun.
- Birinci ve soyadı, kuruluş ve e-posta gibi alıcının hesap ayrıntılarını sunun.
- Alıcının farklı hesap ayrıntılarını onaylamasını veya yerini sormasını ister.
- Etkinleştirdikten sonra sonraki adımlarda alıcıya kılavuzluk edin. Örneğin, bir hoş geldiniz e-postası alın, aboneliği yönetin, destek alın veya belgeleri okuyun.

> [!NOTE]
> Ayrıca, bu alıcı, etkinleştirildikten sonra aboneliğini yönetirken giriş sayfasına yönlendirilir. Alıcının aboneliği etkinleştirildikten sonra, kullanıcının oturum açmasını sağlamak için SSO 'yu kullanmanız gerekir. Kullanıcının bir hesap profiline veya yapılandırma sayfasına yönlendirilmiş olması önerilir.

Aşağıdaki bölümler, bir giriş sayfası oluşturma işleminde size kılavuzluk eder:

1. Giriş sayfası için [bir Azure AD uygulama kaydı oluşturun](#create-an-azure-ad-app-registration) .
1. Uygulamanız için [Başlangıç noktası olarak bir kod örneği kullanın](#use-a-code-sample-as-a-starting-point) .
1. [Üretimde güvenliği geliştirmek için Iki Azure AD uygulaması kullanın](#use-two-azure-ad-apps-to-improve-security-in-production).
1. Ticari Market tarafından URL 'ye eklenen [Market satın alma tanımlama belirtecini çözümleyin](#resolve-the-marketplace-purchase-identification-token) .
1. Oturum açtıktan sonra Azure AD 'den alınan, [kimlik belirtecinde kodlanan taleplerden bilgi okuyun](#read-information-from-claims-encoded-in-the-id-token), bu, istekle birlikte gönderilir.
1. Gerektiğinde ek bilgi toplamak için [MICROSOFT Graph API 'Sini kullanın](#use-the-microsoft-graph-api) .

## <a name="create-an-azure-ad-app-registration"></a>Azure AD uygulama kaydı oluşturma

Ticari Market, Azure AD ile tamamen tümleşiktir. Alıcılar bir [Azure AD hesabı veya Microsoft hesabı (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology)ile kimliği doğrulanan Market 'e ulaşır. Satın alma işleminden sonra alıcı, SaaS uygulamanızın aboneliğini etkinleştirmek ve yönetmek için ticari Market 'ten giriş sayfası URL 'sine gider. Alıcının Azure AD SSO ile uygulamanızda oturum açmasını sağlamalısınız. (Giriş sayfası URL 'SI, teklifin [Teknik yapılandırma](plan-saas-offer.md#technical-information) sayfasında belirtilir.

Kimliği kullanmanın ilk adımı, giriş sayfanızın bir Azure AD uygulaması olarak kaydedildiğinden emin olmak için kullanılır. Uygulamayı kaydetmek, kullanıcıların kimliğini doğrulamak ve Kullanıcı kaynaklarına erişim istemek için Azure AD 'yi kullanmanıza olanak sağlar. Uygulamanın tanım olarak düşünülebilir ve bu da hizmetin uygulama ayarlarına bağlı olarak uygulamaya belirteç verme konusunda bilgi verir.

### <a name="register-a-new-application-using-the-azure-portal"></a>Yeni bir uygulamayı Azure portalını kullanarak kaydetme

Başlamak için [Yeni bir uygulama kaydetme](../active-directory/develop/quickstart-register-app.md)yönergelerini izleyin. Diğer şirketlere ait kullanıcıların uygulamayı ziyaret etmesini sağlamak için, uygulamayı ne zaman kullanabilecebir sorulduğunda çok kiracılı seçeneklerinden birini seçmeniz gerekir.

Microsoft Graph API 'sini sorgulamak istiyorsanız, [Yeni uygulamanızı Web API 'lerine erişecek şekilde yapılandırın](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Bu uygulama için API izinlerini seçtiğinizde, **Kullanıcı. Read** 'in varsayılanı, ekleme işlemini sorunsuz ve otomatik hale getirmek için alıcı hakkındaki temel bilgileri toplamak üzere yeterlidir. Yönetici olmayan tüm kullanıcıların giriş sayfanızı ziyaret etmesini engelleyecek şekilde, yönetici **izni olması gereken** HERHANGI bir API izni isteme.

Hazırlama veya sağlama işleminizin bir parçası olarak yükseltilmiş izinlere ihtiyacınız varsa, Market 'ten gönderilen tüm alıcıların başlangıçta giriş sayfasıyla etkileşime girebilmesi için Azure AD 'nin [artımlı onay](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) işlevlerini kullanmayı düşünün.

## <a name="use-a-code-sample-as-a-starting-point"></a>Başlangıç noktası olarak bir kod örneği kullanma

Azure AD oturum açma özelliği etkinken basit bir Web sitesi uygulayan birkaç örnek uygulama sunuyoruz. Uygulamanız Azure AD 'ye kaydedildikten sonra, **hızlı başlangıç** dikey penceresi Şekil 1 ' de görüldüğü gibi ortak uygulama türlerinin ve geliştirme yığınlarının bir listesini sunar. Ortamınızla eşleşen birini seçin ve indirme ve kurulum yönergelerini izleyin.

***Şekil 1: Azure portal hızlı başlangıç dikey penceresi***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Azure portal hızlı başlangıç dikey penceresini gösterir.":::

Kodu indirip geliştirme ortamınızı ayarladıktan sonra, uygulamadaki yapılandırma ayarlarını uygulama KIMLIĞI, kiracı KIMLIĞI ve önceki yordamda kaydettiğiniz istemci gizliliğini yansıtacak şekilde değiştirin. Tam adımların hangi örneğe kullandığınıza bağlı olarak farklı olacağını unutmayın.

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>Üretimde güvenliği geliştirmek için iki Azure AD uygulaması kullanın

Bu makalede, ticari Market SaaS teklifiniz için bir giriş sayfası uygulamak üzere mimarinin basitleştirilmiş bir sürümü sunulmaktadır. Sayfayı üretimde çalıştırırken, yalnızca farklı, güvenli bir uygulama aracılığıyla SaaS ile ilgili API 'Lerle iletişim kurarak güvenliği iyileştireöneririz. Bunun için iki yeni uygulama oluşturulması gerekir:

- İlk olarak, bu noktaya kadar açıklanan çok kiracılı giriş sayfası uygulaması, SaaS ile ilgili API 'Lerle iletişim kurma işlevselliği olmadan açıklanmıştır. Bu işlevsellik, aşağıda açıklandığı gibi başka bir uygulamaya boşaltılır.
- İkincisi, SaaS ile ilgili API 'lerle iletişim kurmak için bir uygulamadır. Bu uygulama tek kiracılı olmalıdır ve yalnızca kuruluşunuz tarafından kullanılması gerekir ve API 'lere erişimi yalnızca bu uygulamadan sınırlamak için bir erişim denetim listesi oluşturulabilir.

Bu, çözümün [endişeleri ayırmayı](/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) gözlemleyecek senaryolarda çalışmasını sağlar. Örneğin, giriş sayfası Kullanıcı oturumu açmak için ilk kayıtlı Azure AD uygulamasını kullanır. Kullanıcı oturum açtıktan sonra, giriş sayfası ikinci Azure AD ' yi kullanarak SaaS API 'sini çağırıp bir erişim belirteci ister ve çözümle işlemini çağırır.

## <a name="resolve-the-marketplace-purchase-identification-token"></a>Market satın alma tanımlama belirtecini çözümle

Alıcı, giriş sayfanıza gönderildiğinde, URL parametresine bir belirteç eklenir. Bu belirteç hem Azure AD tarafından verilen belirteç hem de hizmetten hizmete kimlik doğrulaması için kullanılan erişim belirtecinden farklıdır ve [SaaS tamamlama API 'leri](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) için bir giriş olarak kullanılır ve aboneliğin ayrıntılarını almak için çağrıyı çözün. SaaS 'nin birlikte getirilmesi API 'Lerinin tüm çağrılarında olduğu gibi, hizmetten hizmete isteğiniz, uygulamanın Azure AD uygulama KIMLIĞI kullanıcısına hizmetten hizmete kimlik doğrulaması için temel alan bir erişim belirteciyle kimlik doğrulamasından geçer.

> [!NOTE]
> Çoğu durumda, bu çağrıyı ikinci bir tek kiracılı uygulamadan yapmak tercih edilir. Bu makalenin önceki kısımlarında [üretimde güvenliği artırmak için bkz. Iki Azure AD uygulaması kullanma](#use-two-azure-ad-apps-to-improve-security-in-production) .

### <a name="request-an-access-token"></a>Erişim belirteci isteme

SaaS 'nin birlikte getirilmesi API 'Leri ile uygulamanızın kimliğini doğrulamak için Azure AD OAuth uç noktası çağırarak oluşturulabilecek bir erişim belirtecine ihtiyacınız vardır. [Yayımcının yetkilendirme belirtecini alma](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)bölümüne bakın.

### <a name="call-the-resolve-endpoint"></a>Çözümleme bitiş noktasını çağırma

SaaS karşılama API 'Leri, Market belirtecinin geçerliliğini onaylamak ve abonelik hakkında bilgi döndürmek için çağrılabilecek [Çözümleme](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) bitiş noktasını uygular.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>KIMLIK belirtecinde kodlanan taleplerden bilgi okuyun

[OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) Flow 'un bir parçası olarak, Azure AD, alıcı giriş sayfasına gönderildiğinde Isteğe bir [kimlik belirteci](../active-directory/develop/id-tokens.md) ekler. Bu belirteç, bu tabloda görülen bilgiler de dahil olmak üzere etkinleştirme işleminde yararlı olabilecek birçok temel bilgi parçasını içerir.

| Değer | Açıklama |
| ------------ | ------------- |
| aud | Bu belirteç için amaçlanan hedef kitle. Bu durumda, uygulama KIMLIĞINIZLE eşleşmesi ve doğrulanması gerekir. |
| preferred_username | Ziyaret eden kullanıcının birincil Kullanıcı adı. Bu bir e-posta adresi, telefon numarası veya başka bir tanımlayıcı olabilir. |
| e-posta | Kullanıcının e-posta adresi. Bu alanın boş olabileceğini unutmayın. |
| name | Belirtecin konusunu tanımlayan insan tarafından okunabilen değer. Bu durumda, alıcının adı olacaktır. |
| id | Microsoft Identity sisteminde, kullanıcıyı uygulamalar arasında benzersiz bir şekilde tanımlayan tanımlayıcı. Microsoft Graph, bu değeri belirli bir kullanıcı hesabı için ID özelliği olarak döndürür. |
| değeri | Alıcının kimden olduğu Azure AD kiracısını temsil eden tanımlayıcı. MSA kimliği söz konusu olduğunda, bu her zaman olur ``9188040d-6c67-4c5b-b112-36a304b66dad`` . Daha fazla bilgi için sonraki bölümdeki nota bakın: Microsoft Graph API 'sini kullanın. |
| alt | Bu özel uygulamada kullanıcıyı benzersiz bir şekilde tanımlayan tanımlayıcı. |
|||

## <a name="use-the-microsoft-graph-api"></a>Microsoft Graph API’sini kullanma

KIMLIK belirteci, alıcıya yönelik temel bilgileri içerir, ancak etkinleştirme işleminiz ekleme işlemini tamamlamaya yönelik ek ayrıntılar gerektirebilir (örneğin, alıcının şirketi). Kullanıcı bu ayrıntıları yeniden girmemek için bu bilgileri istemek üzere [MICROSOFT Graph API](/graph/use-the-api) 'sini kullanın. Standart **Kullanıcı. okuma** izinleri, varsayılan olarak aşağıdaki bilgileri içerir.

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

Kullanıcı şirketinin adı veya Kullanıcı konumu (ülke) gibi ek özellikler, isteğe eklenmek üzere seçilebilir. Daha fazla ayrıntı için [Kullanıcı kaynak türü özelliklerine](/graph/api/resources/user#properties) bakın.

Azure AD 'ye kayıtlı uygulamaların çoğu, kullanıcının şirketinin Azure AD kiracısından bilgilerini okumak için temsilci izinleri verir. Bu bilgilere yönelik Microsoft Graph yönelik istekler, kimlik doğrulaması için bir erişim belirteciyle birlikte gelmelidir. Erişim belirtecini oluşturmaya yönelik belirli adımlar, kullanmakta olduğunuz teknoloji yığınına bağlıdır, ancak örnek kod bir örnek içerir. Daha fazla bilgi için bkz. [Kullanıcı adına erişim edinme](/graph/auth-v2-user).

> [!NOTE]
> MSA kiracısından (kiracı KIMLIĞI olan) hesaplar, ``9188040d-6c67-4c5b-b112-36a304b66dad`` kimlik belirteciyle toplanmamış olandan daha fazla bilgi döndürmez. Bu nedenle, bu hesaplar için Graph API bu çağrıyı atlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market 'te SaaS teklifi oluşturma](create-new-saas-offer.md)