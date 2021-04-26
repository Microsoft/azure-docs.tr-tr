---
title: 'Hızlı başlangıç: .NET Core ile yeni ilke ataması'
description: Bu hızlı başlangıçta, uyumlu olmayan kaynakları belirlemek üzere bir Azure Ilkesi ataması oluşturmak için .NET Core 'u kullanırsınız.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: b89a0db419b1e7213e9742e4a39d073445d05189
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091795"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>Hızlı başlangıç: .NET Core ile uyumlu olmayan kaynakları belirlemek için bir ilke ataması oluşturma

Azure’da uyumluluğu anlamanın ilk adımı, kaynaklarınızın durumunu belirlemektir. Bu hızlı başlangıçta, yönetilen disk kullanmayan sanal makineleri belirlemek için bir ilke ataması oluşturacaksınız. Bu tamamlandığında, _uyumlu olmayan_ sanal makineleri tanımlayacaksınız.

.NET Core kitaplığı, Azure kaynaklarını yönetmek için kullanılır. Bu kılavuzda, bir ilke ataması oluşturmak için Azure Ilkesi için .NET Core kitaplığı 'nın nasıl kullanılacağı açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.
- _ClientID_ ve _ClientSecret_ dahil olmak üzere bir Azure hizmet sorumlusu. Azure Ilkesiyle kullanılmak üzere bir hizmet sorumlusu yoksa veya yeni bir tane oluşturmak istiyorsanız, bkz. [.NET Yönetim kitaplıkları Için Azure kimlik doğrulaması](/dotnet/azure/sdk/authentication#mgmt-auth).
  Sonraki adımlarda bunu yapacağımız için .NET Core paketlerini yüklemek için adımı atlayın.

## <a name="create-the-azure-policy-project"></a>Azure Ilke projesi oluşturma

Azure Ilkesini yönetmek için .NET Core 'u etkinleştirmek üzere yeni bir konsol uygulaması oluşturun ve gerekli paketleri yükler.

1. En son .NET Core 'un yüklü olduğundan emin olun (en azından **3.1.8**). Henüz yüklenmemişse, [DotNet.Microsoft.com](https://dotnet.microsoft.com/download/dotnet-core)adresinden indirin.

1. "PolicyAssignment" adlı yeni bir .NET Core konsol uygulaması başlatın:

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. Dizinleri yeni proje klasörüne değiştirin ve Azure Ilkesi için gerekli paketleri yükler:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Varsayılan değerini `program.cs` aşağıdaki kodla değiştirin ve güncelleştirilmiş dosyayı kaydedin:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. Konsol uygulaması oluşturun ve yayımlayın `policyAssignment` :

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>İlke ataması oluşturma

Bu hızlı başlangıçta, bir ilke ataması oluşturup yönetilen diskler () tanımını **kullanmayan denetim sanal** makinelerini atarsınız `06a78e20-9358-41c9-923c-fb736d382a4d` . Bu ilke tanımı, ilke tanımında ayarlanan koşullarla uyumlu olmayan kaynakları belirler.

1. `{run-folder}`Önceki komutla tanımladığınız dizinleri değiştirin `dotnet publish` .

1. Terminalde aşağıdaki komutu girin:

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

Yukarıdaki komutlarda aşağıdaki bilgiler kullanılmaktadır:

- `{tenantId}` -Kiracı KIMLIĞINIZLE değiştirin
- `{clientId}` -Hizmet sorumlunun istemci KIMLIĞIYLE değiştirin
- `{clientSecret}` -Hizmet sorumlunun istemci gizli anahtarı ile değiştirin
- `{subscriptionId}` - Abonelik kimliğinizle değiştirin
- **ad** -ilke atama nesnesinin benzersiz adı. Yukarıdaki örnekte _audit-VM-manageddisks_ kullanılır.
- **DisplayName** -ilke ataması için görünen ad. Bu durumda, _yönetilen disk ataması olmadan VM 'Leri denetleme_ kullanıyorsunuz demektir.
- **Policydeıd** : atamayı oluşturmak için kullandığınız ilke tanım yolu. Bu durumda, _yönetilen diskleri kullanmayan ilke tanımı denetim VM_'lerinin kimliğidir.
- **Açıklama** -ilkenin ne yaptığını veya neden bu kapsama atandığını daha derin bir açıklama.
- **kapsam** -bir kapsam, ilke atamasının hangi kaynaklarda veya gruplandırmaya uygulanacağını belirler. Bir yönetim grubundan tek bir kaynağa kadar değişebilir. `{scope}`Aşağıdaki desenlerden biriyle değiştirdiğinizden emin olun:
  - Yönetim grubu: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Abonelik: `/subscriptions/{subscriptionId}`
  - Kaynak grubu: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Kaynak `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Artık ortamınızın uyumluluk durumunu anlamak için uyumlu olmayan kaynakları belirlemek için hazırsınız demektir.

## <a name="identify-non-compliant-resources"></a>Uyumlu olmayan kaynakları belirleme

İlke atamalarınız oluşturuldığına göre, uyumlu olmayan kaynakları belirleyebilirsiniz.

1. "Policyuyum" adlı yeni bir .NET Core konsol uygulaması başlatın:

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. Dizinleri yeni proje klasörüne değiştirin ve Azure Ilkesi için gerekli paketleri yükler:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Varsayılan değerini `program.cs` aşağıdaki kodla değiştirin ve güncelleştirilmiş dosyayı kaydedin:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. Konsol uygulaması oluşturun ve yayımlayın `policyAssignment` :

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. `{run-folder}`Önceki komutla tanımladığınız dizinleri değiştirin `dotnet publish` .

1. Terminalde aşağıdaki komutu girin:

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

Yukarıdaki komutlarda aşağıdaki bilgiler kullanılmaktadır:

- `{tenantId}` -Kiracı KIMLIĞINIZLE değiştirin
- `{clientId}` -Hizmet sorumlunun istemci KIMLIĞIYLE değiştirin
- `{clientSecret}` -Hizmet sorumlunun istemci gizli anahtarı ile değiştirin
- `{subscriptionId}` - Abonelik kimliğinizle değiştirin
- **ad** -ilke atama nesnesinin benzersiz adı. Yukarıdaki örnekte _audit-VM-manageddisks_ kullanılır.

' Deki sonuçlar, `response` Azure Portal görünümündeki bir ilke atamasının **kaynak uyumluluğu** sekmesinde gördüklerinize göre eşleşir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

- Portal üzerinden _yönetilen disk ataması olmadan VM 'Leri denetleme_ ilke atamasını silin. İlke tanımı yerleşik bir derleme olduğundan, kaldırılacak tanım yok.

- .NET Core Konsol uygulamalarını ve yüklü paketleri kaldırmak istiyorsanız, `policyAssignment` ve `policyCompliance` Proje klasörlerini silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure ortamınızda uyumlu olmayan kaynakları belirlemek üzere bir ilke tanımı atadınız.

Yeni kaynakların uyumlu olduğunu doğrulamak üzere ilke tanımları atama hakkında daha fazla bilgi edinmek için öğreticiye geçin:

> [!div class="nextstepaction"]
> [İlke oluşturma ve yönetme](./tutorials/create-and-manage.md)
