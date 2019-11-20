---
title: Zaman serisi tahmin modelini otomatik eğitme
titleSuffix: Azure Machine Learning
description: Otomatik makine öğrenimi kullanarak zaman serisi tahmin regresyon modelini eğitmek için Azure Machine Learning nasıl kullanacağınızı öğrenin.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 276e741a9462c19a3cba9ad1f9ac44e2da7ef1d3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580702"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Zaman serisi tahmin modelini otomatik eğitme
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, Azure Machine Learning ' de otomatik makine öğrenimi kullanarak zaman serisi tahmin regresyon modelini eğitme hakkında bilgi edineceksiniz. Tahmin modelinin yapılandırılması, otomatik makine öğrenimi kullanılarak standart regresyon modeli ayarlamaya benzer, ancak zaman serisi verileriyle çalışmaya yönelik bazı yapılandırma seçenekleri ve ön işleme adımları mevcuttur. Aşağıdaki örneklerde nasıl yapılacağı gösterilmektedir:

* Zaman serisi modelleme için verileri hazırlama
* [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) nesnesinde belirli zaman serisi parametrelerini yapılandırma
* Zaman serisi verileriyle tahminleri çalıştırma

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Teknikleri ve yaklaşımları birleştirmek ve önerilen, yüksek kaliteli bir zaman serisi tahmin sağlamak için otomatik ML kullanabilirsiniz. Otomatik bir zaman serisi denemesi, çok sayıda gerileme sorunu olarak değerlendirilir. Geçen zaman serisi değerleri, gerileme için diğer tahminlerle birlikte ek boyutlar haline gelir.

Bu yaklaşım, klasik zaman serisi yöntemlerinin aksine, doğal olarak birden çok bağlamsal değişkeni ve bunların eğitim sırasında birbiriyle ilişkilerini bir başkasına dahil etme avantajına sahiptir. Gerçek dünyada tahmin uygulamalarında, birden çok etken bir tahmini etkileyebilir. Örneğin, satış tahmini yaparken, geçmiş eğilimleri etkileşimlerinin yanı sıra Döviz Kuru ve fiyat, satış sonucunu güvenle bir şekilde ister. Daha fazla avantaj, regresyon modellerindeki tüm son yeniliklerin tahmin için hemen uygulanmasını sağlar.

Daha sonra tahminin ne kadar ileri uzatılmasının (tahmin ufku) yanı sıra lags ve daha fazlasını [yapılandırabilirsiniz](#config) . Otomatikleştirilmiş ML tek bir kez öğreniyor ve genellikle dahili olarak dallanan ve tahmin Horizons tüm öğeleri için dahili olarak dallanmış bir model. Bu nedenle, model parametrelerinin tahmin edilmesi için daha fazla veri ve görünmeyen serinin Genelleştirme olasılığı vardır.

Eğitim verilerinden ayıklanan Özellikler kritik bir rol oynar. Otomatik ML, standart bir ön işleme adımları gerçekleştirir ve mevsimsel etkileri yakalamak ve tahmine dayalı doğruluğu en üst düzeye çıkarmak için ek zaman serisi Özellikler oluşturur.

## <a name="time-series-and-deep-learning-models"></a>Zaman serisi ve derin öğrenme modelleri


Otomatikleştirilen ML, kullanıcılara öneri sisteminin bir parçası olarak hem yerel zaman serisi hem de derin öğrenme modelleri sağlar. Bu öğrenenler şunları içerir:
+ Prophet
+ Otomatik ARıMA
+ Forekaletcn

Otomatikleştirilmiş ML 'nin derin öğrenimi, tek tek ve zaman serisi verilerinin tahmin edilmesini sağlar.

Derin öğrenme modelleri üç adet iç Capa sahiptir:
1. Girişler ile çıkış arasında rastgele eşlemelerden bilgi verebilir
1. Birden çok giriş ve çıkışları destekler
1. Bunlar, uzun dizilerden yayılan giriş verilerinde otomatik olarak desenler ayıklayabilir

Sağlanan daha büyük veriler, örneğin, Microsofts ' Forerogb tcn gibi derin öğrenme modelleri, elde edilen modelin puanlarını geliştirebilirler. 

Yerel zaman serisi öğrenimi de otomatik ML 'nin bir parçası olarak sağlanır. Prophet, önemli dönemsel etkileri ve geçmiş verilerin çeşitli mevsimlerine sahip zaman serisiyle en iyi şekilde çalışmaktadır. Prophet, daha hızlı, güçlü, verilerin eksik olması ve zaman serinizdeki önemli değişiklikler &. 

Oto gerileme tümleşik hareketli ortalama (ARıMA), zaman serisi tahminlerine yönelik popüler istatistiksel bir yöntemdir. Bu tahmin tekniği genellikle verilerin döngüler gibi eğilimler gösterdiği kısa süreli tahmin senaryolarında kullanılır ve bu da tahmin edilemez ve modellenebilir veya tahmin edilebilir. Otomatik-ARıMA, tutarlı ve güvenilir sonuçlar almak için verilerinizi sabit verilere dönüştürür.

## <a name="prerequisites"></a>Ön koşullar

* Azure Machine Learning çalışma alanı. Çalışma alanını oluşturmak için, bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).
* Bu makalede, bir otomatik makine öğrenimi denemesi ayarlamaya yönelik temel benzerlik varsayılmaktadır. Temel otomatik makine öğrenimi tasarım düzenlerini görmek için [öğreticiyi](tutorial-auto-train-models.md) izleyin veya [nasıl yapılır?](how-to-configure-auto-train.md)

## <a name="preparing-data"></a>Veriler hazırlanıyor

Otomatikleştirilmiş makine öğrenimi içindeki bir tahmin gerileme görev türü ve regresyon görev türü arasındaki en önemli fark, verilerinize geçerli bir zaman serisini temsil eden bir özellik dahil etmektedir. Düzenli bir zaman serisinde iyi tanımlanmış ve tutarlı bir sıklık bulunur ve sürekli bir zaman aralığında her örnek noktada bir değer vardır. `sample.csv`bir dosyanın aşağıdaki anlık görüntüsünü göz önünde bulundurun.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Bu veri kümesi, A ve B olmak üzere iki farklı mağaza içeren bir şirkete ait günlük satış verilerinin basit bir örneğidir. Ayrıca, modelin haftalık mevsimsellik algılamasını sağlayacak `week_of_year` bir özellik vardır. `day_datetime` alan, günlük sıklık ile birlikte temiz bir zaman serisini temsil eder ve `sales_quantity` alanı, tahmine dayalı olarak çalışan hedef sütundur. Verileri bir Pandas dataframe 'e okuyun, sonra `to_datetime` işlevini kullanarak zaman serisinin bir `datetime` türü olduğundan emin olun.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Bu durumda, veriler zaten `day_datetime`saat alanı ile artan düzende sıralanır. Ancak, bir deneme ayarlarken, istenen saat sütununun geçerli bir zaman serisi oluşturmak için artan sırada sıralandığına emin olun. Verilerin 1.000 kaydı içerdiğini varsayıyoruz ve eğitim ve test veri kümeleri oluşturmak için verileri bir belirleyici hale getirin. Etiket sütun adını belirleyin ve etiketi olarak ayarlayın. Bu örnekte etiket `sales_quantity`olacaktır. Sonra, `test_target` kümesini oluşturmak için `test_data` etiket alanını ayırın.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Bir modeli, gelecekteki değerleri tahmin etmek için eğitiminde, eğitiminde kullanılan tüm özelliklerin, tasarlanan ufklarınızın tahminleri çalıştırılırken kullanılabilir olmasını sağlayın. Örneğin, geçerli stok fiyatına yönelik bir özellik dahil olmak üzere bir talep tahmini oluştururken eğitim doğruluğunu büyük ölçüde artırabilirsiniz. Ancak, uzun bir ufuk ile tahmin yapmak istiyorsanız gelecekteki zaman serisi noktalarına karşılık gelen stok değerlerini doğru bir şekilde tahmin edemeyebilirsiniz ve model doğruluğu düşebilir.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Deneme yapılandırma ve çalıştırma

Tahmin görevleri için otomatik makine öğrenimi, zaman serisi verilerine özgü ön işleme ve tahmin adımlarını kullanır. Aşağıdaki ön işleme adımları yürütülür:

* Zaman serisi örnek sıklığını (ör. saatlik, günlük, haftalık) algılar ve seriyi sürekli yapmak için olmayan zaman noktaları için yeni kayıtlar oluşturun.
* Hedefte (ileri-Fill aracılığıyla) ve özellik sütunlarında (ortanca sütun değerleri kullanılarak) eksik değerler var
* Farklı seriler genelinde sabit etkileri etkinleştirmek için gren tabanlı özellikler oluşturma
* Mevsimsel desenleri öğrenirken zamana dayalı özellikler oluşturma
* Kategorik değişkenleri sayısal miktarlarla kodla

`AutoMLConfig` nesnesi, otomatik makine öğrenimi görevi için gereken ayarları ve verileri tanımlar. Regresyon sorununa benzer şekilde, görev türü, yineleme sayısı, eğitim verileri ve çapraz doğrulamaları sayısı gibi standart eğitim parametrelerini tanımlarsınız. Tahmin görevleri için, denemeyi etkileyen ayarlanması gereken ek parametreler vardır. Aşağıdaki tabloda her bir parametre ve kullanımı açıklanmaktadır.

| Larına | Açıklama | Gerekli |
|-------|-------|-------|
|`time_column_name`|Zaman serisini oluşturmak ve sıklığını göstermek için kullanılan giriş verilerinde tarih saat sütununu belirtmek için kullanılır.|✓|
|`grain_column_names`|Giriş verilerinde ayrı seri gruplarını tanımlayan ad (ler). Gren tanımlanmazsa, veri kümesinin bir adet zaman serisi olduğu varsayılır.||
|`max_horizon`|Süre serisi sıklığında, istenen maksimum tahmin ufuk kapsamını tanımlar. Birimler, eğitim verilerinizin zaman aralığına göre hesaplanır. Örneğin, aylık, haftalık, öngörülebilir bir şekilde tahmin etmelidir.|✓|
|`target_lags`|Hedef değerleri, verilerin sıklığından sonra gecikme olacak satır sayısı. Bu bir liste veya tek tamsayı olarak temsil edilir. Bağımsız değişkenler ve bağımlı değişken arasındaki ilişki, varsayılan olarak eşleşmediğinden veya ilişkilendiribir şekilde eşleşmediği zaman, gecikme kullanılmalıdır. Örneğin, bir ürün için talebi tahmin edilmeye çalışırken, herhangi bir ay içindeki talep, önceki Commodities 3 ayın fiyatına göre değişebilir. Bu örnekte, modelin doğru ilişki üzerinde eğitim olması için hedefi (istek), 3 aya kadar bir süre sonra gecikme yapmak isteyebilirsiniz.||
|`target_rolling_window_size`|tahmin edilen değerler oluşturmak için *kullanılacak geçmiş dönem* < = eğitim kümesi boyutu. Atlanırsa, *n* tam eğitim kümesi boyutudur. Modele eğitim yaparken yalnızca belirli bir geçmişi düşünmek istediğinizde bu parametreyi belirtin.||
|`enable_dnn`|Tahmini DNNs 'leri etkinleştirin.||

Daha fazla bilgi için [başvuru belgelerine](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) bakın.

Zaman serisi ayarlarını sözlük nesnesi olarak oluşturun. Veri kümesindeki `day_datetime` alanına `time_column_name` ayarlayın. Veriler için **iki ayrı zaman serisi grubunun** oluşturulduğundan emin olmak için `grain_column_names` parametresini tanımlayın; biri mağaza A ve B. son olarak, tüm test kümesinin tahmin edilmesi için `max_horizon` 50 olarak ayarlayın. `target_rolling_window_size`olan bir tahmin penceresi ayarlayın ve `target_lags` parametresiyle birlikte 2 dönemdeki hedef değerlerde tek bir gecikme süresi belirleyin.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10,
    "preprocess": True,
}
```

> [!NOTE]
> Otomatik makine öğrenimi ön işleme adımları (özellik normalleştirme, eksik verileri işleme, metni sayısal olarak dönüştürme, vb.) temel modelin bir parçası haline gelir. Tahmin için model kullanılırken, eğitim sırasında uygulanan aynı ön işleme adımları, giriş verilerinize otomatik olarak uygulanır.

Yukarıdaki kod parçacığında `grain_column_names` tanımlayarak, oto ve birden çok zaman serisi olarak da bilinen iki ayrı zaman serisi grubu oluşturulur. Gren tanımlanmazsa, oto veri kümesinin tek bir zaman serisi olduğunu varsayacaktır. Tek seferlik seriler hakkında daha fazla bilgi edinmek için bkz. [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Şimdi, `forecasting` görev türünü belirterek standart bir `AutoMLConfig` nesnesi oluşturun ve denemeyi iletin. Model bittikten sonra en iyi çalıştırma yinelemesini alın.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Gelişmiş tahmin yapılandırmasına yönelik ayrıntılı kod örnekleri için bkz. [enerji talebi Not defteri](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) , aşağıdakiler dahil:

* tatil algılama ve korleştirme
* çıkış sonrası çapraz doğrulama
* yapılandırılabilir lags
* sıralı pencere toplama özellikleri

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>DNN sağlama tahmini deneme

> [!NOTE]
> Otomatik Machine Learning tahmin için DNN desteği önizleme aşamasındadır.

Tahmin için DNNs 'ten yararlanmak üzere,, oto Mlconfig içindeki `enable_dnn` parametresini true olarak ayarlamanız gerekir. 

DNNs 'yi kullanabilmeniz için, bir AML Işlem kümesini GPU SKU 'Ları ve işlem hedefi olarak en az 2 düğüm ile kullanmanızı öneririz. Daha fazla bilgi için bkz. [AML işlem belgeleri](how-to-set-up-training-targets.md#amlcompute) . GPU 'ları içeren VM boyutları hakkında daha fazla bilgi için bkz. [GPU iyileştirilmiş sanal makine boyutları](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) .

DNN eğitiminin tamamlanmasına yetecek zaman izin vermek için deneme zaman aşımını en az birkaç saate ayarlamayı öneririz.

### <a name="view-feature-engineering-summary"></a>Özellik Mühendisliği özetini görüntüle

Otomatik makine öğreniminde zaman serisi görev türleri için, özellik Mühendisliği sürecinin ayrıntılarını görüntüleyebilirsiniz. Aşağıdaki kod, her ham özelliği aşağıdaki özniteliklerle birlikte göstermektedir:

* Ham Özellik adı
* Bu ham özellikten oluşturulan mühendislik özelliklerinin sayısı
* Tür algılandı
* Özelliğin bırakılmış olup olmadığı
* Ham özellik için özellik dönüştürmelerinin listesi

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>En iyi model ile tahmin

Test veri kümesi değerlerini tahmin etmek için en iyi model yinelemesini kullanın.

```python
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

Alternatif olarak, tahmine dayalı olarak ne zaman başlaması gerektiğine ilişkin belirtimlere izin veren `predict()`yerine `forecast()` işlevini kullanabilirsiniz. Aşağıdaki örnekte, ilk olarak `y_pred` tüm değerleri `NaN`ile değiştirirsiniz. Tahmin kaynağı, genellikle `predict()`kullanılırken olduğu gibi, bu durumda eğitim verilerinin sonunda olacaktır. Ancak, `y_pred` ikinci yarısını `NaN`ile değiştirdiyseniz, işlev sayısal değerleri ilk yarı değiştirilmemiş olarak bırakır, ancak ikinci yarısında `NaN` değerlerini tahmin edebilirsiniz. İşlevi, tahmin edilen değerleri ve hizalı özellikleri döndürür.

Ayrıca, belirli bir tarihe kadar değerleri tahmin etmek için `forecast()` işlevindeki `forecast_destination` parametresini kullanabilirsiniz.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

`actual_labels` gerçek değerleri ve `predict_labels`tahmin edilen değerler arasında RMı 'yi (kök ortalama kare hatası) hesaplayın.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_lables, predict_labels))
rmse
```

Genel model doğruluğu belirlenmediği için, en gerçekçi bir sonraki adım, bilinmeyen gelecek değerleri tahmin etmek için modeli kullanmaktır. Yalnızca test kümesiyle aynı biçimde bir veri kümesi sağlayın `test_data` ancak gelecek tarih süreleriyle, sonuçta elde edilen tahmin kümesi her bir zaman serisi adımının tahmin edilen değerlerdir. Veri kümesindeki son seri kayıtlarının 12/31/2018 için olduğunu varsayın. Bir sonraki güne ait talebi tahmin etmek için (veya tahmin etmeniz gereken çok sayıda dönem < = `max_horizon`), 01/01/2019 için her mağaza için tek bir zaman serisi kaydı oluşturun.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Bu gelecekteki verileri bir veri çerçevesine yüklemek için gerekli adımları yineleyin ve sonra gelecekteki değerleri tahmin etmek için `best_run.predict(test_data)` çalıştırın.

> [!NOTE]
> Değerler `max_horizon`büyük dönem sayısı için tahmin edilemez. Model, gelecekteki değerleri geçerli ufuk ötesinde tahmin etmek için daha büyük bir ufuk ile yeniden eğitilmiş olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

* Otomatik makine öğrenimi ile denemeleri oluşturmayı öğrenmek için [öğreticiyi](tutorial-auto-train-models.md) izleyin.
* Python başvuru belgeleri [için Azure Machine Learning SDK 'sını](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) görüntüleyin.
