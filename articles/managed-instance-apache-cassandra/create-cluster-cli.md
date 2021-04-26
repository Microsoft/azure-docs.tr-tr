---
title: Hızlı başlangıç-Apache Cassandra kümesi için Azure yönetilen örneği oluşturmak için CLı kullanma
description: Azure CLı kullanarak Apache Cassandra kümesi için Azure yönetilen örneği oluşturmak üzere bu hızlı başlangıcı kullanın.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: 56fe69ad7f56d62c9f61738448ea0276fee47063
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862534"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Hızlı başlangıç: Azure CLı kullanarak Apache Cassandra kümesi için Azure yönetilen örneği oluşturma (Önizleme)

Apache Cassandra için Azure yönetilen örneği, yönetilen açık kaynaklı Apache Cassandra veri merkezleri için otomatik dağıtım ve ölçeklendirme işlemleri sağlar. Bu hizmet, karma senaryoları hızlandırmanıza ve devam eden bakımın azaltılmasına yardımcı olur.

> [!IMPORTANT]
> Apache Cassandra için Azure yönetilen örneği şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bu hızlı başlangıçta Apache Cassandra için Azure yönetilen örneği ile bir küme oluşturmak üzere Azure CLı komutlarının nasıl kullanılacağı gösterilmektedir. Ayrıca, bir veri merkezi oluşturmak ve düğümleri veri merkezinde yukarı veya aşağı ölçeklendirmek için de gösterilir.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Şirket içinde barındırılan veya şirket içi ortamınıza bağlantısı olan [Azure sanal ağı](../virtual-network/virtual-networks-overview.md) . Şirket içi ortamları Azure 'a bağlama hakkında daha fazla bilgi için bkz. Şirket [içi ağı Azure 'A bağlama](/azure/architecture/reference-architectures/hybrid-networking/) makalesi.

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> [!IMPORTANT]
> Bu makale, Azure CLı sürüm 2.17.1 veya üstünü gerektirir. Azure Cloud Shell kullanıyorsanız, en son sürüm zaten yüklüdür.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Yönetilen örnek kümesi oluşturma

1. [Azure portalda](https://portal.azure.com/) oturum açma

1. Azure CLı 'de abonelik KIMLIĞINIZI ayarlayın:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Daha sonra, kaynak grubunuzda ayrılmış bir alt ağa sahip bir sanal ağ oluşturun:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```
    > [!NOTE]
    > Apache Cassandra için Azure yönetilen örneğinin dağıtımı, internet erişimi gerektirir. İnternet erişiminin kısıtlandığı ortamlarda dağıtım başarısız olur. VNet 'iniz içindeki erişimi, yönetilen Cassandra 'nin düzgün çalışması için gerekli olan aşağıdaki önemli Azure hizmetlerine engellemediğinizden emin olun:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Sanal Makine Ölçek Kümeleri
    > - Azure İzleme
    > - Azure Active Directory
    > - Azure Güvenlik

1. Yönetilen örnek için gerekli olan sanal ağa bazı özel izinler uygulayın. Komutunu, `az role assignment create` `<subscription ID>` `<resource group name>` ve yerine `<VNet name>` uygun değerleri kullanarak kullanın:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > `assignee` `role` Önceki komutta ve değerleri sabit değerlerdir, bu değerleri tam olarak komutta belirtildiği gibi girin. Bunu yapmadığınızda, küme oluşturulurken hatalara neden olur. Bu komutu yürütürken herhangi bir hatayla karşılaşırsanız, çalıştırma izniniz olmayabilir, lütfen izinler için yöneticinize ulaşın.

1. Daha sonra [az Managed-Cassandra cluster create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create) komutunu kullanarak kümeyi yeni oluşturduğunuz sanal ağınızda oluşturun. Şu komutu çalıştırın `delegatedManagementSubnetId` :

   > [!NOTE]
   > `delegatedManagementSubnetId`Aşağıda sağlayacaksınız değişkeninin değeri, `--scope` Yukarıdaki komutta sağladığınız değerle tamamen aynıdır:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Son olarak, [az Managed-Cassandra Datacenter Create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create) komutunu kullanarak, üç düğüm ile küme için bir veri merkezi oluşturun:

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. Veri merkezi oluşturulduktan sonra, veri merkezindeki düğümleri ölçeklendirmek veya ölçeği daraltmak istiyorsanız [az Managed-Cassandra Datacenter Update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) komutunu çalıştırın. `node-count`Parametresinin değerini istenen değerle değiştirin:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Kümenize bağlanma

Apache Cassandra için Azure yönetilen örneği, genel IP adresleriyle düğüm oluşturmaz. Yeni oluşturulan Cassandra kümenize bağlanmak için, sanal ağ içinde başka bir kaynak oluşturmanız gerekir. Bu kaynak, bir uygulama veya Apache 'in açık kaynaklı sorgu aracı [Ckalite sh](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) 'i olan bir sanal makine olabilir. Ubuntu sanal makinesini dağıtmak için bir [Kaynak Yöneticisi şablonu](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) kullanabilirsiniz. Dağıtıldıktan sonra, makineye bağlanmak için SSH kullanın ve aşağıdaki komutlarda gösterildiği gibi CSQLSH 'i yüklemek için:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>Sorun giderme

Sanal ağınıza izin uygularken bir hatayla karşılaşırsanız (örneğin, *' e5007d2c-4b13-4a74-9b6a-605d99f03501 ' için grafik veritabanında kullanıcı veya hizmet sorumlusu bulunamıyor*), Azure Portal aynı izni el ile uygulayabilirsiniz. Portaldan izinleri uygulamak için, var olan sanal ağınızın **erişim denetimi (IAM)** bölmesine gidin ve "Azure Cosmos DB" Için "Ağ Yöneticisi" rolüne bir rol ataması ekleyin. "Azure Cosmos DB" araması yaptığınızda iki giriş görünürse, aşağıdaki görüntüde gösterildiği gibi her iki girdiyi de ekleyin: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="İzinleri Uygula" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Azure Cosmos DB rol ataması yalnızca dağıtım amacıyla kullanılır. Apache Cassandra için Azure yönetilen ınstanced Azure Cosmos DB için arka uç bağımlılığı yok.  

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, `az group delete` komutunu kullanarak kaynak grubunu, yönetilen örneği ve tüm ilgili kaynakları kaldırabilirsiniz:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure CLı kullanarak Apache Cassandra kümesi için Azure yönetilen örneği oluşturmayı öğrendiniz. Artık kümeyle çalışmaya başlayabilirsiniz:

> [!div class="nextstepaction"]
> [Azure Databricks ile yönetilen Apache Spark kümesi dağıtma](deploy-cluster-databricks.md)