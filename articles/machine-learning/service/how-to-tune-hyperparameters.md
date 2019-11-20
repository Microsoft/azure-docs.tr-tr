---
title: Modelinize yönelik hiper parametreleri ayarlama
titleSuffix: Azure Machine Learning
description: Azure Machine Learning kullanarak, derin öğrenme/makine öğrenimi modelinize yönelik hiper parametreleri verimli bir şekilde ayarlayın. Parametre arama alanını nasıl tanımlayacağınızı, iyileştirmek için bir birincil ölçüm belirtmenizi ve kötü performanslı çalıştırmaları erken sonlandırmayı öğreneceksiniz.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: a7b0276ca41e1b9342b3602a67dea0517c60f66a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489347"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Azure Machine Learning modelinize ait hiper parametreleri ayarlama
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning kullanarak modelinize yönelik hiper parametreleri verimli bir şekilde ayarlayın.  Hiper parametre ayarlama aşağıdaki adımları içerir:

* Parametre arama alanını tanımlayın
* İyileştirmek için bir birincil ölçüm belirtin  
* Kötü performanslı çalıştırmalar için erken sonlandırma ölçütlerini belirtin
* Hiper parametre ayarlama için kaynak ayır
* Yukarıdaki yapılandırmayla bir deneme başlatın
* Eğitim çalıştırmalarını görselleştirin
* Modeliniz için en iyi gerçekleştirme yapılandırmasını seçin

## <a name="what-are-hyperparameters"></a>Hiper parametreler nelerdir?

Hiper parametreler, eğitim sürecinin kendisini yöneten bir model eğiteyi seçtiğiniz ayarlanabilir parametrelerdir. Örneğin, derin bir sinir ağını eğmek için, ağ içindeki gizli katmanların sayısına ve modele eğitim vermeden önce her katmandaki düğüm sayısına karar verirsiniz. Bu değerler genellikle eğitim sürecinde sabit kalır.

Derin öğrenme/makine öğrenimi senaryolarında model performansı, seçilen hiper parametre değerlerine göre değişir. Hiper parametre araştırması hedefi, en iyi performansa neden olan bir yapılandırma bulmak için çeşitli hiper parametre yapılandırmalarında arama sağlamaktır. Genellikle, hiper parametre araştırma süreci, arama alanının büyük olduğu ve her yapılandırmanın değerlendirmesi pahalı olduğu için el ile yapılabilir.

Azure Machine Learning, hiper parametre araştırmasını verimli bir şekilde otomatikleştirerek önemli zamandan ve kaynaklardan tasarruf etmenizi sağlar. Hiper parametre değerleri aralığını ve en fazla eğitim çalıştırması sayısını belirtirsiniz. Daha sonra sistem, farklı parametre yapılandırmaları ile birden çok eş zamanlı çalıştırması otomatik olarak başlatır ve seçtiğiniz ölçüm tarafından ölçülen en iyi performansa neden olan yapılandırmayı bulur. Kötü performanslı eğitim çalıştırmaları otomatik olarak sona erer ve işlem kaynaklarının bir kısmını azaltır. Bu kaynaklar, diğer hiper parametre yapılandırmalarının araştırıp yerine kullanılır.


## <a name="define-search-space"></a>Arama alanını tanımlama

Her hiper parametre için tanımlanan değer aralığını inceleyerek hiper parametreleri otomatik olarak ayarlayın.

### <a name="types-of-hyperparameters"></a>Hiper parametre türleri

Her hiper parametre ayrı veya sürekli olabilir ve bir [parametre ifadesi](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py)tarafından tanımlanan değerlerin bir dağıtımına sahiptir.

#### <a name="discrete-hyperparameters"></a>Ayrık hiper parametreler 

Ayrık hiper parametreler, ayrık değerler arasında bir `choice` olarak belirtilir. `choice` şu olabilir:

* bir veya daha fazla virgülle ayrılmış değer
* `range` nesnesi
* herhangi bir rastgele `list` nesnesi


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Bu durumda, `batch_size` [16, 32, 64, 128] değerlerinden birini alır ve `number_of_hidden_layers` [1, 2, 3, 4] değerlerinden birini alır.

Gelişmiş ayrık hiper parametreler, bir dağıtım kullanılarak da belirtilebilir. Aşağıdaki dağıtımlar desteklenir:

* `quniform(low, high, q)`-Round (Tekdüzen (düşük, yüksek)/q) * q gibi bir değer döndürür
* `qloguniform(low, high, q)`-Round gibi bir değer döndürür (EXP (Tekdüzen (düşük, yüksek))/q) * q
* `qnormal(mu, sigma, q)`-Round (normal (mu, Sigma)/q) * q gibi bir değer döndürür
* `qlognormal(mu, sigma, q)`-Round (EXP (normal (mu, Sigma))/q) * q gibi bir değer döndürür

#### <a name="continuous-hyperparameters"></a>Sürekli hiper parametreler 

Sürekli hiper parametreler, sürekli bir değer aralığında dağıtım olarak belirtilir. Desteklenen dağıtımlar şunları içerir:

* `uniform(low, high)`-düşük ve yüksek arasında eşit olarak dağıtılan bir değer döndürür
* `loguniform(low, high)`-dönüş değerinin logaritması eşit olarak dağıtılabilmesi için, exp (Tekdüzen (düşük, yüksek)) değerine göre çizilen bir değer döndürür
* `normal(mu, sigma)`-normalde ortalama mu ve standart sapma sigma ile dağıtılan gerçek bir değer döndürür
* `lognormal(mu, sigma)`-Return değerine göre çizilen bir değer döndürür (normal (mu, Sigma)), böylece dönüş değerinin logaritması normal şekilde dağıtılır

Parametre alanı tanımına bir örnek:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Bu kod iki parametreli bir arama alanı tanımlar-`learning_rate` ve `keep_probability`. `learning_rate`, ortalama değeri 10 ve standart sapması 3 olan normal bir dağıtıma sahiptir. `keep_probability`, en az 0,05 değeri ve en fazla 0,1 değeri olan bir Tekdüzen dağıtımına sahiptir.

### <a name="sampling-the-hyperparameter-space"></a>Hiper parametre alanını örnekleme

Ayrıca, hiper parametre alanı tanımı üzerinde kullanılacak parametre örnekleme yöntemini belirtebilirsiniz. Azure Machine Learning rastgele örnekleme, kılavuz örneklemesi ve Bayeme örneklemesini destekler.

#### <a name="picking-a-sampling-method"></a>Örnekleme yöntemi seçme

* Izgara örnekleme, hiper parametre alanınız ayrık değerler arasında seçim olarak tanımlanabilececekse ve tanımlanan arama alanındaki tüm değerler üzerinde üstün arama yapmak için yeterli bütçeniz varsa kullanılabilir. Bunlara ek olarak, bir tane, kaynakların çok sayıda azalmasını azaltacak şekilde kötü performanslı çalışmaların otomatik erken sonlandırmasını kullanabilir.
* Rastgele örnekleme, hiper parametre alanının hem ayrık hem de sürekli hiper parametreleri içermesini sağlar. Uygulamada, çoğu zaman iyi sonuçlar üretir ve ayrıca kötü performanslı çalıştırmanın otomatik erken sonlandırmasının kullanılmasına izin verir. Bazı kullanıcılar rastgele örnekleme kullanarak bir ilk arama gerçekleştirir ve ardından sonuçları iyileştirmek için arama alanını tekrarlayarak bir şekilde daraltın.
* Bayeme örneklemesi, hiper parametre değerlerini seçerken, bildirilen birincil ölçümü iyileştirmeye çalışırken önceki örneklere ait bilgilerden yararlanır. Hyperparameter alanını araştırmak için yeterli bütçeniz olduğunda bayeak örneklemesi önerilir. Bayeme örneklemeyle en iyi sonuçlar için, ayarlanan hiper parametrelerin sayısının en fazla 20 katına daha büyük veya buna eşit sayıda çalıştırma kullanmanızı öneririz. Bayeduyma örneklemenin Şu anda hiçbir erken sonlandırma ilkesini desteklemediğini unutmayın.

#### <a name="random-sampling"></a>Rastgele örnekleme

Rastgele örnekleme içinde, hiper parametre değerleri tanımlanmış arama alanından rastgele seçilir. [Rastgele örnekleme](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) , arama alanının hem ayrık hem de sürekli hiper parametreleri içermesini sağlar.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Kılavuz örnekleme

[Grid örneklemesi](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) , tanımlanan arama alanındaki tüm uygun değerler üzerinde basit bir kılavuz araması gerçekleştirir. Yalnızca, `choice`kullanılarak belirtilen ayarlama hiperparametreleri ile kullanılabilir. Örneğin, aşağıdaki alanda toplam altı örnek vardır:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayeme örneklemesi

[Bayeme örneklemesi](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) , bayeme iyileştirme algoritmasını temel alır ve bir sonraki örnek olarak hiper parametre değerlerinde akıllı seçimler yapar. Yeni örnek, bildirilen birincil ölçüyü arttıran şekilde, önceki örneklerin nasıl gerçekleştirildiğiyle ilgili örneği seçer.

Bayeak örneklemesi kullandığınızda, eşzamanlı çalışan sayısı ayarlama işleminin verimliliği üzerinde bir etkiye sahiptir. Genellikle, daha küçük paralellik derecesi daha önce tamamlanmış çalışmalardan faydalan çalışma sayısını arttığı için, daha az sayıda eşzamanlı çalışma daha iyi örnekleme yakınsamasına neden olabilir.

Bayeder örneklemesi yalnızca arama alanı üzerinde `choice`, `uniform`ve `quniform` dağıtımlarını destekler.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Bayema örneklemesi, hiçbir erken sonlandırma ilkesini desteklemez (bkz. [erken sonlandırma Ilkesi belirtme](#specify-early-termination-policy)). Bayeme parametre örneklemesi kullanılırken, `early_termination_policy = None`ayarlayın veya `early_termination_policy` parametresini bırakın.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Birincil ölçümü belirtin

Hiperparameter ayarlama denemesinin [İyileştirilmek istediğiniz birincil ölçüyü](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) belirtin. Her eğitim çalışması, birincil ölçüm için değerlendirilir. Kötü performanslı çalıştırmalar (birincil ölçümün erken sonlandırma ilkesi tarafından ayarlanan ölçütleri karşılamadığında) sonlandırılır. Birincil ölçüm adının yanı sıra, en iyi duruma getirme amacını da belirtirsiniz. birincil ölçümü en üst düzeye çıkarıp en aza indirmenize mi olursunuz.

* `primary_metric_name`: optimize edilecek birincil ölçümün adı. Birincil ölçümün adı, eğitim betiği tarafından günlüğe kaydedilen ölçüm adıyla tam olarak eşleşmelidir. Bkz. [hyperparameter ayarlama Için günlük ölçümleri](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: `PrimaryMetricGoal.MAXIMIZE` veya `PrimaryMetricGoal.MINIMIZE` ya da çalıştırmalar değerlendirilirken birincil metriğin en küçük veya en aza indirilip küçültülmeyeceğini belirler. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

"Doğruluğu" en üst düzeye çıkarmak için çalıştırmaları iyileştirin.  Bu değeri eğitim betiğinizdeki günlüğe kaydettiğinizden emin olun.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Hyperparameter ayarlama için günlük ölçümleri

Modelinize yönelik eğitim betiği, model eğitimi sırasında ilgili ölçümleri günlüğe vermelidir. Hiper parametre ayarlamayı yapılandırdığınızda, çalıştırma performansını değerlendirmek için kullanılacak birincil ölçümü belirtirsiniz. (Bkz. [iyileştirmek için bir birincil ölçüm belirtin](#specify-primary-metric-to-optimize).)  Eğitim betiğinizdeki bu ölçümü hiper parametre ayarlama işleminde kullanılabilir olacak şekilde günlüğe yazmanız gerekir.

Bu ölçüyü eğitim betiğinizdeki aşağıdaki örnek kod parçacığında günlüğe kaydedin:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Eğitim betiği `val_accuracy` hesaplar ve birincil ölçüm olarak kullanılan "doğruluk" olarak günlüğe kaydeder. Ölçüm günlüğe kaydedildiği her seferinde hiper parametre ayarlama hizmeti tarafından alınır. Bu ölçümün ne sıklıkla raporlanduğunu öğrenmek için model geliştiricisi vardır.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Erken sonlandırma ilkesini belirtin

Erken sonlandırma ilkesiyle, kötü performanslı çalıştırmaları otomatik olarak sonlandırın. Sonlandırma, kaynakların kaynaklarını azaltır ve bunun yerine diğer parametre yapılandırmalarının incelenmesi için bu kaynakları kullanır.

Erken sonlandırma ilkesi kullanırken, bir ilkenin ne zaman uygulanacağını denetleyen aşağıdaki parametreleri yapılandırabilirsiniz:

* `evaluation_interval`: ilkeyi uygulama sıklığı. Eğitim betiğinin her seferinde birincil ölçüm bir Aralık olarak sayılır. Bu nedenle, eğitim betiği birincil ölçümü raporlayan her seferinde `evaluation_interval` 1 bir ilke uygular. 2 `evaluation_interval`, her eğitim betiği birincil ölçümü raporlayan her seferinde ilkeyi uygular. Belirtilmezse, `evaluation_interval` varsayılan olarak 1 ' e ayarlanır.
* `delay_evaluation`: belirtilen Aralık sayısı için ilk ilke değerlendirmesini geciktirir. Tüm yapılandırmaların ilk minimum Aralık sayısı için çalışmasına izin veren isteğe bağlı bir parametredir ve bu, eğitimin erken sonlandırılmasını önler. Belirtilmişse, ilke delay_evaluation ' den büyük veya buna eşit olan her bir evaluation_interval için geçerlidir.

Azure Machine Learning, aşağıdaki erken sonlandırma Ilkelerini destekler.

### <a name="bandit-policy"></a>Bandıt ilkesi

[Bandıt](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) , bolluk faktörü/bolluk miktarına ve değerlendirme aralığına dayalı bir sonlandırma ilkesidir. İlke, birincil ölçümün belirtilen bolluk faktörü/bolluk miktarında değil, en iyi şekilde çalışan eğitim çalıştırmasına göre değil, tüm çalıştırmaları erken sonlandırır. Aşağıdaki yapılandırma parametrelerini alır:

* `slack_factor` veya `slack_amount`: en iyi yapan eğitim çalıştırmasına göre izin verilen bolluk. `slack_factor`, izin verilen bolluk oranını bir oran olarak belirtir. `slack_amount`, izin verilen bolluğu bir oran yerine mutlak bir miktar olarak belirtir.

    Örneğin, 10. aralığa uygulanan bir bandıt ilkesi düşünün. 10. aralıkta en iyi şekilde çalışan çalıştırmanın, birincil ölçümü en üst düzeye çıkarmak için bir hedef ile 0,8 birincil ölçüm raporladığını varsayın. İlke 0,2 `slack_factor` belirtilirse, zaman aralığı 10 ' da en iyi ölçüm 0,66 ' den (0.8/(1 +`slack_factor`)) sona erer. Bunun yerine, ilke 0,2 `slack_amount` ile belirtilirse, zaman aralığı 10 ' da en iyi ölçüm 0,6 ' den (0,8-`slack_amount`) sona erer.
* `evaluation_interval`: ilkeyi uygulama sıklığı (isteğe bağlı parametre).
* `delay_evaluation`: belirtilen Aralık sayısı için (isteğe bağlı parametre) ilk ilke değerlendirmesini geciktirir.


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Bu örnekte, ilk sonlandırma ilkesi, ölçümler raporlanırken, değerlendirme aralığı 5 ' den başlayarak her aralıkta uygulanır. En iyi ölçümü (1/(1 + 0,1) veya en iyi çalışan çalıştırmanın %91 ' sinden az olan her çalıştırma sonlandırılır.

### <a name="median-stopping-policy"></a>Ortanca ilke durduruluyor

[Ortanca durdurma](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) , çalıştırmalar tarafından bildirilen birincil ölçümlerin çalışma ortalamaları temelinde erken sonlandırma ilkesidir. Bu ilke, tüm eğitim çalıştırmaları genelinde ortalama ortalamaları hesaplar ve performansı, çalışma ortalamaları ortancası 'nin orta değerinden daha zayıf olan çalıştırmaları sonlandırır. Bu ilke aşağıdaki yapılandırma parametrelerini alır:
* `evaluation_interval`: ilkeyi uygulama sıklığı (isteğe bağlı parametre).
* `delay_evaluation`: belirtilen Aralık sayısı için (isteğe bağlı parametre) ilk ilke değerlendirmesini geciktirir.


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Bu örnekte, erken sonlandırma ilkesi, değerlendirme aralığı 5 ' te başlayarak her aralıkta uygulanır. En iyi birincil ölçümü, tüm eğitim çalışmalarından 1:5 ' ten fazla süre boyunca çalışan ortalamaları 'nın ortanminden daha kötüdür.

### <a name="truncation-selection-policy"></a>Kesme seçim ilkesi

[Kesme seçimi](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) , her değerlendirme aralığında verilen en düşük performanslı çalıştırma yüzdesini iptal eder. Çalıştırmalar, birincil ölçümde performansına göre karşılaştırılır ve en düşük X% sonlandırılır. Aşağıdaki yapılandırma parametrelerini alır:

* `truncation_percentage`: her değerlendirme aralığında sonlandırmak için en düşük performanslı çalıştırmanın yüzdesi. 1 ile 99 arasında bir tamsayı değeri belirtin.
* `evaluation_interval`: ilkeyi uygulama sıklığı (isteğe bağlı parametre).
* `delay_evaluation`: belirtilen Aralık sayısı için (isteğe bağlı parametre) ilk ilke değerlendirmesini geciktirir.


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Bu örnekte, erken sonlandırma ilkesi, değerlendirme aralığı 5 ' te başlayarak her aralıkta uygulanır. 5\. aralığa yönelik performansı 5 Aralık 5 ' teki tüm çalıştırmalar performansının en düşük %20 ' si ise, bir çalıştırma 5. aralığa sonlandırılır.

### <a name="no-termination-policy"></a>Sonlandırma ilkesi yok

Tüm eğitimin tamamlanmasını çalıştırmak istiyorsanız, ilkeyi None olarak ayarlayın. Bu, herhangi bir erken sonlandırma ilkesi uygulamamayı etkilemez.

```Python
policy=None
```

### <a name="default-policy"></a>Varsayılan ilke

Herhangi bir ilke belirtilmemişse, hyperparameter ayarlama hizmeti tüm eğitimin tamamlanmasını yürütmeye izin verir.

### <a name="picking-an-early-termination-policy"></a>Erken sonlandırma ilkesi seçme

* Taahhüt işlerini sonlandırmadan tasarruf sağlayan bir koruyucu ilke arıyorsanız, `evaluation_interval` 1 ve `delay_evaluation` 5 ile bir ortanca durdurma Ilkesi kullanabilirsiniz. Bunlar, birincil ölçümde (değerlendirme Verilerimize göre) bir kayıp olmadan yaklaşık %25 %35 tasarruf sağlayabilen bir koruyucu ayarlardır.
* Erken sonlandırmaya karşı daha fazla ısrarlı tasarruf arıyorsanız, daha büyük bir kesme yüzdesine sahip, daha sıkı (daha küçük), izin verilen bolluk veya kesme seçim Ilkesiyle bandıt Ilkesini kullanabilirsiniz.

## <a name="allocate-resources"></a>Kaynakları Ayır

Toplam eğitim çalışması sayısını belirterek, hyperparameter ayarlama denemenizin kaynak bütçesini denetleyin.  İsteğe bağlı olarak, hyperparameter ayarlama denemeniz için en uzun süreyi belirtin.

* `max_total_runs`: oluşturulacak en büyük toplam eğitim çalıştırması sayısı. Üst sınır-hiper parametre alanı sonlu ise ve daha az örnek varsa, örneğin, daha az çalıştırma olabilir. 1 ile 1000 arasında bir sayı olmalıdır.
* `max_duration_minutes`: hiper parametre ayarlama denemesinde dakika cinsinden en uzun süre. Parametresi isteğe bağlıdır ve varsa, bu süre sonunda çalışan tüm çalıştırmalar otomatik olarak iptal edilir.

>[!NOTE] 
>Hem `max_total_runs` hem de `max_duration_minutes` belirtilirse, bu iki eşikte ilk kez ulaşıldığında hyperparameter ayarlama denemesi sonlanır.

Ayrıca, hyperparameter ayarlama aramanız sırasında aynı anda çalıştırılacak maksimum eğitim çalıştırması sayısını belirtin.

* `max_concurrent_runs`: herhangi bir anda aynı anda çalıştırılacak en fazla çalıştırma sayısı. Belirtilmemişse, tüm `max_total_runs` paralel olarak başlatılır. Belirtilmişse 1 ile 100 arasında bir sayı olmalıdır.

>[!NOTE] 
>Eş zamanlı çalışan sayısı, belirtilen işlem hedefinde bulunan kaynaklar üzerinde yer alır. Bu nedenle, işlem hedefinin istenen eşzamanlılık için kullanılabilir kaynaklara sahip olduğundan emin olmanız gerekir.

Hiper parametre ayarlama için kaynak ayır:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Bu kod, tek seferde dört yapılandırmayı çalıştıran en fazla 20 toplam çalıştırma kullanmak için hyperparameter ayarlama denemesini yapılandırır.

## <a name="configure-experiment"></a>Deneme yapılandırma

Tanımlı hiper parametre arama alanını, erken sonlandırma ilkesini, birincil ölçüyü ve Yukarıdaki bölümlerden kaynak ayırmayı kullanarak [hyperparameter ayarlama denemenizi yapılandırın](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) . Ayrıca, Örneklenmiş hiper parametrelerle çağrılacak bir `estimator` sağlayın. `estimator`, çalıştırdığınız eğitim betiğini, iş başına kaynakları (tek veya birden çok GPU) ve kullanılacak işlem hedefini açıklar. Hiperparameter ayarlama denemeniz için eşzamanlılık kullanılabilir kaynakları kullandığından, `estimator` belirtilen işlem hedefinin istediğiniz eşzamanlılık için yeterli kaynaklara sahip olduğundan emin olun. (Estimators hakkında daha fazla bilgi için bkz. [modelleri eğitme](how-to-train-ml-models.md).)

Hyperparameter ayarlama denemenizi yapılandırın:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Deneme gönder

Hyperparameter ayarlama yapılandırmanızı tanımladıktan sonra [bir deneme iletin](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name`, hyperparameter ayarlama denemenize atadığınız addır ve `workspace` denemeyi oluşturmak istediğiniz çalışma alanıdır (denemeleri hakkında daha fazla bilgi Için bkz. [Azure Machine Learning nasıl çalışır?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Hyperparameter ayarlama denemenize sıcak bir başlangıç yapın (isteğe bağlı)

Genellikle, modelinize yönelik en iyi hiper parametre değerlerini bulmak yinelemeli bir işlem olabilir ve bu, önceki hiper parametre ayarlama çalıştırmalarını öğrenen birden çok ayarlama çalıştırması gerektirir. Bu önceki çalıştırmaların bilgisini yeniden kullanmak, hiper parametre ayarlama sürecini hızlandırarak model ayarlama maliyetini azaltır ve sonuçta elde edilen modelin birincil ölçümünü geliştirir. Bayema örneklemeyle bir hiper parametre ayarlama denemesini ilk kez başlatırken, önceki çalıştırmadan deneme sürümleri, birincil ölçümü geliştirmek için yeni örnekleri akıllıca seçme hakkında daha önce bilgi olarak kullanılacaktır. Ayrıca, rastgele veya kılavuz örnekleme kullanırken, tüm erken sonlandırma kararları önceki çalıştırmaların ölçümlerinden yararlanarak kötü performanslı eğitim çalıştırmalarını tespit eder. 

Azure Machine Learning, daha önce 5 ' ten fazla tamamlanmış/iptal edilen hiper parametre ayarlama üst çalıştırmasından yararlanarak hyperparameter ayarlama çalışmalarınızı sıcak bir şekilde başlatabilmeniz için izin verir. Bu kod parçacığını kullanarak, sıcak başlamak istediğiniz ana çalıştırmaların listesini belirtebilirsiniz:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Ayrıca, bir hiper parametre ayarlama denemesinin bireysel eğitiminin, bütçe kısıtlamalarından dolayı iptal edilmesinin veya diğer nedenlerden dolayı başarısız olduğu durumlar olabilir. Bu durumda, bu tür bireysel eğitim çalıştırmaları son denetim noktasından (eğitim betiğinizin kontrol noktalarını işlediğini varsayarak) sürdürülmesi mümkündür. Bireysel bir eğitim çalıştırmasının sürdürülürken aynı hiper parametre yapılandırması kullanılır ve bu çalıştırma için kullanılan çıkışlar klasörü takılamaz. Eğitim betiği, eğitim çalıştırmasının sürdürüleceği denetim noktasını veya model dosyalarını içeren `resume-from` bağımsız değişkenini kabul etmelidir. Aşağıdaki kod parçacığını kullanarak bireysel eğitim çalıştırmalarını sürdürebilirsiniz:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Hyperparameter ayarlama denemenizi önceki bir deneyden başlatmaya veya `resume_from` isteğe bağlı parametreleri kullanarak bireysel eğitim çalıştırmalarını sürdürmenize olanak tanıtıp, `resume_child_runs` yapılandırma:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          resume_from=warmstart_parents_to_resume_from, 
                          resume_child_runs=child_runs_to_resume,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Denemeyi görselleştirin

Azure Machine Learning SDK, eğitim çalışmalarınızın ilerlemesini görselleştirtiren bir [Not defteri pencere öğesi](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) sağlar. Aşağıdaki kod parçacığı, bir Jupyter not defterinde tek bir yerde çalışan tüm hiperparameter ayarlamayı görselleştirir:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Bu kod, hiper parametre yapılandırmalarının her biri için eğitim çalıştırmaları hakkındaki ayrıntıları içeren bir tablo görüntüler.

![Hiper parametre ayarlama tablosu](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Her çalıştırmaların performansını eğitim ilerledikçe da görselleştirebilirsiniz. 

![Hiper parametre ayarlama çizimi](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Ayrıca, bir paralel koordinat çizimi kullanarak ayrı ayrı hiper parametrelerin performansı ve değerleri arasındaki bağıntıyı görsel olarak belirleyebilirsiniz. 

[Hiper parametre ayarlama paralel koordinatları ![](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Azure Web portalındaki tüm hiperparameter ayarlama çalışmalarınızı da görselleştirebilirsiniz. Web portalında bir denemeyi görüntüleme hakkında daha fazla bilgi için bkz. [denemeleri Track](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>En iyi modeli bulun

Tüm hiper parametre ayarlama işlemi tamamlandıktan sonra, [en iyi şekilde çalışan yapılandırmayı](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true-) ve ilgili hiper parametre değerlerini belirtin:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Örnek Not defteri
Bu klasördeki tren-hyperparameter-* Not defterleri bölümüne bakın:
* [Nasıl yapılır kullanımı-azureml/eğitim-ayrıntılı-öğrenme](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Sonraki adımlar
* [Deneme izleme](how-to-track-experiments.md)
* [Eğitilen model dağıtma](how-to-deploy-and-where.md)
