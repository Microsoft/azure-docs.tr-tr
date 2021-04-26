---
title: 'Görüntü sınıflandırma öğreticisi: modelleri dağıtma'
titleSuffix: Azure Machine Learning
description: Bu öğreticide, bir Python Jupyter not defterinde scikit-öğrenme ile görüntü sınıflandırma modeli dağıtmak için Azure Machine Learning nasıl kullanılacağı gösterilmektedir.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: 0981d325b3c5982793ada480a87afc48bf58acf7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066587"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Öğretici: Azure Container Instances bir görüntü sınıflandırma modeli dağıtma


Bu öğretici, **iki bölümden oluşan bir öğretici serisinin ikinci bölümüdür**. [Önceki öğreticide](tutorial-train-models-with-aml.md) makine öğrenmesi modellerini eğittiniz ve buluttaki çalışma alanınıza modeli kaydettiniz.  Artık modeli bir Web hizmeti olarak dağıtmaya hazırsınız demektir. Bir Web hizmeti, bu durumda bir Docker görüntüsü olan bir görüntüdür. Puanlama mantığını ve modelin kendisini kapsüller. 

Öğreticinin bu bölümünde aşağıdaki görevler için Azure Machine Learning kullanırsınız:

> [!div class="checklist"]
> * Test ortamınızı ayarlayın.
> * Çalışma alanınızdan modeli alın.
> * Modeli Container Instances için dağıtın.
> * Dağıtılan modeli test edin.

Container Instances, iş akışını test etmek ve anlamak için harika bir çözümdür. Ölçeklenebilir üretim dağıtımları için Azure Kubernetes Service’i kullanmayı göz önünde bulundurabilirsiniz. Daha fazla bilgi için bkz. [dağıtma ve nerede](how-to-deploy-and-where.md).

>[!NOTE]
> Bu makaledeki kod, Azure Machine Learning SDK sürümü 1.0.83 ile test edilmiştir.

## <a name="prerequisites"></a>Önkoşullar

Not defterini çalıştırmak için, ilk olarak öğreticide model eğitimi ' ni [(1. bölüm) doldurun: görüntü sınıflandırma modelini eğitme](tutorial-train-models-with-aml.md).   Ardından klonlanan *öğreticiler/Image-Classification-mnist-Data* klasöründe *img-Classification-part2-Deploy. ipynb* Not defterini açın.

Bu öğretici, kendi [Yerel ortamınızda](how-to-configure-environment.md#local)kullanmak istiyorsanız [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 'da da kullanılabilir.  Ortamınızdaki ve ortamınızda yüklü olduğundan emin olun `matplotlib` `scikit-learn` . 

> [!Important]
> Bu makalenin geri kalanında not defterinde gördüğünüz içerikle aynı içerik yer almaktadır.  
>
> Kodu çalıştırırken okumak istiyorsanız, Jupyter not defterine şimdi geçin.
> Bir not defterinde tek bir kod hücresini çalıştırmak için, kod hücresine tıklayın ve **SHIFT + enter** tuşuna basın. Ya da tüm not defteri ' ni üstteki araç çubuğundan **Çalıştır** ' ı seçerek çalıştırın.

## <a name="set-up-the-environment"></a><a name="start"></a>Ortamı ayarlama

Başlangıç olarak test ortamını ayarlayın.

### <a name="import-packages"></a>Paketleri içeri aktarma

Bu öğretici için gereken Python paketlerini içeri aktarın.


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
 
import azureml.core

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

## <a name="deploy-as-web-service"></a>Web hizmeti olarak dağıtma

Modeli ACI 'de barındırılan bir Web hizmeti olarak dağıtın. 

Doğru ACI ortamını oluşturmak için şunları sağlayın:
* Modelin nasıl kullanılacağını gösteren puanlama betiği
* ACI’yı oluşturmak için bir yapılandırma dosyası
* Daha önce eğittiğiniz model

### <a name="create-scoring-script"></a>Puanlama betiği oluşturma

Modelin nasıl kullanılacağını göstermek için web hizmeti çağrısının kullandığı, score.py adlı puanlama betiğini oluşturun.

Puanlama betiğine gerekli iki işlevi eklemelisiniz:
* Normalde modeli genel bir nesneye yükleyen `init()` işlevi. Bu işlev, Docker kapsayıcısı başlatıldığında tek bir kez çalıştırılır. 

* `run(input_data)` işlevi, giriş verileri temelinde bir değer tahmin etmek için modeli kullanır. Çalıştırmanın girişleri ve çıkışlarında serileştirme ve seriden çıkarma için normal olarak JSON kullanılır ama başka biçimler de desteklenir.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

### <a name="create-configuration-file"></a>Yapılandırma dosyası oluşturma

Dağıtım yapılandırma dosyası oluşturun ve ACI kapsayıcısına gereken CPU sayısını ve gigabayt cinsinden RAM miktarını belirtin. Modelinize bağlı olsa da, birçok model için varsayılan 1 çekirdek ve 1 gigabayt RAM çoğunlukla yeterlidir. Daha sonra fazlasına ihtiyacınız olduğunu düşünüyorsanız, görüntüyü yeniden oluşturabilir ve hizmeti yeniden dağıtabilirsiniz.


```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>ACI’da dağıtma
Tahmini tamamlanma süresi: **yaklaşık 2-5 dakika**

Görüntüyü yapılandırın ve dağıtın. Aşağıdaki kod şu adımlardan geçer:

1. Eğitim sırasında kaydedilen ortamı () kullanarak modelin gerektirdiği bağımlılıkları içeren ortam nesnesi oluşturun `tutorial-env` .
1. Modeli bir Web hizmeti olarak dağıtmak için gereken çıkarım yapılandırması oluşturma şunu kullanarak:
   * Puanlama dosyası (`score.py`)
   * önceki adımda oluşturulan ortam nesnesi
1. Modeli ACI kapsayıcısına dağıtın.
1. Web hizmeti HTTP uç noktasını alın.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc3', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

REST istemci çağrılarını kabul eden puanlama web hizmetinin HTTP uç noktasını alın. Bu uç nokta, web hizmetini test etmek veya bunu bir uygulamayla tümleştirmek isteyen herkesle paylaşılabilir.


```python
print(service.scoring_uri)
```

## <a name="test-the-model"></a>Modeli test etme


### <a name="download-test-data"></a>Test verilerini indir
Test verilerini **./Data/** dizinine indirin


```python
import os
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)
```

### <a name="load-test-data"></a>Test verilerini yükleme

Test verilerini, eğitim öğreticisi sırasında oluşturulan **./data/** dizininden yükleyin.


```python
from utils import load_data
import os
import glob

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
```

### <a name="predict-test-data"></a>Test verilerini tahmin etme

Tahminleri almak için test veri kümesinin modele akışını gerçekleştirin.


Aşağıdaki kod şu adımlardan geçer:
1. Verileri JSON dizisi olarak ACI’da barındırılan web hizmetine gönderme. 

1. Hizmeti çağırmak için SDK'nın `run` API’sini kullanma. cURL gibi HTTP araçlarını kullanarak ham çağrılar da yapabilirsiniz.


```python
import json
test = json.dumps({"data": X_test.tolist()})
test = bytes(test, encoding='utf8')
y_hat = service.run(input_data=test)
```

###  <a name="examine-the-confusion-matrix"></a>Karışıklık matrisini inceleme

Test kümesinden kaç örneğin doğru sınıflandırıldığını görmek için karışıklık matrisi oluşturun. Yanlış tahminler için yanlış sınıflandırılmış değere dikkat edin.


```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

Çıkış, karışıklık matrisini gösterir:

```output
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
```

Karışıklık matrisini grafik olarak görüntülemek için `matplotlib` kullanın. Bu grafikte X ekseni asıl değerleri, Y ekseni de tahmini değerleri gösterir. Her kılavuzun rengi, hata oranını gösterir. Renk ne kadar açıksa hata oranı da o kadar yüksektir. Örneğin, birçok 5 yanlışlıkla 3 olarak sınıflandırılmıştır. Bu nedenle, (5, 3) üzerinde parlak bir ızgara görürsünüz.

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


## <a name="show-predictions"></a>Tahminleri göster

Dağıtılan modeli, test verilerinden alınan 30 görüntüden oluşan rastgele bir örnekle test edin.  


1. Döndürülen tahminleri yazdırma ve bunları giriş görüntüleriyle birlikte çizme. Yanlış sınıflandırılmış örnekleri vurgulamak için kırmızı yazı tipi ve ters görüntü (siyah üzerine beyaz) kullanılır. 

 Model doğruluğu yüksek olduğundan, yanlış sınıflandırılmış örneği görebilmek için önce aşağıdaki kodu birkaç kez çalıştırmanız gerekebilir.



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
plt.figure(figsize = (20, 1))

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

Web hizmetini test etmek için ham HTTP isteği de gönderebilirsiniz.


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

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Sonraki adımlar

+ [Azure Machine Learning için dağıtım seçenekleri](how-to-deploy-and-where.md)hakkında bilgi edinin.
+ [Web hizmeti için istemci oluşturma](how-to-consume-web-service.md)hakkında bilgi edinin.
+  [Büyük miktarlarda verileri zaman uyumsuz olarak tahmin edin](./tutorial-pipeline-batch-scoring-classification.md) .
+ Azure Machine Learning modellerinizi [Application Insights](how-to-enable-app-insights.md)izleyin.
+ [Otomatik algoritma seçim](tutorial-auto-train-models.md) öğreticisini deneyin.