---
title: "Öğretici: Azure CLı kullanarak HDInsight 'ta Apache Kafka REST proxy etkin kümesi oluşturma"
description: Azure HDInsight üzerinde Kafka REST proxy kullanarak Apache Kafka işlemlerini nasıl gerçekleştireceğinizi öğrenin.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cfb9ff9e6b107c9da84b164a055453994fc29229
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786648"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Öğretici: Azure CLı kullanarak HDInsight 'ta Apache Kafka REST proxy etkin kümesi oluşturma

Bu öğreticide, Azure komut satırı arabirimi 'ni (CLı) kullanarak Azure HDInsight 'ta Apache Kafka [rest proxy etkin](./rest-proxy.md) bir küme oluşturmayı öğreneceksiniz. Azure HDInsight kuruluşlara yönelik, yönetilen, tam spektrumlu ve açık kaynaklı bir analiz hizmetidir. Apache Kafka açık kaynaklı, dağıtılmış bir akış platformudur. Yayımla-abone ol ileti kuyruğuna benzer işlevler sağladığı için genellikle ileti aracısı olarak kullanılır. Kafka REST proxy, HTTP üzerinden bir [REST API](/rest/api/hdinsight-kafka-rest-proxy/) aracılığıyla Kafka kümeniz ile etkileşime girebilmenizi sağlar. Azure CLI, Azure kaynaklarını yönetmek için Microsoft tarafından sunulan platformlar arası komut satırı deneyimidir.

Apache Kafka API'sine yalnızca aynı sanal ağ içindeki kaynaklar tarafından erişilebilir. Doğrudan SSH kullanarak kümeye erişebilirsiniz. Diğer hizmetleri, ağları veya sanal makineleri Apache Kafka'ya bağlamak için önce bir sanal ağ oluşturmanız e sonra ağ içinde kaynakları oluşturmanız gerekir. Daha fazla bilgi için bkz. [sanal ağ kullanarak Apache Kafka bağlama](./apache-kafka-connect-vpn-gateway.md).

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
> * Kafka REST proxy önkoşulları
> * Azure CLı kullanarak Apache Kafka kümesi oluşturma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Azure AD 'ye kayıtlı bir uygulama. Kafka REST proxy ile etkileşimde bulunmak için yazdığınız istemci uygulamaları, Azure 'da kimlik doğrulaması yapmak için bu uygulamanın KIMLIĞINI ve parolasını kullanacaktır. Daha fazla bilgi için bkz. [Microsoft Identity platformu ile uygulama kaydetme](../../active-directory/develop/quickstart-register-app.md).

* Kayıtlı uygulamanıza üye olarak bir Azure AD güvenlik grubu. Bu güvenlik grubu, REST proxy ile etkileşime girmesine izin verilen uygulamaları denetlemek için kullanılacaktır. Azure AD grupları oluşturma hakkında daha fazla bilgi için bkz. [temel Grup oluşturma ve Azure Active Directory kullanarak üye ekleme](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Azure CLI. En az sürüm 2.0.79 sahip olduğunuzdan emin olun. Bkz. [Azure CLI 'Yi yüklemeyi](/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka kümesi oluşturma

1. Azure aboneliğinizde oturum açın.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Ortam değişkenlerini ayarlayın. Bu öğreticide değişkenlerin kullanımı Bash 'i temel alır. Diğer ortamlar için hafif Çeşitlemeler gerekecektir.

    |Değişken | Açıklama |
    |---|---|
    |resourceGroupName|RESOURCEGROUPNAME ' i yeni kaynak grubunuzun adıyla değiştirin.|
    |location|KONUMU, kümenin oluşturulacağı bir bölge ile değiştirin. Geçerli konumların listesi için `az account list-locations` komutunu kullanın|
    |clusterName|CLUSTERNAME öğesini, yeni kümeniz için genel olarak benzersiz bir adla değiştirin.|
    |storageAccount|STORAGEACCOUNTNAME ' i yeni depolama hesabınız için bir adla değiştirin.|
    |httpPassword|Küme oturum açma, **yönetici** için parola ile değiştirin.|
    |sshPassword|Secure Shell Kullanıcı adı, **sshuser** için parola ile değiştirin.|
    |securityGroupName|SECURITYGROUPNAME öğesini Kafka Rest proxy için istemci AAD güvenlik grubu adıyla değiştirin. Değişkeni `--kafka-client-group-name` için parametresine geçirilecek `az-hdinsight-create` .|
    |Securitygroupıd|SECURITYGROUPıD değerini Kafka Rest proxy için istemci AAD güvenlik grubu KIMLIĞIYLE değiştirin. Değişkeni `--kafka-client-group-id` için parametresine geçirilecek `az-hdinsight-create` .|
    |storageContainer|Kümenin kullanacağı depolama kapsayıcısı, bu öğretici için olduğu gibi kalır. Bu değişken, kümenin adıyla ayarlanır.|
    |workernodeCount|Kümedeki çalışan düğümü sayısı, bu öğretici için olduğu gibi bırakın. Yüksek kullanılabilirlik sağlamak için, Kafka en az 3 çalışan düğümü gerektirir|
    |clusterType|HDInsight kümesinin türü, bu öğretici için olduğu gibi bırakın.|
    |clusterVersion|HDInsight kümesi sürümü, bu öğretici için olduğu gibi bırakın. Kafka Rest proxy, en düşük 4,0 küme sürümünü gerektirir.|
    |componentVersion|Kafka sürümü, bu öğretici için olduğu gibi bırakın. Kafka Rest proxy, en az 2,1 bileşen sürümünü gerektirir.|

    Değişkenleri istenen değerlerle güncelleştirin. Ardından, ortam değişkenlerini ayarlamak için CLı komutlarını girin.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. Aşağıdaki komutu girerek [kaynak grubunu oluşturun](/cli/azure/group#az_group_create) :

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. Aşağıdaki komutu girerek [bir Azure depolama hesabı oluşturun](/cli/azure/storage/account#az_storage_account_create) :

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. Azure Storage hesabından [birincil anahtarı ayıklayın](/cli/azure/storage/account/keys#az_storage_account_keys_list) ve aşağıdaki komutu girerek bir değişkende saklayın:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. Aşağıdaki komutu girerek [bir Azure depolama kapsayıcısı oluşturun](/cli/azure/storage/container#az_storage_container_create) :

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [HDInsight kümesini oluşturun](/cli/azure/hdinsight#az_hdinsight_create). Komutu girmeden önce aşağıdaki parametreleri aklınızda edin:

    1. Kafka kümeleri için gerekli parametreler:

        |Parametre | Açıklama|
        |---|---|
        |--tür|Değer **Kafka** olmalıdır.|
        |--workernode-veri-diskler-düğüm başına|Çalışan düğümü başına kullanılacak veri diski sayısı. HDInsight Kafka yalnızca veri diskleriyle desteklenir. Bu öğretici **2** değerini kullanır.|

    1. Kafka REST proxy için gerekli parametreler:

        |Parametre | Açıklama|
        |---|---|
        |--Kafka-Management-node-size|Düğümün boyutu. Bu öğretici **Standard_D4_v2** değeri kullanır.|
        |--Kafka-Client-Group-ID|Kafka Rest proxy için istemci AAD güvenlik grubu KIMLIĞI. Değer **$securityGroupID** değişkeninden geçirilir.|
        |--Kafka-Client-Group-Name|Kafka Rest proxy için istemci AAD güvenlik grubu adı. Değer **$securityGroupName** değişkeninden geçirilir.|
        |--sürüm|HDInsight kümesi sürümü en az 4,0 olmalıdır. Değer **$clusterVersion** değişkeninden geçirilir.|
        |--Bileşen-sürümü|Kafka sürümü en az 2,1 olmalıdır. Değer **$componentVersion** değişkeninden geçirilir.|
    
        Kümeyi REST proxy olmadan oluşturmak isterseniz,, `--kafka-management-node-size` `--kafka-client-group-id` ve `--kafka-client-group-name` komutunu kaldırın `az hdinsight create` .

    1. Mevcut bir sanal ağınız varsa, parametreleri ve `--vnet-name` `--subnet` değerlerini ve değerlerini ekleyin.

    Kümeyi oluşturmak için aşağıdaki komutu girin:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Küme oluşturma işleminin tamamlanması birkaç dakika sürebilir. Genellikle 15 etrafında.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Makaleyi tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile Verileriniz Azure Storage 'da depolanır, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmadığı halde bir HDInsight kümesi için de ücretlendirilirsiniz. Kümenin ücretleri depolama ücretinden çok daha fazla olduğundan, kullanımda olmadıkları zaman kümeleri silmek ekonomik bir anlam sağlar.

Kaynakları kaldırmak için aşağıdaki komutlardan tümünü veya bazılarını girin:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

Azure CLı kullanarak Azure HDInsight 'ta Apache Kafka REST proxy etkin kümesini başarıyla oluşturdunuz, REST proxy ile etkileşim kurmak için Python kodu kullanın:

> [!div class="nextstepaction"]
> [Örnek uygulama oluştur](./rest-proxy.md#client-application-sample)