---
title: 'Azure Blob depolamadan SQL veritabanına veri kopyalama '
description: Bu öğretici, Azure Blob Depolama alanından Azure SQL Veritabanına veri kopyalamaya ilişkin adım adım yönergeler sağlar.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: 96b1e5a9633ee141fd4aa369468a8866d87f27f2
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683656"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure Blob’dan Azure SQL Veritabanına veri kopyalama

Bu öğreticide, Azure Blob Depolama alanından Azure SQL Veritabanına veri kopyalayan bir Data Factory işlem hattı oluşturacaksınız. Bu öğreticideki yapılandırma düzeni, dosya tabanlı bir veri deposundan ilişkisel bir veri deposuna kopyalama için geçerlidir. Kaynak ve havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Bu öğreticide aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Azure Depolama ve Azure SQL Veritabanı bağlı hizmeti oluşturma.
> * Azure Blob ve Azure SQL Veritabanı veri kümeleri oluşturma.
> * Kopyalama etkinliği içeren bir işlem hattı oluşturma.
> * Bir işlem hattı çalıştırması başlatma.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

Bu öğreticide .NET SDK kullanılır. Azure Data Factory ile etkileşim kurmak için başka mekanizmalar kullanabilirsiniz; "Hızlı Başlangıçlar" altındaki örneklere bakın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar

* **Azure Depolama hesabı**. Blob depolama alanını **kaynak** veri deposu olarak kullanabilirsiniz. Azure depolama hesabınız yoksa, oluşturma adımları için [Depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md) makalesine bakın.
* **Azure SQL Veritabanı**. Veritabanını **havuz** veri deposu olarak kullanabilirsiniz. Azure SQL Veritabanınız yoksa, oluşturma adımları için [Azure SQL veritabanı oluşturma](../sql-database/sql-database-get-started-portal.md) makalesine bakın.
* **Visual Studio** 2015 veya 2017. Bu makaledeki kılavuzda Visual Studio 2017 kullanılır.
* **[Azure .NET SDK](https://azure.microsoft.com/downloads/)** indirip yükleyin.
* **Azure Active Directory’de** [bu yönergeyi](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) izleyerek bir uygulama oluşturun. Sonraki adımlarda kullandığınız şu değerleri not edin: **uygulama kimliği**, **kimlik doğrulama anahtarı** ve **kiracı kimliği**. Aynı makalede bulunan yönergeleri izleyerek uygulamayı "**Katkıda bulunan**" rolüne atayın.

### <a name="create-a-blob-and-a-sql-table"></a>Bir blob ve SQL tablosu oluşturma

Aşağıdaki adımları izleyerek Azure Blob ve Azure SQL Veritabanınızı öğreticiye hazırlayın:

#### <a name="create-a-source-blob"></a>Kaynak blob oluşturma

1. Not Defteri'ni başlatın. Aşağıdaki metni kopyalayıp diskinizde **inputEmp.txt** dosyası olarak kaydedin.

    ```
    John|Doe
    Jane|Doe
    ```

2. [Azure Depolama Gezgini](https://storageexplorer.com/) gibi araçları **adfv2tutorial** kapsayıcısı oluşturmak ve **inputEmp.txt** dosyasını kapsayıcıya yüklemek için kullanın.

#### <a name="create-a-sink-sql-table"></a>Havuz SQL tablosu oluşturma

1. Azure SQL Veritabanınızda **dbo.emp** tablosu oluşturmak için aşağıdaki SQL betiğini kullanın.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Azure hizmetlerinin SQL sunucusuna erişmesine izin ver. Data Factory hizmetinin Azure SQL sunucunuza veri yazabilmesi için Azure SWL sunucunuza ait **Azure hizmetlerine erişime izin ver** ayarının **AÇIK** olduğundan emin olun. Bu ayarı doğrulamak ve etkinleştirmek için aşağıdaki adımları uygulayın:

    1. Soldaki **Diğer hizmetler** hub’ına ve sonra **SQL sunucuları**’na tıklayın.
    2. Sunucunuzu seçin ve **AYARLAR** altındaki **Güvenlik Duvarı**’na tıklayın.
    3. **Güvenlik Duvarı ayarları** sayfasında **Azure hizmetlerine erişime izin ver** için **AÇIK**’a tıklayın.


## <a name="create-a-visual-studio-project"></a>Visual Studio projesi oluşturma

Visual Studio 2015/2017'yi kullanarak bir C# .NET konsol uygulaması oluşturun.

1. **Visual Studio**’yu başlatın.
2. **Dosya**’ya tıklayın, **Yeni**’nin üzerine gelin ve **Proje**’ye tıklayın.
3. Sağ taraftaki proje türleri listesinden **Visual C#**  -> **Konsol Uygulaması (.NET Framework)** öğesini seçin. .NET sürüm 4.5.2 veya üzeri gereklidir.
4. Ad olarak **ADFv2Tutorial** girin.
5. Projeyi oluşturmak için **Tamam**'a tıklayın.

## <a name="install-nuget-packages"></a>NuGet paketlerini yükleme

1. **Araçlar** -> **NuGet Paket Yöneticisi** -> **Paket Yöneticisi Konsolu**’na tıklayın.
2. **Paket Yöneticisi konsolunda**, paketleri yüklemek için aşağıdaki komutları çalıştırın. Ayrıntılarla birlikte [Microsoft. Azure. Management. DataFactory NuGet paketini](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) inceleyin.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Veri fabrikası istemcisi oluşturma

1. **Program.cs** dosyasını açın ve ad alanlarına başvurular eklemek için aşağıdaki deyimleri ekleyin.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

    
2. Aşağıdaki kodu, değişkenleri ayarlayan **Main** yöntemine ekleyin. Yer tutucuları kendi değerlerinizle değiştirin. Data Factory'nin kullanılabileceği Azure bölgelerinin bir listesi için bir sonraki sayfada ilgilendiğiniz bölgeleri seçin ve **Analytics**'i genişleterek **Data Factory**: [Products available by region](https://azure.microsoft.com/global-infrastructure/services/) (Bölgeye göre kullanılabilir durumdaki ürünler) bölümünü bulun. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Aşağıdaki kodu **DataFactoryManagementClient** sınıfının bir örneğini oluşturan **Main** yöntemine ekleyin. Veri fabrikası, bağlı hizmet, veri kümeleri ve işlem hattı oluşturmak için bu nesneyi kullanırsınız. Bu nesneyi ayrıca işlem hattı ayrıntılarını izlemek için kullanabilirsiniz.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Aşağıdaki kodu **veri fabrikası** oluşturan **Main** yöntemine ekleyin.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()

};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma

Bu öğreticide sırasıyla kaynak ve havuz için iki bağlı hizmet oluşturacaksınız:

### <a name="create-an-azure-storage-linked-service"></a>Azure Depolama bağlı hizmeti oluşturma

Aşağıdaki kodu bir **Azure Depolama bağlı hizmeti** oluşturan **Main** yöntemine ekleyin. Desteklenen özellikler ve ayrıntılar hakkında daha fazla bilgi için [Azure Blob bağlı hizmeti özellikleri](connector-azure-blob-storage.md#linked-service-properties) makalesine bakın.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Veritabanı bağlı hizmeti oluşturma

Aşağıdaki kodu bir **Azure SQL Veritabanı bağlı hizmeti** oluşturan **Main** yöntemine ekleyin. Desteklenen özellikler ve ayrıntılar hakkında daha fazla bilgi için [Azure SQL Veritabanı bağlı hizmeti özellikleri](connector-azure-sql-database.md#linked-service-properties) makalesine bakın.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>Veri kümeleri oluşturun

Bu bölümde biri kaynak, diğeri havuz için olmak üzere iki veri kümesi oluşturacaksınız. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Kaynak Azure Blob için veri kümesi oluşturma

Aşağıdaki kodu bir **Azure blob veri kümesi** oluşturan **Main** yöntemine ekleyin. Desteklenen özellikler ve ayrıntılar hakkında daha fazla bilgi için [Azure Blob veri kümesi özellikleri](connector-azure-blob-storage.md#dataset-properties) makalesine bakın.

Azure Blob’da kaynak verilerini temsil eden bir veri kümesi tanımlayın. Bu Blob veri kümesi, önceki adımda oluşturduğunuz Azure Depolama bağlı hizmetini ifade eder:

- Blob kopyalama kaynağı: **FolderPath** ve **FileName**;
- İçerik ayrıştırmayı gösteren blob biçimi: **TextFormat** ve ayarları (örneğin, sütun sınırlayıcısı).
- Bu örnekte havuz SQL tablosuyla eşlenen sütun adları ve veri türleri dahil veri yapısı.

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Havuz Azure SQL Veritabanı için veri kümesi oluşturma

Aşağıdaki kodu bir **Azure SQL Veritabanı veri kümesi** oluşturan **Main** yöntemine ekleyin. Desteklenen özellikler ve ayrıntılar hakkında daha fazla bilgi için [Azure SQL Veritabanı veri kümesi özellikleri](connector-azure-sql-database.md#dataset-properties) makalesine bakın.

Azure SQL Veritabanı’nda havuz verilerini temsil eden bir veri kümesi tanımlayın. Bu veri kümesi, önceki adımda oluşturduğunuz Azure SQL Veritabanı bağlı hizmetini ifade eder. Ayrıca, kopyalanan verileri tutan SQL tablosunu belirtir. 

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

Aşağıdaki kodu **bir kopyalama etkinliği ile işlem hattı** oluşturan **Main** yöntemine ekleyin. Bu öğreticide işlem hattı, kaynak olarak Blob veri kümesini ve havuz olarak SQL veri kümesini alan kopyalama etkinliği adlı bir etkinlik içerir. Kopyalama etkinliğinin ayrıntıları için [Kopyalama Etkinliğine Genel Bakış](copy-activity-overview.md) bölümünden daha fazla bilgi alabilirsiniz.

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>İşlem hattı çalıştırması oluşturma

Aşağıdaki kodu **bir işlem hattı çalıştırması tetikleyen** **Main** yöntemine ekleyin.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>İşlem hattı çalıştırmasını izleme

1. İşlem hattı çalıştırmasını veri kopyalama işlemi tamamlanıncaya kadar sürekli olarak izlemek için aşağıdaki kodu **Main** yöntemine ekleyin.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Aşağıdaki kodu, okunan/yazılan veri boyutu gibi kopyalama etkinliği çalıştırma ayrıntılarını alan **Main** yöntemine ekleyin.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);
    
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Kodu çalıştırma

Uygulamayı derleyip başlatın, ardından işlem hattı yürütmesini doğrulayın.

Konsol; veri fabrikası, bağlı hizmet, veri kümeleri, işlem hattı ve işlem hattı çalıştırmasının ilerleme durumunu yazdırır. Daha sonra işlem hattı çalıştırma durumunu denetler. Okunan/yazılan veri boyutunu içeren kopyalama etkinliği ayrıntılarını görene kadar bekleyin. Ardından, SSMS (SQL Server Management Studio) veya Visual Studio gibi araçlar kullanarak hedef Azure SQL Veritabanınıza bağlanın ve verilerin belirttiğiniz tabloya kopyalanıp kopyalanmadığını denetleyin.

### <a name="sample-output"></a>Örnek çıktı

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
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
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>Sonraki adımlar

Bu örnekteki işlem hattı, verileri bir konumdan Azure blob depolama alanındaki başka bir konuma kopyalar. Şunları öğrendiniz: 

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Azure Depolama ve Azure SQL Veritabanı bağlı hizmeti oluşturma.
> * Azure Blob ve Azure SQL Veritabanı veri kümeleri oluşturma.
> * Kopyalama etkinliği içeren bir işlem hattı oluşturma.
> * Bir işlem hattı çalıştırması başlatma.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.


Şirket içinden buluta veri kopyalama hakkında bilgi edinmek için aşağıdaki öğreticiye geçin: 

> [!div class="nextstepaction"]
>[Buluttan şirket içine veri kopyalama](tutorial-hybrid-copy-powershell.md)
