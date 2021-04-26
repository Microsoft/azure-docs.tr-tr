---
title: Otomatikleştirilen ML nedir? AutoML
titleSuffix: Azure Machine Learning
description: Azure Machine Learning, sağladığınız parametreleri ve ölçütleri kullanarak nasıl otomatik olarak bir model oluşturacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: cartacioS
ms.author: sacartac
ms.date: 10/27/2020
ms.custom: automl
ms.openlocfilehash: 6f8f775e474b47cbfd5f3b4aca8987a009a7f6e1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874144"
---
# <a name="what-is-automated-machine-learning-automl"></a>Otomatik makine öğrenimi (Otomatikml) nedir?

Otomatik ML veya otomatik ml olarak da adlandırılan otomatik makine öğrenimi, Machine Learning modeli geliştirmenin zaman alıcı, yinelemeli görevlerini otomatikleştirme işlemidir. Veri bilimcilerinin, analistlerin ve geliştiricilerin, model kalitesini sürdürüp yüksek ölçekli, verimlilik ve üretkenlik özelliklerine sahip ML modelleri oluşturmalarına olanak tanır. Azure Machine Learning içindeki otomatik ML, Microsoft Research bölümümüzden bir [kahğuyla](https://www.microsoft.com/research/project/automl/)dayanır.

Geleneksel makine öğrenme modeli geliştirme kaynakları yoğun bir şekilde, çok sayıda modeli oluşturmak ve karşılaştırmak için önemli etki alanı bilgisi ve zaman gerektirir. Otomatik makine öğrenimi sayesinde, mükemmel kolaylıklar ve verimlilik sayesinde üretime uygun ML modellerini elde etmek için geçen süreyi hızlandıracaksınız.

## <a name="automl-in-azure-machine-learning"></a>Azure Machine Learning 'de oto ml

Azure Machine Learning otomatikleştirilmiş ML ile çalışmaya yönelik iki deneyim sunar:

* Kod ile deneyimli müşteriler için [Python SDK Azure Machine Learning](/python/api/overview/azure/ml/intro).  [Öğreticiyi kullanmaya başlayın: taksi Fares 'yi tahmin etmek için otomatik makine öğrenimini kullanın](tutorial-auto-train-models.md).

* Sınırlı/kod deneyimi müşterisi için, Studio 'da Azure Machine Learning [https://ml.azure.com](https://ml.azure.com/) .  Bu öğreticilerle çalışmaya başlayın:
    * [Öğretici: Azure Machine Learning OTOMATIK ml ile bir sınıflandırma modeli oluşturun](tutorial-first-experiment-automated-ml.md).
    *  [Öğretici: otomatik makine öğrenimi ile talep tahmini](tutorial-automated-ml-forecast.md)


## <a name="when-to-use-automl-classify-regression--forecast"></a>Oto ml ne zaman kullanılır: sınıflandırma, regresyon, & tahmin

Belirttiğiniz hedef ölçümünü kullanarak bir modeli eğitmesini ve ayarlamanıza Azure Machine Learning istediğinizde otomatik ML uygulayın. Machine Learning modeli geliştirme sürecini otomatik ML verilerinizi keşfetmenizi sunan ve her türlü sorun için bir uçtan uca makine öğrenimi ardışık düzenini belirlemek için kendi kullanıcılarını, veri bilimi uzmanlığına bakılmaksızın kullanıcıları güçler.

Sektörler genelinde veri bilimcileri, analistleri ve geliştiriciler otomatik ML 'yi kullanarak şunları yapabilir:
+ Kapsamlı programlama bilgisi olmadan ML çözümlerini uygulama
+ Zaman ve kaynakları Kaydet
+ Veri bilimi en iyi uygulamalarından yararlanın
+ Çevik sorun çözümü sağlama

### <a name="classification"></a>Sınıflandırma

Sınıflandırma, ortak bir makine öğrenimi görevidir. Sınıflandırma, modellerin eğitim verilerini kullanarak öğreniminde denetlenen bir denetlenen öğrenme türüdür ve bu dersleri yeni verilere uygulanır. Azure Machine Learning, özellikle bu görevler için derin sinir ağ metni özellikleri sınıflandırıcılarıyla ilgili özellikler sunar. [Uygulanabilirlik seçenekleri](how-to-configure-auto-features.md#featurization)hakkında daha fazla bilgi edinin. 

Sınıflandırma modellerinin ana amacı, yeni verilerin eğitim verilerinden dersleri göre hangi kategorilerin üzerine dönemeyeceğini tahmin etmeye yönelik olarak tasarlanmıştır. Ortak sınıflandırma örnekleri arasında sahtekarlık algılama, el yazısı tanıma ve nesne algılama sayılabilir. Daha fazla bilgi edinin ve [OTOMATIKLEŞTIRILMIŞ ml ile sınıflandırma modeli oluşturma konusunda](tutorial-first-experiment-automated-ml.md)bir örnek görüntüleyin.

Bu Python not defterlerinde sınıflandırma ve otomatik makine öğrenimine yönelik örneklere bakın: [sahtekarlık algılama](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [Pazarlama tahmini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)ve [haber grubu veri sınıflandırması](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b)

### <a name="regression"></a>Regresyon

Sınıflandırmaya benzer şekilde, regresyon görevleri de ortak denetimli bir öğrenme görevidir. Azure Machine Learning, [Bu görevlere özel olarak](how-to-configure-auto-features.md#featurization)özellikler sunar.

Tahmin edilen çıkış değerlerinin kategorik olduğu sınıflandırmadan farklı, regresyon modelleri bağımsız tahmine göre sayısal çıkış değerlerini tahmin eder. Gerileme ' de amaç, bir değişkenin diğerlerini nasıl etkilediğini tahmin ederek bu bağımsız tahmine dayalı değişkenler arasında ilişki kurmaya yardımcı olur. Örneğin,, gaz mesafe, güvenlik derecelendirmesi vb. gibi özelliklere göre Otomobil Fiyatları. Daha fazla bilgi edinin ve [otomatik makine öğrenimine bir gerileme](tutorial-auto-train-models.md)örneği görüntüleyin.

Bu Python Not defterlerindeki tahminler için gerileme ve otomatik makine öğrenimine yönelik örneklere bakın: [CPU performansı tahmini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Zaman serisi tahmin

Tahminleri oluşturmak, her işletmenin, gelir, envanter, satış veya müşteri talebi gibi bir parçasıdır. Teknikleri ve yaklaşımları birleştirmek ve önerilen, yüksek kaliteli bir zaman serisi tahmin sağlamak için otomatik ML kullanabilirsiniz. Bu nasıl yapılır: [zaman serisi tahmini için otomatik makine öğrenimi](how-to-auto-train-forecast.md)hakkında daha fazla bilgi edinin. 

Otomatik bir zaman serisi denemesi, çok sayıda gerileme sorunu olarak değerlendirilir. Geçen zaman serisi değerleri, gerileme için diğer tahminlerle birlikte ek boyutlar haline gelir. Bu yaklaşım, klasik zaman serisi yöntemlerinin aksine, doğal olarak birden çok bağlamsal değişkeni ve bunların eğitim sırasında birbiriyle ilişkilerini bir başkasına dahil etme avantajına sahiptir. Otomatikleştirilmiş ML tek bir kez öğreniyor ve genellikle dahili olarak dallanan ve tahmin Horizons tüm öğeleri için dahili olarak dallanmış bir model. Bu nedenle, model parametrelerinin tahmin edilmesi için daha fazla veri ve görünmeyen serinin Genelleştirme olasılığı vardır.

Gelişmiş tahmin yapılandırması şunları içerir:
* tatil algılama ve korleştirme
* zaman serisi ve DNN öğrenenler (Auto-ARıMA, Prophet, Foresertcn)
* Gruplandırma yoluyla birçok model desteklenir
* çıkış sonrası çapraz doğrulama
* yapılandırılabilir lags
* sıralı pencere toplama özellikleri


Bu Python Not defterlerindeki tahminler için gerileme ve otomatik makine öğrenimi örneklerine bakın: [Satış tahmini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), [talep tahmini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)ve içecek [üretim tahmini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automated-ml-works"></a>Otomatikleştirilmiş ML nasıl çalışacaktır?

Eğitim sırasında Azure Machine Learning, paralel olarak, sizin için farklı algoritmalar ve parametreler deneyen bir dizi işlem hattı oluşturur. Hizmet, her yinelemenin eğitim puanı olan bir model oluşturduğu Özellik seçimleri ile eşleştirilmiş ML algoritmaları üzerinden yinelenir. Puan arttıkça, modelin verileri "Sığdır" olarak kabul edilir.  Deneme içinde tanımlanan çıkış ölçütlerine ulaştıktan sonra durur. 

**Azure Machine Learning** kullanarak otomatikleştirilmiş ml eğitim denemeleri şu adımlarla tasarlayabilir ve çalıştırabilirsiniz:

1. Çözülebilmek için **ml sorununu tanımla** : sınıflandırma, tahmin veya gerileme

1. **Python SDK 'sını veya Studio web deneyimini kullanmak isteyip Istemediğinizi seçin**: [Python SDK ve Studio Web deneyimi](#parity)arasındaki eşlik hakkında bilgi edinin.

   * Sınırlı veya kod deneyimi için Azure Machine Learning Studio web deneyimini şurada deneyin: [https://ml.azure.com](https://ml.azure.com/)  
   * Python geliştiricileri için [Azure Machine Learning Python SDK 'sını](how-to-configure-auto-train.md) inceleyin 
    
1. **Etiketli eğitim verilerinin kaynak ve biçimini belirtin**: sayısal tuş takımı dizileri veya Pandas dataframe

1. [Yerel bilgisayarınız, Azure Machine Learning hesaplar, uzak VM 'ler veya Azure Databricks](how-to-set-up-training-targets.md)gibi **model eğitimi Için işlem hedefini yapılandırın**.

1. Farklı modeller üzerinde kaç tane yineleme, hiper parametre ayarları, gelişmiş ön işleme/uygun hale getirme ve en iyi modeli belirlerken hangi ölçümlerin görüneceğini belirleyen **otomatik makine öğrenimi parametrelerini yapılandırın** .  
1. **Eğitim çalıştırmasını gönder.**

1. **Sonuçları gözden geçirin** 

Aşağıdaki diyagramda bu işlem gösterilmektedir. 
![Otomatik makine öğrenimi](./media/concept-automated-ml/automl-concept-diagram2.png)


Ayrıca, çalıştırma sırasında toplanan [ölçümleri içeren](how-to-understand-automated-ml.md) günlüğe kaydedilen çalıştırma bilgilerini inceleyebilirsiniz. Eğitim çalışması, `.pkl` modeli ve veri ön işleme içeren bir Python seri hale getirilmiş nesne (dosya) oluşturur.

Model oluşturma otomatikleştirilmiş olsa da, [önemli veya ilgili özelliklerin oluşturulan modellere nasıl olduğunu da öğrenebilirsiniz](how-to-configure-auto-train.md#explain) .



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>Özellik mühendisliği

Özellik Mühendisliği, ML algoritmalarının daha iyi öğrenilmesine yardımcı olan özellikler oluşturmak için verilerin etki alanı bilgisini kullanma işlemidir. Azure Machine Learning, ölçeklendirme ve normalleştirme teknikleri Özellik mühendisliğini kolaylaştırmak için uygulanır. Toplu olarak, bu teknikler ve özellik Mühendisliği, korleştirme olarak adlandırılır.

Otomatik makine öğrenimi denemeleri için, korleştirme otomatik olarak uygulanır, ancak verilerinize göre de özelleştirilebilir. [Nelerin dahil olduğu hakkında daha fazla bilgi edinin](how-to-configure-auto-features.md#featurization).  

> [!NOTE]
> Otomatik makine öğrenimi adımları (özellik normalleştirme, eksik verileri işleme, metni sayısal olarak dönüştürme, vb.) temel modelin bir parçası haline gelir. Tahmin için model kullanılırken, eğitim sırasında uygulanan aynı özellik adımları, giriş verilerinize otomatik olarak uygulanır.

### <a name="automatic-featurization-standard"></a>Otomatik fealeştirme (Standart)

Her otomatik makine öğrenimi denemenizde, algoritmaların iyi hale getirmek için verileriniz otomatik olarak ölçeklendirilir veya normalleştirilir. Model eğitimi sırasında, her bir modele aşağıdaki ölçeklendirmeden veya normalleştirme tekniklerinden biri uygulanır. Oto ml 'nin modellerinizde [fazla sığdırma ve ıdengeli verilerin nasıl engellenmesine](concept-manage-ml-pitfalls.md) yardımcı olduğunu öğrenin.

|Ölçeklendirme &nbsp; & &nbsp; işleme| Description |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Ortalama ve ölçeklendirerek birim sapması arasındaki özellikleri standartlaştırın  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Her bir özelliği sütuna en düşük ve en yüksek düzeyde ölçeklendirerek özellikleri dönüştürür  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Her bir özelliği en yüksek mutlak değerine göre ölçeklendirin |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Bu korku, bu özellik, quantile aralığına göre |
| [KULLANıCıYA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Verileri daha düşük bir boyut alanına göre projeye yönelik tekil değer ayrıştırma kullanılarak doğrusal Boyut azaltma |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Bu transformatör, kesilen tekil değer ayrıştırma (SVD) yoluyla doğrusal Boyut azaltma gerçekleştirir. PCA 'nın aksine, bu tahmin aracı, tekil değer ayrıştırma 'yı hesaplamadan önce verileri ortalamaz ve bu da SciPy. seyrek matrisleri verimli bir şekilde çalışabilir. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Her bir örnek (yani, veri matrisinin her bir satırı) en az bir sıfır olmayan bileşen ile ölçeklendirildi, norm (L1 veya L2) bir değere eşit olacak şekilde diğer örneklerden bağımsız olarak |

### <a name="customize-featurization"></a>Özelleştirmeleri özelleştirme

Kodlama ve dönüşümler gibi ek özellik mühendisliği teknikleri de mevcuttur. 

Bu ayarı şu şekilde etkinleştirin:

+ Azure Machine Learning Studio: [Bu adımlarla](how-to-use-automated-ml-for-ml-models.md#customize-featurization) **ek yapılandırmayı görüntüle** bölümünde **Otomatik** özelliği etkinleştirin.

+ Python SDK: `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` , [oto mlconfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) nesneniz içinde belirtin. [Korleştirme etkinleştirme](how-to-configure-auto-features.md)hakkında daha fazla bilgi edinin. 

## <a name="ensemble-models"></a><a name="ensemble"></a> Ensesıme modelleri

Otomatik makine öğrenimi, varsayılan olarak etkin olan ensebölümlü modellerini destekler. Enseletirme öğrenimi, tek modeller kullanmanın aksine birden çok modeli birleştirerek makine öğrenimi sonuçlarını ve tahmine dayalı performansı geliştirir. Ensebir yineleme, çalıştırmalarınızın son yinelemeleri olarak görünür. Otomatik makine öğrenimi, modelleri birleştirmek için hem oylama hem de yığınlama yöntemini kullanır:

* **Oylama**: tahmini sınıf olasılıkların (Sınıflandırma görevleri için) veya tahmin edilen gerileme hedeflerinin ağırlıklı ortalaması temelinde tahmin eder (regresyon görevleri için).
* **Yığınlama**: yığınlama heterojen modellerini birleştirir ve bireysel modellerdeki çıktıyı temel alarak bir meta modeli ister. Geçerli varsayılan meta modeller, Sınıflandırma görevleri için LogisticRegression ve gerileme/tahmin görevleri için Elaknet.

Sıralanmış olarak kullanılacak modellerle birlikte hangi modellerin kullanılacağına karar vermek için sıralanmış ensebölümlü başlatma ile [Caruana ensebir seçim algoritması](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) kullanılır. Yüksek düzeyde, bu algoritma en iyi tek puanları içeren en fazla beş modelle ve bu modellerin kötü bir ilk olarak yeniden birleştirmek için en iyi puanın %5 ' inin içinde olduğunu doğrular. Ardından, her bir ensebir yineleme için, mevcut ensede yeni bir model eklenir ve elde edilen puan hesaplanır. Yeni bir model var olan ensebirlikte bulunan puanı iyileştirdiyse, yeni modeli dahil etmek için ensebirlikte bulunan olarak güncelleştirilir.

Otomatik makine öğreniminde varsayılan enseletirme ayarlarını değiştirme için bkz. [nasıl yapılır](how-to-configure-auto-train.md#ensemble) .

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Yerel ve uzaktan yönetilen ML işlem hedefleri ile ilgili yönergeler

Otomatik ML için Web arabirimi her zaman uzak bir [işlem hedefi](concept-compute-target.md)kullanır.  Ancak, Python SDK 'yı kullandığınızda otomatik ML eğitimi için yerel bir işlem veya uzak bir işlem hedefi seçersiniz.

* **Yerel işlem**: eğitim yerel dizüstü BILGISAYARıNıZDA veya VM ortamınızda gerçekleşir. 
* **Uzaktan işlem**: eğitim Machine Learning işlem kümelerinde oluşur.  

### <a name="choose-compute-target"></a>İşlem hedefini seçin
İşlem hedefini seçerken şu faktörleri göz önünde bulundurun:

 * **Yerel bir Işlem seçin**: senaryonuz küçük veriler ve kısa traıns (örneğin, alt çalışma başına birkaç dakika) kullanan ilk araştırmalar veya tanıtımlar hakkında ise, yerel bilgisayarınızdaki eğitim daha iyi bir seçim olabilir.  Kurulum süresi yoktur, altyapı kaynakları (bilgisayarınız veya VM) doğrudan kullanılabilir.
 * **Bir uzak ml işlem kümesi seçin**: daha büyük veri kümeleriyle eğitim yapıyorsanız, daha uzun bir süre olması gereken modeller oluştururken, uzaktan işlem çok daha iyi uçtan uca zaman performansına sahiptir çünkü bu, `AutoML` kümenin düğümlerinde paralel hale getirmek. Uzaktan bir işlem sırasında, iç altyapının başlangıç zamanı, alt çalışma başına 1,5 dakika, ek olarak VM 'Ler henüz çalışır durumda değilse küme altyapısı için de ek dakika ekler.

### <a name="pros-and-cons"></a>Profesyonelleri ve dezavantajları
Yerel ve uzak kullanımını seçerken bu uzmanları ve dezavantajları göz önünde bulundurun.

|  | Profesyonelleri (avantajlar)  |Dezavantajlara (Handicap)  |
|---------|---------|---------|---------|
|**Yerel işlem hedefi** |  <li> Ortam başlangıç zamanı yok   | <li>  Özelliklerin alt kümesi<li>  Paralel hale getirmek çalışma yapılamıyor <li> Büyük veriler için daha kötüsü. <li>Eğitim sırasında veri akışı yok <li>  DNN tabanlı bir özellik yok <li> Yalnızca Python SDK |
|**Uzak ML işlem kümeleri**|  <li> Tüm özellikler kümesi <li> Paralel hale getirmek alt çalıştırmaları <li>   Büyük veri desteği<li>  DNN tabanlı featurleştirme <li>  İsteğe bağlı işlem kümesi dinamik ölçeklenebilirliği <li> Kod yok deneyim (Web UI) Ayrıca kullanılabilir  |  <li> Küme düğümleri için başlangıç saati <li> Her alt çalıştırma için başlangıç saati    |

### <a name="feature-availability"></a>Özellik kullanılabilirliği 

 Aşağıdaki tabloda gösterildiği gibi uzak işlem kullandığınızda daha fazla özellik mevcuttur. 

| Özellik                                                    | Uzak | Yerel | 
|------------------------------------------------------------|--------|-------|
| Veri akışı (büyük veri desteği, 100 GB 'a kadar)          | ✓      |       | 
| DNN-BERT tabanlı metin korleştirme ve eğitim             | ✓      |       |
| Kullanıma hazır GPU desteği (eğitim ve çıkarım)        | ✓      |       |
| Görüntü sınıflandırma ve etiketleme desteği                  | ✓      |       |
| Tahmin için otomatik-ARıMA, Prophet ve Foreroı tcn modelleri | ✓      |       | 
| Paralel olarak birden çok çalıştırma/yineleme                       | ✓      |       |
| Oto ml Studio Web deneyimi Kullanıcı arabiriminde yorumlenebilirliği olan modeller oluşturma      | ✓      |       |
| Studio Web deneyimi Kullanıcı arabiriminde Özellik Mühendisliği özelleştirmesi| ✓      |       |
| Azure ML hyperparameter ayarlaması                             | ✓      |       |
| Azure ML ardışık düzen iş akışı desteği                         | ✓      |       |
| Çalıştırmaya devam et                                             | ✓      |       |
| Tahmin etme                                                | ✓      | ✓     |
| Not defterlerinde denemeleri oluşturma ve çalıştırma                    | ✓      | ✓     |
| Kullanıcı arabirimindeki deneyin bilgilerini ve ölçümlerini kaydetme ve görselleştirme | ✓      | ✓     |
| Veri, guardrayları                                            | ✓      | ✓     |

## <a name="many-models"></a>Birçok model 

[Birçok model Çözüm Hızlandırıcısı](https://aka.ms/many-models) (önizleme) Azure Machine Learning oluşturur ve yüzlerce veya hatta binlerce makine öğrenimi modelini eğitmek, işletmek ve yönetmek IÇIN otomatik ml 'yi kullanmanıza olanak sağlar.

Örneğin, aşağıdaki senaryolarda __her örnek için__ bir model oluşturmak, geliştirilmiş sonuçlara yol açabilir:

* Her bir mağaza için satışları tahmin etme
* Yüzlerce yağ için tahmine dayalı bakım
* Bireysel kullanıcılar için bir deneyim uyarlama.

<a name="parity"></a>

### <a name="experiment-settings"></a>Deneme ayarları 

Aşağıdaki ayarlar otomatik ML denemenizi yapılandırmanıza olanak tanır. 

| |Python SDK 'Sı|Studio Web deneyimi|
----|:----:|:----:
|**Verileri tren/doğrulama kümelerine Böl**| ✓|✓
|**ML görevlerini destekler: sınıflandırma, regresyon ve tahmin**| ✓| ✓
|**Birincil ölçüme göre iyileştirir**| ✓| ✓
|**Azure ML 'yi işlem hedefi olarak destekler** | ✓|✓
|**Tahmin ufku, hedef lags & sıralı pencereyi yapılandırma**|✓|✓
|**Çıkış ölçütünü ayarla** |✓|✓ 
|**Eşzamanlı yinelemeleri ayarla**| ✓|✓
|**Sütunları bırakma**| ✓|✓
|**Blok algoritmaları**|✓|✓
|**Çapraz doğrulama** |✓|✓
|**Azure Databricks kümelerinde eğitimi destekler**| ✓|
|**Uygulanan özellik adlarını görüntüle**|✓|
|**Korturleştirme Özeti**| ✓|
|**Tatiller için korleştirme**|✓|
|**Günlük dosyası ayrıntı düzeyleri**| ✓|

### <a name="model-settings"></a>Model ayarları

Bu ayarlar, otomatik ML denemenizin bir sonucu olarak en iyi modele uygulanabilir.

| |Python SDK 'Sı|Studio Web deneyimi|
|----|:----:|:----:|
|**En iyi model kaydı, dağıtım, explainability**| ✓|✓|
|**Oylama & yığın zenginme modellerini etkinleştirme**| ✓|✓|
|**Birincil olmayan ölçüye göre en iyi modeli göster**|✓||
|**ONNX model uyumluluğunu etkinleştir/devre dışı bırak**|✓||
|**Modeli test etme** | ✓| |

### <a name="run-control-settings"></a>Denetim ayarlarını Çalıştır

Bu ayarlar, deneme çalışmalarınızı ve onun alt çalıştırmalarını incelemenizi ve denetlemenizi sağlar. 

| |Python SDK 'Sı|Studio Web deneyimi|
|----|:----:|:----:|
|**Özet tablosunu Çalıştır**| ✓|✓|
|**Alt çalıştırmalar & çalıştırmaları iptal et**| ✓|✓|
|**Guardrayal**| ✓|✓|
|**& devam ettirmeyi Duraklat**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>Oto ml & ONNX

Azure Machine Learning, otomatik ML 'yi kullanarak bir Python modeli oluşturabilir ve ONNX biçimine dönüştürülmesini sağlayabilirsiniz. Modeller ONNX biçiminde olduktan sonra çeşitli platformlar ve cihazlarda çalıştırılabilir. [ONNX Ile ml modellerini hızlandırma](concept-onnx.md)hakkında daha fazla bilgi edinin.

[Bu Jupyter Not defteri örneğinde,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)bkz. onnx biçimine dönüştürme. [ONNX 'de hangi algoritmaların desteklendiğini](how-to-configure-auto-train.md#select-your-experiment-type)öğrenin.

ONNX çalışma zamanı, C# ' yi de destekler, bu nedenle REST uç noktalarının tanıtılmasını gerektiren veya herhangi bir ağ gecikme süresi gerekmeden C# uygulamalarınızda otomatik olarak oluşturulan modeli kullanabilirsiniz. [Ml.NET ile bir .NET uygulamasında bir oto ml onnx modeli kullanma](./how-to-use-automl-onnx-model-dotnet.md) hakkında daha fazla bilgi EDININ ve [onnx çalışma zamanı C# API 'SI ile ınnx modellerini birlikte](https://github.com/plaidml/onnxruntime/blob/plaidml/docs/CSharp_API.md)kullanın. 

## <a name="next-steps"></a>Sonraki adımlar

Her ne kadar fazla kaynak vardır. 

### <a name="tutorials-how-tos"></a>Öğreticiler/nasıl yapılır-TOS
Öğreticiler, oto ml senaryolarından uçtan uca tanıtım örnekleridir.
+ **Kod ilk deneyimi Için** [öğreticiyi izleyin: bir gerileme MODELINI Azure Machine Learning Python SDK ile otomatik olarak eğitme](tutorial-auto-train-models.md).

 + **Düşük veya kod içermeyen bir deneyim için** bkz. [öğretici: Azure Machine Learning STUDIO ile otomatik ml sınıflandırma modelleri oluşturma](tutorial-first-experiment-automated-ml.md).

Nasıl yapılır makaleleri, oto ml 'nin sunduğu işlevselliğe ek ayrıntı sağlar. Örneğin, 

+ Otomatik eğitim denemeleri için ayarları yapılandırma
    + Azure Machine Learning Studio 'da [Bu adımları kullanın](how-to-use-automated-ml-for-ml-models.md). 
    + Python SDK ile [Bu adımları kullanın](how-to-configure-auto-train.md).

+  Zaman serisi verilerini kullanarak [Bu adımlarla](how-to-auto-train-forecast.md)otomatik olarak eğitme hakkında bilgi edinin.

### <a name="jupyter-notebook-samples"></a>Jupyter Not defteri örnekleri 

[Otomatik makine öğrenimi örnekleri Için GitHub Not defteri deposundaki](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)ayrıntılı kod örneklerini ve kullanım örneklerini gözden geçirin.

### <a name="python-sdk-reference"></a>Python SDK başvurusu

SDK Tasarım desenlerinin ve sınıf belirtimlerinizin uzmanlığınızı, [oto ml sınıfı başvuru belgeleriyle](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)deden çıkarır. 

> [!Note]
> Otomatik makine öğrenimi özellikleri, [ml.net](/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power BI](/power-bi/service-machine-learning-automated) ve [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/) gibi diğer Microsoft çözümlerinde de mevcuttur
