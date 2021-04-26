---
title: Azure Ölçüm Gezgini 'nin gelişmiş özellikleri
description: Azure Ölçüm Gezgini 'nin gelişmiş kullanımları hakkında bilgi edinin.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.openlocfilehash: 444185dc41532bfa55c271e2f0027eb8b71de42f
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959932"
---
# <a name="advanced-features-of-the-azure-metrics-explorer"></a>Azure Ölçüm Gezgini 'nin gelişmiş özellikleri

> [!NOTE]
> Bu makalede, Azure Izleyici 'nin Azure ölçümleri gezgin özelliğinin temel özellikleriyle ilgili bilgi sahibi olduğunuz varsayılır. Yeni bir Kullanıcı kullanıyorsanız ve ilk ölçüm grafiğinizi oluşturmayı öğrenmek istiyorsanız, bkz. [Ölçüm Gezgini ile çalışmaya başlama](./metrics-getting-started.md).

Azure Izleyici 'de [ölçümler](data-platform-metrics.md) , zaman içinde toplanan ve depolanan ölçülen değer ve sayı serisidir. Ölçümler standart ("Platform" olarak da adlandırılır) veya özel olabilir. 

Standart ölçümler, Azure platformu tarafından sağlanır. Azure kaynaklarınızın sistem durumunu ve kullanım istatistiklerini yansıtır. 

## <a name="resource-scope-picker"></a>Kaynak kapsamı seçici
Kaynak kapsamı seçici, tek kaynaklardaki ve birden çok kaynakta ölçümleri görüntülemenize olanak sağlar. Aşağıdaki bölümlerde, kaynak kapsamı seçicisinin nasıl kullanılacağı açıklanmaktadır. 

### <a name="select-a-single-resource"></a>Tek bir kaynak seçin
**Azure izleyici** menüsünde veya bir kaynağın menüsünün **izleme** bölümünden **ölçümler** ' i seçin. Ardından kapsam seçiciyi açmak için **kapsam seçin** öğesini seçin. 

Ölçümlerini görmek istediğiniz kaynakları seçmek için kapsam seçiciyi kullanın. Azure ölçümleri Gezginini bir kaynağın menüsünden açtıysanız kapsam doldurulmalıdır. 

![Kaynak kapsamı seçicinin nasıl açılacağını gösteren ekran görüntüsü.](./media/metrics-charts/scope-picker.png)

Bazı kaynaklar için, tek seferde yalnızca bir kaynağın ölçümlerini görüntüleyebilirsiniz. **Kaynak türleri** menüsünde, bu kaynaklar **tüm kaynak türleri** bölümünde bulunur.

![Tek bir kaynağı gösteren ekran görüntüsü.](./media/metrics-charts/single-resource-scope.png)

Bir kaynak seçtikten sonra, bu kaynağı içeren tüm abonelikleri ve kaynak gruplarını görürsünüz.

![Kullanılabilir kaynakları gösteren ekran görüntüsü.](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Aynı anda birden çok kaynak için ölçümleri görüntülemek veya bir abonelik ya da kaynak grubu genelinde ölçümleri görüntülemek istiyorsanız, **Upoy**' ı seçin.

Seçiminizden memnun kaldığınızda **Uygula**' yı seçin.

### <a name="view-metrics-across-multiple-resources"></a>Birden çok kaynak arasında ölçümleri görüntüleme
Bazı kaynak türleri, birden fazla kaynak üzerinde ölçümleri sorgulayabilir. Kaynaklar aynı abonelik ve konum dahilinde olmalıdır. **Kaynak türleri** menüsünün en üstünde bu kaynak türlerini bulun. 

Daha fazla bilgi için bkz. [birden fazla kaynak seçme](./metrics-dynamic-scope.md#select-multiple-resources).

![Çapraz kaynak türlerini gösteren ekran görüntüsü.](./media/metrics-charts/multi-resource-scope.png)

Birden çok kaynakla uyumlu olan türler için bir abonelik veya birden çok kaynak grubu genelinde ölçümleri sorgulayabilirsiniz. Daha fazla bilgi için bkz. [kaynak grubu veya abonelik seçme](./metrics-dynamic-scope.md#select-a-resource-group-or-subscription).

## <a name="multiple-metric-lines-and-charts"></a>Birden çok ölçüm çizgisi ve grafik

Azure ölçümleri Gezgininde, birden çok ölçüm satırını çizdirme veya aynı anda birden çok ölçüm grafiği gösteren grafikler oluşturabilirsiniz. Bu işlevsellik şunları yapmanıza olanak sağlar:

- Bir değerin birbirleriyle ilişkisini görmek için aynı grafikteki ilgili ölçümleri ilişkilendirin.
- Yakın yakınlarda farklı ölçü birimleri kullanan ölçümleri görüntüleyin.
- Birden çok kaynaktan ölçümleri görsel olarak toplayın ve karşılaştırın.

Örneğin, beş depolama hesabınız olduğunu ve birlikte ne kadar alan tükettiği hakkında bilgi almak istediğinizi düşünelim. Belirli zaman noktalarında tek tek değerleri ve tüm değerlerin toplamını gösteren bir (yığılmış) alan grafiği oluşturabilirsiniz.

### <a name="multiple-metrics-on-the-same-chart"></a>Aynı grafikte birden çok ölçüm

Aynı grafikte birden çok ölçümü görüntülemek için önce [Yeni bir grafik oluşturun](./metrics-getting-started.md#create-your-first-metric-chart). Ardından **ölçüm Ekle**' yi seçin. Aynı grafiğe başka bir ölçüm eklemek için bu adımı tekrarlayın.

> [!NOTE]
> Genellikle, grafikleriniz farklı ölçü birimleri kullanan ölçümleri karıştırmamalıdır. Örneğin, kilobayt kullanan diğeri ile milisaniyelik kullanan bir ölçümü karıştırmaktan kaçının. Ayrıca, ölçeklendirmeden farklı olan ölçümleri karıştırmaktan kaçının. 
>
> Bu gibi durumlarda, bunun yerine birden çok grafik kullanmayı göz önünde bulundurun. Ölçüm Gezgini 'nde yeni bir grafik oluşturmak için **Grafik Ekle** ' yi seçin.

### <a name="multiple-charts"></a>Birden çok grafik

Farklı bir ölçüm kullanan başka bir grafik oluşturmak için **Grafik Ekle**' yi seçin.

Birden çok grafiği yeniden sıralamak veya silmek için üç nokta (**...**) düğmesini seçerek grafik menüsünü açın. Sonra **Yukarı taşı**, **aşağı taşı** veya **Sil**' i seçin.

## <a name="time-range-controls"></a>Zaman aralığı denetimleri

Zaman aralığı [panelini](metrics-getting-started.md#select-a-time-range)kullanarak zaman aralığını değiştirmenin yanı sıra, grafik alanındaki denetimleri kullanarak da kaydırma ve yakınlaştırma yapabilirsiniz.
### <a name="pan"></a>Kaydır

Kaydırmak için grafiğin kenarından sol ve sağ oklara tıklayın.  Bu işlem, seçili zaman aralığını grafiğin zaman aralığında bir yarıya geri ve ileri taşır.  Örneğin, son 24 saati görüntülüyorsanız, sol ok simgesine tıklamak zaman aralığının bir güne ve 12 saate kadar bir süre önce geçmesine neden olur.

Çoğu ölçüm 93 günlük saklama süresini destekler ancak yalnızca 30 gün boyunca görüntülemenize izin verir.  Kaydırma denetimlerini kullanarak, son 30 güne göz atın ve ardından bekletme döneminin geri kalanını görüntülemek için bir seferde 15 gün sonra kolayca geri dönebilirsiniz.

![Sol ve sağ kaydırma denetimlerini gösteren animasyonlu GIF.](./media/metrics-charts/metrics-pan-controls.gif)

### <a name="zoom"></a>Zoom

Grafiğin bir bölümüne yakınlaştırmak için grafiğe tıklayıp sürükleyebilirsiniz.  Yakınlaştırma, grafiğin zaman aralığını seçiminize yayarak güncelleştirecek ve zaman çizgisi "otomatik" olarak ayarlandıysa daha kısa bir zaman aralığı seçer.  Yeni zaman aralığı, ölçümdeki tüm grafikler için geçerlidir.

![Ölçüm yakınlaştırma özelliğinin gösterildiği animasyonlu GIF.](./media/metrics-charts/metrics-zoom-control.gif)

## <a name="aggregation&quot;></a>Toplama

Bir grafiğe ölçüm eklediğinizde, Ölçüm Gezgini otomatik olarak varsayılan bir toplama uygular. Varsayılan değer temel senaryolarda anlamlı hale gelir. Ancak, ölçüm hakkında daha fazla öngörü kazanmak için farklı bir toplama kullanabilirsiniz. 

Bir grafikte farklı toplamalar kullanmadan önce, Ölçüm Gezgini 'nin bunları nasıl işlediğini anlamanız gerekir. Ölçümler, bir zaman dilimi boyunca yakalanan bir dizi ölçümdür (veya &quot;ölçüm değerleri"). Bir grafiği çizerseniz, seçili ölçüm değerleri *zaman dilimi* içinde ayrı olarak toplanır. 

Ölçüm Gezgini 'nin [zaman Seçicisi panelini](./metrics-getting-started.md#select-a-time-range)kullanarak zaman çizgisi boyutunu seçersiniz. Zaman aralığını açık bir şekilde seçmezseniz, şu anda seçili olan zaman aralığı varsayılan olarak kullanılır. Zaman dilimi saptandıktan sonra, her zaman grede yakalanan ölçüm değerleri grafikte toplanır ve zaman çizgisi başına bir veri noktasıdır.

Örneğin, bir grafikte *sunucu yanıt süresi* ölçümünü gösteren bir grafik olduğunu varsayalım. *Son 24 saatin* zaman dilimi boyunca *Ortalama* toplamayı kullanır. Bu örnekte:

- Zaman ayrıntı düzeyi 30 dakikaya ayarlanmışsa, grafik 48 Birleşik veri noktalarından çizilir. Diğer bir deyişle, çizgi grafik grafik çizim alanında 48 nokta bağlar (24 saat x 2 veri noktası saat başına). Her bir veri noktası, ilgili 30 dakikalık zaman döneminin her biri sırasında oluşan sunucu istekleri için yakalanan tüm yanıt sürelerinin *ortalamasını* temsil eder.
- Zaman parçalı yapısını 15 dakikaya geçerseniz, 96 toplanmış veri noktası elde edersiniz.  Yani, saat başına 24 saat x 4 veri noktası alırsınız.

Ölçüm Gezgininde beş temel istatistiksel toplama türü vardır: Sum, Count, min, Max ve Average. Toplam *toplama bazen* *Toplam* toplama olarak adlandırılır. Birçok ölçüm için ölçüm Gezgini, ilgisiz olan ve kullanılamayan toplamaları gizler.

* **Sum**: toplama aralığı boyunca yakalanan tüm değerlerin toplamı.

    ![Toplam isteğinin ekran görüntüsü.](./media/metrics-charts/request-sum.png)

* **Sayı**: toplama aralığı sırasında yakalanan ölçüm sayısı. 
    
    Ölçüm her zaman 1 değeriyle yakalandıktan sonra, sayı toplama toplam toplamaya eşittir. Bu senaryo, ölçüm farklı olay sayısını izliyorsa ve her ölçüm bir olayı temsil ediyorsa yaygın bir senaryodur. Kod, her yeni istek geldiğinde bir ölçüm kaydı yayar.

    ![Bir sayı isteğinin ekran görüntüsü.](./media/metrics-charts/request-count.png)

* **Average**: toplama aralığı sırasında yakalanan ölçüm değerlerinin ortalaması.

    ![Ortalama isteğin ekran görüntüsü.](./media/metrics-charts/request-avg.png)

* **Min**: toplama aralığı sırasında yakalanan en küçük değer.

    ![En düşük isteğin ekran görüntüsü.](./media/metrics-charts/request-min.png)

* **Max**: toplama aralığı sırasında yakalanan en büyük değer.

    ![Maksimum isteğin ekran görüntüsü.](./media/metrics-charts/request-max.png)

## <a name="filters"></a>Filtreler

Ölçümlerinin boyutları olan grafiklere filtre uygulayabilirsiniz. Örneğin, "yanıt türü" boyutunda bir "Işlem sayısı" ölçümü düşünün. Bu boyut, işlemlerdeki yanıtın başarılı veya başarısız olduğunu gösterir. Bu boyuta filtre uygulamanız durumunda yalnızca başarılı (veya yalnızca başarısız) işlemler için bir grafik satırı görürsünüz. 

### <a name="add-a-filter"></a>Filtre ekleme

1. Grafiğin üstünde **Filtre Ekle**' yi seçin.

2. Filtrelemek için bir boyut (özellik) seçin.

   ![Filtreleyebileceğiniz boyutları (özellikleri) gösteren ekran görüntüsü.](./media/metrics-charts/028.png)

3. Boyut (özellik) için uygulamak istediğiniz işleci seçin. Varsayılan işleç = (eşittir)

   ![Filtreyle kullanabileceğiniz operatörü gösteren ekran görüntüsü.](./media/metrics-charts/filter-operator.png)

4. Grafiği çizdirme sırasında filtreye uygulamak istediğiniz boyut değerlerini seçin (Bu örnek, başarılı depolama işlemlerinin filtrelenmesini gösterir):

   ![Başarılı filtrelenmiş depolama işlemlerini gösteren ekran görüntüsü.](./media/metrics-charts/029.png)

5. Filtre değerlerini seçtikten sonra, kapatmak için filtre seçicideki dışarıda ' ı tıklatın. Artık grafik, kaç depolama işlemi başarısız olduğunu gösterir:

   ![Kaç depolama hareketinin başarısız olduğunu gösteren ekran görüntüsü.](./media/metrics-charts/030.png)

6. Aynı grafiklere birden çok filtre uygulamak için 1-5 adımlarını tekrarlayabilirsiniz.


## <a name="metric-splitting"></a>Ölçüm bölme

Ölçüm 'in farklı segmentlerinin nasıl karşılaştırılacağını görselleştirmek için bir ölçümü boyuta göre bölebilirsiniz. Bölmek, bir boyutun harici segmentlerini belirlemenize de yardımcı olabilir.

### <a name="apply-splitting"></a>Bölmeyi Uygula

1. Grafiğin üstünde **bölme Uygula**' yı seçin.
 
   > [!NOTE]
   > Birden çok ölçümde bulunan grafikler bölme işlevini kullanamaz. Ayrıca, bir grafik birden fazla filtreye sahip olsa da, yalnızca bir bölme boyutuna sahip olabilir.

2. Grafiğinizin segmentinde bulunduğu bir boyut seçin:

   ![Grafiğin segmentinde seçilen boyutu gösteren ekran görüntüsü.](./media/metrics-charts/031.png)

   Grafik artık her boyut segmenti için bir tane olmak üzere birden çok satır gösterir:

   ![Her boyutun segmenti için bir tane olmak üzere birden çok satırı gösteren ekran görüntüsü.](./media/metrics-charts/segment-dimension.png)
   
3. Seçili boyut bölündikten sonra görüntülenecek değer sayısı için bir sınır seçin. Varsayılan sınır, yukarıdaki grafikte gösterildiği gibi 10 ' dur. Sınır aralığı 1-50 ' dir.
   
   ![Bölme sınırını gösteren ekran görüntüsü, bölme işleminden sonra değer sayısını kısıtlar.](./media/metrics-charts/segment-dimension-limit.png)
   
4. Kesimlerde sıralama düzenini seçin: artan veya azalan. Varsayılan seçim azalan şekilde belirlenir.
   
   ![Bölünmüş değerlerde sıralama düzenini gösteren ekran görüntüsü.](./media/metrics-charts/segment-dimension-sort.png)

5. Bunu kapatmak için **Gruplandırma seçicisindeki** uzakta ' ye tıklayın.
   

   > [!NOTE]
   > Senaryolarınız için ilgisiz olan kesimleri gizlemek ve grafiklerinizi okumayı kolaylaştırmak için, hem filtreleme hem de aynı boyutta bölme kullanın.

## <a name="locking-the-range-of-the-y-axis"></a>Y ekseninin aralığını kilitleme

Değer (y) ekseninin kilitlenmesi, büyük değerlerin küçük dalgalanmalarını gösteren grafiklerde önemli hale gelir. 

Örneğin, yüzde 99,99 ' dan% 99,5 ' e kadar başarılı isteklerin hacmi, hizmet kalitesinin önemli bir azalmasıyla temsil edebilir. Ancak, varsayılan grafik ayarlarını kullanıyorsanız küçük bir sayısal değer dalgalanması zor veya imkansız olabilir. Bu durumda, küçük bir bırakma daha belirgin hale getirmek için grafiğin en düşük sınırını yüzde 99 olarak kilitlemeniz gerekebilir. 

Diğer bir örnek, kullanılabilir bellek üzerinde bir dalgalanma örnektir. Bu senaryoda, değer teknik açıdan hiçbir şekilde 0 ' a erişmeyecektir. Aralığın daha yüksek bir değere düzeltilmesi, kullanılabilir belleğin düşün daha kolay olmasını sağlayabilir. 

Y ekseni aralığını denetlemek için, grafik menüsünü (**...**) açın. Ardından, gelişmiş grafik ayarlarına erişmek için **grafik ayarları** ' nı seçin.

![Grafik Ayarları seçimini vurgulayan ekran görüntüsü.](./media/metrics-charts/033.png)

**Y ekseni Aralık** bölümündeki değerleri değiştirin veya varsayılan değerlere dönmek için **Otomatik** ' i seçin.
 
 ![Y ekseni aralığı bölümünü vurgulayan ekran görüntüsü.](./media/metrics-charts/034.png)

> [!WARNING]
> Bir süre (Count, Sum, Min veya Max toplamaları kullanarak) sayısını izleyen grafikler için y ekseninin sınırlarını kilitlemeniz gerekiyorsa, genellikle sabit bir zaman ayrıntı düzeyi belirtmeniz gerekir. Bu durumda, otomatik varsayılanlara güvenmemelisiniz. 
>
> Bir kullanıcı tarayıcı penceresini yeniden boyutlandırdıktan sonra veya ekran çözünürlüğünü değiştirdiğinde, zaman ayrıntı düzeyi otomatik olarak değiştirildiğinde grafik değerleri değiştiğinden, sabit bir zaman ayrıntı düzeyi seçersiniz. Zaman ayrıntı düzeyi sonucunda ortaya çıkan değişiklik, grafik görünümünü etkiler ve y ekseni aralığının geçerli seçimini geçersiz duruma çıkarır.

## <a name="line-colors"></a>Çizgi renkleri

Grafikleri yapılandırdıktan sonra, grafik satırlarına otomatik olarak varsayılan bir paletten bir renk atanır. Bu renkleri değiştirebilirsiniz.

Bir grafik çizgisinin rengini değiştirmek için, göstergede grafiğe karşılık gelen renkli çubuğu seçin. Renk Seçici iletişim kutusu açılır. Çizgi rengini yapılandırmak için renk seçiciyi kullanın.

![Rengin nasıl değiştirileceğini gösteren ekran görüntüsü.](./media/metrics-charts/035.png)

Grafiği panoya sabitlemeyi yaparken özelleştirilmiş renklerinizin korunması gerekir. Aşağıdaki bölümde bir grafik sabitleme gösterilmektedir.

## <a name="pinning-to-dashboards"></a>Panolara sabitleme 

Bir grafiği yapılandırdıktan sonra, bir panoya eklemek isteyebilirsiniz. Bir grafiği panoya sabitleyerek takımınızın erişimine açık hale getirebilirsiniz. Ayrıca, diğer izleme Telemetriyi bağlamında görüntüleyerek Öngörüler elde edebilirsiniz.

Yapılandırılmış bir grafiği panoya sabitlemek için grafiğin sağ üst köşesinde **panoya sabitle**' yi seçin.

![Bir grafiğin panoya nasıl sabitlenediğinin gösterildiği ekran görüntüsü.](./media/metrics-charts/036.png)

## <a name="alert-rules"></a>Uyarı kuralları

Görselleştirme ölçütlerini, ölçüm tabanlı bir uyarı kuralı oluşturmak için kullanabilirsiniz. Yeni uyarı kuralı, grafiğinizin hedef kaynağını, ölçümünü, bölmeyi ve filtre boyutlarını içerir. Bu ayarları, uyarı kuralı oluşturma bölmesini kullanarak değiştirebilirsiniz.

Başlamak için **Yeni uyarı kuralı**' nı seçin.

![Yeni uyarı kuralı düğmesinin kırmızı renkle vurgulandığını gösteren ekran görüntüsü.](./media/metrics-charts/042.png)

Uyarı kuralı oluşturma bölmesi açılır. Bölmesinde, grafiğin ölçüm boyutlarını görürsünüz. Bölmedeki alanlar, kuralı özelleştirmenize yardımcı olacak şekilde önceden doldurulur.

![Kural oluşturma bölmesini gösteren ekran görüntüsü.](./media/metrics-charts/041.png)

Daha fazla bilgi için bkz. [ölçüm uyarıları oluşturma, görüntüleme ve yönetme](../alerts/alerts-metric.md).

## <a name="correlate-metrics-to-logs"></a>Ölçümleri günlüklere ilişkilendirme
Müşterilerin ölçüm grafiğinde oluşan kök nedenini tanılamasına yardımcı olmak için günlüklere detaya gitme oluşturduk. Günlüklerde detaya gitme, müşterilerin ölçüm grafiğindeki ani artışları Günlükler ve sorgular ile ilişkilendirmelerini sağlar. 

Deneyimle karşılaşmadan önce, belirtilen farklı türlerde günlükleri ve sorguları tanıtmak istiyoruz. 

| Süre             | Tanım  | 
|------------------|-------------|
| Etkinlik günlükleri    | Hizmet durumu olaylarında güncelleştirmelere ek olarak, dışarıdaki (Yönetim düzlemi) abonelikteki her bir Azure kaynağında bulunan işlemlere ilişkin öngörüler sağlar. Aboneliğinizdeki kaynaklarda hangi yazma işlemlerinin (PUT, POST, SILME) alındığını belirlemek için etkinlik günlüğünü kullanın. Her bir Azure aboneliği için tek bir etkinlik günlüğü vardır.  |   
| Tanılama günlüğü   | Bir Azure kaynağı içinde (veri düzlemi) gerçekleştirilen işlemlere Öngörüler sağlama (örneğin, bir Key Vault gizli anahtar alma veya bir veritabanına istek yapma). Kaynak günlüklerinin içeriği, Azure hizmeti ve kaynak türüne göre farklılık gösterir. **Note:** Hizmet tarafından sağlanması ve müşteri tarafından etkinleştirilmesi gerekir  | 
| Önerilen günlük | Müşterilerin Ölçüm Gezgini 'ndeki anormallikleri araştırmak için yararlanabilen senaryo tabanlı sorgular.  |

Şu anda, seçilmiş kaynak sağlayıcıları için günlüklere detaya gitme seçeneği mevcuttur. Tüm günlüklere gitme deneyimine sahip kaynak sağlayıcıları şunlardır: 

* Application Insights 
* Otomatik Ölçeklendirme 
* Uygulama Hizmetleri  
* Depolama  

Aşağıda Application Insights kaynak sağlayıcısı için örnek deneyimler verilmiştir.

![App Insights ölçümleri dikey penceresinde hatalarda ani artış](./media/metrics-charts/drill-into-log-ai.png)

Başarısız isteklerde ani artış tanılamak için "günlüklere git" e tıklayın.

![Günlüklerde detaya gitme açılan ekranının ekran görüntüsü](./media/metrics-charts/drill-into-logs-dropdown.png)

Hata seçeneğine tıkladığınızda, başarısız işlem işlemleri, en çok özel durum türleri ve bağımlılıklar sağlayan özel bir hata dikey penceresine da yol gösterilir. 

![Uygulama öngörüleri hata dikey penceresinin ekran görüntüsü](./media/metrics-charts/ai-failure-blade.png)

### <a name="common-problems-with-drill-into-logs"></a>Günlüklerde detaya gitme ile ilgili yaygın sorunlar

* Günlük ve sorgular devre dışı-önerilen günlükleri ve sorguları görüntülemek Için tanılama günlüklerinizi Log Analytics yönlendirmelidir. Bunu nasıl yapacağınızı öğrenmek için [Bu belgeyi](./diagnostic-settings.md) okuyun. 
* Etkinlik günlükleri yalnızca belirli kaynak sağlayıcıları için kullanılabilir-günlüklere gitme özelliği yalnızca kullanılabilir. Varsayılan olarak, etkinlik günlükleri sağlanır. 

 
## <a name="troubleshooting"></a>Sorun giderme

Grafiğinizde herhangi bir veri görmüyorsanız, aşağıdaki sorun giderme bilgilerini gözden geçirin:

* Filtreler bölmedeki tüm grafiklere uygulanır. Grafiğe odaklanırken, başka bir grafikteki tüm verileri dışlayan bir filtre ayarladığınızdan emin olun.

* Farklı grafiklerde farklı filtreler ayarlamak için, grafikleri farklı dikey pencerelerde oluşturun. Ardından, grafikleri ayrı sık kullanılanlar olarak kaydedin. İsterseniz, grafikleri bir araya görebileceğiniz şekilde panoya sabitleyebilirsiniz.

* Bir grafiği ölçüsünün tanımlamayan bir özelliğe göre segmentleyebilirsiniz grafik hiçbir içerik görüntülemez. Segmentleme işlemini temizlemeyi deneyin (bölme) veya farklı bir özellik seçin.

## <a name="next-steps"></a>Sonraki adımlar

Ölçümleri kullanarak işlem yapılabilir panolar oluşturmak için bkz. [özel KPI panoları oluşturma](../app/tutorial-app-dashboards.md).