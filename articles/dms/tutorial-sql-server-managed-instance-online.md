---
title: "Öğretici: Azure SQL veritabanı yönetilen örneğine SQL Server çevrimiçi geçişini gerçekleştirmek için Azure veritabanı geçiş hizmeti 'ni kullanma | Microsoft Docs"
description: Azure veritabanı geçiş hizmeti 'ni kullanarak şirket içi SQL Server Azure SQL veritabanı yönetilen örneğine çevrimiçi geçiş gerçekleştirmeyi öğrenin.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: 556fb2c1caf9c763cf5a63b71d3dd1e522104e1d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646956"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Öğretici: DMS kullanarak Azure SQL veritabanı yönetilen örneğine çevrimiçi olarak SQL Server geçirme

Azure veritabanı geçiş hizmeti 'ni kullanarak veritabanlarını şirket içi SQL Server örneğinden bir [Azure SQL veritabanı yönetilen örneğine](../sql-database/sql-database-managed-instance.md) en az kapalı kalma süresiyle geçirebilirsiniz. Bazı el ile efor gerektirebilecek ek yöntemler için bkz. [Azure SQL veritabanı yönetilen örneği 'ne örnek geçişi SQL Server](../sql-database/sql-database-managed-instance-migrate.md)makalesi.

Bu öğreticide, Azure veritabanı geçiş hizmeti 'ni kullanarak, **Adventureworks2012** veritabanını SQL Server şirket içi ÖRNEĞINDEN bir SQL veritabanı yönetilen örneğine geçirmiş olursunuz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturun.
> * Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun ve çevrimiçi geçiş başlatın.
> * Geçişi izleme.
> * Hazırsanız geçiş geçişini gerçekleştirin.

> [!IMPORTANT]
> Azure veritabanı geçiş hizmeti 'ni kullanarak SQL Server ile SQL veritabanı yönetilen örneği arasında çevrimiçi geçişler için, tam veritabanı yedeklemesini ve hizmetin veritabanlarınızı geçirmek için kullanabileceği SMB ağ paylaşımında sonraki günlük yedeklemelerini sağlamanız gerekir. Azure veritabanı geçiş hizmeti herhangi bir yedekleme başlatmaz ve bunun yerine, geçiş için olağanüstü durum kurtarma planınız kapsamında olabilecek mevcut yedeklemeleri kullanır.
> [Sağlama toplamı seçeneğini kullanarak yedeklemeler](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)aldığınızdan emin olun. Ayrıca, birden çok yedeklemeyi (yani tam ve t-log) tek bir yedekleme medyasına eklemediğinizden emin olun; Her bir yedeklemeyi ayrı bir yedekleme dosyasında alın. Son olarak, büyük yedeklemeleri geçirmeye ilişkin olası sorunların oluşma olasılığını azaltmak için sıkıştırılmış yedeklemeleri kullanabilirsiniz.

> [!NOTE]
> Çevrimiçi bir geçiş gerçekleştirmek için Azure veritabanı geçiş hizmeti 'nin kullanılması, Premium fiyatlandırma katmanını temel alan bir örnek oluşturulmasını gerektirir.

> [!IMPORTANT]
> En iyi geçiş deneyimi için, Microsoft, hedef veritabanıyla aynı Azure bölgesinde Azure veritabanı geçiş hizmeti örneği oluşturulmasını önerir. Verileri bölgeler veya coğrafyalar arasında taşımak, geçiş sürecini yavaşlatabilir ve hatalara neden olabilir.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Bu makalede, SQL Server bir SQL veritabanı yönetilen örneğine çevrimiçi geçiş açıklanmaktadır. Çevrimdışı geçiş için bkz. [DMS kullanarak SQL Server SQL veritabanı yönetilen örneğine çevrimdışı geçirme](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) veya VPN kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti Için bir Azure sanal ağı (VNet) oluşturun [ ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Azure [veritabanı geçiş hizmeti 'ni kullanarak Azure SQL veritabanı yönetilen örnek geçişleri için ağ topolojilerini öğrenin](https://aka.ms/dmsnetworkformi). VNet oluşturma hakkında daha fazla bilgi için, [sanal ağ belgelerine](https://docs.microsoft.com/azure/virtual-network/)ve özellikle de adım adım ayrıntılara sahip olan hızlı başlangıç makalelerine bakın.

    > [!NOTE]
    > VNet kurulumu sırasında, ExpressRoute 'u Microsoft 'a ağ eşlemesi ile kullanıyorsanız, aşağıdaki hizmet [uç noktalarını](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) hizmetin sağlanacağı alt ağa ekleyin:
    >
    > * Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
    > * Depolama uç noktası
    > * Service Bus uç noktası
    >
    > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.
    >
    >Şirket içi ağ ile Azure arasında siteden siteye bağlantınız yoksa veya siteden siteye bağlantı bant genişliği varsa, Azure veritabanı geçiş hizmeti 'ni karma modda (Önizleme) kullanmayı göz önünde bulundurun. Karma mod, bulutta çalışan bir Azure veritabanı geçiş hizmeti örneğiyle birlikte Şirket içi geçiş çalışanından yararlanır. Karma modda Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturmak için [Azure Portal kullanarak karma modda Azure veritabanı geçiş hizmeti örneği oluşturma](https://aka.ms/dms-hybrid-create)makalesine bakın.

    > [!IMPORTANT]
    > Geçişin bir parçası olarak kullanılan depolama hesabı ile ilgili olarak şunlardan birini yapmanız gerekir:
    > * Tüm ağın depolama hesabına erişmesine izin vermeyi seçin.
    > * VNet için ACL 'Leri ayarlayın. Daha fazla bilgi için [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](https://docs.microsoft.com/azure/storage/common/storage-network-security)makalesine bakın.

* VNet ağ güvenlik grubu kurallarınızın Azure veritabanı geçiş hizmeti 'ne yönelik aşağıdaki gelen iletişim bağlantı noktalarını engellemediğinden emin olun: 443, 53, 9354, 445, 12000. Azure VNet NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)makalesine bakın.
* [Windows Güvenlik Duvarınızı kaynak veritabanı altyapısı erişimi](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
* Azure veritabanı geçiş hizmeti 'nin varsayılan olarak TCP bağlantı noktası 1433 olan kaynak SQL Server erişmesine izin vermek için Windows Güvenlik duvarınızı açın.
* Dinamik bağlantı noktaları kullanarak birden çok adlandırılmış SQL Server örneği çalıştırıyorsanız, Azure veritabanı geçiş hizmeti 'nin kaynağınızdaki adlandırılmış bir örneğe bağlanabilmesi için SQL Browser hizmetini etkinleştirmek ve güvenlik duvarlarınız aracılığıyla 1434 numaralı UDP bağlantı noktasına erişime izin vermek isteyebilirsiniz. Server.
* Kaynak veritabanlarınızın önünde bir güvenlik duvarı gereci kullanıyorsanız, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanlarına, ayrıca SMB bağlantı noktası 445 üzerinden dosyalara erişmesine izin vermek için güvenlik duvarı kuralları eklemeniz gerekebilir.
* [Azure Portal Azure SQL veritabanı yönetilen örneği oluşturma](https://aka.ms/sqldbmi)makalesindeki ayrıntıyı IZLEYEREK bir SQL veritabanı yönetilen örneği oluşturun.
* Kaynak SQL Server ve hedef yönetilen örnek bağlantısı kurmak için kullanılan oturum açma bilgilerinin sysadmin sunucu rolüne üye olduğundan emin olun.
* Veritabanı geçişi için Azure veritabanı geçiş hizmeti 'nin kullanabileceği tüm veritabanınızın tam veritabanı yedekleme dosyalarını ve sonraki işlem günlüğü yedekleme dosyalarını içeren bir SMB ağ paylaşımının sağlanması.
* Kaynak SQL Server örneğini çalıştıran hizmet hesabının oluşturduğunuz ağ paylaşımında yazma ayrıcalıklarına sahip olduğundan ve kaynak sunucunun bilgisayar hesabının aynı paylaşımda okuma/yazma erişimine sahip olduğundan emin olun.
* Önceden oluşturduğunuz ağ paylaşımında tam denetim ayrıcalığına sahip olan Windows kullanıcısını (ve parolasını) not edin. Azure veritabanı geçiş hizmeti, yedekleme dosyalarını geri yükleme işlemi için Azure depolama kapsayıcısına yüklemek üzere Kullanıcı kimlik bilgisini taklit eder.
* Azure veritabanı geçiş hizmeti 'nin hedef Azure veritabanı yönetilen örneği ve Azure depolama kapsayıcısına bağlanmak için kullanabileceği uygulama KIMLIĞI anahtarını üreten bir Azure Active Directory uygulama KIMLIĞI oluşturun. Daha fazla bilgi için bkz. [Kaynaklara erişebilen bir Azure Active Directory uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > Azure veritabanı geçiş hizmeti, belirtilen uygulama KIMLIĞI için abonelikte katkıda bulunan izni gerektirir. Alternatif olarak, Azure veritabanı geçiş hizmeti 'nin gerektirdiği belirli izinleri veren özel roller de oluşturabilirsiniz. Özel rolleri kullanma hakkında adım adım yönergeler için, [SQL veritabanı yönetilen örnek çevrimiçi geçişleri SQL Server Için özel roller](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance)makalesine bakın.

* DMS hizmetinin veritabanı yedekleme dosyalarını yükleyebileceği ve veritabanlarını geçirmek için kullanabileceği bir **Standart Performans katmanı** Azure Depolama Hesabı oluşturun veya belirtin.  Azure veritabanı geçiş hizmeti örneği oluşturulduğu bölgede Azure Storage hesabını oluşturmayı unutmayın.

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

5. Mevcut bir VNet seçin veya bir sanal ağ oluşturun.

    VNet, kaynak SQL Server ve hedef SQL veritabanı yönetilen örneğine erişimi olan Azure veritabanı geçiş hizmeti sağlar.

    Azure portal ' de VNet oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](https://aka.ms/DMSVnet)makalesine bakın.

    Daha fazla ayrıntı için [Azure veritabanı geçiş hizmeti 'ni kullanarak Azure SQL veritabanı yönetilen örnek geçişleri Için ağ topolojileri](https://aka.ms/dmsnetworkformi)makalesine bakın.

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

4. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** metin kutusunda **SQL Server**' i seçin, **hedef sunucu türü** metin kutusunda, **Azure SQL veritabanı yönetilen örneği**' ni seçin ve ardından **Etkinlik türünü seçin**, **çevrimiçi veri geçişi**' ni seçin.

   ![Azure veritabanı geçiş hizmeti projesi oluştur](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Projeyi oluşturmak için **Etkinlik oluştur ve çalıştır**'ı seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

1. **Geçiş kaynağı ayrıntıları** ekranında SQL Server bağlantı ayrıntılarını belirtin.

2. Sunucunuza bir güvenilir sertifika yüklemediyseniz **Sunucu sertifikasına güven** onay kutusunu işaretleyin.

    Güvenilir sertifika yüklü değilse SQL Server, örnek başlatıldığında otomatik olarak imzalanan bir sertifika oluşturur. Bu sertifika, istemci bağlantılarında kimlik bilgilerini şifrelemek için kullanılır.

    > [!CAUTION]
    > Otomatik olarak imzalanan sertifika kullanarak şifrelenmiş SSL bağlantıları yüksek güvenlik sağlamaz. Ortadaki adam saldırılarına maruz kalabilirler. Üretim ortamında veya internete bağlı sunucularda otomatik olarak imzalanan sertifika ile SSL kullanımına güvenmemeniz gerekir.

   ![Kaynak Ayrıntıları](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. **Kaydet**’i seçin.

4. **Kaynak veritabanlarını seçin** ekranında geçiş için **Adventureworks2012** veritabanını seçin.

   ![Kaynak veritabanlarını seçme](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > SQL Server Integration Services (SSIS) kullanıyorsanız, DMS Şu anda SSIS projeleriniz/paketleriniz (SSıSDB) için katalog veritabanını SQL Server Azure SQL veritabanı yönetilen örneği 'ne geçirmeyi desteklememektedir. Ancak, SSIS 'yi Azure Data Factory (ADF) olarak temin edebilir ve SSIS projelerinizi/paketlerinizi Azure SQL veritabanı yönetilen örneği tarafından barındırılan hedef SSıSDB 'ye yeniden dağıtabilirsiniz. SSIS paketlerini geçirme hakkında daha fazla bilgi için [SQL Server Integration Services paketlerini Azure 'A geçirme](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)makalesine bakın.

5. **Kaydet**’i seçin.

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Geçiş hedefi ayrıntıları** EKRANıNDA, DMS ÖRNEĞININ Azure SQL veritabanı yönetilen örneği ve Azure depolama hesabı 'nın hedef örneğine bağlanmak Için KULLANABILECEĞI **uygulama kimliğini** ve **anahtarını** belirtin.

    Daha fazla bilgi için bkz. [Kaynaklara erişebilen bir Azure Active Directory uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Azure SQL veritabanı yönetilen örneği 'nin hedef örneğini içeren **aboneliği** seçin ve ardından hedef örneği seçin.

    Azure SQL veritabanı yönetilen örneğini henüz sağlamadıysanız, örneği sağlamanıza yardımcı olacak [bağlantıyı](https://aka.ms/SQLDBMI) seçin. Azure SQL veritabanı yönetilen örneği hazırsanız, geçişi yürütmek için bu belirli projeye dönün.

3. Azure SQL veritabanı yönetilen örneğine bağlanmak için **SQL kullanıcısını** ve **parolayı** belirtin.

    ![Hedef seçme](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. **Kaydet**’i seçin.

## <a name="select-source-databases"></a>Kaynak veritabanlarını seçme

1. **Kaynak veritabanlarını seçin** ekranında geçirmek istediğiniz kaynak veritabanını seçin.

    ![Kaynak veritabanlarını seçme](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. **Kaydet**’i seçin.

## <a name="configure-migration-settings"></a>Geçiş ayarlarını yapılandırma

1. **Geçiş ayarlarını yapılandırma** ekranında şu bilgileri girin:

    | | |
    |--------|---------|
    |**SMB Ağ konumu paylaşımı** | Azure veritabanı geçiş hizmeti 'nin geçiş için kullanabileceği tam veritabanı yedekleme dosyalarını ve işlem günlüğü yedekleme dosyalarını içeren yerel SMB ağ paylaşımıdır. Kaynak SQL Server örneğini çalıştıran hizmet hesabının ağ paylaşımında okuma/yazma ayrıcalıkları olmalıdır. Ağ paylaşımındaki bir sunucunun FQDN veya IP adresi değerini girin, örneğin: '\\\sunucuadi.etkialaniadi.com\yedeklemeklasoru' veya '\\\IP adresi\yedeklemeklasoru'.|
    |**Kullanıcı adı** | Windows kullanıcısının yukarıda belirttiğiniz ağ paylaşımında tam denetim ayrıcalığına sahip olduğundan emin olun. Azure veritabanı geçiş hizmeti, yedekleme dosyalarını geri yükleme işlemi için Azure depolama kapsayıcısına yüklemek üzere Kullanıcı kimlik bilgisinin kimliğine bürünecektir. |
    |**Parola** | Kullanıcının parolası. |
    |**Azure Depolama Hesabının aboneliği** | Azure Depolama Hesabını içeren aboneliği seçin. |
    |**Azure Depolama Hesabı** | DMS'nin SMB ağ paylaşımındaki yedekleme dosyalarını yükleyebileceği ve veritabanı geçişi için kullanabileceği Azure Depolama Hesabını seçin.  En iyi dosya yükleme performansı için DMS hizmetiyle aynı bölgede bir Depolama Hesabı seçmenizi öneririz. |

    ![Geçiş Ayarlarını Yapılandırma](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

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

   ![Geçiş etkinliği sürüyor](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Tam geçişi gerçekleştirme

Tam veritabanı yedeklemesi SQL veritabanı yönetilen örneği 'nin hedef örneğine geri yüklendikten sonra, veritabanı geçiş cutover işlemini gerçekleştirmek için kullanılabilir.

1. Çevrimiçi veritabanı geçişini tamamlamaya hazır olduğunuzda **Tam Geçişi Başlat**'ı seçin.

2. Kaynak veritabanlarına gelen tüm trafiği durdurun.

3. [Sonradan alınan günlük yedeğini] alın, yedekleme dosyasını SMB ağ paylaşımına yerleştirin ve bu son işlem günlüğü yedeği geri yüklenene kadar bekleyin.

    Bu noktada **Bekleyen değişiklikler** 0 olmalıdır.

4. **Onayla**'yı ve ardından, **Uygula**'yı seçin.

    ![Tam geçişi tamamlamaya hazırlanma](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Veritabanı geçiş durumu **tamamlandı**olarak görüntülendiğinde, UYGULAMALARıNıZı Azure SQL veritabanı yönetilen örneği 'nin yeni hedef örneğine bağlayın.

    ![Tam geçiş tamamlandı](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Sonraki adımlar

* T-SQL RESTORE komutunu kullanarak bir veritabanını yönetilen bir örneğe nasıl geçirebileceğiniz hakkında bir öğretici için [restore komutunu kullanarak bir veritabanını yönetilen örneğe geri yükleme](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)bölümüne bakın.
* Yönetilen örnek hakkında daha fazla bilgi için bkz. [yönetilen örnek nedir](../sql-database/sql-database-managed-instance.md).
* Uygulamaları yönetilen bir örneğe bağlama hakkında daha fazla bilgi için bkz. [uygulamaları bağlama](../sql-database/sql-database-managed-instance-connect-app.md).
