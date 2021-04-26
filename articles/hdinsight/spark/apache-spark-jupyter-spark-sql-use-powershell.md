---
title: 'Hızlı başlangıç: Azure PowerShell ile Azure HDInsight üzerinde Apache Spark kümesi oluşturma'
description: Bu hızlı başlangıçta, Azure PowerShell kullanılarak nasıl Azure HDInsight’ta Apache Spark kümesi oluşturulacağı ve basit bir Spark SQL sorgusu çalıştırılacağı gösterilmektedir.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0d4a8df4cb6ba47b7772e2ab3b3247d36622dcb6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865970"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-powershell"></a>Hızlı başlangıç: PowerShell kullanarak Azure HDInsight 'ta Apache Spark kümesi oluşturma

Bu hızlı başlangıçta, Azure HDInsight 'ta bir Apache Spark kümesi oluşturmak için Azure PowerShell kullanırsınız. Daha sonra bir Jupyter Notebook oluşturup Apache Hive tablolarda Spark SQL sorguları çalıştırmak için kullanabilirsiniz. Azure HDInsight kuruluşlara yönelik, yönetilen, tam spektrumlu ve açık kaynaklı bir analiz hizmetidir. Azure HDInsight için Apache Spark Framework, bellek içi işleme kullanarak hızlı veri analizi ve küme bilgi işlem desteği sunar. Jupyter Notebook verilerinizle etkileşim kurmanıza, kodu markı metniyle birleştirmenize ve basit görselleştirmeler yapmanıza olanak sağlar.

[Genel bakış: Azure HDInsight](apache-spark-overview.md)  |  'ta Apache Spark [Apache Spark](https://spark.apache.org/)  |  [Apache Hive](https://hive.apache.org/)  |  [Jupyter Notebook](https://jupyter.org/)

Birden çok kümeyi birlikte kullanıyorsanız, bir sanal ağ oluşturmak isteyeceksiniz ve bir Spark kümesi kullanıyorsanız, Hive ambarı bağlayıcısını de kullanmak isteyeceksiniz. Daha fazla bilgi için bkz. [Azure HDInsight için bir sanal ağ planlayın](../hdinsight-plan-virtual-network-deployment.md) ve [Hive ambarı Bağlayıcısı ile Apache Spark ve Apache Hive tümleştirin](../interactive-query/apache-hive-warehouse-connector.md).

## <a name="prerequisite"></a>Önkoşul

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [PowerShell az Module](/powershell/azure/install-az-ps).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>HDInsight 'ta Apache Spark kümesi oluşturma

> [!IMPORTANT]  
> İster kullanın, ister kullanmayın, HDInsight kümeleri faturalaması dakika başına eşit olarak dağıtılmıştır. Kullanmayı bitirdikten sonra kümenizi sildiğinizden emin olun. Daha fazla bilgi için bu makalenin [Kaynakları temizleme](#clean-up-resources) bölümüne bakın.

HDInsight kümesi oluşturma işlemi, aşağıdaki Azure nesnelerinin ve kaynaklarının oluşturulmasını kapsar:

- Bir Azure kaynak grubu. Azure kaynak grubu, Azure kaynakları için bir kapsayıcıdır.
- Bir Azure depolama hesabı veya Azure Data Lake Storage.  Her HDInsight kümesi için bağımlı bir veri depolama alanı gerekir. Bu hızlı başlangıçta, küme depolama birimi olarak Azure depolama Blobları kullanan bir küme oluşturacaksınız. Data Lake Storage Gen2'yi kullanma hakkında daha fazla bilgi için bkz. [Hızlı başlangıç: HDInsight'ta kümeleri ayarlama](../hdinsight-hadoop-provision-linux-clusters.md).
- HDInsight üzerinde farklı küme türleri kümesi.  Bu hızlı başlangıçta bir Spark 2.3 kümesi oluşturursunuz.

Kaynakları oluşturmak için bir PowerShell betiği kullanırsınız. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell betiğini çalıştırdığınızda aşağıdaki değerleri girmeniz istenir:

|Parametre|Değer|
|------|------|
|Azure kaynak grubu adı | Kaynak grubu için benzersiz bir ad girin.|
|Konum| Azure bölgesini belirtin (örneğin, 'Orta ABD'). |
|Varsayılan depolama hesabı adı | Depolama hesabına benzersiz bir ad verin. |
|Küme adı | HDInsight kümesi için benzersiz bir ad sağlayın.|
|Küme oturum açma kimlik bilgileri | Hızlı başlangıcın ilerleyen kısmında küme panosuna bağlanmak için bu hesabı kullanırsınız.|
|SSH kullanıcı kimlik bilgileri | SSH istemcileri, HDInsight 'ta kümelerle bir uzak komut satırı oturumu oluşturmak için kullanılabilir.|

1. [Azure Cloud Shell](../../cloud-shell/overview.md)açmak için aşağıdaki kod bloğunun sağ üst köşesinde **deneyin** ' i seçin ve ardından Azure 'a bağlanmak için yönergeleri izleyin.

2. Aşağıdaki PowerShell betiğini kopyalayıp Cloud Shell yapıştırın.

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight

    # Default cluster size (# of worker nodes), version, and type
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"

    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure storage account and container
    # Note: Storage account kind BlobStorage can only be used as secondary storage for HDInsight clusters.
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

    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"

    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"

    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials" -UserName "sshuser"

    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName

    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName `
        -Context $defaultStorageContext

    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")

    # Create the cluster in HDInsight
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials

    Get-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName
    ```

   Kümenin oluşturulması yaklaşık 20 dakika sürer. Sonraki oturumuna devam etmeden önce küme oluşturulması gerekir.

HDInsight kümeleri oluşturma ile ilgili bir sorun yaşıyorsanız, bunu yapmak için doğru izinlere sahip değilsiniz. Daha fazla bilgi için bkz. [Erişim denetimi gereksinimleri](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook oluşturma

[Jupyter Notebook](https://jupyter.org/) , çeşitli programlama dillerini destekleyen etkileşimli bir not defteri ortamıdır. Not defteri, verilerle etkileşim kurmanıza, kodu markdown metniyle birleştirmenize ve basit görselleştirmeler gerçekleştirmenize olanak sağlar.

1. [Azure Portal](https://portal.azure.com), **HDInsight kümelerini** arayın ve seçin.
   
   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-search-hdinsight-cluster.png" alt-text="Ekran görüntüsünde, H D Insight Azure portal araması gösterilmektedir." border="true":::
   
1. Listeden oluşturduğunuz kümeyi seçin.
   
   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-open-hdinsight-cluster.png" alt-text="Ekran görüntüsü, oluşturduğunuz kümeyle H-Insight kümelerini gösterir." border="true":::
   
1. Küme **genel bakış** sayfasında, **küme panoları**' nı seçin ve ardından **Jupyter Notebook**' yi seçin. İstendiğinde, küme için küme oturum açma kimlik bilgilerini girin.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Etkileşimli Spark SQL sorgusu çalıştırmak için Jupyter Notebook açın" border="true":::

1.   >  Bir not defteri oluşturmak için yeni **pyspark** ' ı seçin.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Etkileşimli Spark SQL sorgusu çalıştırmak için Jupyter Notebook oluşturma" border="true":::

   Untitled(Untitled.pynb) adıyla yeni bir not defteri oluşturulur ve açılır.

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL deyimlerini Çalıştır

SQL (Yapılandırılmış Sorgu Dili), veri sorgulama ve tanımlama için en çok kullanılan dildir. Bilinen SQL söz dizimini kullanan Spark SQL, yapısal verileri işleyen bir Apache Spark uzantısı olarak çalışır.

1. Çekirdeğin hazır olduğunu doğrulayın. Not defterinde çekirdek adının yanında boş bir daire görmeniz, çekirdeğin hazır olduğu anlamına gelir. Dolu daire, çekirdeğin meşgul olduğunu belirtir.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png " alt-text="çekirdek durumu" border="true":::

    Not defterini ilk kez başlattığınızda, çekirdek arka planda birkaç görev gerçekleştirir. Çekirdeğin hazır olmasını bekleyin. 
1. Aşağıdaki kodu boş bir hücreye yapıştırın ve kodu çalıştırmak için **SHIFT + ENTER** tuşlarına basın. Komut, kümedeki Hive tablolarını listeler:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    HDInsight 'ta Spark kümeniz ile bir Jupyter Notebook kullandığınızda, `sqlContext` Spark SQL kullanarak Hive sorguları çalıştırmak için kullanabileceğiniz bir önayar alırsınız. `%%sql`, Hive sorgusunu çalıştırmak için Jupyter Not Defteri’ne `sqlContext` ön ayarını kullanmasını söyler. Sorgu, varsayılan olarak tüm HDInsight kümelerinde sağlanan Hive tablosundaki (**hivesampletable**) ilk 10 satırı getirir. Sonuçları almak 30 saniye kadar sürer. Çıktı şuna benzer:

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query.png " alt-text="HDInsight 'ta Spark 'ta sorgu Apache Hive" border="true":::

    Jupyter’de bir sorguyu her çalıştırdığınızda web tarayıcınızın pencere başlığında not defteri başlığı ile birlikte **(Meşgul)** durumu gösterilir. Ayrıca sağ üst köşedeki **PySpark** metninin yanında içi dolu bir daire görürsünüz.

1. `hivesampletable` komutundaki verileri görmek için başka bir sorgu çalıştırın.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Sorgu çıkışının görüntülenmesi için ekranın yenilenmesi gerekir.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query-output.png " alt-text="HDInsight 'ta Hive sorgusu çıkışı" border="true":::

1. Not defterindeki **Dosya** menüsünden **Kapat ve Durdur**’u seçin. Not defterini kapatmak, küme kaynaklarını serbest bırakır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

HDInsight, verilerinizi Azure Depolama’da veya Azure Data Lake Storage’da depolar, böylece kullanılmadığında bir kümeyi güvenle silebilirsiniz. Ayrıca, kullanılmıyorken dahi HDInsight kümesi için sizden ücret kesilir. Küme ücretleri depolama ücretlerinin birkaç katı olduğundan, kullanılmadığında kümelerin silinmesi mantıklı olandır. [Sonraki adımlar](#next-steps) içinde listelenen öğretici üzerinde hemen çalışmayı planlıyorsanız, kümeyi tutmak isteyebilirsiniz.

Azure portalına geri dönüp **Sil**’i seçin.

:::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-azure-portal-delete-cluster.png " alt-text="HDInsight kümesini silme Azure portal" border="true":::

Kaynak grubu adını seçerek de kaynak grubu sayfasını açabilir ve sonra **Kaynak grubunu sil**’i seçebilirsiniz. Kaynak grubunu silerek, hem HDInsight kümesini hem de varsayılan depolama hesabını silersiniz.

### <a name="piecemeal-clean-up-with-powershell-az-module"></a>PowerShell az Module ile öbek Temizleme

```powershell
# Removes the specified HDInsight cluster from the current subscription.
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

# Removes the specified storage container.
Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Removes a Storage account from Azure.
Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

# Removes a resource group.
Remove-AzResourceGroup `
    -Name $resourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, HDInsight 'ta Apache Spark kümesi oluşturmayı ve temel Spark SQL sorgusunu çalıştırmayı öğrendiniz. Örnek verilerde etkileşimli sorgular çalıştırmak için bir HDInsight kümesi kullanmayı öğrenmek üzere bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Apache Spark etkileşimli sorgular çalıştırma](./apache-spark-load-data-run-query.md)