---
title: Azure Batch hizmeti çözümlerinin kimlik doğrulaması için Azure Active Directory kullanma | Microsoft Docs
description: Batch, Batch hizmetinden kimlik doğrulaması için Azure AD 'yi destekler.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/15/2019
ms.author: lahugh
ms.openlocfilehash: 4ec85078e6664a43dd31cd04c132d87681bda225
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095615"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Active Directory ile Batch hizmeti çözümlerini kimlik doğrulama

Azure Batch, [Azure Active Directory][aad_about] (Azure AD) ile kimlik doğrulamasını destekler. Azure AD, Microsoft 'un çok kiracılı bulut tabanlı dizin ve kimlik yönetimi hizmetidir. Azure, müşterilerinin, hizmet yöneticilerinin ve kurumsal kullanıcıların kimliğini doğrulamak için Azure AD 'yi kullanır.

Azure AD kimlik doğrulamasını Azure Batch ile kullanırken, iki şekilde kimlik doğrulaması yapabilirsiniz:

- Uygulamayla etkileşim kuran bir kullanıcının kimliğini doğrulamak için **Tümleşik kimlik doğrulaması** kullanarak. Tümleşik kimlik doğrulaması kullanan bir uygulama, bir kullanıcının kimlik bilgilerini toplar ve Batch kaynaklarına erişimin kimliğini doğrulamak için bu kimlik bilgilerini kullanır.
- Katılımsız bir uygulamanın kimliğini doğrulamak için bir **hizmet sorumlusu** kullanarak. Hizmet sorumlusu, çalışma zamanında kaynaklara erişirken uygulamayı temsil etmek üzere bir uygulama için ilke ve izinleri tanımlar.

Azure AD hakkında daha fazla bilgi edinmek için [Azure Active Directory belgelerine](https://docs.microsoft.com/azure/active-directory/)bakın.

## <a name="endpoints-for-authentication"></a>Kimlik doğrulaması için uç noktalar

Azure AD ile Batch uygulamalarının kimliğini doğrulamak için kodunuzda iyi bilinen bazı uç noktaları eklemeniz gerekir.

### <a name="azure-ad-endpoint"></a>Azure AD uç noktası

Temel Azure AD yetkilisi uç noktası:

`https://login.microsoftonline.com/`

Azure AD ile kimlik doğrulaması yapmak için bu uç noktayı kiracı KIMLIĞI (dizin KIMLIĞI) ile birlikte kullanırsınız. Kiracı kimliği, kimlik doğrulaması için kullanılacak Azure AD kiracısı tanımlar. Kiracı KIMLIĞINI almak için [Azure Active Directory KIRACı kimliğini alma](#get-the-tenant-id-for-your-active-directory)bölümünde açıklanan adımları izleyin:

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Hizmet sorumlusu kullanarak kimlik doğrulaması yaptığınızda kiracıya özgü uç nokta gereklidir. 
> 
> Tümleşik kimlik doğrulaması kullanarak kimlik doğrulaması yaptığınızda kiracıya özgü uç nokta isteğe bağlıdır, ancak önerilir. Ancak, Azure AD ortak uç noktasını da kullanabilirsiniz. Ortak uç nokta, belirli bir kiracı sağlanmadıysa genel bir kimlik bilgisi toplama arabirimi sağlar. Ortak uç nokta `https://login.microsoftonline.com/common`.
>
>

Azure AD uç noktaları hakkında daha fazla bilgi için bkz. [Azure AD Için kimlik doğrulama senaryoları][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Batch kaynak uç noktası

Batch hizmetine yönelik isteklerin kimliğini doğrulamak için bir belirteç almak üzere **Azure Batch kaynak uç noktasını** kullanın:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Uygulamanızı kiracı ile kaydetme

Kimlik doğrulaması yapmak için Azure AD kullanmanın ilk adımı, uygulamanızı bir Azure AD kiracısında kaydediyor. Uygulamanızı kaydetmek, sağlayan Azure çağırmanızı [Active Directory Authentication Library][aad_adal] (ADAL) kodunuzdan. ADAL, uygulamanızın Azure AD ile kimlik doğrulaması için bir API sağlar. Uygulamanızı kaydetmek, tümleşik kimlik doğrulaması veya hizmet sorumlusu kullanmayı planladığınızdan gereklidir.

Uygulamanızı kaydettiğinizde, Azure AD'ye uygulamanız ile ilgili bilgileri sağlayın. Daha sonra Azure AD, uygulamanızı çalışma zamanında Azure AD ile ilişkilendirmek için kullandığınız bir uygulama KIMLIĞI ( *ISTEMCI kimliği*olarak da bilinir) sağlar. Uygulama KIMLIĞI hakkında daha fazla bilgi edinmek için [Azure Active Directory Içindeki uygulama ve hizmet sorumlusu nesneleri](../active-directory/develop/app-objects-and-service-principals.md)bölümüne bakın.

Batch uygulamanızı kaydetmek için [uygulamaları Azure Active Directory Ile tümleştirme][aad_integrate]bölümünde [uygulama ekleme](../active-directory/develop/quickstart-register-app.md) bölümündeki adımları izleyin. Uygulamanızı yerel bir uygulama olarak kaydettiğinizde, **yeniden yönlendirme URI 'si**için GEÇERLI bir URI belirtebilirsiniz. Gerçek bir uç nokta olması gerekmez.

Uygulamanızı kaydettikten sonra uygulama KIMLIĞINI görürsünüz:

![Batch uygulamanızı Azure AD 'ye kaydetme](./media/batch-aad-auth/app-registration-data-plane.png)

Bir uygulamayı Azure AD 'ye kaydetme hakkında daha fazla bilgi için bkz. [Azure AD Için kimlik doğrulama senaryoları](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Active Directory kiracı KIMLIĞINI alın

Kiracı KIMLIĞI, uygulamanıza kimlik doğrulama hizmetleri sağlayan Azure AD kiracısını tanımlar. Kiracı Kimliğini almak için şu adımları izleyin:

1. Azure portalında Active Directory'nizi seçin.
1. Seçin **özellikleri**.
1. Sağlanan GUID değeri kopyalayın **dizin kimliği**. Bu değer Kiracı kimliği olarak da adlandırılır

![Dizin KIMLIĞINI Kopyala](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Tümleşik kimlik doğrulaması kullan

Tümleşik kimlik doğrulaması ile kimlik doğrulamak için, uygulamanıza Batch hizmeti API 'sine bağlanmak için izin vermeniz gerekir. Bu adım, uygulamanızın Azure AD ile Batch hizmeti API 'sine yapılan çağrıların kimliğini doğrulamasını sağlar.

Uygulamanızı kaydettikten sonra, Batch hizmetine erişim sağlamak için Azure portal bu adımları izleyin:

1. Azure portal sol taraftaki Gezinti bölmesinde **tüm hizmetler**' i seçin. **Uygulama kayıtları**' nı seçin.
1. Uygulama kayıtları listesinde uygulamanızın adını arayın:

    ![Uygulama adınızı arayın](./media/batch-aad-auth/search-app-registration.png)

1. Uygulamayı seçin ve **API izinleri**' ni seçin.
1. **API izinleri** bölümünde **izin Ekle**' yi seçin.
1. **BIR API seçin**' de Batch API 'sini arayın. API'yi bulana kadar aşağıdaki dizelerden her birini arayın:
    1. **Microsoft Azure Batch**
    1. Batch API'sinin kimliği: **ddbf3205-c6bd-46ae-8127-60eb93363864**.
1. Batch API 'sini bulduktan sonra seçin ve **Seç**' i seçin.
1. **Izinleri Seç**' de, **erişim Azure Batch hizmeti** ' nin yanındaki onay kutusunu Işaretleyin ve ardından **izin Ekle**' yi seçin.

**API izinleri** bölümü artık Azure AD uygulamanızın hem Microsoft Graph hem de Batch hizmeti API 'sine erişimi olduğunu gösterir. Uygulamanızı Azure AD 'ye ilk kez kaydettiğinizde izinler otomatik olarak Microsoft Graph verilir.

![API izinleri verme](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Hizmet sorumlusu kullanma

Katılımsız çalışan bir uygulamanın kimliğini doğrulamak için bir hizmet sorumlusu kullanırsınız. Uygulamanızı kaydettikten sonra, hizmet sorumlusu yapılandırmak için Azure portal aşağıdaki adımları izleyin:

1. Uygulamanız için gizli anahtar isteyin.
1. Uygulamanıza rol tabanlı erişim denetimi (RBAC) atayın.

### <a name="request-a-secret-for-your-application"></a>Uygulamanız için gizli dizi isteyin

Uygulamanız bir hizmet sorumlusu ile kimlik doğrulaması gerçekleştiriyorsa, hem uygulama KIMLIĞINI hem de gizli anahtarı Azure AD 'ye gönderir. Kodunuzda kullanmak için gizli anahtar oluşturmanız ve kopyalamanız gerekir.

Azure portal şu adımları uygulayın:

1. Azure portal sol taraftaki Gezinti bölmesinde **tüm hizmetler**' i seçin. **Uygulama kayıtları**' nı seçin.
1. Uygulama kayıtları listesinden uygulamanızı seçin.
1. Uygulamayı seçin ve ardından **sertifikalar & parolaları**' nı seçin. **İstemci gizli** dizileri bölümünde **yeni istemci parolası**' nı seçin.
1. Gizli dizi oluşturmak için gizli dizi için bir açıklama girin. Sonra bir yıl, iki yıl veya süre sonu olmadan gizli dizi için bir süre sonu seçin.
1. Gizli dizi oluşturmak ve göstermek için **Ekle** ' yi seçin. Gizli değeri, sayfadan ayrıldıktan sonra tekrar erişemeyeceksiniz, güvenli bir yere kopyalayın.

    ![Gizli anahtar oluştur](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>Uygulamanıza RBAC atama

Hizmet sorumlusu ile kimlik doğrulaması yapmak için uygulamanıza RBAC atamanız gerekir. Şu adımları uygulayın:

1. Azure portal, uygulamanız tarafından kullanılan Batch hesabına gidin.
1. Batch hesabının **Ayarlar** bölümünde **Access Control (IAM)** öğesini seçin.
1. **Rol atamaları** sekmesini seçin.
1. Seçin **rol ataması Ekle**.
1. **Rol** açılır listesinden uygulamanız için *katkıda* bulunan veya *okuyucu* rolünü seçin. Bu roller hakkında daha fazla bilgi için, bkz. [Azure Portal rol tabanlı Access Control kullanmaya başlama](../role-based-access-control/overview.md).  
1. **Seç** alanına uygulamanızın adını girin. Listeden uygulamanızı seçin ve ardından **Kaydet**' i seçin.

Uygulamanız artık erişim denetimi ayarlarınızda bir RBAC rolü atanmış olarak görünmelidir.

![Uygulamanıza RBAC rolü atama](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>İçin Azure Active Directory Kiracı Kimliğinizi alma

Kiracı KIMLIĞI, uygulamanıza kimlik doğrulama hizmetleri sağlayan Azure AD kiracısını tanımlar. Kiracı Kimliğini almak için şu adımları izleyin:

1. Azure portalında Active Directory'nizi seçin.
1. Seçin **özellikleri**.
1. Sağlanan GUID değeri kopyalayın **dizin kimliği**. Bu değer Kiracı kimliği olarak da adlandırılır

![Dizin KIMLIĞINI Kopyala](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Kod örnekleri

Bu bölümdeki kod örnekleri, tümleşik kimlik doğrulaması ve hizmet sorumlusu kullanılarak Azure AD ile kimlik doğrulaması yapılacağını gösterir. Bu kod örneklerinin çoğu .NET kullanır, ancak kavramlar diğer dillere benzerdir.

> [!NOTE]
> Bir saatten sonra bir Azure AD kimlik doğrulama belirtecinin süresi dolar. Uzun süreli bir **Batchclient** nesnesi kullanırken, her zaman geçerli bir belirtece sahip olduğunuzdan emin olmak için her istekte adal 'dan bir belirteç almanızı öneririz. 
>
>
> .NET ' te bunu başarmak için, Azure AD 'den belirteci alan ve bu yöntemi bir temsilci olarak **BatchTokenCredentials** nesnesine geçiren bir yöntem yazın. Temsilci yöntemi, geçerli bir belirtecin sağlandığından emin olmak için Batch hizmetine yapılan her istekte çağrılır. Varsayılan olarak, ADAL önbelleklerinin belirteçleri, bu nedenle yalnızca gerektiğinde Azure AD 'den yeni bir belirteç alınır. Azure AD 'deki belirteçler hakkında daha fazla bilgi için bkz. [Azure AD Için kimlik doğrulama senaryoları][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Kod örneği: Batch .NET ile Azure AD Tümleşik kimlik doğrulamasını kullanma

Batch .NET 'ten tümleşik kimlik doğrulaması ile kimlik doğrulamak için [Azure Batch .net](https://www.nuget.org/packages/Microsoft.Azure.Batch/) paketine ve [adal](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) paketine başvurun.

Kodunuza aşağıdaki `using` deyimleri ekleyin:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Kodunuzda kiracı KIMLIĞI de dahil olmak üzere Azure AD uç noktasına başvurun. Kiracı KIMLIĞINI almak için [Azure Active Directory KIRACı kimliğini alma](#get-the-tenant-id-for-your-active-directory)bölümünde açıklanan adımları izleyin:

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Batch hizmeti kaynak uç noktasına başvurun:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Batch hesabınıza başvurun:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Uygulamanız için uygulama KIMLIĞINI (istemci KIMLIĞI) belirtin. Uygulama KIMLIĞI Azure portal uygulama kaydınızdan kullanılabilir:

```csharp
private const string ClientId = "<application-id>";
```

Ayrıca, uygulamanızı yerel bir uygulama olarak kaydettirdiğiniz takdirde belirttiğiniz yeniden yönlendirme URI 'sini kopyalayın. Kodunuzda belirtilen yeniden yönlendirme URI 'SI, uygulamayı kaydettiğinizde belirttiğiniz yeniden yönlendirme URI 'siyle eşleşmelidir:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Azure AD 'den kimlik doğrulama belirtecini almak için bir geri çağırma yöntemi yazın. Burada gösterilen **Getauthenticationtokenasync** geri çağırma yöntemi, uygulamayla etkileşime geçen bir kullanıcının kimliğini doğrulamak için adal çağırır. ADAL tarafından sağlanan **Acquiretokenasync** Yöntemi kullanıcıdan kimlik bilgilerini ister ve Kullanıcı bunları sağladıkça (önceden önbelleğe alınmış kimlik bilgileri yoksa) uygulama devam eder:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Temsilciyi bir parametre olarak alan bir **BatchTokenCredentials** nesnesi oluşturun. **Batchclient** nesnesini açmak için bu kimlik bilgilerini kullanın. Batch hizmetinde sonraki işlemler için bu **Batchclient** nesnesini kullanabilirsiniz:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Kod örneği: Batch .NET ile bir Azure AD hizmet sorumlusu kullanma

Batch .NET 'ten bir hizmet sorumlusu ile kimlik doğrulaması yapmak için [Azure Batch .net](https://www.nuget.org/packages/Azure.Batch/) paketine ve [adal](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) paketine başvurun.

Kodunuza aşağıdaki `using` deyimleri ekleyin:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Kodunuzda kiracı KIMLIĞI de dahil olmak üzere Azure AD uç noktasına başvurun. Hizmet sorumlusu kullanırken kiracıya özgü bir uç nokta sağlamanız gerekir. Kiracı KIMLIĞINI almak için [Azure Active Directory KIRACı kimliğini alma](#get-the-tenant-id-for-your-active-directory)bölümünde açıklanan adımları izleyin:

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Batch hizmeti kaynak uç noktasına başvurun:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Batch hesabınıza başvurun:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Uygulamanız için uygulama KIMLIĞINI (istemci KIMLIĞI) belirtin. Uygulama KIMLIĞI Azure portal uygulama kaydınızdan kullanılabilir:

```csharp
private const string ClientId = "<application-id>";
```

Azure portal kopyaladığınız gizli anahtarı belirtin:

```csharp
private const string ClientKey = "<secret-key>";
```

Azure AD 'den kimlik doğrulama belirtecini almak için bir geri çağırma yöntemi yazın. Burada gösterilen **Getauthenticationtokenasync** geri çağırma yöntemi, katılımsız kimlik doğrulaması için adal çağırır:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Temsilciyi bir parametre olarak alan bir **BatchTokenCredentials** nesnesi oluşturun. **Batchclient** nesnesini açmak için bu kimlik bilgilerini kullanın. Ardından Batch hizmetinde sonraki işlemler için bu **Batchclient** nesnesini kullanın:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Kod örneği: Batch Python ile bir Azure AD hizmet sorumlusu kullanma

Batch Python 'dan bir hizmet sorumlusu ile kimlik doğrulaması yapmak için [Azure-Batch](https://pypi.org/project/azure-batch/) ve [Azure-Common](https://pypi.org/project/azure-common/) modüllerini yükleyip başvuru yapın.

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Hizmet sorumlusu kullanırken kiracı KIMLIĞINI sağlamanız gerekir. Kiracı KIMLIĞINI almak için [Azure Active Directory KIRACı kimliğini alma](#get-the-tenant-id-for-your-active-directory)bölümünde açıklanan adımları izleyin:

```python
TENANT_ID = "<tenant-id>"
```

Batch hizmeti kaynak uç noktasına başvurun:  

```python
RESOURCE = "https://batch.core.windows.net/"
```

Batch hesabınıza başvurun:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Uygulamanız için uygulama KIMLIĞINI (istemci KIMLIĞI) belirtin. Uygulama KIMLIĞI Azure portal uygulama kaydınızdan kullanılabilir:

```python
CLIENT_ID = "<application-id>"
```

Azure portal kopyaladığınız gizli anahtarı belirtin:

```python
SECRET = "<secret-key>"
```

**Serviceprincipalcredentials** nesnesi oluştur:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Hizmet sorumlusu kimlik bilgilerini kullanarak bir **BatchServiceClient** nesnesi açın. Ardından, Batch hizmetinde sonraki işlemler için bu **BatchServiceClient** nesnesini kullanın.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Sonraki adımlar

- Azure AD hakkında daha fazla bilgi edinmek için [Azure Active Directory belgelerine](https://docs.microsoft.com/azure/active-directory/)bakın. ADAL 'in nasıl kullanılacağını gösteren ayrıntılı örnekler, [Azure kod örnekleri](https://azure.microsoft.com/resources/samples/?service=active-directory) kitaplığı 'nda bulunabilir.

- Hizmet sorumluları hakkında daha fazla bilgi edinmek için [Azure Active Directory Içindeki uygulama ve hizmet sorumlusu nesneleri](../active-directory/develop/app-objects-and-service-principals.md)bölümüne bakın. Azure portal kullanarak bir hizmet sorumlusu oluşturmak için, bkz. [kaynaklara erişebilen Active Directory uygulama ve hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md). PowerShell veya Azure CLı ile bir hizmet sorumlusu da oluşturabilirsiniz.

- Azure AD 'yi kullanarak Batch yönetimi uygulamalarının kimliğini doğrulamak için bkz. [Active Directory Ile Batch yönetimi çözümlerini kimlik doğrulama](batch-aad-auth-management.md).

- Azure AD belirteci kullanılarak kimliği doğrulanmış bir Batch istemcisi oluşturma hakkında bir Python örneği için bkz. [Python betik örneği ile Azure Batch özel görüntü dağıtma](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) .

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Azure Active Directory nedir?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Azure AD için kimlik doğrulama senaryoları"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Uygulamaları Azure Active Directory tümleştirme"
[azure_portal]: https://portal.azure.com
