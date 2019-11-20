---
title: İstemci uygulamasındaki Azure Active Directory blob 'lara ve kuyruklara erişim yetkisi verme-Azure depolama
description: İstemci uygulama içinden kimlik doğrulamak, bir OAuth 2,0 belirteci edinmek ve Azure Blob depolama ve kuyruk depolama 'ya istekleri yetkilendirmek için Azure Active Directory kullanın.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ca6b055b5d3702cea4ca1986ad1c81b59f76cee3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299626"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-from-a-client-application"></a>İstemci uygulamasından Azure Active Directory blob ve kuyruklara erişim yetkisi verme

Azure Blob depolama veya kuyruk depolama ile Azure Active Directory (Azure AD) kullanmanın önemli bir avantajı, kimlik bilgilerinizin artık kodunuzda depolanmasına gerek kalmaz. Bunun yerine, Microsoft Identity platform (eski adıyla Azure AD) için bir OAuth 2,0 erişim belirteci isteyebilirsiniz. Azure AD, uygulamayı çalıştıran güvenlik sorumlusu (bir Kullanıcı, Grup veya hizmet sorumlusu) kimliğini doğrular. Kimlik doğrulaması başarılı olursa, Azure AD uygulamaya erişim belirtecini döndürür ve uygulama, istekleri Azure Blob depolama veya kuyruk depolama alanına yetkilendirmek için erişim belirtecini kullanabilir.

Bu makalede, Microsoft Identity Platform 2,0 ile yerel uygulamanızı veya Web uygulamanızı kimlik doğrulaması için nasıl yapılandıracağınız gösterilir. Kod örneği .NET özelliklerine sahiptir, ancak diğer diller de benzer bir yaklaşım kullanır. Microsoft Identity Platform 2,0 hakkında daha fazla bilgi için bkz. [Microsoft Identity platform (v 2.0) genel bakış](../../active-directory/develop/v2-overview.md).

OAuth 2,0 kod verme akışına genel bakış için bkz. [oauth 2,0 kod verme akışını kullanarak Azure Active Directory Web uygulamalarına erişimi yetkilendirme](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Azure AD güvenlik sorumlusuna rol atama

Azure depolama uygulamanızdan bir güvenlik sorumlusunun kimliğini doğrulamak için önce bu güvenlik sorumlusu için rol tabanlı erişim denetimi (RBAC) ayarlarını yapılandırın. Azure depolama, kapsayıcılar ve kuyruklar için izinleri çevreleyen yerleşik RBAC rollerini tanımlar. RBAC rolü bir güvenlik sorumlusuna atandığında, bu güvenlik sorumlusuna bu kaynağa erişim verilir. Daha fazla bilgi için bkz. [Azure Blob 'a erişim haklarını yönetme ve RBAC Ile kuyruk verileri](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Uygulamanızı bir Azure AD kiracısıyla kaydetme

Depolama kaynaklarına erişimi yetkilendirmek için Azure AD kullanmanın ilk adımı, istemci uygulamanızı [Azure Portal](https://portal.azure.com)BIR Azure AD kiracısıyla kaydetmekte. İstemci uygulamanızı kaydettiğinizde, Azure AD 'ye uygulama hakkında bilgi sağlarsınız. Daha sonra Azure AD, uygulamanızı çalışma zamanında Azure AD ile ilişkilendirmek için kullandığınız bir istemci KIMLIĞI ( *uygulama kimliği*olarak da bilinir) sağlar. İstemci KIMLIĞI hakkında daha fazla bilgi edinmek için [Azure Active Directory Içindeki uygulama ve hizmet sorumlusu nesneleri](../../active-directory/develop/app-objects-and-service-principals.md)bölümüne bakın.

Azure depolama uygulamanızı kaydetmek için [hızlı başlangıç: bir uygulamayı Microsoft Identity platformu Ile kaydetme](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)bölümünde gösterilen adımları izleyin. Aşağıdaki görüntüde bir Web uygulamasını kaydetmeye yönelik genel ayarlar gösterilmektedir:

![Depolama uygulamanızın Azure AD 'ye nasıl kaydedileceği gösteren ekran görüntüsü](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Uygulamanızı yerel bir uygulama olarak kaydettiğinizde, **yeniden yönlendirme URI 'si**için GEÇERLI bir URI belirtebilirsiniz. Yerel uygulamalar için, bu değerin gerçek bir URL olması gerekmez. Web uygulamaları için, yeniden yönlendirme URI 'si, belirteçlerin sağlandığı URL 'YI belirttiğinden geçerli bir URI olmalıdır.

Uygulamanızı kaydettikten sonra **Ayarlar**altında uygulama kimliğini (veya istemci kimliğini) görürsünüz:

![İstemci KIMLIĞINI gösteren ekran görüntüsü](./media/storage-auth-aad-app/app-registration-client-id.png)

Bir uygulamayı Azure AD 'ye kaydetme hakkında daha fazla bilgi için bkz. [uygulamaları Azure Active Directory tümleştirme](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Kayıtlı uygulama izinlerinizi Azure Storage 'a verme

Daha sonra, uygulamanıza Azure depolama API 'Lerini çağırma izni verin. Bu adım, uygulamanızın Azure AD ile Azure depolama 'ya istekleri yetkilendirebilmesini sağlar.

1. Kayıtlı uygulamanızın **genel bakış** SAYFASıNDA, **API izinlerini görüntüle**' yi seçin.
1. **API izinleri** bölümünde **izin Ekle** ' yi seçin ve **Microsoft API 'leri**seçin.
1. **İstek API 'si izinleri** bölmesini göstermek için sonuçlar listesinden **Azure Storage** ' ı seçin.
1. **Uygulamanız ne tür izinler gerektiriyor?** altında, kullanılabilir Izin türünün **temsilci izinleri**olduğunu gözlemleyin. Bu seçenek varsayılan olarak sizin için seçilidir.
1. **API Izinleri iste** bölmesinin **izin Seç** bölümünde, **user_impersonation**' nin yanındaki onay kutusunu işaretleyin ve **izin Ekle**' ye tıklayın.

    ![Depolama için izinleri gösteren ekran görüntüsü](media/storage-auth-aad-app/registered-app-permissions-1.png)

**API izinleri** bölmesi artık KAYıTLı Azure AD uygulamanızın hem Microsoft Graph hem de Azure depolama 'ya erişimi olduğunu gösterir. Uygulamanızı Azure AD 'ye ilk kez kaydettiğinizde izinler otomatik olarak Microsoft Graph verilir.

![Uygulama kaydetme izinlerini gösteren ekran görüntüsü](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>İstemci parolası oluşturma

Uygulamanın bir belirteç istenirken kimliğini kanıtlamak için bir istemci parolası gerekir. İstemci parolasını eklemek için şu adımları izleyin:

1. Azure portal uygulama kaydınız ' ne gidin.
1. **Sertifikalar & gizli** dizi ayarını seçin.
1. **İstemci**gizli dizileri altında yeni bir gizli dizi oluşturmak için **yeni istemci parolası** ' na tıklayın.
1. Gizli dizi için bir açıklama sağlayın ve istenen süre sonu aralığını seçin.
1. Yeni Gizliliğin değerini hemen güvenli bir konuma kopyalayın. Tam değer size yalnızca bir kez gösterilir.

    ![İstemci gizliliğini gösteren ekran görüntüsü](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Belirteç alımı için istemci kitaplıkları

Uygulamanızı kaydettikten ve Azure Blob depolama veya kuyruk depolama alanındaki verilere erişim izni verdikten sonra, bir güvenlik sorumlusunun kimliğini doğrulamak ve bir OAuth 2,0 belirteci edinmek için uygulamanıza kod ekleyebilirsiniz. Belirtecin kimliğini doğrulamak ve almak için, [Microsoft kimlik platformu kimlik doğrulama kitaplıklarından](../../active-directory/develop/reference-v2-libraries.md) birini veya OpenID Connect 1,0 'yi destekleyen başka bir açık kaynak kitaplığı kullanabilirsiniz. Uygulamanız daha sonra, Azure Blob depolama veya kuyruk depolama 'ya karşı bir isteği yetkilendirmek için erişim belirtecini kullanabilir.

Belirteçleri alma için desteklenen senaryoların listesi için, [Microsoft kimlik doğrulama kitaplığı içeriğinin](/azure/active-directory/develop/msal-overview) [kimlik doğrulama akışları](/en-us/azure/active-directory/develop/msal-authentication-flows) bölümüne bakın.

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Azure AD ile kimlik doğrulaması için iyi bilinen değerler

Azure AD ile bir güvenlik sorumlusunun kimliğini doğrulamak için kodunuzda iyi bilinen bir değer eklemeniz gerekir.

### <a name="azure-ad-authority"></a>Azure AD yetkilisi

Microsoft genel bulutu için temel Azure AD yetkilisi, *kiracı kimliğinin* ACTIVE DIRECTORY kiracı Kimliğiniz (veya dizin kimliği) olduğu durumlarda aşağıdaki gibidir:

`https://login.microsoftonline.com/<tenant-id>/`

Kiracı KIMLIĞI, kimlik doğrulaması için kullanılacak Azure AD kiracısını tanımlar. Ayrıca, dizin KIMLIĞI olarak da adlandırılır. Kiracı KIMLIĞINI almak için Azure portal uygulamanızın kayıt defteri için **genel bakış** sayfasına gidin ve değeri oradan kopyalayın.

### <a name="azure-storage-resource-id"></a>Azure depolama kaynak KIMLIĞI

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET kod örneği: blok blobu oluşturma

Kod örneği, Azure AD 'den bir erişim belirtecinin nasıl alınacağını gösterir. Erişim belirteci, belirtilen kullanıcının kimliğini doğrulamak ve sonra blok blobu oluşturma isteğine yetki vermek için kullanılır. Bu örnek çalışmaya ulaşmak için önce önceki bölümlerde özetlenen adımları izleyin.

Belirteci istemek için uygulamanızın kaydında aşağıdaki değerlere ihtiyacınız olacaktır:

- Azure AD etki alanının adı. Azure Active Directory **genel bakış** sayfasından bu değeri alın.
- Kiracı (veya dizin) KIMLIĞI. Uygulama kaydınızdan **genel bakış** sayfasından bu değeri alın.
- İstemci (veya uygulama) KIMLIĞI. Uygulama kaydınızdan **genel bakış** sayfasından bu değeri alın.
- İstemci yeniden yönlendirme URI 'SI. Uygulama kaydınız için **kimlik doğrulama** ayarlarından bu değeri alın.
- İstemci parolasının değeri. Bu değeri, daha önce kopyaladığınız konumdan alın.

### <a name="create-a-storage-account-and-container"></a>Depolama hesabı ve kapsayıcı oluşturma

Kod örneğini çalıştırmak için Azure Active Directory aynı abonelikte bir depolama hesabı oluşturun. Ardından bu depolama hesabı içinde bir kapsayıcı oluşturun. Örnek kod, bu kapsayıcıda bir Blok Blobu oluşturacak.

Ardından, **Depolama Blobu veri katılımcısı** rolünü, örnek kodu çalıştıracağınız Kullanıcı hesabına açıkça atayın. Bu rolün Azure portal nasıl atanacağı hakkında yönergeler için bkz. [Azure Blob 'a erişim verme ve Azure Portal RBAC ile kuyruk verileri](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Bir Azure depolama hesabı oluşturduğunuzda, Azure AD aracılığıyla verilere erişim için otomatik olarak izinler atanmamıştır. Azure depolama için kendinize bir RBAC rolünü açıkça atamanız gerekir. Aboneliğiniz, kaynak grubunuz, depolama hesabınız veya Kapsayıcınız ya da kuyruğunuzun düzeyinde atayabilirsiniz.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Azure AD ile blob depolamaya erişim yetkisi veren bir Web uygulaması oluşturma

Uygulamanız Azure Storage 'a eriştiğinde Kullanıcı adına bunu yapar, yani blob veya kuyruk kaynaklarına oturum açan kullanıcının izinleri kullanılarak erişilir. Bu kod örneğini denemek için kullanıcıdan bir Azure AD kimliği kullanarak oturum açmasını isteyen bir Web uygulamasına ihtiyacınız vardır. Kendinizinkini oluşturabilir veya Microsoft tarafından sunulan örnek uygulamayı kullanabilirsiniz.

Belirteç içeren ve Azure Storage 'da blob oluşturmak için onu kullanan tamamlanmış örnek bir Web uygulaması [GitHub](https://aka.ms/aadstorage)' da kullanılabilir. Tamamlanan örneği gözden geçirmek ve çalıştırmak, kod örneklerini anlamak için yararlı olabilir. Tamamlanmış örneği çalıştırma hakkında yönergeler için, görünüm başlıklı bölüme bakın [ve tamamlanan örneği çalıştırın](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Başvuruları ve using deyimlerini ekleyin  

Visual Studio 'dan Azure Storage istemci kitaplığı 'nı yükler. **Araçlar** menüsünde, **NuGet Paket Yöneticisi**' ni ve ardından **Paket Yöneticisi konsolu**' nu seçin. Gerekli paketleri .NET için Azure Storage istemci kitaplığından yüklemek üzere konsol penceresine aşağıdaki komutları yazın:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Ardından, aşağıdaki using deyimlerini HomeController.cs dosyasına ekleyin:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Blok Blobu oluşturma

Blok Blobu oluşturmak için aşağıdaki kod parçacığını ekleyin:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Blob ve kuyruk işlemlerini bir OAuth 2,0 belirteciyle yetkilendirmek için HTTPS kullanmanız gerekir.

Yukarıdaki örnekte, .NET istemci kitaplığı, blok blobu oluşturma isteğinin yetkilendirmesini işler. Diğer dillere yönelik Azure depolama istemci kitaplıkları da isteğin yetkilendirmesini de işler. Ancak, REST API kullanarak bir OAuth belirteci ile Azure depolama işlemini arıyorsanız, isteği OAuth belirtecini kullanarak yetkilendirmeniz gerekir.

Blob ve Kuyruk hizmeti işlemlerini OAuth erişim belirteçlerini kullanarak çağırmak için, aşağıdaki örnekte gösterildiği gibi, erişim belirtecini, **taşıyıcı** şemasını kullanarak **Yetkilendirme** üst bilgisine geçirin ve 2017-11-09 veya üzeri bir hizmet sürümünü belirtin:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Azure AD 'den bir OAuth belirteci alın

Sonra, Azure AD 'den Kullanıcı adına belirteç isteyen bir yöntem ekleyin. Bu yöntem, izin verilecek kapsamı tanımlar. İzinler ve kapsamlar hakkında daha fazla bilgi için bkz. [Microsoft Identity platform uç noktasındaki izinler ve onay](../../active-directory/develop/v2-permissions-and-consent.md).

Belirtecin alınacağı kapsamı oluşturmak için kaynak KIMLIĞINI kullanın. Örnek, kaynak KIMLIĞINI yerleşik `user_impersonation` kapsamıyla birlikte kullanarak, belirtecin Kullanıcı adına istenmekte olduğunu gösteren kapsamı oluşturur.

Kullanıcıya, kullanıcının adına belirteç isteme izni vermesini sağlayan bir arayüz sunmanız gerektiğini aklınızda bulundurun. Onay gerekli olduğunda, örnek **Msaluırequiredexception** yakalar ve izin isteğini kolaylaştırmak için başka bir yöntem çağırır:

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Onay, bir kullanıcının adına korumalı kaynaklara erişmesi için yetkilendirme izni veren bir işlemdir. Microsoft Identity Platform 2,0 artımlı onayı destekler, yani bir güvenlik sorumlusu başlangıçta minimum izin kümesi isteyebilir ve gerektiğinde zaman içinde izinler ekleyebilir. Kodunuz bir erişim belirteci istediğinde, `scope` parametresinde, uygulamanızın herhangi bir zamanda ihtiyaç duyacağı izin kapsamını belirtin. Artımlı izin hakkında daha fazla bilgi için bkz. [Microsoft Identity platform (v 2.0) güncelleştirmesinde neden](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)olan **artımlı ve dinamik onay** başlıklı Bölüm.

Aşağıdaki yöntem artımlı izin istemek için kimlik doğrulama özelliklerini oluşturur:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>Tamamlanan örneği görüntüleme ve çalıştırma

Örnek uygulamayı çalıştırmak için öncelikle [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal)'dan kopyalayın veya indirin. Ardından, aşağıdaki bölümlerde açıklandığı şekilde uygulamayı güncelleştirin.

### <a name="provide-values-in-the-settings-file"></a>Ayarlar dosyasında değer sağlama

Ardından, *appSettings. JSON* dosyasını aşağıdaki gibi kendi değerlerinizle güncelleştirin:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Depolama hesabı ve kapsayıcı adını güncelleştirme

*HomeController.cs* dosyasında, blok BLOBUNA başvuran URI 'yi, depolama hesabınızın ve kapsayıcının adını kullanacak şekilde güncelleştirin:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Örtük verme akışını etkinleştir

Örneği çalıştırmak için, uygulama kaydınız için örtük verme akışını yapılandırmanız gerekebilir. Şu adımları uygulayın:

1. Azure portal uygulama kaydınız ' ne gidin.
1. Yönet bölümünde **kimlik doğrulama** ayarını seçin.
1. **Gelişmiş ayarlar**altında, **örtük izin** bölümünde, aşağıdaki görüntüde gösterildiği gibi, erişim belirteçlerini ve kimlik belirteçlerini etkinleştirmek için onay kutularını seçin:

    ![Örtük izin akışı için ayarların nasıl etkinleştirileceğini gösteren ekran görüntüsü](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Localhost tarafından kullanılan bağlantı noktasını güncelleştirme

Örneği çalıştırdığınızda, çalışma zamanında atanan *localhost* bağlantı noktasını kullanmak için uygulama kaydın belirtilen YENIDEN yönlendirme URI 'sini güncelleştirmeniz gerektiğini görebilirsiniz. Atanan bağlantı noktasını kullanmak üzere yeniden yönlendirme URI 'sini güncelleştirmek için şu adımları izleyin:

1. Azure portal uygulama kaydınız ' ne gidin.
1. Yönet bölümünde **kimlik doğrulama** ayarını seçin.
1. **Yeniden yönlendirme URI 'leri**altında, aşağıdaki görüntüde gösterildiği gibi, örnek uygulama tarafından kullanılan bağlantı noktasını düzenleyin:

    ![Uygulama kaydı için yeniden yönlendirme URI 'Lerini gösteren ekran görüntüsü](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Sonraki adımlar

- Microsoft Identity platformu hakkında daha fazla bilgi edinmek için bkz. [Microsoft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/).
- Azure depolama için RBAC rolleri hakkında daha fazla bilgi edinmek için bkz. [RBAC ile depolama verileri için erişim haklarını yönetme](storage-auth-aad-rbac.md).
- Azure depolama ile Azure kaynakları için Yönetilen kimlikler kullanma hakkında bilgi edinmek için bkz. [Azure kaynakları için Azure Active Directory ve yönetilen kimlikler ile bloblara ve kuyruklara erişim kimlik doğrulaması](storage-auth-aad-msi.md).
