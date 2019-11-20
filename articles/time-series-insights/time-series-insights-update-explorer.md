---
title: Önizleme Gezgini 'nde verileri görselleştirme-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights önizleme Gezgini ' nde kullanılabilen özellikler ve seçenekler hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/13/2019
ms.custom: seodec18
ms.openlocfilehash: 17ba808ebfabb68765cf35bbf0799d117bc6383b
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133443"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Azure Time Series Insights önizlemesi Gezgini

Bu makalede Azure Time Series Insights Preview [demo Web uygulamasında](https://insights.timeseries.azure.com/preview/demo)kullanılabilen çeşitli özellikler ve seçenekler açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Azure Time Series Insights önizleme Gezginini kullanmaya başlamak için şunları yapmanız gerekir:

* Sağlanmış bir Time Series Insights ortamı vardır. [Azure Time Series Insights önizleme](./time-series-insights-update-create-environment.md) öğreticisini okuyarak bir örnek sağlama hakkında daha fazla bilgi edinin.
* Hesap için oluşturduğunuz Time Series Insights ortamına [veri erişimi sağlar](./time-series-insights-data-access.md) . Diğerleri de kendiniz için farklı erişim sağlayabilir.
* Ortama veri göndermek için Time Series Insights ortamına bir olay kaynağı ekleyin:
  * [Bir olay hub 'ına nasıl bağlanacağınızı](./time-series-insights-how-to-add-an-event-source-eventhub.md) öğrenin 
  * [IoT Hub 'ına nasıl bağlanacağınızı](./time-series-insights-how-to-add-an-event-source-iothub.md) öğrenin

## <a name="explore-the-time-series-insights-preview-explorer"></a>Time Series Insights önizleme Gezginini keşfet

Azure Time Series Insights önizleme Gezgini aşağıdaki yedi öğeden oluşur:

[![Time Series Insights önizleme Gezgini 'ne genel bakış](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Ortam paneli](#1-environment-panel): tüm Azure Time Series Insights ortamlarınızı görüntüler.
1. [Gezinti çubuğu](#2-navigation-bar): **analiz** ve **model** sayfaları arasında geçiş yapmanızı sağlar.
1. [Hiyerarşi ağacı ve arama paneli](#3-hierarchy-tree-and-search-panel): grafiklenen belirli veri öğelerini seçmenizi ve aramanızı sağlar.
1. [Zaman serisi iyi](#4-time-series-well): Şu anda seçili olan tüm veri öğelerinizi gösterir.
1. [Grafik paneli](#5-chart-panel): geçerli çalışma haritanız görüntüler.
1. [Zaman Çizelgesi](#6-time-editor-panel): çalışma süre değiştirmenize olanak tanır.
1. [Uygulama çubuğu](#7-app-bar): Kullanıcı yönetim seçeneklerinizi (geçerli kiracı gibi) içerir ve bunları ve dil ayarlarını değiştirmenize izin verir.


## <a name="1-environment-panel"></a>1. ortam paneli

Ortam paneline erişiminiz olan tüm zaman serisi görüşleri ortamları görüntüler. Liste, Kullandıkça Öde (Önizleme) ortamlarının yanı sıra S1/S2 ortamlarını (genel kullanılabilirlik) içerir. Hemen burada almak için kullanmak istediğiniz Time Series Insights ortamına tıklamanız yeterlidir.

1. Görüntülenmiş ortamınızın yanındaki aşağı açılan oku seçin.

   [Ortam paneli ![](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Ardından istediğiniz ortamı seçin.

## <a name="2-navigation-bar"></a>2. gezinti çubuğu

  [Gezinti çubuğunu ![](media/v2-update-explorer/navigation-bar.png)](media/v2-update-explorer/navigation-bar.png#lightbox)

İki görünüm arasından seçim yapmak için gezinti çubuğunu kullanın:

* **Çözümle**: modellenen veya Modellenmemiş zaman serisi verilerinizde zengin analizler gerçekleştirmek için bunu kullanın.
* **Model**: yeni Time Series Insights önizleme türlerini, hiyerarşileri ve örnekleri Time Series Insights modelinize göndermek için bunu kullanın.

### <a name="model-authoring"></a>Model yazma

Azure Time Series Insights önizlemesi, zaman serisi modelinizdeki tam oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini destekler.

[Model arama panelini ![](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Zaman serisi model türü**: hesaplamalar yapmak için değişkenler veya formüller tanımlamak üzere Time Series Insights türlerini kullanabilirsiniz. Bunlar, belirli bir Time Series Insights örneğiyle ilişkilendirilir. Bir türü veya daha fazla değişken olabilir.
* **Zaman serisi modeli hiyerarşisi**: hiyerarşileri verilerinizin sistematik kuruluşlar şunlardır. Time Series Insights veri farklı varlıklar arasındaki ilişkileri hiyerarşileri kullanılırlar.
* **Zaman serisi modeli örneği**: zaman serisi örneklerdir. Çoğu durumda, bu, ortamdaki varlığın benzersiz tanımlayıcısı olan **DeviceID** veya **AssetID**.

Zaman serisi modeli hakkında daha fazla bilgi için bkz: [kez serisi modelleri](./time-series-insights-update-tsm.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. hiyerarşi ağacı ve arama paneli

Hiyerarşi ağacı ve arama paneli, grafiğinizde görüntülenmesini istediğiniz belirli zaman serisi örneklerini bulmak için [zaman serisi modeli](./time-series-insights-update-tsm.md) hiyerarşinizde kolayca arama yapmanıza ve gezinmenize izin verir. Örneklerinizi seçtiğinizde, bunlar yalnızca geçerli grafiğe eklenmez, ancak veri kutusuna da eklenir. 

[Hiyerarşi ağacı ve arama paneli ![](media/v2-update-explorer/hierarchy-search.png)](media/v2-update-explorer/hierarchy-search.png#lightbox)

Arama sonuçları bölmesi, göstermek istediğiniz örnekleri bulmayı kolaylaştıran bir hiyerarşi görünümünde veya liste görünümünde sonuçlarınızı görüntülemenize imkan tanır.
 
## <a name="4-time-series-well"></a>4. zaman serisi iyi

İyi, örnek alanlarını ve seçili Time Series Insights örneklerle ilişkili diğer meta verileri görüntüler. Sağ taraftaki onay kutularını seçerek geçerli grafikten belirli örnekleri gizleyebilir veya görüntüleyebilirsiniz. 

  [Önizleme iyi ![](media/v2-update-explorer/preview-well.png)](media/v2-update-explorer/preview-well.png#lightbox)

Öğenin sol tarafındaki kırmızı **silme** (çöp kutusu) denetimini seçerek, geçerli verilerdeki belirli veri öğelerini kaldırabilirsiniz. Ayrıca, her bir öğenin grafikte nasıl görüntülendiğini denetlemenize de olanak tanır. En az/en yüksek gölgeler, veri noktaları ekleyebilir, öğeyi zamanında kaydırabileceğiniz örnek bir şekilde görselleştirmeyi seçebilirsiniz. 

Ayrıca, araştırma denetimi zaman vardiyalarını ve dağılım çizimlerini kolayca oluşturmanızı sağlar.  

  [![Iyi düzen seçenekleri](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Aşağıdaki iletiyi görürseniz, örnek seçilen zaman aralığı boyunca hiçbir veri içermez. Sorunu çözmek için, zaman aralığını artırın veya örneğin veri iletme süresini onaylayın.
>
> ![Veri bildirimi yok](media/v2-update-explorer/no-data-warning.png)

## <a name="5-chart-panel"></a>5. Grafik bölmesi

Grafik, zaman serisi örneklerini satır olarak görüntülemenizi sağlar. Grafik büyütmek için web denetimleri tıklayarak ortam paneli, veri modeli ve zaman aralığı Denetim Masası daraltabilirsiniz. 

  [![önizleme grafiğine genel bakış](media/v2-update-explorer/chart-overview.png)](media/v2-update-explorer/chart-overview.png#lightbox)

1. **Grafik türü**: görselleştirme için hangi veri öğelerinin kullanılabilir olduğunu denetler.

1. **Aralık boyutu**: Aralık boyutu kaydırıcı Aracı, aynı zaman dilimi boyunca aralıkların yakınlaştırmasını ve ölçeğini görmenizi sağlar. Bu, çok büyük bir zaman dilimi arasında hareket konusunda daha kesin bir denetim sağlar ve bu sayede, verilerinizin ayrıntılı, yüksek çözünürlüklü bir şekilde kesişimlerini görmenizi sağlar. Kaydırıcının varsayılan başlangıç noktası, Seçiminizdeki verilerin en uygun görünümü olarak ayarlanır; dengeleme çözümü, sorgu hızı ve ayrıntı düzeyi.

1. **Yakınlaştır ve Kaydır**: grafiği yakınlaştırmak ve kaydırmak için bu denetime tıklayın.

1. **Y ekseni denetimi**: kullanılabilir Y ekseni görünümü seçeneklerinde geçiş yapar:

    * `Stacked`: Her satırın tek bir y ekseni sahiptir.
    * `Overlap`: aynı Y ekseninde birden çok satırı, seçilen satıra göre değişen Y ekseni verileriyle birlikte yığmak için kullanın.
    * `Shared`: Tüm y ekseni veri birlikte görüntülenir.

1. **İşaretleyici öğesi**: Şu anda seçili olan veri öğesi ve ilgili ayrıntıları.

Mevcut grafikteki bir veri noktasına **sağ tıklayıp** fare tuşunu basılı tutup seçili alanı seçtiğiniz uç noktaya sürükleyerek belirli bir veri dilimine daha fazla ayrıntıya gidebilirsiniz. Gri seçili alana **sağ tıklayın** ve aşağıda gösterildiği gibi **Yakınlaştır** ' a tıklayın. Ayrıca, telemetri olaylarını seçili TimeSpan içinde görüntüleyebilir ve indirebilirsiniz.

  [![önizlemesi grafiği önizlemesi](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

**Yakınlaştırma** eylemini gerçekleştirdikten sonra, seçtiğiniz veri kümesini görürsünüz. Time Series Insights verilerinizin üç y ekseni gösterimlerine göre geçiş yapmak için biçim denetimini seçin.

  [![Preview grafik y ekseni](media/v2-update-explorer/standard-chart.png)](media/v2-update-explorer/standard-chart.png#lightbox)

Burada, **Çakışan grafiğin**bir örneğini görebilirsiniz:

  [Çakışan grafik seçeneği ![](media/v2-update-explorer/overlapping-chart.png)](media/v2-update-explorer/overlapping-chart.png#lightbox)

**Diğer eylemler** düğmesi, **indirmeyi CSV olarak** göstermek ve **Power BI seçeneklere aktarmak** için genişler.

  [![daha fazla eylem seçeneği](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

[Time Series Insights native Power BI Bağlayıcısı](concepts-power-bi.md)hakkında bilgi edinin.

## <a name="6-time-editor-panel"></a>6. zaman Düzenleyicisi bölmesi

Time Series Insights ile çalışırken ilk olarak bir zaman aralığı seçersiniz. Seçilen zaman aralığı Time Series Insights güncelleştirme pencere öğeleri ile düzenleme için kullanılabilen veri kümesini denetler.

  [![zaman seçimi paneli](media/v2-update-explorer/timeline-element.png)](media/v2-update-explorer/timeline-element.png#lightbox)

> [!TIP]
> Zaman çizelgesinin bir kısmı, ısınma veya turuncu renkte vurgulanır ve bu da, normal mağazada kullanılabilir olan verilerin yayılmasını gösterir.

Çalışma zamanı aralığını seçmek için Time Series Insights güncelleştirmesinde aşağıdaki Web denetimleri mevcuttur. 

  [![araştırma iyi denetimi](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **İç tarih aralığı kaydırıcı denetimi**: iki uç nokta denetimini istediğiniz zaman aralığında sürükleyerek kullanın. Bu iç tarih aralığı, dış tarih aralığı kaydırıcı denetimi tarafından sınırlandırılır.

1. **Artırın ve azaltın tarih aralığı düğmeleri**: artışa veya sürenizi span istediğiniz aralığı için herhangi bir düğmeyi seçerek.

1. **Zaman aralığı daraltma denetimi**: Bu Web denetimi, iç tarih aralığı kaydırıcı aracı dışında tüm denetimleri gizlemenizi sağlar.

1. **Dış tarih aralığı kaydırıcı denetimi**: bitiş tarihi aralığını seçmek için uç nokta denetimlerini kullanın. Bu, iç tarih aralığı denetiminizdeki kullanılabilir olacak.

1. **Zaman aralığı kaydırıcı denetimi**: son **30 dakika**, **son 12 saat**veya **özel bir Aralık**gibi önceden ayarlanmış zaman aralığı seçimleri arasında hızlıca geçiş yapmak için bunu kullanın. Bu değeri değiştirmeniz aralık boyutu kaydırıcı aracını tartışılan kullanılabilir aralığı aralıkları değiştirir.

   [seçim paneline ve ![](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. uygulama çubuğu

Time Series Insights önizlemesi gezinti paneli Time Series Insights uygulamanızın en üstünde görünür. Aşağıdaki işlevleri sağlar:

### <a name="current-session-share-link-control"></a>Geçerli oturumdaki paylaşımını bağlantı denetimi

  [![paylaşma simgesi](media/v2-update-explorer/share-icon.png)](media/v2-update-explorer/share-icon.png#lightbox)

Ekibinizle bir URL bağlantısı paylaşmak için yeni **paylaşma** simgesini seçin.

  [Örnek URL 'nizi ![paylaşma](media/v2-update-explorer/share-your-view.png)](media/v2-update-explorer/share-your-view.png#lightbox)

### <a name="tenant-section"></a>Kiracı bölümü

  [![kiracı seçimi](media/v2-update-explorer/tenant-selection.png)](media/v2-update-explorer/tenant-selection.png#lightbox)

* Geçerli Time Series Insights oturum açma hesabı bilgilerini görüntüler.
* Kullanılabilir Time Series Insights temaları arasında geçiş yapmak için bunu kullanın.
* Önizleme [demo Web uygulamasını](https://insights.timeseries.azure.com/preview/demo)görüntülemek için kullanın.

### <a name="theme-selection"></a>Tema seçimi

Yeni bir tema seçmek için sağ üst köşede bulunan profil simgenizi seçin. Ardından, **Temayı Değiştir**' i seçin.

  [![teması seçimi](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Dil seçimi, profil simgenizin seçilerek de kullanılabilir.

Azure zaman serisi öngörüleri Önizleme iki tema destekler:

* **Açık tema**: Bu belge boyunca gösterilen varsayılan tema.
* **Koyu tema**: gezgin 'i burada gösterildiği gibi işler:

  [Seçili Koyu tema ![](media/v2-update-explorer/dark-theme-selected.png)](media/v2-update-explorer/dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 ortam denetimleri

### <a name="preview-terms-panel"></a>Önizleme terimleri bölmesi

Bu bölüm yalnızca güncelleştirilmiş kullanıcı Arabiriminde Gezgin kullanma girişimi mevcut S1/S2 ortamlar için geçerlidir. Genel kullanıma açık ürün ve önizleme birleşimini kullanmak isteyebilirsiniz. Bazı işlevler mevcut kullanıcı Arabiriminden güncelleştirilmiş Gezgini'ne ekledik ancak mevcut zaman serisi görüşleri Gezgin ortamında S1/S2 için tam kullanıcı Arabirimi deneyimi elde edebilirsiniz. 

Hiyerarşi yerine, ortamınızda sorgular tanımladığınız Time Series Insights terimler panelini görürsünüz. Bir koşula göre verilerinizi filtrelemek için kullanın.

  [sorgu panelinin ![](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Zaman serisi öngörüleri Önizleme Koşulları Düzenleyicisi paneli aşağıdaki parametreleri alır:

**Burada**: aşağıdaki tabloda listelenen işlenenleri kümesini kullanarak olaylarınızı hızlıca filtrelemek için WHERE yan tümcesini kullanın. Bir işlenen seçerek bir arama yapın, o aramaya bağlı koşul otomatik olarak güncelleştirilir. Desteklenen işlenen türleri şunları içerir:

| İşlem | Desteklenen türler   | Notlar |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Çift, DateTime, zaman aralığı | |
| `=`, `!=`, `<>` | Dize, Bool, Double, DateTime, zaman aralığı, NULL |
| `IN` | Dize, Bool, Double, DateTime, zaman aralığı, NULL | Tüm işlenenler aynı türde veya NULL sabiti olması. |
| `HAS` | Dize | Sağ tarafta yalnızca sabit dize sabit değerlerine izin verilir. Boş dize ve NULL değerlerine izin verilmez. |

Desteklenen sorgu işlemleri ve veri türleri hakkında daha fazla bilgi edinmek için bkz. [zaman serisi ifadesi (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

### <a name="examples-of-where-clauses"></a>WHERE yan tümcelerinin örnekleri

  [![WHERE yan tümcesi örnekleri](media/v2-update-explorer/example-queries.png)](media/v2-update-explorer/example-queries.png#lightbox)

**Ölçü**: geçerli grafiğiniz için öğe olarak kullanabileceğiniz tüm sayısal sütunları (**Double**) görüntüleyen bir açılan liste.

**Bölme ölçütü**: Bu aşağı açılan liste, modelinizde, verilerinizi gruplabilmeniz için kullanabileceğiniz tüm kullanılabilir kategorik sütunları (dizeler) görüntüler. Aynı x ekseninde görüntülenecek en fazla beş terim ekleyebilirsiniz. İstediğiniz parametreleri girin ve sonra yeni bir terim eklemek için **Ekle** ' yi seçin.

  [![sorgulanan ve filtrelenmiş bir görünüm](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Aşağıdaki görüntüde gösterildiği gibi görünür simgesini seçerek grafik panelinde öğeleri görüntüleyebilir ve gizleyebilirsiniz. Sorguları tamamen kaldırmak için kırmızı **X**seçeneğini belirleyin.

  [sorgulanan ve filtrelenmiş bir seçeneği ![Iptal et](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

- Azure Time Series Insights önizlemede [depolama ve](./time-series-insights-update-storage-ingress.md) giriş hakkında bilgi edinin.

- [Veri modellemesi](./time-series-insights-update-tsm.md)Time Series Insights önizleme belgesini okuyun.

- Time Series Insights örneğinizi [tanılamayı ve sorun gidermeyi](./time-series-insights-update-how-to-troubleshoot.md) öğrenin.
