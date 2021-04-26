---
title: Azure-SSIS tümleştirme çalışma zamanını sağlama
description: Azure'da SSIS paketleri dağıtıp çalıştırabilmek için Azure Data Factory'de Azure-SSIS tümleştirme çalışma zamanı sağlamayı öğrenin.
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/02/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 6007ce4b4c54d795ff2cc3188504db11c29219cc
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256421"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory 'de Azure-SSIS tümleştirme çalışma zamanını sağlama

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu öğreticide, Azure Data Factory (ADF) içinde bir Azure-SQL Server Integration Services (SSIS) tümleştirme çalışma zamanı (IR) sağlamak için Azure portal kullanma adımları sunulmaktadır. Bir Azure-SSIS IR şunları destekler:

- Azure SQL veritabanı sunucusu/yönetilen örneği (proje dağıtım modeli) tarafından barındırılan SSIS kataloğuna (SSıSDB) dağıtılan Paketleri çalıştırma
- Azure SQL yönetilen örneği (paket dağıtım modeli) tarafından barındırılan dosya sistemine, Azure dosyalarına veya SQL Server veritabanına (MSDB) dağıtılan Paketleri çalıştırma

Bir Azure-SSIS IR sağlandıktan sonra, Azure 'da paketlerinizi dağıtmak ve çalıştırmak için tanıdık araçları kullanabilirsiniz. Bu araçlar zaten Azure özellikli ve [dtutil](/sql/integration-services/dtutil-utility) ve [Azuredtexec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md)gibi SQL Server veri araçları (ssdt), SQL Server Management Studio (SSMS) ve komut satırı yardımcı programlarını içerir.

Azure-SSIS IR’ler hakkında kavramsal bilgiler için bkz. [Azure SSIS tümleştirme çalışma zamanına genel bakış](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Bu öğreticide aşağıdaki adımları tamamlarsınız:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Azure-SSIS tümleştirme çalışma zamanı sağlama.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

- **Azure SQL veritabanı sunucusu (isteğe bağlı)**. Henüz bir veritabanı sunucunuz yoksa, başlamadan önce Azure portalında bir tane oluşturun. Data Factory, bu veritabanı sunucusunda SSıSDB örneği oluşturacak. 

  Veritabanı sunucusunu tümleştirme çalışma zamanı ile aynı Azure bölgesinde oluşturmanız önerilir. Bu yapılandırma, tümleştirme çalışma zamanının yürütme günlüklerini Azure bölgeleriyle çıkmadan SSSıSDB 'ye yazmasını sağlar.

  Şu noktaları göz önünde bulundurun:

  - Seçili veritabanı sunucusuna bağlı olarak, SSıSDB örneği, elastik havuzun bir parçası olarak veya yönetilen bir örnekte sizin adınıza tek bir veritabanı olarak oluşturulabilir. Bu, genel bir ağda veya bir sanal ağa katılarak erişilebilir. SSıSDB barındıracak veritabanı sunucusu türünü seçme konusunda rehberlik için bkz. [SQL veritabanı ve SQL yönetilen örneği karşılaştırması](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance). 
  
    IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları içeren bir Azure SQL veritabanı sunucusu veya SSıSDB barındırmak için özel uç nokta ile yönetilen bir örnek kullanıyorsanız veya şirket içinde barındırılan bir IR yapılandırmadan şirket içi verilere erişmeniz gerekiyorsa, Azure-SSIS IR bir sanal ağa katılmanız gerekir. Daha fazla bilgi için bkz. [Sanal ağda Azure-SSIS IR oluşturma](./create-azure-ssis-integration-runtime.md).

  - Veritabanı sunucusu için **Azure hizmetlerine erişime Izin ver** ayarının etkin olduğunu doğrulayın. Bu ayar, IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları içeren bir Azure SQL veritabanı sunucusu veya SSSıSDB barındırmak için özel uç nokta olan yönetilen bir örnek kullandığınızda geçerli değildir. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nı güvenli hale getirme](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Bu ayarı PowerShell kullanarak etkinleştirmek için, bkz. [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - İstemci makinenin IP adresini veya istemci makinenin IP adresini içeren bir IP adresi aralığını, veritabanı sunucusunun güvenlik duvarı ayarlarındaki istemci IP adresi listesine ekleyin. Daha fazla bilgi için bkz. [Azure SQL Veritabanı'nda sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı kuralları yapılandırma](../azure-sql/database/firewall-configure.md).

  - Sunucu Yöneticisi kimlik bilgilerinizle SQL kimlik doğrulaması kullanarak veya veri fabrikanızın yönetilen kimliğiyle Azure AD kimlik doğrulamasını kullanarak, veritabanı sunucusuna bağlanabilirsiniz. İkincisi için, veri fabrikanızın yönetilen kimliğini, veritabanı sunucusuna erişim izinleri olan bir Azure AD grubuna eklemeniz gerekir. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure-SSIS IR oluşturma](./create-azure-ssis-integration-runtime.md).

  - Veritabanı sunucunuzun zaten bir SSıSDB örneğine sahip olmadığından emin olun. Azure-SSIS IR sağlanması, var olan bir SSıSDB örneğinin kullanımını desteklemez.

> [!NOTE]
> Data Factory ve Azure-SSIS IR Şu anda kullanılabildiği Azure bölgelerinin listesi için bkz. [bölgeye göre Data Factory ve SSIS IR kullanılabilirliği](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Veri fabrikanızı Azure portal aracılığıyla oluşturmak için, [Kullanıcı arabirimi aracılığıyla veri fabrikası oluşturma](./quickstart-create-data-factory-portal.md#create-a-data-factory)' daki adım adım yönergeleri izleyin. Oluşturulduktan sonra hızlı erişime izin vermek için, bunu yaparken **panoya sabitle** ' yi seçin. 

Data Factory oluşturulduktan sonra, Azure portal genel bakış sayfasını açın. **Başlangıç** sayfasını ayrı bir sekmede açmak için **Yazar & izleyici** kutucuğunu seçin. Burada, Azure-SSIS IR oluşturmaya devam edebilirsiniz.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS tümleştirme çalışma zamanı oluşturma

### <a name="from-the-data-factory-overview"></a>Data Factory genel bakış

1. **Haydi başlayalım** SAYFASıNDA, **SSIS tümleştirmesini Yapılandır** kutucuğunu seçin. 

   !["SSIS Tümleştirme Çalışma Zamanı Yapılandır" kutucuğu](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Azure SSIS IR kurulumunun diğer adımları için [Azure SSIS tümleştirme çalışma zamanı sağlama](#provision-an-azure-ssis-integration-runtime) bölümüne bakın. 

### <a name="from-the-authoring-ui"></a>Yazma kullanıcı arabiriminden

1. Azure Data Factory Kullanıcı arabiriminde, **Yönet** sekmesine geçin ve ardından veri fabrikanızdaki mevcut tümleştirme çalışma zamanlarını görüntülemek için **tümleştirme çalışma zamanları** sekmesine geçin. 

   ![Var olan IR’leri görüntüleme seçimleri](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Azure-SSIS IR oluşturmak için **Yeni** ' yi seçin ve **Integration Runtime kurulum** bölmesini açın. 

   ![Menü aracılığıyla tümleştirme çalışma zamanı](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. **Tümleştirme çalışma zamanı kurulumu** bölmesinde, Azure kutucuğunda **yürütmek üzere var olan SSIS paketlerini** seçin ve ardından **devam**' ı seçin.

   ![Tümleştirme çalışma zamanının türünü belirtin](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Azure SSIS IR kurulumunun diğer adımları için [Azure SSIS tümleştirme çalışma zamanı sağlama](#provision-an-azure-ssis-integration-runtime) bölümüne bakın. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS tümleştirme çalışma zamanı sağlama

**Integration Runtime kurulum** bölmesinde, genel, dağıtım ve gelişmiş ayarları büyük ölçüde yapılandırdığınız üç sayfa bulunur.

### <a name="general-settings-page"></a>Genel ayarlar sayfası

**Tümleştirme çalışma zamanı kurulum** bölmesinin **Genel ayarlar** sayfasında aşağıdaki adımları izleyin. 

   ![Genel ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. **Name** (Ad) alanına tümleştirme çalışma zamanınızın adını girin. 

   1. **Description** (Açıklama) alanına tümleştirme çalışma zamanınız için bir açıklama girin. 

   1. **Location** (Konum)alanında tümleştirme çalışma zamanınızın konumunu seçin. Yalnızca desteklenen konumlar görüntülenir. SSISDB'yi barındırmak için veritabanı sunucunuz ile aynı konumu seçmenizi öneririz. 

   1. **Node Size** (Düğüm Boyutu) alanında tümleştirme çalışma zamanınızdaki düğümün boyutunu seçin. Yalnızca desteklenen düğüm boyutları görüntülenir. Çok yoğun işlem yoğunluğu veya bellek kullanımı yoğun paketler çalıştırmak istiyorsanız büyük bir düğüm boyutu seçin (ölçeği büyütme). 

   1. **Node Number** (Düğüm Sayısı) alanında tümleştirme çalışma zamanınızdaki düğüm sayısını seçin. Yalnızca desteklenen düğüm sayıları görüntülenir. Birçok paketi paralel olarak çalıştırmak istiyorsanız çok sayıda düğümü olan büyük bir küme seçin (ölçeği genişletme). 

   1. **Sürüm/lisans** için, tümleştirme çalışma zamanı için SQL Server sürümünü seçin: Standart veya kurumsal. Tümleştirme çalışma zamanı ' nda Gelişmiş özellikleri kullanmak istiyorsanız Kurumsal ' i seçin. 

   1. Tasarruf **için,** tümleştirme çalışma zamanı için Azure hibrit avantajı seçeneğini belirleyin: **Evet** veya **Hayır**. Karma kullanım ile maliyet tasarruflarından faydalanmak için kendi SQL Server lisansınızı Yazılım Güvencesine getirmek istiyorsanız **Evet** ' i seçin. 

   1. **Devam**’ı seçin. 

### <a name="deployment-settings-page"></a>Dağıtım ayarları sayfası

**Tümleştirme çalışma zamanı kurulum** bölmesinin **dağıtım ayarları** sayfasında, SSISDB ve veya Azure-SSIS IR paket depoları oluşturma seçeneklerine sahip olursunuz.

#### <a name="creating-ssisdb"></a>SSSıSDB oluşturma

**Tümleştirme çalışma zamanı kurulum** bölmesinin **dağıtım ayarları** SAYFASıNDA, paketlerinizi Sssısdb 'ye (proje dağıtım modeli) dağıtmak istiyorsanız, **projelerinizi/paketleri/ortamları/yürütme GÜNLÜKLERINIZI depolamak için Azure SQL veritabanı sunucusu/yönetilen örneği tarafından barındırılan SSIS kataloğunu (sssısdb) oluştur** onay kutusunu seçin. Alternatif olarak, paketlerinizi Azure SQL yönetilen örneği (paket dağıtım modeli) tarafından barındırılan dosya sistemine, Azure dosyalarına veya SQL Server veritabanına (MSDB) dağıtmak istiyorsanız, SSıSDB oluşturma veya onay kutusunu seçme gereksinimi yoktur.

Dağıtım modelinize bakılmaksızın, Azure SQL yönetilen örneği tarafından barındırılan SQL Server Agent paket yürütmelerini düzenlemek ve zamanlamak için kullanmak istiyorsanız, SSıSDB tarafından etkinleştirilir, bu nedenle yine de onay kutusunu seçin. Daha fazla bilgi için bkz. [Azure SQL yönetilen örnek Aracısı aracılığıyla SSIS paket yürütmelerini zamanlama](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Onay kutusunu seçerseniz, kendi adınıza oluşturacağınız ve yönetecağımız SSıSDB 'yi barındırmak için kendi veritabanı sunucunuzu getirmek üzere aşağıdaki adımları izleyin.

   ![SSSıSDB dağıtım ayarları](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. **Subscription** (Abonelik) alanında SSISDB'yi barındıran Azure aboneliğini seçin. 

   1. **Location** (Konum) alanında SSISDB'yi barındıran veritabanı sunucunuzun konumunu seçin. Tümleştirme çalışma zamanınızla aynı konumu seçmenizi öneririz.

   1. **Catalog Database Server Endpoint** (Katalog Veritabanı Sunucusu Uç Noktası) alanında SSISDB'yi barındıracak veritabanı sunucunuzun uç noktasını seçin. 
   
      Seçili veritabanı sunucusuna bağlı olarak, SSıSDB örneği, elastik havuzun bir parçası olarak veya yönetilen bir örnekte sizin adınıza tek bir veritabanı olarak oluşturulabilir. Bu, genel bir ağda veya bir sanal ağa katılarak erişilebilir. SSıSDB barındıracak veritabanı sunucusu türünü seçme konusunda rehberlik için bkz. [SQL veritabanı ve SQL yönetilen örneği karşılaştırması](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      SSD 'yi barındırmak için IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları veya özel uç nokta ile yönetilen bir örnek içeren bir Azure SQL veritabanı sunucusu seçerseniz veya şirket içinde barındırılan bir IR yapılandırmadan şirket içi verilere erişmeniz gerekiyorsa, Azure-SSIS IR bir sanal ağa katılmanız gerekir. Daha fazla bilgi için bkz. [Sanal ağda Azure-SSIS IR oluşturma](./create-azure-ssis-integration-runtime.md).

   1. Veritabanı sunucunuzun SSıSDB 'yi barındıracak kimlik doğrulama yöntemini seçmek için, **ADF 'niz için yönetilen kimlikle Azure AD kimlik doğrulamasını kullan** onay kutusunu seçin. Veri fabrikanızın yönetilen kimliğiyle SQL kimlik doğrulaması ya da Azure AD kimlik doğrulaması ' nı seçersiniz.

      Onay kutusunu seçerseniz, veritabanı sunucunuza erişim izinleri olan bir Azure AD grubuna veri fabrikanızın yönetilen kimliğini eklemeniz gerekir. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure-SSIS IR oluşturma](./create-azure-ssis-integration-runtime.md).
   
   1. **Yönetici Kullanıcı adı** IÇIN, SSISDB barındırmak üzere VERITABANı sunucunuzun SQL kimlik doğrulaması Kullanıcı adı ' nı girin. 

   1. **Yönetici parolası** IÇIN, SSISDB barındırmak üzere VERITABANı sunucunuzun SQL kimlik doğrulama parolasını girin. 

   1. İş sürekliliği ve olağanüstü durum kurtarma (BCDR) için Azure SQL veritabanı/yönetilen örnek yük devretme grubuyla eşitlenmiş olarak çalıştırılan bir çift hazır bekleyen Azure SSIS IR çifti yapılandırmak için **SSSıSDB yük devretmesi ile çift bekleme Azure-SSIS Integration Runtime çiftini kullanın** onay kutusunu seçin.
   
      Onay kutusunu seçerseniz, **çift bekleme çifti adı** metin kutusunda birincil ve ikincil Azure-SSIS IRS çiftini tanımlayacak bir ad girin. Birincil ve ikincil Azure-SSIS IRS 'nizi oluştururken aynı çift adı girmeniz gerekir.

      Daha fazla bilgi için bkz. [BCDR için Azure-SSIS IR yapılandırma](./configure-bcdr-azure-ssis-integration-runtime.md).

   1. **Katalog veritabanı hizmet katmanı** IÇIN, SSISDB barındıracak veritabanı sunucunuzun hizmet katmanını seçin. Temel, standart veya Premium katmanını seçin veya elastik havuz adı seçin.

Uygun olduğunda **Bağlantıyı Sına** ' yı seçin ve başarılı olursa **devam**' ı seçin.

#### <a name="creating-azure-ssis-ir-package-stores"></a>Azure-SSIS IR paket depoları oluşturma

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

### <a name="advanced-settings-page"></a>Gelişmiş ayarlar sayfası

**Tümleştirme çalışma zamanı kurulum** bölmesinin **Gelişmiş ayarlar** sayfasında aşağıdaki adımları izleyin. 

   ![Gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. **Düğüm başına en fazla paralel yürütme** için, tümleştirme çalışma zamanı kümenizde düğüm başına aynı anda çalıştırılacak en fazla paket sayısını seçin. Yalnızca desteklenen paket sayıları görüntülenir. İşlem veya bellek kullanımı yoğun olan tek bir büyük paketi çalıştırmak için birden fazla çekirdek kullanmak istiyorsanız düşük bir sayı seçin. Tek bir çekirdekli bir veya daha fazla küçük paket çalıştırmak istiyorsanız, yüksek bir sayı seçin. 

   1. Azure-SSIS IR Standard/Express özel kurulumları eklemek isteyip istemediğinizi seçmek için **Azure-SSIS Integration Runtime ek sistem yapılandırması/bileşen yüklemeleri Ile özelleştirin** onay kutusunu seçin. Daha fazla bilgi için bkz. [özel kurulum Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).
   
   1. **Azure-SSIS Integration Runtime katılması için bir VNET seçin, ADF 'nin belirli ağ kaynaklarını oluşturmasına izin verin ve isteğe bağlı olarak kendi statik ortak IP adreslerinizi getirin** onay kutusunu seçerek Azure-SSIS IR bir sanal ağa katmak isteyip istemediğinizi seçin.

      IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları ile bir Azure SQL veritabanı sunucusu veya SSıSDB barındırmak için özel uç nokta ile yönetilen bir örnek kullanıyorsanız veya şirket içinde barındırılan bir IR yapılandırmadan şirket içi verilere erişim istiyorsanız bu seçeneği belirleyin. Daha fazla bilgi için bkz. [Sanal ağda Azure-SSIS IR oluşturma](./create-azure-ssis-integration-runtime.md). 
   
   1. Azure-SSIS IR için ara sunucu olarak şirket içinde barındırılan bir IR yapılandırmak isteyip istemediğinizi seçmek için Azure-SSIS Integration Runtime onay kutusunu **bir ara sunucu olarak ayarla Self-Hosted Integration Runtime** seçin. Daha fazla bilgi için bkz. [proxy olarak şirket içinde barındırılan BIR IR ayarlama](./self-hosted-integration-runtime-proxy-ssis.md).   

   1. **Devam**’ı seçin. 

Tümleştirme çalışma zamanı **Kurulum** bölmesinin **Özet** sayfasında, tüm sağlama ayarlarını gözden geçirin, önerilen belge bağlantılarına yer işareti ekleyin ve **Oluştur** ' u seçerek tümleştirme çalışma zamanının oluşturulmasını başlatın. 

   > [!NOTE]
   > Özel kurulum süresini dışlayarak, bu işlem 5 dakika içinde tamamlanır.
   >
   > SSSıSDB kullanıyorsanız, Data Factory hizmeti SSıSDB 'yi hazırlamak için veritabanı sunucunuza bağlanır. 
   > 
   > Bir Azure-SSIS IR sağladığınızda, SSIS için yeniden dağıtılabilir ve Azure Özellik paketi de yüklenir. Bu bileşenler, yerleşik bileşenlerin zaten desteklediği veri kaynaklarına ek olarak Excel dosyaları, erişim dosyaları ve çeşitli Azure veri kaynaklarına bağlantı sağlar. Yerleşik/önceden yüklenmiş bileşenler hakkında daha fazla bilgi için, bkz. [Azure-SSIS IR yerleşik/önceden yüklenmiş bileşenler](./built-in-preinstalled-components-ssis-integration-runtime.md). Yükleyebileceğiniz ek bileşenler hakkında daha fazla bilgi için bkz. [özel ayarlar Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="connections-pane"></a>Bağlantılar bölmesi

Hub 'ı **Yönet** ' in **Bağlantılar** bölmesinde, **tümleştirme çalışma zamanları** sayfasına geçin ve **Yenile**' yi seçin. 

   ![Bağlantılar bölmesi](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Azure-SSIS IR adını seçerek düzenleyebilir/yeniden yapılandırabilirsiniz. Ayrıca, Azure-SSIS IR izlemek/başlatmak/durdurmak/silmek için ilgili düğmeleri seçebilir, Azure-SSIS IR üzerinde çalışacak SSIS paketi yürütme etkinliğinin bulunduğu bir ADF işlem hattını otomatik olarak oluşturabilir ve Azure-SSIS IR JSON kodunu/yükünü görüntüleyebilirsiniz.  Azure-SSIS IR düzenlemeniz/silmeniz, yalnızca durdurulduğunda yapılabilir.

## <a name="deploy-ssis-packages"></a>SSIS paketlerini dağıtma

SSSıSDB kullanıyorsanız, paketlerinizi bu pakete dağıtabilir ve Azure özellikli SSDT veya SSMS araçlarını kullanarak Azure-SSIS IR çalıştırabilirsiniz. Bu araçlar, veritabanı sunucunuza sunucu uç noktası aracılığıyla bağlanır: 

- Azure SQL veritabanı sunucusu için sunucu uç noktası biçimi olur `<server name>.database.windows.net` .
- Özel uç noktası olan yönetilen bir örnek için sunucu uç noktası biçimi olur `<server name>.<dns prefix>.database.windows.net` .
- Ortak uç nokta içeren bir yönetilen örnek için sunucu uç noktası biçimi olur `<server name>.public.<dns prefix>.database.windows.net,3342` . 

SSSıSDB kullanmıyorsanız, paketlerinizi Azure SQL yönetilen örneğiniz tarafından barındırılan dosya sistemine, Azure dosyalarına veya MSDB 'ye dağıtabilir ve [dtutil](/sql/integration-services/dtutil-utility) ve [Azuredtexec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) komut satırı yardımcı programlarını kullanarak Azure-SSIS IR çalıştırabilirsiniz. 

Daha fazla bilgi için bkz. [SSIS projelerini/paketlerini dağıtma](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

Her iki durumda da, Data Factory işlem hatlarında SSIS paketi yürütme etkinliğini kullanarak dağıtılmış paketlerinizi Azure-SSIS IR de çalıştırabilirsiniz. Daha fazla bilgi için bkz. [birinci sınıf Data Factory etkinliği olarak SSIS paketi yürütmeyi çağırma](./how-to-invoke-ssis-package-ssis-activity.md).

Ayrıca bkz. aşağıdaki SSIS belgeleri: 

- [Azure 'da SSIS paketlerini dağıtma, çalıştırma ve izleme](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Azure 'da SSıSDB 'ye bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Azure 'da paket yürütmelerini zamanlama](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Windows kimlik doğrulaması ile şirket içi veri kaynaklarına bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Sonraki adımlar

Azure-SSIS tümleştirme çalışma zamanınızı özelleştirme hakkında bilgi edinmek için şu makaleye bakın: 

> [!div class="nextstepaction"]
> [Azure-SSIS IR özelleştirme](./how-to-configure-azure-ssis-ir-custom-setup.md)