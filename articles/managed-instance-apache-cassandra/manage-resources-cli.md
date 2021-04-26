---
title: Azure CLı kullanarak Apache Cassandra kaynakları için Azure yönetilen örneğini yönetme
description: Azure CLı kullanarak Apache Cassandra için Azure yönetilen örneğinizin yönetimini otomatik hale getirmeye yönelik ortak komutlar hakkında bilgi edinin.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/15/2021
ms.author: thvankra
ms.openlocfilehash: ea28bf21424f0624b4f1bb5856a17672c1c7b106
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875458"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>Azure CLı kullanarak Apache Cassandra kaynakları için Azure yönetilen örneğini yönetme (Önizleme)

Bu makalede, Azure CLı kullanarak Apache Cassandra kümeleri için Azure yönetilen örneğinizin yönetimini otomatikleştirmek üzere kullanılan ortak komutlar açıklanmaktadır.

> [!IMPORTANT]
> Apache Cassandra için Azure yönetilen örneği şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Bu makale, Azure CLı sürüm 2.17.1 veya üstünü gerektirir. Azure Cloud Shell kullanıyorsanız, en son sürüm zaten yüklüdür.
>
> Apache Cassandra kaynakları için Azure yönetilen örneğini yönetme, Azure Resource Manager kaynak URI 'leriyle nasıl çalıştığını ihlal ettiğinden yeniden adlandırılamaz.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Apache Cassandra kümeleri için Azure yönetilen örneği

Aşağıdaki bölümlerde aşağıdakiler de dahil olmak üzere Apache Cassandra kümeleri için Azure yönetilen örneğinin nasıl yönetileceği gösterilmektedir:

* [Yönetilen örnek kümesi oluşturma](#create-cluster)
* [Yönetilen örnek kümesini silme](#delete-cluster)
* [Küme ayrıntılarını al](#get-cluster-details)
* [Küme düğümü durumunu al](#get-cluster-status)
* [Grupları kaynak grubuna göre Listele](#list-clusters-resource-group)
* [Kümeleri abonelik KIMLIĞINE göre Listele](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Yönetilen örnek kümesi oluşturma

[Az Managed-Cassandra kümesi Create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create) komutunu kullanarak Apache Cassandra kümesi Için bir Azure yönetilen örneği oluşturun:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Yönetilen örnek kümesini silme

[Az Managed-Cassandra Cluster Delete](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_delete) komutunu kullanarak bir kümeyi silme:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>Küme ayrıntılarını al

[Az Managed-Cassandra Cluster Show](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_show) komutunu kullanarak küme ayrıntılarını alın:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>Küme düğümü durumunu al

[Az Managed-Cassandra küme düğümü-Status](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_node_status) komutunu kullanarak küme ayrıntılarını alın:

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>Kümeleri kaynak grubuna göre listeleme

[Az Managed-Cassandra Cluster List](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_list) komutunu kullanarak kümeleri kaynak grubuna göre listeleyin:

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>Kümeleri abonelik KIMLIĞINE göre Listele

[Az Managed-Cassandra küme listesi](/cli/azure/managed-cassandra?view=azure-cli-latest&preserve-view=true) komutunu kullanarak KÜMELERI abonelik kimliğine göre listeleyin:

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>Yönetilen örnek veri merkezleri

Aşağıdaki bölümlerde aşağıdakiler de dahil olmak üzere Apache Cassandra datacenters için Azure yönetilen örneğinin nasıl yönetileceği gösterilmektedir:

* [Veri merkezi oluşturma](#create-datacenter)
* [Veri merkezini silme](#delete-datacenter)
* [Veri merkezi ayrıntılarını al](#get-datacenter-details)
* [Veri merkezini güncelleştirme veya ölçeklendirme](#update-datacenter)
* [Kümede veri merkezleri edinme](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Veri merkezi oluşturma

[Az Managed-Cassandra Datacenter Create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create) komutunu kullanarak bir veri merkezi oluşturun:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>Veri merkezini silme

[Az Managed-Cassandra Datacenter Delete](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_delete) komutunu kullanarak bir veri merkezini silme:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Veri merkezi ayrıntılarını al

[Az Managed-Cassandra Datacenter Show](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_show) komutunu kullanarak veri merkezi ayrıntılarını alın:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Veri merkezini güncelleştirme veya ölçeklendirme

[Az Managed-Cassandra Datacenter Update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) komutunu kullanarak bir veri merkezini (değişiklik nodeCount değerini ölçeklendirmek için) güncelleştirin veya ölçeklendirin:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
delegatedSubnetId= '/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>Küme içindeki veri merkezlerini al

[Az Managed-Cassandra Datacenter List](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_list) komutunu kullanarak bir kümede veri merkezleri alın:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure portal yönetilen örnek kümesi oluşturma](create-cluster-portal.md)
* [Azure Databricks ile yönetilen Apache Spark kümesi dağıtma](deploy-cluster-databricks.md)
