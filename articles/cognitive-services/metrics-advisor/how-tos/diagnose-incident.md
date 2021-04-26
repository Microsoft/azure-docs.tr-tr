---
title: Ölçüm Danışmanı 'Nı kullanarak olayları tanılama
titleSuffix: Azure Cognitive Services
description: Ölçüm Danışmanı 'Nı kullanarak bir olayı tanılamayı ve verilerinize ilişkin ayrıntılı görünümler almayı öğrenin.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbullwin
ms.openlocfilehash: ecbfb2d9acf6c62f95c264a14e306442db25e483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101703431"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>Nasıl yapılır: ölçüm Danışmanı kullanarak bir olayı tanılama

Ölçüm Danışmanı, Tanılama için çeşitli özellikler sağlar ve algılanan olayların derinlemesine bir görünümünü verir ve kök neden çözümlemesi sağlar. Ölçüm üzerinde bir grup anomali algılandığında, ölçüm Danışmanı, anormallikleri bir hiyerarşiye göre gruplandırarak üzerine analiz eder.

> [!NOTE]
> Şu anda ölçüm Danışmanı, en az bir boyuta sahip ölçümler için olay tanılamayı destekler ve  *sayısal* türle ölçer. Ölçümünüzün, tanılama hiyerarşisini oluşturmak için kullanılan her boyut için SUM gibi bir toplu boyut değeri olması gerekir. Ölçüm Danışmanı, toplanan değerler oluşturmaya yardımcı olmak için [**otomatik toplama ayarları**](onboard-your-data.md#automatic-roll-up-settings) sunar. 

Belirli bir ölçüm altındaki tüm olayları görmek için sol gezinti penceresindeki **Olay Hub 'ına** tıklayın. Sayfanın üst kısmında, farklı ölçümler seçerek algılama yapılandırmalarının yanı sıra algılama sonuçlarını görebilir ve zaman aralığını değiştirebilirsiniz.

> [!TIP]
> **Olay Hub 'ına** şu şekilde de ulaşabilirsiniz:
> * Ölçümünüzün görselleştirmedeki bir veri noktasına tıkladığınızda ve görüntülenen **geri bildirim Ekle** penceresinin altındaki bağlantıları kullanarak.
> * Ölçümünüzün **Olaylar** sekmesindeki anormalilerin birine tıklanın. 

**Genel bakış** bölümü, seçili zaman aralığında bulunan anomali ve uyarıların sayısı dahil olmak üzere algılama sonuçlarını içerir.

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="Olay Hub 'ı" lightbox="../media/diagnostics/incident-hub-overview.png":::

Seçili ölçüm ve zaman aralığı içindeki algılanan olaylar **olay listesinde** listelenir. Olayları filtrelemeye ve sıralamayla yönelik seçenekler vardır. Örneğin, önem derecesine göre. Daha fazla tanılama için **olay** sayfasına gitmek üzere olayların birine tıklayın.

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="Olay listesi" lightbox="../media/diagnostics/incident-list.png":::

**Tanılama** bölümü bir olay üzerinde derinlemesine analizler gerçekleştirmenize olanak tanır ve kök nedenlerini belirlemek için araçlar sağlar.

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="Olay tanılama" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>Kök neden önerisi

Bir ölçümde bir grup durum algılandığında ve bir olaya neden olduğunda, ölçüm Danışmanı olayın kök nedenini çözümlemeye çalışır. **Kök neden önerisi** , bir olayın olası nedenleri için otomatik öneriler sağlar. Bu özellik yalnızca boyut içinde toplanmış bir değer varsa kullanılabilir. Ölçümün boyutu yoksa, kök nedeni kendisi olur. Kök nedenler, sağ taraftaki Panel bölümünde listelenir ve çeşitli nedenlerden bazıları olabilir. Tabloda veri yoksa, bu boyut analiz gerçekleştirme gereksinimlerini karşılamadığı anlamına gelir.

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="Kök neden önerisi":::


Kök nedeni ölçüm belirli boyutlarla sağlandığında, ölçümün daha fazla ayrıntılarını görüntülemek için **ölçüm 'e git** ' e tıklayabilirsiniz.

## <a name="incident-tree"></a>Olay ağacı

Potansiyel ana nedenlerden dolayı otomatik analizler ile birlikte ölçüm Danışmanı, **olay ağacını** kullanarak el ile kök neden analizini destekler. Olay sayfasında iki tür olay ağacı vardır: **hızlı tanılama** ağacı ve **etkileşimli ağaç**.

Hızlı tanılama ağacı, geçerli bir olayın tanılanmasına ve kök düğümün geçerli olay kök düğümüyle sınırlı olması içindir. Ağaç düğümlerini tıklayarak genişletebilir ve daraltabilirsiniz ve bu nesnenin serisi, ağaç üzerindeki grafikteki geçerli olay serisiyle birlikte gösterilir.

Etkileşimli ağaç, geçerli olayların yanı sıra eski olayları ve ilgili olanları tanılamanıza olanak sağlar. Etkileşimli ağacı kullanırken bir işlem menüsünü açmak için düğüme sağ tıklayın. burada, kök düğümlerde detaya gitmek için bir boyut seçebilir ve her bir düğüm için detaya gitmek üzere bir boyut seçebilirsiniz. Üstteki boyut listesinin iptal düğmesine tıklayarak ayrıntıya gitmeyi bu boyuttan çıkarabilirsiniz veya azaltabilirsiniz. düğümü seçmek için bir düğüme sağ tıklayın ve dizideki geçerli olay serisiyle birlikte serisini gösterin.

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="Olay ağacı" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>Anomali detaya gitme

Olay bilgilerini görüntülerken, örneğin farklı boyutlar ve zaman damgaları gibi daha ayrıntılı bilgiler almanız gerekebilir. Verilerinizde bir veya daha fazla boyut varsa, daha ayrıntılı bir görünüm almak için ayrıntıya gitme işlevini kullanabilirsiniz. 

Detaya gitme işlevini kullanmak için, **Olay Hub 'ında** **ölçüm detaya gitme** sekmesine tıklayın. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="Ölçüm detaya gitme ":::

**Boyutlar** ayarı bir olay için boyutların listesidir, her biri için kullanılabilen diğer boyut değerlerini seçebilirsiniz. Boyut değerleri değiştirildikten sonra. **Zaman damgası** ayarı, geçerli olayı zaman içinde farklı bir süre içinde görüntülemenize olanak sağlar.

### <a name="select-drilling-options-and-choose-a-dimension"></a>Detaya gitme seçeneklerini belirleyin ve bir boyut seçin

İki tür ayrıntıya gitme seçeneği vardır: **detaya gitme** ve **yatay karşılaştırma**.

> [!Note]
> 1. Detaya gitme için, verileri farklı boyut değerlerinden (yani, seçilen boyutlar hariç) inceleyebilirsiniz. 
> 2. Yatay karşılaştırma için, tüm boyutlar dışında farklı boyut değerlerinden verileri inceleyebilirsiniz.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="Boyut detayına git":::

### <a name="value-comparison-for-different-dimension-values"></a>Farklı boyut değerleri için değer karşılaştırması

Ayrıntıya gitme sekmesinin ikinci bölümü, farklı boyut değerleri için karşılaştırmalar içeren bir tablodur. Değer, taban çizgisi değeri, fark değeri, Delta değeri ve bir anomali olup olmadığı dahildir.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="Detaya gitme karşılaştırması" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>Farklı boyut değeri için değer ve beklenen değer karşılaştırmaları

Ayrıntıya gitme sekmesinin üçüncü bölümü, farklı boyut değerleri için değerleri ve beklenen değerleri içeren bir histogramı. Histogram değer ile beklenen değer arasındaki farka göre sıralanır. Beklenmedik bir değeri en büyük etkiyle kolayca bulabilirsiniz. Örneğin, yukarıdaki resimde, **US7** ve tüm anomali için en iyi şekilde katkıda bulunan bir değer hariç, bu durumu bulabilirsiniz.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="Ayrıntıya gitme tablosu" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>Ham değer görselleştirmesi
Detaya gitme sekmesinin son bölümü, ham değerlerin bir çizgi grafiğidir. Bu grafik sağlandığında, ayrıntıları görüntülemek için ölçüm sayfasına gitmeniz gerekmez.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="Ayrıntıya gitme çizgi grafiği" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>Zaman serisi kümelemesini kullanarak benzer bozukluklar görüntüleyin

Bir olayı görüntülerken, onunla ilişkili çeşitli serileri görmek için **benzer zaman serisi Kümelemesi** sekmesini kullanabilirsiniz. Bir gruptaki seriler birlikte özetlenir. Yukarıdaki resimde, en az iki seri grubu olduğunu biliyoruz. Bu özellik yalnızca aşağıdaki gereksinimler karşılandığında kullanılabilir:

1. Ölçümler bir veya daha fazla boyuta veya boyut değerine sahip olmalıdır.
2. Bir ölçüm içindeki serinin benzer bir eğilimi olmalıdır.

Kullanılabilir boyutlar sekmenin üst kısmında listelenir ve seriyi belirtmek için bir seçim yapabilirsiniz.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="Seri grubu":::

## <a name="compare-time-series"></a>Zaman serisini Karşılaştır

Bazen belirli bir zaman serisinde bir anomali algılandığında, tek bir görselleştirmede onu birden çok başka seriler ile karşılaştırmak yararlı olur. **Karşılaştır araçlar** sekmesine tıklayın ve ardından mavi **+ Ekle** düğmesine tıklayın. 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="Karşılaştırılacak seriyi Ekle" lightbox="../media/diagnostics/add-series.png":::

Veri akışınızdan bir seri seçin. Aynı ayrıntı düzeyi veya farklı bir tane seçebilirsiniz. Hedef boyutları seçin ve seri eğilimini yükleyin ve ardından önceki bir seriler ile karşılaştırmak için **Tamam** ' ı tıklatın. Seriler bir görselleştirmede birlikte yerleştirilecek. Karşılaştırma için daha fazla seri eklemeye ve daha fazla öngörü almaya devam edebilirsiniz. Zaman serisi verilerini zaman kaydırılan bir süre boyunca karşılaştırmak için **karşılaştırma araçları** sekmesinin en üstündeki açılan menüye tıklayın.  

> [!Warning]
> Bir karşılaştırma yapmak için, zaman serisi veri analizinde veri noktalarında vardiyaları gerekebilir. böylece verilerinizin ayrıntı düzeyi bunu desteklemelidir. Örneğin, verileriniz haftalık olarak kullanılıyorsa ve gün **içinde** karşılaştırma yaparsanız, hiçbir sonuç elde edersiniz. Bu örnekte, bunun yerine ay **Içindeki ayı** karşılaştırmayı kullanacaksınız.

Zaman kaydırılan bir karşılaştırmayı seçtikten sonra, veri değerlerini, Delta değerlerini veya Delta yüzdesini karşılaştırmak isteyip istemediğinizi seçebilirsiniz.

> [!Note]
> * **Veri değeri** , ham veri değeridir.
> * **Delta değeri** , ham değer ile karşılaştırılan değer arasındaki farktır.
> * **Yüzde Delta değeri** , ham değer ile karşılaştırılan değer arasındaki farkın karşılaştırılan değere bölünmesiyle hesaplanır.

## <a name="related-incidents-across-metrics"></a>Ölçümler arasında ilgili olaylar

Bazen, farklı ölçümlerin olaylarını aynı anda veya diğer ölçümlerde ilgili olayları denetlemeniz gerekebilir. **Çapraz ölçüm Analizi** bölümünde ilgili olayların bir listesini bulabilirsiniz. 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="ölçümler arasında ilgili olaylar":::

Geçerli ölçüm için ilgili olayları görebilmeniz için, ölçümler arasında bir ilişki eklemeniz gerekir. İlişki eklemek için **ölçüm grafiği ayarları** ' na tıklayın. Yalnızca aynı boyut adlarına sahip ölçümler ilgili olabilir. Aşağıdaki parametreleri kullanın.

- Geçerli veri akışı & ölçümü: geçerli olayın veri akışı ve ölçümü
- Yön: iki ölçüm arasındaki ilişkinin yönü. (artık ilgili olaylar listesi için geçerli değildir)
- Başka bir veri akışı & ölçümü: geçerli ölçümle bağlantı kurmak için veri akışı ve ölçüm


## <a name="next-steps"></a>Sonraki adımlar 

- [Geri bildirimi kullanarak anomali algılamayı ayarlama](anomaly-feedback.md)
- [Ölçümleri yapılandırma ve ince ayar algılama yapılandırması](configure-metrics.md)
