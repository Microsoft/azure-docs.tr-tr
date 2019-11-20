---
title: Algoritmaları seçme
titleSuffix: ML Studio (classic) - Azure
description: Kümeleme, sınıflandırma veya regresyon denemeleri için denetimli ve denetlenen öğrenme için Azure Machine Learning Studio (klasik) algoritmaları seçme.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: c69b4c15397dc13f36a707f932c2464a4ff94ca7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838475"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio için algoritma seçme (klasik)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

"Hangi makine öğrenimi algoritmasını kullanmalıyım?" sorusunun yanıtı her zaman "BT bağımlıdır". Bu, verilerin boyutuna, kalitesine ve yapısına bağlıdır. Bu, Yanıtla ne yapmak istediğinize bağlıdır. Bu, algoritmanın matematiğinin, kullanmakta olduğunuz bilgisayar için yönergelere nasıl çevrildiğinden bağlıdır. Bu, ne kadar zaman sahip olduğunuza bağlıdır. En fazla deneyimli veri uzmanları, denemeden önce hangi algoritmanın en iyi şekilde gerçekleştirileceğini söylemez.

Machine Learning Studio (klasik), Microsoft Research 'ta geliştirilen ölçeklenebilir ve ölçeklenebilir karar ağaçları, Bayeme öneri sistemleri, derin sinir ağları ve karar harikası gibi son teknoloji algoritmaları sağlar. Vowpal Wabbit gibi ölçeklenebilir açık kaynaklı makine öğrenimi paketleri de dahildir. Machine Learning Studio klasik sürümü, birden çok Lass ve ikili sınıflandırma, gerileme ve kümeleme için makine öğrenimi algoritmalarını destekler. [Machine Learning modüllerinin](/azure/machine-learning/studio-module-reference/index)tüm listesine bakın.
Belgeler her algoritma ve kullanım için algoritmayı iyileştirmek üzere parametrelerin nasıl ayarlanacağı hakkında bazı bilgiler sağlar.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Machine Learning algoritması bir sayfa

**[Microsoft Azure Machine Learning algoritması](../algorithm-cheat-sheet.md)** ile Ilgili bilgi sayfası, algoritma Azure Machine Learning kitaplığından tahmine dayalı analiz çözümleriniz için doğru makine öğrenimi algoritmasını seçmenize yardımcı olur.
Bu makale, bu uygulama sayfasını nasıl kullanacağınızı adım adım göstermektedir.

> [!NOTE]
> Tek bir sayfayı indirmek ve bu makaleyle birlikte izlemek için, [Machine Learning algoritması sayfası sayfasına](../algorithm-cheat-sheet.md)gidin.
> 
> 

Bu öneriler, birçok veri bilimcilerinin ve makine öğrenimi uzmanlarının derlenmiş görüşleri ve ipuçları. Her şeyi kabul etmedi, ancak onları kaba bir yarışmaya yönelik olarak kullanmaya çalıştık. Deagreement deyimlerinin çoğu "It depends..." ile başlar


> [!TIP]
> Ortak makine öğrenimi sorularını cevaplamak için kullanılan popüler algoritmaların öğrenilmesi için Machine Learning temelleri hakkında kolay bir bilgi grafiğine genel bakış için bkz. [algoritma örnekleri Ile makine öğrenimi temelleri](basics-infographic-with-algorithm-examples.md).

## <a name="flavors-of-machine-learning"></a>Machine Learning 'in türleri

### <a name="supervised"></a>Denetimli

Denetimli öğrenme algoritmaları, örnek kümesini temel alarak tahminler yapar. Örneğin, geçmiş stok fiyatları gelecekteki fiyatlar hakkında tahminler sağlamak için kullanılabilir. Eğitim için kullanılan her örnek, bu örnekte Stok fiyatı olan, ilgilendiğiniz değere göre etiketlidir. Denetimli bir öğrenme algoritması, bu değer etiketlerindeki desenleri arar. Bu, söz konusu olabilecek herhangi bir bilgiyi, haftanın günü, mevsimi, şirketin finansal verileri, sektör türü, kesintiye uğramayan olayların varlığı ve her algoritma farklı tür desenleri arar. Algoritma en iyi model bulduktan sonra, bu düzenin, verileri etiketsiz test verilerinin (yarın 'nin fiyatları) tahmin edilmesi için bu kalıbı kullanır.

Denetimli öğrenme, makine öğrenmesinin popüler ve kullanışlı bir türüdür. Tek bir istisna ile, klasik sürümündeki Azure Machine Learning Studio tüm modüller denetimli bir öğrenme algoritmadır. Azure Machine Learning Studio (klasik) içinde temsil edilen birkaç özel denetimli öğrenme türü vardır: sınıflandırma, gerileme ve anomali algılama.

* **Sınıflandırma**. Veriler bir kategoriyi tahmin etmek için kullanılırken, denetimli öğrenme de sınıflandırma olarak adlandırılır. Bir görüntüyü ' Cat ' veya ' köpek ' resmi olarak atarken bu durum büyük bir durumdur. Yalnızca iki seçenek olduğunda, bu **iki sınıf** veya **binom sınıflandırması**olarak adlandırılır. Daha fazla kategori olduğunda, bu sorun, NCAA Mart 'tan bağımsız olarak, bu sorun **çok sınıflı sınıflandırma**olarak bilinir.
* **Gerileme**. Bir değer tahmin edildiğinde, stok fiyatlarıyla birlikte denetimli öğrenme gerileme olarak adlandırılır.
* **Anomali algılama**. Bazen amaç, olağan dışı olarak yalnızca olağandışı veri noktalarını tanımlamaktır. Sahtekarlık algılamasında, örneğin, tüm yüksek düzeyde olağandışı kredi kartı harcama desenleri şüphelidir. Olası Çeşitlemeler çok sayıda ve eğitim örneklerinden daha az sayıda olduğundan, sahte etkinliğin nasıl göründüğünü öğrenmek mümkün değildir. Anomali algılama 'nın aldığı yaklaşım, normal etkinliğin ne gibi göründüğünü (sahte olmayan işlemlerin geçmişini kullanarak) ve önemli ölçüde farklılık gösteren bir şeyi belirlemektir.

### <a name="unsupervised"></a>Denetimsiz

Denetimli öğreniminde, veri noktalarında bunlarla ilişkili etiket yok. Bunun yerine, denetimli bir öğrenme algoritmasının hedefi, verileri bir şekilde veya yapısını betimleyen şekilde düzenmaktır. Bu, daha basit veya daha fazla görünmesi için verileri kümeler halinde gruplamanın veya karmaşık verilere bakmanın farklı yollarını bulmanın anlamına gelir.

### <a name="reinforcement-learning"></a>Pekiştirmeye dayalı öğrenme

Pekiştirmeye dayalı Learning 'de, algoritma her bir veri noktasına cevap olarak bir eylem seçer. Öğrenme algoritması, daha sonra kararının ne kadar iyi olduğunu belirten bir ödül sinyali de alır.
Bu, algoritmaya bağlı olarak, en yüksek ödül elde edilmesi için stratejisini değiştirir. Şu anda Azure Machine Learning Studio (klasik) içinde hiç pekiştirmeye dayalı Learning algoritma modülü yok. Pekiştirmeye dayalı Learning, bir noktada bir noktadaki algılayıcı okumaların bir veri noktası olduğu ve algoritmanın robot 'un bir sonraki eylemini seçmesi gereken, Robotics 'de ortaktır. Ayrıca, Nesnelerin İnterneti uygulamalar için de doğal bir uyum.

## <a name="considerations-when-choosing-an-algorithm"></a>Algoritma seçerken dikkat edilecek noktalar

### <a name="accuracy"></a>Veritabanınızın

Mümkün olan en doğru yanıtı almak her zaman gerekli değildir.
Bazen, için kullanmak istediğiniz seçeneğe bağlı olarak yaklaşık bir yaklaşık değer vardır. Bu durumda, daha yaklaşık yöntemlerle işleme süresini önemli ölçüde kesmeniz mümkün olabilir. Daha yakın yöntemlerin başka bir avantajı, doğal olarak aşırı sığdırmayı kullanmaktan kaçınmaktır.

### <a name="training-time"></a>Eğitim süresi

Bir modeli eğitmek için gereken dakika veya saat sayısı, algoritmalar arasında harika bir işlem olduğunu gösterir. Eğitim süresi genellikle doğrulukla yakından bağlantılıdır. Ayrıca, bazı algoritmalar diğerlerine göre veri noktası sayısına daha duyarlıdır.
Zaman sınırlı olduğunda, özellikle veri kümesi büyükse, algoritmayı tercih edebilir.

### <a name="linearity"></a>Doğrity

Birçok makine öğrenimi algoritması, doğrlılığını kullanır. Doğrusal sınıflandırma algoritmaları sınıfların düz bir çizgi (veya daha yüksek boyutlu bir analog) ile ayrılalabilen varsayılır. Bunlar, Lojistik gerileme ve destek vektör makinelerini (Azure Machine Learning Studio (klasik) uygulandığı gibi) içerir.
Doğrusal regresyon algoritmaları, veri eğilimlerinin düz bir çizgi olduğunu varsayar. Bu varsayımlar bazı sorunlar için hatalı değildir, ancak diğerleri üzerinde doğruluk altına alır.

![Doğrusal olmayan sınıf sınırı](./media/algorithm-choice/image1.png)

Doğrusal ***olmayan sınıf sınırı*** *-bir doğrusal sınıflandırma algoritmasına bağlı olarak doğruluk azalmasıyla sonuçlanır*

![Doğrusal olmayan bir eğilim içeren veriler](./media/algorithm-choice/image2.png)

Doğrusal olmayan bir ***eğilim Içeren veriler*** *-Doğrusal regresyon yöntemi kullanılması gerekenden çok daha büyük hatalar üretir*

Tehlikeleri olmasına rağmen doğrusal algoritmalar ilk saldırı hattı olarak çok popüler. Algorithmically basit ve hızlı bir şekilde eğitme eğilimindedir.

### <a name="number-of-parameters"></a>Parametre sayısı

Parametreler, bir algoritma ayarlarken bir veri bilimconu açmak için aldığı KBS 'lardır. Bunlar hata toleransı veya yineleme sayısı gibi algoritmaların davranışını etkileyen sayılardır veya algoritmanın nasıl davrandığı çeşitleri arasındaki seçeneklere sahiptir. Algoritmanın eğitim süresi ve doğruluğu, bazı durumlarda yalnızca doğru ayarları almak için oldukça hassas olabilir. Genellikle, çok sayıda parametreye sahip algoritmalar iyi bir bileşim bulmak için en fazla deneme ve hata gerektirir.

Alternatif olarak, seçtiğiniz ayrıntı düzeyinde tüm parametre birleşimlerini otomatik olarak yapan Azure Machine Learning Studio klasik sürümünde bir [parametre swebir](algorithm-parameters-optimize.md) modül bloğu vardır. Parametre alanının yayıldığından emin olmanın harika bir yolu olsa da, bir modeli eğitmek için gereken süre parametre sayısıyla üstel olarak artar.

Çok sayıda parametreye sahip olan baş, genellikle algoritmanın daha fazla esneklik olduğunu gösterir. Genellikle parametre ayarlarının doğru birleşimini bulabileceğiniz için çok iyi bir doğruluk elde edebilir.

### <a name="number-of-features"></a>Özellik sayısı

Belirli veri türleri için, özellik sayısı veri noktalarının sayısıyla karşılaştırıldığında çok büyük olabilir. Bu durum genellikle, genelikler veya metin verileri ile yapılır. Çok sayıda özellik bazı öğrenme algoritmalarından sonra eğitim süresi unfeasibly uzun sürebilir. Destek vektör makineleri özellikle bu durumda iyi uygundur (aşağıya bakın).

### <a name="special-cases"></a>Özel durumlar

Bazı öğrenme algoritmaları, verilerin yapısı veya istenen sonuçlar hakkında belirli varsayımlar yapar. İhtiyaçlarınıza uyan bir tane bulabiliyorsanız, size daha yararlı sonuçlar, daha kesin tahminler veya daha hızlı eğitim süreleri verebilir.

| **Algoritma** | **Veritabanınızın** | **Eğitim süresi** | **Doğrity** | **Parametreler** | **Notlar** |
| --- |:---:|:---:|:---:|:---:| --- |
| **İki sınıf sınıflandırma** | | | | | |
| [Lojistik regresyon](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [karar ormanı](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [karar Jungle](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Düşük bellek ayak izi |
| [Artırılmış karar ağacı](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Büyük bellek ayak izi |
| [sinir ağı](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[Ek özelleştirme mümkündür](azure-ml-netsharp-reference-guide.md) |
| [Ortalama Perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [Destek vektör makinesi](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Büyük özellik kümeleri için iyi |
| [Yerel olarak derin destek vektör makinesi](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Büyük özellik kümeleri için iyi |
| [Bayes ' noktası makinesi](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Çok sınıf sınıflandırması** | | | | | |
| [Lojistik regresyon](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [karar ormanı](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [karar Jungle](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Düşük bellek ayak izi |
| [sinir ağı](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[Ek özelleştirme mümkündür](azure-ml-netsharp-reference-guide.md) |
| [tek-v-tümü](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Seçilen iki sınıf yöntemin özelliklerine bakın |
| **Regresyon** | | | | | |
| [Doğrusal](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [Bayeduyi doğrusal](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [karar ormanı](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [Artırılmış karar ağacı](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Büyük bellek ayak izi |
| [hızlı orman quantile](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Puan tahminleri yerine dağıtımlar |
| [sinir ağı](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[Ek özelleştirme mümkündür](azure-ml-netsharp-reference-guide.md) |
| [Olacağını](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Teknik açıdan günlüğe kaydetme-doğrusal. Tahmine dayalı sayımlar için |
| [numarasını](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |Tahmine dayalı sıralama için |
| **Anormallik algılama** | | | | | |
| [Destek vektör makinesi](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Büyük özellik kümeleri için özellikle iyi |
| [PCA tabanlı anomali algılama](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K-anlamı](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |Kümeleme algoritması |

**Algoritma özellikleri:**

**●** -mükemmel doğruluk, hızlı eğitim süreleri ve doğrity kullanımını gösterir

**○** -iyi doğruluk ve orta eğitim sürelerini gösterir

## <a name="algorithm-notes"></a>Algoritma notları

### <a name="linear-regression"></a>Çizgisel regresyon

Daha önce belirtildiği gibi, [Doğrusal regresyon](/azure/machine-learning/studio-module-reference/linear-regression) , veri kümesine bir hat (veya düzlem veya hiper Düzle) sığdırır. Bu, basit ve hızlı bir çalışmadır, ancak bazı sorunlar için aşırı uyarlaması olabilir.

![Doğrusal eğilimi olan veriler](./media/algorithm-choice/image3.png)

***Doğrusal eğilimi olan veriler***

### <a name="logistic-regression"></a>Lojistik regresyon

Adında ' regresyon ' olsa da, lojistik regresyon aslında [iki sınıf ve çok sınıflı](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) [sınıflandırma için](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) güçlü bir araçtır. Hızlı ve kolaydır. Düz bir çizgi yerine '-şekillendirilmiş eğinin kullanması, verileri gruplara bölmek için doğal bir uyum sağlar. Lojistik regresyon, doğrusal sınıf sınırları verir, bu nedenle kullandığınızda, doğrusal bir yaklaşık ile yaşadığınız bir şeyin olduğundan emin olun.

![Yalnızca bir özellik ile iki sınıflı verilere gerileme lojistik](./media/algorithm-choice/image4.png)

***Tek bir özellik ile iki sınıflı verilere yönelik bir Lojistik gerileme*** ; *sınıf sınırı, lojistik eğrisinin her iki sınıfa da yakın* olduğu bir noktasıdır

### <a name="trees-forests-and-jungles"></a>Ağaçlar, ormanlar ve harikası

Karar ormanları ([gerileme](/azure/machine-learning/studio-module-reference/decision-forest-regression), [iki sınıf](/azure/machine-learning/studio-module-reference/two-class-decision-forest)ve [birden](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)çok sınıf), karar harikası ([iki sınıf](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) ve [birden](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)çok sınıf) ve daha fazla karar ağaçları ([gerileme](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) ve [iki sınıf](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)), kararına göre yapılır temel bir makine öğrenimi kavramı olan ağaçlar. Birçok karar ağacının çeşitleri vardır ancak hepsi aynı şeyi yapar, özellik alanını çoğunlukla aynı etiketle aynı şekilde alt bölümlere ayırır. Bunlar, sınıflandırma veya gerileme yapıp yapmadığınıza bağlı olarak tutarlı kategori veya sabit değer bölgeleri olabilir.

![Karar ağacı alt bir özellik alanını böler](./media/algorithm-choice/image5.png)

***Karar ağacı alt bir özellik alanını kabaca Tekdüzen değerlerinin bölgelerine böler***

Bir özellik alanı, rastgele küçük bölgelere daha fazla bölünebildiğinden, bölge başına bir veri noktasına sahip olmak için yeterince fazla bölmek çok kolay olabilir. Bu, büyük/veya çok fazla ekleme örneğidir. Bunu önlemek için, ağaçların bağıntılı olmadığından emin olmak için özel matematik ile birlikte büyük bir ağaç kümesi oluşturulur. Bu "karar ormanının" ortalaması, fazla sığdırmayı önleyen bir ağacdır. Karar ormanları, çok fazla bellek kullanabilir. Karar harikası, biraz daha uzun bir eğitim zamanının masrafına daha az bellek tüketen bir değişkendir.

Artırılmış karar ağaçları, her bölgede kaç kez alt bölüme ve ne kadar veri noktasına izin verileceğini sınırlayarak aşırı sığdırmayı önleyin. Algoritma, her biri daha önce ağaç tarafından kalan hatayı telafi eden bir ağaç dizisi oluşturur. Sonuç, çok miktarda bellek kullanmanın eğilimi gösteren çok doğru bir öğrenildir. Tam teknik açıklama için, [Friedman 'ın orijinal kağıdına](https://www-stat.stanford.edu/~jhf/ftp/trebst.pdf)göz atın.

[Hızlı orman quantile gerileme](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) , bir bölgedeki verilerin yalnızca tipik (ortanca) değerini değil, aynı zamanda da ölçü biçiminde dağıtımını sağlamak istediğiniz özel durum için karar ağaçlarının bir çeşitlemesi.

### <a name="neural-networks-and-perceptrons"></a>Sinir Networks ve perceptrons

Sinir Networks, çok [Lass](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [iki sınıf](/azure/machine-learning/studio-module-reference/two-class-neural-network)ve [gerileme](/azure/machine-learning/studio-module-reference/neural-network-regression) sorunlarını kapsayan, beyana yönelik öğrenme algoritmalardır. Bunlar sonsuz bir çeşitte sunulur, ancak klasik Azure Machine Learning Studio sinir ağları, tüm yönlendirilmiş döngüsel grafikler biçimidir. Bu, giriş özelliklerinin çıkışlar içine açılmadan önce bir dizi katmanda ileri (hiçbir şekilde geri) geçirileceği anlamına gelir. Her katmanda, girişler çeşitli kombinasyonlara ağırlıklı, toplanmıştır ve sonraki katmana geçirilir. Bu basit hesaplamalar birleşimi, karmaşık sınıf sınırlarını ve veri eğilimlerini öğrenerek Magic tarafından elde edilecek. Bu sıralamanın çok katmanlı ağları, çok fazla teknik raporlama ve bilime sahip olan "derin öğrenme" işlemini gerçekleştirir.

Bu yüksek performans, olmasa da ücretsiz olarak gelmeyecektir. Sinir Networks, özellikle çok sayıda özelliği olan büyük veri kümelerinde eğmek uzun sürebilir. Ayrıca, çoğu algoritmalardan daha fazla parametreye sahiptir, bu da parametre üstün bir şekilde eğitim süresini genişlettiğinden büyük bir işlem olur.
Ve [kendi ağ yapısını belirtmek](azure-ml-netsharp-reference-guide.md)isteyen overachievers için, olanaklar de tüketilillerdir.

![Sinir Networks tarafından öğrenilen sınırlar](./media/algorithm-choice/image6.png)

***Sinir Networks tarafından öğrenilen sınırlar karmaşık ve düzensiz olabilir***

[İki sınıflı ortalama Perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) , sinir Networks 'un eğitim sürelerinin yanıtını skyrocketing. Doğrusal sınıf sınırları sağlayan bir ağ yapısı kullanır. Bugünün standartlarına göre neredeyse temel, ancak uzun bir çalışma robustly geçmişi vardır ve hızlı bir şekilde öğrenecek kadar küçüktür.

### <a name="svms"></a>SVMs

Destek vektör makineleri (SVMs), sınıfları mümkün olduğunca geniş bir kenar boşluğuna ayıran sınırı bulur. İki sınıf açık bir şekilde ayrılacaksa, algoritmalar olabilecek en iyi sınırı bulur. Azure Machine Learning Studio (klasik) yazıldığı gibi, [iki SıNıFLı SVM](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) bunu yalnızca düz bir satırla yapar (SVM-konuş ' ta, doğrusal bir çekirdek kullanır).
Bu doğrusal bir değer sağladığından, oldukça hızlı bir şekilde çalışabilir. Aslında, metin veya genomik verileri gibi özellik yoğun verilerle nerede olduğu. Bu durumlarda, SVMs sınıfları daha hızlı ve diğer algoritmalardan daha kısa bir süre içinde ayırarak yalnızca bir bellek miktarı gerektirmenize ek olarak ayrılabilir.

![Vektör makinesi sınıf sınırını destekleme](./media/algorithm-choice/image7.png)

***Tipik bir destek vektör makinesi sınıfı sınırı iki sınıfı ayıran kenar boşluğunu en üst düzeye çıkarır***

Microsoft Research 'ın başka bir ürünü olan [iki sınıf yerel olarak derin](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) bir SVM, doğrusal sürümün hız ve bellek verimliliğini büyük bir şekilde tutan, doğrusal olmayan bir değişkendir. Doğrusal yaklaşımın yeterince kesin yanıt vermediği durumlarda idealdir. Geliştiriciler sorunu birkaç küçük doğrusal SVM sorununa ayırarak hızlı bir şekilde tutulur. Bu eli nasıl çektikleri hakkındaki ayrıntıların [tam açıklamasını](http://proceedings.mlr.press/v28/jose13.html) okuyun.

Doğrusal olmayan svms 'nin zekice uzantısını kullanarak, [tek sınıf SVM](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) , tüm veri kümesini sıkı bir şekilde özetleyen bir sınır çizer. Anomali algılama için faydalıdır. Bu sınırın dışında kalan tüm yeni veri noktaları, en önemli bir değer olacak şekilde çok daha fazla.

### <a name="bayesian-methods"></a>Bayeme yöntemleri

Bayeme metotlarının yüksek oranda istenen kalitesi vardır: çok fazla ekleme yapmaktan kaçınır. Bu, yanıtın büyük olasılıkla dağıtımını yapmadan önce bazı varsayımlar yaparak bunu yapar. Bu yaklaşımın başka bir byürünü, çok az parametreye sahip olmalarından oluşur. Azure Machine Learning Studio klasik sürümünde hem sınıflandırma ([iki sınıf Bayes ' noktası makinesi](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)) hem de gerileme ([bayeeli doğrusal regresyon](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)) için bayeme algoritmaları vardır.
Bu, verilerin düz bir satırla bölünebileceğini veya uygun olduğunu varsaydığını unutmayın.

Geçmiş notta, Bayes ' Point machines Microsoft Research ' de geliştirilmiştir. Bunların arkasında daha fazla harika bir teorik iş vardır. İlgilendiğiniz öğrenci, [JMLR ' deki özgün makaleye](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) ve [kemal 'e göre ınsighrg bloguna](https://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx)yönlendirilir.

### <a name="specialized-algorithms"></a>Özel algoritmalar
Çok özel bir amacınız varsa, şansda olabilirsiniz. Azure Machine Learning Studio (klasik) koleksiyonu içinde, içinde uzmanlaşan algoritmalar vardır:

- sıra Tahmini ([sıralı regresyon](/azure/machine-learning/studio-module-reference/ordinal-regression)),
- Sayı Tahmini ([Poisson regresyon](/azure/machine-learning/studio-module-reference/poisson-regression)),
- anomali algılama ( [asıl bileşen analizine](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) ve tek bir [destek vektör makinelerine](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine)dayalı olarak bir temel alınarak)
- kümeleme ([K-Ortalamalar](/azure/machine-learning/studio-module-reference/k-means-clustering))

![PCA tabanlı anomali algılama](./media/algorithm-choice/image8.png)

***PCA tabanlı anomali algılama*** *-verilerin büyük çoğunluğu bir stereotypical dağıtımına denk gelir; bu dağıtımın önemli ölçüde büyük bir bölümü şüphelidir*

![K-Ortalamalar kullanılarak gruplanmış veri kümesi](./media/algorithm-choice/image9.png)

***Bir veri kümesi, K-Ortalamalar kullanılarak beş küme halinde gruplandırılır***

Ayrıca, N sınıfı sınıflandırma sorununu N-1 2 sınıfı sınıflandırmayla kesen tek bir ve [birden çok Lass Sınıflandırıcısı](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass)vardır. Doğruluk, eğitim süresi ve doğrity özellikleri, kullanılan iki sınıf Sınıflandırıcıların belirlenir.

![İki sınıf sınıflandırıcıları üç sınıf Sınıflandırıcısı oluşturacak şekilde birleştirildi](./media/algorithm-choice/image10.png)

***İki sınıflı sınıflandırıcının çifti, üç sınıf bir sınıflandırıcı oluşturacak şekilde birleştirilir***

Azure Machine Learning Studio klasik sürümü, [Vowpal Wabbit](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model)başlığı altında güçlü bir makine öğrenimi çerçevesine erişimi de içerir.
Sınıflandırma ve regresyon sorunları öğrendiğinden ve kısmen etiketlenmemiş verilerden bile bilgi sağlayabileceği için kategori burada açıklanmıştır. Bunu, bir dizi öğrenme algoritmalarından, kayıp fonksiyonlarından ve en iyi duruma getirme algoritmalarından herhangi birini kullanacak şekilde yapılandırabilirsiniz. Baştan sona, paralel ve son derece hızlı olacak şekilde tasarlanmıştır. Az miktarda çabayla daha kapsamlı, büyük özellik kümelerini işler.
Microsoft Research 'ın can 'ın kendi John Langford tarafından başlayıp bu yana, bir hisse senedi arabası algoritmalarının alanındaki bir giriş formüldür. Her bir sorun buna uyar, ancak sizinki varsa, bu, kendi arabiriminde öğrenme eğrisi için gereken bir değer olabilir. Ayrıca, çeşitli dillerde [tek başına açık kaynak kodu](https://github.com/JohnLangford/vowpal_wabbit) olarak da kullanılabilir.

## <a name="next-steps"></a>Sonraki Adımlar

* Ortak makine öğrenimi sorularını cevaplamak için kullanılan popüler algoritmaların öğrenilmesi için Machine Learning temelleri hakkında kolay bir bilgi grafiğine genel bakış için bkz. [algoritma örnekleri Ile makine öğrenimi temelleri](basics-infographic-with-algorithm-examples.md).

* Machine Learning Studio (klasik) ' de bulunan tüm makine öğrenimi algoritmalarının kategorilerine göre bir liste için, bkz. Machine Learning Studio (klasik) algoritması ve modül yardımı 'nda [modeli başlatma](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) .

* Machine Learning Studio klasik sürümündeki algoritmaların ve modüllerin tam alfabetik listesi için, Machine Learning Studio (klasik) algoritması ve modül yardımı 'nda [a-Z Machine Learning Studio (klasik) modüllerine](/azure/machine-learning/studio-module-reference/a-z-module-list) bakın.
