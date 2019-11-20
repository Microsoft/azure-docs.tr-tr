---
title: Batch yönetimi çözümlerini doğrulamak için Azure Active Directory kullanma | Microsoft Docs
description: Azure Resource Manager ile oluşturulan uygulamalar ve Batch kaynak sağlayıcısı, Azure AD ile kimlik doğrular.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: lahugh
ms.openlocfilehash: 3f7ba22fa8e2a8709fc37a891b3da64b6d83e654
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095638"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Active Directory ile Batch yönetimi çözümlerini kimlik doğrulama

Azure Batch Management hizmetini çağıran uygulamalar [Azure Active Directory][aad_about] (Azure AD) ile kimlik doğrular. Azure AD, Microsoft 'un çok kiracılı bulut tabanlı dizin ve kimlik yönetimi hizmetidir. Azure, müşterilerinin, hizmet yöneticilerinin ve kurumsal kullanıcıların kimlik doğrulaması için Azure AD kullanır.

Batch yönetimi .NET kitaplığı, Batch hesapları, hesap anahtarları, uygulamalar ve uygulama paketleri ile çalışma türlerini gösterir. Batch yönetimi .NET kitaplığı bir Azure Kaynak sağlayıcısı istemcsahiptir ve bu kaynakları programlı bir şekilde yönetmek için [Azure Resource Manager][resman_overview] birlikte kullanılır. Batch yönetimi .NET kitaplığı ve [Azure Resource Manager][resman_overview]aracılığıyla herhangi bir Azure Kaynak sağlayıcısı istemcisiyle yapılan isteklerin kimliğini doğrulamak IÇIN Azure AD gerekir.

Bu makalede, Batch yönetimi .NET kitaplığı kullanan uygulamalardan kimlik doğrulaması yapmak için Azure AD ile ilgili araştırma yaptık. Tümleşik kimlik doğrulaması kullanarak bir abonelik yöneticisinin veya ortak yöneticinin kimliğini doğrulamak için Azure AD 'nin nasıl kullanılacağını göstereceğiz. Toplu Işlem yönetimi .NET kitaplığı ile Azure AD 'yi kullanmayı adım adım incelemek için GitHub 'da bulunan [AccountManagement][acct_mgmt_sample] örnek projesini kullanıyoruz.

Batch yönetimi .NET kitaplığı ve AccountManagement örneğini kullanma hakkında daha fazla bilgi edinmek için bkz. [.net Için Batch Management istemci kitaplığı Ile Batch hesaplarını ve kotaları yönetme](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Uygulamanızı Azure AD 'ye kaydetme

Azure [Active Directory Authentication Library][aad_adal] (ADAL), Azure AD 'ye uygulamalarınız dahilinde kullanılmak üzere programlı bir arabirim sağlar. Uygulamanızdan ADAL 'yi çağırmak için uygulamanızı bir Azure AD kiracısına kaydetmeniz gerekir. Uygulamanızı kaydettiğinizde, Azure AD kiracısında bir ad dahil olmak üzere uygulamanız hakkındaki bilgilerle Azure AD sağlarsınız. Daha sonra Azure AD, uygulamanızı çalışma zamanında Azure AD ile ilişkilendirmek için kullandığınız bir uygulama KIMLIĞI sağlar. Uygulama KIMLIĞI hakkında daha fazla bilgi edinmek için [Azure Active Directory Içindeki uygulama ve hizmet sorumlusu nesneleri](../active-directory/develop/app-objects-and-service-principals.md)bölümüne bakın.

AccountManagement örnek uygulamasını kaydetmek için [uygulamaları Azure Active Directory Ile tümleştirme][aad_integrate]bölümünde [uygulama ekleme](../active-directory/develop/quickstart-register-app.md) bölümündeki adımları izleyin. Uygulama türü için **Yerel Istemci uygulamasını** belirtin. **Yeniden yönlendirme URI 'si** `urn:ietf:wg:oauth:2.0:oob`için sektör standardı OAuth 2,0 URI 'si. Ancak, gerçek bir uç nokta olması gerekmeyen için `http://myaccountmanagementsample` **yeniden yönlendirme URI 'si**için geçerli herhangi bir URI (gibi) belirtebilirsiniz:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Kayıt işlemini tamamladıktan sonra uygulamanız için listelenen uygulama KIMLIĞINI ve nesne (hizmet sorumlusu) KIMLIĞINI görürsünüz.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Uygulamanıza Azure Resource Manager API erişimi verme

Daha sonra, Azure Resource Manager API 'sine uygulamanıza erişim yetkisi almanız gerekir. Kaynak Yöneticisi API 'SI için Azure AD tanımlayıcısı, **Microsoft azure hizmet yönetim API'si**.

Azure portal şu adımları uygulayın:

1. Azure portal sol taraftaki Gezinti bölmesinde **tüm hizmetler**' i seçin, **uygulama kayıtları**' nı ve **Ekle**' yi tıklatın.
2. Uygulama kayıtları listesinde uygulamanızın adını arayın:

    ![Uygulama adınızı arayın](./media/batch-aad-auth-management/search-app-registration.png)

3. **Ayarlar** dikey penceresini görüntüleyin. İçinde **API erişimi** bölümünden **gerekli izinler**.
4. Yeni bir gerekli izin eklemek için **Ekle** ' ye tıklayın. 
5. Adım 1 ' de **Windows Azure hizmet yönetim API'si**girin, sonuçlar listesinden API ' yi seçin ve **Seç** düğmesine tıklayın.
6. 2\. adımda, **Azure klasik dağıtım modeline kuruluş kullanıcıları olarak erişim**' ın yanındaki onay kutusunu Işaretleyin ve **Seç** düğmesine tıklayın.
7. **Bitti** düğmesine tıklayın.

**Gerekli izinler** dikey penceresi artık, uygulamanıza yönelik IZINLERIN hem adal hem de Kaynak Yöneticisi API 'lerine verildiğini gösterir. Uygulamanızı Azure AD 'ye ilk kez kaydettiğinizde, varsayılan olarak ADAL 'ye izin verilir.

![Azure Resource Manager API 'sine temsilci izinleri verme](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD uç noktaları

Batch yönetimi çözümlerinizin kimliğini Azure AD ile doğrulamak için, iyi bilinen iki uç nokta gerekir.

- **Azure AD ortak uç noktası** , tümleşik kimlik doğrulaması durumunda olduğu gibi belirli bir kiracı sağlanmazsa genel bir kimlik bilgisi toplama arabirimi sağlar:

    `https://login.microsoftonline.com/common`

- **Azure Resource Manager uç noktası** , Batch Yönetim hizmetine yapılan isteklerin kimliğini doğrulamak için bir belirteç almak üzere kullanılır:

    `https://management.core.windows.net/`

AccountManagement örnek uygulaması, bu uç noktaların sabitlerini tanımlar. Bu sabitleri değiştirmeden bırakın:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Uygulama KIMLIĞINIZE başvurun 

İstemci uygulamanız, çalışma zamanında Azure AD 'ye erişmek için uygulama KIMLIĞINI (istemci KIMLIĞI olarak da anılır) kullanır. Uygulamanızı Azure portal kaydettikten sonra, kayıtlı uygulamanız için Azure AD tarafından sunulan uygulama KIMLIĞINI kullanmak üzere kodunuzu güncelleştirin. AccountManagement örnek uygulamasında, uygulama KIMLIĞINIZI Azure portal uygun sabitine kopyalayın:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Ayrıca, kayıt işlemi sırasında belirttiğiniz yeniden yönlendirme URI 'sini kopyalayın. Kodunuzda belirtilen yeniden yönlendirme URI 'SI, uygulamayı kaydettiğinizde belirttiğiniz yeniden yönlendirme URI 'siyle eşleşmelidir.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD kimlik doğrulaması belirteci alma

AccountManagement örneğini Azure AD kiracısına kaydettikten ve örnek kaynak kodunu değerlerinizle güncelleştirdikten sonra, örnek Azure AD kullanarak kimlik doğrulamaya hazırlanmaya hazırdır. Örneği çalıştırdığınızda, ADAL bir kimlik doğrulama belirteci edinmeye çalışır. Bu adımda, sizden Microsoft kimlik bilgilerinizi ister: 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Kimlik bilgilerinizi verdikten sonra örnek uygulama, Batch Yönetim hizmetine kimliği doğrulanmış istekler vermek için devam edebilir. 

## <a name="next-steps"></a>Sonraki adımlar

[AccountManagement örnek uygulamasını][acct_mgmt_sample]çalıştırma hakkında daha fazla bilgi için bkz. [.NET için Batch Management Istemci kitaplığı ile Batch hesaplarını ve kotaları yönetme](batch-management-dotnet.md).

Azure AD hakkında daha fazla bilgi edinmek için [Azure Active Directory belgelerine](https://docs.microsoft.com/azure/active-directory/)bakın. ADAL 'in nasıl kullanılacağını gösteren ayrıntılı örnekler, [Azure kod örnekleri](https://azure.microsoft.com/resources/samples/?service=active-directory) kitaplığı 'nda bulunabilir.

Azure AD 'yi kullanarak Batch hizmeti uygulamalarının kimliğini doğrulamak için bkz. [Active Directory Ile Batch hizmeti çözümlerini kimlik doğrulama](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Azure Active Directory nedir?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Azure AD için kimlik doğrulama senaryoları"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Uygulamaları Azure Active Directory tümleştirme"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
