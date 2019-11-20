---
title: Python kullanarak Azure Veri Gezgini kümesi ve veritabanı oluşturma
description: Python kullanarak Azure Veri Gezgini kümesi ve veritabanı oluşturmayı öğrenin.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: b3329ccb3edb3077a45e3bbf9ba7b48d7e3a93a2
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996237"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Python kullanarak Azure Veri Gezgini kümesi ve veritabanı oluşturma

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM şablonu](create-cluster-database-resource-manager.md)

Azure Veri Gezgini, uygulamalardan, Web sitelerinden, IoT cihazlarından ve daha pek çok büyük miktarlarda veri akışı için gerçek zamanlı analizler için hızlı ve tam olarak yönetilen bir veri analizi hizmetidir. Azure Veri Gezgini kullanmak için, önce bir küme oluşturun ve bu kümede bir veya daha fazla veritabanı oluşturursunuz. Daha sonra sorguları bu verilere karşı çalıştırmak için bir veritabanına (yükleme) sahip olursunuz. Bu makalede, Python kullanarak bir küme ve veritabanı oluşturursunuz.

## <a name="prerequisites"></a>Prerequisites

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

## <a name="install-python-package"></a>Python paketini yükler

Azure Veri Gezgini Python paketini yüklemek için (kusto), yolunda Python içeren bir komut istemi açın. Şu komutu çalıştırın:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Kimlik doğrulaması
Bu makaledeki örnekleri çalıştırmak için, kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu olması gerekir. [Azure AD uygulaması oluşturma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) ' yı denetleyerek ücretsiz BIR Azure AD uygulaması oluşturun ve abonelik kapsamında rol ataması ekleyin. Ayrıca, `Directory (tenant) ID`, `Application ID` ve `Client Secret` ' nin nasıl alınacağını gösterir.

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Veri Gezgini kümesi oluşturma

1. Aşağıdaki komutu kullanarak kümenizi oluşturun:

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
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

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Ayarlanmasını** | **Önerilen değer** | **Alan açıklaması**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Kümenizin istenen adı.|
   | sku_name | *Standard_D13_v2* | Kümeniz için kullanılacak SKU. |
   | katman | *Stand* | SKU katmanı. |
   | kü | *sayısından* | Küme örneklerinin sayısı. |
   | resource_group_name | *testrg* | Kümenin oluşturulacağı kaynak grubu adı. |

    > [!NOTE]
    > **Küme oluşturma** işlemi uzun süredir çalışır. **Create_or_update** yöntemi lropoller 'ın bir örneğini döndürür, daha fazla bilgi almak için bkz. [lropoller sınıfı](/python/api/msrest/msrest.polling.lropoller?view=azure-python) .

1. Kümenizin başarıyla oluşturulup oluşturulmayacağını denetlemek için şu komutu çalıştırın:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Sonuç `Succeeded` değeriyle `provisioningState` içeriyorsa, küme başarıyla oluşturuldu.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Veri Gezgini kümesinde veritabanı oluşturma

1. Aşağıdaki komutu kullanarak veritabanınızı oluşturun:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**Ayarlanmasını** | **Önerilen değer** | **Alan açıklaması**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Veritabanının oluşturulacağı Kümenizin adı.|
   | veritabanı | *mykustodatabase* | Veritabanınızın adı.|
   | resource_group_name | *testrg* | Kümenin oluşturulacağı kaynak grubu adı. |
   | soft_delete_period | *3650 gün, 0:00:00* | Verilerin sorgu için kullanılabilir kalacağı zaman miktarı. |
   | hot_cache_period | *3650 gün, 0:00:00* | Verilerin önbellekte tutulacağı zaman miktarı. |

1. Oluşturduğunuz veritabanını görmek için aşağıdaki komutu çalıştırın:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Artık bir kümeniz ve veritabanınız var.

## <a name="clean-up-resources"></a>Kaynakları Temizleme

* Diğer makalelerimizi izlemeyi planlıyorsanız oluşturduğunuz kaynakları saklayın.
* Kaynakları temizlemek için kümeyi silin. Bir kümeyi sildiğinizde, içindeki tüm veritabanlarını da siler. Kümenizi silmek için aşağıdaki komutu kullanın:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Veri Gezgini Python kitaplığını kullanarak verileri alma](python-ingest-data.md)
