---
title: "Öğretici: Sales Insights 'ı türetmek için uçtan uca ETL işlem hattı oluşturma"
description: Spark isteğe bağlı kümelerini ve Power BI kullanarak satış verilerinden Öngörüler elde etmek için Azure HDInsight ile ETL işlem hatları oluşturma ' yı nasıl kullanacağınızı öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: d662ad59722658ed888aa732c1f45afdf48f850c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889201"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Öğretici: Sales Insights 'ı türetmek için uçtan uca veri işlem hattı oluşturma

Bu öğreticide, ayıklama, dönüştürme ve yükleme (ETL) işlemlerini gerçekleştiren bir uçtan uca veri işlem hattı oluşturacaksınız. İşlem hattı, verileri sorgulamak ve işlemek için Azure HDInsight üzerinde çalışan Apache Spark ve Apache Hive kümelerini kullanır. Ayrıca, veri depolama için Azure Data Lake Storage 2. gibi teknolojileri ve görselleştirme için Power BI de kullanacaksınız.

Bu veri ardışık düzeni, çeşitli depolardaki verileri birleştirir, istenmeyen verileri kaldırır, yeni verileri ekler ve iş öngörülerini görselleştirmek için tüm bunları depolama verilerinize yükler. [Ayıklama, dönüştürme ve yükleme (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)içindeki ETL işlem hatları hakkında daha fazla bilgi edinin.

![ETL mimarisi](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

Bu öğreticinin sonunda iş öngörülerini görselleştirmek için [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) indirin.

## <a name="create-resources"></a>Kaynak oluşturma

### <a name="clone-the-repository-with-scripts-and-data"></a>Depoyu betikler ve verilerle kopyalayın

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üstteki menü çubuğundan Azure Cloud Shell açın. Cloud Shell isteyip istemediğinizi bir dosya paylaşma oluşturmak için aboneliğinizi seçin.

   ![Azure Cloud Shell’i açma](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. **Ortam Seç** açılan menüsünde, **Bash**' i seçin.
1. Azure hesabınızda oturum açın ve aboneliği ayarlayın. 
1. Proje için kaynak grubunu ayarlayın.
   1. Kaynak grubu için benzersiz bir ad seçin.
   1. Sonraki adımlarda kullanılacak değişkenleri ayarlamak için Cloud Shell aşağıdaki kod parçacığını çalıştırın:

       ```azurecli-interactive 
       resourceGroup="<RESOURCE GROUP NAME>"
       subscriptionID="<SUBSCRIPTION ID>"
        
       az account set --subscription $subscriptionID
       az group create --name $resourceGroup --location westus
       ```

1. Cloud Shell ' ye aşağıdaki komutları girerek [HDInsight Sales ıNSIGHTS ETL deposundan](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) Bu öğreticinin veri ve komut dosyalarını indirin:

    ```azurecli-interactive 
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Aşağıdaki dosya ve dizinlerin oluşturulduğunu görmek için Kabuk isteminde `ls` girin:

   ```output
   /salesdata/
   /scripts/
   /templates/
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>İşlem hattı için gereken Azure kaynaklarını dağıtma 

1. `chmod +x scripts/*.sh` betiği için yürütme izinleri ekleyin.
1. Aşağıdaki kaynakları Azure 'da dağıtmak üzere betiği çalıştırmak için komut `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` kullanın:

   1. Azure Blob depolama hesabı. Bu hesap, şirket satışları verilerini tutar.
   2. Azure Data Lake Storage 2. hesabı. Bu hesap, her iki HDInsight kümesi için de depolama hesabı olarak görev yapar. HDInsight ve [Azure hdınsight Data Lake Storage 2. Data Lake Storage 2. ile tümleştirme](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)hakkında daha fazla bilgi edinin.
   3. Kullanıcı tarafından atanan yönetilen kimlik. Bu hesap, HDInsight kümelerinin Data Lake Storage 2. hesabına erişimini sağlar.
   4. Apache Spark kümesi. Bu küme, ham verileri temizlemek ve dönüştürmek için kullanılacaktır.
   5. Apache Hive etkileşimli sorgu kümesi. Bu küme, satış verilerinin sorgulanmasına ve Power BI ile görselleştirmeye olanak sağlayacak.
   6. Ağ güvenlik grubu (NSG) kuralları tarafından desteklenen bir Azure sanal ağı. Bu sanal ağ, kümelerin iletişim kurmasına ve iletişimini güvenlik altına almasına izin verir. 

Küme oluşturma 20 dakika sürebilir.

`resources.sh` betiği aşağıdaki komutu içerir. Bu komut, istenen yapılandırmayla belirtilen kaynakları oluşturmak için bir Azure Resource Manager şablonu (`resourcestemplate.json`) kullanır.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

`resources.sh` betiği, Sales Data. csv dosyalarını bu komutu kullanarak yeni oluşturulan BLOB depolama hesabına de yükler:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

Kümelere SSH erişiminin varsayılan parolası `Thisisapassword1`. Parolayı değiştirmek istiyorsanız, `resourcesparameters.json` dosyasına gidin ve `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword`ve `llapsshPassword` parametreleri için parolayı değiştirin.

### <a name="verify-deployment-and-collect-resource-information"></a>Dağıtımı doğrulama ve kaynak bilgilerini toplama

1. Dağıtımınızın durumunu denetlemek isterseniz Azure portal kaynak grubuna gidin. **Ayarlar**altında **dağıtımlar** ' ı seçin. Dağıtımınızın adını seçin, `ResourcesDeployment`. Burada, başarıyla dağıtılan kaynakları ve devam eden kaynakları görebilirsiniz.
1. Dağıtım bittikten sonra, Azure portal > **kaynak gruplarına** gidin > < RESOURCE_GROUP_NAME >.
1. Satış dosyalarını depolamak için oluşturulan yeni Azure Depolama hesabını bulun. Depolama hesabının adı `blob` ile başlar ve sonra rastgele bir dize içerir. Şunları yapın:
   1. Daha sonra kullanmak üzere depolama hesabı adını bir yere göz önüne alın.
   1. BLOB depolama hesabının adını seçin.
   1. Portalın sol tarafındaki **Ayarlar**altında **erişim anahtarları**' nı seçin.
   1. **KEY1** kutusuna dizeyi kopyalayın ve daha sonra kullanmak üzere kaydedin.
1. HDInsight kümeleri için depolama olarak oluşturulan Data Lake Storage 2. hesabını bulun. Bu hesap, BLOB depolama hesabıyla aynı kaynak grubunda bulunur, ancak `adlsgen2`ile başlar. Şunları yapın:
   1. Data Lake Storage 2. hesabının adını bir yere göz önüne alın.
   1. Data Lake Storage 2. hesabının adını seçin.
   1. Portalın sol tarafındaki **Ayarlar**altında **erişim anahtarları**' nı seçin.
   1. **KEY1** kutusuna dizeyi kopyalayın ve daha sonra kullanmak üzere kaydedin.

> [!Note]
> Depolama hesaplarının adlarını öğrendikten sonra, Azure Cloud Shell isteminde aşağıdaki komutu kullanarak hesap anahtarlarını alabilirsiniz:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Azure Data Factory, Azure işlem hatlarını otomatik hale getirmeye yardımcı olan bir araçtır. Bu görevleri gerçekleştirmenin tek yolu değildir, ancak işlemleri otomatik hale getirmek için harika bir yoldur. Azure Data Factory hakkında daha fazla bilgi için [Azure Data Factory belgelerine](https://azure.microsoft.com/services/data-factory/)bakın. 

Bu veri fabrikasının iki etkinliği olan bir işlem hattı olacaktır: 

- İlk etkinlik verileri Azure Blob depolama alanından Data Lake Storage Gen 2 depolama hesabına kopyalayarak veri alımını taklit eder.
- İkinci etkinlik Spark kümesindeki verileri dönüştürür. Betik, istenmeyen sütunları kaldırarak verileri dönüştürür. Ayrıca, tek bir işlemin ürettiği geliri hesaplayan yeni bir sütun ekler.

Azure Data Factory işlem hattınızı ayarlamak için `adf.sh` betiğini çalıştırın:

1. Dosya üzerinde yürütme izinleri eklemek için `chmod +x adf.sh` kullanın.
1. Betiği çalıştırmak için `./adf.sh` kullanın. 

Bu betik aşağıdaki işlemleri yapar:

1. Data Lake Storage 2. depolama hesabında `Storage Blob Data Contributor` izinlerle bir hizmet sorumlusu oluşturur.
1. [Data Lake Storage 2. dosya sistemine REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create)post isteklerini yetkilendirmek için bir kimlik doğrulama belirteci alır.
1. `sparktransform.py` ve `query.hql` dosyalarındaki Data Lake Storage 2. depolama hesabınızın gerçek adını doldurur.
1. Data Lake Storage 2. ve BLOB depolama hesapları için depolama anahtarlarını alır.
1. , İlişkili bağlı hizmetleri ve etkinlikleri ile bir Azure Data Factory işlem hattı oluşturmak için başka bir kaynak dağıtımı oluşturur. Bağlı hizmetlerin depolama hesaplarına doğru şekilde erişebilmeleri için depolama anahtarlarını şablon dosyasına parametreler olarak geçirir.

Data Factory işlem hattı aşağıdaki komutla dağıtılır:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Veri işlem hattını çalıştırma

### <a name="trigger-the-data-factory-activities"></a>Data Factory etkinliklerini tetikleyin

Oluşturduğunuz Data Factory işlem hattındaki ilk etkinlik, verileri blob depolamadan Data Lake Storage 2. kaydırır. İkinci etkinlik, verilere Spark dönüştürmelerini uygular ve dönüştürülen. csv dosyalarını yeni bir konuma kaydeder. Tüm işlem hattının tamamlanması birkaç dakika sürebilir.

İşlem hatlarını tetiklemek için şunlardan birini yapabilirsiniz:

- PowerShell 'de Data Factory işlem hatlarını tetiklemek için aşağıdaki komutları çalıştırın: 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

- Data Factory 'yi açın ve **yazar & İzleyicisi**' ni seçin. Kopyalama işlem hattını ve sonra portaldan Spark işlem hattını tetikleyin. Portal aracılığıyla işlem hatlarını tetikleme hakkında daha fazla bilgi için, bkz. [HDInsight 'ta isteğe bağlı Apache Hadoop kümelerini Azure Data Factory kullanarak oluşturma](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

İşlem hatları çalıştırmanın çalıştığını doğrulamak için aşağıdaki adımlardan birini alabilirsiniz:

- Portal aracılığıyla veri fabrikanızın **izleyici** bölümüne gidin.
- Azure Depolama Gezgini, Data Lake Storage Gen 2 depolama hesabınıza gidin. `files` dosya sistemine gidin ve sonra işlem hattının başarılı olup olmadığını görmek için `transformed` klasörüne gidin ve içeriğini denetleyin.

HDInsight kullanarak verileri dönüştürmenin diğer yolları için [Jupyter Notebook kullanma konusunda bu makaleye](/azure/hdinsight/spark/apache-spark-load-data-run-query)bakın.

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Power BI verileri görüntülemek için etkileşimli sorgu kümesinde tablo oluşturma

1. SCP kullanarak `query.hql` dosyasını LLAP kümesine kopyalayın:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Aşağıdaki komutu kullanarak LLAP kümesine erişmek için SSH kullanın ve parolanızı girin. `resourcesparameters.json` dosyasını değiştirmediyse parola `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Betiği çalıştırmak için aşağıdaki komutu kullanın:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Bu betik, etkileşimli sorgu kümesinde Power BI erişebileceğiniz bir yönetilen tablo oluşturur. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Satış verilerinden Power BI panosu oluşturma

1. Power BI Desktop’ı açın.
1. **Veri Al**’ı seçin.
1. **HDInsight etkileşimli sorgu kümesi**araması yapın.
1. Kümenizin URI 'sini buraya yapıştırın. `https://<LLAP CLUSTER NAME>.azurehdinsight.net` biçiminde olmalıdır.

   Veritabanı için `default` girin.
1. Kümeye erişmek için kullandığınız kullanıcı adını ve parolayı girin.

Veriler yüklendikten sonra, oluşturmak istediğiniz panoyu deneyebilirsiniz. Power BI panoları kullanmaya başlamak için aşağıdaki bağlantılara bakın:

* [Power BI tasarımcıları için panolara giriş](https://docs.microsoft.com/power-bi/service-dashboards)
* [Öğretici: Power BI hizmeti kullanmaya başlayın](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekecekseniz, bunlar için ücretlendirilmemek üzere aşağıdaki komutu kullanarak tüm kaynakları silin.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Ölçeklendirerek ayıklama, dönüştürme ve yükleme (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)
