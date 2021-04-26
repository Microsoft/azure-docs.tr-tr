---
title: Azure HDInsight oluşturma-Azure Data Lake Storage 2.-Azure CLı
description: Azure CLı kullanarak Azure HDInsight kümeleri ile Azure Data Lake Storage 2. nasıl kullanacağınızı öğrenin.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 09/17/2020
ms.openlocfilehash: bbc1cd27d5c16eddd3aaad748c34445e5017e209
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945489"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Azure CLı kullanarak Data Lake Storage 2. bir küme oluşturma

Depolama için Data Lake Storage 2. kullanan bir HDInsight kümesi oluşturmak için aşağıdaki adımları izleyin.

## <a name="prerequisites"></a>Önkoşullar

- Azure Data Lake Storage 2. [hakkında bilgi sahibi değilseniz genel bakış bölümüne](hdinsight-hadoop-use-data-lake-storage-gen2.md)bakın. 
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).
- CLı betiği örneklerini çalıştırmak için üç seçeneğiniz vardır:
    - Azure portal [Azure Cloud Shell](../cloud-shell/overview.md) kullanın (sonraki bölüme bakın).
    - Katıştırılmış Azure Cloud Shell her kod bloğunun sağ üst köşesinde bulunan "dene" düğmesini kullanarak kullanın.
    - Yerel bir CLı konsolu kullanmayı tercih ediyorsanız [Azure CLI 'nın en son sürümünü](/cli/azure/install-azure-cli) (2.0.13 veya üzeri) yükleyebilirsiniz. `az login`Kullanıcı tarafından atanan yönetilen kimliği dağıtmak Istediğiniz Azure aboneliğiyle ilişkili bir hesabı kullanarak Azure 'da oturum açın. Azure CLı. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Örnek bir [şablon dosyası indirebilir](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) ve [örnek bir parametre dosyası indirebilirsiniz](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Aşağıdaki şablon ve Azure CLı kod parçacığını kullanmadan önce aşağıdaki yer tutucuları doğru değerlerle değiştirin:

| Yer tutucu | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure aboneliğinizin KIMLIĞI |
| `<RESOURCEGROUPNAME>` | Yeni küme ve depolama hesabının oluşturulmasını istediğiniz kaynak grubu. |
| `<MANAGEDIDENTITYNAME>` | Azure Data Lake Storage 2. ile depolama hesabınızda izin verilecek yönetilen kimliğin adı. |
| `<STORAGEACCOUNTNAME>` | Oluşturulacak Azure Data Lake Storage 2. yeni depolama hesabı. |
| `<FILESYSTEMNAME>`  | Bu kümenin depolama hesabında kullanması gereken FileSystem adı. |
| `<CLUSTERNAME>` | HDInsight Kümenizin adı. |
| `<PASSWORD>` | SSH ve ambarı panosunu kullanarak kümede oturum açmak için seçtiğiniz parola. |

Aşağıdaki kod parçacığı aşağıdaki ilk adımları yapar:

1. Azure hesabınızda oturum açar.
1. Oluşturma işlemlerinin yapılacağı etkin aboneliği ayarlar.
1. Yeni dağıtım etkinlikleri için yeni bir kaynak grubu oluşturur.
1. Kullanıcı tarafından atanan yönetilen kimlik oluşturur.
1. Data Lake Storage 2. özelliklerini kullanmak için Azure CLı 'ya bir uzantı ekler.
1. Bayrağını kullanarak Data Lake Storage 2. yeni bir depolama hesabı oluşturur `--hierarchical-namespace true` .

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Sonra portalda oturum açın. Depolama hesabındaki **Depolama Blobu veri katılımcısı** rolüne Yeni Kullanıcı tarafından atanan yönetilen kimliği ekleyin. Bu adım, [Azure Portal kullanımı](hdinsight-hadoop-use-data-lake-storage-gen2.md)altında adım 3 ' te açıklanmaktadır.

 > [!IMPORTANT]
 > Depolama hesabınızın, **Depolama Blobu veri katılımcısı** rolü izinleriyle Kullanıcı tarafından atanan kimliğe sahip olduğundan emin olun, aksi takdirde küme oluşturma başarısız olur.

```azurecli
az deployment group create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

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

Bir HDInsight kümesini başarıyla oluşturdunuz. Şimdi kümenizle nasıl çalışacağınızı öğrenin.

### <a name="apache-spark-clusters"></a>Apache Spark kümeleri

* [Betik eylemlerini kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md)
* [Scala kullanarak tek başına uygulama oluşturma](spark/apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](spark/apache-spark-livy-rest-interface.md)
* [BI ile Apache Spark: bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](spark/apache-spark-use-bi-tools.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kümeleri

* [HDInsight ile Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
* [HDInsight ile MapReduce kullanma](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase kümeleri

* [HDInsight 'ta Apache HBase ile çalışmaya başlama](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight 'ta Apache HBase için Java uygulamaları geliştirme](hbase/apache-hbase-build-java-maven-linux.md)
