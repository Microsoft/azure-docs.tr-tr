---
title: ML işlem hatları içinde otomatik ML kullanma
titleSuffix: Azure Machine Learning
description: Otomatikmlstep, ardışık düzeninizde otomatik makine öğrenimini kullanmanıza olanak sağlar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 02/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl
ms.openlocfilehash: 0de3c9a7cf464f38a1a12d8bc19451fb1158a5ad
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520515"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Python 'da Azure Machine Learning işlem hattında otomatik ML kullanma


Azure Machine Learning otomatikleştirilmiş ML özelliği, mümkün olan her yaklaşımı yeniden uygulamadan yüksek performanslı modelleri keşfetmenize yardımcı olur. Azure Machine Learning işlem hatlarında, verileriniz için en uygun algoritmayı hızlı bir şekilde keşfedelebilecek dağıtılabilir iş akışları oluşturabilirsiniz. Bu makalede, bir veri hazırlama adımını otomatik bir ML adımına nasıl verimli bir şekilde katılacaksınız gösterilmektedir. Otomatikleştirilmiş ML, verilerinize en uygun algoritmayı hızlıca bulabilir. bu sırada, işlem hatlarıyla birlikte MLOps ve model yaşam döngüsü için yol üzerine getirme yapabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

* Azure Machine Learning çalışma alanı. Bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).  

* Azure 'un [otomatik makine öğrenimi](concept-automated-ml.md) ve [makine öğrenimi ardışık düzenleri](concept-ml-pipelines.md) ve SDK 'sı hakkında bilgi sahibi.

## <a name="review-automated-mls-central-classes"></a>Otomatik ML 'nin merkezi sınıflarını gözden geçirin

Bir işlem hattındaki otomatikleştirilen ML bir nesne tarafından temsil edilir `AutoMLStep` . `AutoMLStep`Sınıfı, öğesinin bir alt sınıfıdır `PipelineStep` . `PipelineStep`Nesneleri bir grafik tanımlar `Pipeline` .

Öğesinin birkaç alt sınıfları vardır `PipelineStep` . ' A ek olarak, `AutoMLStep` Bu makalede bir `PythonScriptStep` veri hazırlığı ve modeli kaydetmek için bir diğeri görüntülenir.

İlk _olarak BIR ml_ ardışık düzenine veri taşımanın tercih edilen yolu, `Dataset` nesneleriyle birlikte. Adımlar _arasında_ veri taşımak ve çalışma sırasında veri çıkışı kazanmak için tercih edilen yol, [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) ve [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig) nesneleridir. İle birlikte kullanılabilmesi için `AutoMLStep` `PipelineData` nesnenin bir nesnesine dönüştürülmesi gerekir `PipelineOutputTabularDataset` . Daha fazla bilgi için bkz. [ml işlem hatlarından giriş ve çıkış verileri](how-to-move-data-in-out-of-pipelines.md).

, `AutoMLStep` Bir nesnesi aracılığıyla yapılandırılır `AutoMLConfig` . `AutoMLConfig` , [Python 'da OTOMATIK ml denemeleri yapılandırma](./how-to-configure-auto-train.md#configure-your-experiment-settings)bölümünde anlatıldığı gibi esnek bir sınıftır. 

Bir `Pipeline` üzerinde çalışır `Experiment` . İşlem hattı, `Run` her adım bir alt öğe için içerir `StepRun` . Otomatikleştirilmiş ML 'nin çıkışları `StepRun` eğitim ölçümleri ve en yüksek performanslı modeldir.

Bu makale, şeyleri somut hale getirmek için bir sınıflandırma görevi için basit bir işlem hattı oluşturur. Görev, Titanic Inval 'i tahmin ediyor, ancak veri veya görev geçirilme dışında tartışıyoruz.

## <a name="get-started"></a>başlarken

### <a name="retrieve-initial-dataset"></a>İlk veri kümesini alma

Genellikle, bir ML iş akışı önceden varolan temel verilerle başlar. Bu, kayıtlı bir veri kümesi için iyi bir senaryodur. Veri kümeleri çalışma alanı genelinde görünür, sürüm oluşturmayı destekler ve etkileşimli olarak araştırılabilir. [Azure Machine Learning veri kümeleri oluşturma](how-to-create-register-datasets.md)bölümünde açıklandığı gibi, bir veri kümesi oluşturmanın ve doldurmanın birçok yolu vardır. İşlem hattımızı oluşturmak için Python SDK 'sını kullanacağız, temel verileri indirmek ve ' titanic_ds ' adına kaydetmek için SDK 'Yı kullanın.

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

Kod ilk olarak **config.jsüzerinde** tanımlanan Azure Machine Learning çalışma alanında oturum açar (bir açıklama için bkz. [çalışma alanı yapılandırma dosyası oluşturma](how-to-configure-environment.md#workspace). Zaten kayıtlı adlı bir veri kümesi yoksa `'titanic_ds'` , bir tane oluşturur. Kod, Web 'den CSV verilerini indirir, bunları bir örneğini başlatmak için kullanır `TabularDataset` ve sonra veri kümesini çalışma alanına kaydeder. Son olarak, işlevi `Dataset.get_by_name()` öğesine atar `Dataset` `titanic_ds` . 

### <a name="configure-your-storage-and-compute-target"></a>Depolama ve işlem hedefini yapılandırma

İşlem hattının gerek duyduğu ek kaynaklar depolama ve genellikle işlem kaynaklarını Azure Machine Learning. 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Veri hazırlama ve otomatik ML adımı arasındaki ara veri, çalışma alanının varsayılan veri deposunda depolanabilir, bu nedenle nesne üzerinde çağrıdan daha fazlasını yapmak zorunda kalmazsınız `get_default_datastore()` `Workspace` . 

Bundan sonra kod, AML işlem hedefinin zaten var olup olmadığını denetler `'cpu-cluster'` . Aksi takdirde, küçük bir CPU tabanlı işlem hedefi istiyoruz. Otomatik ML 'nin derin öğrenme özelliklerini kullanmayı planlıyorsanız (örneğin, DNN desteği ile metin kullanımı), [GPU iyileştirmeli sanal makine boyutları](../virtual-machines/sizes-gpu.md)bölümünde açıklandığı gıbı güçlü GPU desteğiyle bir işlem seçmeniz gerekir. 

Kod, hedef sağlanana kadar engeller ve ardından yeni oluşturulan işlem hedefinin bazı ayrıntılarını yazdırır. Son olarak, adlandırılmış işlem hedefi çalışma alanından alınır ve öğesine atanır `compute_target` . 

### <a name="configure-the-training-run"></a>Eğitim çalıştırmasını yapılandırma

Otomatik Mlstep, iş gönderimi sırasında bağımlılıklarını otomatik olarak yapılandırır. Çalışma zamanı bağlamı bir nesne oluşturup yapılandırarak ayarlanır `RunConfiguration` . Burada işlem hedefini ayarlayacağız.

```python
from azureml.core.runconfig import RunConfiguration

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target
```

## <a name="prepare-data-for-automated-machine-learning"></a>Otomatik makine öğrenimi için verileri hazırlama

### <a name="write-the-data-preparation-code"></a>Veri hazırlama kodunu yazma

Taban çizgisi Titanic veri kümesi, bazı değerler eksik olan karma sayısal ve metin verilerinden oluşur. Otomatik makine öğrenimi için hazırlamak üzere veri hazırlama işlem hattı adımı şunları olacak:

- Eksik verileri rastgele verilerle veya "Unknown" öğesine karşılık gelen bir kategori ile doldur
- Kategorik verileri tamsayılara Dönüştür
- Kullanmayı planlamadığımız bırakma sütunları
- Verileri eğitim ve test kümelerine bölme
- Dönüştürülen verileri `OutputFileDatasetConfig` çıkış yollarına yaz

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

df.to_csv(os.path.join(args.output_path,"prepped_data.csv"))

print(f"Wrote prepped data to {args.output_path}/prepped_data.csv")
```

Yukarıdaki kod parçacığı, Titanic verileri için veri hazırlama işleminin tamamlığını ve en düşük örneğidir. Kod parçacığı, kodu bir dosyaya çıkarmak için bir Jupyıter "Magic komutuyla" başlar. Jupyter Not defteri kullanmıyorsanız, bu satırı kaldırın ve dosyayı el ile oluşturun.

`prepare_`Yukarıdaki kod parçacığındaki çeşitli işlevler, giriş veri kümesindeki ilgili sütunu değiştirir. Bu işlevler, bir Pandas nesnesine değiştirildikten sonra veriler üzerinde çalışır `DataFrame` . Her durumda, eksik veriler temsili rastgele veriler veya "bilinmiyor" belirten kategorik veriler ile doldurulur. Metin tabanlı kategorik veriler, tamsayılarla eşlenir. Daha uzun süre gerekli olmayan sütunlar üzerine yazılır veya bırakılır. 

Kod, veri hazırlama işlevlerini tanımladıktan sonra kod, veri değişkenini ayrıştırır. Bu, verilerimizi yazmak istediğimizden oluşan yoldur.  (Bu değerler `OutputFileDatasetConfig` , bir sonraki adımda ele edilecek nesneler tarafından belirlenir.) Kod, kayıtlı öğesini alır `'titanic_cs'` `Dataset` , bir Pandas 'e dönüştürür `DataFrame` ve çeşitli veri hazırlama işlevlerini çağırır. 

`output_path`Bir dizin olduğundan, ' a çağrı `to_csv()` dosya adını belirtir `prepped_data.csv` .

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>Veri hazırlama işlem hattı adımını ( `PythonScriptStep` ) yazın

Yukarıda açıklanan veri hazırlama kodu, işlem `PythonScripStep` hattı ile kullanılacak bir nesneyle ilişkilendirilmelidir. CSV çıktısının yazıldığı yol bir nesne tarafından oluşturulur `OutputFileDatasetConfig` . Daha önce hazırlanan kaynakları,, ve gibi, `ComputeTarget` `RunConfig` `'titanic_ds' Dataset` belirtimi gerçekleştirmek için kullanılır.

```python
from azureml.data import OutputFileDatasetConfig
from azureml.pipeline.steps import PythonScriptStep

prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input('titanic_ds')],
    allow_reuse=True
)
```

`prepped_data_path`Nesne `OutputFileDatasetConfig` bir dizine işaret eden türdür.  Parametresinde belirtildiğine dikkat edin `arguments` . Önceki adımı gözden geçirdikten sonra, veri hazırlama kodunda bağımsız değişkenin değeri `'--output_path'` CSV dosyasının yazıldığı dizin yolu olduğunu görürsünüz. 

## <a name="train-with-automlstep"></a>Oto Mlstep ile eğitme

Bir otomatik ML ardışık düzen adımını yapılandırma sınıfı ile yapılır `AutoMLConfig` . Bu esnek sınıf, [Python 'da OTOMATIK ml denemeleri yapılandırma](./how-to-configure-auto-train.md)bölümünde açıklanmaktadır. Veri girişi ve çıkışı, bir ML ardışık düzeninde özel dikkat gerektiren yapılandırmanın tek yönlerine göre yapılır. İşlem hatlarında giriş ve çıkış `AutoMLConfig` aşağıda ayrıntılı olarak ele alınmıştır. Verilerin ötesinde, ML ardışık düzenleri avantajı farklı adımlar için farklı işlem hedefleri kullanabilme olanağıdır. `ComputeTarget`Yalnızca OTOMATIKLEŞTIRILMIŞ ml işlemi için daha güçlü bir işlem kullanmayı tercih edebilirsiniz. Bunun yapılması, nesnenin parametresine daha güçlü atama yapmak kadar basittir `RunConfiguration` `AutoMLConfig` `run_configuration` .

### <a name="send-data-to-automlstep"></a>Verileri buraya gönder `AutoMLStep`

Bir ML ardışık düzeninde, giriş verileri bir `Dataset` nesne olmalıdır. En yüksek performanslı yol, giriş verilerini nesne biçiminde sağlamaktır `OutputTabularDatasetConfig` . Nesnesi gibi, üzerinde ile bu türde bir nesne oluşturursunuz (örneğin,)  `read_delimited_files()` `OutputFileDatasetConfig` `prepped_data_path` `prepped_data_path` .

```python
# type(prepped_data) == OutputTabularDatasetConfig
prepped_data = prepped_data_path.read_delimited_files()
```

Diğer bir seçenek `Dataset` de çalışma alanında kayıtlı nesneleri kullanmaktır:

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

İki tekniği karşılaştırma:

| Teknik | Avantajlar ve dezavantajları | 
|-|-|
|`OutputTabularDatasetConfig`| Daha yüksek performans | 
|| Doğal yol `OutputFileDatasetConfig` | 
|| İşlem hattı çalıştırıldıktan sonra veriler kalıcı değil |
|| [Tekniği gösteren not defteri `OutputTabularDatasetConfig`](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| Kaydedilmeyecek `Dataset` | Düşük performans |
| | Birçok şekilde oluşturulabilir | 
| | Veriler devam ettirir ve çalışma alanı boyunca görünür |
| | [Kayıtlı tekniği gösteren not defteri `Dataset`](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)


### <a name="specify-automated-ml-outputs"></a>Otomatik ML çıkışları belirtin

Çıkışları, `AutoMLStep` daha yüksek performanslı modelin ve bu modelin kendisi için nihai ölçüm puanlarından oluşur. Bu çıktıları daha fazla ardışık düzen adımlarında kullanmak için, `OutputFileDatasetConfig` nesneleri bunları alacak şekilde hazırlayın.

```python
from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                            datastore=datastore,
                            pipeline_output_name='metrics_output',
                            training_output=TrainingOutput(type='Metrics'))

model_data = PipelineData(name='best_model_data',
                          datastore=datastore,
                          pipeline_output_name='model_output',
                          training_output=TrainingOutput(type='Model'))
```

Yukarıdaki kod parçacığında `PipelineData` ölçümler ve model çıktısı için iki nesne oluşturulur. Her birinin adı, daha önce alınan varsayılan veri deposuna atanır ve kaynağından özel ile ilişkilendirilir `type` `TrainingOutput` `AutoMLStep` . `pipeline_output_name`Bu nesnelere atandığımız için `PipelineData` , değerleri yalnızca bireysel ardışık düzen adımından değil, işlem hattından bir bütün olarak değil, "işlem hattı sonuçlarını inceleme" bölümünde açıklanacaktır. 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>Otomatik ML ardışık düzen adımını yapılandırma ve oluşturma

Girişler ve çıktılar tanımlandıktan sonra, ve oluşturma zamanı `AutoMLConfig` `AutoMLStep` . Yapılandırma ayrıntıları, [Python 'da OTOMATIK ml denemeleri yapılandırma](./how-to-configure-auto-train.md)başlığı altında açıklandığı gibi, görevinize bağlı olacaktır. Titanic Inval sınıflandırma görevi için aşağıdaki kod parçacığında basit bir yapılandırma gösterilmektedir.

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    passthru_automl_config=False,
    outputs=[metrics_data,model_data],
    enable_default_model_output=False,
    enable_default_metrics_output=False,
    allow_reuse=True)
```
Kod parçacığında ile yaygın olarak kullanılan bir deyim gösterilmektedir `AutoMLConfig` . Daha akıcı olan bağımsız değişkenler, farklı bir sözlükte belirtilir, ancak değişmeme olasılığı düşük olan değerler doğrudan `AutoMLConfig` oluşturucuda belirtilir. Bu durumda, `automl_settings` kısa bir çalıştırma belirtin: çalıştırma, ilk olarak yalnızca 2 yinelemeden veya 15 dakikadan sonra durur.

`automl_settings`Sözlük, `AutoMLConfig` oluşturucuya kwarg olarak geçirilir. Diğer parametreler karmaşık değildir:

- `task``classification`Bu örnek için olarak ayarlanır. Diğer geçerli değerler şunlardır `regression` ve `forecasting`
- `path` ve `debug_log` hata ayıklama bilgilerinin yazılacağı projenin ve yerel bir dosyanın yolunu açıklama 
- `compute_target` daha önce tanımlanan, `compute_target` Bu örnekte ucuz BIR CPU tabanlı makinedir. Oto ml 'nin derin öğrenme tesislerini kullanıyorsanız, işlem hedefini GPU tabanlı olacak şekilde değiştirmek isteyebilirsiniz
- `featurization` , olarak ayarlanır `auto` . Diğer ayrıntılar, otomatik ML yapılandırma belgesinin [veri Korleştirme](./how-to-configure-auto-train.md#data-featurization) bölümünde bulunabilir 
- `label_column_name` tahmin etmek için ilgilendiğiniz sütunu belirtir 
- `training_data``OutputTabularDatasetConfig`veri hazırlama adımının çıktılarından yapılan nesnelere ayarlanır 

, `AutoMLStep` `AutoMLConfig` Ve, `PipelineData` ölçümleri ve model verilerini tutmak için oluşturulan nesneler olan ve ' ı alır. 

>[!Important]
> `enable_default_model_output`Yalnızca kullanıyorsanız ve ' i ayarlamanız gerekir `enable_default_metrics_output` `True` `AutoMLStepRun` .

Bu örnekte, otomatik ML işlemi üzerinde çapraz doğrulamalar gerçekleştirecek `training_data` . Bağımsız değişkenle çapraz doğrulama sayısını kontrol edebilirsiniz `n_cross_validations` . Eğitim verilerinizi veri hazırlama adımlarınızın bir parçası olarak zaten ayırdıysanız `validation_data` kendi kendine ayarlayabilirsiniz `Dataset` .

`X`Veri özellikleri ve veri etiketleri için kullanımı zaman zaman görebilirsiniz `y` . Bu teknik kullanım dışıdır ve `training_data` giriş için kullanmanız gerekir. 

## <a name="register-the-model-generated-by-automated-ml"></a>Otomatik ML tarafından oluşturulan modeli Kaydet 

Basit bir ML işlem hattının son adımı oluşturulan modeli kaydediyor. Modeli çalışma alanının model kayıt defterine ekleyerek portalda kullanılabilir ve sürümlenebilir. Modeli kaydetmek için, çıktısını alan bir tane yazın `PythonScriptStep` `model_data` `AutoMLStep` .

### <a name="write-the-code-to-register-the-model"></a>Modeli kaydetmek için kodu yazın

Bir model bir öğesine kaydedilir `Workspace` . Yerel makinenizde çalışma alanınızda oturum açmak için kullanımı hakkında bilgi sahibisiniz `Workspace.from_config()` , ancak çalışma alanını çalışan BIR ml işlem hattı içinden almanın başka bir yolu var. , `Run.get_context()` Etkin öğesini alır `Run` . Bu `run` nesne, burada kullanılan birçok önemli nesneye erişim sağlar `Workspace` .

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>PythonScriptStep kodunu yazma

Model kaydı, `PythonScriptStep` `PipelineParameter` bağımsız değişkenlerinden biri için bir kullanır. İşlem hattı parametreleri, çalışma gönderme zamanında kolayca ayarlanabilme işlem hatları için bağımsız değişkenlerdir. Bildirildiğinde, normal bağımsız değişken olarak geçirilir. 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>Otomatikleştirilmiş ML işlem hattınızı oluşturma ve çalıştırma

Bir `AutoMLStep` normal işlem hattından farklı olmayan bir işlem hattı oluşturmak ve çalıştırmak. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

Yukarıdaki kod veri hazırlama, otomatik ML ve model kaydı adımlarını bir `Pipeline` nesneye birleştirir. Daha sonra bir `Experiment` nesnesi oluşturur. Varsa, `Experiment` Oluşturucu adlandırılmış denemeyi alır veya gerekirse oluşturun. İşlem hattını `Pipeline` `Experiment` zaman uyumsuz olarak çalıştıracak bir nesne oluşturarak öğesine gönderir `Run` . `wait_for_completion()`İşlev, çalıştırma tamamlanana kadar engeller.

### <a name="examine-pipeline-results"></a>İşlem hattı sonuçlarını inceleme 

Tamamlandıktan sonra `run` , `PipelineData` atanmış nesneleri alabilirsiniz `pipeline_output_name` . Sonuçları indirebilir ve daha fazla işleme için yükleyebilirsiniz.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

İndirilen dosyalar alt dizine yazılır `azureml/{run.id}/` . Ölçüm dosyası JSON olarak biçimlendirilir ve İnceleme için bir Pandas dataframe 'e dönüştürülebilir.

Yerel işleme için, Pandas, Pickle, AzureML SDK vb. gibi ilgili paketleri yüklemeniz gerekebilir. Bu örnekte, otomatik ML tarafından bulunan en iyi modelin XGBoost 'e bağlı olması olasıdır.

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

Yukarıdaki kod parçacığı, Azure veri deposundaki konumundan yüklenen ölçüm dosyasını gösterir. Bu dosyayı, açıklamada gösterildiği gibi indirilen dosyadan da yükleyebilirsiniz. Seri durumdan çıkarıldıktan ve bir Pandas DataFrame 'e dönüştürüldükten sonra otomatik ML adımının yinelemelerinin her biri için ayrıntılı ölçümleri görebilirsiniz.

Model dosyası `Model` , yük, daha fazla ölçüm Analizi vb. için kullanabileceğiniz bir nesne için seri durumdan çıkarılmış olabilir. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

Mevcut modellerle ilgili yükleme ve bunlarla çalışma hakkında daha fazla bilgi için bkz. [Azure Machine Learning var olan bir modeli kullanma](how-to-deploy-existing-model.md).

### <a name="download-the-results-of-an-automated-ml-run"></a>Otomatikleştirilmiş bir ML çalıştırmasının sonuçlarını indir

Makale ile birlikte takip ediyorsanız, Örneklenmiş bir `run` nesneniz olacaktır. Ancak `Run` , tamamlanan nesneleri `Workspace` bir nesnenin yoluyla da alabilirsiniz `Experiment` .

Çalışma alanı tüm denemeleri ve çalıştırmalarınızın tüm kayıtlarını içerir. Denemeleri çıkışlarını bulmak ve indirmek ya da kodu kullanmak için portalını kullanabilirsiniz. Geçmişteki bir çalıştırıcıdaki kayıtlara erişmek için, ilgilendiğiniz çalıştırmanın KIMLIĞINI bulmak için Azure Machine Learning kullanın. Bu KIMLIKLE, `run` ve ' nin belirli bir yöntemini seçebilirsiniz `Workspace` `Experiment` .

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

Yukarıdaki koddaki dizeleri geçmiş çalıştırmalarınızın özelliklerine göre değiştirmeniz gerekir. Yukarıdaki kod parçacığında, `ws` normal konuyla ilgili olarak atadığınız varsayılır `Workspace` `from_config()` . İlgilendiğiniz deneme doğrudan alınır ve ardından kod, `Run` değeri eşleştirerek ilgi alanını bulur `run.id` .

Bir nesneye sahip olduktan sonra `Run` ölçümleri ve modeli indirebilirsiniz. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

Her `Run` nesne `StepRun` , tek bir işlem hattı adımı çalıştırması hakkında bilgi içeren nesneler içerir. , `run` `StepRun` İçin nesnesine aranır `AutoMLStep` . Ölçüm ve model varsayılan adları kullanılarak alınır. Bu, `PipelineData` nesneleri öğesinin parametresine geçirmeseniz bile kullanılabilir `outputs` `AutoMLStep` . 

Son olarak, yukarıdaki "işlem hattı sonuçlarını Inceleme" bölümünde anlatıldığı gibi gerçek ölçümler ve model yerel makinenize indirilir.

## <a name="next-steps"></a>Sonraki Adımlar

- Bu Jupyter Not defterini, taksi Fares 'yi tahmin etmek için regresyon kullanan bir işlem hattında [Otomatik ml 'nin tamamen bir örneğini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) çalıştırın
- [Kod yazmadan otomatik ML denemeleri oluşturma](how-to-use-automated-ml-for-ml-models.md)
- [Otomatikleştirilen ml 'yi gösteren çeşitli jupi not defterlerini](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) keşfet
- İşlem hattınızı [uçtan uca mlops](./concept-model-management-and-deployment.md#automate-the-ml-lifecycle) 'ye tümleştirme veya [Mlops GitHub deposunu](https://github.com/Microsoft/MLOpspython) araştırma hakkında bilgi edinin
