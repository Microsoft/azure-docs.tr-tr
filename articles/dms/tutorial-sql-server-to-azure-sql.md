---
title: "Öğretici: Azure SQL veritabanı 'ndaki tek/havuza alınmış bir veritabanına SQL Server çevrimdışı geçiş yapmak için Azure veritabanı geçiş hizmeti 'ni kullanma | Microsoft Docs"
description: Azure veritabanı geçiş hizmeti 'ni kullanarak şirket içi SQL Server 'ten Azure SQL veritabanı 'nda tek bir veritabanına veya havuza alınmış bir veritabanına geçiş yapmayı öğrenin.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 46d5754d046284ae42da91c2eb08bfe709e7e372
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582349"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-offline-using-dms"></a>Öğretici: DMS kullanarak Azure SQL veritabanı 'nda tek bir veritabanına veya havuza alınmış veritabanına SQL Server geçirme

Azure veritabanı geçiş hizmeti 'ni kullanarak veritabanlarını şirket içi SQL Server örneğinden [Azure SQL veritabanı](https://docs.microsoft.com/azure/sql-database/)'na geçirebilirsiniz. Bu öğreticide, Azure veritabanı geçiş hizmeti 'ni kullanarak Azure SQL veritabanı 'nda SQL Server 2016 (veya üzeri) bir şirket içi örneğine geri yüklenmiş **Adventureworks2012** veritabanını geçireceğiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> - Data Migration Yardımcısı'nı kullanarak şirket içi veritabanınızı değerlendirme.
> - Data Migration Yardımcısı'nı kullanarak örnek şemayı geçirme.
> - Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturun.
> - Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun.
> - Geçişi çalıştırma.
> - Geçişi izleme.
> - Geçiş raporu indirme.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Bu makalede, Azure SQL veritabanı 'nda tek bir veritabanına veya havuza alınmış bir veritabanına SQL Server bir çevrimdışı geçiş açıklanır. Çevrimiçi geçiş için bkz. [DMS kullanarak çevrimiçi biçimde SQL Server'ı Azure SQL Veritabanı’na geçirme](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

- [SQL Server 2016 veya sonraki bir sürümü](https://www.microsoft.com/sql-server/sql-server-downloads)indirin ve yükleyin.
- [Sunucu Ağ Protokolünü Etkinleştirme veya Devre Dışı Bırakma](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) makalesindeki yönergeleri izleyerek SQL Server Express yüklemesi sırasında varsayılan olarak devre dışı bırakılan TCP/IP protokolünü etkinleştirin.
- Azure SQL veritabanı 'nda, [Azure Portal kullanarak Azure SQL veritabanı 'nda tek bir veritabanı oluşturma](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started)makalesindeki ayrıntıları izleyerek yaptığınız tek bir (veya havuza alınmış) veritabanı oluşturun.

    > [!NOTE]
    > SQL Server Integration Services (SSIS) kullanıyorsanız ve SSIS projeleriniz/paketleriniz (SSıSDB) için katalog veritabanını SQL Server Azure SQL veritabanı 'na geçirmek istiyorsanız, hedef SSSıSDB, sizin adınıza otomatik olarak oluşturulur ve yönetilir. SSSıS Sağlama Azure Data Factory (ADF). SSIS paketlerini geçirme hakkında daha fazla bilgi için [SQL Server Integration Services paketlerini Azure 'A geçirme](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)makalesine bakın.
  
- [Data Migration Yardımcısı](https://www.microsoft.com/download/details.aspx?id=53595) 3.3 veya üzeri sürümünü indirip yükleyin.
- [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) veya VPN kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti Için bir Azure sanal ağı (VNet) oluşturun [ ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). VNet oluşturma hakkında daha fazla bilgi için, [sanal ağ belgelerine](https://docs.microsoft.com/azure/virtual-network/)ve özellikle de adım adım ayrıntılara sahip olan hızlı başlangıç makalelerine bakın.

    > [!NOTE]
    > VNet kurulumu sırasında, ExpressRoute 'u Microsoft 'a ağ eşlemesi ile kullanıyorsanız, aşağıdaki hizmet [uç noktalarını](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) hizmetin sağlanacağı alt ağa ekleyin:
    > - Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
    > - Depolama uç noktası
    > - Service Bus uç noktası
    >
    > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.
    >
    >Şirket içi ağ ile Azure arasında siteden siteye bağlantınız yoksa veya siteden siteye bağlantı bant genişliği varsa, Azure veritabanı geçiş hizmeti 'ni karma modda (Önizleme) kullanmayı göz önünde bulundurun. Karma mod, bulutta çalışan bir Azure veritabanı geçiş hizmeti örneğiyle birlikte Şirket içi geçiş çalışanından yararlanır. Karma modda Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturmak için [Azure Portal kullanarak karma modda Azure veritabanı geçiş hizmeti örneği oluşturma](https://aka.ms/dms-hybrid-create)makalesine bakın.

- VNet ağ güvenlik grubu kurallarınızın Azure veritabanı geçiş hizmeti 'ne yönelik aşağıdaki gelen iletişim bağlantı noktalarını engellemediğinden emin olun: 443, 53, 9354, 445, 12000. Azure VNet NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)makalesine bakın.
- [Windows Güvenlik Duvarınızı veritabanı altyapısı erişimi](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
- Azure veritabanı geçiş hizmeti 'nin varsayılan olarak TCP bağlantı noktası 1433 olan kaynak SQL Server erişmesine izin vermek için Windows Güvenlik duvarınızı açın.
- Dinamik bağlantı noktaları kullanarak birden çok adlandırılmış SQL Server örneği çalıştırıyorsanız, Azure veritabanı geçiş hizmeti 'nin kaynağınızdaki adlandırılmış bir örneğe bağlanabilmesi için SQL Browser hizmetini etkinleştirmek ve güvenlik duvarlarınız aracılığıyla 1434 numaralı UDP bağlantı noktasına erişime izin vermek isteyebilirsiniz. Server.
- Kaynak veritabanınızın önünde bir güvenlik duvarı gereci kullanırken, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanına erişmesine izin vermek üzere güvenlik duvarı kuralları eklemeniz gerekebilir.
- Azure veritabanı geçiş hizmeti 'nin hedef veritabanlarına erişmesine izin vermek için Azure SQL veritabanı sunucusu için sunucu düzeyinde bir IP [güvenlik duvarı kuralı](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) oluşturun. Azure veritabanı geçiş hizmeti için kullanılan VNet 'in alt ağ aralığını belirtin.
- SQL Server örneğine bağlanmak için kullanılan kimlik bilgilerinin [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) izinlerine sahip olduğundan emin olun.
- Hedef Azure SQL Veritabanı örneğine bağlanmak için kullanılan kimlik bilgilerinin hedef Azure SQL veritabanlarında CONTROL DATABASE iznine sahip olduğundan emin olun.

## <a name="assess-your-on-premises-database"></a>Şirket içi veritabanınızı değerlendirme

Şirket içi SQL Server örneğinden verileri Azure SQL veritabanı 'nda tek bir veritabanına veya havuza alınmış bir veritabanına geçirebilmeniz için, geçişi engelleyebilen herhangi bir engelleyici sorun için SQL Server veritabanını değerlendirmenize gerek duyarsınız. Data Migration Yardımcısı 3.3 veya üzeri bir sürümü kullanarak [SQL Server geçiş değerlendirmesi yapma](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) makalesindeki adımları tamamlayın ve şirket içi veritabanı değerlendirmesi yapın. Yapılması gereken adımların özeti aşağıda verilmiştir:

1. Data Migration Yardımcısı'nda Yeni (+) simgesini ve **Değerlendirme** proje türünü seçin.
2. Bir proje adı belirtin, **Kaynak sunucu türü** metin kutusunda **SQL Server**, **Hedef sunucu türü** metin kutusunda **Azure SQL Veritabanı** seçimini yapın ve ardından **Oluştur**'a tıklayarak projeyi oluşturun.

    Kaynak SQL Server veritabanının Azure SQL veritabanı 'nda tek bir veritabanına veya havuza alınmış veritabanına geçirilmesini değerlendirmek için aşağıdaki değerlendirme raporu türlerinden birini veya her ikisini de seçebilirsiniz:

   - Veritabanı uyumluluğunu denetle
   - Özellik eşliğini denetle

    İki rapor türü de varsayılan olarak seçilidir.

3. Data Migration Yardımcısı'nın **Seçenekler** ekranında **İleri**'yi seçin.
4. **Kaynak seçin** ekranının **Sunucuya bağlan** iletişim kutusunda SQL Server bağlantı bilgilerini girin ve **Bağlan**'ı seçin.
5. **Kaynak ekle** iletişim kutusunda **AdventureWorks2012**'yi, **Ekle**'yi ve ardından **Değerlendirmeyi Başlat**'ı seçin.

    > [!NOTE]
    > SSIS kullanıyorsanız, DMA Şu anda kaynak SSSıSDB değerlendirmesini desteklememektedir. Ancak, SSIS projeleri/paketleri Azure SQL veritabanı tarafından barındırılan hedef SSıSDB 'ye yeniden dağıtıldıklarında değerlendirilecek/doğrulanacak. SSIS paketlerini geçirme hakkında daha fazla bilgi için [SQL Server Integration Services paketlerini Azure 'A geçirme](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)makalesine bakın.

    Değerlendirme tamamlandığında aşağıdaki grafiğe benzer sonuçlar görüntülenir:

    ![Veri geçişi değerlendirmesi](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Azure SQL veritabanı 'nda tek veritabanları veya havuza alınmış veritabanları için değerlendirmeler, tek bir veritabanına veya havuza alınmış veritabanına dağıtım için özellik eşlik sorunlarını ve geçiş engelleme sorunlarını belirler.

    - **SQL Server özellik eşliği** kategorisi kapsamlı öneriler, Azure'daki alternatif yaklaşımlar ve geçiş projelerini planlama konusunda yardımcı olacak çıkarılabilecek adımlar sunar.
    - **Uyumluluk sorunları** kategorisi, şirket içi SQL Server veritabanlarının Azure SQL Veritabanına geçirilmesini engelleyebilecek uyumluluk sorunlarını yansıtan kısmen desteklenen ve desteklenmeyen özellikleri tanımlar. Bu sorunları gidermenize yardımcı olan öneriler de sağlanır.

6. İlgili seçenekleri belirleyerek değerlendirme sonuçlarındaki geçiş engelleyici sorunları ve özellik eşliği sorunlarını gözden geçirin.

## <a name="migrate-the-sample-schema"></a>Örnek şemayı geçirme

Değerlendirmeyi rahatladıktan ve seçilen veritabanının Azure SQL veritabanı 'nda tek bir veritabanına veya havuza alınmış veritabanına geçiş için uygun bir aday olduğunu karşıladıktan sonra, şemayı Azure SQL veritabanı 'na geçirmek için DMA 'yı kullanın.

> [!NOTE]
> Data Migration Yardımcısı'nda bir geçiş projesi oluşturmadan önce önkoşullarda belirtilen şekilde bir Azure SQL veritabanı sağladığınızdan emin olun. Bu öğreticide Azure SQL Veritabanı’nın adının **AdventureWorksAzure** olduğu kabul edilmiştir, ancak istediğiniz adı kullanabilirsiniz.

> [!IMPORTANT]
> SSIS kullanıyorsanız, DMA Şu anda kaynak SSSıSDB geçişini desteklememektedir, ancak SSIS projelerinizi/paketlerinizi Azure SQL veritabanı tarafından barındırılan hedef SSıSDB 'ye yeniden dağıtabilirsiniz. SSIS paketlerini geçirme hakkında daha fazla bilgi için [SQL Server Integration Services paketlerini Azure 'A geçirme](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)makalesine bakın.

**AdventureWorks2012** şemasını tek bir veritabanına veya havuza alınmış VERITABANı Azure SQL veritabanı 'na geçirmek için aşağıdaki adımları uygulayın:

1. Data Migration Yardımcısı'nda Yeni (+) simgesini ve ardından **Proje türü** bölümünde **Geçiş**'i seçin.
2. Bir proje adı belirtin, **Kaynak sunucu türü** metin kutusunda **SQL Server**, **Hedef sunucu türü** metin kutusunda da **Azure SQL Veritabanı**'nı seçin.
3. **Geçiş Kapsamı** bölümünde **Yalnızca şema**'yı seçin.

    Yukarıdaki adımları gerçekleştirdikten sonra aşağıdaki görüntüde gösterildiği gibi Data Migration Yardımcısı arabiriminin görünmesi gerekir:

    ![Data Migration Yardımcısı Projesi Oluşturma](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Projeyi oluşturmak için **Oluştur**'u seçin.
5. Data Migration Yardımcısı'nda kaynak SQL Server için bağlantı bilgilerini belirtin, **Bağlan**'ı ve ardından **AdventureWorks2012** veritabanını seçin.

    ![Data Migration Yardımcısı Kaynak Bağlantı Ayrıntıları](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. **İleri**' yi seçin, **hedef sunucuya Bağlan**' ın altında, Azure SQL veritabanı için hedef bağlantı ayrıntılarını belirtin, **Bağlan**' ı seçin ve ardından Azure SQL 'de önceden sağladığınız **AdventureWorksAzure** veritabanını seçin Veritabanınızı.

    ![Data Migration Yardımcısı Hedef Bağlantı Ayrıntıları](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. **İleri**'yi seçerek **Nesneleri seçin** ekranına ilerleyin ve bu ekranda Azure SQL Veritabanına dağıtılacak **AdventureWorks2012** veritabanındaki şema nesnelerini belirtin.

    Varsayılan olarak, tüm nesneler seçilir.

    ![SQL Betiği Oluşturma](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. **SQL betiği oluştur**'u seçerek SQL betiklerini oluşturun ve hata olup olmadığını inceleyin.

    ![Şema Betiği](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. **Şemayı dağıtma**'yı seçerek şemayı Azure SQL Veritabanına dağıtın. Şema dağıtıldıktan sonra hedef sunucuda anomali olup olmadığını kontrol edin.

    ![Şemayı Dağıtma](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

   ![Portal aboneliklerini gösterme](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Örnek oluşturma

1. Azure portalda +**Kaynak oluştur**'u seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve açılan listeden **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

    ![Azure Market](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. **Geçiş Hizmeti oluşturun** ekranında hizmet için bir ad belirtin, aboneliği ve yeni ya da var olan bir kaynak grubunu seçin.

4. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz konumu seçin.

5. Mevcut bir VNet seçin veya yeni bir sanal ağ oluşturun.

    VNet, kaynak SQL Server ve hedef Azure SQL veritabanı örneğine erişimi olan Azure veritabanı geçiş hizmeti sağlar.

    Azure portal VNet oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](https://aka.ms/DMSVnet)makalesine bakın.

6. Fiyatlandırma katmanını seçin.

    Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![Azure Veritabanı Geçiş Hizmeti örneği ayarlarını yapılandırma](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. Hizmeti oluşturmak için **Oluştur**’u seçin.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmet oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

     ![Azure veritabanı geçiş hizmeti 'nin tüm örneklerini bulun](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında oluşturduğunuz Azure Veritabanı Geçiş Hizmeti örneğinin adını arayın ve sonuçlardan bu örneği seçin.

    ![Azure veritabanı geçiş hizmeti örneğinizi bulun](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

3. +**Yeni Geçiş Projesi**'ni seçin.
4. **Yeni geçiş projesi** ekranında proje için bir ad belirtin, **Kaynak sunucu türü** metin kutusunda **SQL Server**, **Hedef sunucu türü** metin kutusunda **Azure SQL Veritabanı** ve **Etkinlik türünü seçin** alanında **Çevrimdışı veri geçişi** seçimini yapın.

    ![Veritabanı Geçiş Hizmeti Projesi Oluşturma](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Projeyi oluşturmak ve geçiş etkinliğini çalıştırmak için **Etkinlik oluştur ve çalıştır**'ı seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

1. **Geçiş kaynağı ayrıntıları** ekranında SQL Server örneğinin bağlantı ayrıntılarını belirtin.

    Kaynak SQL Server örneği adı için Tam Etki Alanı Adı (FQDN) kullandığınızdan emin olun. DNS ad çözümlemenin mümkün olmadığı durumlarda IP Adresini de kullanabilirsiniz.

2. Kaynak sunucunuza bir güvenilir sertifika yüklemediyseniz **Sunucu sertifikasına güven** onay kutusunu işaretleyin.

    Güvenilir sertifika yüklü değilse SQL Server, örnek başlatıldığında otomatik olarak imzalanan bir sertifika oluşturur. Bu sertifika, istemci bağlantılarında kimlik bilgilerini şifrelemek için kullanılır.

    > [!CAUTION]
    > Otomatik olarak imzalanan sertifika kullanarak şifrelenmiş SSL bağlantıları yüksek güvenlik sağlamaz. Ortadaki adam saldırılarına maruz kalabilirler. Üretim ortamında veya internete bağlı sunucularda otomatik olarak imzalanan sertifika ile SSL kullanımına güvenmemeniz gerekir.

   ![Kaynak Ayrıntıları](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > SSIS kullanırsanız, DMS Şu anda kaynak SSSıSDB geçişini desteklememektedir, ancak SSIS projelerinizi/paketlerinizi Azure SQL veritabanı tarafından barındırılan hedef SSıSDB 'ye yeniden dağıtabilirsiniz. SSIS paketlerini geçirme hakkında daha fazla bilgi için [SQL Server Integration Services paketlerini Azure 'A geçirme](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)makalesine bakın.

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Kaydet**'i seçin ve **Geçiş hedef ayrıntıları** ekranında Data Migration Yardımcısı kullanılarak **AdventureWorks2012** şemasının dağıtıldığı önceden sağlanmış Azure SQL Veritabanı olan hedef Azure SQL Veritabanı sunucusunun bağlantı ayrıntılarını girin.

    ![Hedef seçme](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. **Kaydet**'i seçin ve **Hedef veritabanlarıyla eşleyin** ekranında geçiş yapılacak kaynak ve hedef veritabanlarını eşleyin.

    Hedef veritabanı, kaynak veritabanıyla aynı veritabanı adını içeriyorsa, Azure veritabanı geçiş hizmeti varsayılan olarak hedef veritabanını seçer.

    ![Hedef veritabanlarıyla eşleyin](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. **Kaydet**'i seçin ve **Tablo seçme** ekranında tablo listesini genişletip etkilenen alan listesini inceleyin.

    Azure veritabanı geçiş hizmeti 'nin hedef Azure SQL veritabanı örneğinde bulunan tüm boş kaynak tablolarını otomatik olarak seçtiğine unutmayın. Veri içeren tabloları yeniden geçirmek isterseniz bu dikey pencerede tabloları seçmeniz gerekir.

    ![Tabloları seçme](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. **Kaydet**'i seçin ve **Geçiş özeti** ekranının **Etkinlik adı** metin kutusunda geçiş etkinliği için bir ad belirtin.

5. **Doğrulama seçeneği** bölümünü genişleterek **Doğrulama seçeneğini belirleyin** ekranını açın ve geçirilen veritabanlarında **Şema karşılaştırması**, **Veri tutarlılığı** ve **Sorgu doğruluğu** doğrulaması yapma tercihlerini belirtin.

    ![Doğrulama seçeneğini belirleme](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. **Kaydet**'i seçin ve özeti gözden geçirerek kaynak ve hedef ayrıntılarının belirttiğiniz verilere uyduğundan emin olun.

    ![Geçiş Özeti](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>Geçişi çalıştırma

- **Geçişi çalıştır**'ı seçin.

    Geçiş etkinliği penceresi açılır ve etkinliğin **Durum** bilgisi **Beklemede** olarak değişir.

    ![Etkinlik Durumu](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Geçişi izleme

1. Geçiş etkinliği ekranında **Yenile**'yi seçerek geçişin **Durum** bilgisi **Tamamlandı** olana kadar gösterilen verileri güncelleştirebilirsiniz.

    ![Etkinlik Durumu Tamamlandı](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Geçiş tamamlandıktan sonra **Raporu indir**'i seçerek geçiş işleminin ayrıntılarını içeren raporu indirebilirsiniz.

3. Azure SQL Veritabanı sunucusundaki hedef veritabanlarını doğrulayın.

### <a name="additional-resources"></a>Ek kaynaklar

- [Azure Data Migration hizmeti uygulamalı Laboratuvarı kullanan SQL geçişi](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) .
- Azure SQL veritabanı 'na çevrimiçi geçişler gerçekleştirirken oluşan bilinen sorunlar ve sınırlamalar hakkında daha fazla bilgi için [Azure SQL veritabanı çevrimiçi geçişleri ile Ilgili bilinen sorunlar ve geçici çözümler](known-issues-azure-sql-online.md)makalesine bakın.
- Azure veritabanı geçiş hizmeti hakkında daha fazla bilgi için [Azure veritabanı geçiş hizmeti nedir?](https://docs.microsoft.com/azure/dms/dms-overview)makalesine bakın.
- Azure SQL veritabanı hakkında daha fazla bilgi için [Azure SQL veritabanı hizmeti nedir?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)makalesine bakın.
