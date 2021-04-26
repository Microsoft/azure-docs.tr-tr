---
title: API kimlik doğrulaması ve yetkilendirme-Azure Time Series Insights | Microsoft Docs
description: Bu makalede, Azure Time Series Insights API 'sini çağıran özel bir uygulama için kimlik doğrulama ve yetkilendirmeyi yapılandırma açıklanmaktadır.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref, devx-track-azurecli
ms.openlocfilehash: 8e50b650eaffe3d0ec8d3d2cd1841bd139d33750
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867521"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API’si için kimlik doğrulaması ve yetkilendirme

İş gereksinimlerinize bağlı olarak, çözümünüz Azure Time Series Insights ortamınızın [API 'leriyle](/rest/api/time-series-insights/reference-data-access-overview)etkileşim kurmak için kullandığınız bir veya daha fazla istemci uygulaması içerebilir. Azure Time Series Insights, [OAUTH 2,0 tabanlı Azure AD güvenlik belirteçlerini](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)kullanarak kimlik doğrulaması gerçekleştirir. İstemcilerinizdeki kimlik doğrulaması için doğru izinlere sahip bir taşıyıcı belirteç almanız ve API çağrılarınızla birlikte geçireceğiz. Bu belge, yönetilen kimlik ve Azure Active Directory uygulama kaydı dahil olmak üzere bir taşıyıcı belirteci almak için kullanabileceğiniz kimlik bilgilerini almak için çeşitli yöntemler açıklar.

## <a name="managed-identities"></a>Yönetilen kimlikler

Aşağıdaki bölümlerde, Azure Time Series Insights API 'sine erişmek için Azure Active Directory (Azure AD) tarafından yönetilen bir kimliğin nasıl kullanılacağı açıklanır. Azure 'da Yönetilen kimlikler, Azure AD 'de Azure kaynağı için bir kimlik sağlayarak ve Azure Active Directory (Azure AD) belirteçleri elde etmek üzere kullanarak kimlik bilgilerini yönetmek zorunda olan geliştiricilerin gereksinimini ortadan kaldırır. Yönetilen kimlikler kullanmanın avantajlarından bazıları şunlardır:

- Kimlik bilgilerini yönetmeniz gerekmez. Kimlik bilgilerine, sizin için de erişilebilir.
- Azure Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir Azure hizmetinde kimlik doğrulaması yapmak için Yönetilen kimlikler kullanabilirsiniz.
- Yönetilen kimlikler herhangi bir ek maliyet olmadan kullanılabilir.

İki tür yönetilen kimlik hakkında daha fazla bilgi edinmek için [Azure kaynakları için yönetilen kimlikleri](../active-directory/managed-identities-azure-resources/overview.md) okuyun.

Yönetilen kimlikleri kullanarak şunları yapabilirsiniz:

- Azure VM’leri
- Azure Uygulama Hizmetleri
- Azure İşlevleri
- Azure Container Instances
- ve daha fazlası...

Listenin tamamı için [Azure kaynakları için yönetilen kimlikleri destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) bölümüne bakın.

## <a name="azure-active-directory-app-registration"></a>Azure Active Directory uygulama kaydı

Kimlik bilgilerini yönetmeniz gerekmiyorsa, mümkün olduğunda yönetilen kimlikler kullanmanızı öneririz. İstemci uygulamanız yönetilen kimlikleri destekleyen bir Azure hizmetinde barındırılıyorsa, uygulamanızı bir Azure AD kiracısı ile kaydedebilirsiniz. Uygulamanızı Azure AD 'ye kaydettiğinizde, uygulamanız için Azure AD ile tümleşmesini sağlayan bir kimlik yapılandırması oluşturuyorsunuz. [Azure Portal](https://portal.azure.com/)bir uygulamayı kaydettiğinizde, tek bir kiracı (yalnızca kiracınızda erişilebilir) veya çok kiracılı (diğer kiracılarda erişilebilir) olduğunu ve isteğe bağlı olarak bir yeniden yönlendirme URI 'si (erişim belirtecinin gönderildiği konum) ayarlayabileceğinizi seçersiniz.

Uygulama kaydını tamamladığınızda, ana kiracınızda veya dizininizde bulunan, uygulamanın (uygulama nesnesi) genel olarak benzersiz bir örneğine sahip olursunuz. Ayrıca, uygulamanız için genel olarak benzersiz bir KIMLIĞINIZ (uygulama veya istemci KIMLIĞI) vardır. Portalda, uygulamanızı çalışır hale getirmek, oturum açma iletişim kutusunda uygulamanızın markasını özelleştirmek ve daha fazlasını yapmak için gizli dizileri veya sertifikaları ve kapsamları ekleyebilirsiniz.

Bir uygulamayı portala kaydettiğinizde, ana kiracınızda bir uygulama nesnesi ve hizmet sorumlusu nesnesi otomatik olarak oluşturulur. Microsoft Graph API 'Lerini kullanarak bir uygulamayı kaydeder/oluşturursanız, hizmet sorumlusu nesnesini oluşturmak ayrı bir adımdır. Belirteç istemek için bir hizmet sorumlusu nesnesi gerekir.

Uygulamanızın [güvenlik](../active-directory/develop/identity-platform-integration-checklist.md#security) denetim listesini gözden geçirdiğinizden emin olun. En iyi uygulama olarak, parola kimlik bilgilerini (istemci gizli dizileri) değil [sertifika kimlik bilgilerini](../active-directory/develop/active-directory-certificate-credentials.md)kullanmanız gerekir.

Daha fazla bilgi için bkz. [Azure Active Directory Içindeki uygulama ve hizmet sorumlusu nesneleri](../active-directory/develop/app-objects-and-service-principals.md) .

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>1. Adım: yönetilen kimliğinizi veya uygulama kaydınızı oluşturun

Yönetilen bir kimlik veya uygulama kaydı kullanıp kullanmayacağınızı tanımladıktan sonra, bir sonraki adımınız bir tane sağlamak olacaktır.

### <a name="managed-identity"></a>Yönetilen kimlik

Yönetilen bir kimlik oluşturmak için kullanacağınız adımlar, kodunuzun bulunduğu yere ve sistem tarafından atanan veya Kullanıcı tarafından atanan bir kimlik oluşturmadığınıza bağlı olarak değişir. Farkı anlamak için [yönetilen kimlik türlerini](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) okuyun. Kimlik türünü seçtikten sonra, Azure AD tarafından yönetilen kimlikler [belgelerindeki](../active-directory/managed-identities-azure-resources/index.yml)doğru öğreticiyi bulun ve takip edin. İçin yönetilen kimliklerin nasıl yapılandırılacağı hakkında yönergeler bulacaksınız:

- [Azure VM’leri](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service ve Azure Işlevleri](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- ve daha fazlası...

### <a name="application-registration"></a>Uygulama kaydı

[Uygulamayı kaydetme](../active-directory/develop/quickstart-register-app.md#register-an-application)bölümünde listelenen adımları izleyin.

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>2. Adım: erişim verme

Azure Time Series Insights ortamınız bir istek aldığında, ilk olarak arayanın taşıyıcı belirteci onaylanır. Doğrulama başarılı olursa çağıranın kimliği doğrulanır ve çağıranın istenen eylemi gerçekleştirme yetkisine sahip olduğundan emin olmak için başka bir denetim yapılır. Herhangi bir kullanıcı veya hizmet sorumlusunu yetkilendirmek için, önce bunları okuyucu veya katkıda bulunan rolü atayarak ortama erişim izni vermeniz gerekir.

- [Azure Portal](https://portal.azure.com/) Kullanıcı arabirimi aracılığıyla erişim vermek için, [bir ortamda veri erişimi verme](concepts-access-policies.md) makalesinde listelenen yönergeleri izleyin. Kullanıcıyı seçerken, yönetilen kimliği veya uygulama kaydını adına veya KIMLIğINE göre arayabilirsiniz.

- Azure CLı kullanarak erişim vermek için aşağıdaki komutu çalıştırın. Erişimi yönetmek için kullanılabilecek komutların tam listesi için [buradaki](/cli/azure/tsi/access-policy) belgeleri gözden geçirin.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Azure CLı için timeseriesınsights uzantısı sürüm 2.11.0 veya üstünü gerektirir. Bu uzantı, az TSİ Access-Policy komutunu ilk kez çalıştırdığınızda otomatik olarak yüklenir. Uzantılar hakkında [daha fazla bilgi edinin](/cli/azure/azure-cli-extensions-overview) .

## <a name="step-3-requesting-tokens"></a>3. Adım: belirteç ISTEME

Yönetilen kimliğiniz veya uygulama kaydınız sağlandıktan ve bir rol atandıktan sonra, OAuth 2,0 taşıyıcı belirteçleri istemek için kullanmaya başlamaya hazırsınız demektir. Belirteç elde etmek için kullandığınız yöntem, kodunuzun nerede barındırıldığını ve seçtiğiniz dilde bağlı olarak farklılık gösterir. Kaynağı belirtirken (belirtecin "hedef kitle" olarak da bilinir), URL 'sini veya GUID 'sini Azure Time Series Insights belirleyebilirsiniz:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> URL 'YI kaynak KIMLIĞI olarak kullanırsanız, belirtecin tam olarak olarak verilmesi gerekir `https://api.timeseries.azure.com/` . Sondaki eğik çizgi gereklidir.

> * [Postman](https://www.getpostman.com/) kullanıyorsanız, **AuthUrl** 'niz şu şekilde olur:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` geçerli ancak `https://api.timeseries.azure.com` değil.

### <a name="managed-identities"></a>Yönetilen kimlikler

Azure App Service veya Işlevlerden erişirken, [Azure kaynakları için belirteçlerin alınması](../app-service/overview-managed-identity.md)bölümündeki yönergeleri izleyin.

.NET uygulamaları ve işlevleri için, yönetilen bir kimlikle çalışmanın en kolay yolu, .NET için [Azure Identity Client Library](/dotnet/api/overview/azure/identity-readme) kullanmaktır. Bu istemci kitaplığı, basitliği ve güvenlik avantajları nedeniyle popüler. Geliştiriciler bir kez kod yazabilir ve istemci kitaplığı 'nın, geliştirici hesabı kullanan bir geliştirici iş istasyonunda veya yönetilen hizmet kimliği kullanılarak Azure 'da dağıtılan bir uygulama ortamına göre kimlik doğrulaması yapıp yapmadığını belirlemesine izin verebilir. Öncül AppAuthentication kitaplığındaki geçiş kılavuzu için [Appauthentication ' i Azure 'a okuyun. kimlik geçiş kılavuzu](/dotnet/api/overview/azure/app-auth-migration).

C# kullanarak Azure Time Series Insights için bir belirteç isteyin ve .NET için Azure Identity istemci kitaplığı:

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>Uygulama kaydı

* Geliştiriciler, uygulama kayıtlarına yönelik belirteçleri almak için [Microsoft kimlik doğrulama kitaplığı](../active-directory/develop/msal-overview.md) 'nı (msal) kullanabilir.

MSAL aşağıdakiler dahil olmak üzere birçok uygulama senaryosunda kullanılabilir ancak bunlarla sınırlı değildir:

* [Tek sayfalı uygulamalar (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Web uygulaması oturumu açma ve Kullanıcı adına Web API 'SI çağırma](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [Web API 'SI oturum açmış kullanıcı adına başka bir aşağı akış Web API 'SI çağırma](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Oturum açmış kullanıcı adına bir Web API 'SI çağıran masaüstü uygulaması](../active-directory/develop/scenario-desktop-overview.md)
* [Etkileşimli olarak oturum açan kullanıcı adına bir Web API 'si çağıran mobil uygulama](../active-directory/develop/scenario-mobile-overview.md).
* [Web API 'sini kendi adına çağıran masaüstü/hizmet Daemon uygulaması](../active-directory/develop/scenario-daemon-overview.md)

Bir belirtecin uygulama kaydı olarak nasıl alınacağını ve bir Gen2 ortamından sorgu verilerini nasıl edinediğini gösteren örnek C# kodu için [GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs) 'da örnek uygulamayı görüntüleme

> [!IMPORTANT]
> [Azure Active Directory kimlik doğrulaması kitaplığı (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) KULLANıYORSANıZ, [msal 'e geçiş yapma](../active-directory/develop/msal-net-migration.md)hakkında bilgi edinin.

## <a name="common-headers-and-parameters"></a>Ortak üstbilgiler ve parametreler

Bu bölümde, Azure Time Series Insights gen1 ve Gen2 API 'Lerinde sorgu yapmak için kullanılan genel HTTP istek üstbilgileri ve parametreleri açıklanmaktadır. API 'ye özgü gereksinimler [Azure Time Series Insights REST API başvuru belgelerinde](/rest/api/time-series-insights/)daha ayrıntılı bir şekilde ele alınmıştır.

> [!TIP]
> REST API 'Leri kullanma, HTTP istekleri yapma ve HTTP yanıtlarını işleme hakkında daha fazla bilgi edinmek için [Azure REST API başvurusunu](/rest/api/azure/) okuyun.

### <a name="http-headers"></a>HTTP üstbilgileri

Gerekli istek üstbilgileri aşağıda açıklanmıştır.

| Gerekli istek üst bilgisi | Description |
| --- | --- |
| Yetkilendirme | Azure Time Series Insights kimlik doğrulaması yapmak için, [Yetkilendirme üst bilgisinde](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate)geçerli bir OAuth 2,0 taşıyıcı belirtecinin geçirilmesi gerekir. |

> [!TIP]
> [JavaScript istemci SDK 'sını](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) grafik ve grafiklerle birlikte kullanarak Azure Time Series Insights API 'lerinde program aracılığıyla nasıl kimlik doğrulaması yapılacağını öğrenmek için barındırılan Azure TIME SERIES INSIGHTS [istemci SDK örnek görselleştirmesini](https://tsiclientsample.azurewebsites.net/) okuyun.

İsteğe bağlı istek üstbilgileri aşağıda açıklanmıştır.

| İsteğe bağlı istek üst bilgisi | Description |
| --- | --- |
| İçerik türü | yalnızca `application/json` desteklenir. |
| x-MS-Client-Request-ID | İstemci istek KIMLIĞI. Hizmet bu değeri kaydeder. Hizmetin işlemleri hizmetler arasında izlemesini sağlar. |
| x-MS-Client-Session-ID | İstemci oturum KIMLIĞI. Hizmet bu değeri kaydeder. Hizmetin, hizmetler arasında bir grup işlem izlemesini sağlar. |
| x-MS-Client-Application-Name | Bu isteği oluşturan uygulamanın adı. Hizmet bu değeri kaydeder. |

İsteğe bağlı ancak önerilen yanıt üstbilgileri aşağıda açıklanmıştır.

| Yanıt üst bilgisi | Description |
| --- | --- |
| İçerik türü | Yalnızca `application/json` desteklenir. |
| x-MS-istek kimliği | Sunucu tarafından oluşturulan istek KIMLIĞI. , Bir isteği araştırmak üzere Microsoft 'a başvurmak için kullanılabilir. |
| x-MS-Property-Found-Behavior | GA API isteğe bağlı yanıt üst bilgisi. Olası değerler `ThrowError` (varsayılan) veya `UseNull` . |

### <a name="http-parameters"></a>HTTP parametreleri

> [!TIP]
> [Başvuru belgelerindeki](/rest/api/time-series-insights/)gerekli ve isteğe bağlı sorgu bilgileri hakkında daha fazla bilgi edinin.

Gerekli URL sorgu dizesi parametreleri API sürümüne bağımlıdır.

| Yayınla | API sürümü değerleri |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

İsteğe bağlı URL sorgu dizesi parametreleri, HTTP istek yürütme süreleri için bir zaman aşımı ayarlamayı içerir.

| İsteğe bağlı sorgu parametresi | Açıklama | Sürüm |
| --- |  --- | --- |
| `timeout=<timeout>` | HTTP istek yürütmesi için sunucu tarafı zaman aşımı. Yalnızca [ortam olaylarını al](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) ve [ortam toplamlarını al](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) API 'leri için geçerlidir. Zaman aşımı değeri ISO 8601 Duration biçiminde olmalıdır, örneğin, `"PT20S"` aralığında olmalıdır `1-30 s` . Varsayılan değer `30 s` olarak belirlenmiştir. | Gen1 |
| `storeType=<storeType>` | Isınma mağazasının etkinleştirildiği Gen2 ortamları için, veya üzerinde sorgu çalıştırılabilir `WarmStore` `ColdStore` . Sorgudaki bu parametre, sorgunun hangi depoda yürütülmesi gerektiğini tanımlar. Tanımlı değilse, sorgu soğuk depoda yürütülür. Isınma mağazasını sorgulamak için, **Stoyeniden yazma** 'nın olarak ayarlanması gerekir `WarmStore` . Tanımlı değilse, sorgu soğuk depoya karşı yürütülür. | Gen2 |

## <a name="next-steps"></a>Sonraki adımlar

* Gen1 Azure Time Series Insights API 'sini çağıran örnek kod için [C# kullanarak sorgu Gen1 verilerini](./time-series-insights-query-data-csharp.md)okuyun.

* Gen2 Azure Time Series Insights API kodu örneklerini çağıran örnek kod için [C# kullanarak Query Gen2 Data](./time-series-insights-update-query-data-csharp.md)makalesini okuyun.

* API başvuru bilgileri için [sorgu API 'si başvuru](/rest/api/time-series-insights/reference-query-apis) belgelerini okuyun.
