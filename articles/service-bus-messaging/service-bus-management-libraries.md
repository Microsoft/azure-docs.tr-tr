---
title: Program aracılığıyla Azure Service Bus varlıkları oluşturma | Microsoft Docs
description: Bu makalede, Service Bus ad alanlarını ve varlıklarını dinamik olarak veya programlı bir şekilde sağlamayı açıklar.
ms.devlang: dotnet
ms.topic: article
ms.date: 01/13/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 57192ab2ee1624cb18de832ac91c95290da727df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539882"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Service Bus ad alanlarını ve varlıklarını dinamik olarak sağlama 
Azure Service Bus yönetim kitaplıkları, Service Bus ad alanlarını ve varlıklarını dinamik olarak sağlayabilir. Bu, karmaşık dağıtımlar ve mesajlaşma senaryolarına olanak tanır ve hangi varlıkların sağlanacağı ile programlı bir şekilde belirlenmesini olanaklı kılar. Bu kitaplıklar Şu anda .NET için kullanılabilir.

## <a name="overview"></a>Genel Bakış
Service Bus varlıkları oluşturup yönetmeniz için kullanabileceğiniz üç Yönetim Kitaplığı vardır. Bunlar:

- [Azure. Messaging. ServiceBus. Administration](#azuremessagingservicebusadministration)
- [Microsoft. Azure. ServiceBus. Management](#microsoftazureservicebusmanagement)
- [Microsoft.Azure.Management.ServiceBus](#microsoftazuremanagementservicebus)

Bu paketlerin tümü **Kuyruklar, konular ve abonelikler** üzerinde oluşturma, Get, listeleme, silme, güncelleştirme, silme ve güncelleştirme işlemlerini destekler. Ancak, yalnızca [Microsoft. Azure. Management. ServiceBus](#microsoftazuremanagementservicebus) , **ad alanları** üzerinde oluşturma, güncelleştirme, listeleme, Get ve SILME işlemlerini destekler, SAS anahtarlarını listeleme ve yeniden oluşturma ve daha fazlasını destekler. 

Microsoft. Azure. Management. ServiceBus kitaplığı yalnızca Azure Active Directory (Azure AD) kimlik doğrulamasıyla birlikte çalışarak bir bağlantı dizesi kullanmayı desteklemez. Diğer iki kitaplık (Azure. Messaging. ServiceBus ve Microsoft. Azure. ServiceBus), hizmette kimlik doğrulaması için bir bağlantı dizesi kullanmayı destekler ve daha kolay kullanılır. Bu kitaplıklar arasında, Azure. Messaging. ServiceBus en son ve kullanmanızı öneririz.

Aşağıdaki bölümlerde bu kitaplıklar hakkında daha fazla bilgi sağlanmaktadır. 

## <a name="azuremessagingservicebusadministration"></a>Azure. Messaging. ServiceBus. Administration
Ad alanlarını, kuyrukları, konuları ve abonelikleri yönetmek için [Azure. Messaging. ServiceBus. Administration](/dotnet/api/azure.messaging.servicebus.administration) ad alanındaki [Servicebusadministrationclient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient) sınıfını kullanabilirsiniz. Örnek kod aşağıda verilmiştir. Tüm bir örnek için bkz. [CRUD örneği](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/tests/Samples/Sample07_CrudOperations.cs).

```csharp
using System;
using System.Threading.Tasks;

using Azure.Messaging.ServiceBus.Administration;

namespace adminClientTrack2
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var adminClient = new ServiceBusAdministrationClient(connectionString);
            bool queueExists = await adminClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                var options = new CreateQueueOptions(QueueName)
                {
                    MaxDeliveryCount = 3                    
                };
                await adminClient.CreateQueueAsync(options);
            }


            bool topicExists = await adminClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                var options = new CreateTopicOptions(TopicName)
                {
                    MaxSizeInMegabytes = 1024
                };
                await adminClient.CreateTopicAsync(options);
            }

            bool subscriptionExists = await adminClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                var options = new CreateSubscriptionOptions(TopicName, SubscriptionName)
                {
                    DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0)
                };
                await adminClient.CreateSubscriptionAsync(options);
            }
        }
    }
}

```


## <a name="microsoftazureservicebusmanagement"></a>Microsoft. Azure. ServiceBus. Management 
Ad alanlarını, kuyrukları, konuları ve abonelikleri yönetmek için [Microsoft. Azure. ServiceBus. Management](/dotnet/api/microsoft.azure.servicebus.management) ad alanındaki [managementclient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) sınıfını kullanabilirsiniz. Örnek kod aşağıda verilmiştir: 

> [!NOTE]
> `ServiceBusAdministrationClient`Kitaplığındaki sınıfı, `Azure.Messaging.ServiceBus.Administration` en son SDK olan kitaplıktan kullanmanızı öneririz. Ayrıntılar için [ilk bölüme](#azuremessagingservicebusadministration)bakın. 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.ServiceBus.Management;

namespace SBusManagementClient
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var managementClient = new ManagementClient(connectionString);
            bool queueExists = await managementClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                QueueDescription qd = new QueueDescription(QueueName);
                qd.MaxSizeInMB = 1024;
                qd.MaxDeliveryCount = 3;
                await managementClient.CreateQueueAsync(qd);
            }


            bool topicExists = await managementClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                TopicDescription td = new TopicDescription(TopicName);
                td.MaxSizeInMB = 1024;
                td.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                await managementClient.CreateTopicAsync(td);
            }

            bool subscriptionExists = await managementClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                SubscriptionDescription sd = new SubscriptionDescription(TopicName, SubscriptionName);
                sd.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                sd.MaxDeliveryCount = 3;
                await managementClient.CreateSubscriptionAsync(sd);
            }
        }
    }
}
```


## <a name="microsoftazuremanagementservicebus"></a>Microsoft.Azure.Management.ServiceBus 
Bu kitaplık Azure Resource Manager tabanlı denetim düzlemi SDK 'sının bir parçasıdır. 

### <a name="prerequisites"></a>Önkoşullar

Bu kitaplığı kullanmaya başlamak için Azure Active Directory (Azure AD) hizmeti ile kimlik doğrulaması yapmanız gerekir. Azure AD, Azure kaynaklarınıza erişim sağlayan bir hizmet sorumlusu olarak kimlik doğrulaması yapmanızı gerektirir. Hizmet sorumlusu oluşturma hakkında daha fazla bilgi için şu makalelerden birine bakın:  

* [Kaynaklara erişebilen Active Directory uygulama ve hizmet sorumlusu oluşturmak için Azure portal kullanın](../active-directory/develop/howto-create-service-principal-portal.md)
* [Kaynaklara erişmek üzere hizmet sorumlusu oluşturmak için Azure PowerShell kullanma](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Kaynaklara erişmek üzere hizmet sorumlusu oluşturmak için Azure CLI kullanma](/cli/azure/create-an-azure-service-principal-azure-cli)

Bu öğreticiler size, `AppId` `TenantId` `ClientSecret` Yönetim kitaplıkları tarafından kimlik doğrulaması için kullanılan BIR (istemci kimliği), ve (kimlik doğrulama anahtarı) sağlar. Çalıştırmak istediğiniz kaynak grubu için en az [**Azure Service Bus veri sahibi**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) veya [**katkıda bulunan**](../role-based-access-control/built-in-roles.md#contributor) izinlerinizin olması gerekir.

### <a name="programming-pattern"></a>Programlama stili

Herhangi bir Service Bus kaynağını işlemek için kullanılan desenler ortak bir protokol izler:

1. **Microsoft. IdentityModel. clients. ActiveDirectory** kitaplığını kullanarak Azure AD 'den bir belirteç edinin:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Nesneyi oluşturun `ServiceBusManagementClient` :

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. `CreateOrUpdate`Parametreleri belirtilen değerlerinizle ayarlayın:

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

### <a name="complete-code-to-create-a-queue"></a>Kuyruk oluşturmak için kodu doldurun
Service Bus kuyruğu oluşturmak için örnek kod aşağıda verilmiştir. Tüm örnek için [GitHub 'daki .NET Yönetim örneğine](https://github.com/Azure-Samples/service-bus-dotnet-management/)bakın. 

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

## <a name="fluent-library"></a>Floent kitaplığı
Service Bus varlıklarını yönetmek için akıcı Kitaplığı kullanmanın bir örneği için, [Bu örneğe](https://github.com/Azure/azure-libraries-for-net/tree/master/Samples/ServiceBus)bakın. 

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki başvuru konularına bakın: 

- [Azure. Messaging. ServiceBus. Administration](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)
- [Microsoft. Azure. ServiceBus. Management](/dotnet/api/microsoft.azure.servicebus.management.managementclient)
- [Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus.servicebusmanagementclient)
- [Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent)