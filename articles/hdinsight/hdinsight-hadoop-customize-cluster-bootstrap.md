---
title: Önyükleme kullanarak Azure HDInsight küme yapılandırmasını özelleştirme
description: .Net, PowerShell ve Kaynak Yöneticisi şablonlarını kullanarak HDInsight küme yapılandırmasını programlı bir şekilde özelleştirmeyi öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 15d08b14e38f097e8e9c3e0db893efb1d6efe44d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098666"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Önyükleme kullanarak HDInsight kümelerini özelleştirme

Önyükleme betikleri, Azure HDInsight 'taki bileşenleri programlı bir şekilde yüklemenize ve yapılandırmanıza olanak tanır.

HDInsight kümeniz oluşturulurken yapılandırma dosyası ayarlarını ayarlamak için üç yaklaşım vardır:

* Azure PowerShell kullanma
* .NET SDK kullanma
* Azure Resource Manager şablonu kullanma

Örneğin, bu programlama yöntemlerini kullanarak bu dosyalardaki seçenekleri yapılandırabilirsiniz:

* clusterIdentity.xml
* Core-site. xml
* Gateway. xml
* HBase-env. xml
* HBase-site. xml
* hdfs-site.xml
* Hive-env. xml
* Hive-site. xml
* mapred-site
* oozie-site.xml
* Oozie-env. xml
* storm-site.xml
* tez-site.xml
* webhcat-site. xml
* Yarn-site. xml
* Server. Properties (Kafka-Broker yapılandırması)

Oluşturma süresi boyunca HDInsight kümesine ek bileşenler yükleme hakkında bilgi için bkz. [betik eylemi kullanarak HDInsight kümelerini özelleştirme (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Önkoşullar

* PowerShell kullanıyorsanız, [az Module](https://docs.microsoft.com/powershell/azure/overview)gerekecektir.

## <a name="use-azure-powershell"></a>Azure PowerShell kullanma

Aşağıdaki PowerShell kodu bir [Apache Hive](https://hive.apache.org/) yapılandırmasını özelleştirir:

> [!IMPORTANT]  
> Parametrenin `Spark2Defaults` [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue)ile kullanılması gerekebilir. Aşağıdaki kod örneğinde gösterildiği gibi parametreye boş değerler geçirebilirsiniz.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Çalışan bir PowerShell betiği, [ek](#appendix-powershell-sample)içinde bulunabilir.

**Değişikliği doğrulamak için:**

1. [Azure portalı](https://portal.azure.com) üzerinde oturum açın.
2. Sol menüden **HDInsight kümeleri**' ne tıklayın. Bunu görmüyorsanız, önce **tüm hizmetler** ' e tıklayın.
3. PowerShell betiğini kullanarak yeni oluşturduğunuz kümeye tıklayın.
4. Dikey pencerenin üst kısmındaki **Pano** ' ya tıklayarak, ambarı Kullanıcı arabirimini açın.
5. Sol menüden **Hive** ' ye tıklayın.
6. Özet **HiveServer2** tıklayın.
7. **Configs** sekmesine tıklayın.
8. Sol menüden **Hive** ' ye tıklayın.
9. Tıklayın **Gelişmiş** sekmesi.
10. Aşağı kaydırın ve ardından **Gelişmiş Hive sitesi**' ni genişletin.
11. Bölümünde **Hive. metasarı. Client. Socket. Timeout** için arama yapın.

Diğer yapılandırma dosyalarını özelleştirmeye ilişkin bazı örnekler:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>.NET SDK kullanma
Bkz. [HDInsight 'ta .NET SDK kullanarak Linux tabanlı kümeler oluşturma](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Resource Manager şablonu kullanma
Kaynak Yöneticisi şablonunda önyükleme kullanabilirsiniz:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop, küme önyükleme Azure Resource Manager şablonunu özelleştirir](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Ayrıca bkz.

* [HDInsight 'ta Apache Hadoop kümeleri oluşturma][hdinsight-provision-cluster] diğer özel seçenekleri kullanarak HDInsight kümesi oluşturma hakkında yönergeler sağlar.
* [HDInsight için betik eylem betikleri geliştirme][hdinsight-write-script]
* [HDInsight kümelerinde Apache Spark yükleyip kullanma][hdinsight-install-spark]
* [HDInsight kümelerine Apache Giraph 'Yi yükleyip kullanın](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Küme oluşturma sırasında aşamalar"

## <a name="appendix-powershell-sample"></a>Yer PowerShell örneği

Bu PowerShell betiği bir HDInsight kümesi oluşturur ve bir Hive ayarını özelleştirir. `$nameToken` ,`$httpPassword`Ve değerlerini`$sshPassword`girdiğinizden emin olun.

> [!WARNING]  
> Depolama hesabı türü `BlobStorage` HDInsight kümeleri için kullanılamaz.

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>' 

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>' 
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
