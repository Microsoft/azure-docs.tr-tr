---
title: Azure İzleyici Çalışma Kitapları’na Genel Bakış
description: Çalışma kitaplarının veri analizi ve Azure portal zengin görsel raporların oluşturulması için nasıl esnek bir tuval sağladığını öğrenin.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3d75d7605ba082aac84973aef247de79d55b4c9c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482781"
---
# <a name="azure-monitor-workbooks"></a>Azure Izleyici çalışma kitapları

Çalışma kitapları, Azure portalda zengin görsel raporlarının oluşturulması ve veri analizi için esnek bir tuval sağlar. Azure 'da birden çok veri kaynağına dokunmanıza ve bunları Birleşik etkileşimli deneyimler halinde birleştirmeye olanak tanır.

Çalışma kitapları oluşturmaya yönelik bir video kılavuzu aşağıda verilmiştir.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Veri kaynakları

Çalışma kitapları, Azure içindeki birden çok kaynaktan verileri sorgulayabilir. Çalışma kitaplarının yazarları, temel alınan bileşenlerin kullanılabilirliği, performansı, kullanımı ve genel durumuna ilişkin içgörüler sağlamak için bu verileri dönüştürebilir. Örneğin, yüksek CPU veya düşük bellek örneklerini belirlemek ve sonuçları etkileşimli bir raporda kılavuz olarak görüntülemek için sanal makinelerden performans günlüklerini analiz edin.
  
Ancak çalışma kitaplarının gerçek gücü, tek bir rapordaki ayrı kaynaklardan verileri birleştirebilme becerisidir. Bu, daha zengin veri ve öngörüleri olanaklı hale getirecek şekilde kaynak genelinde bileşik kaynak görünümleri veya birleştirmeleri oluşturulmasına olanak sağlar.

Çalışma kitapları şu anda aşağıdaki veri kaynaklarıyla uyumludur:

* [Günlükler](../visualize/workbooks-data-sources.md#logs)
* [Ölçümler](../visualize/workbooks-data-sources.md#metrics)
* [Azure Kaynak Grafı](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Uyarılar (Önizleme)](../visualize/workbooks-data-sources.md#alerts-preview)
* [İş yükü sistem durumu](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Kaynak Durumu](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Veri Gezgini](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Görsel öğeler

Çalışma kitapları, verilerinizi görselleştirmeye yönelik zengin bir özellik kümesi sağlar. Her görselleştirme türünün ayrıntılı örnekleri için aşağıdaki bağlantılara başvurabilirsiniz:

* [Metin](../visualize/workbooks-text-visualizations.md)
* [Grafikler](../visualize/workbooks-chart-visualizations.md)
* [Kılavuzlar](../visualize/workbooks-grid-visualizations.md)
* [Kutucuklar](../visualize/workbooks-tile-visualizations.md)
* [Ağaçlarında](../visualize/workbooks-tree-visualizations.md)
* [Performansının](../visualize/workbooks-graph-visualizations.md)
* [Bileşik çubuk](../visualize/workbooks-composite-bar.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="Çalışma kitabı görselleştirmeleri örneği." border="false" lightbox="./media/workbooks-overview/visualizations.png":::

### <a name="pinning-visualizations"></a>Görselleştirmeleri sabitleme

Çalışma kitabındaki metin, sorgu ve ölçüm adımları, çalışma kitabı PIN modundayken veya çalışma kitabı yazarı PIN simgesini görünür hale getirmek üzere bu öğe için ayarları etkinleştirmişse, bu öğelerin sabitleme düğmesi kullanılarak sabitlenebilir.

PIN moduna erişmek için, düzenleme moduna girmek üzere **Düzenle** ' ye tıklayın ve üstteki çubukta mavi pin simgesini seçin. Her bir pin simgesi, ekranın sağ tarafındaki her ilgili çalışma kitabı bölümünün *düzenleme* kutusunun üstünde görünür.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="PIN deneyiminin ekran görüntüsü." border="false":::

> [!NOTE]
> Çalışma kitabının durumu, PIN sırasında kaydedilir ve temeldeki çalışma kitabı değiştirilirse bir panodaki sabitlenmiş çalışma kitapları güncelleştirmeyecektir. Sabitlenmiş bir çalışma kitabı bölümünü güncelleştirmek için bu bölümü silip yeniden sabitlemek zorunda olursunuz.

## <a name="getting-started"></a>Kullanmaya başlama

Çalışma kitapları deneyimini araştırmak için öncelikle Azure Izleyici hizmetine gidin. Bu, Azure portal arama kutusuna **izleyici** yazılarak yapılabilir.

Sonra **çalışma kitapları**' nı seçin.

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="Kırmızı kutuda vurgulanan çalışma kitapları düğmesinin ekran görüntüsü." border="false":::

### <a name="gallery"></a>Galeri

Galeri, tüm türlerin çalışma kitaplarını düzenlemenizi, sıralamayı ve yönetmeyi kolaylaştırır.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="Tüm sekmesindeki galerinin ekran görüntüsü." lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>Galeri sekmeleri

Galeride çalışma kitabı türlerinin düzenlenmesine yardımcı olacak dört sekme vardır.

| Tab              | Açıklama                                       |
|------------------|---------------------------------------------------|
| Tümü | Her tür çalışma kitabı, genel şablon ve Şablonlarım için en iyi dört öğeyi gösterir. Çalışma kitapları değiştirilme tarihine göre sıralanır, bu nedenle en son sekiz değiştirilmiş çalışma kitabını görürsünüz.|
| Çalışma Kitapları | Oluşturduğunuz veya sizinle paylaştığınız tüm kullanılabilir çalışma kitaplarının listesini gösterir. |
| Genel Şablonlar | Kullanıma hazır olan tüm kullanılabilir ve Microsoft tarafından yayımlanan işlevsel çalışma kitabı şablonlarının listesini gösterir. Kategoriye göre gruplandırılır. |
| Şablonlarım | Oluşturduğunuz veya sizinle paylaştığınız tüm kullanılabilir dağıtılan çalışma kitabı şablonlarının listesini gösterir. Kategoriye göre gruplandırılır. |

#### <a name="features"></a>Özellikler

* Her sekmede, çalışma kitaplarında bilgi içeren bir kılavuz vardır. Açıklama, son değiştirilme tarihi, Etiketler, abonelik, kaynak grubu, bölge ve paylaşılan durum bilgilerini içerir. Çalışma kitaplarını bu bilgilere göre de sıralayabilirsiniz.
* Kaynak grubuna, aboneliklere, çalışma kitabına/şablon adına veya şablon kategorisine göre filtreleyin.
* Silmek veya toplu silme için birden fazla çalışma kitabı seçin.
* Her çalışma kitabının bir bağlam menüsü vardır (sonda üç nokta/üç nokta), seçim seçildiğinde hızlı eylemlerin bir listesi açılır.
    * Çalışma kitabının kaynak KIMLIĞINI görmek, Etiketler eklemek, kilitleri yönetmek vb. için kaynak erişimi çalışma kitabı kaynak sekmesini görüntüleyin.
    * Çalışma kitabını silin veya yeniden adlandırın.
    * Çalışma kitabını panoya sabitle.

### <a name="workbooks-versus-workbook-templates"></a>Çalışma kitapları ve çalışma kitabı şablonları

Bir _çalışma kitabını_ yeşil renkte ve çok sayıda _çalışma kitabı şablonunu_ mor olarak görebilirsiniz. Şablonlar, birden çok kullanıcı ve takımlar tarafından esnek yeniden kullanım için tasarlanan, seçkin raporlar olarak görev yapar. Bir şablonu açmak, şablonun içeriğiyle doldurulmuş geçici bir çalışma kitabı oluşturur.

İş arkadaşları için gelecekteki raporlama deneyimini bozmadan, şablon tabanlı çalışma kitabının parametrelerini ayarlayabilir ve analiz gerçekleştirebilirsiniz. Bir şablon açarsanız, bazı ayarlamalar yapar ve ardından Kaydet simgesini seçerseniz, şablonu orijinal şablondan dokunulmadan önce yeşil olarak gösterilecek bir çalışma kitabı olarak kaydedersiniz.

Aynı zamanda şablonlar, kaydedilen çalışma kitaplarından de farklılık gösterir. Çalışma kitabının kaydedilmesi ilişkili bir Azure Resource Manager kaynağı oluşturur, ancak yalnızca bir şablonu açarken oluşturulan geçici çalışma kitabının kendisiyle ilişkili benzersiz bir kaynağı olmadığında oluşturulur. Çalışma kitaplarında erişim denetiminin nasıl yönetildiği hakkında daha fazla bilgi edinmek için, [çalışma kitapları erişim denetimi makalesine](../visualize/workbooks-access-control.md)başvurun.

### <a name="exploring-a-workbook-template"></a>Çalışma kitabı şablonunu keşfetme

Varsayılan uygulama çalışma kitabı şablonlarından birini görmek için **uygulama hata analizi** ' ni seçin.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="Uygulama hatası çözümleme şablonunun ekran görüntüsü." border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

Daha önce belirtildiği gibi, şablonu açmak, ile etkileşime girebilmeniz için geçici bir çalışma kitabı oluşturur. Çalışma kitabı, varsayılan olarak, yalnızca özgün şablon yazarı tarafından oluşturulan amaçlanan çözümleme deneyiminin bilgilerini görüntüleyen okuma modunda açılır.

Bu çalışma kitabı söz konusu olduğunda, deneyim etkileşimli olur. Bir abonelik, hedeflenen uygulamalar ve göstermek istediğiniz verilerin zaman aralığını ayarlayabilirsiniz. Bu seçimleri yaptıktan sonra, tek bir satırın seçilmesi, raporun alt kısmındaki iki grafikte hangi verilerin işleneceğini değiştirecek bir de etkileşimli olur.

### <a name="editing-mode"></a>Düzen modu

Bu çalışma kitabı şablonunun nasıl bir araya yerleştirileceğini anlamak için, **Düzenle**' ye tıklayarak düzenleme moduna geçiş yapmanız gerekir.

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="Çalışma kitaplarındaki düzenleme düğmesinin ekran görüntüsü." border="false" :::

Düzenleme moduna geçtikten sonra, çalışma kitabınızın her bir yönüyle ilgili olarak doğru bir dizi **düzenleme** kutusu göründüğünü fark edersiniz.

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="Düzenleme düğmesinin ekran görüntüsü." border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

İstek verileri kılavuzunun hemen altındaki Düzenle düğmesini seçmemiz durumunda, çalışma kitabımızın bu bölümünün Application Insights bir kaynaktaki verilere karşı bir kusto sorgusundan oluştuğunu görebiliriz.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="Temel kusto sorgusunun ekran görüntüsü." border="false" lightbox="./media/workbooks-overview/kusto.png":::

Sağdaki diğer **düzenleme** düğmelerinin seçilmesi, markı tabanlı [metin kutuları](../visualize/workbooks-text-visualizations.md), [parametre seçimi](../visualize/workbooks-parameters.md) Kullanıcı arabirimi öğeleri ve diğer [grafik/görselleştirme türleri](#visualizations)gibi çalışma kitaplarını oluşturan çekirdek bileşenlerin sayısını açığa çıkarır.

Önceden oluşturulmuş şablonları düzenleme modunda inceleyerek, bunları gereksinimlerinize uyacak şekilde değiştirerek ve kendi özel çalışma kitabınızı kaydederek Azure Izleyici çalışma kitaplarında mümkün olan şeyler hakkında bilgi edinmek için harika bir yoldur.

## <a name="dashboard-time-ranges"></a>Pano zaman aralıkları

Sabitlenmiş öğe bir *zaman aralığı* parametresi kullanacak şekilde yapılandırıldıysa, sabitlenmiş çalışma kitabı sorgu parçaları panonun zaman aralığına göre değişir. Panonun zaman aralığı değeri, zaman aralığı parametresinin değeri olarak kullanılır ve Pano zaman aralığının herhangi bir değişikliği sabitlenmiş öğenin güncelleştirilmesine neden olur. Sabitlenmiş bir bölüm panonun zaman aralığını kullanıyorsa, zaman aralığı her değiştiğinde panonun zaman aralığını göstermek için sabitlenmiş bölüm güncelleştirmesinin alt başlığını görürsünüz.

Ayrıca, bir zaman aralığı parametresi kullanan sabitlenmiş çalışma kitabı parçaları, panonun zaman aralığı tarafından belirlenen bir hızda otomatik olarak yenilenir. Sorgunun çalıştırıldığı son zaman sabitlenmiş parçanın alt başlığı içinde görüntülenir.

Sabitlenmiş bir adımda açıkça ayarlanmış bir zaman aralığı varsa (bir zaman aralığı parametresi kullanılmaz), pano ayarlarından bağımsız olarak bu zaman aralığı Pano için her zaman kullanılır. Sabitlenmiş parçanın alt başlığı panonun zaman aralığını göstermez ve sorgu, panoda otomatik olarak yenilemeyecektir. Alt başlık, sorgunun son çalıştırıldığı zamanı gösterecektir.

> [!NOTE]
> *Birleştirme* veri kaynağını kullanan sorgular, panolara sabitlenmesi için şu anda desteklenmiyor.

## <a name="sharing-workbook-templates"></a>Çalışma kitabı şablonlarını paylaşma

Kendi çalışma kitabı şablonlarınızı oluşturmaya başladıktan sonra, bunu daha geniş bir topluluk ile paylaşmak isteyebilirsiniz. Daha fazla bilgi edinmek ve varsayılan Azure Izleyici Galerisi görünümünün parçası olmayan diğer şablonları araştırmak için [GitHub deponuzu](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)ziyaret edin. Mevcut çalışma kitaplarına gitmek için GitHub 'daki [çalışma kitabı kitaplığını](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) ziyaret edin.

## <a name="next-step"></a>Sonraki adım

* Çok sayıda zengin görselleştirmeler seçeneği hakkında daha fazla [bilgi edinmeye başlayın](#visualizations) .
* Çalışma kitabı kaynaklarınıza erişimi [denetleme](../visualize/workbooks-access-control.md) ve paylaşma.