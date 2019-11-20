---
title: Akıllı İçgörüler ile veritabanı performansını izleme
description: Azure SQL veritabanı Akıllı İçgörüler, yapay zeka aracılığıyla veritabanı kullanımını sürekli olarak izlemek ve zayıf performansa neden olan olayları saptamak için yerleşik zekayı kullanır.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: f17c588d28d0904041970eee8e9aa2201f905836
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151422"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance"></a>Veritabanı performansını izlemek ve sorunlarını gidermek için AI kullanarak Akıllı İçgörüler

Azure SQL veritabanı Akıllı İçgörüler, SQL veritabanınız ve yönetilen örnek veritabanı performansında neler olduğunu bilmenizi sağlar.

Akıllı İçgörüler, yapay zeka aracılığıyla veritabanı kullanımını sürekli olarak izlemek ve zayıf performansa neden olan olayları saptamak için yerleşik zeka kullanır. Algılandıktan sonra, sorunun akıllı değerlendirmesi ile tanılama günlüğü oluşturan ayrıntılı bir analiz gerçekleştirilir. Bu değerlendirme, veritabanı performans sorununun bir kök neden analizinden oluşur ve mümkün olduğunda performans iyileştirmeleri için öneriler içerir.

## <a name="what-can-intelligent-insights-do-for-you"></a>Sizin için Akıllı İçgörüler yapabilecekleriniz

Akıllı İçgörüler, Azure yerleşik zekanın aşağıdaki değeri sağlayan benzersiz bir özelliğidir:

- Proaktif izleme
- Özel performans öngörüleri
- Veritabanı performansı performansında erken algılama
- Algılanan sorunların kök neden analizi
- Performans geliştirme önerileri
- Yüzlerce binlerce veritabanında ölçeği genişletme özelliği
- DevOps kaynaklarına yönelik olumlu etki ve toplam sahip olma maliyeti

## <a name="how-does-intelligent-insights-work"></a>Akıllı İçgörüler nasıl çalışır?

Akıllı İçgörüler, son yedi günlük temel iş yüküyle veritabanı iş yükünü son saatten karşılaştırarak veritabanı performansını analiz eder. Veritabanı iş yükü, en çok yinelenen ve en büyük sorgular gibi veritabanı performansı için en önemli değer olarak belirlenen sorgulardan oluşur. Her veritabanı yapısına, verilerine, kullanımına ve uygulamasına göre benzersiz olduğundan, oluşturulan her iş yükü temeli tek bir örneğe özeldir ve benzersizdir. Akıllı İçgörüler, iş yükü taban çizgisinden bağımsız olarak, mutlak işlemsel eşikleri de izler ve çok fazla bekleme süresi, kritik özel durum ve performansı etkileyebilecek sorgu parametrelerinin sorunları ile ilgili sorunları algılar.

Yapay zeka kullanılarak birden çok gözlemlenen ölçümden performans düşüşü sorunu algılanırsa, analiz gerçekleştirilir. Tanılama günlüğü, veritabanınızda neler olduğunu gösteren akıllı bir öngörü ile oluşturulur. Akıllı İçgörüler, çözümleme yapılıncaya kadar veritabanı performans sorununu ilk görünüşünden izlemeyi kolaylaştırır. Algılanan her sorun, ilk sorun algılamadaki yaşam döngüsü boyunca ve performans iyileştirmesinin tamamlanmasına yönelik doğrulaması aracılığıyla izlenir.

![Veritabanı performans analizi iş akışı](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Veritabanı performans sorunlarını ölçmek ve algılamak için kullanılan ölçümler sorgu süresini, zaman aşımı isteklerini, aşırı bekleme süresini ve hatalı istekleri temel alır. Ölçümler hakkında daha fazla bilgi için bu belgenin [algılama ölçümleri](sql-database-intelligent-insights.md#detection-metrics) bölümüne bakın.

Tanımlı SQL veritabanı performansı azaltılmaları, tanılama günlüğüne aşağıdaki özelliklerden oluşan akıllı girdilerle kaydedilir:

| Özellik             | Ayrıntılar              |
| :------------------- | ------------------- |
| Veritabanı bilgileri | Bir öngörü algılanan ve kaynak URI gibi bir veritabanıyla ilgili meta veriler. |
| Gözlenen zaman aralığı | Algılanan öngörü dönemi için başlangıç ve bitiş zamanı. |
| Etkilenen ölçümler | Öngörü oluşturulmasına neden olan ölçümler: <ul><li>Sorgu süresi [saniye] değerini artırır.</li><li>Aşırı bekleyen [saniye].</li><li>Zaman aşımı istekleri [yüzde].</li><li>Hatalı istekler [Percentage].</li></ul>|
| Etki değeri | Ölçülen ölçüm değeri. |
| Etkilenen sorgular ve hata kodları | Sorgu karması veya hata kodu. Bunlar, etkilenen sorgularla kolayca ilişki kurmak için kullanılabilir. Sorgu süresi artışını, bekleme süresini, zaman aşımı sayılarını veya hata kodlarını içeren ölçümler sağlanır. |
| Durumunda | Bir olay sırasında veritabanında tanımlanan algılama. 15 algılama deseni vardır. Daha fazla bilgi için bkz. [akıllı içgörüler veritabanı performans sorunlarını giderme](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Kök neden analizi | İnsan tarafından okunabilen bir biçimde tanımlanan sorunun kök neden analizi. Bazı içgörüler, mümkün olduğunda bir performans geliştirme önerisi içerebilir. |
|||

Azure SQL Analytics ve tipik kullanım senaryolarında Akıllı İçgörüler kullanmaya yönelik uygulamalı bir genel bakış için bkz. gömülü video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

SQL veritabanı performans sorunlarını keşfetme ve sorun giderme Akıllı İçgörüler. SQL veritabanı ve yönetilen örnek veritabanı performans sorunlarını gidermek için Akıllı İçgörüler kullanmak için bkz. [akıllı içgörüler Azure SQL veritabanı performans sorunlarını giderme](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Akıllı İçgörüler seçenekleri

Azure SQL veritabanı 'nda kullanılabilir Akıllı İçgörüler seçenekleri şunlardır:

| Akıllı İçgörüler seçeneği | Tek veritabanı ve havuza alınmış veritabanı desteği | Örnek veritabanı desteği |
| :----------------------------- | ----- | ----- |
| **Akıllı içgörüler yapılandırma** -veritabanlarınız için akıllı içgörüler analizini yapılandırın. | Yes | Yes | 
| Azure SQL veritabanı için Azure SQL Analytics izleme çözümüne akış öngörülerini **Azure SQL Analytics** . | Yes | Yes | 
| Daha fazla özel Tümleştirmeler için Event Hubs, **Olay Hub 'ına** akış öngörüleri alın. | Yes | Yes | 
| **Azure depolama** hakkında bilgi edinin-daha fazla analiz ve uzun süreli arşivleme Için Azure depolama ile akış öngörüleri. | Yes | Yes |

## <a name="configure-intelligent-insights"></a>Akıllı İçgörüler Yapılandır

Akıllı İçgörüler çıkışı, akıllı bir performans tanılama günlüğü. Bu günlük, Azure SQL Analytics, Azure Event Hubs ve Azure depolama ya da üçüncü taraf ürünü için akış yapmak üzere çeşitli yollarla tüketilebilir.

- Azure portal kullanıcı arabiriminden öngörüleri görüntülemek için [Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) ürünü kullanın. Bu, tümleşik Azure çözümüdür ve öngörüleri görüntülemenin en yaygın yoludur.
- Özel izleme ve uyarı senaryoları geliştirmek için ürünü Azure Event Hubs kullanın
- Özel uygulama geliştirme için (örneğin, özel raporlama, uzun vadeli veri arşivleme vb.) ürünü Azure Storage ile birlikte kullanın.

Akıllı İçgörüler diğer ürünlerle Azure SQL Analytics, Azure Olay Hub 'ı, Azure depolama alanı veya üçüncü taraf ürünleri, tüketim için ilk olarak Akıllı İçgörüler günlüğe kaydetme ("Sqlinsıghts" günlüğü) kullanılarak yapılır. bir veritabanının ayarlar dikey penceresi ve sonra bu ürünlerden birine akışa eklenecek Akıllı İçgörüler günlük verilerini yapılandırma.

Akıllı İçgörüler günlüğe kaydetme özelliğini etkinleştirme ve günlük verilerinin bir tüketen ürüne akışını yapılandırma hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı ölçümleri ve tanılama günlüğü](sql-database-metrics-diag-logging.md).

### <a name="set-up-with-azure-sql-analytics"></a>Azure SQL Analytics ayarla

Azure SQL Analytics çözüm, veritabanı performansına ilişkin grafik kullanıcı arabirimi, raporlama ve uyarı özelliklerini Akıllı İçgörüler tanılama günlük verileriyle birlikte sağlar.

> [!TIP]
> Hızlı başlangıç: Akıllı İçgörüler kullanarak baştan sona almanın en kolay yolu, veritabanı performans sorunlarına bir grafik kullanıcı arabirimi sağlayan Azure SQL Analytics birlikte kullanmaktır. Market 'ten Azure SQL Analytics çözüm ekleyin, bu çözüm içinde bir çalışma alanı oluşturun ve Akıllı İçgörüler etkinleştirmek istediğiniz her veritabanı için bir veritabanının Tanılama ayarları dikey penceresinde "Sqlinsıghts" günlüğünün akışını yapılandırın Azure SQL Analytics çalışma alanı.
>

Ön gereksinim, Market 'ten Azure portal panonuza Azure SQL Analytics eklenir ve bir çalışma alanı oluşturmak için bkz. [yapılandırma Azure SQL Analytics](../azure-monitor/insights/azure-sql.md#configuration)

Azure SQL Analytics Akıllı İçgörüler kullanmak için, önceki adımda oluşturduğunuz Azure SQL Analytics çalışma alanına akışa almak üzere Akıllı İçgörüler günlük verilerini yapılandırmak için bkz. [Azure SQL veritabanı ölçümleri ve tanılama günlüğü](sql-database-metrics-diag-logging.md).

Aşağıdaki örnek, Azure SQL Analytics ile görüntülenen bir Akıllı İçgörüler gösterir:

![Akıllı İçgörüler raporu](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Event Hubs ayarla

Event Hubs Akıllı İçgörüler kullanmak için Akıllı İçgörüler günlük verilerini Event Hubs akışa almak üzere yapılandırın, bkz. [Event Hubs Için Azure tanılama günlüklerini akış](../azure-monitor/platform/resource-logs-stream-event-hubs.md).

Özel izleme ve uyarı ayarlamak için Event Hubs kullanmak için, bkz. [Event Hubs ' de ölçümler ve tanılama günlükleri Ile neler yapılır](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Azure depolama ile ayarlama

Depolama ile Akıllı İçgörüler kullanmak için Akıllı İçgörüler günlük verilerinin depolama alanına akışını yapılandırın, bkz. [Azure Storage 'Da akış](sql-database-metrics-diag-logging.md#stream-into-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Akıllı İçgörüler günlüğü için özel tümleştirmeler

Üçüncü taraf araçlarla Akıllı İçgörüler kullanmak veya özel uyarı ve izleme geliştirmesi için, bkz. [akıllı içgörüler veritabanı performansı tanılama günlüğünü kullanma](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Algılama ölçümleri

Akıllı İçgörüler üreten algılama modelleri için kullanılan ölçümler izlemeye dayalıdır:

- Sorgu süresi
- Zaman aşımı istekleri
- Aşırı bekleme süresi
- Hatalı istekler

Sorgu süresi ve zaman aşımı istekleri, veritabanı iş yükü performansıyla ilgili sorunları tespit eden birincil modeller olarak kullanılır. Bunlar, iş yüküyle neler olduğunu doğrudan ölçdiklerinden kullanılır. İş yükü performansının azalmasına ilişkin tüm olası durumları algılamak için, aşırı bekleme süresi ve hatalı çıkış istekleri, iş yükü performansını etkileyen sorunları göstermek için ek modeller olarak kullanılır.

Sistem, otomatik olarak iş yükündeki değişiklikleri ve normal ve sıradan veritabanı performans eşiklerini dinamik olarak belirlemede veritabanına yapılan sorgu isteklerinin sayısını göz önünde bulundurur.

Tüm ölçümler, algılanan her bir performans sorununu kategorilere ayırır scientifically türetilen bir veri modeli aracılığıyla çeşitli ilişkilerde birlikte değerlendirilir. Akıllı bir öngörü ile sunulan bilgiler şunları içerir:

- Algılanan performans sorununun ayrıntıları.
- Algılanan sorunun kök neden analizi.
- Mümkün olduğunda izlenen SQL veritabanının performansının nasıl geliştirileceğine ilişkin öneriler.

## <a name="query-duration"></a>Sorgu süresi

Sorgu süresi performansında azalma modeli, tek tek sorguları analiz eder ve performans temeline kıyasla bir sorgu derlemek ve yürütmek için geçen sürede artışı algılar.

SQL veritabanı yerleşik zeka, sorgu derleme veya sorgu yürütme süresinde iş yükü performansını etkileyen önemli bir artış algılarsa, bu sorgular sorgu süresi performansında azalma sorunları olarak işaretlenir.

Akıllı İçgörüler tanılama günlüğü, sorgunun sorgu karmasının performans düşüklüğü olduğunu çıktı. Sorgu karması, sorgu süresi süresini arttığı sorgu derleme veya yürütme süresi artışla ilgili performans düşüşünün olup olmadığını gösterir.

## <a name="timeout-requests"></a>Zaman aşımı istekleri

Zaman aşımı isteklerinin performansında azalma modeli, tek tek sorguları analiz eder ve sorgu yürütme düzeyindeki zaman aşımlarındaki artışların yanı sıra performans temeli dönemine kıyasla veritabanı düzeyinde genel istek zaman aşımlarını algılar.

Bazı sorgular, yürütme aşamasına ulaşmadan önce bile zaman aşımına uğrar. Durdurulan çalışanların ve isteklerin yanı sıra SQL veritabanı yerleşik zeka ölçüleri ve yürütme aşamasına sahip olup olmadıklarında veritabanına ulaşan tüm sorguları analiz eder.

Yürütülen sorgular için zaman aşımı sayısı veya durdurulan istek çalışanlarının sayısı sistem tarafından yönetilen eşikten kesişdikten sonra, akıllı Öngörüler ile bir tanılama günlüğü doldurulur.

Oluşturulan Öngörüler, zaman aşımına uğrayan isteklerin sayısını ve zaman aşımına uğrayan sorguların sayısını içerir. Performans düşüşünün, yürütme aşamasında zaman aşımı artışla ilgili göstergesi veya genel veritabanı düzeyi verilmiştir. Zaman aşımları içindeki artış veritabanı performansı açısından önemli kabul edildiğinde, bu sorgular zaman aşımı performansı performansında azalma sorunları olarak işaretlenir.

## <a name="excessive-wait-times"></a>Aşırı bekleme süresi

Aşırı bekleme süresi modeli, tek veritabanı sorgularını izler. Sistem tarafından yönetilen mutlak eşikleri engelleyen olağandışı yüksek sorgu bekleme istatistiklerini algılar. Aşağıdaki sorgu çok fazla bekleme süresi ölçümleri yeni SQL Server özelliği kullanılarak gözlemlenir, sorgu deposu bekleme Istatistikleri (sys. query_store_wait_stats):

- Kaynak sınırlarına ulaşma
- Elastik havuz kaynağı sınırlarına ulaşma
- Aşırı sayıda çalışan veya oturum iş parçacığı
- Aşırı veritabanı kilitleme
- Bellek baskısı
- Diğer bekleme istatistikleri

Kaynak sınırlarına veya elastik havuz kaynağı sınırlarına ulaşıldığında, bir abonelikte veya elastik havuzda bulunan kullanılabilir kaynakların tüketimi, mutlak eşiklerin çapraz olduğunu gösterir. Bu istatistikler iş yükü performansının azalmasına işaret ediyor. Aşırı sayıda çalışan veya oturum iş parçacığı, çok sayıda çalışan iş parçacığı veya oturum sayısının çapraz mutlak eşiklerinin başlatıldığını gösterir. Bu istatistikler iş yükü performansının azalmasına işaret ediyor.

Aşırı veritabanı kilitlemesi, bir veritabanındaki kilit sayısının çapraz mutlak eşiklere sahip olduğu bir koşulu belirtir. Bu stat bir iş yükü performansında düşme olduğunu gösterir. Bellek baskısı, bellek isteyen iş parçacığı sayısının mutlak bir eşiğe eşit olarak geçmediğini belirten bir durumdur. Bu stat bir iş yükü performansında düşme olduğunu gösterir.

Diğer bekleme istatistikleri algılama, çeşitli ölçümlerin sorgu deposu bekleme Istatistikleri ile mutlak bir eşiğe geçtiğini belirten bir koşul gösterir. Bu istatistikler iş yükü performansının azalmasına işaret ediyor.

Çok fazla bekleme süresi algılandıktan sonra, kullanılabilir verilere bağlı olarak, Akıllı İçgörüler tanılama günlüğü, etkilenen ve etkilenen sorguların karma değerlerini performans, sorguların yürütülmeyi beklemesine neden olan ölçümlerin ayrıntılarını verir ve ölçülen bekleme süresi.

## <a name="errored-requests"></a>Hatalı istekler

Hatalı istek performansında azalma modeli, tek tek sorguları izler ve temel noktayla karşılaştırıldığında hatalı sorgu sayısında artış algılar. Bu model, SQL Database yerleşik zeka tarafından yönetilen mutlak eşiklerin çapraz olduğu önemli özel durumları da izler. Sistem, veritabanında yapılan sorgu isteklerinin sayısını ve izlenen dönemdeki herhangi bir iş yükü değişikliği için hesapları otomatik olarak değerlendirir.

Yapılan isteklerin genel sayısına göre hatalı isteklerindeki ölçülen artış, iş yükü performansı açısından önemli kabul edildiğinde, etkilenen sorgular hatalı istekleri performans performansında azalma sorunları olarak işaretlenir.

Akıllı İçgörüler günlüğü hatalı isteklerin sayısını verir. Performans düşüşünün hatalı isteklerindeki artışla mi yoksa izlenen kritik özel durum eşiğini ve performans düşüşünün ölçüldüğü ile ilgili olduğunu gösterir.

İzlenen kritik özel durumlar sistem tarafından yönetilen mutlak eşiklerden fazla olursa, kritik özel durum ayrıntılarıyla bir akıllı öngörü oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar

- [Akıllı içgörüler Ile SQL veritabanı performans sorunlarını giderme](sql-database-intelligent-insights-troubleshoot-performance.md)hakkında bilgi edinin.
- [SQL veritabanı performans tanılama günlüğünü akıllı içgörüler](sql-database-intelligent-insights-use-diagnostics-log.md)kullanın.
- SQL Analytics 'i [kullanarak SQL veritabanı izleme](../azure-monitor/insights/azure-sql.md)hakkında bilgi edinin.
- [Azure kaynaklarınızdan günlük verilerini nasıl toplayacağınızı ve](../azure-monitor/platform/resource-logs-overview.md)kullanacağınızı öğrenin.
