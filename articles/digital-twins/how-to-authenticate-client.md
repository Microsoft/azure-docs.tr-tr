---
title: Uygulama kimlik doğrulaması kodu yazma
titleSuffix: Azure Digital Twins
description: Bkz. bir istemci uygulamasında kimlik doğrulama kodu yazma
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 958b0de97b79b447f2570dd9c57c87f380bcd551
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589405"
---
# <a name="write-client-app-authentication-code"></a>İstemci uygulaması kimlik doğrulama kodunu yaz

[Bir Azure dijital TWINS örneği ve kimlik doğrulaması](how-to-set-up-instance-portal.md)ayarladıktan sonra, örnekle etkileşim kurmak için kullanacağınız bir istemci uygulaması oluşturabilirsiniz. Bir başlatıcı istemci projesi ayarladıktan sonra, Azure dijital TWINS örneğinde **kimlik doğrulaması yapmak için bu istemci uygulamasında kod yazmanız** gerekir.

Azure dijital TWINS, [OAUTH 2,0 tabanlı Azure AD güvenlik belirteçlerini](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)kullanarak kimlik doğrulaması gerçekleştirir. SDK 'nizin kimliğini doğrulamak için, Azure dijital TWINS için doğru izinlere sahip bir taşıyıcı belirteci almanız ve API çağrılarınızla birlikte geçireceğiz. 

Bu makalede, istemci kitaplığı kullanılarak kimlik bilgilerinin nasıl alınacağı açıklanır `Azure.Identity` . Bu makalede, [.net (C#) SDK 'sı](/dotnet/api/overview/azure/digitaltwins/client)için yazdıklarınız gibi C# dilinde kod örnekleri gösterilirken, `Azure.Identity` kullandığınız SDK 'Yı (Azure dijital TWINS Için kullanılabilen SDK 'lar hakkında daha fazla bilgi Için bkz. [*nasıl yapılır: Azure Digital TWINS API 'Leri ve SDK 'larını kullanma*](how-to-use-apis-sdks.md)).

## <a name="prerequisites"></a>Önkoşullar

İlk olarak, [*nasıl yapılır: örnek ve kimlik doğrulaması ayarlama*](how-to-set-up-instance-portal.md)bölümünde kurulum adımlarını doldurun. Bu, bir Azure dijital TWINS örneğine sahip olmanızı ve kullanıcılarınızın erişim izinlerine sahip olmasını sağlayacaktır. Bu kurulumdan sonra, istemci uygulama kodu yazmaya hazırsınızdır.

Devam etmek için, kodunuzu yazdığınız bir istemci uygulaması projesine ihtiyacınız olacaktır. Önceden ayarlanmış bir istemci uygulama projeniz yoksa, bu öğreticide kullanmak üzere seçtiğiniz dilde temel bir proje oluşturun.

## <a name="common-authentication-methods-with-azureidentity"></a>Azure. Identity ile ortak kimlik doğrulama yöntemleri

`Azure.Identity` , bir taşıyıcı belirteç almak ve SDK 'inizle kimlik doğrulamak için kullanabileceğiniz çeşitli kimlik bilgileri alma yöntemleri sağlayan bir istemci kitaplığıdır. Bu makale C# dilinde örnekler sağlasa da `Azure.Identity` dahil olmak üzere çeşitli diller için görüntüleyebilirsiniz...

* [.NET (C#)](/dotnet/api/azure.identity)
* [Java](/java/api/overview/azure/identity-readme)
* [JavaScript](/javascript/api/overview/azure/identity-readme)
* [Python](/python/api/overview/azure/identity-readme)

Üç ortak kimlik bilgisi-içindeki yöntemleri alma `Azure.Identity` :

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) `TokenCredential` , Azure 'a dağıtılacak uygulamalar için varsayılan bir kimlik doğrulama akışı sağlar ve **yerel geliştirme için önerilen seçenektir**. Ayrıca, bu makalede önerilen diğer iki yöntemi denemek için de etkinleştirilebilir; `ManagedIdentityCredential` `InteractiveBrowserCredential` bir yapılandırma değişkeniyle birlikte kaydırılır ve buna erişebilir.
* [Managedıdentitycredential](/dotnet/api/azure.identity.managedidentitycredential) , [YÖNETILEN kimlikler (MSI)](../active-directory/managed-identities-azure-resources/overview.md)gerektiren durumlarda harika çalışır ve Azure Işlevleri ile çalışmaya ve Azure hizmetlerine dağıtmaya yönelik iyi bir adaydır.
* [Interactivebrowsercredential](/dotnet/api/azure.identity.interactivebrowsercredential) etkileşimli uygulamalara yöneliktir ve kimliği DOĞRULANMıŞ bir SDK istemcisi oluşturmak için kullanılabilir

Aşağıdaki örnek, .NET (C#) SDK 'Sı ile bunların her birinin nasıl kullanılacağını göstermektedir.

## <a name="authentication-examples-net-c-sdk"></a>Kimlik doğrulama örnekleri: .NET (C#) SDK

Bu bölümde, kimlik doğrulama kodunu yazmak için sunulan .NET SDK 'sını kullanma Için C# dilinde bir örnek gösterilmektedir.

İlk olarak, SDK paketini `Azure.DigitalTwins.Core` ve `Azure.Identity` paketini projenize ekleyin. Seçtiğiniz araçlara bağlı olarak, Visual Studio Paket Yöneticisi 'ni veya komut satırı aracını kullanarak paketleri dahil edebilirsiniz `dotnet` . 

Ayrıca, aşağıdaki using deyimlerini proje kodunuza eklemeniz gerekir:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

Daha sonra, içindeki yöntemlerden birini kullanarak kimlik bilgilerini almak için kod ekleyin `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential yöntemi

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) `TokenCredential` , Azure 'a dağıtılacak uygulamalar için varsayılan bir kimlik doğrulama akışı sağlar ve **yerel geliştirme için önerilen seçenektir**.

Varsayılan Azure kimlik bilgilerini kullanmak için Azure Digital TWINS örneğinin URL 'SI ([bulunacak yönergeler](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) gerekir.

Projenize bir eklemek için bir kod örneği aşağıda verilmiştir `DefaultAzureCredential` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>Yerel Azure kimlik bilgilerini ayarlama

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>Managedıdentitycredential yöntemi

[Managedıdentitycredential](/dotnet/api/azure.identity.managedidentitycredential) yöntemi, [YÖNETILEN kimliklere (MSI)](../active-directory/managed-identities-azure-resources/overview.md)ihtiyacınız olan durumlarda (örneğin, Azure işlevleri ile çalışırken) harika bir şekilde çalışır.

Bu, `ManagedIdentityCredential` `DefaultAzureCredential` `InteractiveBrowserCredential` projenin farklı bir bölümünün kimliğini doğrulamak için veya ile aynı projede kullanabileceğiniz anlamına gelir.

Varsayılan Azure kimlik bilgilerini kullanmak için Azure Digital TWINS örneğinin URL 'SI ([bulunacak yönergeler](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) gerekir.

Bir Azure işlevinde, aşağıdaki gibi yönetilen kimlik bilgilerini kullanabilirsiniz:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>Interactivebrowsercredential yöntemi

[Interactivebrowsercredential](/dotnet/api/azure.identity.interactivebrowsercredential) yöntemi etkileşimli uygulamalara yöneliktir ve kimlik doğrulaması için bir Web tarayıcısı getirir. Bunu, `DefaultAzureCredential` etkileşimli kimlik doğrulaması gerektiren durumlarda kullanabilirsiniz.

Etkileşimli tarayıcı kimlik bilgilerini kullanmak için Azure dijital TWINS API 'Leri için izinlere sahip bir **uygulama kaydına** ihtiyacınız olacaktır. Bu uygulama kaydını ayarlama adımları için bkz. [*nasıl yapılır: uygulama kaydı oluşturma*](how-to-create-app-registration.md). Uygulama kaydı kurulduktan sonra...
* Uygulama kaydının *uygulama (istemci) kimliği* ([bulunacak yönergeler](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* Uygulama kaydının *Dizin (kiracı) kimliği* ([bulunacak yönergeler](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* Azure dijital TWINS örneğinin URL 'SI ([bulunacak yönergeler](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Kullanılarak kimliği doğrulanmış SDK istemcisi oluşturma kodu örneği aşağıda verilmiştir `InteractiveBrowserCredential` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> İstemci KIMLIĞI, kiracı KIMLIĞI ve örnek URL 'sini yukarıda gösterildiği gibi doğrudan koda yerleştirebileceğiniz sürece, kodunuzun bu değerleri bir yapılandırma dosyası veya ortam değişkeniyle alması iyi bir fikirdir.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Azure Işlevleri kimlik doğrulaması ile ilgili diğer notlar

Bkz. nasıl yapılır: işlev bağlamında önemli yapılandırma Seçimlerinizden bazılarını açıklayan daha tam bir örnek için [*verileri işlemek üzere bir Azure Işlevi ayarlama*](how-to-create-azure-function.md) .

Ayrıca, bir işlevde kimlik doğrulamasını kullanmak için şunları unutmayın:
* [Yönetilen kimliği etkinleştirme](../app-service/overview-managed-identity.md?tabs=dotnet)
* [Ortam değişkenlerini](/sandbox/functions-recipes/environment-variables?tabs=csharp) uygun şekilde kullanın
* İşlevler uygulamasına, dijital TWINS API 'Lerine erişmesini sağlayan izinler atayın. Azure Işlevleri işlemleri hakkında daha fazla bilgi için bkz. [*nasıl yapılır: verileri işlemek için bir Azure Işlevi ayarlama*](how-to-create-azure-function.md).

## <a name="authenticate-across-tenants"></a>Kiracılar arasında kimlik doğrulaması

Azure dijital TWINS yalnızca bir  [Azure Active Directory (Azure AD) kiracısı](../active-directory/develop/quickstart-create-new-tenant.md)destekleyen bir hizmettir: Azure dijital TWINS örneğinin bulunduğu abonelikteki ana kiracı.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

Örnekten farklı bir kiracıya ait bir hizmet sorumlusu veya Kullanıcı hesabı kullanarak Azure dijital TWINS örneğinizi erişmeniz gerekiyorsa, başka bir kiracıya ait her bir federal kimliğin Azure dijital TWINS örneğinin "giriş" kiracısından bir **belirteç** istemesini sağlayabilirsiniz. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

Ayrıca, kodınızdaki kimlik bilgisi seçeneklerinde giriş kiracısını de belirtebilirsiniz. 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>Diğer kimlik bilgisi yöntemleri

Yukarıdaki vurgulanan kimlik doğrulama senaryoları uygulamanızın ihtiyaçlarını kapsamamışsa, [**Microsoft Identity platformunda**](../active-directory/develop/v2-overview.md#getting-started)sunulan diğer kimlik doğrulama türlerini keşfedebilirsiniz. Bu platformun belgeleri, uygulama türüne göre düzenlenmiş ek kimlik doğrulama senaryolarını içerir.

## <a name="next-steps"></a>Sonraki adımlar

Azure dijital TWINS 'de güvenliğin nasıl çalıştığı hakkında daha fazla bilgi edinin:
* [*Kavramlar: Azure dijital TWINS çözümleri için güvenlik*](concepts-security.md)

Ya da artık kimlik doğrulama ayarlandığına göre, örneğiniz içinde modeller oluşturmak ve yönetmek için ' yi geçin:
* [*Nasıl yapılır: DTDL modellerini yönetme*](how-to-manage-model.md)