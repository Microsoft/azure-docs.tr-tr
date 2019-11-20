---
title: Yönetim kitaplıklarını Azure Service Bus | Microsoft Docs
description: .NET ' ten Service Bus ad alanlarını ve mesajlaşma varlıklarını yönetin.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2019
ms.author: aschhab
ms.openlocfilehash: faf0a5893b7de276b9a411745500daef4d39da6b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816085"
---
# <a name="service-bus-management-libraries"></a>Service Bus yönetim kitaplıkları

Azure Service Bus yönetim kitaplıkları, Service Bus ad alanlarını ve varlıklarını dinamik olarak sağlayabilir. Bu, karmaşık dağıtımlar ve mesajlaşma senaryolarına olanak tanır ve hangi varlıkların sağlanacağı ile programlı bir şekilde belirlenmesini olanaklı kılar. Bu kitaplıklar, şu anda .NET için kullanılabilir.

## <a name="supported-functionality"></a>Desteklenen işlevi

* Namespace oluşturma, güncelleştirme, silme
* Kuyruk oluşturma, güncelleştirme, silme
* Konu oluşturma, güncelleştirme, silme
* Abonelik oluşturma, güncelleştirme, silme

## <a name="prerequisites"></a>Önkoşullar

Service Bus yönetim kitaplıklarını kullanmaya başlamak için Azure Active Directory (Azure AD) hizmeti ile kimlik doğrulaması yapmanız gerekir. Azure AD, Azure kaynaklarınıza erişim sağlayan bir hizmet sorumlusu olarak kimlik doğrulaması yapmanızı gerektirir. Hizmet sorumlusu oluşturma hakkında daha fazla bilgi için şu makalelerden birine bakın:  

* [Active Directory uygulaması ve kaynaklara erişebilen hizmet sorumlusu oluşturmak için Azure portalını kullanma](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Kaynaklara erişmek üzere hizmet sorumlusu oluşturmak için Azure PowerShell kullanma](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Kaynaklara erişmek üzere hizmet sorumlusu oluşturmak için Azure CLI kullanma](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Bu öğreticiler sunmak bir `AppId` (istemci kimliği) `TenantId`, ve `ClientSecret` (kimlik doğrulama anahtarı), tüm yönetim kitaplıkları ile kimlik doğrulama için kullanılır. Çalıştırmak istediğiniz kaynak grubu için **sahip** izinleriniz olmalıdır.

## <a name="programming-pattern"></a>Programlama düzeni

Herhangi bir Service Bus kaynağını işlemek için kullanılan desenler ortak bir protokol izler:

1. **Microsoft. IdentityModel. clients. ActiveDirectory** kitaplığını kullanarak Azure AD 'den bir belirteç edinin:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. `ServiceBusManagementClient` Nesneyi oluşturun:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. `CreateOrUpdate` Parametreleri belirtilen değerlerinizle ayarlayın:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Çağrıyı yürütün:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Kuyruk oluşturmak için kodu doldurun
Service Bus kuyruğu oluşturmak için kodun tamamı aşağıda verilmiştir: 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> Tüm örnek için [GitHub 'daki .NET Yönetim örneğine](https://github.com/Azure-Samples/service-bus-dotnet-management/)bakın. 

## <a name="next-steps"></a>Sonraki adımlar
[Microsoft. Azure. Management. ServiceBus API başvurusu](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
