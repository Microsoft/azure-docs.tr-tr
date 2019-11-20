---
title: Hızlı başlangıç-SQL yönetilen örneği
description: Azure SQL veritabanı yönetilen örneği ile hızlı bir şekilde çalışmaya başlama hakkında bilgi edinin
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 07/11/2019
ms.openlocfilehash: 602de3e23eb5419958f84b071e2220550d1d04d0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821728"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Azure SQL veritabanı yönetilen örneği ile çalışmaya başlama

[Yönetilen örnek](sql-database-managed-instance-index.yml) dağıtım seçeneği, en son SQL Server şirket Içi (Enterprise Edition) veritabanı altyapısı ile %100 uyumluluğuna sahip bir veritabanı oluşturur ve bu, bir yerel [sanal ağ (VNet)](../virtual-network/virtual-networks-overview.md) uygulamasını sağlar ortak güvenlik sorunları ve şirket içi SQL Server müşterileri için uygun bir [iş modeli](https://azure.microsoft.com/pricing/details/sql-database/) . Bu makalede, yönetilen bir örneği hızlı bir şekilde yapılandırmayı ve oluşturmayı ve veritabanlarınızı geçirmeyi öğreneceksiniz.

## <a name="quickstart-overview"></a>Hızlı başlangıç genel bakış

Aşağıdaki hızlı başlangıçlarda hızlı bir şekilde yönetilen bir örnek oluşturabilir, bir sanal makineyi yapılandırabilir veya istemci uygulaması için site VPN bağlantısına işaret edersiniz ve bir veritabanını bir `.bak` dosyası kullanarak yeni yönetilen örneğinize geri yükleyebilirsiniz.

### <a name="configure-environment"></a>Ortamı yapılandırma

İlk adım olarak, ilk yönetilen örneğinizi, yerleştirileceği ağ ortamı ile oluşturmanız ve yönetilen örneğe sorgular yürüttüğünüz bilgisayardan veya sanal makineden bağlantıyı etkinleştirmeniz gerekir. Aşağıdaki kılavuzlarla yararlanabilirsiniz:

- [Azure Portal kullanarak yönetilen bir örnek oluşturun](sql-database-managed-instance-get-started.md). Azure portal, gerekli parametreleri (Kullanıcı adı/parola, çekirdek sayısı ve en fazla depolama miktarı) yapılandırıp, ağ ayrıntıları ve altyapı gereksinimleri hakkında bilgi sahibi olmadan otomatik olarak Azure ağ ortamını oluşturun. Yalnızca yönetilen bir örnek oluşturmak için izin verilen bir [abonelik türüne](sql-database-managed-instance-resource-limits.md#supported-subscription-types) sahip olduğunuzdan emin olun. Kullanmak istediğiniz kendi ağınız varsa veya ağı özelleştirmek istiyorsanız, bkz. [Azure SQL veritabanı yönetilen örneği için var olan bir sanal ağı yapılandırma](sql-database-managed-instance-configure-vnet-subnet.md) veya [Azure SQL veritabanı yönetilen örneği için bir sanal ağ oluşturma](sql-database-managed-instance-create-vnet-subnet.md).
- Yönetilen bir örnek, genel uç nokta olmadan kendi VNet 'te oluşturulur. İstemci uygulama erişimi için, **aynı VNET 'te (farklı alt ağ) BIR VM oluşturabilir** veya şu hızlı **başlangıçlardan birini kullanarak istemci bilgisayarınızdan VNET 'e Noktadan siteye VPN bağlantısı oluşturabilirsiniz** :
  - Verilerinize doğrudan ortamınızdan erişebilmek için yönetilen Örneğinizde [ortak uç noktayı](sql-database-managed-instance-public-endpoint-configure.md) etkinleştirin.
  - SQL Server Management Studio dahil olmak üzere istemci uygulama bağlantısı için [yönetilen örnek VNET 'Te Azure sanal makinesi](sql-database-managed-instance-configure-vm.md) oluşturun.
  - SQL Server Management Studio ve diğer istemci bağlantısı uygulamalarına sahip olduğunuz istemci bilgisayarınızdan [yönetilen örneğiniz için noktadan sıteye VPN bağlantısı](sql-database-managed-instance-configure-p2s.md) kurun. Bu, yönetilen örneğiniz ve sanal ağı için bağlantı için iki seçenekten oluşur.

  > [!NOTE]
  > Ayrıca, yerel ağınızdan Express Route veya siteden siteye bağlantı kullanabilirsiniz, ancak bu yaklaşımlar bu hızlı başlangıçların kapsamındadır.

Yönetilen örneği el ile oluşturmaya alternatif olarak, bu işlemi betik ve otomatikleştirebilmek için [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md), [Kaynak Yöneticisi şablonuyla POWERSHELL](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)veya [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) kullanabilirsiniz.

### <a name="migrate-your-databases"></a>Veritabanlarınızı geçirin

Yönetilen bir örnek oluşturup erişimi yapılandırdıktan sonra, veritabanlarınızı SQL Server şirket içi veya Azure VM 'lerinden geçirmeye başlayabilirsiniz. Kaynak veritabanında geçirmek istediğiniz desteklenmeyen özelliklerden bazıları varsa geçiş başarısız olur. Hatalardan kaçınmak ve uyumluluğu denetlemek için, SQL Server veritabanlarınızı çözümleyen [Data Migration Yardımcısı (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) yükleyebilir ve bir yönetilen örneğe geçişi engelleyebilen herhangi bir sorunu bulmaktan (örneğin, [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) veya birden çok günlük dosyası. Bu sorunları çöziyorsanız, veritabanınız yönetilen örneğe geçişe hazırlanmaktadır. [Veritabanı yükseltme deneyimi Yardımcısı](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) , iş yükünüzü SQL Server kaydedebilir ve yönetilen bir örneğe geçiş yaparken herhangi bir performans sorunu olduğunu belirleyebilmeniz için bir yönetilen örnek üzerinde yeniden oynamanın bir diğer faydalı aracıdır.

Veritabanınızı yönetilen bir örneğe geçirebilmeniz için yerel SQL Server geri yükleme yeteneklerini kullanarak bir veritabanını bir `.bak` dosyasından yönetilen bir örneğe geri yükleyebilirsiniz. Bu yöntemi, şirket içi veya Azure VM yüklü SQL Server veritabanı altyapısından veritabanları geçirmek için kullanabilirsiniz. Hızlı başlangıç için bkz. [yedeklemeden yönetilen bir örneğe geri yükleme](sql-database-managed-instance-get-started-restore.md). Bu hızlı başlangıçta, `RESTORE` Transact-SQL komutunu kullanarak Azure Blob depolama alanında depolanan bir `.bak` dosyasından geri yükleme yapabilirsiniz.

> [!TIP]
> Azure Blob depolama alanında veritabanınızın bir yedeğini oluşturmak için `BACKUP` Transact-SQL komutunu kullanmak için, bkz. [SQL Server URL 'ye yedekleme](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Bu hızlı başlangıç, yönetilen bir örneğe hızlı bir şekilde veritabanı yedeklemesini oluşturmanıza, yapılandırmanıza ve geri yüklemenize olanak tanır. Bazı senaryolarda, yönetilen örneklerin ve gerekli ağ ortamının dağıtımını özelleştirmeniz ya da otomatik hale getirmeniz gerekir. Bu senaryolar aşağıda açıklanacaktır.

## <a name="customize-network-environment"></a>Ağ ortamını özelleştirme

Örnek, [Azure Portal](sql-database-managed-instance-get-started.md)kullanılarak oluşturulduğunda VNET/alt ağ otomatik olarak yapılandırılabilse de, VNET ve alt ağ parametrelerini yapılandırabileceğiniz Için yönetilen örnekler oluşturmaya başlamadan önce onu oluşturmak iyi olabilir. Ağ ortamını oluşturmanın ve yapılandırmanın en kolay yolu, size örneğin yerleştirileceği ağ ve alt ağı oluşturacak ve yapılandıracağı [Azure kaynak dağıtım](sql-database-managed-instance-create-vnet-subnet.md) şablonunu kullanmaktır. Yalnızca Azure Resource Manager dağıt düğmesine basmanız ve formu parametrelerle doldurmanız gerekir.

Alternatif olarak, ağ oluşturulmasını otomatik hale getirmek için bu [PowerShell betiğini](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) de kullanabilirsiniz.

Yönetilen örneğinizi dağıtmak istediğiniz VNet ve alt ağa zaten sahipseniz, VNet ve alt ağınızın [ağ gereksinimlerini](sql-database-managed-instance-connectivity-architecture.md#network-requirements)karşıladığınızdan emin olmanız gerekir. [Alt ağınızın düzgün yapılandırıldığını doğrulamak için bu PowerShell betiğini](sql-database-managed-instance-configure-vnet-subnet.md)kullanın. Bu betik, ağınızı doğrular ve herhangi bir sorunu bildirir ve ne değiştirilmesi gerektiğini söyler ve sanal ağ/alt ağınızda gerekli değişiklikleri yapmak için teklifleri sunar. VNet/alt ağını el ile yapılandırmak istemiyorsanız bu betiği çalıştırın. Ayrıca, ağ altyapınızın önemli bir yeniden yapılandırması sonrasında da çalıştırabilirsiniz. Kendi ağınızı oluşturup yapılandırmak istiyorsanız, [yönetilen bir örnek ortamı oluşturmak ve yapılandırmak için](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01) [bağlantı mimarisini](sql-database-managed-instance-connectivity-architecture.md) ve bu üstün kılavuzu okuyun.

## <a name="migrate-to-a-managed-instance"></a>Yönetilen örneğe geçirme

Bu hızlı başlangıçlarda bulunan makaleler, bir yönetilen örneği hızlı bir şekilde ayarlamanıza ve yerel `RESTORE` özelliğini kullanarak veritabanlarınızı taşımanızı sağlar. Bu, hızlı kavram kanıtı ve çözümünüzün yönetilen örnek üzerinde çalışabileceğinizden emin olmak istiyorsanız, iyi bir başlangıç noktasıdır. 

Ancak, üretim veritabanınızı geçirmek veya bazı performans testi için kullanmak istediğiniz geliştirme/test veritabanlarını geçirmek için, şunlar gibi bazı ek teknikler kullanmayı göz önünde bulundurmanız gerekir:
- Performans testi-kaynak SQL Server Örneğiniz üzerinde temel performansı ölçmelisiniz ve bunları veritabanını geçirdiğiniz hedef yönetilen örnekteki performansla karşılaştırmalısınız. [Performans karşılaştırması için en iyi uygulamalar](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)hakkında daha fazla bilgi edinin.
- Çevrimiçi geçiş-Bu makalede açıklanan yerel `RESTORE`, veritabanlarının geri yüklenmesini beklemeniz gerekir (ve henüz depolanmıyorsa Azure Blob depolama alanına kopyalanabilir). Bu, özellikle daha büyük veritabanları için uygulamanızın bazı kesintilerine neden olur. Üretim veritabanınızı taşımak için [veri geçiş hizmeti 'ni (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) kullanarak veritabanınızı en düşük kapalı kalma süresiyle geçirin. DMS, kaynak veritabanınızda yapılan değişiklikleri geri yüklenmekte olan yönetilen örnek veritabanına artırarak bunu gerçekleştirir. Bu şekilde, uygulamanızı kaynaktan hedef veritabanına en az kapalı kalma süresi ile hızlıca geçirebilirsiniz.

[Önerilen geçiş süreci](sql-database-managed-instance-migrate.md)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

- Burada [yönetilen örnek içinde desteklenen özelliklerin üst düzey bir listesini](sql-database-features.md) ve [ayrıntılarını ve bilinen sorunları](sql-database-managed-instance-transact-sql-information.md)bulun.
- [Yönetilen örneğin teknik özellikleri](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)hakkında bilgi edinin.
- [Azure SQL veritabanı 'nda yönetilen bir örneği nasıl kullanacağınızı](sql-database-howto-managed-instance.md)daha gelişmiş bir şekilde bulabilirsiniz.
- Şirket [içi veritabanınız için doğru Azure SQL veritabanı/yönetilen örnek SKU 'Sunu belirler](/sql/dma/dma-sku-recommend-sql-db/).
