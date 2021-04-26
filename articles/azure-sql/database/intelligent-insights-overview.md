---
title: Akıllı İçgörüler ile veritabanı performansını izleme
description: Azure SQL veritabanı 'nda Akıllı İçgörüler ve Azure SQL yönetilen örneği, yapay zeka aracılığıyla veritabanı kullanımını sürekli olarak izlemek ve düşük performansa neden olan kötü etkinlikleri saptamak için yerleşik zeka kullanır.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 61033e3eb8264c1e462faac3e4553a855a1d06c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592079"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Veritabanı performansını izlemek ve sorunlarını gidermek için AI kullanarak Akıllı İçgörüler (Önizleme)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve Azure SQL yönetilen örneği Akıllı İçgörüler, veritabanı performansında neler olduğunu bilmenizi sağlar.

Akıllı İçgörüler, yapay zeka aracılığıyla veritabanı kullanımını sürekli olarak izlemek ve zayıf performansa neden olan olayları saptamak için yerleşik zeka kullanır. Algılandıktan sonra, sorunun akıllı değerlendirmesi ile Akıllı İçgörüler kaynak günlüğü (Sqlinsıghts olarak adlandırılır) oluşturan ayrıntılı bir analiz gerçekleştirilir. Bu değerlendirme, veritabanı performans sorununun bir kök neden analizinden oluşur ve mümkün olduğunda performans iyileştirmeleri için öneriler içerir.

## <a name="what-can-intelligent-insights-do-for-you"></a>Sizin için Akıllı İçgörüler yapabilecekleriniz

Akıllı İçgörüler, Azure yerleşik zekanın aşağıdaki değeri sağlayan benzersiz bir özelliğidir:

- Öngörülebilir izleme
- Özel performans öngörüleri
- Veritabanı performansı performansında erken algılama
- Algılanan sorunların kök neden analizi
- Performans geliştirme önerileri
- Yüzlerce binlerce veritabanında ölçeği genişletme özelliği
- DevOps kaynaklarına yönelik olumlu etki ve toplam sahip olma maliyeti

## <a name="how-does-intelligent-insights-work"></a>Akıllı İçgörüler nasıl çalışır?

Akıllı İçgörüler, son yedi günlük temel iş yüküyle veritabanı iş yükünü son saatten karşılaştırarak veritabanı performansını analiz eder. Veritabanı iş yükü, en çok yinelenen ve en büyük sorgular gibi veritabanı performansı için en önemli değer olarak belirlenen sorgulardan oluşur. Her veritabanı yapısına, verilerine, kullanımına ve uygulamasına göre benzersiz olduğundan, oluşturulan her iş yükü temeli bu iş yüküne özgüdür ve benzersizdir. Akıllı İçgörüler, iş yükü taban çizgisinden bağımsız olarak, mutlak işlemsel eşikleri de izler ve çok fazla bekleme süresi, kritik özel durum ve performansı etkileyebilecek sorgu parametrelerinin sorunları ile ilgili sorunları algılar.

Yapay zeka kullanılarak birden çok gözlemlenen ölçümden performans düşüşü sorunu algılanırsa, analiz gerçekleştirilir. Tanılama günlüğü, veritabanınızda neler olduğunu gösteren akıllı bir öngörü ile oluşturulur. Akıllı İçgörüler, çözümleme yapılıncaya kadar veritabanı performans sorununu ilk görünüşünden izlemeyi kolaylaştırır. Algılanan her sorun, ilk sorun algılamadaki yaşam döngüsü boyunca ve performans iyileştirmesinin tamamlanmasına yönelik doğrulaması aracılığıyla izlenir.

![Veritabanı performans analizi iş akışı](./media/intelligent-insights-overview/intelligent-insights-concept.png)

Veritabanı performans sorunlarını ölçmek ve algılamak için kullanılan ölçümler sorgu süresini, zaman aşımı isteklerini, aşırı bekleme süresini ve hatalı istekleri temel alır. Ölçümler hakkında daha fazla bilgi için bkz. [algılama ölçümleri](#detection-metrics).

Tanımlanan veritabanı performansı azaltılmaları, aşağıdaki özelliklerden oluşan akıllı girişlerle Sqlinsıghts günlüğüne kaydedilir:

| Özellik | Ayrıntılar |
| :------------------- | ------------------- |
| Veritabanı bilgileri | Bir öngörü algılanan ve kaynak URI gibi bir veritabanıyla ilgili meta veriler. |
| Gözlenen zaman aralığı | Algılanan öngörü dönemi için başlangıç ve bitiş zamanı. |
| Etkilenen ölçümler | Öngörü oluşturulmasına neden olan ölçümler: <ul><li>Sorgu süresi [saniye] değerini artırır.</li><li>Aşırı bekleyen [saniye].</li><li>Zaman aşımı istekleri [yüzde].</li><li>Hatalı istekler [Percentage].</li></ul>|
| Etki değeri | Ölçülen ölçüm değeri. |
| Etkilenen sorgular ve hata kodları | Sorgu karması veya hata kodu. Bunlar, etkilenen sorgularla kolayca ilişki kurmak için kullanılabilir. Sorgu süresi artışını, bekleme süresini, zaman aşımı sayılarını veya hata kodlarını içeren ölçümler sağlanır. |
| Durumunda | Bir olay sırasında veritabanında tanımlanan algılama. 15 algılama deseni vardır. Daha fazla bilgi için bkz. [akıllı içgörüler veritabanı performans sorunlarını giderme](intelligent-insights-troubleshoot-performance.md). |
| Kök neden analizi | İnsan tarafından okunabilen bir biçimde tanımlanan sorunun kök neden analizi. Bazı içgörüler, mümkün olduğunda bir performans geliştirme önerisi içerebilir. |
|||

Azure SQL Analytics ve tipik kullanım senaryolarında Akıllı İçgörüler kullanmaya yönelik uygulamalı bir genel bakış için şu videoya bakın:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Veritabanı performans sorunlarını keşfetme ve sorun giderme Akıllı İçgörüler. Veritabanı performans sorunlarını gidermek için Akıllı İçgörüler kullanmak için bkz. [akıllı içgörüler performans sorunlarını giderme](intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Akıllı İçgörüler seçenekleri

Kullanılabilir Akıllı İçgörüler seçenekleri şunlardır:

| Akıllı İçgörüler seçeneği | Azure SQL veritabanı desteği | Azure SQL Yönetilen Örneği desteği |
| :----------------------------- | ----- | ----- |
| **Akıllı içgörüler yapılandırma** -veritabanlarınız için akıllı içgörüler analizini yapılandırın. | Yes | Yes |
| **Azure SQL Analytics** içgörüler için Azure SQL Analytics Stream Insights. | Yes | Yes |
| Daha fazla özel Tümleştirmeler için Event Hubs **Azure Event Hubs** Stream öngörülerini edinin. | Yes | Yes |
| **Azure depolama** hakkında bilgi edinin-daha fazla analiz ve uzun süreli arşivleme Için Azure depolama ile akış öngörüleri. | Yes | Yes |

> [!NOTE]
> Akıllı Öngörüler şu bölgelerde kullanılamayan bir önizleme özelliğidir: Batı Avrupa, Kuzey Avrupa, Batı ABD 1 ve Doğu ABD 1.

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Akıllı İçgörüler günlüğünün dışarı aktarılmasını yapılandırma

Akıllı İçgörüler çıkışı, analiz için birkaç hedefden birine akışla eklenebilir:

- Log Analytics çalışma alanına akan çıktı, Azure portal Kullanıcı arabirimi aracılığıyla öngörüleri görüntülemek için [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) ile birlikte kullanılabilir. Bu, tümleşik Azure çözümüdür ve öngörüleri görüntülemenin en yaygın yoludur.
- Azure Event Hubs akış çıkışı, özel izleme ve uyarı senaryoları geliştirmesi için kullanılabilir
- Azure Storage 'a akan çıkış, özel raporlama, uzun vadeli veri arşivleme gibi özel uygulama geliştirme için kullanılabilir.

Azure SQL Analytics, Azure Event Hubs, Azure depolama veya üçüncü taraf ürünlerinin tümleştirilmesi, ilk olarak bir veritabanının Tanılama ayarları dikey penceresinde Akıllı İçgörüler günlüğe kaydetme ("Sqlinsıghts" günlüğü) ve ardından bu hedeflere bir birine akışa eklenecek Akıllı İçgörüler günlük verilerinin yapılandırılması aracılığıyla gerçekleştirilir.

Akıllı İçgörüler günlüğe kaydetme özelliğini etkinleştirme ve ölçüm ve kaynak günlük verilerinin bir tüketen ürüne akışını yapılandırma hakkında daha fazla bilgi için bkz. [ölçümler ve tanılama günlüğü](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

### <a name="set-up-with-azure-sql-analytics"></a>Azure SQL Analytics ayarla

Azure SQL Analytics çözümü, Akıllı İçgörüler kaynak günlük verilerini kullanarak veritabanı performansına ilişkin grafik kullanıcı arabirimi, raporlama ve uyarı özellikleri sağlar.

Market 'ten Azure portal panonuza Azure SQL Analytics ekleyin ve bir çalışma alanı oluşturmak için bkz. [yapılandırma Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md#configuration)

Azure SQL Analytics Akıllı İçgörüler kullanmak için, önceki adımda oluşturduğunuz Azure SQL Analytics çalışma alanına akışa almak üzere Akıllı İçgörüler günlük verilerini yapılandırın, bkz. [ölçümler ve tanılama günlüğü](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

Aşağıdaki örnek, Azure SQL Analytics ile görüntülenen bir Akıllı İçgörüler gösterir:

![Akıllı İçgörüler raporu](./media/intelligent-insights-overview/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Event Hubs ayarla

Event Hubs Akıllı İçgörüler kullanmak için Akıllı İçgörüler günlük verilerini Event Hubs akışa almak üzere yapılandırın, bkz. [ölçüm ve tanılama günlüğe kaydetme](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) ve [Azure tanılama günlüklerini akışa alma Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

Özel izleme ve uyarı ayarlamak için Event Hubs kullanmak için, bkz. [Event Hubs ' de ölçümler ve tanılama günlükleri Ile neler yapılır](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Azure depolama ile ayarlama

Depolama ile Akıllı İçgörüler kullanmak için Akıllı İçgörüler günlük verilerinin depolama alanına akışını yapılandırın, bkz. [Azure Storage 'Da](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage) [ölçüm ve tanılama günlüğü](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) ve akışı.

### <a name="custom-integrations-of-intelligent-insights-log"></a>Akıllı İçgörüler günlüğü için özel tümleştirmeler

Üçüncü taraf araçlarla Akıllı İçgörüler kullanmak veya özel uyarı ve izleme geliştirmesi için, bkz. [akıllı içgörüler veritabanı performansı tanılama günlüğünü kullanma](intelligent-insights-use-diagnostics-log.md).

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
- Mümkün olduğunda izlenen veritabanının performansının nasıl geliştirileceğine ilişkin öneriler.

## <a name="query-duration"></a>Sorgu süresi

Sorgu süresi performansında azalma modeli, tek tek sorguları analiz eder ve performans temeline kıyasla bir sorgu derlemek ve yürütmek için geçen sürede artışı algılar.

Yerleşik zeka, sorgu derleme veya sorgu yürütme süresinde iş yükü performansını etkileyen önemli bir artış algılarsa, bu sorgular sorgu süresi performansında azalma sorunları olarak işaretlenir.

Akıllı İçgörüler tanılama günlüğü, sorgunun sorgu karmasının performans düşüklüğü olduğunu çıktı. Sorgu karması, sorgu süresi süresini arttığı sorgu derleme veya yürütme süresi artışla ilgili performans düşüşünün olup olmadığını gösterir.

## <a name="timeout-requests"></a>Zaman aşımı istekleri

Zaman aşımı isteklerinin performansında azalma modeli, tek tek sorguları analiz eder ve sorgu yürütme düzeyindeki zaman aşımlarındaki artışların yanı sıra performans temeli dönemine kıyasla veritabanı düzeyinde genel istek zaman aşımlarını algılar.

Bazı sorgular, yürütme aşamasına ulaşmadan önce bile zaman aşımına uğrar. Durdurulan çalışanların ve yapılan isteklerin yanı sıra, yerleşik zeka ölçüleri ve yürütme aşamasına sahip olup olmadıklarında veritabanına ulaşan tüm sorguları analiz eder.

Yürütülen sorgular için zaman aşımı sayısı veya durdurulan istek çalışanlarının sayısı sistem tarafından yönetilen eşikten kesişdikten sonra, akıllı Öngörüler ile bir tanılama günlüğü doldurulur.

Oluşturulan Öngörüler, zaman aşımına uğrayan isteklerin sayısını ve zaman aşımına uğrayan sorguların sayısını içerir. Performans düşüşünün, yürütme aşamasında zaman aşımı artışla ilgili göstergesi veya genel veritabanı düzeyi verilmiştir. Zaman aşımları içindeki artış veritabanı performansı açısından önemli kabul edildiğinde, bu sorgular zaman aşımı performansı performansında azalma sorunları olarak işaretlenir.

## <a name="excessive-wait-times"></a>Aşırı bekleme süresi

Aşırı bekleme süresi modeli, tek veritabanı sorgularını izler. Sistem tarafından yönetilen mutlak eşikleri engelleyen olağandışı yüksek sorgu bekleme istatistiklerini algılar. Aşağıdaki sorgu çok fazla bekleme süresi ölçümleri kullanılarak gözlemlenir, [sorgu deposu bekleme istatistikleri (sys.query_store_wait_stats)](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql):

- Kaynak sınırlarına ulaşma
- Elastik havuz kaynağı sınırlarına ulaşma
- Aşırı sayıda çalışan veya oturum iş parçacığı
- Aşırı veritabanı kilitleme
- Bellek baskısı
- Diğer bekleme istatistikleri

Kaynak sınırlarına veya elastik havuz kaynağı sınırlarına ulaşıldığında, bir abonelikte veya elastik havuzda bulunan kullanılabilir kaynakların tüketimi, mutlak eşiklerin çapraz olduğunu gösterir. Bu istatistikler iş yükü performansının azalmasına işaret ediyor. Aşırı sayıda çalışan veya oturum iş parçacığı, çok sayıda çalışan iş parçacığı veya oturum sayısının çapraz mutlak eşiklerinin başlatıldığını gösterir. Bu istatistikler iş yükü performansının azalmasına işaret ediyor.

Aşırı veritabanı kilitlemesi, bir veritabanındaki kilit sayısının çapraz mutlak eşiklere sahip olduğu bir koşulu belirtir. Bu stat bir iş yükü performansında düşme olduğunu gösterir. Bellek baskısı, bellek isteyen iş parçacığı sayısının mutlak bir eşiğe eşit olarak geçmediğini belirten bir durumdur. Bu stat bir iş yükü performansında düşme olduğunu gösterir.

Diğer bekleme istatistikleri algılama, çeşitli ölçümlerin sorgu deposu bekleme Istatistikleri ile mutlak bir eşiğe geçtiğini belirten bir koşul gösterir. Bu istatistikler iş yükü performansının azalmasına işaret ediyor.

Çok fazla bekleme süresi algılandıktan sonra, kullanılabilir verilere bağlı olarak, Akıllı İçgörüler tanılama günlüğü, etkilenen ve etkilenen sorguların karma değerlerini performans, sorguların yürütülmeyi beklemesine neden olan ölçümlerin ayrıntılarını ve ölçülen bekleme süresini verir.

## <a name="errored-requests"></a>Hatalı istekler

Hatalı istek performansında azalma modeli, tek tek sorguları izler ve temel noktayla karşılaştırıldığında hatalı sorgu sayısında artış algılar. Bu model Ayrıca, yerleşik zeka tarafından yönetilen mutlak eşiklerin çapraz olduğu önemli özel durumları izler. Sistem, veritabanında yapılan sorgu isteklerinin sayısını ve izlenen dönemdeki herhangi bir iş yükü değişikliği için hesapları otomatik olarak değerlendirir.

Yapılan isteklerin genel sayısına göre hatalı isteklerindeki ölçülen artış, iş yükü performansı açısından önemli kabul edildiğinde, etkilenen sorgular hatalı istekleri performans performansında azalma sorunları olarak işaretlenir.

Akıllı İçgörüler günlüğü hatalı isteklerin sayısını verir. Performans düşüşünün hatalı isteklerindeki artışla mi yoksa izlenen kritik özel durum eşiğini ve performans düşüşünün ölçüldüğü ile ilgili olduğunu gösterir.

İzlenen kritik özel durumlar sistem tarafından yönetilen mutlak eşiklerden fazla olursa, kritik özel durum ayrıntılarıyla bir akıllı öngörü oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar

- [SQL Analytics kullanarak veritabanlarını izlemeyi](../../azure-monitor/insights/azure-sql.md)öğrenin.
- [Akıllı içgörüler performans sorunlarını giderme](intelligent-insights-troubleshoot-performance.md)hakkında bilgi edinin.