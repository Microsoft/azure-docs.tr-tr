---
title: Önizleme kullanım durumları-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Önizleme kullanım örnekleri hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: 175fbf598ae2224d1e1ee0e9c9a3f9ba446965c3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014681"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Azure Time Series Insights Önizleme kullanım örnekleri

Bu makalede Azure Time Series Insights önizlemesi için bazı yaygın kullanım durumları özetlenmektedir. Bu makaledeki öneriler, Time Series Insights ile uygulamalarınızın ve çözümlerin geliştirilmesi için bir başlangıç noktası olarak görev yapar.

Özellikle, bu makalede aşağıdaki sorulara yanıt verilmiştir:

* Time Series Insights için genel kullanım örnekleri nelerdir?
* [Veri araştırması ve görsel anomali algılama](#data-exploration-and-visual-anomaly-detection)için Time Series Insights kullanmanın avantajları nelerdir?
* [İşletimsel analiz ve işlem verimliliği](#operational-analysis-and-driving-process-efficiency)için Time Series Insights kullanmanın avantajları nelerdir?
* [Gelişmiş analiz](#advanced-analytics)için Time Series Insights kullanmanın avantajları nelerdir?

Bu kullanım senaryolarına genel bir bakış aşağıdaki bölümlerde açıklanmıştır.

## <a name="introduction"></a>Giriş

Azure Time Series Insights, uçtan uca, bir hizmet olarak platform sunumudur. Yüksek düzeyde tanımlı, zaman serisi iyileştirilmiş IoT ölçeğinde verileri toplamak, işlemek, depolamak, çözümlemek ve sorgulamak için kullanılır. Time Series Insights, geçici veri araştırması ve işletimsel analizler için idealdir. Time Series Insights, endüstriyel IoT dağıtımlarının geniş ihtiyaçlarını karşılayan benzersiz bir şekilde genişletilebilir, özelleştirilmiş bir hizmet sunumudur.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Veri inceleme ve görsel anormali algılama

Verilerinizdeki anomalileri bulmak ve gizli eğilimleri keşfetmek için milyarlarca olayı anında inceleyip analiz edin. Time Series Insights, IoT ve DevOps analiz iş yüklerinize neredeyse gerçek zamanlı performans katar.

[![Veri Gezgini](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Çoğu müşteri, öngörü kazanmak için gereken en kısa süreyi, Time Series Insights ' ın en az bir özellik olduğunu kabul etmiş olursunuz:

* Time Series Insights ön veri hazırlığı gerektirmez. 
* Bu, Azure IoT Hub veya Azure Event Hubs örneklerinde birkaç dakika içinde bağlantı kurmak için hızlı bir şekilde çalışmaktadır. 
* Bağlandıktan sonra, milyarlarca olayı görselleştirme ve analiz edebilir ve verilerinizdeki gizli eğilimleri keşfedebilirsiniz.

Time Series Insights sezgisel ve kullanımı basittir. Tek bir kod satırı yazmadan verilerinizle etkileşim kurabilirsiniz. Ayrıca, öğrenmeniz gereken yeni bir dil yoktur, ancak Time Series Insights, SQL 'e tanıdık olan gelişmiş kullanıcılar için ayrıntılı metin tabanlı bir sorgulama dili sağlar. Ayrıca, novıces için seçme ve tıklama araştırması sağlar.

Müşteriler, varlık ile ilgili sorunları hızlı bir şekilde tanılamaya yönelik hızdan yararlanabilir. Bir IoT çözümünde hatanın kök nedenine ulaşmak için DevOps analizini gerçekleştirebilir. Bunlar ayrıca, veri bilimi girişimlerinin bir parçası olarak daha fazla araştırma için bayrak eklenecek alanlara da tanımlayabilir. 

Time Series Insights depolanan verilerle etkileşimde bulunmak için üç temel yol vardır:

- Başlamak için ilk ve en kolay yol Time Series Insights önizleme Gezginini kullanmaktır. Bunu kullanarak tüm IoT verilerinizi tek bir yerde hızla görselleştirebilirsiniz. Verilerinize ilişkin eğilimleri belirlemenize yardımcı olması için ısı haritası gibi araçlar sağlar. Ayrıca bir perspektif görünümü de sağlar. Tek bir panoda bir veya daha fazla Time Series Insights ortamından en fazla dört görünüm karşılaştırmak için bunu kullanın. Pano size tüm konumlarınızın zaman serisi verilerinin bir görünümünü sunar. [Time Series Insights önizleme Gezgini](./time-series-insights-update-explorer.md)hakkında daha fazla bilgi edinin. Time Series Insights ortamınızı planlamak için, [Time Series Insights planlamayı](./time-series-insights-update-plan.md)okuyun.

- Başlangıç yapmanın ikinci yolu, Web uygulamanıza güçlü grafikler ve grafikler eklemek için JavaScript SDK 'sını kullanmaktır. Yalnızca birkaç satır kodla, güçlü sorgular yazabilirsiniz. Çizgi grafiklerini, pasta grafiklerini, çubuk grafiklerini, ısı haritalarını, veri kılavuzlarını ve daha fazlasını doldurmak için bunları kullanın. Bu öğelerin tümü SDK kullanılarak kullanıma hazır. SDK Ayrıca Time Series Insights sorgu API 'Lerini de soyutlar. Bunları, bir panoda göstermek istediğiniz verileri sorgulamak için SQL benzeri koşullar yazmak üzere kullanabilirsiniz. Karma sunum katmanı çözümleri için Time Series Insights parametreli URL 'Ler sunar. Verilerle ilgili ayrıntılı bilgi için Time Series Insights önizleme Gezgini ile sorunsuz bağlantı noktaları sağlar.

    * JavaScript SDK hakkında daha fazla bilgi edinmek için [TIME SERIES INSIGHTS js istemci kitaplığı](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) ve [Time Series Insights istemci](https://github.com/Microsoft/tsiclient) belgeleri hakkında bilgi edinin.

    * [Azure Time Series Insights önizleme Gezgininde verileri görselleştirmeyi](time-series-insights-update-explorer.md)Inceleyerek URL 'leri ve yeni kullanıcı arabirimini paylaşma hakkında daha fazla bilgi edinin.

- Başlangıç yapmanın üçüncü yolu Time Series Insights ' de depolanan verileri sorgulamak için güçlü API 'Leri kullanmaktır. Time Series Insights, `from`, `to`, `first`ve `last`gibi zamana bağlı işleçlere sahiptir. `average`, `min`, `max`, `split by`, `order by`ve `DateHistogram`gibi toplamalara ve Dönüştürmelere sahiptir. Ayrıca, `has`, `in`, `and`, `or`, `greater than`ve `REGEX`gibi filtreleme işleçleri de vardır. Tüm bu işleçler, aşağı akış uygulamalarının verilerinize yönelik ilginç eğilimleri ve desenleri hızlı bir şekilde bulmasını sağlar. Onları, anormal görselleştirmelere göre doldurmak için kullanın.

## <a name="operational-analysis-and-driving-process-efficiency"></a>İşlem analizi ve işlem verimliliğini sağlama

Donanımların sistem durumunu, kullanımını ve performansını ölçeklendirerek izlemek için Time Series Insights kullanın. Time Series Insights işlemsel verimliliği ölçmenin kolay bir yolunu sağlar. Time Series Insights, öngörülemeyen çeşitli IoT iş yüklerini, alma ve sorgu performansından ödün vermeden yönetmenize yardımcı olur.

[![Genel bakış](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

İşlem işlemlerinden gelen verilerin akışını ve sürekli işlenmesi, doğru teknoloji veya çözümle birleştirilirse herhangi bir işi başarıyla dönüştürebilir. Bu çözümler genellikle birden çok sisteminin bir birleşimidir. Bunlar, özellikle IoT bölgesindeki, sürekli olarak değişen verilerin araştırmasını ve analizini etkinleştirir ve ortak bir model paylaşır.

Bu desenler genellikle çeşitli yerel ayarlara yayılan cihazlardan ve sensörlerden milyarlarca olay alan IoT özellikli platformlarla başlar. Bu sistemler, gerçek zamanlı Öngörüler ve eylemler elde etmek için akış verilerini işler ve analiz eder. Veriler genellikle neredeyse gerçek zamanlı ve toplu analiz için normal ve soğuk depoya arşivlenir.

Toplanan veriler, akış sorgulama ve analiz senaryoları için onu temizleştirmek ve bağlamak üzere bir dizi işleme geçer. Azure, varlık bakımı ve üretimi gibi IoT senaryolarına uygulanabilen zengin hizmetler sunar. Bu hizmetler Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Işlevleri, Azure Logic Apps, Azure Databricks, Azure Machine Learning ve Power BI içerir.

Çözüm mimarisi aşağıdaki şekilde elde edilebilir:

- Sınıfının en iyi güvenlik, verimlilik ve gecikme süresi için IoT Hub veya Event Hubs aracılığıyla veri alma.
- Veri işleme ve hesaplamalar gerçekleştirin. Stream Analytics, Logic Apps ve Azure Işlevleri gibi hizmetler aracılığıyla huni alınan veriler. Kullandığınız hizmet, belirli veri işleme ihtiyaçlarına bağlıdır.
- İşleme ardışık düzeninde hesaplanan sinyaller, depolama ve analiz için Time Series Insights gönderilir.

Time Series Insights, geçmiş veriler üzerinde neredeyse gerçek zamanlı veri araştırması ve varlık tabanlı öngörüler sunar. İş gereksinimlerinize bağlı olarak, MapReduce ve Hive işleri, Time Series Insights Azure HDInsight 'a bağlanarak Time Series Insights depolanan veriler üzerinde çalışabilir. Time Series Insights depolanan veriler, Time Series Insights ortak yüzey sorgu API 'Leri aracılığıyla Power BI ve diğer müşteri uygulamaları tarafından kullanılabilir. Bu veriler, derin iş ve operasyonel zeka senaryoları için kullanılabilir.

## <a name="advanced-analytics"></a>Gelişmiş analiz

Machine Learning ve Azure Databricks gibi gelişmiş analiz hizmetleriyle tümleştirin. Milyonlarca cihazdan ham verileri Time Series Insights. Azure Analytics Hizmetleri paketi tarafından sorunsuz şekilde kullanılabilecek bağlamsal verileri ekler.

[![Analizi](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Gelişmiş analiz ve makine öğrenimi, büyük hacimlerden oluşan verileri kullanır ve işler. Bu veriler, veri odaklı kararlar almak ve tahmine dayalı analiz yapmak için kullanılır. IoT kullanım durumlarında, gelişmiş analiz algoritmaları Milyonlarca cihazdan toplanan verilerden bilgi edinirsiniz. Bu cihazlar her saniye birden çok kez veri iletir. IoT cihazlarından toplanan veriler RAW olur. Cihazın konumu ve algılayıcı okuma birimi gibi bağlamsal bilgiler eksiktir. Sonuç olarak, ham verilerin doğrudan gelişmiş analiz için kullanılması zordur.

Time Series Insights IoT verileri ile gelişmiş analizler arasındaki boşluğu iki basit ve ekonomik yollarla köprüler:

- İlk olarak, Time Series Insights IoT Hub kullanarak milyonlarca cihazdan ham telemetri verileri toplar. Bağlamsal bilgiler içeren verileri zenginleştirir ve verileri bir Parquet biçimine dönüştürür. Bu biçim, Machine Learning, Azure Databricks ve üçüncü taraf uygulamalar gibi diğer gelişmiş analiz hizmetleriyle kolayca tümleştirilebilir.

    Time Series Insights bir kuruluştaki tüm veriler için Truth kaynağı olarak görev yapabilir. Aşağı akış analizi iş yükleri için kullanacağı merkezi bir depo oluşturur. Time Series Insights neredeyse gerçek zamanlı bir depolama hizmeti olduğundan, gelişmiş analiz modelleri gelen IoT telemetri verilerinden sürekli olarak bilgi alabilir. Sonuç olarak, modeller daha doğru tahminleri yapabilir.

- İkincisi, makine öğrenimi ve tahmin modellerinin çıktısı, sonuçlarını görselleştirmek ve depolamak için Time Series Insights içine alınabilir. Bu yordam, kuruluşların modellerini iyileştirmelerine ve ince ayar yardımcı olur. Time Series Insights, eğitilen model çıkışları ile aynı düzlemde akış telemetri verilerini görselleştirmeyi basit hale getirir. Bu şekilde, veri bilimi takımlarının eğilimleri belirlemesine ve desenleri belirlemesine yardımcı olur. 

## <a name="next-steps"></a>Sonraki adımlar

- [Time Series Insights önizleme Gezgini](./time-series-insights-update-explorer.md)hakkında daha fazla bilgi edinin.
- Ortamınızı planlamak için [Time Series Insights önizleme planlamasını](./time-series-insights-update-plan.md) okuyun.
- [Time Series Insights istemci](https://github.com/Microsoft/tsiclient) belgelerini okuyun.
