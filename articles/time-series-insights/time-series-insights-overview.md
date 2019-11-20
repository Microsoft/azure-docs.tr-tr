---
title: 'Genel Bakış: ne Azure Time Series Insights? -Azure Time Series Insights | Microsoft Docs'
description: Zaman serisi verilerinin analizi ve IoT çözümleri için yeni bir hizmet olan Azure Time Series Insights'a giriş.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: aa15e9c50cb5cd694a7a6adad352459536292b85
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012584"
---
# <a name="what-is-azure-time-series-insights"></a>Azure Time Series Insights nedir?

Azure Time Series Insights, IoT cihazları tarafından oluşturulan gibi büyük miktarlarda zaman serisi verilerini depolamak, görselleştirmek ve sorgulamak için oluşturulmuştur. Bulutta zaman serisi verilerini depolamak, yönetmek, sorgulamak veya görselleştirmek istiyorsanız Time Series Insights aradığınız çözüm olabilir. 

[![Time Series Insights akış çizelgesi](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

Time Series Insights dört temel işe sahiptir:

- Azure IoT Hub ve Azure Event Hubs gibi bulut ağ geçitleriyle tamamen tümleşiktir. Bu olay kaynaklarına kolayca bağlanır ve JSON kodu içindeki veri içeren iletilerle yapıları ayrıştırarak temiz satırlara ve sütunlara uygular. Meta verileri telemetri verileriyle birleştirerek verilerinizi sütunlu bir depoda dizinler.
- Time Series Insights verilerinizin depolanmasını yönetir. Verilerin her zaman kolayca erişilebilir olduğundan emin olmak için verilerinizi 400 güne kadar belleğe ve SSD 'Ler halinde depolar. İsteğe bağlı olarak milyarlarca olayı Saniyeler içinde sorgulayabilirsiniz.
- Time Series Insights, Time Series Insights Gezgini aracılığıyla kullanıma hazır görselleştirme sağlar. 
- Time Series Insights, hem Time Series Insights Gezgininde hem de zaman serisi verilerinizi özel uygulamalara eklemek için kolayca tümleştirilebilen API 'Leri kullanarak bir sorgu hizmeti sağlar.

Dahili tüketim veya harici müşterilerin kullanması için bir uygulama oluşturuyorsanız, arka uç olarak Time Series Insights kullanabilirsiniz. Zaman serisi verilerini indekslemek, depolamak ve toplamak için kullanabilirsiniz. En üstte özel bir görselleştirme ve Kullanıcı deneyimi oluşturmak için [istemci SDK 'sını](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)kullanın. Time Series Insights ayrıca, bu özelleştirilmiş senaryoları etkinleştirmek için birkaç [sorgu API](how-to-shape-query-json.md) 'si ile donatılmıştır.

Zaman serisi verileri bir varlığın veya işlemin zaman içindeki değişimini gösterir. Zaman serisi verileri zaman damgalarına göre dizinlenmiştir ve zaman, bu verilerin düzenlendiği en anlamlı eksendir. Zaman serisi verileri genellikle sıralı bir sıraya ulaşır, bu nedenle veritabanınıza yönelik bir güncelleştirme yerine ekleme olarak değerlendirilir.

Büyük birimlerde zaman serisi verilerini depolamak, dizinlemek, sorgulamak, analiz etmek ve görselleştirmek için bir sorun olabilir.
Azure Time Series Insights her yeni olayı bir satır olarak yakalar ve depolar ve değişiklik zaman içinde etkili bir şekilde ölçülür. Sonuç olarak, gelecekteki değişikliği tahmin etmeye yardımcı olmak için geçmişte Öngörüler çizmek üzere geriye bakabilirsiniz.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Bulut tabanlı IoT Analizi platformu Azure Time Series Insights hakkında daha fazla bilgi edinin.</br>

[![VIDEOSU](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Birincil senaryolar

- Zaman serisi verilerini ölçeklenebilir bir şekilde depolayın. 

   Time Series Insights'ın temelinde zaman serisi verilerine göre tasarlanmış bir veritabanı bulunur. Ölçeklenebilir ve tam olarak yönetildiğinden Time Series Insights olayları depolama ve yönetme işini işler.

- Verilerin neredeyse gerçek zamanlı olarak araştırıp. 

   Time Series Insights, bir ortamda akış yapan tüm verileri görselleştirtiren bir gezgin sağlar. Bir olay kaynağına bağlandıktan kısa süre sonra Time Series Insights içindeki olay verilerini görüntüleyebilir, inceleyebilir ve sorgulayabilirsiniz. Veriler, bir cihazın beklenen şekilde veri yayıp yaymadığını ve sistem durumu, üretkenlik ve genel verimlilik için bir IoT varlığını izlemesini doğrulamanıza yardımcı olur. 

- Kök neden analizi gerçekleştirin ve anormallikleri tespit edin.

   Time Series Insights, mimari ve perspektif görünümleri gibi araçlara sahiptir ve çok adımlı kök neden analizini kaydeder. Time Series Insights ayrıca, Time Series Insights Explorer 'da uyarıları görüntüleyebilmeniz ve gerçek zamanlı olarak algılanabilmeleri için Azure Stream Analytics gibi uyarı hizmetleri ile de kullanılabilir. 

- Birden çok varlık veya site karşılaştırması için farklı konumlardan akış yapan zaman serisi verilerinin genel bir görünümünü elde edin.

   Bir Time Series Insights ortamına birden fazla olay kaynağı bağlayabilirsiniz. Bu şekilde, birden çok, farklı konumdan, neredeyse gerçek zamanlı olarak akış yapan verileri görüntüleyebilirsiniz. Kullanıcılar, iş liderleriyle veri paylaşmak için bu görünürlüğden yararlanabilir. Sorunları çözmeye, en iyi uygulamaları uygulamanıza ve dersleri paylaşmanıza yardımcı olmak üzere uzmanlığı uygulayabilen etki alanı uzmanlarıyla daha iyi işbirliği yapabilirler.

- Time Series Insights en üstünde bir müşteri uygulaması oluşturun. 

   Time Series Insights, zaman serisi verilerini kullanan uygulamalar oluşturmak için kullanabileceğiniz REST sorgu API 'Lerini kullanıma sunar.

## <a name="capabilities"></a>Özellikler

- **Hızlıca kullanmaya**başlayın: Azure Time Series Insights ön veri hazırlığı gerektirmez, bu sayede IoT Hub 'ınızda veya Olay Hub 'ınızdaki milyonlarca olaya hızlıca bağlanabilirsiniz. Bağlandıktan sonra, IoT çözümlerinizi hızla doğrulamak için sensör verilerini görselleştirin ve bunlarla etkileşim kurabilirsiniz. Kod yazmadan verilerinizle etkileşim kurabilir ve yeni bir dil öğrenmeniz gerekmez. Time Series Insights, ileri düzey kullanıcılar için ayrıntılı, serbest metin bir sorgu yüzeyi sağlar ve sonra araştırmayı işaret edin.

- **Neredeyse gerçek zamanlı**içgörüler: Time Series Insights, tek dakikalık bir gecikmeyle günde milyonlarca algılayıcı olayını alabilir. Time Series Insights, algılayıcı verilerinize ilişkin Öngörüler elde etmenize yardımcı olur. Eğilimleri ve anormallikleri belirlemek, kök neden analizleri gerçekleştirmek ve maliyetli kapalı kalma süresini önlemek için bu uygulamayı kullanın. Gerçek zamanlı ve geçmiş veriler arasındaki çapraz bağıntı, verilerdeki gizli eğilimleri bulmanıza yardımcı olur.

- **Özel çözümler oluşturun**: mevcut uygulamalarınıza Azure Time Series Insights verileri ekleyin. Ayrıca, Time Series Insights REST API 'Leriyle yeni özel çözümler de oluşturabilirsiniz. Diğer kişilerin de içgörülerinizi incelemesini sağlamak için paylaşabileceğiniz kişiselleştirilmiş görünümler oluşturun.

- **Ölçeklenebilirlik**: Zaman Serisi Görüşleri, ölçekli olarak IoT'yi destekleyecek şekilde tasarlanmıştır. Günde 1 milyon ile 100 milyon arası olay alabilir ve varsayılan saklama süresi 31 gündür. Canlı veri akışlarını, geçmiş verilerin yanı sıra neredeyse gerçek zamanlı olarak görselleştirebilir ve çözümleyebilirsiniz.

## <a name="get-started"></a>Başlarken

Başlamak için aşağıdaki adımları izleyin.

1. Azure portal bir Time Series Insights ortamı sağlayın.
1. IoT Hub 'ı veya Olay Hub 'ı gibi bir olay kaynağına bağlanın. 
1. Başvuru verilerini karşıya yükleyin. Bu ek bir hizmet değildir.
1. Time Series Insights gezgininde verilerinizi dakikalar halinde görün.

## <a name="time-series-insights-explorer"></a>Time Series Insights gezgini

Bu diyagramda, Time Series Insights Gezgini aracılığıyla görüntülenen zaman serisi görüşleri verilerinin bir örneği gösterilmektedir.

![Time Series Insights gezgini](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Sonraki adımlar

- Azure Time Series Insights genel kullanıma hazır [ücretsiz tanıtım ortamına](./time-series-quickstart.md)göz atın.
- Time Series Insights ortamınızı nasıl [planlayacağınızı](time-series-insights-environment-planning.md) öğrenin.
