---
title: "Öğretici: SQL Server çevrimiçi SQL yönetilen örneği 'ne geçirme"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni kullanarak SQL Server Azure SQL yönetilen örneğine çevrimiçi geçiş gerçekleştirmeyi öğrenin.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/04/2020
ms.openlocfilehash: 5585eb210b54dfa016d25c430256508e1b0b9f61
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076896"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>Öğretici: DMS kullanarak Azure SQL yönetilen örneği 'ne çevrimiçi SQL Server geçirme

Azure veritabanı geçiş hizmeti 'ni kullanarak veritabanlarını bir SQL Server örneğinden [Azure SQL yönetilen örneğine](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) en az kapalı kalma süresiyle geçirebilirsiniz. Bazı el ile çaba gerektirebilecek ek yöntemler için bkz. [Azure SQL yönetilen örneği 'ne örnek geçişi SQL Server](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md)makalesi.

Bu öğreticide, Azure veritabanı geçiş hizmeti 'ni kullanarak bir SQL Server şirket içi örneğinden bir SQL yönetilen örneğine **Adventureworks2012** veritabanını geçirmiş olursunuz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> * Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun ve çevrimiçi geçiş başlatın.
> * Geçişi izleme.
> * Hazırsanız geçiş geçişini gerçekleştirin.

> [!IMPORTANT]
> Azure veritabanı geçiş hizmeti 'ni kullanarak SQL Server ile SQL yönetilen örneğine çevrimiçi geçişler için, tam veritabanı yedeklemesini ve hizmetin veritabanlarınızı geçirmek için kullanabileceği SMB ağ paylaşımında sonraki günlük yedeklemelerini sağlamanız gerekir. Azure veritabanı geçiş hizmeti herhangi bir yedekleme başlatmaz ve bunun yerine, geçiş için olağanüstü durum kurtarma planınız kapsamında olabilecek mevcut yedeklemeleri kullanır.
> [Sağlama toplamı seçeneğini kullanarak yedeklemeler](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017)aldığınızdan emin olun. Ayrıca, birden çok yedeklemeyi (yani tam ve t-log) tek bir yedekleme medyasına eklemediğinizden emin olun; Her bir yedeklemeyi ayrı bir yedekleme dosyasında alın. Son olarak, büyük yedeklemeleri geçirmeye ilişkin olası sorunların oluşma olasılığını azaltmak için sıkıştırılmış yedeklemeleri kullanabilirsiniz.

> [!NOTE]
> Çevrimiçi bir geçiş gerçekleştirmek için Azure veritabanı geçiş hizmeti 'nin kullanılması, Premium fiyatlandırma katmanını temel alan bir örnek oluşturulmasını gerektirir.

> [!IMPORTANT]
> En iyi geçiş deneyimi için, Microsoft, hedef veritabanıyla aynı Azure bölgesinde Azure veritabanı geçiş hizmeti örneği oluşturulmasını önerir. Verileri bölgeler veya coğrafyalar arasında taşımak, geçiş sürecini yavaşlatabilir ve hatalara neden olabilir.

> [!IMPORTANT]
> Örnek yeniden yapılandırması veya planlı bakım nedeniyle kesintiye uğrama riskini en aza indirmek için çevrimiçi geçiş işleminin süresini olabildiğince azaltın. Böyle bir olay söz konusu olduğunda, geçiş işlemi baştan başlar. Planlı bakım durumunda, geçiş işleminin yeniden başlatılması için 36 saatlik bir yetkisiz kullanım süresi vardır.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Bu makalede, SQL Server bir SQL yönetilen örneğine çevrimiçi geçiş açıklanmaktadır. Çevrimdışı geçiş için bkz. [DMS kullanarak SQL Server SQL yönetilen örneğine çevrimdışı geçirme](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure sanal ağ oluşturun. [Azure veritabanı geçiş hizmeti 'ni kullanarak SQL yönetilen örnek geçişleri için ağ topolojilerini öğrenin](./resource-network-topologies.md). Sanal ağ oluşturma hakkında daha fazla bilgi için [sanal ağ belgelerine](../virtual-network/index.yml)ve özellikle adım adım ayrıntılarla birlikte hızlı başlangıç makalelerine bakın.

    > [!NOTE]
    > Sanal ağ kurulumu sırasında, Microsoft 'a ağ eşlemesi ile ExpressRoute kullanırsanız, hizmetin sağlanacağı alt ağa aşağıdaki hizmet [uç noktalarını](../virtual-network/virtual-network-service-endpoints-overview.md) ekleyin:
    >
    > * Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
    > * Depolama uç noktası
    > * Service Bus uç noktası
    >
    > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.
    >
    >Şirket içi ağ ile Azure arasında siteden siteye bağlantınız yoksa veya siteden siteye bağlantı bant genişliği varsa, Azure veritabanı geçiş hizmeti 'ni karma modda (Önizleme) kullanmayı göz önünde bulundurun. Karma mod, bulutta çalışan bir Azure veritabanı geçiş hizmeti örneğiyle birlikte Şirket içi geçiş çalışanından yararlanır. Karma modda Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturmak için [Azure Portal kullanarak karma modda Azure veritabanı geçiş hizmeti örneği oluşturma](./quickstart-create-data-migration-service-portal.md)makalesine bakın.

    > [!IMPORTANT]
    > Geçişin bir parçası olarak kullanılan depolama hesabı ile ilgili olarak şunlardan birini yapmanız gerekir:
    > * Tüm ağın depolama hesabına erişmesine izin vermeyi seçin.
    > * Mı alt ağında [alt ağ temsilcisini](../virtual-network/manage-subnet-delegation.md) açın ve bu alt ağa izin vermek Için depolama hesabı güvenlik duvarı kurallarını güncelleştirin.

* Sanal ağ ağ güvenlik grubu kurallarınızın ServiceBus, Storage ve AzureMonitor için ServiceTag giden bağlantı noktası 443 ' i engellemediğinden emin olun. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
* [Windows Güvenlik Duvarınızı kaynak veritabanı altyapısı erişimi](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
* Azure veritabanı geçiş hizmeti 'nin varsayılan olarak TCP bağlantı noktası 1433 olan kaynak SQL Server erişmesine izin vermek için Windows Güvenlik duvarınızı açın. Varsayılan örneğiniz başka bir bağlantı noktasında dinliyorsa, bunu güvenlik duvarına ekleyin.
* Dinamik bağlantı noktaları kullanarak birden çok adlandırılmış SQL Server örneği çalıştırıyorsanız, Azure veritabanı geçiş hizmeti 'nin kaynak sunucunuzdaki adlandırılmış bir örneğe bağlanabilmesi için SQL Browser hizmetini etkinleştirmek ve güvenlik duvarlarınız aracılığıyla UDP bağlantı noktası 1434 erişimine izin vermek isteyebilirsiniz.
* Kaynak veritabanlarınızın önünde bir güvenlik duvarı gereci kullanıyorsanız, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanlarına, ayrıca SMB bağlantı noktası 445 üzerinden dosyalara erişmesine izin vermek için güvenlik duvarı kuralları eklemeniz gerekebilir.
* [Azure Portal BIR SQL yönetilen örneği oluşturma](../azure-sql/managed-instance/instance-create-quickstart.md)makalesindeki ayrıntıyı IZLEYEREK bir SQL yönetilen örneği oluşturun.
* Kaynak SQL Server bağlamak için kullanılan oturum açma bilgilerinin ve hedef SQL yönetilen örneğinin sysadmin sunucu rolünün üyesi olduğundan emin olun.
* Veritabanı geçişi için Azure veritabanı geçiş hizmeti 'nin kullanabileceği tüm veritabanınızın tam veritabanı yedekleme dosyalarını ve sonraki işlem günlüğü yedekleme dosyalarını içeren bir SMB ağ paylaşımının sağlanması.
* Kaynak SQL Server örneğini çalıştıran hizmet hesabının oluşturduğunuz ağ paylaşımında yazma ayrıcalıklarına sahip olduğundan ve kaynak sunucunun bilgisayar hesabının aynı paylaşımda okuma/yazma erişimine sahip olduğundan emin olun.
* Önceden oluşturduğunuz ağ paylaşımında tam denetim ayrıcalığına sahip olan Windows kullanıcısını (ve parolasını) not edin. Azure veritabanı geçiş hizmeti, yedekleme dosyalarını geri yükleme işlemi için Azure depolama kapsayıcısına yüklemek üzere Kullanıcı kimlik bilgisini taklit eder.
* Azure veritabanı geçiş hizmeti 'nin hedef Azure veritabanı yönetilen örneği ve Azure depolama kapsayıcısına bağlanmak için kullanabileceği uygulama KIMLIĞI anahtarını üreten bir Azure Active Directory uygulama KIMLIĞI oluşturun. Daha fazla bilgi için bkz. [Kaynaklara erişebilen bir Azure Active Directory uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md).

  > [!NOTE]
  > Azure veritabanı geçiş hizmeti, belirtilen uygulama KIMLIĞI için abonelikte katkıda bulunan izni gerektirir. Alternatif olarak, Azure veritabanı geçiş hizmeti 'nin gerektirdiği belirli izinleri veren özel roller de oluşturabilirsiniz. Özel rolleri kullanma hakkında adım adım yönergeler için, [SQL yönetilen örnek çevrimiçi geçişleri SQL Server Için özel roller](./resource-custom-roles-sql-db-managed-instance.md)makalesine bakın.

* DMS hizmetinin veritabanı yedekleme dosyalarını yükleyebileceği ve veritabanlarını geçirmek için kullanabileceği bir **Standart Performans katmanı** Azure Depolama Hesabı oluşturun veya belirtin.  Azure veritabanı geçiş hizmeti örneği oluşturulduğu bölgede Azure Storage hesabını oluşturmayı unutmayın.

  > [!NOTE]
  > [Saydam veri şifrelemesi](../azure-sql/database/transparent-data-encryption-tde-overview.md) tarafından korunan bir veritabanını çevrimiçi geçiş kullanarak yönetilen bir örneğe geçirdiğinizde, şirket Içi veya Azure VM SQL Server örneğinden karşılık gelen sertifikanın, veritabanı geri yüklemeden önce geçirilmesi gerekir. Ayrıntılı adımlar için bkz. [BIR TDE sertifikayı yönetilen örneğe geçirme](../azure-sql/database/transparent-data-encryption-tde-overview.md).

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

    ![Portal aboneliklerini gösterme](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Veritabanı Geçiş Hizmeti örneğini oluşturma

1. Azure portalda +**Kaynak oluştur**'u seçin, **Azure Veritabanı Geçiş Hizmeti** araması yapın ve açılan listeden **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

     ![Azure Market](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. **Geçiş Hizmeti oluşturun** ekranında hizmet için bir ad belirtin, aboneliği ve yeni ya da var olan bir kaynak grubunu seçin.

4. DMS örneğini oluşturmak istediğiniz konumu seçin.

5. Var olan bir sanal ağı seçin veya bir tane oluşturun.

    Sanal ağ, Azure veritabanı geçiş hizmeti 'ni, kaynak SQL Server ve hedef SQL yönetilen örneği erişimi sağlar.

    Azure portal bir sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

    Daha fazla ayrıntı için [Azure veritabanı geçiş hizmeti 'ni kullanarak SQL yönetilen örnek geçişleri Için ağ topolojileri](./resource-network-topologies.md)makalesine bakın.

6. Premium fiyatlandırma katmanından bir SKU seçin.

    > [!NOTE]
    > Çevrimiçi geçişler yalnızca Premium katmanı kullanılırken desteklenir.

    Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![DMS hizmetini başlatma](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Hizmeti oluşturmak için **Oluştur**’u seçin.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmetin bir örneği oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

    ![Azure veritabanı geçiş hizmeti 'nin tüm örneklerini bulun](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. **Azure Veritabanı Geçiş Hizmeti ekranında** oluşturduğunuz örneğin adını arayın ve sonuçlardan bu örneği seçin.

3. +**Yeni Geçiş Projesi**'ni seçin.

4. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** metin kutusunda **SQL Server**' i seçin, **hedef sunucu türü** metin kutusunda, **Azure SQL yönetilen örneği**' ni seçin ve ardından **etkinlik türü seç**' i seçin, **çevrimiçi veri geçişi**' ni seçin.

   ![Azure veritabanı geçiş hizmeti projesi oluştur](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Projeyi oluşturmak için **Etkinlik oluştur ve çalıştır**'ı seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

1. **Geçiş kaynağı ayrıntıları** ekranında SQL Server bağlantı ayrıntılarını belirtin.

2. Sunucunuza bir güvenilir sertifika yüklemediyseniz **Sunucu sertifikasına güven** onay kutusunu işaretleyin.

    Güvenilir sertifika yüklü değilse SQL Server, örnek başlatıldığında otomatik olarak imzalanan bir sertifika oluşturur. Bu sertifika, istemci bağlantılarında kimlik bilgilerini şifrelemek için kullanılır.

    > [!CAUTION]
    > Kendinden imzalı bir sertifika kullanılarak şifrelenen TLS bağlantıları güçlü güvenlik sağlamaz. Ortadaki adam saldırılarına maruz kalabilirler. Bir üretim ortamında veya internet 'e bağlı sunucularda, otomatik olarak imzalanan sertifikalar kullanarak TLS 'ye güvenmemelisiniz.

   ![Kaynak Ayrıntıları](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. **Kaydet**’i seçin.

4. **Kaynak veritabanlarını seçin** ekranında geçiş için **Adventureworks2012** veritabanını seçin.

   ![Kaynak veritabanlarını seçme](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > SQL Server Integration Services (SSIS) kullanıyorsanız, DMS Şu anda SSIS projeleriniz/paketleriniz (SSıSDB) için katalog veritabanını SQL Server SQL yönetilen örneğine geçirmeyi desteklememektedir. Ancak, SSIS 'yi Azure Data Factory (ADF) olarak temin edebilir ve SSIS projelerinizi/paketlerinizi SQL yönetilen örneği tarafından barındırılan hedef SSıSDB 'ye yeniden dağıtabilirsiniz. SSIS paketlerini geçirme hakkında daha fazla bilgi için [SQL Server Integration Services paketlerini Azure 'A geçirme](./how-to-migrate-ssis-packages.md)makalesine bakın.

5. **Kaydet**’i seçin.

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Geçiş hedefi ayrıntıları** EKRANıNDA, DMS örneğinin SQL yönetilen örneği ve Azure depolama hesabı 'nın hedef örneğine bağlanmak Için KULLANABILECEĞI **uygulama kimliğini** ve **anahtarını** belirtin.

    Daha fazla bilgi için bkz. [Kaynaklara erişebilen bir Azure Active Directory uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md).

2. SQL yönetilen örneği 'nin hedef örneğini içeren **aboneliği** seçin ve ardından hedef örneği seçin.

    SQL yönetilen örneğini henüz sağlamadıysanız, örneği sağlamanıza yardımcı olacak [bağlantıyı](../azure-sql/managed-instance/instance-create-quickstart.md) seçin. SQL yönetilen örneği hazırsanız, geçişi yürütmek için bu belirli projeye dönün.

3. SQL Managed örneğine bağlanmak için **SQL kullanıcısını** ve **parolayı** belirtin.

    ![Hedef seçme](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. **Kaydet**’i seçin.

## <a name="select-source-databases"></a>Kaynak veritabanlarını seçme

1. **Kaynak veritabanlarını seçin** ekranında geçirmek istediğiniz kaynak veritabanını seçin.

    ![Kaynak veritabanlarını seçme](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. **Kaydet**’i seçin.

## <a name="configure-migration-settings"></a>Geçiş ayarlarını yapılandırma

1. **Geçiş ayarlarını yapılandırma** ekranında şu bilgileri girin:

    | Parametre | Açıklama |
    |--------|---------|
    |**SMB Ağ konumu paylaşımı** | Azure veritabanı geçiş hizmeti 'nin geçiş için kullanabileceği tam veritabanı yedekleme dosyalarını ve işlem günlüğü yedekleme dosyalarını içeren yerel SMB ağ veya Azure dosya paylaşımıdır. Kaynak SQL Server örneğini çalıştıran hizmet hesabının ağ paylaşımında okuma/yazma ayrıcalıkları olmalıdır. Ağ paylaşımındaki bir sunucunun FQDN veya IP adresi değerini girin, örneğin: '\\\sunucuadi.etkialaniadi.com\yedeklemeklasoru' veya '\\\IP adresi\yedeklemeklasoru'. Daha iyi performans için, geçirilecek her veritabanı için ayrı bir klasör kullanılması önerilir. Veritabanı düzeyinde dosya paylaşımının yolunu, **Gelişmiş ayarlar** seçeneğini kullanarak sağlayabilirsiniz. SMB paylaşımıyla bağlantı kurma sorunları yaşıyorsanız, bkz. [SMB paylaşma](known-issues-azure-sql-db-managed-instance-online.md#smb-file-share-connectivity). |
    |**Kullanıcı adı** | Windows kullanıcısının yukarıda belirttiğiniz ağ paylaşımında tam denetim ayrıcalığına sahip olduğundan emin olun. Azure veritabanı geçiş hizmeti, yedekleme dosyalarını geri yükleme işlemi için Azure depolama kapsayıcısına yüklemek üzere Kullanıcı kimlik bilgisinin kimliğine bürünecektir. Azure dosya paylaşımının kullanılması durumunda Kullanıcı adı olarak AZURE \ ile önceden tamamlandı depolama hesabı adını kullanın. |
    |**Parola** | Kullanıcının parolası. Azure dosya paylaşımının kullanılması durumunda, parola olarak bir depolama hesabı anahtarı kullanın. |
    |**Azure Depolama Hesabının aboneliği** | Azure Depolama Hesabını içeren aboneliği seçin. |
    |**Azure depolama hesabı** | DMS'nin SMB ağ paylaşımındaki yedekleme dosyalarını yükleyebileceği ve veritabanı geçişi için kullanabileceği Azure Depolama Hesabını seçin.  En iyi dosya yükleme performansı için DMS hizmetiyle aynı bölgede bir Depolama Hesabı seçmenizi öneririz. |

    ![Geçiş Ayarlarını Yapılandırma](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Azure veritabanı geçiş hizmeti hata ' sistem hatası 53 ' veya ' sistem hatası 57 ' gösteriyorsa, neden Azure veritabanı geçiş hizmeti 'nin Azure dosya paylaşımında erişime neden olabilir. Bu hatalardan biriyle karşılaşırsanız lütfen [buradaki](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)yönergeleri kullanarak sanal ağdan depolama hesabına erişim izni verin.

    > [!IMPORTANT]
    > Geri döngü denetimi işlevselliği etkinse ve kaynak SQL Server ile dosya paylaşımı aynı bilgisayarındaysa, kaynak FQDN kullanılarak paylaşım dosyalarına erişemez. Bu sorunu onarmak için [buradaki](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd)yönergeleri kullanarak geri döngü denetim işlevlerini devre dışı bırakın.

2. **Kaydet**’i seçin.

## <a name="review-the-migration-summary"></a>Geçiş özetini gözden geçirme

1. **Geçiş özeti** ekranının **Etkinlik adı** metin kutusunda geçiş etkinliği için bir ad belirtin.

2. Geçiş projesiyle ilgili ayrıntıları gözden geçirin ve doğrulayın.

    ![Geçiş projesi özeti](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Geçişi çalıştırma ve izleme

1. **Geçişi çalıştır**'ı seçin.

2. Geçiş etkinliği ekranını güncelleştirmek için **Yenile**'yi seçin.

   ![Geçiş etkinliği sürüyor](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    İlgili sunucu nesnelerinin geçiş durumunu izlemek için veritabanları ve oturum açma işlemleri kategorilerini genişletebilirsiniz.

   ![Geçiş etkinliği durumu](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Tam geçişi gerçekleştirme

Tam veritabanı yedeklemesi SQL yönetilen örneğinin hedef örneğine geri yüklendikten sonra, veritabanı geçiş cutover işlemini gerçekleştirmek için kullanılabilir.

1. Çevrimiçi veritabanı geçişini tamamlamaya hazır olduğunuzda **Tam Geçişi Başlat**'ı seçin.

2. Kaynak veritabanlarına gelen tüm trafiği durdurun.

3. [Sonradan alınan günlük yedeğini] alın, yedekleme dosyasını SMB ağ paylaşımına yerleştirin ve bu son işlem günlüğü yedeği geri yüklenene kadar bekleyin.

    Bu noktada **Bekleyen değişiklikler** 0 olmalıdır.

4. **Onayla**'yı ve ardından, **Uygula**'yı seçin.

    ![Tam geçişi tamamlamaya hazırlanma](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

    > [!IMPORTANT]
    > Cutover 'den sonra, SQL yönetilen örneği 'nin İş Açısından Kritik hizmet katmanı ile kullanılabilirliği yalnızca, en az bir adet ikincil çoğaltmanın AlwaysOn yüksek kullanılabilirlik grubu için sunulması gereken Genel Amaçlı önemli ölçüde daha uzun sürebilir. Bu işlem süresi verilerin boyutuna bağlıdır, daha fazla bilgi için bkz. [yönetim işlemleri süresi](../azure-sql/managed-instance/management-operations-overview.md#duration).

5. Veritabanı geçiş durumu **tamamlandı** olarak görüntülendiğinde, uygulamalarınızı SQL yönetilen örneği 'nin yeni hedef örneğine bağlayın.

    ![Tam geçiş tamamlandı](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Sonraki adımlar

* T-SQL RESTORE komutunu kullanarak bir veritabanının SQL yönetilen örneği 'ne nasıl geçirileceğini gösteren bir öğretici için [restore komutunu kullanarak BIR SQL yönetilen örneğine bir yedeklemeyi geri yükleme](../azure-sql/managed-instance/restore-sample-database-quickstart.md)bölümüne bakın.
* SQL yönetilen örneği hakkında daha fazla bilgi için bkz. [SQL yönetilen örneği nedir](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
* Uygulamaları SQL yönetilen örneğine bağlama hakkında daha fazla bilgi için bkz. [uygulamaları bağlama](../azure-sql/managed-instance/connect-application-instance.md).
