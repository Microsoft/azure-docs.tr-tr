---
title: 'Azure HDInsight: Azure CLI örnekleri'
description: Azure HDInsight 'ta ortak görevlere yönelik Azure CLı örnekleri.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 09/23/2019
ms.openlocfilehash: 7ce2160fa94653a1155daaef166b7fdbb9386e7e
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266716"
---
# <a name="azure-hdinsight-azure-cli-samples"></a>Azure HDInsight: Azure CLI örnekleri

> [!div class="op_single_selector"]
> [.NET örnekleri](hdinsight-sdk-dotnet-samples.md)
> [Python örnekleri](hdinsight-sdk-python-samples.md)
> [Java örnekleri](hdinsight-sdk-java-samples.md)

Bu makalede ortak görevler için örnek betikler sağlanmaktadır. Her örnek için, değişkenleri uygun değerlerle güncelleştirin ve ardından komutunu yürütün.

## <a name="prerequisites"></a>Önkoşullar

* Azure CLı. Adımlar için bkz. [Azure CLI 'Yı yüklemeyin](https://docs.microsoft.com/cli/azure/install-azure-cli) .

* İsteğe bağlı: Bash. Bu makaledeki örneklerde, Windows 10 ' da bash kabuğu kullanılır. Yükleme adımları için bkz. [Windows 10 Linux Için Windows alt sistemi yükleme kılavuzu](https://docs.microsoft.com/windows/wsl/install-win10) .  Örnekler, bazı hafif değişikliklerle bir Windows komut isteminden çalışır.

## <a name="az-login"></a>az Login

[Azure 'Da oturum açın](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login).

```azurecli
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="az-hdinsight-create"></a>az HDInsight oluştur

[Yeni bir küme oluşturur](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

### <a name="create-a-cluster-with-an-existing-storage-account"></a>Mevcut depolama hesabı ile küme oluşturma

```azurecli
# set variables
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=hadoop
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT
```

### <a name="create-a-cluster-with-the-enterprise-security-package-esp"></a>Kurumsal Güvenlik Paketi (ESP) ile küme oluşturma

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export subnet="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworks/MyVnet/subnets/subnet1"
export domain="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRG/providers/Microsoft.AAD/domainServices/MyDomain.onmicrosoft.com"
export userAssignedIdentity="/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/MyMsiRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/MyMSI"
export domainAccount=MyAdminAccount@MyDomain.onmicrosoft.com
export groupDNS=MyGroup

az hdinsight create \
    --esp \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --subnet $subnet \
    --domain $domain \
    --assign-identity $userAssignedIdentity \
    --cluster-admin-account $domainAccount \
    --cluster-users-group-dns $groupDNS
```

### <a name="create-a-kafka-cluster-with-disk-encryptionkafkaapache-kafka-byokmd"></a>[Disk şifrelemesi](kafka/apache-kafka-byok.md) ile Kafka kümesi oluşturma

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=kafka
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export encryptionKeyName=kafkaClusterKey
export encryptionKeyVersion=00000000000000000000000000000000
export encryptionVaultUri=https://MyKeyVault.vault.azure.net
export userAssignedIdentity="/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/MyMsiRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/MyMSI"

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --workernode-data-disks-per-node 2 \
    --encryption-key-name $encryptionKeyName \
    --encryption-key-version $encryptionKeyVersion \
    --encryption-vault-uri $encryptionVaultUri \
    --assign-identity $userAssignedIdentity
```

### <a name="create-a-cluster-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. ile küme oluşturma

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export adlgen2=MyStorageAccount
export sami=MyMSI

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $adlgen2 \
    --storage-account-managed-identity $sami
```

### <a name="create-a-cluster-with-configuration-from-json-string"></a>JSON dizesinden yapılandırmaya sahip bir küme oluştur

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export clusterConfiguration="{'gateway':{'restAuthCredential.username':'admin'}}"

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --cluster-configuration $clusterConfiguration
```

### <a name="create-a-cluster-with-configuration-from-a-local-file"></a>Yerel bir dosyadan yapılandırma ile küme oluşturma

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export clusterConfiguration=@/mnt/c/HDI/config.json

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --cluster-configuration $clusterConfiguration
```

## <a name="az-hdinsight-application-create"></a>az HDInsight uygulama oluştur

[HDInsight kümesi için bir uygulama oluşturun](https://docs.microsoft.com/cli/azure/hdinsight/application?view=azure-cli-latest#az-hdinsight-application-create).

### <a name="create-an-application-with-a-script-uri"></a>Betik URI 'SI ile uygulama oluşturma

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export applicationName=MyApplication
export clusterName=CLUSTERNAME
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
export scriptActionName=MyScriptAction
export scriptParameters='"-version latest -port 20000"'

az hdinsight application create \
    --resource-group $resourceGroupName \
    --name $applicationName \
    --cluster-name $clusterName \
    --script-uri $scriptURI  \
    --script-action-name $scriptActionName \
    --script-parameters "$scriptParameters"
```

### <a name="create-an-application-with-a-script-uri-and-specified-edge-node-size"></a>Betik URI 'SI ve belirtilen kenar düğümü boyutu ile bir uygulama oluşturun

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export applicationName=MyApplication
export clusterName=CLUSTERNAME
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
export scriptActionName=MyScriptAction
export scriptParameters='"-version latest -port 20000"'
export edgenodeSize=Standard_D4_v2

az hdinsight application create \
    --resource-group $resourceGroupName \
    --name $applicationName \
    --cluster-name $clusterName \
    --script-uri $scriptURI  \
    --script-action-name $scriptActionName \
    --script-parameters "$scriptParameters" \
    --edgenode-size $edgenodeSize
```

### <a name="create-an-application-with-https-endpoint"></a>HTTPS uç noktası ile uygulama oluşturma

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export applicationName=MyApplication
export clusterName=CLUSTERNAME
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
export scriptActionName=MyScriptAction
export scriptParameters='"-version latest -port 20000"'
export destinationPort=8888
export subDomainSuffix=was

az hdinsight application create \
    --resource-group $resourceGroupName \
    --name $applicationName \
    --cluster-name $clusterName \
    --script-uri $scriptURI  \
    --script-action-name $scriptActionName \
    --script-parameters "$scriptParameters" \
    --destination-port $destinationPort \
    --sub-domain-suffix $subDomainSuffix
```

## <a name="az-hdinsight-script-action-execute"></a>az HDInsight betiği-eylem yürütme

[Belirtilen HDInsight kümesinde betik eylemlerini yürütün](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).

### <a name="execute-a-script-action-and-persist-on-success"></a>Betik eylemi yürütme ve başarılı olma devam eden

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export clusterName=CLUSTERNAME
export scriptActionName=MyScriptAction
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
export roles="headnode workernode"

az hdinsight script-action execute \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --name $scriptActionName \
    --script-uri $scriptURI  \
    --roles $roles \
    --persist-on-success
```
