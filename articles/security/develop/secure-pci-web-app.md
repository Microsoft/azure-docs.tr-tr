---
title: PCI DSS için güvenli Web uygulaması | Microsoft Docs
description: Bu örnek uygulama, Azure 'da geliştirme yaparken uygulamanızı ve kuruluşunuzun güvenlik duruşunu geliştiren en iyi güvenlik uygulamalarını uygular.
keywords: yana
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992620"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>PCI uygulaması için güvenli bir altyapı geliştirme

## <a name="overview"></a>Genel Bakış

Bir ödeme kartı sektör (PCI) uygulaması için bu güvenli altyapı, kart sahibi verilerinin toplanması, depolanması ve alınması için uygun bir ödeme kartı sektör platformu hizmet olarak (PaaS) ortamının dağıtımına yönelik rehberlik sağlar. Bu çözüm, müşterilerin belirli güvenlik ve uyumluluk gereksinimlerini karşılayabilme yollarını gösteren ve müşterilerin derlenmesi için bir temel görevi gören bir ortak başvuru mimarisi için Azure kaynaklarının dağıtımını ve yapılandırmasını otomatikleştirir. Azure 'da kendi çözümlerini yapılandırın. Çözüm, ödeme kartı sektör verileri güvenlik standartlarına benzer bir gereksinimlerin alt kümesini uygular (PCI DSS 3,2).

Bu örnek, müşterilerin PCI DSS 3,2 gereksinimlerine benzer bir uyumluluk sağlamasına yardımcı olmak için önceden yapılandırılmış güvenlik denetimleriyle birlikte PaaS Web uygulaması başvuru mimarisini otomatik olarak dağıtır. Çözüm, kaynak dağıtımı ve yapılandırmasına kılavuzluk eden Azure Resource Manager şablonlarından ve PowerShell betiklerinden oluşur.

Uygulama bileşenlerinin düzgün yapılandırıldığından emin olmak için bu makalede açıklanan adımları sırayla izlemelisiniz. Veritabanı, Azure App Service, Azure Key Vault örneği ve Azure Application Gateway örneği birbirlerine bağlıdır.

Dağıtım betikleri altyapıyı ayarlar. Dağıtım betiklerini çalıştırdıktan sonra, bileşenleri ve Hizmetleri birbirine bağlamak için Azure portal el ile yapılandırma yapmanız gerekir.

Bu örnek, perakende sektörü dahilinde çalışan ve güvenli Azure altyapısıyla perakende bir uygulama oluşturmak isteyen Azure 'da deneyimli geliştiricilere yöneliktir.

> [!NOTE]
> Bu mimarinin, müşterilerin belirli gereksinimlerine göre ayarlanmalarına yönelik bir temel işlevi görmesi ve bir üretim ortamında olduğu gibi kullanılmamalıdır.

Bir uygulamayı bu ortama değişiklik olmadan dağıtmak, PCI DSS 3,2 ' nin gereksinimlerini tamamen karşılamak için yeterli değildir. Şunlara dikkat edin:

- Bu mimari, müşterilerin Azure 'ı PCI DSS 3,2 uyumlu bir şekilde kullanmasına yardımcı olmak için bir temel sağlar.
- Müşteriler, her bir müşterinin uygulamasının özelliklerine göre farklılık gösterebileceğinden, bu mimari kullanılarak oluşturulan herhangi bir çözüme uygun güvenlik ve uyumluluk değerlendirmesi yürütmekten sorumludur.

Bu uygulamayı geliştirme ve dağıtma bölümünde şunları yapmayı öğreneceksiniz:

- Azure Key Vault bir örnek oluşturun ve bundan parolaları depolayın ve alın.
- Azure SQL için Azure veritabanı 'nı dağıtın, güvenli veri ayarlayın ve erişim yetkisi verin.
- Azure Web uygulamasını, ön uç güvenlik duvarı aEcess ile ayrılmış bir yalıtılmış olan App Service ortamıyla dağıtın.
- [OWASP Top 10 RuleSet](https://coreruleset.org/)kullanan bir güvenlik duvarı ile Azure Application Gateway örneği oluşturun ve yapılandırın.
- Azure hizmetlerini kullanarak geçişte ve bekleyen verilerin şifrelenmesini etkinleştirin.
- Karmaşıklığınızı değerlendirmek için Azure ilkesini ayarlama ve mavi baskı

Bu uygulamayı geliştirip dağıttıktan sonra, açıklanan yapılandırma ve güvenlik ölçüleriyle birlikte aşağıdaki örnek Web uygulamasını ayarlamış olursunuz.

## <a name="architecture-diagram-and-components"></a>Mimari diyagramı ve bileşenleri
Uygulama, üç katman içeren tipik bir n katmanlı uygulamadır. İzleme ve gizli yönetim bileşenleriyle tümleşik olan ön uç, arka uç ve veritabanı katmanı burada gösterilmektedir:

![Uygulama mimarisi](./media/secure-pci-web-app/architecture.png)

Mimari aşağıdaki bileşenlerden oluşur:

- [App Service ortamı v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Uygulama mimarimiz için App Service Ortamı ve yük dengeleyici sağlar.
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/). Uygulama mimarimiz için ağ geçidini ve güvenlik duvarını sağlar.
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Birden çok platformda genişletilebilir bir uygulama performans yönetimi (APM) hizmeti sağlar.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Uygulamanın gizli dizilerini depolar ve şifreler ve bunların çevresindeki erişim ilkelerinin oluşturulmasını yönetir.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Bulut tabanlı kimlik ve erişim yönetimi hizmeti sağlar, oturum açın ve kaynaklara erişin.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). Etki alanını barındıracak hizmeti sağlar.
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Uygulamalarınızı ölçeklendirir ve hizmetleriniz için yüksek kullanılabilirlik oluşturur
- [Azure Depolama](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Modern veri depolama senaryoları için çözüm sağlar.
- [Azure Web App](https://docs.microsoft.com/azure/app-service/overview/).  Web uygulamalarını barındırmak için HTTP tabanlı bir hizmet sağlar.
- [AzureSQL Için Azure veritabanı](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Uygulama verilerini güvenli bir şekilde depolar.
- [Azure şemaları](https://docs.microsoft.com/azure/governance/blueprints/overview/). Belirli standartlara ve gereksinimlere sahip belirtimler ve uyumluluk sağlar.
- [Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/security-center/)
- [Azure ilkesi](https://docs.microsoft.com/azure/governance/policy/overview). Atanan ilkelerle uyumlu olmayan kaynaklarınızı hesaplar.

## <a name="threat-model"></a>Tehdit modeli
Tehdit modellemesi, işletmenizin ve uygulamanızın olası güvenlik tehditlerini tanımlama ve daha sonra uygun bir risk azaltma planının yerinde olmasını sağlama işlemidir.

Bu örnek, güvenli örnek uygulama için tehdit modellemesini uygulamak üzere [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) kullandı. Bileşenleri ve veri akışlarını diyagram oluşturarak, geliştirme sürecinde sorunları ve tehditleri erkenden ayırt edebilirsiniz. Bu, daha sonra zaman ve para tasarrufu sağlar.

Örnek uygulama için tehdit modelidir:

![Tehdit modeli](./media/secure-pci-web-app/threat-model.png)

Tehdit modelleme aracının ürettiği bazı örnek tehditler ve olası güvenlik açıkları aşağıdaki ekran görüntüsünde gösterilmiştir. Tehdit modeli, sunulan saldırı yüzeyine genel bir bakış sunar ve geliştiricilerin sorunları nasıl azaltacağını düşündüğünü ister.

![Tehdit modeli çıkışı](./media/secure-web-app/threat-model-output.png)

Örneğin, önceki tehdit modeli çıktısına SQL ekleme, Kullanıcı girişlerini silerek ve PostgreSQL için Azure veritabanı 'nda saklı işlevler kullanılarak azaltılmaktadır. Bu hafifletme, veri okuma ve yazma işlemleri sırasında sorguların rastgele yürütülmesini önler.

Geliştiriciler tehdit modeli çıktısındaki tehditleri her birini azaltacak şekilde sistemin genel güvenliğini geliştirir.

## <a name="deployment"></a>Dağıtım
### <a name="prerequisites"></a>Önkoşullar
Uygulamayı çalışır duruma getirmek için şu araçları yüklemeniz gerekir:

- Uygulama kodunu değiştirmek ve görüntülemek için bir kod Düzenleyicisi. [Visual Studio Code](https://code.visualstudio.com/) açık kaynaklı bir seçenektir.
- Geliştirme bilgisayarınızda [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) .
- Sisteminizde [Git](https://git-scm.com/) . Git, kaynak kodu yerel olarak kopyalamak için kullanılır.
- [JQ](https://stedolan.github.io/jq/), JSON 'u Kullanıcı dostu bir şekilde sorgulamak IÇIN bir UNIX aracıdır.

Bu örnek, Azure 'da kaynak dağıtmak üzere Azure Resource Manager API hizmeti tarafından işlenen JSON yapılandırma dosyalarından ve PowerShell betiklerinden oluşur. Ayrıntılı dağıtım yönergelerine [buradan](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM)ulaşabilirsiniz.

#### <a name="quickstart"></a>Hızlı Başlangıç

1.  [Bu](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) GitHub deposunu yerel iş istasyonunuza kopyalayın veya indirin.
2.  0-Setup-AdministrativeAccountAndPermission.md inceleyin ve sunulan komutları çalıştırın.
3.  Contoso örnek verileriyle bir test çözümü dağıtın veya bir başlangıç üretim ortamı pilot.

    Bu betik, contoso örnek verilerini kullanarak bir Web deposunun tanıtımı için Azure kaynaklarını dağıtır.

Bu örnekte, Web uygulamasını App Service dağıtan dağıtım betiğini çalıştırın ve kaynakları oluşturur.

Azure 'da uygulama dağıtmanın birçok yolu vardır; örneğin:

- Azure Resource Manager şablonları
- PowerShell
- Azure CLI
- Azure portal
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Kılavuz ve öneriler

### <a name="network"></a>Ağ
Mimari, adres alanı 10.200.0.0/16 olan bir özel sanal ağ tanımlar.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Ağ güvenlik grupları
Ağ güvenlik grupları (https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) bir sanal ağ içindeki trafiğe izin veren veya reddeden Access Control listeleri (ACL 'ler) içerir. Ağ güvenlik grupları, trafiği bir alt ağda veya tek bir VM düzeyinde güvenli hale getirmek için kullanılabilir. Aşağıdaki ağ güvenlik grupları var:

- Application Gateway için 1 ağ güvenlik grubu
- App Service Ortamı için 1 ağ güvenlik grubu
- Azure SQL veritabanı için 1 ağ güvenlik grubu
- Savunma ana bilgisayarı için 1 ağ güvenlik grubu

Ağ güvenlik gruplarının her biri, çözümün güvenli ve doğru bir şekilde çalışabilmesi için belirli bağlantı noktaları ve protokoller açar. Ayrıca, her bir ağ güvenlik grubu için aşağıdaki yapılandırma etkinleştirilir:

- Tanılama günlükleri ve olayları (https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) etkin ve bir depolama hesabında depolanır)
- Azure Izleyici günlükleri ağ güvenlik grubunun tanılamalarına bağlanır (https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG yapılandırması
App Service Ortamı için NSG yapılandırması, aşağıdaki görüntüde gösterildiği gibi yapılandırılmalıdır.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Her alt ağ, karşılık gelen ağ güvenlik grubuyla ilişkilendirilir.

### <a name="config"></a>Config
Alt ağlar aşağıdaki görüntüde gösterildiği gibi yapılandırılır.
 ![Kurulumunun](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
Etki alanı adı sistemi (DNS), IP adresine bir Web sitesi veya hizmet adı çevirmekten (veya çözümlemeden) sorumludur. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) , Azure altyapısını kullanarak ad ÇÖZÜMLEMESI sağlayan DNS etki alanları için bir barındırma hizmetidir. Kullanıcılar, Azure 'da etki alanlarını barındırarak DNS kayıtlarını, diğer Azure hizmetleriyle aynı kimlik bilgilerini, API 'Leri, araçları ve faturalandırmayı kullanarak yönetebilir. Ayrıca, özel DNS etki alanlarını da destekler Azure DNS.

### <a name="protect-data"></a>Veri koruma
Buluttaki verilerin korunmasına yardımcı olmak için, verilerinizin gerçekleşebileceği olası durumları ve bu durum için hangi denetimlerin kullanılabileceğini hesaba getirmeniz gerekir. Azure veri güvenliği ve şifreleme için en iyi uygulamalar aşağıdaki veri durumlarıyla ilgilidir:

- Bekleyen: Bu, manyetik veya optik disk olmasına bakılmaksızın fiziksel medyada statik olarak bulunan tüm bilgi depolama nesnelerini, kapsayıcıları ve türlerini içerir.
- Aktarım aşamasında: Veriler bileşenler, konumlar veya programlar arasında aktarıldığında aktarım sürecinde olur. Örnekler, bir Service Bus (Şirket içinden buluta veya ExpressRoute gibi karma bağlantılar da dahil olmak üzere) üzerinden veya bir giriş/çıkış sürecinde ağ üzerinden aktarım yapılır.

### <a name="azure-storage"></a>Azure Storage
Rest gereksinimlerinde şifrelenmiş verileri karşılamak için tüm [Azure depolama](https://azure.microsoft.com/services/storage/) , verileri erişen ve şifreleyen anahtarların denetimini sürdürmek için Azure Key Vault kullanır. Bu, PCI DSS 3,2 tarafından tanımlanan kurumsal güvenlik taahhütlerini ve uyumluluk gereksinimlerini desteklemeye karşı, cardş verilerinin korunmasını ve korunmasını sağlar.

### <a name="azure-disk-encryption"></a>Azure Disk Şifrelemesi
Azure disk şifrelemesi, veri diskleri için birim şifrelemesi sağlamak üzere Windows 'un BitLocker özelliğinden yararlanır. Çözüm, disk şifreleme anahtarlarının denetlenmesi ve yönetilmesine yardımcı olmak için Azure Key Vault ile tümleşir.

### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL veritabanı örneği aşağıdaki veritabanı güvenlik önlemlerini kullanır:

- [Active Directory kimlik doğrulaması ve yetkilendirme](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) , veritabanı kullanıcılarının ve diğer Microsoft hizmetlerinin tek bir merkezi konumda kimlik yönetimine izin verebilir.
- [SQL veritabanı denetimi](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) , veritabanı olaylarını izler ve bunları Azure Storage hesabındaki bir denetim günlüğüne yazar.
- Azure SQL veritabanı, bekleyen bilgileri korumak üzere veritabanı, ilişkili yedeklemeler ve işlem günlüğü dosyalarını gerçek zamanlı şifrelemeyi ve şifre çözmeyi gerçekleştiren [Saydam veri şifrelemesini](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)kullanacak şekilde yapılandırılmıştır. Saydam veri şifrelemesi, depolanan verilerin yetkisiz erişime maruz olmadığından emin olmak için güvence sağlar.
- [Güvenlik duvarı kuralları](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) , uygun izinler verilene kadar veritabanı sunucularına tüm erişimi engeller. Güvenlik duvarı, her bir isteğin kaynak IP adresine göre veritabanlarına erişim verir.
- [SQL tehdit algılama](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) , şüpheli veritabanı etkinlikleri, olası güvenlik AÇıKLARı, SQL ekleme saldırıları ve anormal veritabanı erişim desenleri için güvenlik uyarıları sunarak meydana gelebilecek olası tehditlere yönelik algılama ve yanıt verir.
- [Şifrelenmiş sütunlar](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) , gizli verilerin veritabanı sisteminde hiçbir şekilde düz metin olarak göründüğünden emin olmanızı sağlamaktır. Veri şifrelemeyi etkinleştirdikten sonra, yalnızca anahtarlara erişimi olan istemci uygulamaları veya uygulama sunucuları düz metin verilerine erişebilir.
- [SQL veritabanı dinamik veri maskeleme](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) , verileri ayrıcalıklı olmayan kullanıcılara veya uygulamalara maskeleyerek hassas veri pozlamasını sınırlar. Dinamik veri maskeleme, potansiyel olarak duyarlı verileri otomatik olarak bulabilir ve uygun maskeleri uygulanmasını önerebilir. Bu, verilere erişimi, yetkisiz erişim aracılığıyla veritabanından çıkmayacak şekilde tanımanıza ve azaltmaya yardımcı olur. Müşteriler, dinamik veri maskeleme ayarlarının veritabanı şemasına uyacak şekilde ayarlamaktan sorumludur.

### <a name="identity-management"></a>Kimlik yönetimi
Aşağıdaki teknolojiler, Azure ortamındaki cardş verilerine erişimi yönetmek için yetenekler sağlar:

- Azure Active Directory, Microsoft 'un çok kiracılı bulut tabanlı dizin ve kimlik yönetimi hizmetidir. Bu çözüme yönelik tüm kullanıcılar Azure SQL veritabanına erişen kullanıcılar dahil Azure Active Directory oluşturulur.
- Uygulamanın kimlik doğrulaması, Azure Active Directory kullanılarak gerçekleştirilir. Daha fazla bilgi için bkz. [uygulamaları Azure Active Directory tümleştirme](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). Ayrıca, veritabanı sütun şifrelemesi uygulamanın kimliğini Azure SQL veritabanı 'nda doğrulamak için Azure Active Directory kullanır. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda hassas verileri koruma](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Azure rol tabanlı erişim denetimi, yöneticilerin yalnızca kullanıcıların işlerini gerçekleştirmesi için ihtiyaç duyduğu erişim miktarını vermek üzere hassas erişim izinleri tanımlamasına olanak sağlar. Yöneticiler, her kullanıcıya Azure kaynakları için Kısıtlanmamış izin vermek yerine, kart sahibi verilerine erişim için yalnızca belirli eylemlere izin verebilir. Abonelik erişimi, abonelik yöneticisiyle sınırlıdır.
- Azure Active Directory Privileged Identity Management, müşterilerin cardş verileri gibi belirli bilgilere erişimi olan kullanıcı sayısını en aza indirmesine olanak sağlar. Yöneticiler, ayrıcalıklı kimlikleri ve kaynaklara erişimlerini bulma, kısıtlama ve izleme işlemleri için Azure Active Directory Privileged Identity Management kullanabilir. Bu işlev, gerektiğinde isteğe bağlı, tam zamanında yönetim erişimi sağlamak için de kullanılabilir.
- Azure Active Directory Kimlik Koruması bir kuruluşun kimliklerini etkileyen olası güvenlik açıklarını algılar, bir kuruluşun kimlikleriyle ilişkili şüpheli eylemler için otomatik yanıtlar yapılandırır ve kuşkulu araştırır olayları çözmek için uygun eylemi gerçekleştirin.

### <a name="secrets-management"></a>Gizli dizi yönetimi
Çözüm, anahtarların ve parolaların yönetimi için Azure Key Vault kullanır. Azure Anahtar Kasası, bulut uygulamaları ve hizmetleri tarafından kullanılan şifreleme anahtarlarının ve gizli anahtarların korunmasına yardımcı olur. Aşağıdaki Azure Key Vault özellikleri müşterilerin bu verileri korumalarına ve bu verilere erişmesine yardımcı olur:

- Gelişmiş erişim ilkeleri, gereksinim temelinde yapılandırılır.
- Key Vault erişim ilkeleri anahtarlar ve gizlilikler için gereken en düşük izinlerle tanımlanır.
- Key Vault tüm anahtarlar ve gizli dizileri için süre sonu tarihleri vardır.
- Key Vault tüm anahtarlar özel donanım güvenliği modülleri tarafından korunur. Anahtar türü bir HSM korumalı 2048 bit RSA anahtarıdır.
- Key Vault, anahtarları ve gizli dizileri (kimlik doğrulaması anahtarları, depolama hesabı anahtarları, veri şifreleme anahtarları,) şifreleyebilirsiniz. PFX dosyaları ve parolalar), donanım güvenlik modülleri (HSM 'ler) tarafından korunan anahtarlar kullanılarak
- Belirli bir kapsamdaki kullanıcılara, gruplara ve uygulamalara izinler atamak için RBAC kullanın.
- Otomatik yenilemeyle TLS sertifikalarınızı yönetmek için Key Vault kullanın.
- Key Vault için tanılama günlükleri, en az 365 günlük bir bekletme dönemi ile etkinleştirilir.
- Anahtarlar için izin verilen şifreleme işlemleri, gerekli olanlarla kısıtlıdır.

### <a name="azure-security-center"></a>Azure Güvenlik Merkezi
Azure Güvenlik Merkezi ile, müşteriler iş yükleri genelinde güvenlik ilkelerini merkezi olarak uygulayabilir ve yönetebilir, tehditlere maruz kalma olasılığını sınırlayabilir, saldırıları algılayıp yanıtlayabilir. Ayrıca, Azure Güvenlik Merkezi, güvenlik duruşunu artırmaya ve verileri korumaya yardımcı olmak için yapılandırma ve hizmet önerileri sağlamak üzere Azure hizmetleri 'nin mevcut yapılandırmalarına erişir.

Azure Güvenlik Merkezi, ortamlarını hedefleyen olası saldırılardan müşterileri uyarmak için çeşitli algılama özellikleri kullanır. Bu uyarılar uyarıyı neyin tetiklediği, hedeflenen kaynaklar ve saldırının kaynağı hakkındaki değerli bilgileri içerir. Azure Güvenlik Merkezi, bir tehdit ya da şüpheli etkinlik gerçekleştiğinde tetiklenen bir dizi önceden tanımlanmış güvenlik uyarısı içerir. Azure Güvenlik Merkezi 'ndeki özel uyarı kuralları, müşterilerin ortamlarından zaten toplanmış olan verileri temel alarak yeni güvenlik uyarıları tanımlamasına olanak tanır.

Azure Güvenlik Merkezi, öncelik veren güvenlik uyarıları ve olayları sunarak müşterilerin olası güvenlik sorunlarını bulmasını ve adreslerini daha kolay hale getirmesini sağlar. Algılanan her tehdit için tehdit zekası raporu, tehditleri İnceleme ve düzeltme konusunda olay yanıtı ekiplerine yardımcı olacak şekilde oluşturulmuştur.

### <a name="azure-application-gateway"></a>Azure Application Gateway
Mimari, bir Web uygulaması güvenlik duvarı yapılandırılmış bir Azure Application Gateway kullanan güvenlik açıklarına karşı risk düzeyini azaltır ve OWASP kural kümesi etkindir. Ek yetenekler şunlardır:

- Uçtan uca SSL
- TLS v 1.0 ve v 1.1 'yi devre dışı bırak
- TLSv 1.2 'yi etkinleştir
- Web uygulaması güvenlik duvarı (önleme modu)
- OWASP 3,0 RuleSet ile önleme modu
- Tanılama günlüğünü etkinleştirme
- Özel durum araştırmaları
- Azure Güvenlik Merkezi ve Azure Danışmanı ek koruma ve bildirimler sağlar. Azure Güvenlik Merkezi ayrıca bir saygınlık sistem sağlar.

### <a name="logging-and-auditing"></a>Günlüğe kaydetme ve denetleme
Azure Hizmetleri, sistem durumunun yanı sıra sistem durumu ve Kullanıcı etkinliklerini de yoğun olarak günlüğe kaydeder:

- [Etkinlik günlükleri](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) , bir abonelikteki kaynaklarda gerçekleştirilen işlemlerle ilgili öngörüler sağlar. Etkinlik günlükleri, bir işlemin başlatıcısının, oluşma süresinin ve durumunun belirlenmesine yardımcı olabilir.
- [Tanılama günlükleri](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) her kaynak tarafından yayılan tüm günlükleri içerir. Bu Günlükler Windows olay sistemi günlükleri, Azure depolama günlükleri, Key Vault denetim günlükleri ve Application Gateway erişim ve güvenlik duvarı günlükleri içerir. Tüm tanılama günlükleri, arşivleme için merkezi ve şifrelenmiş bir Azure depolama hesabına yazar. Bekletme, kuruluşa özgü saklama gereksinimlerini karşılamak için Kullanıcı tarafından yapılandırılabilir ve 730 güne kadar.

### <a name="azure-monitor-logs"></a>Azure İzleyici günlükleri
Bu Günlükler, işleme, depolama ve Pano raporlama için [Azure izleyici günlüklerinde](https://azure.microsoft.com/services/log-analytics/) birleştirilir. Toplandıktan sonra veriler, özgün kaynağından bağımsız olarak tüm verilerin birlikte çözümlenme olanağı sağlayan Log Analytics çalışma alanları içindeki her bir veri türü için ayrı tablolar halinde düzenlenir. Ayrıca, Azure Güvenlik Merkezi, müşterilerin güvenlik olay verilerine erişmek ve diğer hizmetlerdeki verilerle birleştirmek için kusto sorguları kullanmasına izin veren Azure Izleyici günlükleri ile tümleşir.

Aşağıdaki Azure [izleme çözümleri](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) , bu mimarinin bir parçası olarak dahil edilmiştir:

- [Active Directory değerlendirmesi](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory sistem durumu denetimi çözümü, düzenli bir aralıkta sunucu ortamlarının riskini ve sistem durumunu değerlendirir ve dağıtılan Sunucu altyapısına özgü önerilerin öncelikli bir listesini sağlar.
- [SQL değerlendirmesi](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL sistem durumu denetimi çözümü, düzenli aralıklarla sunucu ortamlarının riskini ve sistem durumunu değerlendirir ve müşterilere dağıtılan Sunucu altyapısına özgü önerilerin öncelikli bir listesini sağlar.
- [Aracı durumu](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Aracı Durumu çözümü, kaç aracının dağıtıldığını ve coğrafi dağıtımını, ayrıca kaç aracının yanıt vermemesine ve işletimsel verileri gönderen aracıların sayısını bildirir.
- [Etkinlik günlüğü Analizi](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Etkinlik Günlüğü Analizi çözümü, bir müşterinin tüm Azure aboneliklerinde Azure etkinlik günlüklerinin analizine yardımcı olur.

### <a name="azure-monitor"></a>Azure İzleyici
[Azure izleyici](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) , kuruluşların Azure kaynaklarında API çağrılarını izleme de dahil olmak üzere performansı izlemelerine, uyarıları oluşturmalarına ve verileri arşivlemesini sağlayarak eğilimleri belirlemesine yardımcı olur.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) , birden çok platformda Web geliştiricileri için genişletilebilir bir uygulama performansı yönetim hizmetidir. Application Insights, performans bozuklularını algılar ve müşteriler Canlı Web uygulamasını izlemek için kullanabilir. Müşterilerin sorunları tanılamasına ve hangi kullanıcıların uygulamayla gerçekten ne yaptığını anlamalarına yardımcı olan güçlü analiz araçları içerir. Müşterilerin performansı ve kullanılabilirliği sürekli gelişmesine yardımcı olmak için tasarlanmıştır.

### <a name="azure-key-vault"></a>Azure Key Vault
Anahtar depolamak istediğiniz kuruluş için bir kasa oluşturun ve aşağıdaki gibi işletimsel görevler için sorumlulukları koruyun:

- Key Vault depolanan veriler şunları içerir:

   - Application Insight anahtarı
   - Veri depolama erişim anahtarı
   - Bağlantı dizesi
   - Veri tablosu adı
   - Kullanıcı Kimlik Bilgileri

- Gelişmiş erişim ilkeleri bir gereksinim temelinde yapılandırılır
- Key Vault erişim ilkeleri anahtarlar ve gizlilikler için gereken en düşük izinlerle tanımlanır
- Key Vault tüm anahtarlar ve gizli dizileri sona erme tarihlerine sahiptir
- Key Vault tüm anahtarlar HSM tarafından korunuyor [anahtar türü = HSM korumalı 2048-bit RSA anahtarı]
- Tüm kullanıcılara/kimliklere rol tabanlı Access Control (RBAC) kullanarak gerekli olan en düşük izinler verilir
- Uygulamalar birbirlerine güvenmedikleri ve çalışma zamanında aynı gizli anahtarlara erişmesi gereken bir Key Vault paylaşmaz
- Key Vault için tanılama günlükleri, en az 365 günlük bir bekletme dönemi ile etkinleştirilir.
- Anahtarlar için izin verilen şifreleme işlemleri, gerekli olanlarla kısıtlıdır

### <a name="vpn-and-expressroute"></a>VPN ve ExpressRoute
Güvenli bir VPN tüneli veya [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) , bu PaaS Web uygulaması başvuru mimarisinin bir parçası olarak dağıtılan kaynaklarla güvenli bir şekilde bağlantı kuracak şekilde yapılandırılmalıdır. Bir VPN veya ExpressRoute 'u uygun şekilde ayarlayarak, müşteriler aktarım sırasında veriler için bir koruma katmanı ekleyebilir.

Azure ile güvenli bir VPN tüneli uygulayarak, şirket içi ağ ve Azure sanal ağı arasında sanal bir özel bağlantı oluşturulabilir. Bu bağlantı Internet üzerinden gerçekleşir ve müşterilerin, müşterinin ağı ile Azure arasındaki şifrelenmiş bir bağlantı içindeki bilgileri güvenli bir şekilde "tünele" tüneletmesine olanak tanır. Siteden siteye VPN, delikler için tüm boyutlardaki kuruluşlar tarafından dağıtılan güvenli, Olgun bir teknolojidir. IPSec tünel modu Bu seçenekte bir şifreleme mekanizması olarak kullanılır.

VPN tünelinin içindeki trafik, siteden siteye VPN ile Internet 'Te gezintiğinden, Microsoft başka bir daha bile daha güvenli bağlantı seçeneği sunar. Azure ExpressRoute, Azure ile şirket içi bir konum veya bir Exchange barındırma sağlayıcısı arasındaki adanmış bir WAN bağlantıdır. ExpressRoute bağlantıları Internet üzerinden gitmediğinden, bu bağlantılar daha fazla güvenilirlik, daha hızlı hız, daha düşük gecikme süreleri ve Internet üzerinden tipik bağlantılardan daha yüksek güvenlik sunar. Ayrıca, bu müşterinin iletişim sağlayıcısının doğrudan bir bağlantısı olduğundan, veriler Internet üzerinden hareket etmez ve bu nedenle bu açık değildir.

Şirket içi bir ağı Azure 'a genişleten güvenli bir karma ağ uygulamak için en iyi yöntemler [mevcuttur](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="cost-considerations"></a>Maliyetle ilgili konular
Henüz bir Azure hesabınız yoksa, ücretsiz bir hesap oluşturabilirsiniz. Kullanmaya başlamak için [ücretsiz hesap sayfasına](https://azure.microsoft.com/free/) gidin, ücretsiz bir Azure hesabıyla neler yapabileceğinizi öğrenin ve 12 ay boyunca hangi ürünlerin ücretsiz olduğunu öğrenebilirsiniz.

Örnek uygulamadaki kaynakları güvenlik özellikleriyle dağıtmak için bazı Premium özellikler için ödeme yapmanız gerekir. Uygulamanın ölçeklendirilen ve Azure tarafından sunulan ücretsiz katmanların ve denemelerin uygulama gereksinimlerini karşılayacak şekilde yükseltilmesi gerekir, maliyetleriniz artırılabilir. Maliyetlerinizi tahmin etmek için Azure [Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/) ' nı kullanın.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makaleler güvenli uygulamalar tasarlamanıza, geliştirmenize ve dağıtmanıza yardımcı olabilir.

- [Tasarıma](secure-design.md)
- [Geliştirme](secure-develop.md)
- [Dağıt](secure-deploy.md)
