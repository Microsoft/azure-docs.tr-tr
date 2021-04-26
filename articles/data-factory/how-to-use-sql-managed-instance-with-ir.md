---
title: Azure SQL yönetilen örneğini Azure-SQL Server Integration Services (SSIS) ile birlikte kullanın Azure Data Factory
description: Azure Data Factory Azure SQL yönetilen örneğini SQL Server Integration Services (SSIS) ile kullanmayı öğrenin.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: 190def0e6e2f77d330d2307753dc9e9d53c55dd7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564162"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Azure Data Factory Azure SQL yönetilen örneğini SQL Server Integration Services (SSIS) ile kullanın

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Artık SQL Server Integration Services (SSIS) projelerinizi, paketleri ve iş yüklerinizi Azure bulutuna taşıyabilirsiniz. Azure SQL veritabanı veya SQL yönetilen örneği üzerinde SQL Server Management Studio (SSMS) gibi tanıdık araçlarla SSIS projelerini ve paketlerini dağıtın, çalıştırın ve yönetin. Bu makalede, Azure-SSIS tümleştirme çalışma zamanı (IR) ile Azure SQL yönetilen örneği kullanılırken aşağıdaki belirli bölgeler vurgulanmıştır:

- [Azure SQL yönetilen örneği tarafından barındırılan SSIS Kataloğu (SSSıSDB) ile bir Azure-SSIS IR sağlama](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [SSIS paketlerini Azure SQL yönetilen örnek Aracısı işine göre yürütme](how-to-invoke-ssis-package-managed-instance-agent.md)
- [SSSıSDB günlüklerini Azure SQL yönetilen örnek Aracısı işine göre temizle](#clean-up-ssisdb-logs)
- [Azure SQL yönetilen örneği ile yük devretmeyi Azure-SSIS IR](configure-bcdr-azure-ssis-integration-runtime.md)
- [Şirket içi SSIS iş yüklerini, veritabanı iş yükü hedefi olarak Azure SQL yönetilen örneği ile ADF 'de SSIS 'ye geçirme](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği tarafından barındırılan SSSıSDB ile Azure-SSIS IR sağlama

### <a name="prerequisites"></a>Önkoşullar

1. Azure Active Directory kimlik doğrulaması seçerken [Azure SQL yönetilen örneği üzerinde Azure Active Directory (Azure AD) etkinleştirin](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Özel uç nokta veya genel uç nokta üzerinden SQL yönetilen örneği bağlamayı seçin:

    - Özel uç nokta (tercih edilen)

        1. Katılacak Azure-SSIS IR sanal ağını seçin:
            - **Farklı bir alt ağla**, yönetilen örnekle aynı sanal ağın içinde.
            - Yönetilen örneğinden farklı bir sanal ağın içinde, sanal ağ eşlemesi (genel VNet eşleme kısıtlamaları nedeniyle aynı bölge ile sınırlıdır) veya sanal ağdan sanal ağa bağlantı aracılığıyla.

            SQL yönetilen örnek bağlantısı hakkında daha fazla bilgi için bkz. [uygulamanızı Azure SQL yönetilen örneğine bağlama](../azure-sql/managed-instance/connect-application-instance.md).

        1. [Sanal ağı yapılandırın](#configure-virtual-network).

    - Ortak uç nokta üzerinden

        Azure SQL yönetilen örnekleri, [genel uç noktalar](../azure-sql/managed-instance/public-endpoint-configure.md)üzerinden bağlantı sağlayabilir. Gelen ve giden gereksinimlerin SQL yönetilen örneği ve Azure-SSIS IR arasında trafiğe izin vermek için karşılaması gerekir:

        - bir sanal ağ içinde Azure-SSIS IR (tercih edilen)

            Azure-SSIS IR gelen trafiğe izin vermek için **SQL yönetilen örneğinin gelen gereksinimi**.

            | Aktarım Protokolü | Kaynak | Kaynak bağlantı noktası aralığı | Hedef | Hedef bağlantı noktası aralığı |
            |---|---|---|---|---|
            |TCP|Azure bulut hizmeti etiketi|*|VirtualNetwork|3342|

            Daha fazla bilgi için bkz. [ağ güvenlik grubunda genel uç nokta trafiğine Izin verme](../azure-sql/managed-instance/public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).

        - bir sanal ağ içinde Azure-SSIS IR

            SQL yönetilen örneği Azure-SSIS IR desteklenmeyen bir bölgede olduğunda, genel VNet eşleme sınırlaması nedeniyle VNet eşlemesi olmayan bir sanal ağ içinde Azure-SSIS IR bir özel senaryo vardır. Bu senaryoda, **bir sanal ağ içindeki Azure-SSIS IR** , **genel uç nokta üzerinden** SQL yönetilen örneğini bağlar. SQL yönetilen örneği ve Azure-SSIS IR arasında trafiğe izin vermek için aşağıdaki ağ güvenlik grubu (NSG) kurallarını kullanın:

            1. Azure-SSIS IR gelen trafiğe izin vermek için **SQL yönetilen örneğinin gelen gereksinimi**.

                | Aktarım Protokolü | Kaynak | Kaynak bağlantı noktası aralığı | Hedef |Hedef bağlantı noktası aralığı |
                |---|---|---|---|---|
                |TCP|Azure-SSIS IR statik IP adresi <br> Ayrıntılar için bkz. [Azure-SSIS IR Için kendi genel IP 'Nizi getirme](join-azure-ssis-integration-runtime-virtual-network.md#publicIP).|*|VirtualNetwork|3342|

             1. SQL yönetilen örneğine giden trafiğe izin vermek için **Azure-SSIS IR giden gereksinim**.

                | Aktarım Protokolü | Kaynak | Kaynak bağlantı noktası aralığı | Hedef |Hedef bağlantı noktası aralığı |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[SQL yönetilen örnek genel uç nokta IP adresi](../azure-sql/managed-instance/management-endpoint-find-ip-address.md)|3342|

### <a name="configure-virtual-network"></a>Sanal ağ yapılandırma

1. **Kullanıcı izni**. Azure-SSIS IR oluşturan kullanıcının, aşağıdaki seçeneklerden birini içeren en az Azure Data Factory kaynağında [rol ataması](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) olması gerekir:

    - Yerleşik ağ katılımcısı rolünü kullanın. Bu rol, gerekenden çok daha büyük bir kapsama sahip olan _Microsoft. Network/ \*_ Permission ile birlikte gelir.
    - Yalnızca gerekli _Microsoft. Network/virtualNetworks/ \* /Join/Action_ iznini içeren özel bir rol oluşturun. Ayrıca, Azure Resource Manager bir sanal ağa katılırken Azure-SSIS IR için kendi genel IP adreslerini getirmek istiyorsanız role _Microsoft. Network/publicIPAddresses/*/Join/Action_ iznini de ekleyin.

1. **Sanal ağ**.

    1. Sanal ağın kaynak grubunun belirli Azure ağ kaynaklarını oluşturup silmesi için emin olun.

        Azure-SSIS IR, sanal ağ ile aynı kaynak grubunda belirli ağ kaynaklarını oluşturması gerekir. Bu kaynaklar şunları içerir:
        - *\<Guid> -Azurebatch-cloudserviceloaddengeleyici* adlı bir Azure yük dengeleyici
        - * \<Guid> -Azurebatch-cloudservicenetworksecuritygroup adlı bir ağ güvenlik grubu
        - -Azurebatch-cloudservicepublicıp adlı bir Azure genel IP adresi

        Azure-SSIS IR, bu kaynaklar oluşturulur. Azure-SSIS IR durdurulduğunda bunlar silinir. Azure-SSIS IR durdurmadan engellemeden kaçınmak için, diğer kaynaklarınızda bu ağ kaynaklarını yeniden kullanmayın.

    1. Sanal ağın ait olduğu kaynak grubunda/abonelikte [kaynak kilidi](../azure-resource-manager/management/lock-resources.md) olmadığından emin olun. Salt bir okuma/silme kilidi yapılandırırsanız, Azure-SSIS IR başlatıp durdurma başarısız olur veya yanıt vermeyi durdurur.

    1. Sanal ağın ait olduğu kaynak grubu/abonelik altında aşağıdaki kaynakların oluşturulmasını engelleyen bir Azure ilkenizin olmadığından emin olun:
        - Microsoft. Network/LoadBalancers
        - Microsoft. Network/NetworkSecurityGroups

    1. Ağ güvenlik grubu (NSG) kuralındaki trafiğe, SQL yönetilen örneği ve Azure-SSIS IR arasında trafiğe izin ver ve Azure-SSIS IR gereken trafik.
        1. Azure-SSIS IR gelen trafiğe izin vermek için **SQL yönetilen örneğinin gelen gereksinimi**.

            | Aktarım Protokolü | Kaynak | Kaynak bağlantı noktası aralığı | Hedef | Hedef bağlantı noktası aralığı | Yorumlar |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000-11999|SQL veritabanı sunucusu bağlantı ilkeniz **yeniden yönlendirme** yerine **proxy** olarak ayarlandıysa yalnızca bağlantı noktası 1433 gerekir.|

        1. **Azure-SSIS IR giden** TRAFIĞE, SQL yönetilen örneği ve Azure-SSIS IR için gereken diğer trafiğe izin vermek için giden bir gereksinim.

        | Aktarım Protokolü | Kaynak | Kaynak bağlantı noktası aralığı | Hedef | Hedef bağlantı noktası aralığı | Yorumlar |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 |SQL yönetilen örneği 'ne giden trafiğe izin verin. Bağlantı ilkesi **yeniden yönlendirme** yerine **Ara sunucu** olarak ayarlandıysa, yalnızca bağlantı noktası 1433 gerekir. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | Sanal ağdaki Azure-SSIS IR düğümleri Azure depolama ve Azure Event Hubs gibi Azure hizmetlerine erişmek için bu bağlantı noktasını kullanır. |
        | TCP | VirtualNetwork | * | İnternet | 80 | Seçim Sanal ağdaki Azure-SSIS IR düğümleri Internet 'ten bir sertifika iptal listesi indirmek için bu bağlantı noktasını kullanır. Bu trafiği engellerseniz, IR 'yi başlatır ve sertifika kullanımı için sertifika iptal listesini denetleme yeteneğini kaybederseniz performansı indirgeyede karşılaşabilirsiniz. Hedefi belirli FQDN 'lere daraltmak istiyorsanız [Azure ExpressRoute veya Kullanıcı tanımlı yol (UDR) kullanma](./join-azure-ssis-integration-runtime-virtual-network.md#route)bölümüne bakın.|
        | TCP | VirtualNetwork | * | Depolama | 445 | Seçim Bu kural yalnızca Azure dosyalarında depolanan SSIS paketini yürütmek istediğinizde gereklidir. |
        |||||||

        1. Azure-SSIS IR için gereken trafiğe izin vermek üzere **Azure-SSIS IR gelen gereksinimi**.

        | Aktarım Protokolü | Kaynak | Kaynak bağlantı noktası aralığı | Hedef | Hedef bağlantı noktası aralığı | Yorumlar |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (IR 'ye Kaynak Yöneticisi sanal bir ağa katılırsanız) <br/><br/>10100, 20100, 30100 (IR 'yi klasik bir sanal ağa katılırsanız)| Data Factory hizmeti, sanal ağdaki Azure-SSIS IR düğümlerle iletişim kurmak için bu bağlantı noktalarını kullanır. <br/><br/> Bir alt ağ düzeyinde NSG oluşturup oluşturamadıkça, Data Factory Azure-SSIS IR barındıran sanal makinelere bağlı ağ arabirimi kartları (NIC 'ler) düzeyinde her zaman bir NSG yapılandırır. Yalnızca belirtilen bağlantı noktalarında Data Factory IP adreslerinden gelen trafiğe NIC düzeyinde NSG tarafından izin verilir. Bu bağlantı noktalarını alt ağ düzeyinde internet trafiğine açmış olsanız bile, IP adreslerinden Data Factory IP adreslerinden gelen trafik NIC düzeyinde engellenir. |
        | TCP | Corpnetgördünüz | * | VirtualNetwork | 3389 | Seçim Bu kural yalnızca Microsoft Supporter 'ın müşteriyi gelişmiş sorun giderme için açmasını istediğinde gereklidir ve sorun giderme sonrasında hemen kapatılabilir. **Corpnetgördünüz** hizmet etiketi, yalnızca Microsoft Kurumsal ağındaki güvenli erişim iş istasyonlarının uzak masaüstünü kullanmasına izin verir. Ve bu hizmet etiketi portaldan seçilemez ve yalnızca Azure PowerShell veya Azure CLı aracılığıyla kullanılabilir. <br/><br/> NIC düzeyi NSG 'de, bağlantı noktası 3389 varsayılan olarak açıktır ve Windows Güvenlik Duvarı 'nda, koruma için her IR düğümünde Varsayılan olarak giden bağlantı noktası 3389 Azure-SSIS IR izin vermediği sırada 3389 numaralı bağlantı noktasını denetlemenize izin veririz. |
        |||||||

    1. Daha fazla bilgi için bkz. [sanal ağ yapılandırması](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration) :
        - Azure-SSIS IR için kendi genel IP adreslerini getir
        - Kendi etki alanı adı sistemi (DNS) sunucunuzu kullanıyorsanız
        - Azure ExpressRoute veya Kullanıcı tanımlı bir yol (UDR) kullanıyorsanız
        - Özelleştirilmiş Azure-SSIS IR kullanıyorsanız

### <a name="provision-azure-ssis-integration-runtime"></a>Sağlama Azure-SSIS Integration Runtime

1. SQL yönetilen örnek özel uç noktası veya genel uç nokta ' ı seçin.

    Azure portal/ADF uygulamasında [Azure-SSIS IR](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) SAĞLARKEN, SQL ayarları SAYFASıNDA, SSIS Kataloğu oluştururken (sssısdb) SQL yönetilen örnek **Özel uç noktasını** veya **genel uç noktasını** kullanın.

    Ortak uç nokta ana bilgisayar adı, <mi_name>. public. <dns_zone>. database.windows.net ve bağlantı için kullanılan bağlantı noktasının 3342 olduğu biçimde gelir.  

    ![Ekran görüntüsü, oluşturma ve Katalog veritabanı sunucu uç noktası girildiğinde tümleştirme çalışma zamanı kurulumunu gösterir.](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. Geçerli olduğunda Azure AD kimlik doğrulaması ' nı seçin.

    ![Katalog-ortak uç nokta](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Azure AD kimlik doğrulamasını etkinleştirme hakkında daha fazla bilgi için bkz. Azure [SQL yönetilen örneği üzerinde Azure AD etkinleştirme](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Azure-SSIS IR, geçerli olduğunda sanal ağa birleştirin.

    Gelişmiş ayar sayfasında, katılacak sanal ağı ve alt ağı seçin.
    
    SQL yönetilen örneği ile aynı sanal ağın içindeyken SQL yönetilen örneğinden farklı bir **alt ağ** seçin. 

    Azure-SSIS IR bir sanal ağa nasıl katılalacağı hakkında daha fazla bilgi için bkz. [Azure-SSIS tümleştirme çalışma zamanını bir sanal ağa ekleme](join-azure-ssis-integration-runtime-virtual-network.md).

    ![Ekran görüntüsü, çalışma zamanının katılması için bir sanal ağ seçebileceğiniz tümleştirme çalışma zamanı kurulumu gelişmiş ayarlarını gösterir.](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

Azure-SSIS IR oluşturma hakkında daha fazla bilgi için, bkz. [Azure Data Factory Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime).

## <a name="clean-up-ssisdb-logs"></a>SSSıSDB günlüklerini temizle

SSıSDB günlükleri bekletme ilkesi, [Catalog.catalog_properties](/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database)Özellikler altında aşağıda tanımlanır:

- OPERATION_CLEANUP_ENABLED

    Değer doğru olduğunda, katalogdan RETENTION_WINDOW (gün) daha eski işlem ayrıntıları ve işlem iletileri silinir. Değer FALSE olduğunda, tüm işlem ayrıntıları ve işlem iletileri katalogda depolanır. Note: bir SQL Server işi işlemi temizleme işlemini gerçekleştirir.

- RETENTION_WINDOW

    İşlem ayrıntılarının ve işlem iletilerinin katalogda depolandığı gün sayısı. Değer-1 olduğunda, bekletme penceresi sonsuz olur. Note: temizlik istenmiyorsa, OPERATION_CLEANUP_ENABLED FALSE olarak ayarlayın.

Yönetici tarafından ayarlanan bekletme penceresinin dışındaki SSSıSDB günlüklerini kaldırmak için saklı yordamı tetikleyebilirsiniz `[internal].[cleanup_server_retention_window_exclusive]` . İsteğe bağlı olarak, saklı yordamı tetiklemek için SQL yönetilen örnek Aracısı iş yürütmeyi zamanlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [SSIS paketlerini Azure SQL yönetilen örnek Aracısı işine göre yürütme](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Iş sürekliliği ve olağanüstü durum kurtarma (BCDR) ayarlama](configure-bcdr-azure-ssis-integration-runtime.md)
- [Şirket içi SSIS iş yüklerini ADF 'de SSIS 'ye geçirme](scenario-ssis-migration-overview.md)