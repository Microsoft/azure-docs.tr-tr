---
title: 'Hızlı başlangıç: Azure CLı ile Apache Spark kümeleri-Azure HDInsight'
description: Bu hızlı başlangıçta, Azure HDInsight 'ta Apache Spark kümesi oluşturmak için Azure CLı 'nin nasıl kullanılacağı gösterilmektedir.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 71b5e9f0ece79633673b183ca7288852f42ca3c0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494713"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak Azure HDInsight 'ta Apache Spark kümesi oluşturma

Bu hızlı başlangıçta Azure CLı kullanarak Azure HDInsight 'ta Apache Spark kümesi oluşturmayı öğreneceksiniz. Apache Spark, bellek içi işleme kullanarak hızlı veri analizi ve küme hesaplama sağlar. [Azure komut satırı arabirimi (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) , Microsoft 'un Azure kaynaklarını yönetmek için platformlar arası komut satırı deneyimidir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure CLı. Azure CLı 'yı yüklemediyseniz, adımlar için bkz. [Azure CLI 'Yi yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli) .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Apache Spark kümesi oluşturma

1. Azure aboneliğinizde oturum açın. Azure Cloud Shell kullanmayı planlıyorsanız, yalnızca kod bloğunun sağ üst köşesinde **deneyin** seçeneğini belirleyin. Aksi takdirde, aşağıdaki komutu girin:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ortam değişkenlerini ayarlayın. Bu hızlı başlangıçta değişkenlerin kullanımı Bash 'i temel alır. Diğer ortamlar için hafif Çeşitlemeler gerekecektir. Aşağıdaki kod parçacığındaki RESOURCEGROUPNAME, LOCATION, CLUSTERNAME, STORAGEACCOUNTNAME ve PASSWORD değerlerini istenen değerlerle değiştirin. Ardından, ortam değişkenlerini ayarlamak için CLı komutlarını girin.

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
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. Aşağıdaki komutu girerek kaynak grubunu oluşturun:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Aşağıdaki komutu girerek bir Azure depolama hesabı oluşturun:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Azure Storage hesabından birincil anahtarı ayıklayın ve aşağıdaki komutu girerek bir değişkende saklayın:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Aşağıdaki komutu girerek bir Azure depolama kapsayıcısı oluşturun:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Aşağıdaki komutu girerek Apache Spark kümesini oluşturun:

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

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıcı tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile, verileriniz Azure Storage’da depolanır, böylece kullanılmadığında bir kümeyi güvenle silebilirsiniz. Ayrıca, kullanılmıyorken dahi HDInsight kümesi için sizden ücret kesilir. Küme ücretleri depolama ücretlerinin birkaç katı olduğundan, kullanılmadığında kümelerin silinmesi mantıklı olandır.

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

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Azure CLı kullanarak Azure HDInsight 'ta Apache Spark kümesi oluşturmayı öğrendiniz.  HDInsight Spark kümesini kullanarak örnek veriler üzerinde etkileşimli sorgular çalıştırma hakkında daha fazla bilgi edinmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Apache Spark etkileşimli sorgular çalıştırma](./apache-spark-load-data-run-query.md)
