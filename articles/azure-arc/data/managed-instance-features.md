---
title: Azure Arc etkin SQL yönetilen örneğinin özellikleri ve özellikleri
description: Azure Arc etkin SQL yönetilen örneğinin özellikleri ve özellikleri
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c3f973da688a20fa60f2fb649b2c8d689bc2a98
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076658"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Azure Arc etkin SQL yönetilen örneğinin özellikleri ve özellikleri

Azure Arc etkin SQL yönetilen örneği, SQL Server en son kararlı sürümüyle ortak bir kod temeli paylaşır. Standart SQL dilinin, sorgu işleme ve veritabanı yönetim özelliklerinin çoğu aynıdır. SQL Server ile SQL veritabanı veya SQL yönetilen örneği arasında ortak olan özellikler şunlardır:

- Dil özellikleri- [akış dili anahtar sözcükleri](/sql/t-sql/language-elements/control-of-flow), [imleçler](/sql/t-sql/language-elements/cursors-transact-sql), [veri türleri](/sql/t-sql/data-types/data-types-transact-sql), [DML deyimlerinin](/sql/t-sql/queries/queries), [koşulların](/sql/t-sql/queries/predicates), [sıra numaralarının](/sql/relational-databases/sequence-numbers/sequence-numbers), [saklı yordamların](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)ve [değişkenlerin](/sql/t-sql/language-elements/variables-transact-sql)denetimi.
- Veritabanı özellikleri- [otomatik ayarlama (plan zorlama)](/sql/relational-databases/automatic-tuning/automatic-tuning), [değişiklik izleme](/sql/relational-databases/track-changes/about-change-tracking-sql-server), [veritabanı harmanlama](/sql/relational-databases/collations/set-or-change-the-database-collation), [Kapsanan veritabanları](/sql/relational-databases/databases/contained-databases), [Kapsanan kullanıcılar](/sql/relational-databases/security/contained-database-users-making-your-database-portable), [veri sıkıştırma](/sql/relational-databases/data-compression/data-compression), [veritabanı yapılandırma ayarları](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), [çevrimiçi dizin işlemleri](/sql/relational-databases/indexes/perform-index-operations-online), [bölümleme](/sql/relational-databases/partitions/partitioned-tables-and-indexes)ve zamana bağlı [Tablolar](/sql/relational-databases/tables/temporal-tables) ([bkz](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables). Başlarken Kılavuzu).
- Güvenlik özellikleri- [uygulama rolleri](/sql/relational-databases/security/authentication-access/application-roles), [dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking) ([Azure Portal ile SQL veritabanı dinamik veri maskeleme Ile çalışmaya başlama](../../azure-sql/database/dynamic-data-masking-configure-portal.md)), [satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security)
- Çok modelli yetenekler- [grafik işleme](/sql/relational-databases/graphs/sql-graph-overview), [JSON verileri](/sql/relational-databases/json/json-data-sql-server), [openxml](/sql/t-sql/functions/openxml-transact-sql), [uzamsal](/sql/relational-databases/spatial/spatial-data-sql-server), [openjson](/sql/t-sql/functions/openjson-transact-sql)ve [XML dizinleri](/sql/t-sql/statements/create-xml-index-transact-sql).



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>Azure Arc etkin SQL yönetilen örneği özellikleri

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> RDBMS yüksek kullanılabilirlik  
  
|Özellik|Azure Arc özellikli SQL Yönetilen Örneği|
|-------------|----------------|
|Günlük aktarma|Yes| 
|Yedekleme sıkıştırması|Yes|
|Veritabanı anlık görüntüsü|Yes|
|Her zaman yük devretme kümesi örneği<sup>1</sup>| Uygulanamaz. Benzer yetenekler mevcuttur |
|Always on kullanılabilirlik grupları<sup>2</sup>|HA özellikleri planlanmaktadır.|
|Temel kullanılabilirlik grupları <sup>2</sup>|HA özellikleri planlanmaktadır.|
|En düşük çoğaltma yürütmesi kullanılabilirlik grubu <sup>2</sup>|HA özellikleri planlanmaktadır.|
|Kümesiz kullanılabilirlik grubu|Yes|
|Çevrimiçi sayfa ve dosya geri yükleme|Yes|
|Çevrimiçi dizin oluşturma|Yes|
|Sürdürülebilir çevrimiçi dizin yeniden oluştur|Yes|
|Çevrimiçi şema değişikliği|Yes|
|Hızlı kurtarma|Yes|
|Yansıtılmış yedeklemeler|Yes|
|Etkin bellek ve CPU ekleme|Yes|
|Şifreli yedekleme|Yes|
|Azure 'a karma yedekleme (URL 'ye yedekleme)|Yes|

<sup>1</sup> Pod hatası olan senaryoda, yenı bir SQL yönetilen örneği, verilerinizi içeren kalıcı birime başlayıp yeniden eklenir. [Burada Kubernetes kalıcı birimleri hakkında daha fazla bilgi edinin](https://kubernetes.io/docs/concepts/storage/persistent-volumes).

<sup>2</sup> sonrakı sürümler AG becerileri sağlayacak 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> RDBMS ölçeklenebilirliği ve performansı  

| Özellik | Azure Arc özellikli SQL Yönetilen Örneği |
|--|--|
| Columnstore | Yes |
| Kümelenmiş columnstore dizinlerinde büyük nesne ikilileri | Yes |
| Çevrimiçi kümelenmemiş columnstore dizini yeniden oluşturma | Yes |
| Bellek İçi OLTP | Yes |
| Kalıcı ana bellek | Yes |
| Tablo ve Dizin bölümlendirme | Yes |
| Veri sıkıştırma | Yes |
| Resource Governor | Yes |
| Bölümlenmiş tablo paralelliği | Yes |
| NUMA 'yı algılayan ve büyük sayfa belleği ve arabellek dizisi ayırma | Yes |
| GÇ kaynak İdaresi | Yes |
| Gecikmeli dayanıklılık | Yes |
| Otomatik Ayarlama | Yes |
| Batch modu Uyarlamalı birleşimler | Yes |
| Toplu iş modu bellek Izni geri bildirimi | Yes |
| Çok deyimli tablo değerli Işlevler için araya eklemeli yürütme | Yes |
| Toplu ekleme geliştirmeleri | Yes |

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> RDBMS güvenliği

| Özellik | Azure Arc özellikli SQL Yönetilen Örneği |
|--|--|
| Satır düzeyinde güvenlik | Yes |
| Always Encrypted | Yes |
| Güvenli Kuşatmalarla Always Encrypted | No |
| Dinamik veri maskeleme | Yes |
| Temel denetim | Yes |
| Hassas denetim | Yes |
| Saydam veritabanı şifrelemesi | Yes |
| Kullanıcı tanımlı roller | Yes |
| Bağımsız veritabanları | Yes |
| Yedeklemeler için şifreleme | Yes |

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> RDBMS yönetilebilirliği  

| Özellik | Azure Arc özellikli SQL Yönetilen Örneği |
|--|--|
| Adanmış yönetici bağlantısı | Yes |
| PowerShell betik oluşturma desteği | Yes |
| Veri katmanı uygulaması bileşen işlemleri için destek-Ayıkla, dağıt, yükselt, Sil | Yes |
| İlke Otomasyonu (zamanlamayı ve değişikliği denetle) | Yes |
| Performans veri toplayıcısı | Yes |
| Standart performans raporları | Yes |
| Plan kılavuzlarını planlıyor ve planlıyor | Yes |
| Dizinli görünümlerin doğrudan sorgusu (NOEXPAND ipucu kullanarak) | Yes |
| Otomatik dizinli görünümler Bakımı | Yes |
| Dağıtılmış bölümlenmiş görünümler | Yes |
| Paralel dizinli işlemler | Yes |
| Sorgu iyileştiricisi tarafından dizinli görünümün otomatik kullanımı | Yes |
| Paralel tutarlılık denetimi | Yes |

### <a name="programmability"></a><a name="Programmability"></a> İle  

| Özellik | Azure Arc özellikli SQL Yönetilen Örneği |
|--|--|
| JSON | Evet |
| Sorgu Deposu | Yes | 
| Zamana bağlı | Yes | 
| Yerel XML desteği | Yes | 
| XML dizini oluşturma | Yes | 
| & UPSERT özellikleri BIRLEŞTIRME | Yes | 
| Tarih ve saat veri türleri | Yes | 
| Uluslararası duruma getirme desteği | Yes | 
| Tam metin ve anlamsal arama | No |
| Sorgudaki dilin belirtimi | Yes | 
| Hizmet Aracısı (mesajlaşma) | Yes | 
| Transact-SQL uç noktaları | Yes | 
| Graf | Yes | 
| Machine Learning Services | No |
| PolyBase | No |


### <a name="tools"></a>Araçlar

Azure Arc etkin SQL yönetilen örneği, verilerinizi yönetmenize yardımcı olabilecek çeşitli veri araçlarını destekler.

| **Araç** | Azure Arc özellikli SQL Yönetilen Örneği|
| --- | --- | --- |
| Azure portal <sup>1</sup> | No |
| Azure CLI’si | No |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | Yes |
| Azure PowerShell | Yes |
| [BACPAC dosyası (dışarı aktarma)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Yes |
| [BACPAC dosyası (içeri aktarma)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Yes |
| [SQL Server Veri Araçları (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | Yes |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | Yes |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | Yes |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | Yes |

<sup>1</sup> Azure Portal yalnızca önizleme sırasında salt okuma modunda Azure Arc etkin SQL yönetilen örneklerini görüntülemek için kullanılır.


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> Desteklenmeyen özellikler & Hizmetleri

Aşağıdaki özellikler ve hizmetler, Azure Arc etkin SQL yönetilen örneği için kullanılamaz. Bu özelliklerin desteği zamanla giderek daha etkin olacaktır.

| Alan | Desteklenmeyen Özellik veya hizmet |
|-----|-----|
| **Veritabanı altyapısı** | Birleştirme çoğaltması |
| &nbsp; | VERITABANıNı uzat |
| &nbsp; | Üçüncü taraf bağlantıları olan dağıtılmış sorgu |
| &nbsp; | SQL Server ve Azure SQL ürünleri dışındaki veri kaynaklarına bağlı sunucular |
| &nbsp; | Sistem genişletilmiş saklı yordamları (XP_CMDSHELL, vb.) |
| &nbsp; | FileTable, FıLESTREAM |
| &nbsp; | EXTERNAL_ACCESS veya güvenli olmayan izin kümesine sahip CLR derlemeleri |
| &nbsp; | Arabellek havuzu uzantısı |
| **SQL Server Agent** |  Alt sistemler: CmdExec, PowerShell, Queue Reader, SSIS, SSAS, SSRS |
| &nbsp; | Uyarılar |
| &nbsp; | Yönetilen Yedekleme |
| **Yüksek kullanılabilirlik** | Veritabanı yansıtma  |
| **Güvenlik** | Genişletilebilir anahtar yönetimi |
| &nbsp; | Bağlı sunucular için AD kimlik doğrulaması | 
| &nbsp; | Kullanılabilirlik grupları (AGs) için AD kimlik doğrulaması | 
