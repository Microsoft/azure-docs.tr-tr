---
title: Azure Active Directory (Azure AD) kimlik doğrulaması
titleSuffix: Azure Cognitive Services
description: Bu makalede, özel bir alt etki alanı ile yeni bir tam ekran okuyucu kaynağı oluşturma ve ardından Azure kiracınızda Azure AD 'yi yapılandırma gösterilmektedir.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: d51c27b90113679c1547f2d030459a03cc22c80c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299798"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>Tam ekran okuyucu hizmeti ile Azure Active Directory (Azure AD) kimlik doğrulamasını kullanma

Aşağıdaki bölümlerde, özel bir alt etki alanı ile yeni bir tam ekran kaynak oluşturmak ve ardından Azure kiracınızda Azure AD 'yi yapılandırmak için Azure Cloud Shell ortamını veya Azure PowerShell kullanacaksınız. Bu ilk yapılandırmayı tamamladıktan sonra, bir erişim belirteci almak için Azure AD 'yi çağırıp, tam ekran okuyucu SDK 'sını kullanırken nasıl yapılacağına benzer bir erişim belirteci elde edersiniz. Takıldıysanız her bir bölümde Azure PowerShell komutlarının her biri için kullanılabilir tüm seçeneklerle bağlantılar sağlanır.

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>Özel alt etki alanı ile bir tam ekran okuyucu kaynağı oluşturma

1. [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)açarak başlayın. Ardından [bir abonelik seçin](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. Ardından, özel bir alt etki alanı ile bir [tam ekran okuyucu kaynağı oluşturun](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) .

   >[!NOTE]
   > LaunchAsync işleviyle okuyucu başlatılırken, alt etki alanı adı, modern Okuyucu SDK 'sında kullanılır.

   -SkuName, F0 (ücretsiz katman) veya S0 (Standart katman, genel önizleme sırasında da ücretsiz) olabilir. S0 katmanının daha yüksek bir çağrı hızı sınırı vardır ve çağrı sayısı üzerinde aylık kota yoktur.

   -Location şunlardan herhangi biri olabilir: `eastus`, `westus`, `australiaeast`, `centralindia`, `japaneast`, `northeurope`, `westeurope`

   -CustomSubdomainName 'in genel olarak benzersiz olması gerekir ve aşağıdakiler gibi özel karakterler içermemelidir: ".", "!", ",".


   ```azurepowershell-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   Başarılı olursa kaynak **uç noktası** , kaynağınız için benzersiz olan alt etki alanı adını göstermelidir.

   Burada, yeni oluşturulan kaynak nesnesini bir **$Resource** değişkenine yakalıyoruz, daha sonra bu kaynağa erişim verilirken kullanılır.


   >[!NOTE]
   > Azure portal bir kaynak oluşturursanız, ' ad ' kaynağı özel alt etki alanı olarak kullanılır. Kaynak genel bakış sayfasına giderek ve burada listelenen uç noktada alt etki alanını (örneğin, `https://[SUBDOMAIN].cognitiveservices.azure.com/`) bularak Portal 'daki alt etki alanı adını kontrol edebilirsiniz. Ayrıca, SDK ile tümleştirme için alt etki alanını almanız gerektiğinde daha sonra da denetleyebilirsiniz.

   Kaynak portalda oluşturulduysa, [var olan bir kaynağı](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0) şimdi de edinebilirsiniz.

   ```azurepowershell-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```

## <a name="assign-a-role-to-a-service-principal"></a>Hizmet sorumlusuna rol atama

Artık kaynakla ilişkili özel bir alt etki alanı olduğuna göre, bir hizmet sorumlusuna bir rol atamanız gerekir.

1. İlk olarak [bir Azure AD uygulaması oluşturalım](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   >[!NOTE]
   > ' İstemci gizli dizisi ' olarak da bilinen parola, kimlik doğrulama belirteçleri alınırken kullanılacaktır.

   ```azurepowershell-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   Burada, yeni oluşturulan Azure AD uygulama nesnesini bir sonraki adımda kullanmak üzere bir **$aadApp** değişkenine yakalıyoruz.

2. Sonra, Azure AD uygulaması için [bir hizmet sorumlusu oluşturmanız](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) gerekir.

   ```azurepowershell-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   Burada, yeni oluşturulan hizmet sorumlusu nesnesini bir sonraki adımda kullanmak üzere bir **$Principal** değişkenine yakalıyoruz.


3. Son adım, "bilişsel [Hizmetler kullanıcısı" rolünün](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) hizmet sorumlusuna atanması (kaynağa kapsamlı olarak). Rol atayarak, bu kaynağa hizmet sorumlusu erişimi vermiş olursunuz. Aboneliğinizdeki birden fazla kaynağa aynı hizmet sorumlusu erişimi verebilirsiniz.

   ```azurepowershell-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > Bu adımın, oluşturduğunuz her bir derinlikli okuyucu kaynağı için gerçekleştirilmesi gerekir. Örneğin, farklı küresel bölgeler için birden çok kaynak oluşturursanız, Azure AD kimlik doğrulamasının tümünde çalışabilmesi için bu bölgesel kaynakların her biri için bu adımı gerçekleştirmeniz gerekir. Ya da daha sonraki bir zamanda yeni bir kaynak oluşturursanız, bu yeni kaynak için bu rol atama adımını da gerçekleştirmeniz gerekecektir.


## <a name="obtain-an-azure-ad-token"></a>Azure AD belirteci edinme

Bu örnekte, parolanız bir Azure AD belirteci almak için hizmet sorumlusunun kimliğini doğrulamak için kullanılır.

1. **Tenantıd**'nizi alın:
   ```azurepowershell-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. Belirteç al:
   ```azurepowershell-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > Modern Okuyucu SDK 'Sı, belirtecin AccessToken özelliğini kullanır, örn. $token. AccessToken. Ayrıntılar için SDK [başvurusu](reference.md) ve kod [örneklerine](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples) bakın.

Alternatif olarak, hizmet sorumlusu bir sertifikayla kimlik doğrulaması yapabilir. Hizmet sorumlusuna ek olarak, başka bir Azure AD uygulaması aracılığıyla temsilci atanmış izinler sunarak da Kullanıcı sorumluları desteklenir. Bu durumda, parolalar veya sertifikalar yerine, belirteçleri alırken kullanıcılardan iki öğeli kimlik doğrulaması istenir.

## <a name="next-steps"></a>Sonraki adımlar

* Node. js kullanarak modern Okuyucu SDK 'Sı ile neler yapabileceğinizi öğrenmek için [Node. js öğreticisini](./tutorial-nodejs.md) görüntüleyin
* Python kullanarak modern Okuyucu SDK 'Sı ile neler yapabileceğinizi öğrenmek için [Python öğreticisini](./tutorial-python.md) görüntüleyin
* Swift [öğreticisini](./tutorial-ios-picture-immersive-reader.md) kullanarak tam ekran okuyucu SDK 'sı ile neler yapabileceğinizi görün.
* [Modern Okuyucu SDK 'sını](https://github.com/microsoft/immersive-reader-sdk) ve [tam ekran okuyucu SDK başvurusunu](./reference.md) keşfet