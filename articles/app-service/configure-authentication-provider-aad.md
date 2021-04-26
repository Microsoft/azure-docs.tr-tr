---
title: Azure AD kimlik doğrulamasını yapılandırma
description: Azure Active Directory kimlik doğrulamasını App Service veya Azure Işlevleri uygulamanız için bir kimlik sağlayıcısı olarak yapılandırmayı öğrenin.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: b1254e7db0e62d08ea2a3d6d30f2abd379675c55
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078324"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>App Service veya Azure Işlevleri uygulamanızı Azure AD oturum açma bilgilerini kullanacak şekilde yapılandırma

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Bu makalede, uygulamanızın kimlik doğrulama sağlayıcısı olarak [Microsoft Identity platform](../active-directory/develop/v2-overview.md) (Azure AD) ile oturum açması için Azure App Service veya Azure işlevleri için kimlik doğrulamanın nasıl yapılandırılacağı gösterilmektedir.

App Service kimlik doğrulaması özelliği, Microsoft Identity platformu ile otomatik olarak bir uygulama kaydı oluşturabilir. Ayrıca sizin veya bir dizin yöneticisinin ayrı olarak oluşturduğu bir kayıt da kullanabilirsiniz.

- [Otomatik olarak yeni bir uygulama kaydı oluşturun](#express)
- [Ayrı olarak oluşturulan mevcut bir kaydı kullanın](#advanced)

> [!NOTE]
> Yeni kayıt oluşturma seçeneği, kamu bulutları için kullanılamaz. Bunun yerine, [bir kaydı ayrı olarak tanımlayın](#advanced).

## <a name="create-a-new-app-registration-automatically"></a><a name="express"> </a>Otomatik olarak yeni bir uygulama kaydı oluşturun

Bu seçenek kimlik doğrulamanın basit olmasını sağlamak için tasarlanmıştır ve yalnızca birkaç tıklama gerektirir.

1. [Azure Portal] oturum açın ve uygulamanıza gidin.
1. Soldaki menüden **kimlik doğrulaması** ' nı seçin. **Kimlik sağlayıcısı ekle**' ye tıklayın.
1. Kimlik sağlayıcısı açılan menüsünde **Microsoft** ' u seçin. Yeni kayıt oluşturma seçeneği varsayılan olarak seçilidir. Kaydın adını veya desteklenen hesap türlerini değiştirebilirsiniz.

    Bir istemci parolası oluşturulup adlı bir yuva yapışkan [uygulama ayarı](./configure-common.md#configure-app-settings) olarak depolanır `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` . Azure Key Vault gizli anahtarı yönetmek istiyorsanız bu ayarı daha sonra [Key Vault başvurularını](./app-service-key-vault-references.md) kullanacak şekilde güncelleştirebilirsiniz.

1. Uygulama için yapılandırılan ilk kimlik sağlayıcısıysanız, **App Service kimlik doğrulama ayarları** bölümü de istenir. Aksi halde, bir sonraki adıma geçebilirsiniz.
    
    Bu seçenekler, uygulamanızın kimliği doğrulanmamış isteklere nasıl yanıt vereceğini ve varsayılan seçimlerin bu yeni sağlayıcı ile oturum açmak için tüm istekleri yeniden yönlenceğini belirtir. Bu davranışı şimdi özelleştirmeyi, **kimlik doğrulama ayarları**' nın yanındaki **Düzenle** ' ye tıklayarak bu ayarları daha sonra ana **kimlik doğrulama** ekranından ayarlayabilirsiniz. Bu seçenekler hakkında daha fazla bilgi edinmek için bkz. [kimlik doğrulama akışı](overview-authentication-authorization.md#authentication-flow).

1. Seçim **İleri** ' ye tıklayın ve uygulama için gereken tüm kapsamları ekleyin. Bunlar uygulama kaydına eklenecektir, ancak daha sonra da değiştirebilirsiniz.
1. **Ekle**'ye tıklayın.

Artık uygulamanızda kimlik doğrulaması için Microsoft Identity platformunu kullanmaya hazırsınız. Sağlayıcı, **kimlik doğrulama** ekranında listelenecektir. Buradan, bu sağlayıcı yapılandırmasını düzenleyebilir veya silebilirsiniz.

Azure depolama ve Microsoft Graph erişen bir Web uygulaması için Azure AD oturum açma yapılandırması örneği için [Bu öğreticiye](scenario-secure-app-authentication-app-service.md)bakın.

## <a name="use-an-existing-registration-created-separately"></a><a name="advanced"> </a>Ayrı olarak oluşturulan mevcut bir kaydı kullanın

Ayrıca, Microsoft Identity platformu için uygulamanızı el ile kaydedebilir, kaydı özelleştirip App Service kimlik doğrulamasını kayıt ayrıntılarıyla yapılandırabilirsiniz. Bu, örneğin, farklı bir Azure AD kiracısından uygulamanızın içinde yer aldığı bir uygulama kaydını kullanmak istiyorsanız yararlıdır.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>App Service uygulamanız IÇIN Azure AD 'de bir uygulama kaydı oluşturma

İlk olarak, uygulama kaydınızı oluşturacaksınız. Bunu yaparken, App Service uygulamasında kimlik doğrulamasını yapılandırırken daha sonra ihtiyacınız olacak aşağıdaki bilgileri toplayın:

- İstemci Kimliği
- Kiracı Kimliği
- İstemci parolası (isteğe bağlı)
- Uygulama KIMLIĞI URI 'SI

Uygulamayı kaydetmek için aşağıdaki adımları uygulayın:

1. [Azure Portal]oturum açın, **uygulama hizmetleri**' ni arayıp seçin ve ardından uygulamanızı seçin. Uygulamanızın **URL 'sini** aklınızda edin. Azure Active Directory Uygulama kaydınızı yapılandırmak için kullanacaksınız.
1. Portal menüsünden **Azure Active Directory**' i seçin, sonra **uygulama kayıtları** sekmesine gidin ve **Yeni kayıt**' ı seçin.
1. **Uygulama kaydetme** sayfasında, uygulama kaydınız Için bir **ad** girin.
1. **Yeniden yönlendirme URI 'si** içinde **Web** ' i seçin ve yazın `<app-url>/.auth/login/aad/callback` . Örneğin, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. **Kaydet**’i seçin.
1. Uygulama kaydı oluşturulduktan sonra, daha sonra için **uygulama (istemci) kimliğini** ve **Dizin (kiracı) kimliğini** kopyalayın.
1. **Kimlik Doğrulaması**'nı seçin. **Örtük izin**' ın altında, App Service **kimlik belirteçlerini** , OpenID kullanıcı oturum açma işlemlerinin izin verecek şekilde etkinleştirin.
1. Seçim **Marka** seçin. **Giriş sayfası URL 'si**' nde App Service uygulamanızın URL 'sini girin ve **Kaydet**' i seçin.
1. **Bir API kümesini kullanıma** sunma ' yı seçin  >  . Tek kiracılı uygulama için App Service uygulamanızın URL 'sini yapıştırın ve **Kaydet** ' i seçin ve çok kiracılı uygulama için, kiracı tarafından doğrulanan etki alanlarından birini temel alan URL 'yi yapıştırın ve ardından **Kaydet**' i seçin.

   > [!NOTE]
   > Bu değer, uygulama kaydının **uygulama kimliği URI 'sidir** . Web uygulamanız buluttaki bir API 'ye erişim gerektiriyorsa, bulut App Service kaynağını yapılandırırken Web uygulamasının **uygulama kimliği URI 'sine** ihtiyacınız vardır. Bunu, örneğin, bulut hizmetinin Web uygulamasına açıkça erişim vermesini istiyorsanız kullanabilirsiniz.

1. **Kapsam ekle**’yi seçin.
   1. **Kapsam adı** alanına *user_impersonation* girin.
   1. Metin kutularına kullanıcıların onay sayfasında görmesini istediğiniz izin kapsam adını ve açıklamasını girin. Örneğin, *uygulamama erişim*' i girin.
   1. **Kapsam Ekle**' yi seçin.
1. Seçim Bir istemci parolası oluşturmak için **Sertifikalar & gizli**  >  **anahtar Ekle yeni istemci parolası**' nı seçin  >  . Sayfada gösterilen istemci gizli değerini kopyalayın. Yeniden gösterilmeyecektir.
1. Seçim Birden çok **yanıt URL 'si** eklemek Için **kimlik doğrulaması**' nı seçin.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>App Service uygulamanızda Azure Active Directory etkinleştirme

1. [Azure Portal] oturum açın ve uygulamanıza gidin.
1. Soldaki menüden **kimlik doğrulaması** ' nı seçin. **Kimlik sağlayıcısı ekle**' ye tıklayın.
1. Kimlik sağlayıcısı açılan menüsünde **Microsoft** ' u seçin.
1. **Uygulama kayıt türü** için, gerekli uygulama bilgilerini otomatik olarak toplayan **Bu dizinde var olan bir uygulama kaydını seçmeyi** seçebilirsiniz. Kaydınız başka bir kiracıya ait ise veya kayıt nesnesini görüntüleme izniniz yoksa, **var olan bir uygulama kaydının ayrıntılarını sağla**' yı seçin. Bu seçenek için aşağıdaki yapılandırma ayrıntılarını doldurmanız gerekir:

    |Alan|Açıklama|
    |-|-|
    |Uygulama (istemci) kimliği| Uygulama kaydının **uygulama (istemci) kimliğini** kullanın. |
    |İstemci parolası (Isteğe bağlı)| Uygulama kaydında oluşturduğunuz istemci gizli anahtarını kullanın. İstemci gizli anahtarı ile karma akış kullanılır ve App Service erişim ve yenileme belirteçleri döndürür. İstemci parolası ayarlanmamışsa, örtük akış kullanılır ve yalnızca bir kimlik belirteci döndürülür. Bu belirteçler sağlayıcı tarafından gönderilir ve EasyAuth belirteç deposunda depolanır.|
    |Veren URL 'Si| `<authentication-endpoint>/<tenant-id>/v2.0`' İ kullanın ve, *\<authentication-endpoint>* [bulut ortamınız için kimlik doğrulama uç noktası](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) ile değiştirin (örneğin, " https://login.microsoftonline.com Genel Azure için"), Ayrıca, *\<tenant-id>* uygulama KAYDıNıN oluşturulduğu **Dizin (kiracı) kimliğiyle** değiştirin. Bu değer, kullanıcıları doğru Azure AD kiracısına yönlendirmek için ve ilgili meta verileri, uygun belirteç imzalama anahtarları ve belirteç verenin talep değerini öğrenmek için indirmek için kullanılır. Azure AD v1 ve Azure Işlevleri uygulamaları için kullanılan uygulamalar için `/v2.0` URL 'de atlayın.|
    |İzin verilen belirteç Izleyicileri| Bu bir bulut veya sunucu uygulaması ise ve bir Web uygulamasından kimlik doğrulama belirteçlerine izin vermek istiyorsanız, Web uygulamasının **uygulama KIMLIĞI URI** 'sini buraya ekleyin. Yapılandırılan **ISTEMCI kimliği** *her zaman* örtük olarak izin verilen bir hedef kitle olarak kabul edilir.|

    İstemci parolası adlı bir yuva yapışkan [uygulama ayarı](./configure-common.md#configure-app-settings) olarak depolanır `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` . Azure Key Vault gizli anahtarı yönetmek istiyorsanız bu ayarı daha sonra [Key Vault başvurularını](./app-service-key-vault-references.md) kullanacak şekilde güncelleştirebilirsiniz.

1. Uygulama için yapılandırılan ilk kimlik sağlayıcısıysanız, **App Service kimlik doğrulama ayarları** bölümü de istenir. Aksi halde, bir sonraki adıma geçebilirsiniz.
    
    Bu seçenekler, uygulamanızın kimliği doğrulanmamış isteklere nasıl yanıt vereceğini ve varsayılan seçimlerin bu yeni sağlayıcı ile oturum açmak için tüm istekleri yeniden yönlenceğini belirtir. Bu davranışı şimdi özelleştirmeyi, **kimlik doğrulama ayarları**' nın yanındaki **Düzenle** ' ye tıklayarak bu ayarları daha sonra ana **kimlik doğrulama** ekranından ayarlayabilirsiniz. Bu seçenekler hakkında daha fazla bilgi edinmek için bkz. [kimlik doğrulama akışı](overview-authentication-authorization.md#authentication-flow).

1. **Ekle**'ye tıklayın.

Artık uygulamanızda kimlik doğrulaması için Microsoft Identity platformunu kullanmaya hazırsınız. Sağlayıcı, **kimlik doğrulama** ekranında listelenecektir. Buradan, bu sağlayıcı yapılandırmasını düzenleyebilir veya silebilirsiniz.

## <a name="configure-client-apps-to-access-your-app-service"></a>İstemci uygulamalarını App Service erişmek için yapılandırma

Önceki bölümde, kullanıcıların kimliğini doğrulamak için App Service veya Azure işlevinizi kaydettiniz. Bu bölümde, yerel istemci veya Daemon uygulamalarının Kullanıcı adına veya kendilerine ait App Service tarafından açığa çıkarılan API 'lere erişim isteyebilecekleri şekilde nasıl kaydedileceği açıklanmaktadır. Yalnızca kullanıcıların kimliğini doğrulamak istiyorsanız, bu bölümdeki adımları tamamlamak gerekli değildir.

### <a name="native-client-application"></a>Yerel istemci uygulaması

Oturum açmış bir kullanıcı adına App Service uygulamanızın API 'Lerine erişim istemek için yerel istemcileri kaydedebilirsiniz.

1. [Azure Portal]   >    >  **Yeni kayıt** uygulama kayıtları Active Directory seçin.
1. **Uygulama kaydetme** sayfasında, uygulama kaydınız Için bir **ad** girin.
1. **Yeniden yönlendirme URI 'si** içinde **ortak istemci (mobil & Masaüstü)** öğesini seçin ve URL 'yi yazın `<app-url>/.auth/login/aad/callback` . Örneğin, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Microsoft Store bir uygulama için, bunun yerine URI olarak [paket SID](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid) 'sini kullanın.
1. **Oluştur**’u seçin.
1. Uygulama kaydı oluşturulduktan sonra, **uygulama (istemci) kimliği** değerini kopyalayın.
1. **API izinleri** Seç  >    >  **API 'lerim** izin Ekle.
1. Daha önce App Service uygulamanız için oluşturduğunuz uygulama kaydını seçin. Uygulama kaydını görmüyorsanız, [App Service uygulamanız Için Azure AD 'de uygulama kaydı oluşturma](#register)bölümüne **user_impersonation** kapsamını eklediğinizden emin olun.
1. **Temsilci izinleri** altında **user_impersonation**' yi seçin ve ardından **izin Ekle**' yi seçin.

Artık App Service uygulamanıza Kullanıcı adına erişim isteyebileceğini bir yerel istemci uygulaması yapılandırdınız.

### <a name="daemon-client-application-service-to-service-calls"></a>Daemon istemci uygulaması (hizmetten hizmete çağrılar)

Uygulamanız, App Service veya Işlev uygulamanızda barındırılan bir Web API 'sini kendi adına (Kullanıcı adına değil) çağırmak için bir belirteç alabilir. Bu senaryo, oturum açmış bir kullanıcı olmadan görevleri gerçekleştiren Etkileşimli olmayan Daemon uygulamaları için yararlıdır. Standart OAuth 2,0 [istemci kimlik bilgileri](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) izni ' nı kullanır.

1. [Azure Portal]   >    >  **Yeni kayıt** uygulama kayıtları Active Directory seçin.
1. **Uygulama kaydetme** sayfasında, Daemon uygulama kaydınız Için bir **ad** girin.
1. Bir Daemon uygulaması için bir yeniden yönlendirme URI 'SI gerekmez, bu da boş bırakabilirsiniz.
1. **Oluştur**’u seçin.
1. Uygulama kaydı oluşturulduktan sonra, **uygulama (istemci) kimliği** değerini kopyalayın.
1. **Sertifikalar & gizli**  >  **anahtar Ekle yeni istemci parolası**' nı seçin  >  . Sayfada gösterilen istemci gizli değerini kopyalayın. Yeniden gösterilmeyecektir.

Artık, parametreyi hedef uygulamanın uygulama KIMLIĞI URI 'sine ayarlayarak [ISTEMCI kimliğini ve istemci gizli anahtarını kullanarak bir erişim belirteci isteyebilirsiniz](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) `resource` .  Elde edilen erişim belirteci daha sonra, standart [OAuth 2,0 yetkilendirme üst bilgisi](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource)kullanılarak hedef uygulamaya sunulabilir ve App Service kimlik doğrulaması/yetkilendirme, Şimdi çağıranın (Bu durumda bir kullanıcı değil) kimliğinin doğrulandığını göstermek için belirteci doğrular ve kullanır.

Bu durumda, Azure AD kiracınızdaki _Tüm_ istemci uygulamalarının erişim belirteci istemesi ve hedef uygulamada kimlik doğrulaması yapmasına izin verir. Ayrıca, yalnızca belirli istemci uygulamalarına izin vermek için _Yetkilendirmeyi_ zorlamak istiyorsanız, bazı ek yapılandırmalar gerçekleştirmeniz gerekir.

1. Korumak istediğiniz App Service veya Işlev uygulamasını temsil eden uygulama kaydının bildiriminde [bir uygulama rolü tanımlayın](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) .
1. Yetkilendirilmiş olması gereken istemciyi temsil eden uygulama kaydında, **API izinleri**  >  **izin Ekle**  >  **API 'leri** seçin.
1. Daha önce oluşturduğunuz uygulama kaydını seçin. Uygulama kaydını görmüyorsanız, [bir uygulama rolü](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)eklediğinizden emin olun.
1. **Uygulama izinleri** altında, daha önce oluşturduğunuz uygulama rolünü seçin ve ardından **izin Ekle**' yi seçin.
1. İstemci uygulamasını izin istemek üzere yetkilendirmek için **yönetici Izni ver** ' e tıkladığınızdan emin olun.
1. Önceki senaryoya benzer şekilde (herhangi bir rol eklenmeden önce), artık aynı hedef için [bir erişim belirteci isteyebilirsiniz](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) `resource` ve erişim belirteci, `roles` Istemci uygulaması için yetkilendirilmiş uygulama rollerini içeren bir talep içerir.
1. Hedef App Service veya Işlev uygulama kodu içinde, artık beklenen rollerin belirteçte bulunduğunu doğrulayabilirsiniz (Bu, App Service kimlik doğrulaması/yetkilendirme tarafından gerçekleştirilmemektedir). Daha fazla bilgi için bkz. [Kullanıcı taleplerine erişme](app-service-authentication-how-to.md#access-user-claims).

Artık kendi kimliğini kullanarak App Service uygulamanıza erişebilen bir Daemon istemci uygulaması yapılandırdınız.

## <a name="best-practices"></a>En iyi uygulamalar

Kimlik doğrulaması ayarlamak için kullandığınız yapılandırmadan bağımsız olarak, aşağıdaki en iyi yöntemler kiracınızı ve uygulamalarınızı daha güvenli tutacaktır:

- Her bir App Service uygulamasına kendi izinlerini ve onayını verin.
- Her App Service uygulamasını kendi kaydıyla yapılandırın.
- Ayrı dağıtım yuvaları için ayrı uygulama kayıtları kullanarak ortamlar arasında izin paylaşımını önleyin. Yeni kodu sınarken, bu uygulama sorunların üretim uygulamasını etkilemesini önlemeye yardımcı olabilir.

## <a name="next-steps"></a><a name="related-content"> </a>Sonraki adımlar

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [Öğretici: Azure depolama ve Microsoft Graph erişen bir Web uygulamasındaki kullanıcıların kimliğini doğrulama ve yetkilendirme](scenario-secure-app-authentication-app-service.md)
* [Öğretici: Azure App Service'te kullanıcıların kimliğini doğrulama ve kullanıcıları uçtan uca yetkilendirme](tutorial-auth-aad.md)
<!-- URLs. -->

[Azure portalı]: https://portal.azure.com/
