---
title: 'Genel Bakış: ne Azure Time Series Insights? -Azure Time Series Insights | Microsoft Docs'
description: Zaman serisi verilerinin analizi ve IoT çözümleri için yeni bir hizmet olan Azure Time Series Insights'a giriş.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: overview
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: cebe0e018b4483a9de1771fb3171effbbd5ca59c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020070"
---
# <a name="what-is-azure-time-series-insights-gen1"></a>Azure Time Series Insights Gen1 nedir?

> [!CAUTION]
> Bu bir Gen1 makaledir.

Azure Time Series Insights, IoT cihazları tarafından oluşturulan gibi büyük miktarlarda zaman serisi verilerini depolamak, görselleştirmek ve sorgulamak için oluşturulmuştur. Bulutta zaman serisi verilerini depolamak, yönetmek, sorgulamak veya görselleştirmek istiyorsanız, büyük olasılıkla sizin için Azure Time Series Insights.

[![Azure Time Series Insights akış çizelgesi](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

Azure Time Series Insights dört ana işi vardır:

- Azure IoT Hub ve Azure Event Hubs gibi bulut ağ geçitleriyle tamamen tümleşiktir. Bu olay kaynaklarına kolayca bağlanır ve JSON kodu içindeki veri içeren iletilerle yapıları ayrıştırarak temiz satırlara ve sütunlara uygular. Meta verileri telemetri verileriyle birleştirerek verilerinizi sütunlu bir depoda dizinler.
- Azure Time Series Insights verilerinizin depolanmasını yönetir. Verilerin her zaman kolayca erişilebilir olduğundan emin olmak için verilerinizi 400 güne kadar belleğe ve SSD 'Ler halinde depolar. İsteğe bağlı olarak milyarlarca olayı Saniyeler içinde sorgulayabilirsiniz.
- Azure Time Series Insights, Azure Time Series Insights Gezgini aracılığıyla kullanıma hazır görselleştirme sağlar.
- Azure Time Series Insights, hem Azure Time Series Insights Gezgininde hem de zaman serisi verilerinizi özel uygulamalara eklemek için kolayca tümleştirilebilen API 'Leri kullanarak bir sorgu hizmeti sağlar.

Dahili tüketim veya harici müşterilerin kullanması için bir uygulama oluşturuyorsanız, arka uç olarak Azure Time Series Insights kullanabilirsiniz. Zaman serisi verilerini indekslemek, depolamak ve toplamak için kullanabilirsiniz. En üstte özel bir görselleştirme ve Kullanıcı deneyimi oluşturmak için [istemci SDK 'sını](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)kullanın. Azure Time Series Insights ayrıca, bu özelleştirilmiş senaryoları etkinleştirmek için birkaç [sorgu API](./concepts-json-flattening-escaping-rules.md) 'si ile donatılmıştır.

Zaman serisi verileri bir varlığın veya işlemin zaman içindeki değişimini gösterir. Zaman serisi verileri zaman damgalarına göre dizinlenmiştir ve zaman, bu verilerin düzenlendiği en anlamlı eksendir. Zaman serisi verileri genellikle sıralı bir sıraya ulaşır, bu nedenle veritabanınıza yönelik bir güncelleştirme yerine ekleme olarak değerlendirilir.

Büyük birimlerde zaman serisi verilerini depolamak, dizinlemek, sorgulamak, analiz etmek ve görselleştirmek için bir sorun olabilir.
Azure Time Series Insights her yeni olayı bir satır olarak yakalar ve depolar ve değişiklik zaman içinde etkili bir şekilde ölçülür. Sonuç olarak, gelecekteki değişikliği tahmin etmeye yardımcı olmak için geçmişte Öngörüler çizmek üzere geriye bakabilirsiniz.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Bulut tabanlı IoT Analizi platformu Azure Time Series Insights hakkında daha fazla bilgi edinin.</br>

[![VIDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Birincil senaryolar

- Zaman serisi verilerini ölçeklenebilir bir şekilde depolayın.

   Azure Time Series Insights, temel alınarak zaman serisi verileriyle tasarlanan bir veritabanı vardır. Ölçeklenebilir ve tam olarak yönetildiğinden Azure Time Series Insights olayları depolama ve yönetme işini işler.

- Verilerin neredeyse gerçek zamanlı olarak araştırıp.

   Azure Time Series Insights, bir ortamda akış yapan tüm verileri görselleştirtiren bir gezgin sağlar. Bir olay kaynağına bağlandıktan kısa süre sonra Azure Time Series Insights içindeki olay verilerini görüntüleyebilir, inceleyebilir ve sorgulayabilirsiniz. Veriler, bir cihazın beklenen şekilde veri yayıp yaymadığını ve sistem durumu, üretkenlik ve genel verimlilik için bir IoT varlığını izlemesini doğrulamanıza yardımcı olur.

- Kök neden analizi gerçekleştirin ve anormallikleri tespit edin.

   Azure Time Series Insights, mimari ve perspektif görünümleri gibi araçlara sahiptir ve çok adımlı kök neden analizini kaydeder. Azure Time Series Insights ayrıca, Azure Time Series Insights Explorer 'da uyarıları görüntüleyebilmeniz ve gerçek zamanlı olarak algılanabilmeleri için Azure Stream Analytics gibi uyarı hizmetleri ile de kullanılabilir.

- Birden çok varlık veya site karşılaştırması için farklı konumlardan akış yapan zaman serisi verilerinin genel bir görünümünü elde edin.

   Birden çok olay kaynağını bir Azure Time Series Insights ortamına bağlayabilirsiniz. Bu şekilde, birden çok, farklı konumdan, neredeyse gerçek zamanlı olarak akış yapan verileri görüntüleyebilirsiniz. Kullanıcılar, iş liderleriyle veri paylaşmak için bu görünürlüğden yararlanabilir. Sorunları çözmeye, en iyi uygulamaları uygulamanıza ve dersleri paylaşmanıza yardımcı olmak üzere uzmanlığı uygulayabilen etki alanı uzmanlarıyla daha iyi işbirliği yapabilirler.

- Azure Time Series Insights en üstünde bir müşteri uygulaması oluşturun.

   Azure Time Series Insights, zaman serisi verilerini kullanan uygulamalar oluşturmak için kullanabileceğiniz REST sorgu API 'Lerini kullanıma sunar.

## <a name="capabilities"></a>Özellikler

- **Hızlıca kullanmaya** başlayın: Azure Time Series Insights ön veri hazırlığı gerektirmez, bu sayede IoT Hub 'ınızda veya Olay Hub 'ınızdaki milyonlarca olaya hızlıca bağlanabilirsiniz. Bağlandıktan sonra, IoT çözümlerinizi hızla doğrulamak için sensör verilerini görselleştirin ve bunlarla etkileşim kurabilirsiniz. Kod yazmadan verilerinizle etkileşim kurabilir ve yeni bir dil öğrenmeniz gerekmez. Azure Time Series Insights, gelişmiş kullanıcılar için ayrıntılı, serbest metinli bir sorgu yüzeyi sağlar ve bu araştırma üzerine gelin ve sağ tıklayın.

- **Neredeyse gerçek zamanlı** içgörüler: Azure Time Series Insights, tek dakikalık bir gecikmeyle günde milyonlarca algılayıcı olayını alabilir. Azure Time Series Insights, algılayıcı verilerinize ilişkin Öngörüler elde etmenize yardımcı olur. Eğilimleri ve anormallikleri belirlemek, kök neden analizleri gerçekleştirmek ve maliyetli kapalı kalma süresini önlemek için bu uygulamayı kullanın. Gerçek zamanlı ve geçmiş veriler arasındaki çapraz bağıntı, verilerdeki gizli eğilimleri bulmanıza yardımcı olur.

- **Özel çözümler oluşturun**: mevcut uygulamalarınıza Azure Time Series Insights verileri ekleyin. Ayrıca, Azure Time Series Insights REST API 'Leriyle yeni özel çözümler de oluşturabilirsiniz. Diğer kişilerin de içgörülerinizi incelemesini sağlamak için paylaşabileceğiniz kişiselleştirilmiş görünümler oluşturun.

- **Ölçeklenebilirlik**: Azure Time Series Insights, IoT 'yi ölçekli olarak destekleyecek şekilde tasarlanmıştır. Günde 1 milyon ile 100 milyon arası olay alabilir ve varsayılan saklama süresi 31 gündür. Canlı veri akışlarını, geçmiş verilerin yanı sıra neredeyse gerçek zamanlı olarak görselleştirebilir ve çözümleyebilirsiniz.

## <a name="get-started"></a>başlarken

Başlamak için aşağıdaki adımları izleyin.

1. Azure portal bir Azure Time Series Insights ortamı sağlayın.
1. IoT Hub 'ı veya Olay Hub 'ı gibi bir olay kaynağına bağlanın.
1. Başvuru verilerini karşıya yükleyin. Bu ek bir hizmet değildir.
1. Azure Time Series Insights Gezgini ile verilerinizi dakikalar içinde gözden geçirin ve görüntüleyin.

## <a name="explorer"></a>Gezgin

Azure Time Series Insights gezginini kullanarak verilerinizdeki eğilimleri görüntüleyin, çözümleyin ve bulun.

![Azure Time Series Insights Gezgini](media/overview/time-series-insights-explorer-panel.png)

[Azure Time Series Insights Gezginini](time-series-insights-explorer.md) kullanmayı ve verilerinizden Öngörüler çizmeyi öğrenin.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Time Series Insights Gen1 [demo ortamını](./time-series-quickstart.md)keşfedebilir.

- Azure Time Series Insights ortamınızı nasıl [planlayacağınızı](time-series-insights-environment-planning.md) öğrenin.