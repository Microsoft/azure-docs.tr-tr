---
title: Azure Izleyici 'de Görünüm Tasarımcısı kutucuklarına yönelik başvuru kılavuzu | Microsoft Docs
description: Azure Izleyici 'de Görünüm Tasarımcısı 'nı kullanarak, Azure portal görüntülenen ve Log Analytics çalışma alanındaki veriler üzerinde çeşitli görselleştirmeler içeren özel görünümler oluşturabilirsiniz. Bu makale, özel Görünümleriniz için kullanılabilen kutucukların ayarlarına yönelik bir başvuru kılavuzudur.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: 4141a4728ef5a7dd8896e4e3f21ee6d1d4dce958
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931967"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Azure Izleyici 'de tasarımcı kutucuklarını görüntülemek için başvuru kılavuzu
Azure Izleyici 'de Görünüm Tasarımcısı 'nı kullanarak, Azure portal Log Analytics çalışma alanınızdaki verileri görselleştirmenize yardımcı olabilecek çeşitli özel görünümler oluşturabilirsiniz. Bu makale, özel Görünümleriniz için kullanılabilen kutucukların ayarlarına yönelik bir başvuru kılavuzudur.

Görünüm Tasarımcısı hakkında daha fazla bilgi için bkz.

* [Görünüm Tasarımcısı](view-designer.md): özel görünümleri oluşturma ve düzenlemeyle Ilgili görünüm tasarımcısına ve yordamlarına genel bir bakış sağlar.
* [Görselleştirme bölümü başvurusu](view-designer-parts.md): özel görünümlerinizdeki kullanılabilir görselleştirme bölümlerinin ayarlarına bir başvuru kılavuzu sağlar.


Kullanılabilir görünüm Tasarımcısı kutucukları aşağıdaki tabloda açıklanmıştır:  

| Kaldır | Açıklama |
|:--- |:--- |
| [Sayısından](#number-tile) |Bir sorgudaki kayıt sayısı. |
| [İki sayı](#two-numbers-tile) |İki farklı sorgudan kayıt sayısı. |
| [Halka](#donut-tile) | Ortasında bir Özet değeri olan bir sorguyu temel alan bir grafik. |
| Çizgi grafik ve belirtme çizgisi | Sorguya dayalı bir çizgi grafiği ve Özet değeri olan bir belirtme çizgisi. |
| [Çizgi grafik](#line-chart-tile) |Bir sorguyu temel alan bir çizgi grafik. |
| [İki zaman çizelgesi](#two-timelines-tile) | Her biri ayrı bir sorguya bağlı olarak, iki serisi olan bir sütun grafiği. |

Sonraki bölümlerde, kutucuk türleri ve özellikleri ayrıntılı olarak açıklanır.

> [!NOTE]
> Görünümlerindeki kutucuklar Log Analytics çalışma alanınızdaki [günlük sorgularını](../log-query/log-query-overview.md) temel alır. Bunlar şu anda Application Insights verileri almak için [çapraz kaynak sorgularını](../log-query/cross-workspace-query.md) desteklemezler.

## <a name="number-tile"></a>Sayı kutucuğu
**Sayı** kutucuğu, bir günlük sorgusundan kayıt sayısını ve bir etiketi görüntüler.

![Sayı kutucuğu](media/view-designer-tiles/tile-number.png)

| Ayar | Açıklama |
|:--- |:--- |
| Adı |Kutucuğun en üstünde görüntülenen metin. |
| Açıklama |Kutucuk adının altında görüntülenen metin. |
| **Kaldır** | |
| Deki |Değerin altında görüntülenen metin. |
| Sorgu |Çalıştırılan sorgu. Sorgu tarafından döndürülen kayıtların sayısı görüntülenir. |
| **İleri** |**> Veri akışı doğrulaması** |
| Etkin |Kutucuk için veri akışı doğrulamasının etkinleştirilmesi gerekiyorsa bu bağlantıyı seçin. Bu yaklaşım, veriler kullanılamıyorsa alternatif bir ileti sağlar. Genellikle, görünümün yüklendiği ve verilerin kullanılabilir hale geldiği geçici süre boyunca bir ileti sağlamak için yaklaşımı kullanırsınız. |
| Sorgu |Verilerin görünüm için kullanılabilir olup olmadığını tespit etmek için çalıştırılan sorgu. Sorgu hiçbir sonuç döndürürse, ana sorgunun değerinin yerine bir ileti görüntülenir. |
| İleti |Veri akışı doğrulama sorgusu veri döndürürse görüntülenen ileti. İleti sağlamazsanız, bir *değerlendirme* durumu iletisi görüntülenir. |


## <a name="two-numbers-tile"></a>İki sayı kutucuğu
Bu kutucuk, iki farklı günlük sorgusunun kayıt sayısını ve her biri için bir etiketi görüntüler.

![İki sayı kutucuğu](media/view-designer-tiles/tile-two-numbers.png)

| Ayar | Açıklama |
|:--- |:--- |
| Adı |Kutucuğun en üstünde görüntülenen metin. |
| Açıklama |Kutucuk adının altında görüntülenen metin. |
| **İlk kutucuk** | |
| Deki |Değerin altında görüntülenen metin. |
| Sorgu |Çalıştırılan sorgu. Sorgu tarafından döndürülen kayıtların sayısı görüntülenir. |
| **İkinci kutucuk** | |
| Deki |Değerin altında görüntülenen metin. |
| Sorgu |Çalıştırılan sorgu. Sorgu tarafından döndürülen kayıtların sayısı görüntülenir. |
| **İleri** |**> Veri akışı doğrulaması** |
| Etkin |Kutucuk için veri akışı doğrulamasının etkinleştirilmesi gerekiyorsa bu bağlantıyı seçin. Bu yaklaşım, veriler kullanılamıyorsa alternatif bir ileti sağlar. Genellikle, görünümün yüklendiği ve verilerin kullanılabilir hale geldiği geçici süre boyunca bir ileti sağlamak için yaklaşımı kullanırsınız. |
| Sorgu |Verilerin görünüm için kullanılabilir olup olmadığını tespit etmek için çalıştırılan sorgu. Sorgu hiçbir sonuç döndürürse, ana sorgunun değerinin yerine bir ileti görüntülenir. |
| İleti |Veri akışı doğrulama sorgusu veri döndürürse görüntülenen ileti. İleti sağlamazsanız, bir *değerlendirme* durumu iletisi görüntülenir. |


## <a name="donut-tile"></a>Halka kutucuk
**Halka** kutucuğu, günlük sorgusunda bir değer sütununu özetleyen tek bir sayı görüntüler. Halka, üst üç kaydın sonuçlarını grafik olarak görüntüler.

![Halka kutucuk](media/view-designer-tiles/tile-donut.png)

| Ayar | Açıklama |
|:--- |:--- |
| Adı |Kutucuğun en üstünde görüntülenen metin. |
| Açıklama |Kutucuk adının altında görüntülenen metin. |
| **Halka** | |
| Sorgu |Halka için çalıştırılan sorgu. İlk özellik bir metin değeridir ve ikinci özellik sayısal bir değerdir. Bu sorgu, sonuçları özetlemek için normalde *Measure* anahtar sözcüğünü kullanır. |
| **Halka** |**> Merkezi** |
| Metin |Halka içindeki değer altında görüntülenen metin. |
| İşlem |Değer özelliğinde gerçekleştirilen ve tek bir değer olarak özetlemek için gerçekleştirilen işlem.<ul><li>Sum: özellik değerine sahip tüm kayıtların değerlerini ekleyin.</li><li>Yüzde: özellik değerine sahip kayıtlardan toplanan değerlerin yüzdesi, tüm kayıtların toplanan değerleriyle karşılaştırılır.</li></ul> |
| Merkez işleminde kullanılan sonuç değerleri |İsteğe bağlı olarak, bir veya daha fazla değer eklemek için artı işaretini (+) seçin. Sorgunun sonuçları, belirttiğiniz özellik değerleriyle kayıtlarla sınırlıdır. Hiçbir değer eklenmediyse, tüm kayıtlar sorguya dahil edilir. |
| **Halka** |**> Ek seçenekler** |
| Renkler |Üç üst özelliklerden her biri için görüntülenen renk. Belirli özellik değerleri için alternatif renkler belirtmek üzere *Gelişmiş renk eşleme*kullanın. |
| Gelişmiş renk eşleme |Belirli özellik değerlerini temsil eden bir renk görüntüler. Belirttiğiniz değer en üstteki üçünde, standart renk yerine alternatif renk görüntülenir. Özellik en üst üç üzerinde değilse, renk gösterilmez. |
| **İleri** |**> Veri akışı doğrulaması** |
| Etkin |Kutucuk için veri akışı doğrulamasının etkinleştirilmesi gerekiyorsa bu bağlantıyı seçin. Bu yaklaşım, veriler kullanılamıyorsa alternatif bir ileti sağlar. Genellikle, görünümün yüklendiği ve verilerin kullanılabilir hale geldiği geçici süre boyunca bir ileti sağlamak için yaklaşımı kullanırsınız. |
| Sorgu |Verilerin görünüm için kullanılabilir olup olmadığını tespit etmek için çalıştırılan sorgu. Sorgu hiçbir sonuç döndürürse, ana sorgunun değerinin yerine bir ileti görüntülenir. |
| İleti |Veri akışı doğrulama sorgusu veri döndürürse görüntülenen ileti. İleti sağlamazsanız, bir *değerlendirme* durumu iletisi görüntülenir. |


## <a name="line-chart-tile"></a>Çizgi grafik kutucuğu
Bu kutucuk, zaman içinde günlük sorgusundan birden çok seriyi görüntüleyen bir çizgi grafiğidir. 

![Çizgi grafik ve belirtme çizgisi kutucuğu](media/view-designer-tiles/tile-line-chart.png)

| Ayar | Açıklama |
|:--- |:--- |
| Adı |Kutucuğun en üstünde görüntülenen metin. |
| Açıklama |Kutucuk adının altında görüntülenen metin. |
| **Çizgi grafik** | |
| Sorgu |Çizgi grafik için çalıştırılan sorgu. İlk özellik bir metin değeridir ve ikinci özellik sayısal bir değerdir. Bu sorgu, sonuçları özetlemek için normalde *Measure* anahtar sözcüğünü kullanır. Sorgu *Interval* anahtar sözcüğünü kullanıyorsa, x ekseni bu zaman aralığını kullanır. Sorgu *Interval* anahtar sözcüğünü kullanmıyorsa, x ekseni saatlik aralıkları kullanır. |
| **Çizgi grafik** |**> Y ekseni** |
| Logaritmik ölçek kullan |Y ekseni için Logaritmik ölçek kullanmak üzere bu bağlantıyı seçin. |
| Birimler |Sorgu tarafından döndürülen değerler için birimleri belirtin. Bu bilgiler, grafik üzerinde değer türlerini ve isteğe bağlı olarak değerleri dönüştürmek için Etiketler göstermek için kullanılır. **Birim türü** birimin kategorisini belirtir ve kullanılabilir olan **geçerli birim türü** değerlerini tanımlar. **Dönüştür** ' de bir değer seçerseniz, sayısal değerler **geçerli birim** türünden **dönüştürme** türüne dönüştürülür. |
| Özel etiket |*Birim* türü etiketinin yanında y ekseni için görüntülenen metin. Hiçbir etiket belirtilmemişse, yalnızca *birim* türü görüntülenir. |
| **İleri** |**> Veri akışı doğrulaması** |
| Etkin |Kutucuk için veri akışı doğrulamasının etkinleştirilmesi gerekiyorsa bu bağlantıyı seçin. Bu yaklaşım, veriler kullanılamıyorsa alternatif bir ileti sağlar. Genellikle, görünümün yüklendiği ve verilerin kullanılabilir hale geldiği geçici süre boyunca bir ileti sağlamak için yaklaşımı kullanırsınız. |
| Sorgu |Verilerin görünüm için kullanılabilir olup olmadığını tespit etmek için çalıştırılan sorgu. Sorgu hiçbir sonuç döndürürse, ana sorgunun değerinin yerine bir ileti görüntülenir. |
| İleti |Veri akışı doğrulama sorgusu veri döndürürse görüntülenen ileti. İleti sağlamazsanız, bir *değerlendirme* durumu iletisi görüntülenir. |


## <a name="line-chart-and-callout-tile"></a>Çizgi grafik ve belirtme çizgisi kutucuğu
Bu kutucukta, zaman içinde bir günlük sorgusundan birden çok seri görüntüleyen ve özetlenen değere sahip bir belirtme çizgisi içeren çizgi grafik bulunur. 

![Çizgi grafik ve belirtme çizgisi kutucuğu](media/view-designer-tiles/tile-line-chart-callout.png)

| Ayar | Açıklama |
|:--- |:--- |
| Adı |Kutucuğun en üstünde görüntülenen metin. |
| Açıklama |Kutucuk adının altında görüntülenen metin. |
| **Çizgi grafik** | |
| Sorgu |Çizgi grafik için çalıştırılan sorgu. İlk özellik bir metin değeridir ve ikinci özellik sayısal bir değerdir. Bu sorgu, sonuçları özetlemek için normalde *Measure* anahtar sözcüğünü kullanır. Sorgu *Interval* anahtar sözcüğünü kullanıyorsa, x ekseni bu zaman aralığını kullanır. Sorgu *Interval* anahtar sözcüğünü kullanmıyorsa, x ekseni saatlik aralıkları kullanır. |
| **Çizgi grafik** |**> Belirtme çizgisi** |
| Belirtme çizgisi başlığı | Belirtme çizgisi değerinin üstünde görüntülenen metin. |
| Seri adı |Belirtme çizgisi değeri olarak kullanılacak seri özelliği değeri. Hiçbir seri sağlanmazsa, sorgudaki tüm kayıtlar kullanılır. |
| İşlem |Değer özelliğinde gerçekleştirilen işlem, belirtme çizgisi için tek bir değer olarak özetleme.<ul><li>Average: tüm kayıtlardaki değerlerin ortalaması.</li><li>Sayı: sorgu tarafından döndürülen tüm kayıtların sayısı.</li><li>Son örnek: grafiğe dahil edilen son aralığın değeri.</li><li>Max: grafiğe dahil edilen aralıkların en büyük değeri.</li><li>Min: grafiğe dahil edilen aralıkların minimum değeri.</li><li>Sum: tüm kayıtlardaki değerlerin toplamı.</li></ul> |
| **Çizgi grafik** |**> Y ekseni** |
| Logaritmik ölçek kullan |Y ekseni için Logaritmik ölçek kullanmak üzere bu bağlantıyı seçin. |
| Birimler |Sorgu tarafından döndürülecek değerler için birimleri belirtin. Bu bilgiler, değer türlerini belirten ve isteğe bağlı olarak değerleri dönüştürmek için grafik etiketlerini göstermek için kullanılır. *Birim* türü birimin kategorisini belirtir ve kullanılabilir *geçerli birim* türü değerlerini tanımlar. *Dönüştür*' de bir değer seçerseniz, sayısal değerler *geçerli birim* türünden *dönüştürme* türüne dönüştürülür. |
| Özel etiket |*Birim* türü etiketinin yanında y ekseni için görüntülenen metin. Hiçbir etiket belirtilmemişse, yalnızca *birim* türü görüntülenir. |
| **İleri** |**> Veri akışı doğrulaması** |
| Etkin |Kutucuk için veri akışı doğrulamasının etkinleştirilmesi gerekiyorsa bu bağlantıyı seçin. Bu yaklaşım, veriler kullanılamıyorsa alternatif bir ileti sağlar. Genellikle, görünümün yüklendiği ve verilerin kullanılabilir hale geldiği geçici süre boyunca bir ileti sağlamak için yaklaşımı kullanırsınız. |
| Sorgu |Verilerin görünüm için kullanılabilir olup olmadığını tespit etmek için çalıştırılan sorgu. Sorgu hiçbir sonuç döndürürse, ana sorgunun değerinin yerine bir ileti görüntülenir. |
| İleti |Veri akışı doğrulama sorgusu veri döndürürse görüntülenen ileti. İleti sağlamazsanız, bir *değerlendirme* durumu iletisi görüntülenir. |


## <a name="two-timelines-tile"></a>İki zaman çizelgesi kutucuğu
**İki zaman çizelgesi** kutucuğu, zaman içindeki iki günlük sorgusunun sonuçlarını sütun grafikleri olarak görüntüler. Her seri için bir belirtme çizgisi görüntülenir. 

![İki zaman çizelgesi kutucuğu](media/view-designer-tiles/tile-two-timelines.png)

| Ayar | Açıklama |
|:--- |:--- |
| Adı |Kutucuğun en üstünde görüntülenen metin. |
| Açıklama |Kutucuk adının altında görüntülenen metin. |
| İlk grafik | |
| Deki |İlk serinin belirtme çizgisinin altında görüntülenen metin. |
| Renk |İlk serideki sütunlar için kullanılan renk. |
| Grafik sorgusu |İlk seri için çalıştırılan sorgu. Her zaman aralığı içindeki kayıtların sayısı grafik sütunları tarafından temsil edilir. |
| İşlem |Değer özelliğinde gerçekleştirilen işlem, belirtme çizgisi için tek bir değer olarak özetleme.<ul><li>Average: tüm kayıtlardaki değerlerin ortalaması.</li><li>Sayı: sorgu tarafından döndürülen tüm kayıtların sayısı.</li><li>Son örnek: grafiğe dahil edilen son aralığın değeri.</li><li>Max: grafiğe dahil edilen aralıkların en büyük değeri.</li></ul> |
| **İkinci grafik** | |
| Deki |İkinci serinin belirtme çizgisinin altında görüntülenen metin. |
| Renk |İkinci serideki sütunlar için kullanılan renk. |
| Grafik sorgusu |İkinci seri için çalıştırılan sorgu. Her zaman aralığı içindeki kayıtların sayısı grafik sütunları tarafından temsil edilir. |
| İşlem |Değer özelliğinde gerçekleştirilen işlem, belirtme çizgisi için tek bir değer olarak özetleme.<ul><li>Average: tüm kayıtlardaki değerlerin ortalaması.</li><li>Sayı: sorgu tarafından döndürülen tüm kayıtların sayısı.</li><li>Son örnek: grafiğe dahil edilen son aralığın değeri.</li><li>Max: grafiğe dahil edilen aralıkların en büyük değeri. |
| **İleri** |**> Veri akışı doğrulaması** |
| Etkin |Kutucuk için veri akışı doğrulamasının etkinleştirilmesi gerekiyorsa bu bağlantıyı seçin. Bu yaklaşım, veriler kullanılamıyorsa alternatif bir ileti sağlar. Genellikle, görünümün yüklendiği ve verilerin kullanılabilir hale geldiği geçici süre boyunca bir ileti sağlamak için yaklaşımı kullanırsınız. |
| Sorgu |Verilerin görünüm için kullanılabilir olup olmadığını tespit etmek için çalıştırılan sorgu. Sorgu hiçbir sonuç döndürürse, ana sorgunun değerinin yerine bir ileti görüntülenir. |
| İleti |Veri akışı doğrulama sorgusu veri döndürürse görüntülenen ileti. İleti sağlamazsanız, bir *değerlendirme* durumu iletisi görüntülenir. |


## <a name="next-steps"></a>Sonraki adımlar
* Kutucuklarındaki sorguları destekleyecek [günlük sorguları](../log-query/log-query-overview.md) hakkında bilgi edinin.
* [Görselleştirme parçalarını](view-designer-parts.md) özel görünümverilerinize ekleyin.
