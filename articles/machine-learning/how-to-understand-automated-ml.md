---
title: Oto ml deneme sonuçlarını değerlendir
titleSuffix: Azure Machine Learning
description: Otomatik makine öğrenimi denemenizin her biri için grafikleri ve ölçümleri görüntülemeyi ve değerlendirmeyi öğrenin.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: 2bed95385823a167c7a31eed11d752894984ea38
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791886"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Otomatik makine öğrenimi sonuçlarını değerlendir

Bu makalede, otomatik makine öğrenimi (otomatik ML) denemenizin eğitilen modellerini değerlendirmeyi ve karşılaştırmayı öğrenin. Otomatikleştirilmiş bir ML denemesi sırasında birçok çalışma oluşturulur ve her çalıştırma bir model oluşturur. Her model için otomatik ML, modelin performansını ölçmenize yardımcı olan değerlendirme ölçümleri ve grafikler oluşturur. 

Örneğin, otomatik ML, deneme türüne göre aşağıdaki grafikleri oluşturur.

| Sınıflandırma| Regresyon/tahmin |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Karışıklık matrisi](#confusion-matrix)                       | [Artıklar histogramı](#residuals)        |
| [Alıcı işletim özelliği (ROC) eğrisi](#roc-curve) | [Tahmin edilen ve true](#predicted-vs-true) |
| [Precision-hatırla (PR) eğrisi](#precision-recall-curve)      |                                          |
| [Eğriyi yükselt](#lift-curve)                                   |                                          |
| [Kümülatif kazançlar eğrisi](#cumulative-gains-curve)           |                                          |
| [Ayar eğrisi](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. (Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://aka.ms/AMLFree) )
- İle oluşturulan bir Azure Machine Learning deneme:
  - [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) (kod gerekmez)
  - [Azure Machine Learning Python SDK 'sı](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Çalıştırma sonuçlarını görüntüle

Otomatikleştirilmiş ML denemeniz tamamlandıktan sonra, çalışma geçmişi şunun aracılığıyla bulunabilir:
  - [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) ile tarayıcı
  - [Rundetails Jupyter pencere öğesini](/python/api/azureml-widgets/azureml.widgets.rundetails) kullanan bir Jupyter Not defteri

Aşağıdaki adımlar ve videoda, çalışma geçmişi ve model değerlendirme ölçümlerini ve grafiklerini Studio 'da nasıl görüntüleyebileceğiniz gösterilmektedir:

1. [Studio 'Da oturum açın](https://ml.azure.com/) ve çalışma alanınıza gidin.
1. Sol menüden **denemeleri**' yi seçin.
1. Denemeleri listesinden denemenizi seçin.
1. Sayfanın altındaki tabloda bir otomatik ML çalıştırması seçin.
1. **Modeller** sekmesinde, değerlendirmek Istediğiniz modelin **algoritma adını** seçin.
1. **Ölçümler** sekmesinde, ölçüm ve grafikleri görüntülemek için soldaki onay kutularını kullanın.

![Studio 'da ölçümleri görüntüleme adımları](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Sınıflandırma ölçümleri

Otomatikleştirilmiş ML, denemenize göre oluşturulan her bir sınıflandırma modeli için performans ölçümlerini hesaplar. Bu ölçümler, scikit öğrenme uygulamasını temel alır. 

Birçok sınıflandırma ölçümü, iki sınıf üzerinde ikili sınıflandırma için tanımlanır ve birden çok sınıf sınıflandırması için bir puan üretmek için sınıfların ortalamasını gerektirir. Scikit-öğrenme, üç adet otomatik ML 'nin sunduğu birkaç ortalama yöntem sağlar: **Macro**, **mikro** ve **ağırlıklı**.

- **Makro** -her sınıf için ölçüyü hesapla ve ön ağırlıklı ortalamayı al
- **Mikro** -toplam doğru pozitif sonuç sayısını, yanlış negatifleri ve hatalı pozitif sonuçları (sınıflardan bağımsız) sayarak ölçüyü küresel olarak hesaplayın.
- **Ağırlıklı** -her sınıf için ölçüyü hesaplayın ve sınıf başına örnek sayısına göre ağırlıklı ortalamayı alın.

Her ortalama Yöntem avantajlarına sahip olsa da, uygun yöntemi seçerken yaygın bir değerlendirme sınıf dengesizdir. Sınıflarda farklı sayıda örnek varsa, minypo sınıflarının çoğunluk sınıflarına eşit ağırlığa verildiği bir makro ortalaması kullanımı daha bilgilendirici olabilir. [OTOMATIK ml 'de ikili ve çoklu sınıf ölçümleri](#binary-vs-multiclass-classification-metrics)hakkında daha fazla bilgi edinin. 

Aşağıdaki tabloda, denemeniz için oluşturulan her sınıflandırma modeli için otomatik ML 'nin hesapladığı model performans ölçümleri özetlenmektedir. Daha fazla ayrıntı için, her ölçümün **Hesaplama** alanında bulunan scikit-öğrenme belgelerine bakın. 

|Metric|Açıklama|Hesaplama|
|--|--|---|
|AUC | AUC, [alıcı Işletim özelliği eğrisinin](#roc-curve)altındaki alandır.<br><br> **Amaç:** 1 ' e yaklaşarak daha iyi <br> **Aralık:** [0, 1]<br> <br>Desteklenen ölçüm adları şunlardır <li>`AUC_macro`, her sınıf için AUC 'nin aritmetik ortalaması.<li> `AUC_micro`, gerçek pozitifleri ve her bir sınıftan yanlış pozitif durumları birleştirerek hesaplanır. <li> `AUC_weighted`, her sınıf için puanın aritmetik ortalaması, her bir sınıftaki doğru örnek sayısı tarafından ağırlıklı olarak hesaplanır.<br><br>Not: yalnızca iki sınıf varsa, otomatik ML tarafından bildirilen AUC değerleri ROC grafiğiyle eşleşmeyebilir. İkili sınıflandırma için, AUC 'nin temel alınan scikit-öğrenme uygulaması aslında makro/mikro/ağırlıklı ortalama uygulanmaz. Bunun yerine, en olası pozitif sınıf için AUC döndürülür. ROC grafiği, birden çok sınıf için olduğu gibi ikili sınıflandırma için sınıf ortalaması uygulamaya devam eder.  |[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Doğruluk, doğru sınıf etiketleriyle tam olarak eşleşen tahminlerden oranıdır. <br> <br>**Amaç:** 1 ' e yaklaşarak daha iyi <br> **Aralık:** [0, 1]|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|Ortalama duyarlık, her bir eşiğin elde ettiği ağırlıklı ortalamanın ağırlıklı ortalaması olarak bir duyarlık geri çağırma eğrisini özetler ve bu da ağırlık olarak kullanılan önceki eşikten geri çekme artışı artar. <br><br> **Amaç:** 1 ' e yaklaşarak daha iyi <br> **Aralık:** [0, 1]<br> <br>Desteklenen ölçüm adları şunlardır<li>`average_precision_score_macro`, her sınıfın ortalama duyarlık puanının aritmetik ortalaması.<li> `average_precision_score_micro`, her kesme sırasında gerçek pozitif sonuçları ve yanlış pozitif durumları birleştirerek hesaplanır.<li>`average_precision_score_weighted`, her sınıfın ortalama duyarlık puanı, her sınıftaki doğru örnek sayısı ile ağırlıklı aritmetik ortasıdır.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Dengeli doğruluk, her sınıf için geri çekmenin aritmetik ortalaması anlamına gelir.<br> <br>**Amaç:** 1 ' e yaklaşarak daha iyi <br> **Aralık:** [0, 1]|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|F1 puanı duyarlık ve geri çağırma için harmonik bir anlama gelir. Bu, hem hatalı pozitif sonuçlar hem de yanlış negatifler için iyi bir dengeli ölçüdür. Ancak, hesaba doğru bir negatifler almaz. <br> <br>**Amaç:** 1 ' e yaklaşarak daha iyi <br> **Aralık:** [0, 1]<br> <br>Desteklenen ölçüm adları şunlardır<li>  `f1_score_macro`: her sınıf için F1 puanı aritmetik ortalaması. <li> `f1_score_micro`: toplam doğru pozitif sonuç sayısı, yanlış negatifler ve hatalı pozitif sonuçlar sayımına göre hesaplanır. <li> `f1_score_weighted`: her bir sınıf için, her bir sınıf için bir F1 puanı sınıf sıklığı ağırlıklı ortalama|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Bu, bir dayalı sınıflandırıcının tahminlerini verilen doğru etiketlerin olumsuz günlük olma olasılığı olarak tanımlanan (ÇOKTERİMLİ) Lojistik gerileme ve sinir Networks gibi uzantılar içinde kullanılan kayıp işlevidir. <br><br> **Amaç:** 0 ' a yaklaşarak daha iyi <br> **Aralık:** [0, INF)|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| Normalleştirilmiş makro geri çağırma, makronun ortalama ve normalleştirilmiş olduğunu geri çağırır, böylece rastgele performans 0 puanına sahip olur ve mükemmel performans 1 puanına sahiptir. <br> <br>**Amaç:** 1 ' e yaklaşarak daha iyi <br> **Aralık:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>burada, `R` `recall_score_macro` rastgele tahminler için beklenen değerdir.<br><br>`R = 0.5`&nbsp;&nbsp;ikili &nbsp; sınıflandırma için. <br>`R = (1 / C)` C sınıfı sınıflandırma sorunları için.|
matthews_correlation | Matthews bağıntı katsayısı, bir sınıfın diğerinden çok daha fazla örneği olsa bile kullanılabilecek, doğruluk açısından dengeli bir ölçüdür. Katsayı 1, mükemmel tahmin, 0 rastgele tahmin ve-1 ters tahmin anlamına gelir.<br><br> **Amaç:** 1 ' e yaklaşarak daha iyi <br> **Aralık:** [-1, 1]|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
duyarlık|Duyarlık negatif örneklerin pozitif olarak etiketlenmesini önlemek için bir modelin özelliğidir. <br><br> **Amaç:** 1 ' e yaklaşarak daha iyi <br> **Aralık:** [0, 1]<br> <br>Desteklenen ölçüm adları şunlardır <li> `precision_score_macro`, her bir sınıf için duyarlık ortalaması. <li> `precision_score_micro`, toplam doğru pozitif değer ve hatalı pozitif sonuçlar sayarak Global olarak hesaplanır. <li> `precision_score_weighted`, her sınıf için duyarlık ortalaması, her bir sınıftaki doğru örnek sayısına göre ağırlıklı olarak.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
yakalama| Geri çağırma, bir modelin tüm pozitif örnekleri tespit etme yeteneğidir. <br><br> **Amaç:** 1 ' e yaklaşarak daha iyi <br> **Aralık:** [0, 1]<br> <br>Desteklenen ölçüm adları şunlardır <li>`recall_score_macro`: her sınıf için geri çekmenin aritmetik ortalaması. <li> `recall_score_micro`: toplam doğru pozitif sonuç sayısı, yanlış negatifler ve hatalı pozitif sonuçlar sayımarak Global olarak hesaplanır.<li> `recall_score_weighted`: her sınıf için geri çekmenin aritmetik ortalaması, her bir sınıftaki doğru örnek sayısına göre ağırlıklı olarak.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|Ağırlıklı doğruluk, her bir örneğin aynı sınıfa ait toplam örnek sayısı tarafından ağırlıklı olduğu doğruluk örneğidir. <br><br>**Amaç:** 1 ' e yaklaşarak daha iyi <br>**Aralık:** [0, 1]|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>İkili ve birden çok Lass sınıflandırma ölçümleri

Otomatikleştirilmiş ML ikili ve çok sınıflı ölçümler arasında ayrım yapmaz. Aynı doğrulama ölçümleri, bir veri kümesinin iki sınıfa veya ikiden fazla sınıfa sahip olup olmadığını rapor edilir. Ancak bazı ölçümler çok sınıflı sınıflandırmaya yöneliktir. Bir ikili veri kümesine uygulandığında, bu ölçümler, bekleolabileceğiniz gibi sınıf olarak hiçbir sınıfı kabul eder `true` . Birden çok sınıf için açıkça amaçlanan ölçümler,, veya ile sonlanmış `micro` `macro` `weighted` . Örnekler,,, `average_precision_score` `f1_score` ve içerir `precision_score` `recall_score` `AUC` .

Örneğin, geri çekmeyi hesaplamak yerine `tp / (tp + fn)` , `micro` `macro` `weighted` bir ikili sınıflandırma veri kümesinin her iki sınıfının birden çok Lass ortalaması (, veya) ortalamaları. Bu, sınıf için geri çekmenin hesaplanmasının ve `true` `false` sonra ikisinin ortalamasını alan eşdeğerdir.

Otomatikleştirilmiş ML ikili sınıflandırma veri kümeleri için ölçümler olan ikili ölçümleri hesaplamaz. Ancak bu ölçümler, otomatik ML 'nin bu belirli çalışma için oluşturduğu [karışıklık matrisi](#confusion-matrix) kullanılarak el ile hesaplanabilir. Örneğin, `tp / (tp + fp)` bir 2x2 karışıklık matris grafiğinde gösterilen doğru pozitif ve yanlış pozitif değerler ile duyarlık hesaplayabilirsiniz.

## <a name="confusion-matrix"></a>Karışıklık matrisi

Karışıklık matrisleri, bir makine öğrenimi modelinin, sınıflandırma modelleriyle ilgili tahminlerde sistematik hatalar yapma konusunda bir görsel sağlar. Adda "karışıklıklar" sözcüğü bir model "kafa karıştırıcı" veya hatalı etiketleme örneklerinden gelir. `i`Bir karışıklık matrisi içindeki satır ve sütundaki bir hücre, `j` sınıfa ait olan `C_i` ve model olarak model tarafından sınıflandırılan değerlendirme veri kümesindeki örneklerin sayısını içerir `C_j` .

Studio 'da, daha koyu bir hücre daha fazla örnek sayısını gösterir. Açılan listede **normalleştirilmiş** görünümü seçmek, sınıf olarak tahmin edilen sınıfın yüzdesini göstermek için her matris satırının üzerinde normalleştirilecektir `C_i` `C_j` . Varsayılan **Ham** görünümün avantajı, gerçek sınıfların dağıtımındaki dengesizliği, modelin minınlık sınıfından örnekleri yanlış sınıflandırmasına neden olup olmadığını, imdengeli veri kümelerinde yaygın bir sorun olduğunu görebilmelidir.

İyi bir modelin karışıklık matrisi, diyagonal olarak birçok örnek içermelidir.

### <a name="confusion-matrix-for-a-good-model"></a>İyi bir model için karışıklık matrisi 
![İyi bir model için karışıklık matrisi ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Hatalı model için karışıklık matrisi
![Hatalı model için karışıklık matrisi](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC eğrisi

Alıcı işletim özelliği (ROC) eğrisi, karar eşiği değiştikçe doğru pozitif oran (TPR) ve yanlış pozitif hız (FPR) arasındaki ilişkiyi çizer. Temel sınıf, minınlık sınıflarından katkıların dengesizliği olduğu için, üst sınıf olan veri kümelerinde eğitim yaparken, ROC eğrisi daha az bilgilendirici olabilir.

Eğrinin altındaki alan (AUC) doğru sınıflandırılan örneklerin oranı olarak yorumlanamaz. Daha kesin olarak AUC, sınıflandırıcı 'nın rastgele seçilmiş bir negatif örnekten daha yüksek bir rastgele seçilmiş pozitif örneği derecelendirmelerinin olasılığıdır. Eğrinin şekli, sınıflandırma eşiğinin veya karar sınırının bir işlevi olarak TPR ve FPR arasındaki ilişki için bir ıntua sağlar.

Grafiğin sol üst köşesine yaklaşıtı bir eğri, olası en iyi modele %100 TPR ve 0% FPR 'ye yaklaşıyor. Rastgele bir model, sol alt köşedeki bir ROC eğrisi ve `y = x` sağ üst köşesinden üretir. Rastgele bir modelden daha kötü bir modelin, satırın altına göre bir ROC eğrisi vardır `y = x` .
> [!TIP]
> Sınıflandırma denemeleri için, otomatik ML modelleri için üretilen çizgi grafiklerin her biri, model başına modeli veya tüm sınıfların ortalaması olarak değerlendirmek için kullanılabilir. Grafiğin sağındaki göstergede sınıf etiketleri ' ne tıklayarak bu farklı görünümler arasında geçiş yapabilirsiniz.

### <a name="roc-curve-for-a-good-model"></a>İyi bir model için ROC eğrisi
![İyi bir model için ROC eğrisi](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>Hatalı model için ROC eğrisi
![Hatalı model için ROC eğrisi](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Duyarlık-geri çağırma eğrisi

Duyarlık geri çağırma eğrisi, karar eşiği değiştikçe duyarlık ve geri çağırma arasındaki ilişkiyi çizer. Yeniden çağırma, bir modelin tüm olumlu örnekleri tespit etme yeteneğinin ve duyarlık 'in negatif örneklerin pozitif olarak etiketlenmesini önlemek için bir modelin özelliğidir. Bazı iş sorunları, yanlış negatifleri ve hatalı pozitif sonuçları önlemenin göreli önemliğine bağlı olarak daha fazla geri çekme ve daha yüksek bir duyarlık gerektirebilir.
> [!TIP]
> Sınıflandırma denemeleri için, otomatik ML modelleri için üretilen çizgi grafiklerin her biri, model başına modeli veya tüm sınıfların ortalaması olarak değerlendirmek için kullanılabilir. Grafiğin sağındaki göstergede sınıf etiketleri ' ne tıklayarak bu farklı görünümler arasında geçiş yapabilirsiniz.
### <a name="precision-recall-curve-for-a-good-model"></a>Precision-iyi bir model için eğriyi geri çağır
![Precision-iyi bir model için eğriyi geri çağır](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Hatalı bir model için duyarlık geri çağırma eğrisi
![Hatalı bir model için duyarlık geri çağırma eğrisi](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Kümülatif kazançlar eğrisi

Kümülatif kazanç eğrisi, tahmin edilen olasılığa göre örnekleri göz önünde bulundurduğumuz örnek yüzdesinin bir işlevi olarak sınıflandırılan pozitif örneklerin yüzdesini doğru bir şekilde çiztik.

Kazancı hesaplamak için, ilk olarak en yüksek olan tüm örnekleri model tarafından öngörülen en düşük olasılığa göre sıralayın. Daha sonra `x%` en yüksek güvenirlik tahminlerden yararlanın. `x%`Kazancın elde etmek için toplam pozitif örnek sayısına göre algılanan pozitif örneklerin sayısını bölün. Kümülatif kazanç, pozitif sınıfa ait olan büyük olasılıkla verilerin yüzde bir kısmını düşünürken tespit ettiğimiz pozitif örneklerin yüzdesidir.

Kusursuz bir model, tüm negatif örneklerin üzerindeki tüm pozitif örnekleri, iki düz kesimden oluşan birikmeli kazanç eğrisine göre derecelendirir. Birincisi, ' den ' e kadar eğimi olan bir satırdır ve `1 / x` `(0, 0)` `(x, 1)` `x` pozitif sınıfa ait örneklerin kesirtir ( `1 / num_classes` sınıflar dengeyse). İkincisi, ile arasında yatay bir satırdır `(x, 1)` `(1, 1)` . İlk kesimde tüm pozitif örnekler doğru sınıflandırıldı ve kümülatif kazanç `100%` , `x%` kabul edilen örneklerin ilki dahilinde.

Taban çizgisi rastgele modelinde, `y = x` `x%` yalnızca `x%` Toplam pozitif örnek sayısının algılandığına ilişkin örneklerin nerede olduğunu takip eden bir kümülatif kazanç eğrisi olacaktır. Kusursuz bir modelde, sol üst köşeye dokunduğu mikro ortalama bir eğri ve toplam kazancı %100 olana kadar eğim veren bir makro ortalama satırı ve `1 / num_classes` yüzde değeri 100 olana kadar yatay olur.
> [!TIP]
> Sınıflandırma denemeleri için, otomatik ML modelleri için üretilen çizgi grafiklerin her biri, model başına modeli veya tüm sınıfların ortalaması olarak değerlendirmek için kullanılabilir. Grafiğin sağındaki göstergede sınıf etiketleri ' ne tıklayarak bu farklı görünümler arasında geçiş yapabilirsiniz.
### <a name="cumulative-gains-curve-for-a-good-model"></a>İyi bir model için birikmeli kazançlar eğrisi
![İyi bir model için birikmeli kazançlar eğrisi](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Hatalı bir model için birikmeli kazançlar eğrisi
![Hatalı bir model için birikmeli kazançlar eğrisi](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Eğriyi yükselt

Yükseltme eğrisi, bir modelin rastgele bir modelle karşılaştırıldığında kaç kez daha iyi performans gösterdiğini gösterir. Yükseltme, toplu kazancın rastgele bir modelin birikmiş kazanmasına oranı olarak tanımlanır.

Bu göreli performans, sınıfların sayısını artırdıkça sınıflandırmanın zor olduğunu dikkate alır. (Rastgele bir model, iki sınıf içeren bir veri kümesiyle karşılaştırıldığında 10 sınıfı olan bir veri kümesinden örneklerin daha yüksek bir kısmını tahmin eder)

Taban çizgisi kaldırma eğrisi, `y = 1` model performansının rastgele bir modelle tutarlı olduğu yerdir. Genel olarak, iyi bir model için yükseltme eğrisi bu grafik üzerinde daha yüksek olacaktır ve x ekseninden uzaklaşarak model tahmine dayalı olarak rastgele tahmine göre çok daha iyi bir performans elde edildiğinde, bu grafikte daha fazla emin olur.

> [!TIP]
> Sınıflandırma denemeleri için, otomatik ML modelleri için üretilen çizgi grafiklerin her biri, model başına modeli veya tüm sınıfların ortalaması olarak değerlendirmek için kullanılabilir. Grafiğin sağındaki göstergede sınıf etiketleri ' ne tıklayarak bu farklı görünümler arasında geçiş yapabilirsiniz.
### <a name="lift-curve-for-a-good-model"></a>İyi bir model için eğriyi kaldırın
![İyi bir model için eğriyi kaldırın](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Hatalı bir model için eğriyi kaldırın
![Hatalı bir model için eğriyi kaldırın](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Ayar eğrisi

Ayar eğrisi, her güven düzeyindeki pozitif örneklerin oranına göre tahmine dayalı olarak bir modelin güvenini çizer. İyi kalibre edilmiş bir model, %100 güvenirlik atayan tahminlerden %100 ' sini doğru olarak sınıflandırır 50 50, tahmine dayalı %20 ' nin %20 ' si için %20 güven atayan ve bu şekilde devam eder. Kusursuz kalibre edilmiş bir modelde, `y = x` modelin her sınıfa ait olma olasılığını mükemmel bir şekilde tahmin ettiği çizgiyi izleyen bir ayar eğrisi bulunur.

Daha duyarlı olmayan bir model, olasılıkların sıfıra yakın olduğunu tahmin eder ve bir, genellikle her bir örneğin sınıfı hakkında daha belirsiz bir şekilde ve ayarlama eğrisi geriye doğru "S" e benzer. Bir alt sınır modeli, tahmin edilen sınıfa ortalama bir olasılık ve ilgili ayar eğrisi ise "S" ile benzer şekilde atanır. Ayar eğrisi, modelin doğru şekilde sınıflandırılacağı bir model göstermez, ancak bunun yerine tahmine dayalı olarak güvenilirliği doğru bir şekilde atayabilme özelliği. Model doğru bir şekilde düşük güvenirlik ve yüksek belirsizlik atarsa, hatalı bir model yine de iyi bir ayarlama eğrisine sahip olabilir.

> [!NOTE]
> Ayar eğrisi, örnek sayısına duyarlıdır; bu nedenle küçük bir doğrulama kümesi, yorumlamak zor olabilecek gürültülü sonuçlar üretebilir. Bu, modelin iyi kalibre olmadığı anlamına gelmez.

### <a name="calibration-curve-for-a-good-model"></a>İyi bir model için ayarlama eğrisi
![İyi bir model için ayarlama eğrisi](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Hatalı model için ayarlama eğrisi
![Hatalı model için ayarlama eğrisi](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Gerileme/tahmin ölçümleri

Otomatik ML, bir gerileme veya tahmin denemesi olmasına bakılmaksızın oluşturulan her bir model için aynı performans ölçümlerini hesaplar. Bu ölçümler, farklı aralıklara sahip veriler üzerinde eğitilen modeller arasında karşılaştırmayı etkinleştirmek üzere normalleştirmeyi de olumsuz hale getirmek Daha fazla bilgi için bkz. [ölçüm normalleştirmesi](#metric-normalization).  

Aşağıdaki tabloda, regresyon ve tahmin denemeleri için oluşturulan model performans ölçümleri özetlenmektedir. Sınıflandırma ölçümleri gibi, bu ölçümler de scıkıt öğrenme uygulamalarına dayalıdır. Uygun scikit öğreni belgeleri **Hesaplama** alanına göre bağlı olarak bağlanır.

|Metric|Açıklama|Hesaplama|
--|--|--|
explained_variance|Açıklanamayan Varyans, hedef değişkende varyasyon için model hesaplarının kapsamını ölçer. Bu, başlangıçtaki verilerin farkının, hataların farkının yüzdesidir. Hataların ortalaması 0 olduğunda, bu değer belirlemenin katsayısına eşittir (aşağıdaki r2_score bakın). <br> <br> **Amaç:** 1 ' e yaklaşarak daha iyi <br> **Aralık:** (-inf, 1]|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Mutlak ortalama hatası, hedef ve tahmin arasındaki mutlak fark değerinin beklenen değeridir.<br><br> **Amaç:** 0 ' a yaklaşarak daha iyi <br> **Aralık:** [0, INF) <br><br> Türü <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`mean_absolute_error, verilerin aralığına göre bölünür. | [Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|Ortalama mutlak yüzde hatası (MAPE), tahmin edilen bir değer ve gerçek değer arasındaki ortalama farkın bir ölçümüdür.<br><br> **Amaç:** 0 ' a yaklaşarak daha iyi <br> **Aralık:** [0, INF) ||
median_absolute_error|Ortanca mutlak hatası, hedef ve tahmin arasındaki tüm mutlak farklılıkların ortancası. Bu kayıp, aykırı değerler için sağlam.<br><br> **Amaç:** 0 ' a yaklaşarak daha iyi <br> **Aralık:** [0, INF)<br><br>Türü <br> `median_absolute_error`<br> `normalized_median_absolute_error`: median_absolute_error, verilerin aralığına göre bölünür. |[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R<sup>2</sup> (belirleme katsayısı), gözlemlenen verilerin toplam varyansı ile ilişkili ortalama kare IÇINDE (MSE) orantılı azaltı ölçer. <br> <br> **Amaç:** 1 ' e yaklaşarak daha iyi <br> **Aralık:** [-1, 1]<br><br>Note: R<sup>2</sup> genellikle aralığa sahiptir (-inf, 1]. MSE, gözlenen varyans değerinden daha büyük olabilir, bu nedenle R<sup>2</sup> verilere ve model tahminlere bağlı olarak rastgele büyük negatif değerlere sahip olabilir. Otomatik ML klipleri-1 ' de R<sup>2</sup> puanlarını raporladı, bu nedenle r<sup>2</sup> için-1 değeri büyük olasılıkla 1 ' den az olan gerçek r<sup>2</sup> puanına neden olur. Negatif bir R<sup>2</sup> puanı yorumlarken diğer ölçüm değerlerini ve verilerin özelliklerini göz önünde bulundurun.|[Hesaplama](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |Kök ortalama kare hatası (RMKEN), hedef ve tahmin arasındaki beklenen kare farkının kare köküdür. Taraflı olmayan bir Estimator için, RMSE standart sapmayla eşittir.<br> <br> **Amaç:** 0 ' a yaklaşarak daha iyi <br> **Aralık:** [0, INF)<br><br>Türü<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: root_mean_squared_error, verilerin aralığına göre bölünür. |[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|Kök ortalama kare günlüğü hatası, beklenen kare içinde Logaritmik hatanın kare köküdür.<br><br>**Amaç:** 0 ' a yaklaşarak daha iyi <br> **Aralık:** [0, INF) <br> <br>Türü <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: root_mean_squared_log_error, verilerin aralığına göre bölünür.  |[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| Spearman bağıntısı, iki veri kümesi arasındaki ilişkinin monoton olmayan bir ölçüdür. Pearson bağıntı aksine, Spearman bağıntısı, her iki veri kümesinin de normalde dağıtıldığını varsaymaz. Diğer bağıntı katkatkatlara benzer şekilde, Spearman 0 ile 1 arasında farklılık gösterir. -1 veya 1 ' in correlations, tam monoton ilişkisini kapsıyor. <br><br> Spearman, tahmin edilen veya gerçek değerlerin derece sırasını değiştirmeseler, öngörülen veya gerçek değerlerde yapılan değişikliklerin Spearman sonucunu değiştirmeyeceği anlamına gelen bir sıralama düzeni bağıntı ölçümdür.<br> <br> **Amaç:** 1 ' e yaklaşarak daha iyi <br> **Aralık:** [-1, 1]|[Hesaplama](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Ölçüm normalleştirme

Otomatikleştirilmiş ML, farklı aralıklara sahip veriler üzerinde eğitilen modeller arasında karşılaştırmaya izin veren gerileme ve tahmin ölçümlerini normalleştirir. Daha büyük bir aralıktaki veriler üzerinde eğitilen bir modelin, bu hata normalleştirilmediği takdirde daha küçük bir aralığa sahip veriler üzerinde eğitilen aynı modelden daha yüksek bir hata vardır.

Hata ölçümlerinin normalleştirilmesi için standart bir yöntem olmasa da, otomatikleştirilmiş ML hatayı verilerin aralığına göre bölmek için ortak yaklaşımı alır: `normalized_error = error / (y_max - y_min)`

Zaman serisi verilerinde bir tahmin modeli değerlendirilirken otomatik ML, her zaman serisinin farklı bir hedef değer dağıtımına sahip olması nedeniyle, otomatik olarak normalleştirmenin zaman serisi KIMLIĞI (Grey) başına gerçekleşmesini sağlamak için ek adımlar gerçekleştirir.
## <a name="residuals"></a>Fazlalıklar

Kalanlar grafiği, gerileme ve tahmin denemeleri için üretilen tahmin hatalarının (resduals) bir histogramı. Kalanlar, `y_predicted - y_true` tüm örnekler için olarak hesaplanarak model sapmanı göstermek için bir histogram olarak görüntülenir.

Bu örnekte her iki modelin de gerçek değerden daha düşük tahmin edilmesi için biraz taraflı olduğunu unutmayın. Bu, gerçek hedeflerin asimetrik bir dağıtımına sahip bir veri kümesi için sık görülen bir durumdur, ancak daha kötü model performansını gösterir. İyi bir modelde, aşırı uç 'de birkaç fazlalıklar ile en az bir artış olacak şekilde sıfır olan bir fazlalıklar dağıtımına sahip olacaktır. Daha kötü bir modelde, sıfır etrafında daha az örnek içeren bir yayma dağıtımı bulunur.

### <a name="residuals-chart-for-a-good-model"></a>İyi bir model için grafik grafiği
![İyi bir model için grafik grafiği](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Hatalı bir model için resduals grafiği
![Hatalı bir model için resduals grafiği](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Tahmin edilen ve true

Gerileme ve tahmin denemesi için tahmin edilen ve gerçek grafik, hedef Özellik (doğru/gerçek değerler) ve modelin tahminleri arasındaki ilişkiyi çizer. Gerçek değerler x ekseni boyunca ve her bir bölme için, ortalama tahmin edilen değer hata çubuklarıyla çizilir. Bu, bir modelin belirli değerleri tahmine karşı aşağı doğru olup olmadığını görmenizi sağlar. Satırda ortalama tahmin görüntülenir ve gölgeli alan, bu ortalarda öngörülen tahmine ilişkin varyansı gösterir.

Genellikle en sık kullanılan gerçek değer en düşük varyans en doğru tahminlere sahip olacaktır. Eğilim çizgisinin, en `y = x` az sayıda doğru değerin bulunduğu ideal satırdan uzaklığı, model performansının mantıksal olarak iyi bir ölçümüdür. Asıl veri dağıtımı hakkında nedenlerle grafiğin alt kısmındaki histogramı kullanabilirsiniz. Dağıtımın seyrek olduğu daha fazla veri örneği dahil, görünmeyen verilerde model performansını iyileştirebilir.

Bu örnekte, daha iyi bir modelin ideal satıra daha yakın olan tahmini ve gerçek bir satırı olduğunu unutmayın `y = x` .

### <a name="predicted-vs-true-chart-for-a-good-model"></a>İyi bir model için öngörülen ve gerçek grafik
![İyi bir model için öngörülen ve gerçek grafik](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Hatalı bir model için öngörülen ve gerçek grafik
![Hatalı bir model için öngörülen ve gerçek grafik](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>Model açıklamaları ve özellik kaynakları

Model değerlendirme ölçümleri ve grafikleri, bir modelin genel kalitesini ölçmek için uygun olmakla çalışırken, sorumlu AI oluştururken tahmine dayalı hale getirmek için kullanılan bir modelin hangi veri kümesini temel alan olduğunu inceleyerek. Bu nedenle otomatikleştirilen ML, veri kümesi özelliklerinin göreli katlarını ölçmek ve raporlamak için bir model açıklamaları panosu sağlar. Bkz. [Azure Machine Learning Studio 'da açıklamalar panosunu görüntüleme](how-to-use-automated-ml-for-ml-models.md#model-explanations-preview).

Kod ilk deneyimi için, bkz. [Azure Machine Learning Python SDK ile OTOMATIKLEŞTIRILMIŞ ml denemeleri için model açıklamalarını](how-to-machine-learning-interpretability-automl.md)ayarlama.

> [!NOTE]
> Forekısıtcn modeli şu anda otomatik ML açıklamaları tarafından desteklenmiyor ve diğer tahmin modelleri, yorumlu araçlara sınırlı erişime sahip olabilir.

## <a name="next-steps"></a>Sonraki adımlar
* [Otomatik makine öğrenme modeli açıklama örnek not defterlerini](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)deneyin.
* Otomatik ML 'ye özgü sorular için ' e ulaşın askautomatedml@microsoft.com .
