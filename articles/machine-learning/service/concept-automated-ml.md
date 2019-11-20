---
title: Otomatikleştirilen ML/otomatik ml nedir?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning, sizin için otomatik olarak bir algoritma seçebilir ve sizin modelinize yönelik en iyi algoritmayı seçmek için sağladığınız parametreleri ve ölçütleri kullanarak size zaman kazandırmak için bir model oluşturabilirsiniz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 11/04/2019
ms.openlocfilehash: f8a83fccefe3310fe1a582ef44d72cfbef7e9469
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133078"
---
# <a name="what-is-automated-machine-learning"></a>Nedir, makine öğrenimi otomatik?

Otomatik ML olarak da adlandırılan otomatik makine öğrenimi, Machine Learning modeli geliştirmenin zaman alıcı, yinelemeli görevlerini otomatikleştirme işlemidir. Veri bilimcilerinin, analistlerin ve geliştiricilerin, model kalitesini sürdürüp yüksek ölçekli, verimlilik ve üretkenlik özelliklerine sahip ML modelleri oluşturmalarına olanak tanır. Otomatikleştirilmiş ML, [Microsoft Research](https://arxiv.org/abs/1705.05355)bölümümüzden bir kesimme dayanır.

Geleneksel makine öğrenme modeli geliştirme kaynakları yoğun bir şekilde, çok sayıda modeli oluşturmak ve karşılaştırmak için önemli etki alanı bilgisi ve zaman gerektirir. Belirttiğiniz hedef ölçümünü kullanarak bir modeli eğitmesini ve ayarlamanıza Azure Machine Learning istediğinizde otomatik ML uygulayın. Daha sonra hizmet, her yinelemenin eğitim puanı olan bir model oluşturduğu Özellik seçimleriyle eşleştirilmiş ML algoritmaları üzerinden yinelenir. Puan arttıkça, modelin verileri "Sığdır" olarak kabul edilir.

Otomatik makine öğrenimi sayesinde, mükemmel kolaylıklar ve verimlilik sayesinde üretime uygun ML modellerini elde etmek için geçen süreyi hızlandıracaksınız.

## <a name="when-to-use-automated-ml"></a>Otomatik ML ne zaman kullanılır?

Machine Learning modeli geliştirme sürecini otomatik ML verilerinizi keşfetmenizi sunan ve her türlü sorun için bir uçtan uca makine öğrenimi ardışık düzenini belirlemek için kendi kullanıcılarını, veri bilimi uzmanlığına bakılmaksızın kullanıcıları güçler.

Sektörler genelinde veri bilimcileri, analistleri ve geliştiriciler otomatik ML 'yi kullanarak şunları yapabilir:

+ Kapsamlı programlama bilgisi olmadan makine öğrenimi çözümleri uygulama
+ Zaman ve kaynakları Kaydet
+ Veri bilimi en iyi uygulamalarından yararlanın
+ Çevik sorun çözümü sağlama

Aşağıdaki tabloda, yaygın olarak kullanılan otomatik ML kullanım durumları listelenmektedir. 

Sınıflandırma| Regresyon | Zaman serisi tahmin
---|---|---
[Sahtekarlık algılama](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[CPU performansı tahmini](https://github.com/Azure/MachineLearningNotebooks/blob/37541b10714f9337dbbae721bea494272dc7d151/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb) |[Talep tahmini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
[Pazarlama tahmini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Malzeme dayanıklılığı tahmini](https://github.com/Azure/MachineLearningNotebooks/blob/37541b10714f9337dbbae721bea494272dc7d151/how-to-use-azureml/automated-machine-learning/regression-concrete-strength/auto-ml-regression-concrete-strength.ipynb)|[Satış tahmini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)

## <a name="how-automated-ml-works"></a>Otomatikleştirilmiş ML nasıl çalışacaktır?

**Azure Machine Learning**kullanarak otomatikleştirilmiş ml eğitim denemeleri şu adımlarla tasarlayabilir ve çalıştırabilirsiniz:

1. Çözülebilmek için **ml sorununu tanımla** : sınıflandırma, tahmin veya gerileme

1. **Etiketli eğitim verilerinin kaynak ve biçimini belirtin**: sayısal tuş takımı dizileri veya Pandas dataframe

1. [Yerel bilgisayarınız, Azure Machine Learning hesaplar, uzak VM 'ler veya Azure Databricks](how-to-set-up-training-targets.md)gibi **model eğitimi Için işlem hedefini yapılandırın**.  [Uzak bir kaynakta](how-to-auto-train-remote.md)otomatik eğitim hakkında bilgi edinin.

1. Farklı modeller üzerinde kaç tane yineleme, hiper parametre ayarları, gelişmiş ön işleme/uygun hale getirme ve en iyi modeli belirlerken hangi ölçümlerin görüneceğini belirleyen **otomatik makine öğrenimi parametrelerini yapılandırın** .  Otomatik eğitim denemenize yönelik ayarları [Azure Machine Learning Studio](https://ml.azure.com)'DA veya [SDK ile](how-to-configure-auto-train.md)yapılandırabilirsiniz. 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **Eğitim çalıştırmasını gönder.**

  ![Otomatik makine öğrenimi](./media/how-to-automated-ml/automl-concept-diagram2.png)

Eğitim sırasında Azure Machine Learning, farklı algoritmalar ve parametreler deneyen paralel işlem hatları için bir sayı oluşturur. Deneme içinde tanımlanan çıkış ölçütlerine ulaştıktan sonra durur.

Ayrıca, çalıştırma sırasında toplanan [ölçümleri içeren](how-to-understand-automated-ml.md) günlüğe kaydedilen çalıştırma bilgilerini inceleyebilirsiniz. Eğitim çalışması, modeli ve veri ön işleme içeren bir Python seri hale getirilmiş nesne (`.pkl` dosyası) oluşturur.

Model oluşturma otomatikleştirilmiş olsa da, [önemli veya ilgili özelliklerin oluşturulan modellere nasıl olduğunu da öğrenebilirsiniz](how-to-configure-auto-train.md#explain) .

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Ön

Her otomatik makine öğrenimi denemesinde, verileriniz varsayılan yöntemler kullanılarak ve isteğe bağlı olarak gelişmiş ön işleme aracılığıyla önceden işlenir.

> [!NOTE]
> Otomatik makine öğrenimi ön işleme adımları (özellik normalleştirme, eksik verileri işleme, metni sayısal olarak dönüştürme, vb.) temel modelin bir parçası haline gelir. Tahmin için model kullanılırken, eğitim sırasında uygulanan aynı ön işleme adımları, giriş verilerinize otomatik olarak uygulanır.

### <a name="automatic-preprocessing-standard"></a>Otomatik ön işleme (Standart)

Her otomatik makine öğrenimi denemenizde, algoritmaların iyi hale getirmek için verileriniz otomatik olarak ölçeklendirilir veya normalleştirilir.  Model eğitimi sırasında, her bir modele aşağıdaki ölçeklendirmeden veya normalleştirme tekniklerinden biri uygulanır.

|Normalleştirme&nbsp;&&nbsp;ölçeklendirme| Açıklama |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Ortalama ve ölçeklendirerek birim sapması arasındaki özellikleri standartlaştırın  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Her bir özelliği sütuna en düşük ve en yüksek düzeyde ölçeklendirerek özellikleri dönüştürür  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Her bir özelliği en yüksek mutlak değerine göre ölçeklendirin |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Bu korku, bu özellik, quantile aralığına göre |
| [KULLANıCıYA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Verileri daha düşük bir boyut alanına göre projeye yönelik tekil değer ayrıştırma kullanılarak doğrusal Boyut azaltma |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Bu transformatör, kesilen tekil değer ayrıştırma (SVD) yoluyla doğrusal Boyut azaltma gerçekleştirir. PCA 'nın aksine, bu tahmin aracı, tekil değer Ayrıştırma işlemine başlamadan önce verileri ortalamaz. Bu, SciPy. seyrek matrisleri verimli bir şekilde çalışalabileceği anlamına gelir |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Her bir örnek (yani, veri matrisinin her bir satırı) en az bir sıfır olmayan bileşen ile, normal (L1 veya L2) bir değere eşit olacak şekilde diğer örneklerden bağımsız olarak yeniden ölçeklenir |

### <a name="advanced-preprocessing-optional-featurization"></a>Gelişmiş ön işleme: isteğe bağlı korleştirme

Eksik değerler imputation, kodlama ve dönüşümler gibi ek gelişmiş ön işleme ve korleştirme de mevcuttur. [Nelerin dahil olduğu hakkında daha fazla bilgi edinin](how-to-create-portal-experiments.md#preprocess). Bu ayarı şu şekilde etkinleştirin:

+ Azure Machine Learning Studio: **yapılandırma çalıştırması** bölümünde [Bu adımlarla](how-to-create-portal-experiments.md) **Görünüm özellik ayarlarını** seçme.

+ Python SDK: [`AutoMLConfig` sınıfı](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)Için `"feauturization": auto' / 'off' / FeaturizationConfig` belirtme.


## <a name="time-series-forecasting"></a>Zaman serisi tahmin etme
Tahminleri oluşturmak, her işletmenin, gelir, envanter, satış veya müşteri talebi gibi bir parçasıdır. Teknikleri ve yaklaşımları birleştirmek ve önerilen, yüksek kaliteli bir zaman serisi tahmin sağlamak için otomatik ML kullanabilirsiniz.

Otomatik bir zaman serisi denemesi, çok sayıda gerileme sorunu olarak değerlendirilir. Geçen zaman serisi değerleri, gerileme için diğer tahminlerle birlikte ek boyutlar haline gelir. Bu yaklaşım, klasik zaman serisi yöntemlerinin aksine, doğal olarak birden çok bağlamsal değişkeni ve bunların eğitim sırasında birbiriyle ilişkilerini bir başkasına dahil etme avantajına sahiptir. Otomatikleştirilmiş ML tek bir kez öğreniyor ve genellikle dahili olarak dallanan ve tahmin Horizons tüm öğeleri için dahili olarak dallanmış bir model. Bu nedenle, model parametrelerinin tahmin edilmesi için daha fazla veri ve görünmeyen serinin Genelleştirme olasılığı vardır.

Daha fazla bilgi edinin ve [zaman serisi tahmini için otomatik makine öğrenimine](how-to-auto-train-forecast.md)bir örnek görüntüleyin. Veya aşağıdakiler de dahil olmak üzere gelişmiş tahmin yapılandırmasına yönelik ayrıntılı kod örnekleri için [enerji talebi not defterine](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) bakın:

* tatil algılama ve korleştirme
* zaman serisi ve DNN öğrenenler (Auto-ARıMA, Prophet, Foresertcn)
* Gruplandırma yoluyla birçok model desteği
* çıkış sonrası çapraz doğrulama
* yapılandırılabilir lags
* sıralı pencere toplama özellikleri

## <a name="ensemble"></a>Ensesıme modelleri

Otomatik makine öğrenimi, varsayılan olarak etkin olan ensebölümlü modellerini destekler. Enseletirme öğrenimi, tek modeller kullanmanın aksine birden çok modeli birleştirerek makine öğrenimi sonuçlarını ve tahmine dayalı performansı geliştirir. Ensebir yineleme, çalıştırmalarınızın son yinelemeleri olarak görünür. Otomatik makine öğrenimi, modelleri birleştirmek için hem oylama hem de yığınlama yöntemini kullanır:

* **Oylama**: tahmini sınıf olasılıkların (Sınıflandırma görevleri için) veya tahmin edilen gerileme hedeflerinin ağırlıklı ortalaması temelinde tahmin eder (regresyon görevleri için).
* **Yığınlama**: yığınlama heterojen modellerini birleştirir ve bireysel modellerdeki çıktıyı temel alarak bir meta modeli ister. Geçerli varsayılan meta modeller, Sınıflandırma görevleri için LogisticRegression ve gerileme/tahmin görevleri için Elaknet.

Sıralanmış olarak kullanılacak modellerle birlikte hangi modellerin kullanılacağına karar vermek için sıralanmış ensebölümlü başlatma ile [Caruana ensebir seçim algoritması](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) kullanılır. Yüksek düzeyde, bu algoritma en iyi tek puanları içeren 5 modelle en iyi şekilde, bu modellerin kötü bir ilk olarak yeniden birleştirmek için en iyi puanın %5 ' inin içinde olduğunu doğrular. Ardından, her bir ensebir yineleme için, mevcut ensede yeni bir model eklenir ve elde edilen puan hesaplanır. Yeni bir model var olan ensebirlikte bulunan puanı iyileştirdiyse, yeni modeli dahil etmek için ensebirlikte bulunan olarak güncelleştirilir.

Otomatik makine öğreniminde varsayılan enseletirme ayarlarını değiştirme için bkz. [nasıl yapılır](how-to-configure-auto-train.md#ensemble) .

## <a name="imbalance"></a>İmdendengelenmiş veriler

Makine öğrenimi sınıflandırma senaryolarına ilişkin verilerde yaygın olarak imledengeli veriler bulunur ve her sınıfta orantısız oranını içeren verilere başvurur. Bu dengesizlik, bir modelin doğruluğu üzerinde sapma yaptığından, bu durum, eğitim modelinin bu farkı taklit etmek için eğitilmesine neden olacak 

Machine Learning iş akışını basitleştirmenin bir parçası olarak, otomatik ML, gibi imdengelenmiş verilerle başa çıkmanıza yardımcı olmak için yerleşik yeteneklere sahiptir 

- **Ağırlık sütunu**: otomatik ml, ağırlıklı bir sütunu giriş olarak destekler, bu da verileri daha fazla veya daha az "önemli" hale getirmek için verilerin ağırlıklı veya aşağı düşmesine neden olabilir. Bu [Not defteri örneğine](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/sample-weight/auto-ml-sample-weight.ipynb) bakın 

- Otomatik ML tarafından kullanılan algoritmalar, 20:1 'e kadar dengesizliği doğru şekilde işleyebilir, yani en yaygın sınıf, verilerde en az ortak sınıftan 20 kat daha fazla satıra sahip olabilir.

### <a name="identify-models-with-imbalanced-data"></a>İmdengelenmiş verilerle modelleri tanımla

Sınıflandırma algoritmaları genellikle doğrulukla değerlendirildiğinden, bir modelin doğruluk puanı kontrol edilirken, imledengelenmiş verilerden etkilenip etkilenmediğini belirlemek için iyi bir yoldur. Belirli sınıflar için gerçekten yüksek doğruluk veya gerçekten düşük doğruluk mı var?

Ayrıca, otomatik ML çalıştırmaları aşağıdaki grafikleri otomatik olarak oluşturur. Bu, modelinizdeki sınıflandırmaların doğruluğunu anlamanıza yardımcı olabilir ve imdenli verilerden etkilenen modelleri tanımlayabilir.

Grafik| Açıklama
---|---
[Karışıklık matrisi](how-to-understand-automated-ml.md#confusion-matrix)| Doğru sınıflandırılan etiketleri verilerin gerçek etiketlerine göre değerlendirir. 
[Precision-geri çek](how-to-understand-automated-ml.md#precision-recall-chart)| Doğru etiketlerin oranını, verilerin bulunan etiket örneklerinin oranına göre değerlendirir 
[ROC eğrileri](how-to-understand-automated-ml.md#roc)| Doğru etiketlerin oranını, yanlış pozitif etiketlerin oranına göre değerlendirir.

### <a name="handle-imbalanced-data"></a>İmdengeli verileri işle 

Aşağıdaki teknikler, otomatik ML dışında imdengeli verileri işlemek için ek seçeneklerdir. 

- Daha küçük sınıfları örnekleyerek veya daha büyük sınıfları aşağı örnekleyerek sınıf dengesizliği olarak yeniden örnekleme yapın. Bu yöntemler, işlemek ve analiz etmek için uzmanlık gerektirir.

- İmdengeli verilerle daha iyi anlaşmalar sağlayan bir performans ölçümü kullanın. Örneğin, F1 puanı ağırlıklı duyarlık ve geri çağırma ortasıdır. Duyarlık, bir sınıflandırıcının exactness--düşük--, duyarlık değeri, bir sınıflandırıcının bir sınıflandırıcısını ölçer, ancak en düşük düzeyde geri çağırma, çok sayıda yanlış negatiflik olduğunu gösterir. 

## <a name="use-with-onnx-in-c-apps"></a>C# Uygulamalarda onnx ile kullanma

Azure Machine Learning, otomatik ML 'yi kullanarak bir Python modeli oluşturabilir ve ONNX biçimine dönüştürülmesini sağlayabilirsiniz. ONNX çalışma zamanı ' C#nı destekler, böylece REST uç noktalarının tanıtılmasını gerektiren veya C# herhangi bir ağ gecikme süresi gerekmeden uygulamalarınızda otomatik olarak oluşturulan modeli kullanabilirsiniz. Bu [Jupyter not defterinde](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb)bu akışa bir örnek deneyin.

## <a name="automated-ml-across-microsoft"></a>Microsoft genelinde otomatik ML

Otomatikleştirilmiş ML, gibi diğer Microsoft çözümlerinde de kullanılabilir:

|Tümleştirmeler|Açıklama|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Visual Studio ve ML.NET otomatik ML (Önizleme) ile Visual Studio Code .NET uygulamalarında otomatik model seçme ve eğitim.|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|HDInsight kümelerinde Spark üzerinde otomatik ML eğitim işlerinizi paralel olarak ölçeklendirin.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Makine öğrenimi modellerini doğrudan Power BI (Önizleme) içinde çağırın.|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|SQL Server 2019 büyük veri kümelerinde verileriniz üzerinde yeni makine öğrenimi modelleri oluşturun.|

## <a name="next-steps"></a>Sonraki adımlar

Bkz. örnekler ve otomatik makine öğrenimi kullanarak modeller oluşturmayı öğrenme:

+ [Öğreticiyi izleyin: Azure otomatik Machine Learning regresyon modelini otomatik olarak eğitme](tutorial-auto-train-models.md)

+ Otomatik eğitim denemenize yönelik ayarları yapılandırın:
  + Azure Machine Learning Studio 'da [Bu adımları kullanın](how-to-create-portal-experiments.md).
  + Python SDK ile [Bu adımları kullanın](how-to-configure-auto-train.md).

+ Zaman serisi verilerini kullanarak otomatik eğitme yapmayı öğrenin, [Bu adımları kullanın](how-to-auto-train-forecast.md).

+ [Otomatik makine öğrenimi için Jupyter Notebook örnekleri](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/) deneyin
