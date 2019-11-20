---
title: Azure SQL veritabanı yönetilen örneğine genel bakış | Microsoft Docs
description: Bu makalede Azure SQL veritabanı yönetilen örneği açıklanmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 08/05/2019
ms.openlocfilehash: 0d59b1cfed1de710725a5dfc91341fec0baa6cb4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331021"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Azure SQL veritabanı yönetilen örneği nedir?

Yönetilen örnek, Azure SQL veritabanı 'nın yeni bir dağıtım seçeneğidir. Bu, en son SQL Server şirket içi (Enterprise Edition) veritabanı altyapısı ile %100 uyumluluk sağlar ve bu, bir yerel [sanal ağ (VNet)](../virtual-network/virtual-networks-overview.md) uygulamasının ele alınmaktadır ortak güvenlik sorunları ve şirket içi SQL Server müşterileri için uygun bir [iş modeli](https://azure.microsoft.com/pricing/details/sql-database/) . Yönetilen örnek dağıtım modeli, mevcut SQL Server müşterilerin Şirket içi uygulamalarını en az uygulama ve veritabanı değişiklikleriyle buluta taşıyıp kaydırmaya izin verir. Aynı zamanda, yönetilen örnek dağıtım seçeneği, yönetim yükünü ve TCO 'ları büyük ölçüde azaltan tüm PaaS yeteneklerini (otomatik düzeltme eki uygulama ve sürüm güncelleştirmeleri, [otomatik yedeklemeler](sql-database-automated-backups.md), [yüksek kullanılabilirlik](sql-database-high-availability.md) ) korur.

> [!IMPORTANT]
> Yönetilen örnek dağıtım seçeneğinin Şu anda kullanılabildiği bölgelerin listesi için bkz. [Desteklenen bölgeler](sql-database-managed-instance-resource-limits.md#supported-regions).

Aşağıdaki diyagramda yönetilen örneklerin temel özellikleri özetlenmektedir:

![Temel Özellikler](./media/sql-database-managed-instance/key-features.png)

Yönetilen örnek dağıtım modeli, şirket içi veya IaaS, kendiliğinden oluşturulmuş veya ISV tarafından sağlanmış ortamdan çok sayıda uygulamayı, mümkün olduğunca düşük geçiş çabasıyla tam olarak yönetilen PaaS bulut ortamına geçirmek isteyen müşteriler için tasarlanmıştır. Müşteriler, Azure 'da tam otomatik [veri geçiş hizmeti 'ni (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) kullanarak, şirket içi SQL Server, şirket içi SQL Server ile uyumluluğu ve müşteri örneklerinin tamamen yalıtımına sahip olan yönetilen bir örneğe taşıyıp kaydırabilirler Yerel VNet desteği.  Yazılım Güvencesi sayesinde, [SQL Server için Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)kullanarak, yönetilen bir örnekteki indirimli ücretler için mevcut lisanslarını Exchange 'e dönüştürebilirsiniz.  Yönetilen bir örnek, bulutta yüksek güvenlik ve zengin bir programlama yüzeyi gerektiren SQL Server örnekleri için en iyi geçiş hedefidir.

Yönetilen örnek dağıtım seçeneği amaçlar, aşamalı bir yayın planı aracılığıyla en son şirket içi SQL Server sürümüyle %100 ' a kadar yüzey alanı uyumluluğunu sağlar.

Azure SQL veritabanı dağıtım seçenekleri arasında karar vermek için: tek veritabanı, havuza alınmış veritabanı ve yönetilen örnek ve sanal makinede barındırılan SQL Server, bkz. [Azure 'da SQL Server doğru sürümünü seçme](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Temel özellikler ve yetenekler

Yönetilen örnek, hem Azure SQL veritabanı hem de SQL Server veritabanı altyapısında bulunan en iyi özellikleri birleştirir.

> [!IMPORTANT]
> Yönetilen bir örnek, çevrimiçi işlemler, otomatik plan düzeltmeleri ve diğer kurumsal performans iyileştirmeleri dahil olmak üzere en son SQL Server sürümünün tüm özellikleriyle çalışır. Kullanılabilen özelliklerin bir karşılaştırması, [Özellik Karşılaştırması: Azure SQL veritabanı ve SQL Server](sql-database-features.md)açıklanmıştır.

| **PaaS avantajları** | **İş sürekliliği** |
| --- | --- |
|Donanım satın alma ve yönetimi yok <br>Temel altyapıyı yönetmeye yönelik yönetim yükü yok <br>Hızlı sağlama ve hizmet ölçeklendirme <br>Otomatik düzeltme eki uygulama ve sürüm yükseltme <br>Diğer PaaS veri hizmetleriyle tümleştirme |% 99,99 çalışma süresi SLA 'Sı  <br>[Yüksek kullanılabilirlik](sql-database-high-availability.md) için yerleşik <br>[Otomatik yedeklemelerle](sql-database-automated-backups.md) korunan veriler <br>Müşteri yapılandırılabilir yedekleme Bekletme dönemi <br>Kullanıcı tarafından başlatılan [yedeklemeler](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Zaman noktası veritabanı geri yükleme](sql-database-recovery-using-backups.md#point-in-time-restore) özelliği |
|**Güvenlik ve uyumluluk** | **Yönetme**|
|Yalıtılmış ortam ([VNET tümleştirmesi](sql-database-managed-instance-connectivity-architecture.md), tek kiracılı hizmet, adanmış işlem ve depolama) <br>[Saydam veri şifrelemesi (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD kimlik doğrulaması](sql-database-aad-authentication.md), çoklu oturum açma desteği <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD Server sorumluları (oturum açmalar)</a> (**genel önizleme**) <br>Azure SQL veritabanı ile aynı uyumluluk standartlarına uyar <br>[SQL denetimi](sql-database-managed-instance-auditing.md) <br>[Gelişmiş Tehdit Koruması](sql-database-managed-instance-threat-detection.md) |Hizmet sağlamayı ve ölçeklendirmeyi otomatikleştirmek için API Azure Resource Manager <br>El ile hizmet sağlama ve ölçeklendirme için Azure portal işlevselliği <br>Veri geçiş hizmeti

> [!IMPORTANT]
> Azure SQL veritabanı (tüm dağıtım seçenekleri), bir dizi uyumluluk standartlarına karşı sertifikalandırilmiştir. Daha fazla bilgi için SQL veritabanı uyumluluk sertifikalarının en güncel listesini bulabileceğiniz [Microsoft Azure Güven Merkezi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) ' ne bakın.

Yönetilen örneklerin temel özellikleri aşağıdaki tabloda gösterilmiştir:

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
| VNet-klasik dağıtım modeli | Hayır |
| Portal desteği | Yes|
| Yerleşik tümleştirme hizmeti (SSIS) | No-SSIS [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) 'in bir parçasıdır |
| Yerleşik analiz hizmeti (SSAS) | Hayır-SSAS ayrı [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Yerleşik raporlama hizmeti (SSRS) | Power BI veya SSRS IaaS kullanma |
|||

## <a name="vcore-based-purchasing-model"></a>Sanal çekirdek tabanlı satın alma modeli

Yönetilen örnekler için [sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md) , şirket içi iş yükü gereksinimlerini buluta çevirebilmeniz için esneklik, denetim, saydamlık ve kolay bir yol sağlar. Bu model, iş yükü gereksinimlerinize göre işlem, bellek ve depolamayı değiştirmenize olanak sağlar. VCore modeli, [SQL Server için Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)yüzde 30 ' a kadar tasarruf için de uygundur.

Sanal çekirdek modelinde, donanım nesilleri arasından seçim yapabilirsiniz.

- **4. nesil** Mantıksal CPU 'Lar Intel E5-2673 v3 (Haswell) 2,4 GHz işlemcileri, ekli SSD, fiziksel çekirdekler, çekirdek başına 7 GB RAM ve 8 ila 24 sanal çekirdek arasında işlem boyutları temel alır.
- **5. nesil** Mantıksal CPU 'Lar Intel E5-2673 v4 (çok Iyi) 2,3 GHz işlemcileri, hızlı NVMe SSD, hiper iş parçacıklı mantıksal çekirdek ve 4 ile 80 çekirdekler arasında işlem boyutlarını temel alır.

[Yönetilen örnek kaynak limitlerdeki](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)donanım oluşturmaları arasındaki fark hakkında daha fazla bilgi bulabilirsiniz.

> [!IMPORTANT]
> Yeni 4. nesil veritabanları artık Avustralya Doğu veya Brezilya Güney bölgelerinde desteklenmez.

## <a name="managed-instance-service-tiers"></a>Yönetilen örnek hizmeti katmanları

Yönetilen örnek iki hizmet katmanlarında kullanılabilir:

- **Genel amaçlı**: tipik performans ve GÇ gecikme gereksinimlerine sahip uygulamalar için tasarlanmıştır.
- **İş açısından kritik**: düşük GÇ gecikme gereksinimleri ve iş yükünde temeldeki bakım işlemlerinin en az etkisi olan uygulamalar için tasarlanmıştır.

Her iki hizmet katmanı da% 99,99 kullanılabilirlik garantisi sağlar ve depolama boyutunu ve işlem kapasitesini bağımsız olarak seçmenizi sağlar. Azure SQL veritabanı 'nın yüksek kullanılabilirlik mimarisi hakkında daha fazla bilgi için bkz. [yüksek kullanılabilirlik ve Azure SQL veritabanı](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Genel amaçlı hizmet katmanı

Aşağıdaki listede Genel Amaçlı hizmet katmanının temel özellikleri açıklanmıştır:

- Tipik performans gereksinimleriyle iş uygulamalarının çoğunluğunun tasarımı
- Yüksek performanslı Azure Blob depolama (8 TB)
- Güvenilir Azure Blob depolama ve [azure Service Fabric](../service-fabric/service-fabric-overview.md) temel alınarak yerleşik [yüksek kullanılabilirlik](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)

Daha fazla bilgi için bkz. [genel amaçlı katmanda depolama katmanı](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) ve [yönetilen örnekler (genel amaçlı) için depolama performansı en iyi uygulamaları ve konuları](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

[Yönetilen örnek kaynak sınırlarındaki](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)hizmet katmanları arasındaki fark hakkında daha fazla bilgi edinin.

### <a name="business-critical-service-tier"></a>İş Açısından Kritik hizmet katmanı

İş Açısından Kritik hizmet katmanı, yüksek GÇ gereksinimlerine sahip uygulamalar için oluşturulmuştur. Birkaç yalıtılmış çoğaltma kullanarak hatalara en yüksek esnekliği sağlar.

Aşağıdaki listede İş Açısından Kritik hizmet katmanının temel özellikleri özetlenmektedir:

- En yüksek performans ve HA gereksinimlerine sahip iş uygulamaları için tasarlanmıştır
- Süper hızlı yerel SSD depolama (4. nesil üzerinde en fazla 1 TB ve 5. nesil üzerinde 4 TB 'a kadar) ile birlikte gelir
- [Always on kullanılabilirlik grupları](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) ve [Azure Service Fabric](../service-fabric/service-fabric-overview.md)temel alınarak yerleşik [yüksek kullanılabilirlik](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) .
- Raporlama ve diğer salt okuma iş yükleri için kullanılabilen, yerleşik ek [salt okuma veritabanı çoğaltması](sql-database-read-scale-out.md)
- Yüksek performanslı gereksinimlere sahip iş yükü için kullanılabilen [bellek ıçı OLTP](sql-database-in-memory.md)  

[Yönetilen örnek kaynak sınırlarındaki](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)hizmet katmanları arasındaki fark hakkında daha fazla bilgi edinin.


## <a name="managed-instance-management-operations"></a>Yönetilen örnek yönetimi işlemleri

Azure SQL Veritabanı yeni yönetilen örnekleri otomatik olarak dağıtmak, örnek özelliklerini güncelleştirmek ve artık gerekmediğinde örnekleri silmek için kullanabileceğiniz yönetim işlemleri sağlar. Bu bölümde yönetim işlemleri ve bunların tipik süreleri hakkında bilgi sağlanır.

[Azure sanal ağları (VNet) içindeki dağıtımları](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) desteklemek ve müşterilere yalıtım ve güvenlik sağlamak için, yönetilen örnek, [sanal kümelere](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)dayanır ve bu, Müşterinin sanal ağ alt ağı. Temelde, boş bir alt ağda bulunan her yönetilen örnek dağıtımı yeni bir sanal küme oluşturma ile sonuçlanır.

Dağıtılmış yönetilen örneklerin sonraki işlemlerinin, temel alınan sanal kümesinde de etkileri olabilir. Bu, ek sanal makinelerin dağıtımı, Yeni dağıtımlar veya mevcut yönetilen örneklere güncelleştirmeler planlarken dikkate alınması gereken bir ek yük ile birlikte, yönetim işlemlerinin süresini etkiler.

Tüm yönetim işlemleri aşağıdaki gibi kategorilere ayrılabilir:

- Örnek dağıtımı (yeni örnek oluşturma). 
- Örnek Güncelleştirmesi (sanal çekirdekler, ayrılmış depolama vb. gibi örnek özellikleri değiştirme).
- Örnek silme.

Genellikle, sanal kümelerdeki işlemler en uzun sürer. Sanal kümelerdeki işlemlerin süresi farklılık gösterir. var olan hizmet telemetri verilerine göre genellikle tahmin edebileceğiniz değerler aşağıda verilmiştir:

- Sanal küme oluşturma. Bu örnek yönetim işlemlerinde zaman uyumlu bir adımdır. **işlemlerin %90 ' i 4 saat içinde tamamlanır**.
- Sanal küme yeniden boyutlandırma (genişletme veya küçültme). Genişleme zaman uyumsuz olarak (örnek yönetim işlemleri süresince etkilenmeden) zaman uyumsuz olarak gerçekleştirilir. **küme genişletmeleri %90, en az 2,5 saat içinde tamamlanır**.
- Sanal küme silme. Silme zaman uyumsuz bir adımdır, ancak boş bir sanal kümede [el ile de başlatılabilir](sql-database-managed-instance-delete-virtual-cluster.md) , bu durumda eşzamanlı olarak yürütülür. **%90, sanal küme silme işleminin 1,5 saat içinde sona ermesini**.

Ayrıca, örneklerin yönetimi barındırılan veritabanlarında gerçekleştirilen işlemlerden birini de içerebilir ve bu da daha uzun süreler elde edebilir:

- Azure depolama 'dan veritabanı dosyaları iliştirme. Bu, Genel Amaçlı hizmet katmanında işlem (vCore) veya depolama ölçeği artırma veya azaltma gibi zaman uyumlu bir adımdır. **Bu işlemlerin %90 ' i 5 dakika içinde tamamlanır**.
- Always on kullanılabilirlik grubu dengeli dağıtımı. Bu, işlem (sanal çekirdek) veya İş Açısından Kritik hizmet katmanındaki depolama ölçeklendirmesinin yanı sıra hizmet katmanını Genel Amaçlı İş Açısından Kritik (veya tersi) olarak değiştirme gibi zaman uyumlu bir adımdır. Bu işlemin süresi toplam veritabanı boyutuyla ve geçerli veritabanı etkinliğiyle orantılıdır (etkin işlem sayısı). Bir örneği güncelleştirme sırasında veritabanı etkinliği toplam süreye önemli bir varyans getirebilir. **Bu işlemlerin %90 ' i 220 GB/saat veya daha yüksek bir süre içinde yürütülür**.

Aşağıdaki tabloda işlemler ve genel toplam süreler özetlenmektedir:

|Kategori  |İşlem  |Uzun süre çalışan segment  |Tahmini süre  |
|---------|---------|---------|---------|
|**Dağıtım** |Boş bir alt ağdaki ilk örnek|Sanal küme oluşturma|işlemlerin %90 ' i 4 saat içinde sona ermesini|
|Kurulum |Boş olmayan bir alt ağda (örneğin, gen 4 örneklerine sahip bir alt ağdaki ilk Gen 5 örneği) başka bir donanım oluşturma örneği|Sanal küme oluşturma *|işlemlerin %90 ' i 4 saat içinde sona ermesini|
|Kurulum |Boş veya boş olmayan bir alt ağda 4 sanal çekirdekler için ilk örnek oluşturma|Sanal küme oluşturma * *|işlemlerin %90 ' i 4 saat içinde sona ermesini|
|Kurulum |Boş olmayan alt ağ içinde sonraki örnek oluşturma (2., üçüncü, vb. örnek)|Sanal küme yeniden boyutlandırma|2,5 saat içinde işlem bitiş %90|
|**Güncelleştirme** |Örnek özelliği değişikliği (yönetici parolası, AAD oturum açma, Azure Hibrit Avantajı bayrağı)|Yok|En fazla 1 dakika|
|Güncelleştir |Örnek depolama ölçeği artırma/azaltma (Genel Amaçlı hizmet katmanı)|-Sanal küme yeniden boyutlandırma<br>-Veritabanı dosyaları iliştirme|2,5 saat içinde işlem bitiş %90|
|Güncelleştir |Örnek depolama ölçeği artırma/azaltma (İş Açısından Kritik hizmet katmanı)|-Sanal küme yeniden boyutlandırma<br>-Always on kullanılabilirlik grubu dengeli dağıtımı|işlemin %90 ' i, tüm veritabanlarının çekirdeğini sağlamak için 2,5 saat + saat (220 GB/saat)|
|Güncelleştir |Örnek işlem (Vçekirdekler) ölçeği artırma ve azaltma (Genel Amaçlı)|-Sanal küme yeniden boyutlandırma<br>-Veritabanı dosyaları iliştirme|2,5 saat içinde işlem bitiş %90|
|Güncelleştir |Örnek işlem (Vçekirdekler) ölçeği artırma ve azaltma (İş Açısından Kritik)|-Sanal küme yeniden boyutlandırma<br>-Always on kullanılabilirlik grubu dengeli dağıtımı|işlemin %90 ' i, tüm veritabanlarının çekirdeğini sağlamak için 2,5 saat + saat (220 GB/saat)|
|Güncelleştir |Örnek ölçeği 4 sanal çekirdeğe kadar (Genel Amaçlı)|-Sanal küme yeniden boyutlandırma (ilk kez yapıldıysa, sanal küme oluşturulması gerekebilir * *)<br>-Veritabanı dosyaları iliştirme|işlemin %90 ' u 4 saat 5 dakika içinde tamamlanır * *|
|Güncelleştir |Örnek ölçeği 4 sanal çekirdeğe kadar (Genel Amaçlı)|-Sanal küme yeniden boyutlandırma (ilk kez yapıldıysa, sanal küme oluşturulması gerekebilir * *)<br>-Always on kullanılabilirlik grubu dengeli dağıtımı|işlemlerin %90 ' i, tüm 220 veritabanlarının çekirdeğini sağlamak için 5 saat + saat ile sona|
|Güncelleştir |Örnek hizmeti katmanı değişikliği (İş Açısından Kritik Genel Amaçlı ve tam tersi)|-Sanal küme yeniden boyutlandırma<br>-Always on kullanılabilirlik grubu dengeli dağıtımı|işlemin %90 ' i, tüm veritabanlarının çekirdeğini sağlamak için 2,5 saat + saat (220 GB/saat)|
|**Üzere**|Örnek silme|Tüm veritabanları için günlük kuyruğu yedeklemesi|%90 işlem en fazla 1 dakika içinde tamamlanır.<br>Note: alt ağdaki son örnek silinirse, bu işlem 12 saat sonra sanal küme silmeyi zamanlar * * *|
|Silme|Sanal küme silme (Kullanıcı tarafından başlatılan işlem olarak)|Sanal küme silme|İşlem %90, en fazla 1,5 saat içinde tamamlanır|

\* sanal kümesi, donanım oluşturma başına oluşturulur.

\* @ no__t-1 Haziran 2019 ' de 4 Vçekirdekler dağıtım seçeneği yayımlanmıştır ve yeni bir sanal küme sürümü gerektirir. Hedef alt ağda 12 Haziran 'dan önce oluşturulan örneklere sahipseniz, 4 sanal çekirdek örneği barındırmak için yeni bir sanal küme otomatik olarak dağıtılacaktır.

\* @ no__t-1 @ no__t-2 12 saat geçerli yapılandırmadır ancak gelecekte değişebilir, bu nedenle buna bir daha fazla bağımlılık yapmayın. Daha önce bir sanal kümeyi silmeniz gerekiyorsa (örneğin alt ağını serbest bırakmak için), bkz. bir [Azure SQL veritabanı yönetilen örneği silindikten sonra bir alt ağı silme](sql-database-managed-instance-delete-virtual-cluster.md).

### <a name="instance-availability-during-management"></a>Yönetim sırasında örnek kullanılabilirliği

Yönetilen örnekler, dağıtım ve silme işlemleri sırasında istemci uygulamalar için kullanılamaz.

Yönetilen örnekler, güncelleştirme işlemleri sırasında kullanılabilir, ancak genellikle 10 saniyeye kadar olan güncelleştirmelerin sonunda gerçekleşen yük devretmenin neden olduğu kısa bir kesinti vardır.

> [!IMPORTANT]
> Yük devretme süresi, uzun süre çalışan bir [Kurtarma süresi](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process)nedeniyle veritabanlarında gerçekleşen işlemler büyük ölçüde farklılık gösterebilir. Bu nedenle, Azure SQL veritabanı yönetilen örneği 'nin işlem veya depolama alanını ölçeklendirmeniz veya hizmet katmanını uzun süre çalışan işlemler (veri alma, veri işleme işleri, dizin yeniden oluşturma vb.) ile aynı anda değiştirmeniz önerilmez. İşlemin sonunda gerçekleştirilecek veritabanı yük devretmesi, devam eden işlemleri iptal eder ve uzun süren kurtarma zamanına yol açabilir.

[Hızlandırılmış veritabanı kurtarma](sql-database-accelerated-database-recovery.md) , Azure SQL veritabanı yönetilen örnekleri için şu anda kullanılamıyor. Bu özellik etkinleştirildikten sonra, uzun süre çalışan işlemler durumunda bile yük devretme süresinin çeşitliğini önemli ölçüde azaltır.



## <a name="advanced-security-and-compliance"></a>Gelişmiş koruma ve uyumluluk

Yönetilen örnek dağıtım seçeneği, Azure Cloud ve SQL Server veritabanı altyapısı tarafından sunulan gelişmiş güvenlik özelliklerini birleştirir.

### <a name="managed-instance-security-isolation"></a>Yönetilen örnek güvenlik yalıtımı

Yönetilen bir örnek, Azure bulutundaki diğer kiracılardan ek güvenlik yalıtımı sağlar. Güvenlik yalıtımı şunları içerir:

- Azure Express Route veya VPN Gateway kullanarak şirket içi ortamınıza [yerel sanal ağ uygulama](sql-database-managed-instance-connectivity-architecture.md) ve bağlantı.
- Varsayılan dağıtımda, SQL uç noktası yalnızca özel bir IP adresi aracılığıyla sunulur ve özel Azure veya karma ağlardan güvenli bağlantı sağlar.
- Adanmış temel altyapıya sahip tek kiracılı (işlem, depolama).

Aşağıdaki diyagramda uygulamalarınız için çeşitli bağlantı seçenekleri özetlenmektedir:

![yüksek kullanılabilirlik](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Alt ağ düzeyinde VNet tümleştirmesi ve ağ ilkesi zorlaması hakkında daha fazla bilgi edinmek için bkz. [yönetilen örnekler Için VNET mimarisi](sql-database-managed-instance-connectivity-architecture.md) ve [Uygulamanızı yönetilen bir örneğe bağlama](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Güvenlik gereksinimlerinizin izin verdiği her yerde, daha fazla avantaj sağlayacak şekilde, aynı alt ağa birden fazla yönetilen örnek yerleştirin. Aynı alt ağdaki örneklerin birlikte bulunması, ağ altyapısı bakımını önemli ölçüde basitleştirir ve örnek sağlama süresini azaltır, çünkü uzun kaynak sağlama süresi bir alt ağda ilk yönetilen örneği dağıtma maliyetiyle ilişkili olur.

### <a name="azure-sql-database-security-features"></a>Azure SQL veritabanı güvenlik özellikleri

Azure SQL veritabanı, verilerinizi korumak için kullanılabilecek bir dizi gelişmiş güvenlik özelliği sağlar.

- [Yönetilen örnek denetimi](sql-database-managed-instance-auditing.md) , veritabanı olaylarını izler ve bunları Azure depolama hesabınıza yerleştirilmiş bir denetim günlüğü dosyasına yazar. Denetim, yasal uyumluluğu korumanıza, veritabanı etkinliklerini anlamanıza ve işle ilgili endişeleri veya şüpheli güvenlik ihlallerini gösterebilecek tutarsızlıklar ve bozukluklar elde etmenize yardımcı olabilir.
- Hareket halinde veri şifreleme-yönetilen bir örnek, Aktarım Katmanı Güvenliği kullanarak hareket halindeki veriler için şifreleme sağlayarak verilerinizin güvenliğini sağlar. Aktarım katmanı güvenliğine ek olarak, yönetilen örnek dağıtımı seçeneği, bekleme sırasında ve [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)ile sorgu işleme sırasında hassas verilerin korunmasını sağlar. Always Encrypted, kritik öneme sahip verilerin çalınması gibi güvenlik ihlallerine karşı sektörde benzersiz koruma sağlayan bir özelliktir. Örneğin, Always Encrypted, kredi kartı numaraları, sorgu işleme sırasında bile veritabanı her zaman şifreli olarak depolanır ve bu da verileri işlemesi gereken yetkili personel veya uygulamalar tarafından kullanım noktasında şifre çözmeye izin verir.
- [Gelişmiş tehdit koruması](sql-database-managed-instance-threat-detection.md) , bir hizmette yerleşik olarak bulunan ve veritabanlarına yararlanmaya yönelik olağan dışı ve zararlı olabilecek girişimleri algılayan ek bir güvenlik bilgileri katmanı sunarak [denetimi](sql-database-managed-instance-auditing.md) tamamlar. Şüpheli etkinlikler, olası güvenlik açıkları ve SQL ekleme saldırıları ve anormal veritabanı erişim desenleri hakkında uyarılırsınız. Gelişmiş tehdit koruması uyarıları [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/) 'nden görüntülenebilir ve şüpheli etkinliğin ayrıntılarını sağlayabilir ve tehdidi araştırmak ve hafifletmek için önerilen eylemleri gerçekleştirebilir.  
- [Dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking) , hassas verileri ayrıcalıksız kullanıcılarla maskeleyerek kısıtlar. Dinamik veri maskeleme, hassas verilerin uygulama katmanında en az etkiyle ne kadar açığa çıkarmasını belirleyebilmenizi sağlayarak gizli verilere yetkisiz erişimin engellenmesine yardımcı olur. Bu özellik, hassas verileri belirlenen veritabanı alanlarına yapılan sorgunun sonuç kümesinde gizleyen ancak veritabanındaki verileri değiştirmeyen ilke tabanlı bir güvenlik özelliğidir.
- [Satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security) , bir veritabanı tablosundaki satırlara erişimi, sorguyu yürüten kullanıcının özelliklerine göre denetlemenizi sağlar (örneğin, Grup üyeliği veya yürütme bağlamı gibi). Satır düzeyi güvenlik (RLS), uygulamanızın güvenlik tasarımını ve kodlama aşamasını kolaylaştırır. RLS, veri satırı erişiminde kısıtlama uygulamanızı sağlar. Örneğin, çalışanların yalnızca departmanlarıyla ilgili veri satırlarına erişmesini veya bir veri erişimini yalnızca ilgili verilerle kısıtlamalarını sağlamak.
- [Saydam veri şifrelemesi (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) , bekleyen verileri şifreleme olarak bilinen yönetilen örnek veri dosyalarını şifreler. TDE, veri ve günlük dosyalarının gerçek zamanlı g/ç şifrelemesini ve şifresini çözmeyi gerçekleştirir. Şifreleme, kurtarma sırasında kullanılabilirlik için veritabanı önyükleme kaydında depolanan bir veritabanı şifreleme anahtarı (DEK) kullanır. Yönetilen bir örnekteki tüm veritabanlarınızı, saydam veri şifrelemesi ile koruyabilirsiniz. TDE, depolama ortamının hırsızlığına karşı korunması için birçok uyumluluk standardı tarafından gerekli olan, bekleyen, bekleyen şifreleme teknolojisine sahip SQL Server.

Şifrelenmiş bir veritabanının yönetilen bir örneğe geçirilmesi, Azure veritabanı geçiş hizmeti (DMS) veya yerel geri yükleme aracılığıyla desteklenir. Yerel geri yükleme kullanarak şifrelenmiş bir veritabanını geçirmeyi planlıyorsanız, var olan TDE sertifikasının şirket içi SQL Server veya bir sanal makinedeki SQL Server bir yönetilen örneğe geçirilmesi gerekli bir adımdır. Geçiş seçenekleri hakkında daha fazla bilgi için bkz. [yönetilen örneğe SQL Server örnek geçişi](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Azure Active Directory Tümleştirmesi

Yönetilen örnek dağıtım seçeneği, Azure Active Directory (AAD) ile tümleştirilmiş geleneksel SQL Server veritabanı altyapısı oturumlarını ve oturum açmaları destekler. Azure AD Server sorumluları (oturum açmalar) (**genel önizleme**), şirket içi ortamınızda kullandığınız şirket içi veritabanı oturumlarının Azure bulut sürümüdür. Azure AD Server sorumluları (oturum açmalar), aynı yönetilen ve çapraz veritabanı sorguları dahil olmak üzere, örnek düzeyi bir işlem gerçekleştirmek için, Azure Active Directory kiracınızdan doğru örnek kapsamlı sorumlular olarak Kullanıcı ve grupları belirtmenizi sağlar Instance.

**Dış sağlayıcıdan**Azure AD Server sorumlularını (oturum açma) (**genel önizleme**) oluşturmak için yeni bir sözdizimi sunulmuştur. Sözdizimi hakkında daha fazla bilgi için bkz. <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">oturum açma oluşturma</a>ve [yönetilen örnek Için Azure Active Directory Yöneticisi sağlama](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) makalesini gözden geçirme.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory tümleştirmesi ve çok faktörlü kimlik doğrulaması

Yönetilen örnek dağıtım seçeneği, veritabanı kullanıcısı ve diğer Microsoft hizmetlerinin kimliklerini [Azure Active Directory tümleştirmeyle](sql-database-aad-authentication.md)merkezi olarak yönetmenize olanak sağlar. Bu özellik, izin yönetimini kolaylaştırırken güvenliği artırır. Azure Active Directory, veri ve uygulama güvenliğini artırmak için [çok faktörlü kimlik doğrulamasını](sql-database-ssms-mfa-authentication-configure.md) (MFA) ve çoklu oturum açma işlemini destekler.

### <a name="authentication"></a>Kimlik Doğrulaması

Yönetilen örnek kimlik doğrulaması, kullanıcıların veritabanına bağlanırken kimliklerini nasıl kanıtlayacağına başvurur. SQL Veritabanı iki kimlik doğrulaması türünü destekler:  

- **SQL kimlik doğrulaması**:

  Bu kimlik doğrulama yöntemi bir Kullanıcı adı ve parola kullanır.
- **Azure Active Directory kimlik doğrulaması**:

  Bu kimlik doğrulama yöntemi, Azure Active Directory tarafından yönetilen kimlikleri kullanır ve yönetilen ve tümleşik etki alanları için desteklenir. [Mümkün olduğunda](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) Active Directory kimlik doğrulamasını (tümleşik güvenlik) kullanın.

### <a name="authorization"></a>Yetkilendirme

Yetkilendirme, bir kullanıcının bir Azure SQL veritabanında neler yapabileceğini belirtir ve Kullanıcı hesabınızın veritabanı rolü üyelikleri ve nesne düzeyi izinleri tarafından kontrol edilir. Yönetilen bir örnek SQL Server 2017 ile aynı yetkilendirme yeteneklerine sahiptir.

## <a name="database-migration"></a>Veritabanı geçişi

Yönetilen örnek dağıtım seçeneği, şirket içi veya IaaS veritabanı uygulamalarından toplu veritabanı geçişi ile Kullanıcı senaryolarını hedefler. Yönetilen örnek birkaç veritabanı geçiş seçeneğini destekler:

### <a name="back-up-and-restore"></a>Yedekleme ve geri yükleme  

Geçiş yaklaşımı SQL yedeklemelerini Azure Blob depolamaya kullanır. Azure Depolama Blobu 'nda depolanan yedeklemeler, [T-SQL restore komutu](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)kullanılarak yönetilen bir örneğe doğrudan geri yüklenebilir.

- Wide World Importers-standart veritabanı yedekleme dosyasının nasıl geri yükleneceğini gösteren bir hızlı başlangıç için bkz. [bir yedekleme dosyasını yönetilen bir örneğe geri yükleme](sql-database-managed-instance-get-started-restore.md). Bu hızlı başlangıçta, bir yedekleme dosyasını Azure blog depolama alanına yüklemeniz ve paylaşılan erişim imzası (SAS) anahtarı kullanarak güvence altına almanız gerekir.
- URL 'den geri yükleme hakkında daha fazla bilgi için bkz. [URL 'Den yerel GERI yükleme](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Yönetilen bir örnekten yedeklemeler, yalnızca başka bir yönetilen örneğe geri yüklenebilir. Şirket içi SQL Server veya tek bir veritabanı/elastik havuza geri yüklenemez.

### <a name="data-migration-service"></a>Veri geçiş hizmeti

Azure veritabanı geçiş hizmeti, birden çok veritabanı kaynağından Azure veri platformları arasında kesintisiz geçiş sağlamak için tasarlanan, tam olarak yönetilen bir hizmettir. Bu hizmet, var olan üçüncü taraf ve SQL Server veritabanlarını Azure SQL veritabanı 'na (tek veritabanları, elastik havuzlardaki havuza alınmış veritabanları ve yönetilen bir örnekteki örnek veritabanları) ve Azure VM 'de SQL Server taşımak için gereken görevleri basitleştirir. Şirket [içi VERITABANıNıZı DMS kullanarak yönetilen örneğe geçirme](https://aka.ms/migratetoMIusingDMS)konusuna bakın.

## <a name="sql-features-supported"></a>Desteklenen SQL özellikleri

Yönetilen örnek dağıtım seçeneği amaçlar, hizmet genel kullanıma sunuluncaya kadar aşamalar halinde SQL Server, şirket içi için %100 Surface alan uyumluluğuna yakın sunmaya olanak sağlar. Özellikler ve karşılaştırma listesi için bkz. [SQL veritabanı özelliği karşılaştırması](sql-database-features.md)ve yönetilen örneklerde t-SQL farkları listesi için SQL Server, bkz. [SQL Server yönetilen örnek T-SQL farklılıkları](sql-database-managed-instance-transact-sql-information.md).

Yönetilen örnek dağıtım seçeneği SQL 2008 veritabanlarına geriye dönük uyumluluğu destekler. SQL 2005 veritabanı sunucularından doğrudan geçiş desteklenir, geçirilen SQL 2005 veritabanlarının uyumluluk düzeyi SQL 2008 olarak güncelleştirilir.
  
Aşağıdaki diyagramda, yönetilen örnekteki Surface alanı uyumluluğu özetlenmektedir:  

![geçiş](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Şirket içinde ve yönetilen bir örnekte SQL Server arasındaki temel farklılıklar

Yönetilen örnek dağıtım seçeneği, bulutta her zaman güncel olmasını sağlar, yani şirket içi SQL Server bazı özellikler kullanımdan kalkmış, kullanımdan kaldırılmakta veya alternatiflere sahip olabilir. Araçların belirli bir özelliğin biraz farklı bir şekilde çalıştığını tanıması gerektiğinde veya bu hizmet tam olarak denetlediğiniz bir ortamda çalışmadığı zaman belirli durumlar vardır:

- Yüksek kullanılabilirlik, [her zaman açık kullanılabilirlik gruplarıyla](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)benzer bir teknoloji kullanılarak yerleşiktir ve önceden yapılandırılmıştır.
- Otomatik yedeklemeler ve zaman içinde geri yükleme. Müşteri, otomatik yedekleme zinciriyle kesintiye uğramayan `copy-only` yedeklemeleri başlatabilir.
- Yönetilen örnek tam fiziksel yolların belirtilmesine izin vermiyor, böylece tüm ilgili senaryolar farklı şekilde desteklenmelidir: RESTORE DB MOVE Ile desteklemez, CREATE DB fiziksel yollara izin vermez, BULK INSERT yalnızca Azure Blob 'Ları ile çalışır, vb.
- Yönetilen örnek, Windows kimlik doğrulamasını bulut alternatifi olarak [Azure AD kimlik doğrulamasını](sql-database-aad-authentication.md) destekler.
- Yönetilen örnek, bellek Içi OLTP nesneleri içeren veritabanları için XTP dosya grubunu ve dosyalarını otomatik olarak yönetir
- Yönetilen örnek SQL Server Integration Services (SSIS) destekler ve SSIS paketlerini depolayan SSIS kataloğunu (SSSıSDB) barındırabilir, ancak bunlar Azure Data Factory (ADF) içinde yönetilen bir Azure-SSIS Integration Runtime (IR) üzerinde yürütülür, bkz. [ADF 'de Azure-SSIS IR oluşturma ](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). SQL veritabanında SSIS özelliklerini karşılaştırmak için bkz. [Azure SQL veritabanı tek veritabanı, elastik havuz ve yönetilen örnek karşılaştırması](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).

### <a name="managed-instance-administration-features"></a>Yönetilen örnek yönetimi özellikleri

Yönetilen örnek dağıtım seçeneği, SQL veritabanı hizmeti bunları sizin için gerçekleştirdiğinden veya bu görevleri büyük ölçüde basitleştirdiği için sistem yöneticisinin yönetim görevlerinde daha az zaman harcamasını sağlar. Örneğin, [Işletim sistemi/RDBMS yükleme ve düzeltme eki uygulama](sql-database-high-availability.md), [dinamik örnek yeniden boyutlandırma ve yapılandırma](sql-database-single-database-scale.md), [yedeklemeler](sql-database-automated-backups.md), [veritabanı çoğaltma](replication-with-sql-database-managed-instance.md) (sistem veritabanları dahil), [yüksek kullanılabilirlik yapılandırması](sql-database-high-availability.md)ve sistem durumu ve [performans izleme](../azure-monitor/insights/azure-sql.md) veri akışlarının yapılandırması.

> [!IMPORTANT]
> Desteklenen, kısmen desteklenen ve desteklenmeyen özelliklerin listesi için bkz. [SQL veritabanı özellikleri](sql-database-features.md). Yönetilen örneklerde T-SQL farklarının listesi için SQL Server ve bkz. [yönetilen örnek T-SQL farklılıkları SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Yönetilen örneği programlı olarak belirleme

Aşağıdaki tabloda Transact SQL aracılığıyla erişilebilen, uygulamanızın yönetilen örnekle çalıştığını tespit etmek ve önemli özellikleri almak için kullanabileceğiniz çeşitli özellikler gösterilmektedir.

|Özellik|Değer|Açıklama|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM)-12.0.2000.8 2018-03-07 telif hakkı (C) 2018 Microsoft Corporation.|Bu değer SQL veritabanı ile aynıdır.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Bu değer SQL veritabanı ile aynıdır.|
|`SERVERPROPERTY('EngineEdition')`|8|Bu değer, yönetilen bir örneği benzersiz şekilde tanımlar.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Şu biçimdeki tam örnek DNS adı: `<instanceName>`. `<dnsPrefix>`.database.windows.net, burada `<instanceName>` adı müşteri tarafından sağlandığı için `<dnsPrefix>`, genel DNS adı benzersizliği ("wcus17662feb9ce98", örneğinde|Örnek: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Sonraki adımlar

- İlk yönetilen örneğinizi oluşturmayı öğrenmek için bkz. [Hızlı Başlangıç Kılavuzu](sql-database-managed-instance-get-started.md).
- Özellikler ve karşılaştırma listesi için bkz. [SQL ortak özellikleri](sql-database-features.md).
- VNet yapılandırması hakkında daha fazla bilgi için bkz. [yönetilen örnek VNET yapılandırması](sql-database-managed-instance-connectivity-architecture.md).
- Yönetilen bir örnek oluşturan ve bir veritabanını bir yedekleme dosyasından geri yükleyen hızlı başlangıç için bkz. [yönetilen örnek oluşturma](sql-database-managed-instance-get-started.md).
- Geçiş için Azure veritabanı geçiş hizmeti 'ni (DMS) kullanan bir öğretici için bkz. [DMS kullanarak yönetilen örnek geçişi](../dms/tutorial-sql-server-to-managed-instance.md).
- Yerleşik sorun giderme zekası ile yönetilen örnek veritabanı performansının gelişmiş izlenmesi için bkz. [Azure SQL Analytics kullanarak Azure SQL veritabanını izleme](../azure-monitor/insights/azure-sql.md).
- Fiyatlandırma bilgileri için bkz. [SQL veritabanı yönetilen örnek fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/managed/).
