---
title: 'Genel Bakış: Azure Time Series Insights Gen2 nedir? -Azure Time Series Insights Gen2 | Microsoft Docs'
description: Azure Time Series Insights Gen2 içindeki değişiklikler, iyileştirmeler ve özellikler hakkında bilgi edinin.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 4ff26289962aa06f9638644e28da23b646554281
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865324"
---
# <a name="what-is-azure-time-series-insights-gen2"></a>Azure Time Series Insights 2. Nesil nedir?

Azure Time Series Insights Gen2, güçlü yeteneklerini mevcut iş akışınız veya uygulamanızla bütünleştirmek için en iyi sınıf kullanıcı deneyimleri ve zengin API 'Ler sağlayan açık ve ölçeklenebilir uçtan uca bir IoT Analytics hizmetidir.

Bu özelliği, Nesnelerin İnterneti (IoT) ölçekli verileri toplamak, işlemek, depolamak, sorgulamak ve görselleştirmek için kullanabilirsiniz. bu veriler, zaman serisi için yüksek düzeyde bağlanabilir ve en iyi duruma getirilmiştir.

Azure Time Series Insights Gen2, geçici veri araştırması ve operasyonel analizler için tasarlandığından gizli eğilimleri ortaya çıkarabilir, anormallikleri açabilir ve kök neden analizi gerçekleştirebilir. Endüstriyel IoT dağıtımlarının geniş ihtiyaçlarını karşılayan açık ve esnek bir tekliftir.

## <a name="video"></a>Video

Azure Time Series Insights Gen2 hakkında daha fazla bilgi edinin.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Using-Azure-Time-Series-Insights-to-create-an-Industrial-IoT-analytics-platform/player]

## <a name="definition-of-iot-data"></a>IoT verilerinin tanımı

Sektör açısından yoğun kuruluşlarda endüstriyel IoT verileri genellikle endüstriyel bir ayarda cihazların ve algılayıcıların farklılaştırmasından dolayı yapısal tutarlılık içermemekte değildir. Bu akışların verileri, önemli boşluklar ve bazen bozulmuş iletiler ve yanlış okumalar ile belirlenir. IoT verileri genellikle ilk taraf veya üçüncü kaynaklardan (örneğin, uçtan uca iş akışları için bağlam ekleyen) gelen ek veri girişlerinin bağlamında anlamlıdır. Hava durumu verileri gibi üçüncü taraf veri kaynaklarından gelen girişler, belirli bir yüklemede telemetri akışlarının sağlanmasına yardımcı olabilir.

Tüm bu işlemler, yalnızca işletimsel ve iş amaçları için verilerin bir bölümü için kullanılır ve analizler için contextuselleştirilmesi gerekir. Endüstriyel veriler genellikle daha uzun süre içinde daha fazla bilgi sahibi olmak için historicized eğilimleri anlamak ve eğilimleri ilişkilendirmek için kullanılır. Toplanan IoT verilerini eyleme dönüştürülebilir içgörüler halinde açmak şunları gerektirir:

* Verilerin çözümlenmesi, filtreleneceği, enterpola, dönüştürülemesinde ve analiz amacıyla hazırlanması için veri işleme.
* Verileri normalleştirerek, diğer bir deyişle verileri normalleştirime ve anlayan bir yapı.
* İşlenen (veya türetilen) verilerin ve ham verilerin uzun veya sonsuz tutulması için ekonomik depolama.

Bu veriler, iş analizi ve raporlama için tutarlı, kapsamlı, güncel ve doğru bilgiler sağlar.

Aşağıdaki görüntüde tipik bir IoT veri akışı gösterilmektedir.

[![IoT veri akışı](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-gen2-for-industrial-iot"></a>Endüstriyel IoT için Azure Time Series Insights Gen2

IoT yatay, üretim, oto ve enerji, yardımcı programlar, akıllı binalar ve danışmanlık gibi çeşitli sektör segmentlerini kapsayan müşterilerle aynıdır. Büyük ölçekli IoT verilerine hedeflenmiş kapsamlı analizler sağlayan, bulut tabanlı bu çok sayıda endüstriyel IoT Market genelinde gelişmeye devam etmektedir.

Azure Time Series Insights Gen2, zaman serisi verilerinin, varlık tabanlı öngörülerin ve bulma, trma, anomali algılama ve operasyonel zeka için en iyi kullanıcı deneyiminin bir arada belirlenmesi için zengin anlam modellemeye sahip bir anahtar, uçtan uca IoT analizi çözümü sunarak bu Pazar gereksinimini ortadan kaldırabilirsiniz.

Etkileşimli veri araştırma olanaklarımızla birleştirilmiş bir zengin işletimsel analiz platformu, IoT varlıklarından toplanan verilerden daha fazla değer elde etmek için Azure Time Series Insights Gen2 kullanabilirsiniz. Gen2 teklifi şunları destekler:

* Sıcak ve soğuk analiz desteğiyle çok katmanlı depolama çözümü, müşterilere, normal veriler üzerinde etkileşimli analizler ve geçmiş verilerin deklikler üzerinde işletimsel zeka 'lar arasında veri yönlendirme seçeneği sağlar.

  * Sık gerçekleştirilen ve kısa süre içinde çok sayıda sorgu daha kısa sürede gerçekleştirmeye yönelik son derece etkileşimli bir sıcak analiz çözümü
  * Azure depolama 'yı temel alan ölçeklenebilir, performansı en iyi duruma getirilmiş bir zaman serisi veri Gölü ve müşterilerin, zaman serisi verilerinin Saniyeler içinde eğilimini eğilim altına almalarına olanak tanır.

* Varlıkların ve cihazların türetilmiş ve ham sinyalleriyle ilişkili etki alanını ve meta verileri açıklayan anlam modeli desteği.

* Müşterinin sahip olduğu Azure depolama hesabında geçmiş zaman serisi verilerini depolamak için esnek analiz platformu, böylelikle müşterilerin IoT verilerinin sahipliğine sahip olmasını sağlar. Veriler, tahmine dayalı analiz, makine öğrenimi ve Spark ve Databricks dahil tanıdık teknolojiler kullanılarak gerçekleştirilen diğer özel hesaplamalar dahil olmak üzere çeşitli veri senaryolarında bağlantı ve birlikte çalışabilme sağlayan açık kaynaklı Apache Parquet biçiminde depolanır.

* Zengin çözünürlüklü, ölçeklenebilir sorgu API 'Leri ve Kullanıcı deneyimi ile varlık tabanlı veri içgörüleri birleştiren zengin analizler; enterpolasyon, skaler ve toplama işlevleri, kategorik değişkenler, dağılım çizimleri ve ayrıntılı analiz için zaman serisi sinyalleriyle ilgili zaman dizisi sinyalleri için destek sunar.

* Kurumsal IoT müşterilerimiz için ölçek, performans, güvenlik ve güvenilirlik ihtiyaçlarını destekleyecek kurumsal sınıf platformu.

* Uçtan uca analiz için genişletilebilirlik ve tümleştirme desteği. Azure Time Series Insights Gen2, çeşitli veri senaryoları için genişletilebilir bir analiz platformu sağlar. Power BI Bağlayıcısı, müşterilerin tek bir cam bölmesinde bı ve zaman serisi analizlerinin Birleşik görünümünü almak için Azure Time Series Insights Gen2 içinde yaptıkları sorguları doğrudan Power BI 'e geçirmesine olanak sağlar.

Aşağıdaki diyagramda, üst düzey veri akışı gösterilmektedir.

  [![Temel işlevler](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights Gen2, veri işleme, depolama (veri ve meta veriler) ve sorgu için, müşterilerin kullanımını iş taleplerine uyacak şekilde ayarlamanıza olanak tanıyan ölçeklenebilir bir Kullandıkça Öde fiyatlandırma modeli sunar.

Bu temel Endüstriyel IoT özelliklerine giriş ile Azure Time Series Insights Gen2, aşağıdaki başlıca avantajları da sağlar:  

| Özellik | Avantaj |
| ---| ---|
| IoT ölçeğinde zaman serisi verileri için çok katmanlı depolama | Verilerin içe aktarılmasıyla ilgili bir paylaşılan veri işleme işlem hattı sayesinde, verileri hem normal hem de soğuk depolara aktarabilirsiniz. Büyük hacimli verileri depolamak için etkileşimli sorgular ve soğuk mağaza için ısınma mağazasını kullanın. Yüksek performanslı varlık tabanlı sorgulardan nasıl yararlanabilmeniz hakkında daha fazla bilgi için bkz. [sorgular](./concepts-query-overview.md). |
| Ham telemetri eklemek ve varlık tabanlı Öngörüler türetmeye yönelik zaman serisi modeli | Tarih serisi modelini, zaman serisi verileriniz için örnekler, hiyerarşiler, türler ve değişkenler oluşturmak için kullanabilirsiniz. Zaman serisi modeli hakkında daha fazla bilgi edinmek için bkz. [zaman serisi modeli](./concepts-model-overview.md).  |
| Diğer veri çözümleriyle sorunsuz ve sürekli tümleştirme | Azure Time Series Insights Gen2 soğuk deposundaki veriler açık kaynaklı Apache Parquet dosyalarında [depolanır](./concepts-storage.md) . Bu, iş zekası, gelişmiş makine öğrenimi ve tahmine dayalı analiz gibi senaryolar için diğer veri çözümleri, 1 veya 3. taraf ile veri tümleştirmeyi sağlar. |
| Neredeyse gerçek zamanlı veri araştırması | [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md) Kullanıcı deneyimi, alma işlem hattı aracılığıyla tüm veri akışı için görselleştirme sağlar. Bir olay kaynağını bağlandıktan sonra olay verilerini görüntüleyebilir, keşfedebilir ve sorgulayabilirsiniz. Bu şekilde, bir cihazın verileri beklendiği gibi yayıp yaymadığını doğrulayabilirsiniz. Ayrıca, bir IoT varlığını sistem durumu, üretkenlik ve genel verimlilik için izleyebilirsiniz. |
| Genişletilebilirlik ve Tümleştirme | Power BI Bağlayıcısı tümleştirmesi, **dışa aktarma** seçeneği aracılığıyla doğrudan zaman serisi Gezgin Kullanıcı deneyiminde kullanılabilir ve müşterilerin kendi Kullanıcı deneyimimizde oluşturdukları zaman serisi sorgularını doğrudan Power BI masaüstüne dışarı aktarıp zaman serisi GRAFIKLERINI diğer bı analizlerinin yanı sıra görüntülemesini sağlar. Bu, IoT zaman serisi dahil çeşitli veri kaynaklarından analizler üzerinde tek bir cam bölmesi sunarak Power BI yatırım yapan endüstriyel IoT kuruluşları için bir yeni senaryolar sınıfına açar. |
| Azure Time Series Insights Gen2 platformunda oluşturulan özel uygulamalar | Azure Time Series Insights Gen2, [JavaScript SDK 'sını](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)destekler. SDK, sorgulara zengin denetimler ve Basitleştirilmiş erişim sağlar. İş ihtiyaçlarınıza uygun Azure Time Series Insights Gen2 en üstünde özel IoT uygulamaları oluşturmak için SDK 'Yı kullanın. Ayrıca, verileri özel IoT uygulamalarına yönlendirmek için doğrudan Azure Time Series Insights Gen2 [sorgu API 'lerini](./concepts-query-overview.md) de kullanabilirsiniz. |

## <a name="region-availability"></a>Bölge kullanılabilirliği

Azure Time Series Insights kullanılabilir olduğunu öğrenmek için [bölgeye göre Azure küresel altyapı ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=time-series-insights) sayfasını ziyaret edin.

Azure Time Series Insights tek bir bölgede bulunur. Bununla birlikte, verilerin bölge dışında çoğaltılabileceği aşağıdaki bölgeler dışında, müşteri verilerini müşterinin hizmet örneğini dağıttığı bölge dışında depolar/işler.

* Doğu Asya
* Güneydoğu Asya
*   Güneydoğu Avustralya
*   West Europe
*   Güney Birleşik Krallık
*   Batı ABD
*   Orta Fransa
*   Orta Kanada

## <a name="next-steps"></a>Sonraki adımlar

Azure Time Series Insights Gen2 kullanmaya başlama:

> [!div class="nextstepaction"]
> [Hızlı başlangıç kılavuzu](./quickstart-explore-tsi.md)

Kullanım örnekleri hakkında bilgi edinin:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 kullanım örnekleri](./overview-use-cases.md)
