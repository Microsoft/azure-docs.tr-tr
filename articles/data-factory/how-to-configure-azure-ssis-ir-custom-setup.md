---
title: Azure-SSIS Integration Runtime için kurulumu özelleştirme
description: Bu makalede, ek bileşenleri yüklemek veya ayarları değiştirmek için bir Azure-SSIS Integration Runtime için özel kurulum arabiriminin nasıl kullanılacağı açıklanır.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: b1b3a34ac495936ed92e29353a41efb8c462768f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387742"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime için kurulumu özelleştirme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory (ADF) içindeki Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) özel kurulumları aracılığıyla özelleştirebilirsiniz. Azure-SSIS IR sağlama veya yeniden yapılandırma sırasında kendi adımlarınızı eklemenize olanak tanır. 

Özel kurulumları kullanarak, Azure-SSIS IR varsayılan işletim yapılandırmasını veya ortamını değiştirebilirsiniz. Örneğin, ek Windows Hizmetleri başlatmak için dosya paylaşımları için erişim kimlik bilgilerini kalıcı hale getirin veya güçlü şifreleme/daha güvenli ağ protokolü (TLS 1,2) kullanın. Ya da Azure-SSIS IR her bir düğümüne derlemeler, sürücüler veya uzantılar gibi ek bileşenler yükleyebilirsiniz. Bunlar, özel olarak oluşturulmuş, açık kaynak veya üçüncü taraf bileşenleri olabilir. Yerleşik/önceden yüklenmiş bileşenler hakkında daha fazla bilgi için, bkz. [Azure-SSIS IR yerleşik/önceden yüklenmiş bileşenler](./built-in-preinstalled-components-ssis-integration-runtime.md).

Azure-SSIS IR özel kurulumları iki şekilde yapabilirsiniz: 
* **Komut dosyası Ile standart özel kurulum**: bir betiği ve ilişkili dosyalarını hazırlayın ve hepsini Azure Depolama hesabınızdaki bir blob kapsayıcısına birlikte karşıya yükleyin. Daha sonra, Azure-SSIS IR ayarladığınızda veya yeniden yapılandırdığınızda, kapsayıcınıza yönelik bir paylaşılan erişim Imzası (SAS) Tekdüzen Kaynak tanımlayıcısı (URI) sağlarsınız. Azure-SSIS IR her bir düğüm, komut dosyasını ve ilişkili dosyalarını kapsayıcınızdan indirir ve özel kurulumunuzu yükseltilmiş izinlerle çalıştırır. Özel Kurulum tamamlandığında her düğüm, çalışma ve diğer günlüklerin standart çıkışını kapsayıcınıza yükler.
* **Komut dosyası olmadan hızlı özel kurulum**: bazı ortak sistem yapılandırma ve Windows komutlarını çalıştırın ya da herhangi bir betiği kullanmadan popüler veya önerilen ek bileşenleri kurun.

Standart ve hızlı özel kurulumlarla hem ücretsiz (Lisanssız) hem de ücretli (lisanslı) bileşenleri yükleyebilirsiniz. Bağımsız bir yazılım satıcısı (ISV) iseniz, bkz. [Azure-SSIS IR için ücretli veya lisanslı bileşenler geliştirme](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Gelecekteki geliştirmelerden faydalanmak için, özel kurulumla Azure-SSIS IR için v3 veya üzeri bir düğüm serisi kullanmanızı öneririz.

## <a name="current-limitations"></a>Geçerli sınırlamalar

Aşağıdaki sınırlamalar yalnızca standart özel kurulumların için geçerlidir:

- Genel derleme önbelleği 'ne (GAC) derlemeleri yüklemek için komut dosyasında *gacutil.exe* kullanmak istiyorsanız, özel kurulumlarınızın bir parçası olarak *gacutil.exe* sağlamanız gerekir. Ya da *genel önizleme* Kapsayıcımız *örnek* klasöründe sağlanan kopyayı kullanabilirsiniz, aşağıdaki **Standart özel kurulum örnekleri** bölümüne bakın.

- Betiğinizdeki bir alt klasöre başvurmak istiyorsanız, *msiexec.exe* `.\` kök klasöre başvurmak için gösterimi desteklemez. Yerine bir komut kullanın `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` .

- Windows tarafından otomatik olarak oluşturulan yönetimsel paylaşımlar veya gizli ağ paylaşımları Şu anda Azure-SSIS IR desteklenmemektedir.

- IBM ıferies erişimi ODBC sürücüsü Azure-SSIS IR desteklenmez. Özel kurulumunuz sırasında yükleme hataları görebilirsiniz. Bunu yaparsanız yardım için IBM desteği ile iletişime geçin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS IR özelleştirmek için aşağıdaki öğeler gereklidir:

- [Bir Azure aboneliği](https://azure.microsoft.com/)

- [Azure-SSIS IR sağlama](./tutorial-deploy-ssis-packages-azure.md)

- [Bir Azure depolama hesabı](https://azure.microsoft.com/services/storage/). Express özel kurulumları için gerekli değildir. Standart özel kurulumlarda, özel kurulum komut dosyanızı ve ilişkili dosyalarını bir blob kapsayıcısına yükler ve depolar. Özel Kurulum işlemi aynı zamanda yürütme günlüklerini aynı blob kapsayıcısına yükler.

## <a name="instructions"></a>Yönergeler

ADF Kullanıcı arabirimindeki özel kurulumlarla Azure-SSIS IR sağlayabilir veya yeniden yapılandırabilirsiniz. PowerShell kullanarak aynı yapmak istiyorsanız, [Azure PowerShell](/powershell/azure/install-az-ps)indirin ve yükleyin.

### <a name="standard-custom-setup"></a>Standart özel kurulum

ADF Kullanıcı arabirimindeki standart özel kurulumlarla Azure-SSIS IR sağlamak veya yeniden yapılandırmak için aşağıdaki adımları izleyin.

1. Özel kurulum komut dosyanızı ve ilişkili dosyalarını (örneğin,. bat,. cmd,. exe,. dll,. msi veya. ps1 dosyaları) hazırlayın.

   * Özel kurulumlarınızın giriş noktası olan *Main. cmd* adlı bir betik dosyanız olmalıdır.  
   * Betiğin sessizce yürütülmesini sağlamak için, önce yerel makinenizde test etmelisiniz.  
   * Diğer araçlarla (örneğin, *msiexec.exe*), kapsayıcınıza karşıya yüklenecek ek günlüklerin oluşturulmasını istiyorsanız, `CUSTOM_SETUP_SCRIPT_LOG_DIR` betiklerinizde günlük klasörü olarak önceden tanımlanmış ortam değişkenini belirtin (örneğin, *msiexec/ı xxx.msi/quiet/LV% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. [Azure Depolama Gezgini](https://storageexplorer.com/)indirin, yükleyin ve açın.

   a. **(Yerel ve bağlı)** altında, **depolama hesapları**' na sağ tıklayın ve ardından **Azure depolama 'ya Bağlan**' ı seçin.

      ![Azure Depolama’ya bağlanma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. **Depolama hesabı adı ve anahtarı kullan**' ı seçin ve ardından **İleri**' yi seçin.

      ![Depolama hesabı adını ve anahtarını kullanma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Azure depolama hesabınızın adını ve anahtarınızı girip, **İleri**' yi seçin ve ardından **Bağlan**' ı seçin.

      ![Depolama hesabı adını ve anahtarını belirtin](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Bağlı Azure depolama hesabınız altında, **BLOB kapsayıcıları**' na sağ tıklayın, **BLOB kapsayıcısı oluştur**' u seçin ve yeni kapsayıcıyı adlandırın.

      ![Blob kapsayıcısı oluşturma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Yeni kapsayıcıyı seçin ve özel kurulum komut dosyanızı ve ilişkili dosyalarını karşıya yükleyin. *Main. cmd* ' yi, kapsayıcının en üst düzeyinde (herhangi bir klasörde değil) karşıya yüklediğinizden emin olun. Kapsayıcının yalnızca gerekli özel kurulum dosyalarını içermesi gerekir, bu nedenle daha sonra Azure-SSIS IR indirmek uzun zaman almaz. Özel kurulumun en uzun süresi şu anda zaman aşımına uğramadan önce 45 dakika içinde ayarlanmıştır. Bu, kapsayıcıınızdan tüm dosyaları indirme ve Azure-SSIS IR yükleme saatini içerir. Kurulum daha fazla zaman gerektiriyorsa, bir destek bileti yükseltin.

      ![Blob kapsayıcısına dosya yükleme](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Kapsayıcıya sağ tıklayın ve **paylaşılan erişim Imzası al**' ı seçin.

      ![Kapsayıcı için paylaşılan erişim Imzasını al](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   örneğin: Kapsayıcınız için SAS URI 'sini, yeterince uzun bir süre sonu ve okuma/yazma/listeleme izni ile oluşturun. Özel kurulum komut dosyanızı ve ilişkili dosyalarını indirmek ve çalıştırmak için SAS URI 'sine ihtiyacınız vardır. Bu, Azure-SSIS IR herhangi bir düğüm yeniden yansıma veya yeniden başlatma yapıldığında gerçekleşir. Ayrıca, Kurulum yürütme günlüklerini karşıya yüklemek için yazma izninizin olması gerekir.

      > [!IMPORTANT]
      > SAS URI 'sinin süresi dolana ve özel kurulum kaynaklarının Azure-SSIS IR, özellikle de bu süre boyunca düzenli olarak durdurup Azure-SSIS IR başlatırsanız, oluşturma işleminin silinmesine göre her zaman kullanılabilir olduğundan emin olun.

      ![Kapsayıcı için paylaşılan erişim Imzasını oluşturma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Kapsayıcının SAS URI 'sini kopyalayın ve kaydedin.

      ![Paylaşılan erişim Imzasını Kopyala ve Kaydet](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. **Integration Runtime kurulum** bölmesinin **Gelişmiş ayarlar** sayfasındaki **ek sistem yapılandırmalarına/bileşen yüklemelerine sahip Azure-SSIS Integration Runtime özelleştirin** onay kutusunu seçin. Ardından, **özel kurulum KAPSAYıCıSı SAS URI 'si** metin kutusuna KAPSAYıCıNıN SAS URI 'sini girin.

   ![Özel kurulumlarla Gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

Standart özel kurulum tamamlandıktan ve Azure-SSIS IR başladıktan sonra, tüm özel kurulum günlüklerini kapsayıcının *Main. cmd. log* klasöründe bulabilirsiniz. *Main. cmd* ve diğer yürütme günlüklerinin standart çıktısını içerirler.

### <a name="express-custom-setup"></a>Hızlı özel kurulum

ADF Kullanıcı arabirimindeki Express özel kurulumları ile Azure-SSIS IR sağlamak veya yeniden yapılandırmak için aşağıdaki adımları izleyin.

1. **Integration Runtime kurulum** bölmesinin **Gelişmiş ayarlar** sayfasındaki **ek sistem yapılandırmalarına/bileşen yüklemelerine sahip Azure-SSIS Integration Runtime özelleştirin** onay kutusunu seçin. 

1. **Yeni** ' yi seçerek **hızlı özel kurulum ekle** bölmesini açın ve ardından **hızlı özel kurulum türü** açılan listesinde bir tür seçin. Şu anda, cmdkey komutunu çalıştırmaya, ortam değişkenleri eklemeye, Azure PowerShell yüklemeye ve lisanslı bileşenleri yüklemeye yönelik hızlı özel ayarlar sunuyoruz.

#### <a name="running-cmdkey-command"></a>Cmdkey komutu çalıştırılıyor

Hızlı özel kurulumunuz için **cmdkey komut türünü Çalıştır** ' ı seçerseniz, Azure-SSIS IR Windows cmdkey komutunu çalıştırabilirsiniz. Bunu yapmak için sırasıyla **/Add**, **/User** ve **/Pass** metin kutularına hedeflenen bilgisayar adı veya etki alanı adı, Kullanıcı adı, hesap adı ve parola ya da hesap anahtarınızı girin. Bu, Azure-SSIS IR SQL sunucuları, dosya paylaşımları veya Azure dosyaları için erişim kimlik bilgilerini kalıcı hale getirebilmeniz için izin verir. Örneğin, Azure dosyalarına erişmek için `YourAzureStorageAccountName.file.core.windows.net` `azure\YourAzureStorageAccountName` `YourAzureStorageAccountKey` sırasıyla **/Add**, **/User** ve **/Pass** gibi bir giriş yapabilirsiniz. Bu, yerel makinenizde Windows [cmdkey](/windows-server/administration/windows-commands/cmdkey) komutunu çalıştırmaya benzer. Şimdilik cmdkey komutunu çalıştırmak için yalnızca bir hızlı özel kurulum desteklenir. Birden çok cmdkey komutunu çalıştırmak için bunun yerine standart bir özel kurulum kullanın.

#### <a name="adding-environment-variables"></a>Ortam değişkenleri ekleme

Hızlı özel kurulumlarınız için **ortam değişkeni Ekle** türünü seçerseniz, Azure-SSIS IR bir Windows ortam değişkeni ekleyebilirsiniz. Bunu yapmak için sırasıyla **değişken adı** ve **değişken değeri** metin kutularında ortam değişkeni adınızı ve değerini girin. Bu, örneğin betik bileşenlerinde/görevlerinde Azure-SSIS IR çalışan paketlerinizde bulunan ortam değişkenini kullanmanıza olanak tanır. Bu, yerel makinenizde Windows [set](/windows-server/administration/windows-commands/set_1) komutunu çalıştırmaya benzer.

#### <a name="installing-azure-powershell"></a>Azure PowerShell yükleniyor

Hızlı özel kurulumlarınız için **yükleme Azure PowerShell** türünü seçerseniz, Azure-SSIS IR daha az PowerShell modülünü yükleyebilirsiniz. Bunu yapmak için, [desteklenen olanlar listesinden](https://www.powershellgallery.com/packages/az)istediğiniz az Module sürüm numarasını (x. y. z) girin. Bu, Azure kaynaklarını (örneğin, [Azure Analysis Services (AAS)](../analysis-services/analysis-services-powershell.md)yönetmek için paketlerinizde Azure PowerShell cmdlet 'leri/betikleri çalıştırmanızı sağlar.

#### <a name="installing-licensed-components"></a>Lisanslı bileşenleri yükleme

Hızlı özel kurulumlarınız için **lisanslı bileşen türünü yükleme** ' yi seçerseniz, **bileşen adı** açılır listesinde ISV iş ortaklarımızın tümleşik bir bileşenini seçebilirsiniz:

   * **Sentryone 'ın 'ın görev fabrikası** bileşenini seçerseniz, Azure-SSIS IR [görev fabrikası](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) paketini sentryone 'ın adresinden yükleyebilirsiniz. Bunu yapmak için, **Lisans anahtarı** metin kutusundan önceden satın aldığınız ürün lisans anahtarını girin. Geçerli tümleşik sürüm **2020.1.3**.

   * **Oh22's HEDDA ' yi seçerseniz. IO** bileşeni, Hedda 'yi yükleyebilirsiniz [.](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR)Azure-SSIS IR, GÇ veri kalitesi/oh22 'den bileşeni Temizleme. Bunu yapmak için, hizmetini önceden satın almanız gerekir. Geçerli tümleşik sürüm **1.0.14**.

   * **Oh22's SQLPhonetics.net** bileşenini seçerseniz, Azure-SSIS IR [SQLPhonetics.net](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) veri kalitesini/eşleşen bileşenini oh22 adresinden yükleyebilirsiniz. Bunu yapmak için, **Lisans anahtarı** metin kutusundan önceden satın aldığınız ürün lisans anahtarını girin. Geçerli tümleşik sürüm **1.0.45**.

   * **Kingswaysoft 'un SSIS Tümleştirme Araç Seti** bileşenini seçerseniz, Azure-SSIS IR Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud gibi CRM/ERP/pazarlama/işbirliği uygulamalarına yönelik [SSIS Tümleştirme Araç Seti](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) bağlayıcılarını yükleyebilirsiniz. Bunu yapmak için, **Lisans anahtarı** metin kutusundan önceden satın aldığınız ürün lisans anahtarını girin. Geçerli tümleşik sürüm **2020,1**' dir.

   * **Kingswaysoft 'ıN SSIS üretkenlik paketi** bileşenini seçerseniz, Azure-SSIS IR [ssmsoft 'Tan SSIS üretkenlik paketi](https://www.kingswaysoft.com/products/ssis-productivity-pack) paketini yükleyebilirsiniz. Bunu yapmak için, **Lisans anahtarı** metin kutusundan önceden satın aldığınız ürün lisans anahtarını girin. Geçerli tümleşik sürüm **20,1**' dir.

   * The **Obald yazılımının Xdikkatini** oluştur bileşenini seçerseniz, Azure-SSIS IR The OBALD yazılımından SAP sıstemlerı (ERP, s/4HANA, siyah beyaz) için [xümlik](https://theobald-software.com/en/xtract-is/) bağlayıcı paketini yükleyebilirsiniz. Bunu yapmak için & sürükleyin ve satın aldığınız ürün lisans dosyasını önceden **Lisans dosyası** giriş kutusuna bırakın/karşıya yükleyin. Geçerli tümleşik sürüm **6.1.1.3**.

   * **Akaydedilmiş geçici tümleştirme hizmeti** bileşenini seçerseniz, Azure-SSIS IR sap ve Salesforce sistemleri Için [tümleştirme hizmeti](https://www.aecorsoft.com/en/products/integrationservice) bağlayıcılarının paketini, akaydedilmiş yazılımdan yükleyebilirsiniz. Bunu yapmak için, **Lisans anahtarı** metin kutusundan önceden satın aldığınız ürün lisans anahtarını girin. Geçerli tümleşik sürüm **3.0.00**.

   * **CDATA 'NıN SSIS standart paket** bileşenini seçerseniz, Microsoft SharePoint bağlayıcıları gibi CDATA 'dan en popüler bileşenlerin [SSIS standart paket](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard) paketini Azure-SSIS IR yükleyebilirsiniz. Bunu yapmak için, **Lisans anahtarı** metin kutusundan önceden satın aldığınız ürün lisans anahtarını girin. Geçerli tümleşik sürüm **19,7354**' dir.

   * **CDATA 'NıN SSIS genişletilmiş paket** bileşenini seçerseniz, Microsoft Dynamics 365 Business Central bağlayıcıları ve diğer bileşenler gibi CDATA 'dan tüm bileşenlerin [SSIS genişletilmiş paket](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended) **paketini Azure-SSIS IR** yükleyebilirsiniz. Bunu yapmak için, **Lisans anahtarı** metin kutusundan önceden satın aldığınız ürün lisans anahtarını girin. Geçerli tümleşik sürüm **19,7354**' dir. Büyük boyutu nedeniyle, yükleme zaman aşımını önlemek için lütfen Azure-SSIS IR düğüm başına en az 4 CPU çekirdeğe sahip olduğundan emin olun.

Eklediğiniz Express özel kurulumları **Gelişmiş ayarlar** sayfasında görünür. Bunları kaldırmak için onay kutularını işaretleyin ve ardından **Sil**' i seçin.

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kullanarak özel kurulumlarla Azure-SSIS IR sağlamak veya yeniden yapılandırmak için aşağıdaki adımları izleyin.

1. Azure-SSIS IR zaten başlatılmış/çalışıyor ise, önce bunu durdurun.

1. Daha sonra, `Set-AzDataFactoryV2IntegrationRuntime` Azure-SSIS IR başlamadan önce cmdlet 'ini çalıştırarak özel kurulumları ekleyebilir veya kaldırabilirsiniz.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Standard")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Extended")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

### <a name="standard-custom-setup-samples"></a>Standart özel kurulum örnekleri

Standart özel kurulumların bazı örneklerini görüntülemek ve yeniden kullanmak için aşağıdaki adımları izleyin.

1. Azure Depolama Gezgini kullanarak genel önizleme kapsayımuza bağlanın.

   a. **(Yerel ve bağlı)** altında **depolama hesapları**' na sağ tıklayın, **Azure depolama 'ya Bağlan**' ı seçin, **bir bağlantı dizesi veya paylaşılan erişim imzası URI 'Si kullan**' ı seçin ve ardından **İleri**' yi seçin.

      ![Paylaşılan erişim Imzasıyla Azure depolama 'ya bağlanma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. **SAS URI 'Si kullan** ' ı seçin ve ardından **URI** metın kutusuna aşağıdaki SAS URI 'sini girin:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Kapsayıcı için paylaşılan erişim Imzasını sağlama](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. **İleri**' yi seçin ve ardından **Bağlan**' ı seçin.

   d. Sol bölmede bağlı **publicpreview** kapsayıcısını seçin ve ardından *customsetupscript* klasörüne çift tıklayın. Bu klasörde aşağıdaki öğeler vardır:

      * Azure-SSIS IR her bir düğümüne temel bir görev yüklemeye yönelik özel bir kurulum içeren *örnek* bir klasör. Görev hiçbir şey yapmaz, ancak birkaç saniye uyku moduna geçmez. Klasör, tüm içeriklerin (*gacutil.exe*, *gacutil.exe.config* ve *1033\gacutlrc.dll*), kapsayıcınıza olduğu gibi kopyalanabilmesi için de bir *Gacutil* klasörü içerir.

      * Gerçek Kullanıcı senaryolarından birkaç özel kurulum örneği içeren bir *Usersenaryolar* klasörü. Azure-SSIS IR birden çok örnek yüklemek istiyorsanız, kendi özel kurulum komut dosyası (*Main. cmd*) dosyalarını tek bir tane içinde birleştirebilir ve ilişkili tüm dosyaları kapsayıcınıza yükleyebilirsiniz.

        ![Genel Önizleme kapsayıcısının içeriği](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Aşağıdaki öğeleri bulmak için *Usersenaryolar* klasörünü çift tıklatın:

      * Azure-SSIS IR her bir düğümüne .NET Framework önceki bir sürümünü yüklemek için özel bir kurulum betiği (*Main. cmd*) içeren BIR *.NET Framework 3,5* klasörü. Bu sürüm bazı özel bileşenler için gerekli olabilir.

      * Azure-SSIS IR her bir düğümüne SQL Server komut satırı yardımcı programları (*MsSqlCmdLnUtils.msi*) yüklemek için özel bir kurulum betiği (*Main. cmd*) içeren bir *bcp* klasörü. Bu yardımcı programlardan biri toplu kopyalama programıdır (*bcp*).

      * Kendi DNS sonekini (örneğin, *test.com*), nitelenmemiş tek etiketli bir etki alanı adına eklemek için özel bir kurulum betiği (*Main. cmd*) IÇEREN bir *dns son eki* klasörü ve Azure-SSIS IR DNS sorguları üzerinde kullanmadan önce tam etki alanı adına (FQDN) dönüştürün.

      * Azure-SSIS IR her bir düğümüne bazı C# derlemelerini ve kitaplıklarını yüklemek için özel bir kurulum betiği (*Main. cmd*) Içeren bir *Excel* klasörü. Bunları, Excel dosyalarını dinamik olarak okumak ve yazmak için betik görevlerinde kullanabilirsiniz. 
      
        İlk olarak, [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) ve [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)indirin ve ardından Tümünü *Main. cmd* ile birlikte kapsayıcınıza yükleyin. Alternatif olarak, yalnızca standart Excel bağlayıcıları (bağlantı Yöneticisi, kaynak ve hedef) kullanmak istiyorsanız, bunları içeren yeniden dağıtılabilir erişim Azure-SSIS IR önceden yüklenmiş olduğundan, özel bir kuruluma gerek kalmaz.
      
      * Azure-SSIS IR her bir düğümüne MySQL ODBC sürücülerini yüklemek için özel bir kurulum betiği (*Main. cmd*) Içeren BIR *MySQL ODBC* klasörü. Bu kurulum, MySQL sunucusuna bağlanmak için ODBC bağlayıcılarını (bağlantı Yöneticisi, kaynak ve hedef) kullanmanıza olanak tanır. 
     
        İlk olarak, [MySQL ODBC sürücü yükleyicilerinin en son 64-bit ve 32-bit sürümlerini indirin](https://dev.mysql.com/downloads/connector/odbc/) (örneğin, *mysql-connector-odbc-8.0.13-winx64.msi* ve *mysql-connector-odbc-8.0.13-win32.msi*) ve ardından bunları bir bütün olarak *ana. cmd* ile birlikte kapsayıcınıza yükleyin.

      * Azure-SSIS IR Enterprise Edition 'ın her bir düğümüne Oracle bağlayıcıları ve OCı sürücüsünü yüklemek için özel bir kurulum betiği (*Main. cmd*) ve sessiz yükleme yapılandırma dosyası (*Client. rsp*) içeren bir *Oracle ENTERPRISE* klasörü. Bu kurulum, Oracle sunucusuna bağlanmak için Oracle bağlantı yöneticisini, kaynağını ve hedefini kullanmanıza olanak sağlar. 
      
        İlk olarak, Microsoft [Indirme merkezi](https://www.microsoft.com/en-us/download/details.aspx?id=55179) 'nden (*AttunitySSISOraAdaptersSetup.msi* ve *AttunitySSISOraAdaptersSetup64.msi*) Microsoft bağlayıcıları v 5.0 ' ı, Oracle 'dan en son Oracle istemcisini (örneğin, *winx64_12102_client.zip*) [indirin.](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html) Ardından, bunları bir bütün olarak *Main. cmd* ve *Client. rsp* ile birlikte kapsayıcısına yükleyin. Oracle 'a bağlanmak için TNS kullanırsanız, *tnsnames. ora*'yı indirmeniz, düzenlemeniz ve kapsayıcıya yüklemeniz gerekir. Bu şekilde, kurulum sırasında Oracle yükleme klasörüne kopyalanabilir.

      * Azure-SSIS IR her bir düğümüne Oracle ODP.NET sürücüsünü yüklemek için özel bir kurulum betiği (*Main. cmd*) Içeren BIR *Oracle standart ADO.net* klasörü. Bu kurulum, Oracle sunucusuna bağlanmak için ADO.NET bağlantı yöneticisini, kaynağını ve hedefini kullanmanıza olanak sağlar. 
      
        İlk olarak, [en son Oracle odp.net sürücüsünü indirin](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (örneğin, *ODP.NET_Managed_ODAC122cR1.zip*) ve ardından dosyayı *Main. cmd* ile birlikte kapsayıcınıza yükleyin.
       
      * Azure-SSIS IR Oracle ODBC sürücüsünü, her bir düğüme yüklemek için özel bir kurulum betiği (*Main. cmd*) IÇEREN *Oracle standart ODBC* klasörü. Betik Ayrıca veri kaynağı adı 'nı (DSN) yapılandırır. Bu kurulum, Oracle sunucusuna bağlanmak için ODBC bağlantı Yöneticisi, kaynak ve hedef ya da Power Query bağlantı yöneticisini ve kaynak ODBC veri kaynağı türünü kullanmanıza olanak sağlar. 
      
        İlk olarak, en son Oracle Instant Client 'ı (temel paket veya temel Lite paketi) ve ODBC paketini indirin ve ardından bunları kendi *ana. cmd* ile birlikte kapsayıcınıza yükleyin:
        * [64 bitlik paketleri indirin](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (temel paket: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Temel Lite paketi: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; ODBC paketi: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [32 bitlik paketleri indirin](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (temel paket: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Temel Lite paketi: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; ODBC paketi: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Azure-SSIS IR her bir düğümüne Oracle OLEDB sürücüsünü yüklemek için özel bir kurulum betiği (*Main. cmd*) Içeren BIR *Oracle Standard OLEDB* klasörü. Bu kurulum, Oracle sunucusuna bağlanmak için OLEDB bağlantı yöneticisini, kaynağını ve hedefini kullanmanıza olanak sağlar. 
     
        İlk olarak, [en son Oracle oledb sürücüsünü indirin](https://www.oracle.com/partners/campaign/index-090165.html) (örneğin, *ODAC122010Xcopy_x64.zip*) ve ardından onu *ana. cmd* ile birlikte kapsayıcınıza yükleyin.

      * Azure-SSIS IR her bir düğümüne PostgreSQL ODBC sürücülerini yüklemek için özel bir kurulum betiği (*Main. cmd*) Içeren bir *PostgreSQL ODBC* klasörü. Bu kurulum, PostgreSQL sunucusuna bağlanmak için ODBC bağlantı yöneticisini, kaynağını ve hedefini kullanmanıza olanak sağlar. 
     
        İlk olarak, [PostgreSQL ODBC sürücü yükleyicilerinin en son 64-bit ve 32-bit sürümlerini indirin](https://www.postgresql.org/ftp/odbc/versions/msi/) (örneğin, *psqlodbc_x64.msi* ve *psqlodbc_x86.msi*) ve sonra bunları kendi *ana. cmd* ile birlikte kapsayıcınıza yükleyin.

      * Azure-SSIS IR Enterprise Edition 'ın her bir düğümüne SAP .NET bağlayıcı derlemesini (*librfc32.dll*) yüklemek için özel bir kurulum betiği (*Main. cmd*) içeren *SAP BW* klasörü. Bu kurulum, SAP BW sunucusuna bağlanmak için SAP BW bağlantı yöneticisini, kaynağını ve hedefini kullanmanıza olanak sağlar. 
      
        İlk olarak, *librfc32.dll* 64 bit veya 32 BIT sürümünü SAP yükleme klasöründen, kapsayıcınıza *Main. cmd* ile birlikte karşıya yükleyin. Daha sonra betik, kurulum sırasında SAP derlemesini *%Windir%\SysWOW64* veya *%Windir%\System32* klasörüne kopyalar.

      * Azure-SSIS IR her bir düğümüne Azure PowerShell yüklemek için özel bir kurulum betiği (*Main. cmd*) Içeren bir *depolama* klasörü. Bu kurulum, [Azure depolama alanınızı yönetmek için Azure PowerShell cmdlet 'leri/betikleri](../storage/blobs/storage-quickstart-blobs-powershell.md)çalıştıran SSIS paketlerini dağıtmanıza ve çalıştırmanıza olanak tanır. 
      
        *Main. cmd* dosyasını, bir örnek *AzurePowerShell.msi* (veya en son sürümü kullanın) kopyalayın ve kapsayıcınıza *storage.ps1* . Paketleriniz için bir şablon olarak *PowerShell. dtsx* kullanın. Paket şablonu, değiştirilebilir bir PowerShell betiği (*storage.ps1*) yükleyen bir [Azure Blob indirme görevini](/sql/integration-services/control-flow/azure-blob-download-task)ve komut dosyasını her düğümde yürüten bir [işlem Çalıştır görevi](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)birleştirir.

      * Özel bir kurulum betiği (*Main. cmd*), ilişkili dosya (*Install. cmd*) ve yükleyici paketleri (*. msi*) içeren bir *Teradata* klasörü. Bu dosyalar, Azure-SSIS IR Enterprise Edition 'ın her bir düğümüne Teradata bağlayıcıları, Teradata Parallel Transporter (TPT) API 'sini ve ODBC sürücüsünü yükler. Bu kurulum, Teradata sunucusuna bağlanmak için Teradata bağlantı yöneticisini, kaynağını ve hedefini kullanmanıza olanak sağlar. 
      
        İlk olarak, [Teradata araçları ve yardımcı programlar 15. x ZIP dosyasını](http://partnerintelligence.teradata.com) (örneğin,  *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*) indirip daha önce bahsedilen *. cmd* ve *. msi* dosyalarıyla birlikte kapsayıcınıza yükleyin.

      * Azure-SSIS IR her bir düğümde güçlü şifreleme ve daha güvenli ağ protokolü (TLS 1,2) kullanmak için özel bir kurulum betiği (*Main. cmd*) Içeren bir *TLS 1,2* klasörü. Betik, eski SSL/TLS sürümlerini de devre dışı bırakır.

      * Azure-SSIS IR her bir düğümüne Zulu OpenJDK yüklemek için özel bir kurulum betiği (*Main. cmd*) ve PowerShell dosyası (*install_openjdk.ps1*) IÇEREN BIR *Zulu OpenJDK* klasörü. Bu kurulum, ORC ve Parquet dosyalarını işlemek için Azure Data Lake Store ve esnek dosya bağlayıcıları kullanmanıza olanak sağlar. Daha fazla bilgi için bkz. [Tümleştirme Hizmetleri Için Azure Feature Pack](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#dependency-on-java). 
      
        İlk olarak, [en son Zulu OpenJDK 'yi indirin](https://www.azul.com/downloads/zulu/zulu-windows/) (örneğin, *zulu8.33.0.1-jdk8.0.192-win_x64.zip*) ve ardından bunu *Main. cmd* ve *install_openjdk.ps1* birlikte kapsayıcınıza yükleyin.

        ![Kullanıcı senaryoları klasöründeki klasörler](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Bu standart özel kurulum örneklerini yeniden kullanmak için, seçili klasörün içeriğini kapsayıcınıza kopyalayın.

1. ADF Kullanıcı arabirimindeki Azure-SSIS IR sağladığınızda veya yeniden yapılandırdığınızda, **tümleştirme çalışma zamanı kurulum** bölmesinin **gelişmiş ayarlar** sayfasında **Azure-SSIS Integration Runtime ek sistem yapılandırması/bileşen yüklemeleri ile Özelleştir** onay kutusunu seçin. Ardından, **özel kurulum KAPSAYıCıSı SAS URI 'si** metin kutusuna KAPSAYıCıNıN SAS URI 'sini girin.
   
1. Azure PowerShell kullanarak Azure-SSIS IR sağladığınızda veya yeniden yapılandırdığınızda, zaten başlatılmış/çalışıyor durumunda uygulamayı durdurun, `Set-AzDataFactoryV2IntegrationRuntime` cmdlet 'ini parametre değeri olarak KAPSAYıCıNıN SAS URI 'siyle çalıştırın `SetupScriptContainerSasUri` ve ardından Azure-SSIS IR başlatın.

1. Standart özel kurulum tamamlandıktan ve Azure-SSIS IR başladıktan sonra, tüm özel kurulum günlüklerini kapsayıcının *Main. cmd. log* klasöründe bulabilirsiniz. *Main. cmd* ve diğer yürütme günlüklerinin standart çıktısını içerirler.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure-SSIS IR Enterprise sürümünü ayarlama](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Azure-SSIS IR için ücretli veya lisanslı bileşenler geliştirin](how-to-develop-azure-ssis-ir-licensed-components.md)
