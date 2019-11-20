---
title: Azure-SSIS tümleştirme çalışma zamanını sağlama
description: Azure'da SSIS paketleri dağıtıp çalıştırabilmek için Azure Data Factory'de Azure-SSIS tümleştirme çalışma zamanı sağlamayı öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 14871aa790bd94dbd23dea30c8a229eac7945c75
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683574"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory 'de Azure-SSIS tümleştirme çalışma zamanını sağlama

Bu öğreticide, Azure Data Factory 'de bir Azure-SQL Server Integration Services (SSIS) tümleştirme çalışma zamanı (IR) sağlamak için Azure portal kullanma adımları sunulmaktadır. Bir Azure-SSIS IR şunları destekler:

- Bir Azure SQL veritabanı sunucusu veya yönetilen örnek (proje dağıtım modeli) tarafından barındırılan SSIS kataloğuna (SSıSDB) dağıtılan Paketleri çalıştırma.
- Dosya sistemlerine, dosya paylaşımlarına veya Azure dosyalarına (paket dağıtım modeli) dağıtılan Paketleri çalıştırma. 

Bir Azure-SSIS IR sağlandıktan sonra, Azure 'da paketlerinizi dağıtmak ve çalıştırmak için tanıdık araçları kullanabilirsiniz. Bu araçlar `dtinstall`, `dtutil`ve `dtexec`gibi SQL Server Veri Araçları, SQL Server Management Studio ve komut satırı araçlarını içerir.

Azure-SSIS IR’ler hakkında kavramsal bilgiler için bkz. [Azure SSIS tümleştirme çalışma zamanına genel bakış](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Bu öğreticide, aşağıdaki adımları tamamlayacaksınız:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Azure-SSIS tümleştirme çalışma zamanı sağlama.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
- **Azure SQL veritabanı sunucusu (isteğe bağlı)** . Henüz bir veritabanı sunucunuz yoksa, başlamadan önce Azure portalında bir tane oluşturun. Data Factory, bu veritabanı sunucusunda SSıSDB örneği oluşturacak. 

  Veritabanı sunucusunu tümleştirme çalışma zamanı ile aynı Azure bölgesinde oluşturmanız önerilir. Bu yapılandırma, tümleştirme çalışma zamanının yürütme günlüklerini Azure bölgeleriyle çıkmadan SSSıSDB 'ye yazmasını sağlar.

  Şu noktaları göz önünde bulundurun:

  - Seçili veritabanı sunucusuna bağlı olarak, SSıSDB örneği, elastik havuzun bir parçası olarak veya yönetilen bir örnekte sizin adınıza tek bir veritabanı olarak oluşturulabilir. Bu, genel bir ağda veya bir sanal ağa katılarak erişilebilir. SSıSDB barındıracak veritabanı sunucusu türünü seçme konusunda rehberlik için bkz. [Azure SQL veritabanı tek veritabanı, elastik havuz ve yönetilen örnek karşılaştırması](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
  
    Sanal ağ hizmet uç noktaları ile bir Azure SQL veritabanı sunucusu veya SSSıSDB 'yi barındırmak için özel bir uç noktası olan yönetilen bir örnek kullanıyorsanız veya şirket içinde barındırılan IR yapılandırmadan şirket içi verilere erişmeniz gerekiyorsa, Azure-SSIS IR bir sanal ağa katılmanız gerekir Network. Daha fazla bilgi için bkz. [Sanal ağda Azure-SSIS IR oluşturma](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
  - Veritabanı sunucusunda **Azure hizmetlerine erişime izin ver** ayarının etkin olduğundan emin olun. Bu ayar, sanal ağ hizmet uç noktaları ile bir Azure SQL veritabanı sunucusu veya SSSıSDB barındırmak için özel uç noktası olan yönetilen bir örnek kullandığınızda geçerli değildir. Daha fazla bilgi için bkz. [Azure SQL veritabanınızın güvenliğini sağlama](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Bu ayarı PowerShell kullanarak etkinleştirmek için, bkz. [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
  - İstemci makinenin IP adresini veya istemci makinenin IP adresini içeren bir IP adresi aralığını, veritabanı sunucusunun güvenlik duvarı ayarlarındaki istemci IP adresi listesine ekleyin. Daha fazla bilgi için bkz. [Azure SQL Veritabanı'nda sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı kuralları yapılandırma](../sql-database/sql-database-firewall-configure.md).
  - Sunucu Yöneticisi kimlik bilgilerinizle SQL kimlik doğrulaması kullanarak veya veri fabrikanızın yönetilen kimliğiyle Azure AD kimlik doğrulamasını kullanarak, veritabanı sunucusuna bağlanabilirsiniz. İkincisi için, veri fabrikanızın yönetilen kimliğini, veritabanı sunucusuna erişim izinleri olan bir Azure AD grubuna eklemeniz gerekir. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure-SSIS IR oluşturma](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
  - Veritabanı sunucunuzun zaten bir SSıSDB örneğine sahip olmadığından emin olun. Azure-SSIS IR sağlanması, var olan bir SSıSDB örneğinin kullanımını desteklemez.


> [!NOTE]
> Data Factory ve Azure-SSIS IR Şu anda kullanılabildiği Azure bölgelerinin listesi için bkz. [bölgeye göre Data Factory ve SSIS IR kullanılabilirliği](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Veri fabrikanızı Azure portal aracılığıyla oluşturmak için, [Kullanıcı arabirimi aracılığıyla veri fabrikası oluşturma](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)' daki adım adım yönergeleri izleyin. Oluşturulduktan sonra hızlı erişime izin vermek için, bunu yaparken **panoya sabitle** ' yi seçin. 

Data Factory oluşturulduktan sonra, Azure portal genel bakış sayfasını açın. **Başlangıç** sayfasını ayrı bir sekmede açmak için **Yazar & izleyici** kutucuğunu seçin. Burada, Azure-SSIS IR oluşturmaya devam edebilirsiniz.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS tümleştirme çalışma zamanı oluşturma

### <a name="from-the-data-factory-overview"></a>Data Factory genel bakış

1. **Başlarken** sayfasında **SSIS Tümleştirme Çalışma Zamanı Yapılandır** kutucuğunu seçin. 

   !["SSIS Tümleştirme Çalışma Zamanı Yapılandır" kutucuğu](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Azure SSIS IR kurulumunun diğer adımları için [Azure SSIS tümleştirme çalışma zamanı sağlama](#provision-an-azure-ssis-integration-runtime) bölümüne bakın. 

### <a name="from-the-authoring-ui"></a>Yazma kullanıcı arabiriminden

1. Azure Data Factory Kullanıcı arabiriminde, **Düzenle** sekmesine geçin ve **Bağlantılar**' ı seçin. Ardından, veri fabrikanızdaki mevcut tümleştirme çalışma zamanlarını görüntülemek için **tümleştirme çalışma zamanları** sekmesine geçin. 

   ![Var olan IR’leri görüntüleme seçimleri](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Bir Azure-SSIS IR oluşturmak için **Yeni**'yi seçin. 

   ![Menü aracılığıyla tümleştirme çalışma zamanı](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. **Tümleştirme Çalışma Zamanı Kurulumu** penceresinde **Mevcut SSIS paketlerini Azure’da yürütmek üzere lift-and-shift ile taşıma**’yı seçip **İleri**’yi seçin. 

   ![Tümleştirme çalışma zamanının türünü belirtin](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Azure SSIS IR kurulumunun diğer adımları için [Azure SSIS tümleştirme çalışma zamanı sağlama](#provision-an-azure-ssis-integration-runtime) bölümüne bakın. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS tümleştirme çalışma zamanı sağlama

1. **Integration Runtime kurulum**'un **Genel ayarlar** sayfasında aşağıdaki adımları izleyin. 

   ![Genel ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. **Name** (Ad) alanına tümleştirme çalışma zamanınızın adını girin. 

   b. **Description** (Açıklama) alanına tümleştirme çalışma zamanınız için bir açıklama girin. 

   c. **Location** (Konum)alanında tümleştirme çalışma zamanınızın konumunu seçin. Yalnızca desteklenen konumlar görüntülenir. SSISDB'yi barındırmak için veritabanı sunucunuz ile aynı konumu seçmenizi öneririz. 

   d. **Node Size** (Düğüm Boyutu) alanında tümleştirme çalışma zamanınızdaki düğümün boyutunu seçin. Yalnızca desteklenen düğüm boyutları görüntülenir. Çok yoğun işlem yoğunluğu veya bellek kullanımı yoğun paketler çalıştırmak istiyorsanız büyük bir düğüm boyutu seçin (ölçeği büyütme). 

   e. **Node Number** (Düğüm Sayısı) alanında tümleştirme çalışma zamanınızdaki düğüm sayısını seçin. Yalnızca desteklenen düğüm sayıları görüntülenir. Birçok paketi paralel olarak çalıştırmak istiyorsanız çok sayıda düğümü olan büyük bir küme seçin (ölçeği genişletme). 

   f. **Sürüm/lisans**için, tümleştirme çalışma zamanı için SQL Server sürümünü seçin: Standart veya kurumsal. Tümleştirme çalışma zamanı ' nda Gelişmiş özellikleri kullanmak istiyorsanız Kurumsal ' i seçin. 

   g. Tasarruf **için,** tümleştirme çalışma zamanı için Azure hibrit avantajı seçeneğini belirleyin: **Evet** veya **Hayır**. Karma kullanım ile maliyet tasarruflarından faydalanmak için kendi SQL Server lisansınızı Yazılım Güvencesine getirmek istiyorsanız **Evet** ' i seçin. 

   h. **İleri**’yi seçin. 

1. **SQL ayarları** sayfasında aşağıdaki adımları izleyin. 

   ![SQL ayarları](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Azure-SSIS IR, paketlere yönelik dağıtım modelini seçmek için **SSIS Kataloğu oluştur...** onay kutusunu seçin. Paketlerin, veritabanı sunucunuz tarafından barındırılan SSıSDB 'ye veya paketlerin dosya sistemlerine, dosya paylaşımlarına veya Azure dosyalarına dağıtıldığı paket dağıtım modeline dağıtıldığı proje dağıtım modelini seçersiniz.
   
   Onay kutusunu seçerseniz, sizin adınıza oluşturacağınız ve yönetecağımız SSıSDB barındırmak için kendi veritabanı sunucunuzu getirmeniz gerekir.
   
   b. **Subscription** (Abonelik) alanında SSISDB'yi barındıran Azure aboneliğini seçin. 

   c. **Location** (Konum) alanında SSISDB'yi barındıran veritabanı sunucunuzun konumunu seçin. Tümleştirme çalışma zamanınızla aynı konumu seçmenizi öneririz.

   d. **Catalog Database Server Endpoint** (Katalog Veritabanı Sunucusu Uç Noktası) alanında SSISDB'yi barındıracak veritabanı sunucunuzun uç noktasını seçin. 
   
   Seçili veritabanı sunucusuna bağlı olarak, SSıSDB örneği, elastik havuzun bir parçası olarak veya yönetilen bir örnekte sizin adınıza tek bir veritabanı olarak oluşturulabilir. Bu, genel bir ağda veya bir sanal ağa katılarak erişilebilir. SSıSDB barındıracak veritabanı sunucusu türünü seçme konusunda rehberlik için bkz. [Azure SQL veritabanı tek veritabanı, elastik havuz ve yönetilen örnek karşılaştırması](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).   

   SSıSDB 'yi barındırmak için sanal ağ hizmet uç noktaları veya yönetilen bir örnek içeren bir Azure SQL veritabanı sunucusu seçerseniz veya şirket içinde barındırılan IR 'yi yapılandırmadan şirket içi verilere erişmeniz gerekiyorsa, Azure-SSIS IR bir veritabanına katılmanız gerekir sanal ağ. Daha fazla bilgi için bkz. [Sanal ağda Azure-SSIS IR oluşturma](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

   e. Veritabanı sunucunuzun SSıSDB 'yi barındıracak kimlik doğrulama yöntemini seçmek için, **ADF 'niz için Managed Identity Ile AAD kimlik doğrulamasını kullan** onay kutusunu seçin. Veri fabrikanızın yönetilen kimliğiyle SQL kimlik doğrulaması ya da Azure AD kimlik doğrulaması ' nı seçersiniz.

   Onay kutusunu seçerseniz, veritabanı sunucunuza erişim izinleri olan bir Azure AD grubuna veri fabrikanızın yönetilen kimliğini eklemeniz gerekir. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure-SSIS IR oluşturma](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
   f. **Yönetici Kullanıcı adı**IÇIN, SSISDB barındırmak üzere VERITABANı sunucunuzun SQL kimlik doğrulaması Kullanıcı adı ' nı girin. 

   g. **Yönetici parolası**IÇIN, SSISDB barındırmak üzere VERITABANı sunucunuzun SQL kimlik doğrulama parolasını girin. 

   h. **Katalog veritabanı hizmet katmanı**IÇIN, SSISDB barındıracak veritabanı sunucunuzun hizmet katmanını seçin. Temel, standart veya Premium katmanını seçin veya elastik havuz adı seçin.

   i. **Bağlantıyı Sına**' yı seçin. Test başarılı olursa, **İleri**' yi seçin. 

1. **Gelişmiş ayarlar** sayfasında, aşağıdaki adımları izleyin. 

   ![Gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. **Düğüm başına en fazla paralel yürütme**için, tümleştirme çalışma zamanı kümenizde düğüm başına aynı anda çalıştırılacak en fazla paket sayısını seçin. Yalnızca desteklenen paket sayıları görüntülenir. İşlem veya bellek kullanımı yoğun olan tek bir büyük paketi çalıştırmak için birden fazla çekirdek kullanmak istiyorsanız düşük bir sayı seçin. Tek bir çekirdekli bir veya daha fazla küçük paket çalıştırmak istiyorsanız, yüksek bir sayı seçin. 

   b. **Özel kurulum KAPSAYıCıSı SAS URI 'si**için, isteğe bağlı olarak, kurulum betiğinizin ve onunla ilişkili dosyaların depolandığı Azure Blob depolama kapsayıcısının paylaşılan erişim IMZASı (SAS) Tekdüzen Kaynak tanımlayıcısı (URI) girin. Daha fazla bilgi için bkz. [özel kurulum Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   c. Tümleştirme çalışma zamanını bir sanal ağa katmak isteyip istemediğinizi seçmek için **Azure-SSIS Integration Runtime için bir VNET seçin ve ADF 'nin belirli ağ kaynaklarını oluşturmasına izin ver** onay kutusunu seçin.

   Sanal ağ hizmet uç noktaları ile bir Azure SQL veritabanı sunucusu veya SSıSDB barındırmak için özel bir uç noktası olan yönetilen bir örnek kullanıyorsanız veya şirket içinde barındırılan bir IR yapılandırmadan şirket içi verilere erişim istiyorsanız bu seçeneği belirleyin. Daha fazla bilgi için bkz. [Sanal ağda Azure-SSIS IR oluşturma](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   d. Azure-SSIS IR için ara sunucu olarak şirket içinde barındırılan bir IR yapılandırmak isteyip istemediğinizi seçmek için, **Azure-SSIS Integration Runtime Için otomatik olarak barındırılan Integration Runtime ara sunucu olarak ayarla** onay kutusunu seçin. Daha fazla bilgi için bkz. [proxy olarak şirket içinde BARıNDıRıLAN IR ayarlama](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

1. **İleri**’yi seçin. 

1. **Özet** sayfasında, tüm sağlama ayarlarını gözden geçirin, önerilen belge bağlantılarına yer işareti ekleyin ve tümleştirme çalışma zamanının oluşturulmasını başlatmak için **son** ' u seçin. 

   > [!NOTE]
   > Özel kurulum süresini dışlayarak, bu işlem 5 dakika içinde tamamlanır.
   >
   > SSSıSDB kullanıyorsanız, Data Factory hizmeti SSıSDB 'yi hazırlamak için veritabanı sunucunuza bağlanır. 
   > 
   > Bir Azure-SSIS IR sağladığınızda, SSIS için yeniden dağıtılabilir ve Azure Özellik paketi de yüklenir. Bu bileşenler, yerleşik bileşenlerin zaten desteklediği veri kaynaklarına ek olarak Excel dosyaları, erişim dosyaları ve çeşitli Azure veri kaynaklarına bağlantı sağlar. Yükleyebileceğiniz diğer bileşenler hakkında daha fazla bilgi için bkz. [özel kurulum Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. **Bağlantılar** sekmesinde, gerekirse **Tümleştirme Çalışma Zamanları**’na geçin. Durumu yenilemek için **Yenile**’yi seçin. 

   !["Yenile" düğmesi ile oluşturma durumu](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Tümleştirme çalışma zamanını durdurmak/başlatmak, düzenlemek veya silmek için **Eylemler** sütunundaki bağlantıları kullanın. Tümleştirme çalışma zamanının JSON kodunu görüntülemek için son bağlantıyı kullanın. Düzenle ve sil düğmeleri yalnızca IR durdurulmuş durumdayken etkin olur. 

   !["Eylemler" sütunundaki bağlantılar](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>SSIS paketlerini dağıtma

SSSıSDB kullanıyorsanız, paketlerinizi bu pakete dağıtabilir ve SQL Server Veri Araçları veya SQL Server Management Studio araçları kullanarak Azure-SSIS IR çalıştırabilirsiniz. Bu araçlar, veritabanı sunucunuza sunucu uç noktası aracılığıyla bağlanır: 

- Özel uç noktası olan bir Azure SQL veritabanı sunucusu için sunucu uç noktası biçimi `<server name>.database.windows.net`.
- Özel uç noktası olan yönetilen bir örnek için sunucu uç noktası biçimi `<server name>.<dns prefix>.database.windows.net`.
- Ortak uç nokta olan yönetilen bir örnek için sunucu uç noktası biçimi `<server name>.public.<dns prefix>.database.windows.net,3342`. 

SSSıSDB kullanmıyorsanız, paketlerinizi dosya sistemlerine, dosya paylaşımlarına veya Azure dosyalarına dağıtabilirsiniz. Daha sonra `dtinstall`, `dtutil`ve `dtexec` komut satırı araçlarını kullanarak bunları Azure-SSIS IR çalıştırabilirsiniz. Daha fazla bilgi için bkz. [SSIS paketlerini dağıtma](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Her iki durumda da, Data Factory işlem hatlarında SSIS paketi yürütme etkinliğini kullanarak Azure-SSIS IR dağıtılan paketlerinizi çalıştırabilirsiniz. Daha fazla bilgi için bkz. [birinci sınıf Data Factory etkinliği olarak SSIS paketi yürütmeyi çağırma](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Ayrıca bkz. aşağıdaki SSIS belgeleri: 

- [Azure 'da SSIS paketlerini dağıtma, çalıştırma ve izleme](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Azure 'da SSıSDB 'ye bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Azure 'da paket yürütmelerini zamanlama](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Windows kimlik doğrulaması ile şirket içi veri kaynaklarına bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Sonraki adımlar

Azure-SSIS tümleştirme çalışma zamanınızı özelleştirme hakkında bilgi edinmek için şu makaleye bakın: 

> [!div class="nextstepaction"]
> [Azure-SSIS IR özelleştirme](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)