---
title: Python kullanarak Azure Veri Gezgini için bir olay hub 'ı veri bağlantısı oluşturma
description: Bu makalede, Python kullanarak Azure Veri Gezgini için bir olay hub 'ı veri bağlantısı oluşturmayı öğreneceksiniz.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: b69cdb1ee04e3824bf5fd20a7db2401161fdf6e7
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031675"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>Python kullanarak Azure Veri Gezgini için bir olay hub 'ı veri bağlantısı oluşturma

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)

Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve üst düzeyde ölçeklenebilir veri keşfetme hizmetidir. Azure Veri Gezgini, Event Hubs, IoT Hub 'larından ve BLOB kapsayıcılarına yazılan bloblardan alma (veri yükleme) sağlar. Bu makalede, Python kullanarak Azure Veri Gezgini için bir olay hub 'ı veri bağlantısı oluşturacaksınız.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

* [Küme ve veritabanı](create-cluster-database-csharp.md) oluşturma

* [Tablo ve sütun eşlemesi](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) oluştur

* [Veritabanı ve tablo ilkelerini](database-table-policies-csharp.md) ayarlama (isteğe bağlı)

* Alma [için verilerle bir olay hub 'ı](ingest-data-event-hub.md#create-an-event-hub)oluşturun. 

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Olay Hub 'ı veri bağlantısı ekleme

Aşağıdaki örnek, bir olay hub 'ı veri bağlantısının programlı bir şekilde nasıl ekleneceğini gösterir. Azure portal kullanarak bir olay hub 'ı veri bağlantısı eklemek için bkz. [Olay Hub 'ına bağlanma](ingest-data-event-hub.md#connect-to-the-event-hub) .

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
data_connection_name = "myeventhubconnect";
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default";
location = "Central US";
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents";
mapping_rule_name = "StormEvents_CSV_Mapping";
data_format = "csv";
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Ayar** | **Önerilen değer** | **Alan açıklaması**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Kiracı KIMLIĞINIZ. Dizin KIMLIĞI olarak da bilinir.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Kaynak oluşturma için kullandığınız abonelik KIMLIĞI.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Kiracınızdaki kaynaklara erişebilen uygulamanın istemci KIMLIĞI.|
| client_secret | *xxxxxxxxxxxxxx* | Kiracınızdaki kaynaklara erişebilen uygulamanın istemci gizli anahtarı. |
| resource_group_name | *testrg* | Kümenizi içeren kaynak grubunun adı.|
| cluster_name | *mykustocluster* | Kümenizin adı.|
| veritabanı | *mykustodatabase* | Kümenizdeki hedef veritabanının adı.|
| data_connection_name | *myeventhubconnect* | Veri bağlantınızın istenen adı.|
| 'in | *StormEvents* | Hedef veritabanındaki hedef tablonun adı.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Hedef tabloyla ilişkili sütun eşlemenin adı.|
| data_format | *CSV* | İletinin veri biçimi.|
| event_hub_resource_id | *Kaynak KIMLIĞI* | Alma için verileri tutan Olay Hub 'ınızın kaynak KIMLIĞI. |
| consumer_group | *$Default* | Olay Hub 'ınızın Tüketici grubu.|
| location | *Orta ABD* | Veri bağlantısı kaynağının konumu.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]