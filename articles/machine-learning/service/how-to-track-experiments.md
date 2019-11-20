---
title: ML denemeleri & ölçümlerini günlüğe kaydet
titleSuffix: Azure Machine Learning
description: Azure ML denemeleri izleyin ve model oluşturma işlemini iyileştirmek için çalışma ölçümlerini izleyin. Eğitim betiğe günlük kaydı ekleyin ve bir çalıştırmanın günlüğe kaydedilmiş sonuçlarını görüntüleyin.  Run. log, Run. start_logging veya ScriptRunConfig kullanın.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.custom: seodec18
ms.openlocfilehash: d8a2c456c725a3170bc940bf17dec6b0c4ad2c3e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584537"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Azure ML deneme çalıştırmaları ve ölçümlerini izleme
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Denemeleri ve izleme çalıştırma ölçümlerinizi izleyerek model oluşturma sürecini geliştirin. Bu makalede, eğitim betiğe günlük kodu eklemeyi, bir deneme çalıştırması göndermeyi, çalışmayı izlemeyi ve Azure Machine Learning sonuçları incelemeyi öğrenin.

> [!NOTE]
> Azure Machine Learning, otomatik makine öğrenimi çalıştırmaları veya eğitim işini çalıştıran Docker kapsayıcısı gibi eğitim sırasında diğer kaynaklardaki bilgileri de günlüğe alabilir. Bu Günlükler belgelenmemiştir. Sorunlarla karşılaşırsanız ve Microsoft Destek ile iletişime geçerek, sorun giderme sırasında bu günlükleri kullanabiliyor olabilirler.

> [!TIP]
> Bu belgedeki bilgiler öncelikli olarak, model eğitimi sürecini izlemek isteyen veri bilimcileri ve geliştiricileri içindir. Kotalar, tamamlanan eğitim çalıştırmaları veya tamamlanmış model dağıtımları gibi Azure Machine Learning 'den kaynak kullanımını ve olayları izlemeyi ilgilenen bir yöneticiyseniz, bkz. [izleme Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>İzlenecek kullanılabilir ölçümler

Bir denemeye eğitim sırasında aşağıdaki ölçümler bir çalıştırmaya eklenebilir. Bir çalıştırmada nelerin izlenebilecek hakkında daha ayrıntılı bir liste görüntülemek için, [sınıf başvurusunu Çalıştır belgelerine](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)bakın.

|Tür| Python işlevi | Notlar|
|----|:----|:----|
|Skaler değerler |Çalışmayacaktır<br>`run.log(name, value, description='')`<br><br>Örnek:<br>Çalıştır. log ("doğruluk", 0,95) |Verilen ada sahip bir sayı veya dize değerini çalıştırmaya göre günlüğe kaydedin. Bir ölçüyü bir çalıştırmaya kaydetmek, ölçümün deneme içinde çalıştırma kaydında depolanmasına neden olur.  Aynı ölçümü bir çalıştırma içinde birden çok kez günlüğe kaydedebilirsiniz. sonuç, bu ölçümün bir vektörü olarak kabul edilir.|
|Liste|Çalışmayacaktır<br>`run.log_list(name, value, description='')`<br><br>Örnek:<br>Çalıştır. log _list ("accuracies", [0,6, 0,7, 0,87]) | Belirtilen ada sahip farklı çalıştır değerlerin bir listesini oturum.|
|sırada|Çalışmayacaktır<br>`run.log_row(name, description=None, **kwargs)`<br>Örnek:<br>Çalıştır. log _row ("X üzerinden X", x = 1, Y = 0,4) | *Log_row* kullanmak, kwarg 'ler içinde açıklandığı gibi birden çok sütunlu bir ölçü oluşturur. Her adlandırılmış parametre belirtilen değeri içeren bir sütun oluşturur.  *log_row* , rastgele bir tanımlama grubu günlüğe kaydetmek için bir kez veya bir döngüde bir bütün tablo oluşturmak için birden çok kez çağrılabilir.|
|Tablo|Çalışmayacaktır<br>`run.log_table(name, value, description='')`<br><br>Örnek:<br>Çalıştır. log _table ("X üzerinden X", {"X": [1, 2, 3], "Y": [0,6, 0,7, 0,89]}) | Bir sözlük nesnesini verilen ada sahip bir çalıştırmaya kaydedin. |
|Görüntüler|Çalışmayacaktır<br>`run.log_image(name, path=None, plot=None)`<br><br>Örnek:<br>`run.log_image("ROC", plt)` | Çalıştırma kaydına bir görüntü kaydedin. Log_image kullanarak bir görüntü dosyası veya bir Matplotlib çizimi çalıştırmaya kaydedilir.  Bu görüntüler, çalıştırma kaydında görünür ve karşılaştırılabilir olacaktır.|
|Bir çalıştırmayı etiketleme|Çalışmayacaktır<br>`run.tag(key, value=None)`<br><br>Örnek:<br>Run. Tag ("Selected", "Yes") | Çalıştırmayı bir dize anahtarıyla ve isteğe bağlı dize değeriyle etiketleyin.|
|Dosya veya dizini karşıya yükle|Çalışmayacaktır<br>`run.upload_file(name, path_or_stream)`<br> <br> Örnek:<br>Run. upload_file ("best_model. pkl", "./model.exe") | Çalıştırma kaydına bir dosya yükleyin. , Belirtilen çıkış dizininde dosyayı otomatik olarak yakala, bu, çoğu çalıştırma türü için varsayılan olarak "./çıktılar" olarak belirlenmiştir.  Yalnızca ek dosyaların yüklenmesi gerektiğinde veya bir çıktı dizini belirtilmediğinde upload_file kullanın. Ada `outputs`, çıktılar dizinine karşıya yüklenecek şekilde eklenmesini öneririz. Bu çalıştırma kaydıyla ilişkili tüm dosyaları `run.get_file_names()` çağırılır.|

> [!NOTE]
> Dolandırıcıklar, listeler, satırlar ve tablolar için ölçümler şunlardır: float, integer veya String.

## <a name="choose-a-logging-option"></a>Günlüğe kaydetme seçeneği seçin

Denemenizi izlemek veya izlemek isterseniz, çalıştırmayı gönderdiğinizde günlüğe kaydetmeyi başlatmak için kod eklemeniz gerekir. Aşağıda, çalıştırma gönderimini tetiklemenin yolları verilmiştir:
* __Run. start_logging__ -günlük işlevlerini eğitim betiğe ekleyin ve belirtilen deneyde etkileşimli bir günlüğe kaydetme oturumu başlatın. **start_logging** , Not defterleri gibi senaryolarda kullanılmak üzere etkileşimli bir çalıştırma oluşturur. Oturum sırasında günlüğe kaydedilen ölçümler, denemenin içindeki çalıştırma kaydına eklenir.
* __ScriptRunConfig__ -günlük işlevlerini eğitim betiğe ekleyin ve tüm betik klasörünü çalıştırmaya yükleyin.  **ScriptRunConfig** , betik çalıştırmaları için yapılandırmaların ayarlanmasına yönelik bir sınıftır. Bu seçenekle, tamamlama hakkında bildirim almak veya izlenecek bir görsel pencere öğesi almak için izleme kodu ekleyebilirsiniz.

## <a name="set-up-the-workspace"></a>Çalışma alanını ayarlama
Günlüğe kaydetme ve deneme göndermeden önce, çalışma alanını ayarlamanız gerekir.

1. Çalışma alanını yükleyin. Çalışma alanı yapılandırmasını ayarlama hakkında daha fazla bilgi için bkz. [çalışma alanı yapılandırma dosyası](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Seçenek 1: start_logging kullanma

**start_logging** , Not defterleri gibi senaryolarda kullanılmak üzere etkileşimli bir çalıştırma oluşturur. Oturum sırasında günlüğe kaydedilen ölçümler, denemenin içindeki çalıştırma kaydına eklenir.

Aşağıdaki örnek, yerel bir Jupyter not defterinde bir basit sktrıdge modelini yerel olarak öğrendi. Farklı ortamlara denemeleri gönderme hakkında daha fazla bilgi edinmek için bkz. [Azure Machine Learning ile model eğitimi için işlem hedeflerini ayarlama](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Yerel bir Jupyter not defterinde eğitim betiği oluşturun. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Azure Machine Learning SDK kullanarak deneme izleme ekleyin ve deneme çalıştırması kaydına kalıcı bir model yükleyin. Aşağıdaki kod, Etiketler, Günlükler ve bir model dosyasını deneme çalıştırmasına ekler.

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    Betik, çalışmayı tamamlandı olarak işaretleyen ```run.complete()```biter.  Bu işlev genellikle etkileşimli not defteri senaryolarında kullanılır.

## <a name="option-2-use-scriptrunconfig"></a>Seçenek 2: ScriptRunConfig kullanma

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) , betik çalıştırmaları için yapılandırmaların ayarlanmasına yönelik bir sınıftır. Bu seçenekle, tamamlama hakkında bildirim almak veya izlenecek bir görsel pencere öğesi almak için izleme kodu ekleyebilirsiniz.

Bu örnek, yukarıdaki temel sköğren Ridge model üzerinde genişletilir. Bu, deneme kapsamındaki çalışma aşamasındaki ölçümleri ve eğitilen modelleri yakalamak için modelin Alfa değerlerinin üzerinde tarama yapmak üzere basit bir parametre tarama işlemi yapar. Örnek, Kullanıcı tarafından yönetilen bir ortamda yerel olarak çalışır. 

1. Eğitim betiği oluşturun `train.py`.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. `train.py` betiği, Ridge modelinde kullanılacak Alfa değerlerinin listesini almanıza izin veren `mylib.py` başvurur.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Kullanıcı tarafından yönetilen bir yerel ortam yapılandırın.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. ```train.py``` betiği Kullanıcı tarafından yönetilen ortamda çalışacak şekilde gönderebilirsiniz. Bu tüm betik klasörü, ```mylib.py``` dosyası dahil olmak üzere eğitim için gönderilir.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Bir çalıştırmayı yönetme

[Başlat, izle ve iptal eğitimi çalıştırmaları](how-to-manage-runs.md) makalesinde, denemeleri ' yi yönetme hakkında belirli Azure Machine Learning iş akışları vurgulanmaktadır.

## <a name="view-run-details"></a>Çalışma ayrıntılarını görüntüle

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Jupyter Not defteri pencere öğesiyle çalışma izleme
Çalıştırmaları göndermek için **ScriptRunConfig** yöntemini kullandığınızda, bir [Jupyıter pencere öğesi](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)ile çalıştırmanın ilerlemesini izleyebilirsiniz. Çalıştırma gönderimi gibi pencere öğesi de zaman uyumsuzdur ve iş tamamlanana kadar her 10-15 saniyede bir canlı güncelleştirmeler sağlar.

1. Çalıştırmanın tamamlanmasını beklerken Jupyıter pencere öğesini görüntüleyin.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Jupyter Not defteri pencere öğesinin ekran görüntüsü](./media/how-to-track-experiments/run-details-widget.png)

Ayrıca, çalışma alanınızda aynı görüntüleme bağlantısını da alabilirsiniz.

```python
print(run.get_portal_url())
```

2. **[Otomatik makine öğrenimi çalıştırmaları için]** Önceki bir çalıştırmalardan grafiklere erişmek için. `<<experiment_name>>` uygun deneme adıyla değiştirin:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Otomatik Machine Learning için Jupyter Not defteri pencere öğesi](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Bir işlem hattının daha ayrıntılı ayrıntılarını görüntülemek için tabloda araştırmak istediğiniz işlem hattına tıklayın ve grafikler Azure Machine Learning Studio 'dan bir açılır pencerede işlenir.

### <a name="get-log-results-upon-completion"></a>Tamamlandıktan sonra günlük sonuçlarını alma

Model eğitimi ve izleme arka planda gerçekleşirken diğer görevleri beklerken çalıştırabilirsiniz. Ayrıca, modelin daha fazla kod çalıştırmadan önce eğitimi tamamlamasını de bekleyebilirsiniz. **ScriptRunConfig**kullandığınızda, model eğitiminin ne zaman tamamlandığını göstermek için ```run.wait_for_completion(show_output = True)``` kullanabilirsiniz. ```show_output``` bayrağı ayrıntılı çıkış sağlar. 


### <a name="query-run-metrics"></a>Sorgu çalıştırma ölçümleri

```run.get_metrics()```kullanarak eğitilen bir modelin ölçümlerini görüntüleyebilirsiniz. Bundan böyle, en iyi modeli belirleyebilmek için yukarıdaki örnekte günlüğe kaydedilen tüm ölçümleri elde edebilirsiniz.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studiohttpsmlazurecom"></a>[Azure Machine Learning Studio](https://ml.azure.com) 'daki çalışma alanınızdaki denemeyi görüntüleyin

Bir deneme çalışmayı tamamladığında, kaydedilen deneme çalıştırması kaydına gidebilirsiniz. [Azure Machine Learning Studio](https://ml.azure.com)'dan geçmişe erişebilirsiniz.

Denemeleri sekmesine gidin ve denemenizin seçimini yapın. Her çalıştırma için günlüğe kaydedilen izlenen ölçümleri ve grafikleri görebileceğiniz deneme çalıştırması panosuna yönlendirilirsiniz. Bu durumda, MSE ve Alfa değerlerini günlüğe verdik.

  ![Azure Machine Learning Studio 'da ayrıntıları çalıştırma](./media/how-to-track-experiments/experiment-dashboard.png)

Belirli bir çalıştırmaya gidebilir ya da çıktılarını veya günlüklerini görüntüleyebilir ya da deneme klasörünü başkalarıyla paylaşabileceğiniz şekilde gönderdiğiniz deneyinin anlık görüntüsünü indirebilirsiniz.

### <a name="viewing-charts-in-run-details"></a>Çalışma ayrıntılarında grafikleri görüntüleme

Bir çalıştırma sırasında farklı ölçüm türlerini kaydetmek ve bunları Azure Machine Learning Studio 'da grafik olarak görüntülemek için günlük API 'Leri kullanmanın çeşitli yolları vardır.

|Günlüğe kaydedilen değer|Örnek kod| Portalda görüntüle|
|----|----|----|
|Sayısal değerlerden oluşan diziyi günlüğe kaydet| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Tek değişkenli çizgi grafik|
|Aynı ölçüm adı ile birlikte sürekli kullanılan tek bir sayısal değeri günlüğe kaydet (for döngüsü içinde olduğu gibi)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Tek değişkenli çizgi grafik|
|2 sayısal sütundan oluşan bir satırı sürekli olarak günlüğe kaydet|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|İki değişkenli çizgi grafik|
|2 sayısal sütun içeren günlük tablosu|`run.log_table(name='Sine Wave', value=sines)`|İki değişkenli çizgi grafik|


## <a name="example-notebooks"></a>Örnek Not defterleri
Aşağıdaki Not defterleri bu makaledeki kavramları göstermektedir:
* [Nasıl yapılır kullanımı-bir not defteri](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [Nasıl yapılır-kullanım-azureml/eğitim/yerel olarak eğitme](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-Use-azureml/eğitim/Logging-api/Logging-api. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Sonraki adımlar

Python için Azure Machine Learning SDK 'nın nasıl kullanılacağını öğrenmek için bu sonraki adımları deneyin:

* En iyi modeli nasıl kaydedeceğinizi ve öğreticide nasıl dağıtabileceğinizi gösteren bir örnek, [Azure Machine Learning ile bir görüntü sınıflandırma modeli eğitme](tutorial-train-models-with-aml.md)bölümüne bakın.

* [PyTorch modellerini Azure Machine Learning Ile eğitme](how-to-train-pytorch.md)hakkında bilgi edinin.
