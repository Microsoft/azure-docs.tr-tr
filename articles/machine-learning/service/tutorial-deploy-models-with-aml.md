---
title: 'Görüntü sınıflandırma öğreticisi: modelleri dağıtma'
titleSuffix: Azure Machine Learning
description: Bu öğreticide, bir Python Jupyter not defterinde scikit-öğrenme ile görüntü sınıflandırma modeli dağıtmak için Azure Machine Learning nasıl kullanılacağı gösterilmektedir. Bu öğretici, iki bölümden oluşan bir serinin ikinci bölümüdür.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: ae657daca86c979495ca14d9df845e2a7a769e0a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476145"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Öğretici: Azure Container Instances bir görüntü sınıflandırma modeli dağıtma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu öğretici, **iki bölümden oluşan bir öğretici serisinin ikinci bölümüdür**. [Önceki öğreticide](tutorial-train-models-with-aml.md) makine öğrenmesi modellerini eğittiniz ve buluttaki çalışma alanınıza modeli kaydettiniz.  

Artık modeli, [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/)bir Web hizmeti olarak dağıtmaya hazır olursunuz. Bir Web hizmeti, bu durumda bir Docker görüntüsü olan bir görüntüdür. Puanlama mantığını ve modelin kendisini kapsüller. 

Öğreticinin bu bölümünde aşağıdaki görevler için Azure Machine Learning kullanırsınız:

> [!div class="checklist"]
> * Test ortamınızı ayarlayın.
> * Çalışma alanınızdan modeli alın.
> * Modeli yerel olarak test edin.
> * Modeli Container Instances için dağıtın.
> * Dağıtılan modeli test edin.

Container Instances, iş akışını test etmek ve anlamak için harika bir çözümdür. Ölçeklenebilir üretim dağıtımları için Azure Kubernetes hizmetini kullanmayı göz önünde bulundurun. Daha fazla bilgi için bkz. [dağıtma ve nerede](how-to-deploy-and-where.md).

>[!NOTE]
> Bu makaledeki kod, Azure Machine Learning SDK sürümü 1.0.41 ile test edilmiştir.

## <a name="prerequisites"></a>Önkoşullar

Not defterini çalıştırmak için, ilk olarak öğreticide model eğitimi ' ni [(1. bölüm) doldurun: görüntü sınıflandırma modelini eğitme](tutorial-train-models-with-aml.md).   Ardından, klonlanan **öğreticiler** klasörünüzde **img-Classification-part2-Deploy. ipynb** Not defterini açın.

Bu öğretici, kendi [Yerel ortamınızda](how-to-configure-environment.md#local)kullanmak istiyorsanız [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 'da da kullanılabilir.  Ortamınızda `matplotlib` ve `scikit-learn` yüklediğinizden emin olun. 

> [!Important]
> Bu makalenin geri kalanında not defterinde gördüğünüz içerikle aynı içerik yer almaktadır.  
>
> Kodu çalıştırırken okumak istiyorsanız, Jupyter not defterine şimdi geçin.
> Bir not defterinde tek bir kod hücresini çalıştırmak için, kod hücresine tıklayın ve **SHIFT + enter**tuşuna basın. Ya da tüm not defteri ' ni üstteki araç çubuğundan **Çalıştır** ' ı seçerek çalıştırın.

## <a name="start"></a>Ortamı ayarlama

Başlangıç olarak test ortamını ayarlayın.

### <a name="import-packages"></a>Paketleri içeri aktarma

Bu öğretici için gereken Python paketlerini içeri aktarın:

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Modeli alma

Önceki öğreticide çalışma alanınızda bir modeli kaydetmiştiniz. Şimdi bu çalışma alanını yükleyin ve modeli yerel dizininize indirin:


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os
ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>Modeli yerel olarak test etme

Dağıtmadan önce, modelinizin yerel olarak çalıştığından emin olun:
* Yük testi verileri.
* Test verilerini tahmin edin.
* Karışıklık matrisini inceleyin.

### <a name="load-test-data"></a>Test verilerini yükleme

Eğitim öğreticisi sırasında oluşturulan **./Data/** dizininden test verilerini yükleyin:

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)
```

### <a name="predict-test-data"></a>Test verilerini tahmin etme

Öngörülere ulaşmak için test veri kümesini modele besle:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load(os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Karışıklık matrisini inceleme

Test kümesinden kaç örneğin doğru sınıflandırıldığını görmek için karışıklık matrisi oluşturun. Yanlış tahminlere yönelik hatalı sınıflandırılan değere dikkat edin: 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

Çıkış, karışıklık matrisini gösterir:

    [[ 960    0    1    2    1    5    6    3    1    1]
     [   0 1112    3    1    0    1    5    1   12    0]
     [   9    8  920   20   10    4   10   11   37    3]
     [   4    0   17  921    2   21    4   12   20    9]
     [   1    2    5    3  915    0   10    2    6   38]
     [  10    2    0   41   10  770   17    7   28    7]
     [   9    3    7    2    6   20  907    1    3    0]
     [   2    7   22    5    8    1    1  950    5   27]
     [  10   15    5   21   15   27    7   11  851   12]
     [   7    8    2   13   32   13    0   24   12  898]]
    Overall accuracy: 0.9204
   

Karışıklık matrisini grafik olarak görüntülemek için `matplotlib` kullanın. Bu grafikte, x ekseni gerçek değerleri gösterir ve y ekseni tahmin edilen değerleri gösterir. Her kılavuzdaki renk hata oranını gösterir. Renk ne kadar açıksa hata oranı da o kadar yüksektir. Örneğin, birçok 5, 3 ' ün yanlış sınıflandırılmakta. Bu nedenle, (5, 3) üzerinde parlak bir ızgara görürsünüz:

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![Karışıklık matrisini gösteren grafik](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-a-web-service"></a>Web hizmeti olarak dağıtma

Modeli test ettikten ve sonuçlardan memnun kaldıktan sonra, modeli Container Instances barındırılan bir Web hizmeti olarak dağıtın. 

Container Instances için doğru ortamı derlemek için aşağıdaki bileşenleri sağlayın:
* Modelinin nasıl kullanılacağını gösteren bir Puanlama betiği.
* Hangi paketlerin yüklenmesi gerektiğini göstermek için bir ortam dosyası.
* Kapsayıcı örneğini derlemek için bir yapılandırma dosyası.
* Daha önce eğitilen model.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Puanlama betiği oluşturma

**Score.py**adlı bir Puanlama betiği oluşturun. Web hizmeti çağrısı, modelinin nasıl kullanılacağını göstermek için bu betiği kullanır.

Puanlama betiğine şu iki gerekli işlevi ekleyin:
* Normalde modeli genel bir nesneye yükleyen `init()` işlevi. Bu işlev, Docker kapsayıcısı başlatıldığında tek bir kez çalıştırılır. 

* `run(input_data)` işlevi, giriş verileri temelinde bir değer tahmin etmek için modeli kullanır. Çalıştırmanın girişleri ve çıkışlarında serileştirme ve seriden çıkarma için normal olarak JSON kullanılır ama başka biçimler de desteklenir.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Ortam dosyası oluşturma

Ardından, betiğin tüm paket bağımlılıklarını belirten **MyENV. yıml**adlı bir ortam dosyası oluşturun. Bu dosya, tüm bu bağımlılıkların Docker görüntüsüne yüklendiğinden emin olmak için kullanılır. Bu modelin `scikit-learn` ve `azureml-sdk`gerekir:

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml", "w") as f:
    f.write(myenv.serialize_to_string())
```
`myenv.yml` dosyanın içeriğini gözden geçirin:

```python
with open("myenv.yml", "r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Yapılandırma dosyası oluşturma

Bir dağıtım yapılandırma dosyası oluşturun. Container Instances Kapsayıcınız için gereken CPU sayısını ve gigabayt miktarını belirtin. Modelinize bağlı olsa da, bir çekirdek ve 1 gigabayt RAM 'in varsayılanı birçok model için yeterlidir. Daha sonra gerekirse, görüntüyü yeniden oluşturmanız ve hizmeti yeniden dağıtmanız gerekir.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  
                                                     "method": "sklearn"},
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Container Instances içinde dağıtma
Dağıtımı tamamlama süresi **yaklaşık yedi ila sekiz dakika**.

Görüntüyü yapılandırın ve dağıtın. Aşağıdaki kod şu adımlardan geçer:

1. Şu dosyaları kullanarak bir görüntü oluşturun:
   * Puanlama dosyası `score.py`.
   * Ortam dosyası `myenv.yml`.
   * Model dosyası.
1. Görüntüyü çalışma alanının altına kaydedin. 
1. Görüntüyü Container Instances kapsayıcısına gönderin.
1. Görüntüyü kullanarak Container Instances bir kapsayıcı başlatın.
1. Web hizmeti HTTP uç noktasını alın.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc',
                       models=[model], 
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

REST istemci çağrılarını kabul eden puanlama web hizmetinin HTTP uç noktasını alın. Bu uç noktayı, Web hizmetini test etmek veya bir uygulamayla bütünleştirmek isteyen herkesle paylaşabilirsiniz: 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>Dağıtılan hizmeti test etme

Daha önce, tüm test verilerini modelin yerel sürümüyle puanlanır. Artık, dağıtılan modeli test verilerinden alınan 30 görüntüden oluşan rastgele bir örnekle test edebilirsiniz.  

Aşağıdaki kod şu adımlardan geçer:
1. Verileri, Container Instances barındırılan Web hizmetine bir JSON dizisi olarak gönderin. 

1. Hizmeti çağırmak için SDK'nın `run` API’sini kullanma. Ayrıca, **kıvrımlı**gibi HERHANGI bir http aracını kullanarak ham çağrılar yapabilirsiniz.

1. Döndürülen tahminleri yazdırma ve bunları giriş görüntüleriyle birlikte çizme. Kırmızı renkli yazı tipi ve ters görüntü, yanlış sınıflandırılmış örnekleri vurgulamak için kullanılır. 

Model doğruluğu yüksek olduğundan, yanlış sınıflandırılmış bir örnek görebilmeniz için aşağıdaki kodu birkaç kez çalıştırmanız gerekebilir:

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize=(20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Bu sonuç, test görüntülerinin rastgele bir örneğinden oluşur:

![Sonuçları gösteren grafik](./media/tutorial-deploy-models-with-aml/results.png)

Web hizmetini test etmek için ham bir HTTP isteği de gönderebilirsiniz:

```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer öğreticiler ve araştırmayla ilgili kaynak grubunu ve çalışma alanını tutmak için bu API çağrısını kullanarak yalnızca Container Instances dağıtımı silebilirsiniz:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Sonraki adımlar

+ [Azure Machine Learning için dağıtım seçenekleri](how-to-deploy-and-where.md)hakkında bilgi edinin.
+ [Web hizmeti için istemci oluşturma](how-to-consume-web-service.md)hakkında bilgi edinin.
+  [Büyük miktarlarda verileri zaman uyumsuz olarak tahmin edin](how-to-run-batch-predictions.md) .
+ Azure Machine Learning modellerinizi [Application Insights](how-to-enable-app-insights.md)izleyin.
+ [Otomatik algoritma seçim](tutorial-auto-train-models.md) öğreticisini deneyin. 
