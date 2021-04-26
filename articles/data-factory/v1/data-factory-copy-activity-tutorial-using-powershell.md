---
title: 'Öğretici: Azure PowerShell kullanarak verileri taşımak için işlem hattı oluşturma '
description: Bu öğreticide, Azure PowerShell kullanarak Kopyalama Etkinliği ile bir Azure Data Factory işlem hattı oluşturursunuz.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 54c296ed8013b9962de9487cfec3e2568c03e738
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377049"
---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Öğretici: Azure PowerShell kullanarak verileri taşıyan bir Data Factory işlem hattı oluşturma
> [!div class="op_single_selector"]
> * [Genel bakış ve ön koşullar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopyalama Sihirbazı](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager şablonu](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API’si](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz. [kopyalama etkinliği öğreticisi](../quickstart-create-data-factory-powershell.md). 

Bu makalede, bir Azure Blob depolama alanından Azure SQL veritabanına veri kopyalayan bir işlem hattı ile veri fabrikası oluşturmak için PowerShell 'in nasıl kullanılacağını öğrenirsiniz. Azure Data Factory’yi ilk kez kullanıyorsanız bu öğreticiyi tamamlamadan önce [Azure Data Factory’ye Giriş](data-factory-introduction.md) makalesini okuyun.   

Bu öğreticide, içinde bir etkinlik olan işlem hattı oluşturursunuz: Kopyalama Etkinliği. Kopyalama etkinliği, verileri, desteklenen bir veri deposundan desteklenen bir havuz veri deposuna kopyalar. Kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Etkinlik, çeşitli veri depolama alanları arasında güvenli, güvenilir ve ölçeklenebilir bir yolla veri kopyalayabilen genel olarak kullanılabilir bir hizmet tarafından desteklenir. Kopyalama etkinliği hakkında daha fazla bilgi için bkz. [veri taşıma etkinlikleri](data-factory-data-movement-activities.md).

Bir işlem hattında birden fazla etkinlik olabilir. Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliğin diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [bir işlem hattında birden fazla etkinlik](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE]
> Bu makalede, tüm Data Factory cmdlet'lerini kapsamaz. Bu cmdlet’leri hakkında kapsamlı bilgi için bkz. [Data Factory Cmdlet Başvurusu](/powershell/module/az.datafactory).
> 
> Bu öğreticideki veri işlem hattı, bir kaynak veri deposundaki verileri hedef veri deposuna kopyalar. Azure Data Factory kullanarak verileri dönüştürme hakkında bir öğretici için bkz. [Öğretici: Hadoop kümesi kullanarak verileri dönüştürmek için işlem hattı oluşturma](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- [Öğretici ön koşulları](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) makalesinde listelenen ön koşulları tamamlayın.
- **Azure PowerShell**'i yükler. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-Az-ps) bölümündeki yönergeleri izleyin.

## <a name="steps"></a>Adımlar
Bu eğitimin bir parçası olarak gerçekleştireceğiniz adımlar şunlardır:

1. Azure **Veri Fabrikası** oluşturun. Bu adımda, ADFTutorialDataFactoryPSH adlı bir veri fabrikası oluşturursunuz. 
1. Veri fabrikasında **bağlı hizmetler** oluşturun. Bu adımda Azure Depolama ve Azure SQL Veritabanı türünde iki bağlı hizmet oluşturursunuz. 

    AzureStorageLinkedService, Azure depolama hesabınızı veri fabrikasına bağlar. Bir kapsayıcı oluşturup verileri [ön koşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak bu depolama hesabına yüklediniz.   

    Azuressqllinkedservice, Azure SQL veritabanını Data Factory 'ye bağlar. Blob depolama alanından kopyalanan veriler bu veritabanında depolanır. Bu veritabanındaki SQL tablosunu, [ön koşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak oluşturdunuz.   
1. Veri fabrikasında girdi ve çıktı **veri kümesi oluşturma**.  

    Azure Depolama bağlı hizmeti, Data Factory hizmetinin Azure depolama hesabınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Giriş blob veri kümesi ise kapsayıcıyı ve girdi verilerini içeren klasörü belirtir.  

    Benzer şekilde, Azure SQL veritabanı bağlı hizmeti, Data Factory hizmetinin veritabanınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Çıktı SQL tablosu veri kümesi ise blob depolama alanındaki verilerin kopyalandığı veritabanında tabloyu belirtir.
1. Veri fabrikasında **işlem hattı** oluşturun. Bu adımda, kopyalama etkinliği ile bir işlem hattı oluşturursunuz.   

    Kopyalama etkinliği, verileri Azure Blob depolama alanındaki bir Blobun Azure SQL veritabanı 'ndaki bir tabloya kopyalar. Verileri desteklenen herhangi bir kaynaktan desteklenen herhangi bir hedefe kopyalamak için bir işlem hattındaki kopyalama etkinliğini kullanabilirsiniz. Desteklenen veri depolarının bir listesi için [veri taşıma etkinlikleri](data-factory-data-movement-activities.md#supported-data-stores-and-formats) makalesine bakın. 
1. İşlem hattını izleyin. Bu adımda, girdi ve çıktı veri kümelerinin dilimlerini PowerShell kullanarak **izlersiniz**.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
> [!IMPORTANT]
> Henüz yapmadıysanız, [öğreticinin ön koşullarını](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) tamamlayın.   

Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattında bir veya daha fazla etkinlik olabilir. Örneğin, verileri bir kaynaktan bir hedef veri deposuna kopyalamak için Kopyalama Etkinliği, giriş verilerini ürün çıkış verilerine dönüştürecek Hive betiğini çalıştırmak için de HDInsight Hive etkinliği. Bu adımda data factory oluşturmayla başlayalım.

1. **PowerShell**’i başlatın. Bu öğreticide sonuna kadar Azure PowerShell’i açık tutun. Kapatıp yeniden açarsanız komutları yeniden çalıştırmanız gerekir.

    Aşağıdaki komutu çalıştırın ve Azure portalda oturum açmak için kullandığınız kullanıcı adı ve parolayı girin:

    ```powershell
    Connect-AzAccount
    ```   

    Bu hesapla ilgili tüm abonelikleri görmek için aşağıdaki komutu çalıştırın:

    ```powershell
    Get-AzSubscription
    ```

    Çalışmak isteğiniz aboneliği seçmek için aşağıdaki komutu çalıştırın. **&lt; Nameofazuyeniden gönderme Scription** &gt; değerini Azure aboneliğinizin adıyla değiştirin:

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```
1. Aşağıdaki komutu çalıştırarak **ADFTutorialResourceGroup** adlı bir Azure Kaynak grubu oluşturun:

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Bu öğreticideki adımlardan bazıları **ADFTutorialResourceGroup** adlı kaynak grubunu kullandığınızı varsayar. Farklı bir kaynak grubu kullanıyorsanız, bu öğreticide ADFTutorialResourceGroup yerine onu kullanmanız gerekir.
1. **ADFTutorialDataFactoryPSH** adlı bir veri fabrikası oluşturmak için **New-azdatafactory** cmdlet 'ini çalıştırın:  

    ```powershell
    $df=New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
    Bu ad zaten alınmış olabilir. Bu nedenle, bir ön ek veya son ek getirerek veri fabrikasının adını benzersiz hale getirin (örneğin: ADFTutorialDataFactoryPSH05152017) ve komutu yeniden çalıştırın.  

Aşağıdaki noktalara dikkat edin:

* Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. Aşağıdaki hatayı alırsanız adı değiştirin (örneğin adınızADFTutorialDataFactory). Bu öğreticide adımları uygularken ADFTutorialFactoryPSH yerine bu adı kullanın. Data Factory yapıtları için bkz. [Data Factory - Adlandırma Kuralları](data-factory-naming-rules.md).

    ```
    Data factory name "ADFTutorialDataFactoryPSH" is not available
    ```
* Data Factory örnekleri oluşturmak için Azure aboneliğinde katkıda bulunan veya yönetici rolünüz olmalıdır.
* Veri fabrikasının adı gelecekte bir DNS adı olarak kaydedilmiş ve herkese görünür hale gelmiş olabilir.
* Şu hatayı alabilirsiniz: “**Bu abonelik Microsoft.DataFactory ad alanını kullanacak şekilde kaydedilmemiş.**” Aşağıdakilerden birini yapın ve yeniden yayımlamayı deneyin:

  * Azure PowerShell’de Data Factory sağlayıcısını kaydetmek için aşağıdaki komutu çalıştırın:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    Data Factory sağlayıcısının kayıtlı olduğunu onaylamak için aşağıdaki komutu çalıştırın:

    ```powershell
    Get-AzResourceProvider
    ```
  * Azure aboneliğini kullanarak [Azure portalda](https://portal.azure.com) oturum açın. Data Factory dikey penceresine gidin veya Azure portalında bir veri fabrikası oluşturun. Bu eylem sağlayıcıyı sizin için otomatik olarak kaydeder.

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Veri depolarınızı ve işlem hizmetlerinizi veri fabrikasına bağlamak için veri fabrikasında bağlı hizmetler oluşturursunuz. Bu öğreticide, Azure HDInsight veya Azure Data Lake Analytics gibi herhangi bir işlem hizmeti kullanmazsınız. Azure Depolama (kaynak) ve Azure SQL Veritabanı (hedef) türünde iki veri deposu kullanırsınız. 

Bu nedenle, AzureStorage ve AzureSqlDatabase türlerinde AzureStorageLinkedService ve AzureSqlLinkedService adlı iki bağlı hizmet oluşturursunuz.  

AzureStorageLinkedService, Azure depolama hesabınızı veri fabrikasına bağlar. Bu depolama hesabı, kapsayıcı oluşturduğunuz ve verileri [ön koşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak yüklediğiniz hesaptır.   

Azuressqllinkedservice, Azure SQL veritabanını Data Factory 'ye bağlar. Blob depolama alanından kopyalanan veriler bu veritabanında depolanır. Bu veritabanındaki emp tablosunu, [önkoşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak oluşturdunuz. 

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Azure depolama hesabı için bağlı hizmet oluşturma
Bu adımda, Azure depolama hesabınızı veri fabrikanıza bağlarsınız.

1. **C:\ADFGetStartedPSH** klasöründe şu içeriğe sahip **AzureStorageLinkedService.json** adlı bir JSON dosyası oluşturun: (Henüz yoksa ADFGetStartedPSH adlı bir klasör oluşturun.)

    > [!IMPORTANT]
    > Dosyayı kaydetmeden önce &lt;accountname&gt; ve &lt;accountkey&gt; değerlerini Azure depolama hesabınızın adı ve anahtarıyla değiştirin. 

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ``` 
1. **Azure PowerShell**’de **ADFGetStartedPSH** klasörüne geçin.
1. Bağlı hizmeti oluşturmak için **New-AzDataFactoryLinkedService** cmdlet 'ini çalıştırın: **AzureStorageLinkedService**. Bu öğreticide kullandığınız bu cmdlet ve diğer Data Factory cmdlet 'leri, **Resourcegroupname** ve **datafactoryname** parametreleri için değerleri geçirmeniz gerekir. Alternatif olarak, bir cmdlet 'i her çalıştırdığınızda ResourceGroupName ve DataFactoryName yazmadan New-AzDataFactory cmdlet 'i tarafından döndürülen DataFactory nesnesini geçirebilirsiniz. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureStorageLinkedService.json
    ```
    Örnek çıktı aşağıdaki gibidir:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ``` 

    Bu bağlı hizmeti oluşturmanın diğer yolu, DataFactory nesnesi yerine kaynak grubu adını ve veri fabrikası adını belirtmektir.  

    ```powershell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName <Name of your data factory> -File .\AzureStorageLinkedService.json
    ```

### <a name="create-a-linked-service-for-azure-sql-database"></a>Azure SQL veritabanı için bağlı hizmet oluşturma
Bu adımda, Azure SQL veritabanını Data Factory 'nize bağlarsınız.

1. C:\ADFGetStartedPSH klasöründe aşağıdaki içeriğe sahip AzureSqlLinkedService.json adlı bir JSON dosyası oluşturun:

    > [!IMPORTANT]
    > &lt;ServerName &gt; , &lt; DatabaseName &gt; , &lt; username@servername &gt; ve &lt; Password &gt; değerini sunucunuzun, veritabanınızın, Kullanıcı hesabınızın adlarıyla ve parolasıyla değiştirin.

    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
1. Bağlı hizmet oluşturmak için şu komutu çalıştırın:

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```
    LinkedServiceName : AzureSqlLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ```

   Sunucunuz için **Azure hizmetlerine erişime Izin ver** ayarının açık olduğundan emin olun. Doğrulayıp etkinleştirmek için aşağıdaki adımları uygulayın:

    1. [Azure Portal](https://portal.azure.com) oturum açın
    1. Soldaki **Diğer hizmetler >** öğesine ve **VERİTABANLARI** kategorisindeki **SQL sunucuları** seçeneğine tıklayın.
    1. SQL sunucuları listesinde sunucunuzu seçin.
    1. SQL sunucusu dikey penceresinde **Güvenlik duvarı ayarlarını göster** bağlantısına tıklayın.
    1. **Güvenlik Duvarı ayarları** dikey penceresinde, **Azure hizmetlerine erişime izin ver** için **AÇIK**’a tıklayın.
    1. Araç çubuğunda **Kaydet** seçeneğine tıklayın. 

## <a name="create-datasets"></a>Veri kümeleri oluşturma
Önceki adımda, Azure depolama hesabınızı ve Azure SQL veritabanınızı veri fabrikanıza bağlamak için bağlı hizmetler oluşturdunuz. Bu adımda, sırasıyla AzureStorageLinkedService ve AzureSqlLinkedService tarafından başvurulan veri depolarında depolanan girdi ve çıktı verilerini temsil eden InputDataset ve OutputDataset adlı iki veri kümesini tanımlarsınız.

Azure Depolama bağlı hizmeti, Data Factory hizmetinin Azure depolama hesabınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Giriş blobu veri kümesi (InputDataset) ise kapsayıcıyı ve girdi verilerini içeren klasörü belirtir.  

Benzer şekilde, Azure SQL veritabanı bağlı hizmeti, Data Factory hizmetinin veritabanınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Çıktı SQL tablosu veri kümesi (OututDataset) ise blob depolama alanındaki verilerin kopyalandığı veritabanında tabloyu belirtir. 

### <a name="create-an-input-dataset"></a>Girdi veri kümesi oluşturma
Bu adımda, InputDataset adlı bir veri kümesi oluşturursunuz. Bu veri kümesi, AzureStorageLinkedService bağlı hizmetiyle temsil edilen Azure Depolama’daki bir blob kapsayıcısının (adftutorial) kök klasöründe bulunan blob dosyasını (emp.txt) işaret eder. fileName için bir değer belirtmezseniz (veya bu adımı atlarsanız) girdi klasöründe bulunan tüm blob’lardaki veriler hedefe kopyalanır. Bu öğreticide, dosya adı için bir değer belirtirsiniz.  

1. **C:\ADFGetStartedPSH** klasöründe aşağıdaki içeriğe sahip **InputDataset.json** adlı bir JSON dosyası oluşturun:

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
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "emp.txt",
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
    | tür | Veriler Azure blob depolama alanında yer aldığından type özelliği **AzureBlob** olarak ayarlanmıştır. |
    | linkedServiceName | Daha önce oluşturduğunuz **AzureStorageLinkedService**’e başvurur. |
    | folderPath | blob **kapsayıcıyı** ve girdi blob’larını içeren **klasörü** belirtir. Bu öğreticide adftutorial, blob kapsayıcısıdır ve klasör, kök klasördür. | 
    | fileName | Bu özellik isteğe bağlıdır. Bu özelliği atarsanız tüm folderPath dosyaları alınır. Bu öğreticide fileName için **emp.txt** belirtilir, bu nedenle işlem için yalnızca bu dosya seçilir. |
    | format -> type |Girdi dosyası metin biçiminde olduğundan **TextFormat**'ı kullanırız. |
    | columnDelimiter | Girdi dosyasındaki sütunlar, **virgül (`,`)** ile ayrılmıştır. |
    | frequency/interval | frequency **Saat**, interval da **1** olarak ayarlanmıştır. Bu, girdi dilimlerinin **saatlik** olarak kullanılabileceğini belirtir. Başka bir deyişle, Data Factory hizmeti belirttiğiniz blob kapsayıcısının (**adftutorial**) kök klasöründe girdi verilerini saatte bir kere arar. İşlem hattı başlangıç ve bitiş zamanlarındaki verileri arar, bu zamanlardan önceki veya sonraki verileri aramaz.  |
    | external | Bu özellik, veriler bu işlem hattı tarafından oluşturulmazsa **true** olarak ayarlanır. Bu öğreticideki girdi verileri, bu işlem hattı tarafından oluşturulmayan emp.txt dosyasında bulunur, bu nedenle bu özelliği true olarak ayarlarız. |

    Bu JSON özellikleri hakkında daha fazla bilgi için bkz. [Azure Blob bağlayıcısı makalesi](data-factory-azure-blob-connector.md#dataset-properties).
1. Data Factory veri kümesi oluşturmak için şu komutu çalıştırın:

    ```powershell  
    New-AzDataFactoryDataset $df -File .\InputDataset.json
    ```
    Örnek çıktı aşağıdaki gibidir:

    ```
    DatasetName       : InputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureBlobDataset
    Policy            : Microsoft.Azure.Management.DataFactories.Common.Models.Policy
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

### <a name="create-an-output-dataset"></a>Çıktı veri kümesi oluşturma
Adımın bu bölümünde **OutputDataset** adlı bir çıktı veri kümesi oluşturursunuz. Bu veri kümesi, **Azurestablinkedservice** tarafından temsil EDILEN Azure SQL VERITABANıNDA bir SQL tablosunu işaret eder. 

1. **C:\ADFGetStartedPSH** klasöründe aşağıdaki içeriğe sahip **OutputDataset.json** adlı bir JSON dosyası oluşturun:

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
            "linkedServiceName": "AzureSqlLinkedService",
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
    | tür | Veri, Azure SQL veritabanındaki bir tabloya kopyalandığından, Type özelliği **Azurestabtable** olarak ayarlanır. |
    | linkedServiceName | Daha önce oluşturduğunuz **AzureSqlLinkedService**’e başvurur. |
    | tableName | Verilerin kopyalandığı **tabloyu** belirtir. | 
    | frequency/interval | frequency **Saatlik** ve interval **1** olarak ayarlanır. Bu durumda çıktı dilimleri, işlem hattı başlangıç ve bitiş zamanları arasında **saatlik** olarak üretilir, bu zamanlardan önce veya sonra üretilmez.  |

    Veritabanındaki emp tablosunda üç sütun vardır: **ID**, **FirstName** ve **LastName**. ID bir kimlik sütunu olduğundan, burada yalnızca **FirstName** ve **LastName** değerlerini belirtmeniz gerekir.

    Bu JSON özellikleri hakkında daha fazla bilgi için bkz. [Azure SQL bağlayıcısı makalesi](data-factory-azure-sql-connector.md#dataset-properties).
1. Veri fabrikası veri kümesi oluşturmak için aşağıdaki komutu çalıştırın.

    ```powershell   
    New-AzDataFactoryDataset $df -File .\OutputDataset.json
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```
    DatasetName       : OutputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureSqlTableDataset
    Policy            :
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma
Bu adımda, girdi olarak **InputDataset** ve çıktı olarak **OutputDataset** kullanan **kopyalama etkinliğine** sahip bir işlem hattı oluşturursunuz.

Şu anda zamanlamayı çıktı veri kümesi yürütmektedir. Bu öğreticide, çıktı veri kümesi saatte bir dilim oluşturacak şekilde yapılandırılır. İşlem hattının başlangıç zamanı ve bitiş zamanı arasında bir gün, yani 24 saat vardır. Bu nedenle işlem hattı, çıktı veri kümesinden 24 dilim oluşturur. 

1. **C:\ADFGetStartedPSH** klasöründe aşağıdaki içeriğe sahip **ADFTutorialPipeline.json** adlı bir JSON dosyası oluşturun:

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
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```
    Aşağıdaki noktalara dikkat edin:

   - Etkinlikler bölümünde, **türü****Copy** olarak ayarlanmış yalnızca bir etkinlik vardır. Kopyalama etkinliği hakkında daha fazla bilgi için bkz. [veri taşıma etkinlikleri](data-factory-data-movement-activities.md). Data Factory çözümlerinde, [veri dönüştürme etkinliklerini](data-factory-data-transformation-activities.md) de kullanabilirsiniz.
   - Etkinlik girdisi **InputDataset** olarak, etkinlik çıktısı ise **OutputDataset** olarak ayarlanmıştır. 
   - **typeProperties** bölümünde **BlobSource** kaynak türü, **SqlSink** de havuz türü olarak belirtilir. Kaynak ve havuz olarak kopyalama etkinliği tarafından desteklenen veri depolarının eksiksiz listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Kaynak/havuz olarak desteklenen belirli bir veri deposunu nasıl kullanacağınızı öğrenmek için tablodaki bağlantıya tıklayın.  

     **start** özelliğinin değerini geçerli günle, **end** değerini de sonraki günle değiştirin. Tarih saatin yalnızca tarih bölümünü belirtip saat bölümünü atlayabilirsiniz. Örneğin, "2016-02-03", "2016-02-03T00:00:00Z" ile eşdeğerdir

     Başlangıç ve bitiş tarih saatleri [ISO biçiminde](https://en.wikipedia.org/wiki/ISO_8601) olmalıdır. Örneğin: 2016-10-14T16:32:41Z. **End** zamanı isteğe bağlıdır; ancak bu öğreticide bunu kullanacağız. 

     **end** özelliği için değer belirtmezseniz "**start + 48 hours**" olarak hesaplanır. İşlem hattını süresiz olarak çalıştırmak için **end** özelliği değerini **9999-09-09** olarak ayarlayın.

     Önceki örnekte, her veri dilimi saatlik oluşturulduğundan 24 veri dilimi vardır.

     İşlem hattı tanımındaki JSON özelliklerinin açıklamaları için [işlem hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Kopyalama etkinliği tanımındaki JSON özelliklerinin açıklamaları için bkz. [veri taşıma etkinlikleri](data-factory-data-movement-activities.md). BlobSource tarafından desteklenen JSON özelliklerinin açıklamaları için bkz. [Azure Blob bağlayıcısı makalesi](data-factory-azure-blob-connector.md). SqlSink tarafından desteklenen JSON özelliklerinin açıklamaları için bkz. [Azure SQL Veritabanı bağlayıcısı makalesi](data-factory-azure-sql-connector.md).
1. Veri fabrikası tablosu oluşturmak için aşağıdaki komutu çalıştırın.

    ```powershell   
    New-AzDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

    Örnek çıktı aşağıdaki gibidir: 

    ```
    PipelineName      : ADFTutorialPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.PipelinePropertie
    ProvisioningState : Succeeded
    ```

**Tebrikler!** Azure Blob depolamadan Azure SQL veritabanı 'na veri kopyalamak için bir işlem hattına sahip bir Azure Veri Fabrikası başarıyla oluşturdunuz. 

## <a name="monitor-the-pipeline"></a>İşlem hattını izleme
Bu adımda, Azure Data Factory 'de neler olduğunu izlemek için Azure PowerShell kullanırsınız.

1. &lt;Datafactoryname &gt; değerini veri fabrikanızın adıyla değiştirin ve **Get-azdatafactory** komutunu çalıştırın ve çıktıyı bir değişkene atayın $df.

    ```powershell  
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name <DataFactoryName>
    ```

    Örnek:
    ```powershell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH0516
    ```

    Ardından, aşağıdaki çıktıyı görmek üzere $df içeriğini yazdırın: 

    ```
    PS C:\ADFGetStartedPSH> $df

    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DataFactoryId     : 6f194b34-03b3-49ab-8f03-9f8a7b9d3e30
    ResourceGroupName : ADFTutorialResourceGroup
    Location          : West US
    Tags              : {}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DataFactoryProperties
    ProvisioningState : Succeeded
    ```
1. İşlem hattının çıkış veri kümesi olan **Outputdataset**'in tüm dilimleri hakkındaki ayrıntıları almak için **Get-AzDataFactorySlice** ' ı çalıştırın.  

    ```powershell   
    Get-AzDataFactorySlice $df -DatasetName OutputDataset -StartDateTime 2017-05-11T00:00:00Z
    ```

   Bu ayar JSON işlem hattının **Başlat** değeriyle eşleşmelidir. 24 dilim görmeniz gerekir, geçerli günün saat 12 AM’den başlayıp ertesi günün 12 AM’inde biten her biri bir saati belirten dilimlerdir.

   Çıktının üç örnek dilimi aşağıda gösterilmiştir: 

    ``` 
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 11:00:00 PM
    End               : 5/12/2017 12:00:00 AM
    RetryCount        : 0
    State             : Ready
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 9:00:00 PM
    End               : 5/11/2017 10:00:00 PM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 8:00:00 PM
    End               : 5/11/2017 9:00:00 PM
    RetryCount        : 0
    State             : Waiting
    SubState          : ConcurrencyLimit
    LatencyStatus     :
    LongRetryCount    : 0
    ```
1. **Belirli** bir dilim için etkinlik çalıştırmalarının ayrıntılarını almak için **Get-azdatafactoryrun** komutunu çalıştırın. StartDateTime parametresinin değerini belirlemek için önceki komutun çıktısındaki tarih-saat değerini kopyalayın. 

    ```powershell  
    Get-AzDataFactoryRun $df -DatasetName OutputDataset -StartDateTime "5/11/2017 09:00:00 PM"
    ```

   Örnek çıktı aşağıdaki gibidir: 

    ```
    Id                  : c0ddbd75-d0c7-4816-a775-704bbd7c7eab_636301332000000000_636301368000000000_OutputDataset
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH0516
    DatasetName         : OutputDataset
    ProcessingStartTime : 5/16/2017 8:00:33 PM
    ProcessingEndTime   : 5/16/2017 8:01:36 PM
    PercentComplete     : 100
    DataSliceStart      : 5/11/2017 9:00:00 PM
    DataSliceEnd        : 5/11/2017 10:00:00 PM
    Status              : Succeeded
    Timestamp           : 5/16/2017 8:00:33 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy  
    ```

Data Factory cmdlet’leri hakkında kapsamlı bilgi için bkz. [Data Factory Cmdlet Başvurusu](/powershell/module/az.datafactory).

## <a name="summary"></a>Özet
Bu öğreticide, Azure blobundan Azure SQL veritabanı 'na veri kopyalamak için bir Azure Veri Fabrikası oluşturdunuz. Data factory, bağlı hizmetler, veri kümeleri ve işlem hattı oluşturmak için PowerShell’i kullandınız. Bu öğreticide gerçekleştirilen üst düzey adımları şunlardır:  

1. Oluşturulan Azure **data factory**.
1. Oluşturulan **bağlı hizmetler**:

   a. Giriş verilerini tutan Azure depolama hesabınızı bağlamak için bir **Azure depolama** bağlı hizmeti.     
   b. Çıktı verilerini barındıran SQL veritabanınıza bağlamak için **Azure SQL** bağlı hizmeti.
1. İşlem hatları için girdi verilerini ve çıktı verilerini açıklayan **veri kümeleri** oluşturuldu.
1. Kaynak olarak **BlobSource**, havuz olarak da **SqlSink** kullanılarak, **Kopyalama Etkinliği** ile **işlem hattı** oluşturuldu.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure Blob depolamayı bir kaynak veri deposu ve Azure SQL veritabanı olarak bir kopyalama işleminde hedef veri deposu olarak kullandınız. Aşağıdaki tabloda, kopyalama etkinliği tarafından kaynak ve hedef olarak desteklenen veri depolarının listesi sağlanmıştır: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Veri deposundan/veri deposuna veri kopyalama hakkında bilgi edinmek için tablodaki veri deposunun bağlantısına tıklayın. 

