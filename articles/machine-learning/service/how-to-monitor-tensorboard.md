---
title: Denemeleri TensorBoard ile görselleştirme
titleSuffix: Azure Machine Learning
description: Deneme çalıştırma geçmişlerini görselleştirmek ve hiper parametre ayarlama ve yeniden eğitimi için olası bölgeleri belirlemek üzere TensorBoard 'ı başlatın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 11/08/2019
ms.openlocfilehash: fc8159b3deba373948f513cb11540695362ecaf1
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954556"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>TensorBoard ve Azure Machine Learning deneme çalıştırmalarını ve ölçümlerini görselleştirin
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, deneme çalışmalarınızı ve ölçümlerini, ana Azure Machine Learning SDK 'sında [`tensorboard` paketini](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) kullanarak TensorBoard 'da görüntülemeyi öğreneceksiniz. Deneme çalışmalarınızı inceledikten sonra Machine Learning modellerinizi daha iyi ayarlayabilir ve yeniden eğitebilirsiniz.

[Tensorboard](https://www.tensorflow.org/tensorboard/r1/overview) , deneme yapınızı ve performansınızı incelemek ve anlamak için bir Web uygulamaları paketidir.

Azure Machine Learning denemeleri ile TensorBoard 'ı nasıl başladığınıza, deneme türüne bağlıdır:
+ Denemeniz, PyTorch, Chainer ve TensorFlow denemeleri gibi TensorBoard tarafından tüketilebilir olan günlük dosyalarını yerel olarak çıktılarsam, [TensorBoard 'ı doğrudan](#direct) deneme 'nin çalıştırma geçmişinden başlatabilirsiniz. 

+ Scikit-denemeleri veya Azure Machine Learning denemeleri gibi TensorBoard tüketilebilir dosyaları yerel olarak çıkışı olmayan için, çalıştırma geçmişlerini TensorBoard günlükleri olarak dışarı aktarmak için [`export_to_tensorboard()` yöntemini](#export) kullanın ve bundan sonra tensorboard 'ı başlatın. 

> [!TIP]
> Bu belgedeki bilgiler öncelikli olarak, model eğitimi sürecini izlemek isteyen veri bilimcileri ve geliştiricileri içindir. Kotalar, tamamlanan eğitim çalıştırmaları veya tamamlanmış model dağıtımları gibi Azure Machine Learning 'den kaynak kullanımını ve olayları izlemeyi ilgilenen bir yöneticiyseniz, bkz. [izleme Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Önkoşullar

* TensorBoard 'u başlatmak ve deneme çalışma geçmişlerinizi görüntülemek için, denemeleri ' nin, ölçümlerini ve performansını izlemek için daha önce günlüğe kaydetme özelliğinin etkinleştirilmesi gerekir.  

* Bu belgedeki kod aşağıdaki ortamların birinde çalıştırılabilir: 

    * Azure Machine Learning Not defteri VM-indirme veya yükleme gerekli değil

        * Öğreticiyi doldurun: SDK ve örnek depoyla önceden yüklenmiş adanmış bir not defteri sunucusu oluşturmak için [ortamı ve çalışma alanını kurma](tutorial-1st-experiment-sdk-setup.md) .

        * Not defteri sunucusundaki örnekler klasöründe, bu dizinlere giderek iki tamamlanmış ve genişletilmiş Not defteri bulun:
            * **> > > nasıl yapılır---------------------by-by**

            * **nasıl kullanılır-azureml > izleme ve izleme-denemeleri > tensorboard. ipynb**

    * Kendi Juptyer Not defteri sunucunuz
       * `tensorboard` ek ile [Azure Machine Learning SDK 'Sını yükler](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
        * [Azure Machine Learning çalışma alanı oluşturun](how-to-manage-workspace.md).  
        * [Bir çalışma alanı yapılandırma dosyası oluşturun](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Seçenek 1: çalışma geçmişini TensorBoard 'da doğrudan görüntüleme

Bu seçenek, PyTorch, Chainer ve TensorFlow denemeleri gibi TensorBoard tarafından tüketilebilir olan günlük dosyalarını yerel olarak veren denemeleri için geçerlidir. Bu, denemenizin durumu değilse, bunun yerine [`export_to_tensorboard()` yöntemini](#export) kullanın.

Aşağıdaki örnek kod, bir uzak işlem hedefi, Azure Machine Learning Işlem için TensorFlow 'un deposundan [Mnist demo deneme](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) deneyimini kullanır. Daha sonra, modelimizi SDK 'nın özel [TensorFlow tahmin aracı](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)ile eğiyoruz ve ardından tensorboard 'i bu TensorFlow deneyine karşı başlatın, diğer bir deyişle, tensorboard olay dosyalarını yerel olarak veren bir deneyimiz.

### <a name="set-experiment-name-and-create-project-folder"></a>Deneme adını ayarla ve proje klasörü oluştur

Burada, denemeyi adlandırın ve klasörünü oluşturun. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>TensorFlow demo deneme kodunu indirin

TensorFlow deposunda, kapsamlı TensorBoard aletli bir yönetim tanıtımı vardır. Bu tanıtım kodu, Azure Machine Learning ile çalışmak üzere bu tanıtımın kodunu değiştirmez. Aşağıdaki kodda, MNIST kodunu indiriyoruz ve yeni oluşturulan deneme klasörüne kaydedebiliyoruz.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Mnist_with_summaries. Kopyala, bu kod dosyası boyunca `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` vb. çağıran satırlar olduğunu fark edeceksiniz. Bu yöntemler, denemeleri 'in çalıştırma geçmişine yönelik anahtar ölçümlerini gruplar, günlüğe kaydeder ve Etiketler. `tf.summary.FileWriter()`, özel olarak günlüğe kaydedilen deneme ölçümlerinden verileri serileştirerek, TensorBoard 'in onları kapamasını sağlar.

 ### <a name="configure-experiment"></a>Deneme yapılandırma

Aşağıda, denememiz yapılandırır ve Günlükler ve veriler için Dizin ayarladık. Bu Günlükler, daha sonra, TensorBoard 'ın daha sonra eriştiği yapıt hizmetine yüklenir.

>[!Note]
> Bu TensorFlow örneği için, yerel makinenize TensorFlow yüklemeniz gerekir. Diğer bir deyişle, yerel makine TensorBoard çalıştırdığı için, TensorBoard modülüne (yani, TensorFlow ile birlikte dahil edilen) Bu not defteri 'nin çekirdeğine erişilebilir olması gerekir.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Denemeniz için bir küme oluşturun
Bu deneme için bir AmlCompute kümesi oluşturacağız, ancak denemeleri 'niz herhangi bir ortamda oluşturulabilir ve deneme çalıştırma geçmişine karşı TensorBoard 'ı yine de başlatabileceksiniz. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>TensorFlow tahmin aracı ile çalıştırma gönder

TensorFlow tahmin aracı, bir işlem hedefinde bir TensorFlow eğitim işi başlatmanın basit bir yolunu sağlar. Bu, herhangi bir çerçeveyi desteklemek için kullanılabilen genel [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) sınıfı aracılığıyla uygulanır. Genel tahmin aracı kullanan eğitim modelleri hakkında daha fazla bilgi için bkz. [tahmin aracı kullanarak Azure Machine Learning modelleri eğitme](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>TensorBoard Başlat

Çalışma sırasında veya tamamlandıktan sonra TensorBoard 'ı başlatabilirsiniz. Aşağıda, `run`yüklenen deneme çalıştırması geçmişini alan `tb`ve ardından `start()` yöntemiyle TensorBoard Başlatan bir TensorBoard nesne örneği oluşturacağız. 
  
[Tensorboard Oluşturucusu](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) bir çalıştırma dizisi alır, bu yüzden emin olun ve tek öğeli dizi olarak geçirin.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Seçenek 2: geçmişi, TensorBoard 'da görüntülenecek şekilde dışa aktarma

Aşağıdaki kod örnek bir deneme oluşturur, Azure Machine Learning çalıştırma geçmişi API 'Lerini kullanarak günlüğe kaydetme işlemini başlatır ve deneme çalıştırması geçmişini görselleştirme için TensorBoard tarafından tüketilen günlüklere dışarı aktarır. 

### <a name="set-up-experiment"></a>Deneme ayarla

Aşağıdaki kod yeni bir deneme ayarlar ve çalıştırma dizini `root_run`adlandırır. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Burada, diabetes veri kümesini yüklüyoruz; scikit-öğren ile birlikte gelen yerleşik küçük bir veri kümesi ve test ve eğitim kümelerine bölmek.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Deneme ve günlük ölçümlerini çalıştırma

Bu kod için, bir doğrusal regresyon modeli ve günlük anahtarı ölçümleri, alfa katsayısı, `alpha`ve ortalama kareli hata, `mse`, çalışma geçmişinde eğeceğiz.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Çalıştırmaları TensorBoard 'a dışarı aktarma

SDK 'nın [export_to_tensorboard ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) yöntemiyle, Azure Machine Learning denemızın çalıştırma geçmişini tensorboard günlüklerine verebiliriz, böylece bunları tensorboard aracılığıyla görüntüleyebiliriz.  

Aşağıdaki kodda, klasörü geçerli çalışma dizinimizde `logdir` oluşturacağız. Bu klasör, deneme çalışma geçmişimizi `root_run` ' den dışarı aktarmamız ve sonra bu çalışmayı tamamlandı olarak işaretliyoruz. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 Ayrıca, çalıştırma `export_to_tensorboard(run_name, logdir)` adını belirterek, belirli bir çalıştırmayı TensorBoard 'e dışarı aktarabilirsiniz.

### <a name="start-and-stop-tensorboard"></a>TensorBoard başlatma ve durdurma
Bu deneme için çalıştırma geçmişimiz verildikten sonra, TensorBoard 'ı [Start ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) yöntemiyle başlatabiliriz. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

İşiniz bittiğinde, TensorBoard nesnesinin [Stop ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) yöntemini çağırdığınızdan emin olun. Aksi halde, ana bilgisayar çekirdeğini kapatıncaya kadar TensorBoard çalışmaya devam edecektir. 

```python
tb.stop()
```

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır ki, iki denemeleri oluşturdunuz ve olası ayarlama ve yeniden eğitimlere yönelik bölgeleri belirlemek için, çalışma geçmişlerine göre TensorBoard 'i nasıl başlatacağınızı öğrendiniz. 

* Modelinize memnun kaldıysanız [model dağıtma](how-to-deploy-and-where.md) makalesini okuyun. 
* [Hyperparameter ayarlaması](how-to-tune-hyperparameters.md)hakkında daha fazla bilgi edinin. 
