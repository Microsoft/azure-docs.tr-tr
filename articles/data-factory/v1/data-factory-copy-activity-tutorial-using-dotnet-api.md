---
title: 'Öğretici: .NET API kullanarak Kopyalama Etkinlikli bir işlem hattı oluşturma '
description: Bu öğreticide, .NET API kullanarak Kopyalama Etkinlikli bir Azure Data Factory işlem hattı oluşturursunuz.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 58fc4007-b46d-4c8e-a279-cb9e479b3e2b
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 27bfebf35e2ba2f732068913682e1d16063a1da5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682882"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Öğretici: .NET API kullanarak Kopyalama Etkinlikli bir işlem hattı oluşturma
> [!div class="op_single_selector"]
> * [Genel bakış ve önkoşullar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopyalama Sihirbazı](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager şablonu](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API’si](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz. [kopyalama etkinliği öğreticisi](../quickstart-create-data-factory-dot-net.md). 

Bu makalede, Azure blob depolama alanından Azure SQL veritabanına veri kopyalayan bir işlem hattıyla veri fabrikası oluşturmak için [.NET API](https://portal.azure.com)’yi nasıl kullanacağınızı öğreneceksiniz. Azure Data Factory’yi ilk kez kullanıyorsanız bu öğreticiyi tamamlamadan önce [Azure Data Factory’ye Giriş](data-factory-introduction.md) makalesini okuyun.   

Bu öğreticide, içinde bir etkinlik olan işlem hattı oluşturursunuz: Kopyalama Etkinliği. Kopyalama etkinliği, verileri, desteklenen bir veri deposundan desteklenen bir havuz veri deposuna kopyalar. Kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Etkinlik, çeşitli veri depolama alanları arasında güvenli, güvenilir ve ölçeklenebilir bir yolla veri kopyalayabilen genel olarak kullanılabilir bir hizmet tarafından desteklenir. Kopyalama Etkinliği hakkında daha fazla bilgi için bkz. [Veri Taşıma Etkinlikleri](data-factory-data-movement-activities.md).

Bir işlem hattında birden fazla etkinlik olabilir. Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliğin diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [bir işlem hattında birden fazla etkinlik](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> .NET API’deki Data Factory ile ilgili tüm belgeleri görmek için bkz. [Data Factory .NET API Başvurusu](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1).
> 
> Bu öğreticideki veri işlem hattı, bir kaynak veri deposundaki verileri hedef veri deposuna kopyalar. Azure Data Factory kullanarak verileri dönüştürme hakkında bir öğretici için bkz. [Öğretici: Hadoop kümesi kullanarak verileri dönüştürmek için işlem hattı oluşturma](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Öğreticiye genel bir bakış atmak ve [ön koşul](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) adımlarını tamamlamak için **Öğreticiye Genel Bakış ve Ön Koşullar** bölümündeki adımları tamamlayın.
* Visual Studio 2012 veya 2013 veya 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/)’yı indirip yükleyin
* Azure PowerShell. Bilgisayarınıza Azure PowerShell’i yüklemek için [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-Az-ps) makalesindeki yönergeleri izleyin. Azure PowerShell’i kullanarak bir Azure Active Directory uygulaması oluşturursunuz.

### <a name="create-an-application-in-azure-active-directory"></a>Azure Active Directory’de uygulama oluşturma
Bir Azure Active Directory uygulaması oluşturun, uygulama için bir hizmet sorumlusu oluşturun ve bunu **Data Factory Katılımcısı** rolüne atayın.

1. **PowerShell**’i başlatın.
2. Aşağıdaki komutu çalıştırın ve Azure portalda oturum açmak için kullandığınız kullanıcı adı ve parolayı girin.

    ```powershell
    Connect-AzAccount
    ```
3. Bu hesapla ilgili tüm abonelikleri görmek için aşağıdaki komutu çalıştırın.

    ```powershell
    Get-AzSubscription
    ```
4. Çalışmak isteğiniz aboneliği seçmek için aşağıdaki komutu çalıştırın. **&lt;NameOfAzureSubscription**&gt; değerini Azure aboneliğinizin adıyla değiştirin.

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```

   > [!IMPORTANT]
   > Bu komutun çıktısından **SubscriptionId** ve **TenantId** değerlerin not alın.

5. PowerShell’de aşağıdaki komutu çalıştırarak **ADFTutorialResourceGroup** adlı bir Azure kaynak grubu oluşturun.

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Kaynak grubu zaten varsa bunun güncelleştirileceğini mi (Y) yoksa (N) olarak tutulacağını mı belirtirsiniz.

    Farklı bir kaynak grubu kullanıyorsanız, bu öğreticide kullanılan ADFTutorialResourceGroup yerine kaynak grubunuzun adını kullanmanız gerekir.
6. Bir Azure Active Directory uygulaması oluşturun.

    ```powershell
    $azureAdApplication = New-AzADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
    ```

    Aşağıdaki hatayı alırsanız farklı bir URL belirtip komutu yeniden çalıştırın.
    
    ```powershell
    Another object with the same value for property identifierUris already exists.
    ```
7. AD hizmet sorumlusunu oluşturun.

    ```powershell
    New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Hizmet sorumlusunu **Data Factory Katılımcısı** rolüne ekleyin.

    ```powershell
    New-AzRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Uygulama kimliğini alın.

    ```powershell
    $azureAdApplication 
    ```
    Çıktıdaki uygulama kimliğini (applicationID) not alın.

Bu adımlardan sonra aşağıdaki dört değere sahip olmanız gerekir:

* Kiracı Kimliği
* Abonelik Kimliği
* Uygulama Kimliği
* Parola (ik komutta belirtilir)

## <a name="walkthrough"></a>Kılavuz
1. Visual Studio 2012/2013/2015'i kullanarak bir C# .NET konsol uygulaması oluşturun.
   1. **Visual Studio** 2012/2013/2015’i başlatın.
   2. **Dosya**’ya tıklayın, **Yeni**’nin üzerine gelin ve **Proje**’ye tıklayın.
   3. **Şablonlar**’ı genişletin ve **Visual C#** seçeneğini belirleyin. Bu kılavuzda C# kullanıyor olsanız da dilediğiniz .NET dilini kullanabilirsiniz.
   4. Sağ taraftaki proje türleri listesinden **Konsol Uygulaması**’nı seçin.
   5. Ad için **DataFactoryAPITestApp** değerini girin.
   6. Konum için **C:\ADFGetStarted** yolunu seçin.
   7. Projeyi oluşturmak için **Tamam**'a tıklayın.
2. **Araçlar**'a tıklayın, **NuGet Paket Yöneticisi**'nin üzerine gelin ve ardından **Paket Yöneticisi Konsolu**'na tıklayın.
3. **Paket Yöneticisi Konsolu**'nda şu adımları uygulayın:
   1. Data Factory paketini yüklemek için şu komutu çalıştırın: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Azure Active Directory paketini yüklemek için şu komutu çalıştırın (kodda Active Directory API'sini kullanırsınız): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Aşağıdaki **appSetttings** bölümünü **App.config** dosyasına ekleyin. Bu ayarlar **GetAuthorizationHeader** yardımcı yöntemi tarafından kullanılır.

    **&lt;Application ID&gt;** , **&lt;Password&gt;** , **&lt;Subscription ID&gt;** ve **&lt;tenant ID&gt;** değerlerini kendi değerlerinizle değiştirin.

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```

5. Aşağıdaki **using** bildirimlerini projedeki kaynak dosyasına (Program.cs) ekleyin.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```

6. **DataPipelineManagementClient** sınıfının bir örneğini oluşturan aşağıdaki kodu **Main** yöntemine ekleyin. Bir veri fabrikası, bağlı hizmet, girdi ve çıktı veri kümeleri ve işlem hattı oluşturmak için bu nesneyi kullanırsınız. Çalışma zamanında bir veri kümesinin dilimlerini izlemek için de bu nesneyi kullanırsınız.

    ```csharp
    // create data factory management client
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > **resourceGroupName** değerini Azure kaynak grubunuzun adıyla değiştirin.
   >
   > Veri fabrikasının adını (dataFactoryName) benzersiz olacak şekilde güncelleştirin. Veri fabrikasının adı genel olarak benzersiz olmalıdır. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](data-factory-naming-rules.md) konusuna bakın.

7. Bir **veri fabrikası** oluşturan aşağıdaki kodu **Main** yöntemine ekleyin.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```

    Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattında bir veya daha fazla etkinlik olabilir. Örneğin, verileri bir kaynaktan bir hedef veri deposuna kopyalamak için Kopyalama Etkinliği, giriş verilerini ürün çıkış verilerine dönüştürecek Hive betiğini çalıştırmak için de HDInsight Hive etkinliği. Bu adımda data factory oluşturmayla başlayalım.
8. Bir **Azure Depolama bağlı hizmeti** oluşturan aşağıdaki kodu **Main** yöntemine ekleyin.

   > [!IMPORTANT]
   > **storageaccountname** ve **accountkey** sözcüklerini Azure Depolama hesabınızın adı ve anahtarıyla değiştirin.

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```

    Veri depolarınızı ve işlem hizmetlerinizi veri fabrikasına bağlamak için veri fabrikasında bağlı hizmetler oluşturursunuz. Bu öğreticide, Azure HDInsight veya Azure Data Lake Analytics gibi herhangi bir işlem hizmeti kullanmazsınız. Azure Depolama (kaynak) ve Azure SQL Veritabanı (hedef) türünde iki veri deposu kullanırsınız. 

    Bu nedenle, AzureStorage ve AzureSqlDatabase türlerinde AzureStorageLinkedService ve AzureSqlLinkedService adlı iki bağlı hizmet oluşturursunuz.  

    AzureStorageLinkedService, Azure depolama hesabınızı veri fabrikasına bağlar. Bu depolama hesabı, kapsayıcı oluşturduğunuz ve verileri [ön koşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak yüklediğiniz hesaptır.
9. Bir **Azure SQL bağlı hizmeti** oluşturan aşağıdaki kodu **Main** yöntemine ekleyin.

   > [!IMPORTANT]
   > **servername**, **databasename**, **username** ve **password** sözcüklerini Azure SQL sunucunuzun, veritabanınızın, kullanıcınızın adlarıyla ve parolasıyla değiştirin.

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

    AzureSqlLinkedService, Azure SQL veritabanınızı veri fabrikasına bağlar. Blob depolama alanından kopyalanan veriler bu veritabanında depolanır. Bu veritabanındaki emp tablosunu, [ön koşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak oluşturdunuz.
10. **Girdi ve çıktı veri kümeleri** oluşturan aşağıdaki kodu **Main** yöntemine ekleyin.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "InputDataset";
    string Dataset_Destination = "OutputDataset";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },

                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
                    },
                    Availability = new Availability()
                    {
                        Frequency = SchedulePeriod.Hour,
                        Interval = 1,
                    },
                }
            }
        });
    ```
    
    Önceki adımda, Azure Depolama hesabınızı ve Azure SQL veritabanınızı veri fabrikanıza bağlamak için bağlı hizmetler oluşturdunuz. Bu adımda, sırasıyla AzureStorageLinkedService ve AzureSqlLinkedService tarafından başvurulan veri depolarında depolanan girdi ve çıktı verilerini temsil eden InputDataset ve OutputDataset adlı iki veri kümesini tanımlarsınız.

    Azure depolama bağlı hizmeti, Data Factory hizmetinin Azure depolama hesabınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Giriş blobu veri kümesi (InputDataset) ise kapsayıcıyı ve girdi verilerini içeren klasörü belirtir.  

    Benzer şekilde, Azure SQL Veritabanı bağlı hizmeti, Data Factory hizmetinin Azure SQL veritabanınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Çıktı SQL tablosu veri kümesi (OutputDataset) ise blob depolama alanındaki verilerin kopyalandığı veritabanında tabloyu belirtir.

    Bu adımda, InputDataset adlı bir veri kümesi oluşturursunuz. Bu veri kümesi, AzureStorageLinkedService bağlı hizmetiyle temsil edilen Azure Depolama’daki bir blob kapsayıcısının (adftutorial) kök klasöründe bulunan blob dosyasını (emp.txt) işaret eder. fileName için bir değer belirtmezseniz (veya bu adımı atlarsanız) girdi klasöründe bulunan tüm blob’lardaki veriler hedefe kopyalanır. Bu öğreticide, fileName için bir değer belirtirsiniz.    

    Bu adımda **OutputDataset** adlı bir çıktı veri kümesi oluşturursunuz. Bu veri kümesi, **AzureSqlLinkedService** ile temsil edilen Azure SQL veritabanında bir SQL tablosunu işaret eder.
11. **Bir işlem hattı oluşturan ve işlem hattını etkinleştiren** aşağıdaki kodu **Main** yöntemine ekleyin. Bu adımda, girdi olarak **InputDataset** ve çıktı olarak **OutputDataset** kullanan **kopyalama etkinliği**’ne sahip bir işlem hattı oluşturursunuz.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2017, 5, 11, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = new DateTime(2017, 5, 12, 0, 0, 0, 0, DateTimeKind.Utc);
    string PipelineName = "ADFTutorialPipeline";

    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new PipelineCreateOrUpdateParameters()
        {
            Pipeline = new Pipeline()
            {
                Name = PipelineName,
                Properties = new PipelineProperties()
                {
                    Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                    End = PipelineActivePeriodEndTime,

                    Activities = new List<Activity>()
                    {
                        new Activity()
                        {
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
                                    Name = Dataset_Source
                                }
                            },
                            Outputs = new List<ActivityOutput>()
                            {
                                new ActivityOutput()
                                {
                                    Name = Dataset_Destination
                                }
                            },
                            TypeProperties = new CopyActivity()
                            {
                                Source = new BlobSource(),
                                Sink = new BlobSink()
                                {
                                    WriteBatchSize = 10000,
                                    WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                }
                            }
                        }
                    }
                }
            }
        });
    ```

    Aşağıdaki noktalara dikkat edin:
   
    - Etkinlikler bölümünde, **türü** **Copy** olarak ayarlanmış yalnızca bir etkinlik vardır. Kopyalama etkinliği hakkında daha fazla bilgi için bkz. [veri taşıma etkinlikleri](data-factory-data-movement-activities.md). Data Factory çözümlerinde, [veri dönüştürme etkinliklerini](data-factory-data-transformation-activities.md) de kullanabilirsiniz.
    - Etkinlik girdisi **InputDataset** olarak, etkinlik çıktısı ise **OutputDataset** olarak ayarlanmıştır. 
    - **typeProperties** bölümünde **BlobSource** kaynak türü, **SqlSink** de havuz türü olarak belirtilir. Kaynak ve havuz olarak kopyalama etkinliği tarafından desteklenen veri depolarının eksiksiz listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Kaynak/havuz olarak desteklenen belirli bir veri deposunu nasıl kullanacağınızı öğrenmek için tablodaki bağlantıya tıklayın.  
   
    Şu anda zamanlamayı çıktı veri kümesi yürütmektedir. Bu öğreticide, çıktı veri kümesi saatte bir dilim oluşturacak şekilde yapılandırılır. İşlem hattının başlangıç zamanı ve bitiş zamanı arasında bir gün, yani 24 saat vardır. Bu nedenle, işlem hattı çıktı veri kümesinden 24 dilim oluşturur.
12. Çıktı veri kümesinin veri diliminin durumunu almak için aşağıdaki kodu **Main** yöntemine ekleyin. Bu örnekte beklenen yalnızca bir dilim vardır.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;

    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");        
        // wait before the next status check
        Thread.Sleep(1000 * 12);

        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });

        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```

13. Bir veri diliminin çalıştırma ayrıntılarını almak için aşağıdaki kodu **Main** yöntemine ekleyin.

    ```csharp
    Console.WriteLine("Getting run details of a data slice");

    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();

    var datasliceRunListResponse = client.DataSliceRuns.List(
            resourceGroupName,
            dataFactoryName,
            Dataset_Destination,
            new DataSliceRunListParameters()
            {
                DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
            }
        );

    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }

    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```

14. **Main** yöntemi tarafından kullanılan aşağıdaki yardımcı yöntemini **Program** sınıfına ekleyin.

    > [!NOTE] 
    > Aşağıdaki kodu kopyalayıp yapıştırırken kopyalanan kodun Main yöntemiyle aynı düzeyde olduğundan emin olun.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. Çözüm Gezgini’nde projeyi (DataFactoryAPITestApp) genişletin, **Başvurular**’a sağ tıklayın ve **Başvuru Ekle**’ye tıklayın. **System.Configuration** derlemesinin onay kutusunu işaretleyin. **Tamam**'a tıklayın.
16. Konsol uygulamasını derleyin. Menüde **Derle**’ye tıklayın ve **Çözümü Derle**’ye tıklayın.
17. Azure blob depolamanızdaki **adftutorial** kapsayıcısında en az bir dosya olduğunu onaylayın. Aksi takdirde, Not Defteri’nde aşağıdaki içeriklerle **Emp.txt** dosyası oluşturun ve dosyayı adftutorial kapsayıcısına yükleyin.

    ```
    John, Doe
    Jane, Doe
    ```
18. Menüden **Hata Ayıkla** -> **Hata Ayıklamayı Başlat**’a tıklayarak örneği çalıştırın. **Getting run details of a data slice** iletisini gördüğünüzde birkaç dakika bekleyin ve **ENTER** tuşuna basın.
19. Azure portalı kullanarak **APITutorialFactory** veri fabrikasının aşağıdaki yapıtlarla birlikte oluşturulduğunu doğrulayın:
    * Bağlı hizmet: **LinkedService_AzureStorage**
    * Veri kümesi: **InputDataset** ve **OutputDataset**.
    * İşlem hattı: **PipelineBlobSample**
20. Belirtilen Azure SQL veritabanındaki **emp** tablosunda, iki çalışan kaydının oluşturulduğunu doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar
.NET API’deki Data Factory ile ilgili tüm belgeleri görmek için bkz. [Data Factory .NET API Başvurusu](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1).

Bu öğreticide, bir kopyalama işleminde kaynak veri deposu olarak Azure blob depolama alanını ve hedef veri deposu olarak Azure SQL veritabanını kullandınız. Aşağıdaki tabloda, kopyalama etkinliği tarafından kaynaklar ve hedefler olarak desteklenen veri depolarının listesi sağlanmıştır: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Veri deposundan/veri deposuna veri kopyalama hakkında bilgi edinmek için tablodaki veri deposunun bağlantısına tıklayın.

