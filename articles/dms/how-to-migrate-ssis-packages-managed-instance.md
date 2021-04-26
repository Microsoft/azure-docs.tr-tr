---
title: SSIS paketlerini SQL yönetilen örneğine geçirme
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni veya Data Migration Yardımcısı kullanarak Azure SQL yönetilen örneği 'ne SQL Server Integration Services (SSIS) paketlerini ve projelerini geçirmeyi öğrenin.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 1f6df64a66c0700f3a13a40ea5046515a2bf1a51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643839"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-managed-instance"></a>SQL Server Integration Services paketlerini Azure SQL yönetilen örneği 'ne geçirme
SQL Server Integration Services (SSIS) kullanıyorsanız ve SSIS projelerinizi/paketlerinizi, SQL Server tarafından barındırılan kaynak SSSıSDB 'den Azure SQL yönetilen örneği tarafından barındırılan hedef SSıSDB 'ye geçirmek istiyorsanız, Azure veritabanı geçiş hizmeti 'ni kullanabilirsiniz.

SSIS 'nin kullandığınız sürümü 2012 'den daha eski veya SSIS projelerini/paketlerinizi geçirmeden önce, SSIS projelerinizi/paketlerinizi geçirmeden önce bunları, SSMS 'den başlatılabilen Tümleştirme Hizmetleri proje Dönüştürme Sihirbazı 'Nı kullanarak dönüştürmeniz gerekir. Daha fazla bilgi için [projeleri proje dağıtım modeline dönüştürme](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#convert)makalesine bakın.

> [!NOTE]
> Azure veritabanı geçiş hizmeti (DMS) Şu anda Azure SQL veritabanını hedef geçiş hedefi olarak desteklemiyor. SSIS projelerini/paketlerini Azure SQL veritabanı 'na yeniden dağıtmak için [SQL Server Integration Services paketlerini Azure SQL veritabanı 'na yeniden dağıtma](./how-to-migrate-ssis-packages.md)makalesine bakın.

Bu makalede şunları öğreneceksiniz:
> [!div class="checklist"]
>
> * Kaynak SSIS projelerini/paketlerini değerlendirin.
> * SSIS projelerini/paketlerini Azure 'a geçirin.

## <a name="prerequisites"></a>Önkoşullar

Bu adımları tamamlayabilmeniz için şunlar gerekir:

* [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için Microsoft Azure sanal ağ oluşturmak için. Daha fazla bilgi için [Azure veritabanı geçiş hizmeti 'ni kullanarak SQL yönetilen örnek geçişleri Için ağ topolojileri]( https://aka.ms/dmsnetworkformi)makalesine bakın. Sanal ağ oluşturma hakkında daha fazla bilgi için [sanal ağ belgelerine](../virtual-network/index.yml)ve özellikle adım adım ayrıntılarla birlikte hızlı başlangıç makalelerine bakın.
* Sanal ağ ağ güvenlik grubu kurallarınızın, ServiceBus, Storage ve AzureMonitor için ServiceTag giden bağlantı noktası 443 ' i engellemediğinden emin olmak için. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
* [Kaynak veritabanı altyapısı erişimi Için Windows Güvenlik duvarınızı](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)yapılandırmak için.
* Azure veritabanı geçiş hizmeti 'nin kaynak SQL Server erişmesine izin vermek üzere Windows Güvenlik duvarınızı açmak için, varsayılan olarak TCP bağlantı noktası 1433 ' dir.
* Dinamik bağlantı noktası kullanarak birden fazla adlandırılmış SQL Server örneği çalıştırıyorsanız Azure Veritabanı Geçiş Hizmeti'nin kaynak sunucunuzdaki adlandırılmış örneğe bağlanabilmesi için SQL Browser Hizmeti'ni etkinleştirebilir ve güvenlik duvarınızda 1434 numaralı UDP bağlantı noktasına erişim izni verebilirsiniz.
* Kaynak veritabanlarınızın önünde bir güvenlik duvarı cihazı kullanıyorsanız Azure Veritabanı Geçiş Hizmeti'nin geçiş amacıyla kaynak veritabanlarına ve 445 numaralı SMB bağlantı noktası aracılığıyla dosyalara erişmesi için güvenlik duvarı kuralları eklemeniz gerekebilir.
* SSıSDB barındırmak için bir SQL yönetilen örneği. Bir tane oluşturmanız gerekiyorsa, [Azure SQL yönetilen örneği oluşturma](../azure-sql/managed-instance/instance-create-quickstart.md)makalesindeki ayrıntıları izleyin.
* Kaynak SQL Server ve hedef yönetilen örneği bağlamak için kullanılan oturum açma bilgilerinin sysadmin sunucu rolünün üyeleri olduğundan emin olmak için.
* SSIS 'nin bir SQL yönetilen örneği tarafından barındırılan hedef SSıSDB ile Azure-SSIS Integration Runtime (IR) içeren Azure Data Factory (ADF) olduğunu doğrulamak için ( [Azure Data Factory 'de Azure-SSIS tümleştirme çalışma zamanını oluşturma](../data-factory/create-azure-ssis-integration-runtime.md)makalesinde açıklandığı gibi).

## <a name="assess-source-ssis-projectspackages"></a>Kaynak SSIS projelerini/paketlerini değerlendir

Kaynak SSSıSDB değerlendirmesi henüz veritabanı Geçiş Yardımcısı (DMA) ile tümleştirmemişse, SSIS projeleriniz/paketleriniz, Azure SQL yönetilen örneği üzerinde barındırılan hedef SSıSDB 'ye yeniden dağıtıldıklarında değerlendirilecek/onaylanır.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

    ![Portal aboneliklerini gösterme](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Veritabanı Geçiş Hizmeti örneğini oluşturma

1. Azure portalda +**Kaynak oluştur**'u seçin, **Azure Veritabanı Geçiş Hizmeti** araması yapın ve açılan listeden **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

     ![Azure Market](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. **Geçiş Hizmeti oluşturun** ekranında hizmet için bir ad belirtin, aboneliği ve yeni ya da var olan bir kaynak grubunu seçin.

4. DMS örneğini oluşturmak istediğiniz konumu seçin.

5. Var olan bir sanal ağı seçin veya bir tane oluşturun.

    Sanal ağ, Azure veritabanı geçiş hizmeti 'ne kaynak SQL Server erişimi sağlar ve Azure SQL yönetilen örneği ' ni hedefleyin.

    Azure portal bir sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

    Daha fazla ayrıntı için [Azure veritabanı geçiş hizmeti 'ni kullanarak Azure SQL yönetilen örnek geçişleri Için ağ topolojileri](./resource-network-topologies.md)makalesine bakın.

6. Fiyatlandırma katmanını seçin.

    Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![DMS hizmetini başlatma](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Hizmeti oluşturmak için **Oluştur**’u seçin.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmetin bir örneği oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

    ![Azure Veritabanı Geçiş Hizmeti’nin tüm örneklerini bulma](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. **Azure Veritabanı Geçiş Hizmeti ekranında** oluşturduğunuz örneğin adını arayın ve sonuçlardan bu örneği seçin.

3. +**Yeni Geçiş Projesi**'ni seçin.

4. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** metin kutusunda **SQL Server**, **hedef sunucu türü** metin kutusunda, **Azure SQL yönetilen örneği**' ni seçin ve ardından **etkinlik türü seç**' i seçin, **SSIS paketi geçişi**' ni seçin.

   ![DMS projesi oluşturma](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Projeyi oluşturmak için **Oluştur**'u seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

1. **Geçiş kaynağı ayrıntıları** ekranında SQL Server bağlantı ayrıntılarını belirtin.

2. Sunucunuza bir güvenilir sertifika yüklemediyseniz **Sunucu sertifikasına güven** onay kutusunu işaretleyin.

    Güvenilir sertifika yüklü değilse SQL Server, örnek başlatıldığında otomatik olarak imzalanan bir sertifika oluşturur. Bu sertifika, istemci bağlantılarında kimlik bilgilerini şifrelemek için kullanılır.

    > [!CAUTION]
    > Kendinden imzalı bir sertifika kullanılarak şifrelenen TLS bağlantıları güçlü güvenlik sağlamaz. Ortadaki adam saldırılarına maruz kalabilirler. Bir üretim ortamında veya internet 'e bağlı sunucularda, otomatik olarak imzalanan sertifikalar kullanarak TLS 'ye güvenmemelisiniz.

   ![Kaynak Ayrıntıları](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. **Kaydet**’i seçin.

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Geçiş hedefi ayrıntıları** ekranında, hedefin bağlantı ayrıntılarını belirtin.

     ![Hedef ayrıntıları](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. **Kaydet**’i seçin.

## <a name="review-the-migration-summary"></a>Geçiş özetini gözden geçirme

1. **Geçiş özeti** ekranının **Etkinlik adı** metin kutusunda geçiş etkinliği için bir ad belirtin.

2. **SSIS projesi ve ortam üzerine yazma seçeneği** için, var olan SSIS projelerinin ve ortamlarının üzerine yazılıp yazılmayacağını belirtin.

    ![Geçiş projesi özeti](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Geçiş projesiyle ilgili ayrıntıları gözden geçirin ve doğrulayın.

## <a name="run-the-migration"></a>Geçişi çalıştırma

* **Geçişi çalıştır**'ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

* Microsoft [veritabanı geçiş kılavuzu](https://datamigration.microsoft.com/)' nda geçiş kılavuzunu gözden geçirin.