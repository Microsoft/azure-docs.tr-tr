---
title: Python aracılığıyla Azure Veri Gezgini ile uçtan uca blob alma
description: Bu makalede, Python kullanan bir uçtan uca örnekle Azure Veri Gezgini blob 'ları nasıl alabileceğinizi öğreneceksiniz.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 1c78336880d685090ae21c725becc90d689c1817
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581794"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-through-python"></a>Python aracılığıyla Azure Veri Gezgini ile uçtan uca blob alma

> [!div class="op_single_selector"]
> * [C#](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve ölçeklenebilir bir veri araştırma hizmetidir. Bu makalede, Azure Blob depolama 'dan Azure Veri Gezgini veri alma hakkında uçtan uca bir örnek sunulmaktadır. 

Programlı olarak bir kaynak grubu, depolama hesabı ve kapsayıcı, Olay Hub 'ı ve Azure Veri Gezgini kümesi ve veritabanı oluşturma hakkında bilgi edineceksiniz. Ayrıca, yeni depolama hesabından verileri almak için Azure Veri Gezgini aracılığıyla nasıl yapılandırılacağınızı öğreneceksiniz.

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

## <a name="install-the-python-package"></a>Python paketi yükleme

Azure Veri Gezgini Python paketini yüklemek için (kusto), yolunda Python içeren bir komut istemi açın. Şu komutları çalıştırın:

```
pip install azure-common
pip install azure-mgmt-resource
pip install azure-mgmt-kusto
pip install azure-mgmt-eventgrid
pip install azure-storage-blob
```
[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](../../includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>Kod örneği 

Aşağıdaki kod örneği, Azure Veri Gezgini veri alımı ile sonuçlanan adım adım bir işlem sağlar. 

İlk olarak bir kaynak grubu oluşturun. Ayrıca, depolama hesabı ve kapsayıcı, Olay Hub 'ı ve Azure Veri Gezgini kümesi ve veritabanı gibi Azure kaynakları da oluşturursunuz. Daha sonra, Azure Veri Gezgini veritabanında tablo ve sütun eşleme ile birlikte Azure Event Grid bir abonelik oluşturursunuz. Son olarak, yeni depolama hesabından verileri almak üzere Azure Veri Gezgini 'yi yapılandırmak için veri bağlantısı oluşturursunuz.

```python
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.resource.resources.models import DeploymentMode
import os.path
import json
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.mgmt.eventgrid import EventGridManagementClient
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
location = "West Europe"
location_small_case = "westeurope"
#Path to the Azure Resource Manager template JSON from the previous section
azure_resource_template_path = "xxxxxxxxx/template.json";

deployment_name = 'e2eexample'
resource_group_name = deployment_name + "resourcegroup"
event_hub_name = deployment_name + "eventhub"
event_hub_namespace_name = event_hub_name + "ns"
storage_account_name = deployment_name + "storage"
storage_container_name = deployment_name + "storagecontainer"
event_grid_subscription_name = deployment_name + "eventgrid"
kusto_cluster_name = deployment_name + "kustocluster"
kusto_database_name = deployment_name + "kustodatabase"
kusto_table_name = "Events"
kusto_column_mapping_name = "Events_CSV_Mapping"
kusto_data_connection_name = deployment_name + "kustoeventgridconnection"


credentials = ServicePrincipalCredentials(
    client_id=client_id,
    secret=client_secret,
    tenant=tenant_id
)
resource_client = ResourceManagementClient(credentials, subscription_id)

print('Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.')
resource_client.resource_groups.create_or_update(
    resource_group_name,
    {
        'location': location_small_case
    }
)

print('Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, and database by using an Azure Resource Manager template.')
#Read the Azure Resource Manager template
with open(azure_resource_template_path, 'r') as template_file_fd:
    template = json.load(template_file_fd)

parameters = {
    'eventHubNamespaceName': event_hub_namespace_name,
    'eventHubName': event_hub_name,
    'storageAccountName': storage_account_name,
    'containerName': storage_container_name,
    'kustoClusterName': kusto_cluster_name,
    'kustoDatabaseName': kusto_database_name
}
parameters = {k: {'value': v} for k, v in parameters.items()}
deployment_properties = {
    'mode': DeploymentMode.incremental,
    'template': template,
    'parameters': parameters
}

#Returns an instance of LROPoller; see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = resource_client.deployments.create_or_update(
    resource_group_name,
    deployment_name,
    deployment_properties
)
poller.wait()

print('Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.')
event_client = EventGridManagementClient(credentials, subscription_id)
storage_resource_id = '/subscriptions/{}/resourceGroups/{}/providers/Microsoft.Storage/storageAccounts/{}'.format(subscription_id, resource_group_name, storage_account_name)
event_hub_resource_id = '/subscriptions/{}/resourceGroups/{}/providers/Microsoft.EventHub/namespaces/{}/eventhubs/{}'.format(subscription_id, resource_group_name, event_hub_namespace_name, event_hub_name)
event_client.event_subscriptions.create_or_update(storage_resource_id, event_grid_subscription_name, {
    'destination': {
        'endpointType': 'EventHub',
        'properties': {
            'resourceId': event_hub_resource_id
        }
    },
    "filter": {
        "subjectBeginsWith": "/blobServices/default/containers/{}".format(storage_container_name),
        "includedEventTypes": ["Microsoft.Storage.BlobCreated"],
        "advancedFilters": []
    }
})


print('Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.')
kusto_uri = "https://{}.{}.kusto.windows.net".format(kusto_cluster_name, location_small_case)
database_name = kusto_database_name
kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)
kusto_client = KustoClient(kusto_connection_string_builder)
create_table_command = ".create table " + kusto_table_name + " (EventTime: datetime, EventId: int, EventSummary: string)"
kusto_client.execute_mgmt(database_name, create_table_command)

create_column_mapping_command = ".create table " + kusto_table_name + " ingestion csv mapping '" + kusto_column_mapping_name \
                                + """' '[{"Name":"EventTime","datatype":"datetime","Ordinal":0},{"Name":"EventId","datatype":"int","Ordinal":1},{"Name":"EventSummary","datatype":"string","Ordinal":2}]'"""
kusto_client.execute_mgmt(database_name, create_column_mapping_command)


print('Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.')
kusto_management_client = KustoManagementClient(credentials, subscription_id)
data_connections = kusto_management_client.data_connections
#Returns an instance of LROPoller; see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=kusto_cluster_name, database_name=kusto_database_name, data_connection_name=kusto_data_connection_name,
                                           parameters=EventGridDataConnection(storage_account_resource_id=storage_resource_id,
                                                                              event_hub_resource_id=event_hub_resource_id, consumer_group="$Default", location=location, table_name=kusto_table_name, mapping_rule_name=kusto_column_mapping_name, data_format="csv"))
poller.wait()
```
|**Ayar** | **Alan açıklaması**|
|---|---|---|
| tenant_id | Kiracı KIMLIĞINIZ. Dizin KIMLIĞI olarak da bilinir.|
| subscription_id | Kaynak oluşturma için kullandığınız abonelik KIMLIĞI.|
| client_id | Kiracınızdaki kaynaklara erişebilen uygulamanın istemci KIMLIĞI.|
| client_secret | Kiracınızdaki kaynaklara erişebilen uygulamanın istemci gizli anahtarı. |

## <a name="test-the-code-example"></a>Kod örneğini test etme

1. Depolama hesabına bir dosya yükleyin.

    ```python
    account_key = "xxxxxxxxxxxxxx"
    block_blob_service = BlockBlobService(account_name=storage_account_name, account_key=account_key)
    blob_name = "test.csv"
    blob_content = """2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm"""
    block_blob_service.create_blob_from_text(container_name=storage_container_name, blob_name=blob_name, text=blob_content)
    ```
    |**Ayar** | **Alan açıklaması**|
    |---|---|---|
    | account_key | Programlı olarak oluşturulan depolama hesabının erişim anahtarı.|

2. Azure Veri Gezgini 'de bir test sorgusu çalıştırın.

    ```python
    kusto_uri = "https://{}.{}.kusto.windows.net".format(kusto_cluster_name, location_small_case)
    kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)
    kusto_client = KustoClient(kusto_connection_string_builder)
    query = "{} | take 10".format(kusto_table_name)
    response = kusto_client.execute_query(kusto_database_name, query)
    print(response.primary_results[0].rows_count)
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunu silmek ve kaynakları temizlemek için aşağıdaki komutu kullanın:

```python
#Returns an instance of LROPoller; see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = resource_client.resource_groups.delete(resource_group_name=resource_group_name)
poller.wait()
```

## <a name="next-steps"></a>Sonraki adımlar

*  Bir küme ve veritabanı oluşturmanın diğer yolları hakkında bilgi edinmek için bkz. [Azure Veri Gezgini kümesi ve veritabanı oluşturma](create-cluster-database-python.md).
* Alma yöntemleri hakkında daha fazla bilgi edinmek için bkz. [Azure Veri Gezgini veri](ingest-data-overview.md)alımı.
* Web uygulaması hakkında bilgi edinmek için bkz. [hızlı başlangıç: Azure Veri Gezgini Web Kullanıcı arabirimindeki verileri sorgulama](web-query-data.md).
* [Sorguları](write-queries.md) kusto sorgu diliyle yazın.