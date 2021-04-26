---
title: Veri Kataloğu kökenini Kullanıcı Kılavuzu (Önizleme)
description: Bu makalede, Azure purview 'ın Catalog kökenini özelliğine genel bakış sunulmaktadır.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: 8b08a60d484aa3d52600b8aef2f53d6ca8a04f9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952176"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Azure purview Veri Kataloğu kökenini Kullanıcı Kılavuzu

Bu makalede, Azure purview Veri Kataloğu 'ndaki Data kökenini özelliklerine genel bir bakış sunulmaktadır.

## <a name="background"></a>Arka Plan

Azure purview 'ın platform özelliklerinden biri, veri işleme tarafından oluşturulan veri kümeleri arasında kökenini gösterme imkanına sahiptir. Data Factory, veri paylaşma ve Power BI gibi sistemler, taşırken verilerin kökenini yakalar. Özel kökenini raporlama, Atlas kancaları ve REST API aracılığıyla da desteklenir.

## <a name="lineage-collection"></a>Kökenini koleksiyonu 
 Kurumsal veri sistemlerinden Azure purview ' de toplanan meta veriler, uçtan uca veri kökenini göstermek için genelinde ele alınır. Kökenini ' i takip eden veri sistemleri, aşağıdaki üç tür için büyük ölçüde kategorilere ayrılmıştır.

### <a name="data-processing-system"></a>Veri işleme sistemi
Veri tümleştirme ve ETL araçları, yürütme sırasında kökenini ' de Azure purview 'a gönderim yapabilir. Data Factory, veri paylaşma, SYNAPSE, Azure Databricks ve benzeri araçlar, bu veri sistemi kategorisine aittir. Veri işleme sistemleri, hedef veri kümeleri oluşturmak için farklı veritabanlarından ve depolama çözümlerinden kaynak olarak veri kümelerine başvurur. Kökenini için purview ile tümleştirilmiş olan veri işleme sistemlerinin listesi aşağıdaki tabloda listelenmiştir.


| Veri işleme sistemi | Desteklenen kapsam |
| ---------------------- | ------------|
| Azure Data Factory | [Kopyalama etkinliği](how-to-link-azure-data-factory.md#data-factory-copy-activity-support) <br> [Veri akışı etkinliği](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [SSIS paketi yürütme etkinliği](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Veri Paylaşımı | [Anlık görüntü paylaşma](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>Veri depolama sistemleri
SQL Server, Teradata ve SAP gibi depolama çözümlerinin & veritabanları, komut dosyası dili kullanarak verileri dönüştürmek için sorgu altyapılarına sahiptir. Saklı yordamlardan veri kökenini, ' de diğer sistemlerden kökenini ile birlikte toplanmaktadır.

| Veri depolama sistemi | Desteklenen kapsam |
| ---------------------- | ------------|
| Teradata | Saklı yordamlar

### <a name="data-analytics--reporting-systems"></a>Veri Analizi & raporlama sistemleri
Azure ML gibi veri sistemleri ve rapor kökenini Azure purview 'a Power BI. Bu sistemler, depolama sistemlerindeki veri kümelerini kullanır ve bı panosu, ML denemeleri vb. oluşturmak için meta modeliyle işlem görür.

| Veri Analizi & raporlama sistemi | Desteklenen kapsam |
| ---------------------- | ------------|
| Power BI | [Veri kümeleri, veri akışları, raporlar & panolar](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>Kökenini kullanmaya başlama

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWxTAK]

Kökenini içinde, veri kümeleri ve süreçler bulunur. İşlem kümeleri de düğümler olarak adlandırılır, ancak işlem kenarları da denir:

* **DataSet (node)**: bir işleme giriş olarak belirtilen bir veri kümesi (yapılandırılmış veya yapılandırılmamış). Örneğin, bir SQL tablosu, Azure Blob ve dosyaları (örneğin. csv ve. xml), tüm veri kümeleri olarak kabul edilir. Purview 'ın kökenini bölümünde, veri kümeleri dikdörtgen kutular tarafından temsil edilir.

* **İşlem (Edge)**: bir veri kümesi üzerinde gerçekleştirilen bir etkinlik veya dönüşüme işlem denir. Örneğin, ADF kopyalama etkinliği, veri paylaşma anlık görüntüsü vb. Purview 'ın kökenini bölümünde, süreçler yuvarlak kenarlı kutular tarafından temsil edilir.

Purview içindeki bir varlık için kökenini bilgilerine erişmek için şu adımları izleyin:

1. Azure portal [Azure purview hesapları sayfasına](https://aka.ms/purviewportal)gidin.

1. Listeden Azure sağlamayla hesabınızı seçin ve **genel bakış** sayfasından **sağlamayla hesabını Başlat** ' ı seçin.

1. Azure takip görünümü **giriş** sayfasında, bir veri kümesi adı veya ADF kopyalama veya veri akışı etkinliği gibi işlem adını arayın. Ve ardından ENTER tuşuna basın.

1. Arama sonuçlarından varlık ' ı seçin ve **kökenini** sekmesini seçin.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="Kökenini sekmesinin nasıl seçileceğini gösteren ekran görüntüsü." border="true":::

## <a name="asset-level-lineage"></a>Varlık düzeyi kökenini

Azure purview, veri kümeleri ve süreçler için varlık düzeyi kökenini destekler. Kökenini varlık düzeyini görmek için katalogdaki geçerli varlığın **kökenini** sekmesine gidin. Geçerli veri kümesi varlık düğümünü seçin. Varsayılan olarak, verilere ait olan sütunların listesi sol bölmede görüntülenir.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="Kökenini sayfasında sütunların görünüm seçme şeklini gösteren ekran görüntüsü" border="true":::

## <a name="dataset-column-lineage"></a>Veri kümesi sütunu kökenini

Bir veri kümesinin sütun düzeyi kökenini görmek için, katalogdaki geçerli varlığın **kökenini** sekmesine gidin ve aşağıdaki adımları izleyin:

1. Kökenini sekmesindeki sol bölmede, Data kökenini içinde görüntülenmesini istediğiniz her sütunun yanındaki onay kutusunu işaretleyin.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="Kökenini sayfasında görüntülenecek sütunların nasıl seçileceğini gösteren ekran görüntüsü." lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

2. Sütun eşlemeyi görmek için sol bölmedeki veya kökenini tuvalinin veri kümesindeki seçili bir sütunun üzerine gelin. Tüm sütun örnekleri vurgulanır.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="Bir veri kökenini yolundaki sütun akışını vurgulamak için bir sütun adının üzerine gelme şeklini gösteren ekran görüntüsü." lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

3. Sütun sayısı sol bölmede görüntülenebilenden daha büyükse, filtre seçeneğini kullanarak belirli bir sütunu ada göre seçin. Alternatif olarak, farenizi kullanarak listede kaydırma yapabilirsiniz.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="Kökenini sayfasında sütunların sütun adına göre filtreleneceğini gösteren ekran görüntüsü." lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

4. Kökenini tuvali daha fazla düğüm ve kenar içeriyorsa, veri varlığını seçmek veya düğümleri ada göre işlemek için filtreyi kullanın. Alternatif olarak, kökenini penceresini kaydırmak için farenizi de kullanabilirsiniz.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="Kökenini sayfasında veri varlık düğümlerini ada göre gösteren ekran görüntüsü." lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

5. Kökenini tuvalindeki veri kümelerinin listesini vurgulamak için sol bölmedeki geçişi kullanın. Geçişi kapatırsanız, seçilen sütunlardan en az birini içeren herhangi bir varlık görüntülenir. Geçişi açarsanız, yalnızca tüm sütunları içeren veri kümeleri görüntülenir.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="Kökenini sayfasındaki düğümlerin listesini filtrelemek için geçiş 'nin nasıl kullanılacağını gösteren ekran görüntüsü." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

## <a name="process-column-lineage"></a>İşlem sütunu kökenini
Veri işlemi bir veya daha fazla çıkış üretmek için bir veya daha fazla giriş veri kümesi alabilir. Purview 'da, sütun düzeyi kökenini işlem düğümleri için kullanılabilir. 
1. Sütunlar panelindeki açılan listeden giriş ve çıkış veri kümeleri arasında geçiş yapın.
2. Giriş veri kümesinden ilgili çıkış veri kümesine kökenini akışını görmek için bir veya daha fazla tablodan sütun seçin.

   :::image type="content" source="./media/catalog-lineage-user-guide/process-column-lineage.png" alt-text="İşlem düğümünün kökenini sütunlarını gösteren ekran görüntüsü." lightbox="./media/catalog-lineage-user-guide/process-column-lineage.png":::

## <a name="browse-assets-in-lineage"></a>Kökenini içindeki varlıklara gözatamıyorum
1. Kökenini görünümünden ilgili meta verilerini görüntülemek için herhangi bir varlık üzerinde **varlığa geç** ' i seçin. Bunun yapılması, kökenini görünümünden katalogdaki başka bir varlığa gözatmaya yönelik etkili bir yoldur.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="Bir kökenini veri varlığı içinde varlık geçişi seçme ekran görüntüsü." lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

2. Kökenini tuvali, popüler veri kümeleri için karmaşık hale gelebilir. Dağınıklığı önlemek için varsayılan görünüm yalnızca odak alanındaki varlık için beş düzey kökenini gösterir. Kökenini 'ın geri kalanı kökenini tuvalindeki kabarcıklara tıklanarak genişletilebilir. Veri tüketicileri, tuvaldeki hiçbir ilgisi olmayan varlıkları da gizleyebilir. Dağınıklığı daha fazla azaltmak için, kökenini tuvalinin en üstünde yer değiştirme **kökenini daha fazlasını** devre dışı bırakın. Bu eylem, kökenini tuvalindeki tüm kabarcıkları gizleyecek.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="Nasıl daha fazla kökenini geçiş yapılacağını gösteren ekran görüntüsü." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

3. Kökenini için en uygun görünümü elde etmek için kökenini tuvalindeki akıllı düğmeleri kullanın. Otomatik düzen, sığacak kadar Yakınlaştır, Yakınlaştır/kapat, tam ekran ve gezinti Haritası, katalogdaki bir modern kökenini deneyimi için kullanılabilir.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="Kökenini Smart düğmelerinin nasıl seçileceğini gösteren ekran görüntüsü." lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>Sonraki adımlar

* [Kökenini için Azure Data Factory bağlantısı](how-to-link-azure-data-factory.md)
* [Kökenini için Azure veri paylaşımıyla bağlantı](how-to-link-azure-data-share.md)