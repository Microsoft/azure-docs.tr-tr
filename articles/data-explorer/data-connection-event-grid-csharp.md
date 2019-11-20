---
title: Kullanarak Azure Veri Gezgini için Event Grid veri bağlantısı oluşturmaC#
description: Bu makalede kullanarak C#Azure Veri Gezgini için Event Grid veri bağlantısı oluşturmayı öğreneceksiniz.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 7da11046b36549943b5749058a215af9b1f538e3
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031545"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Kullanarak Azure Veri Gezgini için Event Grid veri bağlantısı oluşturmaC#

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)


Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve üst düzeyde ölçeklenebilir veri keşfetme hizmetidir. Azure Veri Gezgini, Event Hubs, IoT Hub 'larından ve BLOB kapsayıcılarına yazılan bloblardan alma (veri yükleme) sağlar. Bu makalede, kullanarak C#Azure Veri Gezgini için Event Grid bir veri bağlantısı oluşturacaksınız.

## <a name="prerequisites"></a>Önkoşullar

* Visual Studio 2019 yüklü değilse, **ücretsiz** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)' ı indirip kullanabilirsiniz. Visual Studio kurulumu sırasında **Azure dağıtımını** etkinleştirdiğinizden emin olun.

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

* [Küme ve veritabanı](create-cluster-database-csharp.md) oluşturma

* [Tablo ve sütun eşlemesi](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) oluştur

* [Veritabanı ve tablo ilkelerini](database-table-policies-csharp.md) ayarlama (isteğe bağlı)

* [Event Grid abonelikle bir depolama hesabı](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)oluşturun.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Event Grid veri bağlantısı ekleme

Aşağıdaki örnek, bir Event Grid veri bağlantısının programlı bir şekilde nasıl ekleneceğini gösterir. Azure portal kullanarak Event Grid veri bağlantısı eklemek için [Azure Veri Gezgini 'de Event Grid veri bağlantısı oluşturma](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) konusuna bakın.

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
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Ayar** | **Önerilen değer** | **Alan açıklaması**|
|---|---|---|
| Değerine | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Kiracı KIMLIĞINIZ. Dizin KIMLIĞI olarak da bilinir.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Kaynak oluşturma için kullandığınız abonelik KIMLIĞI.|
| ClientID | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Kiracınızdaki kaynaklara erişebilen uygulamanın istemci KIMLIĞI.|
| clientSecret | *xxxxxxxxxxxxxx* | Kiracınızdaki kaynaklara erişebilen uygulamanın istemci gizli anahtarı. |
| resourceGroupName | *testrg* | Kümenizi içeren kaynak grubunun adı.|
| clusterName | *mykustocluster* | Kümenizin adı.|
| Dosyasında | *mykustodatabase* | Kümenizdeki hedef veritabanının adı.|
| Veri connectionName | *myeventhubconnect* | Veri bağlantınızın istenen adı.|
| tableName | *StormEvents* | Hedef veritabanındaki hedef tablonun adı.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Hedef tabloyla ilişkili sütun eşlemenin adı.|
| dataFormat | *CSV* | İletinin veri biçimi.|
| Eventhubresourceıd | *Kaynak KIMLIĞI* | Olay Hub 'ınızın, Event Grid olayları göndermek üzere yapılandırıldığı kaynak KIMLIĞI. |
| Storageaccountresourceıd | *Kaynak KIMLIĞI* | Alma için verileri tutan depolama hesabınızın kaynak KIMLIĞI. |
| consumerGroup | *$Default* | Olay Hub 'ınızın Tüketici grubu.|
| location | *Orta ABD* | Veri bağlantısı kaynağının konumu.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
