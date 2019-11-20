---
title: 'Öğretici: Visual Studio kullanarak Kopyalama Etkinliği ile işlem hattı oluşturma '
description: Bu öğreticide, Visual Studio kullanarak Kopyalama Etkinliği ile bir Azure Data Factory işlem hattı oluşturursunuz.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.custom: vs-azure
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3d14b4a821f91486c396d5dae516d3a545c02321
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682864"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Öğretici: Visual Studio kullanarak Kopyalama Etkinliği ile işlem hattı oluşturma
> [!div class="op_single_selector"]
> * [Genel bakış ve önkoşullar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopyalama Sihirbazı](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager şablonu](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API’si](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz. [kopyalama etkinliği öğreticisi](../quickstart-create-data-factory-dot-net.md). 

Bu makalede, Azure blob depolama alanından Azure SQL veritabanına veri kopyalayan bir işlem hattıyla veri fabrikası oluşturmak için Microsoft Visual Studio’yu nasıl kullanacağınızı öğreneceksiniz. Azure Data Factory’yi ilk kez kullanıyorsanız bu öğreticiyi tamamlamadan önce [Azure Data Factory’ye Giriş](data-factory-introduction.md) makalesini okuyun.   

Bu öğreticide, içinde bir etkinlik olan işlem hattı oluşturursunuz: Kopyalama Etkinliği. Kopyalama etkinliği, verileri, desteklenen bir veri deposundan desteklenen bir havuz veri deposuna kopyalar. Kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Etkinlik, çeşitli veri depolama alanları arasında güvenli, güvenilir ve ölçeklenebilir bir yolla veri kopyalayabilen genel olarak kullanılabilir bir hizmet tarafından desteklenir. Kopyalama Etkinliği hakkında daha fazla bilgi için bkz. [Veri Taşıma Etkinlikleri](data-factory-data-movement-activities.md).

Bir işlem hattında birden fazla etkinlik olabilir. Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliğin diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [bir işlem hattında birden fazla etkinlik](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE] 
> Bu öğreticideki veri işlem hattı, bir kaynak veri deposundaki verileri hedef veri deposuna kopyalar. Azure Data Factory kullanarak verileri dönüştürme hakkında bir öğretici için bkz. [Öğretici: Hadoop kümesi kullanarak verileri dönüştürmek için işlem hattı oluşturma](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. [Öğreticiye Genel Bakış](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) makalesinin tamamını okuyun ve **ön koşul** adımlarını tamamlayın.       
2. Data Factory örnekleri oluşturmak için abonelik/kaynak grubu düzeyinde [Data Factory Katılımcısı](../../role-based-access-control/built-in-roles.md#data-factory-contributor) rolünün üyesi olmanız gerekir.
3. Bilgisayarınızda şunların yüklü olması gerekir: 
   * Visual Studio 2013 veya Visual Studio 2015
   * Visual Studio 2013 veya Visual Studio 2015 için Azure SDK’sını indirin. [Azure İndirme Sayfası](https://azure.microsoft.com/downloads/)’na gidin ve **.NET** bölümündeki **VS 2013** veya **VS 2015**’e tıklayın.
   * Visual Studio için en son Azure Data Factory eklentisini indirin: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) veya [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Aşağıdaki adımları uygulayarak eklentiyi güncelleştirebilirsiniz: Menüde **Araçlar** -> **Uzantılar ve Güncelleştirmeler** -> **Çevrimiçi** -> **Visual Studio Galerisi** -> **Visual Studio için Microsoft Azure Data Factory Araçları** -> **Güncelleştir**’e tıklayın.

## <a name="steps"></a>Adımlar
Bu eğitimin bir parçası olarak gerçekleştireceğiniz adımlar şunlardır:

1. Veri fabrikasında **bağlı hizmetler** oluşturun. Bu adımda Azure Depolama ve Azure SQL Veritabanı türünde iki bağlı hizmet oluşturursunuz. 
    
    AzureStorageLinkedService, Azure depolama hesabınızı veri fabrikasına bağlar. Bir kapsayıcı oluşturup verileri [önkoşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak bu depolama hesabına yüklediniz.   

    AzureSqlLinkedService, Azure SQL veritabanınızı veri fabrikasına bağlar. Blob depolama alanından kopyalanan veriler bu veritabanında depolanır. Bu veritabanındaki SQL tablosunu, [ön koşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak oluşturdunuz.     
2. Veri fabrikasında girdi ve çıktı **veri kümesi oluşturma**.  
    
    Azure depolama bağlı hizmeti, Data Factory hizmetinin Azure depolama hesabınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Giriş blob veri kümesi ise kapsayıcıyı ve girdi verilerini içeren klasörü belirtir.  

    Benzer şekilde, Azure SQL Veritabanı bağlı hizmeti, Data Factory hizmetinin Azure SQL veritabanınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Çıktı SQL tablosu veri kümesi ise blob depolama alanındaki verilerin kopyalandığı veritabanında tabloyu belirtir.
3. Veri fabrikasında **işlem hattı** oluşturun. Bu adımda, kopyalama etkinliği ile bir işlem hattı oluşturursunuz.   
    
    Kopyalama etkinliği, verileri Azure blob depolama alanındaki bir blobdan Azure SQL veritabanındaki tabloya kopyalar. Verileri desteklenen herhangi bir kaynaktan desteklenen herhangi bir hedefe kopyalamak için bir işlem hattındaki kopyalama etkinliğini kullanabilirsiniz. Desteklenen veri depolarının bir listesi için [veri taşıma etkinlikleri](data-factory-data-movement-activities.md#supported-data-stores-and-formats) makalesine bakın. 
4. Data Factory varlıklarını (bağlı hizmetler, veri kümeleri/tabloları ve işlem hatları) dağıtırken bir Azure **veri fabrikası** oluşturun. 

## <a name="create-visual-studio-project"></a>Visual Studio projesi oluşturma
1. **Visual Studio 2015**’i başlatın. **Dosya**’ya tıklayın, **Yeni**’nin üzerine gelin ve **Proje**’ye tıklayın. **Yeni Proje** iletişim kutusu görmeniz gerekir.  
2. **Yeni Proje** iletişim kutusunda **DataFactory** şablonunu seçip **Boş Data Factory Projesi**’ne tıklayın.  
   
    ![Yeni proje iletişim kutusu](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. Proje adını, çözümün konumunu ve çözüm adını belirtip **Tamam**’a tıklayın.
   
    ![Çözüm Gezgini](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Veri depolarınızı ve işlem hizmetlerinizi veri fabrikasına bağlamak için veri fabrikasında bağlı hizmetler oluşturursunuz. Bu öğreticide, Azure HDInsight veya Azure Data Lake Analytics gibi herhangi bir işlem hizmeti kullanmazsınız. Azure Depolama (kaynak) ve Azure SQL Veritabanı (hedef) türünde iki veri deposu kullanırsınız. 

Bu nedenle, AzureStorage ve AzureSqlDatabase türünde iki bağlı hizmet oluşturursunuz.  

Azure Depolama bağlı hizmeti, Azure depolama hesabınızı veri fabrikasına bağlar. Bu depolama hesabı, kapsayıcı oluşturduğunuz ve verileri [ön koşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak yüklediğiniz hesaptır.   

Azure SQL bağlı hizmeti, Azure SQL veritabanınızı veri fabrikasına bağlar. Blob depolama alanından kopyalanan veriler bu veritabanında depolanır. Bu veritabanındaki emp tablosunu, [ön koşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak oluşturdunuz.

Bağlı hizmetler veri depolarını veya işlem hizmetlerini Azure data factory’ye bağlar. Kopyalama Etkinliği tarafından desteklenen tüm kaynaklar ve havuzlar için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory tarafından desteklenen işlem hizmetlerinin listesi için bkz. [bağlantılı işlem hizmetleri](data-factory-compute-linked-services.md). Bu öğreticide herhangi bir işlem hizmeti kullanmayın. 

### <a name="create-the-azure-storage-linked-service"></a>Azure Storage bağlı hizmeti oluşturma
1. **Çözüm Gezgini**’nde **Bağlı Hizmetler**’e sağ tıklayın, **Ekle**’nin üzerine gelip **Yeni Öğe**’ye tıklayın.      
2. **Yeni Öğe Ekle** iletişim kutusunda **Azure Storage Bağlı Hizmeti**’ni listeden seçip **Ekle**’ye tıklayın. 
   
    ![Yeni Bağlı Hizmet](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. `<accountname>` ve `<accountkey>`* sözcüklerini Azure depolama hesabınızın adı ve anahtarıyla değiştirin. 
   
    ![Azure Storage Bağlı Hizmeti](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. **AzureStorageLinkedService1.json** dosyasını kaydedin.

    Bağlı hizmet tanımındaki JSON özellikleri hakkında daha fazla bilgi için [Azure Blob Depolama bağlayıcısı](data-factory-azure-blob-connector.md#linked-service-properties) makalesine bakın.

### <a name="create-the-azure-sql-linked-service"></a>Azure SQL bağlı hizmeti oluşturma
1. **Çözüm Gezgini**’nde bir kez daha **Bağlı Hizmetler** düğümüne sağ tıklayın, **Ekle**’nin üzerine gelip **Yeni Öğe**’ye tıklayın. 
2. Bu sefer, **Azure SQL Bağlı Hizmeti**’ni seçin ve **Ekle**’ye tıklayın. 
3. **AzureSqlLinkedService1.json dosyasında** `<servername>`, `<databasename>`, `<username@servername>` ve `<password>` öğesini Azure SQL sunucusu, veritabanı, kullanıcı hesabı ve parolası ile değiştirin.    
4. **AzureSqlLinkedService1.json** dosyasını kaydedin. 
    
    Bu JSON özellikleri hakkında daha fazla bilgi için [Azure SQL Veritabanı bağlayıcısı](data-factory-azure-sql-connector.md#linked-service-properties) makalesine bakın.


## <a name="create-datasets"></a>Veri kümeleri oluşturun
Önceki adımda, Azure Depolama hesabınızı ve Azure SQL veritabanınızı veri fabrikanıza bağlamak için bağlı hizmetler oluşturdunuz. Bu adımda, sırasıyla AzureStorageLinkedService1 ve AzureSqlLinkedService1 tarafından başvurulan veri depolarında depolanan girdi ve çıktı verilerini temsil eden InputDataset ve OutputDataset adlı iki veri kümesini tanımlarsınız.

Azure depolama bağlı hizmeti, Data Factory hizmetinin Azure depolama hesabınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Giriş blobu veri kümesi (InputDataset) ise kapsayıcıyı ve girdi verilerini içeren klasörü belirtir.  

Benzer şekilde, Azure SQL Veritabanı bağlı hizmeti, Data Factory hizmetinin Azure SQL veritabanınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Çıktı SQL tablosu veri kümesi (OutputDataset) ise blob depolama alanındaki verilerin kopyalandığı veritabanında tabloyu belirtir. 

### <a name="create-input-dataset"></a>Girdi veri kümesi oluşturma
Bu adımda, InputDataset adlı bir veri kümesi oluşturursunuz. Bu veri kümesi, AzureStorageLinkedService1 bağlı hizmetiyle temsil edilen Azure Depolama’daki bir blob kapsayıcısının (adftutorial) kök klasöründe bulunan blob dosyasını (emp.txt) işaret eder. fileName için bir değer belirtmezseniz (veya bu adımı atlarsanız) girdi klasöründe bulunan tüm blob’lardaki veriler hedefe kopyalanır. Bu öğreticide, fileName için bir değer belirtirsiniz. 

Burada, "veri kümeleri" terimi yerine "tablo" terimini kullanırsınız. Tablo dikdörtgen bir veri kümesidir ve şu anda yalnızca bu veri kümesi türü desteklenmektedir. 

1. **Çözüm Gezgini**’nde **Tablolar**’a sağ tıklayın, **Ekle**’nin üzerine gelip **Yeni Öğe**’ye tıklayın.
2. **Yeni Öğe Ekle** iletişim kutusunda, **Azure Blob**’u seçin ve **Ekle**’ye tıklayın.   
3. JSON metnini aşağıdaki metinle değiştirin ve **AzureBlobLocation1.json** dosyasını kaydedin. 

   ```json   
   {
    "name": "InputDataset",
    "properties": {
      "structure": [
        {
          "name": "FirstName",
          "type": "String"
        },
        {
          "name": "LastName",
          "type": "String"
        }
      ],
      "type": "AzureBlob",
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
        "format": {
          "type": "TextFormat",
          "columnDelimiter": ","
        }
      },
      "external": true,
      "availability": {
        "frequency": "Hour",
        "interval": 1
      }
    }
   }
   ``` 
    Aşağıdaki tabloda, kod parçacığında kullanılan JSON özellikleri için açıklamalar verilmektedir:

    | Özellik | Açıklama |
    |:--- |:--- |
    | type | Veriler Azure blob depolama alanında yer aldığından type özelliği **AzureBlob** olarak ayarlanmıştır. |
    | linkedServiceName | Daha önce oluşturduğunuz **AzureStorageLinkedService**’e başvurur. |
    | folderPath | blob **kapsayıcıyı** ve girdi blob’larını içeren **klasörü** belirtir. Bu öğreticide adftutorial, blob kapsayıcısıdır ve klasör, kök klasördür. | 
    | fileName | Bu özellik isteğe bağlıdır. Bu özelliği atarsanız tüm folderPath dosyaları alınır. Bu öğreticide fileName için **emp.txt** belirtilir, bu nedenle işlem için yalnızca bu dosya seçilir. |
    | format -> type |Girdi dosyası metin biçiminde olduğundan **TextFormat**'ı kullanırız. |
    | columnDelimiter | Girdi dosyasındaki sütunlar, **virgül (`,`)** ile ayrılmıştır. |
    | frequency/interval | frequency **Saat**, interval da **1** olarak ayarlanmıştır. Bu, girdi dilimlerinin **saatlik** olarak kullanılabileceğini belirtir. Başka bir deyişle, Data Factory hizmeti belirttiğiniz blob kapsayıcısının (**adftutorial**) kök klasöründe girdi verilerini saatte bir kere arar. İşlem hattı başlangıç ve bitiş zamanlarındaki verileri arar, bu zamanlardan önceki veya sonraki verileri aramaz.  |
    | external | Bu özellik, veriler bu işlem hattı tarafından oluşturulmazsa **true** olarak ayarlanır. Bu öğreticideki girdi verileri, bu işlem hattı tarafından oluşturulmayan emp.txt dosyasında bulunur, bu nedenle bu özelliği true olarak ayarlarız. |

    Bu JSON özellikleri hakkında daha fazla bilgi için bkz. [Azure Blob bağlayıcısı makalesi](data-factory-azure-blob-connector.md#dataset-properties).   

### <a name="create-output-dataset"></a>Çıktı veri kümesi oluşturma
Bu adımda **OutputDataset** adlı bir çıktı veri kümesi oluşturursunuz. Bu veri kümesi, **AzureSqlLinkedService1** ile temsil edilen Azure SQL veritabanında bir SQL tablosunu işaret eder. 

1. **Çözüm Gezgini**’nde bir kez daha **Tablolar**’a sağ tıklayın, **Ekle**’nin üzerine gelip **Yeni Öğe**’ye tıklayın.
2. **Yeni Öğe Ekle** iletişim kutusunda, **Azure SQL**’i seçin ve **Ekle**’ye tıklayın. 
3. JSON metnini aşağıdaki JSON ile değiştirin ve **AzureSqlTableLocation1.json** dosyasını kaydedin.

   ```json
    {
     "name": "OutputDataset",
     "properties": {
       "structure": [
         {
           "name": "FirstName",
           "type": "String"
         },
         {
           "name": "LastName",
           "type": "String"
         }
       ],
       "type": "AzureSqlTable",
       "linkedServiceName": "AzureSqlLinkedService1",
       "typeProperties": {
         "tableName": "emp"
       },
       "availability": {
         "frequency": "Hour",
         "interval": 1
       }
     }
    }
    ```
    Aşağıdaki tabloda, kod parçacığında kullanılan JSON özellikleri için açıklamalar verilmektedir:

    | Özellik | Açıklama |
    |:--- |:--- |
    | type | type özelliği, veriler Azure SQL veritabanındaki bir tabloya kopyalandığından **AzureSqlTable** olarak ayarlanır. |
    | linkedServiceName | Daha önce oluşturduğunuz **AzureSqlLinkedService**’e başvurur. |
    | tableName | Verilerin kopyalandığı **tabloyu** belirtir. | 
    | frequency/interval | frequency **Saatlik** ve interval **1** olarak ayarlanır. Bu durumda çıktı dilimleri, işlem hattı başlangıç ve bitiş zamanları arasında **saatlik** olarak üretilir, bu zamanlardan önce veya sonra üretilmez.  |

    Veritabanındaki emp tablosunda üç sütun vardır: **ID**, **FirstName** ve **LastName**. ID bir kimlik sütunu olduğundan, burada yalnızca **FirstName** ve **LastName** değerlerini belirtmeniz gerekir.

    Bu JSON özellikleri hakkında daha fazla bilgi için bkz. [Azure SQL bağlayıcısı makalesi](data-factory-azure-sql-connector.md#dataset-properties).

## <a name="create-pipeline"></a>İşlem hattı oluşturma
Bu adımda, girdi olarak **InputDataset** ve çıktı olarak **OutputDataset** kullanan **kopyalama etkinliği**’ne sahip bir işlem hattı oluşturursunuz.

Şu anda zamanlamayı çıktı veri kümesi yürütmektedir. Bu öğreticide, çıktı veri kümesi saatte bir dilim oluşturacak şekilde yapılandırılır. İşlem hattının başlangıç zamanı ve bitiş zamanı arasında bir gün, yani 24 saat vardır. Bu nedenle, işlem hattı çıktı veri kümesinden 24 dilim oluşturur. 

1. **Çözüm Gezgini**’nde **İşlem Hatları**’na sağ tıklayın, **Ekle**’nin üzerine gelip **Yeni Öğe**’ye tıklayın.  
2. **Yeni Öğe Ekle** iletişim kutusunda **Veri İşlem Hattı Kopyala**’yı seçip **Ekle**’ye tıklayın. 
3. JSON’u aşağıdaki JSON ile değiştirin ve **CopyActivity1.json** dosyasını kaydedin.

   ```json   
    {
     "name": "ADFTutorialPipeline",
     "properties": {
       "description": "Copy data from a blob to Azure SQL table",
       "activities": [
         {
           "name": "CopyFromBlobToSQL",
           "type": "Copy",
           "inputs": [
             {
               "name": "InputDataset"
             }
           ],
           "outputs": [
             {
               "name": "OutputDataset"
             }
           ],
           "typeProperties": {
             "source": {
               "type": "BlobSource"
             },
             "sink": {
               "type": "SqlSink",
               "writeBatchSize": 10000,
               "writeBatchTimeout": "60:00:00"
             }
           },
           "Policy": {
             "concurrency": 1,
             "executionPriorityOrder": "NewestFirst",
             "style": "StartOfInterval",
             "retry": 0,
             "timeout": "01:00:00"
           }
         }
       ],
       "start": "2017-05-11T00:00:00Z",
       "end": "2017-05-12T00:00:00Z",
       "isPaused": false
     }
    }
    ```   
   - Etkinlikler bölümünde, **türü** **Copy** olarak ayarlanmış yalnızca bir etkinlik vardır. Kopyalama etkinliği hakkında daha fazla bilgi için bkz. [veri taşıma etkinlikleri](data-factory-data-movement-activities.md). Data Factory çözümlerinde, [veri dönüştürme etkinliklerini](data-factory-data-transformation-activities.md) de kullanabilirsiniz.
   - Etkinlik girdisi **InputDataset** olarak, etkinlik çıktısı ise **OutputDataset** olarak ayarlanmıştır. 
   - **typeProperties** bölümünde **BlobSource** kaynak türü, **SqlSink** de havuz türü olarak belirtilir. Kaynak ve havuz olarak kopyalama etkinliği tarafından desteklenen veri depolarının eksiksiz listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Kaynak/havuz olarak desteklenen belirli bir veri deposunu nasıl kullanacağınızı öğrenmek için tablodaki bağlantıya tıklayın.  
     
     **start** özelliğinin değerini geçerli günle, **end** değerini de sonraki günle değiştirin. Tarih saatin yalnızca tarih bölümünü belirtip saat bölümünü atlayabilirsiniz. Örneğin, "2016-02-03", "2016-02-03T00:00:00Z" ile eşdeğerdir
     
     Başlangıç ve bitiş tarih saatleri [ISO biçiminde](https://en.wikipedia.org/wiki/ISO_8601) olmalıdır. Örneğin: 2016-10-14T16:32:41Z. **End** zamanı isteğe bağlıdır; ancak bu öğreticide bunu kullanacağız. 
     
     **end** özelliği için değer belirtmezseniz "**start + 48 hours**" olarak hesaplanır. İşlem hattını süresiz olarak çalıştırmak için **end** özelliği değerini **9999-09-09** olarak ayarlayın.
     
     Önceki örnekte, her veri dilimi saatlik oluşturulduğundan 24 veri dilimi vardır.

     İşlem hattı tanımındaki JSON özelliklerinin açıklamaları için [işlem hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Kopyalama etkinliği tanımındaki JSON özelliklerinin açıklamaları için bkz. [veri taşıma etkinlikleri](data-factory-data-movement-activities.md). BlobSource tarafından desteklenen JSON özelliklerinin açıklamaları için bkz. [Azure Blob bağlayıcısı makalesi](data-factory-azure-blob-connector.md). SqlSink tarafından desteklenen JSON özelliklerinin açıklamaları için bkz. [Azure SQL Veritabanı bağlayıcısı makalesi](data-factory-azure-sql-connector.md).

## <a name="publishdeploy-data-factory-entities"></a>Data Factory varlıklarını yayımlama/dağıtma
Bu adımda daha önce oluşturduğunuz Data Factory varlıklarını (bağlı hizmetler, veri kümeleri ve işlem hattı) yayımlayacaksınız. Ayrıca bu varlıkları tutmak için oluşturulacak yeni veri fabrikasının adını belirteceksiniz.  

1. Çözüm Gezgini’nde projeye sağ tıklayın ve ardından **Yayımla**’ya tıklayın. 
2. **Microsoft hesabınızda oturum açın** iletişim kutusunu görmezseniz, Azure aboneliğindeki kimlik bilgilerini hesap için girin ve **oturum aç**’a tıklayın.
3. Aşağıdaki iletişim kutusunu göreceksiniz:
   
   ![Yayımla iletişim kutusu](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. Data factory yapılandırma sayfasında aşağıdaki adımları uygulayın: 
   
   1. **Yeni Data Factory Oluştur** seçeneğini seçin.
   2. **Ad** için **VSTutorialFactory** girin.  
      
      > [!IMPORTANT]
      > Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. Yayımlandığında data factory adıyla ilgili bir hata alırsanız data factory adını değiştirip (örneğin, yournameVSTutorialFactory) yayımlamayı yeniden deneyin. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](data-factory-naming-rules.md) konusuna bakın.        
      > 
      > 
   3. **Abonelik** alanı için Azure aboneliğinizi seçin.
      
      > [!IMPORTANT]
      > Herhangi bir abonelik görmüyorsanız aboneliğin yöneticisi veya ortak yöneticisi olan bir hesapla oturum açtığınızdan emin olun.  
      > 
      > 
   4. oluşturulacak data factory için **kaynak grubu** seçin. 
   5. Data factory için **bölge** seçin. Açılır listede yalnızca Data Factory hizmeti tarafından desteklenen bölgeler gösterilmektedir.
   6. **Öğeleri Yayımla** sayfasına geçmek için **İleri**’ye tıklayın.
      
       ![Veri fabrikası yapılandırma sayfası](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. **Öğeleri Yayımla** sayfasında tüm Data Factory varlıklarının işaretli olmasını sağlayın ve **Özet** sayfasına geçmek için **İleri**’ye tıklayın.
   
   ![Öğe yayımlama sayfası](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Özeti gözden geçirin, dağıtım işlemini başlatmak ve **Dağıtım Durumu**’nu görüntülemek için **İleri**’ye tıklayın.
   
   ![Özet yayımlama sayfası](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. **Dağıtım Durumu** sayfasında dağıtım sürecinin durumunu görmelisiniz. Dağıtımını gerçekleştirdikten sonra Son'a tıklayın.
 
   ![Dağıtım durumu sayfası](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Aşağıdaki noktalara dikkat edin: 

* "Abonelik, Microsoft.DataFactory ad alanını kullanacak şekilde kaydedilmemiş" hatasını alırsanız, aşağıdakilerden birini yapın ve yeniden yayımlamayı deneyin: 
  
  * Azure PowerShell’de Data Factory sağlayıcısını kaydetmek için aşağıdaki komutu çalıştırın. 

    ```powershell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Data Factory sağlayıcısının kayıtlı olduğunu onaylamak için aşağıdaki komutu çalıştırabilirsiniz. 
    
    ```powershell
    Get-AzResourceProvider
    ```
  * Azure aboneliğini kullanarak [Azure portalında](https://portal.azure.com) oturum açın ve Data Factory dikey penceresine gidin (ya da) Azure portalında bir data factory oluşturun. Bu eylem sağlayıcıyı sizin için otomatik olarak kaydeder.
* Veri fabrikasının adı gelecekte bir DNS adı olarak kaydedilmiş ve herkese görünür hale gelmiş olabilir.

> [!IMPORTANT]
> Data Factory örnekleri oluşturmak için Azure aboneliğinin yönetici/ortak yöneticisi olmanız gerekir

## <a name="monitor-pipeline"></a>İşlem hattını izleme
Veri fabrikanızın giriş sayfasına gidin:

1. [Azure portalı](https://portal.azure.com)’nda oturum açın.
2. Sol menüdeki **Diğer hizmetler**’e ve **Veri fabrikaları** öğesine tıklayın.

    ![Data factory’lere göz atma](media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png)
3. Veri fabrikanızın adını yazmaya başlayın.

    ![Veri fabrikasının adı](media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png) 
4. Veri fabrikanızın giriş sayfasını görmek için sonuç listesinde veri fabrikanıza tıklayın.

    ![Data factory giriş sayfası](media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png)
5. Bu öğreticide oluşturduğunuz işlem hattını ve veri kümelerini izlemek için [Veri kümelerini ve işlem hatlarını izleme](data-factory-monitor-manage-pipelines.md) makalesindeki yönergeleri izleyin. Visual Studio şu anda Data Factory işlem hatlarını izlemeyi desteklememektedir. 

## <a name="summary"></a>Özet
Bu öğreticide Azure blob’undan Azure SQL veritabanına veri kopyalamak üzere Azure data factory oluşturdunuz. Data factory, bağlı hizmetler, veri kümeleri ve işlem hattı oluşturmak için Visual Studio’yu kullandınız. Bu öğreticide gerçekleştirilen üst düzey adımlar şunlardır:  

1. Oluşturulan Azure **data factory**.
2. Oluşturulan **bağlı hizmetler**:
   1. Girdi verilerini tutan Azure Storage hesabınıza bağlamak için **Azure Storage** bağlı hizmeti.     
   2. Çıktı verilerini tutan Azure SQL veritabanınıza bağlamak için **Azure SQL** bağlı hizmeti. 
3. İşlem hatları için girdi ve çıktı verilerini açıklayan **veri kümeleri** oluşturuldu.
4. Kaynak olarak **BlobSource**’u, havuz olarak da **SqlSink**’i kapsayan **Kopyalama Etkinliği**’ne sahip oluşturulan **işlem hattı**. 

Azure HDInsight kümesini kullanarak verileri dönüştürmek için HDInsight Hive etkinliğinin nasıl kullanıldığını görmek için bkz. [öğretici: Hadoop kümesi kullanarak verileri dönüştürmek için ilk işlem hattınızı oluşturma](data-factory-build-your-first-pipeline.md).

Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliği diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Ayrıntılı bilgi için bkz. [Data Factory’de zamanlama ve yürütme](data-factory-scheduling-and-execution.md). 

## <a name="view-all-data-factories-in-server-explorer"></a>Sunucu Gezgini’nde tüm veri fabrikalarını görüntüleme
Bu bölümde, Azure aboneliğinizdeki tüm veri fabrikalarını görüntülemek ve var olan bir veri fabrikasını temel alan Visual Studio projesi oluşturmak için Visual Studio'daki Sunucu Gezgini’nin nasıl kullanılacağı açıklanmaktadır. 

1. **Visual Studio**’nun menüsünde **Görünüm**’e ve **Sunucu Gezgini**’ne tıklayın.
2. Sunucu Gezgini penceresinde, **Azure**’ü ve **Data Factory**’yi genişletin. **Visual Studio'da oturum açın**’ı görürseniz Azure aboneliğiyle ilişkili **hesabı** girin ve **Devam**’a tıklayın. **parola** girip **Oturum aç**’a tıklayın. Visual Studio, aboneliğinizdeki tüm Azure data factory’leri hakkında bilgi almaya çalışır. Bu işlemin durumunu **Data Factory Görev Listesi** penceresinde görürsünüz.

    ![Sunucu Gezgini](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>Var olan bir veri fabrikası için Visual Studio projesi oluşturma

- Sunucu Gezgini’nde bir veri fabrikasına sağ tıklayıp **Veri Fabrikasını Yeni Projeye Aktar**’ı seçerek mevcut bir veri fabrikasını temel alan Visual Studio projesi oluşturun.

    ![Data factory’yi VS projesine aktarma](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Visual Studio için Data Factory araçlarını güncelleştirme
Visual Studio için Azure Data Factory araçlarını güncelleştirmek üzere aşağıdaki adımları uygulayın:

1. Menüde **Araçlar**’a tıklayın ve **Uzantılar ve Güncelleştirmeler**’i seçin. 
2. Sol bölmede **Güncelleştirmeler**’i, sonra da **Visual Studio Galerisi**’ni seçin.
3. **Visual Studio için Azure Data Factory araçları**’nı seçin ve **Güncelleştir**’e tıklayın. Bu girişi görmüyorsanız araçların en son sürümü zaten yüklüdür. 

## <a name="use-configuration-files"></a>Yapılandırma dosyalarını kullanma
Visual Studio'da bağlı hizmetler/tablolar/işlem hatları için her ortamda farklı özellik yapılandırmak amacıyla yapılandırma dosyalarını kullanabilirsiniz.

Azure Storage bağlı hizmeti için aşağıdaki JSON tanımını dikkate alın. Data Factory varlıklarını dağıttığınız ortama dayanan accountname ve accountkey farklı değerlerini (Dev/Test/Production) **connectionString** olarak belirtmek için. Her ortam için ayrı bir yapılandırma dosyası kullanarak bu davranışı elde edebilirsiniz.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Yapılandırma dosyası ekleme
Aşağıdaki adımları uygulayarak her ortam için bir yapılandırma dosyası ekleyin:   

1. Visual Studio çözümünüzde Data Factory projenize sağ tıklayın, **Ekle**’nin üzerine gelin ve **Yeni öğe**’ye tıklayın.
2. Soldaki yüklenmiş şablonlar listesinden **Config**’i ve **Yapılandırma Dosyası**’nı seçin ve yapılandırma dosyası için bir **ad** girip **Ekle**’ye tıklayın.

    ![Yapılandırma dosyasını ekleme](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Yapılandırma parametrelerini ve değerlerini aşağıda gösterilen biçimde ekleyin:

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<Azure SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    Bu örnek, Azure Storage bağlı hizmetinin ve Azure SQL bağlı hizmetinin connectionString özelliğini yapılandırır. Belirtilen adın sözdiziminin [JsonPath](https://goessner.net/articles/JsonPath/) olduğuna dikkat edin.   

    JSON’un aşağıdaki kodda gösterilen şekilde bir dizi değere sahip bir özelliği varsa:  

    ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    Özellikleri, aşağıdaki yapılandırma dosyasında gösterildiği gibi (sıfır tabanlı dizin oluşturma kullanın) yapılandırın:

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Boşluklu özellik adları
Özellik adında boşluklar varsa, aşağıdaki örnekte (veritabanı sunucu adı) gösterildiği gibi köşeli ayraç kullanın:

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Yapılandırma kullanarak çözümü dağıtma
Azure Data Factory varlıklarını VS’de dağıttığınızda, bu yayımlama işlemi için kullanmak istediğiniz yapılandırmayı belirtebilirsiniz.

Azure Data Factory projesindeki varlıkları yapılandırma dosyası kullanarak oluşturmak için:   

1. Data Factory projesine sağ tıklayıp, **Öğeleri Yayımla** iletişim kutusunu görüntülemek için **Yayımla**’ya tıklayın.
2. Mevcut bir data factory seçip ya da **Data factory yapılandır** sayfasında bir data factory oluşturulması için değerleri belirtip **İleri**’ye tıklayın.   
3. **Öğeleri Yayımla** sayfasında: **Dağıtım Yapılandırması seç** alanı için kullanılabilir yapılandırmaların açılan listesini görürsünüz.

    ![Yapılandırma dosyası seçme](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Kullanmak istediğiniz **yapılandırma dosyasını** seçip **İleri**’ye tıklayın.
5. **Özet** sayfasında JSON dosyasının adını gördüğünüzü doğrulayıp **İleri**’ye tıklayın.
6. Dağıtım işlemi sona erdikten sonra **Son**’a tıklayın.

Dağıtımı yaptığınızda, yapılandırma dosyasındaki değerler, varlıkların Azure Data Factory hizmetine dağıtılmasından önce JSON dosyalarındaki özelliklerin değerlerini ayarlamak için kullanılır.   

## <a name="use-azure-key-vault"></a>Azure Key Vault kullanma
Bağlantı dizeleri gibi hassas verilerin kod deposuna işlenmesi önerilmez ve genellikle güvenlik ilkesine aykırıdır. Hassas bilgileri Azure Key Vault’ta depolama ve Data Factory varlıklarını yayımlarken bu bilgileri kullanma hakkında bilgi için, GitHub üzerinde [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFSecurePublish) örneğine bakın. Visual Studio için Secure Publish uzantısı, gizli anahtarların Key Vault’ta depolanmasına olanak tanır ve bağlı hizmetler/ dağıtım yapılandırmalarında bunların yalnızca başvuruları belirtilir. Bu başvurular, Azure’da Data Factory varlıkları yayımladığınızda çözümlenir. Bu dosyalar daha sonra herhangi bir gizli anahtar kullanıma sunulmadan kaynak depoya işlenebilir.


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, bir kopyalama işleminde kaynak veri deposu olarak Azure blob depolama alanını ve hedef veri deposu olarak Azure SQL veritabanını kullandınız. Aşağıdaki tabloda, kopyalama etkinliği tarafından kaynaklar ve hedefler olarak desteklenen veri depolarının listesi sağlanmıştır: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Veri deposundan/veri deposuna veri kopyalama hakkında bilgi edinmek için tablodaki veri deposunun bağlantısına tıklayın.
