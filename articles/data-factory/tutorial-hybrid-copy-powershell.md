---
title: Azure Data Factory kullanarak SQL Server verileri blob depolamaya kopyalama
description: Azure Data Factory’de şirket içinde barındırılan tümleştirme çalışma zamanını kullanarak şirket içi veri deposundan Azure bulutuna veri kopyalama hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: abnarain
ms.openlocfilehash: d2f59e7e8e86100a2a667634c0e99e6c1d5976da
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683498"
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Öğretici: Verileri şirket içi SQL Server veritabanından Azure Blob depolamaya kopyalama
Bu öğreticide, Azure PowerShell kullanarak verileri şirket içi SQL Server veritabanından bir Azure Blob depolama alanına kopyalayan bir veri fabrikası işlem hattı oluşturacaksınız. Verileri şirket içi ile bulut veri depoları arasında taşıyan, şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturup kullanabilirsiniz. 

> [!NOTE]
> Bu makale, Data Factory hizmetine ayrıntılı giriş bilgileri sağlamaz. Daha fazla bilgi için bkz. [Azure Data Factory'ye giriş](introduction.md). 

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma.
> * SQL Server ve Azure Depolama bağlı hizmetlerini oluşturma. 
> * SQL Server ve Azure Blob veri kümeleri oluşturma.
> * Verileri taşımak için kopyalama etkinliği ile işlem hattı oluşturma.
> * Bir işlem hattı çalıştırması başlatma.
> * İşlem hattı çalıştırmasını izleme.

## <a name="prerequisites"></a>Ön koşullar
### <a name="azure-subscription"></a>Azure aboneliği
Başlamadan önce, mevcut bir Azure aboneliğiniz yoksa [ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Azure rolleri
Veri fabrikası örnekleri oluşturmak için Azure’da oturum açarken kullandığınız kullanıcı hesabına *Katkıda bulunan* veya *Sahip* rolü atanmalı ya da bu hesap Azure aboneliğinin *yöneticisi* olmalıdır. 

Abonelikte sahip olduğunuz izinleri görüntülemek için Azure portalına gidin, sağ üst köşeden kullanıcı adınızı seçtikten sonra **İzinler**’i seçin. Birden çok aboneliğe erişiminiz varsa uygun aboneliği seçin. Bir role kullanıcı eklemeye ilişkin örnek yönergeler için, bkz. [RBAC ve Azure portalı kullanarak erişimi yönetme](../role-based-access-control/role-assignments-portal.md) makalesi.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 ve 2017
Bu öğreticide, şirket içi SQL Server veritabanını bir *kaynak* veri deposu olarak kullanırsınız. Bu öğreticide oluşturduğunuz veri fabrikasındaki işlem hattı, verileri bu şirket içi SQL Server veritabanından (kaynak) Azure Blob depolama alanına (havuz) kopyalar. Daha sonra SQL Server veritabanınızda **emp** adlı bir tablo oluşturur ve tabloya birkaç örnek girdi eklersiniz. 

1. SQL Server Management Studio’yu başlatın. Makinenizde zaten yüklü değilse [SQL Server Management Studio'yu indirme](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)’ye gidin. 

1. Kimlik bilgilerinizi kullanarak SQL Server örneğinize bağlanın. 

1. Örnek bir veritabanı oluşturun. Ağaç görünümünde **Veritabanları**'na sağ tıklayın ve **Yeni Veritabanı**'nı seçin. 
 
1. **Yeni Veritabanı** penceresinde, veritabanı için bir ad girin ve **Tamam**'ı seçin. 

1. **emp** tablosunu oluşturmak ve içine bazı örnek verileri eklemek için veritabanında aşağıdaki sorgu betiğini çalıştırın. Ağaç görünümünde, oluşturduğunuz veritabanına sağ tıklayın ve **Yeni Sorgu**'yu seçin.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```


### <a name="azure-storage-account"></a>Azure Depolama hesabınızın
Bu öğreticide, genel amaçlı bir Azure depolama hesabını (özel olarak Blob depolamayı) hedef/havuz veri deposu olarak kullanırsınız. Genel amaçlı bir Azure depolama hesabınız yoksa bkz. [Depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md). Bu öğreticide oluşturduğunuz veri fabrikasındaki işlem hattı, verileri şirket içi SQL Server veritabanından (kaynak) bu Azure Blob depolama alanına (havuz) kopyalar. 

#### <a name="get-storage-account-name-and-account-key"></a>Depolama hesabı adını ve hesap anahtarını alma
Bu öğreticide, Azure depolama hesabınızın adını ve anahtarını kullanırsınız. Aşağıdakileri yaparak depolama hesabınızın adını ve anahtarını alın: 

1. Azure kullanıcı adı ve parolanızla [Azure portalında](https://portal.azure.com) oturum açın. 

1. Sol bölmede, **Depolama** anahtar sözcüğünü kullanarak **Diğer hizmetler** filtresini ve ardından **Depolama hesapları**’nı seçin.

    ![Depolama hesabını arama](media/doc-common-process/search-storage-account.png)

1. Depolama hesapları listesinde, depolama hesabınız için filtre uygulayın (gerekirse) ve depolama hesabınızı seçin. 

1. **Depolama hesabı** penceresinde **Erişim anahtarları**'nı seçin.

1. **Depolama hesabı adı** ve **key1** kutularında değerleri kopyalayın ve ardından onları öğreticide daha sonra kullanmak için Not Defteri'ne veya başka bir düzenleyiciye yapıştırın. 

#### <a name="create-the-adftutorial-container"></a>Adftutorial kapsayıcını oluşturma 
Bu bölümde, Azure Blob depolama alanınızda **adftutorial** adlı bir blob kapsayıcısı oluşturursunuz. 

1. **Depolama hesabı** penceresinde **Genel Bakış**’a geçin ve sonra **Bloblar**’ı seçin. 

    ![Bloblar seçeneğini belirleyin](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. **Blob hizmeti** penceresinde **Kapsayıcı**’yı seçin. 

1. **Yeni kapsayıcı** penceresinde, **Ad** kutusuna **adftutorial** girin ve ardından **Tamam**’ı seçin. 

    ![Kapsayıcı adını girin](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Kapsayıcılar listesinde **adftutorial**’ı seçin.  

1. **adftutorial** öğesine ait **kapsayıcı** penceresini açık tutun. Öğreticinin sonundaki çıktıyı doğrulamak için bu sayfayı kullanırsınız. Data Factory bu kapsayıcıda çıktı klasörünü otomatik olarak oluşturduğundan sizin oluşturmanız gerekmez.


### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Azure PowerShell'i yükleme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Makinenizde önceden yüklü değilse Azure PowerShell’in en son sürümünü yükleyin. Ayrıntılı yönergeler için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/install-Az-ps). 

#### <a name="log-in-to-powershell"></a>PowerShell’de oturum açın

1. Makinenizde PowerShell'i başlatın ve bu hızlı başlangıç öğreticisi tamamlanana dek açık tutun. Kapatıp yeniden açarsanız bu komutları yeniden çalıştırmanız gerekir.

1. Aşağıdaki komutu çalıştırın ve sonra Azure portalında oturum açmak için kullandığınız Azure kullanıcı adını ve parolasını girin:
       
    ```powershell
    Connect-AzAccount
    ```        

1. Birden çok Azure aboneliğiniz varsa, birlikte çalışmak istediğiniz aboneliği seçmek için aşağıdaki komutu çalıştırın. **SubscriptionId**’yi Azure aboneliğinizin kimliği ile değiştirin:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Daha sonra PowerShell komutlarında kullanacağınız kaynak grubu adı için bir değişken tanımlayın. Aşağıdaki komut metnini PowerShell'e kopyalayın [Azure kaynak grubu](../azure-resource-manager/resource-group-overview.md) için bir ad belirtin (çift tırnak içinde; örneğin, `"adfrg"`) ve ardından komutu çalıştırın. 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

1. Azure kaynak grubunu oluşturmak için aşağıdaki komutu çalıştırın: 

    ```powershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ``` 

    Kaynak grubu zaten varsa, üzerine yazılmasını istemeyebilirsiniz. `$resourceGroupName` değişkenine farklı bir değer atayın ve komutu yeniden çalıştırın.

1. Daha sonra PowerShell komutlarında kullanabileceğiniz veri fabrikası adı için bir değişken tanımlayın. Ad bir harf veya rakamla başlamalıdır; yalnızca harf, rakam ve tire (-) karakteri içerebilir.

    > [!IMPORTANT]
    >  Veri fabrikasının adını genel olarak benzersiz bir adla güncelleştirin. Örnek olarak ADFTutorialFactorySP1127 olabilir. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

1. Veri fabrikasının konumu için bir değişken tanımlayın: 

    ```powershell
    $location = "East US"
    ```  

1. Veri fabrikasını oluşturmak için aşağıdaki `Set-AzDataFactoryV2` cmdlet’ini çalıştırın: 
    
    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

> [!NOTE]
> 
> * Veri fabrikasının adı genel olarak benzersiz olmalıdır. Aşağıdaki hata iletisini alırsanız adı değiştirip yeniden deneyin.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Veri fabrikası örnekleri oluşturmak için Azure’da oturum açarken kullandığınız kullanıcı hesabına *katkıda bulunan* veya *sahip* rolü atanmalı veya bu hesap Azure aboneliğinin *yöneticisi* olmalıdır.
> * Data Factory'nin kullanılabileceği Azure bölgelerinin bir listesi için bir sonraki sayfada ilgilendiğiniz bölgeleri seçin ve **Analytics**'i genişleterek **Data Factory**: [Products available by region](https://azure.microsoft.com/global-infrastructure/services/) (Bölgeye göre kullanılabilir durumdaki ürünler) bölümünü bulun. Veri fabrikası tarafından kullanılan veri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (Azure HDInsight vb.) başka bölgelerde olabilir.
> 
> 

## <a name="create-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma

Bu bölümde, şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturur ve SQL Server veritabanını içeren bir şirket içi makine ile ilişkilendirirsiniz. Şirket içinde barındırılan tümleştirme çalışma zamanı, makinenizdeki SQL Server veitabanınızdaki verileri Azure Blob depolama alanına kopyalayan bileşendir. 

1. Tümleştirme çalışma zamanının adı için bir değişken oluşturun. Benzersiz bir ad kullanın ve bu adı not edin. Daha sonra bu öğreticide kullanacaksınız. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

1. Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma. 

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ``` 

    Örnek çıktı aşağıdaki gibidir:

    ```json
    Name              : ADFTutorialIR
    Type              : SelfHosted
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Description       : selfhosted IR description
    Id                : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Oluşturulan tümleştirme çalışma zamanının durumunu almak için aşağıdaki komutu çalıştırın:

    ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    Örnek çıktı aşağıdaki gibidir:
    
    ```json
    State                     : NeedRegistration
    Version                   : 
    CreateTime                : 9/10/2019 3:24:09 AM
    AutoUpdate                : On
    ScheduledUpdateDate       : 
    UpdateDelayOffset         : 
    LocalTimeZoneOffset       : 
    InternalChannelEncryption : 
    Capabilities              : {}
    ServiceUrls               : {eu.frontend.clouddatahub.net}
    Nodes                     : {}
    Links                     : {}
    Name                      : <Integration Runtime name>
    Type                      : SelfHosted
    ResourceGroupName         : <resourceGroup name>
    DataFactoryName           : <dataFactory name>
    Description               : selfhosted IR description
    Id                        : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Şirket içinde barındırılan tümleştirme çalışma zamanını bulutta Data Factory hizmetine kaydetmek üzere *kimlik doğrulaması anahtarlarını* almak için aşağıdaki komutu çalıştırın. Sonraki adımda makinenize yüklediğiniz şirket içinde barındırılan tümleştirme çalışma zamanını kaydetmek için anahtarların birini (çift tırnak işaretlerini atarak) kopyalayın. 

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```
    
    Örnek çıktı aşağıdaki gibidir:
    
    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>Tümleştirme çalışma zamanını yükleme
1. [Azure Data Factory Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717)’ı yerel Windows makinesine indirin ve yüklemeyi çalıştırın. 

1. **Microsoft Integration Runtime Kurulum Sihirbazına Hoş Geldiniz** sayfasında **İleri**'yi seçin.  

1. **Son Kullanıcı Lisans Sözleşmesi** penceresinde koşulları ve lisans sözleşmesini kabul edin ve **İleri**'yi seçin. 

1. **Hedef Klasör** penceresinde **İleri**’yi seçin. 

1. **Microsoft Integration Runtime yüklenmeye hazır** penceresinde **Yükle**'yi seçin. 

1. **Microsoft Integration Runtime Kurulum Sihirbazı Tamamlandı** sayfasında **Son**'u seçin.

1. **Integration Runtime’ı (şirket içinde barındırılan) Kaydet** penceresinde, önceki bölümde kaydettiğiniz anahtarı yapıştırın ve **Kaydol**'u seçin. 

    ![Tümleştirme çalışma zamanını kaydetme](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

1. **Yeni Integration Runtime (Şirket içinde barındırılan) düğümü** penceresinde **son**' u seçin. 

    ![Yeni Integration Runtime Düğümü penceresi](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

 1. Şirket içinde barındırılan tümleştirme çalışma zamanı başarıyla kaydedildiğinde aşağıdaki ileti görüntülenir: 

    ![Başarıyla kaydedildi](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

1. **Integration Runtime’ı (şirket içinde barındırılan) Kaydet** penceresinde **Configuration Manager'ı Başlat**'ı seçin. 

1. Düğüm bulut hizmetine bağlandığında şu ileti görüntülenir:

    ![Düğüm bağlı](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

1. Aşağıdakini yaparak SQL Server veritabanınıza olan bağlantıyı test edin:

    a. **Configuration Manager** penceresinde **Tanılama** sekmesine geçin.

    b. **Veri kaynağı türü** kutusunda **SqlServer**’ı seçin.

    c. Sunucu adını girin.

    d. Veritabanı adını girin. 

    e. Kimlik doğrulama modunu seçin. 

    f. Kullanıcı adını girin. 

    g. Kullanıcı adıyla ilişkili parolayı girin.

    h. Tümleştirme çalışma zamanının SQL Server’a bağlanabildiğini onaylamak için **Sına**’yı seçin.  
    ![bağlantısı başarılı](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png) 
  
    Bağlantı başarılı olursa yeşil bir onay işareti görüntülenir. Başarılı olmazsa hata ile ilişkili bir hata iletisi alırsınız. Sorunları giderin ve tümleştirme çalışma zamanının SQL Server örneğinize bağlanabildiğinden emin olun.

    Bu öğreticide daha sonra kullanmak için önceki tüm değerleri not edin.
    
## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Veri depolarınızı ve işlem hizmetlerinizi veri fabrikasına bağlamak için veri fabrikasında bağlı hizmetler oluşturun. Bu öğreticide, Azure Depolama Hesabınız ile şirket içi SQL Server örneğinizi veri deposuna bağlarsınız. Bağlı hizmetler, Data Factory hizmetinin bunlara bağlanmak için çalışma zamanında kullandığı bağlantı bilgilerini içerir. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Azure Depolama bağlı hizmeti oluşturma (hedef/havuz)
Bu adımda, Azure depolama hesabınızı veri fabrikasına bağlarsınız.

1. *C:\ADFv2Tutorial* klasöründe aşağıdaki kodla *AzureStorageLinkedService.json* adlı bir JSON dosyası oluşturun. *ADFv2Tutorial* klasörü henüz yoksa oluşturun.  

    > [!IMPORTANT]
    > Dosyayı kaydetmeden önce \<accountName> ve \<accountKey> değerlerini Azure depolama hesabınızın adı ve anahtarıyla değiştirin. Bunları [Önkoşullar](#get-storage-account-name-and-account-key) bölümünde not etmiştiniz.

   ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
   ```

1. PowerShell’de *C:\ADFv2Tutorial* klasörüne geçin.
   ```powershell
   Set-Location 'C:\ADFv2Tutorial'    
   ```

1. AzureStorageLinkedService adlı bağlı hizmeti oluşturmak için aşağıdaki `Set-AzDataFactoryV2LinkedService` cmdlet’ini çalıştırın: 

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Örnek çıktı aşağıdaki gibidir:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroup name>
    DataFactoryName   : <dataFactory name>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

    "Dosya bulunamadı" hatasını alırsanız `dir` komutunu çalıştırarak dosyanın var olduğunu doğrulayın. Dosya *.txt* uzantısına sahipse (örneğin, AzureStorageLinkedService.json.txt) uzantıyı atıp PowerShell komutunu tekrar çalıştırın. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>SQL Server bağlı hizmeti oluşturma ve şifreleme (kaynak)
Bu adımda, şirket içi SQL Server örneğinizi veri fabrikasına bağlarsınız.

1. *C:\ADFv2Tutorial* klasöründe aşağıdaki kodu kullanarak *SqlServerLinkedService.json* adlı bir JSON dosyası oluşturun:

    > [!IMPORTANT]
    > SQL Server’a bağlanmak için kullandığınız kimlik doğrulaması yöntemine dayalı bölümü seçin.

    **SQL kimlik doğrulaması (sa) kullanarak:**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<serverName>;initial catalog=<databaseName>;user id=<userName>;password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name> ",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
   ```    

    **Windows kimlik doğrulaması kullanarak:**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<serverName>;initial catalog=<databaseName>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
    ```

    > [!IMPORTANT]
    > - SQL Server örneğinize bağlanmak için kullandığınız kimlik doğrulaması yöntemine dayalı bölümü seçin.
    > - **\<tümleştirme çalışma zamanı adı>** yerine tümleştirme çalışma zamanınızın adını koyun.
    > - Dosyayı kaydetmeden önce **\<sunucuadı>** , **\<veritabanıadı>** , **\<kullanıcıadı>** ve **\<parola>** değerleri yerine SQL Server örneğinizin değerlerini koyun.
    > - Kullanıcı hesabınızda veya sunucu adında ters eğik çizgi karakteri (\\) kullanmanız gerekirse önüne kaçış karakterini (\\) koyun. Örneğin, *etkialanim\\\\kullanicim* şeklinde kullanın. 

1. Hassas verileri (kullanıcı adı, parola ve benzeri) şifrelemek için `New-AzDataFactoryV2LinkedServiceEncryptedCredential` cmdlet'ini çalıştırın.  
    Bu şifreleme, kimlik bilgilerinin Veri Koruma Uygulama Programlama Arabirimi (DPAPI) kullanılarak şifrelenmesini sağlar. Şifrelenmiş kimlik bilgileri, şirket içinde barındırılan tümleştirme çalışma zamanı düğümünde (yerel makine) yerel olarak kaydedilir. Çıktı yükü, şifrelenmiş kimlik bilgilerini içeren başka bir JSON dosyasına (bu örnekte *encryptedLinkedService.json*) yönlendirilebilir.
    
   ```powershell
   New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

1. EncryptedSqlServerLinkedService öğesini oluşturan aşağıdaki komutu çalıştırın:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Veri kümeleri oluşturun
Bu adımda, girdi ve çıktı veri kümelerini oluşturursunuz. Bunlar, verileri şirket içi SQL Server veritabanından Azure Blob depolama alanına kopyalayan kopyalama işleminin girdi ve çıktı verilerini temsil ederler.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Kaynak SQL Server veritabanı için veri kümesi oluşturma
Bu adımda, SQL Server veritabanı örneğindeki verileri temsil eden bir veri kümesini tanımlarsınız. Veri kümesi SqlServerTable türündedir. Önceki adımda oluşturduğunuz SQL Server bağlı hizmetini ifade eder. Bağlı hizmet, Data Factory hizmetinin çalışma zamanında SQL Server örneğinize bağlanmak için kullandığı bağlantı bilgilerini içerir. Bu veri kümesi, verileri içeren veritabanındaki SQL tablosunu belirtir. Bu öğreticide, **emp** tablosu kaynak verileri içerir. 

1. *C:\ADFv2Tutorial* klasöründe aşağıdaki kodla *SqlServerDataset.json* adlı bir JSON dosyası oluşturun:  
    ```json
    {  
        "name":"SqlServerDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"EncryptedSqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ],
            "typeProperties":{  
                "schema":"dbo",
                "table":"emp"
            }
        }
    }
    ```

1. SqlServerDataset veri kümesini oluşturmak için `Set-AzDataFactoryV2Dataset` cmdlet'ini çalıştırın.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         : 
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Azure Blob depolama alanı (havuz) için veri kümesi oluşturma
Bu adımda, Azure Blob depolama alanına kopyalanacak verileri temsil eden bir veri kümesi tanımlarsınız. Veri kümesi AzureBlob türündedir. Bu öğreticide daha önce oluşturduğunuz Azure Depolama bağlı hizmetine başvurur. 

Bağlı hizmet, veri fabrikasının çalışma zamanında Azure depolama hesabınıza bağlanmak için kullandığı bağlantı bilgilerini içerir. Bu veri kümesi, Azure depolama alanında SQL Server veritabanından verilerin kopyalandığı klasörü belirtir. Bu öğreticide, *adftutorial/fromonprem* klasördür; burada `adftutorial` blob kapsayıcısı ve `fromonprem` de klasördür. 

1. *C:\ADFv2Tutorial* klasöründe aşağıdaki kodla *AzureBlobDataset.json* adlı bir JSON dosyası oluşturun:

    ```json
    {  
        "name":"AzureBlobDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureStorageLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"DelimitedText",
            "typeProperties":{  
                "location":{  
                    "type":"AzureBlobStorageLocation",
                    "folderPath":"fromonprem",
                    "container":"adftutorial"
                },
                "columnDelimiter":",",
                "escapeChar":"\\",
                "quoteChar":"\""
            },
            "schema":[  
    
            ]
        },
        "type":"Microsoft.DataFactory/factories/datasets"
    }
    ```

1. AzureBlobDataset veri kümesini oluşturmak için `Set-AzDataFactoryV2Dataset` cmdlet'ini çalıştırın.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.DelimitedTextDataset
    ```

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma
Bu öğreticide, kopyalama etkinliği ile bir işlem hattı oluşturursunuz. Kopyalama etkinliği, girdi veri kümesi olarak SqlServerDataset’i çıktı veri kümesi olarak da AzureBlobDataset’i kullanır. Kaynak türü *SqlSource* ve havuz türü de *BlobSink* olarak ayarlanır.

1. *C:\ADFv2Tutorial* klasöründe aşağıdaki kodla *SqlServerToBlobPipeline.json* adlı bir JSON dosyası oluşturun:

    ```json
    {  
        "name":"SqlServerToBlobPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"CopySqlServerToAzureBlobActivity",
                    "type":"Copy",
                    "dependsOn":[  
    
                    ],
                    "policy":{  
                        "timeout":"7.00:00:00",
                        "retry":0,
                        "retryIntervalInSeconds":30,
                        "secureOutput":false,
                        "secureInput":false
                    },
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "source":{  
                            "type":"SqlServerSource"
                        },
                        "sink":{  
                            "type":"DelimitedTextSink",
                            "storeSettings":{  
                                "type":"AzureBlobStorageWriteSettings"
                            },
                            "formatSettings":{  
                                "type":"DelimitedTextWriteSettings",
                                "quoteAllText":true,
                                "fileExtension":".txt"
                            }
                        },
                        "enableStaging":false
                    },
                    "inputs":[  
                        {  
                            "referenceName":"SqlServerDataset",
                            "type":"DatasetReference"
                        }
                    ],
                    "outputs":[  
                        {  
                            "referenceName":"AzureBlobDataset",
                            "type":"DatasetReference"
                        }
                    ]
                }
            ],
            "annotations":[  
    
            ]
        }
    }
    ```

1. SqlServerToBlobPipeline işlem hattını oluşturmak için `Set-AzDataFactoryV2Pipeline` cmdlet’ini çalıştırın.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>İşlem hattı çalıştırması oluşturma
SQLServerToBlobPipeline işlem hattı için bir işlem hattı çalıştırması başlatın ve gelecekte izlemek üzere işlem hattı çalıştırma kimliğini yakalayın.

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>İşlem hattı çalıştırmasını izleme

1. SQLServerToBlobPipeline işlem hattının çalışma durumunu sürekli olarak denetlemek için PowerShell’de aşağıdaki betiği çalıştırın ve nihai sonucu yazdırın:

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Örnek çalıştırmanın çıktısı aşağıdaki gibidir:

    ```JSON
    ResourceGroupName    : <resourceGroupName>
    DataFactoryName      : <dataFactoryName>
    ActivityRunId        : 24af7cf6-efca-4a95-931d-067c5c921c25
    ActivityName         : CopySqlServerToAzureBlobActivity
    ActivityType         : Copy
    PipelineRunId        : 7b538846-fd4e-409c-99ef-2475329f5729
    PipelineName         : SQLServerToBlobPipeline
    Input                : {source, sink, enableStaging}
    Output               : {dataRead, dataWritten, filesWritten, sourcePeakConnections...}
    LinkedServiceName    : 
    ActivityRunStart     : 9/11/2019 7:10:37 AM
    ActivityRunEnd       : 9/11/2019 7:10:58 AM
    DurationInMs         : 21094
    Status               : Succeeded
    Error                : {errorCode, message, failureType, target}
    AdditionalProperties : {[retryAttempt, ], [iterationHash, ], [userProperties, {}], [recoveryStatus, None]...}
    ```

1. SQLServerToBlobPipeline işlem hattının çalıştırma kimliğini alabilir ve aşağıdaki komutu çalıştırarak ayrıntılı etkinlik çalıştırma sonucunu denetleyebilirsiniz: 

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Örnek çalıştırmanın çıktısı aşağıdaki gibidir:

    ```json
    {  
        "dataRead":36,
        "dataWritten":32,
        "filesWritten":1,
        "sourcePeakConnections":1,
        "sinkPeakConnections":1,
        "rowsRead":2,
        "rowsCopied":2,
        "copyDuration":18,
        "throughput":0.01,
        "errors":[  
    
        ],
        "effectiveIntegrationRuntime":"ADFTutorialIR",
        "usedParallelCopies":1,
        "executionDetails":[  
            {  
                "source":{  
                    "type":"SqlServer"
                },
                "sink":{  
                    "type":"AzureBlobStorage",
                    "region":"CentralUS"
                },
                "status":"Succeeded",
                "start":"2019-09-11T07:10:38.2342905Z",
                "duration":18,
                "usedParallelCopies":1,
                "detailedDurations":{  
                    "queuingDuration":6,
                    "timeToFirstByte":0,
                    "transferDuration":5
                }
            }
        ]
    }
    ```

## <a name="verify-the-output"></a>Çıktıyı doğrulama
İşlem hattı, *blob kapsayıcısında*fromonprem`adftutorial` adlı çıktı klasörünü otomatik olarak oluşturur. Çıktı klasöründe *dbo.emp.txt* dosyasını gördüğünüzü onaylayın. 

1. Çıktı klasörünü görmek için, Azure portalındaki **adftutorial** kapsayıcı penceresinde **Yenile**’yi seçin.
1. Klasör listesinde `fromonprem` seçeneğini belirleyin. 
1. `dbo.emp.txt` adlı bir dosya gördüğünüzü onaylayın.

    ![Çıktı dosyası](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Sonraki adımlar
Bu örnekteki işlem hattı, verileri bir konumdan Azure Blob depolama alanındaki başka bir konuma kopyalar. Şunları öğrendiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma.
> * SQL Server ve Azure Depolama bağlı hizmetlerini oluşturma. 
> * SQL Server ve Azure Blob veri kümeleri oluşturma.
> * Verileri taşımak için kopyalama etkinliği ile işlem hattı oluşturma.
> * Bir işlem hattı çalıştırması başlatma.
> * İşlem hattı çalıştırmasını izleme.

Data Factory tarafından desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) konusuna bakın.

Kaynaktan hedefe verileri toplu olarak kopyalama hakkında bilgi edinmek için aşağıdaki öğreticiye geçin:

> [!div class="nextstepaction"]
>[Verileri toplu halde kopyalama](tutorial-bulk-copy.md)
