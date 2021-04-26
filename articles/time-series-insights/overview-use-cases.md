---
title: Gen2 kullanım örnekleri-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 kullanım örnekleri hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7d97958c5fd1274495da88c064b63e59e354f691
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606972"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Azure Time Series Insights Gen2 kullanım örnekleri

Bu makalede Azure Time Series Insights Gen2 için bazı yaygın kullanım durumları özetlenmektedir. Bu makaledeki öneriler, Azure Time Series Insights Gen2 ile uygulamalarınızı ve çözümlerinizi geliştirmek için bir başlangıç noktası olarak görev yapar.

Özellikle, bu makalede aşağıdaki sorulara yanıt verilmiştir:

* Azure Time Series Insights Gen2 için genel kullanım örnekleri nelerdir?
* [Veri araştırması ve görsel anomali algılama](#data-exploration-and-visual-anomaly-detection)Için Azure Time Series Insights Gen2 kullanmanın avantajları nelerdir?
* [İşletimsel analiz ve işlem verimliliği](#operational-analysis-and-driving-process-efficiency)Için Azure Time Series Insights Gen2 kullanmanın avantajları nelerdir?
* [Gelişmiş analiz](#advanced-analytics)Için Azure Time Series Insights Gen2 kullanmanın avantajları nelerdir?

Bu kullanım senaryolarına genel bir bakış aşağıdaki bölümlerde açıklanmıştır.

## <a name="introduction"></a>Giriş

Azure Time Series Insights Gen2, uçtan uca, bir hizmet olarak platform sunumudur. Yüksek düzeyde tanımlı, zaman serisi iyileştirilmiş IoT ölçeğinde verileri toplamak, işlemek, depolamak, çözümlemek ve sorgulamak için kullanılır. Geçici veri araştırması ve işletimsel analizler için idealdir. Azure Time Series Insights Gen2, endüstriyel IoT dağıtımlarının geniş ihtiyaçlarını karşılayan benzersiz şekilde genişletilebilir, özelleştirilmiş bir hizmet sunumudur.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Veri araştırması ve görsel anomali algılama

Milyarlarca olayı hızla araştırıp çözümleyin ve verilerinizdeki gizli eğilimleri bulun. Azure Time Series Insights Gen2, IoT ve DevOps analiz iş yükleriniz için neredeyse gerçek zamanlı performansı sunar.

[![Veri Gezgini](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Çoğu müşteri, öngörü kazanmak için gereken en kısa süreyi, Azure Time Series Insights Gen2 ' in en az bir bölümünü kabul etmiş olursunuz:

* Azure Time Series Insights Gen2 ön veri hazırlığı gerektirmez.
* Bu, Azure IoT Hub veya Azure Event Hubs örneklerinde birkaç dakika içinde bağlantı kurmak için hızlı bir şekilde çalışmaktadır.
* Bağlandıktan sonra, milyarlarca olayı görselleştirme ve analiz edebilir ve verilerinizdeki gizli eğilimleri keşfedebilirsiniz.

Azure Time Series Insights Gen2 sezgisel ve kullanımı basittir. Tek bir kod satırı yazmadan verilerinizle etkileşim kurabilirsiniz. Ayrıca, öğrenmeniz gereken yeni bir dil yoktur, ancak Azure Time Series Insights Gen2, SQL 'e alışkın olan ileri düzey kullanıcılar için ayrıntılı metin tabanlı bir sorgulama dili sağlar. Ayrıca, novıces için seçme ve tıklama araştırması sağlar.

Müşteriler, varlık ile ilgili sorunları hızlı bir şekilde tanılamaya yönelik hızdan yararlanabilir. Bir IoT çözümünde hatanın kök nedenine ulaşmak için DevOps analizini gerçekleştirebilir. Bunlar ayrıca, veri bilimi girişimlerinin bir parçası olarak daha fazla araştırma için bayrak eklenecek alanlara da tanımlayabilir.

Azure Time Series Insights Gen2 ' de depolanan verilerle etkileşimde bulunmak için üç temel yol vardır:

* Kullanmaya başlamanın ilk ve en kolay yolu Azure Time Series Insights Gen2 Explorer ' dır. Bunu kullanarak tüm IoT verilerinizi tek bir yerde hızla görselleştirebilirsiniz. Verilerinize ilişkin eğilimleri belirlemenize yardımcı olması için ısı haritası gibi araçlar sağlar. Ayrıca bir perspektif görünümü de sağlar. Tek bir panoda bir veya daha fazla Azure Time Series Insights Gen2 ortamından en fazla dört görünüm karşılaştırmak için bunu kullanın. Pano size tüm konumlarınızın zaman serisi verilerinin bir görünümünü sunar. [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md)hakkında daha fazla bilgi edinin. Ortamınızı planlamak için [Azure Time Series Insights Gen2 planlamayı](./how-to-plan-your-environment.md)okuyun.

* Başlangıç yapmanın ikinci yolu, Web uygulamanıza güçlü grafikler ve grafikler eklemek için JavaScript SDK 'sını kullanmaktır. Yalnızca birkaç satır kodla, güçlü sorgular yazabilirsiniz. Çizgi grafiklerini, pasta grafiklerini, çubuk grafiklerini, ısı haritalarını, veri kılavuzlarını ve daha fazlasını doldurmak için bunları kullanın. Bu öğelerin tümü SDK kullanılarak kullanıma hazır. SDK Ayrıca Azure Time Series Insights Gen2 sorgu API 'Lerini de soyutlar. Bunları, bir panoda göstermek istediğiniz verileri sorgulamak için SQL benzeri koşullar yazmak üzere kullanabilirsiniz. Karma sunum katmanı çözümleri için Azure Time Series Insights Gen2 parametreli URL 'Ler sunar. Verilerle ilgili ayrıntılı bilgi için Azure Time Series Insights Gen2 Explorer ile sorunsuz bağlantı noktaları sağlar.

  * JavaScript SDK 'Sı hakkında daha fazla bilgi edinmek için [js istemci kitaplığı](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) ve [örnek istemci](https://github.com/Microsoft/tsiclient) belgeleri hakkında bilgi edinin.

  * [Azure Time Series Insights Gen2 Explorer 'da verileri görselleştirmeyi](./concepts-ux-panels.md)Inceleyerek URL 'leri ve yeni kullanıcı arabirimini paylaşma hakkında daha fazla bilgi edinin.

* Başlangıç yapmanın üçüncü yolu Azure Time Series Insights Gen2 ' de depolanan verileri sorgulamak için güçlü API 'Leri kullanmaktır. Azure Time Series Insights Gen2,,, ve gibi zamana bağlı işleçlere sahiptir `from` `to` `first` `last` . ,,,,, `average` `sum` `min` `max` `time-weighted average` `time-weighted sum` , Vb. toplamaları ve dönüştürmeleri vardır. Ayrıca filtrelemeye, aritmetik ve Boole işleçlerine, skalar işlevlere vb. izin verir. Tüm bu işleçler, aşağı akış uygulamalarının verilerinize yönelik ilginç eğilimleri ve desenleri hızlı bir şekilde bulmasını sağlar. Onları, anormal görselleştirmelere göre doldurmak için kullanın.

## <a name="operational-analysis-and-driving-process-efficiency"></a>İşletimsel analiz ve işlem verimliliğini işleme

İşlemsel verimlilik ve ölçüdeki donanımların sistem durumunu, kullanımını ve performansını izlemek için Azure Time Series Insights Gen2 kullanın. Azure Time Series Insights Gen2, giriş veya sorgu performansından ödün vermeden farklı ve öngörülemeyen IoT iş yüklerinin yönetilmesine yardımcı olur.

[![Ekran görüntüsü g/ç cihazları/uygulama verileri, akış işleme, işlemsel verimlilik, Intelligence/Insights ve gelişmiş analizler Azure Time Series Insights Gen2 ' de gösterilir.](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

İşlem işlemlerinden gelen verilerin akışını ve sürekli işlenmesi, doğru teknoloji veya çözümle birleştirilirse herhangi bir işi başarıyla dönüştürebilir. Bu çözümler genellikle birden çok sisteminin bir birleşimidir. Bunlar, özellikle IoT bölgesindeki, sürekli olarak değişen verilerin araştırmasını ve analizini etkinleştirir ve ortak bir model paylaşır.

Bu desenler genellikle çeşitli yerel ayarlara yayılan cihazlardan ve sensörlerden milyarlarca olay alan IoT özellikli platformlarla başlar. Bu sistemler, gerçek zamanlı Öngörüler ve eylemler elde etmek için akış verilerini işler ve analiz eder. Veriler genellikle neredeyse gerçek zamanlı ve toplu analiz için normal ve soğuk depoya arşivlenir.

Toplanan veriler, akış sorgulama ve analiz senaryoları için onu temizleştirmek ve bağlamak üzere bir dizi işleme geçer. Azure, varlık bakımı ve üretimi gibi IoT senaryolarına uygulanabilen zengin hizmetler sunar. Bu hizmetler Azure Time Series Insights Gen2, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Işlevleri, Azure Logic Apps, Azure Databricks, Azure Machine Learning ve Power BI içerir.

Çözüm mimarisi aşağıdaki şekilde elde edilebilir:

* Sınıfının en iyi güvenlik, verimlilik ve gecikme süresi için IoT Hub veya Event Hubs aracılığıyla veri alma.
* Veri işleme ve hesaplamalar gerçekleştirin. Stream Analytics, Logic Apps ve Azure Işlevleri gibi hizmetler aracılığıyla huni alınan veriler. Kullandığınız hizmet, belirli veri işleme ihtiyaçlarına bağlıdır.
* İşlem hattından hesaplanan sinyaller, depolamak ve analiz etmek için Azure Time Series Insights Gen2 'e gönderilir.

Azure Time Series Insights Gen2, geçmiş veriler üzerinde neredeyse gerçek zamanlı veri araştırması ve varlık tabanlı öngörüler sunar. İş gereksinimlerinize bağlı olarak, MapReduce ve Hive işleri, Azure Time Series Insights Gen2 Azure HDInsight 'a bağlanarak Azure Time Series Insights Gen2 ' de depolanan veriler üzerinde çalışabilir. Azure Time Series Insights Gen2 ' de depolanan veriler, Azure Time Series Insights Gen2 genel yüzey sorgu API 'Leri aracılığıyla Power BI ve diğer müşteri uygulamaları tarafından kullanılabilir. Bu veriler, derin iş ve operasyonel zeka senaryoları için kullanılabilir.

## <a name="advanced-analytics"></a>Gelişmiş analiz

Machine Learning ve Azure Databricks gibi gelişmiş analiz hizmetleriyle tümleştirin. Milyonlarca cihazdan ham verileri Gen2 Azure Time Series Insights. Azure Analytics Hizmetleri paketi tarafından sorunsuz şekilde kullanılabilecek bağlamsal verileri ekler.

[![Analiz](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Gelişmiş analiz ve makine öğrenimi, büyük hacimlerden oluşan verileri kullanır ve işler. Bu veriler, veri odaklı kararlar almak ve tahmine dayalı analiz yapmak için kullanılır. IoT kullanım durumlarında, gelişmiş analiz algoritmaları Milyonlarca cihazdan toplanan verilerden bilgi edinirsiniz. Bu cihazlar her saniye birden çok kez veri iletir. IoT cihazlarından toplanan veriler RAW olur. Cihazın konumu ve algılayıcı okuma birimi gibi bağlamsal bilgiler eksiktir. Sonuç olarak, ham verilerin doğrudan gelişmiş analiz için kullanılması zordur.

Azure Time Series Insights Gen2, IoT verileri ile gelişmiş analizler arasındaki boşluğu iki basit ve ekonomik yollarla köprüler:

* İlk olarak, Azure Time Series Insights Gen2 IoT Hub kullanarak milyonlarca cihazdan ham telemetri verileri toplar. Bağlamsal bilgiler içeren verileri zenginleştirir ve verileri bir Parquet biçimine dönüştürür. Bu biçim, Machine Learning, Azure Databricks ve üçüncü taraf uygulamalar gibi diğer gelişmiş analiz hizmetleriyle kolayca tümleştirilebilir.

    Azure Time Series Insights Gen2, bir kuruluştaki tüm veriler için Truth kaynağı olarak görev yapabilir. Aşağı akış analizi iş yükleri için kullanacağı merkezi bir depo oluşturur. Azure Time Series Insights Gen2 neredeyse gerçek zamanlı bir depolama hizmeti olduğundan, gelişmiş analiz modelleri gelen IoT telemetri verilerinden sürekli olarak bilgi alabilir. Sonuç olarak, modeller daha doğru tahminleri yapabilir.

* İkincisi, makine öğrenimi ve tahmin modellerinin çıktısı, sonuçlarını görselleştirmek ve depolamak için Azure Time Series Insights Gen2 'e dağıtılabilir. Bu yordam, kuruluşların modellerini iyileştirmelerine ve ince ayar yardımcı olur. Azure Time Series Insights Gen2, eğitilen model çıkışları ile aynı düzlemde akış telemetri verilerini görselleştirmeyi kolaylaştırır. Bu şekilde, veri bilimi takımlarının eğilimleri belirlemesine ve desenleri belirlemesine yardımcı olur.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md)hakkında daha fazla bilgi edinin.
* Ortamınızı planlamak için [Azure Time Series Insights Gen2 en iyi yöntemlerini](./how-to-plan-your-environment.md) okuyun.
* [Örnek istemci](https://github.com/Microsoft/tsiclient) belgelerini okuyun.
