---
title: Azure Data Factory 'de bir Azure-SSIS tümleştirme çalışma zamanı oluşturma
description: Azure 'da SSIS paketlerini dağıtıp çalıştırmak için Azure Data Factory 'de Azure-SSIS tümleştirme çalışma zamanı oluşturmayı öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: ce688248a205981f4a4c60ad01231c0b8f6bae3d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73677367"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory 'de bir Azure-SSIS tümleştirme çalışma zamanı oluşturma

Bu makalede, Azure Data Factory bir Azure-SQL Server Integration Services (SSIS) tümleştirme çalışma zamanı (IR) sağlama adımları sağlanır. Bir Azure-SSIS IR şunları destekler:

- Bir Azure SQL veritabanı sunucusu veya yönetilen örnek (proje dağıtım modeli) tarafından barındırılan SSIS kataloğuna (SSıSDB) dağıtılan Paketleri çalıştırma.
- Dosya sistemlerine, dosya paylaşımlarına veya Azure dosyalarına (paket dağıtım modeli) dağıtılan Paketleri çalıştırma. 

Bir Azure-SSIS IR sağlandıktan sonra, Azure 'da paketlerinizi dağıtmak ve çalıştırmak için tanıdık araçları kullanabilirsiniz. Bu araçlar `dtinstall`, `dtutil`ve `dtexec`gibi SQL Server Veri Araçları, SQL Server Management Studio ve komut satırı araçlarını içerir.

[Sağlama Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) öğretici, Azure portal veya Data Factory uygulaması aracılığıyla bir Azure-SSIS IR oluşturmayı gösterir. Bu öğreticide, bir Azure SQL veritabanı sunucusu veya bir yönetilen örnek için SSSıSDB barındırmak üzere nasıl kullanılacağı gösterilmektedir. Bu makale öğreticiye genişleterek bu isteğe bağlı görevlerin nasıl yapılacağını açıklar:

- SSıSDB barındırmak için sanal ağ hizmet uç noktaları veya özel bir uç nokta ile yönetilen bir örnek içeren bir Azure SQL veritabanı sunucusu kullanın. Bir önkoşul olarak, bir sanal ağa katılması için Azure-SSIS IR sanal ağ izinlerini ve ayarlarını yapılandırmanız gerekir.

- Azure SQL veritabanı sunucusuna veya yönetilen örneğe bağlanmak için veri fabrikanızın yönetilen kimliğiyle birlikte Azure Active Directory (Azure AD) kimlik doğrulaması kullanın. Bir önkoşul olarak, veri fabrikanızın yönetilen kimliğini bir SSıSDB örneği oluşturabileceğiniz bir veritabanı kullanıcısı olarak eklemeniz gerekir.

- Azure-SSIS IR bir sanal ağa katın veya Şirket içindeki verilere erişmek için Azure-SSIS IR bir ara sunucu olarak kendiliğinden konak IR yapılandırın.

Bu makalede, Azure portal, Azure PowerShell ve Azure Resource Manager şablonunu kullanarak bir Azure-SSIS IR sağlama gösterilmektedir.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure aboneliği**. Aboneliğiniz yoksa [ücretsiz bir deneme](https://azure.microsoft.com/pricing/free-trial/) hesabı oluşturabilirsiniz.
- **Azure SQL veritabanı sunucusu veya yönetilen örneği (isteğe bağlı)** . Henüz bir veritabanı sunucunuz yoksa, başlamadan önce Azure portalında bir tane oluşturun. Data Factory, bu veritabanı sunucusunda SSıSDB örneği oluşturacak. 

  Veritabanı sunucusunu tümleştirme çalışma zamanı ile aynı Azure bölgesinde oluşturmanız önerilir. Bu yapılandırma, tümleştirme çalışma zamanının yürütme günlüklerini Azure bölgeleriyle çıkmadan SSSıSDB 'ye yazmasını sağlar.

  Şu noktaları göz önünde bulundurun:

  - Seçili veritabanı sunucusuna bağlı olarak, SSıSDB örneği, elastik havuzun bir parçası olarak veya yönetilen bir örnekte sizin adınıza tek bir veritabanı olarak oluşturulabilir. Bu, genel bir ağda veya bir sanal ağa katılarak erişilebilir. SSıSDB barındıracak veritabanı sunucusu türünü seçme konusunda rehberlik için, bu makaledeki [Azure SQL veritabanı tek veritabanı, elastik havuz ve yönetilen örnek karşılaştırması](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) bölümüne bakın. 
  
    Sanal ağ hizmet uç noktaları ile bir Azure SQL veritabanı sunucusu veya SSSıSDB 'yi barındırmak için özel bir uç noktası olan yönetilen bir örnek kullanıyorsanız veya şirket içinde barındırılan IR yapılandırmadan şirket içi verilere erişmeniz gerekiyorsa, Azure-SSIS IR bir sanal ağa katılmanız gerekir Network. Daha fazla bilgi için bkz. bir [Azure-SSIS IR sanal ağa ekleme](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
  - Veritabanı sunucusunda **Azure hizmetlerine erişime izin ver** ayarının etkin olduğundan emin olun. Bu ayar, sanal ağ hizmet uç noktaları ile bir Azure SQL veritabanı sunucusu veya SSSıSDB barındırmak için özel uç noktası olan yönetilen bir örnek kullandığınızda geçerli değildir. Daha fazla bilgi için bkz. [Azure SQL veritabanınızın güvenliğini sağlama](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Bu ayarı PowerShell kullanarak etkinleştirmek için, bkz. [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
  - İstemci makinenin IP adresini veya istemci makinenin IP adresini içeren bir IP adresi aralığını, veritabanı sunucusunun güvenlik duvarı ayarlarındaki istemci IP adresi listesine ekleyin. Daha fazla bilgi için bkz. [Azure SQL Veritabanı'nda sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı kuralları yapılandırma](../sql-database/sql-database-firewall-configure.md).
  - Sunucu Yöneticisi kimlik bilgilerinizle SQL kimlik doğrulaması kullanarak veya veri fabrikanızın yönetilen kimliğiyle Azure AD kimlik doğrulamasını kullanarak, veritabanı sunucusuna bağlanabilirsiniz. İkincisi için, veri fabrikanızın yönetilen kimliğini, veritabanı sunucusuna erişim izinleri olan bir Azure AD grubuna eklemeniz gerekir. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasını bir Azure-SSIS IR Için etkinleştirme](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
  - Veritabanı sunucunuzun zaten bir SSıSDB örneğine sahip olmadığından emin olun. Azure-SSIS IR sağlanması, var olan bir SSıSDB örneğinin kullanımını desteklemez.
- **Sanal ağ Azure Resource Manager (isteğe bağlı)** . Aşağıdaki koşullardan en az biri doğru ise bir Azure Resource Manager sanal ağınız olmalıdır:
    - Sanal ağ hizmeti uç noktaları veya yönetilen bir örnek olan bir Azure SQL veritabanı sunucusunda SSSıSDB barındırıyorsanız.
    - Şirket içinde barındırılan bir IR yapılandırmadan Azure-SSIS IR çalışan SSIS paketlerindeki şirket içi veri depolarına bağlanmak istiyorsunuz.
- **Azure PowerShell (isteğe bağlı)** . Azure-SSIS IR sağlamak üzere bir PowerShell Betiği çalıştırmak istiyorsanız, [Azure PowerShell 'yi yüklemek ve yapılandırmak](/powershell/azure/install-az-ps)için yönergeleri izleyin.

### <a name="regional-support"></a>Bölgesel destek

Data Factory ve Azure-SSIS IR kullanılabilen Azure bölgelerinin listesi için bkz. [bölgeye göre Data Factory ve SSIS IR kullanılabilirliği](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>SQL veritabanı tek veritabanı, elastik havuz ve yönetilen örnek karşılaştırması

Aşağıdaki tabloda, Azure-SSıR IR ile bağlantılı olarak bir Azure SQL veritabanı sunucusunun ve yönetilen örneğin belirli özellikleri karşılaştırılmaktadır:

| Özellik | Tek veritabanı/elastik havuz| Yönetilen örnek |
|---------|--------------|------------------|
| **Planlama** | SQL Server Agent kullanılamıyor.<br/><br/>Bkz. Data Factory işlem hattında [paket yürütmeyi zamanlama](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Yönetilen örnek Aracısı kullanılabilir. |
| **Kimlik doğrulaması** | **Db_owner** rolünde üye olarak veri fabrikanızın yönetilen kimliği ile herhangi BIR Azure AD grubunu temsil eden bir bulunan veritabanı kullanıcısına sahıp bır SSISDB örneği oluşturabilirsiniz.<br/><br/>Bkz. Azure [SQL veritabanı sunucusunda BIR SSıSDB örneği oluşturmak için bkz. Azure AD kimlik doğrulamasını etkinleştirme](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Veri fabrikanızın yönetilen kimliğini temsil eden kapsanan bir veritabanı kullanıcısına sahip bir SSıSDB örneği oluşturabilirsiniz. <br/><br/>Bkz. Azure [SQL veritabanı yönetilen örneği 'NDE SSıSDB örneği oluşturmak Için Azure AD kimlik doğrulamasını etkinleştirme](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Hizmet katmanı** | Azure SQL veritabanı sunucunuz ile bir Azure-SSIS IR oluşturduğunuzda SSSıSDB için hizmet katmanını seçebilirsiniz. Birden çok hizmet katmanı vardır. | Yönetilen örneğiniz ile bir Azure-SSIS IR oluşturduğunuzda SSSıSDB için hizmet katmanını seçemezsiniz. Yönetilen örnekteki tüm veritabanları, bu örneğe ayrılan kaynağı paylaşır. |
| **Sanal ağ** | Azure-SSIS IR, yalnızca sanal ağ hizmet uç noktaları ile bir Azure SQL veritabanı sunucusu kullanırsanız veya şirket içinde barındırılan IR 'yi yapılandırmadan şirket içi veri depolarına erişmeniz gerekiyorsa, Azure Resource Manager sanal ağlara katılabilirler. | Azure-SSIS IR, yalnızca Azure Resource Manager sanal ağlara katılabilir. Yönetilen örneğiniz için genel bir uç nokta etkinleştirmezseniz sanal ağ gereklidir.<br/><br/>Azure-SSIS IR yönetilen örneğiniz ile aynı sanal ağa katılırsanız, Azure-SSIS IR yönetilen örneğinizin farklı bir alt ağda olduğundan emin olun. Azure-SSIS IR yönetilen örneğinden farklı bir sanal ağa katılırsanız, sanal ağ eşlemesi veya ağdan ağa bağlantı önerilir. Bkz. [uygulamanızı Azure SQL veritabanı yönetilen örneğine bağlama](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Dağıtılmış işlemler** | Bu özellik esnek işlemler aracılığıyla desteklenir. Microsoft Dağıtılmış İşlem Düzenleyicisi (MSDTC) işlemleri desteklenmez. SSIS paketleriniz dağıtılmış işlemleri koordine etmek için MSDTC kullanıyorsa, Azure SQL veritabanı için elastik işlemlere geçiş yapmayı göz önünde bulundurun. Daha fazla bilgi için bkz. [bulut veritabanları arasında dağıtılmış işlemler](../sql-database/sql-database-elastic-transactions-overview.md). | Desteklenmiyor. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Tümleştirme çalışma zamanı oluşturmak için Azure portal kullanma

Bu bölümde, bir Azure-SSIS IR oluşturmak için özellikle Data Factory Kullanıcı arabirimi (UI) veya uygulama Azure portal kullanırsınız.

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Veri fabrikanızı Azure portal aracılığıyla oluşturmak için, [Kullanıcı arabirimi aracılığıyla veri fabrikası oluşturma](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)' daki adım adım yönergeleri izleyin. Oluşturulduktan sonra hızlı erişime izin vermek için, bunu yaparken **panoya sabitle** ' yi seçin. 

Data Factory oluşturulduktan sonra, Azure portal genel bakış sayfasını açın. Yazarın **Başlangıç** sayfasını ayrı bir sekmede açmak için **Yazar & izleyici** kutucuğunu seçin. Burada, Azure-SSIS IR oluşturmaya devam edebilirsiniz.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS tümleştirme çalışma zamanı sağlama

1. **Başlarken** sayfasında **SSIS Tümleştirme Çalışma Zamanı Yapılandır** kutucuğunu seçin.

   ![SSIS Tümleştirme Çalışma Zamanı Yapılandır kutucuğu](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. **Integration Runtime kurulum**'un **Genel ayarlar** sayfasında aşağıdaki adımları izleyin.

   ![Genel ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. **Name** (Ad) alanına tümleştirme çalışma zamanınızın adını girin.

    b. **Description** (Açıklama) alanına tümleştirme çalışma zamanınız için bir açıklama girin.

    c. **Location** (Konum)alanında tümleştirme çalışma zamanınızın konumunu seçin. Yalnızca desteklenen konumlar görüntülenir. SSISDB'yi barındırmak için veritabanı sunucunuz ile aynı konumu seçmenizi öneririz.

    d. **Düğüm boyutu**için, tümleştirme çalışma zamanı kümenizdeki düğüm boyutunu seçin. Yalnızca desteklenen düğüm boyutları görüntülenir. Çok yoğun işlem yoğunluğu veya bellek kullanımı yoğun paketler çalıştırmak istiyorsanız büyük bir düğüm boyutu seçin (ölçeği büyütme).

    e. **Node Number** (Düğüm Sayısı) alanında tümleştirme çalışma zamanınızdaki düğüm sayısını seçin. Yalnızca desteklenen düğüm sayıları görüntülenir. Birçok paketi paralel olarak çalıştırmak istiyorsanız çok sayıda düğümü olan büyük bir küme seçin (ölçeği genişletme).

    f. **Sürüm/lisans**için, tümleştirme çalışma zamanı için SQL Server sürümünü seçin: Standart veya kurumsal. Tümleştirme çalışma zamanı ' nda Gelişmiş özellikleri kullanmak istiyorsanız Kurumsal ' i seçin.

    g. Tasarruf **için,** tümleştirme çalışma zamanı için Azure hibrit avantajı seçeneğini belirleyin: **Evet** veya **Hayır**. Karma kullanım ile maliyet tasarruflarından faydalanmak için kendi SQL Server lisansınızı Yazılım Güvencesine getirmek istiyorsanız **Evet** ' i seçin.

    h. **İleri**’yi seçin.

3. **SQL ayarları** sayfasında aşağıdaki adımları izleyin.

   ![SQL ayarları](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Azure-SSIS IR, paketlere yönelik dağıtım modelini seçmek için **SSIS Kataloğu oluştur...** onay kutusunu seçin. Paketlerin, veritabanı sunucunuz tarafından barındırılan SSıSDB 'ye veya paketlerin dosya sistemlerine, dosya paylaşımlarına veya Azure dosyalarına dağıtıldığı paket dağıtım modeline dağıtıldığı proje dağıtım modelini seçersiniz. 
    
   Onay kutusunu seçerseniz, sizin adınıza oluşturacağınız ve yönetecağımız SSıSDB örneğini barındırmak için kendi veritabanı sunucunuzu getirmeniz gerekir.
   
   b. **Subscription** (Abonelik) alanında SSISDB'yi barındıran Azure aboneliğini seçin. 

   c. **Location** (Konum) alanında SSISDB'yi barındıran veritabanı sunucunuzun konumunu seçin. Tümleştirme çalışma zamanınızla aynı konumu seçmenizi öneririz. 

   d. **Catalog Database Server Endpoint** (Katalog Veritabanı Sunucusu Uç Noktası) alanında SSISDB'yi barındıracak veritabanı sunucunuzun uç noktasını seçin. 
    
   Seçili veritabanı sunucusuna bağlı olarak, SSıSDB örneği, elastik havuzun bir parçası olarak veya yönetilen bir örnekte sizin adınıza tek bir veritabanı olarak oluşturulabilir. Bu, genel bir ağda veya bir sanal ağa katılarak erişilebilir. SSıSDB barındıracak veritabanı sunucusu türünü seçme konusunda rehberlik için, bu makaledeki [Azure SQL veritabanı tek veritabanı, elastik havuz ve yönetilen örnek karşılaştırması](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) bölümüne bakın. 
    
   SSıSDB 'yi barındırmak için sanal ağ hizmet uç noktaları veya yönetilen bir örnek içeren bir Azure SQL veritabanı sunucusu seçerseniz veya şirket içinde barındırılan IR 'yi yapılandırmadan şirket içi verilere erişmeniz gerekiyorsa, Azure-SSIS IR bir veritabanına katılmanız gerekir sanal ağ. Daha fazla bilgi için bkz. bir [Azure-SSIS IR sanal ağa ekleme](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

   e. Veritabanı sunucunuzun SSıSDB 'yi barındıracak kimlik doğrulama yöntemini seçmek için, **ADF 'niz için Managed Identity Ile AAD kimlik doğrulamasını kullan** onay kutusunu seçin. Veri fabrikanızın yönetilen kimliğiyle SQL kimlik doğrulaması ya da Azure AD kimlik doğrulaması ' nı seçersiniz. 
    
   Onay kutusunu seçerseniz, veritabanı sunucunuza erişim izinleri olan bir Azure AD grubuna veri fabrikanızın yönetilen kimliğini eklemeniz gerekir. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasını bir Azure-SSIS IR Için etkinleştirme](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

   f. **Yönetici Kullanıcı adı**IÇIN, SSISDB barındırmak üzere VERITABANı sunucunuzun SQL kimlik doğrulaması Kullanıcı adı ' nı girin. 

   g. **Yönetici parolası**IÇIN, SSISDB barındırmak üzere VERITABANı sunucunuzun SQL kimlik doğrulama parolasını girin. 

   h. **Katalog veritabanı hizmet katmanı**IÇIN, SSISDB barındıracak veritabanı sunucunuzun hizmet katmanını seçin. Temel, standart veya Premium katmanını seçin veya elastik havuz adı seçin. 

   i. **Bağlantıyı Sına**' yı seçin. Test başarılı olursa, **İleri**' yi seçin. 

4. **Gelişmiş ayarlar** sayfasında, aşağıdaki adımları izleyin.

   ![Gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. **Düğüm başına en fazla paralel yürütme**için, tümleştirme çalışma zamanı kümenizde düğüm başına aynı anda çalıştırılacak en fazla paket sayısını seçin. Yalnızca desteklenen paket sayıları görüntülenir. İşlem veya bellek kullanımı yoğun olan tek bir büyük paketi çalıştırmak için birden fazla çekirdek kullanmak istiyorsanız düşük bir sayı seçin. Tek bir çekirdekli bir veya daha fazla küçük paket çalıştırmak istiyorsanız, yüksek bir sayı seçin.

   b. **Özel kurulum KAPSAYıCıSı SAS URI 'si**için, isteğe bağlı olarak, kurulum betiğinizin ve onunla ilişkili dosyaların depolandığı Azure Blob depolama kapsayıcısının paylaşılan erişim IMZASı (SAS) Tekdüzen Kaynak tanımlayıcısı (URI) girin. Daha fazla bilgi için bkz. [özel kurulum Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Tümleştirme çalışma zamanını bir sanal ağa katmak isteyip istemediğinizi seçmek için **Azure-SSIS Integration Runtime için bir VNET seçin ve ADF 'nin belirli ağ kaynaklarını oluşturmasına izin ver** onay kutusunu seçin. 

   Sanal ağ hizmet uç noktaları ile bir Azure SQL veritabanı sunucusu veya SSSıSDB barındırmak için özel bir uç nokta ile yönetilen bir örnek kullanıyorsanız veya şirket içi verilere erişim istiyorsanız bu seçeneği belirleyin. (Yani, kendi kendine barındırılan bir IR yapılandırmadan SSIS paketinizdeki şirket içi veri kaynaklarınız veya hedefleri vardır.) Daha fazla bilgi için bkz. bir [Azure-SSIS IR sanal ağa ekleme](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

   Onay kutusunu seçerseniz, aşağıdaki adımları uygulayın.

   ![Bir sanal ağda gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

   a. **Abonelik**için sanal ağınıza sahip Azure aboneliğini seçin.

   b. **Konum**için, tümleştirme çalışma zamanından aynı konum seçilidir.

   c. **Tür**için, sanal ağınızın türünü seçin: klasik veya Azure Resource Manager. Klasik sanal ağların yakında kullanım dışı olacağı için bir Azure Resource Manager sanal ağı seçmenizi öneririz.

   d. **VNET adı**için sanal ağınızın adını seçin. Bu sanal ağ, SSıSDB 'yi barındırmak için sanal ağ hizmet uç noktaları veya bir sanal ağdaki yönetilen bir örnek ile Azure SQL veritabanı sunucusu için kullanılan bir tane olmalıdır. Ya da bu sanal ağ, şirket içi ağınıza bağlı olan ile aynı olmalıdır.

   e. **Alt ağ adı**için, sanal ağınız için alt ağ adını seçin. Bu, SSıSDB 'yi barındırmak için bir sanal ağdaki yönetilen örnek için kullanılan farklı bir alt ağ olmalıdır.

6. Azure-SSIS IR için ara sunucu olarak şirket içinde barındırılan bir IR yapılandırmak isteyip istemediğinizi seçmek için, **Azure-SSIS Integration Runtime Için otomatik olarak barındırılan Integration Runtime ara sunucu olarak ayarla** onay kutusunu seçin. Daha fazla bilgi için bkz. [proxy olarak şirket içinde BARıNDıRıLAN IR ayarlama](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

   Onay kutusunu seçerseniz, aşağıdaki adımları uygulayın.

   ![Şirket içinde barındırılan IR ile gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

   a. **Şirket Içinde barındırılan Integration Runtime**, Azure-SSIS IR için mevcut şirket IÇINDE barındırılan IR 'yi bir ara sunucu olarak seçin.

   b. **Hazırlama depolama bağlı hizmeti**Için mevcut Azure Blob depolama bağlı hizmetinizi seçin. Ya da, hazırlama için yeni bir tane oluşturun.

   c. **Hazırlama yolu**için, seçtiğiniz Azure Blob depolama hesabınızda bir blob kapsayıcısı belirtin. Ya da, hazırlama için varsayılan bir tane kullanmak üzere boş bırakın.

7. **VNET doğrulaması** > **İleri ' yi**seçin. 

8. **Özet** sayfasında, tüm sağlama ayarlarını gözden geçirin, önerilen belge bağlantılarına yer işareti ekleyin ve tümleştirme çalışma zamanının oluşturulmasını başlatmak için **son** ' u seçin.

    > [!NOTE]
    > Özel kurulum süresini dışlayarak, bu işlem 5 dakika içinde tamamlanır. Ancak Azure-SSIS IR bir sanal ağa katılması 20-30 dakika sürebilir.
    >
    > SSSıSDB kullanıyorsanız, Data Factory hizmeti SSıSDB 'yi hazırlamak için veritabanı sunucunuza bağlanır. Ayrıca, belirtilmişse sanal ağınız için izinleri ve ayarları yapılandırır ve Azure-SSIS IR sanal ağa birleştirir.
    > 
    > Bir Azure-SSIS IR sağladığınızda, SSIS için yeniden dağıtılabilir ve Azure Özellik paketi de yüklenir. Bu bileşenler, yerleşik bileşenlerin zaten desteklediği veri kaynaklarına ek olarak Excel dosyaları, erişim dosyaları ve çeşitli Azure veri kaynaklarına bağlantı sağlar. Yükleyebileceğiniz diğer bileşenler hakkında daha fazla bilgi için bkz. [özel kurulum Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

7. **Bağlantılar** sekmesinde, gerekirse **Tümleştirme Çalışma Zamanları**’na geçin. Durumu yenilemek için **Yenile**’yi seçin.

   ![Oluşturma durumu](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Tümleştirme çalışma zamanını durdurmak/başlatmak, düzenlemek veya silmek için **Eylemler** sütunundaki bağlantıları kullanın. Tümleştirme çalışma zamanının JSON kodunu görüntülemek için son bağlantıyı kullanın. Düzenle ve sil düğmeleri yalnızca IR durdurulmuş durumdayken etkin olur.

   ![Azure SSIS IR eylemleri](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Portalda Azure SSIS tümleştirmesi çalışma zamanları

1. Azure Data Factory Kullanıcı arabiriminde, **Düzenle** sekmesine geçin ve **Bağlantılar**' ı seçin. Ardından, veri fabrikanızdaki mevcut tümleştirme çalışma zamanlarını görüntülemek için **tümleştirme çalışma zamanları** sekmesine geçin.

   ![Mevcut IRS 'yi görüntüle](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Yeni bir Azure-SSIS IR oluşturmak için **Yeni** ' yi seçin.

   ![Menü aracılığıyla tümleştirme çalışma zamanı](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

4. **Tümleştirme Çalışma Zamanı Kurulumu** penceresinde **Mevcut SSIS paketlerini Azure’da yürütmek üzere lift-and-shift ile taşıma**’yı seçip **İleri**’yi seçin.

   ![Tümleştirme çalışma zamanının türünü belirtin](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Azure-SSIS IR ayarlamak için kalan adımlar için bkz. [Azure SSIS tümleştirme çalışma zamanı sağlama](#provision-an-azure-ssis-integration-runtime) bölümü.

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Tümleştirme çalışma zamanı oluşturmak için Azure PowerShell kullanma

Bu bölümde, bir Azure-SSIS IR oluşturmak için Azure PowerShell kullanırsınız.

### <a name="create-variables"></a>Değişken oluşturma

Aşağıdaki betiği kopyalayıp yapıştırın. Değişkenlerin değerlerini belirtin. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
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
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"
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

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu kullanarak bir [Azure Kaynak grubu](../azure-resource-manager/resource-group-overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

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

SSıSDB kullanmıyorsanız, `CatalogServerEndpoint`, `CatalogPricingTier`ve `CatalogAdminCredential` parametrelerini atlayabilirsiniz.

Sanal ağ hizmet uç noktaları ile bir Azure SQL veritabanı sunucusu veya SSSıSDB barındırmak için özel bir uç noktası olan yönetilen bir örnek kullanmıyorsanız veya şirket içi verilere erişmeniz gerekiyorsa, `VNetId` ve `Subnet` parametrelerini atlayabilir veya boş değerleri geçirebilirsiniz. yapıştırabilirsiniz. Şirket içindeki verilere erişmek üzere Azure-SSIS IR için kendi kendine barındırılan IR 'yi bir ara sunucu olarak yapılandırırsanız bunları da atlayabilirsiniz. Aksi takdirde, bunları yok saylayamazsınız ve sanal ağ yapılandırmasından geçerli değerler geçirmeniz gerekir. Daha fazla bilgi için bkz. bir [Azure-SSIS IR sanal ağa ekleme](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

SSıSDB barındırmak için yönetilen örnek kullanıyorsanız, `CatalogPricingTier` parametresini atlayabilir veya boş bir değer geçirebilirsiniz. Aksi takdirde, bunu atlayamazsınız ve Azure SQL veritabanı için desteklenen fiyatlandırma katmanları listesinden geçerli bir değer geçirmeniz gerekir. Daha fazla bilgi için bkz. [SQL veritabanı kaynak sınırları](../sql-database/sql-database-resource-limits.md).

Veritabanı sunucusuna bağlanmak için veri fabrikanızın yönetilen kimliğiyle Azure AD kimlik doğrulaması kullanıyorsanız, `CatalogAdminCredential` parametresini atlayabilirsiniz. Ancak, veri fabrikanızın yönetilen kimliğini veritabanı sunucusuna erişim izinleri olan bir Azure AD grubuna eklemeniz gerekir. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasını bir Azure-SSIS IR Için etkinleştirme](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Aksi takdirde, bunu atlayamazsınız ve SQL kimlik doğrulaması için Sunucu Yöneticisi Kullanıcı adı ve parolanınızdan oluşturulmuş geçerli bir nesne geçirmeniz gerekir.

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

# Add the SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
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
> Bir Azure-SSIS IR sağladığınızda, SSIS için yeniden dağıtılabilir ve Azure Özellik paketi de yüklenir. Bu bileşenler, yerleşik bileşenlerin zaten desteklediği veri kaynaklarına ek olarak Excel dosyaları, erişim dosyaları ve çeşitli Azure veri kaynaklarına bağlantı sağlar. Yükleyebileceğiniz diğer bileşenler hakkında daha fazla bilgi için bkz. [özel kurulum Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Tam betik

Azure-SSIS tümleştirme çalışma zamanı oluşturan tam betik aşağıda verilmiştir.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
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
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

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

# Add the SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
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

2. Azure Resource Manager şablonunu dağıtmak için, aşağıdaki örnekte gösterildiği gibi `New-AzResourceGroupDeployment` komutunu çalıştırın. Örnekte, `ADFTutorialResourceGroup` kaynak grubunuzun adıdır. `ADFTutorialARM.json`, veri fabrikanızın JSON tanımını ve Azure-SSIS IR içeren dosyadır.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Bu komut, veri fabrikanızı oluşturur ve içinde Azure-SSIS IR, ancak IR 'yi başlatmaz.

3. Azure-SSIS IR başlatmak için `Start-AzDataFactoryV2IntegrationRuntime` komutunu çalıştırın:

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
> Bir Azure-SSIS IR sağladığınızda, SSIS için yeniden dağıtılabilir ve Azure Özellik paketi de yüklenir. Bu bileşenler, yerleşik bileşenlerin zaten desteklediği veri kaynaklarına ek olarak Excel dosyaları, erişim dosyaları ve çeşitli Azure veri kaynaklarına bağlantı sağlar. Yükleyebileceğiniz diğer bileşenler hakkında daha fazla bilgi için bkz. [özel kurulum Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>SSIS paketlerini dağıtma

SSSıSDB kullanıyorsanız, paketlerinizi bu pakete dağıtabilir ve SQL Server Veri Araçları veya SQL Server Management Studio araçları kullanarak Azure-SSIS IR çalıştırabilirsiniz. Bu araçlar, veritabanı sunucunuza sunucu uç noktası aracılığıyla bağlanır: 

- Özel uç noktası olan bir Azure SQL veritabanı sunucusu için sunucu uç noktası biçimi `<server name>.database.windows.net`.
- Özel uç noktası olan yönetilen bir örnek için sunucu uç noktası biçimi `<server name>.<dns prefix>.database.windows.net`.
- Ortak uç nokta olan yönetilen bir örnek için sunucu uç noktası biçimi `<server name>.public.<dns prefix>.database.windows.net,3342`. 

SSSıSDB kullanmıyorsanız, paketlerinizi dosya sistemlerine, dosya paylaşımlarına veya Azure dosyalarına dağıtabilirsiniz. Daha sonra `dtinstall`, `dtutil`ve `dtexec` komut satırı araçlarını kullanarak bunları Azure-SSIS IR çalıştırabilirsiniz. Daha fazla bilgi için bkz. [SSIS paketlerini dağıtma](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Her iki durumda da, Data Factory işlem hatlarında SSIS paketi yürütme etkinliğini kullanarak Azure-SSIS IR dağıtılan paketlerinizi çalıştırabilirsiniz. Daha fazla bilgi için bkz. [birinci sınıf Data Factory etkinliği olarak SSIS paketi yürütmeyi çağırma](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Sonraki adımlar

Bu belgelerde diğer Azure-SSIS IR konularına bakın:

- [Azure-SSIS tümleştirme çalışma zamanı](concepts-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede, Azure-SSIS IR dahil olmak üzere genel olarak tümleştirme çalışma zamanları hakkında bilgi sağlanır.
- [Azure-SSIS IR’yi izleme](monitor-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede, Azure-SSIS IR hakkındaki bilgileri nasıl alacağınızı ve anlayacağınızı görebilirsiniz.
- [Azure-SSIS IR’yi yönetme](manage-azure-ssis-integration-runtime.md). Bu makalede Azure-SSIS IR nasıl durdurulacağı, başlatılacağı veya silineceği gösterilmektedir. Ayrıca, daha fazla düğüm ekleyerek Azure-SSIS IR nasıl ölçeklenekullanacağınızı gösterir.
- [Azure-SSIS IR’yi bir sanal ağa ekleyin](join-azure-ssis-integration-runtime-virtual-network.md). Bu makalede, Azure-SSIS IR bir sanal ağa katılma hakkında bilgi sağlanır.
