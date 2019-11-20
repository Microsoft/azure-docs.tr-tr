---
title: T-SQL farklılıklarını çözme-geçiş
description: Azure SQL Veritabanında tam olarak desteklenmeyen Transact-SQL deyimleri
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: 15c661a1ef917dcf73b5a86cd450c94a35b08c88
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822487"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>SQL veritabanına geçiş sırasında Transact-SQL farklılıklarını çözme

Veritabanınızı SQL Server Azure SQL Server 'e [geçirirken](sql-database-single-database-migrate.md) , SQL Server geçirilebilmesi için veritabanınızın bazı yeniden mühendisler gerektirdiğini fark edebilirsiniz. Bu makalede, hem bu yeniden mühendisliğin gerçekleştirilmesi hem de yeniden mühendisliğin gerekli olduğu temel nedenlerini anlamak için size yardımcı olacak rehberlik sunulmaktadır. Uyumsuzlukları algılamak için [Data Migration Yardımcısı (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)kullanın.

## <a name="overview"></a>Genel Bakış

Uygulamaların kullandığı çoğu Transact-SQL özelliği hem Microsoft SQL Server hem de Azure SQL veritabanı 'nda tam olarak desteklenmektedir. Örneğin, veri türleri, işleçler, dize, aritmetik, mantıksal ve imleç işlevleri gibi çekirdek SQL bileşenleri, SQL Server ve SQL veritabanı 'nda aynı şekilde çalışır. Bununla birlikte, DDL (veri tanımlama dili) ve DML (veri işleme dili) öğelerinde yalnızca kısmen desteklenen (Bu makalede daha sonra tartıştığımız) bir T-SQL farkı vardır.

Bunlara ek olarak, Azure SQL veritabanı, ana veritabanı ve işletim sistemi bağımlılıklarındaki özellikleri yalıtmak üzere tasarlandığından, hiçbir şekilde desteklenmeyen bazı özellikler ve söz dizimi vardır. Bu nedenle, çoğu sunucu düzeyi etkinlik SQL veritabanı için uygun değildir. T-SQL deyimleri ve seçenekleri, sunucu düzeyindeki seçenekleri, işletim sistemi bileşenlerini yapılandırıp veya dosya sistemi yapılandırması belirttiğinizde kullanılamaz. Bu tür özellikler gerektiğinde, SQL veritabanından veya başka bir Azure özelliğinden ya da hizmetinden başka bir şekilde, uygun bir alternatif vardır.

Örneğin, yüksek kullanılabilirlik Azure SQL veritabanı ['nda, her zaman açık kullanılabilirlik gruplarıyla](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)benzer teknolojiyi kullanarak yerleşiktir. Kullanılabilirlik gruplarıyla ilgili T-SQL deyimleri, SQL veritabanı tarafından desteklenmez ve Always on kullanılabilirlik gruplarıyla ilişkili dinamik yönetim görünümleri de desteklenmez.

SQL veritabanı tarafından desteklenen ve desteklenmeyen özelliklerin listesi için bkz. [Azure SQL veritabanı özelliği karşılaştırması](sql-database-features.md). Bu sayfadaki liste, bu kılavuz ve Özellikler makalesini tamamlar ve Transact-SQL deyimlerine odaklanır.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Kısmi farklılıklar içeren Transact-SQL sözdizimi deyimleri

Çekirdek DDL (veri tanımlama dili) deyimleri mevcuttur, ancak bazı DDL deyimlerinin disk yerleştirme ve desteklenmeyen özelliklerle ilişkili uzantıları vardır.

- CREATE ve ALTER DATABASE deyimlerinin üç düzine seçeneği vardır. Deyimler yalnızca SQL Server için uygulanan dosya yerleşimi, FıLESTREAM ve hizmet Aracısı seçeneklerini içerir. Bu, geçirmeden önce veritabanları oluşturma, ancak veritabanı oluşturan T-SQL kodunu geçiriyorsanız, CREATE DATABASE (SQL Server Transact-SQL) konumundaki SQL Server sözdizimi ile [veritabanı oluşturma (Azure SQL veritabanı)](https://msdn.microsoft.com/library/dn268335.aspx) ile karşılaştırmanız gerekir [ ](https://msdn.microsoft.com/library/ms176061.aspx)kullandığınız tüm seçeneklerin desteklendiğinden emin olmak için. Azure SQL veritabanı için VERITABANı oluşturma hizmeti hedefi ve yalnızca SQL veritabanı için uygulanan elastik ölçek seçeneklerine sahiptir.
- FıLESTREAM desteklenmediğinden CREATE ve ALTER TABLE deyimlerinin SQL veritabanında kullanılamayan FileTable seçenekleri var.
- CREATE ve ALTER LOGIN deyimleri desteklenir, ancak SQL veritabanı tüm seçenekleri sunmaz. Veritabanınızı daha taşınabilir hale getirmek için SQL veritabanı, mümkün olduğunda oturum açma yerine içerilen veritabanı kullanıcılarını kullanarak teşvik eder. Daha fazla bilgi için bkz. [create/alter LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) ve [Database Access ile denetim ve verme](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Transact-SQL sözdizimi Azure SQL veritabanı 'nda desteklenmiyor

 [Azure SQL veritabanı özellik karşılaştırması](sql-database-features.md)bölümünde açıklanan desteklenmeyen özelliklerle ilgili Transact-SQL deyimlerine ek olarak, aşağıdaki deyimler ve deyim grupları desteklenmez. Bu nedenle, geçirilecek veritabanınız aşağıdaki özelliklerden herhangi birini kullanıyorsa t-SQL ' i yeniden mühendisler ve bu T-SQL özelliklerini ve deyimlerini ortadan kaldırır.

- Harmanlanmış sistem nesneleri
- Bağlantıyla ilgili: Endpoint deyimleri. SQL Veritabanı Windows kimlik doğrulamasını desteklemez ancak ona benzer olan Azure Active Directory kimlik doğrulamasını destekler. Bazı kimlik doğrulaması türleri için SSMS'nin en son sürümü gerekir. Daha fazla bilgi için bkz. [Azure Active Directory Kimlik Doğrulamasını Kullanarak SQL Veritabanına veya SQL Veri Ambarına Bağlanma](sql-database-aad-authentication.md).
- Üç veya dört bölüm adı kullanan veritabanları arası sorgular. (Salt okunur veritabanları arası sorgular [elastik veritabanı sorgusu](sql-database-elastic-query-overview.md) kullanılarak desteklenir.)
- Veritabanları arası sahiplik zinciri, `TRUSTWORTHY` ayarı
- `EXECUTE AS LOGIN` Bunun yerine "EXECUTE AS USER" kullanın.
- Genişletilebilir anahtar yönetimi haricinde şifreleme desteklenir
- Olay: olaylar, olay bildirimleri, sorgu bildirimleri
- Dosya yerleştirme: Microsoft Azure tarafından otomatik olarak yönetilen veritabanı dosyası yerleşimi, boyutu ve veritabanı dosyalarıyla ilgili sözdizimi.
- Yüksek kullanılabilirlik: Microsoft Azure hesabınız aracılığıyla yönetilen, yüksek kullanılabilirliğe ilişkin sözdizimi. Buna yedekleme, geri yükleme, Her Zaman Açık, veritabanı yansıtması, günlük aktarma ve kurtarma modları için söz dizimleri dahildir.
- Günlük okuyucu: SQL veritabanı 'nda kullanılamayan, günlük okuyucusuna dayalı sözdizimi: Itme çoğaltması, veri yakalamayı değiştirme. SQL Veritabanı gönderme temelli çoğaltma gönderisinin abonesi olabilir.
- İşlevler: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Donanım: donanımla ilgili sunucu ayarları ile ilgili sözdizimi: bellek, çalışan iş parçacıkları, CPU benzeşimi, izleme bayrakları. Bunun yerine hizmet katmanlarını ve işlem boyutlarını kullanın.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`ve dört bölümden oluşan adlar
- .NET Framework: SQL Server ile CLR tümleştirmesi
- Anlamsal arama
- Sunucu kimlik bilgileri: bunun yerine [veritabanı kapsamlı kimlik bilgilerini](https://msdn.microsoft.com/library/mt270260.aspx) kullanın.
- Sunucu düzeyi öğeler: sunucu rolleri, `sys.login_token`. `GRANT`, `REVOKE` ve `DENY` sunucu düzeyi izinler kullanılamaz ancak bazıları veritabanı düzeyi izinlerle değiştirilmiştir. Sunucu düzeyi kullanışlı DMV'lerden bazıları, eşdeğer veritabanı düzeyi DMV'lerine sahiptir.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` seçenekleri ve `RECONFIGURE`. Bazı seçenekler [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx) ile kullanılabilir.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: SQL Server Agent veya MSDB veritabanına dayalı sözdizimi: uyarılar, işleçler, merkezi yönetim sunucuları. Bunun yerine Azure PowerShell gibi betik uygulamaları kullanın.
- SQL Server denetim: bunun yerine SQL veritabanı denetimini kullanın.
- SQL Server izleme
- İzleme bayrakları: bazı izleme bayrağı öğeleri uyumluluk modlarına taşındı.
- Transact-SQL hata ayıklama
- Tetikleyiciler: Sunucu kapsamlı veya oturum açma tetikleyicileri
- `USE` deyimi: Veritabanı bağlamını farklı bir veritabanıyla değiştirmek için yeni veritabanıyla yeni bir bağlantı kurmanız gerekir.

## <a name="full-transact-sql-reference"></a>Tam Transact-SQL başvurusu

Transact-SQL dil bilgisi, kullanımı ve örnekleri hakkında daha fazla bilgi için bkz. [Transact-SQL başvurusu (veritabanı altyapısı)](https://msdn.microsoft.com/library/bb510741.aspx) çevrimiçi SQL Server Books Online.

### <a name="about-the-applies-to-tags"></a>"Uygulandığı öğe" etiketleri hakkında

Transact-SQL başvurusu, 2008 SQL Server sürümleriyle ilgili makaleleri içerir. Makale başlığının altında, dört SQL Server platformunu listelemek ve uygulanabilirliği gösteren bir simge çubuğu bulunur. Örneğin kullanılabilirlik grupları SQL Server 2012'de tanıtılmıştır.  [KULLANıLABILIRLIK grubu oluşturma](https://msdn.microsoft.com/library/ff878399.aspx) makalesi, deyimin SQL Server için geçerli olduğunu gösterir **(2012 ile başlayarak)** . Deyim SQL Server 2008, SQL Server 2008 R2, Azure SQL Veritabanı, Azure SQL Veri Ambarı veya Paralel Veri Ambarı için geçerli değildir.

Bazı durumlarda, bir makalenin genel konusu bir üründe kullanılabilir, ancak ürünler arasında küçük farklılıklar vardır. Farklar, makaledeki orta noktalara uygun şekilde gösterilir. Bazı durumlarda, bir makalenin genel konusu bir üründe kullanılabilir, ancak ürünler arasında küçük farklılıklar vardır. Farklar, makaledeki orta noktalara uygun şekilde gösterilir. Örneğin, oluşturma TETIKLEYICISI makalesi SQL veritabanı 'nda mevcuttur. Ancak sunucu düzeyi Tetikleyiciler için **tüm sunucu** seçeneği, sunucu DÜZEYI tetikleyicilerin SQL veritabanında kullanılamayacağını belirtir. Bunun yerine veritabanı düzeyi Tetikleyicileri kullanın.

## <a name="next-steps"></a>Sonraki adımlar

SQL veritabanı tarafından desteklenen ve desteklenmeyen özelliklerin listesi için bkz. [Azure SQL veritabanı özelliği karşılaştırması](sql-database-features.md). Bu sayfadaki liste, bu kılavuz ve Özellikler makalesini tamamlar ve Transact-SQL deyimlerine odaklanır.
