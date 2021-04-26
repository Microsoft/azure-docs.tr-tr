---
title: Azure Data Factory 'de bir Azure-SSIS tümleştirme çalışma zamanı oluşturma
description: Azure 'da SSIS paketlerini dağıtıp çalıştırmak için Azure Data Factory 'de Azure-SSIS tümleştirme çalışma zamanı oluşturmayı öğrenin.
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/09/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: e4f60539e2eb524e95c76814f7527377630349e2
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285250"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory 'de bir Azure-SSIS tümleştirme çalışma zamanı oluşturma

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede, Azure Data Factory (ADF) içinde bir Azure-SQL Server Integration Services (SSIS) tümleştirme çalışma zamanı (IR) sağlama adımları sağlanmaktadır. Bir Azure-SSIS IR şunları destekler:

- Azure SQL veritabanı sunucusu/yönetilen örneği (proje dağıtım modeli) tarafından barındırılan SSIS kataloğuna (SSıSDB) dağıtılan Paketleri çalıştırma
- Azure SQL yönetilen örneği (paket dağıtım modeli) tarafından barındırılan dosya sistemine, Azure dosyalarına veya SQL Server veritabanına (MSDB) dağıtılan Paketleri çalıştırma

Bir Azure-SSIS IR sağlandıktan sonra, Azure 'da paketlerinizi dağıtmak ve çalıştırmak için tanıdık araçları kullanabilirsiniz. Bu araçlar zaten Azure özellikli ve [dtutil](/sql/integration-services/dtutil-utility) ve [Azuredtexec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md)gibi SQL Server veri araçları (ssdt), SQL Server Management Studio (SSMS) ve komut satırı yardımcı programlarını içerir.

[Sağlama Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md) öğretici, Azure portal veya Data Factory uygulaması aracılığıyla bir Azure-SSIS IR oluşturmayı gösterir. Bu öğreticide, bir Azure SQL veritabanı sunucusu veya bir yönetilen örnek için SSSıSDB barındırmak üzere nasıl kullanılacağı gösterilmektedir. Bu makale öğreticiye genişleterek bu isteğe bağlı görevlerin nasıl yapılacağını açıklar:

- SSıSDB barındırmak için IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları veya özel uç nokta olan yönetilen bir örnek içeren bir Azure SQL veritabanı sunucusu kullanın. Bir önkoşul olarak, bir sanal ağa katılması için Azure-SSIS IR sanal ağ izinlerini ve ayarlarını yapılandırmanız gerekir.

- Azure SQL veritabanı sunucusuna veya yönetilen örneğe bağlanmak için veri fabrikanızın yönetilen kimliğiyle birlikte Azure Active Directory (Azure AD) kimlik doğrulaması kullanın. Bir önkoşul olarak, veri fabrikanızın yönetilen kimliğini bir SSıSDB örneği oluşturabileceğiniz bir veritabanı kullanıcısı olarak eklemeniz gerekir.

- Azure-SSIS IR bir sanal ağa katın veya şirket içi verilere erişim sağlamak için Azure-SSIS IR otomatik olarak barındırılan bir IR 'yi proxy olarak yapılandırın.

Bu makalede, Azure portal, Azure PowerShell ve Azure Resource Manager şablonunu kullanarak bir Azure-SSIS IR sağlama gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure aboneliği**. Aboneliğiniz yoksa [ücretsiz bir deneme](https://azure.microsoft.com/pricing/free-trial/) hesabı oluşturabilirsiniz.

- **Azure SQL veritabanı sunucusu veya SQL yönetilen örneği (isteğe bağlı)**. Henüz bir veritabanı sunucunuz veya yönetilen örneğiniz yoksa, başlamadan önce Azure portal bir tane oluşturun. Data Factory, bu veritabanı sunucusunda SSıSDB örneği oluşturacak. 

  Veritabanı sunucusunu veya yönetilen örneği, tümleştirme çalışma zamanı ile aynı Azure bölgesinde oluşturmanızı öneririz. Bu yapılandırma, tümleştirme çalışma zamanının yürütme günlüklerini Azure bölgeleriyle çıkmadan SSSıSDB 'ye yazmasını sağlar.

  Şu noktaları göz önünde bulundurun:

  - SSıSDB örneği, elastik havuzun bir parçası olarak veya yönetilen bir örnekte sizin adınıza tek bir veritabanı olarak oluşturulabilir. Bu, genel bir ağda veya bir sanal ağa katılarak erişilebilir. SSıSDB barındırmak için SQL veritabanı ve SQL yönetilen örneği arasında seçim yapma konusunda rehberlik için, bu makaledeki [SQL veritabanı ve SQL yönetilen örneği karşılaştırması](#comparison-of-sql-database-and-sql-managed-instance) bölümüne bakın. 
  
    SSD 'yi barındırmak için IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları veya özel uç nokta olan bir SQL yönetilen örneği ile bir Azure SQL veritabanı sunucusu kullanırsanız veya şirket içinde barındırılan bir IR yapılandırmadan şirket içi verilere erişmeniz gerekiyorsa, Azure-SSIS IR bir sanal ağa katılmanız gerekir. Daha fazla bilgi için bkz. bir [Azure-SSIS IR sanal ağa ekleme](./join-azure-ssis-integration-runtime-virtual-network.md).

  - Veritabanı sunucusu için **Azure hizmetlerine erişime Izin ver** ayarının etkin olduğunu doğrulayın. Bu ayar, IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları içeren bir Azure SQL veritabanı sunucusu veya SSSıSDB barındırmak için özel uç nokta olan bir SQL yönetilen örneği kullandığınızda geçerli değildir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nı güvenli hale getirme](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Bu ayarı PowerShell kullanarak etkinleştirmek için, bkz. [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - İstemci makinenin IP adresini veya istemci makinenin IP adresini içeren bir IP adresi aralığını, veritabanı sunucusunun güvenlik duvarı ayarlarındaki istemci IP adresi listesine ekleyin. Daha fazla bilgi için bkz. [Azure SQL Veritabanı'nda sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı kuralları yapılandırma](../azure-sql/database/firewall-configure.md).

  - Sunucu Yöneticisi kimlik bilgilerinizle SQL kimlik doğrulaması kullanarak veya veri fabrikanızın yönetilen kimliğiyle Azure AD kimlik doğrulamasını kullanarak, veritabanı sunucusuna bağlanabilirsiniz. İkincisi için, veri fabrikanızın yönetilen kimliğini, veritabanı sunucusuna erişim izinleri olan bir Azure AD grubuna eklemeniz gerekir. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasını bir Azure-SSIS IR Için etkinleştirme](./enable-aad-authentication-azure-ssis-ir.md).

  - Veritabanı sunucunuzun zaten bir SSıSDB örneğine sahip olmadığından emin olun. Azure-SSIS IR sağlanması, var olan bir SSıSDB örneğinin kullanımını desteklemez.

- **Sanal ağ Azure Resource Manager (isteğe bağlı)**. Aşağıdaki koşullardan en az biri doğru ise bir Azure Resource Manager sanal ağınız olmalıdır:

  - IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları veya özel uç nokta ile yönetilen bir örnek olan bir Azure SQL veritabanı sunucusunda SSSıSDB barındırıyorsanız.

  - Şirket içinde barındırılan bir IR yapılandırmadan Azure-SSIS IR çalışan SSIS paketlerindeki şirket içi veri depolarına bağlanmak istiyorsunuz.

- **Azure PowerShell (isteğe bağlı)**. Azure-SSIS IR sağlamak üzere bir PowerShell Betiği çalıştırmak istiyorsanız, [Azure PowerShell 'yi yüklemek ve yapılandırmak](/powershell/azure/install-az-ps)için yönergeleri izleyin.

### <a name="regional-support"></a>Bölgesel destek

Data Factory ve Azure-SSIS IR kullanılabilen Azure bölgelerinin listesi için bkz. [bölgeye göre Data Factory ve SSIS IR kullanılabilirliği](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>SQL veritabanı ve SQL yönetilen örneği karşılaştırması

Aşağıdaki tabloda, Azure-SSıR IR ile bağlantılı olarak bir Azure SQL veritabanı sunucusunun ve SQL yönetilen örneğinin belirli özellikleri karşılaştırılmaktadır:

| Özellik | SQL Veritabanı| SQL yönetilen örneği |
|---------|--------------|------------------|
| **Zamanlama** | SQL Server Agent kullanılamıyor.<br/><br/>Bkz. Data Factory işlem hattında [paket yürütmeyi zamanlama](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity).| Yönetilen örnek Aracısı kullanılabilir. |
| **Kimlik Doğrulaması** | **Db_owner** rolünde üye olarak, veri fabrikanızın yönetilen kimliği ile herhangi BIR Azure AD grubunu temsil eden bir bulunan veritabanı kullanıcısına sahıp bır SSISDB örneği oluşturabilirsiniz.<br/><br/>Bkz. [Azure SQL veritabanı sunucusunda BIR SSıSDB oluşturmak Için Azure AD kimlik doğrulamasını etkinleştirme](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Veri fabrikanızın yönetilen kimliğini temsil eden kapsanan bir veritabanı kullanıcısına sahip bir SSıSDB örneği oluşturabilirsiniz. <br/><br/>Bkz. Azure [SQL yönetilen örneği 'NDE SSıSDB oluşturmak Için Azure AD kimlik doğrulamasını etkinleştirme](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Hizmet katmanı** | Azure SQL veritabanı sunucunuz ile bir Azure-SSIS IR oluşturduğunuzda SSSıSDB için hizmet katmanını seçebilirsiniz. Birden çok hizmet katmanı vardır. | Yönetilen örneğiniz ile bir Azure-SSIS IR oluşturduğunuzda SSSıSDB için hizmet katmanını seçemezsiniz. Yönetilen örnekteki tüm veritabanları, bu örneğe ayrılan kaynağı paylaşır. |
| **Sanal ağ** | IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları ile bir Azure SQL veritabanı sunucusu kullanıyorsanız Azure-SSIS IR, Azure Resource Manager sanal ağa katılabilir. | Özel uç nokta ile yönetilen bir örnek kullanıyorsanız Azure-SSIS IR, bir Azure Resource Manager sanal ağa katılabilir. Yönetilen örneğiniz için genel bir uç nokta etkinleştirmezseniz sanal ağ gereklidir.<br/><br/>Azure-SSIS IR yönetilen örneğiniz ile aynı sanal ağa katılırsanız, Azure-SSIS IR yönetilen örneğinizin farklı bir alt ağda olduğundan emin olun. Azure-SSIS IR yönetilen örneğinden farklı bir sanal ağa katılırsanız, sanal ağ eşlemesi veya ağdan ağa bağlantı önerilir. Bkz. [uygulamanızı Azure SQL veritabanı yönetilen örneğine bağlama](../azure-sql/managed-instance/connect-application-instance.md). |
| **Dağıtılmış işlemler** | Bu özellik esnek işlemler aracılığıyla desteklenir. Microsoft Dağıtılmış İşlem Düzenleyicisi (MSDTC) işlemleri desteklenmez. SSIS paketleriniz dağıtılmış işlemleri koordine etmek için MSDTC kullanıyorsa, Azure SQL veritabanı için elastik işlemlere geçiş yapmayı göz önünde bulundurun. Daha fazla bilgi için bkz. [bulut veritabanları arasında dağıtılmış işlemler](../azure-sql/database/elastic-transactions-overview.md). | Desteklenmez. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Tümleştirme çalışma zamanı oluşturmak için Azure portal kullanma

Bu bölümde, bir Azure-SSIS IR oluşturmak için özellikle Data Factory Kullanıcı arabirimi (UI) veya uygulama Azure portal kullanırsınız.

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Veri fabrikanızı Azure portal aracılığıyla oluşturmak için, [Kullanıcı arabirimi aracılığıyla veri fabrikası oluşturma](./quickstart-create-data-factory-portal.md#create-a-data-factory)' daki adım adım yönergeleri izleyin. Oluşturulduktan sonra hızlı erişime izin vermek için, bunu yaparken **panoya sabitle** ' yi seçin. 

Data Factory oluşturulduktan sonra, Azure portal genel bakış sayfasını açın. Yazarın **Başlangıç** sayfasını ayrı bir sekmede açmak için **Yazar & izleyici** kutucuğunu seçin. Burada, Azure-SSIS IR oluşturmaya devam edebilirsiniz.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS tümleştirme çalışma zamanı sağlama

**Başlarken** sayfasında, **tümleştirme çalışma zamanı kurulum** bölmesini açmak Için **SSIS tümleştirmesini Yapılandır** kutucuğunu seçin.

   ![SSIS Tümleştirme Çalışma Zamanı Yapılandır kutucuğu](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

   **Integration Runtime kurulum** bölmesinde, genel, dağıtım ve gelişmiş ayarları büyük ölçüde yapılandırdığınız üç sayfa bulunur.

#### <a name="general-settings-page"></a>Genel ayarlar sayfası

**Tümleştirme çalışma zamanı kurulum** bölmesinin **Genel ayarlar** sayfasında aşağıdaki adımları izleyin.

   ![Genel ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. **Name** (Ad) alanına tümleştirme çalışma zamanınızın adını girin.

   2. **Description** (Açıklama) alanına tümleştirme çalışma zamanınız için bir açıklama girin.

   3. **Location** (Konum)alanında tümleştirme çalışma zamanınızın konumunu seçin. Yalnızca desteklenen konumlar görüntülenir. SSISDB'yi barındırmak için veritabanı sunucunuz ile aynı konumu seçmenizi öneririz.

   4. **Düğüm boyutu** için, tümleştirme çalışma zamanı kümenizdeki düğüm boyutunu seçin. Yalnızca desteklenen düğüm boyutları görüntülenir. Çok yoğun işlem yoğunluğu veya bellek kullanımı yoğun paketler çalıştırmak istiyorsanız büyük bir düğüm boyutu seçin (ölçeği büyütme).
   > [!NOTE]
   > [İşlem yalıtımına](../azure-government/azure-secure-isolation-guidance.md#compute-isolation)ihtiyacınız varsa lütfen **Standard_E64i_v3** düğüm boyutunu seçin. Bu düğüm boyutu, tüm fiziksel konakları kullanan yalıtılmış sanal makineleri temsil eder ve ABD Savunma Bakanlığı 'nın etki düzeyi 5 (IL5) iş yükleri gibi belirli iş yükleri için gerekli yalıtım düzeyini sağlar.
   
   5. **Node Number** (Düğüm Sayısı) alanında tümleştirme çalışma zamanınızdaki düğüm sayısını seçin. Yalnızca desteklenen düğüm sayıları görüntülenir. Birçok paketi paralel olarak çalıştırmak istiyorsanız çok sayıda düğümü olan büyük bir küme seçin (ölçeği genişletme).

   6. **Sürüm/lisans** için, tümleştirme çalışma zamanı için SQL Server sürümünü seçin: Standart veya kurumsal. Tümleştirme çalışma zamanı ' nda Gelişmiş özellikleri kullanmak istiyorsanız Kurumsal ' i seçin.

   7. Tasarruf **için,** tümleştirme çalışma zamanı için Azure hibrit avantajı seçeneğini belirleyin: **Evet** veya **Hayır**. Karma kullanım ile maliyet tasarruflarından faydalanmak için kendi SQL Server lisansınızı Yazılım Güvencesine getirmek istiyorsanız **Evet** ' i seçin.

   8. **Devam**’ı seçin.

#### <a name="deployment-settings-page"></a>Dağıtım ayarları sayfası

**Tümleştirme çalışma zamanı kurulum** bölmesinin **dağıtım ayarları** sayfasında, SSISDB ve veya Azure-SSIS IR paket depoları oluşturma seçeneklerine sahip olursunuz.

##### <a name="creating-ssisdb"></a>SSSıSDB oluşturma

**Tümleştirme çalışma zamanı kurulum** bölmesinin **dağıtım ayarları** SAYFASıNDA, paketlerinizi Sssısdb 'ye (proje dağıtım modeli) dağıtmak istiyorsanız, **projelerinizi/paketleri/ortamları/yürütme GÜNLÜKLERINIZI depolamak için Azure SQL veritabanı sunucusu/yönetilen örneği tarafından barındırılan SSIS kataloğunu (sssısdb) oluştur** onay kutusunu seçin. Alternatif olarak, paketlerinizi Azure SQL yönetilen örneği (paket dağıtım modeli) tarafından barındırılan dosya sistemine, Azure dosyalarına veya SQL Server veritabanına (MSDB) dağıtmak istiyorsanız, SSıSDB oluşturma veya onay kutusunu seçme gereksinimi yoktur.

Dağıtım modelinize bakılmaksızın, Azure SQL yönetilen örneği tarafından barındırılan SQL Server Agent paket yürütmelerini düzenlemek ve zamanlamak için kullanmak istiyorsanız, SSıSDB tarafından etkinleştirilir, bu nedenle yine de onay kutusunu seçin. Daha fazla bilgi için bkz. [Azure SQL yönetilen örnek Aracısı aracılığıyla SSIS paket yürütmelerini zamanlama](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Onay kutusunu seçerseniz, kendi adınıza oluşturacağınız ve yönetecağımız SSıSDB 'yi barındırmak için kendi veritabanı sunucunuzu getirmek üzere aşağıdaki adımları izleyin.

   ![SSSıSDB dağıtım ayarları](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. **Subscription** (Abonelik) alanında SSISDB'yi barındıran Azure aboneliğini seçin. 

   1. **Location** (Konum) alanında SSISDB'yi barındıran veritabanı sunucunuzun konumunu seçin. Tümleştirme çalışma zamanınızla aynı konumu seçmenizi öneririz.

   1. **Catalog Database Server Endpoint** (Katalog Veritabanı Sunucusu Uç Noktası) alanında SSISDB'yi barındıracak veritabanı sunucunuzun uç noktasını seçin. 
   
      Seçili veritabanı sunucusuna bağlı olarak, SSıSDB örneği, elastik havuzun bir parçası olarak veya yönetilen bir örnekte sizin adınıza tek bir veritabanı olarak oluşturulabilir. Bu, genel bir ağda veya bir sanal ağa katılarak erişilebilir. SSıSDB barındıracak veritabanı sunucusu türünü seçme konusunda rehberlik için bkz. [SQL veritabanı ve SQL yönetilen örneği karşılaştırması](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      SSD 'yi barındırmak için IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları veya özel uç nokta ile yönetilen bir örnek içeren bir Azure SQL veritabanı sunucusu seçerseniz veya şirket içinde barındırılan bir IR yapılandırmadan şirket içi verilere erişmeniz gerekiyorsa, Azure-SSIS IR bir sanal ağa katılmanız gerekir. Daha fazla bilgi için bkz. bir [Azure-SSIS IR sanal ağa ekleme](./join-azure-ssis-integration-runtime-virtual-network.md).

   1. Veritabanı sunucunuzun SSıSDB 'yi barındıracak kimlik doğrulama yöntemini seçmek için, **ADF 'niz için yönetilen kimlikle Azure AD kimlik doğrulamasını kullan** onay kutusunu seçin. Veri fabrikanızın yönetilen kimliğiyle SQL kimlik doğrulaması ya da Azure AD kimlik doğrulaması ' nı seçersiniz.

      Onay kutusunu seçerseniz, veritabanı sunucunuza erişim izinleri olan bir Azure AD grubuna veri fabrikanızın yönetilen kimliğini eklemeniz gerekir. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasını bir Azure-SSIS IR Için etkinleştirme](./enable-aad-authentication-azure-ssis-ir.md).
   
   1. **Yönetici Kullanıcı adı** IÇIN, SSISDB barındırmak üzere VERITABANı sunucunuzun SQL kimlik doğrulaması Kullanıcı adı ' nı girin. 

   1. **Yönetici parolası** IÇIN, SSISDB barındırmak üzere VERITABANı sunucunuzun SQL kimlik doğrulama parolasını girin. 

   1. İş sürekliliği ve olağanüstü durum kurtarma (BCDR) için Azure SQL veritabanı/yönetilen örnek yük devretme grubuyla eşitlenmiş olarak çalıştırılan bir çift hazır bekleyen Azure SSIS IR çifti yapılandırmak için **SSSıSDB yük devretmesi ile çift bekleme Azure-SSIS Integration Runtime çiftini kullanın** onay kutusunu seçin.
   
      Onay kutusunu seçerseniz, **çift bekleme çifti adı** metin kutusunda birincil ve ikincil Azure-SSIS IRS çiftini tanımlayacak bir ad girin. Birincil ve ikincil Azure-SSIS IRS 'nizi oluştururken aynı çift adı girmeniz gerekir.

      Daha fazla bilgi için bkz. [BCDR için Azure-SSIS IR yapılandırma](./configure-bcdr-azure-ssis-integration-runtime.md).

   1. **Katalog veritabanı hizmet katmanı** IÇIN, SSISDB barındıracak veritabanı sunucunuzun hizmet katmanını seçin. Temel, standart veya Premium katmanını seçin veya elastik havuz adı seçin.

Uygun olduğunda **Bağlantıyı Sına** ' yı seçin ve başarılı olursa **devam**' ı seçin.

> [!NOTE]
   > SSıSDB barındırmak için Azure SQL veritabanı sunucusu kullanıyorsanız, verileriniz varsayılan olarak, yedeklemeler için coğrafi olarak yedekli depolama alanında depolanır. Verilerinizin diğer bölgelerde çoğaltılmasını istemiyorsanız, [PowerShell kullanarak yedekleme depolama yedekliliği yapılandırma](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell)yönergelerini izleyin.
   
##### <a name="creating-azure-ssis-ir-package-stores"></a>Azure-SSIS IR paket depoları oluşturma

**Tümleştirme çalışma zamanı kurulum** bölmesinin **dağıtım ayarları** sayfasında, msdb, dosya sistemi veya Azure dosyaları 'Na (paket dağıtım modeli) dağıtılan paketlerinizi Azure-SSIS IR paket depolarıyla yönetmek Istiyorsanız, **Azure SQL yönetilen örneği tarafından barındırılan dosya sistemine/Azure DOSYALARıNA/SQL Server veritabanına (msdb) dağıtılan paketlerinizi yönetmek için paket depolarını oluştur** onay kutusunu seçin.
   
Azure-SSIS IR paket deposu, paketleri içeri/dışarı/dışarı ve dışa aktarmanıza/çalıştırmanıza ve [eskı SSIS paket deposuna](/sql/integration-services/service/package-management-ssis-service)benzer SSMS 'ler aracılığıyla çalıştırılan Paketleri izlemenize/durdurmasına izin verir. Daha fazla bilgi için bkz. [Azure-SSIS IR paketi depoları Ile SSIS paketlerini yönetme](./azure-ssis-integration-runtime-package-store.md).
   
Bu onay kutusunu seçerseniz, **Yeni**' yi seçerek Azure-SSIS IR birden çok paket deposu ekleyebilirsiniz. Buna karşılık, bir paket deposu birden çok Azure-SSIS IRS tarafından paylaşılabilir.

![MSDB/dosya sistemi/Azure dosyaları için dağıtım ayarları](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

**Paket deposu Ekle** bölmesinde aşağıdaki adımları izleyin.
   
   1. **Paket deposu adı** için, paket deponuzın adını girin. 

   1. **Paket deposu bağlı hizmeti** için, paketlerinizin dağıtıldığı dosya sistemi/Azure dosyaları/Azure SQL yönetilen örneği için erişim bilgilerini depolayan mevcut bağlı hizmetinizi seçin veya **Yeni**' yi seçerek yeni bir tane oluşturun. **Yeni bağlı hizmet** bölmesinde aşağıdaki adımları izleyin.
   
      > [!NOTE]
      > Azure dosyalarına erişmek için **Azure dosya depolama** veya **dosya sistemi** bağlı hizmetlerini kullanabilirsiniz. **Azure dosya depolama** bağlı hizmeti kullanıyorsanız, Azure-SSIS IR paket deposu şimdilik yalnızca **temel** ( **hesap anahtarı** veya **SAS URI**) kimlik doğrulama yöntemini destekler.  

      ![Bağlı hizmetler için dağıtım ayarları](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. **Ad** için, bağlı hizmetinizin adını girin. 
         
      1. **Açıklama** için, bağlı hizmetinizin açıklamasını girin. 
         
      1. **Tür** Için **Azure dosya depolama**, **Azure SQL yönetilen örneği** veya **dosya sistemi**' ni seçin.

      1. Her zaman paket depoları için erişim bilgilerini getirmek üzere Azure-SSIS IR kullandığımızdan **Integration Runtime aracılığıyla bağlanmayı** yoksayabilirsiniz.

      1. **Azure dosya depolama**' yı seçerseniz, **kimlik doğrulama yöntemi** için **temel**' yı seçin ve ardından aşağıdaki adımları uygulayın. 

         1. **Hesap seçme yöntemi** için **Azure aboneliği ' nden** seçim yapın veya **el ile girin**.
         
         1. **Azure aboneliğinden** seçim yaparsanız ilgili **Azure aboneliğini**, **depolama hesabı adını** ve **Dosya payını** seçin.
            
         1. **El Ile gir**' i seçerseniz, `\\<storage account name>.file.core.windows.net\<file share name>` **konak** için, `Azure\<storage account name>` **Kullanıcı adı** için ve `<storage account key>` **parola** için girin veya gizli dizi olarak depolandığı **Azure Key Vault** seçin.

      1. **Azure SQL yönetilen örneği**' ni seçerseniz, aşağıdaki adımları izleyin. 

         1. **Bağlantı dizesini** veya **Azure Key Vault** gizli olarak depolandığını seçin.

         1. **Bağlantı dizesi**' ni seçerseniz, aşağıdaki adımları uygulayın. 
             1. **Hesap seçme yöntemi** için **Azure aboneliği ' nden** seçim yaparsanız ilgili **Azure aboneliğini**, **sunucu adını**, **uç nokta türünü** ve **veritabanı adını** seçin. **El Ile gir**' i seçerseniz, aşağıdaki adımları uygulayın. 
                1.  **Tam etki alanı adı** için, `<server name>.<dns prefix>.database.windows.net` `<server name>.public.<dns prefix>.database.windows.net,3342` sırasıyla Azure SQL yönetilen örneğinizin özel veya genel uç noktasını girin. Özel uç noktayı girerseniz, ADF Kullanıcı arabirimine ulaşamadığından **test bağlantısı** geçerli değildir.

                1. **Veritabanı adı** için girin `msdb` .

            1. **Kimlik doğrulama türü** Için, **SQL kimlik doğrulaması**, **yönetilen kimlik** veya **hizmet sorumlusu**' nı seçin.

                - **SQL kimlik doğrulaması**' nı seçerseniz, Ilgili **Kullanıcı adını** ve **parolayı** girin veya gizli dizi olarak depolandığı **Azure Key Vault** seçin.

                -  **Yönetilen kimlik**' i SEÇERSENIZ, ADF tarafından yönetilen KIMLIĞI Azure SQL yönetilen örneğiniz için erişim izni verin.

                - **Hizmet sorumlusu**' nı seçerseniz, ilgili **hizmet sorumlusu kimliği** ve **hizmet sorumlusu anahtarını** girin veya gizli dizi olarak depolandığı **Azure Key Vault** seçin.

      1. **Dosya sistemi**' ni seçerseniz, paketlerinizin **konak** için dağıtıldığı klasörün UNC yolunu ve Ilgili **Kullanıcı adı** ve **parolayı** girin veya gizli dizi olarak depolandığı **Azure Key Vault** seçin.

      1. Uygun olduğunda **test bağlantısı** ' nı seçin ve başarılı olursa **Oluştur**' u seçin.

   1. Eklenmiş paket depolarınız **dağıtım ayarları** sayfasında görüntülenir. Bunları kaldırmak için onay kutularını işaretleyin ve ardından **Sil**' i seçin.

Uygun olduğunda **Bağlantıyı Sına** ' yı seçin ve başarılı olursa **devam**' ı seçin.

#### <a name="advanced-settings-page"></a>Gelişmiş ayarlar sayfası

**Tümleştirme çalışma zamanı kurulum** bölmesinin **Gelişmiş ayarlar** sayfasında aşağıdaki adımları izleyin.

   ![Gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. **Düğüm başına en fazla paralel yürütme** için, tümleştirme çalışma zamanı kümenizde düğüm başına aynı anda çalıştırılacak en fazla paket sayısını seçin. Yalnızca desteklenen paket sayıları görüntülenir. İşlem veya bellek kullanımı yoğun olan tek bir büyük paketi çalıştırmak için birden fazla çekirdek kullanmak istiyorsanız düşük bir sayı seçin. Tek bir çekirdekli bir veya daha fazla küçük paket çalıştırmak istiyorsanız, yüksek bir sayı seçin.

   1. Azure-SSIS IR Standard/Express özel kurulumları eklemek isteyip istemediğinizi seçmek için **Azure-SSIS Integration Runtime ek sistem yapılandırması/bileşen yüklemeleri Ile özelleştirin** onay kutusunu seçin. Daha fazla bilgi için bkz. [özel kurulum Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

      Onay kutusunu seçerseniz, aşağıdaki adımları uygulayın.

      ![Özel kurulumlarla Gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. **Özel kurulum KAPSAYıCıSı SAS URI 'si** için, komut dosyalarını ve standart özel kurulumlarda ilişkili dosyaları DEPOLADıĞıNıZ KAPSAYıCıNıN SAS URI 'sini girin.

      1. **Hızlı** özel kurulum için **Yeni** ' yi seçerek **hızlı özel kurulum ekle** paneli ' ni açın ve ardından **hızlı özel kurulum türü** açılan menüsü altında herhangi bir tür seçin, örneğin, **cmdkey komutunu çalıştırın**, **ortam değişkeni ekleyin**, **lisanslı bileşeni yükleme** vb.

         **Lisanslı bileşen türünü yükle** ' yi seçerseniz, **bileşen adı** açılır menüsü altında ISV iş ortaklarımızdan herhangi bir tümleşik bileşeni seçebilir ve gerekirse ürün lisans anahtarını girebilir/satın aldığınız ürün lisans dosyasını **Lisans anahtarı** / **Lisans dosyası** kutusuna yükleyebilirsiniz.
  
         Eklediğiniz Express özel kurulumları **Gelişmiş ayarlar** sayfasında görünür. Bunları kaldırmak için onay kutularını seçip **Sil**' i seçebilirsiniz.

   1. **Azure-SSIS Integration Runtime katılması için bir VNET seçin, ADF 'nin belirli ağ kaynaklarını oluşturmasına izin verin ve isteğe bağlı olarak kendi statik ortak IP adreslerinizi getirin** onay kutusunu seçerek tümleştirme çalışma zamanı 'nı bir sanal ağa katmak isteyip istemediğinizi seçin. 

      IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları ile bir Azure SQL veritabanı sunucusu veya SSSıSDB barındırmak için özel uç nokta ile yönetilen bir örnek kullanıyorsanız veya şirket içi verilere erişim istiyorsanız (yani, SSIS paketinizdeki şirket içi veri kaynaklarınız veya hedefler varsa, kendi kendine barındırılan bir IR yapılandırmadan) bunu seçin. Daha fazla bilgi için bkz. [Azure-SSIS IR bir sanal ağa ekleme](./join-azure-ssis-integration-runtime-virtual-network.md). 

      Onay kutusunu seçerseniz, aşağıdaki adımları uygulayın.

      ![Bir sanal ağda gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. **Abonelik** için sanal ağınıza sahip Azure aboneliğini seçin.

      1. **Konum** için, tümleştirme çalışma zamanından aynı konum seçilidir.

      1. **Tür** için, sanal ağınızın türünü seçin: klasik veya Azure Resource Manager. Klasik sanal ağların yakında kullanım dışı olacağı için bir Azure Resource Manager sanal ağı seçmenizi öneririz.

      1. **VNET adı** için sanal ağınızın adını seçin. Sanal ağ hizmet uç noktaları veya yönetilen örnek ile birlikte, SSıSDB 'yi barındırmak için özel uç nokta ile Azure SQL veritabanı sunucunuz için kullanılan bir tane olmalıdır. Ya da şirket içi ağınıza bağlı bir aynı olmalıdır. Aksi takdirde, Azure-SSIS IR için kendi statik genel IP adreslerinizi getirmek için herhangi bir sanal ağ olabilir.

      1. **Alt ağ adı** için, sanal ağınız için alt ağ adını seçin. SSıSDB barındırmak için sanal ağ hizmet uç noktaları ile Azure SQL veritabanı sunucunuz için kullanılan bir tane olmalıdır. Veya SSıSDB 'yi barındırmak için özel uç nokta ile yönetilen örneğiniz için kullanılan farklı bir alt ağ olmalıdır. Aksi takdirde, Azure-SSIS IR için kendi statik genel IP adreslerinizi getirmek için herhangi bir alt ağ olabilir.

      1. Azure-SSIS IR için kendi statik genel IP adreslerini getirmek isteyip istemediğinizi seçmek üzere **Azure-SSIS Integration Runtime için statik genel IP adreslerini getir** onay kutusunu seçin. bu sayede, veri kaynaklarınıza yönelik güvenlik duvarında bunlara izin verebilirsiniz.

         Onay kutusunu seçerseniz, aşağıdaki adımları uygulayın.

         1. **İlk statik genel IP adresi** için Azure-SSIS IR gereksinimlerini karşılayan ilk STATIK genel IP adresini seçin. Herhangi bir hesabınız yoksa, yeni bağlantı **Oluştur** ' a tıklayarak Azure Portal STATIK genel IP adresleri oluşturun ve ardından buradan Yenile düğmesine tıklayın, böylece bunları seçebilirsiniz.
      
         1. **İkinci statik genel IP adresi** için Azure-SSIS IR gereksinimlerini karşılayan ikinci STATIK genel IP adresini seçin. Herhangi bir hesabınız yoksa, yeni bağlantı **Oluştur** ' a tıklayarak Azure Portal STATIK genel IP adresleri oluşturun ve ardından buradan Yenile düğmesine tıklayın, böylece bunları seçebilirsiniz.

   1. Azure-SSIS IR için ara sunucu olarak şirket içinde barındırılan bir IR yapılandırmak isteyip istemediğinizi seçmek için Azure-SSIS Integration Runtime onay kutusunu **bir ara sunucu olarak ayarla Self-Hosted Integration Runtime** seçin. Daha fazla bilgi için bkz. [proxy olarak şirket içinde barındırılan BIR IR ayarlama](./self-hosted-integration-runtime-proxy-ssis.md). 

      Onay kutusunu seçerseniz, aşağıdaki adımları uygulayın.

      ![Şirket içinde barındırılan IR ile gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. **Şirket Içinde barındırılan Integration Runtime**, Azure-SSIS IR için mevcut şirket IÇINDE barındırılan IR 'yi bir ara sunucu olarak seçin.

      1. **Hazırlama depolama bağlı hizmeti** Için mevcut Azure Blob depolama bağlı hizmetinizi seçin veya hazırlama için yeni bir tane oluşturun.

      1. **Hazırlama yolu** için, seçili Azure Blob depolama hesabınızda bir blob kapsayıcısı belirtin veya hazırlama için varsayılan bir tane kullanmak üzere boş bırakın.

   1. **VNET doğrulaması**  >  **devam et**' i seçin. 

**Özet** bölümünde, tüm sağlama ayarlarını gözden geçirin, önerilen belge bağlantılarına yer işareti ekleyin ve tümleştirme çalışma zamanının oluşturulmasını başlatmak için **son** ' u seçin.

   > [!NOTE]
   > Özel kurulum süresini dışlayarak, bu işlem 5 dakika içinde tamamlanır. Ancak Azure-SSIS IR bir sanal ağa katılması 20-30 dakika sürebilir.
   >
   > SSSıSDB kullanıyorsanız, Data Factory hizmeti SSıSDB 'yi hazırlamak için veritabanı sunucunuza bağlanır. Ayrıca, belirtilmişse sanal ağınız için izinleri ve ayarları yapılandırır ve Azure-SSIS IR sanal ağa birleştirir.
   > 
   > Bir Azure-SSIS IR sağladığınızda, SSIS için yeniden dağıtılabilir ve Azure Özellik paketi de yüklenir. Bu bileşenler, yerleşik bileşenlerin zaten desteklediği veri kaynaklarına ek olarak Excel dosyaları, erişim dosyaları ve çeşitli Azure veri kaynaklarına bağlantı sağlar. Yerleşik/önceden yüklenmiş bileşenler hakkında daha fazla bilgi için, bkz. [Azure-SSIS IR yerleşik/önceden yüklenmiş bileşenler](./built-in-preinstalled-components-ssis-integration-runtime.md). Yükleyebileceğiniz ek bileşenler hakkında daha fazla bilgi için bkz. [özel ayarlar Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

#### <a name="connections-pane"></a>Bağlantılar bölmesi

Hub 'ı **Yönet** ' in **Bağlantılar** bölmesinde, **tümleştirme çalışma zamanları** sayfasına geçin ve **Yenile**' yi seçin. 

   ![Bağlantılar bölmesi](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Azure-SSIS IR adını seçerek düzenleyebilir/yeniden yapılandırabilirsiniz. Ayrıca, Azure-SSIS IR izlemek/başlatmak/durdurmak/silmek için ilgili düğmeleri seçebilir, Azure-SSIS IR üzerinde çalışacak SSIS paketi yürütme etkinliğinin bulunduğu bir ADF işlem hattını otomatik olarak oluşturabilir ve Azure-SSIS IR JSON kodunu/yükünü görüntüleyebilirsiniz.  Azure-SSIS IR düzenlemeniz/silmeniz, yalnızca durdurulduğunda yapılabilir.

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Portalda Azure SSIS tümleştirmesi çalışma zamanları

1. Azure Data Factory Kullanıcı arabiriminde, **Yönet** sekmesine geçin ve sonra veri fabrikanızdaki mevcut tümleştirme çalışma zamanlarını görüntülemek için **Bağlantılar** bölmesindeki **tümleştirme çalışma zamanları** sekmesine geçin.

   ![Mevcut IR’leri görüntüle](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Yeni bir Azure-SSIS IR oluşturmak ve **tümleştirme çalışma zamanı kurulum** bölmesini açmak için **Yeni** ' yi seçin.

   ![Menü aracılığıyla tümleştirme çalışma zamanı](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. **Tümleştirme çalışma zamanı kurulumu** bölmesinde, Azure kutucuğunda **yürütmek üzere var olan SSIS paketlerini** seçin ve ardından **devam**' ı seçin.

   ![Tümleştirme çalışma zamanının türünü belirtin](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Azure-SSIS IR ayarlamak için kalan adımlar için bkz. [Azure SSIS tümleştirme çalışma zamanı sağlama](#provision-an-azure-ssis-integration-runtime) bölümü.

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Tümleştirme çalışma zamanı oluşturmak için Azure PowerShell kullanma

Bu bölümde, bir Azure-SSIS IR oluşturmak için Azure PowerShell kullanırsınız.

### <a name="create-variables"></a>Değişken oluşturma

Aşağıdaki betiği kopyalayıp yapıştırın. Değişkenlerin değerlerini belirtin. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Oturum açın ve bir abonelik seçin

Oturum açmak ve Azure aboneliğinizi seçmek için aşağıdaki betiği ekleyin.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Veritabanı sunucusuyla bağlantıyı doğrulama

Azure SQL veritabanı sunucunuzu veya yönetilen örneğinizi doğrulamak için aşağıdaki betiği ekleyin.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Sanal ağı yapılandırma

Azure-SSIS tümleştirme çalışma zamanının katılması için sanal ağ izinlerini ve ayarlarını otomatik olarak yapılandırmak üzere aşağıdaki betiği ekleyin.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu kullanarak bir [Azure Kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Kaynak grubunuz zaten varsa, bu kodu betiğinize kopyalamayın. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Veri fabrikası oluşturmak için aşağıdaki komutu çalıştırın.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Tümleştirme çalışma zamanı oluşturma

Azure 'da SSIS paketlerini çalıştıran bir Azure-SSIS tümleştirme çalışma zamanı oluşturmak için aşağıdaki komutları çalıştırın.

SSSıSDB kullanmıyorsanız, `CatalogServerEndpoint` `CatalogPricingTier` ve `CatalogAdminCredential` parametrelerini atlayabilirsiniz.

IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları içeren bir Azure SQL veritabanı sunucusu veya SSSıSDB barındırmak ya da şirket içi verilere erişim gerektirmek için özel uç nokta içeren bir yönetilen örnek kullanmıyorsanız, `VNetId` ve `Subnet` parametrelerini atlayabilir veya boş değerleri geçirebilirsiniz. Ayrıca, şirket içi verilere erişmek üzere Azure-SSIS IR için ara sunucu olarak otomatik olarak barındırılan bir IR yapılandırırsanız bunları atlayabilirsiniz. Aksi takdirde, bunları yok saylayamazsınız ve sanal ağ yapılandırmasından geçerli değerler geçirmeniz gerekir. Daha fazla bilgi için bkz. bir [Azure-SSIS IR sanal ağa ekleme](./join-azure-ssis-integration-runtime-virtual-network.md).

SSıSDB 'yi barındırmak için yönetilen örnek kullanırsanız, `CatalogPricingTier` parametreyi atlayabilir veya boş bir değer geçirebilirsiniz. Aksi takdirde, bunu atlayamazsınız ve Azure SQL veritabanı için desteklenen fiyatlandırma katmanları listesinden geçerli bir değer geçirmeniz gerekir. Daha fazla bilgi için bkz. [SQL veritabanı kaynak sınırları](../azure-sql/database/resource-limits-logical-server.md).

Veritabanı sunucusuna bağlanmak için veri fabrikanızın yönetilen kimliğiyle Azure AD kimlik doğrulaması kullanıyorsanız, `CatalogAdminCredential` parametreyi atlayabilirsiniz. Ancak, veri fabrikanızın yönetilen kimliğini veritabanı sunucusuna erişim izinleri olan bir Azure AD grubuna eklemeniz gerekir. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasını bir Azure-SSIS IR Için etkinleştirme](./enable-aad-authentication-azure-ssis-ir.md). Aksi takdirde, bunu atlayamazsınız ve SQL kimlik doğrulaması için Sunucu Yöneticisi Kullanıcı adı ve parolanınızdan oluşturulmuş geçerli bir nesne geçirmeniz gerekir.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

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

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>Tümleştirme çalışma zamanını başlatma

Azure-SSIS tümleştirme çalışma zamanını başlatmak için aşağıdaki komutları çalıştırın.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Özel kurulum süresini dışlayarak, bu işlem 5 dakika içinde tamamlanır. Ancak Azure-SSIS IR bir sanal ağa katılması 20-30 dakika sürebilir.
>
> SSSıSDB kullanıyorsanız, Data Factory hizmeti SSıSDB 'yi hazırlamak için veritabanı sunucunuza bağlanır. Ayrıca, belirtilmişse sanal ağınız için izinleri ve ayarları yapılandırır ve Azure-SSIS IR sanal ağa birleştirir.
> 
> Bir Azure-SSIS IR sağladığınızda, SSIS için yeniden dağıtılabilir ve Azure Özellik paketi de yüklenir. Bu bileşenler, yerleşik bileşenlerin zaten desteklediği veri kaynaklarına ek olarak Excel dosyaları, erişim dosyaları ve çeşitli Azure veri kaynaklarına bağlantı sağlar. Yerleşik/önceden yüklenmiş bileşenler hakkında daha fazla bilgi için, bkz. [Azure-SSIS IR yerleşik/önceden yüklenmiş bileşenler](./built-in-preinstalled-components-ssis-integration-runtime.md). Yükleyebileceğiniz ek bileşenler hakkında daha fazla bilgi için bkz. [özel ayarlar Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Tam betik

Azure-SSIS tümleştirme çalışma zamanı oluşturan tam betik aşağıda verilmiştir.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

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

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Tümleştirme çalışma zamanı oluşturmak için Azure Resource Manager şablonu kullanma

Bu bölümde, Azure-SSIS tümleştirme çalışma zamanı oluşturmak için bir Azure Resource Manager şablonu kullanırsınız. Örnek bir anlatım aşağıda verilmiştir:

1. Aşağıdaki Azure Resource Manager şablonuyla bir JSON dosyası oluşturun. Açılı ayraç (yer tutucular) değerlerini kendi değerlerinizle değiştirin.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Azure Resource Manager şablonunu dağıtmak için `New-AzResourceGroupDeployment` Aşağıdaki örnekte gösterildiği gibi komutunu çalıştırın. Örnekte, `ADFTutorialResourceGroup` kaynak grubunuzun adıdır. `ADFTutorialARM.json` , veri fabrikanızın JSON tanımını ve Azure-SSIS IR içeren dosyadır.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Bu komut, veri fabrikanızı oluşturur ve içinde Azure-SSIS IR, ancak IR 'yi başlatmaz.

3. Azure-SSIS IR başlatmak için şu `Start-AzDataFactoryV2IntegrationRuntime` komutu çalıştırın:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Özel kurulum süresini dışlayarak, bu işlem 5 dakika içinde tamamlanır. Ancak Azure-SSIS IR bir sanal ağa katılması 20-30 dakika sürebilir.
>
> SSSıSDB kullanıyorsanız, Data Factory hizmeti SSıSDB 'yi hazırlamak için veritabanı sunucunuza bağlanır. Ayrıca, belirtilmişse sanal ağınız için izinleri ve ayarları yapılandırır ve Azure-SSIS IR sanal ağa birleştirir.
> 
> Bir Azure-SSIS IR sağladığınızda, SSIS için yeniden dağıtılabilir ve Azure Özellik paketi de yüklenir. Bu bileşenler, yerleşik bileşenlerin zaten desteklediği veri kaynaklarına ek olarak Excel dosyaları, erişim dosyaları ve çeşitli Azure veri kaynaklarına bağlantı sağlar. Yerleşik/önceden yüklenmiş bileşenler hakkında daha fazla bilgi için, bkz. [Azure-SSIS IR yerleşik/önceden yüklenmiş bileşenler](./built-in-preinstalled-components-ssis-integration-runtime.md). Yükleyebileceğiniz ek bileşenler hakkında daha fazla bilgi için bkz. [özel ayarlar Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>SSIS paketlerini dağıtma

SSSıSDB kullanıyorsanız, paketlerinizi bu pakete dağıtabilir ve Azure özellikli SSDT veya SSMS araçlarını kullanarak Azure-SSIS IR çalıştırabilirsiniz. Bu araçlar, veritabanı sunucunuza sunucu uç noktası aracılığıyla bağlanır: 

- Azure SQL veritabanı sunucusu için sunucu uç noktası biçimi olur `<server name>.database.windows.net` .
- Özel uç noktası olan yönetilen bir örnek için sunucu uç noktası biçimi olur `<server name>.<dns prefix>.database.windows.net` .
- Ortak uç nokta içeren bir yönetilen örnek için sunucu uç noktası biçimi olur `<server name>.public.<dns prefix>.database.windows.net,3342` . 

SSSıSDB kullanmıyorsanız, paketlerinizi Azure SQL yönetilen örneğiniz tarafından barındırılan dosya sistemine, Azure dosyalarına veya MSDB 'ye dağıtabilir ve [dtutil](/sql/integration-services/dtutil-utility) ve [Azuredtexec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) komut satırı yardımcı programlarını kullanarak Azure-SSIS IR çalıştırabilirsiniz. 

Daha fazla bilgi için bkz. [SSIS projelerini/paketlerini dağıtma](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

Her iki durumda da, Data Factory işlem hatlarında SSIS paketi yürütme etkinliğini kullanarak dağıtılmış paketlerinizi Azure-SSIS IR de çalıştırabilirsiniz. Daha fazla bilgi için bkz. [birinci sınıf Data Factory etkinliği olarak SSIS paketi yürütmeyi çağırma](./how-to-invoke-ssis-package-ssis-activity.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu belgelerde diğer Azure-SSIS IR konularına bakın:

- [Azure-SSIS tümleştirme çalışma zamanı](concepts-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede, Azure-SSIS IR dahil olmak üzere genel olarak tümleştirme çalışma zamanları hakkında bilgi sağlanır.
- [Azure-SSIS IR’yi izleme](monitor-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede, Azure-SSIS IR hakkındaki bilgileri nasıl alacağınızı ve anlayacağınızı görebilirsiniz.
- [Azure-SSIS IR’yi yönetme](manage-azure-ssis-integration-runtime.md). Bu makalede Azure-SSIS IR nasıl durdurulacağı, başlatılacağı veya silineceği gösterilmektedir. Ayrıca, daha fazla düğüm ekleyerek Azure-SSIS IR nasıl ölçeklenekullanacağınızı gösterir.
- [Azure 'da SSIS paketlerini dağıtma, çalıştırma ve izleme](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure 'da SSıSDB 'ye bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Windows kimlik doğrulaması ile şirket içi veri kaynaklarına bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Azure 'da paket yürütmelerini zamanlama](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)