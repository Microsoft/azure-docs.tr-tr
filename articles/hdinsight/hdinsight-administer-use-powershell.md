---
title: PowerShell ile Apache Hadoop kümelerini yönetme-Azure HDInsight
description: Azure PowerShell kullanarak HDInsight 'ta Apache Hadoop kümeleri için yönetim görevleri gerçekleştirmeyi öğrenin.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: c8581c2288eacb47fb9475a3b0a584434c8604f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942802"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>HDInsight 'ta Azure PowerShell kullanarak Apache Hadoop kümelerini yönetme

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell, Azure 'da iş yüklerinizin dağıtımını ve yönetimini denetlemek ve otomatik hale getirmek için kullanılabilir. Bu makalede, Azure PowerShell az modülünü kullanarak Azure HDInsight 'ta [Apache Hadoop](https://hadoop.apache.org/) kümelerini yönetmeyi öğreneceksiniz. HDInsight PowerShell cmdlet 'lerinin listesi için, [az. HDInsight başvurusuna](/powershell/module/az.hdinsight)bakın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell [az Module](/powershell/azure/) yüklendi.

## <a name="create-clusters"></a>Küme oluşturma

Bkz. [Azure PowerShell kullanarak HDInsight 'Ta Linux tabanlı kümeler oluşturma](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Kümeleri Listele

Geçerli abonelikteki tüm kümeleri listelemek için aşağıdaki komutu kullanın:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Kümeyi göster

Geçerli abonelikteki belirli bir kümenin ayrıntılarını göstermek için aşağıdaki komutu kullanın:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Kümeleri Sil

Kümeyi silmek için aşağıdaki komutu kullanın:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Kümeyi içeren kaynak grubunu kaldırarak da bir kümeyi silebilirsiniz. Bir kaynak grubunu silmek, varsayılan depolama hesabı da dahil olmak üzere gruptaki tüm kaynakları siler.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Ölçek Kümeleri

Küme ölçekleme özelliği, kümeyi yeniden oluşturmaya gerek kalmadan Azure HDInsight 'ta çalışan bir küme tarafından kullanılan çalışan düğümlerinin sayısını değiştirmenize izin verir. Azure PowerShell kullanarak Hadoop kümesi boyutunu değiştirmek için bir istemci makinesinden aşağıdaki komutu çalıştırın:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Kümeleri ölçeklendirme hakkında daha fazla bilgi için bkz. [HDInsight kümelerini ölçeklendirme](./hdinsight-scaling-best-practices.md).

## <a name="update-http-user-credentials"></a>HTTP Kullanıcı kimlik bilgilerini güncelleştirme

[Set-AzHDInsightGatewayCredential](/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) bir Azure HDInsight kümesinin ağ geçidi http kimlik bilgilerini ayarlar.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Varsayılan depolama hesabını bulun

Aşağıdaki PowerShell betiği, varsayılan depolama hesabı adının ve ilgili bilgilerin nasıl alınacağını gösterir:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>Kaynak grubunu bulma

Kaynak Yöneticisi modunda, her HDInsight kümesi bir Azure Kaynak grubuna aittir.  Kaynak grubunu bulmak için:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>İşleri gönder

**MapReduce işlerini göndermek için**

Bkz. [HDInsight 'ta bulunan MapReduce örneklerini çalıştırma](hadoop/apache-hadoop-run-samples-linux.md).

**Apache Hive işleri göndermek için**

Bkz. [PowerShell kullanarak Apache Hive sorguları çalıştırma](hadoop/apache-hadoop-use-hive-powershell.md).

**Apache Sqoop işleri göndermek için**

Bkz. [HDInsight Ile Apache Sqoop kullanma](hadoop/hdinsight-use-sqoop.md).

**Apache Oozie işleri göndermek için**

Bkz. [HDInsight 'ta bir iş akışı tanımlamak ve çalıştırmak için Apache Hadoop Ile Apache Oozie kullanma](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Azure Blob depolama alanına veri yükleme

Bkz. [HDInsight'a veri yükleme](hdinsight-upload-data.md).

## <a name="see-also"></a>Ayrıca Bkz.

* [Az. HDInsight cmdlet 'leri](/powershell/module/az.hdinsight/#hdinsight)
* [HDInsight 'ta Apache Hadoop kümelerini Azure portal kullanarak yönetin](hdinsight-administer-use-portal-linux.md)
* [Bir komut satırı arabirimi kullanarak HDInsight 'ı yönetme](hdinsight-administer-use-command-line.md)
* [HDInsight kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Hadoop işleri programlı olarak gönderme](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Azure HDInsight 'ı kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md)