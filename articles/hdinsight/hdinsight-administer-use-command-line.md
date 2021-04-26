---
title: Azure CLı kullanarak Azure HDInsight kümelerini yönetme
description: Azure HDInsight kümelerini yönetmek için Azure CLı 'yı nasıl kullanacağınızı öğrenin. Küme türleri Apache Hadoop, Spark, HBase, fırtınası, Kafka, Interactive Query ve ML hizmetlerini içerir.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-azurecli
ms.date: 02/26/2020
ms.openlocfilehash: 14b88700f3968e3bfdc788abb2fc9ce90634068e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770354"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Azure CLı kullanarak Azure HDInsight kümelerini yönetme

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure HDInsight kümelerini yönetmek için [Azure CLI](/cli/azure/) 'yi nasıl kullanacağınızı öğrenin. Azure komut satırı arabirimi (CLI), Azure kaynaklarını yönetmek için Microsoft tarafından sunulan platformlar arası komut satırı deneyimidir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Azure CLI. Azure CLı 'yı yüklemediyseniz, adımlar için bkz. [Azure CLI 'Yi yükleme](/cli/azure/install-azure-cli) .

* HDInsight üzerinde bir Apache Hadoop kümesi. Bkz. [Linux 'Ta HDInsight kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Azure'a Bağlanma

Azure aboneliğinizde oturum açın. Azure Cloud Shell kullanmayı planlıyorsanız, kod bloğunun sağ üst köşesinde bulunan **bunu dene** ' yi seçin. Aksi takdirde, aşağıdaki komutu girin:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Kümeleri Listele

Kümeleri listelemek için [az HDInsight List](/cli/azure/hdinsight#az_hdinsight_list) kullanın. Aşağıdaki komutları, `RESOURCE_GROUP_NAME` kaynak grubunuzun adıyla değiştirin ve ardından komutları girin:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Kümeyi göster

Belirtilen bir kümenin bilgilerini göstermek için [az HDInsight Show](/cli/azure/hdinsight#az_hdinsight_show) ' i kullanın. Aşağıdaki komutu `RESOURCE_GROUP_NAME` ve ilgili bilgileri değiştirerek aşağıdaki komutu düzenleyin `CLUSTER_NAME` :

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Kümeleri Sil

Belirtilen kümeyi silmek için [az HDInsight Delete](/cli/azure/hdinsight#az_hdinsight_delete) kullanın. Aşağıdaki komutu `RESOURCE_GROUP_NAME` ve ilgili bilgileri değiştirerek aşağıdaki komutu düzenleyin `CLUSTER_NAME` :

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Kümeyi içeren kaynak grubunu silerek de bir kümeyi silebilirsiniz. Bu, varsayılan depolama hesabı da dahil olmak üzere gruptaki tüm kaynakların silineceğini aklınızda görürsünüz.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Ölçek Kümeleri

Belirtilen HDInsight kümesini belirtilen boyuta göre yeniden boyutlandırmak için [az HDInsight Resize](/cli/azure/hdinsight#az_hdinsight_resize) ' i kullanın. Aşağıdaki komutu `RESOURCE_GROUP_NAME` , ve `CLUSTER_NAME` ile ilgili bilgilerle değiştirin. `WORKERNODE_COUNT`Kümeniz için istenen çalışan düğüm sayısıyla değiştirin. Kümeleri ölçeklendirme hakkında daha fazla bilgi için bkz. [HDInsight kümelerini ölçeklendirme](./hdinsight-scaling-best-practices.md). Şu komutu girin:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, farklı HDInsight kümesi yönetim görevlerinin nasıl gerçekleştirileceğini öğrendiniz. Daha fazla bilgi için aşağıdaki makalelere bakın:

* [HDInsight 'ta Apache Hadoop kümelerini Azure portal kullanarak yönetin](hdinsight-administer-use-portal-linux.md)
* [Azure PowerShell kullanarak HDInsight 'ı yönetme](hdinsight-administer-use-powershell.md)
* [Azure HDInsight 'ı kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure CLI'yi kullanmaya başlama](/cli/azure/get-started-with-azure-cli)
