---
title: Azure Data Factory Team Data Science Işlemi ile SQL veritabanı 'na veri SQL Server
description: Birlikte verileri şirket içinde ve bulutta veritabanları arasında günlük olarak taşıyacağınız iki veri geçiş etkinliğini oluşturan bir ADF işlem hattı ayarlayın.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e477e4bb3b31477f9407e981d4c8da2340411f55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102615746"
---
# <a name="move-data-from-a-sql-server-database-to-sql-database-with-azure-data-factory"></a>SQL Server veritabanından SQL veritabanı 'na veri taşıma Azure Data Factory

Bu makalede, Azure Data Factory (ADF) kullanılarak Azure Blob depolama aracılığıyla SQL Server veritabanından Azure SQL veritabanı 'na veri taşıma işlemi gösterilmektedir: Bu yöntem, çoğaltılan bir hazırlama kopyasının avantajları olan, desteklenen bir eski yaklaşım olsa da, [en son seçenekler için veri geçiş sayfamıza bakmak tavsiye ederiz](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

Verileri bir Azure SQL veritabanına taşımaya yönelik çeşitli seçenekleri özetleyen bir tablo için bkz. [Azure Machine Learning için verileri Azure SQL veritabanına taşıma](move-sql-azure.md).

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Giriş: ADF nedir ve verileri geçirmek için ne zaman kullanılmalıdır?
Azure Data Factory, verilerin hareketini ve dönüştürülmesini düzenleyen ve otomatikleştiren, tam olarak yönetilen bulut tabanlı bir veri tümleştirme hizmetidir. ADF modelindeki anahtar kavram işlem hattı. İşlem hattı, her biri veri kümelerinde yer alan verilerde gerçekleştirilecek eylemleri tanımlayan bir mantıksal etkinlik gruplandırmasıdır. Bağlı hizmetler, Data Factory veri kaynaklarına bağlanmak için gereken bilgileri tanımlamak üzere kullanılır.

ADF ile, var olan veri işleme hizmetleri, bulutta yüksek oranda kullanılabilir ve yönetilen veri işlem hatlarına eklenebilir. Bu veri ardışık düzenleri verileri almak, hazırlamak, dönüştürmek, analiz etmek ve yayımlamak için zamanlanabilir ve ADF, karmaşık verileri ve işleme bağımlılıklarını yönetip düzenler. Çözümler bulutta hızla oluşturulup dağıtılabilir ve artan sayıda şirket içi ve bulut veri kaynağını birbirine gönderebilir.

ADF 'yi kullanmayı düşünün:

* verilerin hem şirket içi hem de bulut kaynaklarına erişen bir karma senaryoya sürekli geçirilmesi gerektiğinde
* veriler dönüşümlere ihtiyaç duyduğunda veya geçirildiğinde iş mantığı eklendiğinde.

ADF, verilerin düzenli aralıklarla taşınmasını yöneten basit JSON betikleri kullanılarak işlerin zamanlamasını ve izlenmesini sağlar. ADF Ayrıca karmaşık işlemler için destek gibi başka yetenekler de içerir. ADF hakkında daha fazla bilgi için [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/)belgelerine bakın.

## <a name="the-scenario"></a><a name="scenario"></a>Senaryo
İki veri geçiş etkinliğini oluşturan bir ADF işlem hattı ayarladık. Bir arada, verileri bir SQL Server veritabanı ve Azure SQL veritabanı arasında günlük olarak taşır. İki etkinlik şunlardır:

* SQL Server veritabanından Azure Blob depolama hesabına veri kopyalama
* Azure Blob depolama hesabından Azure SQL veritabanı 'na veri kopyalama.

> [!NOTE]
> Burada gösterilen adımlar, ADF ekibi tarafından sunulan daha ayrıntılı öğreticiden uyarlanmıştır: [bir SQL Server veritabanından Azure Blob depolama başvurularına veri kopyalama](../../data-factory/tutorial-hybrid-copy-portal.md) ilgili konunun ilgili bölümlerine, uygun olduğunda sağlanır.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Önkoşullar
Bu öğreticide şunları kabul edersiniz:

* Bir **Azure aboneliği**. Aboneliğiniz yoksa [ücretsiz deneme sürümü](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.
* Bir **Azure depolama hesabı**. Bu öğreticide verileri depolamak için bir Azure depolama hesabı kullanın. Azure depolama hesabınız yoksa [Depolama hesabı oluşturma](../../storage/common/storage-account-create.md) makalesine bakın. Depolama hesabını oluşturduktan sonra, depolamaya erişmek için kullanılan hesap anahtarını edinmeniz gerekir. Bkz. [depolama hesabı erişim anahtarlarını yönetme](../../storage/common/storage-account-keys-manage.md).
* **Azure SQL veritabanına** erişim. Azure SQL veritabanı ayarlamanız gerekirse [Microsoft Azure SQL veritabanı Ile çalışmaya](../../azure-sql/database/single-database-create-quickstart.md) başlama konusu, Azure SQL veritabanı 'nın yeni bir örneğini sağlama hakkında bilgi sağlar.
* **Azure PowerShell** yerel olarak yüklendi ve yapılandırıldı. Yönergeler için bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/).

> [!NOTE]
> Bu yordam [Azure Portal](https://portal.azure.com/)kullanır.
>
>

## <a name="upload-the-data-to-your-sql-server-instance"></a><a name="upload-data"></a> Verileri SQL Server örneğine yükleme
Geçiş işlemini göstermek için [NYC TAXI veri kümesini](https://chriswhong.com/open-data/foil_nyc_taxi/) kullanıyoruz. NYC TAXI veri kümesi, Azure Blob Storage [NYC TAXI verilerinde](https://www.andresmh.com/nyctaxitrips/)bu gönderde belirtildiği gibi kullanılabilir. Verilerin iki dosyası vardır, seyahat ayrıntılarını içeren trip_data.csv dosyası ve her seyahat için ödenen tarifeli havayolu ayrıntılarını içeren trip_far.csv dosyası. Bu dosyaların bir örneği ve açıklaması [NYC TAXI gezme veri kümesi açıklamasında](sql-walkthrough.md#dataset)verilmiştir.

Burada belirtilen yordamı kendi verilerinizin kümesine uyarlayabilir veya NYC TAXI veri kümesini kullanarak açıklanan adımları izleyebilirsiniz. NYC TAXI veri kümesini SQL Server veritabanınıza yüklemek için, [verileri SQL Server veritabanına toplu Içeri aktarma](sql-walkthrough.md#dbload)bölümünde özetlenen yordamı izleyin.

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a> Azure Data Factory oluşturma
Yeni bir Azure Data Factory ve [Azure Portal](https://portal.azure.com/) kaynak grubu oluşturma yönergeleri [Azure Data Factory oluşturma](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Yeni ADF örneğini *adfdsp* olarak adlandırın ve kaynak grubunun adını *adfdsprg* olarak adlandırın.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Azure Data Factory Integration Runtime yükleyip yapılandırın
Integration Runtime, farklı ağ ortamlarında veri tümleştirme özellikleri sağlamak için Azure Data Factory tarafından kullanılan müşteri tarafından yönetilen bir veri tümleştirme altyapısıdır. Bu çalışma zamanına daha önce "Veri Yönetimi Gateway" adı verilir.

Ayarlamak için işlem [hattı oluşturma yönergelerini izleyin](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Veri kaynaklarına bağlanmak için bağlı hizmetler oluşturma
Bağlı bir hizmet Azure Data Factory bir veri kaynağına bağlanmak için gereken bilgileri tanımlar. Bu senaryoda bağlı hizmetlerin gerektiği üç kaynağı vardır:

1. Şirket içi SQL Server
2. Azure Blob Depolama
3. Azure SQL Veritabanı

Bağlı hizmetler oluşturmak için adım adım yordam, [bağlı hizmetler oluşturma](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)bölümünde verilmiştir.


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Veri kümelerine nasıl erişecağınızı belirtmek için tablo tanımlama ve oluşturma
Aşağıdaki komut dosyası tabanlı yordamlarla veri kümelerinin yapısını, konumunu ve kullanılabilirliğini belirten tablolar oluşturun. JSON dosyaları tabloları tanımlamak için kullanılır. Bu dosyaların yapısı hakkında daha fazla bilgi için bkz. [veri kümeleri](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> `Add-AzureAccount`Komut yürütmesi için doğru Azure aboneliğinin seçili olduğunu onaylamak üzere [New-AzureDataFactoryTable](/previous-versions/azure/dn835096(v=azure.100)) cmdlet 'ini yürütmeden önce cmdlet 'ini yürütmelisiniz. Bu cmdlet 'in belgeleri için bkz. [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount).
>
>

Tablolardaki JSON tabanlı tanımlar aşağıdaki adları kullanır:

* SQL Server **tablo adı** *nyctaxi_data*
* Azure Blob depolama hesabındaki **kapsayıcı adı** *ContainerName*

Bu ADF işlem hattı için üç tablo tanımı gereklidir:

1. [SQL şirket içi tablosu](#adf-table-onprem-sql)
2. [Blob tablosu](#adf-table-blob-store)
3. [SQL Azure tablosu](#adf-table-azure-sql)

> [!NOTE]
> Bu yordamlar ADF etkinliklerini tanımlamak ve oluşturmak için Azure PowerShell kullanır. Ancak bu görevler Azure portal kullanılarak da gerçekleştirilebilir. Ayrıntılar için bkz. [veri kümeleri oluşturma](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>SQL şirket içi tablosu
SQL Server için tablo tanımı aşağıdaki JSON dosyasında belirtilmiştir:

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Sütun adları buraya eklenmedi. Sütun adlarında bunları buraya ekleyerek seçebilirsiniz (Ayrıntılar için [ADF belgelerinin](../../data-factory/copy-activity-overview.md) konusunu inceleyin.

Tablonun JSON tanımını dosyada *onpremtabledef.js* adlı bir dosyaya kopyalayın ve bilinen bir konuma kaydedin (burada *C:\temp\onpremtabledef.js* olarak kabul edilir). ADF 'de aşağıdaki Azure PowerShell cmdlet 'ini kullanarak tablo oluşturun:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json
```


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>Blob tablosu
Çıkış blobu konumunun tablosunun tanımı aşağıdaki konumdadır (Bu, alınan verileri Şirket içinden Azure blobuna eşler):

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Tablonun JSON tanımını dosyada *bloboutputtabledef.js* adlı bir dosyaya kopyalayın ve bilinen bir konuma kaydedin (burada *C:\temp\bloboutputtabledef.js* olarak kabul edilir). ADF 'de aşağıdaki Azure PowerShell cmdlet 'ini kullanarak tablo oluşturun:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json
```

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>SQL Azure tablosu
SQL Azure çıktısı için olan tablonun tanımı aşağıda verilmiştir (Bu şema, Blobun gelen verileri eşler):

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Tablonun JSON tanımını dosyada *AzureSqlTable.js* adlı bir dosyaya kopyalayın ve bilinen bir konuma kaydedin (burada *C:\temp\AzureSqlTable.js* olarak kabul edilir). ADF 'de aşağıdaki Azure PowerShell cmdlet 'ini kullanarak tablo oluşturun:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json
```


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>İşlem hattını tanımlama ve oluşturma
İşlem hattına ait etkinlikleri belirtin ve aşağıdaki komut dosyası tabanlı yordamlarla işlem hattı oluşturun. Ardışık düzen özelliklerini tanımlamak için bir JSON dosyası kullanılır.

* Betik, işlem **hattı adının** *Amldsprocesspipeline* olduğunu varsayar.
* Ayrıca, işlem hattının dönemselliğini günlük olarak yürütüyoruz ve iş için varsayılan yürütme süresini (12. UTC) kullanacağınızı unutmayın.

> [!NOTE]
> Aşağıdaki yordamlar, ADF ardışık düzenini tanımlamak ve oluşturmak için Azure PowerShell kullanır. Ancak bu görev Azure portal kullanılarak da gerçekleştirilebilir. Ayrıntılar için bkz. işlem [hattı oluşturma](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Daha önce sunulan tablo tanımlarını kullanarak ADF 'nin işlem hattı tanımı aşağıdaki gibi belirtilir:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has two activities: the first one copies data from SQL Server to Azure Blob, and the second one copies from Azure Blob to Azure Database Table",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from SQL Server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

İşlem hattının bu JSON tanımını dosyada *pipelinedef.js* adlı bir dosyaya kopyalayın ve bilinen bir konuma kaydedin (burada *C:\temp\pipelinedef.js* olarak kabul edilir). Aşağıdaki Azure PowerShell cmdlet 'ini kullanarak ADF 'de işlem hattı oluşturun:

```azurepowershell
New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json
```


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>İşlem hattını başlatma
İşlem hattı artık aşağıdaki komutu kullanarak çalıştırılabilir:

```azurepowershell
Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline
```

*StartDate* ve *EndDate* parametre değerlerinin, işlem hattının çalışmasını istediğiniz gerçek tarihlerle değiştirilmeleri gerekir.

İşlem hattı yürütüldükten sonra, veriler blob için seçilen kapsayıcıda, günde bir dosya olduğunu görebilmelisiniz.

ADF tarafından verileri artımlı olarak boru için sunulan işlevleri yararlanılabilir ettik. ADF 'nin sağladığı bu ve diğer yeteneklerin nasıl yapılacağı hakkında daha fazla bilgi için [ADF belgelerine](https://azure.microsoft.com/services/data-factory/)bakın.