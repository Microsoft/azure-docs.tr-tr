---
title: Azure veritabanı geçiş hizmeti önkoşulları
description: Veritabanı geçişleri gerçekleştirmek için Azure veritabanı geçiş hizmeti 'nin kullanımına ilişkin önkoşullara genel bakış hakkında bilgi edinin.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 55d5594229bccb5fcb6a406e671ed104c1e12378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101094047"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Azure Veritabanı Geçiş Hizmeti'ni kullanmak için önkoşullara genel bakış

Azure veritabanı geçiş hizmeti 'nin veritabanı geçişleri gerçekleştirirken sorunsuz bir şekilde çalışmasını sağlamak için birkaç önkoşul gereklidir. Bazı Önkoşullar, hizmet tarafından desteklenen tüm senaryolarda (kaynak-hedef çiftleri) uygulanır, ancak diğer Önkoşullar belirli bir senaryoya özeldir.

Azure veritabanı geçiş hizmeti kullanılarak ilişkili önkoşullar aşağıdaki bölümlerde listelenmiştir.

## <a name="prerequisites-common-across-migration-scenarios"></a>Geçiş senaryolarında ortak olan Önkoşullar

Tüm desteklenen geçiş senaryolarında ortak olan Azure veritabanı geçiş hizmeti önkoşulları şunlar için gereklidir:

* [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure sanal ağ oluşturun.
* Sanal ağ ağ güvenlik grubu (NSG) kurallarınızın, ServiceBus, Storage ve AzureMonitor için ServiceTag giden bağlantı noktası 443 ' i engellemediğinden emin olun. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
* Kaynak veritabanınızın önünde bir güvenlik duvarı gereci kullanırken, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanına erişmesine izin vermek üzere güvenlik duvarı kuralları eklemeniz gerekebilir.
* [Windows Güvenlik Duvarınızı veritabanı altyapısı erişimi](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
* [Sunucu Ağ Protokolünü Etkinleştirme veya Devre Dışı Bırakma](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) makalesindeki yönergeleri izleyerek SQL Server Express yüklemesi sırasında varsayılan olarak devre dışı bırakılan TCP/IP protokolünü etkinleştirin.

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

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>SQL Server Azure SQL veritabanı 'na geçirmek için Önkoşullar

Tüm geçiş senaryolarında ortak olan Azure veritabanı geçiş hizmeti önkoşullarının yanı sıra, özellikle bir senaryoya veya başka bir senaryoya uygulanan Önkoşullar da vardır.

Azure veritabanı geçiş hizmeti 'ni kullanarak Azure SQL veritabanı geçişlerini SQL Server gerçekleştirirken, tüm geçiş senaryolarında ortak olan önkoşullara ek olarak, aşağıdaki ek önkoşulları ele aldığınızdan emin olun:

* Azure SQL veritabanı örneği [için Azure Portal Azure SQL veritabanı 'nda veritabanı oluşturma](../azure-sql/database/single-database-create-quickstart.md)makalesindeki ayrıntıyı izleyerek yaptığınız bir örnek oluşturun.
* [Data Migration Yardımcısı](https://www.microsoft.com/download/details.aspx?id=53595) 3.3 veya üzeri sürümünü indirip yükleyin.
* Azure Veritabanı Geçiş Hizmeti'ne kaynak SQL Server erişimi sağlamak için Windows güvenlik duvarınızı açın. Varsayılan ayarlarda 1433 numaralı TCP bağlantı noktası kullanılır.
* Dinamik bağlantı noktası kullanarak birden fazla adlandırılmış SQL Server örneği çalıştırıyorsanız, Azure Veritabanı Geçiş Hizmeti'nin kaynak sunucunuzdaki adlandırılmış örneğe bağlanabilmesi için SQL Browser Hizmeti'ni etkinleştirebilir ve güvenlik duvarınızda 1434 numaralı UDP bağlantı noktasına erişim izni verebilirsiniz.
* Azure veritabanı geçiş hizmeti 'nin hedef veritabanlarına erişmesine izin vermek için SQL veritabanı için sunucu düzeyinde bir [güvenlik duvarı kuralı](../azure-sql/database/firewall-configure.md) oluşturun. Azure veritabanı geçiş hizmeti için kullanılan sanal ağın alt ağ aralığını belirtin.
* SQL Server örneğine bağlanmak için kullanılan kimlik bilgilerinin [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql) izinlerine sahip olduğundan emin olun.
* Hedef veritabanına bağlanmak için kullanılan kimlik bilgilerinin hedef veritabanında DENETIM VERITABANı iznine sahip olduğundan emin olun.

   > [!NOTE]
   > Azure veritabanı geçiş hizmeti 'ni kullanarak SQL Server Azure SQL veritabanı 'na geçiş işlemi gerçekleştirmek için gereken önkoşulların tüm listesi için bkz. [Azure SQL veritabanı 'Na geçiş SQL Server](./tutorial-sql-server-to-azure-sql.md).
   >

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance"></a>SQL Server Azure SQL yönetilen örneği 'ne geçirme önkoşulları

* [Azure Portal Azure SQL yönetilen örneği oluşturma](../azure-sql/managed-instance/instance-create-quickstart.md)makalesindeki ayrıntıyı IZLEYEREK bir SQL yönetilen örneği oluşturun.
* Azure veritabanı geçiş hizmeti IP adresi veya alt ağ aralığı için bağlantı noktası 445 üzerindeki SMB trafiğine izin vermek üzere güvenlik duvarlarını açın.
* Azure Veritabanı Geçiş Hizmeti'ne kaynak SQL Server erişimi sağlamak için Windows güvenlik duvarınızı açın. Varsayılan ayarlarda 1433 numaralı TCP bağlantı noktası kullanılır.
* Dinamik bağlantı noktası kullanarak birden fazla adlandırılmış SQL Server örneği çalıştırıyorsanız, Azure Veritabanı Geçiş Hizmeti'nin kaynak sunucunuzdaki adlandırılmış örneğe bağlanabilmesi için SQL Browser Hizmeti'ni etkinleştirebilir ve güvenlik duvarınızda 1434 numaralı UDP bağlantı noktasına erişim izni verebilirsiniz.
* Kaynak SQL Server ve hedef Yönetilen Örnek bağlantısı kurmak için kullanılan oturum açma bilgilerinin sysadmin sunucu rolüne üye olduğundan emin olun.
* Azure Veritabanı Geçiş Hizmeti'nin kaynak veritabanını yedeklemek için kullanabileceği bir ağ paylaşımı oluşturun.
* Kaynak SQL Server örneğini çalıştıran hizmet hesabının oluşturduğunuz ağ paylaşımında yazma ayrıcalıklarına sahip olduğundan ve kaynak sunucunun bilgisayar hesabının aynı paylaşımda okuma/yazma erişimine sahip olduğundan emin olun.
* Önceden oluşturduğunuz ağ paylaşımında tam denetim ayrıcalığına sahip olan Windows kullanıcısını (ve parolasını) not edin. Azure veritabanı geçiş hizmeti, yedekleme dosyalarını geri yükleme işlemi için Azure depolama kapsayıcısına yüklemek üzere Kullanıcı kimlik bilgisini taklit eder.
* [Depolama Gezgini Ile Azure Blob depolama kaynaklarını yönetme](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)makalesindeki adımları kullanarak bir blob kapsayıcısı oluşturun ve SAS URI 'sini alın. SAS URI 'sini oluştururken ilke penceresinde tüm izinleri (okuma, yazma, silme, liste) seçtiğinizden emin olun.

   > [!NOTE]
   > Azure veritabanı geçiş hizmeti 'ni SQL Server SQL yönetilen örneği 'ne geçiş gerçekleştirmek üzere kullanmak için gereken önkoşulların tamamen listesi için, bkz. [SQL Server SQL yönetilen örneği 'Ne geçirme](./tutorial-sql-server-to-managed-instance.md)öğreticisi.

## <a name="next-steps"></a>Sonraki adımlar

Azure veritabanı geçiş hizmeti ve bölgesel kullanılabilirliğine genel bakış için [Azure veritabanı geçiş hizmeti nedir](dms-overview.md)makalesine bakın.