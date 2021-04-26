---
title: Akıllı İçgörüler ile performans sorunlarını giderme
description: Akıllı İçgörüler, Azure SQL veritabanı ve Azure SQL yönetilen örnek performans sorunlarını gidermenize yardımcı olur.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: troubleshooting
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 17ea6716f090144e8dfef16721bfb69dc23e9912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589324"
---
# <a name="troubleshoot-azure-sql-database-and-azure-sql-managed-instance-performance-issues-with-intelligent-insights"></a>Akıllı İçgörüler ile Azure SQL veritabanı ve Azure SQL yönetilen örnek performans sorunlarını giderme
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Bu sayfada Azure SQL veritabanı hakkında bilgi ve [akıllı içgörüler](intelligent-insights-overview.md) kaynak günlüğü aracılığıyla ALGıLANAN Azure SQL yönetilen örnek performans sorunları sağlanmaktadır. Ölçümler ve kaynak günlükleri, özel DevOps uyarısı ve raporlama özellikleri için [Azure izleyici günlüklerine](../../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs), [Azure depolama](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)'ya veya üçüncü taraf bir çözüme akışla eklenebilir.

> [!NOTE]
> Akıllı İçgörüler kullanarak hızlı bir performans sorun giderme kılavuzu için, bu belgede [Önerilen sorun giderme akış](intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) Akış Çizelgesine bakın.
>
> Akıllı Öngörüler şu bölgelerde kullanılamayan bir önizleme özelliğidir: Batı Avrupa, Kuzey Avrupa, Batı ABD 1 ve Doğu ABD 1.

## <a name="detectable-database-performance-patterns"></a>Algılanabilir veritabanı performans desenleri

Akıllı İçgörüler, sorgu yürütme bekleme süreleri, hatalar veya zaman aşımları temelinde performans sorunlarını otomatik olarak algılar. Akıllı İçgörüler çıktıları, kaynak günlüğüne performans desenleri algıladı. Algılanabilir performans desenleri aşağıdaki tabloda özetlenmiştir.

| Algılanabilir performans desenleri | Azure SQL Veritabanı | Azure SQL Yönetilen Örnek |
| :------------------- | ------------------- | ------------------- |
| [Kaynak sınırlarına ulaşma](intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Görüntülenen abonelikte kullanılabilir kaynakların (DTU), veritabanı çalışan iş parçacıklarının veya veritabanı oturum açma oturumlarının tüketimi, kaynak sınırlarına ulaştı. Bu, performansı etkiliyor. | CPU kaynaklarının tüketimi, kaynak sınırlarına ulaşıyor. Bu, veritabanı performansını etkiliyor. |
| [İş yükü artışı](intelligent-insights-troubleshoot-performance.md#workload-increase) | İş yükü artışı veya veritabanındaki iş yükünün sürekli birikmesi algılandı. Bu, performansı etkiliyor. | İş yükü artışı algılandı. Bu, veritabanı performansını etkiliyor. |
| [Bellek baskısı](intelligent-insights-troubleshoot-performance.md#memory-pressure) | İstenen bellek verdiği çalışanlar, istatistiksel olarak önemli miktarda süre için bellek ayırmaları beklemek ya da bellek isteyen çalışanların daha yüksek bir birikmesi sağlar. Bu, performansı etkiliyor. | Bellek izni isteyen çalışanlar, istatistiksel olarak önemli bir süre için bellek ayırmaları bekliyor. Bu, veritabanı performansını etkiliyor. |
| [Kilitleme](intelligent-insights-troubleshoot-performance.md#locking) | Performansı etkileyen aşırı veritabanı kilitlemesi algılandı. | Veritabanı performansını etkileyen aşırı veritabanı kilitleme algılandı. |
| [Artan MAXDOP](intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Maksimum paralellik derecesi seçeneği (MAXDOP), sorgu yürütme verimliliğini etkileyecek şekilde değiştirildi. Bu, performansı etkiliyor. | Maksimum paralellik derecesi seçeneği (MAXDOP), sorgu yürütme verimliliğini etkileyecek şekilde değiştirildi. Bu, performansı etkiliyor. |
| [Pagemandal çekişmesi](intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Birden çok iş parçacığı aynı anda, daha fazla bekleme süreleriyle sonuçlanan ve pagemanmaya neden olan bellek içi veri arabelleği sayfalarına erişmeyi deniyor. Bu, performansı etkiliyor. | Birden çok iş parçacığı aynı anda, daha fazla bekleme süreleriyle sonuçlanan ve pagemanmaya neden olan bellek içi veri arabelleği sayfalarına erişmeyi deniyor. Bu, performansı veritabanını etkiliyor. |
| [Eksik dizin](intelligent-insights-troubleshoot-performance.md#missing-index) | Performansı etkileyen eksik dizin algılandı. | Veritabanı performansını etkileyen eksik dizin algılandı. |
| [Yeni sorgu](intelligent-insights-troubleshoot-performance.md#new-query) | Genel performansı etkileyen yeni sorgu algılandı. | Genel veritabanı performansını etkileyen yeni sorgu algılandı. |
| [Daha fazla bekleme Istatistiği](intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Performansı etkileyen daha fazla veritabanı bekleme süresi algılandı. | Veritabanı performansını etkileyen daha fazla veritabanı bekleme süresi algılandı. |
| [TempDB çekişmesi](intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Birden çok iş parçacığı aynı TempDB kaynağına erişmeye çalışan bir performans sorununa neden oluyor. Bu, performansı etkiliyor. | Birden çok iş parçacığı aynı TempDB kaynağına erişmeye çalışan bir performans sorununa neden oluyor. Bu, veritabanı performansını etkiliyor. |
| [Elastik havuz DTU eksik](intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Esnek havuzdaki kullanılabilir eDTU 'ların eksikliği performansı etkiliyor. | VCore modelini kullandığından Azure SQL yönetilen örneği için kullanılamaz. |
| [Gerileme planı](intelligent-insights-troubleshoot-performance.md#plan-regression) | Yeni plan veya var olan bir planın iş yükünde değişiklik algılandı. Bu, performansı etkiliyor. | Yeni plan veya var olan bir planın iş yükünde değişiklik algılandı. Bu, veritabanı performansını etkiliyor. |
| [Veritabanı kapsamlı yapılandırma değeri değişikliği](intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Veritabanı performansını etkileyen veritabanında yapılandırma değişikliği algılandı. | Veritabanı performansını etkileyen veritabanında yapılandırma değişikliği algılandı. |
| [Yavaş istemci](intelligent-insights-troubleshoot-performance.md#slow-client) | Yavaş uygulama istemcisi, veritabanından alınan çıktıyı yeterince hızlı tüketmedi. Bu, performansı etkiliyor. | Yavaş uygulama istemcisi, veritabanından alınan çıktıyı yeterince hızlı tüketmedi. Bu, veritabanı performansını etkiliyor. |
| [Fiyatlandırma Katmanı düşürme](intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Fiyatlandırma Katmanı düşürme eylemi, kullanılabilir kaynakları düşürdü. Bu, performansı etkiliyor. | Fiyatlandırma Katmanı düşürme eylemi, kullanılabilir kaynakları düşürdü. Bu, veritabanı performansını etkiliyor. |

> [!TIP]
> Veritabanlarının sürekli performans iyileştirmesi için [otomatik ayarlamayı](automatic-tuning-overview.md)etkinleştirin. Bu yerleşik zeka özelliği, veritabanınızı sürekli olarak izler, dizinleri otomatik olarak ekler ve sorgu yürütme planı düzeltmelerini uygular.
>

Aşağıdaki bölümde, algılanabilir performans desenleri daha ayrıntılı olarak açıklanmaktadır.

## <a name="reaching-resource-limits"></a>Kaynak sınırlarına ulaşma

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, kullanılabilir kaynak sınırlarına, çalışan sınırlarına ve oturum sınırlarına ulaşarak ilgili performans sorunlarını birleştirir. Bu performans sorunu algılandıktan sonra, tanılama günlüğü ' nde bir açıklama alanı, performans sorununun kaynak, çalışan veya oturum limitleriyle ilgili olup olmadığını gösterir.

Azure SQL veritabanı 'nda yer alan kaynaklara genellikle [DTU](service-tiers-dtu.md) veya [Vcore](service-tiers-vcore.md) kaynakları ve Azure SQL yönetilen örneğindeki kaynaklar sanal çekirdek kaynakları olarak adlandırılır. Kaynak sınırlarına ulaşılmaya yönelik desenler, algılanan sorgu performansı performansında, ölçülen kaynak limitlerinin herhangi birine ulaşmasından kaynaklanan bir zaman tanınır.

Oturum sınırlarını kaynağı, veritabanında kullanılabilir eşzamanlı oturum açma sayısını belirtir. Veritabanlarına bağlı uygulamalar, kullanılabilir eşzamanlı oturum açma sayısı veritabanına ulaştığında bu performans deseninin tanınması gerekir. Uygulamalar bir veritabanında kullanılabilir olandan daha fazla oturum kullanmayı denediklerinde, sorgu performansı etkilenir.

Çalışan sınırlarına ulaşmak, kullanılabilir çalışanlar DTU veya vCore kullanımında sayılmadığından, kaynak sınırlarına ulaşmak için belirli bir durumdur. Bir veritabanındaki çalışan sınırlarına ulaşmak, kaynağa özgü bekleme sürelerinin artmasına neden olabilir ve bu da sorgu performansında azalma elde edilebilir.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, performans ve kaynak tüketim yüzdelerini etkileyen sorguların sorgu karmalarını çıktı. Bu bilgileri, veritabanı iş yükünüzü iyileştirmek için bir başlangıç noktası olarak kullanabilirsiniz. Özellikle, dizinler ekleyerek performans düşüşünü etkileyen sorguları iyileştirebilirsiniz. Ya da uygulamaları daha eşit bir iş yükü dağıtımı ile iyileştirebilirsiniz. İş yüklerini azaltamaz veya iyileştirmeler yapamazsanız, kullanılabilir kaynak miktarını artırmak için veritabanı aboneliğinizin fiyatlandırma katmanını artırmayı göz önünde bulundurun.

Kullanılabilir oturum sınırlarına ulaştıysanız, veritabanına yapılan oturum açma sayısını azaltarak uygulamalarınızı iyileştirebilirsiniz. Uygulamalarınızdaki oturum açma sayısını veritabanına düşürüyorsanız, veritabanı aboneliğinizin fiyatlandırma katmanını artırmayı düşünün. İsterseniz, daha dengeli bir iş yükü dağıtımı için veritabanınızı bölebilir ve birden çok veritabanına taşıyabilirsiniz.

Oturum sınırlarını çözme hakkında daha fazla öneri için bkz. [en fazla oturum açma limitleriyle başa çıkma](/archive/blogs/latam/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins). Sunucu ve abonelik düzeylerindeki sınırlar hakkında bilgi için bkz. [bir sunucudaki kaynak sınırlarına genel bakış](resource-limits-logical-server.md) .

## <a name="workload-increase"></a>İş yükü artışı

### <a name="what-is-happening"></a>Ne oluyor

Bu performans modelinde, iş yükünün artmasından kaynaklanan sorunlar veya daha önemli bir biçimde iş yükü için bir iş yükü vardır.

Bu algılama, birkaç ölçüm birleşimi aracılığıyla yapılır. Ölçülen temel ölçüm, son iş yükü temeliyle karşılaştırıldığında iş yükündeki artışı algılıyor. Diğer algılama biçimi, etkin çalışan iş parçacıklarında, sorgu performansını etkileyecek kadar büyük bir artışı ölçmeye dayanır.

Daha ciddi bir biçimde, iş yükü, bir veritabanının iş yükünü işleyememesi nedeniyle sürekli olarak satır içinde bulunabilir. Sonuç, iş yükü yığın oluşturma koşulunun bulunduğu sürekli büyüyen bir iş yükü boyutudur. Bu koşul nedeniyle, iş yükünün yürütme için beklediği zaman artar. Bu durum, en ciddi veritabanı performans sorunlarından birini temsil eder. Bu sorun, durdurulan çalışan iş parçacıklarının sayısında artışı izleyerek algılanır.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, yürütmesi arttığı sorguların sayısını ve iş yükünün en büyük katkısıyla sorgunun sorgu karmasını verir. Bu bilgileri iş yükünü iyileştirmek için bir başlangıç noktası olarak kullanabilirsiniz. İş yükü artışına en büyük katkıda bulunan olarak tanımlanan sorgu, özellikle başlangıç noktanız olarak faydalıdır.

İş yüklerini veritabanına daha eşit dağıtmayı düşünebilirsiniz. Dizinler ekleyerek performansı etkileyen sorguyu optimize etmeyi göz önünde bulundurun. Ayrıca, iş yükünüzü birden çok veritabanı arasında dağıtabilirsiniz. Bu çözümler mümkün değilse, kullanılabilir kaynak miktarını artırmak için veritabanı aboneliğinizin fiyatlandırma katmanını artırmayı göz önünde bulundurun.

## <a name="memory-pressure"></a>Bellek baskısı

### <a name="what-is-happening"></a>Ne oluyor

Bu performans stili, son yedi günlük performans temeliyle karşılaştırıldığında bellek basıncına neden olan geçerli veritabanı performansında düşme veya daha ciddi bir bellek yığın oluşturma koşulu olduğunu gösterir.

Bellek baskısı, bellek izni isteyen çok sayıda çalışan iş parçacığının bulunduğu bir performans koşulunu gösterir. Yüksek hacim, veritabanının istek yapan tüm çalışanlara etkin bir şekilde bellek ayıramadığı yüksek bellek kullanım koşuluna neden olur. Bu sorunun en yaygın nedenlerinden biri, veritabanının tek bir tarafında kullanılabilir bellek miktarı ile ilgilidir. Öte yandan, iş yükündeki artış çalışan iş parçacıklarında ve bellek basıncına neden olur.

Bellek baskısı daha ciddi bir biçimde bellek kümesi koşulundadır. Bu durum, belleği serbest bırakan sorgu sayısından daha yüksek sayıda çalışan iş parçacığının bellek izni istediğini gösterir. Veritabanı altyapısı talebi karşılamak için yeterince verimli bir şekilde bellek ayıramadığından, bellek gerektiren bu sayıda çalışan iş parçacığı de sürekli olarak artmış olabilir (yukarı çıkılıyor). Bellek kümesi durumu, en ciddi veritabanı performans sorunlarından birini temsil eder.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, bellek nesne deposu ayrıntılarını, yüksek bellek kullanımı ve ilgili zaman damgaları için en yüksek neden olarak işaretlenen büro (yani, çalışan iş parçacığı) ile birlikte verir. Sorun giderme için temel olarak bu bilgileri kullanabilirsiniz.

En yüksek bellek kullanımıyla birlikte bulunan kişilerle ilgili sorguları iyileştirebilirsiniz veya kaldırabilirsiniz. Ayrıca, kullanmayı planlamadığınız verileri sorgulediğinizden da emin olabilirsiniz. Her zaman sorgularda bir WHERE yan tümcesi kullanmak iyi bir uygulamadır. Ayrıca, verileri taramak yerine aramak için kümelenmemiş dizinler oluşturmanızı öneririz.

Ayrıca, birden çok veritabanı üzerinde iyileştirerek veya dağıtarak iş yükünü azaltabilirsiniz. Ya da iş yükünüzü birden çok veritabanı arasında dağıtabilirsiniz. Bu çözümler mümkün değilse, veritabanının fiyatlandırma katmanını artırarak veritabanı için kullanılabilir bellek kaynakları miktarını artırın.

Ek sorun giderme önerileri için bkz. [bellek desteği: çok sayıda adı olan, bilinmeyen SQL Server bellek tüketicisi](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994).

## <a name="locking"></a>Kilitleme

### <a name="what-is-happening"></a>Ne oluyor

Bu performans stili, son yedi günlük performans temeliyle karşılaştırıldığında aşırı veritabanı kilitlenmesinin algılandığı geçerli veritabanı performansında düşme olduğunu gösterir.

Modern RDBMS 'de kilitleme, mümkün olduğunda birden çok eş zamanlı çalışan ve paralel veritabanı işlemi çalıştırarak performansın kapladıkları çok iş parçacıklı sistemleri uygulamak için gereklidir. Bu bağlamda kilitleme, yalnızca tek bir işlemin, gerekli olan ve kaynaklara yönelik başka bir işlemle rekabet olmayan satırlara, sayfalara, tablolara ve dosyalara özel olarak erişebileceği yerleşik erişim mekanizmasına başvurur. Kaynakları kullanım için kilitleyen işlem ile işiniz bittiğinde, bu kaynaklardaki kilit serbest bırakılır ve diğer işlemlerin gerekli kaynaklara erişmesine izin verilir. Kilitleme hakkında daha fazla bilgi için bkz. [Lock for Database Engine](/previous-versions/sql/sql-server-2008-r2/ms190615(v=sql.105)).

SQL altyapısı tarafından yürütülen işlemler, kullanım için kilitlenen kaynaklara erişmek için uzun süreli süreler bekliyorsa, bu bekleme süresi iş yükü yürütme performansının yavaşlamasına neden olur.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, sorun giderme için temel olarak kullanabileceğiniz kilitleme ayrıntılarının çıktısını verir. Bildirilen engelleme sorgularını analiz edebilir, diğer bir deyişle, kilitleme performansı düşüşünü tanıtan sorgular ve bunları kaldırabilirsiniz. Bazı durumlarda, engelleme sorgularını iyileştirirken başarılı olabilirsiniz.

Sorunu azaltmanın en basit ve en güvenli yolu, işlem kısa tutulması ve en pahalı sorguların kilit parmak izini azaltmaktır. Büyük bir işlem toplu işlemini daha küçük işlemlere kesebilirsiniz. Sorgu kilit parmak izini mümkün olduğunca verimli hale getirerek iyi bir uygulamadır. Kilitlenme olasılığını artırdığından ve genel veritabanı performansını olumsuz yönde etkilediği için büyük taramalar sayısını azaltın. Kilitlemeye neden olan tanımlı sorgular için, tablo taramalarından kaçınmak üzere yeni dizinler oluşturabilir veya var olan dizine sütun ekleyebilirsiniz.

Daha fazla öneri için bkz.:
- [Azure SQL engelleme sorunlarını anlama ve çözme](understand-resolve-blocking.md)
- [SQL Server ' de kilit yükseltme nedeniyle oluşan engelleme sorunlarını çözme](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)

## <a name="increased-maxdop"></a>Artan MAXDOP

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, seçilen bir sorgu yürütme planının çok daha fazla paralelleştirildi olduğunu gösterir. Sorgu iyileştiricisi, mümkün olduğunca işlemleri hızlandırmak için sorguları paralel olarak yürüterek iş yükü performansını geliştirebilir. Bazı durumlarda, bir sorguyu işleyen paralel çalışanlar, daha az sayıda paralel çalışan ile aynı sorguyu yürütmeye ve hatta tek bir çalışan iş parçacığına kıyasla bazı durumlarda bile sonuçları eşitlemeye ve birleştirmeye kıyasla daha fazla zaman harcar.

Uzman sistem, geçerli veritabanı performansını temel dönemine göre analiz eder. Daha önce çalışan bir sorgunun daha yavaş çalışıp çalışmadığını belirler, çünkü sorgu yürütme planı, olması gerekenden daha paraleldir.

MAXDOP sunucu yapılandırma seçeneği, aynı sorguyu paralel olarak yürütmek için kaç CPU çekirdeğinin kullanılabileceğini denetlemek için kullanılır.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, yürütme süresi arttığı için sorgu karmalarıyla ilgili sorgu karmalarını çıktı. Günlük Ayrıca CXP bekleme süreleriyle ilgilidir. Bu süre, tek bir düzenleyici/düzenleyici iş parçacığının (iş parçacığı 0) sonuçları birleştirmeden ve devam etmeden önce diğer tüm iş parçacıklarının bitmesini beklediği süreyi temsil eder. Ayrıca, tanılama günlüğü kötü performanslı sorguların yürütmenin genel olarak beklediği bekleme sürelerini çıktı. Sorun giderme için temel olarak bu bilgileri kullanabilirsiniz.

İlk olarak, karmaşık sorguları iyileştirin veya kolaylaştırın. En iyi yöntem, uzun toplu işlerin daha küçük olanlara bölünmesinden iyidir. Ayrıca, sorgularınızı desteklemek için dizinler oluşturduğunuzdan emin olun. Zayıf performanslı olarak işaretlenen bir sorgu için en yüksek paralellik derecesini (MAXDOP) el ile uygulayabilirsiniz. Bu işlemi T-SQL kullanarak yapılandırmak için, bkz. [MAXDOP sunucu yapılandırma seçeneğini yapılandırma](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

MAXDOP sunucu yapılandırma seçeneğinin varsayılan değer olarak sıfır (0) olarak ayarlanması, veritabanının tek bir sorgu yürütmek için tüm kullanılabilir CPU çekirdeklerini paralel hale getirmek iş parçacıklarıyla kullanıp kullanbildiğini gösterir. MAXDOP 'yi bir (1) olarak ayarlamak, tek bir sorgu yürütmesi için yalnızca bir çekirdeğin kullanılabileceğini gösterir. Pratik koşullarda bu, paralellik özelliğinin kapalı olduğu anlamına gelir. Büyük/küçük harf durumuna, veritabanında kullanılabilir çekirdekler ve tanılama günlüğü bilgileri 'ne bağlı olarak, MAXDOP seçeneğini, bu durumda sorunu giderebilecek paralel sorgu yürütmesi için kullanılan çekirdek sayısına göre ayarlayabilirsiniz.

## <a name="pagelatch-contention"></a>Pagemandal çekişmesi

### <a name="what-is-happening"></a>Ne oluyor

Bu performans stili, son yedi günlük iş yükü temeliyle karşılaştırılan pagemandal çekişmesi nedeniyle geçerli veritabanı iş yükü performansında azalma olduğunu gösterir.

Cadılar, çoklu iş parçacığı oluşturmayı etkinleştirmek için kullanılan hafif eşitleme mekanizmalarına sahiptir. Dizinler, veri sayfaları ve diğer iç yapıları içeren bellek içi yapıların tutarlılığını garanti eder.

Birçok sayıda çim mevcuttur. Kolaylık sağlamak amacıyla, arabellek havuzundaki bellek içi sayfaları korumak için arabellek lamanları kullanılır. GÇ havuzları, henüz arabellek havuzuna yüklenmeyen sayfaları korumak için kullanılır. Arabellek havuzundaki bir sayfadan veriler her yazıldığında veya okurken, bir çalışan iş parçacığının önce sayfa için bir arabellek mandalı elde etmek gerekir. Bir çalışan iş parçacığı, bellek içi arabellek havuzunda zaten kullanılamayan bir sayfaya erişmeyi denediğinde, depolama alanından gerekli bilgileri yüklemek için bir GÇ isteği yapılır. Bu olay sırası, performans düşüşünün daha ciddi bir biçimini gösterir.

Aynı anda birden çok iş parçacığı aynı anda, sorgu yürütmeye yönelik daha fazla bekleme süresi sunan, aynı anda birden fazla iş parçacığı de eş zamanlı olarak bir arada bulunmaları gerçekleştirmeye çalıştığında, sayfada çekişme meydana gelir. Pagemanması GÇ çakışması durumunda verilere depolamadan erişilmesi gerektiğinde, bu bekleme süresi de daha büyük olur. Bu, iş yükü performansını önemli ölçüde etkileyebilir. Pagemandal çekişmesi, birden çok CPU sisteminde bulunan ve her biri için bekleyen iş parçacıklarının en yaygın senaryolarıdır.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, pagemandal çekişmesi ayrıntılarını verir. Sorun giderme için temel olarak bu bilgileri kullanabilirsiniz.

Bir pagemandalı bir iç denetim mekanizması olduğundan, bu, ne zaman kullanılacağını otomatik olarak belirler. Şema tasarımı dahil olmak üzere uygulama kararları, nesnelerin belirleyici davranışı nedeniyle pagemandal davranışını etkileyebilir.

Mandal çekişmesini işlemek için bir yöntem, sıralı bir dizin anahtarını sıralı olmayan bir anahtarla değiştirmek ve bir dizin aralığına eklemeleri eşit bir şekilde dağıtmaktır. Genellikle, dizindeki önde gelen bir sütun iş yükünü orantılı olarak dağıtır. Dikkate alınması gereken başka bir yöntem de tablo bölümleniyor. Bölümlenmiş bir tabloda hesaplanan sütunlu bir karma bölümlendirme şeması oluşturma, aşırı mandal çekişmesini azaltmaya yönelik yaygın bir yaklaşımdır. Pagemanma GÇ çakışması durumunda, dizinler ile tanışın bu performans sorununu azaltmaya yardımcı olur.

Daha fazla bilgi için bkz. SQL Server (PDF indirme) [üzerindeki mandal çekişmesini tanılama ve çözme](http://databaser.net/moniwiki/pds/PerformanceTuning/SQLServerLatchContention.pdf) .

## <a name="missing-index"></a>Eksik dizin

### <a name="what-is-happening"></a>Ne oluyor

Bu performans stili, eksik bir dizin nedeniyle, son yedi günlük taban çizgisi ile karşılaştırıldığında geçerli veritabanı iş yükü performansında azalma olduğunu gösterir.

Sorguların performansını hızlandırmak için bir dizin kullanılır. Ziyaret edilen veya taranan veri kümesi sayfalarının sayısını azaltarak tablo verilerine hızlı erişim sağlar.

Performans düşüşüne neden olan belirli sorgular, bu algılama ile, dizin oluşturma performansı açısından yararlı olacak şekilde tanımlanır.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, iş yükü performansını etkilemek için tanımlanan sorgular için sorgu karmalarını çıktı. Bu sorgular için dizinler oluşturabilirsiniz. Ayrıca, gerekli değillerse bu sorguları iyileştirebilirsiniz veya kaldırabilirsiniz. İyi bir performans uygulaması, kullanmazsınız verileri sorgulamadan kaçınmaktır.

> [!TIP]
> Yerleşik zekanın veritabanlarınız için en iyi performanslı dizinleri otomatik olarak yönetebileceğini biliyoruz mi?
>
> Sürekli performans iyileştirmesi için [otomatik ayarlamayı](automatic-tuning-overview.md)etkinleştirmenizi öneririz. Bu benzersiz yerleşik zeka özelliği, veritabanınızı sürekli olarak izler ve veritabanlarınızın dizinlerini otomatik olarak oluşturur.
>

## <a name="new-query"></a>Yeni sorgu

### <a name="what-is-happening"></a>Ne oluyor

Bu performans stili, çok düşük bir performans olduğunu ve yedi günlük performans temeline göre iş yükü performansını etkileyen yeni bir sorgu algılandığını gösterir.

İyi performanslı bir sorgu yazmak bazen zorlayıcı bir görev olabilir. Sorgu yazma hakkında daha fazla bilgi için bkz. [SQL sorguları yazma](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)). Var olan sorgu performansını iyileştirmek için bkz. [sorgu ayarlama](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, sorgu karmaları da dahil olmak üzere en fazla iki yeni CPU kullanan sorguya bilgi verir. Algılanan sorgu iş yükü performansını etkilediği için sorgunuzu iyileştirebilmenizi sağlayabilirsiniz. Yalnızca kullanmanız gereken verileri almak iyi bir uygulamadır. Ayrıca, WHERE yan tümcesi ile sorguların kullanılması önerilir. Ayrıca, karmaşık sorguları basitleştirmenizi ve bunları daha küçük sorgulara kesmeniz önerilir. Büyük toplu iş sorgularını daha küçük toplu iş sorgularına bölmek çok iyi bir uygulamadır. Yeni sorgular için dizin oluşturma, genellikle bu performans sorununu hafifletmek için iyi bir uygulamadır.

Azure SQL veritabanı 'nda [sorgu performansı içgörüleri](query-performance-insight-use.md)kullanmayı düşünün.

## <a name="increased-wait-statistic"></a>Daha fazla bekleme istatistiği

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, son yedi günlük iş yükü temeliyle karşılaştırıldığında kötü performanslı sorguların tanımlandığı bir iş yükü performans düşüşünü gösterir.

Bu durumda, sistem diğer standart algılanabilir performans kategorileri altındaki zayıf performanslı sorguları sınıflandırıp, ancak gerileme karşı bekleme istatistiğini algıladı. Bu nedenle, bu dosyaları *artan bekleme istatistiği* olan sorgular olarak değerlendirir; burada gerileme ilgili bekleme istatistiği de sunulur.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, etkilenen sorguların daha fazla bekleme süresi ayrıntıları ve sorgu karmaları hakkında bilgi verir.

Sistem kötü performanslı sorguların kök nedenini başarıyla belirleyemediği için tanılama bilgileri el ile sorun giderme için iyi bir başlangıç noktasıdır. Bu sorguların performansını iyileştirebilirsiniz. Yalnızca kullanmanız gereken verileri getirmek ve karmaşık sorguları basitleştirmek ve daha küçük olanlara bölmek için iyi bir uygulamadır.

Sorgu performansını iyileştirme hakkında daha fazla bilgi için bkz. [sorgu ayarlama](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

## <a name="tempdb-contention"></a>TempDB çekişmesi

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans modelinde, tempDB kaynaklarına erişmeye çalışan iş parçacıklarının performans koşullarını içeren bir veritabanı performans koşulu gösterilir. (Bu durum GÇ ile ilgili değildir.) Bu performans sorunu için tipik senaryo, küçük tempDB tablolarının tümünü oluşturan, kullanan ve sonra bırakan yüzlerce eşzamanlı sorgu olur. Sistem, son yedi günlük performans temeline kıyasla veritabanı performansını etkilemek için aynı tempDB tablolarının kullanıldığı eşzamanlı sorguların sayısının yeterince istatistiksel anlamasıyla arttığını algıladı.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğünde tempDB çekişme ayrıntıları çıktı. Sorun giderme için başlangıç noktası olarak bu bilgileri kullanabilirsiniz. Bu tür çekişmeyi hafifetmek ve genel iş yükünün verimini artırmak için kullanabileceğiniz iki şey vardır: geçici tabloları kullanmayı durdurabilirsiniz. Bellek için iyileştirilmiş tabloları da kullanabilirsiniz.

Daha fazla bilgi için bkz. [bellek için iyileştirilmiş tablolara giriş](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables).

## <a name="elastic-pool-dtu-shortage"></a>Elastik havuz DTU eksik

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, son yedi günlük taban çizgisiyle karşılaştırıldığında geçerli veritabanı iş yükü performansındaki düşüşünü gösterir. Bunun nedeni, aboneliğinizin elastik havuzundaki kullanılabilir DTU 'ların yetersizliğidir.

[Azure elastik havuz kaynakları](elastic-pool-overview.md) , ölçekleme amacıyla birden çok veritabanı arasında paylaşılan kullanılabilir kaynaklar havuzu olarak kullanılır. Elastik havuzunuzdaki kullanılabilir eDTU kaynakları, havuzdaki tüm veritabanlarını desteklemek için yeterince büyük değilse, sistem tarafından esnek bir havuz DTU performans sorunu algılanır.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, elastik havuzda bilgi verir, en üst DTU kullanan veritabanlarını listeler ve havuzun en iyi veritabanı tarafından kullanılan DTU yüzdesini sağlar.

Bu performans koşulu, elastik havuzda aynı eDTU havuzunu kullanan birden çok veritabanıyla ilişkili olduğundan, sorun giderme adımları en üst DTU kullanan veritabanlarına odaklanmaktadır. En iyi kullanan veritabanlarında iş yükünü azaltabilirsiniz. bu veritabanları üzerinde en üst düzey sorguların en iyi duruma getirilmesi de dahildir. Ayrıca, kullanmadığınız verileri sorgulamakta de emin olabilirsiniz. Diğer bir yaklaşım ise en üst DTU kullanan veritabanlarını kullanarak ve iş yükünü birden çok veritabanı arasında yeniden dağıtan uygulamaları optimize etmek.

En üst DTU kullanan veritabanlarındaki geçerli iş yükünün azaltılması ve iyileştirmesi mümkün değilse, esnek havuz fiyatlandırma katmanınızı artırmayı göz önünde bulundurun. Bu artış, esnek havuzdaki kullanılabilir DTU 'ların artışına neden olur.

## <a name="plan-regression"></a>Gerileme planı

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, veritabanının bir alt sorgu yürütme planını kullanacağını belirten bir koşul gösterir. Alt plan planı genellikle daha fazla sorgu yürütmeye neden olur ve bu da geçerli ve diğer sorguların uzun sürelerine yol açar.

Veritabanı altyapısı, sorgu yürütme planını bir sorgu yürütmeye en az maliyetle belirler. Sorguların ve iş yüklerinin türü değiştikçe, bazı durumlarda mevcut planlar artık etkili değildir veya veritabanı altyapısı iyi bir değerlendirme yapmamaktadır. Düzeltme olarak sorgu yürütme planları el ile zorunlu olabilir.

Bu algılanabilir performans stili, plan regresyonun üç farklı durumunu birleştirir: yeni plan gerileme, eski plan gerileme ve mevcut planlar iş yükünü değiştirdi. Oluşan belirli plan gerileme türü, tanılama günlüğündeki *Ayrıntılar* özelliğinde verilmiştir.

Yeni plan gerileme koşulu, veritabanı altyapısının eski plan kadar verimli olmayan yeni bir sorgu yürütme planı yürütmeye başladığı bir duruma başvurur. Eski plan gerileme koşulu, veritabanı altyapısı yeni plan kadar etkili olmayan eski plana yeni, daha verimli bir plan kullanarak geçiş yaptığında durum anlamına gelir. Mevcut planlar değişti iş yükü gerileme, eski ve yeni planların sürekli olarak alternatif olduğu, Bakiyenin düşük performanslı plana göre daha fazla ilerlemelerine karşılık gelir.

Plan gerilemeleri hakkında daha fazla bilgi için bkz. [SQL Server plan gerileme nedir?](/archive/blogs/sqlserverstorageengine/what-is-plan-regression-in-sql-server).

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü sorgu karmaları, iyi plan KIMLIĞI, hatalı plan KIMLIĞI ve sorgu kimliklerini çıktı. Sorun giderme için temel olarak bu bilgileri kullanabilirsiniz.

Belirtilen sorgu karmaları ile tanımlayabilmeniz için, belirli sorgularınız için hangi planın daha iyi performans gösterdiğini çözümleyebilirsiniz. Sorgularınız için hangi planın daha iyi çalıştığını belirledikten sonra, el ile zorlayabilirsiniz.

Daha fazla bilgi için bkz. [SQL Server plan gerilemeleri nasıl engeller](/archive/blogs/sqlserverstorageengine/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions).

> [!TIP]
> Yerleşik zeka özelliğinin veritabanınız için en iyi performanslı sorgu yürütme planlarını otomatik olarak yönetebileceğini biliyoruz mi?
>
> Sürekli performans iyileştirmesi için [otomatik ayarlamayı](automatic-tuning-overview.md)etkinleştirmenizi öneririz. Bu yerleşik zeka özelliği, veritabanınızı sürekli olarak izler ve veritabanınız için en iyi şekilde yürütülen sorgu yürütme planlarını oluşturur.

## <a name="database-scoped-configuration-value-change"></a>Veritabanı kapsamlı yapılandırma değeri değişikliği

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans modelinde, veritabanı kapsamlı yapılandırmadaki bir değişikliğin, son yedi günlük veritabanı iş yükü davranışına kıyasla algılanan performans regresyonı olduğunu gösterir. Bu düzende, veritabanı kapsamlı yapılandırmada yapılan son değişikliğin veritabanı performanslarınız için yararlı görünmediğini belirtir.

Veritabanı kapsamlı yapılandırma değişiklikleri, her bir veritabanı için ayarlanabilir. Bu yapılandırma, veritabanınızın bireysel performansını iyileştirmek için büyük/küçük harf olarak kullanılır. Her veritabanı için aşağıdaki seçenekler yapılandırılabilir: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES ve CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, kısa süre önce yapılan ve önceki yedi günlük iş yükü davranışına kıyasla performans düşüşüne neden olan veritabanı kapsamlı yapılandırma değişikliklerini çıktı. Önceki değerlere yapılandırma değişikliklerini geri döndürebilirsiniz. Ayrıca, istenen performans düzeyine ulaşılana kadar değeri değere göre ayarlayabilirsiniz. Veritabanı kapsamı yapılandırma değerlerini, tatmin edici performansa sahip benzer bir veritabanından kopyalayabilirsiniz. Performansla ilgili sorun gidermek için varsayılan değerlere dönün ve bu taban çizgisinden başlayarak ince ayar yapmayı deneyin.

Yapılandırmayı değiştirirken veritabanı kapsamlı yapılandırma ve T-SQL söz dizimini iyileştirme hakkında daha fazla bilgi için bkz. [alter database-kapsamlıdır Configuration (Transact-SQL)](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="slow-client"></a>Yavaş istemci

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, veritabanı sonuçları gönderdiği veritabanını kullanan istemcinin veritabanından gelen çıktıyı tüketmediğini bir koşul gösterir. Veritabanı, yürütülen sorguların sonuçlarını bir arabellekte depolamadığından, devam etmeden önce istemcinin iletilen sorgu çıktılarını kullanmasını bekler. Bu durum Ayrıca, veritabanından alınan istemciye çıkış aktarmak için yeterince hızlı olmayan bir ağla ilgili olabilir.

Bu koşul yalnızca, son yedi günlük veritabanı iş yükü davranışına kıyasla bir performans gerileme algılanırsa oluşturulur. Bu performans sorunu yalnızca, önceki performans davranışına kıyasla istatistiksel ölçüde önemli bir performans düşüşü oluşursa algılanır.

### <a name="troubleshooting"></a>Sorun giderme

Bu algılanabilir performans modelinde bir istemci tarafı koşulu gösterilir. İstemci tarafı uygulama veya istemci tarafı ağında sorun giderme gereklidir. Tanılama günlüğü, son iki saat içinde istemcinin bunları kullanmasını bekleyen sorgu karmalarını ve bekleme sürelerini çıktı olarak verir. Sorun giderme için temel olarak bu bilgileri kullanabilirsiniz.

Bu sorguların tüketimi için uygulamanızın performansını iyileştirebilirsiniz. Olası ağ gecikme sorunlarını da düşünebilirsiniz. Performans düşüşü sorunu son yedi günlük performans temelindeki değişikliği temel aldığı için, en son uygulamanın veya ağ koşulunun bu performans gerileme olayına neden olduğunu araştırabilirsiniz.

## <a name="pricing-tier-downgrade"></a>Fiyatlandırma Katmanı düşürme

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, veritabanı aboneliğinizin fiyatlandırma katmanının indirgendiğini belirten bir koşul gösterir. Veritabanı için kullanılabilir kaynak azaltma (DTU 'Lar) nedeniyle, sistem son yedi günlük temel ile karşılaştırıldığında geçerli veritabanı performansında bir bırakma algıladı.

Ayrıca, veritabanı aboneliğinizin fiyatlandırma katmanının indirgendiğini ve kısa bir süre içinde daha yüksek bir katmana yükseltildiğini belirten bir koşul olabilir. Bu geçici performans düşüşünü algılama, tanılama günlüğünün Ayrıntılar bölümünde fiyatlandırma katmanı düşürme ve yükseltme olarak gerçekleştirilir.

### <a name="troubleshooting"></a>Sorun giderme

Fiyatlandırma katmanınızı azaldıysanız ve bu nedenle kullanılabilir DTU 'Lar varsa ve performansa memnun kaldıysanız, yapmanız gereken bir şey yoktur. Fiyatlandırma katmanınızı azaldıysanız ve veritabanı performanstan memnun kaldıysanız, veritabanı iş yüklerinizi azaltın veya fiyatlandırma katmanını daha yüksek bir düzeye artırmayı düşünün.

## <a name="recommended-troubleshooting-flow"></a>Önerilen sorun giderme akışı

 Akıllı İçgörüler kullanarak performans sorunlarını gidermek için önerilen bir yaklaşım için akış çizelgesini izleyin.

Azure SQL Analytics 'e giderek Azure portal üzerinden Akıllı İçgörüler erişin. Gelen performans uyarısını bulmayı deneyin ve seçin. Algılamalar sayfasında neler olduğunu belirler. Sorunun, sorgu metninin, sorgu süresi eğilimlerini ve olay evriminin belirtilen kök neden analizini gözlemleyin. Sorunu gidermek için Akıllı İçgörüler öneriyi kullanarak sorunu çözmeyi deneyin.

[![Akış grafiği sorunlarını giderme](./media/intelligent-insights-troubleshoot-performance/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> PDF sürümünü indirmek için akış çizelgesini seçin.

Akıllı İçgörüler, genellikle performans sorununun kök neden analizini gerçekleştirmek için bir saat gerektirir. Sorununuzu Akıllı İçgörüler bulamazsanız ve sizin için önemli olduğundan, performans sorununun kök nedenini el ile tanımlamak için Query Store 'u kullanın. (Genellikle, bu sorunlar bir saatten daha eski.) Daha fazla bilgi için bkz. [sorgu deposunu kullanarak performansı izleme](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Sonraki adımlar

- [Akıllı içgörüler](intelligent-insights-overview.md) kavramları öğrenin.
- [Akıllı içgörüler Performans Tanılama günlüğünü](intelligent-insights-use-diagnostics-log.md)kullanın.
- [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md)kullanarak izleyin.
- [Azure kaynaklarınızdan günlük verilerini toplamayı ve](../../azure-monitor/essentials/platform-logs-overview.md)kullanmayı öğrenin.