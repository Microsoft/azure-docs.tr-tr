---
title: Data Factory ve Batch kullanarak büyük ölçekli veri kümelerini işleme
description: Azure Batch paralel işleme özelliği kullanılarak Azure Data Factory bir işlem hattındaki çok büyük miktarlarda verilerin nasıl işlenmesinin nasıl yapılacağını açıklar.
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
ms.openlocfilehash: 611c2a36cac5a589ecd6f9063f5f1bc325860ef6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682657"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Data Factory ve Batch kullanarak büyük ölçekli veri kümelerini işleme
> [!NOTE]
> Bu makale, Azure Data Factory’nin genel kullanıma açık olan 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory özel etkinlikler](../transform-data-using-dotnet-custom-activity.md).

Bu makalede, büyük ölçekli veri kümelerini otomatik ve zamanlanmış bir şekilde taşınan ve işleyen örnek bir çözümün mimarisi açıklanmaktadır. Ayrıca, Data Factory ve Azure Batch kullanarak çözümü uygulamak için bir uçtan uca izlenecek yol sağlar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bu makale, bir örnek çözümün tamamına yönelik bir anlatım içerdiğinden tipik bir makaleden daha uzundur. Toplu Iş ve Data Factory yeni başladıysanız, bu hizmetler ve bunların birlikte nasıl çalıştıkları hakkında bilgi edinebilirsiniz. Hizmetlerle ilgili bir şeyi biliyorsanız ve bir çözümü tasarlamakta/mimarmaya çalışıyorsanız, makalenin mimari bölümüne odaklanırsınız. Bir prototip veya çözüm geliştiriyorsanız, izlenecek yolda adım adım yönergeleri denemek isteyebilirsiniz. Yorumlarınızı Bu içerik ve nasıl kullandığınız hakkında davet ediyoruz.

İlk olarak, Data Factory ve Batch hizmetlerinin bulutta büyük veri kümelerini işleme konusunda nasıl yardımcı olduğunu inceleyelim.     


## <a name="why-azure-batch"></a>Neden Azure Batch?
 Batch 'i, büyük ölçekli paralel ve yüksek performanslı bilgi işlem (HPC) uygulamalarını bulutta verimli bir şekilde çalıştırmak için kullanabilirsiniz. Bu, yönetilen bir sanal makine (VM) koleksiyonunda çalışacak işlem yoğunluklu işleri zamanlayan bir platform hizmetidir. İşlem kaynaklarını, işlerinizin ihtiyaçlarını karşılayacak şekilde otomatik olarak ölçeklendirebilir.

Batch hizmetiyle, uygulamalarınızı paralel olarak ve ölçekte yürütmek için Azure işlem kaynaklarını tanımlayın. İsteğe bağlı veya zamanlanmış işleri çalıştırabilirsiniz. HPC kümesi, tek tek VM 'Ler, sanal ağlar veya karmaşık iş ve görev zamanlama altyapısını el ile oluşturmanız, yapılandırmanız ve yönetmeniz gerekmez.

 Batch hakkında bilginiz yoksa, aşağıdaki makaleler Bu makalede açıklanan çözümün mimarisini/uygulanmasını anlamanıza yardımcı olur:   

* [Toplu Iş temelleri](../../batch/batch-technical-overview.md)
* [Batch özelliklerine genel bakış](../../batch/batch-api-basics.md)

İsteğe bağlı olarak, toplu Iş hakkında daha fazla bilgi edinmek için [Batch belgelerine](https://docs.microsoft.com/azure/batch/)bakın.

## <a name="why-azure-data-factory"></a>Neden Azure Data Factory?
Data Factory, verilerin taşınmasını ve dönüştürülmesini düzenleyen ve otomatikleştiren bulut tabanlı bir veri tümleştirme hizmetidir. Şirket içi ve bulut veri depolarından verileri merkezi bir veri deposuna taşımak için yönetilen veri işlem hatları oluşturmak üzere Data Factory kullanabilirsiniz. Azure Blob depolama örneği bir örnektir. Azure HDInsight ve Azure Machine Learning gibi hizmetleri kullanarak verileri işlemek/dönüştürmek için Data Factory kullanabilirsiniz. Ayrıca, veri işlem hatlarını zamanlanan bir şekilde (örneğin, saatlik, günlük ve haftalık) çalışacak şekilde zamanlayabilirsiniz. Sorunları belirlemek ve işlem yapmak için bir bakışta işlem hatlarını izleyebilir ve yönetebilirsiniz.

  Data Factory konusunda bilgi sahibi değilseniz aşağıdaki makaleler, bu makalede açıklanan çözümün mimarisini/uygulanmasını anlamanıza yardımcı olur:  

* [Data Factory giriş](data-factory-introduction.md)
* [İlk veri işlem hattınızı oluşturun](data-factory-build-your-first-pipeline.md)   

İsteğe bağlı olarak, Data Factory hakkında daha fazla bilgi edinmek için [Data Factory belgelerine](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory)bakın.

## <a name="data-factory-and-batch-together"></a>Birlikte Data Factory ve Batch
Data Factory yerleşik etkinlikleri içerir. Örneğin, kopyalama etkinliği bir kaynak veri deposundan hedef veri deposuna veri kopyalamak/taşımak için kullanılır. Hive etkinliği, Azure 'da Hadoop kümeleri (HDInsight) kullanılarak verileri işlemek için kullanılır. Desteklenen dönüştürme etkinliklerinin listesi için bkz. [veri dönüştürme etkinlikleri](data-factory-data-transformation-activities.md).

Ayrıca, verileri kendi mantığınızla taşımak veya işlemek için özel .NET etkinlikleri oluşturabilirsiniz. Bu etkinlikleri bir HDInsight kümesinde veya VM 'lerin Batch havuzunda çalıştırabilirsiniz. Batch kullandığınızda, havuzunuzu sağladığınız bir formüle göre otomatik olarak (iş yüküne bağlı olarak VM 'Leri eklemek veya kaldırmak) yapılandırabilirsiniz.     

## <a name="architecture-of-a-sample-solution"></a>Örnek çözümün mimarisi
  Bu makalede açıklanan mimari basit bir çözümdür. Finansal hizmetler, görüntü işleme ve işleme ve genomik analizi için risk modellemesi gibi karmaşık senaryolara de uygundur.

Diyagramda, Data Factory veri hareketini ve işlemeyi nasıl düzenleyen gösterilmektedir. Ayrıca Batch 'in verileri paralel bir şekilde nasıl işlediğinde de gösterilir. Kolay başvuru için diyagramı indirin ve yazdırın (11 x 17 inç veya a3 boyut). Yazdırabilmeniz için diyagrama erişmek üzere bkz. [Batch ve Data Factory kullanarak HPC ve veri düzenleme](https://go.microsoft.com/fwlink/?LinkId=717686).

[Büyük ölçekli veri işleme diyagramını ![](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

Aşağıdaki listede işlemin temel adımları verilmiştir. Çözüm, uçtan uca çözümü derlemek için kod ve açıklamalar içerir.

* **Batch 'i bir işlem düğümleri Havuzu (VM) ile yapılandırın.** Düğüm sayısını ve her bir düğümün boyutunu belirtebilirsiniz.

* Blob depolamayı, toplu işlem hizmetini, giriş/çıkış verilerini ve verileri taşıyan ve dönüştüren etkinliklere sahip bir iş akışı/Işlem hattını temsil eden varlıklarla yapılandırılmış **bir Data Factory örneği oluşturun** .

* **Data Factory ardışık düzeninde özel bir .NET etkinliği oluşturun.** Etkinlik, Batch havuzunda çalışan kullanıcı kodudur.

* **Büyük miktarlarda giriş verilerini Azure Storage 'da blob olarak depolayın.** Veriler mantıksal dilimlere bölünür (genellikle zamana göre).

* **Data Factory, paralel olarak işlenen verileri** ikincil konuma kopyalar.

* **Data Factory, Batch tarafından ayrılan havuzu kullanarak özel etkinliği çalıştırır.** Data Factory, etkinlikleri eşzamanlı olarak çalıştırabilir. Her etkinlik bir veri dilimini işler. Sonuçlar depolama alanında depolanır.

* Data Factory, son sonuçları bir uygulama aracılığıyla veya diğer araçlarla daha fazla işlenmek üzere **üçüncü bir konuma taşımaz** .

## <a name="implementation-of-the-sample-solution"></a>Örnek çözümün uygulanması
Örnek çözüm özellikle basittir. Veri kümelerini işlemek için Data Factory ve toplu Işi birlikte nasıl kullanacağınızı göstermek için tasarlanmıştır. Çözüm, bir zaman serisinde düzenlenmiş giriş dosyalarındaki "Microsoft" arama teriminin oluşum sayısını sayar. Daha sonra sayımı dosya çıktısı olarak verir.

**Süre:** Azure, Data Factory ve Batch temel bilgileri hakkında bilgi sahibiyseniz ve aşağıdaki önkoşulları tamamladıysanız, bu çözümün tamamlanması iki saate kadar sürer.

### <a name="prerequisites"></a>Ön koşullar
#### <a name="azure-subscription"></a>Azure aboneliği
Azure aboneliğiniz yoksa hızla ücretsiz bir deneme hesabı oluşturabilirsiniz. Daha fazla bilgi için bkz. [ücretsiz deneme](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure depolama hesabı
Bu öğreticide verileri depolamak için bir depolama hesabı kullanın. Depolama hesabınız yoksa, bkz. [depolama hesabı oluşturma](../../storage/common/storage-quickstart-create-account.md). Örnek çözüm, blob depolamayı kullanır.

#### <a name="azure-batch-account"></a>Azure Batch hesabı
[Azure Portal](https://portal.azure.com/)kullanarak bir Batch hesabı oluşturun. Daha fazla bilgi için bkz. [Batch hesabı oluşturma ve yönetme](../../batch/batch-account-create-portal.md). Batch hesap adı ve hesap anahtarı ' nı aklınızda edin. Ayrıca, bir Batch hesabı oluşturmak için [New-AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) cmdlet 'ini de kullanabilirsiniz. Bu cmdlet 'i kullanma hakkında yönergeler için bkz. [Batch PowerShell cmdlet 'leri ile çalışmaya başlama](../../batch/batch-powershell-cmdlets-get-started.md).

Örnek çözüm, verileri bir işlem düğümleri havuzunda (yönetilen VM 'Ler koleksiyonu) paralel bir şekilde işlemek için Batch 'i (Data Factory Işlem hattı aracılığıyla dolaylı olarak) kullanır.

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch sanal makine havuzu
En az iki işlem düğümüyle bir Batch havuzu oluşturun.

1. [Azure Portal](https://portal.azure.com), sol menüden **gözatıp** ' yi seçin ve **Batch hesapları**' nı seçin.

1. Batch **hesabı** dikey penceresini açmak için Batch hesabınızı seçin.

1. **Havuzlar** kutucuğunu seçin.

1. **Havuzlar** dikey penceresinde, bir havuz eklemek için araç çubuğunda **Ekle** düğmesini seçin.

   a. Havuz için bir KIMLIK girin (**Havuz kimliği**). Havuzun KIMLIĞINI aklınızda edin. Data Factory çözümünü oluştururken ihtiyacınız vardır.

   b. **Işletim sistemi ailesi** ayarı Için **Windows Server 2012 R2** 'yi belirtin.

   c. **Düğüm fiyatlandırma katmanını**seçin.

   d. **Hedef adanmış** ayar için değer olarak **2** yazın.

   e. **Düğüm başına en fazla görev** ayarı için değer olarak **2** yazın.

   f. Havuzu oluşturmak için **Tamam ' ı** seçin.

#### <a name="azure-storage-explorer"></a>Azure Depolama Gezgini
Depolama projelerinizde verileri incelemek ve değiştirmek için [Azure Depolama Gezgini 6](https://azurestorageexplorer.codeplex.com/) veya [cloudxplorer](https://clumsyleaf.com/products/cloudxplorer) (Clumsyyaprak yazılımından) kullanın. Ayrıca, bulutta barındırılan uygulamalarınızın günlüklerinde verileri inceleyebilir ve değiştirebilirsiniz.

1. Özel erişim (anonim erişim yok) ile **myContainer** adlı bir kapsayıcı oluşturun.

1. CloudXplorer kullanıyorsanız, aşağıdaki yapıyla klasörler ve alt klasörler oluşturun:

   ![Klasör ve alt klasör yapısı](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` ve `outputfolder` en üst düzey klasörler `mycontainer`. `inputfolder` klasörü, tarih-saat damgaları olan alt klasörlere sahiptir (YYYY-AA-GG-HH).

   Depolama Gezgini kullanıyorsanız, bir sonraki adımda dosyaları şu adlarla karşıya yüklersiniz: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt`, vb. Bu adım klasörleri otomatik olarak oluşturur.

1. Makinenizde **Microsoft**anahtar sözcüğünü içeren içeriğe sahip bir metin dosyası **. txt** dosyası oluşturun. Örnek olarak "test özel etkinliği Microsoft Test özel etkinliği Microsoft."

1. Dosyayı BLOB depolama alanında aşağıdaki giriş klasörlerine yükleyin:

   ![Giriş klasörleri](./media/data-factory-data-processing-using-batch/image4.png)

   Depolama Gezgini kullanıyorsanız, **myContainer** **dosyasına File. txt** dosyasını yükleyin. Blob 'un bir kopyasını oluşturmak için araç çubuğunda **Kopyala** ' yı seçin. **Blobu Kopyala** iletişim kutusunda **hedef blob adını** `inputfolder/2015-11-16-00/file.txt`olarak değiştirin. `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt`vb. oluşturmak için bu adımı tekrarlayın. Bu eylem klasörleri otomatik olarak oluşturur.

1. `customactivitycontainer`adlı başka bir kapsayıcı oluşturun. Özel etkinlik ZIP dosyasını bu kapsayıcıya yükleyin.

#### <a name="visual-studio"></a>Visual Studio
Data Factory çözümünde kullanılacak özel toplu Iş etkinliğini oluşturmak için Visual Studio 2012 veya üstünü yükler.

### <a name="high-level-steps-to-create-the-solution"></a>Çözümü oluşturmak için üst düzey adımlar
1. Veri işleme mantığını içeren özel bir etkinlik oluşturun.

1. Özel etkinliği kullanan bir veri fabrikası oluşturun.

### <a name="create-the-custom-activity"></a>Özel etkinlik oluşturma
Data Factory özel etkinliği bu örnek çözümün kalbidir. Örnek çözüm, özel etkinliği çalıştırmak için Batch kullanır. Özel etkinlikler geliştirme ve bunları Data Factory işlem hatları içinde kullanma hakkında daha fazla bilgi için bkz. [Data Factory işlem hattında özel etkinlikler kullanma](data-factory-use-custom-activities.md).

Data Factory ardışık düzeninde kullanabileceğiniz bir .NET özel etkinliği oluşturmak için, ıdotnetactivity arabirimini uygulayan bir sınıf ile bir .NET sınıf kitaplığı projesi oluşturursunuz. Bu arabirimin yalnızca bir yöntemi vardır: yürütme. Yöntemin imzası aşağıda verilmiştir:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Yöntemi anlamanız gereken birkaç anahtar bileşene sahiptir:

* Yöntemi dört parametre alır:

  * **Linkedservices**. Bu parametre, giriş/çıkış veri kaynaklarını (örneğin, BLOB depolama) Data Factory 'ye bağlayan bağlı hizmetlerin sıralanabilir bir listesidir. Bu örnekte, hem giriş hem de çıkış için kullanılan Azure depolama türünde yalnızca bir bağlı hizmet vardır.
  * **veri kümeleri**. Bu parametre, veri kümelerinin sıralanabilir bir listesidir. Bu parametreyi, girdi ve çıktı veri kümeleri tarafından tanımlanan konumları ve şemaları almak için kullanabilirsiniz.
  * **etkinlik**. Bu parametre geçerli işlem varlığını temsil eder. Bu durumda, bir Batch hizmetidir.
  * **günlükçü**. İşlem hattı için "Kullanıcı" günlüğü olarak yüzey yapan hata ayıklama açıklamalarını yazmak için günlükçüsü ' i kullanabilirsiniz.
* Yöntemi, gelecekte özel etkinlikleri zincirlemek için kullanılabilecek bir sözlük döndürür. Bu özellik henüz uygulanmadı, bu nedenle yöntemden yalnızca boş bir sözlük döndürün.

#### <a name="procedure-create-the-custom-activity"></a>Yordam: özel etkinlik oluşturma
1. Visual Studio 'da bir .NET sınıf kitaplığı projesi oluşturun.

   a. Visual Studio 2012/2013/2015 ' i başlatın.

   b. **Dosya** > **Yeni** > **Proje**’yi seçin.

   c. **Şablonlar**' ı genişletin ve **Visual C\#** ' yi seçin. Bu kılavuzda, C\#kullanırsınız, ancak özel etkinlik geliştirmek için herhangi bir .NET dili kullanabilirsiniz.

   d. Sağ taraftaki proje türleri listesinden **sınıf kitaplığı** ' nı seçin.

   e. **Ad**için **MyDotNetActivity** girin.

   f. **Konum**için **C:\\ADF** 'yi seçin. ADF klasörü yoksa **ADF** 'yi oluşturun.

   g. Projeyi oluşturmak için **Tamam**'ı seçin.

1. **Araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.

1. Paket Yöneticisi konsolunda, Microsoft. Azure. Management. DataFactory 'yi içeri aktarmak için aşağıdaki komutu yürütün:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. **Azure depolama** NuGet paketini projeye aktarın. Bu örnekte blob Storage API 'sini kullandığınız için bu pakete ihtiyacınız vardır:

    ```powershell
    Install-Package Az.Storage
    ```
1. Aşağıdaki using yönergelerini projedeki kaynak dosyasına ekleyin:

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;
    
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
1. Ad alanının adını **MyDotNetActivityNS**olarak değiştirin.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Sınıfın adını **MyDotNetActivity**olarak değiştirin ve aşağıda gösterildiği gibi **ıdotnetactivity** arabiriminden türetirsiniz:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. **Idotnetactivity** arabiriminin **Execute** yöntemini **MyDotNetActivity** sınıfına uygulayın (ekleyin). Aşağıdaki örnek kodu yöntemine kopyalayın. Bu yöntemde kullanılan mantığın bir açıklaması için, [yöntemi yürütme](#execute-method) bölümüne bakın.

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {
    
       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.
    
       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
       // Initialize the continuation token before using it in the do-while loop.
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
    
           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);
    
       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. Sınıfına aşağıdaki yardımcı yöntemleri ekleyin. Bu yöntemler **Execute** yöntemi tarafından çağrılır. En önemli, **Calculate** yöntemi her Blobun üzerinde yineleme yapan kodu yalıtır.

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
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
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
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
       return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
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
    GetFolderPath yöntemi, DataSet 'in gösterdiği klasörün yolunu döndürür ve GetFileName yöntemi, veri kümesinin işaret ettiği blob/dosyanın adını döndürür.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Calculate yöntemi, giriş dosyalarında (klasördeki Bloblar) "Microsoft" anahtar sözcüğünün örnek sayısını hesaplar. "Microsoft" arama terimi kodda sabit kodlanmış.

1. Projeyi derleyin. Menüden **Oluştur** ' u seçin ve ardından **Build Solution**' ı seçin.

1. Windows Gezgini 'ni başlatın ve **bin\\hata ayıklama** veya **bin\\sürüm** klasörüne gidin. Klasör seçimi, derleme türüne bağlıdır.

1. **\\bin\\hata ayıklama** klasöründeki tüm ikilileri Içeren bir **MyDotNetActivity. zip** dosyası oluşturun. MyDotNetActivity dahil etmek isteyebilirsiniz. bir hata oluştuğunda soruna neden olan kaynak kodundaki satır numarası gibi ek ayrıntılar almanız için **pdb** dosyası.

   ![Bin\Debug klasör listesi](./media/data-factory-data-processing-using-batch/image5.png)

1. **MyDotNetActivity. zip** ' i blob kapsayıcısına blob olarak yükleyin. Bu, ADFTutorialDataFactory içindeki StorageLinkedService bağlı hizmetinin kullandığı blob depolamadaki `customactivitycontainer`. Zaten mevcut değilse blob kapsayıcısı `customactivitycontainer` oluşturun.

#### <a name="execute-method"></a>Execute yöntemi
Bu bölüm yürütme yöntemindeki kod hakkında daha fazla ayrıntı sağlar.

1. Giriş koleksiyonu aracılığıyla yineleme üyeleri [Microsoft. WindowsAzure. Storage. blob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob) ad alanında bulunur. Blob koleksiyonu aracılığıyla yinelemek için **Blobcontinuationtoken** sınıfını kullanmanız gerekir. Temelde, döngüden çıkma mekanizması olarak belirtece sahip bir do-while döngüsü kullanmanız gerekir. Daha fazla bilgi için bkz. [.net 'Ten blob depolamayı kullanma](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Temel döngü burada gösterilmektedir:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    
                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.
    
     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
    } while (continuationToken != null);

    ```
   Daha fazla bilgi için [Listblobskesimli](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobssegmented) yönteme yönelik belgelere bakın.

1. Blob kümesi üzerinden mantıksal olarak çalışmaya yönelik kod, do-while döngüsünün içinde görüntülenir. **Execute** yönteminde do-while döngüsü, Blobların listesini **Calculate**adlı bir yönteme geçirir. Yöntemi, **kesim adlı bir** dize değişkeni döndürür ve bu, kesimdeki tüm Bloblar aracılığıyla yinelendirilen bir sonucudur.

   **Hesaplama** yöntemine geçirilen blob Içindeki "Microsoft" arama teriminin oluşum sayısını döndürür.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. **Hesaplama** yöntemi tamamlandıktan sonra, yeni bir bloba yazılması gerekir. İşlenen her blob kümesi için, sonuçlarla yeni bir blob yazılabilir. Yeni bir bloba yazmak için önce çıktı veri kümesini bulun.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. Kod ayrıca klasör yolunu (depolama kapsayıcısı adı) almak için **GetFolderPath** yardımcı yöntemini çağırır.

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   GetFolderPath yöntemi, veri kümesi nesnesini FolderPath adlı bir özelliğe sahip bir AzureBlobDataSet öğesine yayınlar.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
1. Kod, dosya adını (blob adı) almak için **GetFileName** metodunu çağırır. Kod, klasör yolunu almak için kullanılan önceki koda benzerdir.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
1. Dosyanın adı bir URI nesnesi oluşturularak yazılır. URI Oluşturucusu, kapsayıcı adını döndürmek için **Blobendpoint** özelliğini kullanır. Çıkış blob URI 'sini oluşturmak için klasör yolu ve dosya adı eklenir.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Dosyanın adı yazıldıktan sonra, **Calculate** yönteminden çıktı dizesini yeni bir bloba yazabilirsiniz:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Veri Fabrikası oluşturma
[Özel etkinlik oluştur](#create-the-custom-activity) bölümünde özel bir etkinlik oluşturdunuz ve ZIP dosyasını ikili dosyalar ve PDB dosyası ile bir blob kapsayıcısına yüklediniz. Bu bölümde, özel etkinliği kullanan bir işlem hattı ile veri fabrikası oluşturacaksınız.

Özel etkinlik için giriş veri kümesi, blob depolamada giriş klasöründeki (`mycontainer\\inputfolder`) Blobları (dosyalar) temsil eder. Etkinliğin çıkış veri kümesi, blob depolamada çıkış klasöründeki (`mycontainer\\outputfolder`) çıkış bloblarını temsil eder.

Giriş klasörlerine bir veya daha fazla dosya bırakın:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Örneğin, her bir klasöre aşağıdaki içerikle bir dosya (File. txt) bırakın:

```
test custom activity Microsoft test custom activity Microsoft
```

Her giriş klasörü, klasör iki veya daha fazla dosyaya sahip olsa bile veri fabrikasındaki bir dilime karşılık gelir. Her dilim işlem hattı tarafından işlendiğinde, özel etkinlik söz konusu dilimin giriş klasöründeki tüm Bloblar arasında yinelenir.

Aynı içeriğe sahip beş çıkış dosyası görürsünüz. Örneğin, 2015-11-16-00 klasöründeki dosyayı işlerken çıkış dosyası aşağıdaki içeriğe sahiptir:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Aynı içeriğe sahip birden fazla dosyayı (File. txt, dosya2. txt, File3. txt) giriş klasörüne düşürüyor, çıkış dosyasında aşağıdaki içeriği görürsünüz. Her klasör (2015-11-16-00, vb.), klasörde birden fazla giriş dosyası olsa da bu örnekteki bir dilime karşılık gelir.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Çıkış dosyasında şu anda üç satır bulunur (örneğin, dilimle ilişkili klasörde her giriş dosyası (blob) için bir tane (2015-11-16-00).

Her etkinlik çalıştırması için bir görev oluşturulur. Bu örnekte, ardışık düzende yalnızca bir etkinlik vardır. Bir dilim işlem hattı tarafından işlendiğinde, özel etkinlik dilimi işlemek için toplu Iş üzerinde çalışır. Beş dilim olduğu için (her dilim birden çok blob veya dosya içerebilir), Batch 'te beş görev oluşturulur. Bir görev toplu Iş üzerinde çalıştığında, çalışan özel etkinliktir.

Aşağıdaki izlenecek yol ek ayrıntılar sağlar.

#### <a name="step-1-create-the-data-factory"></a>1\. Adım: Veri Fabrikası oluşturma
1. [Azure Portal](https://portal.azure.com/)oturum açtıktan sonra aşağıdaki adımları uygulayın:

   a. Sol taraftaki menüden **Yeni** ' yi seçin.

   b. **Yeni** dikey pencerede **veri ve analiz** seçin.

   c. **Veri analizi** dikey penceresinde **Data Factory** ' yi seçin.

1. **Yeni Veri Fabrikası** dikey penceresinde ad Için **Customactivityfactory** girin. Veri fabrikasının adı genel olarak benzersiz olmalıdır. "Data Factory Name CustomActivityFactory kullanılamıyor" hatasını alırsanız veri fabrikasının adını değiştirin. Örneğin, yournameCustomActivityFactory ' yi kullanın ve Data Factory 'yi tekrar oluşturun.

1. **Kaynak grubu adı**' nı seçin ve var olan bir kaynak grubunu seçin ya da bir kaynak grubu oluşturun.

1. Veri fabrikasının oluşturulmasını istediğiniz aboneliğin ve bölgenin doğru olduğundan emin olun.

1. **Yeni Veri Fabrikası** dikey penceresinde **Oluştur** ' u seçin.

1. Data Factory, portalın panosunda oluşturulur.

1. Data Factory başarıyla oluşturulduktan **sonra Data Factory sayfasını görürsünüz** . Bu, veri fabrikasının içeriğini gösterir.

   ![Veri Fabrikası sayfası](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>2\. Adım: bağlı hizmetler oluşturma
Bağlı hizmetler veri depolarını veya işlem hizmetlerini bir veri fabrikasına bağlar. Bu adımda, depolama hesabınızı ve Batch hesabınızı veri fabrikasına bağlarsınız.

#### <a name="create-an-azure-storage-linked-service"></a>Azure Depolama bağlı hizmeti oluşturma
1. **Customactivityfactory**için **Data Factory** dikey penceresinde **Yazar ve dağıt** kutucuğunu seçin. Data Factory Düzenleyicisi görüntülenir.

1. Komut çubuğunda **Yeni veri deposu** ' nu seçin ve **Azure Storage** ' ı seçin. Düzenleyicide bir depolama bağlı hizmeti oluşturmak için kullandığınız JSON betiği görüntülenir.

   ![Yeni veri deposu](./media/data-factory-data-processing-using-batch/image7.png)

1. **Hesap adı** değerini depolama hesabınızın adıyla değiştirin. **Hesap anahtarı** değerini depolama hesabının erişim anahtarıyla değiştirin. Depolama erişim anahtarınızı nasıl alabileceğinizi öğrenmek için bkz. [depolama erişim anahtarlarını görüntüleme, kopyalama ve yeniden](../../storage/common/storage-account-manage.md#access-keys)oluşturma.

1. Bağlı hizmeti dağıtmak için komut çubuğunda **Dağıt**’ı seçin.

   ![Dağıtma](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Azure Batch bağlı hizmeti oluşturma
Bu adımda, Batch hesabınız için, Data Factory özel etkinliğini çalıştırmak için kullanılan bir bağlı hizmet oluşturursunuz.

1. Komut çubuğunda **Yeni işlem** ' ı seçin ve Azure Batch ' yi seçin **.** Düzenleyicide Batch bağlantılı hizmet oluşturmak için kullandığınız JSON betiği görüntülenir.

1. JSON betiğine:

   a. **Hesap adını** Batch hesabınızın adıyla değiştirin.

   b. **Erişim anahtarını** Batch hesabının erişim anahtarıyla değiştirin.

   c. **PoolName** özelliği IÇIN havuzun kimliğini girin. Bu özellik için havuz adı ya da havuz KIMLIĞI belirtebilirsiniz.

   d. **Batchuri** JSON özelliği IÇIN Batch URI 'sini girin.

      > [!IMPORTANT]
      > **Batch hesabı** dikey penceresinin URL 'si şu biçimdedir: \<AccountName\>.\<bölgesi\>. batch.azure.com. JSON betiğinin **Batchuri** özelliği için, A88 "AccountName" öğesini kaldırmanız gerekir. * * URL 'den. `"batchUri": "https://eastus.batch.azure.com"` bunun bir örneğidir.
      >
      >

      ![Batch hesabı dikey penceresi](./media/data-factory-data-processing-using-batch/image9.png)

      **PoolName** özelliği için havuzun adı yerıne havuzun kimliğini de belirtebilirsiniz.

      > [!NOTE]
      > Data Factory hizmeti, HDInsight için yaptığı gibi Batch için isteğe bağlı bir seçeneği desteklemez. Bir veri fabrikasında yalnızca kendi toplu Iş havuzunuzu kullanabilirsiniz.
      >
      >
   
   e. **Linkedservicename** özelliği için **StorageLinkedService** belirtin. Bu bağlı hizmeti önceki adımda oluşturdunuz. Bu depolama, dosyalar ve Günlükler için bir hazırlama alanı olarak kullanılır.

1. Bağlı hizmeti dağıtmak için komut çubuğunda **Dağıt**’ı seçin.

#### <a name="step-3-create-datasets"></a>3\. Adım: veri kümeleri oluşturma
Bu adımda, girdi ve çıktı verilerini temsil edecek veri kümeleri oluşturacaksınız.

#### <a name="create-the-input-dataset"></a>Girdi veri kümesini oluşturma
1. Data Factory düzenleyicisinde, araç çubuğundaki **Yeni veri kümesi** düğmesini seçin. Açılan listeden **Azure Blob depolama** ' yı seçin.

1. Sağ bölmedeki JSON betiğini aşağıdaki JSON kod parçacığıyla değiştirin:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
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

    Bu kılavuzda daha sonra başlangıç zamanı 2015-11-16T00:00:00Z ve bitiş zamanı 2015-11-16T05:00:00Z ile Bu izlenecek bir işlem hattı oluşturursunuz. Her saat veri üretmek üzere zamanlandı. bu nedenle beş giriş/çıkış dilimi vardır ( **00**: 00:00-\> **05**: 00:00).

    Giriş veri kümesi için **Sıklık** ve **Aralık** **saat** ve **1**olarak ayarlanır, bu da giriş diliminin saatlik olarak kullanılabildiği anlamına gelir.

    Her bir dilim için başlangıç saati, önceki JSON kod parçacığında bir **Dilimestart** sistem değişkeni tarafından temsil edilir. Her dilimin başlangıç zamanları aşağıda verilmiştir.

    | **'In** | **Başlangıç saati**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**: 00:00 |
    | 2         | 2015-11-16T**01**: 00:00 |
    | 3         | 2015-11-16T**02**: 00:00 |
    | 4         | 2015-11-16T**03**: 00:00 |
    | 5         | 2015-11-16T**04**: 00:00 |

    **FolderPath** , dilim başlangıç saatinin yıl, ay, gün ve saat bölümü (**dilimestart**) kullanılarak hesaplanır. Bir giriş klasörünün bir dilimle nasıl eşlenildiği aşağıda verilmiştir.

    | **'In** | **Başlangıç saati**          | **Giriş klasörü**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**: 00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**: 00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**: 00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**: 00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**: 00:00 | 2015-11-16-**04** |

1. **Inputdataset** tablosunu oluşturmak ve dağıtmak için araç çubuğunda **Dağıt** ' ı seçin.

#### <a name="create-the-output-dataset"></a>Çıktı veri kümesini oluşturma
Bu adımda, çıkış verilerini göstermek için AzureBlob türünde başka bir veri kümesi oluşturursunuz.

1. Data Factory düzenleyicisinde, araç çubuğundaki **Yeni veri kümesi** düğmesini seçin. Açılan listeden **Azure Blob depolama** ' yı seçin.

1. Sağ bölmedeki JSON betiğini aşağıdaki JSON kod parçacığıyla değiştirin:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
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

    Her giriş dilimi için bir çıktı blobu/dosyası oluşturulur. Her bir dilim için bir çıktı dosyası adı verilmiştir. Tüm çıkış dosyaları, `mycontainer\\outputfolder`bir çıkış klasöründe oluşturulur.

    | **'In** | **Başlangıç saati**          | **Çıkış dosyası**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**: 00:00 | 2015-11-16-**00. txt** |
    | 2         | 2015-11-16T**01**: 00:00 | 2015-11-16-**01. txt** |
    | 3         | 2015-11-16T**02**: 00:00 | 2015-11-16-**02. txt** |
    | 4         | 2015-11-16T**03**: 00:00 | 2015-11-16-**03. txt** |
    | 5         | 2015-11-16T**04**: 00:00 | 2015-11-16-**04. txt** |

    Bir giriş klasöründeki tüm dosyaların (örneğin, 2015-11-16-00) başlangıç saati 2015-11-16-00 olan bir dilimin parçası olduğunu unutmayın. Bu dilim işlendiğinde, özel etkinlik her bir dosyayı tarar ve çıkış dosyasında "Microsoft" arama teriminin oluşum sayısını içeren bir satır üretir. 2015-11-16-00 klasöründe üç dosya varsa, 2015-11-16 -00. txt çıkış dosyasında üç satır vardır.

1. **Outputdataset**'i oluşturmak ve dağıtmak için araç çubuğunda **Dağıt** ' ı seçin.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>4\. Adım: özel bir etkinlik ile işlem hattını oluşturma ve çalıştırma
Bu adımda, daha önce oluşturduğunuz özel etkinliği tek bir etkinliğe sahip bir işlem hattı oluşturacaksınız.

> [!IMPORTANT]
> **Dosya. txt dosyasını** blob kapsayıcısındaki giriş klasörlerine yüklemediyseniz, işlem hattını oluşturmadan önce bunu yapın. **Ivduraklatılan** ÖZELLIĞI, JSON ardışık düzeninde false olarak ayarlanır, bu nedenle **Başlangıç** tarihi geçmişte olduğundan işlem hattı hemen çalışır.
>
>

1. Data Factory düzenleyicisinde, komut çubuğunda **Yeni işlem hattı** ' nı seçin. Komutu görmüyorsanız, görüntülemek için üç nokta simgesini seçin.

1. Sağ bölmedeki JSON betiğini aşağıdaki JSON kod parçacığıyla değiştirin:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
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
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Aşağıdaki noktalara dikkat edin:

   * Yalnızca bir etkinlik ardışık düzen içinde ve **Dotnetactivity**türünde.
   * **AssemblyName** , dll **MyDotNetActivity. dll**' nin adına ayarlanır.
   * **EntryPoint** , **MyDotNetActivityNS. MyDotNetActivity**olarak ayarlanır. Bu, temel olarak \<ad alanı\>.\<ClassName\> kodunuzda.
   * **PackageLinkedService** , özel etkinlik ZIP dosyasını içeren BLOB depolama alanına Işaret eden **StorageLinkedService**olarak ayarlanır. Giriş/çıkış dosyaları ve özel etkinlik ZIP dosyası için farklı depolama hesapları kullanıyorsanız, başka bir depolama bağlı hizmeti oluşturmanız gerekir. Bu makalede aynı depolama hesabını kullandığınız varsayılır.
   * **PackageFile** , **customactivitycontainer/MyDotNetActivity. zip**olarak ayarlanır. \<containerforthezip\>/\<nameofthezip. zip\>biçimindedir.
   * Özel etkinlik **ınputdataset** 'i giriş ve **outputdataset** olarak çıktı olarak alır.
   * Özel etkinliğin **Linkedservicename** özelliği **AzureBatchLinkedService**öğesine işaret eder. Bu, özel etkinliğin Batch üzerinde çalıştırılması gerektiğini Data Factory söyler.
   * **Eşzamanlılık** ayarı önemlidir. 1 olan varsayılan değeri kullanırsanız, toplu Iş havuzunda iki veya daha fazla işlem düğümü olsa bile, dilimler bir den sonra işlenir. Bu nedenle, Batch 'in paralel işleme özelliğinden faydalanırsınız. **Eşzamanlılık** değerini daha yüksek bir değere ayarlarsanız 2, iki dilim (toplu işteki iki göreve karşılık gelir) aynı anda işlenebileceği anlamına gelir. Bu durumda, Batch havuzundaki VM 'Ler kullanılır. Eşzamanlılık özelliğini uygun şekilde ayarlayın.
   * Varsayılan olarak herhangi bir noktada VM üzerinde yalnızca bir görev (dilim) yürütülür. Varsayılan olarak, bir Batch havuzu için **VM başına en fazla görev** 1 ' e ayarlanır. Önkoşulların bir parçası olarak, bu özelliği 2 olarak ayarlanmış bir havuz oluşturdunuz. Bu nedenle, iki Data Factory dilimi aynı anda bir VM üzerinde çalışabilir.
     - **Isduraklatılan** özelliği varsayılan olarak false olarak ayarlanır. Dilimler geçmişte çalışmaya başlayacağı için işlem hattı Bu örnekte hemen çalışır. İşlem hattını duraklatmak ve yeniden başlatmak için **false** olarak ayarlamak için bu özelliği **true** olarak ayarlayabilirsiniz.
     -   **Başlangıç** ve **bitiş** zamanları beş saat dışında. Dilimler saatlik olarak üretilir, dolayısıyla işlem hattı tarafından beş dilim üretilir.

1. İşlem hattını dağıtmak için komut çubuğundan **Dağıt**’ı seçin.

#### <a name="step-5-test-the-pipeline"></a>5\. Adım: ardışık düzeni test etme
Bu adımda, dosyaları giriş klasörlerine bırakarak işlem hattını test edersiniz. Her giriş klasörü için tek bir dosya ile işlem hattını test ederek başlayın.

1. Azure portal **Veri Fabrikası** dikey penceresinde **Diyagram**' ı seçin.

   ![Diyagram](./media/data-factory-data-processing-using-batch/image10.png)

1. **Diyagram** görünümünde, **ınputdataset**giriş veri kümesine çift tıklayın.

   ![Inputdataset](./media/data-factory-data-processing-using-batch/image11.png)

1. **Inputdataset** dikey penceresi, beş dilimin tümüyle hazırlanmakta görünür. Her dilim için **dılım başlangıç saati** ve **dilim bitiş saati** ' ne dikkat edin.

   ![Giriş dilimi başlangıç ve bitiş zamanları](./media/data-factory-data-processing-using-batch/image12.png)

1. **Diyagram** görünümünde **outputdataset**' i seçin.

1. Beş çıkış dilimi üretildiyse, **hazırlık** durumunda görünür.

   ![Çıkış dilimi başlangıç ve bitiş zamanları](./media/data-factory-data-processing-using-batch/image13.png)

1. Dilimlerle ilişkili görevleri görüntülemek ve her bir dilimin üzerinde çalıştığı VM 'leri görmek için portalını kullanın. Daha fazla bilgi için [Data Factory ve Batch tümleştirmesi](#data-factory-and-batch-integration) bölümüne bakın.

1. Çıktı dosyaları blob depolamadaki `outputfolder` `mycontainer` altında görüntülenir.

   ![Depolamadaki çıkış dosyaları](./media/data-factory-data-processing-using-batch/image15.png)

   Her giriş dilimi için bir tane olmak üzere beş çıkış dosyası listelenir. Çıkış dosyalarının her biri aşağıdaki çıktıya benzer içeriğe sahiptir:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Aşağıdaki diyagramda, Data Factory dilimlerinin toplu Işteki görevlerle nasıl eşlendiğini gösterilmektedir. Bu örnekte, bir dilimin yalnızca bir çalıştırması vardır.

   ![Dilim eşleme diyagramı](./media/data-factory-data-processing-using-batch/image16.png)

1. Şimdi bir klasörde birden çok dosya deneyin. **Dosya2. txt**, **File3. txt**, **file4. txt**ve **file5. txt** dosyalarını **2015-11-06-01**klasöründeki File. txt ile aynı içerikle oluşturun.

1. Çıkış klasöründe **2015-11-16 -01. txt**çıkış dosyasını silin.

1. **Outputdataset** dikey penceresinde, **dilim başlangıç saati** **11/16/2015 01:00:00**olarak ayarlanan dilime sağ tıklayın. Dilimi yeniden çalıştırmak/yeniden işlemek için **Çalıştır** ' ı seçin. Dilimde artık tek bir dosya yerine beş dosya vardır.

    ![Çalıştırın](./media/data-factory-data-processing-using-batch/image17.png)

1. Dilim çalıştıktan ve durumu **başlamaya**başladıktan sonra, bu dilimin çıkış dosyasındaki içeriği doğrulayın (**2015-11-16 -01. txt**). Çıktı dosyası, blob depolamadaki `outputfolder` `mycontainer` altında görüntülenir. Dilimin her bir dosyası için bir satır olmalıdır.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Beş giriş dosyası ile denemeden önce 2015-11-16 -01. txt çıkış dosyasını silmediyseniz, önceki dilim çalıştırmasında bir satır ve geçerli dilim çalıştırmasının beş satırı görürsünüz. Varsayılan olarak, içerik zaten varsa çıkış dosyasına eklenir.
>
>

#### <a name="data-factory-and-batch-integration"></a>Data Factory ve toplu tümleştirme
Data Factory hizmeti `adf-poolname:job-xxx`adında bir iş oluşturur.

![Batch işleri](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Bir dilimin her etkinlik çalışması için işteki bir görev oluşturulur. 10 dilim işlenmek üzere hazırsanız, işte 10 görev oluşturulur. Havuzda birden çok işlem düğümünüz varsa, paralel olarak çalışan birden fazla dilime sahip olabilirsiniz. İşlem düğümü başına en fazla görev sayısı birden fazla olarak ayarlandıysa, aynı işlem üzerinde birden fazla dilim çalıştırılabilir.

Bu örnekte, beş dilim bulunur, bu nedenle toplu Işte beş görev vardır. Veri fabrikasında JSON ardışık düzeninde **5** olarak ayarlanmış ve **2** VM 'ye sahip Batch havuzunda **VM başına en fazla görev** sayısı **2** **olarak ayarlandığında,** görevler hızlı çalışır. (Görevlerin başlangıç ve bitiş zamanlarını kontrol edin.)

Portal 'ı kullanarak, dilimlerle ilişkili toplu işi ve bunların görevlerini görüntüleyin ve her bir dilimin üzerinde çalıştığı VM 'yi görün.

![Toplu iş görevleri](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>İşlem hattında hata ayıklama
Hata ayıklama birkaç temel teknikten oluşur.

1. Giriş Dilimi **Ready**olarak ayarlanmamışsa, giriş klasörü yapısının doğru olduğundan ve bu dosya. txt dosyasının giriş klasörlerinde bulunduğundan emin olun.

   ![Giriş klasörü yapısı](./media/data-factory-data-processing-using-batch/image3.png)

1. Özel etkinliğinizin **Execute** yönteminde, sorunları gidermenize yardımcı olan bilgileri günlüğe kaydetmek Için **ıactivitygünlükçü** nesnesini kullanın. Günlüğe kaydedilen iletiler Kullanıcı\_0. log dosyasında görünür.

   **Outputdataset** dikey penceresinde dilimi seçerek Ilgili dilimin **veri dilimi** dikey penceresini görüntüleyin. **Etkinlik çalıştırmaları**bölümünde, dilim için bir etkinlik çalıştırması görürsünüz. Komut çubuğunda **Çalıştır** ' ı seçerseniz, aynı dilim için başka bir etkinlik çalıştırması başlatabilirsiniz.

   Etkinlik çalıştırmasını seçtiğinizde, **etkinlik çalıştırma ayrıntıları** dikey penceresini bir günlük dosyaları listesi ile görürsünüz. Günlüğe kaydedilen iletileri Kullanıcı\_0. log dosyasında görürsünüz. Bir hata oluştuğunda, yeniden deneme sayısı ardışık düzen/etkinlik JSON 'u 3 olarak ayarlandığından, üç etkinlik çalıştırması görürsünüz. Etkinlik çalıştırmasını seçtiğinizde, hatayı gidermek için gözden geçirebileceğiniz günlük dosyalarını görürsünüz.

   ![OutputDataset ve veri dilimi dikey pencereleri](./media/data-factory-data-processing-using-batch/image18.png)

   Günlük dosyaları listesinde **User-0. log**öğesini seçin. Sağ bölmede, **ıactivitygünlükçü. Write** metodunu kullanmanın sonuçları görüntülenir.

   ![Etkinlik çalışma ayrıntıları dikey penceresi](./media/data-factory-data-processing-using-batch/image19.png)

   Tüm sistem hata iletileri ve özel durumlar için System-0. log dosyasına bakın.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Hata ayrıntılarının bir hata oluştuğunda çağrı yığını gibi bilgileri içermesi için **pdb** dosyasını ZIP dosyasına ekleyin.

1. Özel etkinliğin ZIP dosyasındaki tüm dosyaların alt klasörleri olmayan en üst düzeyde olması gerekir.

   ![Özel etkinlik ZIP dosyası listesi](./media/data-factory-data-processing-using-batch/image20.png)

1. **AssemblyName** (MyDotNetActivity. dll), **entryPoint** (MyDotNetActivityNS. MyDotNetActivity), **PackageFile** (customactivitycontainer/MyDotNetActivity. zip) ve **packageLinkedService** (öğesinin işaret etmelidir) olduğundan emin olun. ZIP dosyasını içeren BLOB depolama) doğru değerlere ayarlanır.

1. Bir hatayı düzeltmeniz ve dilimi yeniden işlemek istiyorsanız **Outputdataset** dikey penceresinde dilimi sağ tıklatın ve **Çalıştır**' ı seçin.

   ![OutputDataset dikey çalıştırma seçeneği](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Bir kapsayıcı `adfjobs`adlı blob depoınızda bulunur. Bu kapsayıcı otomatik olarak silinmez, ancak çözümü test etmeyi bitirdikten sonra güvenle silebilirsiniz. Benzer şekilde, Data Factory çözümü, `adf-\<pool ID/name\>:job-0000000001`adlı bir toplu iş oluşturur. İsterseniz çözümü test ettikten sonra bu işi silebilirsiniz.
   >
   >
1. Özel etkinlik, paketinizin **app. config** dosyasını kullanmaz. Bu nedenle, kodunuz yapılandırma dosyasından herhangi bir bağlantı dizesini okuduğunda, çalışma zamanında çalışmaz. Batch kullandığınızda en iyi uygulama, Azure Key Vault tüm gizli dizileri tutamaktır. Ardından, anahtar kasasını korumak ve sertifikayı Batch havuzuna dağıtmak için sertifika tabanlı hizmet sorumlusu kullanın. .NET özel etkinliği çalışma zamanında anahtar kasasından gizli dizilerle erişebilir. Bu genel çözüm yalnızca bir bağlantı dizesi değil, herhangi bir gizli dizi türüne ölçeklendirebilir.

    Daha kolay bir geçici çözüm vardır, ancak en iyi yöntem değildir. Bağlantı dizesi ayarlarıyla bir SQL veritabanı bağlı hizmeti oluşturabilirsiniz. Ardından, bağlı hizmeti kullanan bir veri kümesi oluşturabilir ve veri kümesini özel .NET etkinliğine bir kukla giriş veri kümesi olarak zincirleyebilirsiniz. Ardından, bağlantılı hizmetin bağlantı dizesine özel etkinlik kodunda erişebilirsiniz. Çalışma zamanında düzgün çalışmalıdır.  

#### <a name="extend-the-sample"></a>Örneği Genişlet
Data Factory ve Batch özellikleri hakkında daha fazla bilgi edinmek için bu örneği genişletebilirsiniz. Örneğin, farklı bir zaman aralığındaki dilimleri işlemek için aşağıdaki adımları uygulayın:

1. Aşağıdaki alt klasörleri `inputfolder`ekleyin: 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 ve 2015-11-16-09. Giriş dosyalarını bu klasörlere yerleştirin. İşlem hattının bitiş saatini `2015-11-16T05:00:00Z` `2015-11-16T10:00:00Z`olarak değiştirin. **Diyagram** görünümünde **ınputdataset** ' e çift tıklayın ve giriş dilimlerinin hazırlandığından emin olun. Çıkış dilimlerinin durumunu görmek için **Outputdataset** ' e çift tıklayın. Bunlar **, hazırlama durumundaysa,** çıkış dosyaları için çıkış klasörünü kontrol edin.

1. Özellikle toplu Işte oluşan işleme, çözümünüzün performansını nasıl etkilediğini anlamak için **eşzamanlılık** ayarını artırın veya azaltın. **Eşzamanlılık** ayarı hakkında daha fazla bilgi için bkz. "4. Adım: işlem hattını özel bir etkinlikle oluşturma ve çalıştırma."

1. **VM başına en fazla**/daha düşük görev içeren bir havuz oluşturun. Oluşturduğunuz yeni havuzu kullanmak için, Data Factory çözümünde Batch bağlantılı hizmetini güncelleştirin. **VM başına en fazla görev** ayarı hakkında daha fazla bilgi için bkz. "4. Adım: işlem hattını özel bir etkinlikle oluşturma ve çalıştırma."

1. **Otomatik ölçeklendirme** özelliğiyle bir Batch havuzu oluşturun. Batch havuzundaki işlem düğümlerinin otomatik olarak ölçeklendirilmesi, uygulamanız tarafından kullanılan işleme gücünün dinamik ayarlamadır. 

    Burada örnek formül aşağıdaki davranışa erişir. Havuz başlangıçta oluşturulduğunda, tek bir VM ile başlar. $PendingTasks ölçümü, çalışan ve etkin (sıraya alınmış) durumlarda görev sayısını tanımlar. Formül, son 180 saniye içinde bekleyen görevlerin ortalama sayısını bulur ve Targetadanmış 'yi uygun şekilde ayarlar. Targetadanmış, 25 sanal makine dışında hiçbir şekilde geçmeyeceğinden emin olmanızı sağlar. Yeni görevler gönderildiğinde havuz otomatik olarak büyür. Görevler tamamlantıkça, VM 'Ler tek bir tane tarafından ücretsizdir ve otomatik ölçeklendirme bu VM 'Leri küçültür. StartingNumberOfVMs ve Maxnumberofvm 'Leri ihtiyaçlarınıza göre ayarlayabilirsiniz.
 
    Otomatik ölçeklendirme formülü:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Daha fazla bilgi için bkz. [Batch havuzundaki işlem düğümlerini otomatik olarak ölçeklendirme](../../batch/batch-automatic-scaling.md).

   Havuz varsayılan [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)kullanıyorsa, Batch hizmetinin özel etkinliği ÇALıŞTıRMADAN önce VM 'yi hazırlamak 15 ila 30 dakika sürebilir. Havuz farklı bir autoScaleEvaluationInterval kullanıyorsa, Batch hizmeti autoScaleEvaluationInterval Plus 10 dakika alabilir.

1. Örnek çözümde **Execute** yöntemi, çıkış veri dilimi oluşturmak için bir giriş veri dilimini işleyen **Calculate** metodunu çağırır. Giriş verilerini işlemek için kendi yönteminizi yazabilir ve **Execute** yöntemi içindeki **Calculate** yöntemi çağrısını yönteminizin bir çağrısıyla değiştirebilirsiniz.

### <a name="next-steps-consume-the-data"></a>Sonraki adımlar: verileri tüketme
Verileri tamamladıktan sonra, Power BI gibi çevrimiçi araçlarla kullanabilirsiniz. Power BI ve Azure 'da nasıl kullanacağınızı anlamanıza yardımcı olacak bağlantılar aşağıda verilmiştir:

* [Power BI bir veri kümesini keşfet](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Power BI Desktop kullanmaya başlayın](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Power BI verileri yenileme](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure ve Power BI: temel genel bakış](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Başvurular
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Data Factory hizmetine giriş](data-factory-introduction.md)
  * [Data Factory kullanmaya başlayın](data-factory-build-your-first-pipeline.md)
  * [Data Factory işlem hattında özel etkinlikler kullanma](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Toplu Iş temelleri](../../batch/batch-technical-overview.md)
  * [Batch özelliklerine genel bakış](../../batch/batch-api-basics.md)
  * [Azure portal Batch hesabı oluşturma ve yönetme](../../batch/batch-account-create-portal.md)
  * [.NET için Batch istemci kitaplığı ile çalışmaya başlama](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
