---
title: Azure CLı kullanarak Apache Hadoop kümeleri oluşturma-Azure HDInsight
description: Platformlar arası Azure CLı kullanarak Azure HDInsight kümeleri oluşturmayı öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: 09696f5a3df7cc4170c57b862a11bbd5a82e2bc9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494795"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Azure CLı kullanarak HDInsight kümeleri oluşturma

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Bu belgedeki adımlar, Azure CLı kullanarak HDInsight 3,6 kümesi oluşturma konusunda yol gösterir.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure CLı. Azure CLı 'yı yüklemediyseniz, adımlar için bkz. [Azure CLI 'Yi yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Küme oluşturma

1. Azure aboneliğinizde oturum açın. Azure Cloud Shell kullanmayı planlıyorsanız, yalnızca kod bloğunun sağ üst köşesinde **deneyin** seçeneğini belirleyin. Aksi takdirde, aşağıdaki komutu girin:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ortam değişkenlerini ayarlayın. Bu makaledeki değişkenlerin kullanımı Bash 'i temel alır. Diğer ortamlar için hafif Çeşitlemeler gerekecektir. Küme oluşturma için olası parametrelerin tam bir listesi için bkz. [az-HDInsight-Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) .

    |Parametre | Açıklama |
    |---|---|
    |`--size`| Kümedeki çalışan düğümlerinin sayısı. Bu makale, `--size`aktarılan değer olarak `clusterSizeInNodes` değişkenini kullanır. |
    |`--version`| HDInsight kümesi sürümü. Bu makale, `--version`aktarılan değer olarak `clusterVersion` değişkenini kullanır. Ayrıca bkz: [desteklenen HDInsight sürümleri](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| HDInsight kümesinin türü, örneğin: Hadoop, ınteractivehive, HBase, Kafka, fırtınası, Spark, Rserver, mlservices.  Bu makale, `--type`aktarılan değer olarak `clusterType` değişkenini kullanır. Ayrıca bkz: [küme türleri ve yapılandırması](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types).|
    |`--component-version`|' Component = Version ' biçimindeki, boşlukla ayrılmış sürümlerde çeşitli Hadoop bileşenlerinin sürümleri. Bu makale, `--component-version`aktarılan değer olarak `componentVersion` değişkenini kullanır. Ayrıca bkz: [Hadoop bileşenleri](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    `RESOURCEGROUPNAME`, `LOCATION`, `CLUSTERNAME`, `STORAGEACCOUNTNAME`ve `PASSWORD` istenen değerlerle değiştirin. Diğer değişkenlerin değerlerini istediğiniz şekilde değiştirin. Ardından CLı komutlarını girin.

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

3. Aşağıdaki komutu girerek [kaynak grubunu oluşturun](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) :

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Geçerli konumların bir listesi için `az account list-locations` komutunu kullanın ve ardından `name` değerindeki konumlardan birini kullanın.

4. Aşağıdaki komutu girerek [bir Azure depolama hesabı oluşturun](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) :

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

5. [Azure Storage hesabından birincil anahtarı ayıklayın](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) ve aşağıdaki komutu girerek bir değişkende saklayın:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Aşağıdaki komutu girerek [bir Azure depolama kapsayıcısı oluşturun](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) :

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Aşağıdaki komutu girerek [HDInsight kümesini oluşturun](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) :

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight kümeleri, kümenin ayarlanmış olduğu iş yüküne veya teknolojiye karşılık gelen çeşitli türlerde gelir. Bir kümede fırtınası ve HBase gibi birden çok türü birleştiren bir küme oluşturmak için desteklenen bir yöntem yoktur.

    Küme oluşturma işleminin tamamlanması birkaç dakika sürebilir. Genellikle 15 etrafında.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Makaleyi tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile, verileriniz Azure Storage’da depolanır, böylece kullanılmadığında bir kümeyi güvenle silebilirsiniz. Ayrıca, kullanılmıyorken dahi HDInsight kümesi için sizden ücret kesilir. Küme ücretleri depolama ücretlerinin birkaç katı olduğundan, kullanılmadığında kümelerin silinmesi mantıklı olandır.

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
* [HDInsight ile Apache Pig kullanma](hadoop/hdinsight-use-pig.md)
* [HDInsight ile MapReduce kullanma](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase kümeleri

* [HDInsight 'ta Apache HBase ile çalışmaya başlama](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight 'ta Apache HBase için Java uygulamaları geliştirme](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm kümeleri

* [HDInsight üzerinde Apache Storm için Java topolojileri geliştirme](storm/apache-storm-develop-java-topology.md)
* [HDInsight üzerinde Apache Storm Python bileşenlerini kullanma](storm/apache-storm-develop-python-topology.md)
* [HDInsight üzerinde Apache Storm topolojilerini dağıtma ve izleme](storm/apache-storm-deploy-monitor-topology-linux.md)
