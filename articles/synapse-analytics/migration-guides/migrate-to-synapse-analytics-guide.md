---
title: 'Azure SYNAPSE Analytics: geçiş kılavuzu'
description: Veritabanlarınızı Azure SYNAPSE Analytics adanmış bir SQL havuzuna geçirmek için bu kılavuzu izleyin.
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 704c30516e9daf047bf5837aa6e2ed08306193db
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565653"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te bir veri ambarını adanmış bir SQL havuzuna geçirme

Aşağıdaki bölümler, var olan bir veri ambarı çözümünü Azure SYNAPSE Analytics adanmış bir SQL havuzuna geçirme ile ilgili yenilikler hakkında genel bakış sağlar.

## <a name="overview"></a>Genel Bakış

Geçişinize başlamadan önce, Azure SYNAPSE Analytics 'in iş yükünüz için en iyi çözüm olduğunu doğrulamanız gerekir. Azure SYNAPSE Analytics, büyük veriler üzerinde analiz gerçekleştirmek için tasarlanan bir dağıtılmış sistemdir. Azure SYNAPSE Analytics 'e geçiş yapmak, anlaşılması zor olmayan bazı tasarım değişiklikleri gerektirir ancak bu işlem biraz zaman alabilir. İşletmeniz bir kurumsal sınıf veri ambarı gerektiriyorsa, avantajlara değer verilir. Bununla birlikte, Azure SYNAPSE Analytics 'in gücüne ihtiyacınız yoksa, [SQL Server](https://docs.microsoft.com/sql/sql-server/) veya [Azure SQL veritabanı](https://docs.microsoft.com/azure/azure-sql/)'nı kullanmak daha uygun maliyetli bir hesaplıdır.

Şunları yaptığınızda Azure SYNAPSE Analytics 'i kullanmayı göz önünde bulundurun:

- Bir veya daha fazla terabayt veri olmalıdır.
- Analizi önemli miktarda veri üzerinde çalıştırmayı planlayın.
- İşlem ve depolamayı ölçeklendirme yeteneğinin olması gerekir.
- İhtiyaç duymazsanız işlem kaynaklarını duraklatarak maliyetlerde tasarruf etmek isteyebilirsiniz.

Azure SYNAPSE Analytics yerine, işletimsel (OLTP) iş yükleri için olan diğer seçenekleri göz önünde bulundurun:

- Yüksek frekanslı okuma ve yazma işlemleri.
- Çok sayıda tekil seçim.
- Yüksek hacimli tek satır eklemeleri.
- Satır satır işleme ihtiyaçları.
- Uyumsuz biçimler (örneğin, JSON ve XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

Kritik engelleyiciler müşterilerin yüzlerinden biri, bir sistemden diğerine geçiş yaparken veritabanı nesnelerini çevirirler. [Azure SYNAPSE patika](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) , var olan veri ambarınızın nesne çevirisini otomatikleştirerek modern bir veri ambarı platformuna yükseltmenize yardımcı olur. Azure SYNAPSE Analytics 'e daha hızlı geçiş olanağı sağlamak için kod çevirisini otomatikleştiren ücretsiz, sezgisel ve kullanımı kolay bir araçtır.

## <a name="prerequisites"></a>Önkoşullar

# <a name="migrate-from-sql-server"></a>[SQL Server’dan geçirme](#tab/migratefromSQLServer)

SQL Server veri Ambarınızı Azure SYNAPSE Analytics 'e geçirmek için, aşağıdaki önkoşulları karşıladığınızdan emin olun:

- Bir veri ambarına veya analiz iş yüküne sahip olmanız gerekir.
- Azure [SYNAPSE patika](https://www.microsoft.com/en-us/download/details.aspx?id=102787) 'ın en son sürümünü indirin SQL Server nesneleri Azure SYNAPSE Objects 'e geçirin.
- Azure SYNAPSE çalışma alanında [adanmış BIR SQL havuzuna](../get-started-create-workspace.md) sahip olmanız gerekir.

# <a name="migrate-from-netezza"></a>[Netezza 'den geçiş](#tab/migratefromNetezza)

Netezza veri Ambarınızı Azure SYNAPSE Analytics 'e geçirmek için, aşağıdaki önkoşulları karşıladığınızdan emin olun:

- Azure [SYNAPSE patika](https://www.microsoft.com/en-us/download/details.aspx?id=102787) 'ın en son sürümünü indirin SQL Server nesneleri Azure SYNAPSE Objects 'e geçirin.
- Azure SYNAPSE çalışma alanında [adanmış BIR SQL havuzuna](../get-started-create-workspace.md) sahip olmanız gerekir.

Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics çözümleri ve Netezza için geçiş](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Kar tanesi 'nden geçiş](#tab/migratefromSnowflake)

Kar tanesi veri Ambarınızı Azure SYNAPSE Analytics 'e geçirmek için aşağıdaki önkoşulları karşıladığınızdan emin olun:

- Azure [SYNAPSE patika](https://www.microsoft.com/en-us/download/details.aspx?id=102787) 'ın en son sürümünü Indirin ve Azure SYNAPSE nesnelerine kar nesneleri geçirin.
- Azure SYNAPSE çalışma alanında [adanmış BIR SQL havuzuna](../get-started-create-workspace.md) sahip olmanız gerekir.

# <a name="migrate-from-oracle"></a>[Oracle 'dan geçiş](#tab/migratefromOracle)

Oracle veri Ambarınızı Azure SYNAPSE Analytics 'e geçirmek için, aşağıdaki önkoşulları karşıladığınızdan emin olun:

- Bir veri ambarına veya analiz iş yüküne sahip olmanız gerekir.
- Oracle nesnelerini SQL Server dönüştürmek için Oracle SQL Server Geçiş Yardımcısı indirin. Daha fazla bilgi için bkz. [Oracle veritabanlarını SQL Server geçirme (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql).
- Azure [SYNAPSE patika](https://www.microsoft.com/download/details.aspx?id=102787) 'ın en son sürümünü indirin SQL Server nesneleri Azure SYNAPSE Objects 'e geçirin.
- Azure SYNAPSE çalışma alanında [adanmış BIR SQL havuzuna](../get-started-create-workspace.md) sahip olmanız gerekir.

Daha fazla bilgi için bkz. [Oracle veri ambarı Için Azure SYNAPSE Analytics çözümleri ve geçişi](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Geçiş öncesi

Mevcut bir çözümü Azure SYNAPSE Analytics 'e geçirmeye karar verdikten sonra, başlamadan önce geçiş planınızı planlamanız gerekir. Planlamanın birincil amacı, verilerinizin, tablo şemanızın ve kodunuzun Azure SYNAPSE Analytics ile uyumlu olmasını sağlamaktır. Geçerli sisteminiz ile Azure SYNAPSE Analiz'niz arasında, geçici çözüm yapmanız gereken bazı uyumluluk farklılıkları vardır. Ayrıca, büyük miktarlarda verilerin Azure 'a geçirilmesi zaman alır. Dikkatli bir planlama, verilerinizi Azure 'a alma sürecini hızlandıracaktır.

Planlamanın başka bir temel amacı, çözümünüzün Azure SYNAPSE Analytics 'in sağlamak üzere tasarlandığı yüksek sorgu performansından tam olarak yararlanmasını sağlamak için tasarımınızın ayarlanmasından faydalanır. Ölçek için veri ambarları tasarlamak, benzersiz tasarım desenleri sunarak geleneksel yaklaşımlar her zaman en iyi sonucu vermez. Geçişten sonra bazı tasarım ayarlamaları yapıldığında, işlemden önceki değişiklikleri yapmak zamandan daha sonra zaman kazandırır.

## <a name="migrate"></a>Geçiş

Başarılı bir geçiş gerçekleştirmek için tablo şemalarınızı, kodunuzu ve verilerinizi geçirmeniz gerekir. Bu konularda daha ayrıntılı yönergeler için aşağıdaki makalelere bakın:

- [Tablo tasarımını göz önünde bulundurun](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Kod değişikliğini dikkate al](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Verilerinizi geçirme](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [İş yükü yönetimini düşünün](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>Diğer kaynaklar

Müşteri danışmanlık ekibinin, blog gönderileri olarak yayınlanan bazı harika Azure SYNAPSE Analytics (eski adıyla Azure SQL veri ambarı) Kılavuzu vardır. Geçiş hakkında daha fazla bilgi için bkz. [uygulamada Azure SQL veri ambarı 'na veri geçirme](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Gerçek dünyada görevlendirmelere geçiş varlıkları

Bu geçiş senaryosunu tamamlamaya yönelik daha fazla yardım için aşağıdaki kaynaklara bakın. Bunlar, gerçek dünyada geçiş projesi katılımı desteğiyle geliştirilmiştir.

| Başlık/bağlantı                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Veri Iş yükü değerlendirmesi modeli ve aracı](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Bu araç, belirli bir iş yükü için önerilen "en uygun" hedef platformları, bulut hazırlığı ve uygulama ya da veritabanı düzeltme düzeyini sağlar. Otomatikleştirilmiş ve Tekdüzen hedef platformu karar süreci sağlayarak büyük Emlak değerlendirmelerine yardımcı olan basit, tek tıklamayla hesaplama ve rapor oluşturma işlemlerini sunar. |
| [Verileri Azure SYNAPSE Analytics 'e yüklerken veri kodlama sorunlarını işleme](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Bu blog gönderisi, verileri SQL veri ambarı 'na yüklemek için PolyBase 'i kullanırken karşılaşabileceğiniz bazı veri kodlama sorunları hakkında öngörüler sağlar. Bu makale ayrıca, bu tür sorunları aşmak ve verileri başarıyla yüklemek için kullanabileceğiniz bazı seçenekler sağlar. |
| [Azure SYNAPSE Analytics adanmış SQL havuzunda tablo boyutlarını alma](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Bir mimarın gerçekleştirmesi gereken önemli görevlerden biri, yeni bir ortam geçişi sonrası hakkında ölçümleri almak için kullanılır. Örnek olarak Şirket içinden buluta yükleme sürelerinin toplanması ve PolyBase yük sürelerinin toplanması sayılabilir. En önemli görevlerden biri, müşterinin geçerli platformuna kıyasla SQL veri ambarı 'nda depolama boyutunu belirlemektir. |
| [Şirket içi SQL Server oturumlarını Azure SYNAPSE Analytics 'e taşımaya yönelik yardımcı program](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | PowerShell betiği, oturum açma işlemlerini yeniden oluşturmak ve şirket içi SQL Server bir Azure SQL platformu hizmet olarak platform (PaaS) hizmetine veritabanı kullanıcıları seçmek için bir T-SQL komut betiği oluşturur. Araç, Windows Server Active Directory hesaplarının Azure Active Directory hesaplara otomatik olarak eşlenmesini sağlar veya şirket içi Windows Server Active Directory karşı her oturum açma için UPN araması yapabilir. Araç isteğe bağlı olarak SQL Server yerel oturum açmaları de taşıdıkça. Özel sunucu ve veritabanı rolleri, rol üyeliği, veritabanı rolü ve Kullanıcı izinleriyle birlikte betiklerdir. Kapsanan veritabanları desteklenmez ve yalnızca olası SQL Server izinlerinin bir alt kümesi betiklerdir. Destek belgesinde daha fazla bilgi mevcuttur ve betikte daha fazla bilgi sahibi olmak için açıklama bulunur. |

Veri SQL Mühendisliği ekibi bu kaynakları geliştirdik. Bu takımın temel kurucu, veri platformu geçiş projelerini Microsoft 'un Azure veri platformu 'na yönelik karmaşık modernleştirmeyi engellemeyi ve hızlandırmanızı sağlar.

## <a name="videos"></a>Videolar

[Walgreens 'in Retail Inventory System](https://www.youtube.com/watch?v=86dhd8N1lH4) 'i Netezza 'Tan Azure SYNAPSE Analytics 'e kadar 100 TB veri ile nasıl geçirildiğini izleyin.
