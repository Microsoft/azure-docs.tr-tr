---
title: Azure Data Factory Spark programlarını çağır
description: MapReduce etkinliğini kullanarak bir Azure Data Factory 'den Spark programlarını çağırmayı öğrenin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: d5f5da4811a9551f687fed6ab317bb3d33041622
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666170"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Azure Data Factory işlem hatlarından Spark programları çağırma

> [!div class="op_single_selector" title1="Dönüştürme etkinlikleri"]
> * [Hive etkinliği](data-factory-hive-activity.md)
> * [Pig etkinliği](data-factory-pig-activity.md)
> * [MapReduce etkinliği](data-factory-map-reduce.md)
> * [Hadoop akışı etkinliği](data-factory-hadoop-streaming-activity.md)
> * [Spark etkinliği](data-factory-spark.md)
> * [Machine Learning Batch yürütme etkinliği](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning kaynak etkinliğini Güncelleştir](data-factory-azure-ml-update-resource-activity.md)
> * [Saklı yordam etkinliği](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL etkinliği](data-factory-usql-activity.md)
> * [.NET özel etkinliği](data-factory-use-custom-activities.md)

> [!NOTE]
> Bu makale, Azure Data Factory’nin genel kullanıma açık olan 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory Apache Spark etkinliğini kullanarak verileri dönüştürme](../transform-data-using-spark.md).

## <a name="introduction"></a>Giriş
Spark etkinliği Data Factory tarafından desteklenen [veri dönüştürme etkinliklerinden](data-factory-data-transformation-activities.md) biridir. Bu etkinlik, Azure HDInsight 'ta Spark kümenizde belirtilen Spark programını çalıştırır. 

> [!IMPORTANT]
> - Spark etkinliği, birincil depolama olarak Azure Data Lake Store kullanan HDInsight Spark kümelerini desteklemez.
> - Spark etkinliği yalnızca var olan HDInsight Spark kümelerinizi destekler. İsteğe bağlı HDInsight bağlı hizmetini desteklemez.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>İzlenecek yol: Spark etkinliği ile işlem hattı oluşturma
Spark etkinliğiyle Veri Fabrikası işlem hattı oluşturmak için tipik adımlar aşağıda verilmiştir: 

* Veri fabrikası oluşturma.
* HDInsight Spark kümeniz ile ilişkilendirilen depolama alanınızı Data Factory 'ye bağlamak için bir Azure depolama bağlı hizmeti oluşturun.
* HDInsight 'ta Spark kümenizi veri fabrikasına bağlamak için bir HDInsight bağlı hizmeti oluşturun.
* Depolama bağlı hizmetine başvuran bir veri kümesi oluşturun. Şu anda, üretilmekte olan bir çıktı olmasa bile bir etkinlik için çıkış veri kümesi belirtmeniz gerekir. 
* Oluşturduğunuz HDInsight bağlı hizmetine başvuran Spark etkinliğine sahip bir işlem hattı oluşturun. Etkinlik, önceki adımda oluşturduğunuz veri kümesiyle çıkış veri kümesi olarak yapılandırılır. Çıktı veri kümesi, zamanlamayı (saatlik, günlük) değil. Bu nedenle, etkinlik gerçekten bir çıkış üretmese de çıkış veri kümesini belirtmeniz gerekir.

### <a name="prerequisites"></a>Ön koşullar
1. [Depolama hesabı oluşturma](../../storage/common/storage-quickstart-create-account.md)bölümündeki yönergeleri izleyerek genel amaçlı bir depolama hesabı oluşturun.

1. HDInsight 'ta [Spark kümesi oluşturma](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)öğreticisindeki talimatları izleyerek HDInsight 'ta Spark kümesi oluşturma. Adım 1 ' de oluşturduğunuz depolama hesabını bu kümeyle ilişkilendirin.

1. [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py)konumunda bulunan **test.py** Python betik dosyasını indirin ve gözden geçirin.

1. BLOB depolama alanındaki **adfspark** kapsayıcısında **test.py** **Dosya** klasörüne yükleyin. Kapsayıcısını ve klasörü yoksa, oluşturun.

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
Veri fabrikası oluşturmak için bu adımları izleyin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. **Yeni** > **Veri ve Analiz** > **Data Factory**’yi seçin.

1. **Yeni Veri Fabrikası** dikey penceresinde, **ad**' ın altında, **mini-mini**' i girin.

   > [!IMPORTANT]
   > Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. "Data Factory adı, Mini \ df yok" hatasını görürseniz veri fabrikasının adını değiştirin. Örneğin, yournameSparkDFdate kullanın ve Data Factory 'yi yeniden oluşturun. Adlandırma kuralları hakkında daha fazla bilgi için bkz. [Data Factory: Adlandırma kuralları](data-factory-naming-rules.md).

1. Abonelik bölümünde, veri fabrikasını oluşturmak istediğiniz **Azure aboneliğini** seçin.

1. Var olan bir kaynak grubunu seçin veya bir Azure Kaynak grubu oluşturun.

1. **Panoya sabitle** onay kutusunu seçin.

1. **Oluştur**'u seçin.

   > [!IMPORTANT]
   > Data Factory örnekleri oluşturmak için abonelik/kaynak grubu düzeyinde [Data Factory katılımcısı](../../role-based-access-control/built-in-roles.md#data-factory-contributor) rolünün üyesi olmanız gerekir.

1. Veri fabrikasını Azure portal panosunda oluşturulduğu şekilde görürsünüz.

1. Veri fabrikası oluşturulduktan sonra veri fabrikasının içeriğinin gösterildiği **Veri fabrikası** sayfasını görürsünüz. **Data Factory** sayfasını görmüyorsanız, panoda veri fabrikanızın kutucuğunu seçin.

    ![Data Factory dikey penceresi](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Bu adımda, iki bağlı hizmet oluşturursunuz. Bir hizmet, Spark kümenizi veri fabrikanıza bağlar ve diğer hizmet, depolama alanınızı veri fabrikasına bağlar. 

#### <a name="create-a-storage-linked-service"></a>Depolama bağlı hizmeti oluşturma
Bu adımda, depolama hesabınızı veri fabrikanıza bağlarsınız. Bu izlenecek yolda daha sonra oluşturduğunuz bir veri kümesi, bu bağlı hizmeti ifade eder. Sonraki adımda tanımladığınız HDInsight bağlı hizmeti bu bağlı hizmete de başvurur. 

1. **Data Factory** dikey penceresinde **Yazar ve dağıt**' ı seçin. Data Factory Düzenleyicisi görüntülenir.

1. **Yeni veri deposu**’na tıklayın ve **Azure Depolama**’yı seçin.

   ![Yeni veri deposu](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. Depolama bağlı hizmeti oluşturmak için kullandığınız JSON betiği düzenleyicide görüntülenir.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. **Hesap adı** ve **hesap anahtarı** ' nı depolama hesabınızın adı ve erişim anahtarıyla değiştirin. Depolama erişim anahtarınızı nasıl alabileceğinizi öğrenmek için [Depolama hesabınızı yönetme](../../storage/common/storage-account-manage.md#access-keys) sayfasındaki depolama erişim anahtarlarını görüntüleme, kopyalama ve yeniden oluşturma yönergelerine bakın.

1. Bağlı hizmeti dağıtmak için komut çubuğunda **Dağıt** ' ı seçin. Bağlı hizmet başarıyla dağıtıldıktan sonra Draft-1 penceresi kaybolur. Soldaki ağaç görünümünde **AzureStorageLinkedService** öğesini görürsünüz.

#### <a name="create-an-hdinsight-linked-service"></a>HDInsight bağlı hizmeti oluşturma
Bu adımda, HDInsight Spark kümenizi Data Factory 'ye bağlamak için bir HDInsight bağlı hizmeti oluşturursunuz. HDInsight kümesi, bu örnekteki işlem hattının Spark etkinliğinde belirtilen Spark programını çalıştırmak için kullanılır. 

1. Data Factory düzenleyicisinde **daha fazla** > **Yeni işlem** > **HDInsight kümesi**seçin.

    ![HDInsight bağlı hizmeti oluşturma](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Aşağıdaki kod parçacığını kopyalayıp Taslak-1 penceresine yapıştırın. JSON düzenleyicisinde, aşağıdaki adımları uygulayın:

    a. HDInsight Spark kümesi için URI belirtin. Örneğin: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Spark kümesine erişimi olan kullanıcının adını belirtin.

    c. Kullanıcı için parola belirtin.

    d. HDInsight Spark kümesiyle ilişkili depolama bağlı hizmetini belirtin. Bu örnekte, AzureStorageLinkedService.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Spark etkinliği, birincil depolama olarak Azure Data Lake Store kullanan HDInsight Spark kümelerini desteklemez.
    > - Spark etkinliği yalnızca var olan HDInsight Spark kümelerinizi destekler. İsteğe bağlı HDInsight bağlı hizmetini desteklemez.

    HDInsight bağlı hizmeti hakkında daha fazla bilgi için bkz. [HDInsight bağlı hizmeti](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Bağlı hizmeti dağıtmak için komut çubuğunda **Dağıt** ' ı seçin. 

### <a name="create-the-output-dataset"></a>Çıktı veri kümesini oluşturma
Çıktı veri kümesi, zamanlamayı (saatlik, günlük) değil. Bu nedenle, etkinlik herhangi bir çıkış üretmese de, işlem hattındaki Spark etkinliğinin çıkış veri kümesini belirtmeniz gerekir. Etkinlik için bir giriş veri kümesi belirtilmesi isteğe bağlıdır.

1. Data Factory Düzenleyicisi’nde **Diğer** > **Yeni veri kümesi** > **Azure Blob depolama**’yı seçin.

1. Aşağıdaki kod parçacığını kopyalayıp Taslak-1 penceresine yapıştırın. JSON kod parçacığı, **outputdataset**adlı bir veri kümesini tanımlar. Ayrıca, sonuçların **adfspark** adlı blob kapsayıcısında ve **pyfiles/output**adlı klasörde depolandığını belirtirsiniz. Daha önce belirtildiği gibi, bu veri kümesi bir kukla veri kümesidir. Bu örnekteki Spark programı herhangi bir çıktı oluşturmaz. **Kullanılabilirlik** bölümü, çıktı veri kümesinin günlük olarak üretildiğini belirtir. 

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. Veri kümesini dağıtmak için komut çubuğunda **Dağıt** ' ı seçin.


### <a name="create-a-pipeline"></a>İşlem hattı oluşturma
Bu adımda, bir HDInsightSpark etkinliği ile işlem hattı oluşturacaksınız. Şu anda, zamanlama çıktı veri kümesi tarafından yönetildiğinden, etkinlik hiçbir çıktı oluşturmasa bile bir çıktı veri kümesi oluşturmanız gerekir. Etkinlik herhangi bir girdi almazsa, girdi veri kümesi oluşturma işlemini atlayabilirsiniz. Bu nedenle, bu örnekte hiçbir giriş veri kümesi belirtilmedi.

1. Data Factory Düzenleyicisi’nde **Diğer** > **Yeni işlem hattı**’nı seçin.

1. Taslak-1 penceresindeki betiği aşağıdaki komut dosyasıyla değiştirin:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    Aşağıdaki noktalara dikkat edin:

    a. **Type** özelliği **HDInsightSpark**olarak ayarlanır.

    b. **RootPath** özelliği **adfspark\\pyfiles** olarak ayarlanır ve adfspark, blob kapsayıcısı ve pyfiles ise bu kapsayıcıda dosya klasörüdür. Bu örnekte, BLOB depolama, Spark kümesiyle ilişkili olan bir örnektir. Dosyayı farklı bir depolama hesabına yükleyebilirsiniz. Bunu yaparsanız, bu depolama hesabını veri fabrikasına bağlamak için bir depolama bağlı hizmeti oluşturun. Daha sonra, bağlı hizmetin adını, **mini Iş linkedservice** özelliği için bir değer olarak belirtin. Bu özellik ve Spark etkinliğinin desteklediği diğer özellikler hakkında daha fazla bilgi için bkz. [Spark etkinlik özellikleri](#spark-activity-properties).

    c. **Entryfilepath** özelliği, Python dosyası olan **test.py**olarak ayarlanır.

    d. **GetDebugInfo** özelliği **her zaman**olarak ayarlanır; Bu, günlük dosyalarının her zaman oluşturulduğu anlamına gelir (başarılı veya başarısız).

    > [!IMPORTANT]
    > Bir sorun gidermediğiniz takdirde bu özelliği üretim ortamında `Always` olarak ayarlamanıza önerilir.

    e. **Çıktılar** bölümünde bir çıkış veri kümesi vardır. Spark programı herhangi bir çıkış üretmese bile bir çıkış veri kümesi belirtmeniz gerekir. Çıktı veri kümesi, işlem hattı için zamanlamayı (saatlik, günlük) yürütür. 

    Spark etkinliğinin desteklediği özellikler hakkında daha fazla bilgi için, [Spark etkinlik özellikleri](#spark-activity-properties)bölümüne bakın.

1. İşlem hattını dağıtmak için komut çubuğunda **Dağıt** ' ı seçin.

### <a name="monitor-a-pipeline"></a>İşlem hattını izleme
1. İzleme uygulamasını başka bir sekmede başlatmak için **Veri Fabrikası** dikey penceresinde **izleyici & Yönet** ' i seçin.

    ![İzleme ve Yönetme kutucuğu](media/data-factory-spark/monitor-and-manage-tile.png)

1. En üstteki **Başlangıç saati** filtresini **2/1/2017**olarak değiştirin ve **Uygula**' yı seçin.

1. İşlem hattının başlangıç (2017-02-01) ve bitiş zamanları (2017-02-02) arasında yalnızca bir gün olduğu için yalnızca bir etkinlik penceresi görüntülenir. Veri diliminin, **Ready** durumunda olduğunu doğrulayın.

    ![İşlem hattını izleme](media/data-factory-spark/monitor-and-manage-app.png)

1. **Etkinlik pencereleri** listesinde, ayrıntılarını görmek için bir etkinlik çalıştırması seçin. Bir hata varsa sağ bölmede onunla ilgili ayrıntıları görürsünüz.

### <a name="verify-the-results"></a>Sonuçları doğrulama

1. [Bu Web sitesine](https://CLUSTERNAME.azurehdinsight.net/jupyter)giderek HDInsight Spark kümeniz için Jupyter Notebook başlatın. Ayrıca, HDInsight Spark kümeniz için bir küme panosu açıp Jupyter Notebook başlatabilirsiniz.

1. Yeni bir not defteri başlatmak için **yeni** > **pyspark** ' ı seçin.

    ![Jupyter yeni not defteri](media/data-factory-spark/jupyter-new-book.png)

1. Metni kopyalayıp yapıştırarak ve ikinci deyimin sonundaki SHIFT + enter tuşlarına basarak aşağıdaki komutu çalıştırın:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. HVAC tablosundan verileri görtığınızdan emin olun. 

    ![Jupyıter sorgu sonuçları](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Ayrıntılı yönergeler için [Spark SQL sorgusu çalıştırma](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)bölümüne bakın. 

### <a name="troubleshooting"></a>Sorun giderme
GetDebugInfo 'Yu **her zaman**ayarladığınız için, blob kabınızda pyfiles klasöründe bir günlük alt klasörü görürsünüz. Günlük klasöründeki günlük dosyası ek bilgi sağlar. Bu günlük dosyası özellikle bir hata olduğunda yararlıdır. Bir üretim ortamında, bunu **hata**olarak ayarlamak isteyebilirsiniz.

Daha fazla sorun giderme için aşağıdaki adımları uygulayın:


1. `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster` kısmına gidin.

    ![YARN UI uygulaması](media/data-factory-spark/yarnui-application.png)

1. Çalıştırma denemelerinde bir **günlük** seçin.

    ![Uygulama sayfası](media/data-factory-spark/yarn-applications.png)

1. Günlük sayfasında aşağıdaki ek hata bilgilerini görürsünüz:

    ![Günlük hatası](media/data-factory-spark/yarnui-application-error.png)

Aşağıdaki bölümler, Data Factory 'de Spark kümesi ve Spark etkinliğini kullanmak için Data Factory varlıkları hakkında bilgi sağlar.

## <a name="spark-activity-properties"></a>Spark etkinlik özellikleri
İşte bir iş hattının örnek JSON tanımı, Spark etkinliğine sahiptir: 

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    },
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

Aşağıdaki tabloda JSON tanımında kullanılan JSON özellikleri açıklanmaktadır.

| Özellik | Açıklama | Gerekli |
| -------- | ----------- | -------- |
| ad | İşlem hattındaki etkinliğin adı. | Evet |
| açıklama | Etkinliğin ne yaptığını açıklayan metin. | Hayır |
| type | Bu özellik HDInsightSpark olarak ayarlanmalıdır. | Evet |
| linkedServiceName | Spark programının çalıştığı HDInsight bağlı hizmetinin adı. | Evet |
| rootPath | Spark dosyasını içeren blob kapsayıcısı ve klasörü. Dosya adı büyük/küçük harfe duyarlıdır. | Evet |
| entryFilePath | Spark kodunun/paketinin kök klasörünün göreli yolu. | Evet |
| className | Uygulamanın Java/Spark ana sınıfı. | Hayır |
| Değişkenlerinden | Spark programına yönelik komut satırı bağımsız değişkenlerinin listesi. | Hayır |
| proxyUser | Spark programını yürütmek için kimliğe bürünmeye yönelik kullanıcı hesabı. | Hayır |
| Mini yapılandırma | [Spark yapılandırması: uygulama özellikleri](https://spark.apache.org/docs/latest/configuration.html#available-properties)' nde listelenen Spark yapılandırma özellikleri için değerleri belirtin. | Hayır |
| GetDebugInfo | Spark günlük dosyalarının, mini iş Linkedservice tarafından belirtilen HDInsight kümesi (veya) tarafından kullanılan depolamaya ne zaman kopyalanacağını belirtir. İzin verilen değerler None, Always veya Failure 'Tur. Varsayılan değer None ' dır. | Hayır |
| Mini iş Linkedservice | Spark iş dosyasını, bağımlılıklarını ve günlüklerini tutan depolama bağlı hizmeti. Bu özellik için bir değer belirtmezseniz, HDInsight kümesiyle ilişkili depolama kullanılır. | Hayır |

## <a name="folder-structure"></a>Klasör yapısı
Spark etkinliği, Pig olarak bir satır içi betiği desteklemez ve Hive etkinlikleri bunu yapmaz. Spark işleri, Pig/Hive işlerinin daha da genişletilebilir. Spark işleri için, jar paketleri (Java SıNıFYOLUNA yerleştirilmiş), Python dosyaları (PYTHONPATH üzerine yerleştirilmiş) ve diğer tüm dosyalar gibi birden çok bağımlılığı sağlayabilirsiniz.

HDInsight bağlı hizmeti tarafından başvurulan blob depolamada aşağıdaki klasör yapısını oluşturun. Ardından, bağımlı dosyaları **Entryfilepath**tarafından temsil edilen kök klasördeki uygun alt klasörlere yükleyin. Örneğin, Python dosyalarını pyfiles alt klasörüne ve jar dosyalarını kök klasörün jar dosyaları dışındaki alt klasörüne yükleyin. Çalışma zamanında, Data Factory hizmeti blob depolamada aşağıdaki klasör yapısını bekler: 

| Yol | Açıklama | Gerekli | Tür |
| ---- | ----------- | -------- | ---- |
| . | Depolama bağlı hizmetindeki Spark işinin kök yolu. | Evet | Klasör |
| &lt;Kullanıcı tanımlı &gt; | Spark işinin giriş dosyasına işaret eden yol. | Evet | Dosya |
| ./jars | Bu klasördeki tüm dosyalar, kümenin Java Sınıfyoluna yüklenir ve yerleştirilir. | Hayır | Klasör |
| ./pyFiles | Bu klasördeki tüm dosyalar, kümenin PYTHONPATH yüklenir ve bu klasöre yerleştirilir. | Hayır | Klasör |
| ./Files | Bu klasördeki tüm dosyalar, yürütücü çalışma dizinine yüklenir ve yerleştirilir. | Hayır | Klasör |
| ./Arşivler | Bu klasördeki tüm dosyalar sıkıştırılmamış. | Hayır | Klasör |
| ./logs | Spark kümesindeki günlüklerin depolandığı klasör.| Hayır | Klasör |

HDInsight bağlı hizmeti tarafından başvurulan blob depolamada iki Spark iş dosyası içeren depolama için bir örnek aşağıda verilmiştir:

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
