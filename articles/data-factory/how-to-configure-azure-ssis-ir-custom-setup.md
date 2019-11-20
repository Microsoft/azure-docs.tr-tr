---
title: 'Azure-SSIS tümleştirme çalışma zamanı için kurulumu özelleştirme '
description: Bu makalede, ek bileşenleri yüklemek veya ayarları değiştirmek için Azure-SSIS tümleştirme çalışma zamanı için özel kurulum arabirimi 'nin nasıl kullanılacağı açıklanır.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 1/25/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 222672a93ccde7464ec1f37212f18996033a1460
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674858"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS tümleştirme çalışma zamanı için kurulumu özelleştirme

Azure-SSIS Integration Runtime için özel kurulum arabirimi, Azure-SSIS IR sağlama veya yeniden yapılandırma sırasında kendi kurulum adımlarınızı eklemek için bir arabirim sağlar. Özel Kurulum, varsayılan işletim yapılandırması veya ortamını değiştirmenize (örneğin, ek Windows Hizmetleri başlatmak veya dosya paylaşımları için erişim kimlik bilgilerini kalıcı hale getirmek) veya ek bileşenleri (örneğin, derlemeler, sürücüler veya Uzantılar) yüklemenizi sağlar Azure-SSIS IR.

Özel kurulumunuzu, bir betiği ve ilişkili dosyalarını hazırlarken ve bunları Azure Storage hesabınızdaki bir blob kapsayıcısına yükleyerek yapılandırırsınız. Azure-SSIS IR sağladığınızda veya yeniden yapılandırdığınızda, kapsayıcınıza yönelik bir paylaşılan erişim Imzası (SAS) Tekdüzen Kaynak tanımlayıcısı (URI) sağlarsınız. Azure-SSIS IR her bir düğüm, komut dosyasını ve ilişkili dosyalarını kapsayıcınızdan indirir ve özel kurulumunuzu yükseltilmiş ayrıcalıklarla çalıştırır. Özel Kurulum tamamlandığında her düğüm, çalışma ve diğer günlüklerin standart çıkışını kapsayıcınıza yükler.

Hem ücretsiz hem de lisanssız bileşenleri, ücretli veya lisanslı bileşenleri yükleyebilirsiniz. ISV iseniz, bkz. [Azure-SSIS IR için ücretli veya lisanslı bileşenler geliştirme](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Azure-SSIS IR v2 serisi düğümleri özel kurulum için uygun değildir, bu nedenle lütfen bunun yerine v3 serisi düğümlerini kullanın.  V2 serisi düğümlerini zaten kullanıyorsanız, lütfen v3 serisi düğümleri en kısa sürede kullanmaya geçin.

## <a name="current-limitations"></a>Geçerli sınırlamalar

-   Derlemeleri genel derleme önbelleği 'ne (GAC) yüklemek için `gacutil.exe` kullanmak istiyorsanız, özel kurulumlarınızın bir parçası olarak `gacutil.exe` sağlamanız veya genel önizleme kapsayıcısında sunulan kopyayı kullanmanız gerekir.

-   Betiğinizdeki bir alt klasöre başvurmak istiyorsanız, `msiexec.exe` kök klasöre başvurmak için `.\` gösterimini desteklemez. `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`yerine `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` gibi bir komut kullanın.

-   Azure-SSIS IR bir sanal ağa özel kurulum ile katılmanız gerekirse, yalnızca Azure Resource Manager sanal ağ desteklenir. Klasik sanal ağ desteklenmiyor.

-   Yönetim paylaşımının Azure-SSIS IR Şu anda desteklenmiyor.

-   IBM ıferies erişimi ODBC sürücüsü Azure-SSIS IR desteklenmez. Özel Kurulum sırasında yükleme hatası görebilirsiniz. Yardım için lütfen IBM desteği ile iletişime geçin.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS IR özelleştirmek için aşağıdaki işlemleri yapmanız gerekir:

-   [Azure aboneliği](https://azure.microsoft.com/)

-   [Bir Azure SQL veritabanı veya yönetilen örnek sunucusu](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Azure-SSIS IR sağlama](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Bir Azure depolama hesabı](https://azure.microsoft.com/services/storage/). Özel kurulum için özel kurulum komut dosyanızı ve ilişkili dosyalarını bir blob kapsayıcısına yükler ve depolar. Özel Kurulum işlemi aynı zamanda yürütme günlüklerini aynı blob kapsayıcısına yükler.

## <a name="instructions"></a>Yönergeler

1. [Azure PowerShell](/powershell/azure/install-az-ps)indirin ve yükleyin.

1. Özel kurulum komut dosyanızı ve ilişkili dosyalarını (örneğin,. bat,. cmd,. exe,. dll,. msi veya. ps1 dosyaları) hazırlayın.

   1.  Özel kurulumlarınızın giriş noktası olan `main.cmd`adlı bir betik dosyanız olmalıdır.

   1.  Betiğin sessizce yürütüldüğünden emin olmanız gerekir, önce betiği yerel makinede test etmeniz önerilir.

   1.  Diğer araçlarla (örneğin, `msiexec.exe`), kapsayıcınıza karşıya yüklenecek ek günlüklerin oluşturulmasını istiyorsanız, betiklerinizde günlük klasörü olarak `CUSTOM_SETUP_SCRIPT_LOG_DIR` önceden tanımlanmış ortam değişkenini belirtin (örneğin, `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. [Azure Depolama Gezgini](https://storageexplorer.com/)indirin, yükleyin ve başlatın.

   1. **(Yerel ve bağlı)** altında, **depolama hesapları** ' nı sağ seçin ve **Azure depolama 'ya Bağlan**' ı seçin.

      ![Azure depolamaya bağlanma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. **Depolama hesabı adı ve anahtarı kullan** ' ı seçin ve **İleri ' yi**seçin.

      ![Depolama hesabı adını ve anahtarını kullanma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Azure depolama hesabınızın adını ve anahtarınızı girip, **İleri**' yi seçin ve ardından **Bağlan**' ı seçin.

      ![Mağaza hesabı adını ve anahtarını belirtin](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. Bağlı Azure depolama hesabınız altında **BLOB kapsayıcıları**' na sağ tıklayın, **BLOB kapsayıcısı oluştur**' u seçin ve yeni kapsayıcıyı adlandırın.

      ![Blob kapsayıcısı oluşturma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Yeni kapsayıcıyı seçin ve özel kurulum komut dosyanızı ve ilişkili dosyalarını karşıya yükleyin. `main.cmd` herhangi bir klasörde değil, kapsayıcının en üst düzeyinde karşıya yüklediğinizden emin olun. Ayrıca, kapsayıcınıza yalnızca gerekli özel kurulum dosyalarını içerdiğinden emin olun, böylece daha sonra Azure-SSIS IR indirmek uzun sürmeyecektir. Özel kurulum için en uzun süre, şu anda zaman aşımına uğramadan önce 45 dakika içinde ayarlanmıştır ve bu, kapsayıcıınızdan tüm dosyaları indirmek ve Azure-SSIS IR yüklemek için gereken süreyi de içerir. Daha uzun bir süre gerekiyorsa lütfen bir destek bileti yükseltin.

      ![Blob kapsayıcısına dosya yükleme](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Kapsayıcıya sağ tıklayın ve **paylaşılan erişim Imzasını al**' ı seçin.

      ![Kapsayıcı için paylaşılan erişim Imzasını al](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Kapsayıcınız için SAS URI 'sini, yeterince uzun bir süre sonu ve okuma + yazma + liste izinleriyle oluşturun. Azure-SSIS IR herhangi bir düğüm yeniden başlatıldığında özel kurulum komut dosyanızı ve ilişkili dosyalarını indirip çalıştırmak için SAS URI 'sine ihtiyacınız vardır. Kurulum yürütme günlüklerini karşıya yüklemek için yazma izninizin olması gerekir.

      > [!IMPORTANT]
      > Lütfen SAS URI 'sinin süresi dolana ve özel kurulum kaynaklarının Azure-SSIS IR, özellikle de bu süre boyunca düzenli olarak durdurup Azure-SSIS IR başlatırsanız, oluşturma işleminin silinmesine göre her zaman kullanılabilir olduğundan emin olun.

      ![Kapsayıcı için paylaşılan erişim Imzasını oluşturma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Kapsayıcının SAS URI 'sini kopyalayın ve kaydedin.

      ![Paylaşılan erişim Imzasını Kopyala ve Kaydet](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

   1. Azure-SSIS IR Data Factory Kullanıcı arabirimi ile sağladığınızda veya yeniden yapılandırdığınızda, Azure-SSIS IR başlamadan önce, **Gelişmiş ayarlar** panelinde ilgili alana kapsayıcının SAS URI 'sini girin:

      ![Paylaşılan erişim Imzasını girin](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      PowerShell ile Azure-SSIS IR sağladığınızda veya yeniden yapılandırdığınızda, Azure-SSIS IR başlamadan önce, yeni `SetupScriptContainerSasUri` parametresi değeri olarak kapsayıcının SAS URI 'SI ile `Set-AzDataFactoryV2IntegrationRuntime` cmdlet 'ini çalıştırın. Örneğin:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. Özel Kurulum tamamlandıktan ve Azure-SSIS IR başladıktan sonra, `main.cmd` ve diğer yürütme günlüklerinin standart çıkışını depolama kapsayıcının `main.cmd.log` klasöründe bulabilirsiniz.

1. Diğer özel kurulum örneklerini görmek için, Azure Depolama Gezgini ile genel önizleme kapsayıcısına bağlanın.

   a.  **(Yerel ve bağlı)** altında **depolama hesapları**' na sağ tıklayın, **Azure depolama 'ya Bağlan**' ı seçin, **bir bağlantı dizesi veya paylaşılan erişim imzası URI 'Si kullan**' ı seçin ve ardından **İleri**' yi seçin.

      ![Paylaşılan erişim Imzasıyla Azure depolama 'ya bağlanma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  **SAS URI 'Si kullan** ' ı seçin ve genel önizleme kapsayıcısı IÇIN AŞAĞıDAKI SAS URI 'sini girin. **İleri**' yi ve ardından **Bağlan**' ı seçin.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Kapsayıcı için paylaşılan erişim Imzasını sağlama](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Bağlı genel önizleme kapsayıcısını seçin ve `CustomSetupScript` klasöre çift tıklayın. Bu klasörde aşağıdaki öğeler vardır:

      1. Azure-SSIS IR her bir düğümüne temel bir görev yüklemeye yönelik özel bir kurulum içeren `Sample` klasörü. Görev hiçbir şey yapmaz, ancak birkaç saniye uyku moduna geçmez. Klasör ayrıca bir `gacutil` klasörü içerir, (`gacutil.exe`, `gacutil.exe.config`ve `1033\gacutlrc.dll`) tüm içeriği kapsayıcınızda olduğu gibi kopyalanabilir. Ayrıca, `main.cmd` dosya paylaşımları için erişim kimlik bilgilerini kalıcı hale getirmek için açıklamalar içerir.

      1. Gerçek kullanıcı senaryoları için birkaç özel kurulum içeren bir `UserScenarios` klasörü.

   ![Genel Önizleme kapsayıcısının içeriği](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. `UserScenarios` klasörüne çift tıklayın. Bu klasörde aşağıdaki öğeler vardır:

      1. Azure-SSIS IR her bir düğümünde özel bileşenler için gerekebilecek .NET Framework daha önceki bir sürümünü yüklemek üzere özel bir kurulum içeren `.NET FRAMEWORK 3.5` klasörü.

      1. Azure-SSIS IR her bir düğümüne toplu kopyalama programı (`bcp`) dahil olmak üzere SQL Server komut satırı yardımcı programları (`MsSqlCmdLnUtils.msi`) yüklemek için özel bir kurulum içeren `BCP` klasörü.

      1. Azure-SSIS IR her bir düğümüne açık kaynak derlemeler (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`ve `ExcelDataReader.dll`) yüklemek için özel bir kurulum içeren `EXCEL` klasörü.

      1. Tek bir kurulum betiği (`main.cmd`) ve sessiz yükleme yapılandırma dosyası (`client.rsp`) içeren ve Azure-SSIS IR Enterprise Edition 'ın her bir düğümüne Oracle bağlayıcıları ve OCı sürücüsü yükleyen bir `ORACLE ENTERPRISE` klasörü. Bu kurulum, Oracle bağlantı yöneticisini, kaynağını ve hedefini kullanmanıza olanak sağlar. İlk olarak, [Microsoft Indirme merkezi](https://www.microsoft.com/en-us/download/details.aspx?id=55179) 'nden (`AttunitySSISOraAdaptersSetup.msi` ve `AttunitySSISOraAdaptersSetup64.msi`) Microsoft bağlayıcıları v 5.0 'ı ve en son Oracle istemcisini (örneğin `winx64_12102_client.zip`, [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)'dan) indirin ve ardından bunları `main.cmd` ve `client.rsp` ile birlikte karşıya yükleyin Kapsayıcınız. Oracle 'a bağlanmak için TNS kullanırsanız, Ayrıca, kurulum sırasında Oracle yükleme klasörüne kopyalanabilmesi için `tnsnames.ora`indirmeniz, düzenlemeniz ve kapsayıcıya yüklemeniz gerekir.

      1. Azure-SSIS IR her bir düğümüne Oracle ODP.NET sürücüsünü yüklemek için özel bir kurulum betiği (`main.cmd`) içeren `ORACLE STANDARD ADO.NET` klasörü. Bu kurulum, ADO.NET bağlantı yöneticisini, kaynağını ve hedefini kullanmanıza olanak sağlar. İlk olarak, en son Oracle ODP.NET sürücüsünü indirin (örneğin, [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)'dan `ODP.NET_Managed_ODAC122cR1.zip`) ve ardından kapsayıcınıza `main.cmd` birlikte karşıya yükleyin.
       
      1. Oracle ODBC sürücüsünü yüklemek ve Azure-SSIS IR her bir düğümde DSN 'yi yapılandırmak için özel bir kurulum betiği (`main.cmd`) içeren `ORACLE STANDARD ODBC` klasörü. Bu kurulum, Oracle sunucusuna bağlanmak için ODBC bağlantı Yöneticisi/kaynak/hedef veya Power Query bağlantı Yöneticisi/kaynağını ODBC veri kaynağı türü ile birlikte kullanmanıza olanak sağlar. İlk olarak, en son Oracle Instant Client (temel paket veya temel Lite paketi) ve ODBC paketini (örneğin, 64 bitlik paketler) [buradan](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (temel paket: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, temel Lite paketi: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, ODBC paketi: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) veya 32-bit ' i indirin. [buradan](https://www.oracle.com/technetwork/topics/winsoft-085727.html) paketler (temel paket: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, temel Lite paketi: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, ODBC paketi: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`) ve ardından bunları kapsayıcınıza `main.cmd` birlikte karşıya yükleyin.

      1. Azure-SSIS IR Enterprise Edition 'ın her bir düğümüne SAP .NET bağlayıcı derlemesini (`librfc32.dll`) yüklemek için özel bir kurulum betiği (`main.cmd`) içeren bir `SAP BW` klasörü. Bu kurulum, SAP BW bağlantı yöneticisini, kaynağını ve hedefini kullanmanıza olanak sağlar. İlk olarak, `librfc32.dll` 64 bit veya 32 bit sürümünü `main.cmd`ile birlikte, SAP yükleme klasöründen kapsayıcınıza yükleyin. Betik daha sonra, kurulum sırasında SAP derlemesini `%windir%\SysWow64` veya `%windir%\System32` klasörüne kopyalar.

      1. Azure-SSIS IR her bir düğümüne Azure PowerShell yüklemek için özel bir kurulum içeren `STORAGE` klasörü. Bu kurulum, [Azure depolama hesabınızı işlemek Için PowerShell betikleri](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)çalıştıran SSIS paketlerini dağıtmanıza ve çalıştırmanıza olanak tanır. `main.cmd`kopyalayın, örnek bir `AzurePowerShell.msi` (veya en son sürümü yükler) ve `storage.ps1` kapsayıcınıza. Paketleriniz için bir şablon olarak PowerShell. dtsx kullanın. Paket şablonu, `storage.ps1` değiştirilebilir bir PowerShell betiği olarak indiren bir [Azure Blob Indirme görevini](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)ve komut dosyasını her düğümde yürüten bir [işlem Çalıştır görevi](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) birleştirir.

      1. Özel bir kurulum betiği (`main.cmd`), onunla ilişkili dosya (`install.cmd`) ve yükleyici paketleri (`.msi`) içeren `TERADATA` klasörü. Bu dosyalar, Azure-SSIS IR Enterprise Edition 'ın her bir düğümüne Teradata bağlayıcıları, TPT API ve ODBC sürücüsü yükler. Bu kurulum, Teradata bağlantı yöneticisini, kaynağını ve hedefini kullanmanıza olanak sağlar. İlk olarak Teradata araçları ve yardımcı programlar (TTU) 15. x ZIP dosyasını (örneğin, `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) [Teradata](http://partnerintelligence.teradata.com)'dan indirip yukarıdaki `.cmd` ve dosyaları kapsayıcınıza `.msi` birlikte karşıya yükleyin.

   ![Kullanıcı senaryoları klasöründeki klasörler](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. Bu özel kurulum örneklerini denemek için seçili klasördeki içeriği kopyalayıp kapsayıcınıza yapıştırın. PowerShell ile Azure-SSIS IR sağladığınızda veya yeniden yapılandırdığınızda, yeni `SetupScriptContainerSasUri` parametresinin değeri olarak kapsayıcının SAS URI 'SI ile `Set-AzDataFactoryV2IntegrationRuntime` cmdlet 'ini çalıştırın.

## <a name="next-steps"></a>Sonraki adımlar

-   [Azure-SSIS Integration Runtime Enterprise sürümü](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Azure-SSIS tümleştirme çalışma zamanı için ücretli veya lisanslı özel bileşenler geliştirme](how-to-develop-azure-ssis-ir-licensed-components.md)
