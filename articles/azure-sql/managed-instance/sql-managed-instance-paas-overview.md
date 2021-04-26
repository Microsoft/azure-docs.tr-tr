---
title: Azure SQL yönetilen örneği nedir?
description: Azure SQL yönetilen örneğinin en son SQL Server (Enterprise Edition) veritabanı altyapısı ile %100 uyumluluğuna nasıl sağladığı hakkında bilgi edinin
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: bonova
ms.author: bonova
ms.reviewer: sstein, vanto
ms.date: 01/14/2021
ms.openlocfilehash: 5d49a5b57ff4b59005461f2bb13451822723b039
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644983"
---
# <a name="what-is-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği nedir?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL yönetilen örneği, tam olarak yönetilen ve tek yeşil platformun hizmet olarak tüm avantajları ile en geniş SQL Server veritabanı altyapısı uyumluluğunu birleştiren akıllı ve ölçeklenebilir bir bulut veritabanı hizmetidir. SQL yönetilen örneği, en son SQL Server (Enterprise Edition) veritabanı altyapısı ile %100 uyumluluğuna sahiptir. Bu, yaygın güvenlik sorunlarını ele alan yerel bir [sanal ağ (VNet)](../../virtual-network/virtual-networks-overview.md) uygulamasını ve mevcut SQL Server müşterileri için uygun bir [iş modelini](https://azure.microsoft.com/pricing/details/sql-database/) sağlar. SQL yönetilen örneği, mevcut SQL Server müşterilerin Şirket içi uygulamalarını en az uygulama ve veritabanı değişiklikleriyle buluta taşıyıp kaydıramalarına olanak sağlar. Aynı zamanda, SQL yönetilen örneği, yönetim yükünü ve TCO 'ları büyük ölçüde azaltan tüm PaaS yeteneklerini (otomatik düzeltme eki uygulama ve sürüm güncelleştirmeleri, [otomatik yedeklemeler](../database/automated-backups-overview.md), [yüksek kullanılabilirlik](../database/high-availability-sla.md)) korur.

Azure SQL yönetilen örneği 'ne yeni başladıysanız, derinlemesine Azure SQL [video serimizin](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)Azure *SQL yönetilen örnek* videosunu inceleyin:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Managed-Instance-Overview-6-of-61/player]

> [!IMPORTANT]
> SQL yönetilen örneğinin Şu anda kullanılabildiği bölgelerin listesi için bkz. [Desteklenen bölgeler](resource-limits.md#supported-regions).

Aşağıdaki diyagramda SQL yönetilen örneği 'nin temel özellikleri özetlenmektedir:

![Önemli özellikler](./media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL yönetilen örneği, şirket içi veya IaaS, kendiliğinden oluşturulmuş veya ISV tarafından sağlanmış bir ortamdan çok sayıda uygulamayı tam olarak yönetilen PaaS bulut ortamına geçirmek isteyen müşteriler için tasarlanmıştır. Müşteriler, tam otomatik [Azure veri geçiş hizmeti](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)'ni kullanarak mevcut SQL Server örneğini SQL yönetilen örneği 'ne taşıyıp kaydırabilirler. Bu, yerel VNET desteğiyle müşteri örneklerinin SQL Server ve tamamen yalıtımıyla uyumluluk sağlar. Geçiş seçenekleri ve araçları hakkında daha fazla bilgi için bkz. [geçişe genel bakış: Azure SQL yönetilen örneği 'ne SQL Server](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md).</br> Yazılım Güvencesi sayesinde, [SQL Server için Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)kullanarak mevcut LISANSLARıNıZı SQL yönetilen örnekteki indirimli ücretler için Exchange 'e dönüştürebilirsiniz. SQL yönetilen örneği, yüksek güvenlik ve zengin bir programlama yüzeyi gerektiren SQL Server örnekleri için buluttaki en iyi geçiş hedefidir.

> [!TIP]
> Azure SQL nasıl daha iyi hale getirebiliriz? [Ankete katılın](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="key-features-and-capabilities"></a>Temel özellikler ve yetenekler

SQL yönetilen örneği, hem Azure SQL veritabanı hem de SQL Server veritabanı altyapısında bulunan en iyi özellikleri birleştirir.

> [!IMPORTANT]
> SQL yönetilen örneği, çevrimiçi işlemler, otomatik plan düzeltmeleri ve diğer kurumsal performans iyileştirmeleri dahil olmak üzere en son SQL Server sürümünün tüm özellikleriyle çalışır. Kullanılabilen özelliklerin bir karşılaştırması [Özellik Karşılaştırması: Azure SQL yönetilen örneği ve SQL Server](../database/features-comparison.md)açıklanmıştır.

| **PaaS avantajları** | **İş sürekliliği** |
| --- | --- |
|Donanım satın alma ve yönetimi yok <br>Temel altyapıyı yönetmeye yönelik yönetim yükü yok <br>Hızlı sağlama ve hizmet ölçeklendirme <br>Otomatik düzeltme eki uygulama ve sürüm yükseltme <br>Diğer PaaS veri hizmetleriyle tümleştirme |% 99,99 çalışma süresi SLA 'Sı  <br>Yerleşik [yüksek kullanılabilirlik](../database/high-availability-sla.md) <br>[Otomatik yedeklemelerle](../database/automated-backups-overview.md) korunan veriler <br>Müşteri yapılandırılabilir yedekleme Bekletme dönemi <br>Kullanıcı tarafından başlatılan [yedeklemeler](/sql/t-sql/statements/backup-transact-sql?preserve-view=true&view=azuresqldb-mi-current) <br>Bir [noktadan sonra veritabanı geri yükleme](../database/recovery-using-backups.md#point-in-time-restore) özelliği |
|**Güvenlik ve uyumluluk** | **Yönetim**|
|Yalıtılmış ortam ([VNET tümleştirmesi](connectivity-architecture-overview.md), tek kiracılı hizmet, adanmış işlem ve depolama) <br>[Saydam veri şifrelemesi (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure Active Directory (Azure AD) kimlik doğrulaması](../database/authentication-aad-overview.md), çoklu oturum açma desteği <br> [Azure AD Server sorumluları (oturum açmalar)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) <br>Azure SQL veritabanı ile aynı uyumluluk standartlarına uyar <br>[SQL denetimi](auditing-configure.md) <br>[Gelişmiş Tehdit Koruması](threat-detection-configure.md) |Hizmet sağlamayı ve ölçeklendirmeyi otomatikleştirmek için API Azure Resource Manager <br>El ile hizmet sağlama ve ölçeklendirme için Azure portal işlevselliği <br>Veri Geçiş Hizmeti

> [!IMPORTANT]
> Azure SQL yönetilen örneği, bir dizi uyumluluk standartlarına karşı sertifikalandırilmiştir. Daha fazla bilgi için, **SQL veritabanı** altında listelenen SQL yönetilen örnek uyumluluk sertifikalarının en güncel listesini bulabileceğiniz [Microsoft Azure uyumluluk tekliflerini](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers)inceleyin.

SQL yönetilen örneği 'nin temel özellikleri aşağıdaki tabloda gösterilmiştir:

|Özellik | Açıklama|
|---|---|
| Sürüm/derleme SQL Server | SQL Server veritabanı altyapısı (en son kararlı) |
| Yönetilen otomatik yedeklemeler | Yes |
| Yerleşik örnek ve veritabanı izleme ve ölçümler | Yes |
| Otomatik yazılım düzeltme eki uygulama | Yes |
| En son veritabanı altyapısı özellikleri | Yes |
| Veritabanı başına veri dosyası (satır) sayısı | Birden çok |
| Veritabanı başına günlük dosyası (günlük) sayısı | 1 |
| VNet-Azure Resource Manager dağıtımı | Yes |
| VNet-klasik dağıtım modeli | No |
| Portal desteği | Yes|
| Yerleşik tümleştirme hizmeti (SSIS) | No-SSIS [Azure Data Factory PaaS](../../data-factory/tutorial-deploy-ssis-packages-azure.md) 'in bir parçasıdır |
| Yerleşik analiz hizmeti (SSAS) | Hayır-SSAS ayrı [PaaS](../../analysis-services/analysis-services-overview.md) |
| Yerleşik raporlama hizmeti (SSRS) | Azure VM üzerinde [Power BI sayfalandırılmış raporlar](/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) veya konak SSRS 'yi kullanın. SQL yönetilen örneği SSRS 'yi bir hizmet olarak çalıştıramıyor olsa da, Azure sanal makinesine yüklenen bir raporlama sunucusu için [SSRS Katalog veritabanlarını](/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) SQL Server kimlik doğrulaması kullanılarak barındırabilirler. |
|||

## <a name="vcore-based-purchasing-model"></a>Sanal çekirdek tabanlı satın alma modeli

SQL yönetilen örneği için [sanal çekirdek tabanlı satın alma modeli](../database/service-tiers-vcore.md) , şirket içi iş yükü gereksinimlerini buluta çevirebilmeniz için esneklik, denetim, saydamlık ve kolay bir yol sağlar. Bu model, iş yükü gereksinimlerinize göre işlem, bellek ve depolamayı değiştirmenize olanak sağlar. VCore modeli, SQL Server için [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) yüzde 55 tasarruf için de uygundur.

Sanal çekirdek modelinde, donanım nesilleri arasından seçim yapabilirsiniz.

- **4. nesil** mantıksal CPU 'lar Intel &reg; E5-2673 v3 (Haswell) 2,4 GHz IŞLEMCILERI, ekli SSD, fiziksel çekirdekler, çekırdek BAŞıNA 7 GB RAM ve 8 Ila 24 sanal çekirdek arasında işlem boyutları temel alınarak hesaplanır.
- **5. nesil** mantıksal CPU 'lar Intel &reg; E5-2673 v4 (çok Iyi) 2,3 GHz, ıntel &reg; SP-8160 (ufuk Gölü) ve Intel &reg; 8272cl (Cascade Lake) 2,5 GHz işlemcileri, hızlı NVMe SSD, hiper iş parçacıklı mantıksal çekirdek ve 4 ile 80 çekirdekler arasında işlem boyutlarını temel alır.

[SQL yönetilen örnek kaynak limitlerinin](resource-limits.md#hardware-generation-characteristics)donanım nesilleri arasındaki fark hakkında daha fazla bilgi bulabilirsiniz.

## <a name="service-tiers"></a>Hizmet katmanları

SQL yönetilen örneği iki hizmet katmanında kullanılabilir:

- **Genel amaçlı**: tipik performans ve g/ç gecikme süresi gereksinimleriyle uygulamalar için tasarlanmıştır.
- **İş açısından kritik**: düşük g/ç gecikme süresi gereksinimleri ve iş yükünde temeldeki bakım işlemlerinin en az etkisi olan uygulamalar için tasarlanmıştır.

Her iki hizmet katmanı da% 99,99 kullanılabilirlik garantisi sağlar ve depolama boyutunu ve işlem kapasitesini bağımsız olarak seçmenizi sağlar. Azure SQL yönetilen örneğinin yüksek kullanılabilirlik mimarisi hakkında daha fazla bilgi için bkz. [yüksek kullanılabilirlik ve Azure SQL yönetilen örneği](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Genel Amaçlı hizmet katmanı

Aşağıdaki listede Genel Amaçlı hizmet katmanının temel özellikleri açıklanmıştır:

- Tipik performans gereksinimlerine sahip iş uygulamalarının çoğunluğu için tasarlanmıştır
- Yüksek performanslı Azure Blob depolama (8 TB)
- Güvenilir Azure Blob depolama ve [azure Service Fabric](../../service-fabric/service-fabric-overview.md) temel alınarak yerleşik [yüksek kullanılabilirlik](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability)

Daha fazla bilgi için [genel amaçlı katmanında depolama katmanı](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) ve [SQL yönetilen örneği (genel amaçlı) için depolama performansı en iyi uygulamaları ve konuları](/archive/blogs/sqlcat/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose)konusuna bakın.

[SQL yönetilen örnek kaynak limitlerinin](resource-limits.md#service-tier-characteristics)hizmet katmanları arasındaki fark hakkında daha fazla bilgi edinin.

### <a name="business-critical-service-tier"></a>İş Açısından Kritik hizmet katmanı

İş Açısından Kritik hizmet katmanı, yüksek g/ç gereksinimlerine sahip uygulamalar için oluşturulmuştur. Birkaç yalıtılmış çoğaltma kullanarak hatalara en yüksek esnekliği sağlar.

Aşağıdaki listede İş Açısından Kritik hizmet katmanının temel özellikleri özetlenmektedir:

- En yüksek performans ve HA gereksinimlerine sahip iş uygulamaları için tasarlanmıştır
- Süper hızlı yerel SSD depolama (4. nesil üzerinde en fazla 1 TB ve 5. nesil üzerinde 4 TB 'a kadar) ile birlikte gelir
- [Always on kullanılabilirlik grupları](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) ve [Azure Service Fabric](../../service-fabric/service-fabric-overview.md) temel alınarak yerleşik [yüksek kullanılabilirlik](../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)
- Raporlama ve diğer salt okuma iş yükleri için kullanılabilen, yerleşik ek [salt okuma veritabanı çoğaltması](../database/read-scale-out.md)
- Yüksek performanslı gereksinimlere sahip iş yükü için kullanılabilen [bellek ıçı OLTP](../in-memory-oltp-overview.md)  

[SQL yönetilen örnek kaynak sınırlarındaki](resource-limits.md#service-tier-characteristics)hizmet katmanları arasındaki farklılıklar hakkında daha fazla bilgi edinin.

## <a name="management-operations"></a>Yönetim işlemleri

Azure SQL yönetilen örneği, yeni yönetilen örnekleri otomatik olarak dağıtmak, örnek özelliklerini güncelleştirmek ve artık gerekli olmadığında örnekleri silmek için kullanabileceğiniz yönetim işlemleri sağlar. Yönetim işlemlerinin ayrıntılı açıklaması, [yönetilen örnek yönetimi işlemlerine genel bakış](management-operations-overview.md) sayfasında bulunabilir.

## <a name="advanced-security-and-compliance"></a>Gelişmiş koruma ve uyumluluk

SQL yönetilen örneği, Azure platformu ve SQL Server veritabanı altyapısı tarafından sunulan gelişmiş güvenlik özellikleriyle birlikte gelir.

### <a name="security-isolation"></a>Güvenlik yalıtımı

SQL yönetilen örneği, Azure platformunda diğer kiracılardan ek güvenlik yalıtımı sağlar. Güvenlik yalıtımı şunları içerir:

- Azure ExpressRoute veya VPN Gateway kullanarak şirket içi ortamınıza [yerel sanal ağ uygulama](connectivity-architecture-overview.md) ve bağlantı.
- Varsayılan dağıtımda, SQL uç noktası yalnızca özel bir IP adresi aracılığıyla sunulur ve özel Azure veya karma ağlardan güvenli bağlantı sağlar.
- Adanmış temel altyapıya sahip tek kiracılı (işlem, depolama).

Aşağıdaki diyagramda uygulamalarınız için çeşitli bağlantı seçenekleri özetlenmektedir:

![Yüksek kullanılabilirlik](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Alt ağ düzeyinde VNet tümleştirmesi ve ağ ilkesi zorlaması hakkında daha fazla bilgi edinmek için bkz. [yönetilen örnekler Için VNET mimarisi](connectivity-architecture-overview.md) ve [Uygulamanızı yönetilen bir örneğe bağlama](connect-application-instance.md).

> [!IMPORTANT]
> Güvenlik gereksinimlerinizin izin verdiği her yerde, daha fazla avantaj elde edeceğiniz şekilde, aynı alt ağa birden fazla yönetilen örnek yerleştirin. Aynı alt ağdaki örnekleri birlikte bulma işlemi, uzun bir sağlama süresi alt ağda ilk yönetilen örneği dağıtma maliyetiyle ilişkili olduğundan, ağ altyapısı bakımını önemli ölçüde basitleştirir ve örnek sağlama süresini azaltır.

### <a name="security-features"></a>Güvenlik özellikleri

Azure SQL yönetilen örneği, verilerinizi korumak için kullanılabilecek bir dizi gelişmiş güvenlik özelliği sağlar.

- [SQL yönetilen örnek denetimi](auditing-configure.md) , veritabanı olaylarını izler ve bunları Azure depolama hesabınıza yerleştirilmiş bir denetim günlüğü dosyasına yazar. Denetim mevzuatla uyumluluk, veritabanı etkinliğini anlama ve işletme sorunlarını veya şüpheli güvenlik ihlallerini işaret edebilecek farklılıklar ve anormal durumlar hakkında öngörü sahip olmanıza yardımcı olabilir.
- Hareket halinde veri şifreleme-SQL yönetilen örneği, Aktarım Katmanı Güvenliği kullanarak hareket halindeki veriler için şifreleme sağlayarak verilerinizin güvenliğini sağlar. Aktarım katmanı güvenliğine ek olarak, SQL yönetilen örneği, önemli verilerin uçuş, bekleyen ve sorgu işleme sırasında [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)ile korunmasını sağlar. Always Encrypted, kritik verilerin hırsızlık ile ilgili olan ihlaller için veri güvenliği sağlar. Örneğin, Always Encrypted, kredi kartı numaraları, sorgu işleme sırasında bile veritabanı her zaman şifreli olarak depolanır ve bu da verileri işlemesi gereken yetkili personel veya uygulamalar tarafından kullanım noktasında şifre çözmeye izin verir.
- [Gelişmiş tehdit koruması](threat-detection-configure.md) , bir hizmette yerleşik olarak bulunan ve veritabanlarına yararlanmaya yönelik olağan dışı ve zararlı olabilecek girişimleri algılayan ek bir güvenlik bilgileri katmanı sunarak [denetimi](auditing-configure.md) tamamlar. Şüpheli etkinlikler, olası güvenlik açıkları ve SQL ekleme saldırıları ve anormal veritabanı erişim desenleri hakkında uyarılırsınız. Gelişmiş tehdit koruması uyarıları, [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)'nden görüntülenebilir. Şüpheli etkinliklerin ayrıntılarını sağlar ve tehdidi araştırmak ve hafifletmek için eyleme önerilir.  
- [Dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking) , hassas verileri ayrıcalıksız kullanıcılarla maskeleyerek kısıtlar. Dinamik veri maskeleme, hassas verilerin uygulama katmanında en az etkiyle ne kadar açığa çıkarmasını belirleyebilmenizi sağlayarak gizli verilere yetkisiz erişimin engellenmesine yardımcı olur. Bu, belirlenen veritabanı alanları üzerinde sorgu sonuç kümesindeki gizli verileri gizleyen, ancak veritabanındaki veriler değiştirilmediğinde ilke tabanlı bir güvenlik özelliğidir.
- [Satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security) (RLS), bir veritabanı tablosundaki satırlara erişimi, sorguyu yürüten kullanıcının özelliklerine göre denetlemenizi sağlar (örneğin, Grup üyeliği veya yürütme bağlamı gibi). RLS, uygulamanızdaki güvenliğin tasarımını ve kodlamasını basitleştirir. RLS, veri satırı erişiminde kısıtlama uygulamanızı sağlar. Örneğin, çalışanların yalnızca departmanlarıyla ilgili veri satırlarına erişmesini veya bir veri erişimini yalnızca ilgili verilerle kısıtlamalarını sağlamak.
- [Saydam veri şifrelemesi (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) , bekleyen verileri şifreleme olarak bilinen SQL yönetilen örnek veri dosyalarını şifreler. TDE, veri ve günlük dosyalarının gerçek zamanlı g/ç şifrelemesini ve şifresini çözmeyi gerçekleştirir. Şifreleme, kurtarma sırasında kullanılabilirlik için veritabanı önyükleme kaydında depolanan bir veritabanı şifreleme anahtarı (DEK) kullanır. Yönetilen bir örnekteki tüm veritabanlarınızı, saydam veri şifrelemesi ile koruyabilirsiniz. TDE, depolama ortamının hırsızlığına karşı korunması için birçok uyumluluk standardı tarafından gereken SQL Server bekleyen şifreleme teknolojisine sahiptir.

Şifrelenmiş bir veritabanının SQL yönetilen örneğine geçirilmesi, Azure veritabanı geçiş hizmeti veya yerel geri yükleme aracılığıyla desteklenir. Yerel geri yükleme kullanarak şifrelenmiş bir veritabanını geçirmeyi planlıyorsanız, var olan TDE sertifikasının SQL Server örneğinden SQL Managed örneğine geçirilmesi gerekli bir adımdır. Geçiş seçenekleri hakkında daha fazla bilgi için bkz. [SQL yönetilen örneğine SQL Server geçişi](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Azure Active Directory tümleştirmesi

SQL yönetilen örneği, Azure AD ile tümleştirilmiş geleneksel SQL Server veritabanı altyapısı oturumlarını ve oturum açmaları destekler. Azure AD Server sorumluları (oturum açmalar) (**genel önizleme**), şirket içi ortamınızda kullandığınız şirket içi veritabanı oturumlarının bir Azure bulut sürümüdür. Azure AD Server sorumluları (oturum açmalar), aynı yönetilen örnek içindeki çapraz veritabanı sorguları dahil olmak üzere, Azure AD kiracınızdan doğru örnek kapsamlı sorumlular olarak Kullanıcı ve grupları belirtmenize olanak tanır.

**Dış sağlayıcıdan** Azure AD Server sorumlularını (oturum açma) oluşturmak için yeni bir sözdizimi sunulmuştur. Sözdizimi hakkında daha fazla bilgi için bkz. [oturum oluştur](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)ve [SQL yönetilen örneği Için Azure Active Directory Yöneticisi sağlama](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) makalesini gözden geçirin.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory tümleştirmesi ve çok faktörlü kimlik doğrulaması

SQL yönetilen örneği, [Azure Active Directory tümleştirmeyle](../database/authentication-aad-overview.md)veritabanı kullanıcılarının ve diğer Microsoft hizmetlerinin kimliklerini merkezi olarak yönetmenize olanak sağlar. Bu özellik, izin yönetimini kolaylaştırırken güvenliği artırır. Azure Active Directory, çoklu bir oturum açma işlemini desteklerken veri ve uygulama güvenliğini artırmak için [çok faktörlü kimlik doğrulamasını](../database/authentication-mfa-ssms-configure.md) destekler.

### <a name="authentication"></a>Kimlik Doğrulaması

SQL yönetilen örnek kimlik doğrulaması, kullanıcıların veritabanına bağlanırken kimliklerini nasıl kanıtlayacağına başvurur. SQL yönetilen örneği iki tür kimlik doğrulamasını destekler:  

- **SQL kimlik doğrulaması**:

  Bu kimlik doğrulama yöntemi bir Kullanıcı adı ve parola kullanır.
- **Azure Active Directory kimlik doğrulaması**:

  Bu kimlik doğrulama yöntemi, Azure Active Directory tarafından yönetilen kimlikleri kullanır ve yönetilen ve tümleşik etki alanları için desteklenir. [Mümkün olan her durumda](/sql/relational-databases/security/choose-an-authentication-mode)Active Directory kimlik doğrulaması (tümleşik güvenlik) kullanın.

### <a name="authorization"></a>Yetkilendirme

Yetkilendirme, bir kullanıcının Azure SQL yönetilen örneğindeki bir veritabanında neler yapabileceğini belirtir ve Kullanıcı hesabınızın veritabanı rolü üyelikleri ve nesne düzeyi izinleri tarafından denetlenir. SQL yönetilen örneği, SQL Server 2017 ile aynı yetkilendirme yeteneklerine sahiptir.

## <a name="database-migration"></a>Veritabanı geçişi

SQL yönetilen örneği, şirket içi veya IaaS veritabanı uygulamalarından toplu veritabanı geçişi ile Kullanıcı senaryolarını hedefler. SQL yönetilen örneği, geçiş kılavuzlarında ele alınan çeşitli veritabanı geçiş seçeneklerini destekler. Bkz. geçişe genel bakış: daha fazla bilgi için bkz. [Azure SQL yönetilen örneği SQL Server](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md) .

### <a name="backup-and-restore"></a>Yedekleme ve geri yükleme  

Geçiş yaklaşımı SQL yedeklemelerini Azure Blob depolamaya kullanır. Bir Azure depolama blobunda depolanan yedeklemeler, [T-SQL restore komutu](/sql/t-sql/statements/restore-statements-transact-sql?preserve-view=true&view=azuresqldb-mi-current)kullanılarak yönetilen bir örneğe doğrudan geri yüklenebilir.

- Wide World Importers-standart veritabanı yedekleme dosyasının nasıl geri yükleneceğini gösteren bir hızlı başlangıç için bkz. [bir yedekleme dosyasını yönetilen bir örneğe geri yükleme](restore-sample-database-quickstart.md). Bu hızlı başlangıçta, bir yedekleme dosyasını Azure Blob depolama alanına yüklemeniz ve paylaşılan erişim imzası (SAS) anahtarı kullanarak güvence altına almanız gerektiğini gösterir.
- URL 'den geri yükleme hakkında daha fazla bilgi için bkz. [URL 'Den yerel GERI yükleme](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md#backup-and-restore).

> [!IMPORTANT]
> Yönetilen bir örnekten yedeklemeler, yalnızca başka bir yönetilen örneğe geri yüklenebilir. SQL Server örneğine veya Azure SQL veritabanı 'na geri yüklenemez.

### <a name="database-migration-service"></a>Veritabanı Geçiş Hizmeti

Azure veritabanı geçiş hizmeti, birden çok veritabanı kaynağından Azure veri platformları arasında kesintisiz geçiş sağlamak için tasarlanan, tam olarak yönetilen bir hizmettir. Bu hizmet, mevcut üçüncü taraf ve SQL Server veritabanlarını Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure VM 'de SQL Server taşımak için gereken görevleri basitleştirir. [Veritabanı geçiş hizmeti 'ni kullanarak şirket içi VERITABANıNıZı SQL yönetilen örneği 'ne geçirme](../../dms/tutorial-sql-server-to-managed-instance.md)konusuna bakın.

## <a name="sql-features-supported"></a>Desteklenen SQL özellikleri

SQL yönetilen örnek amaçlar, aşamalı bir yayın planı aracılığıyla en son SQL Server sürümü ile %100 Surface alan uyumluluğunu kapatacak. Özellikler ve karşılaştırma listesi için bkz. [SQL yönetilen örnek özelliği karşılaştırması](../database/features-comparison.md)ve SQL yönetilen örnekteki T-SQL farklılıkları listesi SQL Server ve SQL Server, bkz. [SQL yönetilen örnek T-SQL farklılığı](transact-sql-tsql-differences-sql-server.md).

SQL yönetilen örneği, SQL Server 2008 veritabanlarına yönelik geriye dönük uyumluluğu destekler. SQL Server 2005 veritabanı sunucularından doğrudan geçiş desteklenir ve geçirilen SQL Server 2005 veritabanlarının uyumluluk düzeyi SQL Server 2008 olarak güncelleştirilir.
  
Aşağıdaki diyagramda, SQL yönetilen örneği 'nde Surface alanı uyumluluğu özetlenmektedir:  

![yüzey alanı uyumluluğu](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>SQL Server şirket içi ve SQL yönetilen örneği arasındaki temel farklılıklar

Bulutta her zaman güncel olan SQL yönetilen örnek avantajları, yani SQL Server içindeki bazı özelliklerin kullanım dışı, kullanımdan kalkmış olabileceği veya alternatiflere sahip olduğu anlamına gelir. Araçların belirli bir özelliğin biraz farklı bir şekilde çalıştığını veya hizmetin tam olarak denetlediğiniz bir ortamda çalıştığını tanıması gerektiğinde belirli durumlar vardır.

Bazı önemli farklılıklar:

- Yüksek kullanılabilirlik, [her zaman açık kullanılabilirlik gruplarıyla](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)benzer bir teknoloji kullanılarak yerleşiktir ve önceden yapılandırılmıştır.
- Yalnızca otomatik yedeklemeler ve zaman içindeki bir noktaya geri yükleme vardır. Müşteriler `copy-only` , otomatik yedekleme zinciriyle kesintiye uğramayan yedeklemeler başlatabilir.
- Tam fiziksel yolların belirtilmesi desteklenmez. bu nedenle, tüm karşılık gelen senaryolar farklı şekilde desteklenmelidir: RESTORE DB MOVE Ile desteklemez, CREATE DB fiziksel yollara izin vermez, BULK INSERT yalnızca Azure Blob 'ları ile çalışır, vb.
- SQL yönetilen örneği, Windows kimlik doğrulamasına bir bulut alternatifi olarak [Azure AD kimlik doğrulamasını](../database/authentication-aad-overview.md) destekler.
- SQL yönetilen örnek, In-Memory OLTP nesneleri içeren veritabanları için XTP dosya gruplarını ve dosyalarını otomatik olarak yönetir.
- SQL yönetilen örneği SQL Server Integration Services (SSIS) destekler ve SSIS paketlerini depolayan bir SSIS kataloğunu (SSıSDB) barındırabilir, ancak bunlar Azure Data Factory yönetilen bir Azure-SSIS Integration Runtime (IR) üzerinde yürütülür. Bkz. [oluşturma Azure-SSIS IR Data Factory](../../data-factory/create-azure-ssis-integration-runtime.md). SSIS özelliklerini karşılaştırmak için bkz. [SQL veritabanı Ile SQL yönetilen örneği karşılaştırması](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Yönetim özellikleri

SQL yönetilen örneği, hizmet sizi sizin için gerçekleştirdiğinden veya bu görevleri büyük ölçüde basitleştirdiği için sistem yöneticilerinin yönetim görevlerinde daha az zaman harcamasını sağlar. Örneğin, [Işletim sistemi/RDBMS yükleme ve düzeltme eki uygulama](../database/high-availability-sla.md), [dinamik örnek yeniden boyutlandırma ve yapılandırma](../database/single-database-scale.md), [yedeklemeler](../database/automated-backups-overview.md), [veritabanı çoğaltma](replication-between-two-instances-configure-tutorial.md) (sistem veritabanları dahil), [yüksek kullanılabilirlik yapılandırması](../database/high-availability-sla.md)ve sistem durumu ve [performans izleme](../../azure-monitor/insights/azure-sql.md) veri akışlarının yapılandırması.

Daha fazla bilgi için bkz. [desteklenen ve desteklenmeyen SQL yönetilen örnek özelliklerinin listesi](../database/features-comparison.md)ve [SQL yönetilen örnek Ile SQL Server ARASıNDAKI T-SQL farklılıkları](transact-sql-tsql-differences-sql-server.md).

### <a name="programmatically-identify-a-managed-instance"></a>Programlı olarak yönetilen bir örnek tanımla

Aşağıdaki tabloda Transact-SQL aracılığıyla erişilebilen, uygulamanızın SQL yönetilen örneği ile çalıştığını ve önemli özellikleri aldığını algılamak için kullanabileceğiniz çeşitli özellikler gösterilmektedir.

|Özellik|Değer|Yorum|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM)-12.0.2000.8 2018-03-07 telif hakkı (C) 2018 Microsoft Corporation.|Bu değer SQL veritabanı ile aynıdır. Bu SQL Engine sürüm 12 ' i (SQL Server 2014 **) göstermez.** SQL yönetilen örneği her zaman en son kullanılabilir RTM SQL Server sürümüne eşit veya ondan yüksek olan en son kararlı SQL altyapısı sürümünü çalıştırır.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Bu değer SQL veritabanı ile aynıdır.|
|`SERVERPROPERTY('EngineEdition')`|8|Bu değer, yönetilen bir örneği benzersiz şekilde tanımlar.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Aşağıdaki biçimde tam örnek DNS adı: `<instanceName>` . `<dnsPrefix>` . database.windows.net, burada `<instanceName>` müşterinin adı, ad, `<dnsPrefix>` Genel DNS adı benzersizliği garanti etme (örneğin, "wcus17662feb9ce98") olarak yeniden oluşturulur.|Örnek: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Sonraki adımlar

- İlk yönetilen örneğinizi oluşturmayı öğrenmek için bkz. [Hızlı Başlangıç Kılavuzu](instance-create-quickstart.md).
- Özellikler ve karşılaştırma listesi için bkz. [SQL ortak özellikleri](../database/features-comparison.md).
- VNet yapılandırması hakkında daha fazla bilgi için bkz. [SQL yönetilen örnek VNET yapılandırması](connectivity-architecture-overview.md).
- Yönetilen bir örnek oluşturan ve bir veritabanını bir yedekleme dosyasından geri yükleyen hızlı başlangıç için bkz. [yönetilen örnek oluşturma](instance-create-quickstart.md).
- Geçiş için Azure veritabanı geçiş hizmeti 'ni kullanma hakkında bir öğretici için bkz. [veritabanı geçiş hizmeti kullanılarak SQL yönetilen örnek geçişi](../../dms/tutorial-sql-server-to-managed-instance.md).
- Yerleşik sorun giderme zekası ile SQL yönetilen örnek veritabanı performansının gelişmiş izlenmesi için bkz. [Azure SQL Analytics kullanarak Azure SQL yönetilen örneğini izleme](../../azure-monitor/insights/azure-sql.md).
- Fiyatlandırma bilgileri için bkz. [SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/managed/).