---
title: Azure .NET SDK kullanarak veri işlem hatları oluşturma
description: Azure veri fabrikalarını Data Factory SDK kullanarak programlı bir şekilde oluşturmayı, izlemeyi ve yönetmeyi öğrenin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: cabeb6314c16aa5bc37f1139954f8829a37af5bb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682773"
---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Azure Data Factory .NET SDK kullanarak Azure veri fabrikaları oluşturun, izleyin ve yönetin
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz. [kopyalama etkinliği öğreticisi](../quickstart-create-data-factory-dot-net.md). 

## <a name="overview"></a>Genel Bakış
Data Factory .NET SDK kullanarak Azure veri fabrikalarını programlı bir şekilde oluşturabilir, izleyebilir ve yönetebilirsiniz. Bu makalede, bir veri fabrikası oluşturup izleyen örnek bir .NET konsol uygulaması oluşturmak için izleyebileceğiniz bir izlenecek yol yer almaktadır. 

> [!NOTE]
> Bu makale, Data Factory .NET API’nin tamamını kapsamaz. Data Factory için .NET API hakkında kapsamlı belgeler için bkz. [Data Factory .NET API başvurusu](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1) . 

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Visual Studio 2012 veya 2013 veya 2015
* [Azure .NET SDK 'sını](https://azure.microsoft.com/downloads/)indirip yükleyin.
* Azure PowerShell. Bilgisayarınıza Azure PowerShell’i yüklemek için [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/overview) makalesindeki yönergeleri izleyin. Azure PowerShell’i kullanarak bir Azure Active Directory uygulaması oluşturursunuz.

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
    $azureAdApplication = New-AzADApplication -DisplayName "ADFDotNetWalkthroughApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfdotnetwalkthroughapp.org/example" -Password "Pass@word1"
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
İzlenecek yolda, kopyalama etkinliği içeren bir işlem hattı ile veri fabrikası oluşturacaksınız. Kopyalama etkinliği, verileri Azure Blob depolama alanındaki bir klasörden aynı BLOB depolama alanındaki başka bir klasöre kopyalar. 

Kopyalama Etkinliği, Azure Data Factory’de veri hareketini gerçekleştirir. Etkinlik, çeşitli veri depolama alanları arasında güvenli, güvenilir ve ölçeklenebilir bir yolla veri kopyalayabilen genel olarak kullanılabilir bir hizmet tarafından desteklenir. Kopyalama etkinliği hakkında ayrıntılı bilgi için [Veri Taşıma Etkinlikleri](data-factory-data-movement-activities.md) makalesine bakın.

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
4. Projedeki **app. config** dosyasının içeriğini aşağıdaki içerikle değiştirin: 
    
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
5. App. config dosyasında **&lt;uygulama kimliği&gt;** , **&lt;parola&gt;** , **&lt;abonelik KIMLIĞI&gt;** ve **&lt;Kiracı kimliği&gt;** değerlerini kendi değerlerinizle güncelleştirin.
6. Aşağıdaki **using** deyimlerini projedeki **program.cs** dosyasına ekleyin.

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

    //IMPORTANT: specify the name of Azure resource group here
    string resourceGroupName = "ADFTutorialResourceGroup";

    //IMPORTANT: the name of the data factory must be globally unique.
    // Therefore, update this value. For example:APITutorialFactory05122017
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > **resourceGroupName** değerini Azure kaynak grubunuzun adıyla değiştirin. [New-AzureResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini kullanarak bir kaynak grubu oluşturabilirsiniz.
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
9. **Girdi ve çıktı veri kümeleri** oluşturan aşağıdaki kodu **Main** yöntemine ekleyin.

    Giriş blobu için **FolderPath** , **adföğreticisi** olarak ayarlanır/burada **adföğreticisi** blob depolamadaki kapsayıcının adıdır. Bu kapsayıcı Azure Blob depolamada yoksa, şu ada sahip bir kapsayıcı oluşturun: **adföğreticisi** ve kapsayıcıya bir metin dosyası yükleyin.

    Çıkış blobunun FolderPath değeri: **adföğreticisi/apifactor, put/{Slice}** , **dilimin** , her **dilimin (başlangıç** tarihi-saati) değerine göre dinamik olarak hesaplandığı bir değere ayarlanır.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
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
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
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
10. **Bir işlem hattı oluşturan ve işlem hattını etkinleştiren** aşağıdaki kodu **Main** yöntemine ekleyin. Bu işlem hattının **BlobSource**’u bir kaynak olarak, **BlobSink**’i ise bir havuz olarak alan bir **CopyActivity** etkinliği vardır.

    Kopyalama Etkinliği, Azure Data Factory’de veri hareketini gerçekleştirir. Etkinlik, çeşitli veri depolama alanları arasında güvenli, güvenilir ve ölçeklenebilir bir yolla veri kopyalayabilen genel olarak kullanılabilir bir hizmet tarafından desteklenir. Kopyalama etkinliği hakkında ayrıntılı bilgi için [Veri Taşıma Etkinlikleri](data-factory-data-movement-activities.md) makalesine bakın.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
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
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput()
                {
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
    
                },
            }
        }
    });
    ```
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
13. **(isteğe bağlı)** **Ana** yönteme bir veri diliminin çalıştırma ayrıntılarını almak için aşağıdaki kodu ekleyin.

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
        });
    
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
14. **Main** yöntemi tarafından kullanılan aşağıdaki yardımcı yöntemini **Program** sınıfına ekleyin. Bu yöntem, Azure portal 'de oturum açmak için kullandığınız **Kullanıcı adı** ve **parola** sağlamanıza olanak tanıyan bir iletişim kutusu açılır.

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

15. Çözüm Gezgini, projeyi genişletin: **datafactoryapıtestapp**, **Başvurular**' a sağ tıklayın ve **Başvuru Ekle**' ye tıklayın. `System.Configuration` derlemesinin onay kutusunu seçin ve **Tamam**' a tıklayın.
15. Konsol uygulamasını derleyin. Menüde **Derle**’ye tıklayın ve **Çözümü Derle**’ye tıklayın.
16. Azure Blob depolamada adföğreticisi kapsayıcısında en az bir dosya olduğunu doğrulayın. Aksi takdirde, aşağıdaki içerikle birlikte Notepad 'de bir. txt dosyası oluşturun ve bunu adföğreticisi kapsayıcısına yükleyin.

    ```
    John, Doe
    Jane, Doe
    ```
17. Menüden **Hata Ayıkla** -> **Hata Ayıklamayı Başlat**’a tıklayarak örneği çalıştırın. **Getting run details of a data slice** iletisini gördüğünüzde birkaç dakika bekleyin ve **ENTER** tuşuna basın.
18. Azure portalı kullanarak **APITutorialFactory** veri fabrikasının aşağıdaki yapıtlarla birlikte oluşturulduğunu doğrulayın:
    * Bağlı hizmet: **AzureStorageLinkedService**
    * Veri kümesi: **DatasetBlobSource** ve **DatasetBlobDestination**.
    * İşlem hattı: **PipelineBlobSample**
19. **Adföğreticisi** kapsayıcısındaki **apifactorsıput** klasöründe bir çıktı dosyası oluşturulduğunu doğrulayın.

## <a name="get-a-list-of-failed-data-slices"></a>Başarısız veri dilimlerinin listesini al 

```csharp
// Parse the resource path
var ResourceGroupName = "ADFTutorialResourceGroup";
var DataFactoryName = "DataFactoryAPITestApp";

var parameters = new ActivityWindowsByDataFactoryListParameters(ResourceGroupName, DataFactoryName);
parameters.WindowState = "Failed";
var response = dataFactoryManagementClient.ActivityWindows.List(parameters);
do
{
    foreach (var activityWindow in response.ActivityWindowListResponseValue.ActivityWindows)
    {
        var row = string.Join(
            "\t",
            activityWindow.WindowStart.ToString(),
            activityWindow.WindowEnd.ToString(),
            activityWindow.RunStart.ToString(),
            activityWindow.RunEnd.ToString(),
            activityWindow.DataFactoryName,
            activityWindow.PipelineName,
            activityWindow.ActivityName,
            string.Join(",", activityWindow.OutputDatasets));
        Console.WriteLine(row);
    }

    if (response.NextLink != null)
    {
        response = dataFactoryManagementClient.ActivityWindows.ListNext(response.NextLink, parameters);
    }
    else
    {
        response = null;
    }
}
while (response != null);
```

## <a name="next-steps"></a>Sonraki adımlar
Verileri bir Azure Blob depolama alanından Azure SQL veritabanına kopyalayan .NET SDK kullanarak bir işlem hattı oluşturmak için aşağıdaki örneğe bakın: 

- [Blob depolamadan SQL veritabanına veri kopyalamak için bir işlem hattı oluşturma](data-factory-copy-activity-tutorial-using-dotnet-api.md)
