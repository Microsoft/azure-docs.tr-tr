---
title: Bir model için hyperparameter ayarlama
titleSuffix: Azure Machine Learning
description: Azure Machine Learning kullanarak, derin öğrenme ve makine öğrenimi modelleri için hiper parametre ayarlamayı otomatikleştirin.
ms.author: anumamah
author: Aniththa
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ec01561c5c44c850b32187629552b1bdb99537e7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819187"
---
# <a name="hyperparameter-tuning-a-model-with-azure-machine-learning"></a>Azure Machine Learning olan bir modeli hiper parametre olarak ayarlama

Azure Machine Learning [Hyperdrive paketini](/python/api/azureml-train-core/azureml.train.hyperdrive)kullanarak verimli hiper parametre ayarlamayı otomatikleştirin. [Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/)ile hiper parametreleri ayarlamak için gereken adımları tamamlamayı öğrenin:

1. Parametre arama alanını tanımlayın
1. İyileştirmek için bir birincil ölçüm belirtin  
1. Düşük performanslı çalıştırmalar için erken sonlandırma ilkesini belirtin
1. Kaynak oluşturma ve atama
1. Tanımlı yapılandırma ile bir deneme başlatın
1. Eğitim çalıştırmalarını görselleştirin
1. Modeliniz için en iyi yapılandırmayı seçin

## <a name="what-is-hyperparameter-tuning"></a>Hyperparameter ayarlama nedir?

**Hiper parametreler** , model eğitimi sürecini denetlemenize olanak tanıyan ayarlanabilir parametrelerdir. Örneğin, sinir Networks ile gizli katmanların sayısına ve her katmandaki düğüm sayısına karar verirsiniz. Model performansı, hiper parametrelere göre büyük ölçüde farklılık gösterir.

 Hiper parametre **iyileştirmesi** olarak da bilinen hiper **parametre ayarlama**, en iyi performansa neden olan hiper parametrelerin yapılandırmasını bulma işlemidir. İşlem genellikle hesaplama açısından pahalı ve el ile yapılır.

Azure Machine Learning hiper parametreleri verimli bir şekilde iyileştirmek için hiper parametre ayarlamayı otomatikleştirmenizi ve paralel olarak denemeleri çalıştırmanızı sağlar.


## <a name="define-the-search-space"></a>Arama alanını tanımlama

Her hiper parametre için tanımlanan değer aralığını inceleyerek hiper parametreleri ayarlayın.

Hiper parametreler ayrık veya sürekli olabilir ve bir [parametre ifadesi](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions)tarafından tanımlanan değerlerin bir dağıtımına sahiptir.

### <a name="discrete-hyperparameters"></a>Ayrık hiper parametreler

Ayrık hiper parametreler, `choice` ayrık değerler arasında bir olarak belirtilir. `choice` şunları yapabilirsiniz:

* bir veya daha fazla virgülle ayrılmış değer
* bir `range` nesne
* herhangi bir rastgele `list` nesne


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Bu durumda, `batch_size` [16, 32, 64, 128] değerlerinden biri ve `number_of_hidden_layers` [1, 2, 3, 4] değerlerinden birini alır.

Aşağıdaki gelişmiş ayrık hiper parametreler de dağıtım kullanılarak belirtilebilir:

* `quniform(low, high, q)` -Round (Tekdüzen (düşük, yüksek)/q) * q gibi bir değer döndürür
* `qloguniform(low, high, q)` -Round gibi bir değer döndürür (EXP (Tekdüzen (düşük, yüksek))/q) * q
* `qnormal(mu, sigma, q)` -Round (normal (mu, Sigma)/q) * q gibi bir değer döndürür
* `qlognormal(mu, sigma, q)` -Round (EXP (normal (mu, Sigma))/q) * q gibi bir değer döndürür

### <a name="continuous-hyperparameters"></a>Sürekli hiper parametreler 

Sürekli hiper parametreler, sürekli bir değer aralığında dağıtım olarak belirtilir:

* `uniform(low, high)` -Düşük ve yüksek arasında eşit olarak dağıtılan bir değer döndürür
* `loguniform(low, high)` -Return değerinin logaritması eşit olarak dağıtılabilmesi için, exp 'ye (Tekdüzen (düşük, yüksek) göre çizilen bir değer döndürür
* `normal(mu, sigma)` -Normalde ortalama mu ve standart sapma sigma ile dağıtılan gerçek bir değer döndürür
* `lognormal(mu, sigma)` -Return değerinin logaritması normal şekilde dağıtılabilmesi için, exp (normal (mu, Sigma) değerine göre çizilen bir değer döndürür

Parametre alanı tanımına bir örnek:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Bu kod, iki parametreli arama alanını tanımlar- `learning_rate` ve `keep_probability` . `learning_rate` , ortalama değeri 10 ve standart sapması 3 olan normal bir dağıtıma sahiptir. `keep_probability` , en az 0,05 değeri ve en fazla 0,1 değeri olan bir Tekdüzen dağıtımına sahiptir.

### <a name="sampling-the-hyperparameter-space"></a>Hiper parametre alanını örnekleme

Hiper parametre alanı üzerinde kullanılacak parametre örnekleme yöntemini belirtin. Azure Machine Learning aşağıdaki yöntemleri destekler:

* Rastgele örnekleme
* Kılavuz örnekleme
* Bayes örneklemesi

#### <a name="random-sampling"></a>Rastgele örnekleme

[Rastgele örnekleme](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling) ayrık ve sürekli hiper parametreleri destekler. Düşük performanslı çalıştırmaların erken sonlandırılmasını destekler. Bazı kullanıcılar rastgele örneklemeyle bir ilk arama yapılır ve sonuçları iyileştirmek için arama alanını iyileştiriyor.

Rastgele örnekleme içinde, hiper parametre değerleri tanımlanmış arama alanından rastgele seçilir. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Kılavuz örnekleme

[Grid örneklemesi](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling) ayrık hiper parametreleri destekler. Arama alanı üzerinde göreli arama yapmak üzere bütçelerseniz, kılavuz örneklemesi kullanın. Düşük performanslı çalışmaların erken sonlandırılmasını destekler.

Izgara örnekleme, tüm olası değerler üzerinde basit bir kılavuz arar. Grid örneklemesi yalnızca hyperparameters ile kullanılabilir `choice` . Örneğin, aşağıdaki boşluk altı örneğe sahiptir:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayes örneklemesi

[Bayeme örneklemesi](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling) , bayeme iyileştirme algoritmasını temel alır. Bu, önceki örneklerin ne olduğu ile ilgili örnekler, yeni örneklerin ise birincil ölçüyü iyileştirmesine göre seçer.

Hyperparameter alanını keşfetmeye yetecek kadar bütçeniz varsa bayeme örneklemesi önerilir. En iyi sonuçları elde etmek için, ayarlanan hiper parametrelerin sayısının en fazla 20 katına eşit veya daha büyük çalışan sayısını öneririz. 

Eşzamanlı çalışan sayısı, ayarlama işleminin verimliliğini etkiler. Daha az sayıda paralellik derecesi daha önce tamamlanmış çalıştırmaların sağladığı çalıştırmaların sayısını arttığı için, daha az sayıda eşzamanlı çalışma daha iyi örnekleme yakınsamasına neden olabilir.

Bayeder örneklemesi yalnızca `choice` `uniform` `quniform` arama alanı üzerinde, ve dağıtımlarını destekler.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Birincil ölçümü belirtin

Hiperparameter ayarlamayı iyileştirmek istediğiniz [birincil ölçümü](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal) belirtin. Her eğitim çalışması, birincil ölçüm için değerlendirilir. Erken sonlandırma ilkesi, düşük performanslı çalıştırmaları belirlemek için birincil ölçümü kullanır.

Birincil ölçümünüzün aşağıdaki özniteliklerini belirtin:

* `primary_metric_name`: Birincil ölçümün adı, eğitim betiği tarafından günlüğe kaydedilen ölçümün adıyla tam olarak eşleşmesi gerekir
* `primary_metric_goal`: Ya da ya da `PrimaryMetricGoal.MAXIMIZE` , `PrimaryMetricGoal.MINIMIZE` çalıştırmalar değerlendirilirken birincil ölçümün büyütülmesini veya küçültülmeyeceğini belirler. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Bu örnek "doğruluğu" en üst düzeye çıkarır.

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Hyperparameter ayarlama için günlük ölçümleri

Modelinize yönelik eğitim betiği, HyperDrive 'ın hyperparameter ayarlaması için erişebilmesi için model eğitimi sırasında birincil ölçümü günlüğe **vermelidir** .

Eğitim betiğinizdeki birincil ölçümü aşağıdaki örnek kod parçacığıyla günlüğe kaydedin:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Eğitim betiği öğesini hesaplar `val_accuracy` ve birincil ölçüm "doğruluk" olarak kaydeder. Ölçüm her günlüğe kaydedildiğinde, hiper parametre ayarlama hizmeti tarafından alınır. Raporlama sıklığını öğrenmek sizin için önemlidir.

Model eğitimi çalışmalarından günlük değerleri hakkında daha fazla bilgi için bkz. [Azure ML eğitim çalıştırmaları 'nda günlüğü etkinleştirme](how-to-log-view-metrics.md).

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Erken sonlandırma ilkesini belirtin

Erken sonlandırma ilkesiyle kötü performanslı çalıştırmaları otomatik olarak sonlandırın. Erken sonlandırma, hesaplama verimliliğini geliştirir.

Bir ilkenin ne zaman uygulanacağını denetleyen aşağıdaki parametreleri yapılandırabilirsiniz:

* `evaluation_interval`: ilkeyi uygulama sıklığı. Eğitim betiğinin her seferinde birincil ölçüm bir Aralık olarak sayılır. `evaluation_interval`Eğitim betiği birincil ölçümü her bildirdiğinde ilkeyi 1 ' den uygular. `evaluation_interval`Her seferinde 2 ' nin bir ilkeyi uygulayacağı. Belirtilmemişse, `evaluation_interval` Varsayılan olarak 1 olarak ayarlanır.
* `delay_evaluation`: belirtilen Aralık sayısı için ilk ilke değerlendirmesini geciktirir. Bu, tüm yapılandırmaların en az sayıda Aralık için çalışmasına izin vererek eğitimin erken sonlandırmasını önleyen isteğe bağlı bir parametredir. Belirtilmişse, delay_evaluation daha büyük veya eşit olan evaluation_interval her katı ilke uygulanır.

Azure Machine Learning, aşağıdaki erken sonlandırma ilkelerini destekler:
* [Bandıt ilkesi](#bandit-policy)
* [Ortanca ilke durduruluyor](#median-stopping-policy)
* [Kesme seçim ilkesi](#truncation-selection-policy)
* [Sonlandırma ilkesi yok](#no-termination-policy-default)


### <a name="bandit-policy"></a>Bandıt ilkesi

[Bandıt ilkesi](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy#definition) , bolluk faktörünü/bolluk tutarını ve değerlendirme aralığını temel alır. Birincil ölçüm, en başarılı çalıştırmanın belirtilen bolluk faktörü/bolluk miktarı içinde olmadığında bandıt uçları çalıştırılır.

> [!NOTE]
> Bayema örneklemesi erken sonlandırmayı desteklemez. Bayeme örneklemesi kullanılırken, ayarlayın `early_termination_policy = None` .

Aşağıdaki yapılandırma parametrelerini belirtin:

* `slack_factor` ya da `slack_amount` : en iyi yapan eğitim çalıştırmasına göre izin verilen bolluk. `slack_factor` izin verilen bolluğu bir oran olarak belirtir. `slack_amount` izin verilen bolluğu, bir oran yerine mutlak bir miktar olarak belirtir.

    Örneğin, 10. aralığa uygulanan bir bandıt ilkesi düşünün. 10. aralıkta en iyi şekilde çalışan çalıştırmanın birincil ölçüyü en üst düzeye çıkarmak için bir hedef ile 0,8 olduğunu varsayalım. İlke bir `slack_factor` 0,2 belirtiyorsa, en iyi ölçümü 10 ' 0,66 dan (0.8/(1 +)) daha az olan tüm eğitim çalıştırmaları `slack_factor` sonlandırılır.
* `evaluation_interval`: (isteğe bağlı) ilkeyi uygulama sıklığı
* `delay_evaluation`: (isteğe bağlı) belirtilen Aralık sayısı için ilk ilke değerlendirmesini geciktirir


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Bu örnekte, ilk sonlandırma ilkesi, ölçümler raporlanırken, değerlendirme aralığı 5 ' den başlayarak her aralıkta uygulanır. En iyi ölçümü (1/(1 + 0,1) veya en iyi çalışan çalıştırmanın %91 ' sinden az olan her çalıştırma sonlandırılır.

### <a name="median-stopping-policy"></a>Ortanca ilke durduruluyor

[Ortanca durdurma](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy) , çalıştırmalar tarafından bildirilen birincil ölçümlerin çalışma ortalamaları temelinde erken sonlandırma ilkesidir. Bu ilke, tüm eğitim çalıştırmaları genelinde ortalama ortalamaları hesaplar ve birincil ölçüm değeri ortalamaları ortanminden daha zayıf olan çalıştırmaları durduruyor.

Bu ilke aşağıdaki yapılandırma parametrelerini alır:
* `evaluation_interval`: ilkeyi uygulama sıklığı (isteğe bağlı parametre).
* `delay_evaluation`: belirtilen Aralık sayısı için (isteğe bağlı parametre) ilk ilke değerlendirmesini geciktirir.


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Bu örnekte, erken sonlandırma ilkesi, değerlendirme aralığı 5 ' te başlayarak her aralıkta uygulanır. En iyi birincil ölçümü, tüm eğitim çalışmalarından 1:5 ' ten fazla süre boyunca çalışan ortalamaları 'nın ortanminden daha kötüse, bir çalıştırma 5 Aralık tarihinde durdurulur.

### <a name="truncation-selection-policy"></a>Kesme seçim ilkesi

[Kesme seçimi](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy) , her değerlendirme aralığında en düşük performanslı çalıştırmanın yüzdesini iptal eder. Çalıştırmalar, birincil ölçüm kullanılarak karşılaştırılır. 

Bu ilke aşağıdaki yapılandırma parametrelerini alır:

* `truncation_percentage`: her değerlendirme aralığında sonlandırmak için en düşük performanslı çalıştırmanın yüzdesi. 1 ile 99 arasında bir tamsayı değeri.
* `evaluation_interval`: (isteğe bağlı) ilkeyi uygulama sıklığı
* `delay_evaluation`: (isteğe bağlı) belirtilen Aralık sayısı için ilk ilke değerlendirmesini geciktirir


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Bu örnekte, erken sonlandırma ilkesi, değerlendirme aralığı 5 ' te başlayarak her aralıkta uygulanır. 5. aralıkta performansı, 5. aralığa ait performans %20 ' de tüm çalıştırmalar performansının en düşük %20 ' si içinde olduğunda bir çalıştırma 5. aralığa sonlanır.

### <a name="no-termination-policy-default"></a>Sonlandırma ilkesi yok (varsayılan)

Herhangi bir ilke belirtilmemişse, hyperparameter ayarlama hizmeti tüm eğitimin tamamlanmasını yürütmeye izin verir.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Erken sonlandırma ilkesi seçme

* Taahhüt işlerini sonlandırmadan tasarruf sağlayan bir koruyucu ilke için 1 ile 5 arasında bir ortanca durdurma Ilkesi düşünün `evaluation_interval` `delay_evaluation` . Bunlar, birincil ölçümde (değerlendirme Verilerimize göre) bir kayıp olmadan yaklaşık %25 %35 tasarruf sağlayabilen bir koruyucu ayarlardır.
* Daha fazla ısrarlı tasarruf için, daha büyük bir kesme yüzdesine sahip daha küçük bir izin verilen bolluk veya kesme seçim Ilkesiyle bandıt Ilkesini kullanın.

## <a name="create-and-assign-resources"></a>Kaynak oluşturma ve atama

Maksimum eğitim çalıştırması sayısını belirterek kaynak bütçenize kontrol edin.

* `max_total_runs`: En fazla eğitim çalışması sayısı. 1 ile 1000 arasında bir tamsayı olmalıdır.
* `max_duration_minutes`: (isteğe bağlı) hiper parametre ayarlama denemenizin dakika cinsinden en uzun süresi. Bu süre iptal edildikten sonra çalışır.

>[!NOTE] 
>Hem hem `max_total_runs` de `max_duration_minutes` belirtilmişse, bu iki eşikte ilk kez ulaşıldığında hyperparameter ayarlama denemesi sonlanır.

Ayrıca, hyperparameter ayarlama aramanız sırasında aynı anda çalıştırılacak maksimum eğitim çalıştırması sayısını belirtin.

* `max_concurrent_runs`: (isteğe bağlı) aynı anda çalışabilecek en fazla çalıştırma sayısı. Belirtilmezse, tüm çalıştırmalar paralel olarak başlatılır. Belirtilmişse, 1 ile 100 arasında bir tamsayı olmalıdır.

>[!NOTE] 
>Eş zamanlı çalışan sayısı, belirtilen işlem hedefinde bulunan kaynaklar üzerinde yer alır. İşlem hedefinin istenen eşzamanlılık için kullanılabilir kaynaklara sahip olduğundan emin olun.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Bu kod, tek seferde dört yapılandırmayı çalıştıran en fazla 20 toplam çalıştırma kullanmak için hyperparameter ayarlama denemesini yapılandırır.

## <a name="configure-hyperparameter-tuning-experiment"></a>Hyperparameter ayarlama denemesini yapılandırma

[Hyperparameter ayarlama denemeniz yapılandırmak](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig) için aşağıdakileri sağlayın:
* Tanımlı hiper parametre arama alanı
* Erken sonlandırma ilkeniz
* Birincil ölçüm
* Kaynak ayırma ayarları
* ScriptRunConfig `script_run_config`

ScriptRunConfig, Örneklenmiş hiper parametrelerle çalışacak eğitim betiğleridir. İş başına kaynakları (tek veya çok düğümlü) ve kullanılacak işlem hedefini tanımlar.

> [!NOTE]
>İçinde kullanılan işlem hedefinin `script_run_config` eşzamanlılık düzeyinizi karşılamak için yeterli sayıda kaynağı olmalıdır. ScriptRunConfig hakkında daha fazla bilgi için bkz. [eğitim çalıştırmalarını yapılandırma](how-to-set-up-training-targets.md).

Hyperparameter ayarlama denemenizi yapılandırın:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
from azureml.train.hyperdrive import RandomParameterSampling, BanditPolicy, uniform, PrimaryMetricGoal

param_sampling = RandomParameterSampling( {
        'learning_rate': uniform(0.0005, 0.005),
        'momentum': uniform(0.9, 0.99)
    }
)

early_termination_policy = BanditPolicy(slack_factor=0.15, evaluation_interval=1, delay_evaluation=10)

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

, `HyperDriveConfig` Öğesine geçirilen parametreleri ayarlar `ScriptRunConfig script_run_config` . `script_run_config`, Sırasıyla, eğitim betiğine parametreler geçirir. Yukarıdaki kod parçacığı, örnek Not defteri [eğimiyle, hiper parametre ayarlanandan alınır ve PyTorch ile dağıtılır](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch). Bu örnekte, `learning_rate` ve `momentum` parametreleri ayarlanır. Çalıştırmanın erken durdurulması `BanditPolicy` , birincil ölçümü `slack_factor` (bkz. [banditpolicy sınıfı başvurusu](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy)) dışında kalan bir çalıştırmayı durduran bir tarafından belirlenir. 

Örnekteki aşağıdaki kod, yapılacak değerlerin nasıl alındığını, ayrıştırılmasını ve eğitim betiğinin işlevine geçtiğini gösterir `fine_tune_model` :

```python
# from pytorch_train.py
def main():
    print("Torch version:", torch.__version__)

    # get command-line arguments
    parser = argparse.ArgumentParser()
    parser.add_argument('--num_epochs', type=int, default=25,
                        help='number of epochs to train')
    parser.add_argument('--output_dir', type=str, help='output directory')
    parser.add_argument('--learning_rate', type=float,
                        default=0.001, help='learning rate')
    parser.add_argument('--momentum', type=float, default=0.9, help='momentum')
    args = parser.parse_args()

    data_dir = download_data()
    print("data directory is: " + data_dir)
    model = fine_tune_model(args.num_epochs, data_dir,
                            args.learning_rate, args.momentum)
    os.makedirs(args.output_dir, exist_ok=True)
    torch.save(model, os.path.join(args.output_dir, 'model.pt'))
```

> [!Important]
> Her hyperparameter çalıştırması, modeli ve _tüm veri yükleyicilerini_ yeniden oluşturma dahil olmak üzere sıfırdan eğitime yeniden başlatır. Eğitim çalıştırılmadan önce mümkün olduğunca çok veri hazırlığı yapmak için bir Azure Machine Learning işlem hattı veya el ile işlem kullanarak bu maliyeti en aza indirmenize olanak sağlayabilirsiniz. 

## <a name="submit-hyperparameter-tuning-experiment"></a>Hyperparameter ayarlama denemesi gönder

Hyperparameter ayarlama yapılandırmanızı tanımladıktan sonra, denemeyi [iletin](/python/api/azureml-core/azureml.core.experiment%28class%29#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-hyperparameter-tuning-optional"></a>Isınma hiper parametre ayarlamayı başlatma (isteğe bağlı)

Modeliniz için en iyi hiper parametre değerlerini bulma işlemi yinelemeli bir işlem olabilir. Hiper parametre ayarlamayı hızlandırmak için önceki beş çalıştırmaların bilgisini yeniden kullanabilirsiniz.

Örnekleme yöntemine bağlı olarak, normal başlatma farklı şekilde işlenir:
- **Bayeduyma örnekleme**: önceki çalıştırmadan deneme sürümleri, yeni örnekler seçmek ve birincil ölçümü geliştirmek için önceden bilgi olarak kullanılır.
- **Rastgele örnekleme** veya **kılavuz örnekleme**: erken sonlandırma, kötü performanslı çalıştırmaları anlamak için önceki çalıştırmaların bilgisini kullanır. 

Sıcak başlamak istediğiniz ana çalıştırmaların listesini belirtin.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Bir hyperparameter ayarlama denemesi iptal edilirse, son denetim noktasından eğitim çalıştırmalarını sürdürebilirsiniz. Ancak, eğitim betiğinizin denetim noktası mantığını işlemesi gerekir.

Eğitim çalıştırması aynı hiper parametre yapılandırmasını kullanmalıdır ve çıktılar klasörlerini bağlamalıdır. Eğitim betiği, `resume-from` eğitim çalıştırmasının sürdürüleceği denetim noktasını veya model dosyalarını içeren bağımsız değişkeni kabul etmelidir. Aşağıdaki kod parçacığını kullanarak bireysel eğitim çalıştırmalarını sürdürebilirsiniz:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Hyperparameter ayarlama denemenizi önceki bir deneyden başlatmaya veya isteğe bağlı parametreleri ve yapılandırmaya göre bireysel eğitim çalıştırmalarını sürdürmenize olanak sağlayabilirsiniz `resume_from` `resume_child_runs` :

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-hyperparameter-tuning-runs"></a>Hyperparameter ayarlama çalıştırmalarını görselleştirin

Azure Machine Learning Studio 'da hyperparameter ayarlama çalıştırmalarını görselleştirebilir veya bir not defteri pencere öğesi kullanabilirsiniz.

### <a name="studio"></a>Studio

[Azure Machine Learning Studio](https://ml.azure.com)'da tüm hyperparameter ayarlama çalışmalarınızı görselleştirebilirsiniz. Portalda bir denemeyi görüntüleme hakkında daha fazla bilgi için bkz. [Studio 'da çalıştırma kayıtlarını görüntüleme](how-to-log-view-metrics.md#view-the-experiment-in-the-web-portal).

- **Ölçüm grafiği**: Bu görselleştirme, hiper parametre ayarlama süresince her bir Hyperdrive alt öğesi için günlüğe kaydedilen ölçümleri izler. Her satır bir alt çalışmayı temsil eder ve her bir nokta, çalışma zamanının o yinelemesinde birincil ölçüm değerini ölçer.  

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-metrics.png" alt-text="Hiper parametre ayarlama ölçümleri grafiği":::

- **Paralel koordinatlar grafiği**: Bu görselleştirme, birincil ölçüm performansı ile tekil hiper parametre değerleri arasındaki bağıntıyı gösterir. Grafik, eksenlerin hareketi aracılığıyla etkileşimlidir (eksen etiketine tıklayarak ve sürükleyin) ve tek bir eksen genelinde değerleri vurgulayarak (istenen değerlerin bir aralığını vurgulamak için tek bir eksende dikey ' e tıklayıp sürükleyin).

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates.png" alt-text="Hiper parametre ayarlama paralel koordinatlar grafiği":::

- **2 boyutlu dağılım grafiği**: Bu görselleştirme, ilişkili birincil ölçüm değeriyle birlikte her iki ayrı hiper parametre arasındaki bağıntıyı gösterir.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-2-dimensional-scatter.png" alt-text="Hyparameter ayarlama 2 boyutlu dağılım grafiği":::

- **3 boyutlu dağılım grafiği**: Bu görselleştirme, 2B ile aynıdır, ancak birincil ölçüm değeri ile üç hiper parametre boyutunun bağıntı almasına izin verir. Ayrıca, 3B alanda farklı bağıntıları görüntülemek için grafiği yeniden yönlendirmek üzere tıklayıp sürükleyebilirsiniz.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-3-dimensional-scatter.png" alt-text="Hyparameter ayarlama 3 boyutlu dağılım grafiği":::

### <a name="notebook-widget"></a>Not defteri pencere öğesi

Eğitim çalışmalarınızın ilerlemesini görselleştirmek için [Not defteri pencere öğesini](/python/api/azureml-widgets/azureml.widgets.rundetails) kullanın. Aşağıdaki kod parçacığı, bir Jupyter not defterinde tek bir yerde çalışan tüm hiperparameter ayarlamayı görselleştirir:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Bu kod, hiper parametre yapılandırmalarının her biri için eğitim çalıştırmaları hakkındaki ayrıntıları içeren bir tablo görüntüler.

:::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-table.png" alt-text="Hiper parametre ayarlama tablosu":::

Her çalıştırmaların performansını eğitim ilerledikçe da görselleştirebilirsiniz.

## <a name="find-the-best-model"></a>En iyi modeli bulun

Tüm hiper parametre ayarlama işlemi tamamlandıktan sonra, en iyi şekilde çalışan yapılandırma ve hiper parametre değerlerini belirtin:

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
* [nasıl kullanılır-azureml/ml-çerçeveleri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Sonraki adımlar
* [Deneme izleme](how-to-log-view-metrics.md)
* [Eğitilen model dağıtma](how-to-deploy-and-where.md)
