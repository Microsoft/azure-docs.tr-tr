---
title: Azure CLı kullanarak Apache Hadoop kümeleri oluşturma-Azure HDInsight
description: Platformlar arası Azure CLı kullanarak Azure HDInsight kümeleri oluşturmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 02/03/2020
ms.openlocfilehash: 9c19eb58e32fec66e5fe698c82133c8583f67b8b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775142"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Azure CLı kullanarak HDInsight kümeleri oluşturma

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Bu belgedeki adımlar, Azure CLı kullanarak HDInsight 3,6 kümesi oluşturma konusunda yol gösterir.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-cluster"></a>Küme oluşturma

1. Azure aboneliğinizde oturum açın. Azure Cloud Shell kullanmayı planlıyorsanız, kod bloğunun sağ üst köşesinde bulunan **bunu dene** ' yi seçin. Aksi takdirde, aşağıdaki komutu girin:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ortam değişkenlerini ayarlayın. Bu makaledeki değişkenlerin kullanımı Bash 'i temel alır. Diğer ortamlar için hafif Çeşitlemeler gerekecektir. Küme oluşturma için olası parametrelerin tam bir listesi için bkz. [az-HDInsight-Create](/cli/azure/hdinsight#az_hdinsight_create) .

    |Parametre | Açıklama |
    |---|---|
    |`--workernode-count`| Kümedeki çalışan düğümlerinin sayısı. Bu makalede, değişkeni `clusterSizeInNodes` geçirildiği değer olarak kullanılır `--workernode-count` . |
    |`--version`| HDInsight kümesi sürümü. Bu makalede, değişkeni `clusterVersion` geçirildiği değer olarak kullanılır `--version` . Ayrıca bkz: [desteklenen HDInsight sürümleri](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| HDInsight kümesinin türü, örneğin: Hadoop, ınteractivehive, HBase, Kafka, fırtınası, Spark, Rserver, mlservices.  Bu makalede, değişkeni `clusterType` geçirildiği değer olarak kullanılır `--type` . Ayrıca bkz: [küme türleri ve yapılandırması](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|' Component = Version ' biçimindeki, boşlukla ayrılmış sürümlerde çeşitli Hadoop bileşenlerinin sürümleri. Bu makalede, değişkeni `componentVersion` geçirildiği değer olarak kullanılır `--component-version` . Ayrıca bkz: [Hadoop bileşenleri](./hdinsight-component-versioning.md).|

    ,,, `RESOURCEGROUPNAME` `LOCATION` `CLUSTERNAME` `STORAGEACCOUNTNAME` Ve `PASSWORD` değerlerini istenen değerlerle değiştirin. Diğer değişkenlerin değerlerini istediğiniz şekilde değiştirin. Ardından CLı komutlarını girin.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. Aşağıdaki komutu girerek [kaynak grubunu oluşturun](/cli/azure/group#az_group_create) :

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Geçerli konumların bir listesi için `az account list-locations` komutunu kullanın ve sonra değerden birini kullanın `name` .

4. Aşağıdaki komutu girerek [bir Azure depolama hesabı oluşturun](/cli/azure/storage/account#az_storage_account_create) :

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Azure Storage hesabından birincil anahtarı ayıklayın](/cli/azure/storage/account/keys#az_storage_account_keys_list) ve aşağıdaki komutu girerek bir değişkende saklayın:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Aşağıdaki komutu girerek [bir Azure depolama kapsayıcısı oluşturun](/cli/azure/storage/container#az_storage_container_create) :

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Aşağıdaki komutu girerek [HDInsight kümesini oluşturun](/cli/azure/hdinsight#az_hdinsight_create) :

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight kümeleri, kümenin ayarlanmış olduğu iş yüküne veya teknolojiye karşılık gelen çeşitli türlerde gelir. Bir kümede fırtınası ve HBase gibi birden çok türü birleştiren bir küme oluşturmak için desteklenen bir yöntem yoktur.

    Küme oluşturma işleminin tamamlanması birkaç dakika sürebilir. Genellikle 15 etrafında.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Makaleyi tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile Verileriniz Azure Storage 'da depolanır, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmadığı halde bir HDInsight kümesi için de ücretlendirilirsiniz. Kümenin ücretleri depolama ücretinden çok daha fazla olduğundan, kullanımda olmadıkları zaman kümeleri silmek ekonomik bir anlam sağlar.

Kaynakları kaldırmak için aşağıdaki komutlardan tümünü veya bazılarını girin:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Sorun giderme

HDInsight kümeleri oluştururken sorun yaşarsanız bkz. [erişim denetimi gereksinimleri](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Sonraki adımlar

Azure CLı kullanarak bir HDInsight kümesini başarıyla oluşturduğunuza göre, kümenizle nasıl çalışacağınızı öğrenmek için aşağıdakileri kullanın:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kümeleri

* [HDInsight ile Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
* [HDInsight ile MapReduce kullanma](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase kümeleri

* [HDInsight 'ta Apache HBase ile çalışmaya başlama](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight 'ta Apache HBase için Java uygulamaları geliştirme](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm kümeleri

* [HDInsight üzerinde Apache Storm için Java topolojileri geliştirme](storm/apache-storm-develop-java-topology.md)
* [HDInsight üzerinde Apache Storm Python bileşenlerini kullanma](storm/apache-storm-develop-python-topology.md)
* [HDInsight üzerinde Apache Storm topolojilerini dağıtma ve izleme](storm/apache-storm-deploy-monitor-topology-linux.md)
