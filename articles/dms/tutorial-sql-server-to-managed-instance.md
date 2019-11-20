---
title: "Öğretici: Azure SQL veritabanı yönetilen örneği 'ne geçiş yapmak için DMS kullanma | Microsoft Docs"
description: Azure veritabanı geçiş hizmeti 'ni kullanarak şirket içi SQL Server 'ten Azure SQL veritabanı yönetilen örneği 'ne geçiş yapmayı öğrenin.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: ca6f94664ad07b15c9c0c6dada6d6824e97527d9
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903145"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Öğretici: DMS kullanarak SQL Server Azure SQL veritabanı yönetilen örneğine çevrimdışı geçirme

Azure veritabanı geçiş hizmeti 'ni kullanarak veritabanlarını şirket içi SQL Server örneğinden bir [Azure SQL veritabanı yönetilen örneğine](../sql-database/sql-database-managed-instance.md)geçirebilirsiniz. Bazı el ile efor gerektirebilecek ek yöntemler için bkz. [Azure SQL veritabanı yönetilen örneği 'ne örnek geçişi SQL Server](../sql-database/sql-database-managed-instance-migrate.md)makalesi.

Bu öğreticide, Azure veritabanı geçiş hizmeti 'ni kullanarak bir SQL Server şirket içi örneğinden bir SQL veritabanı yönetilen örneğine **Adventureworks2012** veritabanını geçirmiş olursunuz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> - Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturun.
> - Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun.
> - Geçişi çalıştırma.
> - Geçişi izleme.
> - Geçiş raporu indirme.

> [!IMPORTANT]
> SQL Server 'den SQL veritabanı yönetilen örneğine çevrimdışı geçişler için Azure veritabanı geçiş hizmeti, yedekleme dosyalarını sizin için oluşturabilir. Alternatif olarak, en son tam veritabanı yedeklemesini hizmetin veritabanlarınızı geçirmek için kullanacağı SMB ağ paylaşımında sağlayabilirsiniz. Birden çok yedeği tek bir yedekleme medyasına eklemeyin; Her bir yedeklemeyi ayrı bir yedekleme dosyasında alın. Büyük yedeklemeleri geçirmeyle ilgili olası sorunların oluşma olasılığını azaltmak için, sıkıştırılmış yedeklemeler de kullanabileceğinizi unutmayın.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Bu makalede, SQL Server bir SQL veritabanı yönetilen örneğine çevrimdışı geçiş açıklanmaktadır. Çevrimiçi geçiş için bkz. [DMS kullanarak Azure SQL veritabanı yönetilen örneğine çevrimiçi olarak SQL Server geçirme](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

- [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) veya VPN kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti Için bir Azure sanal ağı (VNet) oluşturun [ ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Azure [veritabanı geçiş hizmeti 'ni kullanarak Azure SQL veritabanı yönetilen örnek geçişleri için ağ topolojilerini öğrenin](https://aka.ms/dmsnetworkformi). VNet oluşturma hakkında daha fazla bilgi için, [sanal ağ belgelerine](https://docs.microsoft.com/azure/virtual-network/)ve özellikle de adım adım ayrıntılara sahip olan hızlı başlangıç makalelerine bakın.

    > [!NOTE]
    > VNet kurulumu sırasında, ExpressRoute 'u Microsoft 'a ağ eşlemesi ile kullanıyorsanız, aşağıdaki hizmet [uç noktalarını](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) hizmetin sağlanacağı alt ağa ekleyin:
    > - Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
    > - Depolama uç noktası
    > - Service Bus uç noktası
    >
    > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.

- VNet ağ güvenlik grubu kurallarınızın Azure veritabanı geçiş hizmeti 'ne yönelik aşağıdaki gelen iletişim bağlantı noktalarını engellemediğinden emin olun: 443, 53, 9354, 445, 12000. Azure VNet NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)makalesine bakın.
- [Windows Güvenlik Duvarınızı kaynak veritabanı altyapısı erişimi](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
- Azure veritabanı geçiş hizmeti 'nin varsayılan olarak TCP bağlantı noktası 1433 olan kaynak SQL Server erişmesine izin vermek için Windows Güvenlik duvarınızı açın.
- Dinamik bağlantı noktaları kullanarak birden çok adlandırılmış SQL Server örneği çalıştırıyorsanız, Azure veritabanı geçiş hizmeti 'nin kaynağınızdaki adlandırılmış bir örneğe bağlanabilmesi için SQL Browser hizmetini etkinleştirmek ve güvenlik duvarlarınız aracılığıyla 1434 numaralı UDP bağlantı noktasına erişime izin vermek isteyebilirsiniz. Server.
- Kaynak veritabanlarınızın önünde bir güvenlik duvarı gereci kullanıyorsanız, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanlarına, ayrıca SMB bağlantı noktası 445 üzerinden dosyalara erişmesine izin vermek için güvenlik duvarı kuralları eklemeniz gerekebilir.
- [Azure Portal Azure SQL veritabanı yönetilen örneği oluşturma](https://aka.ms/sqldbmi)makalesindeki ayrıntıyı IZLEYEREK Azure SQL veritabanı yönetilen örneği oluşturun.
- Kaynak SQL Server ve hedef yönetilen örneği bağlamak için kullanılan oturum açma bilgilerinin sysadmin sunucu rolünün üyesi olduğundan emin olun.
- Azure veritabanı geçiş hizmeti 'nin kaynak veritabanını yedeklemek için kullanabileceği bir ağ paylaşma oluşturun.
- Kaynak SQL Server örneğini çalıştıran hizmet hesabının oluşturduğunuz ağ paylaşımında yazma ayrıcalıklarına sahip olduğundan ve kaynak sunucunun bilgisayar hesabının aynı paylaşımda okuma/yazma erişimine sahip olduğundan emin olun.
- Önceden oluşturduğunuz ağ paylaşımında tam denetim ayrıcalığına sahip olan Windows kullanıcısını (ve parolasını) not edin. Azure veritabanı geçiş hizmeti, yedekleme dosyalarını geri yükleme işlemi için Azure depolama kapsayıcısına yüklemek üzere Kullanıcı kimlik bilgisini taklit eder.
- Bir blob kapsayıcısı oluşturun ve [Depolama Gezgini ile Azure Blob Depolama kaynaklarını yönetme](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) makalesindeki adımları kullanarak SAS URI'sini alın. SAS URI değerini oluştururken ilke penceresinde tüm izinleri (Okuma, Yazma, Silme, Listeleme) seçtiğinizden emin olun. Bu ayrıntı, Azure SQL veritabanı yönetilen örneği 'ne veritabanlarını geçirmek için kullanılan yedekleme dosyalarını karşıya yüklemek üzere depolama hesabı kapsayıcınıza erişimi olan Azure veritabanı geçiş hizmeti sağlar.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler** seçeneğini belirleyin ve ardından **Abonelikler**'i seçin.

    ![Portal aboneliklerini gösterme](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)        

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Veritabanı Geçiş Hizmeti örneğini oluşturma

1. Azure portalda +**Kaynak oluştur**'u seçin, **Azure Veritabanı Geçiş Hizmeti** araması yapın ve açılan listeden **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

     ![Azure Market](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. **Geçiş Hizmeti oluşturun** ekranında hizmet için bir ad belirtin, aboneliği ve yeni ya da var olan bir kaynak grubunu seçin.

4. DMS örneğini oluşturmak istediğiniz konumu seçin.

5. Mevcut bir VNet seçin veya bir sanal ağ oluşturun.

    VNet, kaynak SQL Server erişimi olan Azure veritabanı geçiş hizmeti 'ni ve hedef Azure SQL veritabanı yönetilen örneğini sağlar.

    Azure portal ' de VNet oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](https://aka.ms/DMSVnet)makalesine bakın.

    Daha fazla ayrıntı için [Azure veritabanı geçiş hizmeti 'ni kullanarak Azure SQL DB yönetilen örnek geçişleri Için ağ topolojileri](https://aka.ms/dmsnetworkformi)makalesine bakın.

6. Fiyatlandırma katmanını seçin.

    Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![DMS hizmetini başlatma](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Hizmeti oluşturmak için **Oluştur**’u seçin.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmetin bir örneği oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

    ![Azure veritabanı geçiş hizmeti 'nin tüm örneklerini bulun](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. **Azure Veritabanı Geçiş Hizmeti ekranında** oluşturduğunuz örneğin adını arayın ve sonuçlardan bu örneği seçin.

3. +**Yeni Geçiş Projesi**'ni seçin.

4. **Yeni geçiş projesi** ekranında proje için bir ad belirtin, **Kaynak sunucu türü** metin kutusunda **SQL Server**, **Hedef sunucu türü** metin kutusunda **Azure SQL Veritabanı Yönetilen Örneği** ve **Etkinlik türünü seçin** alanında **Çevrimdışı veri geçişi** seçimini yapın.

   ![DMS projesi oluşturma](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Projeyi oluşturmak için **Oluştur**'u seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

1. **Geçiş kaynağı ayrıntıları** ekranında SQL Server bağlantı ayrıntılarını belirtin.

2. Sunucunuza bir güvenilir sertifika yüklemediyseniz **Sunucu sertifikasına güven** onay kutusunu işaretleyin.

    Güvenilir sertifika yüklü değilse SQL Server, örnek başlatıldığında otomatik olarak imzalanan bir sertifika oluşturur. Bu sertifika, istemci bağlantılarında kimlik bilgilerini şifrelemek için kullanılır.

    > [!CAUTION]
    > Otomatik olarak imzalanan sertifika kullanarak şifrelenmiş SSL bağlantıları yüksek güvenlik sağlamaz. Ortadaki adam saldırılarına maruz kalabilirler. Üretim ortamında veya internete bağlı sunucularda otomatik olarak imzalanan sertifika ile SSL kullanımına güvenmemeniz gerekir.

   ![Kaynak Ayrıntıları](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. **Kaydet**’i seçin.

4. **Kaynak veritabanlarını seçin** ekranında geçiş için **Adventureworks2012** veritabanını seçin.

   ![Kaynak veritabanlarını seçme](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > SQL Server Integration Services (SSIS) kullanıyorsanız, DMS Şu anda SSIS projeleriniz/paketleriniz (SSıSDB) için katalog veritabanını SQL Server Azure SQL veritabanı yönetilen örneği 'ne geçirmeyi desteklememektedir. Ancak, SSIS 'yi Azure Data Factory (ADF) olarak temin edebilir ve SSIS projelerinizi/paketlerinizi Azure SQL veritabanı yönetilen örneği tarafından barındırılan hedef SSıSDB 'ye yeniden dağıtabilirsiniz. SSIS paketlerini geçirme hakkında daha fazla bilgi için [SQL Server Integration Services paketlerini Azure 'A geçirme](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)makalesine bakın.

5. **Kaydet**’i seçin.

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Geçiş hedefi ayrıntıları** ekranında, **AdventureWorks2012** veritabanını GEÇIRDIĞINIZ önceden sağlanmış Azure SQL veritabanı yönetilen örneği olan hedefin bağlantı ayrıntılarını belirtin.

    SQL veritabanı yönetilen örneğini henüz sağlamadıysanız, örneği sağlamanıza yardımcı olacak [bağlantıyı](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) seçin. Proje oluşturmaya devam edebilir ve ardından Azure SQL veritabanı yönetilen örneği hazırsanız, geçişi yürütmek için bu belirli projeye geri dönün.

     ![Hedef seçme](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. **Kaydet**’i seçin.

## <a name="select-source-databases"></a>Kaynak veritabanlarını seçme

1. **Kaynak veritabanlarını seçin** ekranında geçirmek istediğiniz kaynak veritabanını seçin.

    ![Kaynak veritabanlarını seçme](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. **Kaydet**’i seçin.

## <a name="select-logins"></a>Oturum açmaları seçme

1. **Oturum açmaları seçin** ekranında geçirmek istediğiniz oturum açmaları seçin.

    >[!NOTE]
    >Bu sürüm yalnızca SQL oturum açma bilgilerinin geçirilmesini destekler.

    ![Oturum açmaları seçme](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. **Kaydet**’i seçin.

## <a name="configure-migration-settings"></a>Geçiş ayarlarını yapılandırma

1. **Geçiş ayarlarını yapılandırma** ekranında şu bilgileri girin:

    | | |
    |--------|---------|
    |**Kaynak yedekleme seçeneği seçin** | Veritabanı geçişinde kullanılacak DMS için tam yedekleme dosyaları varsa **En son yedekleme dosyalarını ben sağlayacağım** seçeneğini belirleyin. DMS'nin önce kaynak veritabanının tam yedeğini alıp geçiş için bu yedeği kullanmasını istiyorsanız **Azure Veritabanı Geçiş Hizmeti'nin yedek dosyalar oluşturmasına izin veriyorum** seçeneğini belirleyin. |
    |**Ağ konumu paylaşımı** | Azure veritabanı geçiş hizmeti 'nin kaynak veritabanı yedeklemelerini atabileceğiniz yerel SMB ağ payı. Kaynak SQL Server örneğini çalıştıran hizmet hesabının ağ paylaşımında yazma ayrıcalıkları olmalıdır. Ağ paylaşımındaki bir sunucunun FQDN veya IP adresi değerini girin, örneğin: '\\\sunucuadi.etkialaniadi.com\yedeklemeklasoru' veya '\\\IP adresi\yedeklemeklasoru'.|
    |**Kullanıcı adı** | Windows kullanıcısının yukarıda belirttiğiniz ağ paylaşımında tam denetim ayrıcalığına sahip olduğundan emin olun. Azure veritabanı geçiş hizmeti, yedekleme dosyalarını geri yükleme işlemi için Azure depolama kapsayıcısına yüklemek üzere Kullanıcı kimlik bilgisinin kimliğine bürünecektir. Geçiş için TDE özelliğinin etkin olduğu veritabanlarının seçilmesi durumunda yukarıdaki Windows kullanıcısının yerleşik yönetici hesabı olması ve Azure Veritabanı Geçiş Hizmeti'nin sertifika dosyalarını yükleyip silmesi için [Kullanıcı Hesabı Denetimi](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) özelliğinin devre dışı bırakılmış olması gerekir. |
    |**Parola** | Kullanıcının parolası. |
    |**Depolama hesabı ayarları** | Azure veritabanı geçiş hizmeti 'ni, hizmetin yedekleme dosyalarını karşıya yükleyen ve veritabanlarını Azure SQL veritabanı yönetilen örneği 'ne geçirmek için kullanılan depolama hesabı kapsayıcınıza erişimi sağlayan SAS URI 'SI. [Blob kapsayıcısı için SAS URI değerini almayı öğrenin](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**TDE Ayarları** | Kaynak veritabanlarını Saydam Veri Şifrelemesi (TDE) etkin bir şekilde geçiriyorsanız, hedef Azure SQL veritabanı yönetilen örneği üzerinde yazma ayrıcalıklarına sahip olmanız gerekir.  Açılan menüden Azure SQL veritabanı yönetilen örneği tarafından sağlanan aboneliği seçin.  Açılan menüden **Azure SQL Veritabanı Yönetilen Örneği** hedefini seçin. |

    ![Geçiş Ayarlarını Yapılandırma](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. **Kaydet**’i seçin.

## <a name="review-the-migration-summary"></a>Geçiş özetini gözden geçirme

1. **Geçiş özeti** ekranının **Etkinlik adı** metin kutusunda geçiş etkinliği için bir ad belirtin.

2. **Doğrulama seçeneği** bölümünü genişleterek **Doğrulama seçeneğini belirleyin** ekranını açın ve geçirilen veritabanlarında sorgu doğrulaması yapma tercihini belirtin ve **Kaydet**'i seçin.

3. Geçiş projesiyle ilgili ayrıntıları gözden geçirin ve doğrulayın.

    ![Geçiş projesi özeti](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. **Kaydet**’i seçin.

## <a name="run-the-migration"></a>Geçişi çalıştırma

- **Geçişi çalıştır**'ı seçin.

  Geçiş etkinliği penceresi açılır ve etkinliğin durum bilgisi **Beklemede** olarak değişir.

## <a name="monitor-the-migration"></a>Geçişi izleme

1. Geçiş etkinliği ekranını güncelleştirmek için **Yenile**'yi seçin.

   ![Geçiş etkinliği sürüyor](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    İlgili sunucu nesnelerinin geçiş durumunu izlemek için veritabanları ve oturum açma işlemleri kategorilerini genişletebilirsiniz.

   ![Geçiş etkinliği sürüyor](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Geçiş tamamlandıktan sonra **Raporu indir**'i seçerek geçiş işleminin ayrıntılarını içeren raporu indirebilirsiniz.

3. Hedef veritabanının hedef veritabanını Azure SQL veritabanı yönetilen örnek ortamında doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

- T-SQL RESTORE komutunu kullanarak bir veritabanını yönetilen bir örneğe nasıl geçirebileceğiniz hakkında bir öğretici için [restore komutunu kullanarak bir veritabanını yönetilen örneğe geri yükleme](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)bölümüne bakın.
- Yönetilen örnek hakkında daha fazla bilgi için bkz. [yönetilen örnek nedir](../sql-database/sql-database-managed-instance.md).
- Uygulamaları yönetilen bir örneğe bağlama hakkında daha fazla bilgi için bkz. [uygulamaları bağlama](../sql-database/sql-database-managed-instance-connect-app.md).
