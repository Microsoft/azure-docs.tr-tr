---
title: PowerShell ile Apache Hadoop kümelerini yönetme-Azure HDInsight
description: Azure PowerShell kullanarak HDInsight 'ta Apache Hadoop kümeleri için yönetim görevleri gerçekleştirmeyi öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.openlocfilehash: b3cdc673d5d99229e3e6934d85cae55f79590830
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494398"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>HDInsight 'ta Azure PowerShell kullanarak Apache Hadoop kümelerini yönetme
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell, Azure 'da iş yüklerinizin dağıtımını ve yönetimini denetlemek ve otomatik hale getirmek için kullanılabilir. Bu makalede, Azure PowerShell az modülünü kullanarak Azure HDInsight 'ta [Apache Hadoop](https://hadoop.apache.org/) kümelerini yönetmeyi öğreneceksiniz. HDInsight PowerShell cmdlet 'lerinin listesi için, [az. HDInsight başvurusuna](https://docs.microsoft.com/powershell/module/az.hdinsight)bakın.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* PowerShell [az Module](https://docs.microsoft.com/powershell/azure/overview) yüklendi.

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

## <a name="scale-clusters"></a>Kümeleri ölçeklendirme
Küme ölçekleme özelliği, kümeyi yeniden oluşturmaya gerek kalmadan Azure HDInsight 'ta çalışan bir küme tarafından kullanılan çalışan düğümlerinin sayısını değiştirmenize izin verir.

HDInsight tarafından desteklenen her küme türü için veri düğümlerinin sayısını değiştirmenin etkisi:

* Apache Hadoop

    Bekleyen veya çalışan bir işi etkilemeden, çalışan bir Hadoop kümesinde çalışan düğümlerinin sayısını sorunsuzca artırabilirsiniz. İşlem devam ederken yeni işler de gönderilebilir. Ölçeklendirme işlemindeki başarısızlık, kümenin her zaman işlevsel bir durumda bırakılması için düzgün şekilde işlenir.

    Bir Hadoop kümesi, veri düğümleri sayısını azaltarak aşağı ölçeklendirilirse, kümedeki hizmetlerin bazıları yeniden başlatılır. Hizmetleri yeniden başlatmak, tüm çalışan ve bekleyen işlerin ölçeklendirme işleminin tamamlanmasından sonra başarısız olmasına neden olur. Ancak, işlem tamamlandıktan sonra işleri yeniden gönderebilirsiniz.
* Apache HBase

    Çalışırken HBase kümenize düğümleri sorunsuzca ekleyebilir veya kaldırabilirsiniz. Bölgesel sunucular, ölçeklendirme işleminin tamamlanması birkaç dakika içinde otomatik olarak dengelenir. Ancak, kümenin baş düğümüne ' da oturum açarak bölgesel sunucuları el ile dengeleyebilir ve ardından bir komut istemi penceresinden aşağıdaki komutları çalıştırabilirsiniz:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    Veri düğümlerini, çalışırken fırtınası kümenize sorunsuzca ekleyebilir veya kaldırabilirsiniz. Ancak ölçeklendirme işleminin başarılı bir şekilde tamamlandıktan sonra topolojiyi yeniden dengelemeniz gerekir.

    Yeniden dengeleme iki şekilde sağlanabilir:

  * Fırtınası Web Kullanıcı arabirimi
  * Komut satırı arabirimi (CLı) aracı

    Daha fazla bilgi için [Apache Storm belgelerine](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) bakın.

    Fırtınası Web Kullanıcı arabirimi HDInsight kümesinde kullanılabilir:

    ![HDInsight fırtınası ölçeği yeniden dengeleme](./media/hdinsight-administer-use-powershell/portal-scale-cluster.png)

    Aşağıda, bir örneğin, bir fırtınası topolojisini yeniden dengelemek için CLı komutunun nasıl kullanılacağı gösterilmektedir:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Azure PowerShell kullanarak Hadoop kümesi boyutunu değiştirmek için bir istemci makinesinden aşağıdaki komutu çalıştırın:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Erişim verme/iptal etme
HDInsight kümeleri aşağıdaki HTTP Web hizmetlerine sahiptir (Bu hizmetlerin tümü, yeniden oluşan uç noktalara sahiptir):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Varsayılan olarak, bu hizmetler erişim için verilir. Erişimi iptal edebilir/atayabilirsiniz. İptal etmek için:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Vermek için:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> Erişimi vererek/iptal ederek, küme kullanıcı adını ve parolasını sıfırladınız.

Erişim verme ve iptal etme, Portal üzerinden de yapılabilir. Bkz. [HDInsight 'ta Apache Hadoop kümelerini Azure Portal kullanarak yönetme](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>HTTP Kullanıcı kimlik bilgilerini güncelleştirme
HTTP erişimini verme/iptal etme ile aynı yordamdır. Kümeye HTTP erişimi verildiyse, önce onu iptal etmeniz gerekir.  Ardından, yeni HTTP Kullanıcı kimlik bilgileriyle erişime izin verin.

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

Bkz. [HDInsight 'a veri yükleme](hdinsight-upload-data.md).

## <a name="see-also"></a>Ayrıca Bkz.

* [HDInsight cmdlet başvurusu belgeleri](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [HDInsight 'ta Apache Hadoop kümelerini Azure portal kullanarak yönetin](hdinsight-administer-use-portal-linux.md)
* [Bir komut satırı arabirimi kullanarak HDInsight 'ı yönetme](hdinsight-administer-use-command-line.md)
* [HDInsight kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Hadoop işleri programlı olarak gönderme](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Azure HDInsight 'ı kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md)
