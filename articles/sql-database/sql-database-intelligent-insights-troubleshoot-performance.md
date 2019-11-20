---
title: Akıllı İçgörüler performans sorunlarını giderme
description: Akıllı İçgörüler, Azure SQL veritabanı performans sorunlarını gidermenize yardımcı olur.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: b6ec1952d730b6515032572def65806a1ccbc0b2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810362"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Akıllı İçgörüler Azure SQL veritabanı performans sorunlarını giderme

Bu sayfada Azure SQL veritabanı hakkında bilgi ve [akıllı içgörüler](sql-database-intelligent-insights.md) veritabanı performansı tanılama günlüğü aracılığıyla algılanan yönetilen örnek performans sorunları sağlanmaktadır. Tanılama günlüğü telemetrisi, [Azure izleyici günlüklerine](../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md), [Azure depolama](sql-database-metrics-diag-logging.md#stream-into-storage)'ya veya özel DevOps uyarısı ve raporlama özelliklerine yönelik bir üçüncü taraf çözümüne akışı yapılabilir.

> [!NOTE]
> Akıllı İçgörüler kullanan hızlı bir SQL veritabanı performans sorunlarını giderme kılavuzu için, bu belgede [Önerilen sorun giderme akışı](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) Akış Çizelgesine bakın.
>

## <a name="detectable-database-performance-patterns"></a>Algılanabilir veritabanı performans desenleri

Akıllı İçgörüler, sorgu yürütme bekleme süreleri, hatalar veya zaman aşımları temelinde SQL veritabanı ve yönetilen örnek veritabanları ile ilgili performans sorunlarını otomatik olarak algılar. Tanılama günlüğüne algılanan performans desenlerine çıkış verir. Algılanabilir performans desenleri aşağıdaki tabloda özetlenmiştir.

| Algılanabilir performans desenleri | Azure SQL veritabanı ve elastik havuzlar için açıklama | Yönetilen örnekteki veritabanlarının açıklaması |
| :------------------- | ------------------- | ------------------- |
| [Kaynak sınırlarına ulaşma](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Görüntülenen abonelikte kullanılabilir kaynakların (DTU), veritabanı çalışan iş parçacıklarının veya veritabanı oturum açma oturumlarının tüketimi sınırlara ulaştı. Bu, SQL veritabanı performansını etkiliyor. | CPU kaynaklarının tüketimi, yönetilen örnek sınırlarına ulaşıyor. Bu, veritabanı performansını etkiliyor. |
| [İş yükü artışı](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | İş yükü artışı veya veritabanındaki iş yükünün sürekli birikmesi algılandı. Bu, SQL veritabanı performansını etkiliyor. | İş yükü artışı algılandı. Bu, veritabanı performansını etkiliyor. |
| [Bellek baskısı](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | İstenen bellek verdiği çalışanların, istatistiksel olarak önemli miktarda süre için bellek ayırmaları beklemesi gerekir. Ya da gerekli bellek izni veren çalışanların daha yüksek bir birikmesi vardır. Bu, SQL veritabanı performansını etkiliyor. | Bellek izni isteyen çalışanlar, istatistiksel olarak önemli bir süre için bellek ayırmaları bekliyor. Bu, veritabanı performansını etkiliyor. |
| [Lemeye](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | SQL veritabanı performansını etkileyen aşırı veritabanı kilitleme algılandı. | Veritabanı performansını etkileyen aşırı veritabanı kilitleme algılandı. |
| [Artan MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Maksimum paralellik derecesi seçeneği (MAXDOP), sorgu yürütme verimliliğini etkileyecek şekilde değiştirildi. Bu, SQL veritabanı performansını etkiliyor. | Maksimum paralellik derecesi seçeneği (MAXDOP), sorgu yürütme verimliliğini etkileyecek şekilde değiştirildi. Bu, veritabanı performansını etkiliyor. |
| [Pagemandal çekişmesi](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Birden çok iş parçacığı aynı anda, daha fazla bekleme süreleriyle sonuçlanan ve pagemanmaya neden olan bellek içi veri arabelleği sayfalarına erişmeyi deniyor. Bu, SQL veritabanı performansını etkiliyor. | Birden çok iş parçacığı aynı anda, daha fazla bekleme süreleriyle sonuçlanan ve pagemanmaya neden olan bellek içi veri arabelleği sayfalarına erişmeyi deniyor. Bu, performansı veritabanını etkiliyor. |
| [Eksik dizin](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | SQL veritabanı performansını etkileyen eksik dizin algılandı. | Veritabanı performansını etkileyen eksik dizin algılandı. |
| [Yeni sorgu](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Genel SQL veritabanı performansını etkileyen yeni sorgu algılandı. | Genel veritabanı performansını etkileyen yeni sorgu algılandı. |
| [Daha fazla bekleme Istatistiği](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | SQL veritabanı performansını etkileyen daha fazla veritabanı bekleme süresi algılandı. | Veritabanı performansını etkileyen daha fazla veritabanı bekleme süresi algılandı. |
| [TempDB çekişmesi](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Birden çok iş parçacığı aynı TempDB kaynağına erişmeye çalışan bir performans sorununa neden oluyor. Bu, SQL veritabanı performansını etkiliyor. | Birden çok iş parçacığı aynı TempDB kaynağına erişmeye çalışan bir performans sorununa neden oluyor. Bu, veritabanı performansını etkiliyor. |
| [Elastik havuz DTU eksik](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Elastik havuzda kullanılabilir eDTU 'ların eksik olması SQL veritabanı performansını etkiliyor. | Sanal çekirdek modeli kullandığından, yönetilen örnek için kullanılamaz. |
| [Gerileme planı](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Yeni plan veya var olan bir planın iş yükünde değişiklik algılandı. Bu, SQL veritabanı performansını etkiliyor. | Yeni plan veya var olan bir planın iş yükünde değişiklik algılandı. Bu, veritabanı performansını etkiliyor. |
| [Veritabanı kapsamlı yapılandırma değeri değişikliği](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | SQL veritabanında yapılandırma değişikliği, veritabanı performansını etkileyerek algılandı. | Veritabanı performansını etkileyen veritabanında yapılandırma değişikliği algılandı. |
| [Yavaş istemci](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Yavaş uygulama istemcisi, veritabanından alınan çıktıyı yeterince hızlı tüketmedi. Bu, SQL veritabanı performansını etkiliyor. | Yavaş uygulama istemcisi, veritabanından alınan çıktıyı yeterince hızlı tüketmedi. Bu, veritabanı performansını etkiliyor. |
| [Fiyatlandırma Katmanı düşürme](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Fiyatlandırma Katmanı düşürme eylemi, kullanılabilir kaynakları düşürdü. Bu, SQL veritabanı performansını etkiliyor. | Fiyatlandırma Katmanı düşürme eylemi, kullanılabilir kaynakları düşürdü. Bu, veritabanı performansını etkiliyor. |

> [!TIP]
> SQL Database 'in sürekli performans iyileştirmesi için [Azure SQL veritabanı otomatik ayarlamayı](sql-database-automatic-tuning.md)etkinleştirin. SQL veritabanı yerleşik zeka 'nın bu benzersiz özelliği, SQL veritabanınızı sürekli olarak izler, dizinleri otomatik olarak ekler ve sorgu yürütme planı düzeltmelerini uygular.
>

Aşağıdaki bölümde, algılanabilir performans desenleri daha ayrıntılı olarak açıklanmaktadır.

## <a name="reaching-resource-limits"></a>Kaynak sınırlarına ulaşma

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, kullanılabilir kaynak sınırlarına, çalışan sınırlarına ve oturum sınırlarına ulaşarak ilgili performans sorunlarını birleştirir. Bu performans sorunu algılandıktan sonra, tanılama günlüğü ' nde bir açıklama alanı, performans sorununun kaynak, çalışan veya oturum limitleriyle ilgili olup olmadığını gösterir.

SQL veritabanı 'ndaki kaynaklara genellikle [DTU](sql-database-what-is-a-dtu.md) veya [Vcore](sql-database-service-tiers-vcore.md) kaynakları denir. Kaynak sınırlarına ulaşılmaya yönelik desenler, algılanan sorgu performansı performansında, ölçülen kaynak limitlerinin herhangi birine ulaşmasından kaynaklanan bir zaman tanınır.

Oturum sınırlarını kaynağı, SQL veritabanında kullanılabilir eşzamanlı oturum açma sayısını belirtir. Bu performans deseninin, SQL veritabanlarına bağlı olan uygulamalar, veritabanında kullanılabilir eşzamanlı oturum açma sayısına ulaştığında tanınır. Uygulamalar bir veritabanında kullanılabilir olandan daha fazla oturum kullanmayı denediklerinde, sorgu performansı etkilenir.

Çalışan sınırlarına ulaşmak, kullanılabilir çalışanlar DTU veya vCore kullanımında sayılmadığından, kaynak sınırlarına ulaşmak için belirli bir durumdur. Bir veritabanındaki çalışan sınırlarına ulaşmak, kaynağa özgü bekleme sürelerinin artmasına neden olabilir ve bu da sorgu performansında azalma elde edilebilir.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, performans ve kaynak tüketim yüzdelerini etkileyen sorguların sorgu karmalarını çıktı. Bu bilgileri, veritabanı iş yükünüzü iyileştirmek için bir başlangıç noktası olarak kullanabilirsiniz. Özellikle, dizinler ekleyerek performans düşüşünü etkileyen sorguları iyileştirebilirsiniz. Ya da uygulamaları daha eşit bir iş yükü dağıtımı ile iyileştirebilirsiniz. İş yüklerini azaltamaz veya iyileştirmeler yapamazsanız, kullanılabilir kaynak miktarını artırmak için SQL veritabanı aboneliğinizin fiyatlandırma katmanını artırmayı göz önünde bulundurun.

Kullanılabilir oturum sınırlarına ulaştıysanız, veritabanına yapılan oturum açma sayısını azaltarak uygulamalarınızı iyileştirebilirsiniz. Uygulamalarınızdaki oturum açma sayısını veritabanına düşürüyorsanız, veritabanınızın fiyatlandırma katmanını artırmayı göz önünde bulundurun. İsterseniz, daha dengeli bir iş yükü dağıtımı için veritabanınızı bölebilir ve birden çok veritabanına taşıyabilirsiniz.

Oturum sınırlarını çözme hakkında daha fazla öneri için bkz. [SQL veritabanı en fazla oturum açma sınırlarına nasıl yöneliktir](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Sunucu ve abonelik düzeylerindeki sınırlar hakkında bilgi için bkz. [SQL veritabanı sunucusundaki kaynak sınırlarına genel bakış](sql-database-resource-limits-database-server.md) .

## <a name="workload-increase"></a>İş yükü artışı

### <a name="what-is-happening"></a>Ne oluyor

Bu performans modelinde, iş yükünün artmasından kaynaklanan sorunlar veya daha önemli bir biçimde iş yükü için bir iş yükü vardır.

Bu algılama, birkaç ölçüm birleşimi aracılığıyla yapılır. Ölçülen temel ölçüm, son iş yükü temeliyle karşılaştırıldığında iş yükündeki artışı algılıyor. Diğer algılama biçimi, etkin çalışan iş parçacıklarında, sorgu performansını etkileyecek kadar büyük bir artışı ölçmeye dayanır.

Daha ciddi bir biçimde, iş yükü SQL veritabanının iş yükünü işleyememesi nedeniyle sürekli olarak oluşabilir. Sonuç, iş yükü yığın oluşturma koşulunun bulunduğu sürekli büyüyen bir iş yükü boyutudur. Bu koşul nedeniyle, iş yükünün yürütme için beklediği zaman artar. Bu durum, en ciddi veritabanı performans sorunlarından birini temsil eder. Bu sorun, durdurulan çalışan iş parçacıklarının sayısında artışı izleyerek algılanır. 

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, yürütmesi arttığı sorguların sayısını ve iş yükünün en büyük katkısıyla sorgunun sorgu karmasını verir. Bu bilgileri iş yükünü iyileştirmek için bir başlangıç noktası olarak kullanabilirsiniz. İş yükü artışına en büyük katkıda bulunan olarak tanımlanan sorgu, özellikle başlangıç noktanız olarak faydalıdır.

İş yüklerini veritabanına daha eşit dağıtmayı düşünebilirsiniz. Dizinler ekleyerek performansı etkileyen sorguyu optimize etmeyi göz önünde bulundurun. Ayrıca, iş yükünüzü birden çok veritabanı arasında dağıtabilirsiniz. Bu çözümler mümkün değilse, kullanılabilir kaynak miktarını artırmak için SQL veritabanı aboneliğinizin fiyatlandırma katmanını artırmayı göz önünde bulundurun.

## <a name="memory-pressure"></a>Bellek baskısı

### <a name="what-is-happening"></a>Ne oluyor

Bu performans stili, son yedi günlük performans temeliyle karşılaştırıldığında bellek basıncına neden olan geçerli veritabanı performansında düşme veya daha ciddi bir bellek yığın oluşturma koşulu olduğunu gösterir.

Bellek baskısı, SQL veritabanında bellek izni isteyen çok sayıda çalışan iş parçacığının bulunduğu bir performans koşulunu gösterir. Yüksek hacim, SQL veritabanının istek yapan tüm çalışanlara etkin bir şekilde bellek ayıramadığı yüksek bellek kullanım koşuluna neden olur. Bu sorunun en yaygın nedenlerinden biri, SQL veritabanı için tek bir taraftaki kullanılabilir bellek miktarıyla ilgilidir. Öte yandan, iş yükündeki artış çalışan iş parçacıklarında ve bellek basıncına neden olur.

Bellek baskısı daha ciddi bir biçimde bellek kümesi koşulundadır. Bu durum, belleği serbest bırakan sorgu sayısından daha yüksek sayıda çalışan iş parçacığının bellek izni istediğini gösterir. SQL veritabanı altyapısı talebi karşılayacak kadar bellek ayıramadığından, bellek istekleri isteyen bu sayıda çalışan iş parçacığı de sürekli olarak artmış olabilir (yukarı çıkılıyor). Bellek kümesi durumu, en ciddi veritabanı performans sorunlarından birini temsil eder.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, bellek nesne deposu ayrıntılarını, yüksek bellek kullanımı ve ilgili zaman damgaları için en yüksek neden olarak işaretlenen büro (yani, çalışan iş parçacığı) ile birlikte verir. Sorun giderme için temel olarak bu bilgileri kullanabilirsiniz. 

En yüksek bellek kullanımıyla birlikte bulunan kişilerle ilgili sorguları iyileştirebilirsiniz veya kaldırabilirsiniz. Ayrıca, kullanmayı planlamadığınız verileri sorgulediğinizden da emin olabilirsiniz. Her zaman sorgularda bir WHERE yan tümcesi kullanmak iyi bir uygulamadır. Ayrıca, verileri taramak yerine aramak için kümelenmemiş dizinler oluşturmanızı öneririz.

Ayrıca, birden çok veritabanı üzerinde iyileştirerek veya dağıtarak iş yükünü azaltabilirsiniz. Ya da iş yükünüzü birden çok veritabanı arasında dağıtabilirsiniz. Bu çözümler mümkün değilse, veritabanı için kullanılabilir bellek kaynakları miktarını artırmak için SQL veritabanı aboneliğinizin fiyatlandırma katmanını artırmayı göz önünde bulundurun.

Ek sorun giderme önerileri için bkz. [bellek desteği: çok sayıda adı olan, bilinmeyen SQL Server bellek tüketicisi](https://blogs.msdn.microsoft.com/sqlmeditation/20../../memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Lemeye

### <a name="what-is-happening"></a>Ne oluyor

Bu performans stili, son yedi günlük performans temeliyle karşılaştırıldığında aşırı veritabanı kilitlenmesinin algılandığı geçerli veritabanı performansında düşme olduğunu gösterir. 

Modern RDBMS 'de kilitleme, mümkün olduğunda birden çok eş zamanlı çalışan ve paralel veritabanı işlemi çalıştırarak performansın kapladıkları çok iş parçacıklı sistemleri uygulamak için gereklidir. Bu bağlamda kilitleme, yalnızca tek bir işlemin, gerekli olan ve kaynaklara yönelik başka bir işlemle rekabet olmayan satırlara, sayfalara, tablolara ve dosyalara özel olarak erişebileceği yerleşik erişim mekanizmasına başvurur. Kaynakları kullanım için kilitleyen işlem ile işiniz bittiğinde, bu kaynaklardaki kilit serbest bırakılır ve diğer işlemlerin gerekli kaynaklara erişmesine izin verilir. Kilitleme hakkında daha fazla bilgi için bkz. [Lock for Database Engine](https://msdn.microsoft.com/library/ms190615.aspx).

SQL altyapısı tarafından yürütülen işlemler, kullanım için kilitlenen kaynaklara erişmek için uzun süreli süreler bekliyorsa, bu bekleme süresi iş yükü yürütme performansının yavaşlamasına neden olur. 

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, sorun giderme için temel olarak kullanabileceğiniz kilitleme ayrıntılarının çıktısını verir. Bildirilen engelleme sorgularını analiz edebilir, diğer bir deyişle, kilitleme performansı düşüşünü tanıtan sorgular ve bunları kaldırabilirsiniz. Bazı durumlarda, engelleme sorgularını iyileştirirken başarılı olabilirsiniz.

Sorunu azaltmanın en basit ve en güvenli yolu, işlem kısa tutulması ve en pahalı sorguların kilit parmak izini azaltmaktır. Büyük bir işlem toplu işlemini daha küçük işlemlere kesebilirsiniz. Sorgu kilit parmak izini mümkün olduğunca verimli hale getirerek iyi bir uygulamadır. Kilitlenme olasılığını artırdığından ve genel veritabanı performansını olumsuz yönde etkilediği için büyük taramalar sayısını azaltın. Kilitlemeye neden olan tanımlı sorgular için, tablo taramalarından kaçınmak üzere yeni dizinler oluşturabilir veya var olan dizine sütun ekleyebilirsiniz. 

Daha fazla öneri için, [SQL Server kilit yükseltme nedeniyle oluşan engelleme sorunlarını çözme](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)konusuna bakın.

## <a name="increased-maxdop"></a>Artan MAXDOP

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, seçilen bir sorgu yürütme planının çok daha fazla paralelleştirildi olduğunu gösterir. SQL veritabanı sorgu iyileştiricisi, mümkün olduğunca işlemleri hızlandırmak için sorguları paralel olarak yürüterek iş yükü performansını geliştirebilir. Bazı durumlarda, bir sorguyu işleyen paralel çalışanlar, daha az sayıda paralel çalışan ile aynı sorguyu yürütmeye ve hatta tek bir çalışan iş parçacığına kıyasla bazı durumlarda bile sonuçları eşitlemeye ve birleştirmeye kıyasla daha fazla zaman harcar.

Uzman sistem, geçerli veritabanı performansını temel dönemine göre analiz eder. Daha önce çalışan bir sorgunun daha yavaş çalışıp çalışmadığını belirler, çünkü sorgu yürütme planı, olması gerekenden daha paraleldir.

SQL veritabanı 'ndaki MAXDOP sunucu yapılandırma seçeneği, aynı sorguyu paralel olarak yürütmek için kaç CPU çekirdeğinin kullanılabileceğini denetlemek için kullanılır. 

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, yürütme süresi arttığı için sorgu karmalarıyla ilgili sorgu karmalarını çıktı. Günlük Ayrıca CXP bekleme süreleriyle ilgilidir. Bu süre, tek bir düzenleyici/düzenleyici iş parçacığının (iş parçacığı 0) sonuçları birleştirmeden ve devam etmeden önce diğer tüm iş parçacıklarının bitmesini beklediği süreyi temsil eder. Ayrıca, tanılama günlüğü kötü performanslı sorguların yürütmenin genel olarak beklediği bekleme sürelerini çıktı. Sorun giderme için temel olarak bu bilgileri kullanabilirsiniz.

İlk olarak, karmaşık sorguları iyileştirin veya kolaylaştırın. En iyi yöntem, uzun toplu işlerin daha küçük olanlara bölünmesinden iyidir. Ayrıca, sorgularınızı desteklemek için dizinler oluşturduğunuzdan emin olun. Zayıf performanslı olarak işaretlenen bir sorgu için en yüksek paralellik derecesini (MAXDOP) el ile uygulayabilirsiniz. Bu işlemi T-SQL kullanarak yapılandırmak için, bkz. [MAXDOP sunucu yapılandırma seçeneğini yapılandırma](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

MAXDOP sunucu yapılandırma seçeneğinin varsayılan değer olarak sıfır (0) olarak ayarlanması, SQL veritabanının tek bir sorgu yürütmek için paralel hale getirmek iş parçacıkları için kullanılabilir tüm mantıksal CPU çekirdeklerini kullanmasını gösterir. MAXDOP 'yi bir (1) olarak ayarlamak, tek bir sorgu yürütmesi için yalnızca bir çekirdeğin kullanılabileceğini gösterir. Pratik koşullarda bu, paralellik özelliğinin kapalı olduğu anlamına gelir. Büyük/küçük harf durumuna, veritabanında kullanılabilir çekirdekler ve tanılama günlüğü bilgileri 'ne bağlı olarak, MAXDOP seçeneğini, bu durumda sorunu giderebilecek paralel sorgu yürütmesi için kullanılan çekirdek sayısına göre ayarlayabilirsiniz.

## <a name="pagelatch-contention"></a>Pagemandal çekişmesi

### <a name="what-is-happening"></a>Ne oluyor

Bu performans stili, son yedi günlük iş yükü temeliyle karşılaştırılan pagemandal çekişmesi nedeniyle geçerli veritabanı iş yükü performansında azalma olduğunu gösterir.

Cadılar, SQL veritabanı tarafından çoklu iş parçacığı oluşturmayı etkinleştirmek için kullanılan hafif eşitleme mekanizmalarına sahiptir. Dizinler, veri sayfaları ve diğer iç yapıları içeren bellek içi yapıların tutarlılığını garanti eder.

SQL veritabanında bulunan birçok sayıda çim vardır. Kolaylık sağlamak amacıyla, arabellek havuzundaki bellek içi sayfaları korumak için arabellek lamanları kullanılır. GÇ havuzları, henüz arabellek havuzuna yüklenmeyen sayfaları korumak için kullanılır. Arabellek havuzundaki bir sayfadan veriler her yazıldığında veya okurken, bir çalışan iş parçacığının önce sayfa için bir arabellek mandalı elde etmek gerekir. Bir çalışan iş parçacığı, bellek içi arabellek havuzunda zaten kullanılamayan bir sayfaya erişmeyi denediğinde, depolama alanından gerekli bilgileri yüklemek için bir GÇ isteği yapılır. Bu olay sırası, performans düşüşünün daha ciddi bir biçimini gösterir.

Aynı anda birden çok iş parçacığı aynı anda, sorgu yürütmeye yönelik daha fazla bekleme süresi sunan, aynı anda birden fazla iş parçacığı de eş zamanlı olarak bir arada bulunmaları gerçekleştirmeye çalıştığında, sayfada çekişme meydana gelir. Pagemanması GÇ çakışması durumunda verilere depolamadan erişilmesi gerektiğinde, bu bekleme süresi de daha büyük olur. Bu, iş yükü performansını önemli ölçüde etkileyebilir. Pagemandal çekişmesi, birden çok CPU sisteminde bulunan ve her biri için bekleyen iş parçacıklarının en yaygın senaryolarıdır.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, pagemandal çekişmesi ayrıntılarını verir. Sorun giderme için temel olarak bu bilgileri kullanabilirsiniz.

Bir pagemandalı SQL veritabanı 'nın iç denetim mekanizması olduğundan, bu, ne zaman kullanılacağını otomatik olarak belirler. Şema tasarımı dahil olmak üzere uygulama kararları, nesnelerin belirleyici davranışı nedeniyle pagemandal davranışını etkileyebilir.

Mandal çekişmesini işlemek için bir yöntem, sıralı bir dizin anahtarını sıralı olmayan bir anahtarla değiştirmek ve bir dizin aralığına eklemeleri eşit bir şekilde dağıtmaktır. Genellikle, dizindeki önde gelen bir sütun iş yükünü orantılı olarak dağıtır. Dikkate alınması gereken başka bir yöntem de tablo bölümleniyor. Bölümlenmiş bir tabloda hesaplanan sütunlu bir karma bölümlendirme şeması oluşturma, aşırı mandal çekişmesini azaltmaya yönelik yaygın bir yaklaşımdır. Pagemanma GÇ çakışması durumunda, dizinler ile tanışın bu performans sorununu azaltmaya yardımcı olur. 

Daha fazla bilgi için bkz. SQL Server (PDF indirme) [üzerindeki mandal çekişmesini tanılama ve çözme](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) .

## <a name="missing-index"></a>Eksik dizin

### <a name="what-is-happening"></a>Ne oluyor

Bu performans stili, eksik bir dizin nedeniyle, son yedi günlük taban çizgisi ile karşılaştırıldığında geçerli veritabanı iş yükü performansında azalma olduğunu gösterir.

Sorguların performansını hızlandırmak için bir dizin kullanılır. Ziyaret edilen veya taranan veri kümesi sayfalarının sayısını azaltarak tablo verilerine hızlı erişim sağlar.

Performans düşüşüne neden olan belirli sorgular, bu algılama ile, dizin oluşturma performansı açısından yararlı olacak şekilde tanımlanır.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, iş yükü performansını etkilemek için tanımlanan sorgular için sorgu karmalarını çıktı. Bu sorgular için dizinler oluşturabilirsiniz. Ayrıca, gerekli değillerse bu sorguları iyileştirebilirsiniz veya kaldırabilirsiniz. İyi bir performans uygulaması, kullanmazsınız verileri sorgulamadan kaçınmaktır.

> [!TIP]
> SQL veritabanı yerleşik zekanın veritabanlarınızın en iyi durumda olan dizinlerini otomatik olarak yönetebileceğini biliyoruz mı?
>
> SQL Database 'in sürekli performans iyileştirmesi için, [SQL veritabanı otomatik ayarlamayı](sql-database-automatic-tuning.md)etkinleştirmenizi öneririz. SQL veritabanı yerleşik zeka 'nın bu benzersiz özelliği, SQL veritabanınızı sürekli olarak izler ve veritabanlarınızın dizinlerini otomatik olarak oluşturur.
>

## <a name="new-query"></a>Yeni sorgu

### <a name="what-is-happening"></a>Ne oluyor

Bu performans stili, çok düşük bir performans olduğunu ve yedi günlük performans temeline göre iş yükü performansını etkileyen yeni bir sorgu algılandığını gösterir.

İyi performanslı bir sorgu yazmak bazen zorlayıcı bir görev olabilir. Sorgu yazma hakkında daha fazla bilgi için bkz. [SQL sorguları yazma](https://msdn.microsoft.com/library/bb264565.aspx). Var olan sorgu performansını iyileştirmek için bkz. [sorgu ayarlama](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, sorgu karmaları da dahil olmak üzere en fazla iki yeni CPU kullanan sorguya bilgi verir. Algılanan sorgu iş yükü performansını etkilediği için sorgunuzu iyileştirebilmenizi sağlayabilirsiniz. Yalnızca kullanmanız gereken verileri almak iyi bir uygulamadır. Ayrıca, WHERE yan tümcesi ile sorguların kullanılması önerilir. Ayrıca, karmaşık sorguları basitleştirmenizi ve bunları daha küçük sorgulara kesmeniz önerilir. Büyük toplu iş sorgularını daha küçük toplu iş sorgularına bölmek çok iyi bir uygulamadır. Yeni sorgular için dizin oluşturma, genellikle bu performans sorununu hafifletmek için iyi bir uygulamadır.

[Azure SQL veritabanı sorgu performansı içgörüleri](sql-database-query-performance.md)kullanmayı düşünün.

## <a name="increased-wait-statistic"></a>Daha fazla bekleme Istatistiği

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, son yedi günlük iş yükü temeliyle karşılaştırıldığında kötü performanslı sorguların tanımlandığı bir iş yükü performans düşüşünü gösterir.

Bu durumda, sistem diğer standart algılanabilir performans kategorileri altındaki zayıf performanslı sorguları sınıflandırıp, ancak gerileme karşı bekleme istatistiğini algıladı. Bu nedenle, bu dosyaları *artan bekleme istatistiği*olan sorgular olarak değerlendirir; burada gerileme ilgili bekleme istatistiği de sunulur. 

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, etkilenen sorguların daha fazla bekleme süresi ayrıntıları ve sorgu karmaları hakkında bilgi verir.

Sistem kötü performanslı sorguların kök nedenini başarıyla belirleyemediği için tanılama bilgileri el ile sorun giderme için iyi bir başlangıç noktasıdır. Bu sorguların performansını iyileştirebilirsiniz. Yalnızca kullanmanız gereken verileri getirmek ve karmaşık sorguları basitleştirmek ve daha küçük olanlara bölmek için iyi bir uygulamadır. 

Sorgu performansını iyileştirme hakkında daha fazla bilgi için bkz. [sorgu ayarlama](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>TempDB çekişmesi

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans modelinde, tempDB kaynaklarına erişmeye çalışan iş parçacıklarının performans koşullarını içeren bir veritabanı performans koşulu gösterilir. (Bu durum GÇ ile ilgili değildir.) Bu performans sorunu için tipik senaryo, küçük tempDB tablolarının tümünü oluşturan, kullanan ve sonra bırakan yüzlerce eşzamanlı sorgu olur. Sistem, son yedi günlük performans temeline kıyasla veritabanı performansını etkilemek için aynı tempDB tablolarının kullanıldığı eşzamanlı sorguların sayısının yeterince istatistiksel anlamasıyla arttığını algıladı.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğünde tempDB çekişme ayrıntıları çıktı. Sorun giderme için başlangıç noktası olarak bu bilgileri kullanabilirsiniz. Bu tür çekişmeyi hafifetmek ve genel iş yükünün verimini artırmak için kullanabileceğiniz iki şey vardır: geçici tabloları kullanmayı durdurabilirsiniz. Bellek için iyileştirilmiş tabloları da kullanabilirsiniz. 

Daha fazla bilgi için bkz. [bellek için iyileştirilmiş tablolara giriş](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Elastik havuz DTU eksik

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, son yedi günlük taban çizgisiyle karşılaştırıldığında geçerli veritabanı iş yükü performansındaki düşüşünü gösterir. Bunun nedeni, aboneliğinizin elastik havuzundaki kullanılabilir DTU 'ların yetersizliğidir. 

SQL veritabanı 'ndaki kaynaklara genellikle, CPU ve GÇ (veri ve işlem günlüğü GÇ) kaynaklarının karışan bir ölçüden oluşan [DTU kaynakları](sql-database-purchase-models.md#dtu-based-purchasing-model)denir. [Azure elastik havuz kaynakları](sql-database-elastic-pool.md) , ölçekleme amacıyla birden çok veritabanı arasında paylaşılan bir kullanılabilir eDTU kaynakları havuzu olarak kullanılır. Elastik havuzunuzdaki kullanılabilir eDTU kaynakları, havuzdaki tüm veritabanlarını desteklemek için yeterince büyük değilse, sistem tarafından esnek bir havuz DTU performans sorunu algılanır.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, elastik havuzda bilgi verir, en üst DTU kullanan veritabanlarını listeler ve havuzun en iyi veritabanı tarafından kullanılan DTU yüzdesini sağlar.

Bu performans koşulu, elastik havuzda aynı eDTU havuzunu kullanan birden çok veritabanıyla ilişkili olduğundan, sorun giderme adımları en üst DTU kullanan veritabanlarına odaklanmaktadır. En iyi kullanan veritabanlarında iş yükünü azaltabilirsiniz. bu veritabanları üzerinde en üst düzey sorguların en iyi duruma getirilmesi de dahildir. Ayrıca, kullanmadığınız verileri sorgulamakta de emin olabilirsiniz. Diğer bir yaklaşım ise en üst DTU kullanan veritabanlarını kullanarak ve iş yükünü birden çok veritabanı arasında yeniden dağıtan uygulamaları optimize etmek.

En üst DTU kullanan veritabanlarındaki geçerli iş yükünün azaltılması ve iyileştirmesi mümkün değilse, esnek havuz fiyatlandırma katmanınızı artırmayı göz önünde bulundurun. Bu artış, esnek havuzdaki kullanılabilir DTU 'ların artışına neden olur.

## <a name="plan-regression"></a>Gerileme planı

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, SQL veritabanı 'nın bir alt sorgu yürütme planının kullanıldığı bir koşulu gösterir. Alt plan planı genellikle daha fazla sorgu yürütmeye neden olur ve bu da geçerli ve diğer sorguların uzun sürelerine yol açar.

SQL veritabanı, sorgu yürütme planını bir sorgu yürütmeye en az maliyetle belirler. Sorguların ve iş yüklerinin türü değiştikçe, bazı durumlarda mevcut planlar artık etkili değildir veya belki de SQL veritabanı iyi bir değerlendirme yapmamaktadır. Düzeltme olarak sorgu yürütme planları el ile zorunlu olabilir.

Bu algılanabilir performans stili, plan regresyonun üç farklı durumunu birleştirir: yeni plan gerileme, eski plan gerileme ve mevcut planlar iş yükünü değiştirdi. Oluşan belirli plan gerileme türü, tanılama günlüğündeki *Ayrıntılar* özelliğinde verilmiştir.

Yeni plan gerileme koşulu, SQL veritabanı 'nın eski plan kadar verimli olmayan yeni bir sorgu yürütme planı yürütmeye başladığı bir duruma başvurur. Eski plan regresyon koşulu, SQL veritabanı yeni plan kadar verimli olmayan eski plana yeni, daha verimli bir plan kullanarak geçiş yaptığında durum anlamına gelir. Mevcut planlar değişti iş yükü gerileme, eski ve yeni planların sürekli olarak alternatif olduğu, Bakiyenin düşük performanslı plana göre daha fazla ilerlemelerine karşılık gelir.

Plan gerilemeleri hakkında daha fazla bilgi için bkz. [SQL Server plan gerileme nedir?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü sorgu karmaları, iyi plan KIMLIĞI, hatalı plan KIMLIĞI ve sorgu kimliklerini çıktı. Sorun giderme için temel olarak bu bilgileri kullanabilirsiniz.

Belirtilen sorgu karmaları ile tanımlayabilmeniz için, belirli sorgularınız için hangi planın daha iyi performans gösterdiğini çözümleyebilirsiniz. Sorgularınız için hangi planın daha iyi çalıştığını belirledikten sonra, el ile zorlayabilirsiniz. 

Daha fazla bilgi için bkz. [SQL Server plan gerilemeleri nasıl engeller](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> SQL veritabanı yerleşik zekanın, veritabanlarınız için en iyi performanslı sorgu yürütme planlarını otomatik olarak yönetebileceğini biliyoruz mi?
>
> SQL Database 'in sürekli performans iyileştirmesi için, [SQL veritabanı otomatik ayarlamayı](sql-database-automatic-tuning.md)etkinleştirmenizi öneririz. SQL veritabanı 'nın bu benzersiz özelliği, SQL veritabanınızı sürekli olarak izler ve veritabanlarınız için en iyi şekilde yürütülen sorgu yürütme planlarını oluşturur.
>

## <a name="database-scoped-configuration-value-change"></a>Veritabanı kapsamlı yapılandırma değeri değişikliği

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans modelinde, veritabanı kapsamlı yapılandırmadaki bir değişikliğin, son yedi günlük veritabanı iş yükü davranışına kıyasla algılanan performans regresyonı olduğunu gösterir. Bu düzende, veritabanı kapsamlı yapılandırmada yapılan son değişikliğin veritabanı performanslarınız için yararlı görünmediğini belirtir.

Veritabanı kapsamlı yapılandırma değişiklikleri, her bir veritabanı için ayarlanabilir. Bu yapılandırma, veritabanınızın bireysel performansını iyileştirmek için büyük/küçük harf olarak kullanılır. Her veritabanı için aşağıdaki seçenekler yapılandırılabilir: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES ve CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Sorun giderme

Tanılama günlüğü, kısa süre önce yapılan ve önceki yedi günlük iş yükü davranışına kıyasla performans düşüşüne neden olan veritabanı kapsamlı yapılandırma değişikliklerini çıktı. Önceki değerlere yapılandırma değişikliklerini geri döndürebilirsiniz. Ayrıca, istenen performans düzeyine ulaşılana kadar değeri değere göre ayarlayabilirsiniz. Veritabanı kapsamı yapılandırma değerlerini, tatmin edici performansa sahip benzer bir veritabanından kopyalayabilirsiniz. Performansla ilgili sorun gidermek için varsayılan SQL veritabanı varsayılan değerlerine dönün ve bu taban çizgisinden başlayarak ince ayar yapmayı deneyin.

Yapılandırmayı değiştirirken veritabanı kapsamlı yapılandırma ve T-SQL söz dizimini iyileştirme hakkında daha fazla bilgi için bkz. [alter database-kapsamlıdır Configuration (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Yavaş Istemci

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, SQL veritabanını kullanan istemcinin, veritabanının sonuçları gönderdiği kadar hızlı bir şekilde veritabanından çıktı tüketmez olduğunu gösterir. SQL veritabanı, yürütülen sorguların sonuçlarını bir arabellekte depolamadığından, devam etmeden önce istemcinin iletilen sorgu çıktılarını kullanmasını bekler. Bu durum Ayrıca, SQL veritabanından tüketen istemciye çıkış aktarmak için yeterince hızlı olmayan bir ağla ilgili olabilir.

Bu koşul yalnızca, son yedi günlük veritabanı iş yükü davranışına kıyasla bir performans gerileme algılanırsa oluşturulur. Bu performans sorunu yalnızca, önceki performans davranışına kıyasla istatistiksel ölçüde önemli bir performans düşüşü oluşursa algılanır.

### <a name="troubleshooting"></a>Sorun giderme

Bu algılanabilir performans modelinde bir istemci tarafı koşulu gösterilir. İstemci tarafı uygulama veya istemci tarafı ağında sorun giderme gereklidir. Tanılama günlüğü, son iki saat içinde istemcinin bunları kullanmasını bekleyen sorgu karmalarını ve bekleme sürelerini çıktı olarak verir. Sorun giderme için temel olarak bu bilgileri kullanabilirsiniz.

Bu sorguların tüketimi için uygulamanızın performansını iyileştirebilirsiniz. Olası ağ gecikme sorunlarını da düşünebilirsiniz. Performans düşüşü sorunu son yedi günlük performans temelindeki değişikliği temel aldığı için, en son uygulamanın veya ağ koşulunun bu performans gerileme olayına neden olduğunu araştırabilirsiniz. 

## <a name="pricing-tier-downgrade"></a>Fiyatlandırma Katmanı düşürme

### <a name="what-is-happening"></a>Ne oluyor

Bu algılanabilir performans stili, SQL veritabanı aboneliğinizin fiyatlandırma katmanının indirgendiğini belirten bir koşul gösterir. Veritabanı için kullanılabilir kaynak azaltma (DTU 'Lar) nedeniyle, sistem son yedi günlük temel ile karşılaştırıldığında geçerli veritabanı performansında bir bırakma algıladı.

Ayrıca, SQL veritabanı aboneliğinizin fiyatlandırma katmanının indirgendiğini ve kısa bir süre içinde daha yüksek bir katmana yükseltildiğini belirten bir koşul olabilir. Bu geçici performans düşüşünü algılama, tanılama günlüğünün Ayrıntılar bölümünde fiyatlandırma katmanı düşürme ve yükseltme olarak gerçekleştirilir.

### <a name="troubleshooting"></a>Sorun giderme

Fiyatlandırma katmanınızı düşürüyorsanız ve bu nedenle SQL veritabanı için kullanılabilir DTU 'Lar varsa ve performansa memnun kaldıysanız, yapmanız gereken bir şey yoktur. Fiyatlandırma katmanınızı azaldıysanız ve SQL veritabanı performanstan memnun kaldıysanız, veritabanı iş yüklerinizi azaltın veya fiyatlandırma katmanını daha yüksek bir düzeye artırmayı düşünün.

## <a name="recommended-troubleshooting-flow"></a>Önerilen sorun giderme akışı

 Akıllı İçgörüler kullanarak performans sorunlarını gidermek için önerilen bir yaklaşım için akış çizelgesini izleyin.

Azure SQL Analytics 'e giderek Azure portal üzerinden Akıllı İçgörüler erişin. Gelen performans uyarısını bulmayı deneyin ve seçin. Algılamalar sayfasında neler olduğunu belirler. Sorunun, sorgu metninin, sorgu süresi eğilimlerini ve olay evriminin belirtilen kök neden analizini gözlemleyin. Sorunu gidermek için Akıllı İçgörüler öneriyi kullanarak sorunu çözmeyi deneyin. 

[![sorun giderme akış grafiği](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> PDF sürümünü indirmek için akış çizelgesini seçin.

Akıllı İçgörüler, genellikle performans sorununun kök neden analizini gerçekleştirmek için bir saat gerektirir. Sorununuzu Akıllı İçgörüler bulamazsanız ve sizin için önemli olduğundan, performans sorununun kök nedenini el ile tanımlamak için Query Store 'u kullanın. (Genellikle, bu sorunlar bir saatten daha eski.) Daha fazla bilgi için bkz. [sorgu deposunu kullanarak performansı izleme](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Sonraki adımlar
- [Akıllı içgörüler](sql-database-intelligent-insights.md) kavramları öğrenin.
- [Azure SQL veritabanı performans tanılama günlüğünü akıllı içgörüler](sql-database-intelligent-insights-use-diagnostics-log.md)kullanın.
- [Azure SQL Analytics kullanarak Azure SQL veritabanını](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql)izleyin.
- [Azure kaynaklarınızdan günlük verilerini toplamayı ve](../azure-monitor/platform/resource-logs-overview.md)kullanmayı öğrenin.
