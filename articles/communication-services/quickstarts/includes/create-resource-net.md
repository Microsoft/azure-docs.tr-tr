---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 443595a52458d7ff7c168f4c120257cfb60fad2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110907"
---
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/dotnet/).
- İşletim sisteminiz için en son sürüm [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) .
- [.NET Identity SDK 'sının](/dotnet/api/azure.identity)en son sürümünü alın.
- [.NET Yönetim SDK 'sının](../../concepts/sdk-options.md)en son sürümünü alın.

## <a name="installing-the-sdk"></a>SDK 'Yı yükleme

İlk olarak, C# projenize Iletişim Hizmetleri Yönetim SDK 'sını ekleyin:

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>Abonelik Kimliği

Azure aboneliğinizin KIMLIĞINI bilmeniz gerekir. Bu, portaldan elde edilebilir:

1.  Azure hesabınızda oturum açın
2.  Sol kenar çubuğundan abonelikler seçin
3.  Hangi aboneliğin gerekli olduğunu seçin
4.  Genel Bakış ' a tıklayın
5.  Abonelik KIMLIĞINIZI seçin

Bu hızlı başlangıçta, abonelik KIMLIĞINI adlı bir ortam değişkeninde depoladığınız varsayılmaktadır `AZURE_SUBSCRIPTION_ID` .

## <a name="authentication"></a>Kimlik Doğrulaması

Azure Iletişim hizmetleriyle iletişim kurmak için öncelikle Azure 'da kimlik doğrulaması yapmanız gerekir. Genellikle bunu bir hizmet sorumlusu kimliği kullanarak yapabilirsiniz.

### <a name="option-1-managed-identity"></a>Seçenek 1: yönetilen kimlik

Kodunuz Azure 'da bir hizmet olarak çalışıyorsa, kimlik doğrulamasının en kolay yolu Azure 'dan yönetilen bir kimlik elde etmek için kullanılır. [Yönetilen kimlikler](../../../active-directory/managed-identities-azure-resources/overview.md)hakkında daha fazla bilgi edinin.

[Yönetilen kimlikleri destekleyen Azure hizmetleri](../../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

[App Service ve Azure Işlevleri için Yönetilen kimlikler kullanma](../../../app-service/overview-managed-identity.md?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[Sistem tarafından atanan yönetilen kimlik](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[Kullanıcı tarafından atanan yönetilen kimlik](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)

Oluşturduğunuz yönetilen kimliğin ClientID 'si açıkça geçirilmesi gerekir `ManagedIdentityCredential` .

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>Seçenek 2: hizmet sorumlusu

Yönetilen bir kimlik kullanmak yerine, kendinizi yönettiğiniz hizmet sorumlusunu kullanarak Azure 'da kimlik doğrulaması yapmak isteyebilirsiniz. [Azure Active Directory ' de bir hizmet sorumlusu oluşturma ve yönetme](../../../active-directory/develop/howto-create-service-principal-portal.md)ile ilgili belgeler hakkında daha fazla bilgi edinin.

Hizmet sorumlunuzu oluşturduktan sonra, Azure portal hakkında aşağıdaki bilgileri toplamanız gerekir:

- **İstemci Kimliği**
- **İstemci parolası**
- **Kiracı Kimliği**

Bu değerleri `AZURE_CLIENT_ID` sırasıyla,, ve adlı ortam değişkenlerine depolayın `AZURE_CLIENT_SECRET` `AZURE_TENANT_ID` . Daha sonra şu şekilde bir Iletişim Hizmetleri yönetim istemcisi oluşturabilirsiniz:

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>Seçenek 3: Kullanıcı kimliği

Azure 'u bir hizmet kimliği kullanmak yerine etkileşimli bir kullanıcı adına çağırmak isterseniz, bir Azure Communication Services yönetim istemcisi oluşturmak için aşağıdaki kodu kullanabilirsiniz. Bu, kullanıcıdan MSA veya Azure AD kimlik bilgilerini isteyecek bir tarayıcı penceresi açar.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>Iletişim Hizmetleri kaynaklarını yönetme

### <a name="interacting-with-azure-resources"></a>Azure kaynaklarıyla etkileşim kurma

Artık kimliğiniz doğrulandıktan sonra, API çağrıları yapmak için yönetim istemcinizi kullanabilirsiniz.

Aşağıdaki örneklerden her biri için, Iletişim Hizmetleri kaynaklarınızı var olan bir kaynak grubuna atacağız.

Bir kaynak grubu oluşturmanız gerekiyorsa, [Azure Portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md) veya [Azure Resource Manager SDK 'sını](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md)kullanarak bunu yapabilirsiniz.

### <a name="create-and-manage-a-communication-services-resource"></a>Iletişim Hizmetleri kaynağı oluşturma ve yönetme

İletişim Hizmetleri Yönetimi SDK 'Sı istemcisi () örneğimiz, ``Azure.ResourceManager.Communication.CommunicationManagementClient`` Iletişim Hizmetleri kaynakları üzerinde işlemler gerçekleştirmek için kullanılabilir.

#### <a name="create-a-communication-services-resource"></a>İletişim Hizmetleri kaynağı oluşturma

Bir Iletişim Hizmetleri kaynağı oluştururken, kaynak grubu adını ve kaynak adını belirtirsiniz. `Location`Özelliğin her zaman olacağını `global` ve genel önizleme sırasında `DataLocation` değerin olması gerektiğini unutmayın `UnitedStates` .

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>Iletişim Hizmetleri kaynağını güncelleştirme

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>Tüm Iletişim Hizmetleri kaynaklarını listeleyin

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>Iletişim Hizmetleri kaynağını silme

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>Anahtarları ve bağlantı dizelerini yönetme

Her Iletişim Hizmetleri kaynağında, bir dizi erişim anahtarı ve karşılık gelen bağlantı dizesi vardır. Bu anahtarlara Yönetim SDK 'Sı ile erişilebilir ve ardından diğer Iletişim Hizmetleri SDK 'Ları tarafından Azure Iletişim hizmetlerinde kimlik doğrulaması için kullanılır.

#### <a name="get-access-keys-for-a-communication-services-resource"></a>Iletişim Hizmetleri kaynağı için erişim anahtarları alın

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>Iletişim Hizmetleri kaynağı için erişim anahtarını yeniden oluşturma

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```