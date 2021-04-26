---
title: "Öğretici: SQL Server SQL yönetilen örneği 'ne geçirme"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni kullanarak SQL Server 'ten bir Azure SQL yönetilen örneğine geçiş yapmayı öğrenin.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 2dfcb4ade9da76d5ec4883e9f4b5b3cb3e0c8682
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076845"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-dms"></a>Öğretici: DMS kullanarak SQL Server Azure SQL yönetilen örneğine çevrimdışı geçirme

Azure veritabanı geçiş hizmeti 'ni kullanarak veritabanlarını bir SQL Server örneğinden [Azure SQL yönetilen örneğine](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)geçirebilirsiniz. Bazı el ile efor gerektirebilecek ek yöntemler için bkz. [SQL yönetilen örneği 'ne örnek geçişi SQL Server](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md)makalesi.

Bu öğreticide, Azure veritabanı geçiş hizmeti 'ni kullanarak bir SQL Server şirket içi örneğinden bir SQL yönetilen örneğine **Adventureworks2012** veritabanını geçirmiş olursunuz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> - Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> - Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun.
> - Geçişi çalıştırma.
> - Geçişi izleme.
> - Geçiş raporu indirme.

> [!IMPORTANT]
> Azure veritabanı geçiş hizmeti SQL Server 'den SQL yönetilen örneğine çevrimdışı geçişler için yedekleme dosyalarını oluşturabilir. Alternatif olarak, en son tam veritabanı yedeklemesini hizmetin veritabanlarınızı geçirmek için kullanacağı SMB ağ paylaşımında sağlayabilirsiniz. Birden çok yedeği tek bir yedekleme medyasına eklemeyin; Her bir yedeklemeyi ayrı bir yedekleme dosyasında alın. Büyük yedeklemeleri geçirmeyle ilgili olası sorunların oluşma olasılığını azaltmak için, sıkıştırılmış yedeklemeler de kullanabileceğinizi unutmayın.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Bu makalede, SQL Server bir SQL yönetilen örneğine çevrimdışı geçiş açıklanmaktadır. Çevrimiçi geçiş için bkz. [DMS kullanarak BIR SQL yönetilen örneğine SQL Server geçirme](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

- [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure sanal ağ oluşturun. [Azure veritabanı geçiş hizmeti 'ni kullanarak SQL yönetilen örnek geçişleri için ağ topolojilerini öğrenin](./resource-network-topologies.md). Sanal ağ oluşturma hakkında daha fazla bilgi için [sanal ağ belgelerine](../virtual-network/index.yml)ve özellikle adım adım ayrıntılarla birlikte hızlı başlangıç makalelerine bakın.

    > [!NOTE]
    > Sanal ağ kurulumu sırasında, Microsoft 'a ağ eşlemesi ile ExpressRoute kullanırsanız, hizmetin sağlanacağı alt ağa aşağıdaki hizmet [uç noktalarını](../virtual-network/virtual-network-service-endpoints-overview.md) ekleyin:
    > - Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
    > - Depolama uç noktası
    > - Service Bus uç noktası
    >
    > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.

- Sanal ağ ağ güvenlik grubu kurallarınızın ServiceBus, Storage ve AzureMonitor için ServiceTag giden bağlantı noktası 443 ' i engellemediğinden emin olun. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
- [Windows Güvenlik Duvarınızı kaynak veritabanı altyapısı erişimi](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
- Azure veritabanı geçiş hizmeti 'nin varsayılan olarak TCP bağlantı noktası 1433 olan kaynak SQL Server erişmesine izin vermek için Windows Güvenlik duvarınızı açın. Varsayılan örneğiniz başka bir bağlantı noktasında dinliyorsa, bunu güvenlik duvarına ekleyin.
- Dinamik bağlantı noktaları kullanarak birden çok adlandırılmış SQL Server örneği çalıştırıyorsanız, Azure veritabanı geçiş hizmeti 'nin kaynak sunucunuzdaki adlandırılmış bir örneğe bağlanabilmesi için SQL Browser hizmetini etkinleştirmek ve güvenlik duvarlarınız aracılığıyla UDP bağlantı noktası 1434 erişimine izin vermek isteyebilirsiniz.
- Kaynak veritabanlarınızın önünde bir güvenlik duvarı gereci kullanıyorsanız, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanlarına, ayrıca SMB bağlantı noktası 445 üzerinden dosyalara erişmesine izin vermek için güvenlik duvarı kuralları eklemeniz gerekebilir.
- [Azure Portal BIR SQL yönetilen örneği oluşturma](../azure-sql/managed-instance/instance-create-quickstart.md)makalesindeki ayrıntıyı IZLEYEREK bir SQL yönetilen örneği oluşturun.
- Kaynak SQL Server ve hedef SQL yönetilen örneği bağlamak için kullanılan oturum açma bilgilerinin sysadmin sunucu rolünün üyesi olduğundan emin olun.

    >[!NOTE]
    >Varsayılan olarak, Azure veritabanı geçiş hizmeti yalnızca SQL oturum açmaları geçirmeyi destekler. Ancak, Windows oturumlarını şu şekilde geçirebilme özelliğini etkinleştirebilirsiniz:
    >
    >- Hedef SQL yönetilen örneğinin, **genel yönetici** rolüne sahip bir kullanıcı tarafından Azure Portal aracılığıyla yapılandırılabilen AAD okuma erişimi olduğundan emin olun.
    >- Yapılandırma sayfasında Azure portal aracılığıyla ayarlanan Windows Kullanıcı/Grup oturum açma geçişlerini etkinleştirmek için Azure veritabanı geçiş hizmeti örneğinizi yapılandırma. Bu ayarı etkinleştirdikten sonra değişikliklerin etkili olması için hizmeti yeniden başlatın.
    >
    > Hizmeti yeniden başlattıktan sonra, Windows Kullanıcı/Grup oturum açmalar geçiş için kullanılabilen oturum açma listesinde görünür. Geçiş yaptığınız herhangi bir Windows Kullanıcı/Grup oturumu açma için, sizden ilişkili etki alanı adını girmeniz istenir. Hizmet Kullanıcı hesapları (etki alanı adı NT YETKILISI olan hesap) ve Sanal Kullanıcı hesapları (etki alanı adı NT HIZMETI olan hesap adı) desteklenmez.

- Azure veritabanı geçiş hizmeti 'nin kaynak veritabanını yedeklemek için kullanabileceği bir ağ paylaşma oluşturun.
- Kaynak SQL Server örneğini çalıştıran hizmet hesabının oluşturduğunuz ağ paylaşımında yazma ayrıcalıklarına sahip olduğundan ve kaynak sunucunun bilgisayar hesabının aynı paylaşımda okuma/yazma erişimine sahip olduğundan emin olun.
- Önceden oluşturduğunuz ağ paylaşımında tam denetim ayrıcalığına sahip olan Windows kullanıcısını (ve parolasını) not edin. Azure veritabanı geçiş hizmeti, yedekleme dosyalarını geri yükleme işlemi için Azure depolama kapsayıcısına yüklemek üzere Kullanıcı kimlik bilgisini taklit eder.
- Bir blob kapsayıcısı oluşturun ve [Depolama Gezgini ile Azure Blob Depolama kaynaklarını yönetme](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container) makalesindeki adımları kullanarak SAS URI'sini alın. SAS URI değerini oluştururken ilke penceresinde tüm izinleri (Okuma, Yazma, Silme, Listeleme) seçtiğinizden emin olun. Bu ayrıntı, Azure veritabanı geçiş hizmeti 'ni, veritabanlarını SQL yönetilen örneğine geçirmek için kullanılan yedekleme dosyalarını karşıya yüklemek üzere depolama hesabı kapsayıcınıza erişimi sağlar.

    > [!NOTE]
    > Azure veritabanı geçiş hizmeti, [geçiş ayarlarını yapılandırma](#configure-migration-settings) adımı sırasında depolama hesabı ayarlarını yapılandırırken hesap düzeyi SAS belirtecini kullanmayı desteklemez.
    
## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

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

5. Var olan bir sanal ağı seçin veya bir tane oluşturun.

    Sanal ağ, Azure veritabanı geçiş hizmeti 'ni, kaynak SQL Server ve hedef SQL yönetilen örneği erişimi sağlar.

    Azure portal bir sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

    Daha fazla ayrıntı için [Azure veritabanı geçiş hizmeti 'ni kullanarak Azure SQL yönetilen örnek geçişleri Için ağ topolojileri](./resource-network-topologies.md)makalesine bakın.

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

4. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** metin kutusunda **SQL Server**' i seçin, **hedef sunucu türü** metin kutusunda, **Azure SQL yönetilen örneği**' ni seçin ve ardından **etkinlik türü seç**' i seçin, **çevrimdışı veri geçişi**' ni seçin.

   ![DMS projesi oluşturma](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Projeyi oluşturmak için **Oluştur**'u seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

1. **Geçiş kaynağı ayrıntıları** ekranında SQL Server bağlantı ayrıntılarını belirtin.

2. Sunucunuza bir güvenilir sertifika yüklemediyseniz **Sunucu sertifikasına güven** onay kutusunu işaretleyin.

    Güvenilir sertifika yüklü değilse SQL Server, örnek başlatıldığında otomatik olarak imzalanan bir sertifika oluşturur. Bu sertifika, istemci bağlantılarında kimlik bilgilerini şifrelemek için kullanılır.

    > [!CAUTION]
    > Kendinden imzalı bir sertifika kullanılarak şifrelenen TLS bağlantıları güçlü güvenlik sağlamaz. Ortadaki adam saldırılarına maruz kalabilirler. Bir üretim ortamında veya internet 'e bağlı sunucularda, otomatik olarak imzalanan sertifikalar kullanarak TLS 'ye güvenmemelisiniz.

   ![Kaynak Ayrıntıları](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. **Kaydet**’i seçin.

4. **Kaynak veritabanlarını seçin** ekranında geçiş için **Adventureworks2012** veritabanını seçin.

   ![Kaynak veritabanlarını seçme](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > SQL Server Integration Services (SSIS) kullanıyorsanız, DMS Şu anda SSIS projeleriniz/paketleriniz (SSıSDB) için katalog veritabanını SQL Server SQL yönetilen örneğine geçirmeyi desteklememektedir. Ancak, SSIS 'yi Azure Data Factory (ADF) olarak temin edebilir ve SSIS projelerinizi/paketlerinizi SQL yönetilen örneği tarafından barındırılan hedef SSıSDB 'ye yeniden dağıtabilirsiniz. SSIS paketlerini geçirme hakkında daha fazla bilgi için [SQL Server Integration Services paketlerini Azure 'A geçirme](./how-to-migrate-ssis-packages.md)makalesine bakın.

5. **Kaydet**’i seçin.

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Geçiş hedefi ayrıntıları** ekranında, **AdventureWorks2012** VERITABANıNı geçirdiğiniz önceden sağlanmış SQL yönetilen örneği olan hedefin bağlantı ayrıntılarını belirtin.

    SQL yönetilen örneğini henüz sağlamadıysanız, örneği sağlamanıza yardımcı olacak [bağlantıyı](../azure-sql/managed-instance/instance-create-quickstart.md) seçin. Proje oluşturmaya devam edebilirsiniz ve sonra SQL yönetilen örneği hazırsanız, geçişi yürütmek için bu belirli projeye geri dönün.

    ![Hedef seçme](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. **Kaydet**’i seçin.

## <a name="select-source-databases"></a>Kaynak veritabanlarını seçme

1. **Kaynak veritabanlarını seçin** ekranında geçirmek istediğiniz kaynak veritabanını seçin.

    ![Kaynak veritabanlarını seçme](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. **Kaydet**’i seçin.

## <a name="select-logins"></a>Oturum açmaları seçme

1. **Oturum açmaları seçin** ekranında geçirmek istediğiniz oturum açmaları seçin.

    >[!NOTE]
    >Varsayılan olarak, Azure veritabanı geçiş hizmeti yalnızca SQL oturum açmaları geçirmeyi destekler. Windows oturumlarının geçirilmesi desteğini etkinleştirmek için, Bu öğreticinin **Önkoşullar** bölümüne bakın.

    ![Oturum açmaları seçme](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. **Kaydet**’i seçin.

## <a name="configure-migration-settings"></a>Geçiş ayarlarını yapılandırma

1. **Geçiş ayarlarını yapılandırma** ekranında şu bilgileri girin:

    | Parametre | Açıklama |
    |--------|---------|
    |**Kaynak yedekleme seçeneği seçin** | Veritabanı geçişinde kullanılacak DMS için tam yedekleme dosyaları varsa **En son yedekleme dosyalarını ben sağlayacağım** seçeneğini belirleyin. DMS'nin önce kaynak veritabanının tam yedeğini alıp geçiş için bu yedeği kullanmasını istiyorsanız **Azure Veritabanı Geçiş Hizmeti'nin yedek dosyalar oluşturmasına izin veriyorum** seçeneğini belirleyin. |
    |**Ağ konumu paylaşımı** | Azure veritabanı geçiş hizmeti 'nin kaynak veritabanı yedeklemelerini atabileceğiniz yerel SMB ağ payı. Kaynak SQL Server örneğini çalıştıran hizmet hesabının ağ paylaşımında yazma ayrıcalıkları olmalıdır. Ağ paylaşımındaki bir sunucunun FQDN veya IP adresi değerini girin, örneğin: '\\\sunucuadi.etkialaniadi.com\yedeklemeklasoru' veya '\\\IP adresi\yedeklemeklasoru'.|
    |**Kullanıcı adı** | Windows kullanıcısının yukarıda belirttiğiniz ağ paylaşımında tam denetim ayrıcalığına sahip olduğundan emin olun. Azure veritabanı geçiş hizmeti, yedekleme dosyalarını geri yükleme işlemi için Azure depolama kapsayıcısına yüklemek üzere Kullanıcı kimlik bilgisinin kimliğine bürünecektir. Geçiş için TDE özelliğinin etkin olduğu veritabanlarının seçilmesi durumunda yukarıdaki Windows kullanıcısının yerleşik yönetici hesabı olması ve Azure Veritabanı Geçiş Hizmeti'nin sertifika dosyalarını yükleyip silmesi için [Kullanıcı Hesabı Denetimi](/windows/security/identity-protection/user-account-control/user-account-control-overview) özelliğinin devre dışı bırakılmış olması gerekir. |
    |**Parola** | Kullanıcının parolası. |
    |**Depolama hesabı ayarları** | Azure veritabanı geçiş hizmeti 'ni, hizmetin yedekleme dosyalarını karşıya yükleyen ve veritabanlarını SQL yönetilen örneği 'ne geçirmek için kullanılan depolama hesabı kapsayıcınıza erişimi sağlayan SAS URI 'SI. [Blob kapsayıcısı için SAS URI değerini almayı öğrenin](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Bu SAS URI 'SI, depolama hesabı için değil, blob kapsayıcısı için olmalıdır.|
    |**TDE Ayarları** | Kaynak veritabanlarını Saydam Veri Şifrelemesi (TDE) etkin bir şekilde geçiriyorsanız, hedef SQL yönetilen örneği üzerinde yazma ayrıcalıklarına sahip olmanız gerekir.  Açılan menüden SQL yönetilen örneğinin sağlandığı aboneliği seçin.  Açılan menüden **Azure SQL Veritabanı Yönetilen Örneği** hedefini seçin. |

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

   ![Geçiş etkinliği ekranını ve yenileme düğmesini gösteren ekran görüntüsü.](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    İlgili sunucu nesnelerinin geçiş durumunu izlemek için veritabanları ve oturum açma işlemleri kategorilerini genişletebilirsiniz.

   ![Geçiş etkinliği sürüyor](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Geçiş tamamlandıktan sonra **Raporu indir**'i seçerek geçiş işleminin ayrıntılarını içeren raporu indirebilirsiniz.

3. Hedef veritabanının hedef SQL yönetilen örnek ortamında olduğunu doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

- T-SQL RESTORE komutunu kullanarak bir veritabanının SQL yönetilen örneği 'ne nasıl geçirileceğini gösteren bir öğretici için [restore komutunu kullanarak BIR SQL yönetilen örneğine bir yedeklemeyi geri yükleme](../azure-sql/managed-instance/restore-sample-database-quickstart.md)bölümüne bakın.
- SQL yönetilen örneği hakkında daha fazla bilgi için bkz. [SQL yönetilen örneği nedir](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
- Uygulamaları SQL yönetilen örneğine bağlama hakkında daha fazla bilgi için bkz. [uygulamaları bağlama](../azure-sql/managed-instance/connect-application-instance.md).
