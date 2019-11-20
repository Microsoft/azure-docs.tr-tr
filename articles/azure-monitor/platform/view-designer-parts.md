---
title: Azure Izleyici 'de Görünüm Tasarımcısı bölümlerine yönelik başvuru kılavuzu | Microsoft Docs
description: Azure Izleyici 'de Görünüm Tasarımcısı 'nı kullanarak, Azure portal görüntülenen ve Log Analytics çalışma alanındaki veriler üzerinde çeşitli görselleştirmeler içeren özel görünümler oluşturabilirsiniz. Bu makale, özel görünümlerinizdeki kullanılabilir görselleştirme bölümlerinin ayarlarına yönelik bir başvuru kılavuzudur.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 853f0153283f31c9242b884babf5778f96cce141
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932002"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Azure Izleyici 'de tasarımcı görselleştirme parçalarını görüntülemek için başvuru kılavuzu
Azure Izleyici 'de Görünüm Tasarımcısı 'nı kullanarak, Azure portal Log Analytics çalışma alanınızdaki verileri görselleştirmenize yardımcı olabilecek çeşitli özel görünümler oluşturabilirsiniz. Bu makale, özel görünümlerinizdeki kullanılabilir görselleştirme bölümlerinin ayarlarına yönelik bir başvuru kılavuzudur.

Görünüm Tasarımcısı hakkında daha fazla bilgi için bkz.

* [Görünüm Tasarımcısı](view-designer.md): özel görünümleri oluşturma ve düzenlemeyle Ilgili görünüm tasarımcısına ve yordamlarına genel bir bakış sağlar.
* [Kutucuk başvurusu](view-designer-tiles.md): özel görünümlerinizin kullanılabilir her kutucuğun ayarlarına bir başvuru sağlar.


Kullanılabilir görünüm Tasarımcısı kutucuk türleri aşağıdaki tabloda açıklanmıştır:

| Görünüm türü | Açıklama |
|:--- |:--- |
| [Sorgu listesi](#list-of-queries-part) |Günlük sorgularının bir listesini görüntüler. Sonuçlarını göstermek için her bir sorguyu seçebilirsiniz. |
| [Sayı ve liste](#number-and-list-part) |Üst bilgi, bir günlük sorgusundan kayıt sayısını gösteren tek bir sayı görüntüler. Liste, bir sorgunun ilk on sonucunu, sayısal bir sütunun göreli değerini veya zaman içindeki değişikliğini gösteren bir grafiği görüntüler. |
| [İki sayı ve liste](#two-numbers-and-list-part) |Üst bilgi, ayrı günlük sorgularından kayıt sayılarını gösteren iki sayı görüntüler. Liste, bir sorgunun ilk on sonucunu, sayısal bir sütunun göreli değerini veya zaman içindeki değişikliğini gösteren bir grafiği görüntüler. |
| [Halka ve liste](#donut-and-list-part) |Üstbilgi, günlük sorgusunda bir değer sütununu özetleyen tek bir sayı görüntüler. Halka, üst üç kaydın sonuçlarını grafik olarak görüntüler. |
| [İki zaman çizelgesi ve liste](#two-timelines-and-list-part) |Üst bilgi, bir günlük sorgusunda değer sütununu özetleyen tek bir sayı gösteren bir belirtme çizgisi ile zaman içindeki iki günlük sorgusunun sonuçlarını sütun grafikleri olarak görüntüler. Liste, bir sorgunun ilk on sonucunu, sayısal bir sütunun göreli değerini veya zaman içindeki değişikliğini gösteren bir grafiği görüntüler. |
| [Bilgi](#information-part) |Üst bilgi, statik metin ve isteğe bağlı bir bağlantı görüntüler. Listede statik bir başlık ve metin içeren bir veya daha fazla öğe görüntülenir. |
| [Çizgi grafik, belirtme çizgisi ve liste](#line-chart-callout-and-list-part) |Üst bilgi, zaman içinde günlük sorgusundan birden çok serinin bulunduğu bir çizgi grafiği ve özetlenen değere sahip bir belirtme çizgisi görüntüler. Liste, bir sorgunun ilk on sonucunu, sayısal bir sütunun göreli değerini veya zaman içindeki değişikliğini gösteren bir grafiği görüntüler. |
| [Çizgi grafik ve liste](#line-chart-and-list-part) |Üst bilgi, zaman içinde günlük sorgusundan birden çok serinin bulunduğu bir çizgi grafik görüntüler. Liste, bir sorgunun ilk on sonucunu, sayısal bir sütunun göreli değerini veya zaman içindeki değişikliğini gösteren bir grafiği görüntüler. |
| [Çizgi grafik parçaları bölümü](#stack-of-line-charts-part) |Zaman içinde günlük sorgusundan birden çok serinin bulunduğu üç ayrı çizgi grafik görüntüler. |

Sonraki bölümlerde, kutucuk türleri ve özellikleri ayrıntılı olarak açıklanır.

> [!NOTE]
> Görünümlerdeki parçalar Log Analytics çalışma alanınızdaki [günlük sorgularını](../log-query/log-query-overview.md) temel alır. Bunlar şu anda Application Insights verileri almak için [çapraz kaynak sorgularını](../log-query/cross-workspace-query.md) desteklemezler.

## <a name="list-of-queries-part"></a>Sorgu listesi bölümü
Sorgu listesi bölümü, günlük sorgularının bir listesini görüntüler. Sonuçlarını göstermek için her bir sorguyu seçebilirsiniz. Görünüm, varsayılan olarak tek bir sorgu içerir ve ek sorgular eklemek için **+ sorgu** seçeneğini belirleyebilirsiniz.

![Sorgu görünümü listesi](media/view-designer-parts/view-list-queries.png)

| Ayar | Açıklama |
|:--- |:--- |
| **Genel** | |
| Başlık |Görünümün en üstünde görüntülenen metin. |
| Yeni Grup |Görünümde geçerli görünümden başlayarak yeni bir grup oluşturmak için bu bağlantıyı seçin. |
| Önceden seçilmiş Filtreler |Bir sorgu seçerken sol Filtre bölmesine dahil edilecek özelliklerin virgülle ayrılmış listesi. |
| Oluşturma modu |Sorgu seçildiğinde görüntülenen ilk görünüm. Sorguyu açtıktan sonra kullanılabilir görünümleri seçebilirsiniz. |
| **Sorgular** | |
| Arama sorgusu |Çalıştırılacak sorgu. |
| Kolay ad | Görüntülenen açıklayıcı ad. |

## <a name="number-and-list-part"></a>Sayı ve liste bölümü
Üst bilgi, bir günlük sorgusundan kayıt sayısını gösteren tek bir sayı görüntüler. Liste, bir sorgunun ilk on sonucunu, sayısal bir sütunun göreli değerini veya zaman içindeki değişikliğini gösteren bir grafiği görüntüler.

![Sorgu görünümü listesi](media/view-designer-parts/view-number-list.png)

| Ayar | Açıklama |
|:--- |:--- |
| **Genel** | |
| Grup başlığı |Görünümün en üstünde görüntülenen metin. |
| Yeni Grup |Görünümde geçerli görünümden başlayarak yeni bir grup oluşturmak için bu bağlantıyı seçin. |
| Simg |Üst bilgisinde sonucun yanında görüntülenen resim dosyası. |
| Simge kullan |Simgeyi göstermek için bu bağlantıyı seçin. |
| **Başlık** | |
| Deki |Üstbilginin üstünde görüntülenen metin. |
| Sorgu |Üst bilgi için çalıştırılacak sorgu. Sorgu tarafından döndürülen kayıtların sayısı görüntülenir. |
| Tıklama gezintisi | Üst bilgiye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Listele** | |
| Sorgu |Liste için çalıştırılacak sorgu. Sonuçlarda ilk on kaydın ilk iki özelliği görüntülenir. İlk özellik bir metin değeridir ve ikinci özellik sayısal bir değerdir. Çubuklar sayısal sütunun göreli değerine göre otomatik olarak oluşturulur.<br><br>Listedeki kayıtları sıralamak için sorgudaki `Sort` komutunu kullanın. Sorguyu çalıştırmak ve tüm kayıtları döndürmek için **Tümünü görüntüle**' yi seçebilirsiniz. |
| Grafiği gizle |Sayısal sütunun sağ tarafındaki grafiği devre dışı bırakmak için bu bağlantıyı seçin. |
| Mini grafikleri etkinleştir |Yatay çubuk yerine bir mini grafik göstermek için bu bağlantıyı seçin. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Renk |Çubukların veya mini grafiklerin rengi. |
| Ad ve değer ayırıcısı |Metin özelliğini birden çok değere ayrıştırmak için kullanılacak tek karakterlik sınırlayıcı. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Tıklama gezintisi | Listedeki bir öğeye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Listele** |**> Sütun başlıkları** |
| Adı |İlk sütunun en üstünde görüntülenen metin. |
| Değer |İkinci sütunun en üstünde görüntülenen metin. |
| **Listele** |**> Eşikleri** |
| Eşikleri etkinleştir |Eşikleri etkinleştirmek için bu bağlantıyı seçin. Daha fazla bilgi için bkz. [ortak ayarlar](#thresholds). |

## <a name="two-numbers-and-list-part"></a>İki sayı ve liste bölümü
Üst bilgide ayrı günlük sorgularından kayıt sayısını görüntüleyen iki sayı vardır. Liste, bir sorgunun ilk on sonucunu, sayısal bir sütunun göreli değerini veya zaman içindeki değişikliğini gösteren bir grafiği görüntüler.

![Liste görünümü & iki sayı](media/view-designer-parts/view-two-numbers-list.png)

| Ayar | Açıklama |
|:--- |:--- |
| **Genel** | |
| Grup başlığı |Görünümün en üstünde görüntülenen metin. |
| Yeni Grup |Görünümde geçerli görünümden başlayarak yeni bir grup oluşturmak için bu bağlantıyı seçin. |
| Simg |Üst bilgisinde sonucun yanında görüntülenen resim dosyası. |
| Simge kullan |Simgeyi göstermek için bu bağlantıyı seçin. |
| **Başlık gezintisi** | |
| Tıklama gezintisi | Üst bilgiye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Başlık** | |
| Deki |Üstbilginin üstünde görüntülenen metin. |
| Sorgu |Üst bilgi için çalıştırılacak sorgu. Sorgu tarafından döndürülen kayıtların sayısı görüntülenir. |
| **Listele** | |
| Sorgu |Liste için çalıştırılacak sorgu. Sonuçlarda ilk on kaydın ilk iki özelliği görüntülenir. İlk özellik bir metin değeridir ve ikinci özellik sayısal bir değerdir. Çubuklar sayısal sütunun göreli değerine göre otomatik olarak oluşturulur.<br><br>Listedeki kayıtları sıralamak için sorgudaki `Sort` komutunu kullanın. Sorguyu çalıştırmak ve tüm kayıtları döndürmek için **Tümünü görüntüle**' yi seçebilirsiniz. |
| Grafiği gizle |Sayısal sütunun sağ tarafındaki grafiği devre dışı bırakmak için bu bağlantıyı seçin. |
| Mini grafikleri etkinleştir |Yatay çubuk yerine bir mini grafik göstermek için bu bağlantıyı seçin. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Renk |Çubukların veya mini grafiklerin rengi. |
| İşlem |Mini grafik için gerçekleştirilecek işlem. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Ad ve değer ayırıcısı |Metin özelliğini birden çok değere ayrıştırmak için kullanılacak tek karakterlik sınırlayıcı. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Tıklama gezintisi | Listedeki bir öğeye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Listele** |**> Sütun başlıkları** |
| Adı |İlk sütunun en üstünde görüntülenen metin. |
| Değer |İkinci sütunun en üstünde görüntülenen metin. |
| **Listele** |**> Eşikleri** |
| Eşikleri etkinleştir |Eşikleri etkinleştirmek için bu bağlantıyı seçin. Daha fazla bilgi için bkz. [ortak ayarlar](#thresholds). |

## <a name="donut-and-list-part"></a>Halka ve liste bölümü
Üstbilgi, günlük sorgusunda bir değer sütununu özetleyen tek bir sayı görüntüler. Halka, üst üç kaydın sonuçlarını grafik olarak görüntüler.

![Halka ve liste görünümü](media/view-designer-parts/view-donut-list.png)

| Ayar | Açıklama |
|:--- |:--- |
| **Genel** | |
| Grup başlığı |Kutucuğun en üstünde görüntülenen metin. |
| Yeni Grup |Görünümde geçerli görünümden başlayarak yeni bir grup oluşturmak için bu bağlantıyı seçin. |
| Simg |Üst bilgisinde sonucun yanında görüntülenen resim dosyası. |
| Simge kullan |Simgeyi göstermek için bu bağlantıyı seçin. |
| **Üst Bilgi** | |
| Başlık |Üstbilginin üstünde görüntülenen metin. |
| Konu |Üstbilginin en üstünde bulunan başlık altında görüntülenen metin. |
| **Halka** | |
| Sorgu |Halka için çalıştırılacak sorgu. İlk özellik bir metin değeridir ve ikinci özellik sayısal bir değerdir. |
| Tıklama gezintisi | Üst bilgiye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Halka** |**> Merkezi** |
| Metin |Halka içindeki değer altında görüntülenen metin. |
| İşlem |Değer özelliğinde gerçekleştirilecek işlem, tek bir değer olarak özetlenir.<ul><li>Sum: tüm kayıtların değerlerini ekler.</li><li>Yüzde: **ortadaki işlemde kullanılan sonuç değerlerinde** değerler tarafından döndürülen kayıtların oranı, sorgudaki toplam kayda göre yapılır.</li></ul> |
| Merkez işleminde kullanılan sonuç değerleri |İsteğe bağlı olarak, bir veya daha fazla değer eklemek için artı işaretini (+) seçin. Sorgunun sonuçları, belirttiğiniz özellik değerleriyle kayıtlarla sınırlıdır. Hiçbir değer eklenmediyse, tüm kayıtlar sorguya dahil edilir. |
| **Ek seçenekler** |**> Renkler** |
| Renk 1<br>Renk 2<br>Renk 3 |Halka içinde görüntülenen değerlerin her biri için rengi seçin. |
| **Ek seçenekler** |**> Gelişmiş renk eşleme** |
| Alan değeri |Halka içerisine dahil olursa farklı bir renk olarak göstermek için bir alanın adını yazın. |
| Renk |Benzersiz alanın rengini seçin. |
| **Listele** | |
| Sorgu |Liste için çalıştırılacak sorgu. Sorgu tarafından döndürülen kayıtların sayısı görüntülenir. |
| Grafiği gizle |Sayısal sütunun sağ tarafındaki grafiği devre dışı bırakmak için bu bağlantıyı seçin. |
| Mini grafikleri etkinleştir |Yatay çubuk yerine bir mini grafik göstermek için bu bağlantıyı seçin. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Renk |Çubukların veya mini grafiklerin rengi. |
| İşlem |Mini grafik için gerçekleştirilecek işlem. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Ad ve değer ayırıcısı |Metin özelliğini birden çok değere ayrıştırmak için kullanılacak tek karakterlik sınırlayıcı. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Tıklama gezintisi | Listedeki bir öğeye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Listele** |**> Sütun başlıkları** |
| Adı |İlk sütunun en üstünde görüntülenen metin. |
| Değer |İkinci sütunun en üstünde görüntülenen metin. |
| **Listele** |**> Eşikleri** |
| Eşikleri etkinleştir |Eşikleri etkinleştirmek için bu bağlantıyı seçin. Daha fazla bilgi için bkz. [ortak ayarlar](#thresholds). |

## <a name="two-timelines-and-list-part"></a>İki zaman çizelgesi ve liste bölümü
Üst bilgi, bir günlük sorgusunda değer sütununu özetleyen tek bir sayı gösteren bir belirtme çizgisi ile zaman içindeki iki günlük sorgusunun sonuçlarını sütun grafikleri olarak görüntüler. Liste, bir sorgunun ilk on sonucunu, sayısal bir sütunun göreli değerini veya zaman içindeki değişikliğini gösteren bir grafiği görüntüler.

![İki zaman çizelgesi ve liste görünümü](media/view-designer-parts/view-two-timelines-list.png)

| Ayar | Açıklama |
|:--- |:--- |
| **Genel** | |
| Grup başlığı |Kutucuğun en üstünde görüntülenen metin. |
| Yeni Grup |Görünümde geçerli görünümden başlayarak yeni bir grup oluşturmak için bu bağlantıyı seçin. |
| Simg |Üst bilgisinde sonucun yanında görüntülenen resim dosyası. |
| Simge kullan |Simgeyi göstermek için bu bağlantıyı seçin. |
| **Başlık gezintisi** | |
| Tıklama gezintisi | Üst bilgiye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Birinci grafik<br>Ikinci grafik** | |
| Deki |İlk serinin belirtme çizgisinin altında görüntülenen metin. |
| Renk |Serideki sütunlar için kullanılacak renk. |
| Sorgu |İlk seri için çalıştırılacak sorgu. Her zaman aralığı içindeki kayıt sayısı grafik sütunları tarafından temsil edilir. |
| İşlem |Değer özelliğinde gerçekleştirilecek işlem, belirtme çizgisi için tek bir değer olarak özetleme.<ul><li>Sum: tüm kayıtlardaki değerlerin toplamı.</li><li>Average: tüm kayıtlardaki değerlerin ortalaması.</li><li>Son örnek: grafiğe dahil edilen son aralıktan değer.</li><li>İlk örnek: grafiğe dahil olan ilk aralıktan değer.</li><li>Sayı: sorgu tarafından döndürülen tüm kayıtların sayısı.</li></ul> |
| **Listele** | |
| Sorgu |Liste için çalıştırılacak sorgu. Sorgu tarafından döndürülen kayıtların sayısı görüntülenir. |
| Grafiği gizle |Sayısal sütunun sağ tarafındaki grafiği devre dışı bırakmak için bu bağlantıyı seçin. |
| Mini grafikleri etkinleştir |Yatay çubuk yerine bir mini grafik göstermek için bu bağlantıyı seçin. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Renk |Çubukların veya mini grafiklerin rengi. |
| İşlem |Mini grafik için gerçekleştirilecek işlem. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Tıklama gezintisi | Listedeki bir öğeye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Listele** |**> Sütun başlıkları** |
| Adı |İlk sütunun en üstünde görüntülenen metin. |
| Değer |İkinci sütunun en üstünde görüntülenen metin. |
| **Listele** |**> Eşikleri** |
| Eşikleri etkinleştir |Eşikleri etkinleştirmek için bu bağlantıyı seçin. Daha fazla bilgi için bkz. [ortak ayarlar](#thresholds). |

## <a name="information-part"></a>Bilgi bölümü
Üst bilgi, statik metin ve isteğe bağlı bir bağlantı görüntüler. Listede statik bir başlık ve metin içeren bir veya daha fazla öğe görüntülenir.

![Bilgi görünümü](media/view-designer-parts/view-information.png)

| Ayar | Açıklama |
|:--- |:--- |
| **Genel** | |
| Grup başlığı |Kutucuğun en üstünde görüntülenen metin. |
| Yeni Grup |Görünümde geçerli görünümden başlayarak yeni bir grup oluşturmak için bu bağlantıyı seçin. |
| Renk |Üst bilgi için arka plan rengi. |
| **Üst Bilgi** | |
| Görüntü |Üst bilgide görüntülenen resim dosyası. |
| Etiket |Üst bilgide görüntülenen metin. |
| **Üst Bilgi** |**> Bağlantısı** |
| Etiket |Bağlantı metni. |
| Url |Bağlantının URL 'Si. |
| **Bilgi öğeleri** | |
| Başlık |Her öğenin başlığı için görüntülenen metin. |
| İçerik |Her öğe için görüntülenen metin. |

## <a name="line-chart-callout-and-list-part"></a>Çizgi grafik, belirtme çizgisi ve liste bölümü
Üstbilgi, zaman içinde bir günlük sorgusundan birden çok serinin bulunduğu bir çizgi grafiği ve özetlenen değere sahip bir belirtme çizgisi görüntüler. Liste, bir sorgunun ilk on sonucunu, sayısal bir sütunun göreli değerini veya zaman içindeki değişikliğini gösteren bir grafiği görüntüler.

![Çizgi grafik, belirtme çizgisi ve liste görünümü](media/view-designer-parts/view-line-chart-callout-list.png)

| Ayar | Açıklama |
|:--- |:--- |
| **Genel** | |
| Grup başlığı |Kutucuğun en üstünde görüntülenen metin. |
| Yeni Grup |Görünümde geçerli görünümden başlayarak yeni bir grup oluşturmak için bu bağlantıyı seçin. |
| Simg |Üst bilgisinde sonucun yanında görüntülenen resim dosyası. |
| Simge kullan |Simgeyi göstermek için bu bağlantıyı seçin. |
| **Üst Bilgi** | |
| Başlık |Üstbilginin üstünde görüntülenen metin. |
| Konu |Üstbilginin en üstünde bulunan başlık altında görüntülenen metin. |
| **Çizgi grafik** | |
| Sorgu |Çizgi grafik için çalıştırılacak sorgu. İlk özellik bir metin değeridir ve ikinci özellik sayısal bir değerdir. Bu sorgu, sonuçları özetlemek için normalde *Measure* anahtar sözcüğünü kullanır. Sorgu *Interval* anahtar sözcüğünü kullanıyorsa, grafiğin x ekseni bu zaman aralığını kullanır. Sorgu *Interval* anahtar sözcüğünü içermiyorsa x ekseni saatlik aralıkları kullanır. |
| Tıklama gezintisi | Üst bilgiye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Çizgi grafik** |**> Belirtme çizgisi** |
| Belirtme çizgisi başlığı |Belirtme çizgisi değerinin üstünde görüntülenen metin. |
| Seri adı |Belirtme çizgisi değeri için kullanılacak serinin Özellik değeri. Hiçbir seri sağlanmazsa, sorgudaki tüm kayıtlar kullanılır. |
| İşlem |Değer özelliğinde gerçekleştirilecek işlem, belirtme çizgisi için tek bir değer olarak özetleme.<ul><li>Average: tüm kayıtlardaki değerlerin ortalaması.</li><li>Sayı: sorgu tarafından döndürülen tüm kayıtların sayısı.</li><li>Son örnek: grafiğe dahil edilen son aralıktan değer.</li><li>Max: grafiğe dahil edilen aralıkların en büyük değeri.</li><li>Min: grafiğe dahil edilen aralıkların en küçük değeri.</li><li>Sum: tüm kayıtlardaki değerlerin toplamı.</li></ul> |
| **Çizgi grafik** |**> Y ekseni** |
| Logaritmik ölçek kullan |Y ekseni için Logaritmik ölçek kullanmak üzere bu bağlantıyı seçin. |
| Birimler |Sorgu tarafından döndürülecek değerler için birimleri belirtin. Bu bilgiler, değer türlerini belirten ve isteğe bağlı olarak değerleri dönüştürmek için grafik etiketlerini göstermek için kullanılır. *Birim* türü birimin kategorisini belirtir ve kullanılabilir *geçerli birim* türü değerlerini tanımlar. *Dönüştür*' de bir değer seçerseniz, sayısal değerler *geçerli birim* türünden *dönüştürme* türüne dönüştürülür. |
| Özel etiket |*Birim* türü etiketinin yanında y ekseni için görüntülenen metin. Hiçbir etiket belirtilmemişse, yalnızca *birim* türü görüntülenir. |
| **Listele** | |
| Sorgu |Liste için çalıştırılacak sorgu. Sorgu tarafından döndürülen kayıtların sayısı görüntülenir. |
| Grafiği gizle |Sayısal sütunun sağ tarafındaki grafiği devre dışı bırakmak için bu bağlantıyı seçin. |
| Mini grafikleri etkinleştir |Yatay çubuk yerine bir mini grafik göstermek için bu bağlantıyı seçin. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Renk |Çubukların veya mini grafiklerin rengi. |
| İşlem |Mini grafik için gerçekleştirilecek işlem. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Ad ve değer ayırıcısı |Metin özelliğini birden çok değere ayrıştırmak için kullanılacak tek karakterlik sınırlayıcı. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Tıklama gezintisi | Listedeki bir öğeye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Listele** |**> Sütun başlıkları** |
| Adı |İlk sütunun en üstünde görüntülenen metin. |
| Değer |İkinci sütunun en üstünde görüntülenen metin. |
| **Listele** |**> Eşikleri** |
| Eşikleri etkinleştir |Eşikleri etkinleştirmek için bu bağlantıyı seçin. Daha fazla bilgi için bkz. [ortak ayarlar](#thresholds). |

## <a name="line-chart-and-list-part"></a>Çizgi grafik ve liste bölümü
Üstbilgi, zaman içinde günlük sorgusundan birden çok serinin bulunduğu bir çizgi grafiği görüntüler. Liste, bir sorgunun ilk on sonucunu, sayısal bir sütunun göreli değerini veya zaman içindeki değişikliğini gösteren bir grafiği görüntüler.

![Çizgi grafik ve liste görünümü](media/view-designer-parts/view-line-chart-callout-list.png)

| Ayar | Açıklama |
|:--- |:--- |
| **Genel** | |
| Grup başlığı |Kutucuğun en üstünde görüntülenen metin. |
| Yeni Grup |Görünümde geçerli görünümden başlayarak yeni bir grup oluşturmak için bu bağlantıyı seçin. |
| Simg |Üst bilgisinde sonucun yanında görüntülenen resim dosyası. |
| Simge kullan |Simgeyi göstermek için bu bağlantıyı seçin. |
| **Üst Bilgi** | |
| Başlık |Üstbilginin üstünde görüntülenen metin. |
| Konu |Üstbilginin en üstünde bulunan başlık altında görüntülenen metin. |
| **Çizgi grafik** | |
| Sorgu |Çizgi grafik için çalıştırılacak sorgu. İlk özellik bir metin değeridir ve ikinci özellik sayısal bir değerdir. Bu sorgu, sonuçları özetlemek için normalde *Measure* anahtar sözcüğünü kullanır. Sorgu *Interval* anahtar sözcüğünü kullanıyorsa, grafiğin x ekseni bu zaman aralığını kullanır. Sorgu *Interval* anahtar sözcüğünü içermiyorsa x ekseni saatlik aralıkları kullanır. |
| Tıklama gezintisi | Üst bilgiye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Çizgi grafik** |**> Y ekseni** |
| Logaritmik ölçek kullan |Y ekseni için Logaritmik ölçek kullanmak üzere bu bağlantıyı seçin. |
| Birimler |Sorgu tarafından döndürülecek değerler için birimleri belirtin. Bu bilgiler, değer türlerini belirten ve isteğe bağlı olarak değerleri dönüştürmek için grafik etiketlerini göstermek için kullanılır. *Birim* türü birimin kategorisini belirtir ve kullanılabilir *geçerli birim* türü değerlerini tanımlar. *Dönüştür*' de bir değer seçerseniz, sayısal değerler *geçerli birim* türünden *dönüştürme* türüne dönüştürülür. |
| Özel etiket |*Birim* türü etiketinin yanında y ekseni için görüntülenen metin. Hiçbir etiket belirtilmemişse, yalnızca *birim* türü görüntülenir. |
| **Listele** | |
| Sorgu |Liste için çalıştırılacak sorgu. Sorgu tarafından döndürülen kayıtların sayısı görüntülenir. |
| Grafiği gizle |Sayısal sütunun sağ tarafındaki grafiği devre dışı bırakmak için bu bağlantıyı seçin. |
| Mini grafikleri etkinleştir |Yatay çubuk yerine bir mini grafik göstermek için bu bağlantıyı seçin. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Renk |Çubukların veya mini grafiklerin rengi. |
| İşlem |Mini grafik için gerçekleştirilecek işlem. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Ad ve değer ayırıcısı |Metin özelliğini birden çok değere ayrıştırmak için kullanılacak tek karakterlik sınırlayıcı. Daha fazla bilgi için bkz. [ortak ayarlar](#sparklines). |
| Tıklama gezintisi | Listedeki bir öğeye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Listele** |**> Sütun başlıkları** |
| Adı |İlk sütunun en üstünde görüntülenen metin. |
| Değer |İkinci sütunun en üstünde görüntülenen metin. |
| **Listele** |**> Eşikleri** |
| Eşikleri etkinleştir |Eşikleri etkinleştirmek için bu bağlantıyı seçin. Daha fazla bilgi için bkz. [ortak ayarlar](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Çizgi grafik parçaları bölümü
Çizgi grafik yığını, aşağıda gösterildiği gibi zaman içindeki bir günlük sorgusundan birden çok serinin bulunduğu üç ayrı çizgi grafik görüntüler:

![Çizgi grafik yığını](media/view-designer-parts/view-stack-line-charts.png)

| Ayar | Açıklama |
|:--- |:--- |
| **Genel** | |
| Grup başlığı |Kutucuğun en üstünde görüntülenen metin. |
| Yeni Grup |Görünümde geçerli görünümden başlayarak yeni bir grup oluşturmak için bu bağlantıyı seçin. |
| Simg |Üst bilgisinde sonucun yanında görüntülenen resim dosyası. |
| **Grafik 1<br>grafik 2<br>grafik 3** |**> Üst bilgisi** |
| Başlık |Grafiğin üstünde görüntülenen metin. |
| Konu |Grafiğin üst kısmında bulunan başlık altında görüntülenen metin. |
| **Grafik 1<br>grafik 2<br>grafik 3** |**Çizgi grafik** |
| Sorgu |Çizgi grafik için çalıştırılacak sorgu. İlk özellik bir metin değeridir ve ikinci özellik sayısal bir değerdir. Bu sorgu, sonuçları özetlemek için normalde *Measure* anahtar sözcüğünü kullanır. Sorgu *Interval* anahtar sözcüğünü kullanıyorsa, grafiğin x ekseni bu zaman aralığını kullanır. Sorgu *Interval* anahtar sözcüğünü içermiyorsa x ekseni saatlik aralıkları kullanır. |
| Tıklama gezintisi | Üst bilgiye tıkladığınızda gerçekleştirilecek eylem.  Daha fazla bilgi için bkz. [ortak ayarlar](#click-through-navigation). |
| **Grafik** |**> Y ekseni** |
| Logaritmik ölçek kullan |Y ekseni için Logaritmik ölçek kullanmak üzere bu bağlantıyı seçin. |
| Birimler |Sorgu tarafından döndürülecek değerler için birimleri belirtin. Bu bilgiler, değer türlerini belirten ve isteğe bağlı olarak değerleri dönüştürmek için grafik etiketlerini göstermek için kullanılır. *Birim* türü birimin kategorisini belirtir ve kullanılabilir *geçerli birim* türü değerlerini tanımlar. *Dönüştür*' de bir değer seçerseniz, sayısal değerler *geçerli birim* türünden *dönüştürme* türüne dönüştürülür. |
| Özel etiket |*Birim* türü etiketinin yanında y ekseni için görüntülenen metin. Hiçbir etiket belirtilmemişse, yalnızca *birim* türü görüntülenir. |

## <a name="common-settings"></a>Ortak ayarlar
Aşağıdaki bölümlerde, birkaç görselleştirme bölümünde ortak olan ayarlar açıklanır.

### <a name="name-value-separator"></a>Ad ve değer ayırıcısı
Ad ve değer ayırıcısı, metin özelliğini bir liste sorgusundan birden çok değere ayrıştırmak için kullanılacak tek karakterli sınırlayıcıdır. Bir sınırlayıcı belirtirseniz, **ad** kutusunda aynı sınırlayıcıyla ayırarak her bir alan için adlar sağlayabilirsiniz.

Örneğin, *Redmond-building 41* ve *Bellevue-derleniyor*gibi değerleri içeren *konum* adlı bir özellik düşünün. Ad ve değer ayırıcısı ve ad için *şehir oluşturma* için bir tire (-) belirtebilirsiniz. Bu yaklaşım her değeri *City* ve *Building*adlı iki özelliğe ayrıştırır.

### <a name="click-through-navigation"></a>Tıklama gezintisi
Tıklama gezinmesi, bir görünümdeki üstbilgiye veya liste öğesine tıkladığınızda hangi eylemin yapılacağını tanımlar.  Bu, [Log Analytics](../../azure-monitor/log-query/portals.md) bir sorgu açar ya da başka bir görünüm başlatır.

Aşağıdaki tabloda tıklama gezintisi için ayarlar açıklanmaktadır.

| Ayar           | Açıklama |
|:--|:--|
| Günlük araması (otomatik) | Bir üstbilgi öğesi seçtiğinizde çalıştırılacak günlük sorgusu.  Bu, öğenin temel aldığı günlük sorgusuna bağlıdır.
| Günlük araması        | Listedeki bir öğeyi seçtiğinizde çalıştırılacak günlük sorgusu.  Sorguyu **Gezinti sorgu** kutusuna yazın.   Kullanıcının seçtiği öğenin söz dizimini eklemek için *{Selected Item}* kullanın.  Örneğin, sorguda *bilgisayar* adlı bir sütun varsa ve gezinti sorgusu *{Selected Item}* ise, bir bilgisayar seçtiğinizde *bilgisayar = "Bilgisayarım"* gibi bir sorgu çalıştırılır. Gezinti sorgusu *tür = olay {seçili öğe}* ise, sorgu *türü = olay bilgisayarı = "Bilgisayarım"* çalıştırılır. |
| Görüntüle              | Bir başlık öğesi veya listedeki bir öğe seçtiğinizde açılacak şekilde görüntüleyin.  **Görünüm adı** kutusunda çalışma alanınızda bir görünümün adını seçin. |



### <a name="sparklines"></a>Mini grafikler
Mini grafik, zaman içinde liste girişinin değerini gösteren küçük bir çizgi grafiğidir. Bir liste içeren görselleştirme parçaları için, sayısal bir sütunun göreli değerini veya bir mini grafiğin görüntülenip zaman içinde değerini gösteren bir mini çizgi görüntülenmesini seçebilirsiniz.

Aşağıdaki tabloda, mini grafiklerin ayarları açıklanmaktadır:

| Ayar | Açıklama |
|:--- |:--- |
| Mini grafikleri etkinleştir |Yatay çubuk yerine bir mini grafik göstermek için bu bağlantıyı seçin. |
| İşlem |Mini grafikler etkinse, bu, Mini Grafiğin değerlerini hesaplamak için listedeki her bir özellikte gerçekleştirilecek işlemdir.<ul><li>Son örnek: serinin zaman aralığındaki son değeri.</li><li>Max: serinin zaman aralığındaki en büyük değeri.</li><li>Min: zaman aralığı boyunca serinin minimum değeri.</li><li>Sum: serinin zaman aralığındaki değerlerinin toplamı.</li><li>Özet: başlıktaki sorgu ile aynı `measure` komutunu kullanır.</li></ul> |

### <a name="thresholds"></a>Leriyle
Eşikleri kullanarak, listedeki her öğenin yanında renkli bir simge görüntüleyebilirsiniz. Eşikler, belirli bir değeri aşan veya belirli bir aralık dahilinde olan öğelerin hızlı bir görsel göstergesini sağlar. Örneğin, kabul edilebilir bir değere sahip öğeler için yeşil bir simge, değer bir uyarıyı belirten bir Aralık içindeyse sarı ve bir hata değerini aşarsa kırmızı bir simge görüntüleyebilirsiniz.

Bir bölüm için eşikleri etkinleştirdiğinizde bir veya daha fazla eşik belirtmeniz gerekir. Bir öğenin değeri bir eşik değerinden büyükse ve sonraki eşik değerinden düşükse, bu değerin rengi kullanılır. Öğe en yüksek eşik değerinden büyükse, başka bir renk kullanılır. 

Her eşik kümesi, **varsayılan**değeri olan bir eşiğe sahiptir. Bu, başka hiçbir değer aşılmadığı takdirde ayarlanmış olan renktir. **Ekle** (+) veya **Delete** (x) düğmesini seçerek eşikleri ekleyebilir veya kaldırabilirsiniz.

Aşağıdaki tabloda eşiklerin ayarları açıklanmaktadır:

| Ayar | Açıklama |
|:--- |:--- |
| Eşikleri etkinleştir |Her bir değerin solunda bir renk simgesi göstermek için bu bağlantıyı seçin. Simge, belirtilen eşiklere göre değerin sistem durumunu gösterir. |
| Adı |Eşik değerinin adı. |
| Eşiği |Eşiğin değeri. Her bir liste öğesi için sistem durumu rengi, öğenin değeri tarafından aşılan en yüksek eşik değerinin rengine ayarlanır. Eşik değeri aşılmadığı takdirde, varsayılan bir renk kullanılır. |
| Renk |Eşik değerini gösteren renk. |

## <a name="next-steps"></a>Sonraki adımlar
* Görselleştirme kısımlarındaki sorguları destekleyecek [günlük sorguları](../log-query/log-query-overview.md) hakkında bilgi edinin.
