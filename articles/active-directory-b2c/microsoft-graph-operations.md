---
title: Microsoft Graph ile kaynakları yönetme
titleSuffix: Azure AD B2C
description: Microsoft Graph API 'sini çağırarak ve işlemi otomatikleştirmek için bir uygulama kimliği kullanarak Azure AD B2C kiracısındaki kaynakları yönetme.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cf62330fd677dc978c8f25a81c6a1e5bfbb612ac
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717612"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Microsoft Graph ile Azure AD B2C yönetme

Microsoft Graph, Azure AD B2C dizininizdeki kaynakları yönetmenizi sağlar. Aşağıdaki Microsoft Graph API işlemleri, kullanıcılar, kimlik sağlayıcıları, Kullanıcı akışları, özel ilkeler ve ilke anahtarları dahil Azure AD B2C kaynaklarının yönetimi için desteklenir. Aşağıdaki bölümlerde yer alan her bağlantı, bu işlem için Microsoft Graph API başvurusu içinde karşılık gelen sayfayı hedefler. 

## <a name="prerequisites"></a>Önkoşullar

MS Graph API kullanmak ve Azure AD B2C kiracınızdaki kaynaklarla etkileşim kurmak için izinleri veren bir uygulama kaydına ihtiyacınız vardır. Yönetim uygulamanızın kullanabileceği bir uygulama kaydı oluşturmak için, [Azure AD B2C yönetme Microsoft Graph](microsoft-graph-get-started.md) makalesindeki adımları izleyin. 

## <a name="user-management"></a>Kullanıcı yönetimi

- [Kullanıcıları listele](/graph/api/user-list)
- [Tüketici kullanıcısı oluşturma](/graph/api/user-post-users)
- [Kullanıcı al](/graph/api/user-get)
- [Kullanıcı güncelleştirme](/graph/api/user-update)
- [Kullanıcı silme](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Kullanıcı telefon numarası yönetimi (Beta)

Kullanıcı tarafından [SMS veya sesli çağrılar](identity-provider-local.md#phone-sign-in-preview)veya [Multi-Factor Authentication](multi-factor-authentication.md)kullanarak oturum açmak için kullanılabilecek bir telefon numarası. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulama yöntemleri API 'si](/graph/api/resources/phoneauthenticationmethod).

- [Ekle](/graph/api/authentication-post-phonemethods)
- [Liste](/graph/api/authentication-list-phonemethods)
- [Al](/graph/api/phoneauthenticationmethod-get)
- [Güncelleştirme](/graph/api/phoneauthenticationmethod-update)
- [Silme](/graph/api/phoneauthenticationmethod-delete)

[Liste](/graph/api/authentication-list-phonemethods) işleminin yalnızca etkin telefon numaralarını döndürdüğünü göz önünde kalmaz. Aşağıdaki telefon numarası, liste işlemleriyle birlikte kullanılmak üzere etkinleştirilmelidir. 

![Telefonla oturum açmayı etkinleştir](./media/microsoft-graph-operations/enable-phone-sign-in.png)

> [!NOTE]
> Geçerli beta sürümünde, bu API yalnızca telefon numarası ülke kodu ve telefon numarası arasında bir boşluk ile depolanıyorsa kullanılabilir. Azure AD B2C hizmeti şu anda varsayılan olarak bu alanı eklemez.

## <a name="self-service-password-reset-email-address-beta"></a>Self servis parola sıfırlama e-posta adresi (Beta)

Parolayı sıfırlamak için [Kullanıcı adı oturum açma hesabı](identity-provider-local.md#username-sign-in) tarafından kullanılabilen bir e-posta adresi. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulama yöntemleri API 'si](/graph/api/resources/emailauthenticationmethod).

- [Ekle](/graph/api/emailauthenticationmethod-post)
- [Liste](/graph/api/emailauthenticationmethod-list)
- [Al](/graph/api/emailauthenticationmethod-get)
- [Güncelleştirme](/graph/api/emailauthenticationmethod-update)
- [Silme](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Kimlik sağlayıcıları

Azure AD B2C kiracınızdaki Kullanıcı akışlarınız için kullanılabilen [kimlik sağlayıcılarını](add-identity-provider.md) yönetin.

- [Azure AD B2C kiracısında kayıtlı kimlik sağlayıcılarını listeleyin](/graph/api/identityprovider-list)
- [Kimlik sağlayıcısı oluşturma](/graph/api/identityprovider-post-identityproviders)
- [Kimlik sağlayıcısı al](/graph/api/identityprovider-get)
- [Kimlik sağlayıcısını Güncelleştir](/graph/api/identityprovider-update)
- [Bir kimlik sağlayıcısını silme](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Kullanıcı akışı

Kaydolma, oturum açma, Birleşik kayıt ve oturum açma, parola sıfırlama ve profil güncelleştirme için önceden oluşturulmuş ilkeleri yapılandırın.

- [Kullanıcı akışlarını listeleme](/graph/api/identitycontainer-list-b2cuserflows)
- [Kullanıcı akışı oluşturma](/graph/api/identitycontainer-post-b2cuserflows)
- [Kullanıcı akışı al](/graph/api/b2cidentityuserflow-get)
- [Kullanıcı akışını silme](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Kullanıcı akışı kimlik doğrulama yöntemleri (Beta)

Kullanıcıların yerel hesaplar aracılığıyla kaydolmasına izin vermek için bir mekanizma seçin. Yerel hesaplar, Azure AD 'nin kimlik onaylama işlemi olduğu hesaplardır. Daha fazla bilgi için bkz. [b2cAuthenticationMethodsPolicy Resource Type](/graph/api/resources/b2cauthenticationmethodspolicy).

- [Al](/graph/api/b2cauthenticationmethodspolicy-get)
- [Güncelleştirme](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Özel ilkeler

Aşağıdaki işlemler, [özel ilkeler](custom-policy-overview.md)olarak bilinen Azure AD B2C güven çerçevesi ilkelerini yönetmenizi sağlar.

- [Bir kiracıda yapılandırılmış tüm güven çerçevesi ilkelerini listeleme](/graph/api/trustframework-list-trustframeworkpolicies)
- [Güven çerçevesi İlkesi Oluştur](/graph/api/trustframework-post-trustframeworkpolicy)
- [Mevcut bir güven çerçevesi ilkesinin özelliklerini okuyun](/graph/api/trustframeworkpolicy-get)
- [Güven altyapısı ilkesini güncelleştirin veya oluşturun.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Mevcut bir güven çerçevesini silme ilkesi](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>İlke anahtarları

Kimlik deneyimi çerçevesi, bileşenler arasında güven sağlamak için bir özel ilkede başvurulan gizli dizileri depolar. Bu gizlilikler simetrik veya asimetrik anahtarlar/değerler olabilir. Azure portal, bu varlıklar **ilke anahtarları** olarak gösterilir.

Microsoft Graph API 'sindeki ilke anahtarları için en üst düzey kaynak, [güvenilir çerçeve anahtar kümesi](/graph/api/resources/trustframeworkkeyset)' dir. Her **anahtar kümesi** en az bir **anahtar** içerir. Anahtar oluşturmak için, önce boş bir anahtar kümesi oluşturun ve sonra anahtar kümesi içinde bir anahtar oluşturun. El ile gizli dizi oluşturabilir, bir sertifikayı veya PKCS12 anahtarını karşıya yükleyebilirsiniz. Anahtar, oluşturulmuş bir gizli dizi, bir dize (Facebook uygulaması gizli dizisi gibi) veya karşıya yüklediğiniz bir sertifika olabilir. Bir anahtar kümesi birden fazla anahtara sahipse, anahtarlardan yalnızca biri etkin olur.

### <a name="trust-framework-policy-keyset"></a>Güven çerçevesi ilkesi anahtar kümesi

- [Güven çerçevesi anahtar kümelerini listeleyin](/graph/api/trustframework-list-keysets)
- [Güven çerçevesi anahtar kümeleri oluşturma](/graph/api/trustframework-post-keysets)
- [Anahtar kümesi al](/graph/api/trustframeworkkeyset-get)
- [Güven çerçevesi anahtar kümelerini güncelleştirme](/graph/api/trustframeworkkeyset-update)
- [Güven çerçevesi anahtar kümelerini silme](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Güven çerçevesi ilke anahtarı

- [Anahtar kümesi içinde şu anda etkin olan anahtarı al](/graph/api/trustframeworkkeyset-getactivekey)
- [Anahtar kümesi içinde anahtar oluşturma](/graph/api/trustframeworkkeyset-generatekey)
- [Dize tabanlı gizli dizi yükle](/graph/api/trustframeworkkeyset-uploadsecret)
- [X. 509.440 sertifikasını karşıya yükle](/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12 biçim sertifikasını karşıya yükle](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Uygulamalar

- [Uygulamaları listeleme](/graph/api/application-list)
- [Uygulama oluşturma](/graph/api/resources/application)
- [Uygulamayı güncelleştirme](/graph/api/application-update)
- [Hizmet sorumlusu oluşturma](/graph/api/resources/serviceprincipal)
- [Oauth2Permission Grant oluştur](/graph/api/resources/oauth2permissiongrant)
- [Uygulamayı Sil](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Uygulama uzantısı özellikleri

- [Uzantı özelliklerini listele](/graph/api/application-list-extensionproperty)

Azure AD B2C, Kullanıcı başına 100 özel öznitelik tutan bir dizin sağlar. Kullanıcı akışları için, bu uzantı özellikleri [Azure Portal kullanılarak yönetilir](user-flow-custom-attributes.md). Özel ilkeler için Azure AD B2C, ilke uzantı özelliğine ilk kez bir değer yazdığında, sizin için özelliği oluşturur.

## <a name="audit-logs"></a>Denetim günlükleri

- [Denetim günlüklerini listeleme](/graph/api/directoryaudit-list)

Azure AD B2C denetim günlüklerine erişme hakkında daha fazla bilgi için bkz. [Azure AD B2C denetim günlüklerine erişme](view-audit-logs.md).

## <a name="conditional-access"></a>Koşullu Erişim

- [Tüm koşullu erişim ilkelerini listeleyin](/graph/api/conditionalaccessroot-list-policies?tabs=http)
- [Koşullu erişim ilkesinin özelliklerini ve ilişkilerini okuyun](/graph/api/conditionalaccesspolicy-get)
- [Yeni bir koşullu erişim ilkesi oluşturma](/graph/api/resources/application)
- [Koşullu erişim ilkesini güncelleştirme](/graph/api/conditionalaccesspolicy-update)
- [Koşullu erişim ilkesini silme](/graph/api/conditionalaccesspolicy-delete)

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Kod örneği: program aracılığıyla Kullanıcı hesaplarını yönetme

Bu kod örneği, Microsoft Graph API ile etkileşim kurmak için [Microsoft Graph SDK 'sını](/graph/sdks/sdks-overview) kullanan bir .NET Core konsol uygulamasıdır. Kodu, bir Azure AD B2C kiracısındaki kullanıcıları programlı bir şekilde yönetmek için API 'nin nasıl çağrılacağını gösterir.
[Örnek Arşivi](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip) Indirebilir, GitHub 'daki [depoya gözatabilir](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) veya depoyu klonlayabilirsiniz:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Kod örneğini aldıktan sonra, ortamınız için yapılandırın ve ardından projeyi derleyin:

1. Projeyi [Visual Studio](https://visualstudio.microsoft.com) 'da veya [Visual Studio Code](https://code.visualstudio.com)açın.
1. `src/appsettings.json` dosyasını açın.
1. Bölümünde, `appSettings` `your-b2c-tenant` kiracınızın adıyla ve `Application (client) ID` ve `Client secret` yönetim uygulaması kaydınızın değerleriyle değiştirin. Daha fazla bilgi için bkz. [Microsoft Graph uygulamasını kaydetme](microsoft-graph-get-started.md).
1. Deponun yerel kopyanızda bir konsol penceresi açın, dizine geçin ve `src` ardından projeyi derleyin:

    ```console
    cd src
    dotnet build
    ```
    
1. Uygulamayı `dotnet` komutla çalıştırın:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

Uygulama, yürütebilmeniz için kullanabileceğiniz komutların bir listesini görüntüler. Örneğin, tüm kullanıcıları alma, tek bir Kullanıcı alma, Kullanıcı silme, Kullanıcı parolasını güncelleştirme ve toplu içeri aktarma.

### <a name="code-discussion"></a>Kod tartışması

Örnek kod, Microsoft Graph erişen yüksek kaliteli, verimli ve esnek uygulamalar oluşturmayı basitleştirmek için tasarlanan [Microsoft Graph SDK 'sını](/graph/sdks/sdks-overview)kullanır.

Microsoft Graph API 'sine yönelik herhangi bir istek, kimlik doğrulaması için bir erişim belirteci gerektirir. Çözüm, Microsoft Graph SDK ile kullanmak üzere Microsoft kimlik doğrulama kitaplığı 'nın (MSAL) kimlik doğrulama senaryosu tabanlı bir sarmalayıcı sağlayan [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet paketini kullanır.

`RunAsync` _Program. cs_ dosyasındaki yöntemi:

1. _appsettings.js_ dosyadaki uygulama ayarlarını okur
1. Kimlik doğrulama sağlayıcısını [OAuth 2,0 istemci kimlik bilgileri verme](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) akışı kullanarak başlatır. İstemci kimlik bilgileri verme akışı ile, uygulama Microsoft Graph API 'sini çağırmak için bir erişim belirteci alabilir.
1. Kimlik doğrulama sağlayıcısına Microsoft Graph hizmeti istemcisini ayarlar:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Başlatılmış *GraphServiceClient* daha sonra Kullanıcı yönetimi işlemlerini gerçekleştirmek Için _userservice. cs_ ' de kullanılır. Örneğin, Kiracıdaki Kullanıcı hesaplarının bir listesini alma:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Microsoft Graph SDK 'ları kullanarak API çağrıları yapın](/graph/sdks/create-requests) Microsoft Graph bilgileri okuma ve yazma hakkında bilgiler içerir, `$select` döndürülen özellikleri denetlemek, özel sorgu parametreleri sağlamak ve `$filter` ve sorgu parametrelerini kullanmak için kullanın `$orderBy` .

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
