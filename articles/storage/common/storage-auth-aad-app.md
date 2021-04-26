---
title: İstemci uygulamasından gelen istekleri yetkilendirmek için Azure AD 'den bir belirteç alın
titleSuffix: Azure Storage
description: İstemci uygulama içinden kimlik doğrulamak, bir OAuth 2,0 belirteci edinmek ve Azure Blob depolama ve kuyruk depolama 'ya istekleri yetkilendirmek için Azure Active Directory kullanın.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: baad1a0b173ae89fec9d160572224c6cb0aa615d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574637"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>İstemci uygulamasından gelen istekleri yetkilendirmek için Azure AD 'den bir belirteç alın

Azure Blob depolama veya kuyruk depolama ile Azure Active Directory (Azure AD) kullanmanın önemli bir avantajı, kimlik bilgilerinizin artık kodunuzda depolanmasına gerek kalmaz. Bunun yerine, Microsoft Identity platform 'dan bir OAuth 2,0 erişim belirteci isteyebilirsiniz. Azure AD, uygulamayı çalıştıran güvenlik sorumlusu (bir Kullanıcı, Grup veya hizmet sorumlusu) kimliğini doğrular. Kimlik doğrulaması başarılı olursa, Azure AD uygulamaya erişim belirtecini döndürür ve uygulama, istekleri Azure Blob depolama veya kuyruk depolama alanına yetkilendirmek için erişim belirtecini kullanabilir.

Bu makalede, yerel uygulamanızı veya Web uygulamanızı, indirmek üzere kullanılabilecek örnek bir uygulama kullanarak Microsoft Identity platformu ile kimlik doğrulaması için nasıl yapılandıracağınız gösterilir. Örnek uygulama .NET özellikleri, ancak diğer diller de benzer bir yaklaşım kullanır. Microsoft Identity platformu hakkında daha fazla bilgi için bkz. [Microsoft Identity platform genel bakış](../../active-directory/develop/v2-overview.md).

OAuth 2,0 kod verme akışına genel bakış için bkz. [oauth 2,0 kod verme akışını kullanarak Azure Active Directory Web uygulamalarına erişimi yetkilendirme](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="about-the-sample-application"></a>Örnek uygulama hakkında

Örnek uygulama, bir Web uygulamasının bir yerel geliştirme ortamında Azure AD ile kimlik doğrulaması için nasıl yapılandırılacağını gösteren uçtan uca bir deneyim sunar. Örnek uygulamayı görüntülemek ve çalıştırmak için öncelikle [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal)'dan kopyalayın veya indirin. Ardından, bir Azure Uygulama kaydını yapılandırmak ve ortamınız için uygulamayı güncelleştirmek üzere makalesinde özetlenen adımları izleyin.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Azure AD güvenlik sorumlusuna rol atama

Azure depolama uygulamanızdan bir güvenlik sorumlusunun kimliğini doğrulamak için önce bu güvenlik sorumlusu için Azure rol tabanlı erişim denetimi (Azure RBAC) ayarlarını yapılandırın. Azure depolama, kapsayıcılar ve kuyruklar için izinleri çevreleyen yerleşik rolleri tanımlar. Azure rolü bir güvenlik sorumlusuna atandığında, bu güvenlik sorumlusuna bu kaynağa erişim verilir. Daha fazla bilgi için bkz. Azure [BLOB ile erişim haklarını yönetme ve Azure RBAC Ile kuyruk verileri](./storage-auth-aad-rbac-portal.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Uygulamanızı bir Azure AD kiracısıyla kaydetme

Depolama kaynaklarına erişimi yetkilendirmek için Azure AD kullanmanın ilk adımı, istemci uygulamanızı [Azure Portal](https://portal.azure.com)BIR Azure AD kiracısıyla kaydetmekte. İstemci uygulamanızı kaydettiğinizde, Azure AD 'ye uygulama hakkında bilgi sağlarsınız. Daha sonra Azure AD, uygulamanızı çalışma zamanında Azure AD ile ilişkilendirmek için kullandığınız bir istemci KIMLIĞI ( *uygulama kimliği* olarak da bilinir) sağlar. İstemci KIMLIĞI hakkında daha fazla bilgi edinmek için [Azure Active Directory Içindeki uygulama ve hizmet sorumlusu nesneleri](../../active-directory/develop/app-objects-and-service-principals.md)bölümüne bakın. Azure depolama uygulamanızı kaydetmek için [hızlı başlangıç: bir uygulamayı Microsoft Identity platformu Ile kaydetme](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)bölümünde gösterilen adımları izleyin. 

Aşağıdaki görüntüde bir Web uygulamasını kaydetmeye yönelik genel ayarlar gösterilmektedir. Bu örnekte, yeniden yönlendirme URI 'sinin `http://localhost:5000/signin-oidc` geliştirme ortamında örnek uygulamayı test etmek için olarak ayarlandığını unutmayın. Bu ayarı daha sonra Azure portal kayıtlı uygulamanızın **kimlik doğrulama** ayarı altında değiştirebilirsiniz:

:::image type="content" source="media/storage-auth-aad-app/app-registration.png" alt-text="Depolama uygulamanızın Azure AD 'ye nasıl kaydedileceği gösteren ekran görüntüsü":::

> [!NOTE]
> Uygulamanızı yerel bir uygulama olarak kaydettiğinizde, **yeniden yönlendirme URI 'si** için GEÇERLI bir URI belirtebilirsiniz. Yerel uygulamalar için, bu değerin gerçek bir URL olması gerekmez. Web uygulamaları için, yeniden yönlendirme URI 'si, belirteçlerin sağlandığı URL 'YI belirttiğinden geçerli bir URI olmalıdır.

Uygulamanızı kaydettikten sonra **Ayarlar** altında uygulama kimliğini (veya istemci kimliğini) görürsünüz:

:::image type="content" source="media/storage-auth-aad-app/app-registration-client-id.png" alt-text="İstemci KIMLIĞINI gösteren ekran görüntüsü":::

Bir uygulamayı Azure AD 'ye kaydetme hakkında daha fazla bilgi için bkz. [uygulamaları Azure Active Directory tümleştirme](../../active-directory/develop/quickstart-register-app.md).

### <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Kayıtlı uygulama izinlerinizi Azure Storage 'a verme

Daha sonra, uygulamanıza Azure depolama API 'Lerini çağırma izni verin. Bu adım, uygulamanızın Azure AD ile Azure depolama 'ya istekleri yetkilendirebilmesini sağlar.

1. Kayıtlı uygulamanızın **API izinleri** sayfasında **izin Ekle**' yi seçin.
1. **Microsoft API 'leri** sekmesinde **Azure Storage**' ı seçin.
1. **İstek API 'si izinleri** bölmesinde, **uygulamanız gereken izin türü altında mı?**, kullanılabilir izin türünün **temsilci izinleri** olduğunu gözlemleyin. Bu seçenek varsayılan olarak sizin için seçilidir.
1. **İzinler** altında, **user_impersonation**' nin yanındaki onay kutusunu seçin ve ardından **izin Ekle** düğmesini seçin.

    :::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-1.png" alt-text="Depolama API 'SI için izinleri gösteren ekran görüntüsü":::

1. Ardından, **varsayılan dizin için yönetici Izni ver**' i tıklayarak bu izinler için yönetici onayı verin.

**API izinleri** bölmesi artık KAYıTLı Azure AD uygulamanızın hem Microsoft Graph hem de Azure Storage API 'lerine erişimi olduğunu ve bu izin varsayılan dizin için verildiğini gösterir. Uygulamanızı Azure AD 'ye ilk kez kaydettiğinizde izinler otomatik olarak Microsoft Graph verilir.

:::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-2.png" alt-text="Kayıtlı uygulama için API izinlerini gösteren ekran görüntüsü":::

### <a name="create-a-client-secret"></a>İstemci parolası oluşturma

Uygulamanın bir belirteç istenirken kimliğini kanıtlamak için bir istemci parolası gerekir. İstemci parolasını eklemek için şu adımları izleyin:

1. Azure portal uygulama kaydınız ' ne gidin.
1. **Sertifikalar & gizli** dizi ayarını seçin.
1. **İstemci** gizli dizileri altında yeni bir gizli dizi oluşturmak için **yeni istemci parolası** ' na tıklayın.
1. Gizli dizi için bir açıklama sağlayın ve istenen süre sonu aralığını seçin.
1. Yeni Gizliliğin değerini hemen güvenli bir konuma kopyalayın. Tam değer size yalnızca bir kez gösterilir.

    ![İstemci gizliliğini gösteren ekran görüntüsü](media/storage-auth-aad-app/client-secret.png)

### <a name="enable-implicit-grant-flow"></a>Örtük verme akışını etkinleştir

Ardından, uygulamanız için örtük verme akışını yapılandırın. Şu adımları izleyin:

1. Azure portal uygulama kaydınız ' ne gidin.
1. **Yönet** bölümünde **kimlik doğrulama** ayarını seçin.
1. **Örtük izin** bölümünde, aşağıdaki görüntüde gösterildiği gıbı, kimlik belirteçlerini etkinleştirmek için onay kutusunu seçin:

    :::image type="content" source="media/storage-auth-aad-app/enable-implicit-grant-flow.png" alt-text="Örtük izin akışı için ayarların nasıl etkinleştirileceğini gösteren ekran görüntüsü":::

## <a name="client-libraries-for-token-acquisition"></a>Belirteç alımı için istemci kitaplıkları

Uygulamanızı kaydettikten ve Azure Blob depolama veya kuyruk depolama alanındaki verilere erişim izni verdikten sonra, bir güvenlik sorumlusunun kimliğini doğrulamak ve bir OAuth 2,0 belirteci edinmek için uygulamanıza kod ekleyebilirsiniz. Belirtecin kimliğini doğrulamak ve almak için, [Microsoft kimlik platformu kimlik doğrulama kitaplıklarından](../../active-directory/develop/reference-v2-libraries.md) birini veya OpenID Connect 1,0 'yi destekleyen başka bir açık kaynak kitaplığı kullanabilirsiniz. Uygulamanız daha sonra, Azure Blob depolama veya kuyruk depolama 'ya karşı bir isteği yetkilendirmek için erişim belirtecini kullanabilir.

Belirteçleri alma için desteklenen senaryoların listesi için, [Microsoft kimlik doğrulama kitaplığı (msal)](../../active-directory/develop/msal-overview.md) belgelerinin [kimlik doğrulama akışları](../../active-directory/develop/msal-authentication-flows.md) bölümüne bakın.

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

Ardından, **Depolama Blobu veri katılımcısı** rolünü, örnek kodu çalıştıracağınız Kullanıcı hesabına açıkça atayın. Bu rolün Azure portal nasıl atanacağı hakkında yönergeler için bkz. [Azure Portal kullanarak blob ve sıra verilerine erişim için bir Azure rolü atama](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Bir Azure depolama hesabı oluşturduğunuzda, Azure AD aracılığıyla verilere erişim için otomatik olarak izinler atanmamıştır. Azure depolama için kendinize açık bir Azure rolü atamanız gerekir. Aboneliğiniz, kaynak grubunuz, depolama hesabınız veya Kapsayıcınız ya da kuyruğunuzun düzeyinde atayabilirsiniz.
>
> Veri erişimi için kendinize bir rol atamadan önce, Azure portal Azure portal aracılığıyla Depolama hesabınızdaki verilere erişebilirsiniz. bu nedenle, veri erişimi için hesap anahtarını da kullanabilir. Daha fazla bilgi için bkz. [Azure Portal blob verilerine erişimi yetkilendirmeyi seçme](../blobs/authorize-data-operations-portal.md).

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Azure AD ile blob depolamaya erişim yetkisi veren bir Web uygulaması oluşturma

Uygulamanız Azure Storage 'a eriştiğinde Kullanıcı adına bunu yapar, yani blob veya kuyruk kaynaklarına oturum açan kullanıcının izinleri kullanılarak erişilir. Bu kod örneğini denemek için kullanıcıdan bir Azure AD kimliği kullanarak oturum açmasını isteyen bir Web uygulamasına ihtiyacınız vardır. Kendinizinkini oluşturabilir veya Microsoft tarafından sunulan örnek uygulamayı kullanabilirsiniz.

Belirteç içeren ve Azure Storage 'da blob oluşturmak için onu kullanan tamamlanmış örnek bir Web uygulaması [GitHub](https://aka.ms/aadstorage)' da kullanılabilir. Tamamlanan örneği gözden geçirmek ve çalıştırmak, kod örneklerini anlamak için yararlı olabilir. Tamamlanmış örneği çalıştırma hakkında yönergeler için, görünüm başlıklı bölüme bakın [ve tamamlanan örneği çalıştırın](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Başvuruları ve using deyimlerini ekleyin  

Visual Studio 'dan Azure Storage istemci kitaplığı 'nı yükler. **Araçlar** menüsünden **NuGet Paket Yöneticisi**’ni ve ardından **Paket Yöneticisi Konsolu**’nu seçin. Gerekli paketleri .NET için Azure Storage istemci kitaplığından yüklemek üzere konsol penceresine aşağıdaki komutları yazın:

# <a name="net-v12-sdk"></a>[.NET V12 SDK](#tab/dotnet)

```console
Install-Package Azure.Storage.Blobs
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview
```

Ardından, aşağıdaki using deyimlerini HomeController. cs dosyasına ekleyin:

```csharp
using Microsoft.Identity.Web; //MSAL library for getting the access token
using Azure.Storage.Blobs;
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview //or a later version
```

Ardından, aşağıdaki using deyimlerini HomeController. cs dosyasına ekleyin:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

---

#### <a name="create-a-block-blob"></a>Blok Blobu oluşturma

Blok Blobu oluşturmak için aşağıdaki kod parçacığını ekleyin. Açılı ayraçlar içindeki değerleri kendi değerlerinizle değiştirmeyi unutmayın:

# <a name="net-v12-sdk"></a>[.NET V12 SDK](#tab/dotnet)

```csharp
private static async Task<string> CreateBlob(TokenAcquisitionTokenCredential tokenCredential)
{
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    BlobClient blobClient = new BlobClient(blobUri, tokenCredential);

    string blobContents = "Blob created by Azure AD authenticated user.";
    byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

    using (MemoryStream stream = new MemoryStream(byteArray))
    {
        await blobClient.UploadAsync(stream);
    }
    return "Blob successfully created";
}
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user.
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with the URL to your blob.
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

---

> [!NOTE]
> Blob ve kuyruk işlemlerini bir OAuth 2,0 belirteciyle yetkilendirmek için HTTPS kullanmanız gerekir.

Yukarıdaki örnekte, .NET istemci kitaplığı, blok blobu oluşturma isteğinin yetkilendirmesini işler. Diğer dillere yönelik Azure depolama istemci kitaplıkları da isteğin yetkilendirmesini de işler. Ancak, REST API kullanarak bir OAuth belirteci ile Azure depolama işlemini arıyorsanız, OAuth belirtecini kullanarak **Yetkilendirme** üst bilgisini oluşturmanız gerekecektir.

Blob ve Kuyruk hizmeti işlemlerini OAuth erişim belirteçlerini kullanarak çağırmak için, aşağıdaki örnekte gösterildiği gibi, erişim belirtecini, **taşıyıcı** şemasını kullanarak **Yetkilendirme** üst bilgisine geçirin ve 2017-11-09 veya üzeri bir hizmet sürümünü belirtin:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-access-token-from-azure-ad"></a>Azure AD'den erişim belirteci alma

Sonra, Azure AD 'den Kullanıcı adına belirteç isteyen bir yöntem ekleyin. Bu yöntem, izin verilecek kapsamı tanımlar. İzinler ve kapsamlar hakkında daha fazla bilgi için bkz. [Microsoft Identity platform uç noktasındaki izinler ve onay](../../active-directory/develop/v2-permissions-and-consent.md).

Belirtecin alınacağı kapsamı oluşturmak için kaynak KIMLIĞINI kullanın. Örnek, kaynak KIMLIĞINI yerleşik kapsamla birlikte kullanarak `user_impersonation` , belirtecin Kullanıcı adına istenmekte olduğunu gösteren kapsamı oluşturur.

Kullanıcıya, Kullanıcı adına belirteç isteme izni vermesini sağlayan bir arabirim ile Kullanıcı sunmanız gerektiğini aklınızda bulundurun:

```csharp
[AuthorizeForScopes(Scopes = new string[] { "https://storage.azure.com/user_impersonation" })]
public async Task<IActionResult> Blob()
{
    string message = await CreateBlob(new TokenAcquisitionTokenCredential(_tokenAcquisition));
    ViewData["Message"] = message;
    return View();
}
```

Onay, bir kullanıcının adına korumalı kaynaklara erişmesi için yetkilendirme izni veren bir işlemdir. Microsoft Identity platformu artımlı onayı destekler, yani bir güvenlik sorumlusu başlangıçta minimum izin kümesi isteyebilir ve gerektiğinde zaman içinde izinler ekleyebilir. Kodunuz bir erişim belirteci istediğinde, uygulamanızın ihtiyaç duyacağı izin kapsamını belirtin. Artımlı izin hakkında daha fazla bilgi için bkz. [artımlı ve dinamik izin](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

## <a name="view-and-run-the-completed-sample"></a>Tamamlanan örneği görüntüleme ve çalıştırma

Örnek uygulamayı çalıştırmak için öncelikle [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal)'dan kopyalayın veya indirin. Ardından, aşağıdaki bölümlerde açıklandığı şekilde uygulamayı güncelleştirin.

### <a name="provide-values-in-the-settings-file"></a>Ayarlar dosyasında değer sağlama

Dosyadaki *appsettings.js* aşağıdaki gibi kendi değerlerinizle güncelleştirin:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "ClientSecret": "<client-secret>",
    "ClientCertificates": [
    ],
    "CallbackPath": "/signin-oidc"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Depolama hesabı ve kapsayıcı adını güncelleştirme

*HomeController. cs* dosyasında, blok BLOBUNA başvuran URI 'yi, bir depolama hesabınızın ve kapsayıcının adını kullanacak şekilde güncelleştirin, açılı ayraçlar içindeki değerleri kendi değerlerinizle değiştirin:

```html
https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt
```

## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft kimlik platformu](../../active-directory/develop/index.yml)
- [Azure RBAC ile depolama verilerine erişim haklarını yönetme](./storage-auth-aad-rbac-portal.md)
- [Azure kaynakları için Azure Active Directory ve yönetilen kimliklerle blob 'lara ve kuyruklara erişim kimlik doğrulamasını yapın](storage-auth-aad-msi.md)
