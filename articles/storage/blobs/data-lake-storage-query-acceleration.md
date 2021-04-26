---
title: Azure Data Lake Storage sorgu hızlandırma
description: Sorgu hızlandırma, uygulama ve analiz çerçevelerinin yalnızca bir işleme işlemi için gerekli olan verileri alarak veri işlemeyi önemli ölçüde iyileştirmesine olanak sağlar.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 09/09/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: dc1d217dba64c36aa219abbd4d2220a494347689
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912765"
---
# <a name="azure-data-lake-storage-query-acceleration"></a>Azure Data Lake Storage sorgu hızlandırma

Sorgu hızlandırma, uygulama ve analiz çerçevelerinin yalnızca belirli bir işlemi gerçekleştirmek için gereksinim duydukları verileri alarak veri işlemeyi önemli ölçüde iyileştirmesini sağlar. Bu, depolanan veriler hakkında kritik Öngörüler kazanmak için gereken süre ve işleme gücünü azaltır.

## <a name="overview"></a>Genel Bakış

Sorgu hızlandırma, verilerin diskten okunışında *satırları ve sütunları filtrelemesini sağlayan* filtreleme koşullarını ve *sütun projeksiyonlarını* kabul eder. Yalnızca bir koşulun koşullarını karşılayan veriler ağ üzerinden uygulamaya aktarılır. Bu, ağ gecikmesini ve işlem maliyetini azaltır.  

Bir sorgu hızlandırma isteğindeki satır filtresi koşullarını ve sütun projeksiyonlarını belirtmek için SQL 'i kullanabilirsiniz. İstek yalnızca bir dosya işler. Bu nedenle, SQL 'in JOIN ve Group By toplamalara göre gelişmiş ilişkisel özellikleri desteklenmez. Sorgu hızlandırma, her istek için girdi olarak CSV ve JSON biçimli verileri destekler.

Sorgu hızlandırma özelliği Data Lake Storage (hiyerarşik ad alanı üzerinde etkinleştirilmiş depolama hesapları) ile sınırlı değildir. Sorgu hızlandırma, hiyerarşik bir ad alanı **etkin olmayan depolama** hesaplarındaki bloblarla tamamen uyumludur. Bu, depolama hesaplarında zaten Bloblar olarak depoladığınız verileri işlemeniz durumunda ağ gecikmesi ve işlem maliyetlerinde aynı azalmayı elde edebilmeniz anlamına gelir.

İstemci uygulamasında sorgu hızlandırmasının nasıl kullanılacağına ilişkin bir örnek için, bkz. [Azure Data Lake Storage sorgu hızlandırma kullanarak verileri filtreleme](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Veri akışı

Aşağıdaki diyagramda, tipik bir uygulamanın verileri işlemek için sorgu hızlandırmasını nasıl kullandığı gösterilmektedir.

> [!div class="mx-imgBorder"]
> ![Sorgu hızlandırmayı genel bakış](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. İstemci uygulaması, koşullar ve sütun projeksiyonlarını belirterek dosya verileri ister.

2. Sorgu hızlandırma belirtilen SQL sorgusunu ayrıştırır ve verileri ayrıştırmaya ve filtrelemeye yönelik işi dağıtır.

3. İşlemciler diskten verileri okur, uygun biçimi kullanarak verileri ayrıştırır ve ardından belirtilen koşullara ve sütun projeksiyonlarını uygulayarak verileri filtreler.

4. Sorgu hızlandırma, yanıt parçalarını istemci uygulamasına geri akışı için birleştirir.

5. İstemci uygulaması, akışlı yanıtı alır ve ayrıştırır. Uygulamanın herhangi bir ek veriyi filtrelemesine ihtiyacı yoktur ve istenen hesaplamayı veya dönüştürmeyi doğrudan uygulayabilirler.

## <a name="better-performance-at-a-lower-cost"></a>Daha düşük bir maliyetle daha iyi performans

Sorgu hızlandırma, uygulamanız tarafından aktarılan ve işlenen veri miktarını azaltarak performansı iyileştirir.

Toplanan bir değeri hesaplamak için, uygulamalar genellikle bir dosyadaki **Tüm** verileri alır ve sonra verileri yerel olarak işleyin ve filtreler. Analiz iş yükleri için giriş/çıkış desenlerinin çözümlenmesi, uygulamaların, belirli hesaplamaları gerçekleştirmek üzere okudukları verilerin yalnızca %20 ' sini gerektirdiğini açığa çıkarır. Bu istatistik, [bölüm ayıklama](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md#hive-partitioning)gibi teknikler uygulandıktan sonra da geçerlidir. Bu, söz konusu verilerin %80 ' i ağ üzerinden, ayrıştırılarak ve uygulama tarafından filtrelenebilir olması anlamına gelir. Bu model, aslında gereksiz verileri kaldırmak için tasarlanan önemli bir işlem maliyeti doğurur.  

Azure, sektör lideri bir ağı, hem verimlilik hem de gecikme süresi bakımından, bu ağ genelinde sorunsuz bir şekilde aktarmak, uygulama performansı için hala maliyetlidir. Sorgu hızlandırma, depolama isteği sırasında istenmeyen verileri filtreleyerek bu maliyeti ortadan kaldırır.

Ayrıca, gereksiz verileri ayrıştırmak ve filtrelemek için gereken CPU yükü, uygulamanızın işini yapmak için daha fazla sayıda ve daha büyük VM sağlamasını gerektirir. Bu işlem yükünü sorgu hızlandırılarak aktararak, uygulamalar önemli maliyet tasarruflarını fark edebilir.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Sorgu hızlandırmasının avantajlarından faydalanabilecek uygulamalar

Sorgu hızlandırma, dağıtılmış analiz çerçeveleri ve veri işleme uygulamaları için tasarlanmıştır. 

Apache Spark ve Apache Hive gibi dağıtılmış analiz çerçeveleri Framework içine bir depolama soyutlama katmanı içerir. Bu altyapılar Ayrıca, Kullanıcı sorguları için en iyi sorgu planını belirlerken temel alınan g/ç hizmetinin yeteneklerini kapsayan sorgu iyileştiricilerin de yer alır. Bu çerçeveler sorgu hızlandırmayı tümleştirmeye başlıyor. Sonuç olarak, bu çerçevelerin kullanıcıları sorguları üzerinde herhangi bir değişiklik yapmak zorunda kalmadan geliştirilmiş sorgu gecikmesini ve daha düşük toplam sahip olma maliyetini görecektir. 

Sorgu hızlandırma, veri işleme uygulamaları için de tasarlanmıştır. Bu tür uygulamalar genellikle, analiz öngörülerine doğrudan yol açabilecek büyük ölçekli veri dönüştürmeleri gerçekleştirir, bu sayede her zaman kurulu olan dağıtılmış analitik çerçeveleri kullanmaz. Bu uygulamalar genellikle doğrudan sorgu hızlandırma gibi özelliklerden yararlanabilmeleri için temeldeki depolama hizmetiyle daha doğrudan ilişkiye sahiptir. 

Bir uygulamanın sorgu hızlandırmasını nasıl tümleştirebileceğinizi gösteren bir örnek için, bkz. [Azure Data Lake Storage sorgu hızlandırma kullanarak verileri filtreleme](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Fiyatlandırma

Azure Data Lake Storage hizmeti içindeki daha fazla işlem yükü nedeniyle, sorgu hızlandırmasını kullanmaya yönelik fiyatlandırma modelinin normal Azure Data Lake Storage işlem modelinden farkı vardır. Sorgu hızlandırma, taranan veri miktarına ve çağırana döndürülen veri miktarına yönelik bir ücret ödemesine yönelik bir maliyet ücretlendirir. Daha fazla bilgi için bkz. [Azure Data Lake Storage 2. fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/data-lake/).

Faturalama modelinde değişikliğe rağmen sorgu hızlandırmanın fiyatlandırma modeli, bir iş yükü için toplam sahip olma maliyetini düşürmek üzere tasarlanmıştır ve bu da daha pahalı VM maliyetlerinde azalma olur.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Lake Storage sorgu hızlandırmasını kullanarak verileri filtreleme](data-lake-storage-query-acceleration-how-to.md)
- [Sorgu hızlandırma SQL dil başvurusu](query-acceleration-sql-reference.md)