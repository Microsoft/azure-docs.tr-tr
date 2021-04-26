---
title: SQL Server saklı yordam etkinliği
description: Data Factory bir işlem hattından bir Azure SQL veritabanı veya Azure SYNAPSE Analytics 'teki saklı yordamı çağırmak için SQL Server saklı yordam etkinliğini nasıl kullanabileceğinizi öğrenin.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 05717352936bed888e108277d0163e43bc5a37af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368770"
---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server saklı yordam etkinliği
> [!div class="op_single_selector" title1="Dönüştürme etkinlikleri"]
> * [Hive etkinliği](data-factory-hive-activity.md)
> * [Pig etkinliği](data-factory-pig-activity.md)
> * [MapReduce etkinliği](data-factory-map-reduce.md)
> * [Hadoop akışı etkinliği](data-factory-hadoop-streaming-activity.md)
> * [Spark etkinliği](data-factory-spark.md)
> * [Azure Machine Learning Studio (klasik) Batch Yürütme Etkinliği](data-factory-azure-ml-batch-execution-activity.md)
> * [Azure Machine Learning Studio (klasik) Kaynak Güncelleştirme Etkinliği](data-factory-azure-ml-update-resource-activity.md)
> * [Saklı Yordam Etkinliği](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL Etkinliği](data-factory-usql-activity.md)
> * [.NET özel etkinliği](data-factory-use-custom-activities.md)

> [!NOTE]
> Bu makale, Azure Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory saklı yordam kullanarak verileri dönüştürme etkinliği](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Genel Bakış
Ham verileri tahmine dayalı ve Öngörüler halinde dönüştürmek ve [işlemek için bir](data-factory-create-pipelines.md) Data Factory işlem hattındaki veri dönüştürme etkinliklerini kullanırsınız. Saklı yordam etkinliği Data Factory desteklediği dönüştürme etkinliklerinden biridir. Bu makale, veri dönüştürme ve Data Factory desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](data-factory-data-transformation-activities.md) makalesinde oluşturulur.

Saklı yordam etkinliğini, kuruluşunuzda bulunan aşağıdaki veri depolarından birinde veya bir Azure sanal makinesinde (VM) bulunan bir saklı yordamı çağırmak için kullanabilirsiniz:

- Azure SQL Veritabanı
- Azure Synapse Analytics
- SQL Server veritabanı. SQL Server kullanıyorsanız, veritabanını barındıran aynı makineye veya veritabanına erişimi olan ayrı bir makineye Veri Yönetimi ağ geçidi yükleyebilirsiniz. Veri Yönetimi ağ geçidi, Azure VM 'de bulunan ve Şirket içindeki veri kaynaklarını güvenli ve yönetilen bir şekilde, bulut hizmetleriyle bağlayan bir bileşendir. Ayrıntılar için bkz. [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) makalesi.

> [!IMPORTANT]
> Verileri Azure SQL veritabanı 'na veya SQL Server kopyalarken, Copy etkinliğinde **Sqlsink** öğesini, **sqlWriterStoredProcedureName** özelliğini kullanarak bir saklı yordam çağırmak üzere yapılandırabilirsiniz. Daha fazla bilgi için bkz. [kopyalama etkinliğinden saklı yordamı çağırma](data-factory-invoke-stored-procedure-from-copy-activity.md). Özelliği hakkında daha fazla bilgi için şu bağlayıcı makalelerine bakın: [Azure SQL veritabanı](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Kopyalama etkinliği kullanarak verileri Azure SYNAPSE Analytics 'e kopyalarken saklı bir yordamı çağırmak desteklenmez. Ancak, Azure SYNAPSE Analytics 'te saklı yordam çağırmak için saklı yordam etkinliğini kullanabilirsiniz.
>
> Verileri Azure SQL veritabanı veya SQL Server ya da Azure SYNAPSE Analytics 'ten kopyalarken, **sqlReaderStoredProcedureName** özelliğini kullanarak kaynak veritabanından veri okumak için bir saklı yordam çağırmak üzere, Copy etkinliğinde **SQLSource** ' u yapılandırabilirsiniz. Daha fazla bilgi için şu bağlayıcı makalelerine bakın: [Azure SQL veritabanı](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SYNAPSE Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

Aşağıdaki izlenecek yol, Azure SQL veritabanı 'nda saklı yordam çağırmak için bir işlem hattındaki saklı yordam etkinliğini kullanır.

## <a name="walkthrough"></a>Kılavuz
### <a name="sample-table-and-stored-procedure"></a>Örnek tablo ve saklı yordam
1. SQL Server Management Studio veya rahat olan herhangi bir aracı kullanarak Azure SQL veritabanınızda aşağıdaki **tabloyu** oluşturun. DateTimeStamp sütunu, karşılık gelen KIMLIğIN oluşturulduğu tarih ve saat olur.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    ID, belirtilen benzersiz ve DateTimeStamp sütunu, karşılık gelen KIMLIğIN oluşturulduğu tarih ve saat.
    
    ![Örnek veriler](./media/data-factory-stored-proc-activity/sample-data.png)

    Bu örnekte, saklı yordam bir Azure SQL veritabanında bulunur. Saklı yordam Azure SYNAPSE Analytics ve SQL Server veritabanı 'nda ise yaklaşım benzerdir. SQL Server veritabanı için bir [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md)yüklemelisiniz.
2. **SampleTable** öğesine veri ekleyen aşağıdaki **saklı yordamı** oluşturun.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > Parametrenin **adı** ve büyük **harfleri** (Bu örnekteki DateTime), ARDıŞıK düzen/etkinlik JSON öğesinde belirtilen parametreyle eşleşmelidir. Saklı yordam tanımında **\@** parametresi için önek olarak kullanıldığından emin olun.

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
1. [Azure portalı](https://portal.azure.com/)’nda oturum açın.
2. Sol taraftaki menüde **Yeni** ' ye tıklayın, **Intelligence + Analytics**' e tıklayın ve **Data Factory**' ye tıklayın.

    ![Yeni Veri Fabrikası 1](media/data-factory-stored-proc-activity/new-data-factory.png)
3. **Yeni Veri Fabrikası** dikey penceresinde ad Için **sprocdf** girin. Azure Data Factory adları **genel olarak benzersizdir**. Fabrika 'nin başarıyla oluşturulmasını sağlamak için, adınızla veri fabrikasının adına önek uygulamanız gerekir.

   ![Yeni Data Factory 2](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. **Azure aboneliğinizi** seçin.
5. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:
   1. **Yeni oluştur** ' a tıklayın ve kaynak grubu için bir ad girin.
   2. **Var olanı kullan** ' a tıklayın ve var olan bir kaynak grubunu seçin.
6. Data factory için **konum** seçin.
7. Panoda, Data Factory 'yi bir sonraki oturum açışınızda görebilmeniz **için panoya sabitle ' yi** seçin.
8. **Yeni data factory** dikey penceresinde **Oluştur**’a tıklayın.
9. Azure portal **panosunda** oluşturulan veri fabrikasını görürsünüz. Data factory sorunsuz oluşturulduktan sonra data factory sayfasını görürsünüz, burada size data factory içeriği gösterilir.

   ![Data Factory giriş sayfası](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Azure SQL bağlı hizmeti oluşturma
Data Factory 'yi oluşturduktan sonra, veri fabrikanıza SampleTable tablosu ve usp_sample saklı yordamını içeren Azure SQL veritabanı 'nda veritabanınızı bağlayan bir Azure SQL bağlı hizmeti oluşturursunuz.

1. Data Factory Düzenleyiciyi başlatmak için **Sprocdf** **Data Factory** dikey penceresinde **Yazar ve dağıt** ' a tıklayın.
2. Komut çubuğunda **Yeni veri deposu** ' na tıklayın ve **Azure SQL veritabanı**' nı seçin. Düzenleyicide bir Azure SQL bağlı hizmeti oluşturmak için JSON betiği görmeniz gerekir.

   ![Yeni veri deposu 1](media/data-factory-stored-proc-activity/new-data-store.png)
3. JSON betiğine aşağıdaki değişiklikleri yapın:

   1. `<servername>`Sunucunuzun adıyla değiştirin.
   2. `<databasename>`Tabloyu ve saklı yordamı oluşturduğunuz veritabanıyla değiştirin.
   3. `<username@servername>`Veritabanına erişimi olan kullanıcı hesabı ile değiştirin.
   4. `<password>`Kullanıcı hesabının parolasıyla değiştirin.

      ![Yeni veri deposu 2](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Bağlı hizmeti dağıtmak için komut çubuğunda **Dağıt** ' a tıklayın. Sol taraftaki ağaç görünümünde Azuressqllinkedservice öğesini gördüğünüzü onaylayın.

    ![bağlı hizmet 1 ile ağaç görünümü](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Çıktı veri kümesi oluşturma
Saklı yordam herhangi bir veri üretmese bile, saklı yordam etkinliği için bir çıktı veri kümesi belirtmeniz gerekir. Bunun nedeni, etkinliğin zamanlamasını yönlendiren (etkinliğin ne sıklıkta saat, günlük, vb.) bir çıktı veri kümesidir. Çıktı veri kümesi, Azure SQL veritabanı veya Azure SYNAPSE Analytics 'e veya saklı yordamın çalıştırılmasını istediğiniz SQL Server veritabanına başvuran **bağlı bir hizmet** kullanmalıdır. Çıktı veri kümesi, diğer bir etkinlik tarafından sonraki işleme için saklı yordamın sonucunu geçirmek için bir yol işlevi görebilir (işlem hattındaki[etkinlikleri zincirleyerek](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) ). Ancak, Data Factory saklı yordamın çıkışını bu veri kümesine otomatik olarak yazmaz. Bu, çıktı veri kümesinin işaret ettiği bir SQL tablosuna yazan saklı yordamdır. Bazı durumlarda, çıkış veri kümesi bir **kukla veri kümesi** (saklı yordamın çıkışını gerçekten tutmayan bir tabloyu işaret eden bir veri kümesi) olabilir. Bu kukla veri kümesi yalnızca saklı yordam etkinliğini çalıştırmaya yönelik zamanlamayı belirtmek için kullanılır.

1. Tıklayın **... Araç çubuğundan daha fazla** , **Yeni veri kümesi** ve **Azure SQL**' e tıklayın. Komut çubuğunda **Yeni veri kümesi** ve **Azure SQL**' i seçin.

    ![bağlı hizmet 2 ile ağaç görünümü](media/data-factory-stored-proc-activity/new-dataset.png)
2. Aşağıdaki JSON betiğini kopyalayın/JSON düzenleyicisine yapıştırın.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Veri kümesini dağıtmak için komut çubuğunda **Dağıt** ' a tıklayın. Veri kümesini ağaç görünümünde görtığınızdan emin olun.

    ![bağlı hizmetlerle ağaç görünümü](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>SqlServerStoredProcedure etkinliği ile işlem hattı oluşturma
Şimdi, saklı yordam etkinliğiyle bir işlem hattı oluşturalım.

Aşağıdaki özelliklere dikkat edin:

- **Type** özelliği **sqlserverstoredprocedure** olarak ayarlanır.
- Tür özelliklerindeki **storedProcedureName** , **usp_sample** (saklı yordamın adı) olarak ayarlanır.
- **StoredProcedureParameters** bölümü **DateTime** adlı bir parametre içerir. JSON 'daki parametrenin adı ve büyük harfleri, saklı yordam tanımındaki parametresinin adı ve büyük harfleri ile aynı olmalıdır. Bir parametre için null değer geçirmeniz gerekiyorsa, söz dizimini kullanın: `"param1": null` (tümü küçük harf).

1. Tıklayın **... Daha fazla** komut çubuğu ve yeni işlem **hattı**' na tıklayın.
2. Aşağıdaki JSON kod parçacığını kopyalayıp yapıştırın:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. İşlem hattını dağıtmak için araç çubuğunda **Dağıt** ' a tıklayın.

### <a name="monitor-the-pipeline"></a>İşlem hattını izleme
1. Data Factory Düzenleyici dikey penceresini kapatmak ve Data Factory dikey penceresine dönmek için **X** işaretine, sonra da **Diyagram**’a tıklayın.

    ![Diyagram kutucuğu 1](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. **Diyagram görünümünde**, işlem hatlarına ve bu öğreticide kullanılan veri kümelerine bir genel bakış görürsünüz.

    ![Diyagram kutucuğu 2](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Diyagram görünümünde veri kümesine çift tıklayın `sprocsampleout` . Dilimleri, Ready durumunda görürsünüz. JSON 'dan başlangıç saati ve bitiş saati arasındaki her saat için bir dilim üretildiği için beş dilim olmalıdır.

    ![Diyagram kutucuğu 3](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Bir dilim **Ready** durumunda olduğunda, `select * from sampletable` verilerin tabloya saklı yordam tarafından eklendiğini doğrulamak için veritabanında bir sorgu çalıştırın.

   ![Çıktı verileri](./media/data-factory-stored-proc-activity/output.png)

   Azure Data Factory işlem hatlarını izleme hakkında ayrıntılı bilgi için bkz. işlem [hattını izleme.](data-factory-monitor-manage-pipelines.md)

## <a name="specify-an-input-dataset"></a>Giriş veri kümesi belirtin
İzlenecek yolda, saklı yordam etkinliğinde herhangi bir giriş veri kümesi yok. Bir giriş veri kümesi belirtirseniz, giriş veri kümesinin dilimi kullanılabilir olana kadar saklı yordam etkinliği çalışmaz (hazır durumda). Veri kümesi, bir dış veri kümesi (aynı ardışık düzende başka bir etkinlik tarafından üretilmeyen) veya bir yukarı akış etkinliği tarafından üretilen bir iç veri kümesi (Bu etkinlikten önce çalışan etkinlik) olabilir. Saklı yordam etkinliği için birden çok giriş veri kümesi belirtebilirsiniz. Bunu yaparsanız, saklı yordam etkinliği yalnızca tüm giriş veri kümesi dilimleri kullanılabilir olduğunda çalışır (hazır durumda). Giriş veri kümesi, saklı yordamda parametre olarak tüketilemiyor. Yalnızca saklı yordam etkinliğini başlatmadan önce bağımlılığı denetlemek için kullanılır.

## <a name="chaining-with-other-activities"></a>Diğer etkinliklerle zincirleme
Bu etkinlikle bir yukarı akış etkinliğini zincirlemek istiyorsanız, bu etkinliğin bir girişi olarak yukarı akış etkinliğinin çıkışını belirtin. Bunu yaptığınızda, saklı yordam etkinliği yukarı akış etkinliği tamamlanana kadar çalışmaz ve yukarı akış etkinliğinin çıkış veri kümesi kullanılabilir (hazır durumda). Birden çok yukarı akış etkinliğinin çıkış veri kümelerini, saklı yordam etkinliğinin giriş veri kümeleri olarak belirtebilirsiniz. Bunu yaptığınızda, saklı yordam etkinliği yalnızca tüm giriş veri kümesi dilimleri kullanılabilir olduğunda çalışır.

Aşağıdaki örnekte, kopyalama etkinliğinin çıktısı, saklı yordam etkinliğinin bir girişi olan OutputDataset ' dir. Bu nedenle, kopyalama etkinliği tamamlanana ve OutputDataset dilimi kullanılabilir (hazır durumda) kadar saklı yordam etkinliği çalışmaz. Birden çok giriş veri kümesi belirtirseniz, saklı yordam etkinliği tüm giriş veri kümesi dilimleri kullanılabilir olana kadar çalışmaz (hazır durumda). Giriş veri kümeleri, saklı yordam etkinliğine doğrudan parametre olarak kullanılamaz.

Zincir oluşturma etkinlikleri hakkında daha fazla bilgi için bkz. bir işlem hattında [birden çok etkinlik](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Benzer şekilde, mağaza yordamı etkinliğini **aşağı akış etkinlikleriyle** (saklı yordam etkinliğinin tamamlanmasından sonra çalışan etkinlikler) bağlamak için, saklı yordam etkinliğinin çıkış veri kümesini ardışık düzen etkinliğinin bir girişi olarak belirtin.

> [!IMPORTANT]
> Verileri Azure SQL veritabanı 'na veya SQL Server kopyalarken, Copy etkinliğinde **Sqlsink** öğesini, **sqlWriterStoredProcedureName** özelliğini kullanarak bir saklı yordam çağırmak üzere yapılandırabilirsiniz. Daha fazla bilgi için bkz. [kopyalama etkinliğinden saklı yordamı çağırma](data-factory-invoke-stored-procedure-from-copy-activity.md). Özelliği hakkında daha fazla bilgi için şu bağlayıcı makalelerine bakın: [Azure SQL veritabanı](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Verileri Azure SQL veritabanı veya SQL Server ya da Azure SYNAPSE Analytics 'ten kopyalarken, **sqlReaderStoredProcedureName** özelliğini kullanarak kaynak veritabanından veri okumak için bir saklı yordam çağırmak üzere, Copy etkinliğinde **SQLSource** ' u yapılandırabilirsiniz. Daha fazla bilgi için şu bağlayıcı makalelerine bakın: [Azure SQL veritabanı](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SYNAPSE Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>JSON biçimi
Saklı yordam etkinliğinin tanımlanması için JSON biçimi aşağıda verilmiştir:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

Aşağıdaki tabloda bu JSON özellikleri açıklanmaktadır:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| name | Etkinliğin adı |Yes |
| açıklama |Etkinliğin ne için kullanıldığını açıklayan metin |No |
| tür | Şu şekilde ayarlanmalıdır: **Sqlserverstoredprocedure** | Yes |
| girişi | İsteğe bağlı. Bir giriş veri kümesi belirtirseniz, saklı yordam etkinliğinin çalışması için (' hazır ' durumunda) kullanılabilir olmalıdır. Giriş veri kümesi, saklı yordamda parametre olarak tüketilemiyor. Yalnızca saklı yordam etkinliğini başlatmadan önce bağımlılığı denetlemek için kullanılır. |No |
| çıkışı | Saklı yordam etkinliği için bir çıkış veri kümesi belirtmeniz gerekir. Çıktı veri kümesi, saklı yordam etkinliğinin **zamanlamasını** belirtir (saatlik, haftalık, aylık, vb.). <br/><br/>Çıktı veri kümesi, Azure SQL veritabanı veya Azure SYNAPSE Analytics 'e veya saklı yordamın çalıştırılmasını istediğiniz SQL Server veritabanına başvuran **bağlı bir hizmet** kullanmalıdır. <br/><br/>Çıktı veri kümesi, diğer bir etkinlik tarafından sonraki işleme için saklı yordamın sonucunu geçirmek için bir yol işlevi görebilir (işlem hattındaki[etkinlikleri zincirleyerek](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) ). Ancak, Data Factory saklı yordamın çıkışını bu veri kümesine otomatik olarak yazmaz. Bu, çıktı veri kümesinin işaret ettiği bir SQL tablosuna yazan saklı yordamdır. <br/><br/>Bazı durumlarda, çıkış veri kümesi yalnızca saklı yordam etkinliğini çalıştırmaya yönelik zamanlamayı belirtmek için kullanılan bir **kukla veri kümesi** olabilir. |Yes |
| storedProcedureName |Azure SQL veritabanı, Azure SYNAPSE Analytics veya çıkış tablosunun kullandığı bağlı hizmet tarafından temsil edilen SQL Server saklı yordamın adını belirtin. |Yes |
| storedProcedureParameters |Saklı yordam parametrelerinin değerlerini belirtin. Bir parametre için null değer geçirmeniz gerekiyorsa, "Param1": null (tümü küçük harf) sözdizimini kullanın. Bu özelliği kullanma hakkında bilgi edinmek için aşağıdaki örneğe bakın. |No |

## <a name="passing-a-static-value"></a>Statik bir değer geçirme
Şimdi ' senaryo ' adlı, ' belge örneği ' adlı statik bir değer içeren tabloya ' senaryo ' adlı başka bir sütun eklemeyi düşünün.

![Örnek veriler 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tablosundan**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Saklı yordam:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Şimdi, **senaryo** parametresini ve değeri saklı yordam etkinliğinden geçirin. Yukarıdaki örnekteki **Typeproperties** bölümü aşağıdaki kod parçacığı gibi görünür:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory veri kümesi:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Data Factory işlem hattı**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
