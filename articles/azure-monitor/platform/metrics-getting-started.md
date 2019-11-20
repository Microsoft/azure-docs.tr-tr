---
title: Azure Ölçüm Gezgini 'ni kullanmaya başlama
description: Azure Ölçüm Gezgini ile ilk ölçüm grafiğinizi oluşturmayı öğrenin.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: ceaefffaf04a3ab266cde300e8c4b93a5e804796
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861087"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Azure Ölçüm Gezgini kullanmaya başlama

## <a name="where-do-i-start"></a>Nereden başlayabilirim?
Azure Izleyici Ölçüm Gezgini, grafikleri çizdirme, eğilimleri görsel olarak ilişkilendirme ve ölçüm değerlerinde ani artışları ve DIP 'leri araştırmanıza olanak tanıyan Microsoft Azure portal bir bileşenidir. Kaynaklarınızın sistem durumunu ve kullanımını araştırmak için ölçüm gezginini kullanın. Aşağıdaki sırada başlatın:

1. [Bir kaynak ve ölçüm seçin](#create-your-first-metric-chart) ve temel bir grafik görürsünüz. Daha sonra araştırmanıza uygun [bir zaman aralığı seçin](#select-a-time-range) .

1. [Boyut filtrelerini uygulamayı ve bölmeyi](#apply-dimension-filters-and-splitting)deneyin. Filtreler ve bölme, ölçüm segmentlerinin genel ölçüm değerine katkıda bulunduğunu analiz etmenize ve olası aykırı değerleri tespit etmenize olanak tanır.

1. Panoya sabitlemeden önce grafiği özelleştirmek için [Gelişmiş ayarları](#advanced-chart-settings) kullanın. Ölçüm değeri bir eşiğin altına çıktığında veya altına düştüğünde bildirimleri almak için [uyarıları yapılandırın](alerts-metric-overview.md) .

## <a name="create-your-first-metric-chart"></a>İlk ölçüm grafiğinizi oluşturma

Bir ölçüm grafiği oluşturmak için, kaynak, kaynak grubu, abonelik veya Azure Izleyici görünümünden **ölçümler** sekmesini açın ve şu adımları izleyin:

1. Kaynak seçiciyi kullanarak, ölçümlerini görmek istediğiniz kaynağı seçin. ( **Ölçümleri** belirli bir kaynak bağlamında açtıysanız, kaynak önceden seçilmiş olur).

    > ![Bir kaynak seçin](./media/metrics-getting-started/resource-picker.png)

2. Bazı kaynaklar için bir ad alanı seçmelisiniz. Ad alanı, ölçümleri kolayca bulabilmeniz için, ölçümleri düzenlemenin bir yoludur. Örneğin, depolama hesaplarının dosya, tablo, blob 'Lar ve sıra ölçümlerini depolamak için ayrı ad alanları vardır. Birçok kaynak türünün yalnızca bir ad alanı vardır.

3. Kullanılabilir ölçümler listesinden bir ölçüm seçin.

    > ![Bir ölçüm seçin](./media/metrics-getting-started/metric-picker.png)

4. İsteğe bağlı olarak, ölçüm toplamayı değiştirebilirsiniz. Örneğin, grafiğinizin ölçüm minimum, maksimum veya Ortalama değerlerini göstermesini isteyebilirsiniz.

> [!NOTE]
> Aynı grafikte birden çok ölçüm çizilmek istiyorsanız **ölçüm Ekle** düğmesini kullanın ve bu adımları tekrarlayın. Tek bir görünümdeki birden çok grafik için üstteki **Grafik Ekle** düğmesini seçin.

## <a name="select-a-time-range"></a>Bir zaman aralığı seçin

Varsayılan olarak, grafik en son 24 saat ölçüm verilerini gösterir. Zaman aralığını değiştirmek, grafiğinizde yakınlaştırmak veya uzaklaştırmak için **zaman Seçicisi** bölmesini kullanın. 

![Zaman aralığı Bölmesini Değiştir](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Grafiğin ilgi çekici bir alanını (ani veya bir DIP) araştırmak için **zaman fırçası** 'nı kullanın. Fare işaretçisini alanın başlangıcına koyun, sol fare düğmesine tıklayın ve basılı tutun, alanın diğer tarafına sürükleyin ve sonra düğmeyi bırakın. Grafik bu zaman aralığında yakınlaşacaktır. 

## <a name="apply-dimension-filters-and-splitting"></a>Boyut filtrelerini uygulama ve bölme

[Filtreleme](metrics-charts.md#apply-filters-to-charts) ve [bölme](metrics-charts.md#apply-splitting-to-a-chart) boyutları olan ölçümler için güçlü tanılama araçlarıdır. Bu özellikler çeşitli ölçüm segmentlerinin ("boyut değerleri") ölçümün genel değerini nasıl etkilediğini gösterir ve olası aykırı değerleri tanımlamanızı sağlar.

- **Filtreleme** , grafiğe hangi boyut değerlerinin ekleneceğini seçmenizi sağlar. Örneğin, *sunucu yanıt süresi* ölçümünü grafik oluştururken başarılı istekleri göstermek isteyebilirsiniz. Filtreyi *istek boyutunun başarısına* uygulamanız gerekir. 

- Grafiğe, grafiğin her bir boyutun değeri için ayrı satırlar görüntüleyip görüntülemediğini veya değerleri tek bir satıra toplamayacağını **denetler.** Örneğin, tüm sunucu örneklerinde ortalama yanıt süresi için bir satır görebilir veya her bir sunucu için ayrı satırlara bakabilirsiniz. Ayrı satırları görmek için *sunucu örneği* boyutunda bölme uygulamanız gerekir.

Filtreleme ve bölme uygulanmış [grafiklerin örneklerine](metric-chart-samples.md) bakın. Makale, grafikleri yapılandırmak için kullanılan adımları gösterir.

## <a name="advanced-chart-settings"></a>Gelişmiş grafik ayarları

Grafik stilini, başlığı özelleştirebilir ve gelişmiş grafik ayarlarını değiştirebilirsiniz. Özelleştirme ile işiniz bittiğinde çalışmanızı kaydetmek için bir panoya sabitleyin. Ayrıca, ölçüm uyarılarını yapılandırabilirsiniz. Azure Izleyici Ölçüm Gezgini 'nin bu ve diğer gelişmiş özellikleri hakkında bilgi edinmek için [ürün belgelerini](metrics-charts.md) izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Ölçüm Gezgini gelişmiş özellikleri hakkında bilgi edinin](metrics-charts.md)
* [Sorun giderme Ölçüm Gezgini](metrics-troubleshoot.md)
* [Azure hizmetleri için kullanılabilir ölçümlerin listesine bakın](metrics-supported.md)
* [Yapılandırılmış grafik örneklerine bakın](metric-chart-samples.md)
