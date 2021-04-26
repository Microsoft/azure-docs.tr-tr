---
title: Azure Application Insights Kullanıcı, oturum ve olay Analizi
description: Web uygulamanızın kullanıcılarının demografik analizi.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6eb91734afac81e103cebea48865793fa687ad71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024838"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Application Insights Kullanıcı, oturum ve olay Analizi

Kullanıcıların Web uygulamanızı ne zaman kullandığı, en çok hangi sayfalardan ilgilendikleri, kullanıcılarınızın nerede olduğunu ve hangi tarayıcıları ve işletim sistemlerini kullandıkları hakkında bilgi edinin. [Azure Application Insights](./app-insights-overview.md)kullanarak iş ve kullanım telemetrisini çözümleyin.

![Application Insights kullanıcıların ekran görüntüsü](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>başlarken

Application Insights portalındaki kullanıcılar, oturumlar veya olaylar dikey penceresinde verileri henüz görmüyorsanız, [kullanım araçlarını kullanmaya nasıl başlaleyeceğinizi öğrenin](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Kullanıcılar, oturumlar ve olaylar segmentleme aracı

Kullanım dikey pencerelerinin üçü, Web uygulamanızdan Telemetriyi üç perspektiften dilimlemek ve zaratmak için aynı aracı kullanır. Verileri filtreleyerek ve bölerek, farklı sayfaların ve özelliklerin göreli kullanımı hakkındaki öngörüleri açabilirsiniz.

* **Kullanıcılar aracı**: uygulamanızı ve özelliklerini kullanan kişi sayısı.  Kullanıcılar, tarayıcı tanımlama bilgilerinde depolanan anonim kimlikler kullanılarak sayılır. Farklı tarayıcıları veya makineleri kullanan tek bir kişi, birden fazla kullanıcı olarak sayılır.
* **Oturumlar aracı**: Kullanıcı etkinliğinin kaç oturumu, uygulamanızın belirli sayfalarını ve özelliklerini içerir. Bir oturum, kullanıcının süresi dolduktan sonra veya 24 saatlik sürekli kullanım sonrasında sayılır.
* **Olaylar aracı**: uygulamanızın belirli sayfa ve özelliklerinin ne sıklıkta kullanıldığı. Bir tarayıcı Uygulamanızdan bir sayfa yüklediğinde bir sayfa görünümü [sayılır.](./javascript.md) 

    Özel bir olay, uygulamanızda gerçekleşen bir şeyin bir oluşumunu temsil eder, genellikle düğme tıklamasıyla veya bir görevin tamamlanması gibi Kullanıcı etkileşimidir. [Özel olaylar oluşturmak](./api-custom-events-metrics.md#trackevent)için uygulamanıza kod eklersiniz.

## <a name="querying-for-certain-users"></a>Belirli kullanıcıları sorgulama

Kullanıcılar aracının en üstündeki sorgu seçeneklerini ayarlayarak farklı Kullanıcı gruplarını keşfedebilirsiniz:

* Göster: analiz edilecek kullanıcıların bir kohortu seçin.
* Kullanılan: özel olayları ve sayfa görünümlerini seçin.
* Sırasında: bir zaman aralığı seçin.
* Tarafından: bir süre veya tarayıcı ya da şehir gibi başka bir özellik tarafından verilerin demet olarak nasıl yapılacağını seçin.
* Bölme ölçütü: verileri ayırmak veya segmentlere ayırmak için bir özellik seçin. 
* Filtre ekleme: sorguyu, tarayıcı veya şehir gibi özelliklerine göre belirli kullanıcılar, oturumlar veya olaylarla sınırlayın. 
 
## <a name="saving-and-sharing-reports"></a>Raporları kaydetme ve paylaşma 
Kullanıcı raporlarını, Raporlarım bölümünde yalnızca sizin için özel olarak kaydedebilir veya paylaşılan Raporlar bölümünde bu Application Insights kaynağına erişimi olan diğer herkesle paylaşılacağını sağlayabilirsiniz.

Kullanıcılar, oturumlar veya olaylar raporunun bağlantısını paylaşmak için; araç çubuğunda **paylaşma** ' ya tıklayın, ardından bağlantıyı kopyalayın.

Kullanıcılar, oturumlar veya olaylar raporundaki verilerin bir kopyasını paylaşmak için; araç çubuğunda **paylaşma** ' ya tıkladıktan sonra, verilerle Word belgesi oluşturmak için **Word simgesine** tıklayın. Ya da, ana grafiğin üzerindeki **sözcük simgesine** tıklayın.

## <a name="meet-your-users"></a>Kullanıcılarınızı karşılayın

**Kullanıcılarınızın uyması** bölümünde geçerli sorguyla eşleşen beş örnek kullanıcı hakkında bilgi gösterilir. Toplamaların yanı sıra kişilerin uygulamanızı nasıl kullandıkları hakkında öngörüler sağlayabilir.

## <a name="next-steps"></a>Sonraki adımlar

- Kullanım deneyimlerini etkinleştirmek için [özel olaylar](./api-custom-events-metrics.md#trackevent) veya [sayfa görünümleri](./api-custom-events-metrics.md#page-views)göndermeye başlayın.
- Özel olayları veya sayfa görünümlerini zaten gönderirseniz, kullanıcıların hizmetinizi nasıl kullandığını öğrenmek için kullanım araçları ' nı araştırın.
    - [Huniler](usage-funnels.md)
    - [Deposuna](usage-retention.md)
    - [Kullanıcı Akışları](usage-flows.md)
    - [Çalışma Kitapları](../visualize/workbooks-overview.md)
    - [Kullanıcı bağlamı Ekle](./usage-overview.md)