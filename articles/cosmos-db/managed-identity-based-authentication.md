---
title: Azure Cosmos DB verilerine erişmek için sistem tarafından atanan yönetilen kimliği kullanma
description: Azure Cosmos DB anahtarlara erişmek için bir Azure Active Directory (Azure AD) sistem tarafından atanan yönetilen kimliğin (yönetilen hizmet kimliği) nasıl yapılandırılacağını öğrenin.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b85e1fc74688f2883531bd3a6e724a2ce326a9db
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600259"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Azure Cosmos DB verilerine erişmek için sistem tarafından atanan Yönetilen kimlikler kullanın
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, [Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)kullanarak Azure Cosmos DB anahtarlarına erişmek için *güçlü, anahtar dönüşü belirsiz* bir çözüm ayarlayacaksınız. Bu makaledeki örnekte Azure Işlevleri kullanılmaktadır, ancak yönetilen kimlikleri destekleyen herhangi bir hizmet kullanabilirsiniz. 

Azure Cosmos DB anahtarlarını kopyalamaya gerek kalmadan Azure Cosmos DB veriye erişebilen bir işlev uygulaması oluşturmayı öğreneceksiniz. İşlev uygulaması her dakikada bir uyandırır ve bir Aquarium mertank 'ın geçerli sıcaklığını kaydeder. Zamanlayıcı tarafından tetiklenen bir işlev uygulaması ayarlamayı öğrenmek için, [Azure 'da bir Zamanlayıcı tarafından tetiklenen bir Işlev oluşturma](../azure-functions/functions-create-scheduled-function.md) makalesine bakın.

Senaryoyu basitleştirmek için, daha eski sıcaklık belgelerini temizlemek üzere bir [yaşam süresi](./time-to-live.md) ayarı zaten yapılandırılmıştır. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Bir işlev uygulamasına sistem tarafından atanan yönetilen kimlik atama

Bu adımda, işlev uygulamanıza sistem tarafından atanmış bir yönetilen kimlik atayacaksınız.

1. [Azure Portal](https://portal.azure.com/) **Azure işlev** bölmesini açın ve işlev uygulamanıza gidin. 

1. **Platform özellikleri**  >  **kimlik** sekmesini açın: 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="İşlev uygulaması için platform özelliklerini ve kimlik seçeneklerini gösteren ekran görüntüsü.":::

1. **Kimlik** sekmesinde, sistem kimliği durumunu **açın ve** **Kaydet**'  i seçin. **Kimlik** bölmesi aşağıdaki gibi görünmelidir:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Sistem kimliği durumunun açık olarak ayarlandığını gösteren ekran görüntüsü.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Azure Cosmos hesabınıza erişim izni verin

Bu adımda, işlev uygulamasının sistem tarafından atanan yönetilen kimliğine bir rol atayacaksınız. Azure Cosmos DB yönetilen kimliğe atayabilmeniz için birden çok yerleşik rol içerir. Bu çözüm için aşağıdaki iki rolü kullanacaksınız:

|Yerleşik rol  |Description  |
|---------|---------|
|[DocumentDB hesabı Katılımcısı](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|, Azure Cosmos DB hesaplarını yönetebilir. Okuma/yazma anahtarlarının alınmasına izin verir. |
|[Cosmos DB hesabı okuyucu rolü](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Azure Cosmos DB hesabı verilerini okuyabilir. Okuma anahtarlarının alınmasına izin verir. |

> [!IMPORTANT]
> Azure Cosmos DB ' de rol tabanlı erişim denetimi için destek yalnızca denetim düzlemi işlemleri için geçerlidir. Veri düzlemi işlemleri, birincil anahtarlar veya kaynak belirteçleri aracılığıyla güvenli hale getirilir. Daha fazla bilgi için bkz. [verilere güvenli erişim](secure-access-to-data.md) makalesi.

> [!TIP] 
> Rolleri atadığınızda, yalnızca gerekli erişimi atayın. Hizmetiniz yalnızca veri okumayı gerektiriyorsa, **Cosmos DB hesabı okuyucusu** rolünü yönetilen kimliğe atayın. En az ayrıcalık erişiminin önemi hakkında daha fazla bilgi için, [ayrıcalıklı hesapların daha düşük pozlaması](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) makalesine bakın.

Bu senaryoda, işlev uygulaması Aquarium 'un sıcaklığını okur ve sonra bu verileri Azure Cosmos DB bir kapsayıcıya geri yazar. İşlev uygulaması verileri yazması gerektiğinden, **DocumentDB hesabı katılımcısı** rolünü atamanız gerekir. 

### <a name="assign-the-role-using-azure-portal"></a>Azure portal kullanarak rol atama

1. Azure portal oturum açın ve Azure Cosmos DB hesabınıza gidin. **Erişim denetimi (IAM)** bölmesini ve ardından **rol atamaları** sekmesini açın:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Erişim denetim bölmesini ve rol atamaları sekmesini gösteren ekran görüntüsü.":::

1. **+ Ekle** > **Rol ataması ekle**’yi seçin.

1. **Rol ataması Ekle** paneli sağ tarafta açılır:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Rol ataması Ekle bölmesini gösteren ekran görüntüsü.":::

   * **Rol**: **DocumentDB hesabı katılımcısı** seçin
   * **Erişim ata**: **sistem tarafından atanan yönetilen kimlik Seç** alt bölümünün altında **işlev uygulaması**' yi seçin.
   * **Seç**: bölmesi, aboneliğinizdeki **yönetilen sistem kimliğine** sahip tüm işlev uygulamalarıyla doldurulur. Bu durumda **FishTankTemperatureService** işlev uygulamasını seçin: 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Örnek ile doldurulmuş rol atama Ekle bölmesini gösteren ekran görüntüsü.":::

1. İşlev uygulamanızı seçtikten sonra **Kaydet**' i seçin.

### <a name="assign-the-role-using-azure-cli"></a>Azure CLı kullanarak rol atama

Azure CLı kullanarak rolü atamak için, Azure Cloud Shell açın ve aşağıdaki komutları çalıştırın:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Azure Cosmos DB anahtarlarına programlı olarak erişin

Artık Azure Cosmos DB izinlerinde **DocumentDB hesabı katılımcısı** rolüyle sistem tarafından atanmış bir yönetilen kimliğe sahip bir işlev uygulamasına sahipsiniz. Aşağıdaki işlev uygulama kodu Azure Cosmos DB anahtarları alır, bir CosmosClient nesnesi oluşturur, Aquarium 'un sıcaklığını alır ve bunu Azure Cosmos DB kaydeder.

Bu örnek Azure Cosmos DB hesap Anahtarlarınıza erişmek için [liste anahtarları API](/rest/api/cosmos-db-resource-provider/2021-03-15/databaseaccounts/listkeys) 'sini kullanır.

> [!IMPORTANT] 
> [Cosmos DB hesap okuyucusu rolünü atamak](#grant-access-to-your-azure-cosmos-account) Istiyorsanız, [salt okuma anahtarları API 'sini](/rest/api/cosmos-db-resource-provider/2021-03-15/databaseaccounts/listreadonlykeys)kullanmanız gerekir. Bu, yalnızca salt okunurdur tuşları doldurur.

Liste anahtarları API 'SI nesneyi döndürür `DatabaseAccountListKeysResult` . Bu tür C# kitaplıklarında tanımlı değildir. Aşağıdaki kod bu sınıfın uygulamasını gösterir:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

Örnek ayrıca, aşağıdaki gibi tanımlanan "TemperatureRecord" adlı basit bir belge kullanır:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Sistem tarafından atanan yönetilen kimlik belirtecini almak için [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) kitaplığını kullanacaksınız. Belirteci almanın ve kitaplık hakkında daha fazla bilgi bulmanın diğer yollarını öğrenmek için `Microsoft.Azure.Service.AppAuthentication` , [hizmetten hizmete kimlik doğrulama](/dotnet/api/overview/azure/service-to-service-authentication) makalesine bakın.


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Artık [işlev uygulamanızı dağıtmaya](../azure-functions/create-first-function-vs-code-csharp.md)hazırsınız.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB ve Azure Active Directory ile sertifika tabanlı kimlik doğrulaması](certificate-based-authentication.md)
* [Azure Key Vault kullanarak Azure Cosmos DB anahtarları güvenli hale getirme](access-secrets-from-keyvault.md)
* [Azure Cosmos DB için güvenlik temeli](security-baseline.md)
