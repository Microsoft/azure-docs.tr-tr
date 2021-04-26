---
title: Azure VM 'lerinde SQL Server yedekleme için Azure Backup destek matrisi
description: Azure Backup hizmeti ile Azure VM 'lerinde SQL Server yedeklenirken destek ayarlarının ve sınırlamaların özetini sağlar.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: 354f64eb86cd545860c47562fba7ff43babe72ca
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714155"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Azure VM 'lerinde SQL Server yedekleme için destek matrisi

Azure Backup, Microsoft Azure bulut platformunda barındırılan Azure VM 'lerinde SQL Server veritabanlarını yedeklemek için kullanabilirsiniz. Bu makalede, Azure VM 'lerinde SQL Server yedekleme senaryoları ve dağıtımları için genel destek ayarları ve sınırlamaları özetlenmektedir.

## <a name="scenario-support"></a>Senaryo desteği

**Destek** | **Ayrıntılar**
--- | ---
**Desteklenen dağıtımlar** | SQL Market Azure VM 'Leri ve Market olmayan (SQL Server el ile yüklenmiş) VM 'Ler desteklenir.
**Desteklenen bölgeler** | Avustralya Güney Doğu (Ao), Doğu Avustralya (AE), Avustralya Orta (AC), Avustralya Orta 2 (AC) <br> Güney Brezilya (BRS)<br> Kanada Orta (CNC), Kanada Doğu (CE)<br> Güney Doğu Asya (SEA), Doğu Asya (EA) <br> Doğu ABD (EUS), Doğu ABD 2 (EUS2), Orta Batı ABD (WCUS), Batı ABD (WUS); Batı ABD 2 (WUS 2) Orta Kuzey ABD (NCUS) Orta ABD (cu DÜZEYINDE KAPSANıR) Orta Güney ABD (SCUS) <br> Hindistan Orta (ıNC), Hindistan Güney (INS), Hindistan Batı <br> Japonya Doğu (JPE), Japonya Batı (JPW) <br> Kore Orta (KRC), Kore Güney (KRS) <br> Kuzey Avrupa (NE), Batı Avrupa <br> UK Güney (UKS), UK Batı (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Teksas, US DoD Orta, US DoD Doğu <br> Almanya Kuzey, Almanya Orta Batı <br> İsviçre Kuzey, İsviçre Batı <br> Orta Fransa <br> Çin Doğu, Çin Doğu 2, Çin Kuzey, Çin Kuzey 2
**Desteklenen işletim sistemleri** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux Şu anda desteklenmiyor.
**Desteklenen SQL Server sürümleri** | SQL Server 2019, [ürün yaşam döngüsünü ara](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)sayfasında SQL Server 2017 SQL Server 2016 ve SPS 'yi [ürün yaşam döngüsünü ara sayfasında](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)ayrıntılı olarak SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, geliştirici, Express.<br><br>Express yerel DB sürümleri desteklenmez.
**Desteklenen .NET sürümleri** | VM 'de yüklü .NET Framework 4.5.2 veya üzeri

## <a name="feature-considerations-and-limitations"></a>Özellik konuları ve sınırlamaları

|Ayar  |Üst sınır |
|---------|---------|
|Bir sunucuda (ve bir kasada) korunabilen veritabanlarının sayısı    |   2000      |
|Veritabanı boyutu destekleniyor (bunun ötesinde performans sorunları çıkabilir)   |   6 TB *      |
|Bir veritabanında desteklenen dosya sayısı    |   1000      |

_* Veritabanı boyutu sınırı, destekdiğimiz veri aktarım hızına ve yedekleme süresi sınırı yapılandırmasına bağlıdır. Sabit sınır değildir. Yedekleme verimlilik performansı hakkında [daha fazla bilgi edinin](#backup-throughput-performance) ._

* SQL Server yedekleme Azure portal veya **PowerShell** içinde yapılandırılabilir. CLı desteklenmez.
* Çözüm her iki tür [dağıtım](../azure-resource-manager/management/deployment-models.md) için de desteklenir-Azure Resource Manager VM 'ler ve klasik VM 'ler.
* Tüm yedekleme türleri (tam/fark/günlük) ve kurtarma modelleri (basit/tam/toplu günlüğe kaydedilir) desteklenir.
* **Salt okuma** veritabanları için: tam ve salt kopya tam yedeklemeler yalnızca desteklenen yedekleme türleridir.
* Yedekleme ilkesinde Kullanıcı tarafından açıkça etkinleştirildiyse SQL yerel sıkıştırması desteklenir. Azure Backup, Kullanıcı tarafından ayarlandığı şekilde bu denetimin değerine bağlı olarak, örnek düzeyi varsayılan değerlerini COMPRESSION/NO_COMPRESSION yan tümcesiyle geçersiz kılar.
* TDE etkin veritabanı yedeklemesi desteklenir. Bir TDE şifreli veritabanını başka bir SQL Server geri yüklemek için öncelikle [sertifikayı hedef sunucuya geri yüklemeniz](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)gerekir. SQL Server 2016 ve daha yeni sürümler için TDE özellikli veritabanları için yedekleme sıkıştırması, [burada](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)açıklandığı gibi daha düşük aktarım boyutunda mevcuttur.
* Yansıtma veritabanları ve veritabanı anlık görüntüleri için yedekleme ve geri yükleme işlemleri desteklenmez.
* SQL Server **Yük devretme kümesi örneği (FCı)** desteklenmez.
* Tek başına SQL Server örneğinizi veya SQL Always on kullanılabilirlik grubunu yedeklemek için birden fazla yedekleme çözümü kullanmak yedekleme hatasına neden olabilir. Bunu yapmaktan kaçının. Bir kullanılabilirlik grubunun iki düğümünü aynı veya farklı çözümlerle tek tek yedeklemek, yedekleme hatasına da neden olabilir.
* Kullanılabilirlik grupları yapılandırıldığında, yedeklemeler, birkaç etkene göre farklı düğümlerden alınır. Bir kullanılabilirlik grubu için yedekleme davranışı aşağıda özetlenmiştir.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Always On kullanılabilirlik gruplarında yedekleme davranışı

Yedeklemenin, bir kullanılabilirlik grubunun (AG) yalnızca bir düğümünde yapılandırılmasını öneririz. Her zaman yedeklemeyi birincil düğümle aynı bölgede yapılandırın. Diğer bir deyişle, her zaman yedeklemeyi yapılandırdığınız bölgede birincil düğümün bulunması gerekir. AG 'nin tüm düğümleri yedeklemenin yapılandırıldığı bölgede ise sorun yoktur.

#### <a name="for-cross-region-ag"></a>Bölgeler arası AG için

* Yedekleme tercihinden bağımsız olarak, yedeklemeler yalnızca yedeklemenin yapılandırıldığı bölgedeki düğümlerden çalıştırılır. Bunun nedeni, bölgeler arası yedeklemelerin desteklenmemektedir. Yalnızca iki düğümünüz varsa ve ikincil düğüm diğer bölgedeyse, yedeklemeler birincil düğümden çalışmaya devam eder (yedekleme tercihiniz ' ikincil yalnızca ' değilse).
* Bir düğüm, yedeklemenin yapılandırıldığı noktadan farklı bir bölgeye yük devreder, yedeklemeler başarısız olan bölgedeki düğümlerde başarısız olur.

Yedekleme tercihine ve yedeklemeler türlerine (tam/değişiklik/günlük/salt kopya) bağlı olarak, yedeklemeler belirli bir düğümden alınır (birincil/ikincil).

#### <a name="backup-preference-primary"></a>Yedekleme tercihi: birincil

**Yedekleme Türü** | **Node**
--- | ---
Tam | Birincil
Di | Birincil
Günlük |  Birincil
Copy-Only tam |  Birincil

#### <a name="backup-preference-secondary-only"></a>Yedekleme tercihi: yalnızca Ikincil

**Yedekleme Türü** | **Node**
--- | ---
Tam | Birincil
Di | Birincil
Günlük |  İkincil
Copy-Only tam |  İkincil

#### <a name="backup-preference-secondary"></a>Yedekleme tercihi: Ikincil

**Yedekleme Türü** | **Node**
--- | ---
Tam | Birincil
Di | Birincil
Günlük |  İkincil
Copy-Only tam |  İkincil

#### <a name="no-backup-preference"></a>Yedekleme tercihi yok

**Yedekleme Türü** | **Node**
--- | ---
Tam | Birincil
Di | Birincil
Günlük |  İkincil
Copy-Only tam |  İkincil

## <a name="backup-throughput-performance"></a>Yedek verimlilik performansı

Azure Backup, büyük SQL veritabanlarının tam ve fark yedeklemeleri (500 GB) için 200 Mbps 'lik tutarlı veri aktarımı hızını destekler. En iyi performansı kullanmak için şunları doğrulayın:

- Temel alınan VM (veritabanını barındıran SQL Server örneğini içeren), gereken ağ aktarım hızı ile yapılandırılır. VM 'nin en yüksek aktarım hızı 200 Mbps 'den küçükse Azure Backup en uygun hızda veri aktarabilir.<br>Ayrıca, veritabanı dosyalarını içeren diskte, sağlanan yeterli üretilen iş olması gerekir. Azure VM 'lerinde disk işleme ve performans hakkında [daha fazla bilgi edinin](../virtual-machines/disks-performance.md) . 
- VM 'de çalışan süreçler VM bant genişliğini tüketmez. 
- Yedekleme zamanlamaları, veritabanlarının bir alt kümesi arasında yayılır. Bir VM 'de eşzamanlı olarak çalışan birden çok yedekleme, yedeklemeler arasındaki ağ tüketimi oranını paylaşır. Eşzamanlı yedeklemelerin sayısını denetleme hakkında [daha fazla bilgi edinin](faq-backup-sql-server.yml#can-i-control-how-many-concurrent-backups-run-on-the-sql-server-) .

>[!NOTE]
> VM kaynakları, bant genişliği ve yedekleme ilkesi temel alınarak sunucu başına önerilen korumalı veritabanlarının yaklaşık sayısını hesaplamak için [ayrıntılı kaynak planlayıcısı](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) ' nı indirin.

## <a name="next-steps"></a>Sonraki adımlar

Azure VM 'de çalışan [SQL Server bir veritabanını nasıl yedekleyeceğinizi](backup-azure-sql-database.md) öğrenin.