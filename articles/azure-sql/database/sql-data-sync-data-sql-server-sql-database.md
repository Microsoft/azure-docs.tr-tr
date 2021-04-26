---
title: Azure için SQL Data Sync nedir?
description: Bu genel bakış, Azure için SQL Data Sync sunarak verileri birden çok bulutta ve şirket içi veritabanlarında eşitlemenize olanak tanır.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/20/2019
ms.openlocfilehash: 695409740348e78ae51b263b44d9ed1cbadc1054
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531922"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Azure için SQL Data Sync nedir?

SQL Data Sync, hem şirket içinde hem de bulutta birden çok veritabanı üzerinde çift yönlü olarak seçtiğiniz verileri eşitlemenize olanak sağlayan Azure SQL veritabanı üzerinde oluşturulmuş bir hizmettir. 

> [!IMPORTANT]
> Azure SQL Data Sync Şu anda Azure SQL yönetilen örneğini desteklemez.


## <a name="overview"></a>Genel Bakış 

Veri eşitleme, bir eşitleme grubu kavramını temel alarak. Eşitleme grubu, eşitlemek istediğiniz veritabanlarının bir grubudur.

Veri eşitleme, verileri eşitlemek için bir hub ve bağlı bileşen topolojisi kullanır. Eşitleme grubundaki veritabanlarından birini hub veritabanı olarak tanımlarsınız. Veritabanlarının geri kalanı üye veritabanlarıdır. Eşitleme yalnızca Hub ve bireysel Üyeler arasında gerçekleşir.

- **Merkez veritabanı** BIR Azure SQL veritabanı olmalıdır.
- **Üye veritabanları** , Azure SQL veritabanı 'nda ya da SQL Server örneklerinde veritabanları olabilir.
- **Eşitleme meta verileri veritabanı** , veri eşitleme için meta verileri ve günlüğü içerir. Eşitleme meta verileri veritabanı, hub veritabanıyla aynı bölgede bulunan bir Azure SQL veritabanı olmalıdır. Eşitleme meta verileri veritabanı, müşteri tarafından oluşturulmuştur ve müşteriye aittir. Her bölge ve abonelik için yalnızca bir eşitleme meta veri veritabanınız olabilir. Eşitleme grupları veya eşitleme aracıları mevcut durumdayken eşitleme meta verileri veritabanı silinemez veya yeniden adlandırılamaz. Microsoft, eşitleme meta verileri veritabanı olarak kullanılmak üzere yeni, boş bir veritabanı oluşturmayı önerir. Veri eşitleme, bu veritabanında tabloları oluşturur ve sık sık iş yükü çalıştırır.

> [!NOTE]
> Bir şirket içi veritabanını üye veritabanı olarak kullanıyorsanız, [yerel bir eşitleme Aracısı yükleyip yapılandırmanız](sql-data-sync-sql-server-configure.md#add-on-prem)gerekir.

![Veritabanları arasında verileri eşitleme](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

Bir eşitleme grubu aşağıdaki özelliklere sahiptir:

- **Eşitleme şeması** hangi verilerin eşitlendiğini açıklar.
- **Eşitleme yönü** iki yönlü olabilir veya yalnızca bir yönde akabilir. Diğer bir deyişle, eşitleme yönü *hub* veya *hub 'a üye* ya da her ikisi de olabilir.
- Eşitleme **aralığı** , eşitlemenin ne sıklıkla oluştuğunu açıklar.
- **Çakışma çözümleme ilkesi** , *hub WINS* veya *üye WINS* olabilen bir grup düzeyi ilkesidir.

## <a name="when-to-use"></a>Kullanılması gereken durumlar

Veri eşitleme, verilerin Azure SQL veritabanı veya SQL Server çeşitli veritabanları arasında güncelleştirilmesi gereken durumlarda yararlıdır. Veri eşitlemeye yönelik başlıca kullanım durumları aşağıda verilmiştir:

- **Karma veri eşitleme:** Veri eşitleme ile, karma uygulamaları etkinleştirmek için SQL Server ve Azure SQL veritabanı 'nda veritabanlarınız arasında verileri eşitlenmiş halde tutabilirsiniz. Bu özellik, buluta geçmeyi düşünen ve Azure 'a bazı uygulamaları yerleştirmek istediğiniz müşterileri ele alabilir.
- **Dağıtılmış uygulamalar:** Birçok durumda, farklı veritabanları arasında farklı iş yüklerini ayırmak yararlı olur. Örneğin, büyük bir üretim veritabanınız varsa, ancak bu verilerde bir raporlama veya analiz iş yükü çalıştırmanız gerekiyorsa, bu ek iş yükü için ikinci bir veritabanı olması yararlı olacaktır. Bu yaklaşım, üretim iş yükünüzün performans etkisini en aza indirir. Bu iki veritabanını eşitlenmiş halde tutmak için veri eşitleme kullanabilirsiniz.
- **Küresel olarak dağıtılan uygulamalar:** Birçok işletme birçok bölgeye ve hatta birkaç ülkeye/bölgeye yayılamaz. Ağ gecikmesini en aza indirmek için, verilerinizin size yakın bir bölgede olması en iyisidir. Veri eşitleme ile, dünyanın dört bir yanındaki bölgelerde veritabanlarını kolayca koruyabilirsiniz.

Veri eşitleme, aşağıdaki senaryolar için tercih edilen çözüm değildir:

| Senaryo | Önerilen bazı çözümler |
|----------|----------------------------|
| Olağanüstü Durum Kurtarma | [Azure coğrafi olarak yedekli yedeklemeler](automated-backups-overview.md) |
| Ölçeği oku | [Salt okunurdur ve salt okuma sorgu iş yüklerinin yükünü dengelemek için salt okuma çoğaltmaları kullanın (Önizleme)](read-scale-out.md) |
| ETL (OLTP-OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) veya [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| SQL Server 'den Azure SQL veritabanı 'na geçiş. Ancak, kaynak ve hedefin eşitlenmiş durumda tutulduğundan emin olmak için geçiş tamamlandıktan sonra SQL Data Sync kullanılabilir.  | [Azure Veritabanı Geçiş Hizmeti](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Nasıl çalışır?

- **Veri değişikliklerini izleme:** Veri eşitleme, INSERT, Update ve DELETE tetikleyicilerini kullanarak değişiklikleri izler. Değişiklikler, Kullanıcı veritabanındaki bir yan tabloya kaydedilir. BULK INSERT varsayılan olarak Tetikleyicileri tetikleyeceğini unutmayın. FIRE_TRIGGERS belirtilmemişse, hiçbir ekleme tetikleyicisi yürütülmez. Veri eşitleme 'nin Bu eklemeleri izleyebilmesi için FIRE_TRIGGERS seçeneğini ekleyin. 
- **Veriler eşitleniyor:** Veri eşitleme, hub ve bağlı bileşen modelinde tasarlanmıştır. Hub her üyeyle tek tek eşitlenir. Hub 'daki değişiklikler üyeye indirilir ve Üyeden yapılan değişiklikler hub 'a yüklenir.
- **Çakışmalar çözümleniyor:** Veri eşitleme, çakışma çözümü, *Merkez WINS* veya *üye WINS* için iki seçenek sunar.
  - *Merkez WINS*' i seçerseniz, hub 'daki değişiklikler her zaman üyenin değişikliklerinin üzerine yazar.
  - *Üye WINS*' i seçerseniz, üyedeki değişiklikler hub 'daki değişiklikler üzerine yazılır. Birden fazla üye varsa, son değer öncelikle hangi üyenin eşitlendiği üzerinde değişir.

## <a name="compare-with-transactional-replication"></a>Işlemsel çoğaltma ile karşılaştırın

| | Data Sync | İşlem Çoğaltması |
|---|---|---|
| **Avantajlar** | -Etkin-etkin destek<br/>-Şirket içi ve Azure SQL veritabanı arasında çift yönlü | -Düşük gecikme süresi<br/>-İşlemsel tutarlılık<br/>-Geçişten sonra var olan topolojiyi yeniden kullan <br/>-Azure SQL yönetilen örnek desteği |
| **Dezavantajlar** | -İşlem tutarlılığı yok<br/>-Daha yüksek performans etkisi | -Azure SQL veritabanından yayımlanamıyor <br/>-Yüksek bakım maliyeti |

## <a name="private-link-for-data-sync-preview"></a>Veri eşitleme için özel bağlantı (Önizleme)
Yeni özel bağlantı (Önizleme) özelliği, veri eşitleme işlemi sırasında eşitleme hizmeti ile üye/Merkez veritabanları arasında güvenli bir bağlantı kurmak için hizmet tarafından yönetilen özel uç nokta seçmenize olanak sağlar. Hizmet tarafından yönetilen özel uç nokta, belirli bir sanal ağ ve alt ağ içindeki özel bir IP adresidir. Veri eşitleme içinde, hizmet tarafından yönetilen özel uç nokta Microsoft tarafından oluşturulur ve yalnızca belirli bir eşitleme işlemi için veri eşitleme hizmeti tarafından kullanılır. Özel bağlantıyı ayarlamadan önce, özelliğin [genel gereksinimlerini](sql-data-sync-data-sql-server-sql-database.md#general-requirements) okuyun. 

![Veri eşitleme için özel bağlantı](./media/sql-data-sync-data-sql-server-sql-database/sync-private-link-overview.png)

> [!NOTE]
> Eşitleme grubu dağıtımı sırasında veya PowerShell kullanarak, Azure portal **Özel uç nokta bağlantıları** sayfasında, hizmet tarafından yönetilen özel uç noktasını el ile onaylamanız gerekir.

## <a name="get-started"></a>başlarken 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Azure portal veri eşitlemesini ayarlama

- [Azure SQL Data Sync’i ayarlama](sql-data-sync-sql-server-configure.md)
- Veri eşitleme Aracısı- [Azure SQL Data Sync Için veri eşitleme Aracısı](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>PowerShell ile veri eşitlemesini ayarlama

- [Azure SQL veritabanı 'nda birden çok veritabanı arasında eşitleme yapmak için PowerShell 'i kullanma](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [PowerShell kullanarak Azure SQL veritabanındaki bir veritabanı ile bir SQL Server örneğindeki veritabanları arasında eşitleme](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="set-up-data-sync-with-rest-api"></a>REST API veri eşitlemesini ayarlama
- [Azure SQL veritabanı 'nda birden çok veritabanı arasında eşitleme yapmak için REST API kullanma](scripts/sql-data-sync-sync-data-between-sql-databases-rest-api.md)

### <a name="review-the-best-practices-for-data-sync"></a>Veri eşitleme için en iyi uygulamaları gözden geçirin

- [Azure SQL Data Sync için en iyi yöntemler](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Bir sorun oluştu

- [Azure SQL Data Sync ile ilgili sorun giderme](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Tutarlılık ve performans

### <a name="eventual-consistency"></a>Nihai tutarlılık

Veri eşitleme, tetikleyici tabanlı olduğundan, işlemsel tutarlılık garanti edilmez. Microsoft, tüm değişikliklerin sonunda yapıldığından ve veri eşitlemenin veri kaybına neden olmadığı garantisi verir.

### <a name="performance-impact"></a>Performans etkisi

Veri eşitleme, değişiklikleri izlemek için INSERT, Update ve DELETE tetikleyicilerini kullanır. Değişiklik izleme için Kullanıcı veritabanında yan tablolar oluşturur. Bu değişiklik izleme etkinliklerinin veritabanı iş yükünüz üzerinde bir etkisi vardır. Hizmet katmanınızı değerlendirin ve gerekirse yükseltin.

Eşitleme grubu oluşturma, güncelleştirme ve silme sırasında sağlama ve sağlamayı kaldırma, veritabanı performansını da etkileyebilir.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Gereksinimler ve sınırlamalar

### <a name="general-requirements"></a>Genel gereksinimler

- Her tablo bir birincil anahtara sahip olmalıdır. Herhangi bir satırdaki birincil anahtar değerini değiştirmeyin. Birincil anahtar değerini değiştirmeniz gerekiyorsa, satırı silin ve yeni birincil anahtar değeriyle yeniden oluşturun.

> [!IMPORTANT]
> Mevcut bir birincil anahtarın değerini değiştirmek aşağıdaki hatalı davranışa neden olur:
> - Eşit bir sorun bildirmese de, hub ve üye arasındaki veriler kaybolabilir.
> - Birincil anahtar değişikliği nedeniyle izleme tablosu kaynaktan mevcut olmayan bir satır içerdiğinden eşitleme başarısız olabilir.

- Hem eşitleme üyeleri hem de Hub için anlık görüntü yalıtımının etkinleştirilmesi gerekir. Daha fazla bilgi için bkz. [SQL Server'da Anlık Görüntü Yalıtımı](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

- Özel bağlantıyı veri eşitleme ile birlikte kullanmak için, hem üye hem de Merkez veritabanlarının aynı bulut türünde (örneğin, hem genel bulutta hem de kamu bulutu 'nda) Azure 'da (aynı veya farklı bölgelerde) barındırılması gerekir. Ayrıca, özel bağlantıyı kullanmak için Microsoft. Network kaynak sağlayıcılarının hub ve üye sunucuları barındıran abonelikler için kayıtlı olması gerekir. Son olarak, eşitleme yapılandırması sırasında, Azure portal veya PowerShell aracılığıyla "özel uç nokta bağlantıları" bölümünde bulunan veri eşitleme için özel bağlantıyı el ile onaylamanız gerekir. Özel bağlantıyı onaylama hakkında daha fazla bilgi için bkz. [set up SQL Data Sync](./sql-data-sync-sql-server-configure.md). Hizmet tarafından yönetilen özel uç noktasını onayladıktan sonra, eşitleme hizmeti ile üye/Merkez veritabanları arasındaki tüm iletişimler özel bağlantı üzerinden gerçekleşir. Mevcut eşitleme grupları bu özelliğin etkinleştirilmesini sağlamak için güncelleştirilebilirler.

### <a name="general-limitations"></a>Genel sınırlamalar

- Tablo, birincil anahtar olmayan bir kimlik sütununa sahip olamaz.
- Veri eşitleme kullanmak için bir tablonun kümelenmiş bir dizini olmalıdır.
- Birincil anahtar şu veri türlerine sahip olamaz: sql_variant, binary, varbinary, Image, XML.
- Aşağıdaki veri türlerini birincil anahtar olarak kullanırken dikkatli olun, çünkü desteklenen duyarlık yalnızca ikinci-saat, DateTime, datetime2, DateTimeOffset olur.
- Nesnelerin (veritabanları, tablolar ve sütunlar) adları, yazdırılabilir karakterler (.), sol köşeli ayraç ([) veya sağ köşeli ayraç (]) içeremez.
- Tablo adı yazdırılabilir karakterler içeremez:! "# $% ' () * +-boşluk
- Azure Active Directory kimlik doğrulaması desteklenmiyor.
- Aynı ada sahip ancak farklı bir şemaya sahip tablolar varsa (örneğin, dbo. Customers ve Sales. Customers) yalnızca bir tablo eşitlemeye eklenebilir.
- User-Defined veri türlerine sahip sütunlar desteklenmez
- Sunucuları farklı abonelikler arasında taşımak desteklenmez. 
- İki birincil anahtar yalnızca büyük/küçük harf olarak farklıysa (örn. foo ve foo), veri eşitleme bu senaryoyu desteklemez.

#### <a name="unsupported-data-types"></a>Desteklenmeyen veri türleri

- Akışı
- SQL/CLR UDT
- XMLSchemaCollection (XML destekleniyor)
- İmleç, RowVersion, timestamp, HierarchyId

#### <a name="unsupported-column-types"></a>Desteklenmeyen sütun türleri

Veri eşitleme, salt okuma veya sistem tarafından oluşturulmuş sütunları eşitleyemiyor. Örnek:

- Hesaplanan sütunlar.
- Zamana bağlı tablolar için sistem tarafından oluşturulan sütunlar.

#### <a name="limitations-on-service-and-database-dimensions"></a>Hizmet ve veritabanı boyutlarına ilişkin sınırlamalar

| **Boyutlarına**                                                  | **Sınır**              | **Geçici çözüm**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Herhangi bir veritabanının ait olduğu en fazla eşitleme grubu sayısı.       | 5                      |                             |
| Tek bir eşitleme grubundaki en fazla uç nokta sayısı              | 30                     |                             |
| Tek bir eşitleme grubundaki en fazla şirket içi uç nokta sayısı. | 5                      | Birden çok eşitleme grubu oluşturma |
| Veritabanı, tablo, şema ve sütun adları                       | ad başına 50 karakter |                             |
| Bir eşitleme grubundaki tablolar                                          | 500                    | Birden çok eşitleme grubu oluşturma |
| Bir eşitleme grubundaki tablodaki sütunlar                              | 1000                   |                             |
| Tablodaki veri satırı boyutu                                        | 24 MB                  |                             |

> [!NOTE]
> Yalnızca bir eşitleme grubu varsa, tek bir eşitleme grubunda 30 ' a kadar uç nokta olabilir. Birden fazla eşitleme grubu varsa, tüm eşitleme gruplarındaki bitiş noktalarının toplam sayısı 30 ' u aşamaz. Bir veritabanı birden çok eşitleme grubuna aitse, birden fazla uç nokta olarak sayılır.

### <a name="network-requirements"></a>Ağ gereksinimleri

> [!NOTE]
> Özel bağlantı kullanıyorsanız, bu ağ gereksinimleri uygulanmaz. 

Eşitleme grubu oluşturulduğunda, veri eşitleme hizmetinin hub veritabanına bağlanması gerekir. Eşitleme grubunu oluşturduğunuzda, Azure SQL Server 'ın ayarlarında aşağıdaki yapılandırma olmalıdır `Firewalls and virtual networks` :

 * *Ortak ağ erişimini reddetme* *devre dışı* olarak ayarlanmalıdır.
 * *Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin ver* ' in *Evet* olarak ayarlanması veya [veri eşitleme hizmeti tarafından kullanılan IP adresleri](network-access-controls-overview.md#data-sync)için IP kuralları oluşturmanız gerekir.

Eşitleme grubu oluşturulup sağlandıktan sonra bu ayarları devre dışı bırakabilirsiniz. Eşitleme Aracısı doğrudan hub veritabanına bağlanır ve aracının hub sunucusuna erişmesine izin vermek için sunucunun [güvenlik DUVARı IP kurallarını](firewall-configure.md) veya [Özel uç noktalarını](private-endpoint-overview.md) kullanabilirsiniz.

> [!NOTE]
> Eşitleme grubunun şema ayarlarını değiştirirseniz, hub veritabanının yeniden sağlanması için veri eşitleme hizmeti 'nin sunucuya yeniden erişmesine izin vermeniz gerekir.

## <a name="faq-about-sql-data-sync"></a>SQL Data Sync hakkında SSS

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>SQL Data Sync hizmeti maliyeti ne kadar sürer?

SQL Data Sync hizmetin kendisi için ücret alınmaz. Ancak, SQL veritabanı örneğiniz için ve dışında veri taşıma ücretleri de toplaırsınız. Daha fazla bilgi için bkz. [SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Veri eşitlemesini destekleyen bölgeler

SQL Data Sync tüm bölgelerde kullanılabilir.

### <a name="is-a-sql-database-account-required"></a>Bir SQL veritabanı hesabı gereklidir

Evet. Hub veritabanını barındırmak için bir SQL veritabanı hesabınızın olması gerekir.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Yalnızca SQL Server veritabanları arasında eşitleme yapmak için veri eşitlemeyi kullanabilir miyim

Doğrudan değil. Ancak, Azure 'da bir hub veritabanı oluşturup sonra şirket içi veritabanlarını eşitleme grubuna ekleyerek SQL Server veritabanları arasında dolaylı olarak eşitleme yapabilirsiniz.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Farklı aboneliklere ait olan SQL veritabanında veritabanları arasında eşitleme yapmak için veri eşitleme kullanabilir miyim

Evet. Farklı aboneliklere ait kaynak gruplarına ait veritabanları arasında eşitleme yapabilirsiniz.

- Abonelikler aynı kiracıya aitse ve tüm abonelikler için izniniz varsa, Azure portal eşitleme grubunu yapılandırabilirsiniz.
- Aksi takdirde, farklı aboneliklere ait olan eşitleme üyelerini eklemek için PowerShell kullanmanız gerekir.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Farklı bulutlara ait olan SQL veritabanında veritabanları arasında eşitleme yapmak için veri eşitleme kullanabilir miyim (Azure genel bulutu ve Azure Çin 21Vianet gibi)

Evet. Farklı bulutlara ait veritabanları arasında eşitleme yapabilirsiniz. Farklı aboneliklere ait olan eşitleme üyelerini eklemek için PowerShell kullanmanız gerekir.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Veri eşitlemesini, üretim veritabanından boş bir veritabanına veri kaynağı için kullanabilir miyim ve sonra bunları eşitleyebilirsiniz

Evet. Şemayı orijinalden komut dosyasıyla yeni veritabanında el ile oluşturun. Şemayı oluşturduktan sonra, verileri kopyalamak ve eşitlenmiş halde tutmak için tabloları bir eşitleme grubuna ekleyin.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Veritabanlarınızı yedeklemek ve geri yüklemek için SQL Data Sync kullanmalıyım

Verilerinizin yedeğini oluşturmak için SQL Data Sync kullanılması önerilmez. SQL Data Sync eşitlemeler sürümü olmadığından belirli bir zaman noktasına yedekleme ve geri yükleme yapamazsınız. Ayrıca, SQL Data Sync saklı yordamlar gibi diğer SQL nesnelerini de yedeklememez ve geri yükleme işleminin hemen eşdeğerini yapmaz.

Önerilen bir yedekleme tekniği için bkz. [Azure SQL veritabanı 'nda bir veritabanını kopyalama](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Veri eşitleme eşitleme şifrelenmiş tabloları ve sütunları

- Bir veritabanı Always Encrypted kullanıyorsa, *yalnızca şifrelenmemiş tabloları* ve sütunları eşitleyebilirsiniz. Veri eşitlemesi verilerin şifresini çözemediği için şifrelenmiş sütunları eşitleyemezsiniz.
- Bir sütun Column-Level şifrelemeyi (CLE) kullanıyorsa, satır boyutu en fazla 24 MB boyutundan daha az olduğu sürece sütunu eşitleyebilirsiniz. Veri eşitleme, anahtar (CLE) tarafından şifrelenmiş sütunu normal ikili veriler olarak değerlendirir. Diğer eşitleme üyelerdeki verilerin şifresini çözmek için aynı sertifikaya sahip olmanız gerekir.

### <a name="is-collation-supported-in-sql-data-sync"></a>SQL Data Sync içinde harmanlama destekleniyor

Evet. SQL Data Sync, aşağıdaki senaryolarda harmanlamayı destekler:

- Seçili eşitleme şeması tabloları hub 'ınızda veya üye veritabanlarında yoksa, eşitleme grubunu dağıttığınızda, hizmet otomatik olarak ilgili tabloları ve sütunları boş hedef veritabanlarında seçilen harmanlama ayarları ile birlikte oluşturur.
- Eşitlenecek tablolar hem hub 'ınızdaki hem de üye veritabanlarında zaten mevcutsa, SQL Data Sync eşitleme grubunu başarılı bir şekilde dağıtmak için birincil anahtar sütunlarının hub ve üye veritabanları arasında aynı harmanlamaya sahip olmasını gerektirir. Birincil anahtar sütunlarından farklı sütunlarda harmanlama kısıtlaması yok.

### <a name="is-federation-supported-in-sql-data-sync"></a>SQL Data Sync sürümünde Federasyon destekleniyor

Federasyon kök veritabanı SQL Data Sync hizmetinde herhangi bir kısıtlama olmadan kullanılabilir. Federasyon veritabanı uç noktasını geçerli SQL Data Sync sürümüne ekleyemezsiniz.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Kendi veritabanınızı getir (BYOD) özelliğini kullanarak Dynamics 365 ' den aktarılmış verileri eşitlemek için veri eşitlemeyi kullanabilir miyim?

Dynamics 365 kendi veritabanınızı getir özelliği, yöneticilerin veri varlıklarını uygulamadan kendi Microsoft Azure SQL veritabanı 'na dışarı aktarmanıza olanak tanır. Veri eşitleme, veriler **artımlı gönderim** kullanılarak aktarılıyorsa (tam gönderim desteklenmez) ve **hedef veritabanında Tetikleyicileri etkinleştir** **Evet** olarak ayarlanırsa, bu verileri diğer veritabanlarına eşitlemek için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

### <a name="update-the-schema-of-a-synced-database"></a>Eşitlenmiş bir veritabanının şemasını güncelleştirme

Bir eşitleme grubundaki bir veritabanının şemasını güncelleştirmeniz mı gerekiyor? Şema değişiklikleri otomatik olarak çoğaltılmaz. Bazı çözümler için aşağıdaki makalelere bakın:

- [Azure 'da SQL Data Sync şema değişikliklerinin çoğaltılmasını otomatikleştirin](./sql-data-sync-update-sync-schema.md)
- [Mevcut bir eşitleme grubunda eşitleme şemasını güncelleştirmek için PowerShell kullanma](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>İzleme ve sorun giderme

SQL Data Sync beklendiği gibi yapılıyor mu? Etkinliği izlemek ve sorunları gidermek için aşağıdaki makalelere bakın:

- [Azure Izleyici günlükleriyle SQL Data Sync izleme](./monitor-tune-overview.md)
- [Azure SQL Data Sync ile ilgili sorun giderme](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>Azure SQL veritabanı hakkında daha fazla bilgi

Azure SQL veritabanı hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [SQL Veritabanı'na Genel Bakış](sql-database-paas-overview.md)
- [Veritabanı Yaşam Döngüsü Yönetimi](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
