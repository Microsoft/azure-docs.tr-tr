---
title: Bir Azure Data Factory işlem hattında özel etkinlikler kullanma
description: Özel etkinlikler oluşturmayı ve bunları bir Azure Data Factory işlem hattında kullanmayı öğrenin.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 698ca6736af86358de13f6deae8f1e2dba92f46e
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990661"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Bir Azure Data Factory işlem hattında özel etkinlikler kullanma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-use-custom-activities.md)
> * [Sürüm 2 (geçerli sürüm)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de özel etkinlikler](../transform-data-using-dotnet-custom-activity.md).

Azure Data Factory ardışık düzeninde kullanabileceğiniz iki tür etkinlik vardır.

- Verileri [, desteklenen kaynak ve havuz veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats)arasında taşımak Için [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) .
- Azure HDInsight, Azure Batch ve Azure Machine Learning gibi işlem hizmetlerini kullanarak verileri dönüştürmek için [veri dönüştürme etkinlikleri](data-factory-data-transformation-activities.md) .

Data Factory desteklemediği bir veri deposuna/veritabanından veri taşımak için kendi veri taşıma mantığınızla özel bir **etkinlik** oluşturun ve etkinliği bir işlem hattında kullanın. Benzer şekilde, Data Factory tarafından desteklenmeyen bir şekilde verileri dönüştürmek/işlemek için kendi veri dönüştürme mantığınızla özel bir etkinlik oluşturun ve etkinliği bir işlem hattında kullanın.

Özel bir etkinliği, sanal makinelerin **Azure Batch** havuzunda çalışacak şekilde yapılandırabilirsiniz. Azure Batch kullanırken, yalnızca var olan bir Azure Batch havuzu kullanabilirsiniz.

Aşağıdaki izlenecek yol, özel bir .NET etkinliği oluşturmaya ve bir işlem hattındaki özel etkinliği kullanmaya yönelik adım adım yönergeler sağlar. İzlenecek yol **Azure Batch** bağlı bir hizmet kullanır.

> [!IMPORTANT]
> - Şirket içi veri kaynaklarına erişmek için özel etkinlikten bir Veri Yönetimi ağ geçidi kullanılması mümkün değildir. Şu anda [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) , Data Factory içindeki kopyalama etkinliği ve saklı yordam etkinliğini destekler.

## <a name="walkthrough-create-a-custom-activity"></a>İzlenecek yol: özel etkinlik oluşturma
### <a name="prerequisites"></a>Önkoşullar
* Visual Studio 2012/2013/2015/2017
* [Azure .NET SDK](https://azure.microsoft.com/downloads/)’yı indirip yükleyin

### <a name="azure-batch-prerequisites"></a>Azure Batch önkoşulları
İzlenecek yolda, işlem kaynağı olarak Azure Batch kullanarak özel .NET etkinliklerinizi çalıştırırsınız. **Azure Batch**, büyük ölçekli paralel ve yüksek performanslı bilgi işlem (HPC) uygulamalarını bulutta verimli bir şekilde çalıştırmanızı sağlayan bir platform hizmetidir. Azure Batch, yönetilen bir **sanal makine koleksiyonunda**çalışacak işlem yoğunluğu olan işleri zamanlar ve işlerin ihtiyaçlarını karşılamak için işlem kaynaklarını otomatik olarak ölçeklendirebilir. Azure Batch hizmetine ayrıntılı bir genel bakış için [Azure Batch temel bilgiler][batch-technical-overview] makalesine bakın.

Öğretici için, VM havuzu ile bir Azure Batch hesabı oluşturun. Adımlar aşağıdaki gibidir:

1. [Azure Portal](https://portal.azure.com)kullanarak bir **Azure Batch hesabı** oluşturun. Yönergeler için [Azure Batch hesap oluşturma ve yönetme][batch-create-account] makalesine bakın.
2. Azure Batch hesap adı, hesap anahtarı, URI ve havuz adı ' na göz önüne alın. Azure Batch bağlı bir hizmet oluşturmak için bunlara ihtiyacınız vardır.
    1. Azure Batch hesabının giriş sayfasında, aşağıdaki biçimde bir **URL** görürsünüz: `https://myaccount.westus.batch.azure.com`. Bu örnekte, **myaccount** Azure Batch hesabının adıdır. Bağlı hizmet tanımında kullandığınız URI, hesap adı olmayan URL 'dir. Örneğin: `https://<region>.batch.azure.com`.
    2. Sol menüdeki **anahtarlar** ' a tıklayın ve **birincil erişim anahtarı**' nı kopyalayın.
    3. Mevcut bir havuzu kullanmak için menüdeki **havuzlar** ' a tıklayın ve havuzun **kimliğini** aklınızda edin. Mevcut bir havuzunuz yoksa, sonraki adıma geçin.
2. **Azure Batch havuzu**oluşturun.

   1. [Azure Portal](https://portal.azure.com), sol menüden **gözatıp** ' ye tıklayın ve **Batch hesapları**' na tıklayın.
   2. **Batch hesabı** dikey penceresini açmak için Azure Batch hesabınızı seçin.
   3. **Havuzlar** kutucuğuna tıklayın.
   4. **Havuzlar** dikey penceresinde, bir havuz eklemek için araç çubuğundaki Ekle düğmesine tıklayın.
      1. Havuz için bir KIMLIK girin (havuz KIMLIĞI). **Havuzun kimliğini**aklınızda yapın; Data Factory çözümü oluştururken buna ihtiyacınız vardır.
      2. Işletim sistemi ailesi ayarı için **Windows Server 2012 R2** 'yi belirtin.
      3. **Düğüm fiyatlandırma katmanını**seçin.
      4. **Hedef adanmış** ayar için **2** değerini girin.
      5. **Düğüm başına en fazla görev** ayarı için **2** değerini girin.
   5. Havuzu oluşturmak için **Tamam**'a tıklayın.
   6. Havuzun **kimliğini** aklınızda edin.

### <a name="high-level-steps"></a>Üst düzey adımlar
Bu izlenecek yolun bir parçası olarak gerçekleştirdiğiniz iki üst düzey adım aşağıda verilmiştir:

1. Basit veri dönüştürme/işleme mantığını içeren özel bir etkinlik oluşturun.
2. Özel etkinliği kullanan bir işlem hattı ile Azure Veri Fabrikası oluşturun.

### <a name="create-a-custom-activity"></a>Özel etkinlik oluşturma
.NET özel etkinliği oluşturmak için, bu **ıdotnetactivity** arabirimini uygulayan bir sınıf içeren bir **.NET sınıf kitaplığı** projesi oluşturun. Bu arabirimin yalnızca bir yöntemi vardır: [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) ve imzası:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

Yöntemi dört parametre alır:

- **Linkedservices**. Bu özellik, etkinlik için giriş/çıkış veri kümeleri tarafından başvurulan veri deposu bağlı hizmetlerinin sıralanabilir bir listesidir.
- **veri kümeleri**. Bu özellik, etkinlik için giriş/çıkış veri kümelerinin sıralanabilir bir listesidir. Bu parametreyi, girdi ve çıktı veri kümeleri tarafından tanımlanan konumları ve şemaları almak için kullanabilirsiniz.
- **etkinlik**. Bu özellik geçerli etkinliği temsil eder. Özel etkinlikle ilişkili genişletilmiş özelliklere erişmek için kullanılabilir. Ayrıntılar için bkz. [genişletilmiş özelliklere erişme](#access-extended-properties) .
- **günlükçü**. Bu nesne, işlem hattının Kullanıcı günlüğünde yüzey olan hata ayıklama açıklamalarını yazmanızı sağlar.

Yöntemi, gelecekte özel etkinlikleri zincirlemek için kullanılabilecek bir sözlük döndürür. Bu özellik henüz uygulanmadı, bu nedenle yöntemden boş bir sözlük döndürün.

### <a name="procedure"></a>Yordam
1. **.NET sınıf kitaplığı** projesi oluşturun.
   <ol type="a">
     <li>Visual Studio 'Yu başlatın.</li>
     <li><b>Dosya</b>’ya tıklayın, <b>Yeni</b>’nin üzerine gelin ve <b>Proje</b>’ye tıklayın.</li>
     <li><b>Şablonlar</b>’ı genişletin ve <b>Visual C#</b> seçeneğini belirleyin. Bu kılavuzda kullanın C#, ancak özel etkinlik geliştirmek için herhangi bir .net dili kullanabilirsiniz.</li>
     <li>Sağ taraftaki proje türleri listesinden <b>sınıf kitaplığı</b> ' nı seçin. Visual Studio 'da <b>Sınıf Kitaplığı ' nı (.NET Framework)</b> seçin </li>
     <li><b>Ad</b>için <b>MyDotNetActivity</b> girin.</li>
     <li><b>Konum</b>için <b>C:\adfgetstarted</b> öğesini seçin.</li>
     <li>Projeyi oluşturmak için <b>Tamam</b>'a tıklayın.</li>
   </ol>

2. **Araçlar**'a tıklayın, **NuGet Paket Yöneticisi**'nin üzerine gelin ve ardından **Paket Yöneticisi Konsolu**'na tıklayın.

3. Paket Yöneticisi konsolunda, **Microsoft. Azure. Management. DataFactory**'yi içeri aktarmak için aşağıdaki komutu yürütün.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. **Azure Storage** NuGet paketini projeye aktarın.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Data Factory hizmet başlatıcısı, WindowsAzure. Storage 'un 4,3 sürümünü gerektirir. Özel etkinlik projenizde daha sonraki bir Azure depolama derlemesi sürümüne başvuru eklerseniz, etkinlik yürütüldüğünde bir hata görürsünüz. Hatayı gidermek için bkz. [AppDomain yalıtım](#appdomain-isolation) bölümü.
5. Aşağıdaki **using** deyimlerini projedeki kaynak dosyaya ekleyin.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. **Ad alanının** adını **MyDotNetActivityNS**olarak değiştirin.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Sınıfın adını **MyDotNetActivity** olarak değiştirin ve aşağıdaki kod parçacığında gösterildiği gibi **ıdotnetactivity** arabiriminden türetirsiniz:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. **Idotnetactivity** arabiriminin **Execute** yöntemini **MyDotNetActivity** sınıfına uygulayın ve aşağıdaki örnek kodu yöntemine kopyalayın.

    Aşağıdaki örnek, bir veri dilimiyle ilişkilendirilen her Blobun arama teriminin ("Microsoft") oluşum sayısını sayar.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.
    /// </summary>

    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

        // to log information, use the logger object
        // log all extended properties
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }

        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;

        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;

        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);

        // get the first Azure Storage linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;

        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;

        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.

        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

        // initialize the continuation token before using it in the do-while loop.
        BlobContinuationToken continuationToken = null;
        do
        {   // get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                     true,
                                     BlobListingDetails.Metadata,
                                     null,
                                     continuationToken,
                                     null,
                                     null);

            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
            // with the data slice. definition of the method is shown in the next step.

            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

        } while (continuationToken != null);

        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;

        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path
        logger.Write("Writing blob to the folder: {0}", folderPath);

        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);

        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.

        return new Dictionary<string, string>();
    }
    ```
9. Aşağıdaki yardımcı yöntemleri ekleyin:

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>

    private static string GetFolderPath(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the folder path found in the type properties
        return blobDataset.FolderPath;
    }

    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>

    private static string GetFileName(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>

    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
        string output = string.Empty;
        logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
        foreach (IListBlobItem listBlobItem in Bresult.Results)
        {
            CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
            if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
            {
                string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                logger.Write("input blob text: {0}", blobText);
                string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                var matchQuery = from word in source
                                 where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                 select word;
                int wordCount = matchQuery.Count();
                output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
            }
        }
        return output;
    }
    ```

    GetFolderPath yöntemi, DataSet 'in gösterdiği klasörün yolunu döndürür ve GetFileName yöntemi, veri kümesinin işaret ettiği blob/dosyanın adını döndürür. FolderPath, {Year}, {month}, {Day} vb. gibi değişkenler kullanarak tanımlıyorsa, yöntemi çalışma zamanı değerleriyle değiştirmeden, dizeyi döndürür. Bilgi için bkz. [erişim genişletilmiş özellikler](#access-extended-properties) bölümü,

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Calculate yöntemi giriş dosyalarında Microsoft anahtar sözcük örneklerinin sayısını hesaplar (klasördeki Bloblar). Arama terimi ("Microsoft") kodda sabit kodlanmış.
10. Projeyi derleyin. Menüden **Oluştur** ' a tıklayın ve **çözüm oluştur**' a tıklayın.

    > [!IMPORTANT]
    > .NET Framework 4.5.2 sürümünü projeniz için hedef çerçeve olarak ayarlayın: projeye sağ tıklayın ve **Özellikler** ' e tıklayarak hedef Framework 'ü ayarlayın. Data Factory, 4.5.2 ' den sonraki sürümlere .NET Framework göre derlenen özel etkinlikleri desteklemez.

11. **Windows Gezgini**'ni başlatın ve derleme türüne göre **bin\Debug** veya **bin\release** klasörüne gidin.
12. \<proje klasörü\>\bin\Debug klasöründeki tüm ikilileri içeren bir **MyDotNetActivity. zip** dosyası oluşturun. Hata oluştuğunda soruna neden olan kaynak kodundaki satır numarası gibi ek ayrıntılar almak için **MyDotNetActivity. pdb** dosyasını dahil edin.

    > [!IMPORTANT]
    > Özel etkinliğin zip dosyasındaki tüm dosyalar alt klasör olmadan **en üst düzeyde** olmalıdır.

    ![İkili çıktı dosyaları](./media/data-factory-use-custom-activities/Binaries.png)
14. Zaten mevcut değilse, **customactivitycontainer** adlı bir blob kapsayıcısı oluşturun.
15. MyDotNetActivity. zip ' i, AzureStorageLinkedService tarafından başvurulan **genel amaçlı** bir Azure Blob depolama (etkin/seyrek erişimli BLOB depolama) olarak, customactivitycontainer 'a yükleyin.

> [!IMPORTANT]
> Bu .NET etkinliği projesini Visual Studio 'da bir Data Factory projesi içeren bir çözüme ekler ve Data Factory uygulama projesinden .NET etkinlik projesine bir başvuru eklerseniz, ZIP 'i el ile oluşturmanın en son iki adımını gerçekleştirmeniz gerekmez dosyayı ve genel amaçlı Azure Blob depolamaya yükleyin. Visual Studio 'Yu kullanarak Data Factory varlıkları yayımladığınızda, bu adımlar yayımlama işlemi tarafından otomatik olarak yapılır. Daha fazla bilgi için bkz. [Visual Studio 'da Data Factory projesi](#data-factory-project-in-visual-studio) bölümü.

## <a name="create-a-pipeline-with-custom-activity"></a>Özel etkinlikle bir işlem hattı oluşturma
Özel bir etkinlik oluşturdunuz ve ZIP dosyasını, **genel amaçlı** bir Azure depolama hesabındaki bir blob kapsayıcısına ikili dosyalarla karşıya yüklediniz. Bu bölümde, özel etkinliği kullanan bir işlem hattı ile bir Azure Data Factory oluşturacaksınız.

Özel etkinlik için giriş veri kümesi blob depolamada adföğreticisi kapsayıcısının customactivityınput klasöründe Blobları (dosyalar) temsil eder. Etkinliğin çıkış veri kümesi, blob depolamada adföğreticisi kapsayıcısının customactivityoutput klasöründe çıktı bloblarını temsil eder.

Aşağıdaki içerikle **File. txt** dosyası oluşturun ve bunu **adföğreticisi** kapsayıcısının **customactivityınput** klasörüne yükleyin. Henüz yoksa adföğreticisi kapsayıcısını oluşturun.

```
test custom activity Microsoft test custom activity Microsoft
```

Klasör iki veya daha fazla dosyaya sahip olsa bile, giriş klasörü Azure Data Factory bir dilime karşılık gelir. Her dilim işlem hattı tarafından işlendiğinde, özel etkinlik söz konusu dilimin giriş klasöründeki tüm Bloblar arasında yinelenir.

Adftutorial\customactivityoutput klasöründe bir veya daha fazla satır içeren bir çıkış dosyası görürsünüz (giriş klasöründeki blob sayısıyla aynı):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Bu bölümde gerçekleştirdiğiniz adımlar aşağıda verilmiştir:

1. Bir **Veri Fabrikası**oluşturun.
2. Özel etkinliğin çalıştığı VM 'lerin Azure Batch havuzu ve giriş/çıkış bloblarını tutan Azure depolama alanı için **bağlı hizmetler** oluşturun.
3. Özel etkinliğin giriş ve çıkışını temsil eden girdi ve çıktı **veri kümeleri** oluşturun.
4. Özel etkinliği kullanan bir işlem **hattı** oluşturun.

> [!NOTE]
> **Dosya. txt dosyasını** oluşturun ve henüz yapmadıysanız bir blob kapsayıcısına yükleyin. Yukarıdaki bölümdeki yönergelere bakın.

### <a name="step-1-create-the-data-factory"></a>1\. Adım: Veri Fabrikası oluşturma
1. Azure portal oturum açtıktan sonra aşağıdaki adımları uygulayın:
   1. Sol menüde **kaynak oluştur ' a** tıklayın.
   2. **Yeni** dikey pencerede **veri ve analiz** ' ye tıklayın.
   3. **Veri analizi** dikey penceresinde **Data Factory**’ye tıklayın.

      ![Yeni Azure Data Factory menüsü](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. **Yeni Veri Fabrikası** dikey penceresinde ad Için **Customactivityfactory** girin. Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. Şu hatayı alırsanız: **"customactivityfactory" Data Factory adı kullanılamıyor**, veri fabrikasının adını değiştirin (örneğin, **Yournamecustomactivityfactory**) ve yeniden oluşturmayı deneyin.

    ![Yeni Azure Data Factory dikey penceresi](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. **Kaynak grubu adı**' na tıklayın ve var olan bir kaynak grubunu seçin ya da bir kaynak grubu oluşturun.
4. Data Factory 'nin oluşturulmasını istediğiniz doğru **aboneliği** ve **bölgeyi** kullandığınızı doğrulayın.
5. **Yeni data factory** dikey penceresinde **Oluştur**’a tıklayın.
6. Azure portal **panosunda** oluşturulan veri fabrikasını görürsünüz.
7. Data Factory başarıyla oluşturulduktan sonra, veri fabrikasının içeriğini gösteren Data Factory dikey penceresini görürsünüz.

    ![Data Factory dikey penceresi](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>2\. Adım: bağlı hizmetler oluşturma
Bağlı hizmetler veri depolarını veya işlem hizmetlerini Azure data factory’ye bağlar. Bu adımda, Azure depolama hesabınızı ve Azure Batch hesabınızı veri fabrikasına bağlarsınız.

#### <a name="create-azure-storage-linked-service"></a>Azure Storage bağlı hizmeti oluşturma
1. **Customactivityfactory**IÇIN **Data Factory** dikey penceresinde **Yazar ve dağıt** kutucuğuna tıklayın. Data Factory Düzenleyicisi’ni görürsünüz.
2. Komut çubuğunda **Yeni veri deposu** ' na tıklayın ve **Azure Storage**' ı seçin. Düzenleyicide Azure Storage bağlı hizmeti oluşturmak için JSON betiğini görmeniz gerekir.

    ![Yeni veri deposu-Azure depolama](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. `<accountname>` Azure depolama hesabınızın adıyla ve `<accountkey>` Azure Storage hesabının erişim anahtarıyla değiştirin. Depolama erişim anahtarınızı nasıl alacağınız hakkında bilgi için bkz. [Depolama erişim anahtarlarını görüntüleme, kopyalama ve yeniden oluşturma](../../storage/common/storage-account-manage.md#access-keys).

    ![Azure Storage beğenilen hizmeti](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Bağlı hizmeti dağıtmak için komut çubuğunda **Dağıt**’a tıklayın.

#### <a name="create-azure-batch-linked-service"></a>Azure Batch bağlı hizmeti oluştur
1. Data Factory düzenleyicisinde,... öğesine tıklayın **. Daha fazla** komut çubuğu üzerinde **Yeni işlem**' e ve ardından menüden **Azure Batch** ' ı seçin.

    ![Yeni işlem-Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. JSON betiğine aşağıdaki değişiklikleri yapın:

   1. **AccountName** özelliği için Azure Batch hesap adı belirtin. **Azure Batch hesabı dikey** penceresindeki **URL** şu biçimdedir: `http://accountname.region.batch.azure.com`. JSON 'daki **Batchuri** özelliği IÇIN, URL 'den `accountname.` kaldırmanız ve `accountName` JSON özelliği için `accountname` kullanmanız gerekir.
   2. **AccessKey** özelliği için Azure Batch hesap anahtarını belirtin.
   3. **PoolName** özelliği için önkoşulların bir parçası olarak oluşturduğunuz havuzun adını belirtin. Havuzun adı yerine havuzun KIMLIĞINI de belirtebilirsiniz.
   4. **Batchuri** özelliği IÇIN Azure Batch URI belirtin. Örnek: `https://westus.batch.azure.com`.
   5. **Linkedservicename** özelliği için **AzureStorageLinkedService** belirtin.

        ```json
        {
          "name": "AzureBatchLinkedService",
          "properties": {
            "type": "AzureBatch",
            "typeProperties": {
              "accountName": "myazurebatchaccount",
              "batchUri": "https://westus.batch.azure.com",
              "accessKey": "<yourbatchaccountkey>",
              "poolName": "myazurebatchpool",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
        ```

       **PoolName** özelliği için havuzun adı yerıne havuzun kimliğini de belirtebilirsiniz.

### <a name="step-3-create-datasets"></a>3\. Adım: veri kümeleri oluşturma
Bu adımda, girdi ve çıktı verilerini temsil edecek veri kümeleri oluşturacaksınız.

#### <a name="create-input-dataset"></a>Girdi veri kümesi oluşturma
1. Data Factory **düzenleyicide** ... öğesine tıklayın **. Daha fazla** komut çubuğu üzerinde, **Yeni veri kümesi**' ne tıklayın ve ardından açılan menüden **Azure Blob depolama** ' yı seçin.
2. Sağ bölmedeki JSON öğesini aşağıdaki JSON kod parçacığıyla değiştirin:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/customactivityinput/",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```

   Başlangıç zamanı ile bu kılavuzda daha sonra bir işlem hattı oluşturursunuz: 2016-11-16T00:00:00Z ve bitiş zamanı: 2016-11-16T05:00:00Z. Saatlik olarak veri üretmek üzere zamanlandı. bu nedenle beş giriş/çıkış dilimi vardır ( **00**: 00:00-> **05**: 00:00).

   Giriş veri kümesi için **Sıklık** ve **Aralık** **saat** ve **1**olarak ayarlanır, bu da giriş diliminin saatlik olarak kullanılabildiği anlamına gelir. Bu örnekte, intputfolder dosyasında aynı dosya (File. txt).

   Yukarıdaki JSON kod parçacığında, bu sistem değişkeni ile temsil edilen her bir dilimin başlangıç zamanları aşağıda verilmiştir.
3. **Inputdataset**'i oluşturmak ve dağıtmak için araç çubuğunda **Dağıt** ' a tıklayın. Düzenleyici’nin başlık çubuğunda **TABLO BAŞARIYLA OLUŞTURULDU** iletisini gördüğünüzü onaylayın.

#### <a name="create-an-output-dataset"></a>Çıktı veri kümesi oluşturma
1. **Data Factory düzenleyicisinde**,... öğesine tıklayın **.** Komut çubuğunda, **Yeni veri kümesi**' ne ve ardından **Azure Blob depolama**' yı seçin.
2. Sağ bölmedeki JSON betiğini aşağıdaki JSON betiği ile değiştirin:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
                "partitionedBy": [
                    {
                        "name": "slice",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy-MM-dd-HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

     Çıkış konumu **adföğreticisi/customactivityoutput/** ve çıkış dosyası adı yyyy-mm-dd-hh. txt ' dir. burada yyyy-aa-gg-hh, üretilmekte olan dilimin yıl, ay, tarih ve saattir. Ayrıntılar için bkz. [Geliştirici başvurusu][adf-developer-reference] .

    Her giriş dilimi için bir çıktı blobu/dosyası oluşturulur. Her bir dilim için bir çıktı dosyası adı verilmiştir. Tüm çıkış dosyaları bir çıkış klasöründe oluşturulur: **adftutorial\customactivityoutput**.

   | 'In | Başlangıç saati | Çıktı dosyası |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16 -00. txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16 -01. txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16 -02. txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16 -03. txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16 -04. txt |

    Bir giriş klasöründeki tüm dosyaların yukarıda belirtilen başlangıç zamanlarını içeren bir dilimin parçası olduğunu unutmayın. Bu dilim işlendiğinde, özel etkinlik her bir dosya boyunca tarar ve çıkış dosyasında arama teriminin ("Microsoft") oluşum sayısıyla birlikte bir satır üretir. Giriş klasöründe üç dosya varsa, her saatlik dilimin çıkış dosyasında üç satır vardır: 2016-11-16 -00. txt, 2016-11-16:01:00:00. txt, vb.
3. **Outputdataset**'i dağıtmak için komut çubuğunda **Dağıt** ' a tıklayın.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Özel etkinliği kullanan bir işlem hattı oluşturma ve çalıştırma
1. Data Factory düzenleyicisinde,... öğesine tıklayın **. Daha fazla bilgi**için, komut çubuğunda **Yeni işlem hattı** ' nı seçin.
2. Sağ bölmedeki JSON öğesini aşağıdaki JSON betiği ile değiştirin:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Aşağıdaki noktalara dikkat edin:

   * **Eşzamanlılık** **2** olarak ayarlanır, böylece iki dilim Azure Batch havuzundaki 2 VM 'ye paralel olarak işlenir.
   * Etkinlikler bölümünde bir etkinlik vardır ve bu tür: **Dotnetactivity**.
   * **AssemblyName** , DLL adı olarak ayarlanır: **MyDotnetActivity. dll**.
   * **EntryPoint** , **MyDotNetActivityNS. MyDotNetActivity**olarak ayarlanır.
   * **PackageLinkedService** , özel etkinlik ZIP dosyasını içeren BLOB depolama alanına Işaret eden **AzureStorageLinkedService** olarak ayarlanır. Giriş/çıkış dosyaları ve özel etkinlik ZIP dosyası için farklı Azure depolama hesapları kullanıyorsanız, başka bir Azure depolama bağlı hizmeti oluşturursunuz. Bu makalede aynı Azure Depolama hesabını kullandığınız varsayılır.
   * **PackageFile** , **customactivitycontainer/MyDotNetActivity. zip**olarak ayarlanır. Şu biçimdedir: containerforthezip/nameofthezip. zip.
   * Özel etkinlik **ınputdataset** 'i giriş ve **outputdataset** olarak çıktı olarak alır.
   * Özel etkinliğin linkedServiceName özelliği, özel etkinliğin Azure Batch VM 'lerde çalıştırılması gerektiğini Azure Data Factory bildiren **AzureBatchLinkedService**'e işaret eder.
   * **ısduraklatılan** özelliği varsayılan olarak **false** olarak ayarlanır. Dilimler geçmişte çalışmaya başlayacağı için işlem hattı Bu örnekte hemen çalışır. İşlem hattını duraklatmak ve yeniden başlatmak için false olarak ayarlamak için bu özelliği true olarak ayarlayabilirsiniz.
   * **Başlangıç** saati ve **bitiş** zamanları **beş** saat dışında ve dilimler saatlik olarak üretildiğinde, işlem hattı tarafından beş dilim üretilir.
3. İşlem hattını dağıtmak için komut çubuğunda **Dağıt** ' a tıklayın.

### <a name="monitor-the-pipeline"></a>İşlem hattını izleme
1. Azure portal Data Factory dikey penceresinde **Diyagram**' a tıklayın.

    ![Diyagram kutucuğu](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Diyagram görünümünde, şimdi OutputDataset ' e tıklayın.

    ![Diyagram görünümü](./media/data-factory-use-custom-activities/diagram.png)
3. Beş çıkış dilimlerinin, Ready durumunda olduğunu görmeniz gerekir. Bunlar, hazırlanma durumunda yoksa henüz üretilmemiştir.

   ![Çıkış dilimleri](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Çıkış dosyalarının, **adföğreticisi** kapsayıcısındaki blob depolamada oluşturulduğunu doğrulayın.

   ![Özel etkinlikten çıkış][image-data-factory-output-from-custom-activity]
5. Çıkış dosyasını açarsanız, aşağıdaki çıktıya benzer bir çıktı görmeniz gerekir:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Data Factory, işlem hatları ve veri kümelerinizi izlemek için [Azure Portal][azure-preview-portal] veya Azure PowerShell cmdlet 'lerini kullanın. Portalda veya cmdlet 'leri kullanarak indirebileceğiniz günlüklerde (özellikle User-0. log) özel etkinlik için koddaki **Activitygünlükçüsü** içindeki iletileri görebilirsiniz.

   ![günlükleri özel etkinlikten indir][image-data-factory-download-logs-from-custom-activity]

Veri kümelerini ve işlem hatlarını izlemeye yönelik ayrıntılı adımlar için bkz. işlem [hatlarını izleme ve yönetme](data-factory-monitor-manage-pipelines.md) .

## <a name="data-factory-project-in-visual-studio"></a>Visual Studio 'da Data Factory projesi
Azure portal kullanmak yerine, Visual Studio 'Yu kullanarak Data Factory varlıkları oluşturabilir ve yayımlayabilirsiniz. Visual Studio kullanarak Data Factory varlıkları oluşturma ve yayımlama hakkında ayrıntılı bilgi için bkz. [Visual Studio kullanarak ilk işlem hattınızı](data-factory-build-your-first-pipeline-using-vs.md) oluşturma ve [Azure Blob 'dan Azure SQL makalelerine veri kopyalama](data-factory-copy-activity-tutorial-using-visual-studio.md) .

Visual Studio 'da Data Factory projesi oluşturuyorsanız aşağıdaki ek adımları uygulayın:

1. Data Factory projesini, özel etkinlik projesini içeren Visual Studio çözümüne ekleyin.
2. Data Factory projesinden .NET etkinlik projesine bir başvuru ekleyin. Data Factory proje ' ye sağ tıklayın, **Ekle**' nin üzerine gelin ve ardından **başvuru**' ya tıklayın.
3. **Başvuru Ekle** Iletişim kutusunda **MyDotNetActivity** projesini seçip **Tamam**' a tıklayın.
4. Çözümü derleyin ve yayımlayın.

    > [!IMPORTANT]
    > Data Factory varlıklarını yayımladığınızda, sizin için otomatik olarak bir ZIP dosyası oluşturulur ve BLOB kapsayıcısına yüklenir: customactivitycontainer. Blob kapsayıcısı yoksa, otomatik olarak oluşturulur.

## <a name="data-factory-and-batch-integration"></a>Data Factory ve toplu tümleştirme
Data Factory hizmeti şu ada sahip Azure Batch bir iş oluşturur: **ADF-PoolName: Job-xxx**. Sol taraftaki menüden **işler** ' e tıklayın.

![Azure Data Factory-Batch işleri](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Bir dilimin her etkinlik çalışması için bir görev oluşturulur. İşlenmek üzere beş dilim varsa, bu işte beş görev oluşturulur. Batch havuzunda birden çok işlem düğümü varsa, iki veya daha fazla dilim paralel olarak çalıştırılabilir. İşlem düğümü başına en fazla görev > 1 olarak ayarlanırsa aynı işlem üzerinde çalışan birden fazla dilim de olabilir.

![Azure Data Factory-Batch iş görevleri](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Aşağıdaki diyagramda Azure Data Factory ve Batch görevleri arasındaki ilişki gösterilmektedir.

![Toplu & Data Factory](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Sorun giderme hataları
Sorun giderme birkaç temel teknikten oluşur:

1. Aşağıdaki hatayı görürseniz, genel amaçlı bir Azure Blob depolama alanı kullanmak yerine, sık/seyrek erişimli bir BLOB depolama alanı kullanıyor olabilirsiniz. ZIP dosyasını **genel amaçlı bir Azure depolama hesabına**yükleyin.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Aşağıdaki hatayı görürseniz, CS dosyasındaki sınıf adının JSON işlem hattında **entryPoint** özelliği için belirttiğiniz adla eşleştiğinden emin olun. Yönergede, sınıfın adı: MyDotNetActivity ve JSON 'daki giriş noktası: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Adlar eşleşiyorsa, tüm ikili dosyaların ZIP dosyasının **kök klasöründe** olduğunu doğrulayın. Diğer bir deyişle, ZIP dosyasını açtığınızda, herhangi bir alt klasörde değil kök klasördeki tüm dosyaları görmeniz gerekir.
3. Giriş Dilimi **Ready**olarak ayarlanmamışsa, giriş klasörü yapısının doğru olduğunu ve giriş klasörlerinde **File. txt dosyasının** bulunduğunu doğrulayın.
3. Özel etkinliğinizin **Execute** yönteminde, sorunları gidermenize yardımcı olan bilgileri günlüğe kaydetmek Için **ıactivitygünlükçü** nesnesini kullanın. Günlüğe kaydedilen iletiler Kullanıcı günlük dosyalarında görünür (bir veya daha fazla dosya: user-0. log, User-1. log, User-2. log, vb.).

   **Outputdataset** dikey penceresinde, bu DILIMIN **veri dilimi** dikey penceresini görmek için dilime tıklayın. Bu dilim için **etkinlik çalıştırmaları** görürsünüz. Dilim için bir etkinlik çalıştırması görmeniz gerekir. Komut çubuğunda Çalıştır ' a tıklarsanız, aynı dilim için başka bir etkinlik çalıştırması başlatabilirsiniz.

   Etkinlik çalıştırmaya tıkladığınızda, **etkınlık çalıştırma ayrıntıları** dikey penceresini bir günlük dosyaları listesiyle görürsünüz. Günlüğe kaydedilen iletileri user_0. log dosyasında görürsünüz. Bir hata oluştuğunda, yeniden deneme sayısı ardışık düzen/etkinlik JSON 'u 3 olarak ayarlandığından, üç etkinlik çalıştırması görürsünüz. Etkinlik çalıştırmaya tıkladığınızda, hatayı gidermek için gözden geçirebileceğiniz günlük dosyalarını görürsünüz.

   Günlük dosyaları listesinde **User-0. log**dosyasına tıklayın. Sağ bölmede **ıactivitygünlükçü. Write** metodunu kullanmanın sonuçları vardır. Tüm iletileri görmüyorsanız: user_1. log, user_2. log vb. adlı daha fazla günlük dosyasına sahip olup olmadığınızı denetleyin. Aksi takdirde, kod son günlüğe kaydedilen iletiden sonra başarısız olmuş olabilir.

   Ayrıca, tüm sistem hata iletileri ve özel durumlar için **System-0. log** ' u denetleyin.
4. Hata ayrıntılarının bir hata oluştuğunda **çağrı yığını** gibi bilgileri Içermesi için **pdb** dosyasını ZIP dosyasına ekleyin.
5. Özel etkinliğin zip dosyasındaki tüm dosyalar alt klasör olmadan **en üst düzeyde** olmalıdır.
6. **AssemblyName** (MyDotNetActivity. dll), **entryPoint**(MyDotNetActivityNS. MyDotNetActivity), **PackageFile** (customactivitycontainer/MyDotNetActivity. zip) ve **packageLinkedService** (işaret etmelidir) olduğundan emin olun. ZIP dosyasını içeren **genel amaçlı**Azure Blob depolama alanı, doğru değerlere ayarlanır.
7. Bir hatayı düzelttiyseniz ve dilimi yeniden işlemek istiyorsanız **OutputDataset** dikey penceresindeki dilime sağ tıklayın ve **Çalıştır**’a tıklayın.
8. Aşağıdaki hatayı görürseniz, > 4.3.0 sürümünün Azure depolama paketini kullanıyorsunuz. Data Factory hizmet başlatıcısı, WindowsAzure. Storage 'un 4,3 sürümünü gerektirir. Azure Storage derlemesinin sonraki sürümünü kullanmanız gerekiyorsa, bir iş için [AppDomain yalıtım](#appdomain-isolation) bölümüne bakın.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Azure Storage paketi 'nin 4.3.0 sürümünü kullanacaksanız, > 4.3.0 sürümünün Azure depolama paketine yönelik mevcut başvuruyu kaldırın. Ardından, NuGet paket yöneticisi konsolundan aşağıdaki komutu çalıştırın.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Projeyi derleyin. Bin\Debug klasöründen sürüm > 4.3.0 Azure. Storage derlemesini silin. İkili dosyaları ve PDB dosyasını içeren bir zip dosyası oluşturun. Eski ZIP dosyasını blob kapsayıcısında (customactivitycontainer) Bu dosyayla değiştirin. Başarısız olan dilimleri yeniden çalıştırın (dilimi sağ tıklatın ve Çalıştır ' a tıklayın).
8. Özel etkinlik, paketinizin **app. config** dosyasını kullanmaz. Bu nedenle, kodunuz yapılandırma dosyasından herhangi bir bağlantı dizesini okuduğunda, çalışma zamanında çalışmaz. Azure Batch kullanmanın en iyi uygulaması, **Azure keykasasındaki**gizli dizileri tutmak, **anahtar kasasını**korumak için sertifika tabanlı hizmet sorumlusu kullanmak ve sertifikayı Azure Batch havuzuna dağıtmaktır. Böylece .NET özel etkinliği çalıştırma sırasında KeyVault’tan parolalara erişebilir. Bu çözüm, genel bir çözümdür ve yalnızca bağlantı dizesinde değil, herhangi bir gizli dizi türüne ölçeklendirebilir.

   Daha kolay bir geçici çözüm vardır (ancak en iyi yöntem değildir): bağlantı dizesi ayarları ile bir **Azure SQL bağlı hizmeti** oluşturabilir, bağlı hizmeti kullanan bir veri kümesi oluşturabilir ve veri kümesini özel .net etkinliğine bir kukla giriş veri kümesi olarak zincirleyebilirsiniz. Ardından, bağlantılı hizmetin bağlantı dizesine özel etkinlik kodunda erişebilirsiniz.

## <a name="update-custom-activity"></a>Özel etkinliği Güncelleştir
Özel etkinlik için kodu güncelleştirirseniz, derleyin ve yeni ikili dosyaları içeren zip dosyasını blob depolamaya yükleyin.

## <a name="appdomain-isolation"></a>AppDomain yalıtımı
Data Factory başlatıcısı tarafından kullanılan derleme sürümleriyle sınırlı olmayan özel bir etkinlik oluşturmayı gösteren [çapraz AppDomain örneğine](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) bakın (örnek: WindowsAzure. Storage v 4.3.0, Newtonsoft. JSON v 6.0. x, vb.).

## <a name="access-extended-properties"></a>Genişletilmiş özelliklere erişin
Aşağıdaki örnekte gösterildiği gibi, etkinlik JSON içinde genişletilmiş özellikler bildirebilirsiniz:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```

Örnekte, iki genişletilmiş özellik vardır:, **daBaşlat** ve **datafactoryname**. Festart 'ın değeri, Festart sistem değişkenine göre belirlenir. Desteklenen sistem değişkenlerinin listesi için bkz. [Sistem değişkenleri](data-factory-functions-variables.md) . DataFactoryName değeri, CustomActivityFactory 'ye sabit olarak kodlanmıştır.

**Execute** yönteminde bu genişletilmiş özelliklere erişmek için aşağıdaki koda benzer bir kod kullanın:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Azure Batch otomatik ölçeklendiriliyor
Ayrıca, **Otomatik ölçeklendirme** özelliği ile bir Azure Batch havuzu da oluşturabilirsiniz. Örneğin, 0 adanmış VM ile bir Azure Batch havuzu ve bekleyen görevlerin sayısına göre bir otomatik ölçeklendirme formülü oluşturabilirsiniz.

Buradaki örnek formül aşağıdaki davranışa ulaşır: havuz başlangıçta oluşturulduğunda 1 VM ile başlar. $PendingTasks ölçümü, çalışan + etkin (sıraya alınmış) durumundaki görevlerin sayısını tanımlar.  Formül, son 180 saniye içinde bekleyen görevlerin ortalama sayısını bulur ve Targetadanmış 'yi uygun şekilde ayarlar. Targetadanmış, 25 sanal makine dışında hiçbir şekilde geçmeyeceğinden emin olmanızı sağlar. Bu nedenle, yeni görevler gönderildiğinde havuz otomatik olarak büyür ve görevler tamamlandıkça, VM 'Ler tek bir kez serbest olur ve otomatik ölçeklendirme bu VM 'Leri küçültür. startingNumberOfVMs ve Maxnumberofvm 'Ler gereksinimlerinize göre ayarlanabilir.

Otomatik ölçeklendirme formülü:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Ayrıntılar için bkz. [bir Azure Batch havuzundaki işlem düğümlerini otomatik olarak ölçeklendirme](../../batch/batch-automatic-scaling.md) .

Havuz varsayılan [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)kullanıyorsa, Batch hizmeti özel etkinliği ÇALıŞTıRMADAN önce VM 'yi hazırlamak için 15-30 dakika sürebilir.  Havuz farklı bir autoScaleEvaluationInterval kullanıyorsa, Batch hizmeti autoScaleEvaluationInterval + 10 dakika alabilir.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>.NET SDK kullanarak özel etkinlik oluşturma
Bu makaledeki izlenecek yolda, Azure portal kullanarak özel etkinliği kullanan bir işlem hattı ile veri fabrikası oluşturacaksınız. Aşağıdaki kod, bunun yerine .NET SDK kullanarak veri fabrikasının nasıl oluşturulacağını gösterir. [.NET API 'yi kullanarak kopyalama etkinliği ile işlem hattı oluşturma başlıklı](data-factory-copy-activity-tutorial-using-dotnet-api.md) işlem hatlarını programlı bir şekilde oluşturmak için SDK kullanma hakkında daha fazla ayrıntı bulabilirsiniz.

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

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
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

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
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
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
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
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

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
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
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

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
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Visual Studio 'da özel etkinlikte hata ayıklama
GitHub 'daki [Azure Data Factory yerel ortam](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) örneği, Visual Studio içinde özel .net etkinliklerinin hatalarını ayıklamanızı sağlayan bir araç içerir.

## <a name="sample-custom-activities-on-github"></a>GitHub 'da örnek özel etkinlikler
| Örnek | Özel etkinlik ne yapar |
| --- | --- |
| [Http veri yükleyici](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Data Factory 'de özel C# etkinlik kullanarak bir HTTP uç noktasından Azure Blob depolama alanına veri indirir. |
| [Twitter Yaklaşım Analizi örneği](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Azure Machine Learning Studio modelini çağırır ve yaklaşım analizi, Puanlama, tahmin vb. |
| [R betiğini çalıştırın](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Zaten R 'nin yüklü olduğu HDInsight kümenizde RScript. exe ' yi çalıştırarak R betiğini çağırır. |
| [Çapraz AppDomain .NET etkinliği](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Data Factory başlatıcısı tarafından kullanılan farklı derleme sürümlerini kullanır |
| [Azure Analysis Services bir modeli yeniden işleme](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Azure Analysis Services bir modeli yeniden işler. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
