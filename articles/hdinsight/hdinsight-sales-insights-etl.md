---
title: "Öğretici: Azure HDInsight 'ta Sales Insights 'ı türetmek için uçtan uca ETL işlem hattı oluşturma"
description: Spark isteğe bağlı kümelerini ve Power BI kullanarak satış verilerinden Öngörüler elde etmek için Azure HDInsight ile ETL işlem hatları oluşturma ' yı nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 140709a0ddb548c126ceca208fdeef2db77616bf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761808"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Öğretici: Azure HDInsight 'ta Sales Insights 'ı türetmek için uçtan uca veri işlem hattı oluşturma

Bu öğreticide, ayıklama, dönüştürme ve yükleme (ETL) işlemlerini gerçekleştiren bir uçtan uca veri işlem hattı oluşturacaksınız. İşlem hattı, verileri sorgulamak ve işlemek için Azure HDInsight üzerinde çalışan [Apache Spark](./spark/apache-spark-overview.md) ve Apache Hive kümelerini kullanır. Ayrıca, veri depolama için Azure Data Lake Storage 2. gibi teknolojileri ve görselleştirme için Power BI de kullanacaksınız.

Bu veri ardışık düzeni, çeşitli depolardaki verileri birleştirir, istenmeyen verileri kaldırır, yeni verileri ekler ve iş öngörülerini görselleştirmek için tüm bunları depolama verilerinize yükler. [Ayıklama, dönüştürme ve yükleme (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)içindeki ETL işlem hatları hakkında daha fazla bilgi edinin.

:::image type="content" source="./media/hdinsight-sales-insights-etl/architecture.png" alt-text="ETL mimarisi" border="false":::

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Azure CLı-en az sürüm 2.2.0. Bkz. [Azure CLI 'Yi yüklemeyi](/cli/azure/install-azure-cli).

* bir komut satırı JSON işlemcisi olan JQ.  Bkz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) ..

* [Azure yerleşik rol sahibinin](../role-based-access-control/built-in-roles.md)bir üyesi.

* Data Factory işlem hattını tetiklemek için PowerShell kullanıyorsanız [az Module](/powershell/azure/)gerekecektir.

* Bu öğreticinin sonunda iş öngörülerini görselleştirmek [Power BI Desktop](https://aka.ms/pbiSingleInstaller) .

## <a name="create-resources"></a>Kaynak oluşturma

### <a name="clone-the-repository-with-scripts-and-data"></a>Depoyu betikler ve verilerle kopyalayın

1. Azure aboneliğinizde oturum açın. Azure Cloud Shell kullanmayı planlıyorsanız, kod bloğunun sağ üst köşesinde bulunan **bunu dene** ' yi seçin. Aksi takdirde, aşağıdaki komutu girin:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Azure rolü [sahibinin](../role-based-access-control/built-in-roles.md)bir üyesi olduğunuzdan emin olun. `user@contoso.com`Hesabınızla değiştirin ve ardından şu komutu girin:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Hiçbir kayıt döndürülmezse, üye değilsiniz ve bu öğreticiyi tamamlayamayacağız.

1. Bu öğreticinin verilerini ve komut dosyalarını [HDInsight Sales ıNSIGHTS ETL deposundan](https://github.com/Azure-Samples/hdinsight-sales-insights-etl)indirin. Aşağıdaki komutu girin:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Oluşturulduğundan emin olun `salesdata scripts templates` . Aşağıdaki komutla doğrulayın:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>İşlem hattı için gereken Azure kaynaklarını dağıtma

1. Komut dosyaları için şunu girerek yürütme izinleri ekleyin:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Kaynak grubu için değişken ayarlayın. `RESOURCE_GROUP_NAME`Var olan veya yeni bir kaynak grubunun adıyla değiştirin, ardından şu komutu girin:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Betiği yürütün. `LOCATION`İstediğiniz değerle değiştirin, sonra şu komutu girin:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Hangi bölgeyi belirtlediğinizden emin değilseniz, [az Account List-Locations](/cli/azure/account#az_account_list_locations) komutuyla aboneliğiniz için desteklenen bölgelerin bir listesini alabilirsiniz.

    Komut aşağıdaki kaynakları dağıtır:

    * Azure Blob depolama hesabı. Bu hesap, şirket satışları verilerini tutar.
    * Azure Data Lake Storage 2. hesabı. Bu hesap, her iki HDInsight kümesi için de depolama hesabı olarak görev yapar. HDInsight ve [Azure hdınsight Data Lake Storage 2. Data Lake Storage 2. ile tümleştirme](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)hakkında daha fazla bilgi edinin.
    * Kullanıcı tarafından atanan yönetilen kimlik. Bu hesap, HDInsight kümelerinin Data Lake Storage 2. hesabına erişimini sağlar.
    * Apache Spark kümesi. Bu küme, ham verileri temizlemek ve dönüştürmek için kullanılacaktır.
    * Apache Hive [etkileşimli sorgu](./interactive-query/apache-interactive-query-get-started.md) kümesi. Bu küme, satış verilerinin sorgulanmasına ve Power BI ile görselleştirmeye olanak sağlayacak.
    * Ağ güvenlik grubu (NSG) kuralları tarafından desteklenen bir Azure sanal ağı. Bu sanal ağ, kümelerin iletişim kurmasına ve iletişimini güvenlik altına almasına izin verir.

Küme oluşturma 20 dakika sürebilir.

Kümelere SSH erişiminin varsayılan parolası `Thisisapassword1` . Parolayı değiştirmek istiyorsanız, `./templates/resourcesparameters_remainder.json` dosyaya gidin ve,,, `sparksshPassword` `sparkClusterLoginPassword` `llapClusterLoginPassword` ve `llapsshPassword` parametrelerinin parolasını değiştirin.

### <a name="verify-deployment-and-collect-resource-information"></a>Dağıtımı doğrulama ve kaynak bilgilerini toplama

1. Dağıtımınızın durumunu denetlemek isterseniz Azure portal kaynak grubuna gidin. **Ayarlar** altında **dağıtımlar**' ı, sonra dağıtımınızı seçin. Burada, başarıyla dağıtılan kaynakları ve devam eden kaynakları görebilirsiniz.

1. Kümelerin adlarını görüntülemek için aşağıdaki komutu girin:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Azure depolama hesabı ve erişim anahtarı 'nı görüntülemek için aşağıdaki komutu girin:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Data Lake Storage 2. hesabı ve erişim anahtarını görüntülemek için aşağıdaki komutu girin:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Azure Data Factory, Azure Pipelines otomatik hale getirmeye yardımcı olan bir araçtır. Bu görevleri gerçekleştirmenin tek yolu değildir, ancak işlemleri otomatik hale getirmek için harika bir yoldur. Azure Data Factory hakkında daha fazla bilgi için [Azure Data Factory belgelerine](https://azure.microsoft.com/services/data-factory/)bakın.

Bu veri fabrikasının iki etkinliği olan bir işlem hattı olacaktır:

* İlk etkinlik verileri Azure Blob depolama alanından Data Lake Storage Gen 2 depolama hesabına kopyalayarak veri alımını taklit eder.
* İkinci etkinlik Spark kümesindeki verileri dönüştürür. Betik, istenmeyen sütunları kaldırarak verileri dönüştürür. Ayrıca, tek bir işlemin ürettiği geliri hesaplayan yeni bir sütun ekler.

Azure Data Factory işlem hattınızı ayarlamak için aşağıdaki komutu yürütün.  Yine de `hdinsight-sales-insights-etl` dizininiz olmalıdır.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Bu betik aşağıdaki işlemleri yapar:

1. `Storage Blob Data Contributor`Data Lake Storage 2. depolama hesabı üzerinde izinlere sahip bir hizmet sorumlusu oluşturur.
1. [Data Lake Storage 2. dosya sistemine REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create)post isteklerini yetkilendirmek için bir kimlik doğrulama belirteci alır.
1. Data Lake Storage 2. depolama hesabınızın gerçek adını `sparktransform.py` ve `query.hql` dosyalarını doldurur.
1. Data Lake Storage 2. ve BLOB depolama hesapları için depolama anahtarlarını alır.
1. , İlişkili bağlı hizmetleri ve etkinlikleri ile bir Azure Data Factory işlem hattı oluşturmak için başka bir kaynak dağıtımı oluşturur. Bağlı hizmetlerin depolama hesaplarına doğru şekilde erişebilmeleri için depolama anahtarlarını şablon dosyasına parametreler olarak geçirir.

## <a name="run-the-data-pipeline"></a>Veri işlem hattını çalıştırma

### <a name="trigger-the-data-factory-activities"></a>Data Factory etkinliklerini tetikleyin

Oluşturduğunuz Data Factory işlem hattındaki ilk etkinlik, verileri blob depolamadan Data Lake Storage 2. kaydırır. İkinci etkinlik, verilere Spark dönüştürmelerini uygular ve dönüştürülen. csv dosyalarını yeni bir konuma kaydeder. Tüm işlem hattının tamamlanması birkaç dakika sürebilir.

Data Factory adını almak için aşağıdaki komutu girin:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

İşlem hattını tetiklemek için şunlardan birini yapabilirsiniz:

* PowerShell 'de Data Factory işlem hattını tetikleyin. `RESOURCEGROUP`Ve `DataFactoryName` değerlerini uygun değerlerle değiştirin ve ardından aşağıdaki komutları çalıştırın:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    `Get-AzDataFactoryV2PipelineRun`İlerlemeyi izlemek için gerektiğinde yeniden yürütün.

    Veya

* Data Factory 'yi açın ve **yazar & İzleyicisi**' ni seçin. `IngestAndTransform`Komut zinciri portalından tetikleyin. Portal aracılığıyla işlem hatlarını tetikleme hakkında daha fazla bilgi için, bkz. [HDInsight 'ta isteğe bağlı Apache Hadoop kümelerini Azure Data Factory kullanarak oluşturma](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

İşlem hattının çalıştırıldığını doğrulamak için aşağıdaki adımlardan birini alabilirsiniz:

* Portal aracılığıyla veri fabrikanızın **izleyici** bölümüne gidin.
* Azure Depolama Gezgini, Data Lake Storage Gen 2 depolama hesabınıza gidin. `files`Dosya sistemine gidin ve sonra işlem `transformed` hattının başarılı olup olmadığını görmek için klasöre gidin ve içeriğini denetleyin.

HDInsight kullanarak verileri dönüştürmenin diğer yolları için [Jupyter Notebook kullanma konusunda bu makaleye](/azure/hdinsight/spark/apache-spark-load-data-run-query)bakın.

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Power BI verileri görüntülemek için etkileşimli sorgu kümesinde tablo oluşturma

1. `query.hql`SCP 'yi kullanarak dosyayı LLAP kümesine kopyalayın. Şu komutu girin:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Anımsatıcı: varsayılan parola `Thisisapassword1` .

1. LLAP kümesine erişmek için SSH kullanın. Şu komutu girin:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Betiği çalıştırmak için aşağıdaki komutu kullanın:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Bu betik, etkileşimli sorgu kümesinde Power BI erişebileceğiniz bir yönetilen tablo oluşturur.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Satış verilerinden Power BI panosu oluşturma

1. Power BI Desktop'ı açın.

1. Menüden **verileri**  >  **daha fazla al...**  >  ' a gidin. **Azure**  >  **HDInsight etkileşimli sorgusu**.

1. **Bağlan**’ı seçin.

1. **HDInsight etkileşimli sorgu** iletişim kutusundan:
    1. **Sunucu** metin kutusunda, LLAP kümenizin adını biçiminde girin `https://LLAPCLUSTERNAME.azurehdinsight.net` .
    1. **Veritabanı** metin kutusuna yazın `default` .
    1. **Tamam**’ı seçin.

1. **AzureHive** iletişim kutusundan:
    1. **Kullanıcı adı** metin kutusuna girin `admin` .
    1. **Parola** metin kutusuna girin `Thisisapassword1` .
    1. **Bağlan**’ı seçin.

1. **Gezgin**'den `sales` verileri önizlemek için ve/veya öğesini seçin `sales_raw` . Veriler yüklendikten sonra, oluşturmak istediğiniz panoyu deneyebilirsiniz. Power BI panoları kullanmaya başlamak için aşağıdaki bağlantılara bakın:

* [Power BI tasarımcıları için panolara giriş](https://docs.microsoft.com/power-bi/service-dashboards)
* [Öğretici: Power BI hizmeti ile çalışmaya başlama](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekecekseniz, bunlar için ücretlendirilmemek üzere aşağıdaki komutu kullanarak tüm kaynakları silin.

1. Kaynak grubunu kaldırmak için şu komutu girin:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Hizmet sorumlusunu kaldırmak için şu komutları girin:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Ölçeklendirerek ayıklama, dönüştürme ve yükleme (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)
