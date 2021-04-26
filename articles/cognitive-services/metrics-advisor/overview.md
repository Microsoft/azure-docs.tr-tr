---
title: Ölçüm Danışmanı hizmeti nedir?
titleSuffix: Azure Cognitive Services
description: Ölçüm Danışmanı nedir?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: dfdd7286013bbb6462fb8e5b1bdf52e6ed738029
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384689"
---
# <a name="what-is-metrics-advisor-preview"></a>Ölçüm Danışmanı (Önizleme) nedir? 

Ölçüm Danışmanı, zaman serisi verilerinde veri izleme ve anomali algılama işlemleri gerçekleştirmek için AI kullanan Azure bilişsel hizmetler 'in bir parçasıdır. Hizmet, verilerinize model uygulama sürecini otomatikleştirir ve makine öğrenimine gerek kalmadan veri alımı, anomali algılama ve Tanılama için bir dizi API ve Web tabanlı çalışma alanı sağlar. Geliştiriciler, hizmetin en üstünde Aıops, tahmine dayalı bakım ve iş izleme uygulamaları oluşturabilir. Ölçüm Danışmanı 'nı kullanarak şunları yapın:

* Birden çok veri kaynağından çok boyutlu verileri analiz etme
* Anormallikleri tanımla ve bağıntı
* Verilerinizde kullanılan anomali algılama modelini yapılandırın ve hassas ayarlar yapın
* Kök neden analizi ile ilgili bozukluklar ve yardım

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Ölçüm danışmanına genel bakış":::

Bu belge aşağıdaki makale türlerini içerir:
* [Hızlı başlangıç](./Quickstarts/web-portal.md) , hizmete çağrı yapmanızı ve sonuçların kısa bir süre içinde elde etmenize olanak tanıyan adım adım yönergelerdir. 
* [Nasıl yapılır kılavuzlarında](./how-tos/onboard-your-data.md) , hizmeti daha belirli veya özelleştirilmiş yollarla kullanmaya yönelik yönergeler bulunur.
* [Kavramsal makaleler](glossary.md) , hizmetin işlevselliği ve özellikleri hakkında ayrıntılı açıklamalar sağlar.

## <a name="connect-to-a-variety-of-data-sources"></a>Çeşitli veri kaynaklarına bağlanma

Ölçüm Danışmanı, SQL Server, Azure Blob depolama, MongoDB gibi birçok veri mağazasında bulunan [çok boyutlu ölçüm](how-tos/onboard-your-data.md) verilerine bağlanabilir ve bunları alabilir.

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Kullanımı kolay ve özelleştirilebilir anomali algılama

* Ölçüm Danışmanı, herhangi bir makine öğrenimine gerek kalmadan verileriniz için en iyi modeli otomatik olarak seçer.
* [Çok boyutlu ölçümler](glossary.md#multi-dimensional-metric)içindeki her bir zaman serisini otomatik olarak izleyin.
* Verilerinize uygulanan modeli ve gelecekteki anomali algılama sonuçlarını özelleştirmek için [parametre ayarlama](how-tos/configure-metrics.md) ve [etkileşimli geri bildirimleri](how-tos/anomaly-feedback.md) kullanın.

## <a name="real-time-alerts-through-multiple-channels"></a>Birden çok kanal aracılığıyla gerçek zamanlı uyarılar

Anomali algılandığında, ölçüm Danışmanı, e-posta kancaları, Web kancaları ve Azure DevOps kancaları gibi kancaları kullanarak [gerçek zamanlı uyarıları](how-tos/alerts.md) birden çok kanal aracılığıyla gönderebilir. Esnek uyarı kuralları hangi uyarıların gönderileceğini ve bunların hedefini özelleştirmenizi sağlar.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Anormallikleri çözümleyerek akıllı tanılama öngörüleri

Çok boyutlu ölçümler üzerinde algılanan anormallikleri çözümleyin ve en olası temel neden, tanılama ağaçları, ölçüm detaya gitme ve daha fazlasını içeren [akıllı tanılama öngörüleri](how-tos/diagnose-incident.md) oluşturun. [Ölçüm grafiğini](how-tos/metrics-graph.md)yapılandırarak, olayları görselleştirmenize yardımcı olmak için çapraz ölçüm Analizi etkinleştirilebilir.


## <a name="typical-workflow"></a>Tipik iş akışı

İş akışı basittir: verilerinizi ekledikten sonra anomali algılamayı hassas şekilde ayarlayabilir ve senaryonuza uyacak şekilde yapılandırma oluşturabilirsiniz.

1. Ölçüm Danışmanı için [bir Azure kaynağı oluşturun](https://go.microsoft.com/fwlink/?linkid=2142156) . 
2. Web portalını kullanarak ilk monitörünüzü oluşturun.
    1. Verilerinizi ekleme
    2. Anomali algılama için ince ayar yapma
    3. Uyarılara abone olma
    4. Tanılama öngörülerini görüntüleme
3. Örneğinizi özelleştirmek için REST API kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* Hızlı başlangıcı keşfet: [Web 'de ilk ölçümünüzün izlenmesi](quickstarts/web-portal.md).
* Hızlı başlangıcı keşfet: [çözümünüzü özelleştirmek IÇIN REST API 'Lerini kullanın](./quickstarts/rest-api-and-client-library.md).
