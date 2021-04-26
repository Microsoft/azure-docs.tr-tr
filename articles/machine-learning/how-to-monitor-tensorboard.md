---
title: Denemeleri TensorBoard ile görselleştirme
titleSuffix: Azure Machine Learning
description: Deneme çalıştırma geçmişlerini görselleştirmek ve hiper parametre ayarlama ve yeniden eğitimi için olası bölgeleri belirlemek üzere TensorBoard 'ı başlatın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: minxia
ms.author: minxia
ms.date: 02/27/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: f8986ef895c5766d363261ef263851ee96d9c0da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727680"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>TensorBoard ve Azure Machine Learning deneme çalıştırmalarını ve ölçümlerini görselleştirin


Bu makalede, deneme çalışmalarınızı ve ölçümlerini, ana Azure Machine Learning SDK 'daki [ `tensorboard` paketi](/python/api/azureml-tensorboard/) kullanarak tensorboard 'da görüntülemeyi öğreneceksiniz. Deneme çalışmalarınızı inceledikten sonra Machine Learning modellerinizi daha iyi ayarlayabilir ve yeniden eğitebilirsiniz.

[Tensorboard](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard) , deneme yapınızı ve performansınızı incelemek ve anlamak için bir Web uygulamaları paketidir.

Azure Machine Learning denemeleri ile TensorBoard 'ı nasıl başladığınıza, deneme türüne bağlıdır:
+ Denemeniz, PyTorch, Chainer ve TensorFlow denemeleri gibi TensorBoard tarafından tüketilebilir olan günlük dosyalarını yerel olarak çıktılarsam, [TensorBoard 'ı doğrudan](#launch-tensorboard) deneme 'nin çalıştırma geçmişinden başlatabilirsiniz. 

+ Örneğin, Scikit-öğrenme veya Azure Machine Learning denemeleri gibi TensorBoard tüketilebilir dosyalarını yerel olarak çıkışı olmayan denemeleri için, çalıştırma geçmişlerini TensorBoard günlükleri olarak dışarı aktarmak ve bu uygulamayı buradan çalıştırmak için [ `export_to_tensorboard()` yöntemini](#export) kullanın. 

> [!TIP]
> Bu belgedeki bilgiler öncelikli olarak, model eğitimi sürecini izlemek isteyen veri bilimcileri ve geliştiricileri içindir. Kotalar, tamamlanan eğitim çalıştırmaları veya tamamlanmış model dağıtımları gibi Azure Machine Learning 'den kaynak kullanımını ve olayları izlemeyi ilgilenen bir yöneticiyseniz, bkz. [izleme Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Önkoşullar

* TensorBoard 'u başlatmak ve deneme çalışma geçmişlerinizi görüntülemek için, denemeleri ' nin, ölçümlerini ve performansını izlemek için daha önce günlüğe kaydetme özelliğinin etkinleştirilmesi gerekir.  
* Bu belgedeki kod aşağıdaki ortamların birinde çalıştırılabilir: 
    * Azure Machine Learning işlem örneği-indirme veya yükleme gerekli değil
        * Öğreticiyi doldurun: SDK ve örnek depoyla önceden yüklenmiş adanmış bir not defteri sunucusu oluşturmak için [ortamı ve çalışma alanını kurma](tutorial-1st-experiment-sdk-setup.md) .
        * Not defteri sunucusundaki örnekler klasöründe, bu dizinlere giderek iki tamamlanmış ve genişletilmiş Not defteri bulun:
            * **Nasıl yapılır-> > > >---------------------------kullanımı**
            * **nasıl kullanılır-azureml > izleme ve izleme-denemeleri > tensorboard > > tensorboard. ipynb**
    * Kendi Juptyer Not defteri sunucunuz
       * [Azure Machine Learning SDK 'sını](/python/api/overview/azure/ml/install) ek Ile birlikte yükleyip `tensorboard`
        * [Azure Machine Learning çalışma alanı oluşturun](how-to-manage-workspace.md).  
        * [Bir çalışma alanı yapılandırma dosyası oluşturun](how-to-configure-environment.md#workspace).

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Seçenek 1: çalışma geçmişini TensorBoard 'da doğrudan görüntüleme

Bu seçenek, PyTorch, Chainer ve TensorFlow denemeleri gibi TensorBoard tarafından tüketilebilir olan günlük dosyalarını yerel olarak veren denemeleri için geçerlidir. Bu, denemenizin durumu değilse, bunun yerine [ `export_to_tensorboard()` yöntemini](#export) kullanın.

Aşağıdaki örnek kod, bir uzak işlem hedefi, Azure Machine Learning Işlem için TensorFlow 'un deposundan [Mnist demo deneme](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) deneyimini kullanır. Ardından, TensorFlow modelini eğitmek için bir çalıştırma yapılandırıp başlatacağız ve ardından TensorBoard 'i bu TensorFlow deneyine karşı başlatacak.

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
Bu kod dosyası mnist_with_summaries. Kopyala, vb. çağıran satırlar olduğunu fark edilir `tf.summary.scalar()`  `tf.summary.histogram()` `tf.summary.FileWriter()` . Bu yöntemler, denemeleri 'in çalıştırma geçmişine yönelik anahtar ölçümlerini gruplar, günlüğe kaydeder ve Etiketler. `tf.summary.FileWriter()`Bu, özellikle de önem taşıyan deneme ölçümlerinden verileri serileştirerek, TensorBoard 'in onları kapamasını sağlar.

 ### <a name="configure-experiment"></a>Deneme yapılandırma

Aşağıda, denememiz yapılandırır ve Günlükler ve veriler için Dizin ayarladık. Bu Günlükler, çalışma geçmişine yüklenecek ve bu da TensorBoard 'in daha sonra eriştiği anlamına gelir.

> [!Note]
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

# Writing logs to ./logs results in their being uploaded to the run history,
# and thus, made accessible to our TensorBoard instance.
args = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Denemeniz için bir küme oluşturun
Bu deneme için bir AmlCompute kümesi oluşturacağız, ancak denemeleri 'niz herhangi bir ortamda oluşturulabilir ve deneme çalıştırma geçmişine karşı TensorBoard 'ı yine de başlatabileceksiniz. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpu-cluster"

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="configure-and-submit-training-run"></a>Eğitim çalıştırmasını yapılandırma ve gönderme

Bir ScriptRunConfig nesnesi oluşturarak bir eğitim işi yapılandırın.

```Python
from azureml.core import ScriptRunConfig
from azureml.core import Environment

# Here we will use the TensorFlow 2.2 curated environment
tf_env = Environment.get(ws, 'AzureML-TensorFlow-2.2-GPU')

src = ScriptRunConfig(source_directory=exp_dir,
                      script='mnist_with_summaries.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
run = exp.submit(src)
```

### <a name="launch-tensorboard"></a>TensorBoard Başlat

Çalışma sırasında veya tamamlandıktan sonra TensorBoard 'ı başlatabilirsiniz. Aşağıda, ' `tb` de yüklenen deneme çalıştırması geçmişini alan `run` ve ardından tensorboard 'ı yöntemiyle Başlatan bir tensorboard nesne örneği oluşturacağız `start()` . 
  
[Tensorboard Oluşturucusu](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard) bir çalıştırma dizisi alır, bu yüzden emin olun ve tek öğeli dizi olarak geçirin.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

> [!Note]
> Bu örnek TensorFlow kullanırken, TensorBoard, PyTorch veya Chainer ile kolayca kullanılabilir. TensorFlow, TensorBoard çalıştıran makinede kullanılabilir olmalıdır, ancak PyTorch veya Chainer hesaplamaları yapan makinede gerekli değildir. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Seçenek 2: geçmişi, TensorBoard 'da görüntülenecek şekilde dışa aktarma

Aşağıdaki kod örnek bir deneme oluşturur, Azure Machine Learning çalıştırma geçmişi API 'Lerini kullanarak günlüğe kaydetme işlemini başlatır ve deneme çalıştırması geçmişini görselleştirme için TensorBoard tarafından tüketilen günlüklere dışarı aktarır. 

### <a name="set-up-experiment"></a>Deneme ayarla

Aşağıdaki kod yeni bir deneme ayarlar ve çalıştırma dizinini adlandırır `root_run` . 

```python
from azureml.core import Workspace, Experiment
import azureml.core

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

Bu kod için, bir doğrusal regresyon modeli ve günlük anahtarı ölçümleri, alfa katsayısı `alpha` ve ortalama kare hatası, `mse` çalışma geçmişi içinde eğiyoruz.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
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

SDK 'nın [export_to_tensorboard ()](/python/api/azureml-tensorboard/azureml.tensorboard.export) yöntemiyle, Azure Machine Learning denemızın çalıştırma geçmişini tensorboard günlüklerine verebiliriz, böylece bunları tensorboard aracılığıyla görüntüleyebiliriz.  

Aşağıdaki kodda, klasörü `logdir` geçerli çalışma dizinimizde oluşturacağız. Bu klasör, deneme çalıştırması geçmişimizi ve Günlüklerimizi nereden dışarı aktarmamız `root_run` ve sonra bu çalışmayı tamamlandı olarak işaretliyoruz. 

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

> [!Note]
> Ayrıca, çalıştırmanın adını belirterek belirli bir çalıştırmayı TensorBoard öğesine dışarı aktarabilirsiniz.  `export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>TensorBoard başlatma ve durdurma
Bu deneme için çalıştırma geçmişimiz verildikten sonra, TensorBoard 'ı [Start ()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#start-start-browser-false-) yöntemiyle başlatabiliriz. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

İşiniz bittiğinde, TensorBoard nesnesinin [Stop ()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#stop--) yöntemini çağırdığınızdan emin olun. Aksi halde, ana bilgisayar çekirdeğini kapatıncaya kadar TensorBoard çalışmaya devam edecektir. 

```python
tb.stop()
```

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır ki, iki denemeleri oluşturdunuz ve olası ayarlama ve yeniden eğitimlere yönelik bölgeleri belirlemek için, çalışma geçmişlerine göre TensorBoard 'i nasıl başlatacağınızı öğrendiniz. 

* Modelinize memnun kaldıysanız [model dağıtma](how-to-deploy-and-where.md) makalesini okuyun. 
* [Hyperparameter ayarlaması](how-to-tune-hyperparameters.md)hakkında daha fazla bilgi edinin.
