---
title: "Öğretici: Python 'da ilk Azure ML modelinizi eğitme"
titleSuffix: Azure Machine Learning
description: Bu öğreticide Azure Machine Learning temel tasarım düzenlerini öğrenir ve diabetes veri kümesine bağlı olarak basit bir scikit-öğrenme modeli eğitirsiniz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 11/04/2019
ms.openlocfilehash: 11eaafec750b2ca35933b431866def51c25e8961
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582467"
---
# <a name="tutorial-train-your-first-ml-model"></a>Öğretici: ilk ML modelinizi eğitme

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu öğretici, **iki bölümden oluşan bir öğretici serisinin ikinci bölümüdür**. Önceki öğreticide [bir çalışma alanı oluşturdunuz ve bir geliştirme ortamı seçtiniz](tutorial-1st-experiment-sdk-setup.md). Bu öğreticide Azure Machine Learning temel tasarım düzenlerini öğrenir ve diabetes veri kümesine bağlı olarak basit bir scikit-öğrenme modeli eğitirsiniz. Bu Öğreticiyi tamamladıktan sonra, daha karmaşık denemeleri ve iş akışları geliştirmek üzere ölçeklenebilmeniz için SDK 'nın pratik bilgisine sahip olursunuz.

Bu öğreticide, aşağıdaki görevleri öğreneceksiniz:

> [!div class="checklist"]
> * Çalışma alanınızı bağlayın ve bir deneme oluşturun
> * Veri yükleme ve scikit 'i eğitme-modelleri öğrenme
> * Portalda Eğitim sonuçlarını görüntüleme
> * En iyi modeli alma

## <a name="prerequisites"></a>Ön koşullar

Tek önkoşul, bu öğreticiden birini, [kurulum ortamını ve çalışma alanını](tutorial-1st-experiment-sdk-setup.md)çalıştırmanın bir parçasıdır.

Öğreticinin bu kısmında, örnek Jupyter Not defteri ' nde kodu birinci bölüm sonunda açılan `tutorials/tutorial-1st-experiment-sdk-train.ipynb` çalıştırırsınız. Bu makalede, not defterindeki aynı koda adım adım yol gösterilir.

## <a name="open-the-notebook"></a>Not defterini açın

1. [Azure Machine Learning Studio](https://ml.azure.com/)'da oturum açın.

1. [Bir kısım](tutorial-1st-experiment-sdk-setup.md#open)içinde gösterildiği gibi, klasörünüzde **-1. deneme-SDK-tren. ipynb öğreticisini** açın.


> [!Warning]
> Jupyter arabiriminde *Yeni* bir Not **defteri oluşturmayın!** Not defteri `tutorials/tutorial-1st-experiment-sdk-train.ipynb`, bu öğretici için **gereken tüm kod ve verileri** dahil değildir.

## <a name="connect-workspace-and-create-experiment"></a>Çalışma alanını bağlama ve deneme oluşturma

> [!Important]
> Bu makalenin geri kalanında not defterinde gördüğünüz içerikle aynı içerik yer almaktadır.  
>
> Kodu çalıştırırken okumak istiyorsanız, Jupyter not defterine şimdi geçin. 
> Bir not defterinde tek bir kod hücresini çalıştırmak için, kod hücresine tıklayın ve **SHIFT + enter**tuşuna basın. Ya da tüm not defteri ' ni üstteki araç çubuğundan **Çalıştır** ' ı seçerek çalıştırın.

`Workspace` sınıfını içeri aktarın ve bu işlevi kullanarak abonelik bilgilerinizi dosya `config.json` yükleyin `from_config().` bu, varsayılan olarak geçerli dizindeki JSON dosyasını arar, ancak `from_config(path="your/file/path")`kullanarak dosyayı işaret etmek için bir yol parametresi de belirtebilirsiniz. Bir bulut Not defteri sunucusunda, dosya otomatik olarak kök dizinde bulunur.

Aşağıdaki kod ek kimlik doğrulaması isterse, bağlantıyı bir tarayıcıya yapıştırmanız ve kimlik doğrulama belirtecini girmeniz yeterlidir.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Şimdi çalışma alanınızda bir deneme oluşturun. Deneme, deneme koleksiyonunu temsil eden başka bir temel bulut kaynağıdır (bireysel model çalıştırmaları). Bu öğreticide, çalıştırma oluşturmak ve Azure Machine Learning Studio 'daki model öğreticinizi izlemek için denemeyi kullanırsınız. Parametreler, çalışma alanı başvurunuz ve deneme için bir dize adı içerir.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Veri yükleme ve eğitim için hazırlanma

Bu öğreticide, diabetes ilerlemesini tahmin etmek için Age, cinsiyet ve BMI gibi özellikleri kullanan diabetes veri kümesini kullanırsınız. [Azure Open DataSet](https://azure.microsoft.com/services/open-datasets/) sınıfından verileri yükleyin ve `train_test_split()`kullanarak eğitim ve test kümelerine ayırın. Bu işlev, modeli aşağıdaki eğitimin test edilmesi için kullanılacak görülmeyen verileri içerecek şekilde ayırır.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Modeli eğitme

Basit bir scikit-eğitim modeli, küçük ölçekli eğitim için kolayca yerel olarak yapılabilir, ancak onlarca farklı özellik bilimiyle ve hiper parametre ayarları ile birçok yinelemeye eğitim yaparken, eğitilen modelleri ve bunları nasıl kullanabileceğinizi izlemek kolaydır. bunları eğitimli. Aşağıdaki tasarım modelinde, bulutta öğreticinizi kolayca izlemek için SDK 'nın nasıl kullanılacağı gösterilmektedir.

Farklı hiper parametre alfa değerleri aracılığıyla bir döngüdeki modelleri gösteren bir betik oluşturun.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

Yukarıdaki kod şunları gerçekleştirir:

1. `alphas` dizisindeki her Alfa hiper parametre değeri için, deneme içinde yeni bir çalıştırma oluşturulur. Alfa değeri, her çalıştırma arasında ayrım yapmak için günlüğe kaydedilir.
1. Her çalıştırmada, bir Ridge modeli örneği oluşturulur, eğitilmiş ve tahmin çalıştırmaları için kullanılır. Kök-ortalama-kare-hatası, fiili ve tahmin edilen değerler için hesaplanır ve ardından çalıştırmaya kaydedilir. Bu noktada, çalışma için hem alfa değeri hem de rmo doğruluğu için eklenmiş meta veriler vardır.
1. Ardından, her bir çalıştırmaya ait model serileştirilir ve çalıştırmaya yüklenir. Bu, model dosyasını portalda çalıştırağından indirmelerine olanak sağlar.
1. Her yinelemenin sonunda, çalıştırma `run.complete()`çağırarak tamamlanır.

Eğitim tamamlandıktan sonra, portalda deneye bir bağlantı getirmek için `experiment` değişkenini çağırın.

```python
experiment
```

<table style="width:100%"><tr><th>Ad</th><th>Çalışma Alanı</th><th>Rapor sayfası</th><th>Docs sayfası</th></tr><tr><td>Diabetes-deneme</td><td>çalışma alanınızın adı</td><td>Azure portal bağlantısı</td><td>Belge bağlantısı</td></tr></table>

## <a name="view-training-results-in-portal"></a>Portalda Eğitim sonuçlarını görüntüleme

**Azure Portal bağlantısını** izleyen ana deneme sayfasına gidersiniz. İşte denemenize ait tüm bireysel çalıştırmaları görürsünüz. Özel olarak günlüğe kaydedilen tüm değerler (Bu durumda`alpha_value` ve `rmse`) her çalıştırma için alanlar haline gelir ve ayrıca deneme sayfasının en üstündeki grafikler ve kutucuklar için kullanılabilir hale gelir. Bir grafiğe veya kutucuğa günlüğe kaydedilmiş bir ölçüm eklemek için, üzerine gelin, Düzenle düğmesine tıklayın ve özel olarak günlüğe kaydedilen ölçümünüzün bulun.

Eğitim modelleri, yüzlerce ve binlerce ayrı çalıştırma üzerinde ölçeklendirirseniz, Bu sayfa, eğitilen her modeli, özellikle eğitilen ve benzersiz ölçümlerinizin zaman içinde nasıl değiştiğini görmenizi kolaylaştırır.

![Portalda ana deneme sayfası](./media/tutorial-quickstart/experiment-main.png)

`RUN NUMBER` sütununda bir çalıştırma numarası bağlantısına tıkladığınızda her bir çalıştırmaya ilişkin sayfaya gidersiniz. Varsayılan sekme **ayrıntıları** , her çalıştırma hakkında daha ayrıntılı bilgi gösterir. **Çıktılar** sekmesine gidin ve her eğitim yinelemesi sırasında çalıştırmaya yüklenmiş olan modelin `.pkl` dosyasını görürsünüz. Burada, model dosyasını el ile yeniden eğitmek yerine indirebilirsiniz.

![Portalda Ayrıntılar sayfasını Çalıştır](./media/tutorial-quickstart/model-download.png)

## <a name="get-the-best-model"></a>En iyi modeli al

Portaldaki deneyden model dosyalarını indirebilmenin yanı sıra, bunları program aracılığıyla da indirebilirsiniz. Aşağıdaki kod, denemenin her çalıştırması boyunca yinelenir ve günlüğe kaydedilen çalışma ölçümlerine ve çalıştırma ayrıntılarına (run_id içeren) erişir. Bu, en iyi çalışmayı izler, bu durumda en düşük kök-ortalama-kare-hatası ile çalıştırılır.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Deneme nesnesiyle birlikte `Run` oluşturucusunu kullanarak bireysel çalışmayı getirmek için en iyi çalıştırma KIMLIĞINI kullanın. Sonra bu çalıştırınızdan yüklenebilecek tüm dosyaları görmek için `get_file_names()` çağırın. Bu durumda, yalnızca eğitim sırasında her çalıştırma için bir dosya karşıya yüklenir.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Çalıştırılacak model dosyası adını belirterek çalıştırma nesnesinde `download()` çağırın. Varsayılan olarak, bu işlev geçerli dizine indirir.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Azure Machine Learning öğreticileri çalıştırmayı planlıyorsanız, bu bölümü tamamlamayın.

### <a name="stop-the-notebook-vm"></a>Not defteri VM 'sini durdur

[!INCLUDE [aml-stop-server](../../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Her şeyi sil

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Ayrıca, kaynak grubunu koruyabilir ancak tek bir çalışma alanını silebilirsiniz. Çalışma alanı özelliklerini görüntüleyin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki görevleri yaptınız:

> [!div class="checklist"]
> * Çalışma alanınızı bağladınız ve bir deneme oluşturdunuz
> * Yüklenen veriler ve eğitimli scikit-modelleri öğrenme
> * Görüntülenen eğitim sonuçları portalda ve modeller alındı

[Modelinizi](tutorial-deploy-models-with-aml.md) Azure Machine Learning ile dağıtın.
[Otomatik makine öğrenimi](tutorial-auto-train-models.md) denemeleri geliştirmeyi öğrenin.
