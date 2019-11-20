---
title: 'Görüntü sınıflandırma öğreticisi: modelleri eğitme'
titleSuffix: Azure Machine Learning
description: Bir görüntü sınıflandırma modelini scikit ile eğitme hakkında bilgi edinin Azure Machine Learning bir Python Jupyter not defterinde öğrenin. Bu öğretici, iki bölümden oluşan bir serinin birinci bölümüdür.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 4d16c07bf42c99b905868cb956d82e8723da61d6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581535"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>Öğretici: veri ve scikit ile görüntü sınıflandırma modellerini eğitme-Azure Machine Learning kullanmayı öğrenin
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu öğreticide, bir makine öğrenimi modelini uzaktan işlem kaynaklarında eğitebilirsiniz. Bir Python Jupyter not defterinde Azure Machine Learning için eğitim ve dağıtım iş akışını kullanacaksınız.  Ardından not defterini şablon olarak kullanıp kendi verilerinizle kendi makine öğrenmesi modelinizi eğitebilirsiniz. Bu öğretici, **iki bölümden oluşan bir öğretici serisinin birinci bölümüdür**.  

Bu öğreticide, [Mnist](http://yann.lecun.com/exdb/mnist/) veri kümesini ve scikit 'i kullanarak basit bir Lojistik gerileme yapılır. Azure Machine Learning [hakkında bilgi edinin](https://scikit-learn.org) . MNIST, 70.000 gri tonlamalı resimden oluşan popüler bir veri kümesidir. Her görüntü, sıfır ile dokuz arasında bir sayıyı temsil eden 28 x 28 piksellik bir sayının el ile yazılmış bir rakamdır. Amaç, belirli bir resim temsil ettiği rakamı tanımlamak için çok sınıflı bir sınıflandırıcı oluşturmaktır.

Aşağıdaki eylemleri nasıl gerçekleştireceğinizi öğrenin:

> [!div class="checklist"]
> * Geliştirme ortamınızı ayarlayın.
> * Verilere erişin ve verileri inceleyin.
> * Uzak bir kümede basit bir lojistik regresyon modeli eğitme.
> * Eğitim sonuçlarını gözden geçirin ve en iyi modeli kaydedin.

Bir modelin nasıl seçeceğinizi ve [Bu öğreticinin ikinci bölümünde](tutorial-deploy-models-with-aml.md)nasıl dağıtılacağını öğreneceksiniz.

Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

>[!NOTE]
> Bu makaledeki kod, [Azure MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) sürümü 1.0.65 ile test edilmiştir.

## <a name="prerequisites"></a>Ön koşullar

* [Öğreticiyi doldurun: Ilk ml denemenizi oluşturmaya başlama](tutorial-1st-experiment-sdk-setup.md) :
    * Çalışma alanı oluşturma
    * Öğreticiler Not defterini çalışma alanındaki klasörünüze kopyalayın.
    * Bulut tabanlı bir not defteri VM 'si oluşturun.

* Klonlanan **öğreticiler** klasörünüzde **img-Classification-part1-eğitim. ipynb** Not defterini açın. 


Öğretici ve ilgili **Utils.py** dosyası, kendi [Yerel ortamınızda](how-to-configure-environment.md#local)kullanmak isterseniz [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 'da da kullanılabilir. Bu öğreticinin bağımlılıklarını yüklemek için `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` çalıştırın.

> [!Important]
> Bu makalenin geri kalanında not defterinde gördüğünüz içerikle aynı içerik yer almaktadır.  
>
> Kodu çalıştırırken okumak istiyorsanız, Jupyter not defterine şimdi geçin. 
> Bir not defterinde tek bir kod hücresini çalıştırmak için, kod hücresine tıklayın ve **SHIFT + enter**tuşuna basın. Ya da tüm not defteri ' ni üstteki araç çubuğundan **Çalıştır** ' ı seçerek çalıştırın.

## <a name="start"></a>Geliştirme ortamınızı ayarlama

Geliştirme çalışmanızdaki tüm kurulum bir Python not defterinde gerçekleştirilebilir. Kurulum aşağıdaki eylemleri içerir:

* Python paketlerini içeri aktarın.
* Yerel bilgisayarınızın uzak kaynaklarla iletişim kurabilmesi için bir çalışma alanına bağlanın.
* Tüm çalıştırmalarınızı izlemek için bir deneme oluşturun.
* Eğitim için kullanmak üzere bir uzak işlem hedefi oluşturun.

### <a name="import-packages"></a>Paketleri içeri aktarma

Bu oturumda ihtiyacınız olan Python paketlerini içeri aktarın. Azure Machine Learning SDK sürümünü de görüntüle:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Çalışma alanına bağlan

Mevcut çalışma alanından bir çalışma alanı nesnesi oluşturun. `Workspace.from_config()`, **config. JSON** dosyasını okur ve `ws`adlı bir nesneye ayrıntıları yükler:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

### <a name="create-an-experiment"></a>Deneme oluşturma

Çalışma alanınızdaki çalıştırmaları izleyecek bir deneme oluşturun. Bir çalışma alanı birden çok denemeleri içerebilir:

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Mevcut bir işlem hedefi oluşturun veya ekleyin

Yönetilen bir hizmet olan Azure Machine Learning Işlem kullanarak, veri bilimcileri, Azure sanal makinelerinin kümelerinde makine öğrenimi modellerini eğitebilir. Örnek olarak GPU desteği olan VM 'Ler sayılabilir. Bu öğreticide eğitim ortamınız olarak Azure Machine Learning Işlem oluşturursunuz. Aşağıdaki kod, çalışma alanınızda zaten mevcut değilse sizin için işlem kümelerini oluşturur.

 **İşlem hedefinin oluşturulması yaklaşık beş dakika sürer.** İşlem kaynağı zaten çalışma alanında ise, kod onu kullanır ve oluşturma işlemini atlar.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Artık bulutta bir modeli eğitmek için gerekli paketleriniz ve işlem kaynaklarınız vardır.

## <a name="explore-data"></a>Verileri inceleme

Bir modeli eğitmadan önce, bunu eğitebilmek için kullandığınız verileri anlamanız gerekir. Bu bölümde şunları nasıl yapabileceğinizi öğrenirsiniz:

* MNIST veri kümesini indirin.
* Örnek görüntüleri görüntüleyin.

### <a name="download-the-mnist-dataset"></a>MNIST veri kümesini indirme

Ham veri dosyalarını almak için Azure açık veri kümelerini kullanın. [Azure açık veri](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) kümeleri, daha doğru modeller için makine öğrenimi çözümlerine senaryoya özgü özellikler eklemek için kullanabileceğiniz, seçkin ortak veri kümeleridir. Her bir veri kümesi, verileri farklı yollarla almak için karşılık gelen bir sınıfa sahiptir, bu durumda `MNIST`.

Bu kod, `Dataset`bir alt sınıfı olan `FileDataset` nesne olarak verileri alır. `FileDataset`, veri Mağazalarınız veya genel URL 'lerinde herhangi bir biçimin tek veya birden çok dosyasına başvurur. Sınıfı, veri kaynağı konumuna yönelik bir başvuru oluşturarak dosyaları işlem dosyalarınıza indirme veya bağlama olanağı sağlar. Ayrıca, eğitim sırasında kolay bir şekilde almak için veri kümesini çalışma alanınıza kaydedersiniz.

Veri kümeleri ve SDK kullanımları hakkında daha fazla bilgi edinmek için [nasıl yapılır?](how-to-create-register-datasets.md) konusunu izleyin.

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>Bazı örnek görüntüleri gösterme

Sıkıştırılmış dosyaları `numpy` dizilerine yükleyin. Ardından `matplotlib` kullanarak, üst kısımlarında etiketleriyle veri kümesinden 30 rastgele görüntü çizin. Bu adım, bir `util.py` dosyasına dahil edilen `load_data` bir işlev gerektirir. Bu dosya örnek klasöründe bulunur. Bu not defteriyle aynı klasöre yerleştirildiğinden emin olun. `load_data` işlevi, sıkıştırılmış dosyaları yalnızca sayısal tuş takımı dizileri olarak ayrıştırır.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(data_folder, "train-images-idx3-ubyte.gz"), False) / 255.0
X_test = load_data(os.path.join(data_folder, "t10k-images-idx3-ubyte.gz"), False) / 255.0
y_train = load_data(os.path.join(data_folder, "train-labels-idx1-ubyte.gz"), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, "t10k-labels-idx1-ubyte.gz"), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Rastgele görüntü örnekleri gösterilir:

![Görüntülerin rastgele örneği](./media/tutorial-train-models-with-aml/digits.png)

Artık bu görüntülerin nasıl göründüğü ve beklenen tahmin sonucu hakkında bir fikriniz oldu.

## <a name="train-on-a-remote-cluster"></a>Uzak kümede eğitme

Bu görev için, işi daha önce ayarlamış olduğunuz uzak eğitim kümesine gönderin.  İş göndermek için şunları yaparsınız:
* Dizin oluşturma
* Eğitim betiği oluşturma
* Bir tahmin aracı nesnesi oluşturma
* İşi gönderme

### <a name="create-a-directory"></a>Dizin oluşturma

Gerekli kodu bilgisayarınızdan uzak kaynağa teslim etmek için bir dizin oluşturun.

```python
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Eğitim betiği oluşturma

İşi kümeye göndermek için, önce bir eğitim betiği oluşturun. Aşağıdaki kodu çalıştırarak, az önce oluşturduğunuz dizinde `train.py` adlı eğitim betiğini oluşturun.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Betiğin verileri nasıl aldığına ve modelleri nasıl kaydettiğine dikkat edin:

+ Eğitim betiği, verileri içeren dizini bulmak için bir bağımsız değişken okur. Daha sonra işi gönderdiğinizde, bu bağımsız değişken için veri deposuna işaret edersiniz: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ Eğitim betiği, modelinizi **çıktılar**adlı bir dizine kaydeder. Bu dizine yazılan her şey otomatik olarak çalışma alanınıza yüklenir. Modelinize bu dizinden daha sonra öğreticide erişirsiniz. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ Eğitim betiği dosya `utils.py` veri kümesini doğru bir şekilde yüklemesini gerektirir. Aşağıdaki kod, `script_folder`, uzak kaynaktaki eğitim betiğinin yanı sıra dosyaya `utils.py` kopyalar.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>Tahmin aracı oluşturma

Bir [sköğrenme tahmin aracı](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) nesnesi, çalıştırmayı göndermek için kullanılır. Bu öğeleri tanımlamak için aşağıdaki kodu çalıştırarak tahmin aracı 'nizi oluşturun:

* Tahmin aracı nesnesinin adı, `est`.
* Betiklerinizi içeren dizin. Bu dizindeki dosyaların tümü yürütülmek üzere küme düğümlerine yüklenir.
* Bilgi işlem hedefi. Bu durumda, oluşturduğunuz Azure Machine Learning işlem kümesini kullanırsınız.
* Eğitim betiği adı, **train.py**.
* Eğitim betiğiyle gerekli parametreler.

Bu öğreticide, bu hedef AmlCompute ' dir. Betik klasöründeki tüm dosyalar, çalışma için küme düğümlerine yüklenir. **Data_folder** , veri kümesini kullanacak şekilde ayarlanır. İlk olarak, eğitim için gereken bağımlılıkları belirten bir ortam nesnesi oluşturun. 

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

env = Environment('my_env')
cd = CondaDependencies.create(pip_packages=['azureml-sdk','scikit-learn','azureml-dataprep[pandas,fuse]>=1.1.14'])
env.python.conda_dependencies = cd
```

Ardından aşağıdaki kodla tahmin aracı oluşturun.

```python
from azureml.train.sklearn import SKLearn

script_params = {
    '--data-folder': mnist_file_dataset.as_named_input('mnist_opendataset').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env, 
              entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>İşi kümeye gönderme

Tahmin aracı nesnesini göndererek denemeyi çalıştırın:

```python
run = exp.submit(config=est)
run
```

Çağrı zaman uyumsuz olduğundan, iş başlatıldıktan hemen sonra bir **hazırlık** veya **çalışma** durumu döndürür.

## <a name="monitor-a-remote-run"></a>Uzaktan çalıştırmayı izleme

Toplam olarak, ilk çalıştırma **yaklaşık 10 dakika**sürer. Ancak, komut dosyası bağımlılıkları değişmedikçe, sonraki çalıştırmalar için aynı görüntü yeniden kullanılır. Bu nedenle kapsayıcı başlatma zamanı çok daha hızlıdır.

Beklerken ne olur:

- **Görüntü oluşturma**: Estimator tarafından belirtilen Python ortamıyla eşleşen bir Docker görüntüsü oluşturulur. Görüntü, çalışma alanına yüklenir. Resim oluşturma ve karşıya yükleme **yaklaşık beş dakika**sürer.

  Kapsayıcı sonraki çalıştırmalar için önbelleğe alındığından, bu aşama her Python ortamı için bir kez gerçekleşir. Görüntü oluşturma sırasında, günlükler çalıştırma geçmişine aktarılır. Bu günlükleri kullanarak görüntü oluşturma ilerlemesini izleyebilirsiniz.

- **Ölçeklendirme**: uzak küme, çalışmayı Şu anda kullanılabilir olandan daha fazla düğüm gerektiriyorsa, ek düğümler otomatik olarak eklenir. Ölçeklendirme genellikle **yaklaşık beş dakika sürer.**

- **Çalışıyor**: Bu aşamada, gerekli betikler ve dosyalar işlem hedefine gönderilir. Ardından veri depoları bağlanır veya kopyalanır. Ve sonra **entry_script** çalıştırılır. İş çalışırken **stdout** ve **./logs** dizini çalıştırma geçmişine akışla kaydedilir. Bu günlükleri kullanarak çalıştırmanın ilerlemesini izleyebilirsiniz.

- **Işlem sonrası**: Bu sonuçlara erişebilmek için çalıştırmanın **./çıktılar** dizini çalışma alanınızdaki çalışma geçmişine kopyalanır.

Çalışan bir işin ilerlemesini birkaç şekilde denetleyebilirsiniz. Bu öğreticide bir jupi pencere öğesi ve bir `wait_for_completion` yöntemi kullanılmaktadır.

### <a name="jupyter-widget"></a>Jupyter pencere öğesi

Bir [Jupyıter pencere öğesi](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)ile çalıştırmanın ilerlemesini izleyin. Çalıştırma gönderimi gibi pencere öğesi zaman uyumsuzdur ve iş bitene kadar her 10 ila 15 saniye canlı güncelleştirmeler sağlar:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Pencere öğesi eğitimin sonunda aşağıdaki gibi görünür:

![Not defteri pencere öğesi](./media/tutorial-train-models-with-aml/widget.png)

Bir çalıştırmayı iptal etmeniz gerekirse, [Bu yönergeleri](https://aka.ms/aml-docs-cancel-run)izleyebilirsiniz.

### <a name="get-log-results-upon-completion"></a>Tamamlandıktan sonra günlük sonuçlarını alma

Model eğitimi ve izlemesi arka planda yapılır. Daha fazla kod çalıştırmadan önce model eğitimi bitirene kadar bekleyin. Model eğitiminin ne zaman bittiğini göstermek için `wait_for_completion` kullanın:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Çalıştırma sonuçlarını görüntüleme

Artık uzak düğümde eğitilmiş bir modeliniz vardır. Modelin doğruluğunu alın:

```python
print(run.get_metrics())
```

Çıktıda, uzak modelde 0,9204 doğruluğu görülmektedir:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

Sonraki öğreticide, bu modeli daha ayrıntılı bir şekilde araştırın.

## <a name="register-model"></a>Modeli kaydetme

Eğitim betiğindeki son adım dosya `outputs/sklearn_mnist_model.pkl`, işin çalıştırıldığı kümenin VM 'sinde `outputs` adlı bir dizinde yazdı. `outputs`, bu dizindeki tüm içeriğin çalışma alanınıza otomatik olarak yüklendiği özel bir dizindir. Bu içerik çalışma alanınızın altında yer alan denemedeki çalıştırma kaydında gösterilir. Bu nedenle model dosyası artık çalışma alanınızda de kullanılabilir.

Bu çalıştırma ile ilişkili dosyaları görebilirsiniz:

```python
print(run.get_file_names())
```

Daha sonra bu modeli sorgulayabilir, inceleyebilir ve dağıtabilmeniz için modeli çalışma alanına kaydedin.

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Yalnızca Azure Machine Learning Işlem kümesini de silebilirsiniz. Ancak otomatik ölçeklendirme açıktır ve küme en az sıfırdır. Bu nedenle, bu belirli kaynak kullanımda olmadığında ek işlem ücretleri oluşturmaz:

```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Sonraki adımlar

Bu Azure Machine Learning öğreticide, aşağıdaki görevler için Python kullandınız:

> [!div class="checklist"]
> * Geliştirme ortamınızı ayarlayın.
> * Verilere erişin ve verileri inceleyin.
> * Popüler scikit-makine öğrenimi kitaplığını kullanarak uzak kümede birden çok modeli eğitme
> * Eğitim ayrıntılarını gözden geçirin ve en iyi modeli kaydedin.

Bu kayıtlı modeli, öğretici serisinin sonraki bölümündeki yönergeleri kullanarak dağıtmaya hazırsınız:

> [!div class="nextstepaction"]
> [Öğretici 2 - Modelleri dağıtma](tutorial-deploy-models-with-aml.md)
