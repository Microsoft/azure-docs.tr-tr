---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803015"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Azure Resource Manager belirteci edinme
Azure Active Directory, Azure Resource Manager kullanarak kaynaklarda gerçekleştirdiğiniz tüm görevlerin kimlik doğrulamasından geçmesini sağlamalıdır. Burada gösterilen örnek, parola kimlik doğrulamasını kullanır, diğer yaklaşımlar için bkz. [kimlik doğrulama Azure Resource Manager istekleri][lnk-authenticate-arm].

1. Uygulama kimliği ve parola kullanarak Azure AD 'den bir belirteç almak için, Program.cs içindeki **Main** yöntemine aşağıdaki kodu ekleyin.
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Aşağıdaki kodu **Main** yönteminin sonuna ekleyerek belirteci kullanan bir **ResourceManagementClient** nesnesi oluşturun:
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Kullanmakta olduğunuz kaynak grubu için bir başvuru oluşturun veya bir başvuru alın:
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx