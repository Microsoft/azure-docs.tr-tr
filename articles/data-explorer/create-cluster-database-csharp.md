---
title: Kullanarak bir Azure Veri Gezgini kümesi ve veritabanı oluşturmaC#
description: Şunu kullanarak bir Azure Veri Gezgini kümesi ve veritabanı oluşturmayı öğreninC#
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: b5839ef7d9f1e5498beabfcdf0f1605fc1378498
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800420"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Kullanarak bir Azure Veri Gezgini kümesi ve veritabanı oluşturmaC#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM şablonu](create-cluster-database-resource-manager.md)

Azure Veri Gezgini uygulamalar, web siteleri, IoT cihazları ve daha fazlasından akışı yapılan büyük miktarda veri üzerinde gerçek zamanlı analiz yapmaya yönelik hızlı ve tam olarak yönetilen bir veri analizi hizmetidir. Azure Veri Gezgini kullanmak için, önce bir küme oluşturun ve bu kümede bir veya daha fazla veritabanı oluşturursunuz. Daha sonra sorguları bu verilere karşı çalıştırmak için bir veritabanına (yükleme) sahip olursunuz. Bu makalede, kullanarak C#bir küme ve veritabanı oluşturursunuz.

## <a name="prerequisites"></a>Önkoşullar

* Visual Studio 2019 yüklü değilse, **ücretsiz** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)' ı indirip kullanabilirsiniz. Visual Studio kurulumu sırasında **Azure dağıtımını** etkinleştirdiğinizden emin olun.

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

## <a name="install-c-nuget"></a>NuGet C# 'i yükler

* [Azure Veri Gezgini (kusto) NuGet paketini](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)yükler.

* Kimlik doğrulaması için [Microsoft. IdentityModel. clients. ActiveDirectory NuGet paketini](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) yükler.

## <a name="authentication"></a>Kimlik Doğrulaması
Bu makaledeki örnekleri çalıştırmak için, kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu olması gerekir. [Azure AD uygulaması oluşturma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) ' yı denetleyerek ücretsiz BIR Azure AD uygulaması oluşturun ve abonelik kapsamında rol ataması ekleyin. Ayrıca, `Directory (tenant) ID`, `Application ID` ve `Client Secret` ' nin nasıl alınacağını gösterir.

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Veri Gezgini kümesi oluşturma

1. Aşağıdaki kodu kullanarak kümenizi oluşturun:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };

    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Ayar** | **Önerilen değer** | **Alan açıklaması**|
   |---|---|---|
   | clusterName | *mykustocluster* | Kümenizin istenen adı.|
   | skuName | *Standard_D13_v2* | Kümeniz için kullanılacak SKU. |
   | tier | *Standart* | SKU katmanı. |
   | capacity | *sayısından* | Küme örneklerinin sayısı. |
   | resourceGroupName | *testrg* | Kümenin oluşturulacağı kaynak grubu adı. |

    > [!NOTE]
    > **Küme oluşturma** uzun süren bir işlemdir, bu yüzden CreateOrUpdate yerine CreateOrUpdateAsync kullanılması önemle önerilir. 

1. Kümenizin başarıyla oluşturulup oluşturulmayacağını denetlemek için şu komutu çalıştırın:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Sonuç `Succeeded` değeriyle `ProvisioningState` içeriyorsa, küme başarıyla oluşturuldu.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Veri Gezgini kümesinde veritabanı oluşturma

1. Aşağıdaki kodu kullanarak veritabanınızı oluşturun:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Ayar** | **Önerilen değer** | **Alan açıklaması**|
   |---|---|---|
   | clusterName | *mykustocluster* | Veritabanının oluşturulacağı Kümenizin adı.|
   | Dosyasında | *mykustodatabase* | Veritabanınızın adı.|
   | resourceGroupName | *testrg* | Kümenin oluşturulacağı kaynak grubu adı. |
   | softDeletePeriod | *3650:00:00:00* | Verilerin sorgu için kullanılabilir kalacağı zaman miktarı. |
   | hotCachePeriod | *3650:00:00:00* | Verilerin önbellekte tutulacağı zaman miktarı. |

2. Oluşturduğunuz veritabanını görmek için aşağıdaki komutu çalıştırın:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

Artık bir kümeniz ve veritabanınız var.

## <a name="clean-up-resources"></a>Kaynakları temizleme

* Diğer makalelerimizi izlemeyi planlıyorsanız oluşturduğunuz kaynakları saklayın.
* Kaynakları temizlemek için kümeyi silin. Bir kümeyi sildiğinizde, içindeki tüm veritabanlarını da siler. Kümenizi silmek için aşağıdaki komutu kullanın:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Veri Gezgini .NET Standard SDK 'sını kullanarak verileri alma (Önizleme)](net-standard-ingest-data.md)
