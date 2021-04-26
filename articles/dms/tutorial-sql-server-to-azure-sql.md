---
title: "Öğretici: SQL Server Azure SQL veritabanı 'na çevrimdışı geçirme"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni kullanarak SQL Server 'den Azure SQL veritabanı 'na geçiş yapmayı öğrenin.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/03/2021
ms.openlocfilehash: 0330ea419b92d4e2e85c01e2770ec0d92987c4d2
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107117"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-using-dms"></a>Öğretici: DMS kullanarak Azure SQL veritabanı 'na SQL Server geçirme

Azure veritabanı geçiş hizmeti 'ni kullanarak veritabanlarını bir SQL Server örneğinden [Azure SQL veritabanı](/azure/sql-database/)'na geçirebilirsiniz. Bu öğreticide, Azure veritabanı geçiş hizmeti 'ni kullanarak Azure SQL veritabanı 'nda SQL Server 2016 (veya üzeri) bir şirket içi örneğine geri yüklenmiş [Adventureworks2016](/sql/samples/adventureworks-install-configure#download-backup-files) veritabanını geçireceğiniz.

Şunları öğrenirsiniz:
> [!div class="checklist"]
>
> - Data Migration Yardımcısı kullanarak, herhangi bir engelleme sorunu için şirket içi veritabanınızı değerlendirin ve değerlendirin.
> - Veritabanı örnek şemasını geçirmek için Data Migration Yardımcısı kullanın. 
> - Azure DataMigration kaynak sağlayıcısını kaydedin.
> - Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> - Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun.
> - Geçişi çalıştırma.
> - Geçişi izleme.


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

- [SQL Server 2016 veya sonraki bir sürümü](https://www.microsoft.com/sql-server/sql-server-downloads)indirin ve yükleyin.
- [Sunucu Ağ Protokolünü Etkinleştirme veya Devre Dışı Bırakma](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) makalesindeki yönergeleri izleyerek SQL Server Express yüklemesi sırasında varsayılan olarak devre dışı bırakılan TCP/IP protokolünü etkinleştirin.
- Azure SQL veritabanı 'nda, [Azure Portal kullanarak Azure SQL veritabanı 'nda veritabanı oluşturma](../azure-sql/database/single-database-create-quickstart.md)makalesindeki ayrıntıları izleyerek yaptığınız bir veritabanı oluşturun. Bu öğreticide Azure SQL Veritabanı’nın adının **AdventureWorksAzure** olduğu kabul edilmiştir, ancak istediğiniz adı kullanabilirsiniz.

    > [!NOTE]
    > SQL Server Integration Services (SSIS) kullanıyorsanız ve SSIS projeleriniz/paketleriniz (SSıSDB) için katalog veritabanını SQL Server Azure SQL veritabanı 'na geçirmek istiyorsanız, SSIS 'yi Azure Data Factory (ADF) sağladığınızda, hedef SSSıSDB sizin adınıza otomatik olarak oluşturulur ve yönetilir. SSIS paketlerini geçirme hakkında daha fazla bilgi için [SQL Server Integration Services paketlerini Azure 'A geçirme](./how-to-migrate-ssis-packages.md)makalesine bakın.
  
- [Data Migration Yardımcısı](https://www.microsoft.com/download/details.aspx?id=53595)en son sürümünü indirip yükleyin.
- [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure sanal ağ oluşturun. Sanal ağ oluşturma hakkında daha fazla bilgi için [sanal ağ belgelerine](../virtual-network/index.yml)ve özellikle adım adım ayrıntılarla birlikte hızlı başlangıç makalelerine bakın.

    > [!NOTE]
    > Sanal ağ kurulumu sırasında, Microsoft 'a ağ eşlemesi ile ExpressRoute kullanırsanız, hizmetin sağlanacağı alt ağa aşağıdaki hizmet [uç noktalarını](../virtual-network/virtual-network-service-endpoints-overview.md) ekleyin:
    >
    > - Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
    > - Depolama uç noktası
    > - Service Bus uç noktası
    >
    > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.
    >
    >Şirket içi ağ ile Azure arasında siteden siteye bağlantınız yoksa veya siteden siteye bağlantı bant genişliği varsa, Azure veritabanı geçiş hizmeti 'ni karma modda (Önizleme) kullanmayı göz önünde bulundurun. Karma mod, bulutta çalışan bir Azure veritabanı geçiş hizmeti örneğiyle birlikte Şirket içi geçiş çalışanından yararlanır. Karma modda Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturmak için [Azure Portal kullanarak karma modda Azure veritabanı geçiş hizmeti örneği oluşturma](./quickstart-create-data-migration-service-hybrid-portal.md)makalesine bakın.

- Sanal ağ ağ güvenlik grubunuzun giden güvenlik kurallarınızın ServiceBus, Storage ve AzureMonitor için ServiceTag giden bağlantı noktası 443 ' ü engellemediğinden emin olun. Azure sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
- [Windows Güvenlik Duvarınızı veritabanı altyapısı erişimi](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
- Azure veritabanı geçiş hizmeti 'nin varsayılan olarak TCP bağlantı noktası 1433 olan kaynak SQL Server erişmesine izin vermek için Windows Güvenlik duvarınızı açın. Varsayılan örneğiniz başka bir bağlantı noktasında dinliyorsa, bunu güvenlik duvarına ekleyin.
- Dinamik bağlantı noktaları kullanarak birden çok adlandırılmış SQL Server örneği çalıştırıyorsanız, Azure veritabanı geçiş hizmeti 'nin kaynak sunucunuzdaki adlandırılmış bir örneğe bağlanabilmesi için SQL Browser hizmetini etkinleştirmek ve güvenlik duvarlarınız aracılığıyla UDP bağlantı noktası 1434 erişimine izin vermek isteyebilirsiniz.
- Kaynak veritabanınızın önünde bir güvenlik duvarı gereci kullanırken, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanına erişmesine izin vermek üzere güvenlik duvarı kuralları eklemeniz gerekebilir.
- Azure veritabanı geçiş hizmeti 'nin hedef veritabanlarına erişmesine izin vermek için, Azure SQL veritabanı için sunucu düzeyinde bir IP [güvenlik duvarı kuralı](../azure-sql/database/firewall-configure.md) oluşturun. Azure veritabanı geçiş hizmeti için kullanılan sanal ağın alt ağ aralığını belirtin.
- SQL Server örneğine bağlanmak için kullanılan kimlik bilgilerinin [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql) izinlerine sahip olduğundan emin olun.
- Azure SQL veritabanı örneğine bağlanmak için kullanılan kimlik bilgilerinin hedef veritabanlarında [DENETIM veritabanı](/sql/t-sql/statements/grant-database-permissions-transact-sql) iznine sahip olduğundan emin olun.

    > [!IMPORTANT]
    > Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturmak, normalde aynı kaynak grubu içinde olmayan sanal ağ ayarlarına erişim gerektirir. Sonuç olarak, DMS 'nin bir örneğini oluşturan kullanıcı abonelik düzeyinde izin gerektirir. Gerektiği şekilde atayabileceğiniz gerekli rolleri oluşturmak için aşağıdaki betiği çalıştırın:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="assess-your-on-premises-database"></a>Şirket içi veritabanınızı değerlendirme

Verileri bir SQL Server örneğinden Azure SQL veritabanı 'nda tek bir veritabanına veya havuza alınmış bir veritabanına geçirebilmeniz için, geçişi engelleyebilen herhangi bir engelleyici sorun için SQL Server veritabanını değerlendirmenize gerek duyarsınız. Data Migration Yardımcısı kullanarak, şirket içi veritabanı değerlendirmesini tamamlamaya yönelik [SQL Server geçiş değerlendirmesi gerçekleştirme](/sql/dma/dma-assesssqlonprem) makalesinde açıklanan adımları izleyin. Yapılması gereken adımların özeti aşağıda verilmiştir:

1. Data Migration Yardımcısı'nda Yeni (+) simgesini ve **Değerlendirme** proje türünü seçin.
2. Bir proje adı belirtin. **Değerlendirme türü** açılan listesinde, **veritabanı altyapısı**' nı seçin, **kaynak sunucu türü** metin kutusunda **SQL Server**' i seçin, **hedef sunucu türü** metin kutusunda, **Azure SQL veritabanı**' nı seçin ve ardından **Oluştur** ' u seçerek projeyi oluşturun.

    Kaynak SQL Server veritabanının Azure SQL veritabanı 'nda tek bir veritabanına veya havuza alınmış veritabanına geçirilmesini değerlendirmek için aşağıdaki değerlendirme raporu türlerinden birini veya her ikisini de seçebilirsiniz:

   - Veritabanı uyumluluğunu denetle
   - Özellik eşliğini denetle

    İki rapor türü de varsayılan olarak seçilidir.

3. Data Migration Yardımcısı'nın **Seçenekler** ekranında **İleri**'yi seçin.
4. **Kaynak seçin** ekranının **Sunucuya bağlan** iletişim kutusunda SQL Server bağlantı bilgilerini girin ve **Bağlan**'ı seçin.
5. **Kaynak Ekle** Iletişim kutusunda **Adventureworks2016** öğesini seçin, **Ekle**' yi seçin ve **değerlendirmeyi Başlat**' ı seçin.

    > [!NOTE]
    > SSIS kullanıyorsanız, DMA Şu anda kaynak SSSıSDB değerlendirmesini desteklememektedir. Ancak, SSIS projeleri/paketleri Azure SQL veritabanı tarafından barındırılan hedef SSıSDB 'ye yeniden dağıtıldıklarında değerlendirilecek/doğrulanacak. SSIS paketlerini geçirme hakkında daha fazla bilgi için [SQL Server Integration Services paketlerini Azure 'A geçirme](./how-to-migrate-ssis-packages.md)makalesine bakın.

    Değerlendirme tamamlandığında aşağıdaki grafiğe benzer sonuçlar görüntülenir:

    ![Veri geçişi değerlendirmesi](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Azure SQL veritabanındaki veritabanları için değerlendirmeler, tek bir veritabanına veya havuza alınmış veritabanına dağıtım için özellik eşlik sorunlarını ve geçiş engelleme sorunlarını belirler.

    - **SQL Server özellik eşliği** kategorisi kapsamlı öneriler, Azure'daki alternatif yaklaşımlar ve geçiş projelerini planlama konusunda yardımcı olacak çıkarılabilecek adımlar sunar.
    - **Uyumluluk sorunları** kategorisi, SQL Server VERITABANLARıNı Azure SQL veritabanı 'na geçirmeyi engelleyebilen uyumluluk sorunlarını yansıtan kısmen desteklenen veya desteklenmeyen özellikleri tanımlar. Bu sorunları gidermenize yardımcı olan öneriler de sağlanır.

6. İlgili seçenekleri belirleyerek değerlendirme sonuçlarındaki geçiş engelleyici sorunları ve özellik eşliği sorunlarını gözden geçirin.

## <a name="migrate-the-sample-schema"></a>Örnek şemayı geçirme

Değerlendirmeyi rahatladıktan ve seçilen veritabanının Azure SQL veritabanı 'nda tek bir veritabanına veya havuza alınmış veritabanına geçiş için uygun bir aday olduğunu karşıladıktan sonra, şemayı Azure SQL veritabanı 'na geçirmek için DMA 'yı kullanın.

> [!NOTE]
> Data Migration Yardımcısı ' de bir geçiş projesi oluşturmadan önce, önkoşullardan bahsedildiği gibi Azure 'da bir veritabanı sağladığınızdan emin olun. 

> [!IMPORTANT]
> SSIS kullanıyorsanız, DMA Şu anda kaynak SSSıSDB geçişini desteklememektedir, ancak SSIS projelerinizi/paketlerinizi Azure SQL veritabanı tarafından barındırılan hedef SSıSDB 'ye yeniden dağıtabilirsiniz. SSIS paketlerini geçirme hakkında daha fazla bilgi için [SQL Server Integration Services paketlerini Azure 'A geçirme](./how-to-migrate-ssis-packages.md)makalesine bakın.

**Adventureworks2016** şemasını tek bir veritabanına veya havuza alınmış VERITABANı Azure SQL veritabanı 'na geçirmek için aşağıdaki adımları uygulayın:

1. Data Migration Yardımcısı'nda Yeni (+) simgesini ve ardından **Proje türü** bölümünde **Geçiş**'i seçin.
2. Bir proje adı belirtin, **Kaynak sunucu türü** metin kutusunda **SQL Server**, **Hedef sunucu türü** metin kutusunda da **Azure SQL Veritabanı**'nı seçin.
3. **Geçiş Kapsamı** bölümünde **Yalnızca şema**'yı seçin.

    Yukarıdaki adımları gerçekleştirdikten sonra aşağıdaki görüntüde gösterildiği gibi Data Migration Yardımcısı arabiriminin görünmesi gerekir:

    ![Data Migration Yardımcısı Projesi Oluşturma](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Projeyi oluşturmak için **Oluştur**'u seçin.
5. Data Migration Yardımcısı, SQL Server için kaynak bağlantısı ayrıntılarını belirtin, **Bağlan**' ı seçin ve ardından **Adventureworks2016** veritabanını seçin.

    ![Data Migration Yardımcısı Kaynak Bağlantı Ayrıntıları](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. **İleri**' yi seçin, **hedef sunucuya Bağlan**' ın altında, Azure SQL veritabanı için hedef bağlantı ayrıntılarını belirtin, **Bağlan**' ı seçin ve ardından Azure SQL veritabanı 'nda önceden sağladığınız **AdventureWorksAzure** veritabanını seçin.

    ![Data Migration Yardımcısı Hedef Bağlantı Ayrıntıları](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. **Nesneleri seçme** ekranına Ilerlemek için **İleri** ' yi seçin, burada, Azure SQL veritabanı 'na dağıtılması gereken **Adventureworks2016** veritabanında şema nesnelerini belirtebilirsiniz.

    Varsayılan olarak, tüm nesneler seçilir.

    ![SQL Betiği Oluşturma](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. **SQL betiği oluştur**'u seçerek SQL betiklerini oluşturun ve hata olup olmadığını inceleyin.

    ![Şema Betiği](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. **Şemayı dağıtma**'yı seçerek şemayı Azure SQL Veritabanına dağıtın. Şema dağıtıldıktan sonra hedef sunucuda anomali olup olmadığını kontrol edin.

    ![Şemayı Dağıtma](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portalında oturum açın. **Abonelik** arayın ve seçin.

   ![Portal aboneliklerini gösterme](media/tutorial-sql-server-to-azure-sql/portal-select-subscription-1.png)

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Geçiş için arama yapın ve ardından **Microsoft. DataMigration** için **Kaydol** ' u seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Örnek oluşturma

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin. **Azure veritabanı geçiş hizmeti**'ni arayıp seçin.

    ![Azure Market](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-sql-server-to-azure-sql/dms-create-1.png)
  
3. **Geçiş hizmeti oluşturma** temelleri ekranında:

     - Aboneliği seçin.
     - Yeni bir kaynak grubu oluşturun veya var olan bir grubu seçin.
     - Azure veritabanı geçiş hizmeti örneği için bir ad belirtin.
     - Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz konumu seçin.
     - Hizmet modu olarak **Azure** ' ı seçin.
     - Fiyatlandırma katmanını seçin. Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![Azure veritabanı geçiş hizmeti örneği temelleri ayarlarını yapılandırma](media/tutorial-sql-server-to-azure-sql/dms-settings-2.png)

     - **Sonraki: Ağ**’ı seçin.

4. **Geçiş hizmeti oluştur** ağ ekranında:

    - Var olan bir sanal ağı seçin veya yeni bir ağ oluşturun. Sanal ağ, kaynak SQL Server ve hedef Azure SQL veritabanı örneğine erişimi olan Azure veritabanı geçiş hizmeti sağlar. Azure portal sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

    ![Azure veritabanı geçiş hizmeti örneği ağ ayarlarını yapılandırma](media/tutorial-sql-server-to-azure-sql/dms-settings-3.png)

    - Hizmeti oluşturmak için **gözden geçir + oluştur** ' u seçin.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmet oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.

1. Azure portal menüsünde **tüm hizmetler**' i seçin. **Azure veritabanı geçiş Hizmetleri**'ni arayın ve seçin.

     ![Azure veritabanı geçiş hizmeti 'nin tüm örneklerini bulun](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. **Azure veritabanı geçiş Hizmetleri** ekranında, oluşturduğunuz Azure veritabanı geçiş hizmeti örneğini seçin.

3. **Yeni geçiş projesi** seçin.

     ![Azure veritabanı geçiş hizmeti örneğinizi bulun](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** metin kutusunda **SQL Server**' i seçin, **hedef sunucu türü** metin kutusunda, **Azure SQL veritabanı**' nı seçin ve * * geçiş etkinlik türü * * seçin, **veri geçişi**' ni seçin.

    ![Veritabanı Geçiş Hizmeti Projesi Oluşturma](media/tutorial-sql-server-to-azure-sql/dms-create-project-2.png)

5. Projeyi oluşturmak ve geçiş etkinliğini çalıştırmak için **Etkinlik oluştur ve çalıştır**'ı seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

1. **Kaynak Seç** ekranında, kaynak SQL Server örneği için bağlantı ayrıntılarını belirtin.

    Kaynak SQL Server örneği adı için Tam Etki Alanı Adı (FQDN) kullandığınızdan emin olun. DNS ad çözümlemenin mümkün olmadığı durumlarda IP Adresini de kullanabilirsiniz.

2. Kaynak sunucunuza bir güvenilir sertifika yüklemediyseniz **Sunucu sertifikasına güven** onay kutusunu işaretleyin.

    Güvenilir sertifika yüklü değilse SQL Server, örnek başlatıldığında otomatik olarak imzalanan bir sertifika oluşturur. Bu sertifika, istemci bağlantılarında kimlik bilgilerini şifrelemek için kullanılır.

    > [!CAUTION]
    > Kendinden imzalı bir sertifika kullanılarak şifrelenen TLS bağlantıları güçlü güvenlik sağlamaz. Ortadaki adam saldırılarına maruz kalabilirler. Bir üretim ortamında veya internet 'e bağlı sunucularda, otomatik olarak imzalanan sertifikalar kullanarak TLS 'ye güvenmemelisiniz.

    > [!IMPORTANT]
    > SSIS kullanırsanız, DMS Şu anda kaynak SSSıSDB geçişini desteklememektedir, ancak SSIS projelerinizi/paketlerinizi Azure SQL veritabanı tarafından barındırılan hedef SSıSDB 'ye yeniden dağıtabilirsiniz. SSIS paketlerini geçirme hakkında daha fazla bilgi için [SQL Server Integration Services paketlerini Azure 'A geçirme](./how-to-migrate-ssis-packages.md)makalesine bakın.

   ![Kaynak Ayrıntıları](media/tutorial-sql-server-to-azure-sql/dms-source-details-2.png)
   
3. **İleri ' yi seçin: veritabanlarını seçin**.

## <a name="select-databases-for-migration"></a>Geçiş için veritabanları seçin

Azure SQL veritabanı 'na geçirmek istediğiniz tüm veritabanlarını veya belirli veritabanlarını seçin. DMS, seçili veritabanları için beklenen geçiş süresi sağlar. Geçiş daha fazla kabul edilebilir ise geçişe devam edin. Geçiş kapalı kalma süresi kabul edilemez ise, [SQL yönetilen örneğine neredeyse sıfır kapalı kalma süresi ile](tutorial-sql-server-managed-instance-online.md) geçiş yapmayı veya diğer seçenekler için [DMS ekibine](mailto:DMSFeedback@microsoft.com) başvurmayı düşünün. 

1. Kullanılabilir veritabanları listesinden geçirmek istediğiniz veritabanlarını seçin. 
1. Beklenen kesinti süresini gözden geçirin. Kabul edilebilir ise, Ileri ' yi seçin **: hedef >>seçin**

   ![Kaynak veritabanları](media/tutorial-sql-server-to-azure-sql/select-database.png)



## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Hedef Seç** ekranında, Azure SQL veritabanınıza kimlik doğrulama ayarları sağlayın. 

   ![Hedef seçin](media/tutorial-sql-server-to-azure-sql/select-target.png)
   
   > [!NOTE]
   > Şu anda, SQL kimlik doğrulaması desteklenen tek kimlik doğrulama türüdür.

1. Ileri ' yi seçin **: hedef veritabanlarına eşle** ekranı, kaynak ve hedef veritabanını geçiş için eşleyin.

    Hedef veritabanı, kaynak veritabanıyla aynı veritabanı adını içeriyorsa, Azure veritabanı geçiş hizmeti varsayılan olarak hedef veritabanını seçer.

    ![Hedef veritabanlarıyla eşleyin](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity-2.png)

1. Ileri ' yi seçin **: yapılandırma geçiş ayarları**, Tablo listesini Genişlet ve etkilenen alanların listesini gözden geçirin.

    Azure veritabanı geçiş hizmeti, hedef Azure SQL veritabanı örneğinde bulunan tüm boş kaynak tablolarını otomatik olarak seçer. Veri içeren tabloları yeniden geçirmek isterseniz bu dikey pencerede tabloları seçmeniz gerekir.

    ![Tabloları seçme](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity-2.png)

1. Ileri ' yi seçin **: Özet**, geçiş yapılandırmasını gözden geçirin ve **etkinlik adı** metin kutusunda, geçiş etkinliği için bir ad belirtin.

    ![Doğrulama seçeneğini belirleme](media/tutorial-sql-server-to-azure-sql/dms-configuration-2.png)

## <a name="run-the-migration"></a>Geçişi çalıştırma

- **Geçişi Başlat**' ı seçin.

    Geçiş etkinliği penceresi görünür ve etkinliğin **durumu** **Beklemede** olur.

    ![Etkinlik Durumu](media/tutorial-sql-server-to-azure-sql/dms-activity-status-1.png)

## <a name="monitor-the-migration"></a>Geçişi izleme

1. Geçiş etkinliği ekranında **Yenile**'yi seçerek geçişin **Durum** bilgisi **Tamamlandı** olana kadar gösterilen verileri güncelleştirebilirsiniz.

    ![Etkinlik Durumu Tamamlandı](media/tutorial-sql-server-to-azure-sql/dms-completed-activity-1.png)

2. Hedef **Azure SQL veritabanındaki** hedef veritabanlarını doğrulayın.

### <a name="additional-resources"></a>Ek kaynaklar

- Azure veritabanı geçiş hizmeti hakkında daha fazla bilgi için [Azure veritabanı geçiş hizmeti nedir?](./dms-overview.md)makalesine bakın.
- Azure SQL veritabanı hakkında daha fazla bilgi için [Azure SQL veritabanı hizmeti nedir?](../azure-sql/database/sql-database-paas-overview.md)makalesine bakın.
