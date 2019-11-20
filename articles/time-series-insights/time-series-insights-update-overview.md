---
title: 'Genel Bakış: Azure Time Series Insights önizlemesi nedir? -Azure Time Series Insights | Microsoft Docs'
description: Azure Time Series Insights önizlemede önemli iyileştirmeler hakkında bilgi edinin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: cde846152ff183db857b8a0549fafd24700ffff9
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014738"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Azure Time Series Insights Önizleme nedir?

Azure Time Series Insights önizlemesi, uçtan uca bir hizmet olarak platform (PaaS) teklifiyle yapılır. Bu özelliği, Nesnelerin İnterneti (IoT) ölçekli verileri toplamak, işlemek, depolamak, analiz etmek ve sorgulamak için kullanabilirsiniz. Bu, zaman serisi için yüksek düzeyde olan ve en iyileştirilmiş veriler. 

Time Series Insights, geçici veri araştırması ve işletimsel analizler için tasarlanmıştır. Endüstriyel IoT dağıtımlarının geniş ihtiyaçlarını karşılayan genişletilebilir ve özelleştirilmiş bir hizmet sunumudur.

## <a name="video"></a>Video

Azure Time Series Insights önizleme hakkında daha fazla bilgi edinin.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>IoT verilerinin tanımı

Sektör açısından yoğun kuruluşlarda endüstriyel IoT verileri genellikle endüstriyel bir ayarda cihazların ve algılayıcıların farklılaştırmasından dolayı yapısal tutarlılık içermemekte değildir. Bu akışların verileri, önemli boşluklar ve bazen bozulmuş iletiler ve yanlış okumalar ile belirlenir. IoT verileri genellikle ilk taraf veya üçüncü kaynaklardan (örneğin, uçtan uca iş akışları için bağlam ekleyen) gelen ek veri girişlerinin bağlamında anlamlıdır. Hava durumu verileri gibi üçüncü taraf veri kaynaklarından gelen girişler, belirli bir yüklemede telemetri akışlarının sağlanmasına yardımcı olabilir. 

Tüm bu işlemler, yalnızca işletimsel ve iş amaçları için verilerin bir bölümü için kullanılır ve analizler için contextuselleştirilmesi gerekir. Endüstriyel veriler genellikle daha uzun süre içinde daha fazla bilgi sahibi olmak için historicized eğilimleri anlamak ve eğilimleri ilişkilendirmek için kullanılır. Toplanan IoT verilerini eyleme dönüştürülebilir içgörüler halinde açmak şunları gerektirir: 

* Verilerin çözümlenmesi, filtreleneceği, enterpola, dönüştürülemesinde ve analiz amacıyla hazırlanması için veri işleme.
* Verileri normalleştirerek, diğer bir deyişle verileri normalleştirime ve anlayan bir yapı.
* İşlenen (veya türetilen) verilerin ve ham verilerin uzun veya sonsuz tutulması için ekonomik depolama.

Bu veriler, iş analizi ve raporlama için tutarlı, kapsamlı, güncel ve doğru bilgiler sağlar.

Aşağıdaki görüntüde tipik bir IoT veri akışı gösterilmektedir.

[![IoT veri akışı](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Endüstriyel IOT için Azure Time Series Insights

IoT yatay, üretim, oto ve enerji, yardımcı programlar, akıllı binalar ve danışmanlık gibi çeşitli sektör segmentlerini kapsayan müşterilerle aynıdır. Büyük ölçekli IoT verilerine hedeflenmiş kapsamlı analizler sağlayan, bulut tabanlı bu çok sayıda endüstriyel IoT Market genelinde gelişmeye devam etmektedir. 

Azure Time Series Insights, zaman serisi verilerinin, varlık tabanlı öngörülerin ve bulma için en iyi sınıf kullanıcı deneyiminin yerleşik olarak belirlenmesi için zengin anlam modellemesi sunarak bu Pazar gereksinimini ortadan kaldırarak, popüler, anomali algılama ve operasyonel zeka. 

Etkileşimli veri araştırma olanaklarımızla birleştirilmiş bir zengin işletimsel analiz platformu, IoT varlıklarından toplanan verilerden daha fazla değer elde etmek için Time Series Insights kullanabilirsiniz. Önizleme teklifi şunları destekler: 

* Sıcak ve soğuk analiz desteğiyle çok katmanlı depolama çözümü, müşterilere, normal veriler üzerinde etkileşimli analizler ve geçmiş verilerin deklikler üzerinde işletimsel zeka 'lar arasında veri yönlendirme seçeneği sağlar. 

    *   Sık gerçekleştirilen ve kısa süre içinde çok sayıda sorgu daha kısa sürede gerçekleştirmeye yönelik son derece etkileşimli bir sıcak analiz çözümü 
    *   Azure depolama 'yı temel alan ölçeklenebilir, performansı en iyi duruma getirilmiş bir zaman serisi veri Gölü ve müşterilerin, zaman serisi verilerinin Saniyeler içinde eğilimini eğilim altına almalarına olanak tanır. 

* Varlıkların ve cihazların türetilmiş ve ham sinyalleriyle ilişkili etki alanını ve meta verileri açıklayan anlam modeli desteği.

* Müşterinin sahip olduğu Azure depolama hesabında geçmiş zaman serisi verilerini depolamak için esnek analiz platformu, böylelikle müşterilerin IoT verilerinin sahipliğine sahip olmasını sağlar. Veriler, tahmine dayalı analiz, makine öğrenimi ve Spark dahil tanıdık teknolojiler kullanılarak gerçekleştirilen diğer özel hesaplamalar dahil olmak üzere çeşitli veri senaryolarında bağlantı ve birlikte çalışabilirliği sağlayan açık kaynaklı Apache Parquet biçiminde depolanır. Databricks ve Jupyıter.

* Gelişmiş sorgu API 'Leri ve Kullanıcı deneyimi ile varlık tabanlı veri içgörülerini enterpolasyon, skaler ve toplama işlevleri, kategorik değişkenler, dağılım çizimleri ve zaman değiştirme zamanı desteğiyle zengin, geçici veri analiziyle birleştiren zengin analiz derinlemesine analize yönelik dizi sinyalleri.

*   Kurumsal IoT müşterilerimiz için ölçek, performans, güvenlik ve güvenilirlik ihtiyaçlarını destekleyecek kurumsal sınıf platformu.

* Uçtan uca analiz için genişletilebilirlik ve tümleştirme desteği. Time Series Insights çeşitli veri senaryoları için genişletilebilir bir analiz platformu sağlar. Time Series Insights Power BI Bağlayıcısı, müşterilerin tek bir cam bölmesinde bı ve zaman serisi analizlerinin Birleşik görünümünü almak için doğrudan Power BI Time Series Insights yaptıkları sorguları almasını sağlar.

Aşağıdaki diyagramda, üst düzey veri akışı gösterilmektedir.

  [![temel özellikleri](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights, veri işleme, depolama (veri ve meta veriler) ve sorgu için, müşterilerin kullanımını iş taleplerine uyacak şekilde ayarlamanıza olanak tanıyan ölçeklenebilir bir Kullandıkça Öde fiyatlandırma modeli sunar. 
 
Bu temel Endüstriyel IoT özelliklerine giriş ile Time Series Insights, aşağıdaki başlıca avantajları da sağlar.  

| | |
| ---| ---|
| IoT ölçeğinde zaman serisi verileri için çok katmanlı depolama | Verilerin içe aktarılmasıyla ilgili bir paylaşılan veri işleme işlem hattı sayesinde, verileri hem normal hem de soğuk depolara aktarabilirsiniz. Büyük hacimli verileri depolamak için etkileşimli sorgular ve soğuk mağaza için ısınma mağazasını kullanın. Yüksek performanslı varlık tabanlı sorgulardan nasıl yararlanabilmeniz hakkında daha fazla bilgi için bkz. [sorgular](./time-series-insights-update-tsq.md). |
| Ham telemetri eklemek ve varlık tabanlı Öngörüler türetmeye yönelik zaman serisi modeli | Tarih serisi modelini, zaman serisi verileriniz için örnekler, hiyerarşiler, türler ve değişkenler oluşturmak için kullanabilirsiniz. Zaman serisi modeli hakkında daha fazla bilgi edinmek için bkz. [zaman serisi modeli](./time-series-insights-update-tsm.md).  |
| Diğer veri çözümleriyle sorunsuz ve sürekli tümleştirme | Time Series Insights soğuk depodaki veriler, açık kaynaklı Apache Parquet dosyalarında [depolanır](./time-series-insights-update-storage-ingress.md) . Bu, iş zekası, gelişmiş makine öğrenimi ve tahmine dayalı analiz gibi senaryolar için diğer veri çözümleri, 1 veya 3. taraf ile veri tümleştirmeyi sağlar. |
| Neredeyse gerçek zamanlı veri araştırması | [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md) Kullanıcı deneyimi, alma işlem hattı aracılığıyla tüm veri akışı için görselleştirme sağlar. Bir olay kaynağını bağlandıktan sonra olay verilerini görüntüleyebilir, keşfedebilir ve sorgulayabilirsiniz. Bu şekilde, bir cihazın verileri beklendiği gibi yayıp yaymadığını doğrulayabilirsiniz. Ayrıca, bir IoT varlığını sistem durumu, üretkenlik ve genel verimlilik için izleyebilirsiniz. | 
| Genişletilebilirlik ve Tümleştirme | Azure Time Series Insights Power BI bağlayıcı tümleştirmesi, **dışa aktarma** seçeneği aracılığıyla doğrudan zaman serisi Gezgini kullanıcı deneyiminde kullanılabilir ve müşterilerin Kullanıcı deneyimimizde oluşturdukları zaman serisi sorgularını doğrudan dışarı aktarmalarına izin verir. Power BI masaüstüne ekleyin ve zaman serisi grafiklerini diğer bı analizlerinin yanı sıra görüntüleyin. Bu, IoT zaman serisi dahil çeşitli veri kaynaklarından analizler üzerinde tek bir cam bölmesi sunarak Power BI yatırım yapan endüstriyel IoT kuruluşları için bir yeni senaryolar sınıfına açar. | 
| Time Series Insights platformunda oluşturulan özel uygulamalar | Time Series Insights [JavaScript SDK 'sını](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)destekler. SDK, zengin denetimleri ve sorguları yönelik Basitleştirilmiş erişim sağlar. İş gereksinimlerinize uyacak şekilde Time Series Insights en üstünde özel IoT uygulamaları oluşturmak için SDK 'Yı kullanın. Ayrıca, verileri özel IoT uygulamalarına yönlendirmek için doğrudan Time Series Insights [sorgu API 'lerini](./time-series-insights-update-tsq.md) de kullanabilirsiniz. |

## <a name="next-steps"></a>Sonraki adımlar

Azure Time Series Insights Preview ile çalışmaya başlama:

> [!div class="nextstepaction"]
> [Hızlı Başlangıç Kılavuzu](./time-series-insights-update-quickstart.md)

Kullanım örnekleri hakkında bilgi edinin:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Önizleme kullanım örnekleri](./time-series-insights-update-use-cases.md)